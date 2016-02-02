<properties
	pageTitle="Azure 배치 서비스 기본 사항 | Microsoft Azure"
	description="대규모 병렬 및 HPC 워크로드의 경우 Azure 배치 서비스를 사용하는 방법에 대해 알아봅니다."
	services="batch"
	documentationCenter=""
	authors="dlepow"
	manager="timlt"
	editor=""/>

<tags
	ms.service="batch"
	ms.workload="big-compute"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="11/19/2015"
	ms.author="danlep"/>

# Azure 배치의 기본 사항

Azure 배치는 클라우드에서 효율적으로 대규모 병렬 및 HPC(고성능 컴퓨팅) 응용 프로그램을 실행하도록 도와줍니다. 가상 컴퓨터의 관리된 컬렉션에서 실행되는 계산 집약적인 작업을 예약하는 플랫폼 서비스이며 작업의 요구를 충족하도록 계산 리소스의 크기를 조정할 수 있습니다. 배치 서비스를 사용하면 요청 시 또는 일정에 따라 실행하는 Azure 계산 리소스 및 대규모 일괄 처리 작업을 프로그래밍 방식으로 정의하며 HPC 클러스터, 개별 VM, 가상 네트워크 또는 작업 스케줄러를 수동으로 구성하고 관리할 필요가 없습니다.

## 사용 사례

배치는 대용량의 유사 태스크를 실행하여 원하는 결과를 얻을 수 있는 *배치 처리* 또는 *배치 컴퓨팅*을 위한 관리된 서비스입니다. 배치 컴퓨팅은 일정에 따라 또는 요청이 있을 때 금융 서비스에서 엔지니어링에 이르는 필드에서 대량의 데이터를 처리하고, 변환하고, 분석하는 조직의 일반적인 패턴입니다.

배치 작업은 기본적으로 병렬("병렬 처리가 적합한") 응용 프로그램이나 작업에 적합하며 이러한 응용 프로그램이나 작업이 여러 컴퓨터에서 병렬 작업으로 실행되도록 합니다. 그림 1을 참조하세요.

![병렬 태스크][parallel]

**그림 1. 여러 컴퓨터에서 실행되는 병렬 태스크**

예를 들면 다음과 같습니다.

* 재무 위험 모델링
* 이미지 렌더링 및 이미지 처리
* 미디어 인코딩 및 트랜스코딩
* 유전자 서열 분석
* 소프트웨어 테스트

또한 배치는 끝에 감소 단계가 있는 병렬 계산 및 메시지 전달 인터페이스(MPI) 응용 프로그램 등 보다 복잡한 HPC 작업을 수행할 수 있습니다.

>[AZURE.NOTE] 이 때에 배치는 Windows Server 기반 가상 컴퓨터에서 실행하는 워크로드를 지원합니다.

Azure에서 다른 HPC 솔루션 옵션과 배치의 비교는 [배치 및 HPC 솔루션](batch-hpc-solutions.md)을 참조하세요.

## 배치를 사용하는 개발

