---
title: "배치용 Azure CLI 시작 | Microsoft Docs"
description: "Azure CLI에서 Azure Batch 서비스 리소스를 관리하기 위한 Batch 명령에 대한 간단한 소개를 봅니다."
services: batch
documentationcenter: 
author: tamram
manager: timlt
editor: 
ms.assetid: fcd76587-1827-4bc8-a84d-bba1cd980d85
ms.service: batch
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: multiple
ms.workload: big-compute
ms.date: 01/23/2017
ms.author: tamram
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: 698c481e2eff5e0a3b893a0377d9f4cd2f052eb4
ms.lasthandoff: 03/21/2017


---
# <a name="manage-batch-resources-with-azure-cli"></a>Azure CLI를 사용하여 Batch 리소스 관리

교차 플랫폼 Azure CLI(Azure 명령줄 인터페이스)를 사용하면 Linux, Mac 및 Windows 명령 셸에서 풀, 작업 및 태스크 등 배치 계정 및 리소스를 관리할 수 있습니다. Azure CLI을 사용하여 Batch API, Azure Portal, Azure PowerShell cmdlet으로 실행한 많은 동일한 작업을 수행하고 스크립트를 작성할 수 있습니다.

이 문서는 Azure CLI 버전 0.10.5를 기반으로 합니다.

## <a name="prerequisites"></a>선행 조건
* [Azure CLI 설치](../cli-install-nodejs.md)
* [Azure 구독에 Azure CLI 연결](../xplat-cli-connect.md)
* **Resource Manager** 모드로 전환: `azure config mode arm`

> [!TIP]
> 서비스 업데이트 및 향상을 최대한 활용하기 위해 Azure CLI 설치를 자주 업데이트하는 것이 좋습니다.
> 
> 

## <a name="command-help"></a>명령 도움말
명령 이후에 `-h` 을 옵션으로 추가하여 Azure CLI에서 모든 명령에 대한 도움말 텍스트를 표시할 수 있습니다. 다음과 같은 노래들을 선곡할 수 있다.

* `azure` 명령에 대한 도움말을 보려면 `azure -h`을 입력합니다.
* CLI에서 모든 Batch 명령의 목록을 가져오려면 `azure batch -h`을 사용합니다.
* 배치 계정을 만드는 도움말을 보려면 `azure batch account create -h`

확실하지 않은 경우 `-h` 명령줄 옵션을 사용하여 모든 Azure CLI 명령에 대한 도움말을 봅니다.

## <a name="create-a-batch-account"></a>배치 계정 만들기
Usage:

    azure batch account create [options] <name>

예:

    azure batch account create --location "West US"  --resource-group "resgroup001" "batchaccount001"

지정된 매개 변수를 사용하여 새 배치 계정을 만듭니다. 최소한 위치, 리소스 그룹 및 계정 이름을 지정해야 합니다. 리소스 그룹이 아직 없는 경우 `azure group create`을 실행하여 만들고 `--location` 옵션에 Azure 지역(예: "미국 서부") 중 하나를 지정합니다. 다음과 같은 노래들을 선곡할 수 있다.

    azure group create --name "resgroup001" --location "West US"

> [!NOTE]
> 배치 계정 이름은 계정을 만든 Azure 지역 내에서 고유해야 합니다. 소문자 영숫자만 포함할 수 있으며 길이는 3-24자여야 합니다. 배치 계정 이름에 `-` 또는 `_`와 같은 특수 문자를 사용할 수 없습니다.
> 
> 

### <a name="linked-storage-account-autostorage"></a>연결된 저장소 계정(autostorage)
(경우에 따라) **범용** 저장소 계정을 만들 때 배치 계정에 연결할 수 있습니다. 배치의 [응용 프로그램 패키지](batch-application-packages.md) 기능은 [배치 파일 규칙 .NET](batch-task-output.md) 라이브러리와 마찬가지로 연결된 일반 용도 저장소 계정에서 Blob Storage를 사용합니다. 이러한 선택적 기능은 배치 태스크를 실행하는 응용 프로그램을 배포하고 생성된 데이터를 유지하도록 지원합니다.

