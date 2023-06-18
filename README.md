# Oracle-DB
Scenario based PL/SQL code and password validation /Hospital details.

SET SERVEROUTPUT ON;

CREATE TABLE PATIENT_MASTER
(
PATIENT_ID NUMBER PRIMARY KEY,
PATIENT_NAME VARCHAR2(45) NOT NULL,
GENDER VARCHAR2(8) NOT NULL,
DATE_OF_BIRTH DATE NOT NULL,
PHONE_NUMBER VARCHAR2(12) NOT NULL UNIQUE
);

INSERT INTO PATIENT_MASTER VALUES(1,'ALEX','MALE','05-02-81','9825121079');
INSERT INTO PATIENT_MASTER VALUES(2,'SCOTT','MALE','21-04-88','9825451079');
INSERT INTO PATIENT_MASTER VALUES(3,'OLIVIA','FEMALE','09-03-86','9865121079');
INSERT INTO PATIENT_MASTER VALUES(4,'MIKE','MALE','16-07-90','9825121779');
INSERT INTO PATIENT_MASTER VALUES(5,'MAC','MALE','19-02-83','9825121479');
INSERT INTO PATIENT_MASTER VALUES(6,'JERRY','MALE','12-06-84','9825131079');
INSERT INTO PATIENT_MASTER VALUES(7,'HARRY','MALE','17-03-80','9825122379');
INSERT INTO PATIENT_MASTER VALUES(8,'KATTY','FEMALE','08-03-90','9825231079');
INSERT INTO PATIENT_MASTER VALUES(9,'TOM','MALE','22-05-91','9825121099');
INSERT INTO PATIENT_MASTER VALUES(10,'JACK','MALE','24-06-95','9825121089');


CREATE TABLE DISEASE_MASTER 
(
DISEASE_ID NUMBER PRIMARY KEY,
DISEASE_NAME VARCHAR2(25) NOT NULL
);


INSERT INTO DISEASE_MASTER  VALUES (1, 'INFLUENZA');
INSERT INTO DISEASE_MASTER  VALUES (2, 'COVID-19');
INSERT INTO DISEASE_MASTER  VALUES (3, 'MALARIA');
INSERT INTO DISEASE_MASTER  VALUESSET SERVEROUTPUT ON;

CREATE TABLE PATIENT_MASTER
(
PATIENT_ID NUMBER PRIMARY KEY,
PATIENT_NAME VARCHAR2(45) NOT NULL,
GENDER VARCHAR2(8) NOT NULL,
DATE_OF_BIRTH DATE NOT NULL,
PHONE_NUMBER VARCHAR2(12) NOT NULL UNIQUE
);


INSERT INTO PATIENT_MASTER VALUES(1,'ALEX','MALE','05-02-81','9825121079');
INSERT INTO PATIENT_MASTER VALUES(2,'SCOTT','MALE','21-04-88','9825451079');
INSERT INTO PATIENT_MASTER VALUES(3,'OLIVIA','FEMALE','09-03-86','9865121079');
INSERT INTO PATIENT_MASTER VALUES(4,'MIKE','MALE','16-07-90','9825121779');
INSERT INTO PATIENT_MASTER VALUES(5,'MAC','MALE','19-02-83','9825121479');
INSERT INTO PATIENT_MASTER VALUES(6,'JERRY','MALE','12-06-84','9825131079');
INSERT INTO PATIENT_MASTER VALUES(7,'HARRY','MALE','17-03-80','9825122379');
INSERT INTO PATIENT_MASTER VALUES(8,'KATTY','FEMALE','08-03-90','9825231079');
INSERT INTO PATIENT_MASTER VALUES(9,'TOM','MALE','22-05-91','9825121099');
INSERT INTO PATIENT_MASTER VALUES(10,'JACK','MALE','24-06-95','9825121089');


