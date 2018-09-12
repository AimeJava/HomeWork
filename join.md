# 总结 #



    select x.cityName,s.cityName,sh.cityName from s_provinces x
       inner join s_provinces s on x.id = s.parentId
       inner join s_provinces sh on s.id = sh.parentId

    where x.cityName = '广东省';

**1、先根据总表的数据获取广东省的id对应市的id，即以广东省的id为条件查询出广东省所包含的所以市**

**2、再根据每个市的id对应的县id 再查出所包含的所以县**
	

