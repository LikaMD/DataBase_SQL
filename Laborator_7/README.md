# Laboratorul Nr.6
# CREAREA TABELELOR SI INDECSILOR

#TASK_01
Sa se scrie o instructiune T-SQL, care ar popula coloana Adresa _ Postala _ Profesor din tabelul profesori cu valoarea 'Mun. Chisinau', unde adresa este necunoscuta:

```SQL
use universitatea
go
update profesori
set Adresa_Postala_Profesor = 'Mun. Chisinau'
where Adresa_Postala_Profesor is null
```

![interogarea 1](Image1.PNG)

#TASK_02
Sa se modifice schema tabelului grupe, ca sa corespunda urmatoarelor cerinte: a) Campul Cod_ Grupa sa accepte numai valorile unice ~i sa nu accepte valori necunoscute. b) Sa se tina cont ca cheie primarii, deja, este definitii asupra coloanei Id_ Grupa.
```SQL
create unique index idx_cod_grupa
on grupe (cod_grupa);
exec sp_helpindex grupe;
```
![interogarea 2](Image2.PNG)


#TASK_03
La tabelul "grupe", sa se adauge 2 coloane noi "Sef_grupa" si "Prof_Indrumator", ambele de tip INT. Sa se populeze campurile nou-create cu cele mai potrivite candidaturi in baza criteriilor de mai jos: 
a) "Seful grupei" trebuie sa aiba cea mai buna reusita (medie) din grupa la toate formele de evaluare si la toate disciplinele. Un student nu poate fi sef de grupa la mai multe grupe. 
b) Profesorul indrumator trebuie sa predea un numar maximal posibil de discipline la grupa data. Daca nu exista o singura candidatura, care corespunde primei cerinte, atunci este ales din grupul de candidati acel cu identificatorul (Id_Profesor) minimal. Un profesor nu poate fi indrumator la mai multe grupe. 
c) Sa se scrie instructiunile ALTER, SELECT, UPDATE necesare pentru crearea coloanelor in tabelul "grupe", pentru selectarea candidatilor si inserarea datelor.
```SQL
alter table grupe add sef_grupa int, prof_indrumator int;

declare @nr_grupe int = (select count(Id_Grupa) from grupe)
declare @initial int = 1;

while (@initial <= @nr_grupe)
     begin
	    update grupe
		set sef_grupa = (select top 1 sel.Id_Student
		                from (select Id_Student, avg(cast(Nota as float)) as Media
						      from studenti_reusita
							  where Id_Grupa = @initial
							  group by Id_Student) sel
					    order by sel.Media desc),
		prof_indrumator = (select les.Id_Profesor
		                   from(select top 1 Id_Profesor, count(distinct Id_Disciplina) as Nr_discipline
						        from studenti_reusita
								where Id_Grupa = @initial
								group by Id_Profesor
								order by Nr_discipline desc) les)
		where Id_Grupa = @initial
		set @initial = @initial +1;
end

alter table grupe add constraint prof_stud unique(sef_grupa,prof_indrumator);
```

![interogarea 3](Image3.PNG)

#TASK_04
Sa se scrie o instructiune T-SQL, care ar mari toate notele de evaluare a sefilor de grupe cu un punct. Nota maximala (10) nu poate fi marita:
```SQL
update studenti_reusita
set Nota = Nota + 1
where Tip_Evaluare = 'Examen'
      and Id_Student = any(select sef_grupa 
	                       from grupe)
	  and Nota != 10
```
![interogarea 4](Image4.PNG)
