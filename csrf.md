# 跨站请求伪造（csrf）攻击

## 简介：

CSRF（Cross-site request forgery）跨站请求伪造，也被称为“One Click Attack”或者Session Riding，通常缩写为CSRF或者XSRF，是一种对网站的恶意利用。尽管听起来像跨站脚本（[XSS](http://baike.baidu.com/view/50325.htm)），但它与XSS非常不同，XSS利用站点内的信任用户，而CSRF则通过伪装来自受信任用户的请求来利用受信任的网站。与[XSS](http://baike.baidu.com/view/50325.htm)攻击相比，CSRF攻击往往不大流行（因此对其进行防范的资源也相当稀少）和难以防范，所以被认为比[XSS](http://baike.baidu.com/view/50325.htm)更具危险性。

## 防御原理：

在web应用中增加前置过滤器，对需要验证的请求验证是否包含csrf的token信息，如果不包含，则报错。这样攻击网站无法获取到token信息，则跨域提交的信息都无法通过过滤器的校验。 hap通过spring-security开启csrf验证功能。

## 实现过程：

1.首先开启csrf验证。

![](/assets/csrf1.png)

2.通过bean csrfSecurityRequestMatcher进行url过滤 默认只拦截所有的post请求,可以再bean中配置excludeUrls属性来添加不需验证的url如\/login。

![](/assets/csrf2.png)

3.将session中用于验证csrf的token值放入heade.html的meta属性中

![](/assets/csrf3.png)

4.在kendo.hap.js中添加脚本为所有的ajax请求添加token值。

![](/assets/csrf4.png)

5.若是form表单post操作需要为请求添加token请求参数。

## 使用说明：

1.若为普通form表单（非文件上传）请求 在form中添加

_&lt;input type="hidden" name="${\_csrf.parameterName}" value="${\_csrf.token}" \/&gt;_

2.若为文件上传form表单请求则在action的url后添加参数如： \/XXX?_${\_csrf.parameterName}=${\_csrf.token}_

3.若为后台自动发送post请求则在session获取csrf值自行构建post请求。

![](/assets/csrf7.png)

