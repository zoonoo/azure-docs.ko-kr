<properties 
	pageTitle="Azure 배치 개발 라이브러리 및 도구" 
	description="Azure 배치 및 배치 앱 응용 프로그램을 개발하는 데 필요한 라이브러리 및 도구를 다운로드하세요." 
	services="batch" 
	documentationCenter="" 
	authors="dlepow" 
	manager="timlt"
	editor="yidingz"/>

<tags 
	ms.service="batch" 
	ms.workload="big-compute" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/27/2015" 
	ms.author="danlep"/>


# Azure 배치 개발 라이브러리 및 도구 
<p> Azure 배치 및 배치 앱 응용 프로그램을 개발하려면 다음 라이브러리 및 도구를 다운로드하세요.

## 배치

+ [.NET용 배치 클라이언트 라이브러리](http://www.nuget.org/packages/Azure.Batch/)(영문)(NuGet) - 배치 서비스를 사용하여 작업을 실행하기 위한 클라이언트 응용 프로그램을 개발합니다.
+ [배치 관리 라이브러리](http://www.nuget.org/packages/Microsoft.Azure.Management.Batch/)(영문)(NuGet) - 배치 서비스 계정을 관리합니다.
+ [배치 탐색기](https://code.msdn.microsoft.com/windowsazure/Azure-Batch-Explorer-c1d37768)(영문)(MSDN) - 배치 계정 내에서 작업 및 태스크, TVM(태스크용 가상 컴퓨터) 및 풀, TVM의 파일을 비롯한 주요 요소를 찾아보고, 액세스 및 업데이트하기 위한 GUI 응용 프로그램 및 샘플입니다.

> [AZURE.TIP]배치 탐색기는 배치를 처음 사용하거나 배치 작업을 모니터링하고 문제를 해결하려는 경우에 유용한 도구입니다. 코드 샘플이기 때문에 소스 코드에서 배치 .NET API를 사용하여 기능이 구현된 방식을 보여 줍니다. [블로그 게시물](http://blogs.technet.com/b/windowshpc/archive/2015/01/20/azure-batch-explorer-sample-walkthrough.aspx)을 참조하세요.

<p>
## 배치 앱

+ [배치 앱 클라우드 SDK](http://www.nuget.org/packages/Microsoft.Azure.Batch.Apps.Cloud/)(영문)(NuGet) - 응용 프로그램이 작업을 배치 서비스로 오프로드할 수 있습니다.
+ [배치 앱 Visual Studio 확장](https://visualstudiogallery.msdn.microsoft.com/8b294850-a0a5-43b0-acde-57a07f17826a)(영문)(Visual Studio 갤러리) - 배치 앱 클라우드 SDK를 사용하여 Visual Studio에서 응용 프로그램을 클라우드에서 사용할 수 있도록 설정합니다.
+ [배치 앱 클라이언트 SDK](http://www.nuget.org/packages/Microsoft.Azure.Batch.Apps/)(영문)(NuGet) - 작업을 배치 서비스로 오프로드하도록 설정된 응용 프로그램을 조작합니다.
+ [배치 앱 Python 클라이언트](https://github.com/Azure/azure-batch-apps-python)(영문)(GitHub) - 배치 앱 서비스에서 설정된 응용 프로그램을 조작하기 위한 Python 클라이언트 모듈입니다.
+ [배치 앱 Blender 샘플](https://github.com/Azure/azure-batch-apps-blender)(영문)(GitHub) - 배치 앱 SDK 및 Python 클라이언트를 사용하여 클라우드 기반 렌더링 플랫폼을 설정하는 Blender 오픈 소스 렌더링 소프트웨어의 추가 기능입니다.


## 추가 리소스

+ [코드 샘플](https://code.msdn.microsoft.com/site/search?f[0].Type=Topic&f[0].Value=Azure%20Batch&f[0].Text=Azure%20Batch)(MSDN)
+ [Azure 배치 포럼](https://social.msdn.microsoft.com/forums/azure/home?forum=azurebatch)
+ [.NET용 Azure 배치 라이브러리 시작](batch-dotnet-get-started.md)(영문)  

<!--Anchors-->
[Batch]: #batch
[Batch Apps]: #batch-apps
[Additional resources]: #additional-resources

<!---HONumber=July15_HO3-->