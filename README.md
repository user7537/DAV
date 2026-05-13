= Questions

1. Retrieve names of students enrolled in any society. 
2. Retrieve all society names. 
3. Retrieve students' names starting with the letter ‘A’. 
4. Retrieve students' details studying in courses ‘computer science’ or ‘chemistry’.
5. 5. Retrieve students’ names whose roll no either starts with ‘X’ or ‘Z’ and ends with ‘9’ 
6. Find society details with more than N TotalSeats where N is to be input by the user 
7. Update society table for the mentor name of a specific society 
8. Find society names in which more than five students have enrolled 
9. Find the name of the youngest student enrolled in society ‘NSS’ 
10. Find the name of the most popular society (on the basis of enrolled students) 
11. Find the name of two least popular societies (on the basis of enrolled students) 
12. Find the students names who are not enrolled in any society.  
13. Find the students names enrolled in at least two societies 
14. Find society names in which maximum students are enrolled 
15. Find names of all students who have enrolled in any society and society names in which 
at least one student has enrolled 
16. Find names of students who are enrolled in any of the three societies ‘Debating’, 
‘Dancing’ and ‘Sashakt’. 
17. Find society names such that its mentor has a name with  ‘Gupta’ in it. 
18. Find the society names in which the number of enrolled students is only 10% of its 
capacity. 
19. Display the vacant seats for each society. 
20. Increment Total Seats of each society by 10% 
21. Add the enrollment fees paid (‘yes’/’No’) field in the enrollment table. 
22. Update date of enrollment of society id ‘s1’ to ‘2018-01-15’, ‘s2’ to the current date and 
‘s3’ to ‘2018-01-02’. 
23. Create a view to keep track of society names with the total number of students enrolled in 
it. 
24. Find student names enrolled in all the societies. 
25. Count the number of societies with more than 5 students enrolled in it 
26. Add column Mobile number in student table with default value ‘9999999999’ 
27. Find the total number of students whose age is > 20 years. 
28. Find names of students who were born in 2001 and are enrolled in at least one society. 
29. Count all societies whose name starts with ‘S’ and ends with ‘t’ and at least 5 students are 
enrolled in the society. 
30. Display the following information: 
Society name 
Mentor name 
Total Capacity Total Enrolled Unfilled Seats
II. Do the following database administration commands: 
Create user, create role, grant privileges to a role, revoke privileges from a role, create index 






= I : Schema Creation

