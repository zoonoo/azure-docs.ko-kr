<properties linkid="develop-java-how-to-guides-web-sso" urlDisplayName="Web SSO" pageTitle="Single sign-on with Azure Active Directory (Java)" metaKeywords="Azure Java web app, Azure single sign-on, Azure Java Active Directory" description="Learn how to create a Java web application that uses single sign-on with Azure Active Directory." metaCanonical="" services="active-directory" documentationCenter="Java" title="Web Single Sign-On with Java and Azure Active Directory" authors="mbaldwin" solutions="" manager="mbaldwin" editor="" />

<tags ms.service="active-directory" ms.workload="identity" ms.tgt_pltfrm="na" ms.devlang="Java" ms.topic="article" ms.date="01/01/1900" ms.author="mbaldwin"></tags>

# Java 및 Azure Active Directory를 사용한 웹 Single Sign-On

## <a name="introduction"></a>소개

이 자습서에서는 Java 개발자에게 Azure Active Directory를 활용하여 Office 365 고객의 사용자에 대해 Single Sign-On을 사용하도록 설정하는 방법을 보여 줍니다. 다음 방법에 대해 알아봅니다.

-   고객의 테넌트에서 웹 응용 프로그램 프로비전
-   WS-Federation을 사용하여 응용 프로그램 보호

### 필수 조건

이 자습서에서는 특정 응용 프로그램 서버를 사용하지만 숙련된 Java 개발자의 경우 아래에 설명된 프로세스를 다른 환경에도 적용할 수 있습니다. 이 자습서에는 다음과 같은 개발 환경 필수 조건이 필요합니다.

-   [Azure Active Directory용 Java 샘플 코드][]
-   [Java Runtime Environment 1.6][]
-   [JBoss Application Server 버전 7.1.1.Final][]
-   [JBoss Developer Studio IDE][]
-   IIS(인터넷 정보 서비스) 7.5(SSL 사용)
-   Windows PowerShell
-   [Office 365 PowerShell Commandlet][]

### 목차

-   [소개][]
-   [1단계: Java 응용 프로그램 만들기][]
-   [2단계: 회사 디렉터리 테넌트에서 응용 프로그램 프로비전][]
-   [3단계: 직원 로그인을 위해 WS-Federation을 사용하여 응용 프로그램 보호][]
-   [요약][]

## <a name="createapp"></a>1단계: Java 응용 프로그램 만들기

이 단계에서는 보호된 리소스를 나타내는 간단한 Java 응용 프로그램을 만드는 방법을 설명합니다. 이 리소스에 대한 액세스 권한은 자습서의 뒷부분에 설명된 회사의 STS에 의해 관리되는 페더레이션 인증을 통해 부여됩니다.

1.  JBoss Developer Studio의 새 인스턴스를 엽니다.
2.  **File** 메뉴에서 **New**를 클릭한 후 **Project...**를 클릭합니다.
3.  **New Project** 대화 상자에서 **Maven** 폴더를 확장하고 **Maven Project**를 클릭한 후 **Next**를 클릭합니다.
4.  **New Maven Project** 대화 상자에서 **Create a simple project (skip archetype selection)**를 선택한 후 **Next**를 클릭합니다.
5.  **New Maven Project** 대화 상자의 다음 페이지에서 **Group Id** 및 **Artifact Id** 텍스트 상자에 *sample*을 입력합니다. 그런 다음 **Packaging** 드롭다운 메뉴에서 **war**을 선택하고 **Finish**를 클릭합니다.
6.  새 전문가 프로젝트가 만들어집니다. 왼쪽의 **Project Explorer** 메뉴에서 **sample** 프로젝트를 확장하고 **pom.xml** 파일을 마우스 오른쪽 단추로 클릭한 후 **Open With**를 클릭하고 **Text Editor**를 클릭합니다.
7.  **pom.xml** 파일에서 *\<project\>* 섹션 내에 다음 XML을 추가합니다.

        <repositories>
            <repository>
                <id>jboss-public-repository-group</id>
                <name>JBoss Public Maven Repository Group</name>
                <url>https://repository.jboss.org/nexus/content/groups/public-jboss/</url>
                <layout>default</layout>
                <releases>
                    <enabled>true</enabled>
                    <updatePolicy>never</updatePolicy>
                </releases>
                <snapshots>
                    <enabled>true</enabled>
                    <updatePolicy>never</updatePolicy>
                </snapshots>
            </repository>
        </repositories>
        <pluginRepositories>
            <pluginRepository>
                <id>jboss-public-repository-group</id>
                <name>JBoss Public Maven Repository Group</name>
                <url>https://repository.jboss.org/nexus/content/groups/public-jboss/</url>
                <layout>default</layout>
                <releases>
                    <enabled>true</enabled>
                    <updatePolicy>always</updatePolicy>
                </releases>
                <snapshots>
                    <enabled>true</enabled>
                    <updatePolicy>always</updatePolicy>
                </snapshots>
            </pluginRepository>
        </pluginRepositories>
        <build>
            <plugins>
                <plugin>
                    <groupId>org.apache.maven.plugins</groupId>
                    <artifactId>maven-compiler-plugin</artifactId>
                    <version>2.0.2</version>
                    <configuration>
                        <source>1.6</source>
                        <target>1.6</target>
                    </configuration>
                </plugin>
            </plugins>
        </build> 

    이 XML을 입력한 후 **pom.xml** 파일을 저장합니다.

