## Sql

* 获取分页数据
* 分页数据量

```xml
<!--    !=2代表可以显示-->
    <select id="getBlogsBySid" resultType="blog">
        select <include refid="getFields"></include>
        from m_blog
        where status!=2
        <if test="sid!=0">
            and sid = #{sid}
        </if>
        order by created desc
        limit #{offset},#{limit}
    </select>
<!--    获取博客数量-->
    <select id="getBlogsRows" resultType="int">
        select count(id)
        from m_blog
        where status!=2
        and sid =#{sid}
    </select>
```

## Controller

* 传入参数有当前页码以及每页数量
* 根据当前页码计算偏移量，并将其和pagesize 传递给mapper
* 同时获取数据量一并传递给前端

```java
@Autowired
    BlogMapper blogMapper;
    @RequestMapping("/blogList/{sid}/{currentPage}/{pageSize}")
    public Result getblogs(@PathVariable int sid,@PathVariable int currentPage,@PathVariable int pageSize){
        List<Blog> blogs = blogMapper.getBlogsBySid(sid, (currentPage - 1) * pageSize, pageSize);
        if (blogs!=null){
            PageResult pageResult = new PageResult(blogMapper.getBlogsRows(sid),blogs);
            return Result.succ(pageResult);
        }
        System.out.println("查询BlogList:"+sid);
        return Result.fail("获取当前博客列表失败");
    }
```

## 插件

```
 <dependency>
            <groupId>com.github.pagehelper</groupId>
            <artifactId>pagehelper</artifactId>
            <version>4.1.2</version>
        </dependency>

```

