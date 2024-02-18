---
title: Haven James Data Exploration
---




```sql users_added
SELECT 
  date_trunc('${inputs.agg}', created) AS month,
  COUNT(*) AS new_users,
  SUM(COUNT(*)) OVER (ORDER BY date_trunc('${inputs.agg}', created)) AS total_users,
  
FROM postgres.users
WHERE created > '2023-01-01'
and status in ('active', 'cancelled')
GROUP BY 1
order by 1 desc

```
<LineChart
    data={users_added}
    x=month
    y=total_users
    y2=new_users
    y2SeriesType=bar
    title = "New Users Added"
/>

```sql users_status
WITH user_status_counts AS (
  SELECT 
    date_trunc('${inputs.agg}', created) AS month,
    status,
    COUNT(*) AS new_users
  FROM postgres.users
  WHERE created > '2023-01-01'
    AND status IS NOT NULL 
    AND status != ''
  GROUP BY 1, 2
)

SELECT 
  month,
  status,
  new_users,
  SUM(new_users) OVER (PARTITION BY status ORDER BY month) AS total_active_users
FROM user_status_counts
ORDER BY 1, 2
```

<BarChart
    data={users_status}
    x=month
    y=new_users
    series = status
    type = stacked100
    title = "User Status"
/>

```sql churned_users
select 
  date_trunc('${inputs.agg}', cancelled) as month,
  count(*) as new_users
from postgres.users
where created > '2023-01-01'
and status = 'cancelled'
group by 1
```

<BarChart
    data={churned_users}
    y=new_users
    title = "Churned by Month"
/>

```sql active_users
with base as (
  select 
    date_trunc('${inputs.agg}', last_visited) as month,
    id
  from postgres.users
  where last_visited > '2023-01-01'
  group by 1,2
)
select 
    month
    , count(*) as users
from base
group by 1
```

<LineChart
    data={active_users}
    y=users
    title = "Active Users"
/>


