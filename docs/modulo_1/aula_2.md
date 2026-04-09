### 1. Conceitos Básicos

*   **Tabela:** Uma coleção de dados organizados que representa uma entidade ou relacionamento. Exemplo: A tabela `Medico`.
*   **Linha (Tupla):** Representa um fato ou registro individual dentro da tabela. Exemplo: A linha contendo `1`, `Paulo Rangel`, `pr@eee.ccc.br`, `23453`, `1`.
*   **Coluna (Atributo):** Define o tipo de dado e o significado dos valores em cada linha. Exemplo: A coluna `valor` na tabela `Consulta`.
*   **PK (Chave Primária - Primary Key):** Atributo ou conjunto de atributos que identifica exclusivamente cada linha em uma tabela. Exemplo: O campo `codigo` na tabela `Medico`.
*   **FK (Chave Estrangeira - Foreign Key):** Atributo que referencia a Chave Primária de outra tabela para estabelecer um relacionamento. Exemplo: O campo `codmed` na tabela `Consulta` mapeia para a tabela `Medico`.
*   **NULL:** Representa um valor desconhecido, ausente ou não aplicável. Exemplo: O campo `codconv` em algumas linhas da tabela `Consulta` possui o valor NULL.

---

### 2. Ordem de Execução das Cláusulas

A ordem em que você escreve uma consulta SQL (`SELECT` -> `FROM` -> `WHERE`...) não é a mesma ordem em que o motor do banco de dados a processa. A execução real segue esta sequência:

1.  **FROM:** Localiza e carrega as tabelas necessárias.
2.  **WHERE:** Filtra as linhas brutas com base em condições.
3.  **GROUP BY:** Agrupa as linhas filtradas baseadas em atributos comuns.
4.  **HAVING:** Filtra os grupos inteiros criados na etapa anterior.
5.  **SELECT:** Extrai (projeta) as colunas específicas solicitadas.
6.  **ORDER BY:** Classifica o resultado final antes de exibi-lo.

**Por que difere?** O banco de dados precisa primeiro saber de onde vêm os dados (`FROM`), aplicar os filtros individuais (`WHERE`) e realizar agrupamentos antes de poder projetar as colunas finais (`SELECT`) e ordená-las (`ORDER BY`). Seguir esse fluxo lógico otimiza a recuperação e processamento.

---

### 3. Aliases de Tabela

Aliases (apelidos) são nomes alternativos e temporários dados a tabelas (ou colunas) durante a consulta usando a sintaxe `FROM Tabela Apelido` ou `FROM Tabela AS Apelido`. 

**Por que usar?** Eles economizam digitação e, o mais importante, resolvem ambiguidades quando você junta tabelas que possuem colunas com nomes idênticos ou quando junta uma tabela a si mesma.

```sql
-- "M" é o alias para Medico e "C" é o alias para Consulta
SELECT M.nome, C.data
FROM Medico M
JOIN Consulta C ON M.codigo = C.codmed;
```

---

### 4. WHERE vs HAVING

*   **WHERE:** É executado *antes* do agrupamento e serve para filtrar linhas individuais. Não pode conter funções de agregação (como `SUM` ou `COUNT`).
*   **HAVING:** É executado *depois* do agrupamento (`GROUP BY`) e serve para filtrar os grupos gerados, frequentemente utilizando funções de agregação.

```sql
SELECT M.nome, SUM(C.valor) as total_arrecadado
FROM Medico M
JOIN Consulta C ON M.codigo = C.codmed
WHERE M.codcid = 1  -- Filtra médicos de uma cidade específica (linha a linha)
GROUP BY M.nome
HAVING SUM(C.valor) > 100; -- Filtra apenas os grupos (médicos) com soma > 100
```

---

### 5. DISTINCT: Evitando Duplicatas nos JOINs

**Por que JOINs geram duplicatas?** Ao usar JOIN entre uma tabela e outra (relacionamento 1:N), a linha da primeira tabela é repetida (redundância controlada) para cada correspondência encontrada na segunda tabela. Por exemplo, se o médico "Paulo Rangel" fez duas consultas, seu nome aparecerá duas vezes no resultado.

**Como o DISTINCT resolve:** A palavra-chave `DISTINCT` instrui o banco de dados a eliminar as linhas duplicadas do resultado final, garantindo que os valores apareçam apenas uma vez.

