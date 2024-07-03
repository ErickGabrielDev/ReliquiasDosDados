# As Relíquias dos Dados
Uma vez que o banco estiver bem estruturado e desenhado, é possível realizar testes, simulando relatórios ou telas que o sistema possa necessitar. A tarefa consiste em criar consultas que levem aos resultados esperados.

- 1. Todos os dados e o valor médio das consultas do ano de 2020 e das que foram feitas sob convênio.
```sql
select * from consulta where year(data_consulta) = 2020 and numero_carteira_convenio is not null;
```

- 2. Todos os dados das internações que tiveram data de alta maior que a data prevista para a alta.
```sql
select * from internacao where data_alta > data_prevista_alta;
```

- 3. Receituário completo da primeira consulta registrada com receituário associado.
```sql
select * 
from receita as r
inner join consulta as c
on r.codigo_consulta = c.codigo_consulta
order by data_consulta asc
limit 1;
```


- 4. Todos os dados da consulta de maior valor e também da de menor valor (ambas as consultas não foram realizadas sob convênio).
```sql
select * from consulta where valor_consulta = (select max(valor_consulta) from consulta) or valor_consulta = (select min(valor_consulta) from consulta);
```


- 5. Todos os dados das internações em seus respectivos quartos, calculando o total da internação a partir do valor de diária do quarto e o número de dias entre a entrada e a alta.
```sql
select i.data_prevista_alta, i.data_entrada, i.data_alta, q.numero_quarto, tq.descricao_quarto, tq.diaria_quarto * datediff(i.data_alta, i.data_entrada) as total_internacao
from internacao as i
inner join quarto as q
on i.numero_quarto = q.numero_quarto
inner join tipo_quarto as tq
on q.codigo_tipo_quarto = tq.codigo_tipo_quarto;
```


- 6. Data, procedimento e número de quarto de internações em quartos do tipo “apartamento”.
```sql
select i.data_entrada, p.descricao_procedimento, q.numero_quarto, tq.descricao_quarto
from internacao as i
inner join procedimento_internacao ip
on ip.codigo_internacao = i.codigo_internacao
inner join procedimento p
on ip.codigo_procedimento = p.codigo_procedimento
inner join quarto as q
on i.numero_quarto = q.numero_quarto
inner join tipo_quarto as tq
on tq.codigo_tipo_quarto = q.codigo_tipo_quarto
where tq.descricao_quarto = 'Apartamento';
```


- 7. Nome do paciente, data da consulta e especialidade de todas as consultas em que os pacientes eram menores de 18 anos na data da consulta e cuja especialidade não seja “pediatria”, ordenando por data de realização da consulta.
 
- 8. Nome do paciente, nome do médico, data da internação e procedimentos das internações realizadas por médicos da especialidade “gastroenterologia”, que tenham acontecido em “enfermaria”.

- 9. Os nomes dos médicos, seus CRMs e a quantidade de consultas que cada um realizou.
```sql
select nome_medico, crm_medico, count(codigo_consulta) as numero_consultas
from medico as m
inner join consulta as c on m.codigo_medico = c.codigo_medico group by nome_medico, crm_medico;
```


- 10. Todos os médicos que tenham "Gabriel" no nome.
```sql
select * from medico where nome_medico = 'Gabriel';
```


- 11. Os nomes, CREs e número de internações de enfermeiros que participaram de mais de uma internação.
```sql
select nome_enfermeiro, cre_enfermeiro, count(i.codigo_internacao) as internacoes
from enfermeiro as e
inner join enfermeiro_internacao as ei
on e.codigo_enfermeiro = ei.codigo_enfermeiro
inner join internacao as i 
on i.codigo_internacao = ei.codigo_internacao
group by nome_enfermeiro, cre_enfermeiro;
```
