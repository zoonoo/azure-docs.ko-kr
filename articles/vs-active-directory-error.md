<properties title="인증 검색 중 오류 발생" pageTitle="인증 검색 중 오류 발생" metaKeywords="" description="" services="active-directory" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="active-directory" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/8/2014" ms.author="ghogen, kempb"></tags>

### 인증 검색 중 오류 발생

이전 인증 코드를 검색하는 동안 마법사에서 호환되지 않는 인증 유형을 검색했습니다.

##### 무엇을 확인합니까?

마법사에서는 이전 버전의 Visual Studio를 사용하여 구성된 인증 코드의 버전을 감지하려고 합니다. 이 오류가 발생한 경우에는 프로젝트에 호환되지 않는 인증 코드가 있음을 의미합니다. 마법사에서는 이전 버전의 Visual Studio에서 다음과 같은 인증 유형을 감지합니다.

-   Windows 인증
-   개별 사용자 계정
-   조직 계정

MVC 프로젝트에서 Windows 인증을 감지하기 위해 마법사는 사용자의 **web.config** 파일에서 `authentication` 요소를 찾습니다.

<pre class="prettyprint">
    &lt;configuration&gt;
        &lt;system.web&gt;
            &lt;authentication mode=&quot;Windows&quot; /&gt;
        &lt;/system.web&gt;
    &lt;/configuration&gt;
</pre>

Web API 프로젝트에서 Windows 인증을 감지하기 위해 마법사는 사용자 프로젝트의 **.csproj** 파일에서 `IISExpressWindowsAuthentication` 요소를 찾습니다.

<pre class="prettyprint">
    &lt;Project&gt;
        &lt;PropertyGroup&gt;
            &lt;IISExpressWindowsAuthentication&gt;enabled&lt;/IISExpressWindowsAuthentication&gt;
        &lt;/PropertyGroup&gt;
    &lt;/Project&gt;
</pre>

개별 사용자 계정 인증을 감지하기 위해 마법사는 사용자의 **Packages.config** 파일에서 패키지 요소를 찾습니다.

<pre class="prettyprint">
    &lt;packages&gt;
        &lt;package id=&quot;Microsoft.AspNet.Identity.EntityFramework&quot; version=&quot;2.1.0&quot; targetFramework=&quot;net45&quot; /&gt;
    &lt;/packages&gt;
</pre>

조직 계정 인증의 이전 양식을 감지하기 위해 마법사는 **web.config**에서 다음 요소를 찾습니다.

<pre class="prettyprint">
    &lt;configuration*gt;
        &lt;appSettings&gt;
            &lt;add key=&quot;ida:Realm&quot; value=&quot;***&quot; /&gt;
        &lt;/appSettings&gt;
    &lt;/configuration&gt;
</pre>

인증 유형을 변경하려면 호환되지 않는 인증 유형을 제거하고 마법사를 다시 실행하세요.

자세한 내용은 [Azure AD의 인증 시나리오][Azure AD의 인증 시나리오]를 참조하세요.

  [Azure AD의 인증 시나리오]: http://msdn.microsoft.com/library/azure/dn499820.aspx
