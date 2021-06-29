
# Data Science Coding Question Answers 

:+1: The purpose of this repo is to quick refresh Sql and Python codes. The questions will be listed by Company and the answers will be provided in both SQL and Python.

Here are the questions list:

### Google:
* [1.Activity Rank](#Activity-Rank)
* [2.Find Correlation Between E-mails And Activity Time](#Find-Correlation-Between-E-mails-And-Activity-Time)

### Airbnb:
* 


### Activity Rank
* Output the ***user, total emails, and their activity rank*** (based on their total number of email they sent).

* **SQL Answer**
```
 select 
 from_user as sender,
 count(*) as total_email_sent,
 row_number()over(order by count(*) desc ) as rnk
 from google_gmail_emails
 group by from_user
 ```

* **Python Answer**
 ```
 import pandas as pd
 import numpy as np

 df=google_gmail_emails
 result=df.groupby(['from_user']).size().to_frame('total_emails').reset_index()
 result['rank']=result['total_emails'].rank(method='first',ascending=False)
```

[back to top](#Data-Science-Coding-Question-Answers)

### Find Correlation Between E-mails And Activity Time
* Find the correlation between the ***number of emails received*** and the ***total exercise per day***. The total exercise per day is calculated by counting the number of user sessions per day.
* **SQL Answer**
```
with email as (
select email_recieved, exercise_per_day from
(select count(distinct g.id) as email_recieved, g.to_user, g.day
from google_gmail_emails g
group by g.day,g.to_user) a
inner join
(select f.day,count(distinct session_id) as exercise_per_day, f.user_id
from google_fit_location f
group by f.day,f.user_id) b
on a.day=b.day and a.to_user=b.user_id)

select corr(email_recieved, exercise_per_day)
from email

```
 
 * **Python Answer**
```
#find recieved emails by day
mail_base=google_gmail_emails.groupby(['day','to_user'])['id'].nunique().to_frame('n_emails').reset_index()

#find distinct sessions per user per day
location=google_fit_location.groupby(['day','user_id'])['session_id'].nunique().to_frame('total_exercise').reset_index()

# merge the two table using day and user_id
merged=pd.merge(mail_base,location,left_on=['day','to_user'],right_on=['day','user_id'])

# corr() function to calculate correlation
result=merged['n_emails'].corr(merged['total_exercise'])
```
[back to top](#Data-Science-Coding-Question-Answers)

### 4. 
* **SQL Answer**
```

```
* **Python Answer** 
```

```
[back to top](#Data-Science-Coding-Question-Answers)
