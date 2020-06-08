# MongoDB-Heroku
Configuração na hospedagem de um app node.js e ligação entre o MongoDB Atlas e Heroku no app, (em windows)

## Hospedagem no Heroku


### Init no projeto

Entre no cmd e vá até o diretorio do seu projeto

Inicie o gerenciador de pacotes do node no seu projeto:


$ npm init


Clique "enter" até que finalize o comando.

Após a finalização do comando, surgirão dois arquivos JSON no diretório do seu projeto.

Acessando seu projeto através de algum editor de código(IDE), edite o arquivo "package.json". Na parte de "scripts" adicione uma nova configuração, isto é, após pôr a vírgula na configuração de "text", coloque a seguinte configuração "start" : node "nome do arquivo-pai do backend do seu projeto"(costuma-se ser o app.js). Com isso, o comando "$ npm start" fará a mesma função do comando nodemon.

Também é necessario  mudança na porta de acesso web. Siga o exemplo abaixo onde configuramos a porta no arquivo <i>app.js</i>, já modificado:

```javascript
const PORTA = process.env.PORT || 15829

app.listen(PORTA, function(){
    console.log("O servidor está rolando na url http://localhost:15829!")
});
```

Veja que a dupla barra é a chave na escolha da porta. caso seja encontrada alguma porta em algum ambiente de execução, ela será a escolhida, isto é, caso o mesmo esteja sendo executado em alguma outra plataforma-servidor, como a heroku ou qualquer outra, ela será identificada e escolhida. Caso não seja encontrada nenhuma, então, entendemos que será o ambiente de desenvolvimento, isto é, a porta utilizada por sua escolhida na produção do projeto.

### Conexão com o GIT

É necessário baixar o git e instalar(casualmente, next->next no windows).


<div> Link para download do Git <a href = "https://git-scm.com/downloads">https://git-scm.com/downloads<a></div><br>


Após isso, continuando no diretório do seu projeto, inicie o git:


$ git init


Volte a edição do seu projeto e adicione o arquivo <i>".gitignore"</i>, esse arquivo serve para ignorar o que há dentro dele no momento da adição do projeto ao git. Com isso, nesse arquivo digite: node_modules . No qual é a pasta com todas as instalações feitas do node. Com isso, todas essas pastas serão ignoradas no momento de adicionar ao git.

Para adicionar o projeto no git, volte pro cmd com o diretório do seu projeto e como comando digite:


$ git add .


Após a adição dos arquivos, é hora do commit. Digite o comando:


$ git commit -am "sua mensagem"
<br>

## Conectando ao MongoDB

Abra seu navegador e se registre no site da MongoDB Atlas. Eles disponibilizam banco de dados com 512MB gratuitamente.

<div>Link para se registrar na MongoDB<a href="https://www.mongodb.com/atlas-signup-from-mlab?utm_source=mlab.com&utm_medium=referral&utm_campaign=mlab%20signup&utm_content=blue%20sign%20up%20button">Clique aqui</a></div><br>

Após o registro, abrirá uma tela com 3 opções, escolha a opção <b>Shared Clusters</b>, no qual é a versão gratuita.

Em seguida aparecerá as primeiras configurações do banco de dados a ser criado. Por default recomendo que caso mude algo, seja o nome da Cluster.

Depois disso, aparecerá um painel com o nome <i>SandBox</i>. Aguarde o carregamento. Dentro de tal painel encontrarás o nome <b>Connect</b>. Clique nele.

Então, é agora que irá configurar o banco de dados com o seu projeto.

O primeiro passo é adicionar o IP, em <i>Add Your Current IP Address</i>

Recomendo que adicione o IP: <b>0.0.0.0/0</b>, para que a conexão com a Heroku aconteça normalmente.

Agora crie um usuario de tal banco. Preste atenção no user e na senha que coloca, utilizaremos-os no código do projeto daqui a pouco.
<br>

Confirme os dados. Após isso, prossiga com a operação clicando em <b>Choose a connection method</b>.

O segundo passo é a ligação do banco de dados com o seu projeto. Clique em <b>Connect Your Application</b> e copie o link que está disponivel e volte pro seu projeto.
Note que o link está com umas palavras com cores verdes. Nelas você colocará o nome do user criado de tal banco, a senha criada, e caso vocÊ tenha colocado um nome para o banco, o nome dele, sucessivamente.


#### Mudanças no projeto para conexão do DB

Obviamente não sei como é seu projeto, mas pensamento organizacionamente, é bem provavel que tenha alguma pasta com as configurações do projeto. Nele crie um arquivo .js com o nome <i>db.js</i>, nele iremos configurar a conexão entre o banco de dados da Heroku e também ao banco de dados de ambiente local(aquele que você deve está usando agora).

É um arquivo simples, mas muito importante. Veja o código abaixo:

```javascript
if(process.env.NODE_ENV == "production"){
    module.exports = {mongoURI: "LINK-DO-MONGODB"}
}else{
    module.exports = {mongoURI: "mongodb://localhost/NOME-DO-SEU-DB-LOCAL"}
}
```

Note que o if identifica se o ambiente é o da Heroku, caso seja, o modulo exportado será com o link do seu DB criado no MongoDB. Caso não seja o ambiente da Heroku, sabemos que o ambiente de desenvolvimento local, logo, utilizaremos a conexão com o localhost.

Após isso, indo para o app.js, criamos uma constante dado require do caminho do arquivo db.js. Vamos na parte que configura-se a conexão do mongoose e colocamos a constante criada e o MongoURI, veja o exemplo abaixo:

```javascript
const db = require('./config/db')

mongoose.Promise = global.Promise;
mongoose.connect(db.mongoURI, {
    useMongoClient: true
})
```

## Conectando ao Heroku

Antes de falar sobre a conta na Heroku, é necessário dizer sobre o download da Heroku CLI, a partir dele que poderemos manipular o Heroku via terminal.

<div>Link para download da Heroku CLI<a href="https://devcenter.heroku.com/articles/heroku-cli#download-and-install">https://devcenter.heroku.com/articles/heroku-cli#download-and-install</a></div>
<br>

Após o download do mesmo, digite no cmd, no diretorio do seu projeto:

$ heroku login
<br>

, e clique enter. Ele abrirá a página de login da heroku em algum navegador. Caso não tenha conta, crie. É simples e fácil.

<div>Link do site da Heroku: <a href="https://www.heroku.com/">https://www.heroku.com/</a></div><br>


Após o login através da página que foi aberta, o terminal automaticamente irá se conectar a sua conta. 

Após isso, no diretorio do seu projeto digite: 

$ heroku create 
<br>

É fácil ver que esse comando cria um arquivo heroku, visite a página inicial da sua conta na Heroku. Verás que um arquivo aparecerá.
Após a criação é hora de usar o comando:

$ heroku git:clone -a nome-do-seu-arquivoHeroku-criado
<br>

#### Configurando Heroku com MnngoDB 

Para que haja a conexão entre os servidoores, precisamos configurar a Heroku via terminal. Basta apenas colocar o comando :

$ heroku config:get MONGODB_URI
<br>

Após isso é hora de dar o push do projeto para a Heroku, com o comando:

$ git push heroku master
