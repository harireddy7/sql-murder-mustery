# SQL Murder Mystery 🕵

The SQL Murder Mystery is an engaging and interactive problem designed to test and improve your SQL querying skills. In this problem, you will play the role of a detective trying to solve a murder case by exploring and querying a database filled with information about the crime, suspects, and evidence. The primary objective is to find out who committed the crime, using your SQL skills to sift through the data.

## Skills Tested ✅

- **Data Exploration**: Navigate the database to understand its structure and the relationships between various tables.
- **SQL Querying**: Use various SQL commands to extract, filter, and analyze data.
- **Critical Thinking**: Apply logical reasoning to piece together clues and solve the mystery.

## Investigation 🔍

### Find the Murderer 🦹

#### 1. We will check the `crime_scene_report` on the murder committed date

```SQL
SELECT * FROM crime_scene_report
WHERE type = 'murder' and date = 20180115 and Lower(city) = 'sql city';
```
| Date       | Type   | Description                                                                                                                                | City     |
|------------|--------|--------------------------------------------------------------------------------------------------------------------------------------------|----------|
| 2018-01-15 | Murder | Security footage shows that there were 2 witnesses. The first witness lives at the last house on "Northwestern Dr". The second witness, named Annabel, lives somewhere on "Franklin Ave". | SQL City |

We got the details of witnesses for the murder. Let's find out their interview reports

#### 2. Find the interview reports of both witnesses

```SQL
SELECT * FROM interview
WHERE person_id = (
  SELECT id from person
  WHERE address_street_name = 'Northwestern Dr'
  ORDER BY address_number DESC limit 1
) OR person_id = (
	SELECT id from person
	WHERE Lower(name) LIKE '%Annabel%'AND
  	address_street_name = 'Franklin Ave'
);
```

| Person ID | Transcript                                                                                                                                                           |
|-----------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 14887     | I heard a gunshot and then saw a man run out. He had a "Get Fit Now Gym" bag. The membership number on the bag started with "48Z". Only gold members have those bags. The man got into a car with a plate that included "H42W". |
| 16371     | I saw the murder happen, and I recognized the killer from my gym when I was working out last week on January 9th.                                                    |

Now that we have the witness reports, we can go ahead and find the murderer

#### 3. Find the murderer

```SQL
SELECT p.name
FROM get_fit_now_check_in c
JOIN get_fit_now_member m
	ON c.membership_id = m.id
JOIN person p
	ON m.person_id = p.id
JOIN drivers_license dl
	ON p.license_id = dl.id
WHERE
	m.membership_status = 'gold'AND
	m.id LIKE '48Z%' AND
	c.check_in_date = '20180109' AND
	dl.plate_number LIKE '%H42W%'
LIMIT 1
;
```
| name |
|------------|
| Jeremy Bowers |

🎉 There we go, we got the murderer!! You will see this message upon submitting the murderer's name.

| value |
|------------|
| Congrats, you found the murderer! But wait, there's more... If you think you're up for a challenge, try querying the interview transcript of the murderer to find the real villain behind this crime. If you feel especially confident in your SQL skills, try to complete this final step with no more than 2 queries. Use this same INSERT statement with your new suspect to check your answer. |

### Find the person behind the murder 🧠

Now that we found the murderer, let's find the person who paid for the murder. Let's see his interview report for that.

```SQL
SELECT * from interview
WHERE person_id = (
  SELECT p.id
  FROM get_fit_now_check_in c
  JOIN get_fit_now_member m
  ON c.membership_id = m.id
  JOIN person p ON m.person_id = p.id
  JOIN drivers_license dl ON p.license_id = dl.id
  WHERE
	  m.membership_status = 'gold'AND
	  m.id LIKE '48Z%' AND
	  c.check_in_date = '20180109' AND
	  dl.plate_number LIKE '%H42W%'
  LIMIT 1
);
```

| person_id	| transcript
|------------|------------|
| 67318 | I was hired by a woman with a lot of money. I don't know her name but I know she's around 5'5" (65") or 5'7" (67"). She has red hair and she drives a Tesla Model S. I know that she attended the SQL Symphony Concert 3 times in December 2017. |

Let's find out the main villain behind the murder from this report.


```SQL
SELECT DISTINCT p.name
FROM person p
JOIN drivers_license dl
ON p.license_id = dl.id
JOIN facebook_event_checkin f
ON f.person_id = p.id
WHERE
	dl.hair_color = 'red' AND
	dl.gender = 'female' AND
	LOWER(dl.car_make) = 'tesla' AND
	LOWER(dl.car_model) = 'model s' AND
	dl.height BETWEEN 65 AND 67 AND
	LOWER(f.event_name) = 'sql symphony concert';
```
| name|
|------------|
| Miranda Priestly|

Submit this name to the solution. We get -

| value|
|------------|
| Congrats, you found the brains behind the murder! Everyone in SQL City hails you as the greatest SQL detective of all time. Time to break out the champagne!|

**🎉 Congratulations! 🎉**
You've successfully solved the SQL Murder Mystery and identified the murderer!

---
# Police Report 🕵

### SQL City Police Department Investigation Report
#### Case: SQL Murder Mystery
#### Date: January 15, 2018
#### Location: SQL City

---

### Summary of Investigation

**Crime Scene Report:**  
On January 15, 2018, a murder was reported in SQL City. The crime scene footage revealed two witnesses. The first witness lived at the last house on Northwestern Dr, and the second witness, Annabel, resided on Franklin Ave.

**Witness Interviews:**  
1. **Witness 1 (Person ID: 14887):**  
   - Reported hearing a gunshot and seeing a man flee the scene carrying a "Get Fit Now Gym" bag. The gym membership number on the bag began with "48Z," indicating it belonged to a gold member. The man entered a car with a license plate containing "H42W".

2. **Witness 2 (Person ID: 16371):**  
   - Saw the murder and recognized the killer from the gym on January 9th.

**Identification of the Murderer:**  
Based on the witness reports, the investigation focused on gym members with gold status and the specified membership number. Surveillance records from the gym on January 9th and vehicle registration details led to the identification of **Jeremy Bowers** as the suspect.

**Confession and Additional Information:**  
Upon questioning, Jeremy Bowers confessed to the murder but revealed he was hired by an unknown woman with distinctive features: around 5'5" to 5'7" tall, red hair, and driving a Tesla Model S. She was also noted to have attended the SQL Symphony Concert three times in December 2017.

**Identification of the Mastermind:**  
Further investigation into attendees of the SQL Symphony Concert who matched the description led to the identification of **Miranda Priestly**. She was identified through cross-referencing event check-ins and vehicle registration records.

---

### Conclusion:
The investigation successfully identified **Jeremy Bowers** as the murderer and **Miranda Priestly** as the mastermind behind the crime. Both individuals have been apprehended and charged accordingly. SQL City hails the investigative team for their exceptional work in solving this complex case.

---

### Sign-Off  
**Detective:** Hari Kotha  
**Date:** June 08, 2024  
**SQL City Police Department**

---


