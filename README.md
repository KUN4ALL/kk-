# CREATE DATABASE
CREATE DATABASE DSC;

# USE DATABASE
USE  DSC;



# TO CREATE TABLE STUDENT 
CREATE TABLE STUDENT ( ROLLNO VARCHAR(10) PRIMARY KEY, STUDENTNAME VARCHAR(50),COURSE VARCHAR(50), DOB DATE  );


# CREATE TABLE SOCIETY
CREATE TABLE SOCIETY (SOCID INT PRIMARY KEY,SOCNAME VARCHAR(50),MENTORNAME VARCHAR(50),TOTALSEATS INT );

# TO CREATE TABLE ENROLLMENT
CREATE TABLE ENROLLMENT (
    ROLLNO VARCHAR(10),               
    SOCID INT,                        
    ENROLLMENTDATE DATE,           
    PRIMARY KEY (ROLLNO, SOCID),     
    FOREIGN KEY (ROLLNO) REFERENCES STUDENT(ROLLNO), 
    FOREIGN KEY (SOCID) REFERENCES SOCIETY(SOCID)   
);


# INSERT COMMANDS FOR STUDENT 
 INSERT INTO STUDENT (ROLLNO, STUDENTNAME, COURSE, DOB) 
 VALUES 
  ('101', 'John Doe', 'Computer Science', '2000-05-15'),
  ('102', 'Jane Smith', 'Mathematics', '1999-07-21'),
  ('103', 'Alice Brown', 'Physics', '2001-03-12'),
  ('104', 'David Wilson', 'Chemistry', '2000-11-08'),
  ('105', 'Emily Davis', 'Biology', '1998-02-25'),
  ('106', 'Michael Johnson', 'Engineering', '2000-06-30'),
  ('107', 'Sarah Lee', 'Economics', '1999-12-05');
  

# INSERT COMMANDS FOR SOCIETY
INSERT INTO SOCIETY (SOCID, SOCNAME, MENTORNAME, TOTALSEATS)    
VALUES 
    (1, 'Drama Club', 'Mr. Johnson', 30),
    (2, 'Science Society', 'Dr. Smith', 25),
    (3, 'Literature Club', 'Ms. Adams', 20),
    (4, 'Music Band', 'Mr. Brown', 15),
    (5, 'Art Society', 'Ms. Clarke', 18),
    (6, 'Robotics Club', 'Dr. Lee', 10),
    (7, 'Sports Committee', 'Mr. Green', 50),  
    (8, 'NSS', 'Dr. Gupta', 80);              

# INSERT COMMANDS FOR ENROLLMENT 
INSERT INTO ENROLLMENT (ROLLNO, SOCID, ENROLLMENTDATE)
VALUES 
    ('101', 1, '2024-01-15'),
    ('102', 2, '2024-01-16'),
    ('103', 3, '2024-01-17'),
    ('104', 4, '2024-01-18'),
    ('105', 5, '2024-01-19'),
    ('106', 6, '2024-01-20'),
    ('107', 7, '2024-01-21'),
    ('101', 8, '2024-02-01'),
    ('102', 8, '2024-02-02')
ON DUPLICATE KEY UPDATE ENROLLMENTDATE = VALUES(ENROLLMENTDATE);

# PRACTICAL QUERIES #

1. Retrieve names of students enrolled in any society:

SELECT STUDENTNAME 
FROM STUDENT 
WHERE ROLLNO IN (SELECT ROLLNO FROM ENROLLMENT);

2. Retrieve all society names:

SELECT SOCNAME FROM SOCIETY;

3. Retrieve students' names starting with the letter 'A':

SELECT STUDENTNAME 
FROM STUDENT 
WHERE STUDENTNAME LIKE 'A%';

4. Retrieve students studying in courses 'Computer Science' or 'Chemistry':

SELECT * 
FROM STUDENT 
WHERE COURSE IN ('Computer Science', 'Chemistry');

5. Retrieve students' names whose roll numbers start with 'X' or 'Y' and end with '5':