```sql
CREATE TABLE STUDENT (
    RollNo CHAR(6) PRIMARY KEY,
    StudentName VARCHAR(20),
    Course VARCHAR(20),
    DOB DATE,
    MobileNumber VARCHAR(10) DEFAULT '9999999999'
);

CREATE TABLE SOCIETY (
    SocID CHAR(6) PRIMARY KEY,
    SocName VARCHAR(20),
    MentorName VARCHAR(30),
    TotalSeats INT UNSIGNED
);

CREATE TABLE ENROLLMENT (
    RollNo CHAR(6),
    SID CHAR(6),
    DateOfEnrollment DATE,
    FeesPaid VARCHAR(3),
    PRIMARY KEY (RollNo, SID),
    FOREIGN KEY (RollNo) REFERENCES STUDENT(RollNo),
    FOREIGN KEY (SID) REFERENCES SOCIETY(SocID)
);

== Question 1 : 
```sql
SELECT DISTINCT s.StudentName
FROM STUDENT s
JOIN ENROLLMENT e ON s.RollNo = e.RollNo;
```
== Question 2

```sql
SELECT SocName FROM SOCIETY;
```

== Question 3
```sql
SELECT StudentName FROM STUDENT
WHERE StudentName LIKE 'A%';
```

== Question 4
```sql
SELECT * FROM STUDENT
WHERE Course IN ('computer science', 'chemistry');
```

== Question 5
```sql
SELECT StudentName FROM STUDENT
WHERE (RollNo LIKE 'X%' OR RollNo LIKE 'Z%')
AND RollNo LIKE '%9';
```

== Question 6
```sql
SELECT * FROM SOCIETY
WHERE TotalSeats > N;
```

== Question 7
```sql
UPDATE SOCIETY
SET MentorName = 'New Mentor'
WHERE SocID = 's1';
```

== Question 8
```sql
SELECT SocName
FROM SOCIETY s
JOIN ENROLLMENT e ON s.SocID = e.SID
GROUP BY SocName
HAVING COUNT(*) > 5;
```

== Question 9
``` sql
SELECT s.StudentName
FROM STUDENT s
JOIN ENROLLMENT e ON s.RollNo = e.RollNo
JOIN SOCIETY so ON so.SocID = e.SID
WHERE so.SocName = 'NSS'
ORDER BY s.DOB DESC
LIMIT 1;
```

== Question 10
```sql
SELECT so.SocName
FROM SOCIETY so
JOIN ENROLLMENT e ON so.SocID = e.SID
GROUP BY so.SocName
ORDER BY COUNT(*) DESC
LIMIT 1;
```

== Question 11
```sql
SELECT so.SocName
FROM SOCIETY so
LEFT JOIN ENROLLMENT e ON so.SocID = e.SID
GROUP BY so.SocName
ORDER BY COUNT(e.RollNo) ASC
LIMIT 2;
```

== Question 12
``` sq;
SELECT StudentName
FROM STUDENT
WHERE RollNo NOT IN (SELECT RollNo FROM ENROLLMENT);
```

== Question 13
```sql
SELECT s.StudentName
FROM STUDENT s
JOIN ENROLLMENT e ON s.RollNo = e.RollNo
GROUP BY s.StudentName
HAVING COUNT(e.SID) >= 2;
```

== Question 14
```sql
SELECT so.SocName
FROM SOCIETY so
JOIN ENROLLMENT e ON so.SocID = e.SID
GROUP BY so.SocName
HAVING COUNT(*) = (
    SELECT MAX(cnt)
    FROM (
        SELECT COUNT(*) cnt
        FROM ENROLLMENT
        GROUP BY SID
    ) t
);
```

== Question 15
```sql
SELECT DISTINCT s.StudentName, so.SocName
FROM STUDENT s
JOIN ENROLLMENT e ON s.RollNo = e.RollNo
JOIN SOCIETY so ON so.SocID = e.SID;
```

== Question 16
```sql
SELECT DISTINCT s.StudentName
FROM STUDENT s
JOIN ENROLLMENT e ON s.RollNo = e.RollNo
JOIN SOCIETY so ON so.SocID = e.SID
WHERE so.SocName IN ('Debating', 'Dancing', 'Sashakt');
```

== Question 17
```sql
SELECT SocName
FROM SOCIETY
WHERE MentorName LIKE '%Gupta%';
```

== Question 18
```sql
SELECT so.SocName
FROM SOCIETY so
LEFT JOIN ENROLLMENT e ON so.SocID = e.SID
GROUP BY so.SocName, so.TotalSeats
HAVING COUNT(e.RollNo) = 0.1 * so.TotalSeats;
```

== Question 19
```sql
SELECT so.SocName,
       so.TotalSeats - COUNT(e.RollNo) AS VacantSeats
FROM SOCIETY so
LEFT JOIN ENROLLMENT e ON so.SocID = e.SID
GROUP BY so.SocName, so.TotalSeats;
```

== Question 20
```sql
UPDATE SOCIETY
SET TotalSeats = TotalSeats * 1.1;
```

== Question 21
```sql
SELECT * FROM ENROLLMENT;
```

== Question 22
```sql
UPDATE ENROLLMENT
SET DateOfEnrollment = CASE
    WHEN SID = 's1' THEN '2018-01-15'
    WHEN SID = 's2' THEN CURRENT_DATE
    WHEN SID = 's3' THEN '2018-01-02'
