# TechTFQ-30DaysSQLChallenge-DAY6
Student Performance SQL challenge

going through the challenge of SQL interview questions featured in the TechTFQ channel



In this repository we will be going through the SQL interview questions featured in the following YouTube video [SQL Interview Questions](https://www.youtube.com/watch?v=dgIYeUAOzbM&list=PLavw5C92dz9Hxz0YhttDniNgKejQlPoAn&index=6).

# **Day 6: The problem statement: Fetching the Students Performance**

**PROBLEM STATEMENT**
You are given a table having the marks of one student in every test. 
You have to output the tests in which the student has improved his performance. 
For a student to improve his performance he has to score more than the previous test.
Provide 2 solutions, one including the first test score and second excluding it.

![image](https://github.com/Highashikata/TechTFQ-30DaysSQLChallenge-DAY6/assets/96960411/baf2d4de-e2ec-4549-b7b9-396844a3ab77)


**DDL & DML**

```
drop table if exists  student_tests;
create table student_tests
(
	test_id		int,
	marks		int
);
insert into student_tests values(100, 55);
insert into student_tests values(101, 55);
insert into student_tests values(102, 60);
insert into student_tests values(103, 58);
insert into student_tests values(104, 40);
insert into student_tests values(105, 50);

select * from student_tests;



```




**DQL**

```
SELECT *
FROM STUDENT_TESTS;

-- Output1: Fetching the Tests where students have improved 
-- their performances more than the previous test including the 1st record




/* 
Output2: Fetching the Tests where students have improved 
their performances more than the previous test excluding the 1st record
*/
----------- This query is false, but kept it to see the logic that I began with while treating sql problems
/*
SELECT ST1.TEST_ID,
	CASE
		WHEN ST1.MARKS > LAG(ST2.MARKS, 1, 0) OVER(ORDER BY ST1.TEST_ID) THEN ST1.MARKS
	END AS MARKS
FROM STUDENT_TESTS ST1
JOIN STUDENT_TESTS ST2 ON ST1.TEST_ID = ST2.TEST_ID;
*/

-----------------------------------------------------------------------------------------------------------------------------------------
-----------------------------------------------------------------------------------------------------------------------------------------
---------------------------------------------- Output  1: The query including the 1st record --------------------------------------------
-----------------------------------------------------------------------------------------------------------------------------------------
-----------------------------------------------------------------------------------------------------------------------------------------


/* To see the previous values of each record */
SELECT *,
	LAG(MARKS, 1, 0) OVER(ORDER BY TEST_ID)
FROM STUDENT_TESTS;



/* Using Subquery*/ 
SELECT TEST_ID,
	MARKS
FROM
	(SELECT *,
			LAG(MARKS, 1, 0) OVER(ORDER BY TEST_ID) AS PREVIOUS_MARK
		FROM STUDENT_TESTS) X
WHERE X.MARKS > X.PREVIOUS_MARK;


/* Using CTE */
WITH CTE_PREVIOUS_MARK AS
	(SELECT *,
			LAG(MARKS, 1, 0) OVER(ORDER BY TEST_ID) AS PREVIOUS_MARK
		FROM STUDENT_TESTS)
SELECT TEST_ID,
	MARKS
FROM CTE_PREVIOUS_MARK CTE
WHERE CTE.MARKS > CTE.PREVIOUS_MARK;


/* Using LEFT SELF JOIN */
SELECT 
    s1.TEST_ID,
    s1.MARKS
FROM 
    STUDENT_TESTS s1
LEFT JOIN 
    STUDENT_TESTS s2 ON s1.TEST_ID = s2.TEST_ID + 1
WHERE 
    s1.MARKS > COALESCE(s2.MARKS, 0);


/* La jointure 
STUDENT_TESTS s2 ON s1.TEST_ID = s2.TEST_ID + 1 
elle permet de joindre la ligne de la table1 avant la ligne précédente de la table 2
*/
-----------------------------------------------------------------------------------------------------------------------------------------
-----------------------------------------------------------------------------------------------------------------------------------------
---------------------------------------------- Output  2: The query excluding the 1st record --------------------------------------------
-----------------------------------------------------------------------------------------------------------------------------------------
-----------------------------------------------------------------------------------------------------------------------------------------


/* To see the previous values of each record and putting the current value if there's no previous mark */
SELECT *,
	LAG(MARKS, 1, MARKS) OVER(ORDER BY TEST_ID)
FROM STUDENT_TESTS;



/* Using Subquery*/ 
SELECT TEST_ID,
	MARKS
FROM
	(SELECT *,
			LAG(MARKS, 1, MARKS) OVER(ORDER BY TEST_ID) AS PREVIOUS_MARK
		FROM STUDENT_TESTS) X
WHERE X.MARKS > X.PREVIOUS_MARK;


/* Using CTE */
WITH CTE_PREVIOUS_MARK AS
	(SELECT *,
			LAG(MARKS, 1, 0) OVER(ORDER BY TEST_ID) AS PREVIOUS_MARK
		FROM STUDENT_TESTS)
SELECT TEST_ID,
	MARKS
FROM CTE_PREVIOUS_MARK CTE
WHERE CTE.MARKS > CTE.PREVIOUS_MARK;


/* Using LEFT SELF JOIN */
SELECT 
    s1.TEST_ID,
    s1.MARKS
FROM 
    STUDENT_TESTS s1
JOIN 
    STUDENT_TESTS s2 ON s1.TEST_ID = s2.TEST_ID + 1
WHERE 
    s1.MARKS > s2.MARKS;


```


### **Remarques**

- La première requête LEFT SELF JOIN (avec COALESCE) remplace les valeurs NULL par 0, ce qui permet à la condition de la clause WHERE d'être évaluée pour toutes les lignes de s1, même si aucune correspondance n'est trouvée dans s2.
- La deuxième requête LEFT SELF JOIN ne traite pas les valeurs NULL de s2.MARKS, donc les lignes où s2.MARKS est NULL sont exclues du résultat final.
C'est pourquoi la première requête avec COALESCE peut inclure la première ligne, car même si s2.MARKS est NULL pour cette ligne, il est remplacé par 0 grâce à COALESCE, ce qui permet à la condition s1.MARKS > 0 d'être évaluée comme vraie.





