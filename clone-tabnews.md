- [[GitHub]] e [[Git]]
- [[Protocolos http]] 
- [[editorconfig|.editorconfig]]
- [[DNS]] 







## 





# Testes automatizados

Os Teste Runners são códigos que executam outros códigos e esperam que o código testado se comporte de uma maneira específica  pré determinada

## Jest

Para esse Projeto vamos utilizar o Jest, Teste Runner criado pelo Facebook em foco inicial com React, 

 

## Corpo de um test com jest

Primeiro você tem a função `test()`, ali sinaliza que um teste incia, o primeiro parametro é o nome que você vai dar para o teste, o segundo é uma função callback que vai ter em seu corpo um `expect()` que fica que recebe uma função e depois é encadeado por um `.toBe()` que recebe o que você espera que aconteça

```jsx
const somar = (a, b) => {
  return a + b
}

exports.somar = somar
```

```jsx
const calculadora = require('../models/calculadora.js')

test('Somar 2 + 2 deveria returnar 4', () => {
  const resutado = calculadora.somar(2, 2)

  expect(resutado).toBe(4)
})
```

# Protocolo HTTP

## Endpoints

Podemos assumir que um endpoint é um endereço de uma API (Aplcation Programming Interface)

Praticamente tudo tem uma interface, assim como um site, geladeira ou microondas, elas são feitas para podermos utilizar suas função de maneira segura e de uma forma que a gente entenda, uma API é como qualquer interface, porém ela é uma interface programatica, feita para uma maquina entender melhor, e como qualquer outra interface, ela pode ser alterada com forma a nessecidade

 

## API no nextjs

No nextjs para criar uma rota de API devemos seguir a mesma convenção de pastas dentro de pages, todos arquivos dentro de `pages/api` se torna uma rota API publica.

Para retornarmos dados devemos criar uma função que tem dois parametros, `request` (requisição), que tem  dados  de quem esta fazendo a requisição, e `response` (resposta), que vai dar a resposta 

```jsx
const status = (request, response) => {
  response.status(200).send('Os alunos do curso.dev são acima da média')
}

export default status

// Em http://localhost:3000/api/status
// Os alunos do curso.dev sÃ£o acima da mÃ©dia
```

Podemos perceber que a mesagem ficou “estranha”, isso ocorre porque o metodo send não assume o charset ou character set, a grosso modo ‘o tipo do teclado ultilizado’ que quase que globalmente é ultilizado  o `utf-8`, porém temos outro método que assume esse charset por padrão que é o `.json()` que devolve um `json`

```jsx
const status = (request, response) => {
  response
    .status(200)
    .json({ chave: 'Os alunos do curso.dev são acima da média' })
}

export default status

// Resposta
// {
//   "chave": "Os alunos do curso.dev são acima da média"
// }
```

### Mas como esse `utf-8` foi para ai

Para sabrmos isso vamos utilizar o `curl` (Cliente `URL`) que é uma ferramenta de linha de comando que faz requisições `http` para utilzarmos ele basta ir ao terminal digitar `curl <endpoint que deseja fazer a solicitação>` em nosso caso

```bash
curl http://localhost:3000/api/status

# Resposta
{
   "chave": "Os alunos do curso.dev são acima da média"
}
```

porém por padrão o `curl` esconde alguma informações da `response` para mostrar todos os dados devemos por a flag `--verbose` ou `-v`

```bash
curl http://localhost:3000/api/status -v

# Resposta
*   Trying 127.0.0.1:3000...
* Connected to localhost (127.0.0.1) port 3000 (#0)
> GET /api/status HTTP/1.1
> Host: localhost:3000
> User-Agent: curl/8.0.1
> Accept: */*
> 
< HTTP/1.1 200 OK
< Content-Type: application/json; charset=utf-8
< ETag: "thakdn88n31h"
< Content-Length: 55
< Vary: Accept-Encoding
< Date: Sun, 19 Nov 2023 13:57:50 GMT
< Connection: keep-alive
< Keep-Alive: timeout=5
< 
* Connection #0 to host localhost left intact
{"chave":"Os alunos do curso.dev são acima da média"}%
```

