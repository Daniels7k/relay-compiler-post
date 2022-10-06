# Intro

Nesse guia você vai aprender como configurar o relay em sua aplicação React e evitar alguns problemas que podem acontecer.

> Atenção esse guia leva em consideração que você tenha um conhecimento prévio em aplicações React, servidores GraphQL e Relay.

![relay.banner.webp](https://cdn.hashnode.com/res/hashnode/image/upload/v1665055037208/ES5ZcyGHb.webp align="center")

# Criando uma aplicação React

Para que fique mais claro, vamos criar uma aplicação React do zero, mas você pode pular esse passo caso já tenha um projeto pronto.

Primeiramente vamos iniciar nosso projeto com o comando:

```npx create-react-app my-app```.


![create-react-app.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1665055810051/yK7FZbxBy.png align="center")


Com nossa aplicação React pronta já podemos instalar o Relay Compiler, va até a raiz do seu projeto utilizando o comando: <br/> 

```cd my-app```

# Instalando o Relay Compiler


Já estando na raiz do projeto vamos rodar o comando: <br/> 

```npm install --dev relay-compiler``` 

Nosso package.json vai ficar assim:

```
{
    ...
    "devDependencies": {
      "relay-compiler": "^14.1.0"
    }
    ...
}
```


# Definindo scripts

Com relay instalado vamos adicionar 2 novos scripts no nosso package.json:

```
// adicionando uma nova seção nos scripts
{
  ...
 "scripts": {
    ...
    "relay": "relay-compiler"
    "start": "npm run relay && react-scripts start",

    ...
 },
 ...
}
```

``relay``

O comando ***relay*** realiza a chamada de outro script no node_modules do relay 
compiler.

``start``

O ***start*** vai garantir que o relay busque o schema mais atualizado do nosso back-end sempre que iniciemos a nossa aplicação react.

# Obtendo o schema do GraphQL server

> Caso você não tenha um graphql server, acesse esse [Schema Link](https://github.com/Daniels7k/relay-compiler-post/blob/99f743a8c21b62050debf27b34535d9eca1573de/schema.graphql) copie o conteudo, crie uma pasta chamada schema.graphql na raiz do seu projeto e cole o conteudo nele, após isso você pode pular essa etapa.

Vamos obter o ***schema.graphql*** do nosso graphql server utilizando uma biblioteca npm chamada [get-graphql-schema](https://www.npmjs.com/package/get-graphql-schema), vamos ver como utiliza-la a seguir.

Instale a biblioteca utilizando esse comando ```npm install -g get-graphql-schema```, e em seguida vamos utilizar o este comando ```get-graphql-schema [OPTIONS] ENDPOINT_URL > schema.graphql```, vamos ver o que esta acontecendo aqui.

``OPTIONS``

No campo ***OPTIONS*** podem ser declaradas algumas peculiariedades, como por exemplo, se seu servidor requisita uma autenticação você pode, enviar no seu header, um token de autorização com o comando ```-h``` 'X-API-KEY=ABC123' ou até mesmo solicitar que a saída do nosso schema seja em json, utilizando o comando -j. As opções não são obrigatórias então não se preocupe.

``ENDPOINT_URL``

Já o ***ENDPOINT_URL***, como o nome já diz é o endpoint do seu graphql server, então aqui você pode declarar algo como ``http://localhost:/4000/graphql``. Essa opção só aceita links com a marcação HTTP/HTTPS, então um link curto como ***localhost:/4000/graphql*** não funcionara.

``> schema.graphql``

E o campo ***> schema.graphq*** define onde vai ser armazenado o arquivo gerado, você pode alterar para algo como ***> ./src/data/schema.graphql***.

#### Exemplo do comando completo: 

```
get-graphql-schema http://localhost:4000/graphql > schema.graphql
```
Se tudo der certo executando o comando vamos receber algo parecido com esse arquivo:

![schema.graphql.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1665061161892/0KTI2g3ER.png align="left")

# Configurando o Relay Compiler

Agora precisamos definir algumas configurações, vamos adicionar um novo campo no nosso package.json:

```
{
  ...
  "relay": {
    "src": "./src/",
    "schema": "./schema.graphql",
    "language": "javascript"
  },
  ...
}

```
Vamos a uma pequena explicação do que esta acontecendo aqui.

``schema``

Primeiramente falaremos sobre o campo ***schema***, ele define onde está armazenado o ***schema.graphql*** que obtemos pelo comando ```get-graphql-schema```.

``src``

Já o campo ***src*** define onde vai ser armazenado o arquivo ```__generated__```, que é responsavel por ***??????????????***.

``language``

E o campo ***language***, é uma referência para que linguagem estamos usando para operar o relay, elas podem ser alteradas entre ```javascript```, ```typescript``` e ```flow```.

Essas são as configurações padrão para que o relay funcione, mas você pode personaliza-la da forma que preferir, para saber sobre mais configurações, recomendo dar uma lida nesse [artigo](https://github.com/facebook/relay/tree/main/packages/relay-compiler).


# Rodando o script

***PRONTO!***  com tudo isso feito, já podemos rodar nosso script, execute o comando: 

```npm start``` 

Se tudo der certo, você vai obter algo parecido com o isso no seu console:


![script running.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1665063739298/5qbxt9IGF.png align="left")

# Problemas que você pode ter

### falied to read file

Em alguns computadores com o sistema operacional windows, você pode receber o erro ***falied to read file*** que é algo relacionado ao caminho que o script percorre, ele se parece com isso:

![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1665064455027/r04XM8m8A.png align="left")

Perceba como o caminho esta com um formato estranho:

![path error.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1665064553381/CGvTrCjAO.png align="left")

 A solução que encontrei foi instalar uma biblioteca chamada ```watchman``` que é comumente utilizada junto com o relay.

Para instalar essa biblioteca é bem simples, primeiramente vamos instalar um package manager chamado ***Chocolatey***, siga os passos de instalação na documentação oficial [aqui](https://chocolatey.org/install).

Em seguida abra seu console ***PowerShell*** ou ***CMD** como administrador e copie o seguinte comando:

 ```choco install watchman```

Se tudo der certo você vai receber isso no console: 

![watchman successful.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1665065272832/E2_nBPADq.png align="left")


E pronto, tente rodar o relay-compiler novamente e provavelmente vai dar certo.

### Codificações em UTF-8 / UTF-16

Em alguns casos, o arquivo schema.graphql obtido pela biblioteca ```get-graphql-schema``` pode ser salvo na codificação UTF-16, e não na UTF-8 que é a aceita pelo relay, caso isso aconteça você pode receber um erro similar a esse: 

```
Error: Error loading schema. Expected the schema to be a .graphql or a .json
file, describing your GraphQL server's API. Error detail:

GraphQLError: Syntax Error: Cannot parse the unexpected character "\uFFFD".
```

Se for o caso, acesse esse [link](https://github.com/prisma-labs/get-graphql-schema/issues/30) e tente uma das soluções.

# Finalizando

Espero que tenha ficado claro como configurar o seu Relay Compiler, e caso haja algum problema ou dúvida em relação ao ***Relay*** ou ***GraphQL***, recomendo dar uma lida nos links abaixo:

- [Relay](https://relay.dev/docs/)
- [GraphQL](https://graphql.org/learn/)
