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

Read about LEFT JOIN [1] and RIGHT JOIN [2] to get the better understanding.*

Solution #1: Using LEFT OUTER JOIN

> SELECT pages.page_id\
FROM pages\
LEFT OUTER JOIN page_likes AS likes\
  ON pages.page_id = likes.page_id\
WHERE likes.page_id IS NULL;\

Another solution to this problem, since pages with NO LIKES are needed, would be the NOT EXISTS clause (refer to Solution #2). It's an appropriate and efficient operator to get this information. Check out here.

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

  **My reflections: after looking at Data Lemur's sol'ns, I definitely overthought this one./ May be time to have a formal lesson on different join types and really understand how they function.**