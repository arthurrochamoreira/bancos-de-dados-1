# Notas de Aula — SQL: Funções de Agregação e Álgebra Relacional

---

## ⚠️ Atenção para a Prova

Funções de agregação cobradas: `MIN`, `MAX`, `COUNT`, `AVG`, `SUM`

> **Diferença importante entre `SUM` e `COUNT`:**
> - `COUNT(*)` conta o número de linhas (inclusive nulas)
> - `COUNT(coluna)` conta apenas valores não nulos naquela coluna
> - `SUM(coluna)` soma os valores numéricos de uma coluna

**Pesquisar:** boas práticas de indentação em SQL

---

## Questão 9 da Lista *(Questão de Prova)*

**Enunciado:** Listar funcionários e os carros que reservaram em Brasília (incluindo funcionários sem reserva em Brasília).

### Álgebra Relacional

```
A = π codCidade (σ nomeCidade = "Brasília" (Cidade))
B = π codCarro (A ⋈ Carro)
C = π codFunc, codCarro (B ⋈ Reserva)
D = π nomeFunc, codCarro (Funcionario ⟕ C)
```

### SQL

```sql
SELECT
    Funcionario.nomeFunc,
    Carro.codCarro
FROM Funcionario
LEFT JOIN Reserva
    ON Funcionario.codFunc = Reserva.codFunc
LEFT JOIN Carro
    ON Reserva.codCarro = Carro.codCarro
LEFT JOIN Cidade
    ON Carro.codCidade = Cidade.codCidade
WHERE Cidade.nomeCidade = 'Brasília'
   OR Carro.codCarro IS NULL;
```

---

## Questão 10 da Lista

**Enunciado:** Listar clientes que **nunca** reservaram um carro de cor Prata.

> **Regra importante:** A chave estrangeira deve ser unida (`JOIN`) com a tabela onde ela é chave primária.

### Álgebra Relacional

```
A = π nomeCliente (σ cor = "Prata" ((Cliente ⋈ Reserva) ⋈ Carro))
B = π codCliente, nomeCliente (Cliente)
C = π nomeCliente (B − A)
```

### SQL

```sql
SELECT Cliente.nomeCliente
FROM Cliente
WHERE Cliente.codCliente NOT IN (
    SELECT Reserva.codCliente
    FROM Reserva
    JOIN Carro
        ON Reserva.codCarro = Carro.codCarro
    WHERE Carro.cor = 'Prata'
);
```

---

## Funções de Agregação — Exemplos

### Exemplo 1: `COUNT`

Conta produtos medidos em litros:

```sql
SELECT COUNT(*)
FROM Produtos
WHERE Unidade = 'Litros';
```

### Exemplo 2: `MAX` com Subconsulta

Lista clientes com a reserva mais recente:

```sql
SELECT
    Cliente.nomeCliente,
    Cliente.idade
FROM Cliente
LEFT JOIN Reserva
    ON Cliente.codCliente = Reserva.codCliente
WHERE Reserva.data = (
    SELECT MAX(data)
    FROM Reserva
);
```
