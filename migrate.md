# 分离的方法 #

### 公司表 ###
因为职员表不需要知道公司的详细信息 所以需要将其不相关的字段分离出来只留id进行关联
公司字段有：
**id**、**公司简称**、**公司全称**、**公司人数** 、**公司等级**

所以从职员表里把公司的字段分割出来并进行创建新表

    create table company as
	select company_id, company_short_name, company_full_name, company_size, financestage
	from lagou_backups;

### 城市表 ###

因为职员表不需要有具体的地区信息 所以需要将其不相关的字段分离出来只留id进行关联

城市表字段有：
**id**、**省**、**市**、**区或县** 

因为我要拿到对应的县或区 所以通过链接查询查出对应的数据

    select r.id as dId, s.cityName as province, p.cityName as city, r.cityName as district from s_provinces s 
    join s_provinces p on s.id = p.parentId
    join s_provinces r on p.id = r.parentId
    where s.depth = 1 and p.depth = 2 and r.depth = 3

但是单单拿区和县不够的 ，毕竟市还可以当做一个单独的代表，所以我还有把市给取出来

    select p.id as cId, s.cityName as province, p.cityName as city,null 
    from s_provinces s
    join s_provinces p on s.id = p.parentId
    where s.depth = 1 and p.depth = 2;

所以根据上面的两个条件 我就可以使用链接查询加union可以查询出省、市、县或区，并进行创建一个城市表

	create table lagou_city as 
    select 
    a.id,
    s.cityName as province
    ,sh.cityName as city,
    a.cityName as district 
    from s_provinces x
    join s_provinces s on x.id = s.parentId
    join s_provinces sh on s.id = sh.parentId
    join s_provinces a on sh.id = a.parentId
    where x.cityName = '中国'
    union all
    select a.id,s.cityName,a.cityName,null from s_provinces x
       join s_provinces s on x.id = s.parentId
    join s_provinces a on s.id = a.parentId
    where x.cityName = '中国';

然后把职员表的地区用相对应的id进行替换


# 总结 #

先思考职员表会拥有哪些主要信息（字段）

把次要不相关的字段进行整合分类并分离

目前到最后面一共会分出有3个表
	
> 职员表有地区的id 也有所在公司的id

所以最后会绑定相应的主外键将3个表进行关联

合成一个新的职员信息表

   