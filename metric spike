USE job;

CREATE TABLE users(
user_id INT NOT NULL,
created_at DATETIME,
company_id INT NOT NULL,
language VARCHAR(15) NOT NULL,
activated_at DATETIME DEFAULT NULL,
state VARCHAR(10) NOT NULL);

select * from users;

CREATE TABLE events(
user_id INT NOT NULL,
occurred_at DATETIME,
event_type VARCHAR(20) ,
event_name VARCHAR(30) ,
location VARCHAR(30) ,
device VARCHAR(30) ,
user_type INT DEFAULT NULL );

select * from events;

CREATE TABLE email_events(
user_id INT NOT NULL,
occurred_at DATETIME,
action VARCHAR(30) ,
user_type INT);

select * from email_events;

/*A.User engagement*/
SELECT EXTRACT( week from e.occurred_at) AS weeks,
COUNT(DISTINCT e.user_id) AS weekly_active_users
FROM events e
WHERE e.event_type = 'engagement'
AND e.event_name = 'login'
GROUP BY 1
ORDER BY 1 ;

/*B.User growth for product*/
SELECT EXTRACT( week from u.created_at) AS day, COUNT(*) AS all_users, 
COUNT(CASE WHEN u.activated_at IS NOT NULL THEN u.user_id ELSE NULL END) AS activated_users 
FROM users u 
WHERE u.created_at >= '2013-01-01' AND u.created_at < '2014-09-01' 
GROUP BY 1 
ORDER BY 1;

/*C.Weekly retention*/
SELECT EXTRACT(week FROM z.occurred_at) AS "week", AVG(z.age_at_event) AS "Average age during week", 
COUNT(DISTINCT CASE WHEN z.user_age > 70 THEN z.user_id ELSE NULL END) AS "10+ weeks", 
COUNT(DISTINCT CASE WHEN z.user_age < 70 AND z.user_age >=63 THEN z.user_id ELSE NULL END) AS "9 weeks", 
COUNT(DISTINCT CASE WHEN z.user_age < 63 AND z.user_age >=56 THEN z.user_id ELSE NULL END) AS "8 weeks", 
COUNT(DISTINCT CASE WHEN z.user_age < 56 AND z.user_age >=49 THEN z.user_id ELSE NULL END) AS "7 weeks",  
COUNT(DISTINCT CASE WHEN z.user_age < 49 AND z.user_age >=42 THEN z.user_id ELSE NULL END) AS "6 weeks",  
COUNT(DISTINCT CASE WHEN z.user_age < 42 AND z.user_age >=35 THEN z.user_id ELSE NULL END) AS "5 weeks",  
COUNT(DISTINCT CASE WHEN z.user_age < 35 AND z.user_age >=28 THEN z.user_id ELSE NULL END) AS "4 weeks",  
COUNT(DISTINCT CASE WHEN z.user_age < 28 AND z.user_age >=21 THEN z.user_id ELSE NULL END) AS "3 weeks",  
COUNT(DISTINCT CASE WHEN z.user_age < 21 AND z.user_age >=14 THEN z.user_id ELSE NULL END) AS "2 weeks", 
COUNT(DISTINCT CASE WHEN z.user_age < 14 AND z.user_age >=7 THEN z.user_id ELSE NULL END) AS "1 weeks",  
COUNT(DISTINCT CASE WHEN z.user_age < 7 AND z.user_age >=0 THEN z.user_id ELSE NULL END) AS "Less than a week"
FROM (SELECT e.occurred_at, u.user_id, EXTRACT(week from u.activated_at) AS activation_week, 
EXTRACT(DAY FROM e.occurred_at - u.activated_at) AS age_at_event, 
DATEDIFF('2014-09-01',u.activated_at) AS user_age
FROM users u JOIN events e 
ON e.user_id = u.user_id AND e.event_type = 'engagement' 
AND e.event_name= 'login' AND e.occurred_at >= '2014-05-01' AND e.occurred_at < '2014-09-01' 
WHERE u.activated_at IS NOT NULL ) z 
GROUP BY 1 
ORDER BY 1  
LIMIT 100;

/*D.Weekly engagement*/
SELECT EXTRACT(week FROM occurred_at) AS week, 
COUNT(DISTINCT e.user_id) AS weekly_active_users, 
COUNT(DISTINCT CASE WHEN e.device 
IN('macbook pro','lenovo thinkpad','macbook air','dell inspiron notebook','asus chromebook','dell inspiron desktop','acer aspire notebook','hp pavilion desktop','acer aspire desktop','mac mini') 
THEN e.user_id ELSE NULL END) AS computer, 
COUNT(DISTINCT CASE WHEN e.device 
IN('iphone 5','samsung galaxy s4','nexus 5','iphone 5s','iphone 4s','nokia lumia 635','htc one','samsung galaxy note','amazon fire phone') 
THEN e.user_id ELSE NULL END) AS phone, 
COUNT(DISTINCT CASE WHEN e.device 
IN('ipad air','nexus 7','ipad mini','nexus 10','kindle fire','windows surface','samsung galaxy tablet') 
THEN e.user_id ELSE NULL END) AS  tablet 
FROM events e 
WHERE e.event_type = 'engagement' AND e.event_name = 'login' 
GROUP BY 1  
ORDER BY 1 
LIMIT 100;

/*E.Email engagement*/
SELECT EXTRACT(week FROM occurred_at) AS week, 
COUNT(CASE WHEN e.action = 'sent_weekly_digest' THEN e.user_id ELSE NULL END) AS weekly_emails, 
COUNT(CASE WHEN e.action = 'sent_reengagement_email' THEN e.user_id ELSE NULL END) AS reengagement_emails, 
COUNT(CASE WHEN e.action = 'email_open' THEN e.user_id ELSE NULL END) AS email_opens, 
COUNT(CASE WHEN e.action = 'email_clickthrough' THEN e.user_id  ELSE NULL END) AS email_clickthroughs  
FROM email_events e 
GROUP BY 1;
