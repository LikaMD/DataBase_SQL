# Laboratorul Nr.10
# Crearea si utilizarea declansatoarelor

# TASK_01

Sa se modifice declansatorul inregistrare_noua,in asa fel,incat in cazul actualizarii auditoriului sa apara mesajul de informare, care, in afara de disciplina si ora, va afisa codul grupei afectate, ziua, blocul, auditoriul vechi si auditoriul nou.

```SQL
DROP TRIGGER IF EXISTS inregistrare_noua2
GO
CREATE TRIGGER inregistrare_noua2 ON plan_studii.orarul
AFTER UPDATE
AS SET NOCOUNT ON
IF UPDATE(Auditoriu)
SELECT 'Lectia la disciplina ' + UPPER(disc_St.Disciplina)+ ', a grupei ' + grupe.Cod_Grupa +
		', ziua de ' + CAST(inserted.Zi as VARCHAR(5)) + ', de la ora ' + CAST(inserted.Ora as VARCHAR(5))
		+ ', a fost transferata in aula ' + CAST(inserted.Auditoriu as VARCHAR(5)) + ', Blocul '+
		CAST(inserted.Bloc as VARCHAR(5)) + '. Auditoriul vechi: ' + CAST(deleted.Auditoriu as VARCHAR(5))+
		', Auditoriul nou: ' + CAST(inserted.Auditoriu as VARCHAR(5))
FROM inserted,deleted, disc_St, grupe
WHERE deleted.Id_Disciplina = disc_St.Id_Disciplina
AND inserted.Id_Grupa = grupe.Id_Grupa
GO

UPDATE plan_studii.orarul SET Auditoriu=510 WHERE Auditoriu = 501
```

