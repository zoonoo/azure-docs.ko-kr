<properties
	pageTitle="Azure 배치 개발 라이브러리 및 도구 | Microsoft Azure"
	description="Azure 배치 응용 프로그램을 개발하는 데 필요한 라이브러리 및 도구를 다운로드하십시오."
	services="batch"
	documentationCenter=""
	authors="dlepow"
	manager="timlt"
	editor=""/>

<tags
	ms.service="batch"
	ms.workload="big-compute"
	ms.tgt_pltfrm="na"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="07/24/2015"
	ms.author="danlep"/>


# Azure 배치 개발 라이브러리 및 도구
<p> Azure 배치 응용 프로그램을 개발하려면 다음 라이브러리 및 도구를 다운로드하십시오.

## 배치

+ [.NET용 배치 클라이언트 라이브러리](http://www.nuget.org/packages/Azure.Batch/)(영문)(NuGet) - 배치 서비스를 사용하여 작업을 실행하기 위한 클라이언트 응용 프로그램을 개발합니다.
+ [배치 관리 라이브러리](http://www.nuget.org/packages/Microsoft.Azure.Management.Batch/)(영문)(NuGet) - 배치 계정을 관리합니다.
+ [배치 탐색기](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer)(영문)(GitHub) - Batch 계정 내에서 작업 및 태스크, 계산 노드 및 풀, 계산 노드의 파일을 비롯한 주요 요소를 찾아보고, 액세스 및 업데이트하기 위한 GUI 응용 프로그램 및 샘플입니다. [블로그 게시물](http://blogs.technet.com/b/windowshpc/archive/2015/01/20/azure-batch-explorer-sample-walkthrough.aspx)을 참조하세요.


## 배치 앱

+ [배치 앱 클라우드 SDK](http://www.nuget.org/packages/Microsoft.Azure.Batch.Apps.Cloud/1.1.1-preview)(영문)(NuGet) - 응용 프로그램이 작업을 배치 서비스로 오프로드할 수 있습니다.
+ [배치 앱 Visual Studio 확장](https://visualstudiogallery.msdn.microsoft.com/8b294850-a0a5-43b0-acde-57a07f17826a)(영문)(Visual Studio 갤러리) - 배치 앱 클라우드 SDK를 사용하여 Visual Studio에서 응용 프로그램을 클라우드에서 사용할 수 있도록 설정합니다.
+ [배치 앱 클라이언트 SDK](http://www.nuget.org/packages/Microsoft.Azure.Batch.Apps/2.3.0-preview)(영문)(NuGet) - 작업을 배치 서비스로 오프로드하도록 설정된 응용 프로그램을 조작합니다.
+ [배치 앱 Python 클라이언트](https://github.com/Azure/azure-batch-apps-python)(영문)(GitHub) - 배치 앱 서비스에서 설정된 응용 프로그램을 조작하기 위한 Python 클라이언트 모듈입니다.

>[AZURE.IMPORTANT]Azure는 미리 보기로 배치 앱 API만 제공합니다. 테스트 또는 개념 증명 프로젝트 용도로만 API를 개발해야 합니다. 주요 배치 앱 기능은 추후 서비스 릴리스의 배치 API에 통합될 예정입니다.

## 추가 리소스

+ [코드 샘플](https://github.com/Azure/azure-batch-samples)(GitHub)
+ [Azure 배치 포럼](https://social.msdn.microsoft.com/forums/azure/home?forum=azurebatch)

<!--Anchors-->
[Batch]: #batch
[Batch Apps]: #batch-apps
[Additional resources]: #additional-resources

<!---HONumber=August15_HO6-->