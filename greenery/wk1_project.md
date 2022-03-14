**How many users do we have?**

select
  count(*)
from dbt_robert_t.stg_users;

130 users

**On average, how many orders do we receive per hour?**

select
  round(avg(num_orders), 0)
from 
  (select 
  date_trunc('hour', created_at) as created_at_hour,
  count(*) as num_orders
from dbt_robert_t.stg_orders
group by 1) avg_num_orders

8 orders 

**On average, how long does an order take from being placed to being delivered?**

with deliver_time as ( 
  select
    delivered_at - created_at as deliver_time
  from dbt_robert_t.stg_orders
) 
select 
  avg(deliver_time) as avg_deliver_time
from deliver_time

3 days 21:24:11.803279

**How many users have only made one purchase? Two purchases? Three+ purchases?**

with orders_per_user as ( 
  select
    user_id,
    count(*) as num_orders
  from dbt_robert_t.stg_orders
  group by user_id 
)
select 
  case 
    when num_orders = 1 then '1'
    when num_orders = 2 then '2'
    when num_orders >= 3 then '3+'
  end as purchase_cats,
  count(*) as num_users
from orders_per_user
group by purchase_cats

1 purchase - 25 users
2 purchases - 28 users
3+ purchases - 71 users

**On average, how many unique sessions do we have per hour?**

select
  round(avg(unique_sessions), 0)
from (select 
        date_trunc('hour', created_at) as created_at_hour,
        count(distinct(session_id)) as unique_sessions
      from dbt_robert_t.stg_events
      group by created_at_hour) unique_sessions_per_hour
      
16 sessions