기존 Azure Storage 계정을 만들 때 새 배치 계정에 연결하려면 `--autostorage-account-id` 옵션을 지정합니다. 이 옵션에는 저장소 계정의 정규화된 리소스 ID가 필요합니다.

먼저, 저장소 계정의 세부 정보를 표시합니다.

    azure storage account show --resource-group "resgroup001" "storageaccount001"

그런 다음 `--autostorage-account-id` 옵션에 대한 **Url** 값을 사용합니다. URL 값은 "/subscriptions/"로 시작하고 저장소 계정에 대한 구독 ID 및 리소스 경로를 포함합니다.

    azure batch account create --location "West US"  --resource-group "resgroup001" --autostorage-account-id "/subscriptions/8ffffff8-4444-4444-bfbf-8ffffff84444/resourceGroups/resgroup001/providers/Microsoft.Storage/storageAccounts/storageaccount001" "batchaccount001"

## <a name="delete-a-batch-account"></a>배치 계정 삭제
Usage:

    azure batch account delete [options] <name>

예:

    azure batch account delete --resource-group "resgroup001" "batchaccount001"

지정된 배치 계정을 삭제합니다. 대화 상자가 나타나면 계정을 제거할지 확인합니다(계정 제거를 완료하려면 다소 시간이 걸릴 수 있음).

