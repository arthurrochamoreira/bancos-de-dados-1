- chave primaria: requisitos, caracteristicas, quantidade; o que define uma chave primária?

- chave estrangeiro: pesquisar o mesmo do acima 

- em algebra relacional não existe seleção = null.

- na prova, nao vai haver valores, so vai ter o noma da tabela e o nome dos atributos; Quando e chave primaria vem um sublinhado, quando chave secundaria um #.

- qual a ordem correta pra fazer as operações de join,seleção projeção e etc. qual a ordem mais eficiente., otimização de consultas. fazer as opeações antes de fazer o join.

- pi nomeALuno((s ano='2024'(matriculas)) J Aluno)

- pi nomeCurso((s ano='2024'(matriculas)) J Curso)

- pi nomeCurso(s nomeDepto = 'Exatas'((s ano='2024'(matriculas) J Curso) J Depto))

- pi nomeAluno((s nomeCurso = 'matematica'(s ano='2024'(matriculas) J Curso)) J Aluno)

- pi nomeCurso(Matriculas(curso) - pi idCursos(s ano='2024'(matriculas))) J Curso)

pesquisar como funciona a seleção, a seleção vai tupla por tupla, consultar primeiro todo mundo que fez matematica e depois consultar todo mundo que fez história.

pesquisar modelador.

prova: 5 questões, cada uma valendo 2 pontos. 


Atenção na seleção com multiplas condições

A = S nomeCurso = 'Matematica'(S ano="2024"(Matriculas)) S Curso)
B = S nomeCurso = 'historia'(S ano="2024"(Matriculas)) S Curso)
C = (A inter B) J Aluno
D = pi nomeAluno(C)

13.

- A S ano = 2024(matriculas)
B (A J Curso)
C = Pi idCursos, nomeCurso(B)
D = Pi idCursos, nomeCursos(Curso)

14.

A = Pi idDepto (Pi nomeCurso = "Matematica"(Curso))
B = Pi idDepto (S nomeCurso = "Quimica"(Curso))
C = A intersec B

ver o relax pra exercicios de algebra de sbd, colar conteudo do aprender (script)

ver a ordem de execução das operações da expressão

A = S cliente_nome = 'Rodrigo'(Cliente)
B = Pi rua, cidade (A)
C = B J Cliente
D = Pi nome (C-A)

SELECT Pessoa.*, Aloca.Projeto_Codigo, Projeto.Descricao
FROM Pessoa 
JOIN Aloca ON Pessoa.cpf = Aloca.cpf
JOIN Projeto ON Aloca.Projeto_Codigo = Projeto.Projeto_Codigo

SELECT Projeto.*
JOIN Aloca ON Projeto.Projeto_Codigo = Aloca.Projeto_Codigo
WHERE Aloca.CPF = 123

SELECT Pessoa.cpf
FROM Pessoa LEFT JOIN Aloca ON Pessoa.cpf = Aloca.cpf
WHERE Aloca.cpf IS NULL
