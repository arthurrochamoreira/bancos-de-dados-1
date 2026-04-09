# Guia de Estudos Otimizado: Bancos de Dados, SQL e Álgebra Relacional

## 1. Conceitos de Chaves (PK e FK)

### Chave Primária (PK - *Primary Key*)
*   **O que a define:** É uma chave candidata escolhida para identificar exclusivamente cada tupla (linha) dentro de uma relação.
*   **Quantidade:** Cada tabela possui exatamente **uma** chave primária designada.
*   **Requisitos/Características:**
    *   Seus valores não podem se repetir (devem ser únicos).
    *   **Não pode ser NULL** (Regra de Integridade de Entidade).
*   **Na prova:** Será representada por um atributo **sublinhado**.

### Chave Estrangeira (FK - *Foreign Key*)
*   **O que a define:** É um atributo (ou conjunto de atributos) que referencia a chave primária de outra tabela, servindo para manter a **integridade referencial** entre os dados.
*   **Quantidade:** Uma tabela pode ter zero, uma ou múltiplas chaves estrangeiras.
*   **Requisitos/Características:** O valor de uma FK deve corresponder a um valor existente na PK referenciada, ou deve ser **NULL**. Pode referenciar a própria tabela.
*   **Na prova:** Será representada pelo símbolo `#`.

---

## 2. Álgebra Relacional e Otimização

### A Operação de Seleção ($\sigma$) e Valores NULL
*   **Como funciona a seleção:** A condição de seleção é aplicada como um filtro **independentemente para cada tupla (linha) individual** da relação.
*   **A regra do NULL:** Na álgebra relacional e em SQL, uma comparação direta de igualdade `= NULL` retorna *Desconhecido* e a tupla é descartada. O correto é sempre utilizar o operador `IS NULL` ou `IS NOT NULL`.

### Otimização de Consultas (A Ordem Correta)
*   **Ordem mais eficiente:** A regra de ouro (heurística) da otimização é aplicar as operações de **Seleção ($\sigma$) e Projeção ($\pi$) ANTES da operação de JOIN ($\bowtie$)**.
*   **Por quê?** A operação de JOIN tem um custo multiplicativo. Aplicar a seleção e a projeção primeiro reduz drasticamente o número de linhas e colunas que serão combinadas, economizando muito processamento.

---

## 3. Resolução de Expressões de Álgebra Relacional

**Otimizando Consultas (Fazer operações restritivas antes do JOIN):**
*   $\pi_{nomeCurso} (\sigma_{ano='2024'}(Matriculas) \bowtie Curso \bowtie \sigma_{nomeDepto = 'Exatas'}(Depto))$
*   $\pi_{nomeAluno} (\sigma_{ano='2024'}(Matriculas) \bowtie \sigma_{nomeCurso = 'Matematica'}(Curso) \bowtie Aluno)$

**Diferença de Conjuntos (Exceções):**
*   $\pi_{nomeCurso} ( (\pi_{idCurso}(Curso) - \pi_{idCurso}(\sigma_{ano='2024'}(Matriculas))) \bowtie Curso )$
    *Explicação:* Pega todos os cursos, subtrai os cursos que tiveram matrícula em 2024, resultando nos cursos sem matrícula neste ano.

### Atenção: Seleção com Múltiplas Condições Excludentes
Para encontrar alunos que fizeram Matemática **E** História no mesmo ano, é necessário usar **Intersecção ($\cap$)**, pois uma única tupla não pode ser dois cursos ao mesmo tempo.
*   A = $\pi_{idCurso}(\sigma_{nomeCurso = 'Matematica'}(Curso))$
*   B = $\pi_{idCurso}(\sigma_{nomeCurso = 'Historia'}(Curso))$
*   C = (A $\cap$ B)
*   D = $\pi_{nomeAluno} (\sigma_{ano='2024'}(Matriculas) \bowtie C \bowtie Aluno)$

---

## 4. Consultas SQL: Revisão de JOINs e Diferença de Conjuntos

**JOINs Sucessivos:**
Para unir dados que dependem de uma tabela associativa (M:N), basta encadear os JOINs.
```sql
SELECT Pessoa.*, Aloca.Projeto_Codigo, Projeto.Descricao
FROM Pessoa 
JOIN Aloca ON Pessoa.cpf = Aloca.cpf
JOIN Projeto ON Aloca.Projeto_Codigo = Projeto.Projeto_Codigo;
```

**Diferença de Conjuntos usando LEFT JOIN:**
A forma mais comum em SQL de descobrir "quem NÃO possui correspondência" é usar o `LEFT JOIN` acompanhado de um `IS NULL` na chave da tabela da direita.
```sql
SELECT Pessoa.cpf
FROM Pessoa 
LEFT JOIN Aloca ON Pessoa.cpf = Aloca.cpf
WHERE Aloca.cpf IS NULL;
```
*Explicação:* O `LEFT JOIN` traz todas as Pessoas. Se a Pessoa não estiver na tabela `Aloca`, os campos de `Aloca` virão preenchidos com `NULL`. O `WHERE` filtra exatamente essas linhas.

---

## 5. Dicas para a Prova

*   **Estrutura:** 5 questões, valendo 2 pontos cada.
*   **Semântica:** Baseie-se **apenas** na estrutura (nome da tabela, atributos, sublinhado para PK e `#` para FK).
*   **Modelador:** Refere-se ao software (como Rational Rose ou ERwin) usado para desenhar os diagramas ER/Esquemas e gerar DDLs.
*   **Ordem de execução SQL:** Lembre-se sempre de `FROM` $\rightarrow$ `WHERE` $\rightarrow$ `GROUP BY` $\rightarrow$ `HAVING` $\rightarrow$ `SELECT` $\rightarrow$ `ORDER BY`.