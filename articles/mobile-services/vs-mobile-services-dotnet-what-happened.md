<properties 
	pageTitle="" 
	description="Visual Studio에서 Azure 모바일 서비스 .NET 프로젝트의 변경 사항을 설명합니다." 
	services="mobile-services" 
	documentationCenter="" 
	authors="patshea123" 
	manager="douge" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="NA" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="07/02/2015" 
	ms.author="patshea123"/>

# 내 프로젝트에서 무엇이 변경되었습니까?

> [AZURE.SELECTOR]
> - [Getting Started](vs-mobile-services-dotnet-getting-started.md)
> - [What Happened](vs-mobile-services-dotnet-what-happened.md)

###<span id="whathappened">내 프로젝트에서 무엇이 변경되었습니까?</span>

#####참조 추가됨

프로젝트에 Azure Mobile Services NuGet 패키지가 추가되었습니다. 결과로 다음과 같은 .NET 참조가 프로젝트에 추가되었습니다.

- `Microsoft.WindowsAzure.Mobile`
- `Microsoft.WindowsAzure.Mobile.Ext`
- `Newtonsoft.Json`
- `System.Net.Http.Extensions`
- `System.Net.Http.Primitives` 

#####모바일 서비스에 대한 연결 문자열 값

App.xaml.cs 파일에 선택한 모바일 서비스의 응용 프로그램 URL 및 응용 프로그램 키를 포함하는 **MobileServiceClient** 개체가 생성되었습니다.

####모바일 서비스 프로젝트가 추가됨

.NET 모바일 서비스가 연결된 서비스 공급자에서 만들어지면 모바일 서비스 프로젝트가 만들어진 후 솔루션에 추가됩니다.


[모바일 서비스에 대한 자세한 정보](http://azure.microsoft.com/documentation/services/mobile-services/)

<!---HONumber=July15_HO4-->