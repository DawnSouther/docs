# 代码生成器

# 简介：

本功能可根据数据表生成相关的dto，mapper impl，service，controller与html，包含相关的增删改查功能。

**网页入口**：http:\/\/localhost:8080\/hap\/generator\/generator.html

## 使用说明：

**主页面：**

![](/assets/gnerator1.png)

**项目路径**：指向本机hap。

**包父路径：**指向hap的包路径。

**包路径：**为所要生成的包名。

**选择表：**选中所要维护的表。

**文件名：** 将自动根据表名生成对应名称（修改dto可选择是否根据dto修改其余文件，也可单独修改其余文件）（ 文件创建之前会判断项目中是否有同名文件 ） 。

**是否创建：**勾选创建文件。

**是否覆盖：**勾选则可以覆盖同路径下同名文件。

**选择ftl模板：**可根据选择的ftl模板生成对应的html页面。

## 生成示例：

**1：相关文件**

![](/assets/generator2.png)

2：相关的dto

![](/assets/generator4.png)

**3：相关controller**（包含增删改查）

![](/assets/generator5.png)

**4：相关mapper**（包含基础resultMap）

![](/assets/generator6.png)

**5：生成基础的增删改查功能的html页面**

![](/assets/generator3.png)

**6：service文件** （mapper类似）

![](/assets/gernerator7.png)
