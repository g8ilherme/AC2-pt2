# Preparacao para a pergunta tecnica presencial

**Importante:** o enunciado nao informa a pergunta que sera feita pelo professor. Por isso, este documento e um guia de estudo, e nao uma resposta inventada para uma pergunta desconhecida.

## 1. O que e uma API REST?

Uma API REST permite que um programa envie requisicoes HTTP para consultar ou alterar dados. Neste projeto, a API recebe pedidos como `GET /alunos` ou `POST /alunos` e devolve respostas JSON.

## 2. Para que servem os metodos HTTP?

- **GET**: consultar dados.
- **POST**: cadastrar dados novos.
- **PUT**: atualizar dados existentes.
- **DELETE**: excluir dados.

## 3. Por que usar o MongoDB em vez de arrays?

O MongoDB guarda os alunos e disciplinas de forma persistente. Assim, eles continuam cadastrados mesmo quando a API e fechada ou reiniciada. Os arrays em memoria perderiam os dados ao reiniciar.

## 4. Qual e o papel do Mongoose?

O Mongoose faz a comunicacao entre Node.js e MongoDB e define a estrutura dos documentos (schema), como os campos `ra`, `nome` e `disciplinas`.

## 5. Como o projeto impede RA duplicado?

O campo `ra` tem `unique: true` no schema do Mongoose, que gera um indice unico no MongoDB. Quando um RA repetido e enviado, a API trata esse erro e retorna o codigo **409 Conflict**.

## 6. Como impede disciplinas duplicadas?

Na inclusao de disciplina, a API usa um filtro no MongoDB que so permite adicionar um codigo se esse codigo ainda nao estiver nas disciplinas do aluno. No cadastro inicial do aluno, os codigos da lista tambem sao conferidos. Se repetir, retorna **409**.

## 7. O que significam os status HTTP?

- **200 OK**: consulta, atualizacao ou exclusao realizada.
- **201 Created**: aluno ou disciplina cadastrados.
- **400 Bad Request**: faltou algum campo obrigatorio ou o formato esta invalido.
- **404 Not Found**: o aluno ou a disciplina nao existe.
- **409 Conflict**: conflito de duplicidade de RA ou codigo de disciplina.

## 8. Como funciona o filtro por nome?

`GET /alunos?nome=jo` usa uma pesquisa parcial e sem diferenciar letras maiusculas e minusculas. O `jo` pode encontrar `João`, porque a API usa regex com a opcao `i`.

## 9. Como funciona o resumo?

`GET /alunos/1/resumo` busca o aluno e retorna o RA, o nome, a quantidade de disciplinas e os professores sem repeticao. O `Set` remove nomes de professores repetidos.

## 10. Para que servem as pastas?

- **routes**: define as URLs e metodos HTTP.
- **controllers**: contem a logica de cada operacao.
- **models**: define como os documentos do MongoDB sao armazenados.
- **config**: configura a conexao com o MongoDB.
- **middlewares**: devolve erros HTTP em formato JSON.
- **scripts**: cadastra os tres alunos iniciais.

## Resposta oral resumida (se pedirem para explicar o projeto)

"Eu desenvolvi uma API REST com Node.js, Express e MongoDB para cadastrar e gerenciar alunos e disciplinas. Separei o codigo em rotas, controllers, models e configuracao do banco. O Mongoose salva os dados no MongoDB. Coloquei validacoes para impedir RA e disciplinas duplicadas e retornar os codigos HTTP corretos. Tambem inclui filtros por nome e disciplina e um resumo dos professores do aluno."