No caso dessa ferramenta podemos notar três tipos de informações as com `*` são os coisas que o `curl` está fazendo internamente para funcionar, as com `>` é a requisição, isso é o que o navegador faz por debaixo dos panos para acessar um `endpoint`,  os com `<` é o cabeçalho que recebemos como `response` é aqui que vemos onde onde está o `utf-8`, e por fim temos o `bory`,  o corpo, o conteúdo da resposta que no nosso caso é o objeto `json` 

Isso é o que acontece por debaixo dos panos na internet, são requisições e responsa que são enviadas o tempo todo, e dependendo da respostas as ferramentas utilizam os dados, por exemplo: o navegador de internet faz uma requisição para um `endpoint` e se a resposta for um `html` ele o transforma em layout

## Versionamento de `API`

No versionamento de `API` temos dois principais tipos de mudanças, as `Breaking Change` que são mudanças que quebram o layout quebram as regras pré estabelecidas para utilização da `API`   

E quando temos mudanças que não quebram essas regras temos uma `No-breaking Change` por exemplo: adicionei uma nova propriedade em minha `API` sem afetar nenhuma já existente

Temos varias formas de versionar uma `API` uma das principais formas é a `URI Path Versioning` onde controlamos a versão pelo caminho no domínio por exemplo:

`https://ampertecno.com.br/api/v1/contents` 

`https://ampertecno.com.br/api/v2/contents` 

Também existe a `Header Versioning` em que o cliente manda um cabeçalho dizendo qual versão gostaria de estar utilizando.

A principio vamos utilizar a primeira estratégia para versionamento

![Captura de tela de 2023-11-19 14-29-53.png](versionamento-de-api.png)

## Primeiro teste de integração

Vamos fazer um teste usando `fetch` para verificar se o status retorna `200` (sucesso) 

```jsx
test('GET to /api/v1/status should return 200', async () => {
  const response = await fetch('http://localhost:3000/api/v1/status')
  expect(response.status).toBe(200)
})
```

# Qual banco de dados utilizar

Geralmente tem três passos principais para escolher um banco de dados

`DBMS - Data Base Management System` Sistema de gerenciamento de banco de dados, é o “pacotão banco de dados”, é aquete que tem o banco de dados e o que gerencia, por exemplo `Postgres`, `MySQL`

`Query` o que vai ser usado para consultar o banco de dados

`Migrations` 

Existem vários tipos de banco de dados, como os Relacionais, os não relacionais que tem os os subtipos armazenar documentos objetos, listas, armazenamento chave valor, Series temporais usados muito para dados de monitoria, Espacial geralmente para dados geográficos

Para fazer as `query` muitos utilizam os `ORM`  Object-Relational-Mapping é uma abstração que facilita a manipulação dos dados, onde em vez de usar `Linguagem SQL` usamos objetos com metodos por exemplo `user.count()`

Uma das vantagens de usar um `ORM` é que geralmente eles tem suporte a vários bancos de dados `SQL` , então mesmo você migrando de banco de dados você utiliza o mesmo código

Mas em nosso caso vamos fazer tudo na unha, vamos criar nosso próprio `ORM` , só precisamos de uma forma de se conectar no banco de dados, e para isso vamos usar o `pg` ou `node-postgres` esse modulo abstrai o minimo que da 

Agora só falta as `migrations` que é um arquivo que descreve ou instrui as mudanças em seu banco de dados, uma rotina e para esse trabalho vamos utilizar o `node-pg-migrate` 

Cookies

Cabeçarios

Status Code

# Docker

