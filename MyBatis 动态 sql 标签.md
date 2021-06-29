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

   

6. **foreach**

7. **bind**

```
特殊字符     转义序列      含义
<           &lt;        小于
>           &gt;        大于
&           &amp;       且
"           &quot;      双引号
'           &apos;      单引号
<>          &lt;&gt;    不等于
```