SELECT STUDENTNAME 
FROM STUDENT 
WHERE (ROLLNO LIKE 'X%5' OR ROLLNO LIKE 'Y%5');

6. Find society details with more than N TotalSeats (user input):

SELECT * 
FROM SOCIETY 
WHERE TOTALSEATS > N;
Replace N with the desired number.

7. Update the mentor name of a specific society:

UPDATE SOCIETY 
SET MENTORNAME = 'New Mentor Name' 
WHERE SOCNAME = 'Specific Society Name';


8. Find society names in which more than five students have enrolled:
SELECT SOCNAME 
FROM SOCIETY S 
JOIN ENROLLMENT E ON S.SOCID = E.SOCID 
GROUP BY S.SOCID, S.SOCNAME 
HAVING COUNT(E.ROLLNO) > 5;

9. Find the name of the youngest student enrolled in society 'NSS':
SELECT STUDENTNAME 
FROM STUDENT 
WHERE ROLLNO IN (SELECT ROLLNO FROM ENROLLMENT WHERE SOCID = (SELECT SOCID FROM SOCIETY WHERE SOCNAME = 'NSS')) 
ORDER BY DOB DESC 
LIMIT 1;

10. Find the name of the most popular society (on the basis of enrolled students):
SELECT SOCNAME 
FROM SOCIETY S 
JOIN ENROLLMENT E ON S.SOCID = E.SOCID 
GROUP BY S.SOCID, S.SOCNAME 
ORDER BY COUNT(E.ROLLNO) DESC 
LIMIT 1;

11. Find the names of two least popular societies:
SELECT SOCNAME 
FROM SOCIETY S 
JOIN ENROLLMENT E ON S.SOCID = E.SOCID 
GROUP BY S.SOCID, S.SOCNAME 
ORDER BY COUNT(E.ROLLNO) ASC 
LIMIT 2;

12. Find the names of students who are not enrolled in any society:

SELECT STUDENTNAME 
FROM STUDENT 
WHERE ROLLNO NOT IN (SELECT ROLLNO FROM ENROLLMENT);

13.Find the names of students enrolled in at least two societies:
SELECT STUDENTNAME 
FROM STUDENT S 
JOIN ENROLLMENT E ON S.ROLLNO = E.ROLLNO 
GROUP BY S.ROLLNO, S.STUDENTNAME 
HAVING COUNT(E.SOCID) >= 2;

14. Find the society names in which the maximum students are enrolled:
SELECT SOCNAME 
FROM SOCIETY S 
JOIN ENROLLMENT E ON S.SOCID = E.SOCID 
GROUP BY S.SOCID, S.SOCNAME 
ORDER BY COUNT(E.ROLLNO) DESC 
LIMIT 1;

15. Find names of all students and societies with at least one enrollment:
SELECT STUDENTNAME, SOCNAME 
FROM STUDENT S 
JOIN ENROLLMENT E ON S.ROLLNO = E.ROLLNO 
JOIN SOCIETY SOC ON E.SOCID = SOC.SOCID;

16. Find names of students enrolled in 'Debating', 'Dancing', or 'Sashakt':
SELECT STUDENTNAME 
FROM STUDENT 
WHERE ROLLNO IN (
    SELECT ROLLNO 
    FROM ENROLLMENT 
    WHERE SOCID IN (
        SELECT SOCID 
        FROM SOCIETY 
        WHERE SOCNAME IN ('Debating', 'Dancing', 'Sashakt')
    )
);

17. Find society names whose mentor has 'Gupta' in their name:

SELECT SOCNAME 
FROM SOCIETY 
WHERE MENTORNAME LIKE '%Gupta%';

18. Find society names where enrolled students are only 10% of its capacity:
SELECT SOCNAME 
FROM SOCIETY S 
JOIN ENROLLMENT E ON S.SOCID = E.SOCID 
GROUP BY S.SOCID, S.SOCNAME, S.TOTALSEATS 
HAVING COUNT(E.ROLLNO) <= S.TOTALSEATS * 0.10;

