## Easy SQL Questions on Data Lemur

**Data Science Skills [LinkedIn SQL Interview Question]**

    Given a table of candidates and their skills, you're tasked with finding the candidates best suited for an open Data Science job. You want to find candidates who are proficient in Python, Tableau, and PostgreSQL.

    Write a query to list the candidates who possess all of the required skills for the job. Sort the output by candidate ID in ascending order.

    Assumption:
    There are no duplicates in the candidates table.

`candidates` Table:
| Column Name	| Type |
| ----------- | ----------- |
| candidate_id	| integer |
| skill	| varchar |

*Solution:*

> SELECT candidate_id\
FROM candidates\
WHERE skill IN ('Python', 'Tableau', 'PostgreSQL')\
GROUP BY candidate_id\
HAVING COUNT(skill) = 3\
ORDER BY candidate_id;\

---

**Page With No Likes [Facebook SQL Interview Question]**

    Assume you're given the tables below about Facebook Page and Page likes (as in "Like a Facebook Page").

    Write a query to return the IDs of the Facebook pages which do not possess any likes. The output should be sorted in ascending order.Assume you're given the tables below about Facebook Page and Page likes (as in "Like a Facebook Page").

    Write a query to return the IDs of the Facebook pages which do not possess any likes. The output should be sorted in ascending order.

`pages` Table:
| Column Name | Type |
| ----------- | ----------- |
| page_id	| integer |
| page_name	| varchar |

`page_likes` Table:
| Column Name	| Type |
| ----------- | ----------- |
| user_id	| integer |
| page_id	| integer |
| liked_date	| datetime |

*My Solution:*
> SELECT pages.page_id\
FROM pages\
FULL JOIN (SELECT COUNT(user_id) as like_count, page_id\
      FROM page_likes\
      GROUP BY page_id) likes on likes.page_id = pages.page_id\
WHERE like_count IS NULL;\

*Solutions*

*There are two ways to go about it. Either LEFT JOIN or RIGHT JOIN can be established between tables pages and page_likes or a subquery can be used to identify which pages have not been liked by any user.

The LEFT JOIN clause starts selecting data from the left table. For each row in the left table (pages), it compares the value in the page_id column with the value of each row in the page_id column in the right table (page_likes).

When page_id are found on both sides, the LEFT JOIN clause creates a new row that contains columns that appear in the SELECT clause and adds this row to the result set.

In case page_id frompages table is not available in page_likes table, the LEFT JOIN clause also creates a new row that contains columns that appear in the SELECT clause. In addition, it fills the columns that come from the page_likes (right table) with NULL. Rows having NULL values in the result is the set of the solution.

Read about LEFT JOIN [1]("https://www.postgresqltutorial.com/postgresql-tutorial/postgresql-left-join/") and RIGHT JOIN [2]("https://www.postgresqltutorial.com/postgresql-tutorial/postgresql-right-join/") to get the better understanding.*

Solution #1: Using LEFT OUTER JOIN

> SELECT pages.page_id\
FROM pages\
LEFT OUTER JOIN page_likes AS likes\
  ON pages.page_id = likes.page_id\
WHERE likes.page_id IS NULL;\

