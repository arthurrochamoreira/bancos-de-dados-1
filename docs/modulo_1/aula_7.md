# Anotações AULA 7

## Queries

### 1 — Agregação com HAVING

```sql
SELECT   codCidade, COUNT(*), AVG(salario) AS Total
FROM     Funcionario
WHERE    salario > 3000
GROUP BY codCidade
HAVING   AVG(salario) > 3200
ORDER BY Total
```

### 2 — Subquery com IN

```sql
SELECT Cliente.nome
FROM   Cliente
WHERE  Cliente.idCli IN (
         SELECT Compra.idCli
         FROM   Compra
         WHERE  Compra.idProd = 2
       )
```

### 3 — Clientes que fizeram alguma compra (DISTINCT + ordem alfabética)

```sql
SELECT DISTINCT Cliente.nome
FROM   Cliente
WHERE  Cliente.idCli IN (
         SELECT Compra.idCli
         FROM   Compra
       )
ORDER BY Cliente.nome ASC
```

### 4 — EXISTS com SELECT 1

```sql
SELECT Cliente.nome
FROM   Cliente
WHERE  EXISTS (
         SELECT 1
         FROM   Compra
         WHERE  Compra.idCli = Cliente.idCli
       )
```

---

## Anotações

- **= vs IN:** usar `=` somente quando a subquery retorna exatamente um resultado. Quando pode retornar vários, usar `IN`.
- **EXISTS + SELECT 1:** mais eficiente que `IN` em muitos casos — o banco para na primeira linha encontrada e não precisa retornar valores reais.

---

## Cai na prova

- [ ] Listar o nome dos dois clientes que mais compraram produtos.
- [ ] Listar o nome dos clientes com compras acima da média.
- [ ] Buscar um cliente em uma posição específica (ex.: 10º da lista).
- [ ] Mostrar como representar em álgebra relacional que um cliente não fez nenhuma compra.

---

## Para pesquisar

- [ ] Como colocar uma subquery dentro do `FROM` (tabela derivada / inline view).
- [ ] Custo das operações na notação Big-O.
- [ ] Por que usar `IN` e não `=` na query 2 (subquery pode retornar múltiplas linhas).