8.  **sample** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **Maven**을 클릭한 후 **Update Maven Project**를 클릭합니다. **Update Maven Project** 대화 상자에서 **OK**를 클릭합니다. 이 단계에서는 프로젝트를 **pom.xml** 변경 내용으로 업데이트합니다.

9.  **sample** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **New**를 클릭한 후 **JSP File**을 클릭합니다.

10. **New JSP File** 대화 상자에서 새 파일의 경로를 *sample/src/main/webapp*으로 변경합니다. 그런 다음 파일의 이름을 **index.jsp**로 지정하고 **Finish**를 클릭합니다.

11. 새 **index.jsp** 파일이 자동으로 열립니다. 자동으로 생성된 코드를 다음으로 바꾼 후 파일을 저장합니다.

        <%@ page language="java" contentType="text/html; charset=ISO-8859-1"  pageEncoding="ISO-8859-1"%>
        <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
        <html>
        <head>
            <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
            <title>Index</title>
        </head>
        <body>
            <h3>Index Page</h3>
        </body>
        </html> 

12. **sample** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **Run As**를 클릭한 후 **Run on Server**를 클릭합니다.

13. **Run On Server** 대화 상자에서 **JBoss Enterprise Application Platform 6.x**가 선택되어 있는지 확인한 후 **Finished**를 클릭합니다.

## <a name="provisionapp"></a>2단계: 회사 디렉터리 테넌트에서 응용 프로그램 프로비전

이 단계에서는 Azure Active Directory 고객의 관리자가 해당 테넌트에서 Java 응용 프로그램을 프로비전하고 Single Sign-On을 구성하는 방법을 설명합니다. 이 단계가 완료된 후 회사의 직원은 해당 Office 365 계정을 사용하여 웹 응용 프로그램에 인증할 수 있습니다.

프로비전 프로세스는 응용 프로그램의 새 서비스 사용자를 만드는 것으로 시작됩니다. 서비스 사용자는 Azure Active Directory에서 디렉터리에 응용 프로그램을 등록하고 인증하는 데 사용됩니다.

1.  아직 수행하지 않은 경우 Office 365 PowerShell Commandlet을 다운로드하여 설치합니다.
2.  **시작** 메뉴에서 **Windows PowerShell용 Microsoft Online Services 모듈** 콘솔을 실행합니다. 이 콘솔은 서비스 사용자 만들기 및 수정과 같은 Office 365 테넌트에 대한 특성 구성을 위한 명령줄 환경을 제공합니다.
3.  필수 **MSOnlineExtended** 모듈을 가져오려면 다음 명령을 입력하고 Enter 키를 누릅니다.

        Import-Module MSOnlineExtended -Force

4.  Office 365 디렉터리에 연결하려면 회사의 관리자 자격 증명을 제공해야 합니다. 다음 명령을 입력하고 Enter 키를 누른 후 프롬프트에 자격 증명을 입력합니다.

        Connect-MsolService

