<properties 
	pageTitle="Azure Active Directory 및 Visual Studio 연결 서비스 시작(WebApi 프로젝트) | Microsoft Azure" 
	description="Visual Studio 연결 서비스를 사용하여 Azure AD를 만들거나 연결한 후에 WebApi 프로젝트에 Azure Active Directory를 사용하여 시작하는 방법입니다." 
  services="active-directory"
	documentationCenter="" 
	authors="TomArcher" 
	manager="douge" 
	editor=""/>
  
<tags 
	ms.service="active-directory" 
	ms.workload="web" 
	ms.tgt_pltfrm="vs-getting-started" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/18/2015" 
	ms.author="tarcher"/>

# Azure Active Directory 및 Visual Studio 연결 서비스 시작(WebApi 프로젝트)

> [AZURE.SELECTOR]
> - [Getting Started](vs-active-directory-webapi-getting-started.md)
> - [What Happened](vs-active-directory-webapi-what-happened.md)

##컨트롤러에 액세스하려면 인증 필요
 
프로젝트의 모든 컨트롤러는 **Authorize** 특성으로 표시되었습니다. 이 특성으로 인해 사용자가 인증해야만 이러한 컨트롤러에서 정의한 API에 액세스할 수 있습니다. 컨트롤러에 익명으로 액세스할 수 있도록 하려면 컨트롤러에서 이 특성을 제거하세요. 더 세부적인 수준에서 권한을 설정하려면 특성을 컨트롤러 클래스에 적용하는 대신 인증이 필요한 각 메서드에 적용하세요.

[Azure Active Directory에 대한 자세한 정보](https://azure.microsoft.com/services/active-directory/)
 

<!---HONumber=AcomDC_0128_2016-->