계산 노드의 풀을 만들고 관리하며 실행할 작업 및 태스크를 예약하려면 배치 API를 사용하여 개발합니다. 요구에 따라, 일정에 따라 또는 [Azure 데이터 팩터리](https://azure.microsoft.com/documentation/services/data-factory/)와 같은 도구로 관리하는 더 큰 워크플로의 일부로서 작업 및 태스크를 실행하기 위해 클라이언트 앱 또는 프런트 엔드를 작성합니다.

배치 개념에 대한 자세한 사항은 [Azure 배치 기능 개요](batch-api-basics.md)를 참조하세요.

### 필요한 계정

+ **Azure 계정 및 구독** - 계정이 없는 경우 [MSDN 구독자 혜택](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)을 활성화하거나 [무료 평가판](https://azure.microsoft.com/pricing/free-trial/)을 등록할 수 있습니다.

+ **배치 계정** - 배치 API를 호출할 때 배치 계정 및 선택키의 이름 및 URL를 자격 증명으로 사용합니다. 배치 계정과 연관된 계산 노드, 풀, 작업 및 태스크와 같은 모든 배치 리소스입니다. 배치 계정을 만들고 계정에 대한 액세스 키를 관리하는 방법은 [Azure 포털](batch-account-create-portal.md)을 사용하는 것입니다.

+ **저장소 계정** - 대부분의 배치 시나리오에서는 데이터 입력 및 출력과 계산 노드에서 실행되는 스크립트 또는 실행 파일을 저장할 Azure 저장소 계정이 필요합니다. 저장소 계정을 만들려면 [Azure 저장소 계정 정보](../storage/storage-create-storage-account.md)를 참조하세요.

### 배치 개발 라이브러리 및 도구

Visual Studio와 함께 이러한 .NET 라이브러리 및 도구를 사용하여 Azure 배치 응용 프로그램을 개발하고 관리합니다.

+ [배치 클라이언트 .NET 라이브러리](http://www.nuget.org/packages/Azure.Batch/)(NuGet) - 배치 서비스를 사용하여 작업을 실행하기 위한 클라이언트 응용 프로그램을 개발합니다.
+ [배치 관리 .NET 라이브러리](http://www.nuget.org/packages/Microsoft.Azure.Management.Batch/)(NuGet) - 배치 계정을 관리합니다.
+ [배치 탐색기](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer)(GitHub) - 배치 계정 내에 작업 및 태스크, 계산 노드 및 풀, 계산 노드의 파일을 비롯한 리소스를 찾아보고, 액세스 및 업데이트하기 위한 GUI 응용 프로그램을 빌드합니다. [블로그 게시물](http://blogs.technet.com/b/windowshpc/archive/2015/01/20/azure-batch-explorer-sample-walkthrough.aspx)을 참조하세요.


## 시나리오: 병렬 워크로드 규모 확장

배치 API가 있는 일반적인 시나리오는 기본적으로 최대 수천 개의 계산 코어를 제공하는 계산 노드 풀에서 이미지를 렌더링하는 등의 병렬 작업을 확장하는 작업을 포함합니다.

그림 2는 배치를 사용하여 병렬 워크로드를 실행하는 배치 .NET 클라이언트 응용 프로그램을 사용하는 워크플로를 보여줍니다.


![작업 항목 워크플로][work_item_workflow]

**그림 2. 배치에서 병렬 작업 규모 확장**

1.	작업에 필요한 입력 파일(예: 소스 데이터 또는 이미지)을 Azure 저장소 계정에 업로드합니다. 배치 서비스는 작업 태스크가 실행될 때 계산 노드에 파일을 로드합니다.

2.	저장소 계정에 계산 노드에서 실행할 프로그램 파일 또는 스크립트를 업로드합니다. 이진 파일 및 해당 종속 어셈블리를 포함할 수 있습니다. 또한 배치 서비스는 작업 태스크가 실행될 때 계산 노드에 이러한 파일을 로드합니다.

3.	풀 크기와 실행되는 OS와 같은 속성을 지정하여 프로그래밍 방식으로 배치 계정에서 배치 계산 노드를 만듭니다. 또한 워크로드에 대응하여 풀에서 노드의 수를 [확장하거나 축소](batch-automatic-scaling.md)하는 방법을 정의할 수 있습니다. 태스크가 실행될 때 이 풀에서 노드가 할당됩니다.

4.	프로그래밍 방식으로 배치 작업을 정의하여 노드 풀에서 워크로드를 실행합니다.

5.	태스크를 작업에 추가합니다. 각 태스크는 사용자가 업로드한 프로그램을 사용하여 업로드된 파일의 정보를 처리합니다. 워크로드에 따라 각 계산 노드에서 [동시에 여러 작업](batch-parallel-node-tasks.md)을 실행하여 리소스 사용량을 최대화합니다. 또한 배치는 특수한 [작업 준비 및 완성 작업](batch-job-prep-release.md)을 지원하여 예약된 작업이 실행되기 전에 계산 노드를 준비하고 나중에 정리합니다.

6.	배치 워크로드를 실행하고 진행률 및 결과를 모니터링합니다. 응용 프로그램이 HTTPS를 통해 배치 서비스와 통신합니다. 많은 수의 풀, 작업 및 태스크를 모니터링할 때 응용 프로그램 성능을 향상시키려면 [효율적인 쿼리 기술](batch-efficient-list-queries.md) 배치 서비스를 활용합니다.






## 다음 단계

* [배치 클라이언트 .NET 라이브러리](batch-dotnet-get-started.md)를 사용하여 첫 번째 응용 프로그램 개발 시작
* [GitHub](https://github.com/Azure/azure-batch-samples)에서 배치 코드 샘플 찾아보기

[parallel]: ./media/batch-technical-overview/parallel.png
[work_item_workflow]: ./media/batch-technical-overview/work_item_workflow.png

<!---HONumber=AcomDC_0128_2016-->