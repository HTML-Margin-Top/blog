---
title: MYBATIS使用心得
date: 2018-01-08 15:24:13
tags:
---
首先，少年，答应别人的承诺，就一定要兑现。
<!-- more -->

## mybatis 运算符
` &lt;`	<	小于号
`&gt;`	>	大于号
`&amp;`	&	与
`&apos;`	'	单引号
`&quot;`	"	双引号
* * *

##  动态查询
###  choose, when, otherwise
choose标签是按顺序判断其内部when标签中的test条件出否成立，如果有一个成立，则 choose 结束。
当 choose 中所有 when 的条件都不满则时，则执行 otherwise 中的sql。
类似于Java 的 switch 语句，choose 为 switch，when 为 case，otherwise 则为 default。
```bash
<choose>
    <when test="order == 'ASC'">
        ASC
    </when>
    <when test="order == 'DESC'">
        DESC
    </when>
</choose>
```

###   trim, where, set
介绍之前看一下下面一段代码
```bash
<select id="findActiveBlogLike" resultType="Blog">
SELECT * FROM BLOG
WHERE
<if test="state != null">
    state = #{state}
</if>
<if test="title != null">
    AND title like #{title}
</if>
<if test="author != null and author.name != null">
    AND author_name like #{author.name}
</if>
</select>
```
如果果这些条件没有一个能匹配上将会怎样？最终这条 SQL 会变成这样：
```bash
SELECT * FROM BLOG
WHERE
```
这会导致查询失败。如果仅仅第二个条件匹配又会怎样？这条 SQL 最终会是这样:
```bash
SELECT * FROM BLOG
WHERE
AND title like ‘someTitle’
```
这个查询也会失败。这个问题不能简单的用条件句式来解决，如果你也曾经被迫这样写过，那么你很可能从此以后都不想再这样去写了。
MyBatis 有一个简单的处理，这在90%的情况下都会有用。而在不能使用的地方，你可以自定义处理方式来令其正常工作。
一处简单的修改就能得到想要的效果：
```bash
<select id="findActiveBlogLike"
     resultType="Blog">
  SELECT * FROM BLOG
  <where>
    <if test="state != null">
         state = #{state}
    </if>
    <if test="title != null">
        AND title like #{title}
    </if>
    <if test="author != null and author.name != null">
        AND author_name like #{author.name}
    </if>
  </where>
</select>
```
where 元素知道只有在一个以上的if条件有值的情况下才去插入“WHERE”子句。
而且，若最后的内容是“AND”或“OR”开头的，where 元素也知道如何将他们去除。

自定义处理方式：
```bash
<trim prefix="WHERE" prefixOverrides="AND |OR ">
  ...
</trim>
```
当trim标签prefixOverrides属性不为空时，遍历prefixOverrides集合的值，
并且用trim标签里第一个子标签（比如if标签）的sql的语句头去匹配prefixOverrides集合的元素值，
一旦匹配成功，则将第一个子标签的sql语句匹配的元素删掉，继续向下运行，判断prefix属性是否有值，
若有值，在将prefix的值放到第一个子标签的sql语句开头。
prefixOverrides 属性会过滤掉前缀中通过管道分隔的文本序列（注意此例中的空格也是必要的）
prefix：前缀添加内容

类似的用于动态更新语句的解决方案叫做 set。set 元素可以被用于动态包含需要更新的列，而舍去其他的。比如：
```bash
<update id="updateAuthorIfNecessary">
  update Author
    <set>
      <if test="username != null">username=#{username},</if>
      <if test="password != null">password=#{password},</if>
      <if test="email != null">email=#{email},</if>
      <if test="bio != null">bio=#{bio}</if>
    </set>
  where id=#{id}
</update>
```
这里，set 元素会动态前置 SET 关键字，同时也会消除无关的逗号，因为用了条件语句之后很可能就会在生成的赋值语句的后面留下这些逗号。

若你对等价的自定义 trim 元素的样子感兴趣，那这就应该是它的真面目：
```bash
<trim prefix="SET" suffixOverrides=",">
  ...
</trim>
```
suffixOverrides属性会过滤掉后缀中通过管道分隔的文本序列。
注意这里我们忽略的是后缀中的值，而又一次附加了前缀中的值。

###   foreach
动态 SQL 的另外一个常用的必要操作是需要对一个集合进行遍历，通常是在构建 IN 条件语句的时候。比如：
```bash
<select id="selectPostIn" resultType="domain.blog.Post">
  SELECT *
  FROM POST P
  WHERE ID in
  <foreach item="item" index="index" collection="list"
      open="(" separator="," close=")">
        #{item}
  </foreach>
</select>
```
oreach 元素的功能是非常强大的，它允许你指定一个集合，声明可以用在元素体内的集合项和索引变量。
它也允许你指定开闭匹配的字符串以及在迭代中间放置分隔符。这个元素是很智能的，因此它不会偶然地附加多余的分隔符。

注意：你可以将任何可迭代对象（如列表、集合等）和任何的字典或者数组对象传递给foreach作为集合参数。
当使用可迭代对象或者数组时，index是当前迭代的次数，item的值是本次迭代获取的元素。
当使用字典（或者Map.Entry对象的集合）时，index是键，item是值。
* * *

## mybatis中的递归
例如权限系统，可能要根据子节点查询树形结构，根据Mybatis和Mysql,可以这样查询：
```bash
<!--获取角色所属的组织机构-->
<select id="selectRoleTree" parameterType="java.lang.String" resultMap="RoleCustom">
    SELECT * FROM TRole
    WHERE  RoleID = #{roleID}
</select>

<!-- 返回结果集 -->
<resultMap type="com.scale.po.authority.RoleCustom" id="RoleCustom">
    <id column="RoleID" property="roleid"/>
    <result column="RoleCode" property="roleCode"/>
    <result column="RoleName" property="roleName"/>
    <result column="Departmentid" property="departmentid"/>
    <result column="RoleLevel" property="roleLevel"/>
    <result column="Status" property="status"/>
    <result column="Remark" property="remark"/>
    <result column="RecorderID" property="recorderid"/>
    <result column="RecordTime" property="recordTime"/>
    <result column="ParentID" property="parentID"/>
    <collection
            property="childList"
            column="RoleID"
            ofType="com.scale.po.authority.RoleCustom"
            select="findMenuByparentId">
    </collection>
</resultMap>

<!-- 根据父键查询 -->
<select id="findMenuByparentId" parameterType="String" resultMap="RoleCustom">
    select * from TRole where ParentID = #{RoleID}
</select>
```
这样就可以实现递归查询树形结构数据了。

