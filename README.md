# Projeto Escola - API REST com Node.js e MongoDB

API desenvolvida para gerenciar **alunos e disciplinas**. Usa Node.js, Express, Mongoose e banco de dados MongoDB. Os registros ficam salvos no MongoDB, **nao em arrays de memoria**.

## 1. O que instalar

- Node.js 20.19 ou superior (recomendado Node.js 22 LTS).
- MongoDB Community Server (instalado e executando **ou** um banco MongoDB Atlas).
- Postman para executar os 22 testes.
- Git para enviar o projeto ao GitHub.

## 2. MongoDB Atlas configurado

Este projeto ja vem com a conexao MongoDB Atlas no arquivo `.env` (banco `projeto_escola`). **O `.env` contem credenciais: nao envie este ZIP nem esse arquivo para repositorios publicos.** O `.gitignore` ja impede que o Git adicione `.env` normalmente.

1. Extraia o ZIP e abra a pasta `ac2` no VS Code.
2. Rode `npm install` no terminal da pasta.
3. No painel do MongoDB Atlas, acesse **Network Access** e autorize o IP publico da rede de onde o PC vai acessar. Em redes diferentes, cada pessoa precisa liberar o proprio IP. Para uso temporario, revise/remova a liberacao depois.
4. Confirme que o usuario em **Database Access** tem permissao de leitura e escrita no banco.
5. Rode `npm run seed` para inserir tres alunos iniciais (uma vez ou mais, sem duplicar).
6. Rode `npm start` e acesse `http://localhost:3000/alunos`.

Se aparecer `MongoServerSelectionError` ou erro de IP, revise o Network Access e a conectividade. Se aparecer `Authentication failed`, confira usuario/senha do Atlas. Se o cluster estiver pausado, ative-o.

> A conexao online depende das permissoes e da disponibilidade do Atlas. O projeto foi configurado e testado localmente, mas nao foi possivel verificar a conexao remota neste ambiente.

## 3. Inserir os tres alunos iniciais

Com MongoDB funcionando, execute:

```bash
npm run seed
```

Esse comando cadastra os RAs 1, 2 e 3 caso ainda nao existam; nao apaga dados. A colecao usada e `alunos`.

Dados iniciais:

| RA | Aluno | Disciplinas |
|---|---|---|
| 1 | João | MAT101 e HIS101 |
| 2 | Maria | MAT101 e BIO101 |
| 3 | Pedro | GEO101 |

## 4. Iniciar a API

```bash
npm start
```

Para desenvolvimento com reinicio automatico:

```bash
npm run dev
```

O terminal deve exibir `MongoDB conectado com sucesso!` e `API disponivel em http://localhost:3000`.

Para verificar, abra: http://localhost:3000/alunos

Se aparecer erro de conexao, confira se o MongoDB esta ligado e se a variavel `MONGODB_URI` esta correta.

## 5. Rotas disponiveis

| Metodo | URL | Funcao | Sucesso |
|---|---|---|---|
| GET | `/alunos` | Listar alunos | 200 |
| GET | `/alunos/:ra` | Buscar aluno | 200 |
| GET | `/alunos/:ra/disciplinas` | Disciplinas de um aluno | 200 |
| GET | `/alunos?nome=jo` | Buscar por nome parcial | 200 |
| GET | `/alunos?disciplina=MAT101` | Filtrar por disciplina | 200 |
| GET | `/alunos/:ra/resumo` | Resumo com professores unicos | 200 |
| POST | `/alunos` | Cadastrar aluno | 201 |
| PUT | `/alunos/:ra` | Atualizar o nome | 200 |
| DELETE | `/alunos/:ra` | Excluir o aluno | 200 |
| POST | `/alunos/:ra/disciplinas` | Incluir disciplina | 201 |
| PUT | `/alunos/:ra/disciplinas/:codigo` | Editar nome e professor da disciplina | 200 |
| DELETE | `/alunos/:ra/disciplinas/:codigo` | Remover disciplina | 200 |

`ra` e `codigo` sao substituidos pelos valores reais, por exemplo `/alunos/1/disciplinas/MAT101`.

### Exemplo de POST /alunos

No Postman, escolha metodo `POST`, URL `http://localhost:3000/alunos`, aba `Body` > `raw` > `JSON`:

