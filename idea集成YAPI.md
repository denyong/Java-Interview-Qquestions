<div align="center" style="font-size:22px; font-weight:bold">IDEA集成YApi实现接口自动化导出</div>

##### 前言

在前后端分离开发的项目中，后端同学在对接需求、实现业务的同时，还需要给前端联调同学提供接口说明文档[^ 标准接口文档内容]，正规的团队合作或者项目对接，工整、快速的编写接口说明文档十分重要，结合项目组目前使用YApi作为接口文档管理工具，下文将简单总结idea集成YApi进行文档快速生成。

##### 环境准备

IntelliJ IDEA、YApi（私有化部署）、网络可用。

##### 集成步骤

+ 安装IDEA EasyApi插件

  进入`"Setting"->"Plugins"->搜索"EasyYapi"->点击Installed->重启IDEA`

  <img src="/Users/mlamp/Library/Application Support/typora-user-images/image-20210530142646540.png" alt="image-20210530142646540" style="zoom:100%;" />

+ 配置EasyApi插件

  进入`"Setting"->"Other Setting"->"EasyApi"->"Support"`

  <img src="/Users/mlamp/Library/Application Support/typora-user-images/image-20210530144304331.png" alt="image-20210530144304331" style="zoom:100%;" />

  <div align = 'center'><font size="2" color = "#888888">YApi获取token</font></div>

  <img src="https://i.loli.net/2020/08/10/JhT3GEBcx47PuAv.png" alt="img" style="zoom:100%;" />

  <div align = 'center'><font size="2" color = "#888888">Postman获取token</font></div>

  <img src="/Users/mlamp/Documents/Postman.png" alt="Hij8hfrTV4Rc9bE" style="zoom:100%;" />

+ 导出接口文档至YApi

  选中一个或多个API文件（Controller或者RPC Interface），使用快捷键 `alt insert(windows)/command E(mac)` ->选中`Export Yapi(当前Controller文件所有接口自动导出)/Export Api(可自由选择目标接口自动导出)`或`右键->Generate->Export Yapi(Export Api)`，确定导出类型及接口对象后回车。

  <img src="/Users/mlamp/Documents/NAnOKS.gif" alt="NAnOKS" style="zoom:200%;" />

  

  ##### Java注释规范（配合EasyYapi使用）

  + 类注释

    <div><font color="#888888" size="2.9">案例</font></div>

    ```java
    /**
     * 分类名称
     * 分类备注/描述
     *
     * @module 归属项目
     * @author LuoBaoTao
     * @date 2021/5/15 下午2:25
     */
    @RestController
    @RequestMapping("/demo")
    public class DemoController {
      
    }
    ```

    + 第一行默认是 `分类名称`

    + 第二行到第一个以@开头的行之前是 `接口描述`

    + `@module`用于分类api，首次导出该module时需要绑定YApi token，如果在类注释上缺失该注解，则IDEA默认module为当前类名

    + 请注意Java编码规范，类头请明确 `@author`，`@date`，YApi接口上创建人显示与代码注释中的`@author`无关，与导入操作账号有关，时间与`@date`无关，与导入操作时间有关

      <div><font color="#888888" size="2.9">通过live templates配置类注释模版</font></div>

      ```java
      /**
       * TODO
       * TODO
       *
       * @module TODO
       * @author LuoBaoTao
       * @date $date$ $time$
       */
      ```

  + 方法注释

    <div><font color="#888888" size="2.9">案例1：简单类型入参、简单类型返回</font></div>

    ```java
     /**
      * api名称
      * api描述
      *
      * @param paramA 参数 A
      * @param paramB 参数 B
      * @return
      */
     @GetMapping("/pathOfApi1")
     public String methodName1(@RequestParam("paramA")String paramA, @RequestParam("paramB")String paramB){
        log.info("paramA {} , paramB {} ", paramA, paramB);
        return "OK";
     }
    ```

    + 第一行默认是 `api名称`

    + 第二行到第一个以@开头的行之前是 `api描述`

    + `@param +【‘空格’】+【参数名】+【‘空格’】+【参数描述】` 为入参参数说明，导入文档后自动识别是否必须，默认使用`application/x-www-form-urlencoded`解析

    + `@RequestMapper + 【请求方式】`标识接口请求方式

    + 返回类型自动识别当前方法的返回类型，与`@return`注解无关

      <div><font color="#888888" size="2.9">案例2：自定义类型入参、自定义类型返回</font></div>

      ```java
      /**
       * api名称
       * api描述
       * 
       * @param mineDTO
       * @return resultVO
       * @ignore
       */
      @PostMapping(value = "/pathOfApi2")
      public ResultVO methodName2(@RequestBody MineDTO mineDTO){
          ResultVO resultVO = new ResultVO();
          resultVO.setCode(000);
          resultVO.setMessage("success");
          return resultVO;
       }
      ```
      
      + 使用`@RequestBody`标识的自定义类型入参，将使用`application/json`解析
      + 当接口被`@ignore`修饰后，该接口将不会被自动导入至YApi中
    
  + 参数注释
  
    <div><font color="#888888" size="2.9">自定义类型入参</font></div>
  
    ```java
    @Data
    public class MineDTO{
    	/**
    		* 名字
    		*/
    	@NotBlank
    	private String name;
    	/**
    		* 年龄
    		*/
    	@NotNull
    	private Integer age;
    	/**
    		* 描述
    		*/
    	private String desc;
      /**
    		* 性别
    		*/
     	private Integer gender;
    }
    ```
    
  <div><font color="#888888" size="2.9">返回自定义类型</font></div>
    
  ```java
    @Data
    public class ResultVO{
      /**
    		* 状态码
    		*/
      private Integer code;
      /**
    		* 消息
    		*/
      private String message;
      /**
    		* 返回数据
    		*/
      private Object data;
    }
    ```
    
    + 直接在字段上一行输入`/**`按`Enter`键出
    + `@NotBlank`或`@NotNull`导出后将被标识必须，否则将被标识非必需
  + `@deprecated`标识字段后将自动导出被标记为`已废弃

  ##### 自动化接口导出YApi须知
  
  每个项目接口默认是唯一存在在YApi中，接口相关注释一旦修改并被扫描上传，将覆盖上一版本，欲找回需在IDEA中回退代码并重新导出，如此便要求，开发者不可轻易修改非自己编写的注释，以免弄丢别人数据，或在扫描接口上传时，只上传自己已确认的接口，尽可能避免全文件扫描上传。

[^ 标准接口文档内容]: 接口URL、接口名称、使用场景说明、Request参数、Response参数、错误码