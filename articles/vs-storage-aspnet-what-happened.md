<properties title="Azure 저장소 시작" pageTitle="Azure 저장소 시작" metaKeywords="Azure, Getting Started, Storage" description="" services="storage" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="storage" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/10/2014" ms.author="ghogen, kempb"></tags>

> [AZURE.SELECTOR]
>
> -   [시작하기][시작하기]
> -   [변경된 내용][변경된 내용]

### <span id="whathappened">내 프로젝트에서 무엇이 변경되었습니까?</span>

##### 참조 추가됨

Azure 저장소 NuGet 패키지가 Visual Studio 프로젝트에 추가되었습니다.
이 패키지는

-   `Microsoft.Data.Edm`
-   `Microsoft.Data.OData`
-   `Microsoft.Data.Services.Client`
-   `Microsoft.WindowsAzure.Configuration`
-   `Microsoft.WindowsAzure.Storage`
-   `Newtonsoft.Json`
-   `System.Data`
-   `System.Spatial`

##### 추가된 Azure 저장소에 대한 연결 문자열

프로젝트의 web.config 파일에 선택한 저장소 계정의 연결 문자열과 키를 포함하는 요소가 생성되었습니다.

자세한 내용은 [ASP.NET][ASP.NET]을 참조하세요.

  [시작하기]: /documentation/articles/vs-storage-aspnet-getting-started-blobs/
  [변경된 내용]: /documentation/articles/vs-storage-aspnet-what-happened/
  [ASP.NET]: http://www.asp.net
