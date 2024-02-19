---
title: Haven James Data Exploration
---

<Dropdown title="Aggregate" name=agg>
    <DropdownOption valueLabel="Monthly" value="month" />
    <DropdownOption valueLabel="Weekly" value="week" />
    <DropdownOption valueLabel="Daily" value="day" />
</Dropdown>

<Dropdown title="Date Range" name=days_ago>
    <DropdownOption valueLabel="Last Year" value="365" />
    <DropdownOption valueLabel="Last 6 Month" value="182" />
    <DropdownOption valueLabel="Last Quarter" value="91" />
    <DropdownOption valueLabel="Last Month" value="30" />
    <DropdownOption valueLabel="Last Week" value="7" />
    <DropdownOption valueLabel="All" value="9999" />
</Dropdown>

```sql users_added
SELECT 
  date_trunc('${inputs.agg}', created) AS month,
  COUNT(*) AS new_users,
  SUM(COUNT(*)) OVER (ORDER BY date_trunc('${inputs.agg}', created)) AS total_users,
  
FROM postgres.users
WHERE 1=1 
and created > current_date - interval '${inputs.days_ago} days'
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
    title = "New Users Added by {inputs.agg}"
/>

```sql users_status
WITH user_status_counts AS (
  SELECT 
    date_trunc('${inputs.agg}', created) AS month,
    status,
    COUNT(*) AS new_users
  FROM postgres.users
  WHERE 1=1
    and created > current_date - interval '${inputs.days_ago} days'
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
    title = "User Status by {inputs.agg}"
/>

```sql churned_users
select 
  date_trunc('${inputs.agg}', cancelled) as month,
  count(*) as new_users
from postgres.users
where 1=1
and created > current_date - interval '${inputs.days_ago} days'
and status = 'cancelled'
group by 1
```

<BarChart
    data={churned_users}
    y=new_users
    title = "Churned by {inputs.agg}"
/>

```sql active_users
with base as (
  select 
    date_trunc('day', created) as day
    , user_id
  from postgres.log
  where 1=1
  and created > current_date - interval '${inputs.days_ago} days'
  group by 1, 2
)

select
  date_trunc('${inputs.agg}', day) as time
  , count(*) as users 
from base
group by 1
order by 1 desc
```

<BarChart
    data={active_users}
    y=users
    title = "Active Users by {inputs.agg} - last log (click, search)"
/>

```sql user_activity
with base as (
  select 
    date_trunc('day', created) as day
    , action
    , user_id
  from postgres.log
  where 1=1
  and created > current_date - interval '${inputs.days_ago} days'
  group by 1, 2, 3
)

select
  date_trunc('${inputs.agg}', day) as time
  , action
  , count(*) as users 
from base
group by 1, 2
order by 1 desc
```

<BarChart
    data={user_activity}
    y=users
    title = "User Activity by {inputs.agg}"
    series = action
/>