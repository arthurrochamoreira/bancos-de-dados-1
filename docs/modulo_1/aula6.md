tabela da foto de hj questão de prova
lembrar do group by
o que acontece se esquecer do group by no slide com o titulo de exemplificando 
Calcule o total de vendas (EM valor para cada produto em cada dia) quest
WHERE não pode usar função agregadora

SELECT Cliente, COUNT(*)
FROM vendas
GROUP BY Cliente
HAVING COUNT (*) > 1

MIN AVG MAX SUM (Unicas agregadoras que vão cair na prova)