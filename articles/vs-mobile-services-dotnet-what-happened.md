<properties title="모바일 서비스 시작" pageTitle="" metaKeywords="Azure, Getting Started, Mobile Services" description="" services="mobile-services" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="mobile-services" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/8/2014" ms.author="ghogen, kempb"></tags>

> [AZURE.SELECTOR]
>
> -   [시작하기][시작하기]
> -   [변경된 내용][변경된 내용]

### <span id="whathappened">내 프로젝트에서 무엇이 변경되었습니까?</span>

##### 참조 추가됨

프로젝트에 Azure Mobile Services NuGet 패키지가 추가되었습니다. 결과로 다음과 같은 .NET 참조가 프로젝트에 추가되었습니다.

-   `Microsoft.WindowsAzure.Mobile`
-   `Microsoft.WindowsAzure.Mobile.Ext`
-   `Newtonsoft.Json`
-   `System.Net.Http.Extensions`
-   `System.Net.Http.Primitives`

##### 모바일 서비스에 대한 연결 문자열 값

App.xaml.cs 파일에 선택한 모바일 서비스의 응용 프로그램 URL 및 응용 프로그램 키를 포함하는 **MobileServiceClient** 개체가 생성되었습니다.

[모바일 서비스에 대한 자세한 정보][모바일 서비스에 대한 자세한 정보]

  [시작하기]: /documentation/articles/vs-mobile-services-dotnet-getting-started/
  [변경된 내용]: /documentation/articles/vs-mobile-services-dotnet-what-happened/
  [모바일 서비스에 대한 자세한 정보]: http://azure.microsoft.com/documentation/services/mobile-services/