CREATE TABLE DISEASE_MASTER 
(
DISEASE_ID NUMBER PRIMARY KEY,
DISEASE_NAME VARCHAR2(25) NOT NULL
);


INSERT INTO DISEASE_MASTER  VALUES (1, 'INFLUENZA');
INSERT INTO DISEASE_MASTER  VALUES (2, 'COVID-19');
INSERT INTO DISEASE_MASTER  VALUES (3, 'MALARIA');
INSERT INTO DISEASE_MASTER  VALUES (4, 'CHOLERAHOLERA');
INSERT INTO DISEASE_MASTER  VALUES (5, 'TUBERCULOSIS');
INSERT INTO DISEASE_MASTER  VALUES (6, 'DENGUE');
INSERT INTO DISEASE_MASTER  VALUES (7, 'HIV/AIDS');
INSERT INTO DISEASE_MASTER  VALUES (8, 'CANCER');
INSERT INTO DISEASE_MASTER  VALUES (9, 'ALZHEIMER');
INSERT INTO DISEASE_MASTER  VALUES (10, 'PARKINSON');


SELECT * FROM PATIENT_MASTER;
SELECT * FROM DISEASE_MASTER;
SELECT * FROM PRESCRIPTION;


CREATE TABLE PRESCRIPTION
(
PRESCRIPTION_ID NUMBER PRIMARY KEY,
DOCTOR_NAME VARCHAR2(25),
DATE_OF_VISIT DATE ,
PATIENT_ID NUMBER ,
DISEASE_ID NUMBER,
PRESCRIPTION_DETAILS VARCHAR2(500),
FOREIGN KEY (PATIENT_ID) REFERENCES PATIENT_MASTER(PATIENT_ID),
FOREIGN KEY (DISEASE_ID) REFERENCES DISEASE_MASTER (DISEASE_ID)
);

CREATE SEQUENCE PRESCRIPTION_ID MINVALUE 1 START WITH 1 INCREMENT BY 1 NOCACHE;


CREATE OR REPLACE PROCEDURE Doctors_Pens 
(
V_PHONE_NUMBER IN NUMBER,
V_DOCTOR_NAME IN  VARCHAR2,
V_DATE_OF_VISIT IN  DATE,
V_PATIENT_ID IN NUMBER,
V_DISEASE_ID IN NUMBER ,
V_PRESCRIPTION_DETAILS IN VARCHAR2,
ACTION IN VARCHAR2
)
AS

V_PATIENT PATIENT_MASTER%ROWTYPE;
CURSOR C_PATIENT IS SELECT * FROM PATIENT_MASTER WHERE PHONE_NUMBER = V_PHONE_NUMBER;

RES VARCHAR2(10);
BEGIN
SELECT COUNT(*) INTO RES FROM PATIENT_MASTER WHERE PHONE_NUMBER = V_PHONE_NUMBER;