Another solution to this problem, since pages with NO LIKES are needed, would be the NOT EXISTS clause (refer to Solution #2). It's an appropriate and efficient operator to get this information. Check out [here]("https://www.postgresqltutorial.com/postgresql-tutorial/postgresql-exists/#:~:text=The%20NOT%20EXISTS%20is%20opposite,the%20NOT%20EXISTS%20returns%20false.").

Both methods give the same output.

Solution #2: Using EXCEPT

> SELECT page_id\
FROM pages\
EXCEPT\
SELECT page_id\
FROM page_likes;\

Solution #3: Using NOT IN

> SELECT page_id
FROM pages\
WHERE page_id NOT IN (\
  SELECT DISTINCT page_id\
  FROM page_likes\
);

Solution #4: Using NOT EXISTS

> SELECT page_id
FROM pages\
WHERE NOT EXISTS (\
  SELECT page_id\
  FROM page_likes AS likes\
  WHERE likes.page_id = pages.page_id\
);

\
\
  **My reflections: after looking at Data Lemur's sol'ns, I definitely overthought this one.**\
  **May be time to have a formal lesson on different join types and really understand how they function.**

---

**Unfinished Parts [Tesla SQL Interview Question]**

    Tesla is investigating production bottlenecks and they need your help to extract the relevant data. Write a query that determines which parts with the assembly steps have initiated the assembly process but remain unfinished.

    Assumptions:

        parts_assembly table contains all parts currently in production, each at varying stages of the assembly process.
        An unfinished part is one that lacks a finish_date.

    This question is straightforward, so let's approach it with simplicity in both thinking and solution.

    Effective April 11th 2023, the problem statement and assumptions were updated to enhance clarity.

`parts_assembly` Table
|	Column Name |	Type |
| ----------- | ----------- |
|	part	|	string |
|	finish_date	|	datetime |
|	assembly_step	|	integer |

*Solution:*

Great news! The parts table already includes all parts currently in production, so no additional filtering is necessary to exclude non-production parts.

To extract unfinished parts, we can simply filter for rows where the finish_date column contains no data, indicated by a NULL value.

SELECT part, assembly_step
FROM parts_assembly
WHERE finish_date IS NULL;

---

**Histogram of Tweets [Twitter SQL Interview Question]**

This is the same question as problem #6 in the SQL Chapter of [Ace the Data Science Interview]("https://amzn.to/3kF79Fx")!

Assume you're given a table Twitter tweet data, write a query to obtain a histogram of tweets posted per user in 2022. Output the tweet count per user as the bucket and the number of Twitter users who fall into that bucket.

In other words, group the users by the number of tweets they posted in 2022 and count the number of users in each group.

`tweets` Table:
|	Column Name	|	Type |
| ----------- | ----------- |
|	tweet_id	|	integer |
|	user_id	|	integer |
|	msg	|	string |
|	tweet_date	|	timestamp |

*My Solution:*
> SELECT tweets_cte.tweets_num tweet_bucket,\
  COUNT(tweets_cte.user_id) as users_num\
FROM (SELECT user_id,\
        COUNT(tweet_id) AS tweets_num\
      FROM tweets\
      WHERE tweet_date BETWEEN '2022-01-01' AND '2022-12-31'\ 
      GROUP BY user_id) tweets_cte\
GROUP BY tweets_cte.tweets_num;\

*Solution*

First, we need to find the number of tweets posted by each user in 2022 by grouping the tweet records by user ID and counting the tweets.

> SELECT 
  user_id,\
  COUNT(tweet_id) AS tweet_count_per_user\
FROM tweets\ 
WHERE tweet_date BETWEEN '2022-01-01'\ 
  AND '2022-12-31'\
GROUP BY user_id;\

The output shows the number of tweets posted by each user in 2022:
|	user_id	|	tweet_count_per_user |
| ----------- | ----------- |
|	111 |	2 |
|	148	|	1 |
|	254	|	1 |

Based on the output, we can infer that in the year 2022, user 111 has posted two tweets, while users 148 and 254 have only posted one tweet each.

Next, we use the query above as a subquery, then we use the tweet_count_per_user field as the tweet bucket and retrieve the number of users.

> SELECT\ 
  tweet_count_per_user AS tweet_bucket,\ 
  COUNT(user_id) AS users_num\ 
FROM (\
  SELECT\ 
    user_id,\ 
    COUNT(tweet_id) AS tweet_count_per_user\ 
  FROM tweets\ 
  WHERE tweet_date BETWEEN '2022-01-01'\ 
    AND '2022-12-31'\
  GROUP BY user_id) AS total_tweets\ 
GROUP BY tweet_count_per_user;\

This query generates a histogram of the number of tweets per user in 2022. The output shows the tweet count per user as the tweet bucket and the number of Twitter users who fall into that bucket.
| tweet_bucket	| users_num |
| ----------- | ----------- |
| 1	| 2 |
| 2	| 1 |

Alternatively, we can use a Common Table Expression (CTE) instead of a subquery to compute the tweet counts.

A CTE is a data set that is created temporarily and can be used within a query. It is available for use during the entire session of the query execution. On the other hand, a subquery is a query nested within another query and can only be used within that query. A subquery typically acts as a column with a single value in the FROM or WHERE clause.

The benefits of using a CTE are that it is more readable and can be reused throughout the query session, whereas a subquery can only be used within the query in which it is defined.

Solution #2: Using CTE

> WITH total_tweets AS (\
  SELECT\ 
    user_id,\ 
    COUNT(tweet_id) AS tweet_count_per_user\
  FROM tweets\ 
  WHERE tweet_date BETWEEN '2022-01-01'\ 
    AND '2022-12-31'\ 
  GROUP BY user_id)\ 
  
> SELECT\ 
  tweet_count_per_user AS tweet_bucket\ 
  COUNT(user_id) AS users_num\ 
FROM total_tweets\ 
GROUP BY tweet_count_per_user;\

  **My reflections: had a surprisingly hard time with this one.**\
  **I was on the right track with using the CTE and/or subquery but got tripped up.**