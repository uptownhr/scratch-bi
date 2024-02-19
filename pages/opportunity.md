---
title: Opportunity
---
<Dropdown itle="Aggregate" name=agg>
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

```sql opportunities_created
select 
  date_trunc('${inputs.agg}', created) as week,
  count(*) as new_opportunities
from postgres.opportunity
where 1=1 
and created > current_date - interval '${inputs.days_ago} days'
group by 1
```

<BarChart
    data={opportunities_created}
    y=new_opportunities
    title = "Opportunities Added by {inputs.agg}"
/>

```sql user_likes
select 
  date_trunc('${inputs.agg}', created) as week,
  count(*) as new_likes
from postgres.likes
where 1=1 
and created > current_date - interval '${inputs.days_ago} days'
group by 1
```

<BarChart
    data={user_likes}
    y=new_likes
    title = "New Opportunity Likes by {inputs.agg}"
/>

```sql user_count_likes
with by_day as (
    select 
      date_trunc('day', created) as day,
      user_id,
    from postgres.likes
    where 1=1
    and created > current_date - interval '${inputs.days_ago} days'
    group by 1, 2
)

select 
  date_trunc('${inputs.agg}', day) as time
  , count(*) as users
from by_day
group by 1
```

<BarChart
    data={user_count_likes}
    y=users
    title = "Users that Liked by {inputs.agg}"
/>