```sql
-- SEM DISTINCT: Retorna Paulo Rangel e Luara dos Santos múltiplas vezes
SELECT M.nome 
FROM Medico M 
JOIN Consulta C ON M.codigo = C.codmed 
WHERE M.codcid = 1;

-- COM DISTINCT: Retorna cada nome apenas uma vez
SELECT DISTINCT M.nome 
FROM Medico M 
JOIN Consulta C ON M.codigo = C.codmed 
WHERE M.codcid = 1;
```

| nome |
| :--- |
| Paulo Rangel |
| Luara dos Santos |

---

### 6. INNER JOIN

Retorna apenas as linhas que possuem correspondência em **ambas** as tabelas interligadas. Se um médico não fez nenhuma consulta, ele é excluído do resultado.

```sql
-- Traz apenas médicos que realizaram consultas com valor > 100
SELECT DISTINCT M.nome 
FROM Medico M 
JOIN Consulta C ON M.codigo = C.codmed 
WHERE C.valor > 100;
```

| nome |
| :--- |
| Luara dos Santos |
| José Paulo O |

---

### 7. LEFT JOIN

Retorna **todas** as linhas da tabela à esquerda (`Medico`), independentemente de haver correspondência na tabela à direita (`Consulta`). Onde não houver correspondência, o banco preenche os dados da tabela da direita com `NULL`.

```sql
-- Retorna todos os médicos da codcid = 1.
-- Luan dos Santos e Carolina Pereira aparecem mesmo não tendo consultas (seus dados de C seriam NULL).
SELECT DISTINCT M.nome 
FROM Medico M 
LEFT JOIN Consulta C ON M.codigo = C.codmed 
WHERE M.codcid = 1;
```

| nome |
| :--- |
| Paulo Rangel |
| Luara dos Santos |
| Luan dos Santos |
| Carolina Pereira |

---

### 8. RIGHT JOIN

Retorna **todas** as linhas da tabela à direita (`Consulta`), preenchendo os dados da tabela à esquerda (`Medico`) com `NULL` caso não haja correspondência. 

```sql
-- Retorna apenas os médicos atrelados a alguma consulta e que são da codcid = 1.
SELECT DISTINCT M.nome 
FROM Medico M 
RIGHT JOIN Consulta C ON M.codigo = C.codmed 
WHERE M.codcid = 1;
```

| nome |
| :--- |
| Paulo Rangel |
| Luara dos Santos |

---

### 9. Tabela Comparativa de JOINs

| Tipo de JOIN | O que retorna? | Quando usar? | Armadilhas Comuns |
| :--- | :--- | :--- | :--- |
| **INNER JOIN** | Apenas as interseções. Exige correspondência nas duas tabelas. | Quando você só quer dados completos (ex: apenas médicos que realizaram consultas). | Perda de informações importantes se a existência na 2ª tabela não for obrigatória. |
| **LEFT JOIN** | Tudo da tabela Esquerda + correspondências da Direita (ou NULL). | Quando a tabela principal é a primeira e você não quer perder seus registros (ex: relatório de todos os médicos e suas consultas, se houver). | Filtrar no `WHERE` por colunas da tabela da Direita pode anular o efeito do LEFT JOIN (veja abaixo). |
| **RIGHT JOIN** | Tudo da tabela Direita + correspondências da Esquerda (ou NULL). | Mesmo cenário do LEFT JOIN, mas com a ordem de leitura das tabelas invertida. | Menos usado por convenção, pode confundir a leitura estrutural da query. |

---

### 10. Erros Comuns

*   **Filtro WHERE anulando um LEFT JOIN:**
    Se você usa um `LEFT JOIN` para garantir que registros sem correspondência apareçam (com NULL na tabela da direita), mas logo em seguida coloca uma condição `WHERE` exigindo um valor na tabela da direita (ex: `WHERE C.valor > 50`), o banco eliminará as linhas NULL. Isso transforma seu `LEFT JOIN` em um `INNER JOIN` na prática. 
    *Solução:* Mova condições sobre tabelas secundárias para a cláusula `ON` da junção.
*   **A armadilha do `NULL = NULL`:**
    O SQL utiliza lógica de três valores (Verdadeiro, Falso e Desconhecido). Como o NULL representa um valor desconhecido, comparar `NULL = NULL` ou `NULL <> NULL` retorna um resultado "Desconhecido" e a linha é rejeitada pela cláusula WHERE. 
    *Solução:* Utilize sempre `IS NULL` ou `IS NOT NULL`.
*   **Duplicatas sem DISTINCT:**
    Ao conectar entidades de relacionamentos `1:N` (como 1 Médico para N Consultas), a projeção focada na tabela "1" produzirá cópias visuais se não utilizarmos o `DISTINCT` ou agruparmos os dados, pois a tupla é propagada multiplicativamente.