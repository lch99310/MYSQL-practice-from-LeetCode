# MYSQL-practices-from-leetcode
175. Combine Two Tables
~~~~sql
SELECT p.firstName, p.lastName, a.city, a.state
FROM Person p
LEFT JOIN Address a
ON p.personId = a.personId
~~~~

176. Second Highest Salary
~~~~sql
SELECT MAX(e.salary) AS "SecondHighestSalary"
FROM Employee e
WHERE e.salary < (SELECT MAX(salary) FROM Employee)
~~~~

177. Nth Highest Salary
~~~~sql
CREATE FUNCTION getNthHighestSalary(N INT) RETURNS INT
BEGIN
DECLARE M int;
SET M = N -1;
  RETURN (
      # Write your MySQL query statement below.
      SELECT DISTINCT e.salary AS "getNthHighestSalary(N)"
      FROM Employee e 
      ORDER BY e.salary DESC
      LIMIT 1 OFFSET M
  );
END
~~~~

178. Rank Scores
~~~~sql
SELECT s.score, DENSE_RANK() OVER(ORDER BY s.score DESC) AS "rank"
FROM Scores s
~~~~
* Note: 
* Rank generates ranking number based on total number. e.g.: 1,2,2,4,5.... 
* Dense_Rank generate consecutive ranking number. e.g.: 1,2,2,3,4.....

181. Employees Earning More Than Their Managers
~~~~sql
SELECT e.name AS "Employee"
FROM Employee e, Employee m
WHERE e.managerId = m.id AND e.salary > m.salary
~~~~

182. Duplicate Emails
~~~~sql
SELECT  p.email
FROM Person p
GROUP BY p.email
HAVING count(p.email) > 1
~~~~

183. Customers Who Never Order
~~~~sql
SELECT c.name AS "Customers"
FROM Customers c
LEFT JOIN Orders o
ON c.id = o.customerId
WHERE o.id IS NULL
~~~~
or
~~~~sql
SELECT c.name AS "Customers"
FROM Customers c
WHERE c.id NOT IN (
    SELECT o.customerId 
    FROM Orders o)
~~~~

184. Department Highest Salary
~~~~sql
SELECT d.name AS "Department", e.name AS "Employee", e.salary AS "Salary"
FROM Employee e LEFT JOIN Department d
ON e.departmentId = d.id
WHERE (e.departmentId, e.salary) IN (
    SELECT e.departmentId, MAX(e.salary)
    FROM Employee e, Department d
    WHERE e.departmentId = d.id
    GROUP BY e.departmentId)
~~~~

196. Delete Duplicate Emails
~~~~sql
DELETE p1.*
FROM Person p1, Person p2
WHERE p1.email = p2.email AND p1.id > p2.id;
~~~~

197. Rising Temperature
~~~~sql
SELECT w1.id
FROM Weather w1, Weather w2
WHERE DATEDIFF(w1.recordDate, w2.recordDate) = 1 AND w1.temperature > w2.temperature
~~~~

511. Game Play Analysis I
~~~~sql
SELECT a.player_id, MIN(a.event_date) AS "first_login"
FROM Activity a
GROUP BY a.player_id
~~~~

512. Game Play Analysis II
~~~~sql
SELECT a.player_id, a.device_id
FROM Activity a 
WHERE (a.player_id, a.event_date) IN (
    SELECT player_id, MIN(event_date)
    FROM Activity 
    GROUP BY player_id )
~~~~

Wrong answer↓
~~~~sql 
SELECT a.player_id, a.device_id
FROM Activity a 
GROUP BY a.player_id
HAVING MIN(a.event_date)
~~~~
* Because HAVING has to be followed by a condition, however in SELECT clause there is no condition.
* **Note: WHERE clause cannot follow by aggregation, HAVING clause can follow by aggregation. That's different!**

