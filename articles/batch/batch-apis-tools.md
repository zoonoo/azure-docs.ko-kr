---
title: "Azure Batch API 및 도구를 사용하여 대규모 병렬 처리 솔루션 개발 | Microsoft Docs"
description: "Azure Batch 서비스를 사용하여 솔루션을 개발하는 데 사용할 수 있는 API 및 도구에 대해 알아봅니다."
services: batch
documentationcenter: 
author: tamram
manager: timlt
editor: 
ms.assetid: 93e37d44-7585-495e-8491-312ed584ab79
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/08/2017
ms.author: tamram
translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: 405cb202e2b6da0dd24e6f7b8ed55ce8c56e0fe1
ms.lasthandoff: 03/31/2017

---


# <a name="overview-of-batch-apis-and-tools"></a>Batch API 및 도구 개요

Azure 배치를 통한 병렬 워크로드 처리는 일반적으로 [배치 API](#batch-development-apis)중 하나를 사용하여 프로그래밍 방식으로 수행됩니다. 클라이언트 응용 프로그램 또는 서비스는 배치 API를 통해 배치 서비스와 통신할 수 있습니다. 배치 API를 사용하면 가상 컴퓨터 또는 클라우드 서비스 중 하나인 계산 노드의 풀을 만들고 관리할 수 있습니다. 그런 다음 해당 노드에서 작업과 태스크를 실행하도록 예약할 수 있습니다. 

조직의 대규모 워크로드를 효율적으로 처리하거나 고객에게 서비스 프런트 엔드를 제공할 수 있으므로 요청 시 또는 일정에 따라 작업 및 태스크를 단일, 수백 또는 수천 개의 노드에서 실행할 수 있습니다. 또한 [Azure Data Factory](../data-factory/data-factory-data-processing-using-batch.md?toc=%2fazure%2fbatch%2ftoc.json)와 같은 도구에서 관리하는 대규모 워크플로의 일부로 Azure 배치를 사용할 수도 있습니다.

> [!TIP]
> 배치 API에서 제공하는 기능을 좀더 자세히 알아볼 준비가 되면 [개발자를 위한 배치 기능 개요](batch-api-basics.md)를 확인하세요.
> 
> 

## <a name="azure-accounts-youll-need"></a>필요한 Azure 계정
배치 솔루션을 개발할 경우 Microsoft Azure에서는 다음 계정을 사용합니다.

* **Azure 계정 및 구독** - Azure 구독이 없는 경우 [MSDN 구독자 혜택][msdn_benefits]을 활성화하거나 [무료 Azure 계정][free_account]을 등록할 수 있습니다. 계정을 만들면 기본 구독이 생성됩니다.
* **배치 계정** - 풀, 계산 노드, 작업 및 태스크를 포함하여 Azure 배치 리소스는 Azure 배치 계정과 연결됩니다. 응용 프로그램에서 배치 서비스를 요청할 때는 Azure 배치 계정 이름, 계정의 URL 및 액세스 키를 사용하여 요청을 인증합니다. Azure 포털에서 [Azure 배치 계정을 만들](batch-account-create-portal.md) 수 있습니다.
* **저장소 계정** - 배치는 [Azure Storage][azure_storage]에 있는 파일에 대한 작업을 기본적으로 지원합니다. 거의 모든 배치 시나리오에서는 Azure Blob 저장소를 사용하여 태스크에서 실행하는 프로그램 및 프로그램에서 처리하는 데이터를 준비하고, 생성되는 출력 데이터를 저장합니다. 저장소 계정을 만들려면 [Azure 저장소 계정 정보](../storage/storage-create-storage-account.md)를 참조하세요.

## <a name="batch-development-apis"></a>배치 개발 API
응용 프로그램과 서비스는 직접 REST API 호출을 실행하거나 다음 클라이언트 라이브러리 중 하나 이상을 사용하여 Azure 배치 워크로드를 실행하고 관리할 수 있습니다.

| API | API 참조 | 다운로드 | 자습서 | 코드 샘플 | 자세한 정보 |
| --- | --- | --- | --- | --- | --- |
| **Batch REST** |[MSDN][batch_rest] |해당 없음 |- |- | [지원되는 버전](https://docs.microsoft.com/rest/api/batchservice/batch-service-rest-api-versioning) |
| **Batch .NET** |[docs.microsoft.com][api_net] |[NuGet ][api_net_nuget] |[자습서](batch-dotnet-get-started.md) |[GitHub][api_sample_net] | [릴리스 정보](https://github.com/Azure/azure-sdk-for-net/blob/AutoRest/src/Batch/Client/changelog.md) |
| **배치 Python** |[readthedocs.io][api_python] |[PyPI][api_python_pypi] |[자습서](batch-python-tutorial.md)|[GitHub][api_sample_python] | [추가 정보](https://github.com/Azure/azure-sdk-for-python/blob/master/doc/batch.rst) |
| **Batch Node.js** |[github.io][api_nodejs] |[npm][api_nodejs_npm] |- |- | [추가 정보](https://github.com/Azure/azure-sdk-for-node/tree/master/lib/services/batch) |
| **배치 Java**(미리 보기) |[github.io][api_java] |[Maven][api_java_jar] |- |[추가 정보][api_sample_java] | [추가 정보](https://github.com/Azure/azure-batch-sdk-for-java)|

## <a name="batch-command-line-tools"></a>Batch 명령줄 도구

개발 API에서 제공하는 기능은 다음과 같은 명령줄 도구도 사용할 수 있습니다. 

* [배치 PowerShell cmdlets][batch_ps]: [Azure PowerShell](/powershell/azureps-cmdlets-docs) 모듈의 Azure 배치 cmdlet을 사용하여 PowerShell로 배치 리소스를 관리할 수 있습니다.
* [Azure CLI](/cli/azure/overview): Azure CLI(Azure 명령줄 인터페이스)는 배치를 포함하여 여러 Azure 서비스와 상호 작용하기 위한 셸 명령을 제공하는 크로스 플랫폼 도구 집합입니다.

## <a name="batch-resource-management"></a>배치 리소스 관리

배치용 Azure Resource Manager API는 배치 계정에 대한 프로그래밍 방식 액세스를 제공합니다. 이러한 API를 사용하면 배치 계정, 할당량 및 응용 프로그램 패키지를 프로그래밍 방식으로 관리할 수 있습니다.  

| API | API 참조 | 다운로드 | 자습서 | 코드 샘플 |
| --- | --- | --- | --- | --- |
| **배치 Resource Manager REST** |[docs.microsoft.com][api_rest_mgmt] |해당 없음 |- |[GitHub](https://github.com/Azure-Samples/batch-dotnet-manage-batch-accounts) |
| **배치 Resource Manager .NET** |[docs.microsoft.com][api_net_mgmt] |[NuGet ][api_net_mgmt_nuget] | [자습서](batch-management-dotnet.md) |[GitHub][api_sample_net] |


## <a name="batch-tools"></a>배치 도구
배치를 사용하여 솔루션을 구축해야 하는 것은 아니지만 배치 응용 프로그램 및 서비스를 구축 및 디버깅하는 동안 사용할 가치 있는 도구는 다음과 같습니다.

* [Azure Portal][portal]: Azure Portal의 배치 블레이드에서 배치 풀, 작업 및 태스크를 만들고 모니터링하며 삭제합니다. 작업을 실행하는 동안 해당하는 리소스 풀 및 다른 리소스 풀에 대한 상태 정보를 보고 풀에 있는 계산 노드에서 파일을 다운로드할 수 있습니다(예: 문제를 해결하는 동안 실패한 작업의 `stderr.txt` 를 다운로드함). 또한 계산 노드에 로그인하는 데 사용할 수 있는 RDP(원격 데스크톱) 파일을 다운로드할 수 있습니다.
* [Azure 배치 탐색기][batch_explorer]: 배치 탐색기는 독립 실행형 WPF(Windows Presentation Foundation) 클라이언트 응용 프로그램에서 Azure Portal과 유사한 배치 리소스 관리 기능을 제공합니다. [GitHub][github_samples]에서 사용할 수 있는 배치 .NET 샘플 응용 프로그램 중 하나를 Visual Studio 2015 이상을 사용하여 빌드하고 배치 솔루션을 개발하고 디버깅하는 배치 계정에서 리소스를 검색하고 관리하는 데 사용할 수 있습니다. 작업, 풀 및 태스크 세부 정보를 보고 계산 노드에서 파일을 다운로드하며 배치 탐색기로 다운로드할 수 있는 RDP(원격 데스크톱) 파일을 사용하여 원격으로 노드에 연결합니다.
* [Microsoft Azure Storage 탐색기][storage_explorer]: 엄격히 말해 Azure 배치 도구는 아니지만 저장소 탐색기는 배치 솔루션을 개발 및 디버깅하는 동안 유용할 수 있는 또 다른 도구입니다.

## <a name="next-steps"></a>다음 단계

* 배치를 사용하려는 사용자를 위한 중요한 정보는 [개발자를 위한 배치 기능 개요](batch-api-basics.md)를 참고합니다. 문서에는 배치 응용 프로그램을 빌드하는 동안 사용할 수 있는 풀, 노드, 작업 및 태스크와 같은 배치 서비스 리소스 및 여러 API 기능에 대한 자세한 내용이 포함됩니다.
* [.NET용 Azure 배치 라이브러리 시작](batch-dotnet-get-started.md) 에서는 일반적인 배치 워크플로를 사용하여 간단한 워크로드를 실행하는 데 C# 및 배치 .NET 라이브러리를 사용하는 방법을 알아봅니다. 이 문서는 배치 서비스 사용 방법을 학습하는 과정의 첫 단계입니다. 또한 [Python 버전](batch-python-tutorial.md) 자습서도 있습니다.
* [GitHub의 코드 샘플][github_samples]을 다운로드하여 샘플 워크로드를 예약하고 처리하기 위해 C# 및 Python가 배치와 상호 작용하는 방법을 확인합니다.
* 배치로 작업할 때 사용할 수 있는 리소스를 알아보려면 [배치 학습 경로][learning_path]를 확인하세요.


[azure_storage]: https://azure.microsoft.com/services/storage/
[api_java]: http://azure.github.io/azure-sdk-for-java/
[api_java_jar]: http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-batch%22
[api_net]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_nuget]: https://www.nuget.org/packages/Azure.Batch/
[api_rest_mgmt]: https://docs.microsoft.com/\rest/api/batchmanagement/
[api_net_mgmt]: https://msdn.microsoft.com/library/azure/mt463120.aspx
[api_net_mgmt_nuget]: https://www.nuget.org/packages/Microsoft.Azure.Management.Batch/
[api_nodejs]: http://azure.github.io/azure-sdk-for-node/azure-batch/latest/
[api_nodejs_npm]: https://www.npmjs.com/package/azure-batch
[api_python]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.html
[api_python_pypi]: https://pypi.python.org/pypi/azure-batch
[api_sample_net]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp
[api_sample_python]: https://github.com/Azure/azure-batch-samples/tree/master/Python/Batch
[api_sample_java]: https://github.com/Azure/azure-batch-samples/tree/master/Java/
[batch_ps]: /powershell/resourcemanager/azurerm.batch/v2.7.0/azurerm.batch
[batch_rest]: https://msdn.microsoft.com/library/azure/Dn820158.aspx
[free_account]: https://azure.microsoft.com/free/
[github_samples]: https://github.com/Azure/azure-batch-samples
[learning_path]: https://azure.microsoft.com/documentation/learning-paths/batch/
[msdn_benefits]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[batch_explorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[storage_explorer]: http://storageexplorer.com/
[portal]: https://portal.azure.com