5.  이제 응용 프로그램의 새 서비스 사용자를 만듭니다. 다음 명령을 입력하고 Enter 키를 누릅니다.

        New-MsolServicePrincipal -ServicePrincipalNames @("javaSample/localhost") -DisplayName "Federation Sample Website" -Type Symmetric -Usage Verify -StartDate "12/01/2012" -EndDate "12/01/2013" 

    이 단계에서는 다음과 유사한 정보가 출력됩니다.

        The following symmetric key was created as one was not supplied qY+Drf20Zz+A4t2w e3PebCopoCugO76My+JMVsqNBFc=
        DisplayName           : Federation Sample Java Website
        ServicePrincipalNames : {javaSample/localhost}
        ObjectId              : 59cab09a-3f5d-4e86-999c-2e69f682d90d
        AppPrincipalId        : 7829c758-2bef-43df-a685-717089474505
        TrustedForDelegation  : False
        AccountEnabled        : True
        KeyType               : Symmetric
        KeyId                 : f1735cbe-aa46-421b-8a1c-03b8f9bb3565
        StartDate             : 12/01/2012 08:00:00 a.m.
        EndDate               : 12/01/2013 08:00:00 a.m.
        Usage                 : Verify 

    > [WACOM.NOTE]
    > 이 출력을 저장해야 하며, 특히 생성된 대칭 키를 저장해야 합니다. 이 키는 서비스 사용자를 만드는 동안에만 표시되며 나중에는 이 키를 검색할 수 없습니다. 다른 값은 Graph API를 사용하여 디렉터리에서 정보를 읽고 쓰는 데 필요합니다.

6.  마지막 단계는 응용 프로그램의 릴레이 URL을 설정하는 것입니다. 릴레이 URL은 응답이 다음과 같은 인증 시도로 전송되는 위치입니다. 다음 명령을 입력하고 Enter 키를 누릅니다.

        $replyUrl = New-MsolServicePrincipalAddresses -Address "https://localhost:8443/sample" 

        Set-MsolServicePrincipal -AppPrincipalId "7829c758-2bef-43df-a685-717089474505" -Addresses $replyUrl 

이제 디렉터리에서 웹 응용 프로그램이 프로비전되어 회사 직원이 이를 웹 Single Sign-On에 사용할 수 있습니다.

## <a name="protectapp"></a>3단계: 직원 로그인을 위해 WS-Federation을 사용하여 응용 프로그램 보호

이 단계에서는 WIF(Windows Identity Foundation)를 사용하는 페더레이션 로그인 및 필수 조건에서 샘플 코드 패키지의 일부로 다운로드한 **waad-federation** 라이브러리에 대한 지원을 추가하는 방법을 보여 줍니다. 또한 로그인 페이지를 추가하고 응용 프로그램과 디렉터리 테넌트 간에 트러스트를 구성합니다.

1.  JBoss Developer Studio에서 **File**을 클릭한 후 **Import**를 클릭합니다.

2.  **Import** 대화 상자에서 **Maven** 폴더를 확장하고 **Existing Maven Projects**를 클릭한 후 **Next**를 클릭합니다.

3.  **Import Maven Projects** 대화 상자에서 **Root Directory** 경로를 샘플 코드의 **waad-federation** 라이브러리를 다운로드한 위치로 설정합니다. 그런 다음 **waad-federation** 프로젝트에서 **pom.xml** 파일 옆에 있는 확인란을 선택하고 **Finish**를 클릭합니다.

4.  **sample** 프로젝트를 확장하고 **pom.xml** 파일을 마우스 오른쪽 단추로 클릭한 후 **Open With**를 클릭하고 **Text Editor**를 클릭합니다.

5.  **pom.xml** 파일에서 *\<project\>* 섹션 내에 다음 XML을 추가한 후 파일을 저장합니다.

        <dependencies>
            <dependency>
                <groupId>javax.servlet</groupId>
                <artifactId>servlet-api</artifactId>
                <version>3.0-alpha-1</version>
            </dependency>
            <dependency>
                <groupId>com.microsoft.samples.waad.federation</groupId>
                <artifactId>waad-federation</artifactId>
                <version>0.0.1-SNAPSHOT</version>
            </dependency>
        </dependencies> 

6.  **sample** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **Maven**을 클릭한 후 **Update Project**를 클릭합니다. **Update Maven Project** 대화 상자에서 **OK**를 클릭합니다. 이 단계에서는 프로젝트를 **pom.xml** 변경 내용으로 업데이트합니다.

7.  **sample** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **New**를 클릭한 후 **Filter**를 클릭합니다.

