# Aula 02 - Álgebra Relacional (complementar com o conteúdo do livro)

## Tarefas
- Anotar todas as condições para realizar cada operação
- Fazer questões de concurso de BD1 sobre a aula do dia
- Verificar plugins para criar carrossel de imagens no GitHub Pages
- O exercício do CPF do médico no slide pode cair de forma semelhante na prova
- Criar repositório para animações
- A aula foi focada em projeção
- Na prova, haverá uma questão semelhante à da foto

## Conceitos Básicos
- **Tupla** = linha  
- **Atributos** = colunas  

## União
**Condições para realizar união entre duas tabelas:**
- Mesma quantidade de colunas
- Mesmo domínio (tipos compatíveis: int, string, char, etc.)
- A ordem das colunas não importa

[cite_start]Com base no livro *Sistemas de Banco de Dados* de Elmasri e Navathe (6ª edição), a operação de **União** ($\cup$) é uma operação binária da álgebra relacional que combina tuplas de duas relações[cite: 16, 17].

Abaixo estão os requisitos e o passo a passo detalhado:

### Requisitos para a Operação de União
[cite_start]Para realizar a união entre duas relações $R$ e $S$, elas devem ser obrigatoriamente **compatíveis na união** (ou compatíveis no tipo)[cite: 15, 41]. Os requisitos específicos são:

1.  [cite_start]**Mesmo Grau:** As duas relações devem possuir o mesmo número de atributos[cite: 15, 41].
2.  [cite_start]**Domínios Idênticos:** Os domínios (tipos de dados) dos atributos correspondentes devem ser os mesmos[cite: 15, 41]. [cite_start]Ou seja, o domínio do $i$-ésimo atributo de $R$ deve ser igual ao domínio do $i$-ésimo atributo de $S$[cite: 15, 41].
3.  [cite_start]**Ordem dos Atributos:** Os atributos devem aparecer na mesma ordem em ambas as relações para que a correspondência entre os campos seja válida[cite: 15].

### Passo a Passo da Operação
A operação de união segue o seguinte processo lógico e técnico:

1.  [cite_start]**Verificação de Compatibilidade:** Valide se $R$ e $S$ atendem aos requisitos de compatibilidade citados acima (mesmo grau e domínios)[cite: 15, 41].
2.  [cite_start]**Agregação de Tuplas:** Identifique todas as tuplas que aparecem na relação $R$, na relação $S$, ou em ambas[cite: 16, 17].
3.  [cite_start]**Eliminação de Duplicatas:** Como uma relação é definida matematicamente como um conjunto de tuplas, qualquer registro que apareça em ambas as tabelas originais deve ser incluído apenas uma vez no resultado final[cite: 15, 17, 41].
4.  [cite_start]**Definição do Esquema Resultante:** A relação resultante terá, por padrão, os mesmos nomes de atributos que a primeira relação ($R$) da operação[cite: 15].
5.  [cite_start]**Implementação Técnica (Otimização):** Em nível de sistema, a união costuma ser executada através de algoritmos de **ordenação-intercalação** ou **hashing** para identificar e remover duplicatas de forma eficiente[cite: 41].

### Exemplo em SQL
[cite_start]Na linguagem SQL, essa operação é realizada pelo operador `UNION`[cite: 15, 50]. Por padrão, o SQL já executa a eliminação de duplicatas:
```sql
(SELECT Atributo1, Atributo2 FROM Tabela_R)
UNION
(SELECT Atributo1, Atributo2 FROM Tabela_S);
```

---

### Referências Encontradas

* **Capítulo 6:** Álgebra e cálculo relacional.
    * [cite_start]**Seção 6.2.1:** As operações UNIÃO, INTERSECÇÃO e SUBTRAÇÃO (Página 101)[cite: 16, 17].
* **Capítulo 4:** SQL básica.
    * [cite_start]**Seção 4.3.4:** Operações de conjunto em SQL (Páginas 72-73)[cite: 15].
* **Capítulo 19:** Algoritmos para processamento e otimização de consulta.
    * [cite_start]**Seção 19.4.2:** Implementação de operações de conjunto (Página 470)[cite: 41].

**Referência Bibliográfica (ABNT):**
ELMASRI, Ramez; NAVATHE, Shamkant B. **Sistemas de banco de dados**. 6. ed. São Paulo: Pearson Addison Wesley, 2011.

## Projeção
- Operação que seleciona atributos (colunas) de uma tabela

## Produto Cartesiano
- Combina cada tupla da tabela da esquerda com todas as tuplas da tabela da direita
- Resultado contém todos os atributos de ambas as tabelas
- Pouco utilizado devido ao risco de estouro de memória

## Joins (alternativa ao produto cartesiano)

### Inner Join
- Combina apenas tuplas com valores correspondentes
- Exemplo: combina tuplas com o mesmo código
- Pode ser entendido como um produto cartesiano com filtro
- Requer atributos em comum entre as tabelas

### Left Join
- Retorna todas as tuplas da tabela da esquerda
- Quando não há correspondência, preenche com `NULL`

### Right Join
- Retorna todas as tuplas da tabela da direita
- Quando não há correspondência, preenche com `NULL`

### Full Join
- Retorna todas as tuplas de ambas as tabelas
- Preenche com `NULL` quando não há correspondência

## Outros Conceitos
- **Chave estrangeira**: atributo que referencia a chave primária de outra tabela

## Aula 02 - Parte 2: Combinação de Operadores
- Estudar a tabela resultante das operações entre tabelas