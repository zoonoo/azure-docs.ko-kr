<properties title="Active Directory Authentication 시작" pageTitle="" metaKeywords="Azure, Getting Started, Active Directory" description="" services="active-directory" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="active-directory" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/8/2014" ms.author="ghogen, kempb"></tags>

> [AZURE.SELECTOR]
>
> -   [시작하기][시작하기]
> -   [변경된 내용][변경된 내용]

## Azure Active Directory 시작(.NET 프로젝트)

##### 컨트롤러에 액세스하려면 인증 필요

프로젝트의 모든 컨트롤러는 **Authorize** 특성으로 표시되었습니다. 이 특성으로 인해 사용자가 인증해야만 이러한 컨트롤러에 액세스할 수 있습니다. 컨트롤러에 익명으로 액세스할 수 있도록 하려면 컨트롤러에서 이 특성을 제거하세요. 더 세부적인 수준에서 권한을 설정하려면 특성을 컨트롤러 클래스에 적용하는 대신 인증이 필요한 각 메서드에 적용하세요.

##### SignIn/SignOut 컨트롤 추가

뷰에 SignIn/SignOut 컨트롤을 추가하려면 \*\*\_LoginPartial.cshtml\*\* 부분 뷰를 사용하여 뷰 중 하나에 기능을 추가할 수 있습니다. 다음은 표준 \*\*\_Layout.cshtml\*\* 뷰에 추가한 기능의 예입니다. (navbar-collapse 클래스가 포함된 div의 마지막 요소를 확인):

<pre class="prettyprint">
    &lt;!DOCTYPE html&gt; 
     &lt;html&gt; 
     &lt;head&gt; 
         &lt;meta charset=&quot;utf-8&quot; /&gt; 
        &lt;meta name=&quot;viewport&quot; content=&quot;width=device-width, initial-scale=1.0&quot;&gt; 
        &lt;title&gt;@ViewBag.Title - My ASP.NET Application&lt;/title&gt; 
        @Styles.Render(&quot;~/Content/css&quot;) 
        @Scripts.Render(&quot;~/bundles/modernizr&quot;) 
    &lt;/head&gt; 
    &lt;body&gt; 
        &lt;div class=&quot;navbar navbar-inverse navbar-fixed-top&quot;&gt; 
            &lt;div class=&quot;container&quot;&gt; 
                &lt;div class=&quot;navbar-header&quot;&gt; 
                    &lt;button type=&quot;button&quot; class=&quot;navbar-toggle&quot; data-toggle=&quot;collapse&quot; data-target=&quot;.navbar-collapse&quot;&gt; 
                        &lt;span class=&quot;icon-bar&quot;&gt;&lt;/span&gt; 
                        &lt;span class=&quot;icon-bar&quot;&gt;&lt;/span&gt; 
                        &lt;span class=&quot;icon-bar&quot;&gt;&lt;/span&gt; 
                    &lt;/button&gt; 
                    @Html.ActionLink(&quot;Application name&quot;, &quot;Index&quot;, &quot;Home&quot;, new { area = &quot;&quot; }, new { @class = &quot;navbar-brand&quot; }) 
                &lt;/div&gt; 
                &lt;div class=&quot;navbar-collapse collapse&quot;&gt; 
                    &lt;ul class=&quot;nav navbar-nav&quot;&gt; 
                        &lt;li&gt;@Html.ActionLink(&quot;Home&quot;, &quot;Index&quot;, &quot;Home&quot;)&lt;/li&gt; 
                        &lt;li&gt;@Html.ActionLink(&quot;About&quot;, &quot;About&quot;, &quot;Home&quot;)&lt;/li&gt; 
                        &lt;li&gt;@Html.ActionLink(&quot;Contact&quot;, &quot;Contact&quot;, &quot;Home&quot;)&lt;/li&gt; 
                    &lt;/ul&gt; 
                    @Html.Partial(&quot;_LoginPartial&quot;) 
                &lt;/div&gt; 
            &lt;/div&gt; 
        &lt;/div&gt; 
        &lt;div class=&quot;container body-content&quot;&gt; 
            @RenderBody() 
            &lt;hr /&gt; 
            &lt;footer&gt; 
                &lt;p&gt;© @DateTime.Now.Year - My ASP.NET Application&lt;/p&gt; 
            &lt;/footer&gt; 
        &lt;/div&gt; 
        @Scripts.Render(&quot;~/bundles/jquery&quot;) 
        @Scripts.Render(&quot;~/bundles/bootstrap&quot;) 
        @RenderSection(&quot;scripts&quot;, required: false) 
    &lt;/body&gt; 
    &lt;/html&gt;
</pre>

[Azure Active Directory에 대한 자세한 정보][Azure Active Directory에 대한 자세한 정보]

  [시작하기]: /documentation/articles/vs-active-directory-dotnet-getting-started/
  [변경된 내용]: /documentation/articles/vs-active-directory-dotnet-what-happened/
  [Azure Active Directory에 대한 자세한 정보]: http://azure.microsoft.com/services/active-directory/