Antes do docker existia um grande problema de ambiente, um grande exemplo é quando duas pessoas iriam subir um banco de dados, e mesmo essas pessoas seguindo um mesmo tutorial, em uma maquina funcionava e na outra não, isso ocorre porque mesmo subindo um banco de dados igual, o ambiente onde ele esta sendo levantado tem suas diferenças, como qual sistema operacional esta usando, ou qual versão desse mesmo sistema, ou mesmo as diferença de hardware.

Uma das soluções encontrada foi a virtualização de sistemas operacionais sendo principalmente utilizando o VitualBox, nesse você configura uma maquina com o hardware e sistema operacional que desejar. Porém essa solução ocupava muita memória, e para cada nova virtualização você separa um “pedaço” do seu computador. 

 Mas em 2013 Solomon Hykes “encontrou” dois recursos no linux que já existia desde 2002 (Name Spaces) e 2007 (cgroups) de que são os `Namespace` um recurso onde se pode isolar processos usando um mesmo kernel, e o cgroups ou Control Groups que um recurso que tem controle granular desses processos onde por exemplo pode limitar o quanto de memória um processo pode usar, Solomo Hykes teve a sacada de criar uma abstração para esse recurso e o chamou de Docker  

 Mas em 2013 Solomon Hykes “encontrou” dois recursos no linux que já existia desde de que são os `Namespace` um recurso onde se pode isolar processos usando um mesmo kernel, e o cgroups ou Control Groups que um recurso que tem controle granular desses processos onde por exemplo pode limitar o quanto de memória um processo pode usar, Solomo Hykes teve a sacada de criar uma abstração para esse recurso e o chamou de `Docker` , no caso ele não criou nada, só criou uma interface mais amigavel, com isso agora podemos “usar um sistema operacional” sem ter que rodar outro sistema operacional.

## Primeiros passos

- Primeiro por padrão devemos criar um arquivo na raiz do projeto chamado `compose.yaml` (YAML Ain’t MarkupLanguage) que significa yaml não é uma linguagem de marcação, o que de fato não é, ela é um superset do `json` um arquivo para guardar informações, como por exemplo a configuração de algo, e funciona parecido com python pois define as hierarquias com identação, mas usando espaços.
- Colocamos nesse arquivo os serviços que queremos colocar, em nosso caso vamos usar uma image pronta então colocamos `sercices` ,`database`,  `image` que escolher, em nosso caso vamos usar uma image alpine do postgres que pegamos no docker hub
- A imagem do postgres precisa de algumas variáveis de ambiente e uma obrigatoria que é `POSTGRES_PASSWORD`
- Para iniciar os serviços do doceker `sudo systemctl start docker`
- Para fazer o docker iniciar junto com o sistema basta executar `sudo systemctl enable docker`
- Para verificar se os serviços estão em execução `sudo systemctl status docker`
- `docker compose up` comando para subir container

```jsx
services:
  database:
    image: 'postgres:16.1-alpine3.19'
    environment:
      POSTGRES_PASSWORD: 'local_password'
```

- `docker ps` para listar os processos ativos, no caso ele mostra os contêineres ativos
- `docker ps -a` para listar os processos ativos ou inativos, no caso ele mostra todos contêineres

```bash
CONTAINER ID   IMAGE                      COMMAND                  CREATED        STATUS                    PORTS     NAMES
896af2c555af   postgres:16.1-alpine3.19   "docker-entrypoint.s…"   44 hours ago   Exited (0) 44 hours ago             clone-tabnews-database-1
```

- Nesse mundo de processos temos os `exits codes` que são parecidos com os status code da web, esses status são emitidos quando finalizamos os processos, sendo `0` deu tudo certo e qualquer coisa acima disso é que deu errado por exemplo `255`
- `docker logs <nome do container>` você consegue ver todos os logs do container

