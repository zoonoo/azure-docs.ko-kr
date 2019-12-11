---
title: .NET MVC 프로젝트에서 Azure AD 시작 | Microsoft
description: Visual Studio 연결 서비스를 사용 하 여 Azure AD에 연결 하거나 만든 후 .NET MVC 프로젝트에서 Azure Active Directory 사용을 시작 하는 방법
author: ghogen
manager: jillfra
ms.assetid: 1c8b6a58-5144-4965-a905-625b9ee7b22b
ms.prod: visual-studio-windows
ms.technology: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 03/12/2018
ms.author: ghogen
ms.custom: aaddev, vs-azure
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6a603f37850cba13328889fcd120851f3c97c8c3
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74966540"
---
# <a name="getting-started-with-azure-active-directory-aspnet-mvc-projects"></a>Azure Active Directory 시작 (ASP.NET MVC 프로젝트)

> [!div class="op_single_selector"]
> - [Első lépések](vs-active-directory-dotnet-getting-started.md)
> - [무슨 일이 있었나요](vs-active-directory-dotnet-what-happened.md)

이 문서에서는 Visual Studio의 **project > 연결된 서비스** 명령을 통해 ASP.NET MVC 프로젝트에 Active Directory를 추가한 후의 추가 지침을 제공 합니다. 이미 프로젝트에 서비스를 추가 하지 않은 경우 언제 든 지이 작업을 수행할 수 있습니다.

연결 된 서비스를 추가할 때 프로젝트에 대 한 변경 내용은 [내 MVC 프로젝트](vs-active-directory-dotnet-what-happened.md) 의 변경 내용을 참조 하세요.

## <a name="requiring-authentication-to-access-controllers"></a>컨트롤러에 대 한 액세스 인증 요구

프로젝트의 모든 컨트롤러는 `[Authorize]` 특성으로 표시 되었습니다. 이 특성을 사용 하려면 이러한 컨트롤러에 액세스 하기 전에 사용자를 인증 해야 합니다. 컨트롤러에 익명으로 액세스할 수 있도록 하려면 컨트롤러에서이 특성을 제거 합니다. 더 세부적인 수준에서 권한을 설정 하려면 컨트롤러 클래스에 적용 하는 대신 권한 부여가 필요한 각 메서드에 특성을 적용 합니다.

## <a name="adding-signin--signout-controls"></a>SignIn/SignOut 컨트롤 추가

보기에 SignIn/SignOut 컨트롤을 추가 하려면 `_LoginPartial.cshtml` 부분 보기를 사용 하 여 보기 중 하나에 기능을 추가할 수 있습니다. 다음은 표준 `_Layout.cshtml` 뷰에 추가 된 기능의 예입니다. (클래스 탐색 모음-축소)를 사용 하 여 div의 마지막 요소를 확인 합니다.

```html
<!DOCTYPE html>
 <html>
 <head>
     <meta charset="utf-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>@ViewBag.Title - My ASP.NET Application</title>
    @Styles.Render("~/Content/css")
    @Scripts.Render("~/bundles/modernizr")
</head>
<body>
    <div class="navbar navbar-inverse navbar-fixed-top">
        <div class="container">
            <div class="navbar-header">
                <button type="button" class="navbar-toggle" data-toggle="collapse" data-target=".navbar-collapse">
                    <span class="icon-bar"></span>
                    <span class="icon-bar"></span>
                    <span class="icon-bar"></span>
                </button>
                @Html.ActionLink("Application name", "Index", "Home", new { area = "" }, new { @class = "navbar-brand" })
            </div>
            <div class="navbar-collapse collapse">
                <ul class="nav navbar-nav">
                    <li>@Html.ActionLink("Home", "Index", "Home")</li>
                    <li>@Html.ActionLink("About", "About", "Home")</li>
                    <li>@Html.ActionLink("Contact", "Contact", "Home")</li>
                </ul>
                @Html.Partial("_LoginPartial")
            </div>
        </div>
    </div>
    <div class="container body-content">
        @RenderBody() 
        <hr />
        <footer>
            <p>&copy; @DateTime.Now.Year - My ASP.NET Application</p>
        </footer>
    </div>
    @Scripts.Render("~/bundles/jquery")
    @Scripts.Render("~/bundles/bootstrap")
    @RenderSection("scripts", required: false)
</body>
</html>
```

## <a name="next-steps"></a>Következő lépések

- [Azure Active Directory에 대 한 인증 시나리오](authentication-scenarios.md)
- [ASP.NET 웹 앱에 Microsoft에 로그인 추가](quickstart-v1-aspnet-webapp.md)
