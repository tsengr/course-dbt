What is our user repeat rate?
with user_order_counts as (
  select
    user_id,
    count(user_id) as user_order_count,
    case 
      when count(user_id) < 2 then 0
      when count(user_id) >= 2 then 1 
    end as is_multi_purchase_user
  from dbt_robert_t.stg_orders
  group by user_id
) 
select 
  round(sum(is_multi_purchase_user)/count(distinct user_id)::numeric * 100, 2) || '%' as repeat_rate
from user_order_counts

79.84%

What are good indicators of a user who will likely purchase again? What about indicators of users who are likely NOT to purchase again? If you had more data, what features would you want to look into to answer this question?