END;
```

== Question 23
```sql
CREATE VIEW SocietyEnrollment AS
SELECT so.SocName, COUNT(e.RollNo) AS TotalStudents
FROM SOCIETY so
LEFT JOIN ENROLLMENT e ON so.SocID = e.SID
GROUP BY so.SocName;
```
== Question 24
```sql
SELECT StudentName
FROM STUDENT s
WHERE NOT EXISTS (
    SELECT SocID FROM SOCIETY
    EXCEPT
    SELECT SID FROM ENROLLMENT e WHERE e.RollNo = s.RollNo
);
```
== Question 25
```sql
SELECT COUNT(*)

FROM (
    SELECT SID
    FROM ENROLLMENT
    GROUP BY SID
    HAVING COUNT(*) > 5
) t;
```

== Question 26
```
SELECT * FROM STUDENT;
```

== Question 27
```sql
SELECT COUNT(*)
FROM STUDENT
WHERE TIMESTAMPDIFF(YEAR, DOB, CURDATE()) > 20;
```

== Question 28
```sql
SELECT DISTINCT s.StudentName
FROM STUDENT s
JOIN ENROLLMENT e ON s.RollNo = e.RollNo
WHERE YEAR(s.DOB) = 2001;
```

== Question 29
```sql
SELECT COUNT(*)
FROM (
    SELECT so.SocName
    FROM SOCIETY so
    JOIN ENROLLMENT e ON so.SocID = e.SID
    WHERE so.SocName LIKE 'S%t'
    GROUP BY so.SocName
    HAVING COUNT(*) >= 5
) t;
```

== Question 30
```sql
SELECT so.SocName,
       so.MentorName,
       so.TotalSeats AS TotalCapacity,
       COUNT(e.RollNo) AS TotalEnrolled,
       so.TotalSeats - COUNT(e.RollNo) AS UnfilledSeats
FROM SOCIETY so
LEFT JOIN ENROLLMENT e ON so.SocID = e.SID
GROUP BY so.SocName, so.MentorName, so.TotalSeats;
```

== DBA 1
```sql
CREATE USER 'user1'@'localhost' IDENTIFIED BY 'password';
```

== DBA 2
```sql
CREATE ROLE 'role1';
```

== DBA 3
```sql
GRANT SELECT, INSERT, UPDATE ON *.* TO 'role1';
```

== DBA 4
```sql
GRANT 'role1' TO 'user1'@'localhost';
```

== DBA 5
```sql
REVOKE INSERT ON *.* FROM 'role1';
```
== DBA 6
```sql
CREATE INDEX idx_student_name ON STUDENT(StudentName);
```


To  
The Embassy of Japan / Selection Committee  
MEXT Scholarship Program  

Subject: Recommendation Letter for Mr. Vivaan Singh Adhikari  

I am pleased to recommend Mr. Vivaan Singh Adhikari for the Japanese Government (MEXT) Scholarship. I have known him in my capacity as his professor at Atma Ram Sanatan Dharma College, University of Delhi, where he is currently pursuing B.Sc. (Hons.) Computer Science.

During my interaction with Vivaan, I have found him to be a sincere, disciplined, and responsible student. He demonstrates a serious attitude toward his academic responsibilities and possesses the adaptability and commitment required to function effectively in a rigorous academic environment.

Vivaan is respectful in his conduct, cooperative with peers, and maintains a professional attitude in academic settings. He has shown the potential for further growth in initiative and confidence, particularly in new and challenging environments.

I believe he has strong potential for academic growth through undergraduate studies in Japan and will make productive use of the opportunities provided through the MEXT Scholarship. I am confident that the knowledge and experience gained will contribute positively to his future academic and professional development.

I strongly recommend Mr. Vivaan Singh Adhikari for the Japanese Government (MEXT) Scholarship.

Sincerely,

[Teacher Name]  
[Designation]  
Department of Computer Science  
Atma Ram Sanatan Dharma College  
University of Delhi  

Signature: __________  
Date: __________


