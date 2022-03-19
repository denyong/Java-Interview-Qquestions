# MyBatis

## MyBatis 动态 sql 标签

### 什么是MyBatis 动态 sql

MyBatis 动态 sql 可以让我们在 Xml 映射文件内，以标签的形式编写动态 sql，完成逻辑判断和动态拼接 sql 的功能，MyBatis 提供了 9 种动态 sql 标签 `trim|where|set|foreach|if|choose|when|otherwise|bind`。

其执行原理为，使用 OGNL 从 sql 参数对象中计算表达式的值，根据表达式的值动态拼接 sql，以此来完成动态 sql 的功能。

为了统一变量名称，下面使用name和age两个字段来完成案例演示。

1. **if** 

   <if test> 标签用来判断变量是否满足条件，满足则拼接为SQL。不满足则忽略。

   场景一：name不为空且name不为空字符串。

   ```
   SELECT
       id,
       appId,
       products,
   FROM
       business
   WHERE
       id = 15
       -- 注意：若<if test=""> 使用了双引号，则进行值判断时name!='' 应该使用''单引号 
       <if test="name != null and name != ''">
           and name = #{name}
       </if>
   ```

   上述语句若不满足条件将拼接为：

   ```
   SELECT id, appId, products FROM business WHERE id = 15;
   ```

   上述语句若满足条件将拼接为：

   ```
   SELECT id, appId, products FROM business WHERE id = 15 and name = 'aa';
   ```

   场景二：age不为空且age=18。

   ```
   SELECT
       id,
       appId,
       products,
   FROM
       business
   WHERE
       id = 15
       <if test="age != null and age == 18">
           and age = #{age}
       </if>
   ```

   上述语句若不满足条件将拼接为：

   ```
   SELECT id, appId, products FROM business WHERE id = 15;
   ```

   上述语句若满足条件将拼接为：

   ```
   SELECT id, appId, products FROM business WHERE id = 15 and age= 18;
   ```

   

2. **choose**   **when**   **otherwise**

   在一些特定查询场景下，我们需要多对多个值进行判断，选择出一个符合条件的值。**choose**   标签类似于Java 的 switch 语句，choose 为 switch，when 为 case，otherwise 则为 default

   ```
   SELECT
       id,
       appId,
       products
   FROM
       business
   WHERE
       id = 15
       <choose>
           <when test="name != null">
               and name = #{name}
           </when>
           <when test="age != null">
               and age = #{age}
           </when>
           <otherwise>
               and name = "lisi"
               and age = 18
           </otherwise>
       </choose>
   ```

   场景一：name不为空且age不为空

   当判断name不为空成立时，则跳出**choose** 不在继续往下判断。SQL为：

   ```
   SELECT id, appId, products FROM business WHERE id = 15 and name = 'a';
   ```

   场景二：name为空且age不为空

   当判断name为空成立时，且age不为空   则跳出**choose** 不在继续往下判断。SQL为：

   ```
   SELECT id, appId, products FROM business WHERE id = 15 and age = 1;
   ```

   场景三：name为空且age为空

   当判断name为空成立时，且age为空时   **choose** 会执行**otherwise**中的内容。SQL为：

   ```
   SELECT id, appId, products FROM business WHERE id = 15 and name = "lisi" and age = 18;
   ```

3. **trim**

   1.去除多余的关键字

   ```
   <select id="findActiveBlogLike"
        resultType="Blog">
     SELECT * FROM BLOG 
     <trim prefix="WHERE" prefixOverrides="AND">
       <if test="state != null">
         state = #{state}
       </if> 
       <if test="title != null">
         AND title like #{title}
       </if>
       <if test="author != null and author.name != null">
         AND author_name like #{author.name}
       </if>
   </trim>
   </select>
   ```

   2.去除多余的逗号

   ```
   insert into business
   <trim prefix="(" suffix=")" suffixOverrides=",">
     <if test="name != null">
       name,
     </if>
     <if test="age != null">
       age
     </if>
   </trim>
   values
   <trim prefix="(" suffix=")" suffixOverrides=",">
     <if test="name != null">
       name,
     </if>
     <if test="age != null">
       age
     </if>
   </trim>
   ```

   

