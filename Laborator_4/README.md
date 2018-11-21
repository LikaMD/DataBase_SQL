# Laboratorul Nr.4

1. Interogarea Nr. 16 

select distinct Nume_Student, Prenume_Student, Nume_profesor, Prenume_Profesor
from studenti_reusita sr
inner join discipline d on sr.Id_Disciplina=d.Id_Disciplina
inner join studenti s on sr.Id_Student=s.Id_Student
inner join profesori p on sr.Id_Profesor=p.Id_Profesor
where d.Nr_ore_plan_disciplina<60


2. Interogarea Nr. 31

select Nume_Student, Prenume_Student,Id_Disciplina
from studenti_reusita sr
inner join studenti s on sr.Id_Student = s.Id_Student
where sr.Nota <= 4
group by Nume_Student,Prenume_Student,Id_Disciplina
having count(sr.Id_Disciplina)>2
```


3. Interogarea Nr. 35

```SQL
select d.Disciplina,AVG(cast(sr.Nota as float)) as Media
from studenti_reusita sr
inner join discipline d on sr.Id_Disciplina = d.Id_Disciplina
group by d.Disciplina
having AVG(cast(sr.Nota as float)) > 7
order by Media
```
