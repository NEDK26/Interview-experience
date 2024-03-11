# 流水账笔记

1. Springboot什么时候用到热部署

   测试环境中，提升调试效果

   生成环境，变更代码和重启服务器需要十分谨慎，往往需要复杂的审批

2. Banner定制启动信息

   resources目录下新建一个banner.txt即可

   启动类中加入此行

   ```java
   app.setBannerMode(Banner.mode.OFF)
   ```

   

3.  控制器类

   ```java
   @RestController
   @RequestMapping(value='',method='',param='') # 映射请求url
   ```

   

4. 