4. **where**

   在做条件查询时，我们需要先对条件做过滤，比如name不为空时，才会使用name来做条件过滤

   ```
   SELECT
       id,
       appId,
       products
   FROM
       business
   <where>
   	id = 15
       <if test="name != null">
           AND name = #{name}
       </if>
       <if test="age != null">
           AND age = #{age}
       </if>
   </where>
   ```

   场景一：name不为空 age为空

   当判断name不为空成立时，**where** 条件中会出现name的判断。SQL为：

   ```
   SELECT id, appId, products FROM business WHERE id = 15 AND name = 'a';
   ```

   场景二：age不为空 name为空

   当判断age不为空成立时，**where** 条件中会出现age的判断。SQL为：

   ```
   SELECT id, appId, products FROM business WHERE id = 15 AND age = 18;
   ```

   场景三：name为空 且age为空

   当判断name为空且age为空成立时，**where** 条件中则不会出现name和age的判断。SQL为：

   ```
   SELECT id, appId, products FROM business WHERE id = 15;
   ```

5. **set**

   set使用于更新的场景，如果参数为空时，我们不想更新字段，那么可以使用set来判断

   ```
   update business
   <set>
   	<if test="name != null and name != ''">name=#{name},</if>
   	<if test="age != null">age=#{age},</if>
   </set>
   where id=1
   ```

6. **foreach**

   | 属性         | 描述                                                         |
   | ------------ | ------------------------------------------------------------ |
   | `item`       | 循环体中的具体对象，每次迭代中获取的元素。支持属性的点路径访问，如`item.ageitem.info.details`。具体说明：在`list`和数组中是其中的对象，在`map`中是`value`。该参数为必选 |
   | `collection` | 要做`foreach`的对象，作为入参时，`List<?>`对象默认用`list`代替作为键，数组对象有array代替作为键，`Map`对象用`map`代替作为键。当然在作为入参时可以使用`@Param(“keyName”)`来设置键，设置`keyName`后，`list,array,map`将会失效，而是使用`@Param`指定的别名。 除了入参这种情况外，还有一种作为参数对象的某个字段的时候。举个例子：如果`User`有属性`List ids`。入参是`User`对象，那么这个`collection = "ids"`如果`User`有属性`Ids ids`;其中`Ids`是个对象，`Ids`有个属性`List id`;入参是`User`对象，那么`collection = "ids.id"`上面只是举例，具体`collection`等于什么，就看你想对那个元素做循环。该参数为必选 |
   | `separator`  | 元素之间的分隔符，例如在`in()`的时候，`separator=","`会自动在元素中间用`“,“`隔开，避免手动输入逗号导致`sql`错误，如`in(1,2,)`这样。该参数可选 |
   | `open`       | `foreach`代码的开始符号，一般是`(`和`close=")"`合用。常用在`in(),values()`时。该参数可选 |
   | `close`      | `foreach`代码的关闭符号，一般是`)`和`open="("`合用。常用在`in(),values()`时。该参数可选 |
   | `index`      | 在`list`和`数组`中,`index`是元素的序号，在`map`中，`index`是元素的`key`，该参数可选 |

   ```
   select name from business where id in
   <foreach collection="list" open="(" separator="," item="item" index="index" close=")">
       #{item}
   </foreach>
   ```

7. **bind**

   在进行模糊查询时，如果使用“${}”拼接字符串，则无法防止 SQL 注入问题。如果使用字符串拼接函数或连接符号，但不同数据库的拼接函数或连接符号不同。

   例如 MYSQL 的 concat 函数、Oracle 的连接符号“||”，这样 SQL 映射文件就需要根据不同的数据库提供不同的实现，显然比较麻烦，且不利于代码的移植。所以MyBatis 提供了 <bind> 元素来解决这一问题

   ```
   <!--使用bind元素进行模糊查询-->
   <select id="selectBusinessById" resultType="com.po.MyUser">
       <!-- bind 中的 name 是 com.po.MyUser 的属性名-->
       <bind name="paran_uname" value="'%' + name + '%'"/>
           select * from user where uname like #{paran_uname}
   </select>
   ```

## Mybatis缓存机制