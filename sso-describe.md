# 单点登录

参照 [CAS集成](chapter2.1.md)可以开启单点登录。

Hap目前支持单点登录成功后，可以跳转至登录前的URL。

> 注意系统管理-系统配置页面的配置项---登录成功跳转URL。默认设置为/index，则可以自动跳转至登录前的URL，如果改为自定义的其他页面，则会带上?targetUrl=xxx参数先跳转至系统配置的URL，可自行处理跳转。

可以配置CAS和标准登录并存，默认登录页面为SSO登录页面，访问/login，则可以进入标准登录页面。可以参照如下配置：

修改spring/casSecurity.xml


```xml
<!-- 此文件用于CAS sso登录方式 -->

<beans:beans xmlns="http://www.springframework.org/schema/security"
             xmlns:beans="http://www.springframework.org/schema/beans"
             xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
             xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-4.2.xsd
                                 http://www.springframework.org/schema/security http://www.springframework.org/schema/security/spring-security-4.0.xsd">
    <!-- CAS 基本属性配置-->
    <beans:bean id="serviceProperties"
          class="org.springframework.security.cas.ServiceProperties">
        <beans:property name="service"
                  value="${cas.service}"/>
        <beans:property name="sendRenew" value="false"/>
    </beans:bean>

    <!-- CAS 认证配置 -->
    <http  entry-point-ref="casEntryPoint" access-decision-manager-ref="accessDecisionManager" create-session="never" >
        <csrf request-matcher-ref="csrfCasSecurityRequestMatcher" />
        <intercept-url pattern="/resources/**" access="permitAll" />
        <intercept-url pattern="/lib/**" access="permitAll" />
        <intercept-url pattern="/common/**" access="permitAll" />
        <intercept-url pattern="/login" access="permitAll" />
        <intercept-url pattern="/login.html" access="permitAll" />
        <intercept-url pattern="/verifiCode" access="permitAll" />
        <intercept-url pattern="/websocket/**" access="permitAll" />
        <intercept-url pattern="/**" access="hasRole('ROLE_USER')" />
        <access-denied-handler error-page="/access-denied"/>

        <form-login login-page='/login' authentication-success-handler-ref="successHandler"
                    authentication-failure-handler-ref="loginFailureHandler"/>
        <!--authentication-failure-url="/login?error=true"/>-->
        <!-- 验证码拦截器 -->
        <custom-filter ref="captchaVerifierFilter" before="FORM_LOGIN_FILTER"/>


        <custom-filter ref="requestSingleLogoutFilter" before="LOGOUT_FILTER"/>
        <custom-filter position="CAS_FILTER" ref="casFilter" />
        <custom-filter ref="singleLogoutFilter" before="CAS_FILTER"/>

        <headers defaults-disabled="true">
            <cache-control/>
        </headers>
    </http>

    <!-- 认证管理器,确定用户,角色及相应的权限 -->
    <beans:bean id="accessDecisionManager" class="org.springframework.security.access.vote.UnanimousBased">
        <!-- 投票器 -->
        <beans:constructor-arg>
            <beans:list>
                <beans:bean class="com.hand.hap.security.PermissionVoter"/>
                <beans:bean class="org.springframework.security.web.access.expression.WebExpressionVoter"/>
                <beans:bean class="org.springframework.security.access.vote.RoleVoter"/>
                <beans:bean class="org.springframework.security.access.vote.AuthenticatedVoter"/>

            </beans:list>
        </beans:constructor-arg>
    </beans:bean>


    <!-- CAS Filter 配置 -->
    <beans:bean id="casFilter"
          class="org.springframework.security.cas.web.CasAuthenticationFilter">
        <beans:property name="authenticationManager" ref="authenticationManager"/>
        <beans:property name="authenticationSuccessHandler" ref="successHandler"/>
    </beans:bean>

    <beans:bean id="successHandler" class="com.hand.hap.security.CustomAuthenticationSuccessHandler">
        <beans:property name="defaultTargetUrl" value="/index"/>
    </beans:bean>


    <beans:bean id="casEntryPoint"
          class="org.springframework.security.cas.web.CasAuthenticationEntryPoint">
        <beans:property name="loginUrl" value="${cas.ssoserver.loginurl}"/>
        <beans:property name="serviceProperties" ref="serviceProperties"/>
    </beans:bean>


    <authentication-manager alias="authenticationManager">
        <authentication-provider ref="casAuthenticationProvider"/>
        <authentication-provider user-service-ref="customUserDetailsService">
            <password-encoder ref="passwordManager"/>
        </authentication-provider>
    </authentication-manager>

    <beans:bean id="casAuthenticationProvider"
          class="org.springframework.security.cas.authentication.CasAuthenticationProvider">
        <beans:property name="serviceProperties" ref="serviceProperties" />
        <beans:property name="authenticationUserDetailsService" ref="customAuthenticationUserDetailsService"/>
        <beans:property name="ticketValidator">
            <beans:bean class="org.jasig.cas.client.validation.Cas20ServiceTicketValidator">
                <beans:constructor-arg index="0" value="${cas.ssoserver.url}" />
            </beans:bean>
        </beans:property>
        <beans:property name="key" value="an_id_for_this_auth_provider_only"/>
    </beans:bean>


    <beans:bean id="customAuthenticationUserDetailsService" class="com.hand.hap.security.CustomAuthenticationUserDetailsService">
    </beans:bean>


    <beans:bean id="singleLogoutFilter" class="org.jasig.cas.client.session.SingleSignOutFilter"/>
    <!-- This filter redirects to the CAS Server to signal Single Logout should be performed -->
    <beans:bean id="requestSingleLogoutFilter"
          class="org.springframework.security.web.authentication.logout.LogoutFilter">
        <beans:constructor-arg value="${cas.ssoserver.logouturl}"/>
        <beans:constructor-arg>
            <beans:bean class="org.springframework.security.web.authentication.logout.SecurityContextLogoutHandler"/>
        </beans:constructor-arg>
        <beans:property name="filterProcessesUrl" value="/logout"/>
    </beans:bean>

    <beans:bean id="csrfCasSecurityRequestMatcher" class="com.hand.hap.security.CsrfSecurityRequestMatcher">
        <beans:property name="excludeUrls">
            <beans:list>
                <beans:value>/login</beans:value>
                <beans:value>/websocket/**</beans:value>
            </beans:list>
        </beans:property>
    </beans:bean>

    <beans:bean id="captchaVerifierFilter" class="com.hand.hap.security.CaptchaVerifierFilter">
        <beans:property name="captchaField" value="verifiCode"/>
    </beans:bean>
    <beans:bean id="loginFailureHandler" class="com.hand.hap.security.LoginFailureHandler"/>
</beans:beans>

```





