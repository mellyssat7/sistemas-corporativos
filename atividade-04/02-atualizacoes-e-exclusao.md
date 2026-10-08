# Atividade 04 — Atualizações e Exclusão de Documentos

## Encontro 16 — Prática de revisão: MongoDB com Docker

### 1. Atualização do chamado "Monitor sem imagem"

Após a realização das consultas, foi necessário atualizar o chamado com título `Monitor sem imagem`.

A atualização foi realizada utilizando `updateOne()`, permitindo alterar somente o documento correspondente ao filtro informado.

Foram realizadas três alterações no documento:

* alteração do `status` para `em_atendimento`;
* alteração da `prioridade` para `media`;
* inclusão do marcador `urgente`.

Também foi acrescentado um novo registro ao campo `historico`, permitindo manter o registro da alteração realizada.

A operação utilizada foi:

```javascript
db.chamados.updateOne(
  { titulo: "Monitor sem imagem" },
  {
    $set: {
      status: "em_atendimento",
      prioridade: "media"
    },
    $addToSet: {
      marcadores: "urgente"
    },
    $push: {
      historico: {
        acao: "Atualização do chamado",
        data: new Date()
      }
    }
  }
)
```

O operador `$set` foi utilizado para definir novos valores para os campos `status` e `prioridade`.

O operador `$addToSet` foi utilizado para adicionar o marcador `urgente` ao array `marcadores`, evitando a inclusão de um valor duplicado.

O operador `$push` foi utilizado para adicionar um novo elemento ao array `historico`.

---

### 2. Verificação da atualização

Após a atualização, o chamado foi consultado para verificar as alterações realizadas.

A consulta utilizada foi:

```javascript
db.chamados.findOne({
  titulo: "Monitor sem imagem"
})
```

A consulta permitiu verificar que o chamado passou a apresentar:

```text
status: em_atendimento
prioridade: media
```

Também foi possível verificar a presença do marcador `urgente` e o novo registro no histórico.

---

### 3. Inserção de chamado temporário para teste de exclusão

Antes de testar a exclusão de documentos, foi inserido um chamado temporário.

O objetivo foi realizar a exclusão sobre um documento de teste, evitando remover acidentalmente um chamado utilizado nas demais consultas da atividade.

A inserção foi realizada com `insertOne()`:

```javascript
db.chamados.insertOne({
  titulo: "Chamado temporário para exclusão",
  categoria: "teste",
  prioridade: "baixa",
  status: "aberto",
  solicitante: {
    nome: "Teste",
    setor: "TI"
  },
  marcadores: ["teste"],
  historico: [],
  criadoEm: new Date()
})
```

O documento foi criado exclusivamente para demonstrar uma operação segura de exclusão.

---

### 4. Consulta do chamado temporário antes da exclusão

Antes de excluir o documento, foi realizada uma consulta utilizando um filtro específico:

```javascript
db.chamados.findOne({
  titulo: "Chamado temporário para exclusão"
})
```

Essa etapa permitiu confirmar que o documento existia e que o filtro utilizado para a exclusão identificava o documento correto.

A conferência antes da exclusão é importante para reduzir o risco de remover um documento diferente do pretendido.

---

### 5. Exclusão do chamado temporário

Depois da confirmação da existência do documento, foi realizada a exclusão utilizando `deleteOne()`:

```javascript
db.chamados.deleteOne({
  titulo: "Chamado temporário para exclusão"
})
```

O resultado da operação indicou:

```text
deletedCount: 1
```

O valor `1` em `deletedCount` indica que um documento foi removido.

---

### 6. Verificação da exclusão

Após a operação de exclusão, o documento temporário deixou de fazer parte da coleção.

A operação foi realizada com um filtro específico pelo título do chamado, evitando uma exclusão sem critério.

O chamado temporário foi utilizado somente para demonstrar o funcionamento de `deleteOne()` e não fazia parte dos quatro chamados utilizados como base da atividade.

---

## Resumo do bloco

Neste segundo bloco da atividade foram trabalhados:

* atualização de documentos com `updateOne()`;
* alteração de campos com `$set`;
* inclusão de valores em arrays com `$addToSet`;
* inclusão de registros no histórico com `$push`;
* consulta de um documento específico com `findOne()`;
* inserção de um documento temporário para teste;
* exclusão de documentos com `deleteOne()`;
* utilização de filtros específicos para operações de alteração e exclusão;
* verificação do resultado da exclusão por meio de `deletedCount`.

O bloco demonstra como realizar alterações e exclusões de documentos de forma controlada utilizando os operadores e métodos trabalhados no MongoDB.