## <a name="manage-account-access-keys"></a>계정 선택키 관리
배치 계정에서 [리소스를 만들고 수정할](#create-and-modify-batch-resources) 선택키가 필요합니다.

### <a name="list-access-keys"></a>선택키 나열
Usage:

    azure batch account keys list [options] <name>

예:

    azure batch account keys list --resource-group "resgroup001" "batchaccount001"

지정된 배치 계정에 대한 계정 키를 나열합니다.

### <a name="generate-a-new-access-key"></a>새 액세스 키 생성
Usage:

    azure batch account keys renew [options] --<primary|secondary> <name>

예:

    azure batch account keys renew --resource-group "resgroup001" --primary "batchaccount001"

지정된 배치 계정에 대한 지정된 계정 키를 다시 생성합니다.

## <a name="create-and-modify-batch-resources"></a>배치 리소스 만들기 및 수정
Azure CLI를 사용하여 풀, 계산 노드, 작업 및 태스크 같은 Batch 리소스를 만들고 읽고 업데이트하며 삭제(CRUD)할 수 있습니다. 이러한 CRUD 작업에는 배치 계정 이름, 선택키 및 끝점이 필요합니다. 이러한 항목을 `-a`, `-k` 및 `-u` 옵션으로 지정하거나 (자동으로 채워진 경우) CLI에서 자동으로 사용하는 [환경 변수](#credential-environment-variables)를 설정할 수 있습니다.

### <a name="credential-environment-variables"></a>자격 증명 환경 변수
실행한 모든 명령에 대한 명령줄에 `-a`, `-k` 및 `-u` 옵션을 지정하는 대신 `AZURE_BATCH_ACCOUNT`, `AZURE_BATCH_ACCESS_KEY` 및 `AZURE_BATCH_ENDPOINT` 환경 변수를 설정할 수 있습니다. `-a`, `-k` 및 `-u` 옵션을 생략할 수 있도록 Batch CLI가 이러한 변수를 사용합니다(설정한 경우). 이 문서의 나머지 부분에서는 이러한 환경 변수를 사용한다고 가정합니다.

> [!TIP]
> `azure batch account keys list`을 사용하여 키를 나열하고 `azure batch account show`를 사용하여 계정의 끝점을 표시합니다.
> 
> 

### <a name="json-files"></a>JSON 파일
풀 및 작업같은 Batch 리소스를 만들 때 매개 변수를 명령줄 옵션으로 전달하는 대신 새 리소스의 구성이 포함된 JSON 파일을 지정할 수 있습니다. 다음과 같은 노래들을 선곡할 수 있다.

`azure batch pool create my_batch_pool.json`

명령줄 옵션만 사용하여 여러 리소스 생성 작업을 수행할 수 있지만 일부 기능에는 리소스 세부 정보를 포함하는 JSON 형식의 파일이 필요합니다. 예를 들어 시작 태스크에 대한 리소스 파일을 지정하려면 JSON 파일을 사용해야 합니다.

리소스를 만드는 데 필요한 JSON을 찾으려면 MSDN에 대한 [Batch REST API 참조][rest_api] 설명서를 참조하세요. 각 " *리소스 유형*추가" 항목은 리소스를 만드는 예제 JSON을 포함하며 JSON 파일에 대한 템플릿으로 사용할 수 있습니다. 예를 들어, 풀 생성을 위한 JSON은 [계정에 풀 추가][rest_add_pool]에 위치합니다.

> [!NOTE]
> 리소스를 만들 때 JSON 파일을 지정하면 해당 리소스에 대한 명령줄에 지정된 다른 모든 매개 변수가 무시됩니다.
> 
> 

## <a name="create-a-pool"></a>풀 만들기
Usage:

    azure batch pool create [options] [json-file]

예제(가상 컴퓨터 구성):

    azure batch pool create --id "pool001" --target-dedicated 1 --vm-size "STANDARD_A1" --image-publisher "Canonical" --image-offer "UbuntuServer" --image-sku "14.04.2-LTS" --node-agent-id "batch.node.ubuntu 14.04"

예제(Cloud Services 구성):

    azure batch pool create --id "pool002" --target-dedicated 1 --vm-size "small" --os-family "4"

Batch 서비스에서 계산 노드의 풀을 만듭니다.

[배치 기능 개요](batch-api-basics.md#pool)에서 언급한 대로 풀의 노드에 대한 운영 체제를 선택하는 경우 **가상 컴퓨터 구성** 및 **Cloud Services 구성**의 두 가지 옵션이 있습니다. `--image-*` 옵션을 사용하여 Virtual Machine 구성 풀을 만들고 `--os-family` 옵션을 사용하여 Cloud Services 구성 풀을 만듭니다. `--os-family` 및 `--image-*` 옵션 모두를 지정할 수 없습니다.

풀 [응용 프로그램 패키지](batch-application-packages.md) 및 [시작 태스크](batch-api-basics.md#start-task)에 대한 명령줄을 지정할 수 있습니다. 그러나 시작 태스크에 대한 리소스 파일을 지정하려면 대신 [JSON 파일](#json-files)을 사용해야 합니다.

다음을 사용하여 풀을 삭제합니다.

    azure batch pool delete [pool-id]

> [!TIP]
> `--image-*` 옵션의 적절한 값에 대한 [가상 컴퓨터 이미지 목록](batch-linux-nodes.md#list-of-virtual-machine-images)을 확인합니다.
> 
> 

## <a name="create-a-job"></a>작업 만들기
Usage:

    azure batch job create [options] [json-file]

예:

    azure batch job create --id "job001" --pool-id "pool001"

배치 계정에 작업을 추가하고 해당 작업이 실행되는 풀을 지정합니다.

다음을 사용하여 작업을 삭제합니다.

    azure batch job delete [job-id]

## <a name="list-pools-jobs-tasks-and-other-resources"></a>풀, 작업, 태스크 및 기타 리소스 나열
각 Batch 리소스 유형은 배치 계정을 쿼리하고 해당 형식의 리소스를 나열하는 `list` 명령을 지원합니다. 예를 들어 작업에 있는 계정 및 태스크에 풀을 나열할 수 있습니다.

    azure batch pool list
    azure batch task list --job-id "job001"

### <a name="listing-resources-efficiently"></a>효율적으로 리소스 나열
더 빠른 쿼리를 위해 `list` 작업에 대한 **선택**, **필터** 및 **확장** 절 옵션을 지정할 수 있습니다. 이러한 옵션을 사용하여 Batch 서비스에서 반환한 데이터의 양을 제한합니다. 모든 필터링이 서버 쪽에서 발생하기 때문에 관심있는 데이터만 네트워크로 전달됩니다. 나열 작업을 수행하는 경우 이러한 절을 사용하여 대역폭(및 시간)을 줄입니다.

예를 들어 "renderTask"로 시작하는 ID를 가진 풀만 반환합니다.

    azure batch task list --job-id "job001" --filter-clause "startswith(id, 'renderTask')"

Batch CLI는 Batch 서비스에서 지원하는 세 개의 절을 모두 지원합니다.

* `--select-clause [select-clause]` 각 엔터티에 대한 속성의 하위 집합을 반환합니다.
* `--filter-clause [filter-clause]` 지정된 OData 식과 일치하는 엔터티만 반환합니다.
* `--expand-clause [expand-clause]` 단일 기본 REST 호출에서 엔터티 정보를 가져옵니다. 확장 절은 이번에 `stats` 속성만 지원합니다.

세 가지 절 및 이를 사용하는 나열 쿼리 수행에 대한 세부 정보는 [효율적으로 Azure Batch 서비스 쿼리](batch-efficient-list-queries.md)를 참조하세요.

## <a name="application-package-management"></a>응용 프로그램 패키지 관리
응용 프로그램 패키지는 풀에서 계산 노드에 응용 프로그램을 배포하는 간단한 방법을 제공합니다. Azure CLI를 사용하여 응용 프로그램 패키지를 업로드하고 패키지 버전을 관리하고 패키지를 삭제할 수 있습니다.

새 응용 프로그램을 만들고 패키지 버전을 추가하려면:

**만듭니다** .

    azure batch application create "resgroup001" "batchaccount001" "MyTaskApplication"

**추가합니다** .

    azure batch application package create "resgroup001" "batchaccount001" "MyTaskApplication" "1.10-beta3" package001.zip

**활성화합니다** .

    azure batch application package activate "resgroup001" "batchaccount001" "MyTaskApplication" "1.10-beta3" zip

응용 프로그램의 **기본 버전** 설정:

    azure batch application set "resgroup001" "batchaccount001" "MyTaskApplication" --default-version "1.10-beta3"

### <a name="deploy-an-application-package"></a>응용 프로그램 패키지 배포
새 풀을 만들 때 배포에 하나 이상의 응용 프로그램 패키지를 지정할 수 있습니다. 풀을 만들 때 패키지를 지정하는 경우 노드가 풀에 조인하면 각 노드에 배포됩니다. 패키지는 노드를 다시 부팅하거나 이미지로 다시 설치하는 경우에 배포됩니다.

풀에 참가하면서 풀의 노드에 응용 프로그램 패키지를 배포하도록 풀을 만들 때 `--app-package-ref` 옵션을 지정합니다. `--app-package-ref` 옵션은 계산 노드에 배포할 세미콜론(;)으로 구분된 응용 프로그램 ID 목록을 허용합니다.

    azure batch pool create --pool-id "pool001" --target-dedicated 1 --vm-size "small" --os-family "4" --app-package-ref "MyTaskApplication"

명령줄 옵션을 사용하여 풀을 만드는 경우, 현재는 계산 노드에 배포할 응용 프로그램 패키지 *버전*(예: “1.10-beta3”)을 지정할 수 없습니다. 따라서, 풀을 만들기 전에 `azure batch application set [options] --default-version <version-id>`를 사용하여 응용 프로그램의 기본 버전을 우선 지정해야 합니다(이전 세션 참조). 그러나 풀을 만들 때 명령줄 옵션 대신 [JSON 파일](#json-files)을 사용하는 경우 풀에 대한 패키지 버전을 지정할 수 있습니다.

응용 프로그램 패키지에 대한 자세한 내용은 [Azure Batch 응용 프로그램 패키지를 사용하여 응용 프로그램 배포](batch-application-packages.md)를 참조하세요.

> [!IMPORTANT]
> 응용 프로그램 패키지를 사용하려면 [Azure Storage 계정](#linked-storage-account-autostorage) 을 배치 계정에 연결해야 합니다.
> 
> 

### <a name="update-a-pools-application-packages"></a>풀의 응용 프로그램 패키지 업데이트
기존 풀에 할당된 응용 프로그램을 업데이트하려면 `azure batch pool set` 명령을 `--app-package-ref` 옵션과 함께 실행합니다.

    azure batch pool set --pool-id "pool001" --app-package-ref "MyTaskApplication2"

기존 풀에 이미 존재하는 계산 노드에 새로운 응용 프로그램 패키지를 배포하려면, 해당 노드를 다시 시작하거나 이미지로 다시 설치해야 합니다.

    azure batch node reboot --pool-id "pool001" --node-id "tvm-3105992504_1-20160930t164509z"

> [!TIP]
> `azure batch node list`를 사용하면 풀의 노드 목록을 노드 ID와 함께 가져올 수 있습니다.
> 
> 

배포 전에 응용 프로그램을 기본 버전으로 반드시 구성해 두어야 한다는 점을 기억해야 합니다(`azure batch application set [options] --default-version <version-id>`).

## <a name="troubleshooting-tips"></a>문제 해결 팁
이 섹션에서는 Azure CLI 문제를 해결할 때 사용할 리소스를 제공하려고 합니다. 반드시 모든 문제를 해결하지는 않지만 원인의 범위를 좁히고 도움말 리소스를 가리키는 데 도움이 될 수 있습니다.

* `-h` 을 사용하여 모든 CLI 명령의 **도움말 텍스트** 를 봅니다.
* `-v` 및 `-vv`를 사용하여 **자세한 정보** 명령 출력을 표시합니다. `-vv`는 "추가" 자세한 정보이며 실제 REST 요청 및 응답을 표시합니다. 이러한 스위치는 전체 오류 출력을 표시하는 데 유용합니다.
* `--json` 옵션을 사용하여 **명령 출력을 JSON으로** 볼 수 있습니다. 예를 들어 `azure batch pool show "pool001" --json` 은 JSON 형식으로 pool001의 속성을 표시합니다. 그런 다음 이 출력을 복사하고 수정하여 `--json-file` 에서 사용할 수 있습니다(이 문서의 앞부분에서 [JSON 파일](#json-files) 참조).
* [MSDN에 대한 Batch 포럼][batch_forum]은 도움말 리소스이며 Batch 팀 구성원에 의해 밀접하게 모니터링됩니다. 문제가 발생하거나 특정 작업에 도움이 필요한 경우 거기에 질문을 게시해야 합니다.
* 모든 Batch 리소스 작업은 현재 Azure CLI에서 지원됩니다. 예를 들어 현재는 풀에 대한 응용 프로그램 패키지 *버전* 인 패키지 ID을 지정할 수 없습니다. 이러한 경우는 명령줄 옵션을 사용하는 대신 명령에 대한 `--json-file` 를 제공해야 합니다. 최신 CLI 버전을 최신 상태로 유지하여 이후 향상 기능을 선택해야 합니다.

## <a name="next-steps"></a>다음 단계
* [Azure Batch 응용 프로그램 패키지를 사용하여 응용 프로그램 배포](batch-application-packages.md) 를 참조하여 Batch 계산 노드에서 실행하는 응용 프로그램을 관리하고 배포하는 데 이 기능을 사용하는 방법을 알아봅니다.
* 항목의 수 및 쿼리에 대해 배치에 반환되는 정보의 유형을 줄이는 데 대한 자세한 내용은 [효율적인 배치 서비스 쿼리](batch-efficient-list-queries.md) 을 참조하세요.

[batch_forum]: https://social.msdn.microsoft.com/forums/azure/en-US/home?forum=azurebatch
[github_readme]: https://github.com/Azure/azure-xplat-cli/blob/dev/README.md
[rest_api]: https://msdn.microsoft.com/library/azure/dn820158.aspx
[rest_add_pool]: https://msdn.microsoft.com/library/azure/dn820174.aspx

