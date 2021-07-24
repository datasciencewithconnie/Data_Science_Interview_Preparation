
# Data Science Coding Question Answers 

:+1: The purpose of this repo is to quick refresh Sql and Python codes. The questions will be listed by difficulty and company. And the answers will be provided in both SQL and Python.

Here are the questions list:

### Easy:
* [12-Submission Types](#12-Submission-Types)
* [13-Apartments in New York City and Harlem](#13-Apartments-in-New-York-City-and-Harlem)
* [14-Find all wineries which produce wines](#14-Find-all-wineries-which-produce-wines)
* [15-Users Activity Per Month Day](#15-Users-Activity-Per-Month-Day)
* [16-](#16-) 
* [17-](#17-)
* [18-](#18-)
* [19-](#19-) 
* [20-](#20-)
* [21-](#21-)
* [22-](#22-)
* [23-](#23-)
* [24-](#24-)
* [25-](#25-)
* [26-](#26-)
* [27-](#27-)
* [28-](#28-)
* [29-](#29-)
* [30-](#30-)


### Google:
* [1-Activity Rank](#1-Activity-Rank)
* [2-Find Correlation Between E-mails And Activity Time](#2-Find-Correlation-Between-E-mails-And-Activity-Time)
* [3-Total AdWords Earnings](#3-Total-Adwords-Earnings)
* [4-User Email Labels](#4-User-Email-Labels)
* [5-Common Letters](#5-Common-Letters)
* [6-Common Friends Friend](#6-Common-Friends-Friend)
* [7-File Contents Shuffle](#7-File-Contents-Shuffle)
* [8-Price Of A Handyman](#8-Price-Of-A-Handyman)
* [9-Words With Two Vowels](#9-Words-With-Two-Vowels)

### Airbnb:

* [10-3 Bed Minimum](#10-3-Bed-Minimum)
* [11-Cheapest Properties](#11-Cheapest-Properties)


### 1-Activity Rank
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

### 2-Find Correlation Between E-mails And Activity Time
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

### 3-Total AdWords Earnings
Find the ***total AdWords earnings for each business type***. Output the business types along with the total earnings.

* **SQL Answer**
```
select business_type, sum(adwords_earnings)as total_earnings
from google_adwords_earnings
group by business_type
```
* **Python Answer** 
```
result=google_adwords_earnings.groupby(['business_type'])['adwords_earnings'].sum().reset_index()
```
[back to top](#Data-Science-Coding-Question-Answers)

### 4-User Email Labels
Find the number of emails received by each user under each built-in email label. The email labels are: 'Promotion', 'Social', and 'Shopping'. Output the user along with the number of promotion, social, and shopping mails count.
* **SQL Answer**
```
select e.to_user
,sum(case when l.label='Promotion' then 1 else 0 end) as Promo_cnt
,sum(case when l.label='Social' then 1 else 0 end) as Social_cnt
,sum(case when l.label='Shopping' then 1 else 0 end) as Shop_cnt
from google_gmail_emails e
left join google_gmail_labels l
on e.id=l.email_id
group by e.to_user
```
* **Python Answer** 
```
merged=pd.merge(google_gmail_emails,google_gmail_labels, left_on='id', right_on='email_id',how='left').fillna(0)
#find the labels in the following three categories
result= merged[merged['label'].isin(['Shopping', 'Social', 'Promotion'])] 
#calculate count by using size()
result = result.groupby(['to_user', 'label']).size().reset_index(name = 'cnt')
result.pivot_table(index='to_user', columns='label', values='cnt', aggfunc='sum').fillna(0).reset_index().sort_values(by='to_user')
```
[back to top](#Data-Science-Coding-Question-Answers)



### 5-Common Letters
Find the top 3 most common letters across all the words from both the tables. Output the letter along with the number of occurrences and order records in descending order based on the number of occurrences.

* **SQL Answer**
```
SELECT letter,
       COUNT(*) AS n_occurences
FROM
  (SELECT unnest(regexp_split_to_array(word, '')) AS letter #unnest to letter
   FROM
     (SELECT LOWER(unnest(string_to_array(CONTENTS, ' '))) AS word #unnest to words
      FROM google_file_store
      UNION ALL SELECT LOWER(unnest(string_to_array(words1, ','))) AS word #unnest to words
      FROM google_word_lists
      UNION ALL SELECT LOWER(unnest(string_to_array(words2, ','))) AS word #unnest to words
      FROM google_word_lists) all_words) all_letters
GROUP BY letter
ORDER BY n_occurences DESC
LIMIT 3
```
* **Python Answer** 
```
import pandas as pd
import numpy as np

df1 = google_file_store.contents.str.lower().str.split(expand=True).stack().tolist() #unnest to words
df2 = google_word_lists.words1.str.split(',',expand=True).stack().tolist() #unnest to words
df3 = google_word_lists.words2.str.split(',',expand=True).stack().tolist() #unnest to words
alist = df1+df2+df3
tr=' '.join(alist)
a = list(tr)
letters = pd.DataFrame(a,columns=['letter'])
letters['letter'].replace(' ', np.nan, inplace=True)
letters = letters.dropna()
result = letters.groupby('letter').size().to_frame('n_occurences').reset_index().sort_values('n_occurences', ascending = False).head(3)
```
[back to top](#Data-Science-Coding-Question-Answers)

### 6-Common Friends Friend
Find the number of a user's friends' friend who are also the user's friend. Output the user id along with the count.
* **SQL Answer**
```
select a.user_id, 
count(distinct c.user_id) as ff_cnt
from google_friends_network a
inner join google_friends_network b
on a.friend_id=b.user_id
inner join google_friends_network c
on b.friend_id=c.user_id and c.friend_id=a.user_id
group by a.user_id
```
* **Python Answer** 
```
merged = google_friends_network.merge(google_friends_network,left_on='friend_id', right_on = 'user_id', suffixes = ('_a','_b')).merge(google_friends_network,left_on=['friend_id_b','user_id_a'],right_on = ['user_id', 'friend_id'])
friends = merged.rename(columns = {'user_id':'friend_id_c'}).drop_duplicates(['user_id_a','friend_id_c'])
result= friends.groupby(['user_id_a'])['friend_id_c'].nunique().reset_index()
```
[back to top](#Data-Science-Coding-Question-Answers)

### 7-File Contents Shuffle
Sort the words alphabetically in 'final.txt' and make a new file named 'wacky.txt'. Output the file contents in one column and the filename 'wacky.txt' in another column.

* **SQL Answer**
```
SELECT 'wacky.txt' AS filename,
       array_to_string(array_agg(lower(word)), ' ') AS CONTENTS
FROM
  (SELECT *
   FROM
     (SELECT UNNEST (string_to_array(CONTENTS, ' ')) AS word
      FROM google_file_store
      WHERE filename ILIKE 'final%' ) un
   ORDER BY word) base
```
* **Python Answer** 
```
final = google_file_store[google_file_store['filename'].str.contains('final')]
words = final['contents'].str.split(expand=True).stack().tolist()
data = [['wacky.txt', sorted(words, key=lambda x: x.lower())]]
result = pd.DataFrame(data, columns = ['filename', 'contents'])
```
[back to top](#Data-Science-Coding-Question-Answers)

### 8-Price Of A Handyman
Find the price that a small handyman business is willing to pay per employee. Get the result based on the mode of the adword earnings per employee distribution. Small businesses are considered to have not more than one employee.

* **SQL Answer**
```
select mode() within group (order by earning_per_per) # mode() function finds the value appears most frequent in the specific column
from
(select adwords_earnings/n_employees as earning_per_per
from google_adwords_earnings
where business_type='handyman' and n_employees<=1) a
```
* **Python Answer** 
```
handyman = google_adwords_earnings[(google_adwords_earnings['business_type']=='handyman') & (google_adwords_earnings['n_employees']<=1)]
handyman.loc[:,('price_willing_to_pay_per_employee')] = handyman.loc[:,('adwords_earnings')] /handyman.loc[:,('n_employees')]
mode = handyman.mode().dropna()
result = mode[['price_willing_to_pay_per_employee']]
```
[back to top](#Data-Science-Coding-Question-Answers)

### 9-Words With Two Vowels
Find all words which contain exactly two vowels in any list in the table.

* **SQL Answer**
```
SELECT word
FROM
  (SELECT UNNEST (string_to_array(words1, ',')) AS word
   FROM google_word_lists
   UNION SELECT UNNEST (string_to_array(words2, ',')) AS word
   FROM google_word_lists) words
WHERE NOT word ~ '([aeiou].*){3}'
  AND word ~ '([aeiou].*){2}'
```
* **Python Answer** 
```
w1 = google_word_lists.assign(w1=google_word_lists['words1'].str.split(',')).explode('w1')
w1 = w1['w1'].tolist()
w2= google_word_lists.assign(w2=google_word_lists['words2'].str.split(',')).explode('w2')
w2 = w2['w2'].tolist()
words = w1 + w2
df = pd.DataFrame (words,columns=['word']).drop_duplicates(subset = 'word')
df['Vowels'] = df.word.str.lower().str.count(r'[aeiou]')
result = df[df['Vowels'] == 2][['word']]
```
[back to top](#Data-Science-Coding-Question-Answers)

### 10-3 Bed Minimum
Find the average number of beds in each neighborhood that has at least 3 beds in total. 
Output results along with the neighborhood name and sort the results based on the number of average beds in descending order.
* **SQL Answer**
```
SELECT 
    neighbourhood,
    avg(beds) AS n_bedrooms_avg
FROM airbnb_search_details
GROUP BY neighbourhood
having sum(beds)>=3
order by avg(beds) desc
```
* **Python Answer** 
```
result=airbnb_search_details.groupby(['neighbourhood']).filter(lambda g:sum(g['beds'])>=3).groupby(['neighbourhood']).mean().reset_index()[['neighbourhood','beds']].sort_values('beds',ascending=False)
```
[back to top](#Data-Science-Coding-Question-Answers)

### 11-Cheapest Properties
Find the price of the cheapest property for every city.
* **SQL Answer**
```
select city, min(price) 
from airbnb_search_details
group by city
```
* **Python Answer** 
```
result=airbnb_search_details.groupby(['city'])['price'].min().reset_index()
```
[back to top](#Data-Science-Coding-Question-Answers)

### 12-Submission Types
Write a query that returns the user ID of all users that have created at least one ‘Refinance’ submission and at least one ‘InSchool’ submission.
* **SQL Answer**
```
SELECT user_id
FROM loans
WHERE TYPE in ('Refinance', 'InSchool')
GROUP BY user_id
HAVING count(DISTINCT TYPE) =2
```
* **Python Answer** 
```
result=loans[loans['type'].isin(['Refinance','InSchool'])].groupby(['user_id'])['type'].nunique().to_frame('n_types').reset_index()
result=result[result['n_types']==2]['user_id'].unique()
```
[back to top](#Data-Science-Coding-Question-Answers)


### 13-Apartments in New York City and Harlem
Find the search details of 50 apartment searches the Harlem neighborhood of New York City.
* **SQL Answer**
```
select  * from airbnb_search_details
where city='NYC' and 
Property_type='Apartment' and
neighbourhood='Harlem'
limit 50
```
* **Python Answer** 
```
result=airbnb_search_details[(airbnb_search_details['city']=='NYC')
&(airbnb_search_details['property_type']=='Apartment') 
&(airbnb_search_details['neighbourhood']=='Harlem')].head(50)
```
[back to top](#Data-Science-Coding-Question-Answers)

### 14 Find all wineries which produce wines
Find all wineries which produce wines by possessing aromas of plum, cherry, rose, or hazelnut.
* **SQL Answer**
```
select distinct winery from winemag_p1
WHERE description like '%plum%' or
description like '%cherry%'  or
description like '%rose%'  or
description like '%hazelnut%' 
```
* **Python Answer** 
```
#'|' represents or
result=winemag_p1[winemag_p1['description'].str.contains('plum|cherry|rose|hazelnut')]['winery'].unique()
```
[back to top](#Data-Science-Coding-Question-Answers)

### 15 Users Activity Per Month Day
Return a distribution of users activity per day of the month

* **SQL Answer**
```
select 
extract( day from post_date) as day
,count(distinct post_id) as post_cnt
from facebook_posts
group by extract( day from post_date)
```
* **Python Answer** 
```
facebook_posts.groupby(pd.to_datetime(facebook_posts['post_date']).dt.day)['post_id'].count().to_frame('user_activity').reset_index()
```
[back to top](#Data-Science-Coding-Question-Answers)

### 16
* **SQL Answer**
```

```
* **Python Answer** 
```

```
[back to top](#Data-Science-Coding-Question-Answers)

### 17
* **SQL Answer**
```

```
* **Python Answer** 
```

```
[back to top](#Data-Science-Coding-Question-Answers)

### 18
* **SQL Answer**
```

```
* **Python Answer** 
```

```
[back to top](#Data-Science-Coding-Question-Answers)

### 19
* **SQL Answer**
```

```
* **Python Answer** 
```

```
[back to top](#Data-Science-Coding-Question-Answers)

### 20
* **SQL Answer**
```

```
* **Python Answer** 
```

```
[back to top](#Data-Science-Coding-Question-Answers)

### 21
* **SQL Answer**
```

```
* **Python Answer** 
```
```
[back to top](#Data-Science-Coding-Question-Answers)

### 22
* **SQL Answer**
```

```
* **Python Answer** 
```
```
[back to top](#Data-Science-Coding-Question-Answers)

### 23
* **SQL Answer**
```

```
* **Python Answer** 
```

```
[back to top](#Data-Science-Coding-Question-Answers)


### 24
* **SQL Answer**
```

```
* **Python Answer** 
```

```
[back to top](#Data-Science-Coding-Question-Answers)


### 25
* **SQL Answer**
```

```
* **Python Answer** 
```

```
[back to top](#Data-Science-Coding-Question-Answers)


### 26
* **SQL Answer**
```

```
* **Python Answer** 
```

```
[back to top](#Data-Science-Coding-Question-Answers)


### 27
* **SQL Answer**
```

```
* **Python Answer** 
```

```
[back to top](#Data-Science-Coding-Question-Answers)


### 28
* **SQL Answer**
```

```
* **Python Answer** 
```

```
[back to top](#Data-Science-Coding-Question-Answers)


### 29
* **SQL Answer**
```

```
* **Python Answer** 
```

```
[back to top](#Data-Science-Coding-Question-Answers)


### 30
* **SQL Answer**
```

```
* **Python Answer** 
```

```
[back to top](#Data-Science-Coding-Question-Answers)


