# SQL-Murder-Mystery
Solution to solving a murder mystery case using SQL

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

## Question and Solution

#### 1. Write a query that shows the interview transcripts for our two subjects.
First I needed to grab the interview transcript from the time of the crime.
Reminder: A **murder** that occurred in **SQL City on Jan. 15, 2018**.

```
SELECT *
FROM crime_scene_report
WHERE date = '20180115'
AND type = 'murder'
AND city = 'SQL City';
```
