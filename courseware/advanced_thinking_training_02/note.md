explain type:
```
system > const > eq_ref > ref > fulltext > ref_or_null > index_merge > unique_subquery > index_subquery > range > index > ALL
```

统计指定日期范围内每天都登录的用户(连续x天都登录、加行号)
```
#没考虑时间连续
select userid,d,IF(@pre=userid,@rownum:=@rownum+1,@rownum:=1) as rownum ,@pre:=userid   from (
select  userid,DATE_FORMAT(logintime,'%Y%m%d') as d
 from user_login 
where logintime>='2019-03-05' and logintime<'2019-03-11'
GROUP BY userid,DATE_FORMAT(logintime,'%Y%m%d'))  a,(select @pre:='',@rownum:=0 ) b

#考虑时间连续
select user_id, d, IF(@pre_uid = user_id and datediff(d, @pre_date) = 1 , @rownum := @rownum + 1, @rownum := 1) as rownum, @pre_uid := user_id,@pre_date := d
from (
    select user_id, DATE_FORMAT(created_at, '%Y%m%d') as d
    from test_user_login
    where created_at >= '2019-05-051' and created_at < '2019-06-01'
    GROUP BY user_id, d) a,
        (select @pre_uid := '', @pre_date := '', @rownum := 0) b

```
