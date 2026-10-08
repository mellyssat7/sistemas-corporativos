# Atividade 04 — Modelagem e Consultas com MongoDB

## Encontro 16 — Prática de revisão: MongoDB com Docker

### 1. Modelagem dos documentos

A atividade utiliza como cenário um sistema de chamados internos de suporte.

A coleção `chamados` foi planejada para armazenar documentos contendo informações gerais do chamado e dados específicos relacionados ao atendimento.

Estrutura principal utilizada:

* `titulo`: título ou descrição resumida do chamado.
* `categoria`: categoria do chamado.
* `prioridade`: prioridade do atendimento.
* `status`: situação atual do chamado.
* `solicitante`: informações do solicitante.
* `detalhes`: informações específicas relacionadas à categoria.
* `marcadores`: conjunto de marcadores associados ao chamado.
* `historico`: registros das alterações realizadas no chamado.
* `criadoEm`: data de criação do chamado.

A estrutura dos documentos é flexível, permitindo que documentos diferentes possuam campos específicos conforme a necessidade do chamado.

---

### 2. Inserção dos chamados

Foram inseridos documentos na coleção `chamados`, representando diferentes situações de atendimento.

Exemplo de estrutura utilizada:

```javascript
db.chamados.insertOne({
  titulo: "Monitor sem imagem",
  categoria: "equipamento",
  prioridade: "alta",
  status: "aberto",
  solicitante: {
    nome: "Melyssa",
    setor: "Financeiro"
  },
  detalhes: {
    equipamento: "Monitor",
    patrimonio: "MON-001"
  },
  marcadores: ["hardware"],
  historico: [],
  criadoEm: new Date()
})
```

Também foram inseridos outros chamados com diferentes categorias, prioridades, setores e marcadores para possibilitar a realização das consultas propostas na atividade.

---

### 3. Consulta de todos os chamados

Para visualizar todos os documentos existentes na coleção, foi utilizada a consulta:

```javascript
db.chamados.find()
```

A consulta retornou os chamados cadastrados na coleção.

A coleção utilizada no exercício possui inicialmente **4 chamados**.

---

### 4. Contagem dos chamados

Para verificar a quantidade de documentos existentes na coleção:

```javascript
db.chamados.countDocuments()
```

Resultado:

```text
4
```

A operação `countDocuments()` permite obter a quantidade de documentos que atendem ao critério informado. Quando nenhum filtro é fornecido, a operação contabiliza todos os documentos da coleção.

---

### 5. Chamados abertos e de alta prioridade

Para localizar chamados que possuem simultaneamente `status` igual a `aberto` e `prioridade` igual a `alta`:

```javascript
db.chamados.find({
  status: "aberto",
  prioridade: "alta"
})
```

A consulta utiliza mais de um campo no filtro. Nesse caso, os documentos precisam atender às duas condições.

---

### 6. Chamados de prioridade alta ou média

Para consultar chamados cuja prioridade seja `alta` ou `media`:

```javascript
db.chamados.find({
  prioridade: {
    $in: ["alta", "media"]
  }
})
```

O operador `$in` permite verificar se o valor de um campo pertence a uma lista de valores possíveis.

---

### 7. Chamados do setor Financeiro

Para localizar chamados cujo solicitante pertence ao setor `Financeiro`:

```javascript
db.chamados.find({
  "solicitante.setor": "Financeiro"
})
```

A consulta utiliza a notação com ponto (`.`) para acessar o campo `setor`, que está armazenado dentro do documento aninhado `solicitante`.

---

### 8. Chamados com o marcador `hardware`

Para localizar chamados que possuem o marcador `hardware`:

```javascript
db.chamados.find({
  marcadores: "hardware"
})
```

Como `marcadores` é um array, o MongoDB consegue localizar documentos nos quais esse valor esteja presente no conjunto de marcadores.

---

### 9. Projeção dos campos dos chamados

Para retornar somente alguns campos dos documentos:

```javascript
db.chamados.find(
  {},
  {
    titulo: 1,
    prioridade: 1,
    status: 1
  }
)
```

A projeção permite controlar quais campos serão apresentados no resultado da consulta.

Os campos selecionados foram:

* `titulo`
* `prioridade`
* `status`

---

### 10. Chamados ordenados por prioridade

Para ordenar os chamados pela prioridade:

```javascript
db.chamados.find().sort({
  prioridade: 1
})
```

O método `sort()` define a ordem dos resultados.

O valor `1` representa ordenação crescente.

---

### 11. Chamados ordenados por prioridade com limite de 2 resultados

Para ordenar os chamados e retornar somente os dois primeiros resultados:

```javascript
db.chamados.find()
  .sort({
    prioridade: 1
  })
  .limit(2)
```

O método `limit(2)` restringe a quantidade de documentos retornados pela consulta.

A combinação de `sort()` e `limit()` permite obter uma quantidade limitada de resultados após aplicar uma determinada ordenação.

---

## Resumo do bloco

Neste primeiro bloco da atividade foram trabalhados:

* modelagem de documentos no MongoDB;
* inserção de documentos;
* consulta de documentos;
* contagem de documentos;
* filtros com múltiplos campos;
* operador `$in`;
* acesso a campos aninhados;
* consulta de valores presentes em arrays;
* projeção de campos;
* ordenação com `sort()`;
* limitação de resultados com `limit()`.

Essas operações correspondem à etapa de revisão prática de modelagem, inserção e consultas utilizando MongoDB.