```json
{
  "ra": "5",
  "nome": "Lucas",
  "disciplinas": [
    { "codigo": "ING101", "nome": "Inglês", "professor": "Prof. Paula" }
  ]
}
```

O resultado esperado e `201 Created` com os dados do aluno.

### Exemplo de POST /alunos/1/disciplinas

```json
{
  "codigo": "FIS101",
  "nome": "Física",
  "professor": "Prof. Ricardo"
}
```

## 6. Executar os 22 testes no Postman

1. Abra o **Postman**.
2. Clique em **Import** e importe `postman/Projeto Escola - 22 testes.postman_collection.json`.
3. Abra a colecao e confira a variavel `baseUrl` com valor `http://localhost:3000`.
4. Rode `npm run seed` **antes dos testes** caso os tres alunos iniciais nao estejam no banco.
5. Execute as requisicoes **na ordem de 01 ate 22**. A cada requisicao, confira a resposta e tire o print no Postman.
6. Salve os prints com os nomes exatos indicados em `evidencias/GUIA_DE_PRINTS.md`.
7. Coloque os prints nas pastas `evidencias/sucesso` (01 a 12) e `evidencias/erros` (13 a 22).
8. Execute `npm run verificar:evidencias` para identificar se falta algum arquivo.

> **Os prints nao acompanham este projeto porque precisam ser capturados com o Postman conectado a um MongoDB funcionando.** A colecao de testes esta pronta, inclusive com verificacao automatica dos codigos HTTP.

### Quando um teste falhar por dados antigos

Os casos de teste usam RA `4` e disciplina `FIS101` temporariamente. Apos uma execucao completa, eles sao removidos. Se voce interrompeu a sequencia e tentar executar novamente, exclua manualmente o RA 4 ou a disciplina FIS101 antes de repetir **somente se** eles tiverem sido criados no teste anterior. Nao apague os alunos 1, 2 e 3.

## 7. Validacoes implementadas

- `400` - RA, nome, codigo ou professor ausente/vazio, e JSON invalido.
- `404` - aluno ou disciplina nao encontrados.
- `409` - RA ja cadastrado ou codigo de disciplina repetido para o mesmo aluno.
- `200` - consultas, atualizacoes e exclusoes bem-sucedidas.
- `201` - cadastros de alunos e disciplinas bem-sucedidos.

A regra de RA unico e protegida por um **indice unique no MongoDB**. A insercao de disciplinas verifica duplicidade no proprio banco na mesma operacao. Ao atualizar o nome de um aluno, as disciplinas nao sao alteradas.

## 8. Organizacao

```text
projeto-escola/
├── src/
│   ├── config/database.js
│   ├── controllers/alunoController.js
│   ├── models/Aluno.js
│   ├── routes/alunoRoutes.js
│   ├── middlewares/errorHandler.js
│   ├── utils/ApiError.js
│   ├── utils/validacao.js
│   ├── scripts/seed.js
│   ├── app.js
│   └── server.js
├── tests/validacao.test.js
├── scripts/verificar-evidencias.js
├── postman/Projeto Escola - 22 testes.postman_collection.json
├── evidencias/sucesso/
├── evidencias/erros/
├── .env.example
├── .gitignore
├── package.json
├── README.md
└── RESPOSTA_TECNICA.md
```

## 9. Testes automaticos das validacoes

```bash
npm test
```

Esses testes nao substituem os 22 prints do Postman.

## 10. Enviar ao GitHub

1. Crie um repositorio publico no GitHub chamado `projeto-escola`.
2. Abra o projeto no VS Code e use o Git para enviar **o codigo e as pastas de prints**.
3. Confirme no GitHub que as pastas `evidencias/sucesso` e `evidencias/erros` contem as 22 imagens reais.
4. Nao envie `node_modules`, `.env`, senha do Atlas nem arquivos particulares (o `.gitignore` evita isso).
5. Entregue na faculdade o link do repositorio.

Comandos Git (apos criar o repositorio vazio, substituindo a URL pelo seu repositorio):

```bash
git init
git add .
git commit -m "API Escola com MongoDB e evidencias"
git branch -M main
git remote add origin https://github.com/SEU-USUARIO/projeto-escola.git
git push -u origin main
```

## 11. Pergunta tecnica

Como a pergunta sera feita presencialmente, `RESPOSTA_TECNICA.md` contem um resumo para estudar, sem fingir que sabemos qual pergunta o professor vai escolher.
