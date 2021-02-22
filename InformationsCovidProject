/*Question1*/
DROP TABLE IF EXISTS Pub;
CREATE TABLE Pub(
	PLN VARCHAR(20),
	PubName VARCHAR(100),
	PCounty VARCHAR(30),
	PRIMARY KEY(PLN)
);

DROP TABLE IF EXISTS Person;
CREATE TABLE Person(
	PPSN INT,
	PName VARCHAR(60),
	PCounty VARCHAR (30),
	Age INT,
	DailyPubLimit INT,
	PRIMARY KEY (PPSN)
);

DROP TABLE IF EXISTS Visit;
CREATE TABLE Visit(
	PLN VARCHAR(20),
	PPSN INT,
	StartDateOfVisit DATETIME,
	EndDateOfVisit DATETIME,
	PRIMARY KEY(PLN, PPSN, StartDateOfVisit, EndDateOfVisit),
	FOREIGN KEY (PLN) REFERENCES Pub(PLN) ON DELETE CASCADE,
	FOREIGN KEY (PPSN) REFERENCES Person(PPSN) ON DELETE CASCADE
);

DROP TABLE IF EXISTS NeighbouringCounty;
CREATE TABLE NeighbouringCounty(
	County1 VARCHAR(30),
	County2 VARCHAR(30)

);

DROP TABLE IF EXISTS Covid_Diagnosis;
CREATE TABLE Covid_Diagnosis(
	PPSN INT,
	DiagnosisDate DATE,
	IsolationEndDate DATE,
	PRIMARY KEY (PPSN),
	FOREIGN KEY (PPSN) REFERENCES Person(PPSN) ON DELETE CASCADE
);



/*Question3*/

DROP TRIGGER IF EXISTS CantGoPub
DELIMITER //
CREATE TRIGGER CantGoPub 
BEFORE INSERT ON Visit
FOR EACH ROW
BEGIN 
	
	DECLARE CD_PPSN INT;
	DECLARE Visit_PPSN INT;	
	DECLARE IsoEndDate DATE;
	DECLARE VisitDate DATETIME;

	SELECT Covid_Diagnosis.PPSN, Visit.PPSN, Covid_Diagnosis.IsolationEndDate, Visit.StartDateOfVisit  
	INTO CD_PPSN, Visit_PPSN, IsoEndDate, VisitDate
	FROM Covid_Diagnosis JOIN Visit ON Covid_Diagnosis.PPSN = Visit.PPSN;
	
	IF (IsoEndDate > VisitDate) THEN
		SIGNAL SQLSTATE '45000' 
		SET MESSAGE_TEXT ='Error can not go to the pub when in isolation';
	END IF;
END;//
DELIMITER ;


/*Question4*/

DROP TRIGGER IF EXISTS RestrictedArea;
DELIMITER //
CREATE TRIGGER RestrictedArea
BEFORE INSERT ON Visit
FOR EACH ROW 
BEGIN 
	DECLARE V_PLN VARCHAR(10);
	DECLARE PUB_COUNTY VARCHAR(20);
	DECLARE PER_PPSN INT;
	DECLARE PER_COUNTY VARCHAR(20);
	DECLARE N_COUNTY1 VARCHAR(20);
	DECLARE N_COUNTY2 VARCHAR(20);
	DECLARE V_PPSN INT;
	DECLARE PUB_PLN VARCHAR(10);

	SELECT Person.PPSN, Visit.PPSN
	INTO PER_PPSN, V_PPSN
	FROM Person JOIN Visit
	WHERE Person.PPSN = Visit.PPSN;

	IF (PER_PPSN = V_PPSN) THEN
		SELECT Visit.PLN, Pub.PLN, Pub.PCounty, Person.PPSN, Person.PCounty
		INTO V_PLN, PUB_PLN, PUB_COUNTY, PER_PPSN, PER_COUNTY
		FROM Visit JOIN Pub JOIN Person
		WHERE Visit.PLN = Pub.PLN ;
		 
		IF (PER_COUNTY != PUB_COUNTY) THEN
			SELECT Pub.PCounty, NeighbouringCounty.County1, NeighbouringCounty.County2
			INTO PUB_COUNTY, N_COUNTY1, N_COUNTY2
			FROM NeighbouringCounty JOIN Pub ON Pub.PCounty = NeighbouringCounty.County1;
		
			IF (PUB_COUNTY != N_COUNTY1) OR (PUB_COUNTY != N_COUNTY2) THEN
				SIGNAL SQLSTATE '45000'
				SET MESSAGE_TEXT = 'Error can not visit a pub in a restriced area';
			END IF;
		END IF;	
	END IF;

END; //
DELIMITER ; 


/*Question5*/



/*Question2*/
INSERT INTO Pub
VALUES ('L1234', 'Murphys', 'Cork'),
		('L2345', 'Joes', 'Limerick'),
		('L3456', 'BatBar', 'Kerry');
	
		
INSERT INTO NeighbouringCounty
VALUES ('Cork', 'Limerick'),
		('Limerick', 'Cork'),
		('Cork', 'Kerry'),
		('Kerry', 'Cork');
	
INSERT INTO Person 
VALUES (1, 'Liza', 'Cork', 22, 5),
		(2, 'Alex', 'Limerick', 19, 7),
		(3, 'Tom', 'Kerry', 23, 10),
		(4, 'Peter', 'Cork', 39, 8);
		
INSERT INTO Visit
VALUES ('L1234', 1, '2020/02/10 10:00', '2020/02/10 11:00'),
		('L1234', 1, '2020/12/08 11:00', '2020/12/08 11:30'),
		('L2345', 3, '2020/12/03 11:00', '2020/12/03 11:50');
		
	
INSERT INTO Covid_Diagnosis 
VALUES (2, '2020/11/02', '2020/02/21');


/*Question6*/
DROP VIEW IF EXISTS COVID_NUMBERS;
CREATE VIEW COVID_NUMBERS (county, cases)
	AS SELECT (Person.PCounty), COUNT(Covid_Diagnosis.PPSN)
		FROM Covid_Diagnosis JOIN Person ON Covid_Diagnosis.PPSN = Person.PPSN
		GROUP BY Person.PCounty;
	

