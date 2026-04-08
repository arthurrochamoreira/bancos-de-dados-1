questão 9 da lista (Questão de Prova):

cobrado na prova de agregadoras: MIN, MAX, COUNT, AVG, SUM (Atenção ao SUM e COUNT (Ver diferença))
pesquisar identação em SQL boas praticas

A = pi codCidade(S = nomeCidade = "Brasília")
B = pi codCarro ( A JOIN Carro)
C = pi codFunc, codCarro(B JOIN Reserva)
D = pi nomeFunc, (Funcionario LEFT JOIN C)

SELECT Funcionario.nomeFunc, Carro.codCarro
FROM Funcionario 
LEFT JOIN Reserva ON Funcionario.codFunc = Reserva.codFunc
LEFT JOIN Carro ON Reserva.codCarro = Carro.codCarro
LEFT JOIN Cidade ON Carro.codCidade = Cidade.codCidade
WHERE Cidade.nomeCidade = "Brasília" OR Carro.codCarro IS NULL

questão 10 lista:

Chave estrangeira deve fazer o join com a tabela onde ela e chave primaria (A primaria com a estrangeira)

A = pi Cliente.nomeCliente(S cor = "Prata"((Cliente JOIN Reserva)JOIN Carro))
B = pi codCliente, nomeCliente (Cliente)
C = pi nomeCliente (B-A)

SELECT Cliente.nomeCliente
FROM Cliente
WHERE Cliente.codCliente NOT IN 
(SELECT Reserva.codCliente 
FROM Reserva JOIN Carro ON Reserva.codCarro = Carro.codCarro 
WHERE Carro.cor = 'Prata')

Funções de Agregação: 

SELECT COUNT(*)
FROM Produtos
WHERE Unidade = 'Litros'

SELECT Cliente.nomeCliente, Cliente.idade
FROM Cliente LEFT JOIN Reserva ON Cliente.codCliente = Reserva.codCliente
WHERE Reserva.data = (SELECT Reserva.data FROM Reserva WHERE Reserva.data = (SELECT MAX(Reserva.data)FROM Reserva))