```bash
2023-12-10 23:15:50.867 UTC [1] LOG:  starting PostgreSQL 16.1 on x86_64-pc-linux-musl, compiled by gcc (Alpine 13.2.1_git20231014) 13.2.1 20231014, 64-bit
2023-12-10 23:15:50.868 UTC [1] LOG:  listening on IPv4 address "0.0.0.0", port 5432
2023-12-10 23:15:50.868 UTC [1] LOG:  listening on IPv6 address "::", port 5432
2023-12-10 23:15:50.874 UTC [1] LOG:  listening on Unix socket "/var/run/postgresql/.s.PGSQL.5432"
2023-12-10 23:15:50.880 UTC [50] LOG:  database system was shut down at 2023-12-10 23:15:50 UTC
2023-12-10 23:15:50.894 UTC [1] LOG:  database system is ready to accept connectionsdocker logs
2023-12-10 23:20:50.983 UTC [48] LOG:  checkpoint starting: time
2023-12-10 23:20:53.735 UTC [48] LOG:  checkpoint complete: wrote 30 buffers (0.2%); 0 WAL file(s) added, 0 removed, 0 recycled; write=2.713 s, sync=0.026 s, total=2.753 s; sync files=10, longest=0.004 s, average=0.003 s; distance=140 kB, estimate=140 kB; lsn=0/1513580, redo lsn=0/1513548
2023-12-10 23:32:19.735 UTC [1] LOG:  received fast shutdown request
2023-12-10 23:32:19.741 UTC [1] LOG:  aborting any active transactions
2023-12-10 23:32:19.768 UTC [1] LOG:  background worker "logical replication launcher" (PID 53) exited with exit code 1
2023-12-10 23:32:19.831 UTC [48] LOG:  shutting down
2023-12-10 23:32:19.848 UTC [48] LOG:  checkpoint starting: shutdown immediate
2023-12-10 23:32:19.922 UTC [48] LOG:  checkpoint complete: wrote 0 buffers (0.0%); 0 WAL file(s) added, 0 removed, 0 recycled; write=0.001 s, sync=0.001 s, total=0.090 s; sync files=0, longest=0.000 s, average=0.000 s; distance=0 kB, estimate=126 kB; lsn=0/1513630, redo lsn=0/1513630
2023-12-10 23:32:20.009 UTC [1] LOG:  database system is shut down
```

- Para rodar o contêiner em segundo plano podemos usar o `docker compose up -d`
- Para se precisamos de qualquer cliente, seja por interface gráfica ou por linha de comando, e para fazer os primeiros testes vamos utilizar o `psql` que é o cliente oficial de linha de comando do `postgres`
- Vamos instalar no meu fedora é sudo `dnf install postgressql-client`
- Vamos se conectar ao container, primeiro devemos passar as variáveis que são, `host` que é nossa maquina local, a `port` que é a porta em que o `postgress` esta sendo exposto que por padrão é a `5432` e o `username` que como não definimos é postgres  `psql --host=localhost --port=5432 --username=postgres`
- Mas para se conectar no contêiner precisamos conectar as portas então vai dar um esse

```bash
psql: error: connection to server at "localhost" (::1), port 5432 failed: Conexão recusada
        Is the server running on that host and accepting TCP/IP connections?
connection to server at "localhost" (127.0.0.1), port 5432 failed: Conexão recusada
        Is the server running on that host and accepting TCP/IP connections?
```

isso ocorre porque o postgres está expondo a porta mas esta fechado no container, pois ainda não definimos qual porta do container vamos nos conectar

- Vamos definir as portas então em nosso arquivo `YAML` onde um lado sera meu `host` e o outro será  `gest` host:guest

```yaml
services:
  database:
    image: 'postgres:16.1-alpine3.19'
    environment:
      POSTGRES_PASSWORD: 'local_password'
    ports:
      - '5432:5432'
```

- Porém para as alterações fazerem efeito devemos destruir e levantar novamente o container, e um comando para facilitar isso é o `docker compose up -d --force-recreate`
- Agora se tentarmos usar o `psql` vamos conseguir, nosso banco de dados já está pronto para se conectar
- Para sair do cliente devemos dar o comando `\q`
- Agora para melhor organização vamos mover o arquivo `compose.ymal` para uma pasta `infra`
- Porém agora o `docker compose` não sabe onde está nosso arquivo, então quando formos levantar o contêiner vamos ter que indicar com `--file` ou `-f` : `docker compose -f infra/compose.yaml up`

