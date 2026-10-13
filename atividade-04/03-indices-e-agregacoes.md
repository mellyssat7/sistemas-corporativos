# Atividade 04 — Índices e Agregações

## Encontro 16 — Prática de revisão: MongoDB com Docker

### 1. Análise do plano de execução antes do índice

Foi analisado o plano de execução de uma consulta que busca chamados com status `aberto` e categoria `equipamento`.

Consulta utilizada:

    db.chamados.find({
      status: "aberto",
      categoria: "equipamento"
    }).explain("executionStats")

O plano apresentou os seguintes resultados:

    stage: COLLSCAN
    nReturned: 0
    totalKeysExamined: 0
    totalDocsExamined: 4

O estágio `COLLSCAN` indica que o MongoDB percorreu os documentos da coleção para verificar quais atendiam aos critérios da consulta.

Foram examinados quatro documentos. Nenhum foi retornado porque, após a atualização realizada anteriormente, não havia um chamado que atendesse simultaneamente aos dois filtros.

### 2. Criação de índice composto

Para auxiliar consultas que utilizam os campos `status` e `categoria`, foi criado um índice composto.

Comando utilizado:

    db.chamados.createIndex({
      status: 1,
      categoria: 1
    })

O MongoDB retornou:

    status_1_categoria_1

O índice reúne os campos `status` e `categoria`, ambos em ordem crescente. Ele permite que o MongoDB utilize uma estrutura de índice para localizar documentos que correspondam aos filtros, podendo evitar uma varredura completa da coleção.

### 3. Verificação dos índices da coleção

Para verificar os índices existentes, foi executado:

    db.chamados.getIndexes()

A coleção possui o índice padrão `_id_` e o índice composto `status_1_categoria_1`.

O índice `_id_` é criado automaticamente pelo MongoDB. O índice composto foi criado para auxiliar consultas que utilizam `status` e `categoria`.

### 4. Análise do plano de execução após a criação do índice

A mesma consulta foi executada novamente após a criação do índice:

    db.chamados.find({
      status: "aberto",
      categoria: "equipamento"
    }).explain("executionStats")

O plano apresentou:

    stage: FETCH
    inputStage:
      stage: IXSCAN
      indexName: status_1_categoria_1

    nReturned: 0
    totalKeysExamined: 0
    totalDocsExamined: 0

O estágio `IXSCAN` indica que o MongoDB utilizou o índice `status_1_categoria_1`. O estágio `FETCH` representa a etapa de obtenção dos documentos encontrados pelo índice.

Nenhum documento foi retornado porque atualmente não existe um chamado que corresponda simultaneamente aos dois filtros. Isso não significa que o índice falhou: o plano de execução confirma que ele foi utilizado.

### 5. Comparação dos planos de execução

Antes da criação do índice, o plano apresentou:

    stage: COLLSCAN
    totalDocsExamined: 4
    totalKeysExamined: 0
    nReturned: 0

Depois da criação do índice, o plano apresentou:

    stage: IXSCAN
    indexName: status_1_categoria_1
    totalDocsExamined: 0
    totalKeysExamined: 0
    nReturned: 0

A comparação demonstra que o MongoDB passou de `COLLSCAN`, que percorre os documentos da coleção, para `IXSCAN`, que utiliza o índice.

Esses resultados correspondem à consulta executada e ao estado atual dos dados. Os índices podem reduzir o trabalho necessário para localizar documentos, dependendo dos filtros e dos índices disponíveis.

### 6. Quantidade de chamados por categoria

Foi utilizada uma agregação para contar os chamados existentes em cada categoria.

Comando utilizado:

    db.chamados.aggregate([
      {
        $group: {
          _id: "$categoria",
          quantidade: { $sum: 1 }
        }
      }
    ])

O resultado foi:

    equipamento: 2
    software: 1
    acesso: 1

O estágio `$group` agrupa os documentos pelo campo `categoria`. O operador `$sum: 1` contabiliza um documento por vez dentro de cada grupo.

Assim, a coleção contém dois chamados de equipamento, um de software e um de acesso.

### 7. Quantidade de chamados não fechados por categoria

Foi realizada uma agregação para contar os chamados cujo status é diferente de `fechado`.

Comando utilizado:

    db.chamados.aggregate([
      {
        $match: {
          status: { $ne: "fechado" }
        }
      },
      {
        $group: {
          _id: "$categoria",
          quantidade: { $sum: 1 }
        }
      }
    ])

O resultado foi:

    equipamento: 1
    software: 1
    acesso: 1

O estágio `$match` seleciona os chamados cujo status é diferente de `fechado`. Em seguida, `$group` agrupa os documentos selecionados por categoria, enquanto `$sum: 1` contabiliza os documentos de cada grupo.

O resultado indica que existe um chamado não fechado em cada uma das três categorias.

## Resumo do bloco

Nesta etapa da atividade foram trabalhados:

- análise do plano de execução com `explain("executionStats")`;
- identificação dos estágios `COLLSCAN` e `IXSCAN`;
- criação de índice composto com `createIndex()`;
- verificação dos índices com `getIndexes()`;
- comparação dos planos de execução antes e depois da criação do índice;
- utilização de `$match` para filtrar documentos;
- agrupamento com `$group`;
- contagem com `$sum`;
- análise da quantidade de chamados por categoria;
- análise dos chamados que ainda não estão fechados.

A prática demonstra como os índices podem auxiliar nas consultas e como o recurso de agregação do MongoDB permite resumir e analisar os documentos armazenados.

