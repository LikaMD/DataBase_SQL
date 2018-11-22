# Laboratorul Nr.5
# TRANSACT-SQL-INSTRUCTIUNI PROCEDURALE

TASK_01
Completati următorul cod pentru a afișa cel mai mare numar dintre cele trei numere prezentate:

```SQL
declare @first int, @second int, @third int;
declare @higher int;

set @first = rand() * 25;
set @second = rand() * 25;
set @third = rand() * 25;

if @first < @second set @higher = @second
else set @higher = @first;
if @higher < @third set @higher = @third;

print 'First number = ' + cast(@first as varchar(2)); 
print 'Second number = ' + cast(@second as varchar(2));
print 'Third number = ' + cast(@third as varchar(2)); 
print 'Higher number is: ' + cast(@higher as varchar(2));

```

![interogarea 1](https://github.com/LikaMD/DataBase_SQL/blob/master/Laborator_5/lab5.1.PNG)




- TASK_02
Afișati primele zece date (numele, prenumele studentului) în funcție de valoarea notei (cu excepția notelor 6 și 8) a studentului la primul test al disciplinei Baze de date, folosind structura de altemativă IF. .. ELSE. Sa se foloseasca variabilele.

```

declare @Nume_Disciplina varchar(20) = 'Baze de date';
declare @Tipul_Testului varchar(20) = 'Testul 1';
declare @Nota1 int = 6;
declare @Nota2 int = 8;

if @Nota1 !=any (select  top (10) Nota
from studenti, studenti_reusita, discipline
where studenti.Id_Student = studenti_reusita.Id_Student
and discipline.Id_Disciplina = studenti_reusita.Id_Disciplina
and Disciplina = @Nume_Disciplina
and Tip_Evaluare = @Tipul_Testului)

and @Nota2 != any (select  top (10) Nota
from studenti, studenti_reusita, discipline
where studenti.Id_Student = studenti_reusita.Id_Student
and discipline.Id_Disciplina = studenti_reusita.Id_Disciplina
and Disciplina = @Nume_Disciplina
and Tip_Evaluare = @Tipul_Testului)

begin

select  top (10) Nume_Student, Prenume_Student, Nota
from studenti, studenti_reusita, discipline
where discipline.Id_Disciplina = studenti_reusita.Id_Disciplina
and studenti.Id_Student = studenti_reusita.Id_Student
and Disciplina = @Nume_Disciplina
and Tip_Evaluare = @Tipul_Testului
and Nota not in (@Nota1, @Nota2)

end
```

![interogarea 2](https://github.com/LikaMD/DataBase_SQL/blob/master/Laborator_5/lab5.2.PNG?raw=true)


-TASK_03
Rezolvati aceeași sarcină, 1, apelând la structura selectivâ CASE.
```
declare @N1 int , @N2 int, @N3 int;
declare @MAI_MARE int;
set @N1 = 60 * rand();
set @N2 = 60 * rand();
set @N3 = 60 * rand();
set @MAI_MARE = @N1;
set @MAI_MARE = case 
					 when  @MAI_MARE < @N2 and @N3 < @N2
					 then  @N2
					 when @MAI_MARE < @N3 and @N2<@N3
					 then  @N3
					 else @MAI_MARE
			     end   
print @N1;
print @N2;
print @N3;
print 'Mai mare = ' + cast( @MAI_MARE   as varchar(2));
```

![interogarea 3](https://github.com/LikaMD/DataBase_SQL/blob/master/Laborator_5/lab5.3.PNG)


-TASK_04_01
Modificati exercitiile din sarcinile 1 și 2 pentru a include procesarea erorilor cu TRY și CATCH, și RAISERRROR.

```
declare @N1 int , @N2 int, @N3 int;
declare @MAI_MARE int;
set @N1 = 60 * rand();
set @N2 = 60 * rand();
set @N3 = 60 * rand() ;
set @MAI_MARE = @N1;

begin try
if @N1 = @N2 or @N1 = @N3 or @N2 = @N3 
    raiserror ('Some numbers have the same value', 1,1)
else
begin
if @MAI_MARE < @N2
   set @MAI_MARE = @N2;
if @MAI_MARE < @N3
   set @MAI_MARE = @N3;
print @N1;
print @N2;
print @N3;
print 'Mai mare = ' + cast(@MAI_MARE as varchar(2));
end
end try

begin catch
print ' An error occured!' 
print 'The details of the error'
print ' The number of error:' + cast(ERROR_NUMBER() as varchar(20))
print ' Level of Severity:' + cast(ERROR_SEVERITY() as varchar(20))
print ' The error status:' + cast(ERROR_STATE() as varchar(20))
print ' The error line:' + cast(ERROR_LINE() as varchar(20))
end catch
```
![interogarea 4](https://github.com/LikaMD/DataBase_SQL/blob/master/Laborator_5/lab5.41.PNG)


-TASK_04_02
```
declare @Nume_Disciplina varchar(20) = 'Baze de date';
declare @Tipul_Testului varchar(20) = 'Testul 1';
declare @Nota1 int = 6;
declare @Nota2 int = 8;

if @Nota1 = @Nota2 
   raiserror('Notele ce urmeaza a fi cautate au valori egale',2,2)
else
if @Nota1 !=any (select  top (10) Nota
from studenti, studenti_reusita, discipline
where studenti.Id_Student = studenti_reusita.Id_Student
and discipline.Id_Disciplina = studenti_reusita.Id_Disciplina
and Disciplina = @Nume_Disciplina
and Tip_Evaluare = @Tipul_Testului)

and @Nota2 != any (select  top (10) Nota
from studenti, studenti_reusita, discipline
where studenti.Id_Student = studenti_reusita.Id_Student
and discipline.Id_Disciplina = studenti_reusita.Id_Disciplina
and discipline.Id_Disciplina = studenti_reusita.Id_Disciplina
and Disciplina = @Nume_Disciplina
and Tip_Evaluare = @Tipul_Testului)

begin try
begin

select top (10) Nume_Student, Prenume_Student, Nota
from studenti, studenti_reusita, discipline
where studenti.Id_Student = studenti_reusita.Id_Student
and discipline.Id_Disciplina = studenti_reusita.Id_Disciplina
and Disciplina = @Nume_Disciplina
and Tip_Evaluare = @Tipul_Testului
and Nota not in (@Nota1, @Nota2)

end
end try
begin catch
print ' An error occured!' 
print 'The details of the error'
print ' The number of error:' + cast(ERROR_NUMBER() as varchar(20))
print ' Level of Severity:' + cast(ERROR_SEVERITY() as varchar(20))
print ' The error status:' + cast(ERROR_STATE() as varchar(20))
print ' The error line:' + cast(ERROR_LINE() as varchar(20))
end catch
```
![interogarea 5](https://github.com/LikaMD/DataBase_SQL/blob/master/Laborator_5/lab5.42.PNG)