# Agora vamos criar um database.js

E para isso vamos usar o `pg`

- Então primeiro vamos instalar e vamos usar a versão 8.11.3
- Para criar nosso component vamos rodar nosso testes em modo watch, o estanho é que o jest fala que não tem testes a ser rodados

```bash
No tests found related to files changed since last commit.
Press `a` to run all tests, or run Jest with `--watchAll`.

Watch Usage
 › Press a to run all tests.
 › Press f to run only failed tests.
 › Press p to filter by a filename regex pattern.
 › Press t to filter by a test name regex pattern.
 › Press q to quit watch mode.
 › Press Enter to trigger a test run.
```

Isso ocorre porque o jest por padrão só testa arquivos que foram modificados, e por padrão isso é conveniente, mas vamos usar um `jest —watchAll` para vigiar tudo, e também já vamos mudar em nosso script do `package.json`  

```json
"scripts": {
		//...
    "test:watch": "jest --watchAll"
  }
```

- Então dentro de nossa pasta infra vamos criar nosso `database.js`
- Vendo a documentação do `node-postgres` vamos tentar nossa primeira query

```jsx
import { Client } from 'pg'

// Criamos uma função assicrona
const query = async (queryObject) => {
	
	// Instanciamos um objeto client que de do pg
  const client = new Client()
	// Iniciamos uma conecção em nosso banco de dados
  await client.connect()

	// Usamos metodo para fazer a primeira query e armazenamos em result
  const result = await client.query(queryObject)
	// Finalizamos a conecção
  await client.end()
  return result
}

export default {
  query,
}
```

porém isso vai dar erro porque em nenhum momento colocamos nossas credenciais em nosso `client`

```jsx
import { Client } from 'pg'

const query = async (queryObject) => {
  const client = new Client({
    host: 'localhost',
    port: 5432,
    user: 'postgres',
    database: 'postgres',
    password: 'local_password',
  })
  await client.connect()

  const result = await client.query(queryObject)
  await client.end()
  return result
}ba

export default {
  query,
}
```

No `bash` se adiconar um estaço antes do comando ele não vai para a `history` 

# Variáveis de ambiente

Quando trabalhamos com dados sensiveis, nunca devemos expor ao publico, então para ajustar isso vamos usar o `dotenv` para armazenalos, ta mas como isso funciona.

- Nosso host trabalha com processos e esses processos usam as variáveis de ambientes que necessitam para funcionar
- Uma maneira de ver por exemplo as variáveis de ambiente de nosso host é digitando `env` no terminal
- Para setar uma variável para um processo especifico basta digitar `NOME_DA_VARIAVEL=<valor da variável>` e depois iniciar o processo desejado, porém essa maneira é de longe não é a melhor
- Com `dotenv` conseguimos por todas as variáveis que precisarmos em um arquivo .`env` e automaticamente esse arquivo vai disponibilizar as variáveis de ambiante, e no `nextjs` já utilizamos ele por padrão então não é necessário instalar nada.
- E para acessar a variável em nosso código basta acessar o `process.env.NOME_DA_VARIAVEL`

```jsx
POSTGRES_HOST=localhost
POSTGRES_PORT=5432
POSTGRES_USER=postgres
POSTGRES_DB=postgres
POSTGRES_PASSWORD=local_password
```

```jsx
import { Client } from 'pg'

const query = async (queryObject) => {
  const client = new Client({
    host: process.env.POSTGRES_HOST,
    port: process.env.POSTGRES_PORT,
    user: process.env.POSTGRES_USER,
    database: process.env.POSTGRES_DB,
    password: process.env.POSTGRES_PASSWORD,
  })
  await client.connect()

  const result = await client.query(queryObject)
  await client.end()
  return result
}

export default {
  query,
}
```