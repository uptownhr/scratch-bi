---
title: Search
---

<Dropdown name=agg>
    <DropdownOption valueLabel="Monthly" value="month" />
    <DropdownOption valueLabel="Weekly" value="week" />
    <DropdownOption valueLabel="Daily" value="day" />
</Dropdown>

```sql user_search
select 
  date_trunc('${inputs.agg}', created) as week,
  count(*) as new_searches
from postgres.log
where action = 'search'
group by 1
```

<LineChart
data={user_search}
y=new_searches
title = "Users Searches"
/>



```sql users_that_searched
with users_searched as (
    select 
      date_trunc('${inputs.agg}', created) as searched_date,
      user_id,
      count(*) as new_searches
    from postgres.log
    where action = 'search'
    group by 1, 2
    order by 1 desc
)

select 
  searched_date
  , count(*) as users
from users_searched
-- where searched_date > '2024-02-01'
group by 1
order by 2 desc
```

<LineChart
    data={users_that_searched}
    y=users
    title = "Users Searches"
/>

<DataTable data="{users_that_searched}" search="true" />