577. Employee Bonus
~~~~sql
SELECT e.name, b.bonus
FROM Employee e 
LEFT JOIN Bonus b
ON e.empId = b.empId
WHERE b.bonus < 1000 OR b.bonus IS null
~~~~
* Because it is possible that there is no bonus, use LEFT JOIN can fix this problem.
* The difference between IS and =, IS is an operator tests a value against a Boolean value. = is eqaul to.

584. Find Customer Referee
~~~~sql
SELECT c.name
FROM Customer c
WHERE c.referee_id != 2 OR c.referee_id IS NULL
~~~~
* Note: != is the same as <> in MySQL

586. Customer Placing the Largest Number of Orders
~~~~sql
SELECT o.customer_number
FROM Orders o
GROUP BY o.customer_number
ORDER BY COUNT(o.order_number) DESC
LIMIT 1
~~~~

595. Big Countries
~~~~sql
SELECT name, population, area
FROM World
WHERE area >= 3000000 OR population >= 25000000
~~~~

596. Classes More Than 5 Students
~~~~sql
SELECT c.class
FROM Courses c
GROUP BY c.class
HAVING COUNT(c.student) >= 5
~~~~

620. Not Boring Movies
~~~~sql
SELECT c.*
FROM Cinema c
WHERE (c.id % 2) = 1 AND c.description != "boring"
ORDER BY rating desc
~~~~

627. Swap Salary  * (need to review)
~~~~sql
UPDATE Salary s
SET s.sex = (CASE WHEN s.sex = "m" THEN "f" ELSE "m" END)
~~~~

1068. Product Sales Analysis I
~~~~sql
SELECT p.product_name, s.year, s.price
FROM Sales s, Product p
WHERE s.product_id = p.product_id AND s.sale_id IN (
    SELECT DISTINCT(sale_id)
    FROM Sales)
~~~~

1179. Reformat Department Table  * (need to review)
~~~~sql
SELECT d.id,
SUM(CASE WHEN d.month = "Jan" THEN d.revenue ELSE NULL END) AS "Jan_Revenue",
SUM(CASE WHEN d.month = "Feb" THEN d.revenue ELSE NULL END) AS "Feb_Revenue",
SUM(CASE WHEN d.month = "Mar" THEN d.revenue ELSE NULL END) AS "Mar_Revenue",
SUM(CASE WHEN d.month = "Apr" THEN d.revenue ELSE NULL END) AS "Apr_Revenue",
SUM(CASE WHEN d.month = "May" THEN d.revenue ELSE NULL END) AS "May_Revenue",
SUM(CASE WHEN d.month = "Jun" THEN d.revenue ELSE NULL END) AS "Jun_Revenue",
SUM(CASE WHEN d.month = "Jul" THEN d.revenue ELSE NULL END) AS "Jul_Revenue",
SUM(CASE WHEN d.month = "Aug" THEN d.revenue ELSE NULL END) AS "Aug_Revenue",
SUM(CASE WHEN d.month = "Sep" THEN d.revenue ELSE NULL END) AS "Sep_Revenue",
SUM(CASE WHEN d.month = "Oct" THEN d.revenue ELSE NULL END) AS "Oct_Revenue",
SUM(CASE WHEN d.month = "Nov" THEN d.revenue ELSE NULL END) AS "Nov_Revenue",
SUM(CASE WHEN d.month = "Dec" THEN d.revenue ELSE NULL END) AS "Dec_Revenue"
FROM Department d
GROUP BY d.id
ORDER by d.id
~~~~

1193. Monthly Transactions I
~~~~sql
SELECT DATE_FORMAT(t.trans_date, '%Y-%m') AS "month", t.country, COUNT(t.state) AS "trans_count", SUM(IF(t.state = "approved", 1, 0)) AS "approved_count", SUM(t.amount) AS "trans_total_amount", SUM(IF(t.state = "approved", t.amount, 0)) AS "approved_total_amount"
FROM Transactions t
GROUP BY DATE_FORMAT(t.trans_date, "%Y-%m"), t.country
~~~~
