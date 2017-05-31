---
title: "Azure AD v2 Windows 데스크톱 시작 - 설정 | Microsoft Docs"
description: "Windows Desktop .NET(XAML) 응용 프로그램이 Azure Active Directory v2 끝점으로 보호되는 액세스 토큰을 필요로 하는 API를 호출하는 방식"
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mbaldwin
editor: 
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/09/2017
ms.author: andret
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 8c9d5482a83f92ac406c228b46df18d03dcf15d3
ms.contentlocale: ko-kr


---

## <a name="set-up-your-project"></a>프로젝트 설정

이 섹션에서는 토큰이 필요한 Web API를 쿼리할 수 있도록 Windows Desktop .NET 응용 프로그램(XAML)을 *Microsoft에 로그인*과 통합하는 방식을 설명하기 위해 새 프로젝트를 만드는 방법에 대한 단계별 지침을 제공합니다.

이 가이드에서 만든 응용 프로그램은 화면에 결과를 표시하기 위한 단추와 로그아웃 단추를 노출시킵니다.

> 이 샘플의 Visual Studio 프로젝트를 다운로드하고 싶으세요? [프로젝트를 다운로드](https://github.com/Azure-Samples/active-directory-dotnet-desktop-msgraph-v2/archive/master.zip) 하면 실행 전 코드 샘플을 구성하는 [구성](#create-an-application-express) 단계를 건너뛸 수 있습니다.


### <a name="create-your-application"></a>응용 프로그램 만들기
1. Visual Studio에서: `File` > `New` > `Project`<br/>
2. [템플릿]에서 `Visual C#`을 선택합니다.
3. `WPF App`(또는 [WPF 응용 프로그램], Visual Studio 버전에 따라 다름)을 선택합니다.

## <a name="add-the-microsoft-authentication-library-msal-to-your-project"></a>프로젝트에 MSAL(Microsoft 인증 라이브러리) 추가
1. Visual Studio에서: `Tools` > `Nuget Package Manager` > `Package Manager Console`
2. 패키지 관리자 콘솔 창에서 다음 내용을 복사/붙여넣습니다.

```powershell
Install-Package Microsoft.Identity.Client -Pre
```

> 위의 패키지는 MSAL(Microsoft 인증 라이브러리)을 설치합니다. MSAL은 Azure Active Directory v2로 보호되는 API에 액세스하는 데 사용되는 사용자 토큰의 획득, 캐싱 및 새로 고침을 처리합니다.

## <a name="add-the-code-to-initialize-msal"></a>MSAL을 초기화하는 코드 추가
이 단계를 수행하면 MSAL 라이브러리와의 상호 작용을 처리하는 클래스를 만들 수 있습니다(예: 토큰 처리).

1. `App.xaml.cs` 파일을 열고 클래스에 MSAL 라이브러리에 대한 참조를 추가합니다.

```csharp
using Microsoft.Identity.Client;
```
<!-- Workaround for Docs conversion bug -->
<ol start="2">
<li>
앱 클래스를 다음으로 업데이트합니다.
</li>
</ol>

```csharp
public partial class App : Application
{
    //Below is the clientId of your app registration. 
    //You have to replace the below with the Application Id for your app registration
    private static string ClientId = "your_client_id_here";

    public static PublicClientApplication PublicClientApp = new PublicClientApplication(ClientId);

}
```

## <a name="create-your-applications-ui"></a>응용 프로그램 UI 만들기
아래 섹션에서는 응용 프로그램에서 Microsoft Graph와 같은 보호되는 백 엔드 서버를 쿼리하는 방법을 보여 줍니다. MainWindow.xaml 파일은 프로젝트 템플릿의 일부로 자동으로 생성되어야 합니다. 이 파일을 열고 아래 지침을 따릅니다.

1.    다음으로 응용 프로그램의 `<Grid>`를 바꿉니다.

```xml
<Grid>
    <StackPanel Background="Azure">
        <StackPanel Orientation="Horizontal" HorizontalAlignment="Right">
            <Button x:Name="CallGraphButton" Content="Call Microsoft Graph API" HorizontalAlignment="Right" Padding="5" Click="CallGraphButton_Click" Margin="5" FontFamily="Segoe Ui"/>
            <Button x:Name="SignOutButton" Content="Sign-Out" HorizontalAlignment="Right" Padding="5" Click="SignOutButton_Click" Margin="5" Visibility="Collapsed" FontFamily="Segoe Ui"/>
        </StackPanel>
        <Label Content="API Call Results" Margin="0,0,0,-5" FontFamily="Segoe Ui" />
        <TextBox x:Name="ResultText" TextWrapping="Wrap" MinHeight="120" Margin="5" FontFamily="Segoe Ui"/>
        <Label Content="Token Info" Margin="0,0,0,-5" FontFamily="Segoe Ui" />
        <TextBox x:Name="TokenInfoText" TextWrapping="Wrap" MinHeight="70" Margin="5" FontFamily="Segoe Ui"/>
    </StackPanel>
</Grid>
```