8.  **Create Filter** 대화 상자에서 **Class name** 항목에 대해 *FederationFilter*를 입력한 후 **Finish**를 클릭합니다.

9.  자동으로 생성된 **FederationFilter.java** 파일이 열립니다. 해당 코드를 다음 코드로 바꾸고 파일을 저장합니다.

        import java.io.IOException;
        import javax.servlet.Filter;
        import javax.servlet.FilterChain;
        import javax.servlet.FilterConfig;
        import javax.servlet.ServletException;
        import javax.servlet.ServletRequest;
        import javax.servlet.ServletResponse;
        import javax.servlet.http.HttpServletRequest;
        import javax.servlet.http.HttpServletResponse;
        import java.util.regex.*;
        import com.microsoft.samples.federation.FederatedLoginManager; import com.microsoft.samples.federation.URLUTF8Encoder;

        public class FederationFilter implements Filter {
            private String loginPage;
            private String allowedRegex;
            public void init(FilterConfig config) throws ServletException {
                this.loginPage = config.getInitParameter("login-page-url");
                this.allowedRegex = config.getInitParameter("allowed-regex");
            }
            public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) throws IOException, ServletException {
                    if (request instanceof HttpServletRequest) { 
                        HttpServletRequest httpRequest = (HttpServletRequest) request;
                        if (!httpRequest.getRequestURL().toString().contains(this.loginPage)) {
                            FederatedLoginManager loginManager = FederatedLoginManager.fromRequest(httpRequest);
                            boolean allowedUrl = Pattern.compile(this.allowedRegex).matcher(httpRequest.getRequestURL().toString()).find();
                            if (!allowedUrl && !loginManager.isAuthenticated()) {
                                HttpServletResponse httpResponse = (HttpServletResponse) response;
                                String encodedReturnUrl = URLUTF8Encoder.encode(httpRequest.getRequestURL().toString());
                                httpResponse.setHeader("Location", this.loginPage + "?returnUrl=" + encodedReturnUrl);
                                httpResponse.setStatus(302);
                                return;
                            }
                        }
                    }
                chain.doFilter(request, response);
            }
            public void destroy() {
            }
        } 

10. **Project Explorer**에서 **src**, **main**, **webapp** 폴더를 차례로 확장합니다. **web.xml** 파일을 마우스 오른쪽 단추로 클릭하고 **Open With**를 클릭한 후 **Text Editor**를 클릭합니다.

11. **web.xml** 파일에서 보안 페이지 및 비보안 페이지를 처리하기 위한 필터를 추가하고, 인증되지 않은 사용자도 로그인 페이지(**login-page-url** 필터 매개 변수로 지정됨)로 리디렉션됩니다. 그러나 URL이 **allowed-regex** 필터 매개 변수에 지정된 regex와 일치하는 경우 필터링되지 않습니다. *\<web-app\>* 섹션 내에 다음 XML을 추가한 후 **web.xml** 파일을 저장합니다.

        <filter>
            <filter-name>FederationFilter</filter-name>
            <filter-class>FederationFilter</filter-class>
            <init-param>
                <param-name>login-page-url</param-name>
                <param-value>/sample/login.jsp</param-value>
            </init-param>
            <init-param>
                <param-name>allowed-regex</param-name>
                <param-value>(\/sample\/login.jsp|\/sample\/wsfed-saml|\/sample\/oauth)</param-value>
            </init-param>
        </filter>
        <filter-mapping>
            <filter-name>FederationFilter</filter-name>
            <url-pattern>/*</url-pattern>
        </filter-mapping> 

12. 로그인 페이지를 만들려면 **sample** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **New**를 클릭한 후 **JSP File**을 클릭합니다.

13. **New JSP File** 대화 상자에서 새 파일의 경로를 *sample/src/main/webapp*으로 변경합니다. 그런 다음 파일의 이름을 **login.jsp**로 지정하고 **Finish**를 클릭합니다.

14. 새 **login.jsp** 파일이 자동으로 열립니다. 자동으로 생성된 코드를 다음으로 바꾼 후 파일을 저장합니다.

        <%@ page language="java" contentType="text/html; charset=ISO-8859-1"  pageEncoding="ISO-8859-1"%>
        <%@ page import="com.microsoft.samples.federation.*"%>
        <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
        <html>
        <head>
            <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1"> 
            <title>Login Page</title>
        </head>
        <body>
            <h3>Login Page</h3>
            <a href="<%=FederatedLoginManager.getFederatedLoginUrl(request.getParameter("returnUrl"))%>"><%=FederatedConfiguration.getInstance().getStsFriendlyName()%></a>
        </body>
        </html> 

15. **Project Explorer**에서 **sample** 프로젝트의 **/src/main** 폴더를 확장합니다. **resources** 폴더를 마우스 오른쪽 단추로 클릭하고 **New**를 클릭한 후 **Other**를 클릭합니다.

