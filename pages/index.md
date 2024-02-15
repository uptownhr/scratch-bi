---
title: Haven James Data Exploration
---

<LineChart
    data={users_by_week}
    y=new_users
    title = "Users by Month"
/>

<LineChart
    data={users_by_week_status}
    y=new_users
    title = "Users by Month Status"
    series = status
/>

<BarChart
    data={users_by_week_status}
    x=week
    y=new_users
    series = status
/>

<BarChart
    data={users_by_week_status}
    x=week
    y=new_users
    series = status
    type = stacked100
/>

<LineChart
    data={churned_by_week}
    y=new_users
    title = "Churned by Month"
/>

<DataTable data={churned} search=true>
    <Column id=id />
    <Column id=status />
    <Column id=billing_interval />
    <Column id=membership_plan />
    <Column id=created />
    <Column id=cancelled />
</DataTable>

<LineChart
    data={opportunities_created_by_week}
    y=new_opportunities
    title = "Opportunities Added by Week"
/>

<LineChart
    data={user_likes_by_week}
    y=new_likes
    title = "New Opportunity Likes by Week"
/>

<LineChart
    data={active_users_monthly}
    y=users
    title = "Monthly Active Users"
/>

<LineChart
    data={active_users_weekly}
    y=users
    title = "Weekly Active Users"
/>

<LineChart
    data={active_users_daily}
    y=users
    title = "Daily Active Users"
/>


<LineChart
    data={user_search_by_week}
    y=new_searches
    title = "Users Searches by Week"
/>

## Run SQL using Code Fences

```sql user_search_by_week
select 
  date_trunc('week', created) as week,
  count(*) as new_searches
from postgres.log
where action = 'search'
group by 1
```

```sql user_likes_by_week
select 
  date_trunc('week', created) as week,
  count(*) as new_likes
from postgres.likes
where created > '2023-08-01'
group by 1
```

```sql opportunities_created_by_week
select 
  date_trunc('week', created) as week,
  count(*) as new_opportunities
from postgres.opportunity
where created > '2023-01-01'
group by 1
```

```sql users_by_week
select 
  date_trunc('week', created) as week,
  count(*) as new_users
from postgres.users
where created > '2023-01-01'
group by 1
```

```sql users_by_week_status
select 
  date_trunc('month', created) as week
  , status
  , count(*) as new_users
from postgres.users
where created > '2023-01-01'
and status is not null 
and status != ''
group by 1, 2
```

```sql churned_by_week
select 
  date_trunc('week', cancelled) as week,
  count(*) as new_users
from postgres.users
where created > '2023-01-01'
and status = 'cancelled'
group by 1
```

```sql churned
select 
  id
  , email
  , status
  , billing_interval
  , membership_plan
  , cancelled
  , created
from postgres.users
where created > '2023-01-01'
and status = 'cancelled'
order by cancelled desc

```

```sql active_users_monthly
with base as (
  select 
    date_trunc('month', last_visited) as month,
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

```sql active_users_weekly
with base as (
  select 
    date_trunc('week', last_visited) as weekly,
    id
  from postgres.users
  where last_visited > '2023-06-01'
  group by 1,2
)
select 
    weekly
    , count(*) as users
from base
group by 1
```

```sql active_users_daily
with base as (
  select 
    date_trunc('day', last_visited) as daily,
    id
  from postgres.users
  where last_visited > '2023-10-01'
  group by 1,2
)
select 
    daily
    , count(*) as users
from base
group by 1
```