19. Display the vacant seats for each society:
SELECT S.SOCNAME, S.TOTALSEATS - COUNT(E.ROLLNO) AS VACANT_SEATS
FROM SOCIETY S
LEFT JOIN ENROLLMENT E ON S.SOCID = E.SOCID
GROUP BY S.SOCID, S.TOTALSEATS;

20. Increment Total Seats of each society by 10%:
UPDATE SOCIETY 
SET TOTALSEATS = TOTALSEATS * 1.10;

21. Add the enrollment fees paid (Yes/No) field in the ENROLLMENT table:
ALTER TABLE ENROLLMENT 
ADD ENROLLMENTFEES VARCHAR(3) DEFAULT 'No';

22. Update the enrollment date for specific societies:
UPDATE ENROLLMENT 
SET ENROLLMENTDATE = '2024-01-18' 
WHERE SOCID = 'S1';

UPDATE ENROLLMENT 
SET ENROLLMENTDATE = CURDATE() 
WHERE SOCID = 'S2';

UPDATE ENROLLMENT 
SET ENROLLMENTDATE = '2024-01-15' 
WHERE SOCID = 'S3';


23. Create a view to keep track of society names with the total number of students enrolled in it:
CREATE VIEW SocietyEnrollment AS

SELECT S.SOCNAME, COUNT(E.ROLLNO) AS TOTAL_ENROLLED
FROM SOCIETY S
LEFT JOIN ENROLLMENT E ON S.SOCID = E.SOCID
GROUP BY S.SOCID, S.SOCNAME;

24. Find student names enrolled in all the societies:
SELECT STUDENTNAME 
FROM STUDENT S
WHERE NOT EXISTS (
    SELECT SOCID 
    FROM SOCIETY 
    WHERE SOCID NOT IN (
        SELECT SOCID 
        FROM ENROLLMENT 
        WHERE ROLLNO = S.ROLLNO
    )
);


25. Count the number of societies with more than 5 students enrolled:
SELECT COUNT(*) AS SOCIETIES_WITH_MORE_THAN_5_STUDENTS
FROM (
    SELECT SOCID 
    FROM ENROLLMENT 
    GROUP BY SOCID 
    HAVING COUNT(ROLLNO) > 5
) AS T;

26.Add a column Mobile Number in the STUDENT table with a default value of '9999999999':
ALTER TABLE STUDENT 
ADD MOBILENUMBER VARCHAR(10) DEFAULT '9999999999';


27. Find the total number of students whose age is > 20 years:
SELECT COUNT(*) AS TOTAL_STUDENTS_OVER_20 
FROM STUDENT 
WHERE TIMESTAMPDIFF(YEAR, DOB, CURDATE()) > 20;

28. Find names of students born in 2001 and enrolled in at least one society:
SELECT STUDENTNAME 
FROM STUDENT 
WHERE YEAR(DOB) = 2001 
AND ROLLNO IN (SELECT ROLLNO FROM ENROLLMENT);



29. Count all societies whose name starts with 'S', ends with 'T', and have at least 8 students enrolled:
SELECT COUNT(*) AS SOCIETY_COUNT 
FROM SOCIETY S
JOIN ENROLLMENT E ON S.SOCID = E.SOCID
WHERE SOCNAME LIKE 'S%T'
GROUP BY S.SOCID 
HAVING COUNT(E.ROLLNO) >= 8;

30. Display society details with unfilled seats:
SELECT S.SOCNAME, S.MENTORNAME, S.TOTALSEATS, COUNT(E.ROLLNO) AS TOTAL_ENROLLED, 
       S.TOTALSEATS - COUNT(E.ROLLNO) AS UNFILLED_SEATS
FROM SOCIETY S
LEFT JOIN ENROLLMENT E ON S.SOCID = E.SOCID
GROUP BY S.SOCID, S.SOCNAME, S.MENTORNAME, S.TOTALSEATS;













