---
title: Search
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

```sql user_search
select 
  date_trunc('${inputs.agg}', created) as week,
  count(*) as new_searches
from postgres.log
where action = 'search'
and week > current_date - interval '${inputs.days_ago} days'
group by 1
```

<BarChart
data={user_search}
y=new_searches
title = "Total Searches by {inputs.agg}"
/>



```sql users_that_searched
with users_searched as (
    select 
      date_trunc('${inputs.agg}', created) as searched_date,
      user_id
    from postgres.log
    where action = 'search'
    group by 1, 2
    order by 1 desc
)

select 
  searched_date
  , count(*) as users
from users_searched
where 1=1
and searched_date > current_date - interval '${inputs.days_ago} days'
group by 1
order by 2 desc
```

<BarChart
    data={users_that_searched}
    y=users
    title = "Users that Searched by {inputs.agg}"
/>