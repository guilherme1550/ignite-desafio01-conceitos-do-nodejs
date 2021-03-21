<h1 align="center">Ignite Desafio 01 - Conceitos do Node.js</h1>

<p align="center">
  <a href="#-Projeto">Desafio</a>&nbsp;&nbsp;&nbsp;|&nbsp;&nbsp;&nbsp;
  <a href="#-Rotas">Rotas</a>&nbsp;&nbsp;&nbsp;|&nbsp;&nbsp;&nbsp;
  <a href="#-Específicação-dos-testes">Específicação dos testes</a>&nbsp;&nbsp;&nbsp;|&nbsp;&nbsp;&nbsp;
  <a href="#-Tecnologias">Tecnologias</a>&nbsp;&nbsp;&nbsp;|&nbsp;&nbsp;&nbsp;
  <a href="#-Como-executar">Como executar</a>
</p>

<p align="center">
  <img alt="Ignite" src="public/ignite-2560x1080.png">
</p>

## 📙 Desafio

Neste desafio, foi possível treinar o que foi aprendido até agora no Bootcamp Ignite.

Esta é uma aplicacão para gerenciar tarefas(em inglês *todos*). É permitido criar usuário com `name` e `username`, bem como fazer o CRUD de *todos*:

- Criar um novo *todo*;
- Listar todos os *todos*;
- Alterar o `title` e `deadline`de um *todo* existente;
- Marcar um *todo* como feito;
- Excluir um *todo*;

Tudo isso para cada usuário em específico(o `username` é passado pelo header)

## 🔸 Rotas

### POST `/users`

A rota recebe `name`, e `username` dentro do corpo da requisição. Ao cadastrar um novo usuário, ele é armazenado dentro de um objeto no seguinte formato:  

```jsx
{ 
	id: 'uuid', // precisa ser um uuid
	name: 'Danilo Vieira', 
	username: 'danilo', 
	todos: []
}
```
O ID é um UUID, e a lista `todos` sempre inicia como um array vazio.

### GET `/todos`

A rota recebe, pelo header da requisição, uma propriedade `username` contendo o username do usuário e retorna uma lista com todas as tarefas desse usuário.

### POST `/todos`

A rota recebe `title` e `deadline` dentro do corpo da requisição e, uma propriedade `username` contendo o username do usuário dentro do header da requisição. Ao criar um novo *todo*, ele é armazenado dentro da lista `todos` do usuário que está criando essa tarefa. Cada tarefa esta no seguinte formato: 

```jsx
{ 
	id: 'uuid', // precisa ser um uuid
	title: 'Nome da tarefa',
	done: false, 
	deadline: '2021-02-27T00:00:00.000Z', 
	created_at: '2021-02-22T00:00:00.000Z'
}
```

A propriedade `done` sempre é iniciada como `false` ao criar um *todo*.

**Dica**: Ao fazer a requisição com o Insomnia ou Postman, preencha a data de `deadline` com o formato `ANO-MÊS-DIA`. A tarefa é salva pela rota da seguinte forma: 

```jsx
{ 
	id: 'uuid', // precisa ser um uuid
	title: 'Nome da tarefa',
	done: false, 
	deadline: new Date(deadline), 
	created_at: new Date()
}
```

O uso do `new Date(deadline)` realiza a transformação da string "ANO-MÊS-DIA" (por exemplo "2021-02-25") para uma data válida do JavaScript.

### PUT `/todos/:id`

A rota recebe, pelo header da requisição, uma propriedade `username` contendo o username do usuário e recebe as propriedades `title` e `deadline` dentro do corpo. É alterado **apenas** o `title` e o `deadline` da tarefa que possui o `id` igual ao `id` presente nos parâmetros da rota.

### PATCH `/todos/:id/done`

A rota recebe, pelo header da requisição, uma propriedade `username` contendo o username do usuário e altera a propriedade `done` para `true` no *todo* que possuir um `id` igual ao `id` presente nos parâmetros da rota.

### DELETE `/todos/:id`