IF LENGTH(V_PHONE_NUMBER)> 10 AND ACTION = 'VIEW' THEN 
  DBMS_OUTPUT.PUT_LINE('Please enter 10 digit phone number !! Thank you.');
  
   ELSIF RES = 1 THEN 
     OPEN C_PATIENT;
     LOOP
     FETCH C_PATIENT INTO V_PATIENT;
     EXIT WHEN C_PATIENT%NOTFOUND;
     DBMS_OUTPUT.PUT_LINE(V_PATIENT.PATIENT_NAME||' '||V_PATIENT.GENDER||' '||V_PATIENT.DATE_OF_BIRTH||' '||V_PATIENT.PHONE_NUMBER);
     END LOOP;
     CLOSE C_PATIENT;
  
    ELSE 
    DBMS_OUTPUT.PUT_LINE('Patient NOT FOUND !! Please register patient before consultation.');
   
   END IF;
   
       IF ACTION = 'SAVE' THEN
      INSERT INTO PRESCRIPTION(DOCTOR_NAME,DATE_OF_VISIT,PATIENT_ID,DISEASE_ID,PRESCRIPTION_DETAILS)
         VALUES (V_DOCTOR_NAME,V_DATE_OF_VISIT,V_PATIENT_ID,V_DISEASE_ID,V_PRESCRIPTION_DETAILS);
     END IF;
         
        IF ACTION = 'PATIENT HISTORY' THEN
    
      FOR R IN(SELECT * FROM PATIENT_MASTER P
               JOIN DISEASE_MASTER D ON P.PATIENT_ID = D.DISEASE_ID
               JOIN PRESCRIPTION PR ON P.PATIENT_ID = PR.PATIENT_ID
               WHERE PHONE_NUMBER = V_PHONE_NUMBER)
          
               LOOP
               DBMS_OUTPUT.PUT_LINE(R.PATIENT_NAME||' '||' '||R.GENDER||' '||R.DATE_OF_BIRTH||' '||R.DISEASE_NAME||' '||' '||R.DOCTOR_NAME||' 
               '||R.DATE_OF_VISIT||' '||R.PRESCRIPTION_DETAILS);
        END LOOP;
          END IF;
   
EXCEPTION
    WHEN NO_DATA_FOUND
                      THEN
     DBMS_OUTPUT.PUT_LINE('Check your above code carefully. BYE');
       

END;

DECLARE
V_PATIENT PATIENT_MASTER%ROWTYPE;
BEGIN
Doctors_pens(&ENTER_PHONE_NUMBER,NULL,NULL,NULL,NULL,NULL,'VIEW');
--DBMS_OUTPUT.PUT_LINE('PATIENT DETAILS: '||V_PATIENT.PATIENT_NAME||' '||V_PATIENT.GENDER||' '||V_PATIENT.DATE_OF_BIRTH||' '||V_PATIENT.PHONE_NUMBER);
END;


--- MEDIACL HISTORY ---
DECLARE
ACTION VARCHAR2(10) ;
BEGIN
DOCTORS_PENS(&ENTER_PATIENT_PHONE_NUMBER,NULL,NULL,NULL,NULL,NULL,'PATIENT HISTORY');
END;





















SELECT DATE_OF_BIRTH,FLOOR(MONTHS_BETWEEN(TRUNC(SYSDATE),DATE_OF_BIRTH)/12) AGE FROM PATIENT_MASTER;

SELECT P.PATIENT_NAME,GENDER,DATE_OF_BIRTH,FLOOR(MONTHS_BETWEEN(TRUNC(SYSDATE),DATE_OF_BIRTH)/12) AGE ,D.DISEASE_NAME,PR.DOCTOR_NAME,DATE_OF_VISIT,PRESCRIPTION_DETAILS
FROM PATIENT_MASTER P JOIN DISEASE_MASTER D ON P.PATIENT_ID = D.DISEASE_ID
JOIN PRESCRIPTION PR ON P.PATIENT_ID = PR.PATIENT_ID;


DECLARE
  ACTION VARCHAR2(10) := 'SAVE';
BEGIN
  DOCTORS_PENS('DR.AMIT SINGH',TO_DATE('14-03-23','DD-MM-YY'),01,02,'The infectious disease caused by the most recently discovered coronavirus.',ACTION); -- Call the stored procedure with the action parameter
  
  --INSERT INTO PRESCRIPTION (DOCTOR_NAME,DATE_OF_VISIT,PATIENT_ID,DISEASE_ID,PRESCRIPTION_DETAILS)
 -- VALUES ('DR.AMIT SINGH',TO_DATE('14-03-23','DD-MM-YY'),01,02,'The infectious disease caused by the most recently discovered coronavirus.');
END;






VAR V_PHONE_NUMBER NUMBER
VAR V_PATIENT_NAME VARCHAR2(10)
VAR V_GENDER VARCHAR2(8)
VAR V_DATE_OF_BIRTH PATIENT
VAR P_PHONE_NUMBER VARCHAR2(14)
EXEC :V_PHONE_NUMBER := &ENTER_PATIENT_PHONE_NUMBER
EXEC DOCTORS_PENS(:V_PHONE_NUMBER,:V_PATIENT_NAME ,:V_GENDER,:V_DATE_OF_BIRTH,:P_PHONE_NUMBER);
PRINT V_PATIENT_NAME 
PRINT V_GENDER
PRINT V_DATE_OF_BIRTH
PRINT P_PHONE_NUMBER


 SELECT P.PATIENT_NAME,GENDER,DATE_OF_BIRTH,FLOOR(MONTHS_BETWEEN(TRUNC(SYSDATE),DATE_OF_BIRTH)/12) AGE ,
    D.DISEASE_NAME,PR.DOCTOR_NAME,DATE_OF_VISIT,PRESCRIPTION_DETAILS
   FROM PATIENT_MASTER P
   JOIN DISEASE_MASTER D
   ON P.PATIENT_ID = D.DISEASE_ID
   JOIN PRESCRIPTION PR
   ON P.PATIENT_ID = PR.PATIENT_ID
   WHERE;

SELECT PATIENT_NAME,GENDER,DATE_OF_BIRTH,PHONE_NUMBER 
       INTO V_PATIENT_NAME ,V_GENDER ,V_DATE_OF_BIRTH ,P_PHONE_NUMBER  
         FROM PATIENT_MASTER
           WHERE PHONE_NUMBER = V_PHONE_NUMBER;
           
           
           
        CREATE OR REPLACE PROCEDURE JOINS
        
        IS BEGIN
        FOR R IN(SELECT * FROM PATIENT_MASTER P
          JOIN DISEASE_MASTER D ON P.PATIENT_ID = D.DISEASE_ID
          JOIN PRESCRIPTION PR ON P.PATIENT_ID = PR.PATIENT_ID)
          
          LOOP
          DBMS_OUTPUT.PUT_LINE(R.PATIENT_NAME||' '||R.DISEASE_NAME||' '||R.PRESCRIPTION_DETAILS);
          END LOOP;
          END;
          
          BEGIN
          JOINS;
          END; (4, 'CHOLERAholera');
INSERT INTO DISEASE_MASTER  VALUES (5, 'TUBERCULOSIS');
INSERT INTO DISEASE_MASTER  VALUES (6, 'DENGUE');
INSERT INTO DISEASE_MASTER  VALUES (7, 'HIV/AIDS');
INSERT INTO DISEASE_MASTER  VALUES (8, 'CANCER');
INSERT INTO DISEASE_MASTER  VALUES (9, 'ALZHEIMER');
INSERT INTO DISEASE_MASTER  VALUES (10, 'PARKINSON');


SELECT * FROM PATIENT_MASTER;
SELECT * FROM DISEASE_MASTER;
SELECT * FROM PRESCRIPTION;


CREATE TABLE PRESCRIPTION
(
PRESCRIPTION_ID NUMBER PRIMARY KEY,
DOCTOR_NAME VARCHAR2(25),
DATE_OF_VISIT DATE ,
PATIENT_ID NUMBER ,
DISEASE_ID NUMBER,
PRESCRIPTION_DETAILS VARCHAR2(500),
FOREIGN KEY (PATIENT_ID) REFERENCES PATIENT_MASTER(PATIENT_ID),
FOREIGN KEY (DISEASE_ID) REFERENCES DISEASE_MASTER (DISEASE_ID)
);



CREATE SEQUENCE PRESCRIPTION_ID MINVALUE 1 START WITH 1 INCREMENT BY 1 NOCACHE;




CREATE OR REPLACE PROCEDURE Doctors_Pens 
(
V_PHONE_NUMBER IN NUMBER,
V_DOCTOR_NAME IN  VARCHAR2,
V_DATE_OF_VISIT IN  DATE,
V_PATIENT_ID IN NUMBER,
V_DISEASE_ID IN NUMBER ,
V_PRESCRIPTION_DETAILS IN VARCHAR2,
ACTION IN VARCHAR2
)
AS

V_PATIENT PATIENT_MASTER%ROWTYPE;
CURSOR C_PATIENT IS SELECT * FROM PATIENT_MASTER WHERE PHONE_NUMBER = V_PHONE_NUMBER;

RES VARCHAR2(10);
BEGIN
SELECT COUNT(*) INTO RES FROM PATIENT_MASTER WHERE PHONE_NUMBER = V_PHONE_NUMBER;

IF LENGTH(V_PHONE_NUMBER)> 10 AND ACTION = 'VIEW' THEN 
  DBMS_OUTPUT.PUT_LINE('Please enter 10 digit phone number !! Thank you.');
  
   ELSIF RES = 1 THEN 
     OPEN C_PATIENT;
     LOOP
     FETCH C_PATIENT INTO V_PATIENT;
     EXIT WHEN C_PATIENT%NOTFOUND;
     DBMS_OUTPUT.PUT_LINE(V_PATIENT.PATIENT_NAME||' '||V_PATIENT.GENDER||' '||V_PATIENT.DATE_OF_BIRTH||' '||V_PATIENT.PHONE_NUMBER);
     END LOOP;
     CLOSE C_PATIENT;
  
    ELSE 
    DBMS_OUTPUT.PUT_LINE('Patient NOT FOUND !! Please register patient before consultation.');
   
   END IF;
   
       IF ACTION = 'SAVE' THEN
      INSERT INTO PRESCRIPTION(DOCTOR_NAME,DATE_OF_VISIT,PATIENT_ID,DISEASE_ID,PRESCRIPTION_DETAILS)
         VALUES (V_DOCTOR_NAME,V_DATE_OF_VISIT,V_PATIENT_ID,V_DISEASE_ID,V_PRESCRIPTION_DETAILS);
     END IF;
         
        IF ACTION = 'PATIENT HISTORY' THEN
    
      FOR R IN(SELECT * FROM PATIENT_MASTER P
               JOIN DISEASE_MASTER D ON P.PATIENT_ID = D.DISEASE_ID
               JOIN PRESCRIPTION PR ON P.PATIENT_ID = PR.PATIENT_ID
               WHERE PHONE_NUMBER = V_PHONE_NUMBER)
          
               LOOP
               DBMS_OUTPUT.PUT_LINE(R.PATIENT_NAME||' '||' '||R.GENDER||' '||R.DATE_OF_BIRTH||' '||R.DISEASE_NAME||' '||' '||R.DOCTOR_NAME||' 
               '||R.DATE_OF_VISIT||' '||R.PRESCRIPTION_DETAILS);
        END LOOP;
          END IF;
   
EXCEPTION
    WHEN NO_DATA_FOUND
                      THEN
     DBMS_OUTPUT.PUT_LINE('Check your above code carefully. BYE');
       

END;

DECLARE
V_PATIENT PATIENT_MASTER%ROWTYPE;
BEGIN
Doctors_pens(&ENTER_PHONE_NUMBER,NULL,NULL,NULL,NULL,NULL,'VIEW');
--DBMS_OUTPUT.PUT_LINE('PATIENT DETAILS: '||V_PATIENT.PATIENT_NAME||' '||V_PATIENT.GENDER||' '||V_PATIENT.DATE_OF_BIRTH||' '||V_PATIENT.PHONE_NUMBER);
END;


--- MEDIACL HISTORY ---
DECLARE
ACTION VARCHAR2(10) ;
BEGIN
DOCTORS_PENS(&ENTER_PATIENT_PHONE_NUMBER,NULL,NULL,NULL,NULL,NULL,'PATIENT HISTORY');
END;





















SELECT DATE_OF_BIRTH,FLOOR(MONTHS_BETWEEN(TRUNC(SYSDATE),DATE_OF_BIRTH)/12) AGE FROM PATIENT_MASTER;

SELECT P.PATIENT_NAME,GENDER,DATE_OF_BIRTH,FLOOR(MONTHS_BETWEEN(TRUNC(SYSDATE),DATE_OF_BIRTH)/12) AGE ,D.DISEASE_NAME,PR.DOCTOR_NAME,DATE_OF_VISIT,PRESCRIPTION_DETAILS
FROM PATIENT_MASTER P JOIN DISEASE_MASTER D ON P.PATIENT_ID = D.DISEASE_ID
JOIN PRESCRIPTION PR ON P.PATIENT_ID = PR.PATIENT_ID;


DECLARE
  ACTION VARCHAR2(10) := 'SAVE';
BEGIN
  DOCTORS_PENS('DR.AMIT SINGH',TO_DATE('14-03-23','DD-MM-YY'),01,02,'The infectious disease caused by the most recently discovered coronavirus.',ACTION); -- Call the stored procedure with the action parameter
  
  --INSERT INTO PRESCRIPTION (DOCTOR_NAME,DATE_OF_VISIT,PATIENT_ID,DISEASE_ID,PRESCRIPTION_DETAILS)
 -- VALUES ('DR.AMIT SINGH',TO_DATE('14-03-23','DD-MM-YY'),01,02,'The infectious disease caused by the most recently discovered coronavirus.');
END;






VAR V_PHONE_NUMBER NUMBER
VAR V_PATIENT_NAME VARCHAR2(10)
VAR V_GENDER VARCHAR2(8)
VAR V_DATE_OF_BIRTH PATIENT
VAR P_PHONE_NUMBER VARCHAR2(14)
EXEC :V_PHONE_NUMBER := &ENTER_PATIENT_PHONE_NUMBER
EXEC DOCTORS_PENS(:V_PHONE_NUMBER,:V_PATIENT_NAME ,:V_GENDER,:V_DATE_OF_BIRTH,:P_PHONE_NUMBER);
PRINT V_PATIENT_NAME 
PRINT V_GENDER
PRINT V_DATE_OF_BIRTH
PRINT P_PHONE_NUMBER


 SELECT P.PATIENT_NAME,GENDER,DATE_OF_BIRTH,FLOOR(MONTHS_BETWEEN(TRUNC(SYSDATE),DATE_OF_BIRTH)/12) AGE ,
    D.DISEASE_NAME,PR.DOCTOR_NAME,DATE_OF_VISIT,PRESCRIPTION_DETAILS
   FROM PATIENT_MASTER P
   JOIN DISEASE_MASTER D
   ON P.PATIENT_ID = D.DISEASE_ID
   JOIN PRESCRIPTION PR
   ON P.PATIENT_ID = PR.PATIENT_ID
   WHERE;

SELECT PATIENT_NAME,GENDER,DATE_OF_BIRTH,PHONE_NUMBER 
       INTO V_PATIENT_NAME ,V_GENDER ,V_DATE_OF_BIRTH ,P_PHONE_NUMBER  
         FROM PATIENT_MASTER
           WHERE PHONE_NUMBER = V_PHONE_NUMBER;
           
           
           
        CREATE OR REPLACE PROCEDURE JOINS
        
        IS BEGIN
        FOR R IN(SELECT * FROM PATIENT_MASTER P
          JOIN DISEASE_MASTER D ON P.PATIENT_ID = D.DISEASE_ID
          JOIN PRESCRIPTION PR ON P.PATIENT_ID = PR.PATIENT_ID)
          
          LOOP
          DBMS_OUTPUT.PUT_LINE(R.PATIENT_NAME||' '||R.DISEASE_NAME||' '||R.PRESCRIPTION_DETAILS);
          END LOOP;
          END;
          
          BEGIN
          JOINS;
          END;