16. **New** 대화 상자에서 **JBoss Tools Web** 폴더를 확장하고 **Properties File**을 클릭한 후 **Next**를 클릭합니다.

17. **New File Properties** 대화 상자에서 파일의 이름을 **federation**으로 지정한 후 **Finish**를 클릭합니다.

18. **Project Explorer**에서 **sample** 프로젝트의 **src/main/resources** 폴더를 확장합니다. **federation.properties** 파일을 마우스 오른쪽 단추로 클릭하고 **Open With**를 클릭한 후 **Text Editor**를 클릭합니다.

19. **federation.properties** 파일에 다음 구성 항목을 포함한 후 파일을 저장합니다.

        federation.trustedissuers.issuer=https://accounts.accesscontrol.windows.net/v2/wsfederation
        federation.trustedissuers.thumbprint=qY+Drf20Zz+A4t2we3PebCopoCugO76My+JMVsqNBFc=
        federation.trustedissuers.friendlyname=Fabrikam
        federation.audienceuris=spn:7829c758-2bef-43df-a685-717089474505
        federation.realm=spn:7829c758-2bef-43df-a685-717089474505
        federation.reply=https://localhost:8443/sample/wsfed-saml 

    > [WACOM.NOTE]
    > **audienceuris** 및 **realm** 값은 "spn:"으로 시작되어야 합니다.

20. 이제 새 서블릿을 만들어야 합니다. **sample** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **New**, **Other**, **Servlet**을 차례로 클릭합니다.

21. **Create Servlet** 대화 상자에서 **Class name**을 *FederationServlet*으로 입력하고 **Finish**를 클릭합니다.

22. **FederationServlet.java** 파일이 자동으로 열립니다. 해당 내용을 다음 코드로 바꾼 후 파일을 저장합니다.

        import java.io.IOException;
        import javax.servlet.ServletException;
        import javax.servlet.http.HttpServlet;
        import javax.servlet.http.HttpServletRequest;
        import javax.servlet.http.HttpServletResponse;
        import com.microsoft.samples.federation.FederatedAuthenticationListener;
        import com.microsoft.samples.federation.FederatedLoginManager;
        import com.microsoft.samples.federation.FederatedPrincipal;
        import com.microsoft.samples.federation.FederationException;

        public class FederationServlet extends HttpServlet {
            private static final long serialVersionUID = 1L;

            protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
                String token = request.getParameter("wresult").toString();

                if (token == null) {
                    response.sendError(400, "You were supposed to send a wresult parameter with a token");
                }
                FederatedLoginManager loginManager = FederatedLoginManager.fromRequest(request, new SampleAuthenticationListener());

                try {
                    loginManager.authenticate(token, response);
                } catch (FederationException e) {
                    response.sendError(500, "Oops! and error occurred.");
                }
            }

            private class SampleAuthenticationListener implements FederatedAuthenticationListener {
                @Override
                public void OnAuthenticationSucceed(FederatedPrincipal principal) {
                    // ***
                    // do whatever you want with the principal object that contains the token's claims
                    // ***
                }
            }
        } 

23. **Project Explorer**에서 **src/main/webapp/WEB-INF** 폴더를 확장합니다. **web.xml** 파일을 마우스 오른쪽 단추로 클릭하고 **Open With**를 클릭한 후 **Text Editor**를 클릭합니다.

24. **web.xml** 파일에서 *\<url-pattern\>* 섹션의 **/FederationServlet** 설정을 **/ws-saml**로 바꿉니다. 예를 들면 다음과 같습니다.

        <servlet>
            <description></description>
            <display-name>FederationServlet</display-name>
            <servlet-name>FederationServlet</servlet-name>
            <servlet-class>FederationServlet</servlet-class>
        </servlet>
        <servlet-mapping>
            <servlet-name>FederationServlet</servlet-name>
            <url-pattern>/wsfed-saml</url-pattern>
        </servlet-mapping> 