A rota recebe, pelo header da requisição, uma propriedade `username` contendo o username do usuário e exclui o *todo* que possui um `id` igual ao `id` presente nos parâmetros da rota.

## 🔸 Específicação dos testes

### Testes de usuários

- **Should be able to create a new user**

Para que esse teste passe, deve-se permitir que um usuário seja criado e retorne um json com o usuário criado.

Também é necessário que retorne a resposta com o código `201`.

- **Should not be able to create a new user when username already exists**

Para que esse teste passe, antes de criar um usuário deve-se validar se outro usuário com o mesmo `username` já existe. Caso exista, retorne uma resposta com status `400` e um json no seguinte formato:

```jsx
{
	error: 'Mensagem do erro'
}
```

Pode ser qualquer mensagem, desde que a propriedade seja `error`.

### Testes de *todos*

**Middleware**

Para completar todos os testes referentes à *todos* é necessário antes ter completado o código que falta no middleware `checkExistsUserAccount`. Para isso, deve-se pegar o `username` do usuário no header da requisição, verificar se esse usuário existe e então colocar esse usuário dentro da `request` antes de chamar a função `next`. Caso o usuário não seja encontrado, deve-se retornar uma resposta contendo status `404` e um json no seguinte formato:

```jsx
{
	error: 'Mensagem do erro'
}
```

**Observação:** O username deve ser enviado pelo header em uma propriedade chamada `username`.

- **Should be able to list all user's todos**

Para que esse teste passe, na rota GET `/todos` é necessário pegar o usuário que foi repassado para o `request` no middleware `checkExistsUserAccount` e então retornar a lista `todos` que está no objeto do usuário.

- **Should be able to create a new todo**

Para que esse teste passe, na rota POST `/todos` é necessário pegar o usuário que foi repassado para o `request` no middleware `checkExistsUserAccount`, pegar também o `title` e o `deadline` do corpo da requisição e adicionar um novo *todo* na lista `todos` que está no objeto do usuário.

É importante seguir a estrutura padrão de um *todo*.

- **Should be able to update a todo**

Para que esse teste passe, na rota PUT `/todos/:id` é necessário atualizar um *todo* existente, recebendo o `title` e o `deadline` pelo corpo da requisição e o `id` presente nos parâmetros da rota.

- **Should not be able to update a non existing todo**

Para que esse teste passe, não deve ser possível a atualização de um *todo* que não existe e retornar uma resposta contendo um status `404` e um json no seguinte formato: 

```jsx
{
	error: 'Mensagem do erro'
}
```

- **Should be able to mark a todo as done**

Para que esse teste passe, na rota PATCH `/todos/:id/done` deve-se mudar a propriedade `done`de um *todo* de `false` para `true`, recebendo o `id` presente nos parâmetros da rota.

- **Should not be able to mark a non existing todo as done**

Para que esse teste passe, não deve ser possível a mudança da propriedade `done` de um *todo* que não exista e retornar uma resposta contendo um status `404` e um json no seguinte formato: 

```jsx
{
	error: 'Mensagem do erro'
}
```

- **Should be able to delete a todo**

Para que esse teste passe, na rota DELETE `/todos/:id` deve-se permitir que um *todo* seja excluído usando o `id` passado na rota. O retorno deve ser apenas um status `204` que representa uma resposta sem conteúdo.

- **Should not be able to delete a non existing todo**

Para que esse teste passe, não deve ser possível excluir um *todo* que não exista e retornar uma resposta contendo um status `404` e um json no seguinte formato:

```jsx
{
	error: 'Mensagem do erro'
}
```

<br/><br/>

## 💻 Tecnologias

Essa aplicacão foi desenvolvida com as seguintes tecnologias:

- [Express](https://expressjs.com/pt-br/)
- [Jest](https://jestjs.io/)
<br/><br/>

## 🔸 Como executar

- Clone o repositório
- Instale as dependências com `yarn`
- Inicie o servidor com `yarn test`
- Utilize o Insomnia ou algum outro programa semelhante para realizar as requisicões.

---

<p align="center">🚀 Desenvolvido durante o bootcamp da Rockeseat Ignite - Trilha Nodejs 🚀<p>