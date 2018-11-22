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

![interogarea 2](Image2.PNG)




-TASK_03
Rezolvati aceeași sarcină, 1, apelând la structura selectivâ CASE.

![interogarea 3](Image3.PNG)




-TASK_04_01
Modificati exercitiile din sarcinile 1 și 2 pentru a include procesarea erorilor cu TRY și CATCH, și RAISERRROR.

![interogarea 4](Image4.PNG)




-TASK_04_02

![interogarea 5](Image5.PNG)