25. **Project Explorer**에서 **src/main/webapp** 폴더를 확장합니다. **index.jsp** 파일을 마우스 오른쪽 단추로 클릭하고 **Open With**를 클릭한 후 **Text Editor**를 클릭합니다.

26. **index.jsp** 파일에서 기존 코드를 다음 코드로 바꾼 후 파일을 저장합니다.

        <%@ page language="java" contentType="text/html; charset=ISO-8859-1" pageEncoding="ISO-8859-1"%>
        <%@ page import="com.microsoft.samples.federation.*"%>
        <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
        <html>
        <head>
            <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
            <title>Index Page</title>
        </head>
        <body>
            <h3>Welcome <strong><%=FederatedLoginManager.fromRequest(request).getPrincipal().getName()%></strong>!</h3>
            <h2>Claim list:</h2>
            <ul> 
                <% for (Claim claim : FederatedLoginManager.fromRequest(request).getClaims()) { %>
                <li><%= claim.toString()%></li>
                <%  } %>
            </ul>
        </body>
        </html> 

27. **Project Explorer**에서 **src/main/webapp/WEB-INF** 폴더를 확장합니다. **web.xml** 파일을 마우스 오른쪽 단추로 클릭하고 **Open With**를 클릭한 후 **Text Editor**를 클릭합니다.

28. 이제 응용 프로그램에 대해 SSL을 사용하도록 설정합니다. **web.xml** 파일에서 *\<web-app\>* 섹션 내에 다음 *\<security-constraint\>* 섹션을 삽입한 후 파일을 저장합니다.

        <security-constraint>
            <web-resource-collection>
                <web-resource-name>SSL Forwarding</web-resource-name>
                <url-pattern>/*</url-pattern>
                <http-method>POST</http-method>
                <http-method>GET</http-method>
            </web-resource-collection>
            <user-data-constraint>
                <transport-guarantee>CONFIDENTIAL</transport-guarantee>
            </user-data-constraint>
        </security-constraint> 

    > [WACOM.NOTE]
    > 계속하기 전에 JBoss Server가 이미 SSL을 지원하도록 구성되어 있는지 확인하세요.

29. 이제 종단 간 응용 프로그램을 실행할 준비가 되었습니다. **sample** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **Run As**를 클릭한 후 **Run On Server**를 클릭합니다. 이전에 지정한 값을 그대로 사용하고 **Finish**를 클릭합니다.

30. JBoss 브라우저에 로그인 페이지가 열립니다. **Awesome Computers** 링크를 클릭하면 디렉터리 테넌트 자격 증명을 사용하여 로그인할 수 있는 Office 365 ID 공급자 페이지로 리디렉션됩니다(예: <*john.doe@fabrikam.onmicrosoft.com*>).

31. 로그인하고 나면 인증된 사용자로 보안 페이지(**sample/index.jsp**)로 다시 리디렉션됩니다.

## <a name="summary"></a>요약

이 자습서에서는 Azure Active Directory의 Single Sign-On 기능을 사용하여 단일 테넌트 Java 응용 프로그램을 만들고 구성하는 방법을 살펴보았습니다.

  [Azure Active Directory용 Java 샘플 코드]: https://github.com/WindowsAzure/azure-sdk-for-java-samples/tree/master/WAAD.WebSSO.JAVA
  [Java Runtime Environment 1.6]: http://www.oracle.com/technetwork/java/javase/downloads/index.html
  [JBoss Application Server 버전 7.1.1.Final]: http://www.jboss.org/jbossas/downloads/
  [JBoss Developer Studio IDE]: https://devstudio.jboss.com/earlyaccess/
  [Office 365 PowerShell Commandlet]: http://onlinehelp.microsoft.com/ko-KR/office365-enterprises/ff652560.aspx
  [소개]: #introduction
  [1단계: Java 응용 프로그램 만들기]: #createapp
  [2단계: 회사 디렉터리 테넌트에서 응용 프로그램 프로비전]: #provisionapp
  [3단계: 직원 로그인을 위해 WS-Federation을 사용하여 응용 프로그램 보호]: #protectapp
  [요약]: #summary
