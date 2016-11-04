---
title: Azure Active Directory 및 Visual Studio 연결 서비스 시작(MVC 프로젝트) | Microsoft Docs
description: Visual Studio 연결 서비스를 사용하여 Azure AD를 만들거나 연결한 후에 MVC 프로젝트에 Azure Active Directory를 사용하여 시작하는 방법입니다.
services: active-directory
documentationcenter: ''
author: TomArcher
manager: douge
editor: ''

ms.service: active-directory
ms.workload: web
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 08/15/2016
ms.author: tarcher

---
# Azure Active Directory 및 Visual Studio 연결 서비스 시작(MVC 프로젝트)
> [!div class="op_single_selector"]
> * [시작](vs-active-directory-dotnet-getting-started.md)
> * [변경된 내용](vs-active-directory-dotnet-what-happened.md)
> 
> 

## 컨트롤러에 액세스하려면 인증 필요
프로젝트의 모든 컨트롤러는 **Authorize** 특성으로 표시되었습니다. 이 특성으로 인해 사용자가 인증해야만 이러한 컨트롤러에 액세스할 수 있습니다. 컨트롤러에 익명으로 액세스할 수 있도록 하려면 컨트롤러에서 이 특성을 제거하세요. 더 세부적인 수준에서 권한을 설정하려면 특성을 컨트롤러 클래스에 적용하는 대신 인증이 필요한 각 메서드에 적용하세요.

## SignIn/SignOut 컨트롤 추가
뷰에 SignIn/SignOut 컨트롤을 추가하려면 **\_LoginPartial.cshtml** 부분 뷰를 사용하여 뷰 중 하나에 기능을 추가할 수 있습니다. 다음은 표준 **\_Layout.cshtml** 뷰에 추가한 기능의 예입니다. (navbar-collapse 클래스가 포함된 div의 마지막 요소를 확인):

<pre>
    &lt;!DOCTYPE html> 
     &lt;html> 
     &lt;head> 
         &lt;meta charset="utf-8" /> 
        &lt;meta name="viewport" content="width=device-width, initial-scale=1.0"> 
        &lt;title>@ViewBag.Title - My ASP.NET Application&lt;/title> 
        @Styles.Render("~/Content/css") 
        @Scripts.Render("~/bundles/modernizr") 
    &lt;/head> 
    &lt;body> 
        &lt;div class="navbar navbar-inverse navbar-fixed-top"> 
            &lt;div class="container"> 
                &lt;div class="navbar-header"> 
                    &lt;button type="button" class="navbar-toggle" data-toggle="collapse" data-target=".navbar-collapse"> 
                        &lt;span class="icon-bar">&lt;/span> 
                        &lt;span class="icon-bar">&lt;/span> 
                        &lt;span class="icon-bar">&lt;/span> 
                    &lt;/button> 
                    @Html.ActionLink("Application name", "Index", "Home", new { area = "" }, new { @class = "navbar-brand" }) 
                &lt;/div> 
                &lt;div class="navbar-collapse collapse"> 
                    &lt;ul class="nav navbar-nav"> 
                        &lt;li>@Html.ActionLink("Home", "Index", "Home")&lt;/li> 
                        &lt;li>@Html.ActionLink("About", "About", "Home")&lt;/li> 
                        &lt;li>@Html.ActionLink("Contact", "Contact", "Home")&lt;/li> 
                    &lt;/ul> 
                    <span style="background-color:yellow">@Html.Partial("_LoginPartial")</span> 
                &lt;/div> 
            &lt;/div> 
        &lt;/div> 
        &lt;div class="container body-content"> 
            @RenderBody() 
            &lt;hr /> 
            &lt;footer> 
                &lt;p>&amp;copy; @DateTime.Now.Year - My ASP.NET Application&lt;/p> 
            &lt;/footer> 
        &lt;/div> 
        @Scripts.Render("~/bundles/jquery") 
        @Scripts.Render("~/bundles/bootstrap") 
        @RenderSection("scripts", required: false) 
    &lt;/body> 
    &lt;/html>
</pre>

[Azure Active Directory에 대한 자세한 정보](https://azure.microsoft.com/services/active-directory/)

<!---HONumber=AcomDC_0817_2016-->