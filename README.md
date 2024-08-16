# SQL-Murder-Mystery
Solution to solving a murder mystery case using SQL

![Screenshot 2024-08-16 120259](https://github.com/user-attachments/assets/61b1aad6-04dc-4388-a2b0-70958eb12c1b)

## Table of Contents
- [Project Objective](#project-objective)
- [Entity Relationship Diagram](#entity-relationship-diagram)
- [Question and Solution](#question-and-solution)

## Project Objective
A crime has taken place and the detective needs your help. The detective gave you the crime scene report, but you somehow lost it. You vaguely remember that the crime was a ​murder​ that occurred sometime on ​Jan.15, 2018​ and that it took place in ​SQL City​. Start by retrieving the corresponding crime scene report from the police department’s database.

More information about this game can be found [here](https://mystery.knightlab.com/walkthrough.html). Feel free to try it yourself! 

**Spoilers ahead as this is my solution to the case!**

## Entity Relationship Diagram
![Screenshot 2024-08-15 192316](https://github.com/user-attachments/assets/20e7715f-974e-44bd-ae6a-273f8840c875)

---

## Question and Solution

#### 1. Write a query that identifies the first witness.

- First I need to grab the interview transcript from the time of the crime.
- Reminder: A **murder** that occurred in **SQL City on Jan. 15, 2018**.

```sql ---
SELECT *
FROM crime_scene_report
WHERE date = '20180115'
AND type = 'murder'
AND city = 'SQL City';
```
| date | type | description | city |
|-----:|-----------|-----------|-----------|
| 20180115 | murder |Security footage shows that there were 2 witnesses. The first witness lives at the last house on "Northwestern Dr". The second witness, named Annabel, lives somewhere on "Franklin Ave".|SQL City|

```sql ---
SELECT *
FROM person
WHERE address_street_name = 'Northwestern Dr'
ORDER BY address_number DESC
LIMIT 1;
```

**Answer:**

| id | name | license_id | address_number | address_street_name | ssn |
|-----:|-----------|-----------|-----------|-----------|-----------|
| 14887 |	Morty Schapiro | 118009 | 4919 | Northwestern Dr | 111564949|

- The first witness was Morty Schapiro.

---

#### 2. Write a query that identifies the second witness.

```sql ---
SELECT *
FROM person
WHERE address_street_name = 'Franklin Ave'
AND name LIKE 'Annabel%';
```
**Answer:**
| id | name | license_id | address_number | address_street_name | ssn |
|-----:|-----------|-----------|-----------|-----------|-----------|
| 16371 |	Annabel Miller |	490173 |	103 |	Franklin Ave |	318771143 |

- The second witness was Annabel Miller.

---

#### 3. Write a query that shows the interview transcripts for our two subjects.

```sql ---
SELECT name, transcript
FROM person p 
JOIN interview i
ON p.id  = i.person_id
WHERE name = 'Morty Schapiro'
OR name = 'Annabel Miller';
```
**Answer:**
| name | transcript |
|-----:|-----------|
| Morty Schapiro | I heard a gunshot and then saw a man run out. He had a "Get Fit Now Gym" bag. The membership number on the bag started with "48Z". Only gold members have those bags. The man got into a car with a plate that included "H42W". |
| Annabel Miller |	I saw the murder happen, and I recognized the killer from my gym when I was working out last week on January the 9th. |

---

#### 4. Find the murderer!

- It took me two queries to narrrow my search as I linked multiple tables together. First, I linked the gfit membership table, gfit check in table, and the person table.
- Reminder: Membership ID starting with **"48Z"** and **Gold member** status on **Jan 9th, 2018**

```sql ---
SELECT p.name
FROM person p
JOIN get_fit_now_member gfit
ON p.id = gfit.person_id
JOIN get_fit_now_check_in gcheck
ON gfit.id = gcheck.membership_id
WHERE membership_status = 'gold'
AND check_in_date = '20180109'
AND membership_id LIKE '48z%';
```
| name |
|-----:|
| Joe Germuska |
| Jeremy Bowers |

- This narrowed my search to 2 people.
- Next I joined the drivers license table as the witness had a portion of the license plate **"H42W"**

```sql ---
SELECT p.name 
FROM person p  
JOIN get_fit_now_member gfit  
ON p.id = gfit.person_id  
JOIN get_fit_now_check_in gcheck  
ON gfit.id = gcheck.membership_id  
JOIN drivers_license d 
ON p.license_id = d.id 
WHERE membership_status = 'gold' 
AND check_in_date = '20180109' 
AND membership_id LIKE '48Z%' 
AND plate_number LIKE '%H42W%';
```
**Answer:**
| name |
|-----:|
| Jeremy Bowers |

- Jeremy Bowers was the murderer!
---
#### 5. Bonus Task
- Try querying the interview transcript of the murderer to find the real villian behind this crime.

```sql ---
SELECT name, transcript
FROM person p 
JOIN interview i
ON p.id  = i.person_id
WHERE name = 'Jeremy Bowers';
```
**Answer:**
| name | transcript |
|-----:|-----------|
| Jeremy Bowers |	I was hired by a woman with a lot of money. I don't know her name but I know she's around 5'5" (65") or 5'7" (67"). She has red hair and she drives a Tesla Model S. I know that she attended the SQL Symphony Concert 3 times in December 2017. |
- With the information provided in the interview transcript, I know I have to join the driver's license table, person table, and facebook event table.

```sql ---
SELECT p.name, d.hair_color AS hair, d.height
FROM person p
JOIN drivers_license d
ON p.license_id = d.id
WHERE hair_color = 'red'
AND height BETWEEN '65' AND '67'
AND car_make = 'Tesla';
AND car_model = 'Model S';
```
| name | hair | height |
|-----:|-----------|-----------|
| Red Korb |	red |	65 |
| Regina George |	red |	66 |
| Miranda Priestly |	red |	66 |

- Now that I have 3 suspects, I can narrow my search with the facebook event, "SQL Symphony Concert."
  
```sql ---
SELECT DISTINCT p.name, d.hair_color AS hair, d.height
FROM person p
JOIN drivers_license d
ON p.license_id = d.id
JOIN facebook_event_checkin f
ON p.id = f.person_id
WHERE hair_color = 'red'
AND height BETWEEN '65' AND '67'
AND car_make = 'Tesla'
AND car_model = 'Model S'
AND event_name ='SQL Symphony Concert';
```
**Answer:**
| name | hair | height |
|-----:|-----------|-----------|
| Miranda Priestly |	red |	66 |
- The mastermind was Miranda Priestly!

---
**Congrats, you found the brains behind the murder! Everyone in SQL City hails you as the greatest SQL detective of all time. Time to break out the champagne!**
