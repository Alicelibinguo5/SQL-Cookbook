-  rows between unbounded preceding and 1 preceding

```sql
# Write your MySQL query statement below
with cta as(
    select *,
    case when start_day > (max(end_day) over(partition by hall_id order by start_day rows between unbounded preceding and 1 preceding )
    )then 1 else 0 end  new_group
    from HallEvents 
),
cte as(
    select *, sum(new_group) over(partition by hall_id order by start_day)group_id
    from cta
)
select hall_id,
min(start_day)start_day,
max(end_day)end_day
from cte
group by 1,group_id
order by 2,1
```
