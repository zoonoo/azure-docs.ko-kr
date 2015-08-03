<properties 
	pageTitle="" 
	description="Azure Active Directory(Web API 프로젝트) 시작 마법사 정보" 
	services="active-directory" 
	documentationCenter="" 
	authors="patshea123" 
	manager="douge" 
	editor="tglee"/>
  
<tags 
	ms.service="active-directory" 
	ms.workload="web" 
	ms.tgt_pltfrm="vs-getting-started" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/06/2015" 
	ms.author="patshea123"/>

# Azure Active Directory 시작(Web API 프로젝트)

> [AZURE.SELECTOR]
> - [Getting Started](vs-active-directory-webapi-getting-started.md)
> - [What Happened](vs-active-directory-webapi-what-happened.md)

#####컨트롤러에 액세스하려면 인증 필요
 
프로젝트의 모든 컨트롤러는 **Authorize** 특성으로 표시되었습니다. 이 특성으로 인해 사용자가 인증해야만 이러한 컨트롤러에서 정의한 API에 액세스할 수 있습니다. 컨트롤러에 익명으로 액세스할 수 있도록 하려면 컨트롤러에서 이 특성을 제거하세요. 더 세부적인 수준에서 권한을 설정하려면 특성을 컨트롤러 클래스에 적용하는 대신 인증이 필요한 각 메서드에 적용하세요.

[Azure Active Directory에 대한 자세한 정보](http://azure.microsoft.com/services/active-directory/)
 

<!---HONumber=July15_HO4-->