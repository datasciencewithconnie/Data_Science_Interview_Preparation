
# Data Science Coding Question Answer :+1:

The purpose of this repo is to quick refresh Sql and Python codes. The questions will be listed by Company and the answers will be provided in both SQL and Python.

# Google

* 1.Question - Activity Rank
* Output the user, total emails, and their activity rank(based on their total number of email they sent).

* **SQL**
```
 select 
 from_user as sender,
 count(*) as total_email_sent,
 row_number()over(order by count(*) desc ) as rnk
 from google_gmail_emails
 group by from_user
 ```

* **Python**
 ```
 import pandas as pd
 import numpy as np

 df=google_gmail_emails
 result=df.groupby(['from_user']).size().to_frame('total_emails').reset_index()
 result['rank']=result['total_emails'].rank(method='first',ascending=False)
```
