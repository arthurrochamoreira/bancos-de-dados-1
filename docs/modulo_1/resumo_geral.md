# Guia SQL — Do Zero às Consultas Avançadas

> **Como usar este guia:** Cada seção é autocontida. Você pode chegar aqui por qualquer caminho — de uma busca, de um link, ou lendo do início. Cada tópico explica seu próprio contexto.

---

## Índice de Navegação

| Seção | O que você vai aprender |
|-------|------------------------|
| [1. O que é um banco de dados relacional](#1-o-que-é-um-banco-de-dados-relacional) | Conceito, tabelas, linhas e colunas |
| [2. Terminologia formal do modelo relacional](#2-terminologia-formal-do-modelo-relacional) | Relação, tupla, atributo, domínio |
| [3. Chaves: primária, candidata e estrangeira](#3-chaves-primária-candidata-e-estrangeira) | Identificar e relacionar dados |
| [4. Restrições e integridade](#4-restrições-e-integridade) | NOT NULL, unicidade, integridade referencial |
| [5. O que é SQL](#5-o-que-é-sql) | A linguagem de consulta |
| [6. A ordem de execução das cláusulas SQL](#6-a-ordem-de-execução-das-cláusulas-sql) | FROM → WHERE → GROUP BY → HAVING → SELECT → ORDER BY |
| [7. SELECT — consultando dados](#7-select--consultando-dados) | Obter colunas de uma tabela |
| [8. WHERE — filtrando linhas](#8-where--filtrando-linhas) | Operadores condicionais e lógicos |
| [9. DISTINCT — eliminando duplicatas](#9-distinct--eliminando-duplicatas) | Quando e como usar |
| [10. NULL — valores ausentes](#10-null--valores-ausentes) | O que é NULL, IS NULL, IS NOT NULL |
| [11. Operadores de texto — LIKE, %, _](#11-operadores-de-texto--like--_) | Buscas por padrão em strings |
| [12. Operadores matemáticos em SQL](#12-operadores-matemáticos-em-sql) | Cálculos dentro das consultas |
| [13. ORDER BY — ordenando resultados](#13-order-by--ordenando-resultados) | ASC e DESC |
| [14. JOIN — cruzando tabelas](#14-join--cruzando-tabelas) | INNER, LEFT, RIGHT JOIN com exemplos reais |
| [15. A diferença entre INNER, LEFT e RIGHT JOIN](#15-a-diferença-entre-inner-left-e-right-join) | Quando cada um retorna linhas diferentes |
| [16. GROUP BY e HAVING — agrupando e filtrando grupos](#16-group-by-e-having--agrupando-e-filtrando-grupos) | Agregações com condições |
| [17. Consulta completa — exemplo integrador](#17-consulta-completa--exemplo-integrador) | Tudo junto em um só exemplo |

---

## 1. O que é um banco de dados relacional

Você está começando do zero, ou quer entender por que SQL existe antes de sair digitando código.

Um **banco de dados** é uma coleção organizada de dados armazenados e gerenciados por um software chamado **SGBD** (Sistema Gerenciador de Banco de Dados — ex: MySQL, PostgreSQL, SQL Server, Oracle).

No modelo **relacional**, os dados são organizados em **tabelas** (também chamadas de *relações*). Cada tabela armazena informações sobre um único assunto — pacientes, produtos, pedidos, etc.

**Exemplo mental:** imagine uma clínica médica.
- Uma tabela `Medico` guarda dados dos médicos.
- Uma tabela `Consulta` guarda os atendimentos realizados.
- Essas duas tabelas se relacionam: cada consulta tem um médico responsável.

Um banco de dados pode conter dezenas ou centenas de tabelas que se relacionam entre si.

**→ Próximos passos:** [Terminologia formal](#2-terminologia-formal-do-modelo-relacional) | [O que é SQL](#5-o-que-é-sql)

---

## 2. Terminologia formal do modelo relacional

Se você veio de um livro acadêmico ou de uma aula de banco de dados, vai encontrar esses termos em vez de "tabela", "linha" e "coluna".

O modelo relacional tem uma linguagem matemática própria. Veja a correspondência:

| Termo formal | Equivalente prático |
|--------------|---------------------|
| **Relação** | Tabela |
| **Tupla** | Linha (uma linha da tabela) |
| **Atributo** | Coluna (cabeçalho da tabela) |
| **Domínio** | Conjunto de valores válidos para uma coluna |
| **Grau** | Número de atributos (colunas) de uma relação |
| **Cardinalidade** | Número de tuplas (linhas) de uma relação |

**Domínio** é o conjunto de valores possíveis para um atributo. Por exemplo:
- O domínio de `Sexo` pode ser `{'M', 'F'}`.
- O domínio de `Idade` pode ser todos os inteiros positivos.
- O domínio de `CPF` pode ser strings de 11 dígitos.

**Importante:** numa relação, cada valor de tupla deve ser **atômico** — indivisível. Isso é chamado de *pressuposto da primeira forma normal* (1FN). Não existe "lista dentro de uma célula" no modelo relacional puro.

**Valores NULL** representam dados desconhecidos ou inaplicáveis. Por exemplo, um médico autônomo pode não ter e-mail cadastrado — esse campo fica NULL.

**→ Próximos passos:** [Chaves](#3-chaves-primária-candidata-e-estrangeira) | [NULL em detalhe](#10-null--valores-ausentes)

---

## 3. Chaves: primária, candidata e estrangeira

Você precisa entender como as tabelas se identificam e se relacionam. As chaves são o mecanismo central para isso.

### Superchave

Qualquer conjunto de atributos que identifica unicamente cada tupla de uma relação. Toda relação tem pelo menos uma superchave: o conjunto de todos os seus atributos.

### Chave candidata

Uma superchave **mínima** — da qual não podemos remover nenhum atributo sem perder a unicidade.

**Exemplo:** na tabela `Carro`, tanto `Placa` quanto `Numero_chassi` identificam cada carro unicamente. Ambas são chaves candidatas.

### Chave primária (PK — *Primary Key*)

A chave candidata escolhida para identificar oficialmente cada tupla. Convenção: sublinhada nos diagramas.

- **Nunca pode ser NULL** (restrição de integridade de entidade).
- **Nunca pode se repetir** na mesma tabela.

```
Medico (codigo, nome, email, CRM, codcid)
          ↑
     chave primária
```

### Chave estrangeira (FK — *Foreign Key*)

Um atributo (ou conjunto de atributos) em uma tabela que referencia a chave primária de outra tabela. É o mecanismo que cria o relacionamento entre tabelas.

```
Consulta (data, hora, codpac, codmed, valor, codconv)
                              ↑
              chave estrangeira → referencia Medico.codigo
```

**Regra:** o valor de uma chave estrangeira deve corresponder a um valor existente na tabela referenciada — ou ser NULL. Isso é a **restrição de integridade referencial**.

**→ Próximos passos:** [Restrições](#4-restrições-e-integridade) | [JOIN — cruzando tabelas via FK](#14-join--cruzando-tabelas)

---

## 4. Restrições e integridade

Você quer entender por que o banco de dados rejeita certas inserções ou atualizações.

O modelo relacional define quatro categorias de restrições que garantem a qualidade dos dados:

**1. Restrições de domínio**
Cada valor inserido numa coluna deve pertencer ao domínio definido para aquela coluna. Tentar inserir texto numa coluna numérica viola essa restrição.

**2. Restrições de chave**
Não podem existir duas tuplas com o mesmo valor de chave primária na mesma tabela.

**3. Restrição de NOT NULL**
Atributos marcados como NOT NULL não aceitam valores ausentes. A chave primária *sempre* é implicitamente NOT NULL.

**4. Integridade referencial**
Um valor de chave estrangeira deve existir como chave primária na tabela referenciada. Você não pode inserir uma consulta referenciando um médico com código inexistente.

**Por que isso importa na prática?**
Sem essas restrições, o banco aceitaria dados inconsistentes: consultas sem médico, produtos sem preço, pedidos para clientes inexistentes.

**→ Próximos passos:** [Chaves](#3-chaves-primária-candidata-e-estrangeira) | [NULL](#10-null--valores-ausentes)

---

## 5. O que é SQL

Você sabe que precisa aprender SQL, mas não sabe exatamente o que ela é.

**SQL** (*Structured Query Language* — Linguagem de Consulta Estruturada) é a linguagem padrão para interagir com bancos de dados relacionais. Ela permite:

- **Consultar dados** (SELECT) — perguntar ao banco o que você quer ver
- **Inserir dados** (INSERT) — adicionar novas linhas
- **Atualizar dados** (UPDATE) — modificar linhas existentes
- **Deletar dados** (DELETE) — remover linhas
- **Criar estruturas** (CREATE TABLE, ALTER TABLE) — definir tabelas e colunas

Este guia foca nas **consultas** (SELECT), que é onde a maioria das pessoas começa e onde fica mais tempo.

SQL é **declarativa**: você descreve *o que* quer, não *como* buscar. O banco de dados decide a estratégia de execução.

```sql
-- Você diz O QUÊ quer:
SELECT nome FROM Medico WHERE codcid = 1;

-- O banco decide COMO buscar (índice, varredura, etc.)
```

**→ Próximos passos:** [Ordem de execução das cláusulas](#6-a-ordem-de-execução-das-cláusulas-sql) | [SELECT](#7-select--consultando-dados)

---

## 6. A ordem de execução das cláusulas SQL

Você escreve SQL numa ordem, mas o banco executa em outra. Entender isso explica muitos erros e comportamentos inesperados.

A ordem em que você *escreve* uma consulta SQL **não** é a ordem em que ela é *executada*.

### Ordem de escrita (sintaxe)

```sql
SELECT   <colunas>
FROM     <tabela(s)>
WHERE    <condição sobre linhas>
GROUP BY <colunas de agrupamento>
HAVING   <condição sobre grupos>
ORDER BY <colunas de ordenação>
```

### Ordem de execução (lógica)

| Passo | Cláusula | O que acontece |
|-------|----------|----------------|
| 1º | `FROM` | Identifica a(s) tabela(s) fonte |
| 2º | `WHERE` | Filtra linhas individuais |
| 3º | `GROUP BY` | Agrupa as linhas restantes |
| 4º | `HAVING` | Filtra grupos formados |
| 5º | `SELECT` | Seleciona as colunas a exibir |
| 6º | `ORDER BY` | Ordena o resultado final |

**Consequência prática:** você não pode usar um alias definido no SELECT dentro do WHERE, porque WHERE é executado *antes* do SELECT.

```sql
-- ERRADO: 'total' não existe ainda quando WHERE é avaliado
SELECT valor * 2 AS total
FROM Consulta
WHERE total > 200;

-- CORRETO: repita a expressão no WHERE
SELECT valor * 2 AS total
FROM Consulta
WHERE valor * 2 > 200;
```

**→ Próximos passos:** [SELECT](#7-select--consultando-dados) | [WHERE](#8-where--filtrando-linhas) | [GROUP BY e HAVING](#16-group-by-e-having--agrupando-e-filtrando-grupos)

---

## 7. SELECT — consultando dados

Você quer extrair dados de uma tabela. O SELECT é o ponto de partida de toda consulta.

### Ver a tabela inteira

```sql
SELECT * FROM Medico;
```

O `*` significa "todas as colunas". Útil para explorar, mas evite em produção — traz colunas desnecessárias e pode ser lento.

### Selecionar colunas específicas

```sql
SELECT nome, email FROM Medico;
```

Resultado: somente as colunas `nome` e `email`, todas as linhas.

### Usar aliases (apelidos)

```sql
SELECT nome AS medico, CRM AS registro FROM Medico;
```

Aliases renomeiam colunas no resultado. Não alteram a tabela original.

### Combinar colunas com expressões

```sql
SELECT nome, valor * 1.1 AS valor_com_taxa FROM Consulta;
```

### Qualificar colunas com o nome da tabela

Quando você cruza múltiplas tabelas, use `Tabela.coluna` para evitar ambiguidade:

```sql
SELECT Medico.nome, Consulta.valor
FROM Medico
JOIN Consulta ON Medico.codigo = Consulta.codmed;
```

Você também pode usar apelidos de tabela (aliases de tabela) para encurtar:

```sql
SELECT M.nome, C.valor
FROM Medico M
JOIN Consulta C ON M.codigo = C.codmed;
```

**→ Próximos passos:** [WHERE](#8-where--filtrando-linhas) | [DISTINCT](#9-distinct--eliminando-duplicatas) | [JOIN](#14-join--cruzando-tabelas)

---

## 8. WHERE — filtrando linhas

Você não quer *todas* as linhas da tabela — quer apenas as que satisfazem uma condição.

O WHERE funciona como um filtro aplicado linha por linha. Só passam as linhas onde a condição é verdadeira.

### Operadores de comparação

| Operador | Significado | Exemplo |
|----------|-------------|---------|
| `=` | Igual | `WHERE valor = 100` |
| `!=` ou `<>` | Diferente | `WHERE valor != 100` |
| `>` | Maior que | `WHERE valor > 100` |
| `>=` | Maior ou igual | `WHERE valor >= 100` |
| `<` | Menor que | `WHERE valor < 50` |
| `<=` | Menor ou igual | `WHERE valor <= 50` |

### Operadores lógicos

```sql
-- AND: ambas as condições devem ser verdadeiras
SELECT * FROM Consulta WHERE valor > 50 AND codmed = 3;

-- OR: pelo menos uma condição deve ser verdadeira
SELECT * FROM Consulta WHERE valor > 100 OR codmed = 1;

-- NOT: inverte a condição
SELECT * FROM Medico WHERE NOT codcid = 1;
```

### BETWEEN — intervalo

```sql
SELECT * FROM Consulta WHERE valor BETWEEN 50 AND 100;
-- equivale a: WHERE valor >= 50 AND valor <= 100
```

### IN — lista de valores

```sql
SELECT * FROM Medico WHERE codcid IN (1, 4, 5);
-- equivale a: WHERE codcid = 1 OR codcid = 4 OR codcid = 5
```

**→ Próximos passos:** [NULL e IS NULL](#10-null--valores-ausentes) | [LIKE e padrões de texto](#11-operadores-de-texto--like--_) | [Ordem de execução](#6-a-ordem-de-execução-das-cláusulas-sql)

---

## 9. DISTINCT — eliminando duplicatas

Sua consulta está retornando linhas repetidas e você quer apenas valores únicos.

Quando você faz JOIN entre tabelas, ou quando múltiplas linhas têm o mesmo valor na coluna que você quer ver, o resultado pode ter repetições.

### Sem DISTINCT — com repetições

```sql
SELECT M.nome
FROM Medico M
JOIN Consulta C ON M.codigo = C.codmed
WHERE M.codcid = 1;
```

Resultado (cada consulta gera uma linha):
```
nome
-----------
Paulo Rangel
Paulo Rangel
Luara dos Santos
Luara dos Santos
Luara dos Santos
```

### Com DISTINCT — valores únicos

```sql
SELECT DISTINCT M.nome
FROM Medico M
JOIN Consulta C ON M.codigo = C.codmed
WHERE M.codcid = 1;
```

Resultado:
```
nome
-----------
Paulo Rangel
Luara dos Santos
```

**Quando usar DISTINCT:**
- Quando você quer saber *quais* valores existem, não *quantas vezes* cada um aparece.
- Quando o JOIN inevitavelmente multiplica linhas (um médico com várias consultas aparece várias vezes).

**Quando NÃO usar:**
- Quando você precisa da contagem exata de ocorrências.
- Como "conserto" para um JOIN incorreto — corrija o JOIN em vez de mascarar com DISTINCT.

**→ Próximos passos:** [JOIN](#14-join--cruzando-tabelas) | [GROUP BY para agrupar e contar](#16-group-by-e-having--agrupando-e-filtrando-grupos)

---

## 10. NULL — valores ausentes

Você está filtrando dados e percebeu que comparações normais não funcionam com valores nulos.

**NULL** não é zero. Não é uma string vazia `""`. NULL significa *ausência de valor* — o valor é desconhecido, inexistente ou inaplicável.

### Três interpretações de NULL

1. **Valor desconhecido:** o médico tem e-mail, mas não foi informado.
2. **Valor inexistente:** o campo não se aplica a essa tupla (um autônomo não tem CRM de empresa).
3. **Valor não disponível:** o dado existe, mas não está acessível no momento.

### A armadilha das comparações com NULL

```sql
-- ERRADO: isso nunca retorna nada!
SELECT * FROM Medico WHERE email = NULL;
SELECT * FROM Medico WHERE email != NULL;
```

NULL não é igual a nada — nem a outro NULL. Toda comparação com NULL retorna um resultado especial: *desconhecido*. O WHERE só deixa passar resultados *verdadeiros*, não *desconhecidos*.

### A forma correta: IS NULL e IS NOT NULL

```sql
-- Médicos SEM e-mail cadastrado:
SELECT * FROM Medico WHERE email IS NULL;

-- Médicos COM e-mail cadastrado:
SELECT * FROM Medico WHERE email IS NOT NULL;
```

### NULL em operações matemáticas

Qualquer operação aritmética com NULL retorna NULL:

```sql
SELECT 100 + NULL;  -- resultado: NULL
SELECT NULL * 5;    -- resultado: NULL
```

### NULL em funções de agregação

Funções como `COUNT`, `SUM`, `AVG` **ignoram** valores NULL automaticamente. `COUNT(coluna)` conta apenas valores não-nulos; `COUNT(*)` conta todas as linhas.

**→ Próximos passos:** [WHERE](#8-where--filtrando-linhas) | [LEFT JOIN e NULLs](#15-a-diferença-entre-inner-left-e-right-join)

---

## 11. Operadores de texto — LIKE, %, _

Você precisa buscar por texto que você conhece parcialmente — o início de um nome, parte de um e-mail, etc.

Para buscas por padrão em colunas de texto, SQL oferece o operador `LIKE` combinado com caracteres coringa.

### O operador LIKE

```sql
SELECT * FROM Medico WHERE nome LIKE 'Paulo%';
```

### O curinga % — zero ou mais caracteres

`%` substitui qualquer sequência de caracteres (inclusive nenhum).

```sql
-- Nomes que começam com "Paulo"
SELECT * FROM Medico WHERE nome LIKE 'Paulo%';
-- retorna: Paulo Rangel

-- Nomes que terminam com "Santos"
SELECT * FROM Medico WHERE nome LIKE '%Santos';
-- retorna: Luara dos Santos, Luan dos Santos

-- Nomes que contêm "Ana" em qualquer posição
SELECT * FROM Medico WHERE nome LIKE '%Ana%';
-- retorna: Ana Maria, Carla Ana
```

### O curinga _ — exatamente um caractere

`_` substitui exatamente um caractere qualquer.

```sql
-- E-mails com exatamente 2 caracteres antes do @
SELECT * FROM Medico WHERE email LIKE '__@%';
-- O padrão é: qualquer char, qualquer char, @, qualquer coisa

-- Nomes onde o segundo caractere é "a"
SELECT * FROM Medico WHERE nome LIKE '_a%';
-- retorna: Carla Ana, Nena Lina (segundo char é 'a')
```

### Combinando % e _

```sql
-- Palavras de exatamente 4 caracteres
SELECT * FROM Tabela WHERE coluna LIKE '____';
-- 4 underscores = 4 caracteres exatos

-- Começa com 'N', depois qualquer coisa, termina com 'a'
SELECT * FROM Medico WHERE nome LIKE 'N%a';
```

### NOT LIKE

```sql
-- Médicos cujo nome NÃO começa com "Paulo"
SELECT * FROM Medico WHERE nome NOT LIKE 'Paulo%';
```

**→ Próximos passos:** [WHERE](#8-where--filtrando-linhas) | [NULL](#10-null--valores-ausentes)

---

## 12. Operadores matemáticos em SQL

Você precisa fazer cálculos sobre os dados — preços com desconto, médias, totais.

SQL suporta operações matemáticas diretamente nas colunas:

| Operador | Operação | Exemplo |
|----------|----------|---------|
| `+` | Adição | `valor + 10` |
| `-` | Subtração | `valor - 5` |
| `*` | Multiplicação | `valor * 1.1` |
| `/` | Divisão | `valor / 2` |
| `%` | Módulo (resto) | `codigo % 2` |

### Exemplos práticos

```sql
-- Exibir o valor da consulta com 10% de acréscimo
SELECT codmed, valor, valor * 1.1 AS valor_com_taxa
FROM Consulta;

-- Calcular diferença em relação a uma referência
SELECT nome, valor - 100 AS diferenca_da_media
FROM Consulta
WHERE valor > 100;
```

### Divisão inteira — cuidado!

Em alguns SGBDs, dividir dois inteiros retorna um inteiro (sem decimais). Use CAST ou garanta que ao menos um operando seja decimal:

```sql
SELECT 7 / 2;       -- pode retornar 3 (inteiro)
SELECT 7 / 2.0;     -- retorna 3.5
SELECT 7.0 / 2;     -- retorna 3.5
```

### Precedência

Segue as regras matemáticas normais: `*` e `/` antes de `+` e `-`. Use parênteses para controlar a ordem:

```sql
SELECT (valor + 50) * 0.9 AS valor_final FROM Consulta;
```

**→ Próximos passos:** [SELECT com expressões](#7-select--consultando-dados) | [GROUP BY e funções de agregação](#16-group-by-e-having--agrupando-e-filtrando-grupos)

---

## 13. ORDER BY — ordenando resultados

Você quer controlar a ordem em que os resultados aparecem — por data, por nome, por valor.

Por padrão, o banco de dados não garante nenhuma ordem específica nos resultados. Para garantir uma ordenação, use `ORDER BY`.

### Sintaxe básica

```sql
SELECT nome, valor
FROM Consulta
ORDER BY valor;          -- ascendente (padrão)

SELECT nome, valor
FROM Consulta
ORDER BY valor DESC;     -- descendente (maior para menor)

SELECT nome, valor
FROM Consulta
ORDER BY valor ASC;      -- ascendente explícito
```

### Ordenar por múltiplas colunas

```sql
-- Primeiro por data (mais recente), depois por valor (maior)
SELECT data, codmed, valor
FROM Consulta
ORDER BY data DESC, valor DESC;
```

O banco ordena primeiro pela primeira coluna. Em caso de empate, ordena pela segunda, e assim por diante.

### ORDER BY com alias

```sql
SELECT nome, valor * 1.1 AS valor_final
FROM Consulta
ORDER BY valor_final DESC;
-- Aqui o alias funciona porque ORDER BY é executado depois do SELECT
```

### Posição da cláusula

`ORDER BY` sempre é a última cláusula — é o passo 6 da [ordem de execução](#6-a-ordem-de-execução-das-cláusulas-sql).

**→ Próximos passos:** [SELECT](#7-select--consultando-dados) | [Consulta completa](#17-consulta-completa--exemplo-integrador)

---

## 14. JOIN — cruzando tabelas

Seus dados estão em tabelas separadas e você precisa combiná-los em uma só consulta.

JOIN é a operação que combina linhas de duas tabelas com base numa condição de junção — normalmente uma chave estrangeira ligando-se a uma chave primária.

### Por que os dados ficam em tabelas separadas?

Por **normalização** — a boa prática de não repetir dados. Em vez de guardar o nome do médico em cada linha de `Consulta`, guardamos apenas o `codmed` (chave estrangeira) e buscamos o nome na tabela `Medico` quando necessário.

### Sintaxe do JOIN

```sql
SELECT M.nome, C.valor, C.data
FROM Medico M
JOIN Consulta C ON M.codigo = C.codmed;
```

- `FROM Medico M` — tabela base com alias `M`
- `JOIN Consulta C` — tabela a cruzar com alias `C`
- `ON M.codigo = C.codmed` — condição de junção: o código do médico deve ser o mesmo nos dois lados

### JOIN com WHERE

Você pode filtrar linhas *depois* do cruzamento:

```sql
-- Médicos da cidade 1 que têm consultas com valor acima de 100
SELECT DISTINCT M.nome
FROM Medico M
JOIN Consulta C ON M.codigo = C.codmed
WHERE M.codcid = 1 AND C.valor > 100;
```

### JOIN com múltiplas tabelas

```sql
SELECT M.nome, P.nome AS paciente, C.valor
FROM Medico M
JOIN Consulta C ON M.codigo = C.codmed
JOIN Paciente P ON C.codpac = P.codigo
WHERE C.valor > 100;
```

### O que acontece internamente

O banco combina cada linha de `Medico` com cada linha de `Consulta` onde a condição ON é verdadeira. O resultado é um conjunto de linhas combinadas que você pode filtrar com WHERE, agrupar com GROUP BY, etc.

**→ Próximos passos:** [INNER vs LEFT vs RIGHT JOIN](#15-a-diferença-entre-inner-left-e-right-join) | [DISTINCT com JOIN](#9-distinct--eliminando-duplicatas)

---

## 15. A diferença entre INNER, LEFT e RIGHT JOIN

Você já entende o JOIN básico, mas quer saber quando usar cada variante — especialmente quando precisa incluir registros sem correspondência.

### Dados de referência

Vamos usar as tabelas de exemplo dos materiais:

**Tabela `Medico`** (médicos da cidade 1): Paulo Rangel (cod. 1), Luara dos Santos (cod. 7), Luan dos Santos (cod. 8), Carolina Pereira (cod. 110).

**Tabela `Consulta`**: tem registros para os médicos de código 1, 2, 3, 4, 7. Não tem consultas para Luan dos Santos (8) nem Carolina Pereira (110).

---

### INNER JOIN (ou apenas JOIN)

Retorna **apenas as linhas com correspondência nas duas tabelas**. Quem não tem par é excluído.

```sql
SELECT DISTINCT M.nome
FROM Medico M
JOIN Consulta C ON M.codigo = C.codmed
WHERE M.codcid = 1;
```

Resultado:
```
nome
-----------
Paulo Rangel
Luara dos Santos
```

Luan dos Santos e Carolina Pereira ficam de fora — não têm consultas.

---

### LEFT JOIN (LEFT OUTER JOIN)

Retorna **todas as linhas da tabela da esquerda** (a que vem no FROM), mesmo que não haja correspondência na tabela da direita. Onde não há correspondência, as colunas da tabela da direita aparecem como NULL.

```sql
SELECT DISTINCT M.nome
FROM Medico M
LEFT JOIN Consulta C ON M.codigo = C.codmed
WHERE M.codcid = 1;
```

Resultado:
```
nome
-----------
Paulo Rangel
Luara dos Santos
Luan dos Santos
Carolina Pereira
```

Agora todos os médicos da cidade 1 aparecem — inclusive os que nunca atenderam nenhuma consulta.

**Uso típico:** encontrar registros sem correspondência ("médicos que nunca tiveram consulta").

```sql
-- Médicos sem nenhuma consulta:
SELECT M.nome
FROM Medico M
LEFT JOIN Consulta C ON M.codigo = C.codmed
WHERE C.codmed IS NULL;
```

---

### RIGHT JOIN (RIGHT OUTER JOIN)

Retorna **todas as linhas da tabela da direita**, mesmo sem correspondência na tabela da esquerda.

```sql
SELECT DISTINCT M.nome
FROM Medico M
RIGHT JOIN Consulta C ON M.codigo = C.codmed
WHERE M.codcid = 1;
```

Resultado:
```
nome
-----------
Paulo Rangel
Luara dos Santos
```

Aqui, a tabela da direita (`Consulta`) é a que comanda — o WHERE sobre `M.codcid = 1` filtra os médicos antes de decidir quais consultas incluir. O resultado fica igual ao INNER JOIN neste caso porque as consultas dos médicos 8 e 110 simplesmente não existem.

**Dica prática:** RIGHT JOIN pode ser sempre reescrito como LEFT JOIN trocando a ordem das tabelas. Muitos desenvolvedores preferem usar apenas LEFT JOIN por clareza.

---

### Resumo visual

```
Medico: [Paulo] [Luara] [Luan] [Carolina]
Consulta: [Paulo ✓] [Luara ✓] [--] [--]

INNER JOIN → Paulo, Luara  (só quem tem par)
LEFT JOIN  → Paulo, Luara, Luan, Carolina  (todos os médicos)
RIGHT JOIN → Paulo, Luara  (todos os registros de Consulta que existem)
```

**→ Próximos passos:** [NULL e IS NULL](#10-null--valores-ausentes) | [DISTINCT](#9-distinct--eliminando-duplicatas)

---

## 16. GROUP BY e HAVING — agrupando e filtrando grupos

Você quer saber totais, médias ou contagens por categoria — não linha por linha.

### O problema que GROUP BY resolve

Você tem 13 linhas de consulta e quer saber: *quantas consultas cada médico realizou?* Sem GROUP BY, você precisaria fazer uma consulta por médico. Com GROUP BY, uma só consulta resolve.

### Funções de agregação

Antes de GROUP BY, você precisa conhecer as funções que resumem grupos:

| Função | O que faz |
|--------|-----------|
| `COUNT(coluna)` | Conta linhas não-nulas |
| `COUNT(*)` | Conta todas as linhas do grupo |
| `SUM(coluna)` | Soma os valores |
| `AVG(coluna)` | Calcula a média |
| `MAX(coluna)` | Retorna o maior valor |
| `MIN(coluna)` | Retorna o menor valor |

### Sintaxe do GROUP BY

```sql
SELECT codmed, COUNT(*) AS total_consultas, SUM(valor) AS receita_total
FROM Consulta
GROUP BY codmed;
```

Resultado: uma linha por médico, com o total de consultas e a soma dos valores.

**Regra importante:** No SELECT de uma consulta com GROUP BY, você só pode incluir:
- Colunas que estão no GROUP BY
- Funções de agregação

Qualquer outra coluna gera erro ou resultado imprevisível.

### HAVING — filtrando grupos

WHERE filtra linhas individuais *antes* do agrupamento. HAVING filtra *grupos* — depois do GROUP BY.

```sql
-- Médicos com mais de 3 consultas:
SELECT codmed, COUNT(*) AS total
FROM Consulta
GROUP BY codmed
HAVING COUNT(*) > 3;

-- Médicos cuja receita total supera 200:
SELECT codmed, SUM(valor) AS receita
FROM Consulta
GROUP BY codmed
HAVING SUM(valor) > 200;
```

### WHERE vs HAVING — a distinção crucial

```sql
-- WHERE filtra ANTES de agrupar (elimina linhas)
-- HAVING filtra DEPOIS de agrupar (elimina grupos)

SELECT codmed, COUNT(*) AS total
FROM Consulta
WHERE valor > 50         -- só conta consultas acima de 50
GROUP BY codmed
HAVING COUNT(*) >= 2;    -- só mostra médicos com 2+ dessas consultas
```

**→ Próximos passos:** [Consulta completa](#17-consulta-completa--exemplo-integrador) | [ORDER BY](#13-order-by--ordenando-resultados)

---

## 17. Consulta completa — exemplo integrador

Você já aprendeu todas as peças. Aqui elas se encaixam numa consulta real.

### O problema

> "Liste o nome dos médicos da cidade 1 que realizaram mais de 2 consultas com valor acima de R$80, ordenados pelo total de consultas em ordem decrescente."

### A solução, passo a passo

```sql
SELECT   M.nome,                        -- (5º) seleciona nome e contagem
         COUNT(*) AS total_consultas
FROM     Medico M                       -- (1º) tabela base
JOIN     Consulta C                     -- (1º) cruzamento
         ON M.codigo = C.codmed
WHERE    M.codcid = 1                   -- (2º) filtra cidade do médico
         AND C.valor > 80              --       e valor da consulta
GROUP BY M.nome                         -- (3º) agrupa por médico
HAVING   COUNT(*) > 2                   -- (4º) só grupos com mais de 2
ORDER BY total_consultas DESC;          -- (6º) ordena do maior para menor
```

### Lendo a consulta na ordem de execução

1. **FROM + JOIN:** busca dados de `Medico` e `Consulta`, cruzando onde `codigo = codmed`
2. **WHERE:** fica apenas com médicos da cidade 1 cujas consultas valem mais de R$80
3. **GROUP BY:** agrupa as linhas restantes por nome do médico
4. **HAVING:** elimina grupos com 2 ou menos consultas
5. **SELECT:** para cada grupo que sobrou, mostra o nome e a contagem
6. **ORDER BY:** ordena do médico com mais consultas para o com menos

### Anatomia das cláusulas opcionais

Nenhuma cláusula além de `SELECT` e `FROM` é obrigatória. Você usa cada uma quando precisa:

```sql
-- Consulta mínima válida:
SELECT nome FROM Medico;

-- Adicionando filtro:
SELECT nome FROM Medico WHERE codcid = 1;

-- Adicionando cruzamento:
SELECT M.nome FROM Medico M JOIN Consulta C ON M.codigo = C.codmed WHERE M.codcid = 1;

-- Adicionando deduplicação:
SELECT DISTINCT M.nome FROM Medico M JOIN Consulta C ON M.codigo = C.codmed WHERE M.codcid = 1;

-- Adicionando agrupamento e ordenação:
SELECT M.nome, COUNT(*) AS total
FROM Medico M JOIN Consulta C ON M.codigo = C.codmed
WHERE M.codcid = 1
GROUP BY M.nome
ORDER BY total DESC;
```

---

## Referência rápida — Armadilhas comuns

| Armadilha | O que acontece | Solução |
|-----------|----------------|---------|
| `WHERE coluna = NULL` | Nunca retorna nada | Use `IS NULL` |
| Alias no WHERE | Erro ou comportamento inesperado | Repita a expressão |
| SELECT coluna não agrupada | Erro ou dado aleatório | Inclua no GROUP BY ou use agregação |
| JOIN sem ON | Produto cartesiano (todos × todos) | Sempre especifique a condição ON |
| INNER JOIN descartando registros sem par | Perde dados que você precisava | Use LEFT JOIN |
| ORDER BY sem direção | Ascendente por padrão | Especifique ASC ou DESC explicitamente |

---

*Baseado em: Elmasri & Navathe — Sistemas de Banco de Dados (6ª ed.) · Lynn Beighley — Use a Cabeça SQL · Material de aula SQL-INNERs*