![task01](https://github.com/LikaMD/DataBase_SQL/blob/master/Laborator_10/task01.PNG)


# TASK_02

Sa se creeze declansatorul, care ar asigura popularea corecta (consecutiva) a tabelelor studenti si studenti_reusita,si ar permite evitarea erorilor la nivelul cheilor externe.

```SQL
CREATE TRIGGER declans10_2 ON reusita_St
INSTEAD OF INSERT
AS SET NOCOUNT ON
   
  INSERT INTO reusita_St 
  SELECT * FROM inserted
  WHERE Id_Student in (SELECT Id_Student FROM stud_St)
  GO

  INSERT INTO reusita_St values ((select max(Id_Student) from stud_St), 103, 104, 1, 'Examen', null, null)

 -- INSERT INTO stud_St values ((select max(Id_Student) from reusita_St)+2,'Artur', 'Croitoru', '1996-12-12', null)

 -- delete from studentiS where Id_Student = (select max(Id_Student) from stud_St)
  select * from stud_St where Id_Student= (select max(Id_Student) from stud_St)
  select * from reusita_St where Id_Student = (select max(Id_Student) from stud_St)
```
![task02](https://github.com/LikaMD/DataBase_SQL/blob/master/Laborator_10/task02.PNG)

# TASK_03

 Sa se creeze un declansator, care ar interzice micsorarea notelor in tabelul studenti_reusita si modificarea valorilor campului Data_Evaluare, unde valorile acestui camp sunt nenule. Declansatorul trebuie sa se lanseze, numai daca sunt afectate datele studentilor din grupa, "CIB 171". Se va afisa un mesaj de avertizare in cazul tentativei de a incalca constrangerea.

```SQL
IF OBJECT_ID('decltask6_3', 'TR') is not null
   DROP TRIGGER decltask6_3
   GO
CREATE TRIGGER decltask6_3 ON studenti_reusita_test
AFTER UPDATE
AS
SET NOCOUNT ON
IF UPDATE (Nota)
DECLARE @ID_GRUPA INT = (SELECT Id_Grupa  
                         FROM grupe 
						 WHERE Cod_Grupa = 'CIB171')
DECLARE @count int = (SELECT count(*) 
                      FROM deleted , inserted 
			          where deleted.Id_Disciplina = inserted.Id_Disciplina 
					  and deleted.Id_Grupa = inserted.Id_Grupa 
			          and deleted.Id_Profesor = inserted.Id_Profesor 
					  and deleted.Tip_Evaluare = inserted.Tip_Evaluare 
			          and deleted.Id_Student = inserted.Id_Student
			          and inserted.Nota < deleted.Nota 
			          and inserted.Id_Grupa = @ID_GRUPA)	
BEGIN
IF (@count > 0 )
PRINT ('Nu se perminte micsorarea notelor pentru grupa CIB 171')
ROLLBACK TRANSACTION
end
IF UPDATE(Data_evaluare)
		SET @count = (SELECT count(*) 
		FROM deleted 
		WHERE Data_Evaluare is not null and Id_Grupa = @ID_GRUPA)
		IF @count > 0
		BEGIN
			PRINT ('Nu se permite modificarea campului Tip_Evaluare')
			ROLLBACK TRANSACTION
		END
GO
```
![task03](https://github.com/LikaMD/DataBase_SQL/blob/master/Laborator_10/task03.PNG)

# TASK_04

Sa se creeze un declansator DDL care ar interzice modificarea coloanei ld_Disciplina in tabelele bazei de date universitatea cu afisarea mesajului respectiv.

```SQL
CREATE TRIGGER decltask_5 on database
FOR Alter_Table
AS 
SET NOCOUNT ON
DECLARE @nume_disciplina varchar(50)
SELECT @nume_disciplina=EVENTDATA().value('(/EVENT_INSTANCE/AlterTableActionList/*/Columns/Name)[1]', 'nvarchar(100)') 
IF @nume_disciplina='Disciplina'
BEGIN 
PRINT ('Nu poate fi modificata coloana Id_Disciplina')
ROLLBACK;
END

ALTER TABLE plan_studii.discipline ALTER COLUMN Id_Disciplina varchar(100)
```

![task041](https://github.com/LikaMD/DataBase_SQL/blob/master/Laborator_10/task041.PNG)

```SQL
CREATE TRIGGER decl_ex10 on database
FOR Alter_Table
AS 
SET NOCOUNT ON
DECLARE @Cod_Grupa varchar(50)
SELECT @Cod_Grupa=EVENTDATA().value('(/EVENT_INSTANCE/AlterTableActionList/*/Columns/Name)[1]', 'nvarchar(100)') 
IF @Cod_Grupa='Cod_Grupa'
BEGIN 
PRINT ('Nu poate fi modificata coloana Cod_Grupa')
ROLLBACK;
END

ALTER TABLE grupe ALTER COLUMN Cod_Grupa varchar(100)
```

![task042](https://github.com/LikaMD/DataBase_SQL/blob/master/Laborator_10/task042.PNG)

# TASK_05

Sa se creeze un declansator DDL care ar interzice modificarea schemei bazei de date in afara orelor de lucru.

```SQL
DROP TRIGGER IF EXISTS decla_time_t9
GO
CREATE TRIGGER decla_time_t9
ON DATABASE
FOR ALTER_TABLE
AS
SET NOCOUNT ON
DECLARE @Currenttime TIME
DECLARE @Startday TIME
DECLARE @Endday TIME
SELECT @Currenttime = CONVERT(Time, GETDATE())
SELECT @Startday = '8:00:00'
SELECT @Endday = '17:00:00'

IF (@Currenttime < @Startday) OR (@Currenttime > @Endday)
BEGIN	
PRINT 'Baza de date poate fi modificata in orelor de lucru. Ora curenta: ' + cast(@Currenttime as VARCHAR(20))
ROLLBACK
END
GO


alter table grupe add Nume varchar(49);
```

![task05](https://github.com/LikaMD/DataBase_SQL/blob/master/Laborator_10/task05.PNG)


# TASK_06

 Sa se creeze un declansator DDL care, la modificarea proprietatilor coloanei ld_Profesor dintr-un tabel, ar face schimbari asemanatoare in mod automat in restul tabelelor.

```SQL
CREATE TRIGGER decl6task6
ON DATABASE
FOR ALTER_TABLE
AS
SET NOCOUNT ON
DECLARE @input varchar(500)
DECLARE @id_profesor varchar (20)
DECLARE @table varchar (50)
DECLARE @new_input varchar(500)

SELECT @id_profesor = EVENTDATA().value('(/EVENT_INSTANCE/AlterTableActionList/*/Columns/Name)[1]', 'nvarchar(max)')
IF @id_profesor = 'Id_Profesor'
BEGIN
SELECT @input = EVENTDATA().value ('(/EVENT_INSTANCE/TSQLCommand/CommandText)[1]', 'nvarchar(max)')
SELECT @table = EVENTDATA().value ('(/EVENT_INSTANCE/ObjectName)[1]','nvarchar(max)')

SELECT @new_input = REPLACE(@id_profesor, @table, 'studenti_reusita1');
EXECUTE (@new_input)


SELECT @new_input = REPLACE(@id_profesor, @table, 'profesori');
EXECUTE (@new_input)


SELECT @new_input = REPLACE(@id_profesor, @table, 'orarul');
EXECUTE (@new_input)

PRINT 'Datele au fost modificate cu succes'
END

ALTER TABLE profesori ALTER COLUMN Id_Profesor SMALLINT
```
![task06](https://github.com/LikaMD/DataBase_SQL/blob/master/Laborator_10/task06.PNG)