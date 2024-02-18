---
title: Opportunity
---
<Dropdown name=agg>
    <DropdownOption valueLabel="Monthly" value="month" />
    <DropdownOption valueLabel="Weekly" value="week" />
    <DropdownOption valueLabel="Daily" value="day" />
</Dropdown>

```sql opportunities_created
select 
  date_trunc('${inputs.agg}', created) as week,
  count(*) as new_opportunities
from postgres.opportunity
where created > '2023-01-01'
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
where created > '2023-08-01'
group by 1
```

<BarChart
    data={user_likes}
    y=new_likes
    title = "New Opportunity Likes"
/>