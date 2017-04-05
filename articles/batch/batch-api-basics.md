---
title: "개발자를 위한 Azure 배치 개요 | Microsoft Docs"
description: "개발자의 관점에서 배치 서비스와 해당 API에 대한 기능을 알아봅니다."
services: batch
documentationcenter: .net
author: tamram
manager: timlt
editor: 
ms.assetid: 416b95f8-2d7b-4111-8012-679b0f60d204
ms.service: batch
ms.devlang: multiple
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-compute
ms.date: 03/08/2017
ms.author: tamram
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: f323afdea34e973f3ecdd54022f04b3f0d86afb1
ms.lasthandoff: 04/03/2017


---
# <a name="develop-large-scale-parallel-compute-solutions-with-batch"></a>Batch를 사용하여 대규모 병렬 계산 솔루션 개발

Azure 배치 서비스의 핵심 구성 요소 개요에서는 배치 개발자가 대규모 병렬 계산 솔루션을 구축하는 데 사용할 수 있는 기본 서비스 기능 및 리소스를 설명합니다.

분산된 컴퓨팅 응용 프로그램 또는 [REST API][batch_rest_api] 호출을 직접 실행하는 서비스를 개발하거나 [Batch SDK](batch-apis-tools.md#batch-development-apis) 중 하나를 사용하는 경우 이 문서에서 설명할 다양한 리소스 및 기능을 사용하게 됩니다.

> [!TIP]
> 배치 서비스에 대한 고급 소개는 [Azure 배치의 기본 사항](batch-technical-overview.md)을 참조하세요.
>
>

## <a name="batch-service-workflow"></a>배치 서비스 워크플로
병렬 워크로드를 처리하기 위해 배치 서비스를 사용하는 대부분의 응용 프로그램 및 서비스에서는 다음과 같은 높은 수준의 워크플로를 일반적으로 사용합니다.

1. [Azure Storage][azure_storage] 계정으로 처리하려는 **데이터 파일**을 업로드합니다. 배치는 Azure Blob 저장소에 액세스하기 위한 기본 제공 지원을 포함하고 태스크는 실행 시 이러한 파일을 [계산 노드](#compute-node) 로 다운로드할 수 있습니다.
2. 태스크가 실행할 **응용 프로그램 파일**을 업로드합니다. 이러한 파일은 이진 파일 또는 스크립트와 해당 종속성일 수 있으며 작업의 태스크로 실행됩니다. 작업하면 저장소 계정에서 다음 파일을 다운로드할 수 있습니다. 또는 응용 프로그램 관리 및 배포를 위한 [응용 프로그램 패키지](#application-packages) 배치 기능을 사용할 수 있습니다.
3. 계산 노드 [풀](#pool) 을 만듭니다. 풀을 만들 경우 풀, 크기 및 운영 체제에 대한 계산 노드 수를 지정합니다. 작업의 각 태스크를 실행할 경우 풀의 노드 중 하나에서 실행하도록 할당합니다.
4. [작업](#job)을 만듭니다. 작업에서는 태스크의 컬렉션을 관리합니다. 각 작업을 작업의 태스크를 실행하는 특정 풀과 연결합니다.
5. 작업에 [태스크](#task) 를 추가합니다. 각 태스크는 업로드한 스크립트나 응용 프로그램을 실행하여 저장소 계정에서 다운로드한 데이터 파일을 처리합니다. 각 태스크가 완료되면 해당 출력을 Azure 저장소에 업로드할 수 있습니다.
6. 작업 진행 상태를 모니터링하고 Azure 저장소에서 태스크 출력을 검색합니다.

다음 섹션에서는 이러한 내용 및 분산된 전산 시나리오를 사용할 수 있는 Batch의 다른 리소스를 설명합니다.

> [!NOTE]
> 배치 서비스를 사용하려면 [배치 계정](batch-account-create-portal.md)이 필요합니다. 또한 거의 모든 솔루션에서는 파일 저장 및 검색을 위해 [Azure Storage][azure_storage] 계정을 사용합니다. 배치는 현재 [Azure 저장소 계정 정보](../storage/storage-create-storage-account.md)의 5단계 [저장소 계정 만들기](../storage/storage-create-storage-account.md#create-a-storage-account)에서 설명한 대로 **범용** 저장소 계정 유형만 지원합니다.
>
>

## <a name="batch-service-resources"></a>배치 서비스 리소스
계정, 계산 노드, 풀, 작업, 태스크 등 다음 리소스 중 일부는 배치 서비스를 사용하는 모든 솔루션에 필요합니다. 작업 일정, 응용 프로그램 패키지 등의 기타 리소스는 유용하기는 하지만 선택적 기능입니다.

* [계정](#account)
* [계산 노드](#compute-node)
* [풀](#pool)
* [작업](#job)

  * [작업 일정](#scheduled-jobs)
* [Task](#task)

  * [시작 태스크](#start-task)
  * [작업 관리자 태스크](#job-manager-task)
  * [작업 준비 및 해제 태스크](#job-preparation-and-release-tasks)
  * [MPI(다중 인스턴스 태스크)](#multi-instance-tasks)
  * [작업 종속성](#task-dependencies)
* [응용 프로그램 패키지](#application-packages)

## <a name="account"></a>계정
배치 계정은 배치 서비스 내에서 고유 하게 식별되는 엔터티입니다. 모든 처리는 배치 계정과 연결됩니다. 배치 서비스를 사용하여 작업을 수행하는 경우 계정 이름 및 하나의 계정 키가 모두 필요합니다. [Azure 포털을 사용하여 Azure 배치 계정을 만들](batch-account-create-portal.md)수 있습니다.

## <a name="compute-node"></a>계산 노드
계산 노드는 응용 프로그램의 워크로드 중 일부를 처리하도록 전담하는 Azure VM(가상 컴퓨터)입니다. 노드의 크기에 따라 노드에 할당되는 CPU 코어 수, 메모리 용량 및 로컬 파일 시스템 크기가 결정됩니다. Azure 클라우드 서비스 또는 가상 컴퓨터 마켓플레이스 이미지를 사용하여 Windows 또는 Linux 노드의 풀을 만들 수 있습니다. 이러한 옵션에 대한 자세한 내용은 다음 [풀](#pool) 을 참조하세요.

노드는 노드의 운영 체제 환경에서 지원하는 실행 파일이나 스크립트를 실행할 수 있습니다. 여기에 Windows용 \*.exe, \*.cmd, \*.bat 및 PowerShell 스크립트, Linux용 이진 파일, 셸 및 Python 스크립트를 포함합니다.

배치의 모든 계산 노드는 다음 사항도 포함합니다.

* 태스크에서 참조로 사용할 수 있는 표준 [폴더 구조](#files-and-directories) 및 연결된 [환경 변수](#environment-settings-for-tasks)
* **방화벽** 설정
* [원격 액세스](#connecting-to-compute-nodes) 

## <a name="pool"></a>풀
풀은 응용 프로그램이 실행되는 노드 컬렉션입니다. 사용자가 풀을 만들 수도 있고, 완료할 작업을 지정한 경우에는 배치 서비스에서 자동으로 풀을 만듭니다. 응용 프로그램의 리소스 요구 사항을 충족하는 풀을 만들고 관리할 수 있습니다. 풀은 풀이 생성된 배치 계정을 통해서만 사용할 수 있습니다. 하나의 배치 계정에는 둘 이상의 풀이 있을 수 있습니다.

Azure 배치 풀은 코어 Azure 계산 플랫폼을 기반으로 합니다. 배치 풀은 대규모 할당, 응용 프로그램 설치, 데이터 배포, 상태 모니터링 및 풀 내의 유연한 계산 노드 수 조정([크기 조정](#scaling-compute-resources)) 기능을 제공합니다.

풀에 추가된 모든 노드에는 고유 이름 및 IP 주소가 할당됩니다. 노드가 풀에서 제거되면 운영 체제 또는 파일에 적용된 모든 변경 내용이 손실되며, 해당 이름 및 IP 주소가 나중에 사용할 수 있도록 해제됩니다. 노드가 풀에서 제거되면 수명이 끝납니다.

풀을 만들 때는 다음과 같은 특성을 지정할 수 있습니다.

* 계산 노드 **운영 체제** 및 **버전**

    풀의 노드에서 운영 체제를 선택하는 경우 **가상 컴퓨터 구성** 및 **Cloud Services 구성** 등의 두 가지 옵션이 있습니다.

    **가상 컴퓨터 구성**은 [Azure 가상 컴퓨터 마켓플레이스][vm_marketplace]에서 계산 노드에 대한 Linux와 Windows 이미지를 제공합니다.
    가상 컴퓨터 구성 노드를 포함하는 풀을 만들 때 노드 크기뿐만 아니라 **가상 컴퓨터 이미지 참조** 및 노드에 설치할 배치 **노드 에이전트 SKU**도 지정해야 합니다. 이러한 풀 속성에 대한 자세한 내용은 [Azure 배치 풀에서 Linux 계산 노드 프로비전](batch-linux-nodes.md)을 참조하세요.

    **클라우드 서비스 구성** 은 Windows 계산 노드 *만*제공합니다. 클라우드 서비스 구성 풀에 사용 가능한 운영 체제는 [Azure 게스트 OS 릴리스 및 SDK 호환성 매트릭스](../cloud-services/cloud-services-guestos-update-matrix.md)에 나열됩니다. 클라우드 서비스 노드를 포함하는 풀을 만드는 경우 노드 크기 및 해당 *OS 제품군*만을 지정해야 합니다. Windows 계산 노드 풀을 만들 때 클라우드 서비스가 가장 일반적으로 사용됩니다.

  * *OS 제품군* 은 OS와 함께 설치되는 .NET 버전도 결정합니다.
  * Cloud Services 내의 작업자 역할과 마찬가지로 *OS 버전*을 지정할 수 있습니다(작업자 역할에 대한 자세한 내용은 [클라우드 서비스 개요](../cloud-services/cloud-services-choose-me.md#tell-me-about-cloud-services)의 [클라우드 서비스 정보](../cloud-services/cloud-services-choose-me.md) 섹션 참조).
  * 작업자 역할과 마찬가지로, *OS 버전*에 대해 `*`를 지정하는 것이 좋습니다. 그러면 노드가 자동으로 업그레이드되며 새로 릴리스된 버전을 사용하는 데 필요한 조정 작업이 없습니다. 특정 OS 버전을 선택하는 기본 사용 사례는 버전을 업데이트하기 전에 이전 버전과의 호환성 테스트를 수행할 수 있게 하여 응용 프로그램 호환성을 유지하는 것입니다. 유효성이 검사되면 풀의 *OS 버전*을 업데이트하고 새 OS 이미지를 설치할 수 있습니다. 실행 중인 태스크는 모두 중단되고 다시 큐에 대기됩니다.
* **노드 크기**

    **클라우드 서비스 구성** 계산 노드 크기는 [클라우드 서비스 크기](../cloud-services/cloud-services-sizes-specs.md)에 나열됩니다. 배치는 `ExtraSmall`, `STANDARD_A1_V2` 및 `STANDARD_A2_V2`를 제외한 모든 클라우드 서비스 크기를 지원합니다.

    **가상 컴퓨터 구성** 계산 노드 크기는 [Azure에서 가상 컴퓨터에 대한 크기](../virtual-machines/linux/sizes.md)(Linux) 및 [Azure에서 가상 컴퓨터에 대한 크기](../virtual-machines/windows/sizes.md)(Windows)에 나열되어 있습니다. 배치는 `STANDARD_A0`및 프리미엄 저장소(`STANDARD_GS`, `STANDARD_DS` 및 `STANDARD_DSV2` 시리즈) 크기를 제외한 모든 Azure VM 크기를 지원합니다.

    계산 노드 크기를 선택할 때는 노드에서 실행할 응용 프로그램의 특징과 요구 사항을 고려합니다. 응용 프로그램이 다중 스레드되었는지 여부 및 응용 프로그램에서 소비하는 메모리 양과 같은 측면은 가장 적절하고 비용 효과적인 노드 크기를 결정하는 데 도움을 줄 수 있습니다. 하나의 태스크가 한 번에 하나의 노드에서 실행되는 것으로 가정하여 노드 크기를 선택하는 것이 일반적입니다. 하지만 작업 실행 중에 계산 노드에 [병렬로 실행되는](batch-parallel-node-tasks.md) 다수의 태스크(따라서 다수의 응용 프로그램 인스턴스)가 있을 수도 있습니다. 이런 경우, 병렬 작업 실행에 대해 증가되는 수요를 충족하기 위해 더 큰 노드 크기를 선택하는 것이 일반적입니다. 자세한 내용은 [태스크 일정 정책](#task-scheduling-policy)을 참조하세요.

    풀에 포함된 모든 노드의 크기가 같습니다. 상이한 시스템 요구 사항 및/또는 부하 수준으로 응용 프로그램을 실행하려면 별도의 풀을 사용하는 것이 좋습니다.
* **노드의 대상 수**

    풀에서 배포하려는 계산 노드 수입니다. 상황에 따라 풀이 원하는 노드 수에 도달하지 않을 수 있기 때문에 *대상* 이라고 합니다. 배치 계정의 [코어 할당량](batch-quota-limit.md) 에 도달하였거나 또는 최대 노드 수를 제한하는 풀에 적용한 자동 크기 조정 수식이 있는 경우 풀이 원하는 노드 수에 도달하지 않을 수 있습니다(다음 "정책 크기 조정" 섹션 참조).
* **크기 조정 정책**

    동적 워크로드의 경우 풀에 [자동 크기 조정 수식](#scaling-compute-resources)을 작성하고 적용할 수 있습니다. 배치 서비스는 수식을 주기적으로 평가하여 사용자가 지정할 수 있는 여러 풀, 작업 및 태스크에 따라 풀 내의 노드 수를 조정합니다.
* **태스크 일정 정책**

    [노드당 최대 태스크](batch-parallel-node-tasks.md) 구성은 풀 내의 각 계산 노드에서 병렬로 실행할 수 있는 최대 태스크 수를 결정합니다.

    기본 구성은 노드에서 한 번에 하나의 태스크를 실행하도록 지정하지만 노드에서 동시에 두 개 이상의 태스크를 실행하는 것이 효과적인 시나리오도 있습니다. [동시 노드 태스크](batch-parallel-node-tasks.md) 문서에서 [예제 시나리오](batch-parallel-node-tasks.md#example-scenario)를 참조하여 노드당 여러 태스크를 실행하여 이득이 될 수 있는 방법을 확인합니다.

    *채우기 유형*을 지정할 수도 있습니다. 이는 태스크를 다른 노드에 할당하기 전에 배치가 태스크를 풀의 모든 노드에 균등하게 분산시킬지 또는 각 노드를 최대 태스크 수로 패키지할지를 결정합니다.
* **통신 상태** 

    대부분의 시나리오에서 태스크는 독립적으로 작동하고 다른 노드와 통신할 필요가 없습니다. 하지만 [MPI 시나리오](batch-mpi.md)와 같이 태스크가 통신해야 하는 응용 프로그램이 있습니다.

    **노드 간 통신**을 허용하도록 풀을 구성하여 풀 내의 노드가 런타임에 통신할 수 있습니다. 노드 간 통신 기능을 사용하는 경우 클라우드 서비스 구성 풀의 노드는 1100보다 큰 포트에서 서로 통신할 수 있고 가상 컴퓨터 구성 풀은 모든 포트에서 트래픽을 제한하지 않습니다.

    노드 간 통신을 사용하도록 설정하면 클러스터 내의 노드 배치에도 영향을 주며 배포 제한 때문에 풀의 노드 최대 수를 제한할 수 있습니다. 응용 프로그램에 노드 간 통신이 필요하지 않은 경우 배치 서비스는 잠재적으로 여러 다른 클러스터 및 데이터 센터의 많은 노드를 풀에 할당하여 병렬 처리 능력을 증가시킬 수 있습니다.
* **시작 태스크** 

    선택적인 *시작 태스크* 는 노드가 다시 시작하거나 이미지로 다시 설치할 때 뿐만 아니라 해당 노드가 풀을 연결할 때에도 각 노드에서 실행됩니다. 시작 태스크는 태스크가 계산 노드에서 실행하는 응용 프로그램을 설치하는 등 태스크를 실행하기 위해 계산 노드를 준비하는 데 특히 유용합니다.
* **응용 프로그램 패키지**

    [응용 프로그램 패키지](#application-packages) 를 지정하여 풀에 계산 노드를 배포할 수 있습니다. 응용 프로그램 패키지는 태스크가 실행하는 응용 프로그램의 간소화된 배포 및 버전 관리를 제공합니다. 풀에 지정할 응용 프로그램 패키지는 해당 풀에 조인되거나 다시 부팅 또는 이미지로 다시 설치되는 모든 노드에 설치됩니다. 현재 응용 프로그램 패키지는 Linux 계산 노드에서 지원되지 않습니다.
* **네트워크 구성**

    풀의 계산 노드를 생성해야 하는 Azure [VNet(가상 네트워크)](../virtual-network/virtual-networks-overview.md) 의 ID를 지정할 수 있습니다. 자세한 내용은 [풀 네트워크 구성](#pool-network-configuration) 섹션을 참조하세요.

> [!IMPORTANT]
> 모든 배치 계정에는 배치 계정에 있는 **코어**(및 계산 노드)의 수를 제한하는 기본 **할당량**이 있습니다. 기본 할당량 및 [할당량을 증가](batch-quota-limit.md#increase-a-quota)하는 방법에 대한 지침(예: 배치 계정의 최대 코어 수)은 [Azure 배치 서비스에 대한 할당량 및 한도](batch-quota-limit.md)에서 찾을 수 있습니다. "풀이 X 노드보다 더 멀리 도달할 수 없는 경우" 이 코어 할당량이 원인일 수 있습니다.
>
>

## <a name="job"></a>작업
작업은 태스크의 컬렉션입니다. 풀의 계산 노드에서 해당 태스크에 의해 수행된 계산 방식을 관리합니다.

* 작업은 작업이 실행되는 **풀**을 지정합니다. 각 작업에 새 풀을 만들거나 많은 작업에 하나의 풀을 사용할 수 있습니다. 작업 일정과 연결된 각 작업 또는 작업 일정과 연결된 모든 작업에 풀을 만들 수 있습니다.
* 선택적 **작업 우선 순위**를 지정할 수 있습니다. 현재 진행 중인 다른 작업보다 높은 우선 순위로 작업을 제출하면 우선 순위가 높은 작업의 태스크가 우선 순위가 낮은 작업의 태스크보다 먼저 큐에 삽입됩니다. 이미 실행 중인 우선 순위가 낮은 작업의 태스크는 선취되지 않습니다.
* 작업 **제약 조건** 을 사용하여 작업에 특정 제한을 지정할 수 있습니다.

    **최대 벽시계 시간**을 설정할 수 있습니다. 그래서 지정된 최대 벽시계 시간보다 오랫동안 작업이 실행되면 작업 및 연결된 모든 태스크가 종료됩니다.

    배치는 실패한 태스크를 검색하고 다시 시도할 수 있습니다. 태스크가 *항상* 다시 시도되거나 다시 시도되지 *않도록* 지정하는 것을 포함하여 **태스크 다시 시도 최대 횟수**를 제약 조건으로 지정할 수 있습니다. 태스크 다시 시도는 태스크가 다시 실행되기 위해 다시 큐에 대기되는 것을 의미합니다.
* 클라이언트 응용 프로그램이 작업에 태스크를 추가할 수 있습니다. 또는 [작업 관리자 태스크](#job-manager-task)를 지정할 수 있습니다. 작업 관리자 태스크는 풀의 계산 노드 중 하나에서 작업 관리자 태스크를 실행하여 작업에 필요한 태스크를 만드는 데 필요한 정보를 포함합니다. 작업 관리자 태스크는 배치에서 특별히 처리되며, 작업이 생성되는 즉시 큐에 대기되고 실패할 경우 다시 시작됩니다. 작업 관리자 태스크는 작업이 인스턴스화되기 전에 태스크를 정의할 수 있는 유일한 방법이기 때문에 *작업 일정*에서 만든 작업에 [필요](#scheduled-jobs)합니다.
* 작업 내의 모든 태스크가 완료되면 작업은 기본적으로 활성 상태로 유지됩니다. 작업에서 모든 태스크가 완료되면 작업이 자동으로 종료되도록 이 동작을 변경할 수 있습니다. 작업의 **onAllTasksComplete** 속성(배치 .NET의 [OnAllTasksComplete][net_onalltaskscomplete])을 *terminatejob*으로 설정하여 태스크가 모두 완료 상태인 경우 작업을 자동으로 종료합니다.

    배치 서비스는 태스크가 *없는* 작업을 모든 태스크를 완료한 것으로 간주합니다. 따라서 이 옵션은 [작업 관리자 태스크](#job-manager-task)와 함께 가장 일반적으로 사용됩니다. 작업 관리자 없이 작업 자동 종료를 사용하려는 경우 처음부터 새 작업의 **onAllTasksComplete** 속성을 *noaction*으로 설정한 다음 작업에 태스크의 추가를 완료한 후에 *terminatejob*으로 설정해야 합니다.

### <a name="job-priority"></a>작업 우선 순위
배치에서 만드는 작업에 우선 순위를 할당할 수 있습니다. 배치 서비스는 작업의 우선 순위 값을 사용하여 계정 내의 작업 예약 순서를 결정합니다( [예약된 작업](#scheduled-jobs)과 혼동하지 않아야 합니다). 우선 순위 값의 범위는 -1000에서 1000까지이며 -1000이 가장 낮은 우선 순위를, 1000이 가장 높은 우선 순위를 나타냅니다. 작업의 우선 순위를 업데이트하려면, [작업 속성 업데이트][rest_update_job] 작업(배치 REST)을 사용하거나 [CloudJob.Priority][net_cloudjob_priority] 속성(배치 .NET)을 수정합니다.

동일한 계정 내에서 우선 순위가 높은 작업은 우선 순위가 낮은 작업보다 먼저 예약됩니다. 하나의 계정에서 우선 순위가 더 높은 작업이 다른 계정에서 우선 순위가 더 낮은 다른 작업보다 먼저 예약되는 것은 아닙니다.

풀 간의 예약 작업은 서로 별개입니다. 다양한 풀 사이에서, 연결된 풀에 유휴 노드가 부족한 경우 우선 순위가 높은 작업이 먼저 예약된다고 보장할 수 없습니다. 동일한 풀에서는 우선 순위가 같은 작업이 예약될 기회가 동일합니다.

### <a name="scheduled-jobs"></a>Scheduled jobs
[작업 일정][rest_job_schedules]을 사용하여 배치 서비스 내에서 되풀이되는 작업을 만들 수 있습니다. 작업 일정은 작업을 실행할 시간을 지정하며, 실행할 작업에 대한 사양을 포함합니다. 일정의 빈도, 즉 일정이 시행되는 시점 및 기간, 일정의 기간 동안 작업을 만들어야 하는 빈도를 지정할 수 있습니다.

## <a name="task"></a>작업
태스크는 작업과 연결되는 계산의 단위입니다. 노드에서 실행됩니다. 태스크는 실행을 위해 노드에 할당되거나, 노드를 사용할 수 있을 때까지 큐에 대기됩니다. 간단히 말해 태스크는 계산 노드에서 하나 이상의 프로그램이나 스크립트를 실행하여 완료해야 하는 작업을 수행합니다.

태스크를 만들 때 다음을 지정할 수 있습니다.

* 태스크에 대한 **명령줄** 입니다. 계산 노드에 응용 프로그램 또는 스크립트를 실행하는 명령줄입니다.

    명령줄이 셸에서 실제로 실행되지 않도록 주의해야 합니다. 따라서 기본적으로 [환경 변수](#environment-settings-for-tasks) 확장과 같은 셸 기능을 활용할 수 없습니다(`PATH` 포함). 이러한 기능을 활용하려면 예를 들어 Windows 노드에서 `cmd.exe` 또는 Linux 노드에서 `/bin/sh`를 시작하여 명령줄에서 셸을 호출해야 합니다.

    `cmd /c MyTaskApplication.exe %MY_ENV_VAR%`

    `/bin/sh -c MyTaskApplication $MY_ENV_VAR`

    태스크가 노드의 `PATH` 또는 참조 환경 변수가 아닌 응용 프로그램 또는 스크립트를 실행해야 하는 경우 태스크 명령줄에서 명시적으로 셸을 호출합니다.
* **리소스 파일** . 태스크의 명령줄을 실행하기 전에 이러한 파일은 범용 Azure Storage 계정의 Blob 저장소에서 노드로 자동으로 복사됩니다. 자세한 내용은 [시작 태스크](#start-task) 및 [파일 및 디렉터리](#files-and-directories) 섹션을 참조하세요.
* 응용 프로그램에 필요한 **환경 변수** . 자세한 내용은 [태스크에 대한 환경 설정](#environment-settings-for-tasks) 섹션을 참조하세요.
* 태스크가 실행되어야 하는 **제약 조건** . 예를 들어 제약 조건은 태스크가 실행되도록 허용된 최대 시간, 실패한 태스크를 다시 시도해야 하는 최대 횟수 및 태스크의 작업 중인 디렉터리에 파일을 보관하는 최대 시간을 포함합니다.
* **응용 프로그램 패키지** 입니다. [응용 프로그램 패키지](#application-packages) 는 태스크가 실행하는 응용 프로그램의 간소화된 배포 및 버전 관리를 제공합니다. 작업 수준 응용 프로그램 패키지는 공유 풀 환경에서 특히 유용하며 여기서는 다른 작업이 하나의 풀에서 실행되고 작업이 완료될 때 풀이 삭제되지 않습니다. 작업에 있는 태스크가 풀에 있는 노드보다 적은 경우 태스크를 실행하는 노드에만 응용 프로그램을 배포하므로 태스크 응용 프로그램 패키지는 데이터 전송을 최소화할 수 있습니다.

노드에서 계산을 수행하도록 정의한 태스크 외에 다음과 같은 특수한 태스크도 배치 서비스에서 제공됩니다.

* [시작 태스크](#start-task)
* [작업 관리자 태스크](#job-manager-task)
* [작업 준비 및 해제 태스크](#job-preparation-and-release-tasks)
* [MPI(다중 인스턴스 태스크)](#multi-instance-tasks)
* [작업 종속성](#task-dependencies)

### <a name="start-task"></a>시작 태스크
풀과 **시작 태스크** 를 연결하여 해당 노드의 운영 환경을 준비할 수 있습니다. 예를 들어, 태스크를 실행하는 응용 프로그램을 설치하거나 백그라운드 프로세스를 시작하는 등의 작업을 수행할 수 있습니다. 시작 태스크는 노드가 풀에 처음 추가된 경우 및 다시 시작되거나 이미지로 다시 설치되는 경우를 포함하여 풀에 남아 있는 한, 노드가 시작될 때마다 실행됩니다.

시작 태스크의 주요 이점은 태스크 실행에 필요한 계산 노드를 구성하고 응용 프로그램을 설치하는 데 필요한 모든 정보를 포함할 수 있다는 것입니다. 따라서 풀 내의 노드 수를 늘리려면 간단히 새 대상 노드 수를 지정하면 됩니다. 시작 태스크는 배치 서비스에 새 노드를 구성하고 태스크를 수락할 준비가 되게 하는 데 필요한 정보를 제공합니다.

모든 Azure 배치 태스크와 마찬가지로, 실행할 **명령줄** 외에 [Azure Storage][azure_storage]의 **리소스 파일** 목록을 지정할 수 있습니다. 배치 서비스는 먼저 Azure Storage에서 노드에 리소스 파일을 복사한 다음 명령줄을 실행합니다. 풀 시작 태스크의 경우 파일은 일반적으로 태스크 응용 프로그램 및 해당 종속성을 포함합니다.

그러나 시작 태스크는 또한 계산 노드에서 실행되는 모든 태스크에서 사용될 참조 데이터를 포함할 수도 있습니다. 예를 들어, 시작 태스크의 명령줄은 `robocopy` 작업을 수행하여 응용 프로그램 파일(리소스 파일로 지정되고 노드에 다운로드됨)을 시작 태스크의 [작업 중인 디렉터리](#files-and-directories)에서 [공유 폴더](#files-and-directories)로 복사한 다음 MSI 또는 `setup.exe`을 실행할 할 수 있습니다.

> [!IMPORTANT]
> 배치는 현재 [Azure 저장소 계정 정보](../storage/storage-create-storage-account.md)의 5단계 [저장소 계정 만들기](../storage/storage-create-storage-account.md#create-a-storage-account)에서 설명한 대로 **범용** 저장소 계정 유형*만* 지원합니다. 배치 태스크(표준 태스크, 시작 태스크, 작업 준비 태스크 및 작업 릴리스 태스크 포함)은 *범용* 저장소 계정에서 **만** 상주하는 리소스 파일을 지정해야 합니다.
>
>

일반적으로 배치 서비스에서 시작 태스크가 완료되기를 기다린 후 노드에 태스크를 할당할 준비가 되었다고 간주하는 것이 좋지만 이를 구성할 수 있습니다.

계산 노드에서 시작 태스크가 실패하면 노드 상태가 실패를 반영하여 업데이트되고 노드에 태스크가 할당되지 않습니다. 저장소에서 해당 리소스 파일을 복사하는 동안 문제가 발생하거나 해당 명령줄에서 실행된 프로세스에서 0이 아닌 종료 코드를 반환하면 시작 태스크가 실패할 수 있습니다.

*기존* 풀에 시작 태스크를 추가하거나 업데이트하는 경우 노드에 적용할 시작 태스크에 대한 해당 계산 노드를 다시 부팅해야 합니다.

### <a name="job-manager-task"></a>작업 관리자 태스크
일반적으로 **작업 관리자 태스크**를 사용하여 작업 실행을 제어 및/또는 모니터링합니다. 예를 들어 작업에 대한 태스크를 만들고 제출하고 실행할 추가 작업을 결정하고 작업이 완료되는 시점을 결정합니다. 그러나 작업 관리자 태스크는 이러한 작업에 제한되지 않습니다. 이 작업에 필요한 모든 작업을 수행할 수 있는 완전한 태스크입니다. 예를 들어 작업 관리자 태스크는 매개 변수로 지정된 파일을 다운로드하고, 해당 파일의 내용을 분석하고, 이러한 내용에 따라 추가 태스크를 제출할 수 있습니다.

작업 관리자 태스크는 다른 모든 작업이 시작되기 전에 시작됩니다. 다음과 같은 기능을 제공합니다.

* 작업이 만들어질 때 배치 서비스에서 태스크로 자동으로 제출됩니다.
* 작업의 다른 태스크보다 먼저 실행되도록 예약됩니다.
* 풀 크기를 줄일 경우 연결된 해당 노드가 풀에서 마지막으로 제거됩니다.
* 해당 종료는 작업의 모든 태스크에 대한 종료에 연결될 수 있습니다.
* 작업 관리자 태스크는 다시 시작해야 할 경우 가장 높은 우선 순위가 지정됩니다. 유휴 노드를 사용할 수 없는 경우 배치 서비스는 풀에서 실행 중인 다른 태스크 중 하나를 종료하여 작업 관리자 태스크를 실행할 공간을 확보할 수 있습니다.
* 작업의 작업 관리자 태스크는 다른 작업의 태스크보다 우선 순위가 높지 않습니다. 작업 간에는 작업 수준 우선 순위만 준수됩니다.

### <a name="job-preparation-and-release-tasks"></a>작업 준비 및 해제 태스크
배치는 사전 작업 실행 설치를 위한 작업 준비 태스크를 제공합니다. 작업 릴리스 태스크는 사후 작업 유지 관리 또는 정리를 위한 작업입니다.

* **작업 준비 태스크**: 작업 준비 태스크는 다른 모든 작업 태스크가 실행되기 전에 태스크를 실행하도록 예약된 모든 계산 노드에서 실행됩니다. 예를 들어 작업 준비 태스크를 사용하여 모든 태스크에서 공유하는 데이터를 복사할 수 있지만 작업에 고유해야 합니다.
* **작업 해제 태스크**: 작업이 완료된 경우 하나 이상의 태스크를 실행한 풀의 각 노드에서 작업 해제 태스크가 실행됩니다. 예를 들어 작업 해제 태스크를 사용하여 작업 준비 태스크에서 복사한 데이터를 삭제하거나, 진단 로드 데이터를 압축 및 업로드할 수 있습니다.

작업 준비 및 릴리스 태스크를 사용하면 태스크가 호출될 때 실행할 명령줄을 지정할 수 있습니다. 파일 다운로드, 상승된 권한 실행, 사용자 지정 환경 변수, 최대 실행 기간, 재시도 횟수 및 파일 보존 시간 등의 기능을 제공합니다.

작업 준비 및 해제 태스크에 대한 자세한 내용은 [Azure 배치 계산 노드에서 작업 준비 및 완료 태스크 실행](batch-job-prep-release.md)을 참조하세요.

### <a name="multi-instance-task"></a>다중 인스턴스 태스크
[다중 인스턴스 태스크](batch-mpi.md) 는 둘 이상의 계산 노드에서 동시에 실행될 수 있도록 구성된 태스크입니다. 다중 인스턴스 태스크와 함께 할당되는 계산 노드 그룹이 필요한 MPI(Message Passing Interface)와 같은 고성능 컴퓨팅 시나리오를 사용하여 단일 워크로드를 처리할 수 있습니다.

배치 .NET 라이브러리를 사용하여 일괄 처리에서 MPI 작업 실행에 대한 자세한 내용은 [다중 인스턴스 태스크를 사용하여 Azure 배치에서 MPI(Message Passing Interface) 응용 프로그램 실행](batch-mpi.md)을 확인합니다.

### <a name="task-dependencies"></a>작업 종속성
[작업 종속성](batch-task-dependencies.md)은 그 이름에서 알 수 있듯이, 한 작업이 실행되기 전까지 다른 작업에 종속되도록 지정할 수 있습니다. 이 기능은 "다운스트림" 태스크가 "업스트림" 태스크의 출력을 사용하거나 업스트림 태스크가 다운스트림 태스크에 필요한 몇 가지 초기화를 수행하는 상황에 대한 지원을 제공합니다. 이 기능을 사용하려면 먼저 배치 작업에서 태스크 종속성을 사용하도록 설정해야 합니다. 그런 다음 다른(또는 여러 다른) 태스크에 종속된 각 태스크에 대해 해당 태스크가 종속된 태스크를 지정합니다.

태스크 종속성을 통해 다음과 같은 시나리오를 구성할 수 있습니다.

* *taskB*가 *taskA*에 종속됨(*taskB*는 *taskA*가 완료될 때까지 실행을 시작하지 않음)
* *taskC*는 *taskA* 및 *taskB*에 종속됨
* *taskD*는 실행하기 전에 태스크 범위(예: 태스크 *1*~*10*)에 종속됨

이 기능에 대한 자세한 정보는 [azure-batch-samples][github_samples] GitHub 리포지토리에서 [Azure 배치에서 태스크 종속성](batch-task-dependencies.md) 및 [TaskDependencies][github_sample_taskdeps] 코드 예제를 확인합니다.

## <a name="environment-settings-for-tasks"></a>태스크에 대한 환경 설정
Batch 서비스에 의해 실행되는 각 태스크는 계산 노드에 설정된 환경 변수에 액세스할 수 있습니다. 여기에는 Batch 서비스에 의해 정의된([서비스 정의][msdn_env_vars]) 환경 변수와 태스크에 대해 정의할 수 있는 사용자 지정 환경 변수가 포함됩니다. 태스크가 실행하는 응용 프로그램 및 스크립트는 실행 중에 이러한 환경 변수에 액세스할 수 있습니다.

이러한 엔터티에 대한 *환경 설정* 속성을 채워서 태스크 또는 작업 수준에서 사용자 지정 환경 변수를 설정할 수 있습니다. 예를 들어 [작업에 태스크 추가][rest_add_task] 작업(배치 REST API) 또는 배치 .NET의 [CloudTask.EnvironmentSettings][net_cloudtask_env] 및 [CloudJob.CommonEnvironmentSettings][net_job_env] 속성을 참조하세요.

클라이언트 응용 프로그램 또는 서비스는 [태스크에 대한 정보 가져오기][rest_get_task_info] 작업(배치 REST)을 사용하거나 [CloudTask.EnvironmentSettings][net_cloudtask_env] 속성(배치 .NET)에 액세스하여 태스크의 환경 변수(시스템 정의 및 사용자 정의)를 가져올 수 있습니다. 계산 노드에서 실행되는 프로세스는 친숙한 `%VARIABLE_NAME%`(Windows) 또는 `$VARIABLE_NAME`(Linux) 구문을 사용하여 노드의 다음 환경 변수 및 기타 환경 변수에 액세스할 수 있습니다.

[계산 노드 환경 변수][msdn_env_vars]에서 모든 서비스 정의 환경 변수의 전체 목록을 찾을 수 있습니다.

## <a name="files-and-directories"></a>파일 및 디렉터리
각 태스크에는 *작업 디렉터리* 가 있으며 여기서 0개 이상의 파일 및 디렉터리를 만듭니다. 태스크가 실행하는 프로그램, 처리하는 데이터 및 수행하는 처리의 출력을 저장하는 데 작업 디렉터리를 사용할 수 있습니다. 태스크의 모든 파일 및 디렉터리는 태스크 사용자가 소유합니다.

배치 서비스는 노드의 파일 시스템 일부를 *루트 디렉터리*로 노출합니다. 태스크는 `AZ_BATCH_NODE_ROOT_DIR` 환경 변수를 참조하여 루트 디렉터리에 액세스할 수 있습니다. 환경 변수 사용에 대한 자세한 내용은 [태스크에 대한 환경 설정](#environment-settings-for-tasks)을 참조하세요.

루트 디렉터리는 다음과 같은 디렉터리 구조를 포함합니다.

![계산 노드 디렉터리 구조][1]

* **shared**: 이 디렉터리는 노드에서 실행되는 *모든* 태스크에 대한 읽기/쓰기 액세스를 제공합니다. 노드에서 실행되는 태스크는 이 디렉터리에서 파일을 만들고, 읽고, 업데이트하고, 삭제할 수 있습니다. 태스크는 `AZ_BATCH_NODE_SHARED_DIR` 환경 변수를 참조하여 이 디렉터리에 액세스할 수 있습니다.
* **startup**: 이 디렉터리는 시작 태스크에서 작업 디렉터리로 사용됩니다. 시작 태스크에서 노드에 다운로드한 모든 파일은 여기에 저장됩니다. 시작 태스크는 이 디렉터리 아래에서 파일을 만들고, 읽고, 업데이트하고, 삭제할 수 있습니다. 태스크는 `AZ_BATCH_NODE_STARTUP_DIR` 환경 변수를 참조하여 이 디렉터리에 액세스할 수 있습니다.
* **태스크**: 노드에서 실행되는 각 태스크에 대해 디렉터리가 만들어집니다. `AZ_BATCH_TASK_DIR` 환경 변수를 참조하여 액세스할 수 있습니다.

    각 태스크 디렉터리 내에서 배치 서비스는 `AZ_BATCH_TASK_WORKING_DIR` 환경 변수에 의해 고유 경로가 지정되는 작업 디렉터리(`wd`)를 만듭니다. 이 디렉터리는 태스크에 대한 읽기/쓰기 액세스를 제공합니다. 태스크는 이 디렉터리 아래에서 파일을 만들고, 읽고, 업데이트하고, 삭제할 수 있습니다. 이 디렉터리는 태스크에 대해 지정된 *RetentionTime* 제약 조건에 따라 유지됩니다.

    `stdout.txt` 및 `stderr.txt`: 이러한 파일은 태스크를 실행하는 동안 태스크 폴더에 기록됩니다.

> [!IMPORTANT]
> 풀에서 노드가 제거되면 노드에 저장된 *모든* 파일이 제거됩니다.
>
>

## <a name="application-packages"></a>응용 프로그램 패키지
[응용 프로그램 패키지](batch-application-packages.md) 기능은 풀의 계산 노드에 응용 프로그램을 간편하게 관리 및 배포할 수 있는 방법을 제공합니다. 해당 이진 및 지원 파일을 포함하여 태스크에서 실행하는 여러 버전의 응용 프로그램을 업로드하고 관리할 수 있습니다. 풀의 계산 노드에 이러한 응용 프로그램을 하나 이상 자동으로 배포할 수 있습니다.

풀 및 태스크 수준에서 응용 프로그램 패키지를 지정할 수 있습니다. 풀 응용 프로그램 패키지를 지정하면 응용 프로그램은 풀의 모든 노드에 배포됩니다. 태스크 응용 프로그램 패키지를 지정하면 태스크의 명령줄이 실행되기 전에 작업의 태스크 중 하나에서 실행되도록 예약된 노드에만 응용 프로그램이 배포됩니다.

배치는 세부적인 Azure Storage 작업을 처리하여 응용 프로그램 패키지를 저장하고 계산 노드에 배포합니다. 따라서 코드와 관리 오버헤드가 간소화될 수 있습니다.

응용 프로그램 패키지 기능에 대한 자세한 내용은 [Azure 배치 응용 프로그램 패키지를 사용하여 응용 프로그램 배포](batch-application-packages.md)를 참조하세요.

> [!NOTE]
> *기존* 풀에 풀 응용 프로그램 패키지를 추가하는 경우 노드에 배포될 응용 프로그램 패키지에 대한 해당 계산 노드를 다시 부팅해야 합니다.
>
>

## <a name="pool-and-compute-node-lifetime"></a>풀 및 계산 노드 수명
Azure 배치 솔루션을 설계할 때 풀을 만드는 방법 및 시기와 해당 풀 내의 계산 노드를 사용 가능한 상태로 유지할 기간에 대해 설계를 결정해야 합니다.

하나의 극단적인 예로, 제출하는 각 작업에 대해 풀을 만들고 태스크 실행이 완료되는 즉시 그 풀을 제거할 수 있습니다. 이렇게 하면 노드가 필요할 때만 할당되고 유휴 상태가 되는 즉시 종료되므로 사용률이 최대화됩니다. 이것은 작업이 노드가 할당되기를 기다려야 한다는 것을 의미하지만 노드가 개별적으로 사용이 가능해지고 할당되고 시작 태스크가 완료되는 즉시 태스크의 실행이 예약된다는 점이 중요합니다. Batch는 태스크를 노드에 할당하기 전에 풀 내의 모든 노드를 사용할 수 있을 때까지 기다리지 *않습니다*. 이렇게 하면 사용 가능한 모든 노드의 최대 사용률을 보장합니다.

또 다른 극단적인 예로, 작업을 즉시 시작하는 것이 우선 순위가 가장 높은 경우 미리 풀을 만들고 작업이 제출되기 전에 해당 노드를 사용할 수 있습니다. 이 시나리오에서는 태스크를 즉시 시작할 수 있지만, 태스크가 할당되기를 기다리는 동안 노드가 유휴 상태일 수 있습니다.

통합 접근 방식은 일반적으로 가변적이지만 지속적인 부하를 처리하는 데 사용됩니다. 여러 작업이 제출되는 하나의 풀을 사용하되 작업 부하에 따라 노드 수를 확장하거나 축소할 수 있습니다(다음 섹션에서 [계산 리소스 크기 조정](#scaling-compute-resources) 참조). 이는 현재 부하에 따라 사후 대응적으로 수행하거나 부하를 예측할 수 있는 경우 사전 대응적으로 수행할 수 있습니다.

## <a name="pool-network-configuration"></a>풀 네트워크 구성

Azure Batch에서 계산 노드의 풀을 만드는 경우 풀의 계산 노드를 생성해야 하는 Azure [VNet(가상 네트워크)](https://azure.microsoft.com/documentation/articles/virtual-networks-overview/)의 ID를 지정할 수 있습니다.

* **클라우드 서비스 구성** 풀만이 VNet을 할당할 수 있습니다.

* VNet은 다음과 같아야 합니다.

   * Azure Batch 계정과 동일한 Azure **지역**이어야 합니다.
   * Azure Batch 계정과 동일한 **구독**이어야 합니다.
   * **클래식** VNet이어야 합니다. Azure Resource Manager 배포 모델을 사용하여 만든 VNet은 지원되지 않습니다.

* VNet에는 풀의 `targetDedicated` 속성에 맞도록 사용 가능한 **IP 주소**가 충분히 있어야 합니다. 서브넷에 사용 가능한 IP 주소가 충분하지 않으면 Batch 서비스는 풀에서 계산 노드를 부분적으로 할당하고 크기 조정 오류를 반환합니다.
* *MicrosoftAzureBatch* 서비스 주체에는 지정된 VNet에 대한 [클래식 가상 컴퓨터 참가자](../active-directory/role-based-access-built-in-roles.md#classic-virtual-machine-contributor) RBAC(역할 기반 액세스 제어) 역할이 있어야 합니다. Azure Portal에서 다음을 수행합니다.

  * **VNet**, **액세스 제어(IAM)** > **역할** > **클래식 가상 컴퓨터 참가자** > **추가**를 차례로 선택합니다.
  * **검색** 상자에 "MicrosoftAzureBatch"를 입력합니다.
  * **MicrosoftAzureBatch** 확인란을 확인합니다.
  * **선택** 단추를 선택합니다.

* 계산 노드에 대한 통신이 VNet와 연결된 **NSG(네트워크 보안 그룹)**에서 거부된 경우 Batch 서비스는 계산 노드의 상태를 **사용할 수 없음**으로 설정합니다. 서브넷은 Azure Batch 서비스의 통신이 계산 노드에서 태스크를 예약할 수 있도록 허용해야 합니다.

## <a name="scaling-compute-resources"></a>계산 리소스 크기 조정
[자동 크기 조정](batch-automatic-scaling.md)으로 배치 서비스가 계산 시나리오의 현재 워크로드 및 리소스 사용량에 따라 풀의 계산 노드 수를 동적으로 조정하도록 할 수 있습니다. 그러면 필요한 리소스만을 사용하고 필요하지 않은 리소스를 해제하여 응용 프로그램을 실행하는 전체 비용을 낮출 수 있습니다.

[자동 크기 조정 수식](batch-automatic-scaling.md#automatic-scaling-formulas) 을 작성하고 해당 수식의 풀과 연결하여 자동 크기 조정을 사용하도록 설정합니다. 배치 서비스는 이 수식을 사용하여 다음 크기 조정 간격(구성할 수 있는 간격)에 대한 풀의 노드 대상 수를 확인합니다. 풀을 만들 때 자동 크기 조정 설정을 지정하거나 나중에 풀에 자동 크기 조정을 사용하도록 설정할 수 있습니다. 크기 조정을 사용하는 풀에 크기 조정 설정을 업데이트할 수도 있습니다.

예를 들어 작업에서는 실행될 많은 태스크를 제출해야 합니다. 현재 큐에 대기된 태스크 수 및 작업에서 이러한 태스크의 완료율에 따라 풀의 노드 수를 조정하는 크기 조정 수식을 풀에 할당할 수 있습니다. 배치 서비스는 수식을 정기적으로 계산하고 워크로드 및 다른 수식 설정에 따라 풀 크기를 조정합니다. 서비스는 큐에 대기 중인 태스크 수가 많은 경우 필요에 따라 노드를 추가하고, 큐에 대기 중이거나 실행 중인 태스크가 없는 경우 노드를 제거합니다. 

크기 조정 수식은 다음 메트릭을 기반으로 할 수 있습니다.

* **시간 메트릭** 은 지정된 시간 동안 5분 간격으로 수집되는 통계를 기반으로 합니다.
* **리소스 메트릭** 은 CPU 사용량, 대역폭 사용량, 메모리 사용량 및 노드 수를 기반으로 합니다.
* **태스크 메트릭**은 *활성*(큐에 대기), *실행* 또는 *완료* 등 태스크 상태에 따라 다릅니다.

자동 크기 조정이 풀에서 계산 노드 수를 감소시키면 감소 작업 시에 실행 중인 태스크를 처리하는 방법을 고려해야 합니다. 이를 제공하기 위해 배치가 수식에 포함할 수 있는 *노드 할당 취소 옵션* 을 제공합니다. 예를 들어 실행 중인 태스크를 즉시 중지하거나 즉시 중지한 다음 다른 노드에서 실행하기 위해 다시 큐에 대기하거나 풀에서 노드를 제거하기 전에 완료하도록 지정할 수 있습니다.

자동으로 응용 프로그램 크기를 조정하는 방법에 대한 자세한 내용은 [Azure 배치 풀에서 자동으로 계산 노드 크기 조정](batch-automatic-scaling.md)을 참조하세요.

> [!TIP]
> 계산 리소스 사용률을 극대화하기 위해 작업 끝에서 목표 노드 수를 0으로 설정하지만 실행 중인 태스크를 완료할 수 있도록 합니다.
>
>

## <a name="security-with-certificates"></a>인증서를 사용한 보안
일반적으로 태스크에 대한 중요한 정보(예: [Azure Storage 계정][azure_storage]의 키)를 암호화하거나 암호를 해독할 때는 인증서를 사용해야 합니다. 이를 지원하려면 노드에 인증서를 설치할 수 있습니다. 암호화된 암호는 명령줄 매개 변수를 통해 태스크에 전달되거나 태스크 리소스 중 하나에 포함되며 암호를 해독하는 데 설치된 인증서를 사용할 수 있습니다.

[인증서 추가][rest_add_cert] 작업(배치 REST) 또는 [CertificateOperations.CreateCertificate][net_create_cert] 메서드(배치 .NET)를 사용하여 배치 계정에 인증서를 추가할 수 있습니다. 그런 다음 인증서를 기존 풀이나 새 풀에 연결할 수 있습니다. 인증서가 풀에 연결되면 배치 서비스가 풀의 각 노드에 인증서를 설치합니다. 배치 서비스는 노드가 시작되면 시작 태스크 및 작업 관리자 태스크를 포함하여 모든 태스크를 시작하기 전에 해당 인증서를 설치합니다.

*기존* 풀에 풀 인증서를 추가하는 경우 노드에 적용될 인증서에 대한 해당 계산 노드를 다시 부팅해야 합니다.

## <a name="error-handling"></a>오류 처리
배치 솔루션 내에서 태스크 오류와 응용 프로그램 오류를 모두 처리해야 할 수 있습니다.

### <a name="task-failure-handling"></a>태스크 오류 처리
태스크 오류는 다음 범주로 분류됩니다.

* **예약 오류**

    태스크에 대해 지정된 파일의 전송이 어떤 이유로든 실패한 경우 해당 태스크에 대해 *예약 오류*가 설정됩니다.

    태스크의 리소스 파일이 이동되었거나, 저장소 계정을 더 이상 사용할 수 없거나, 노드에 파일을 복사하지 못하도록 하는 다른 문제가 발생하는 경우 예약 오류가 발생할 수 있습니다.
* **응용 프로그램 오류**

    태스크의 명령줄에서 지정된 프로세스도 실패할 수 있습니다. 태스크에 의해 실행된 프로세스에서 0이 아닌 종료 코드가 반환되면 프로세스가 실패한 것으로 간주됩니다(다음 섹션에서 *태스크 종료 코드* 참조).

    응용 프로그램 오류의 경우 지정된 횟수까지 자동으로 태스크를 다시 시도하도록 배치를 구성할 수 있습니다.
* **제약 조건 오류**

    작업 또는 태스크의 최대 실행 기간을 지정하는 제약 조건( *maxWallClockTime*)을 지정할 수 있습니다. 이 기능은 작동되지 않는 태스크를 종료하는 데 유용할 수 있습니다.

    최대 기간을 초과하면 태스크가 *완료됨*으로 표시되지만 종료 코드가 `0xC000013A`로 설정되고 *schedulingError* 필드가 `{ category:"ServerError", code="TaskEnded"}`로 표시됩니다.

### <a name="debugging-application-failures"></a>응용 프로그램 오류 디버그
* `stderr` 및 `stdout`

    실행하는 동안 응용 프로그램에서 문제를 해결하는 데 사용할 수 있는 진단 출력을 생성할 수 있습니다. 앞서 [파일 및 디렉터리](#files-and-directories) 섹션에서 설명했듯이, 배치 서비스는 계산 노드의 태스크 디렉터리에 있는 `stdout.txt` 및 `stderr.txt` 파일에 표준 출력 및 표준 오류 출력을 작성합니다. Azure 포털 또는 배치 SDK 중 하나를 사용하여 이러한 파일을 다운로드할 수 있습니다. 예를 들어, 배치 .NET API에서 [ComputeNode.GetNodeFile][net_getfile_node] 및 [CloudTask.GetNodeFile][net_getfile_task]을 사용하여 문제 해결을 위해 이러한 파일 및 다른 파일을 검색할 수 있습니다.
* **태스크 종료 코드**

    앞서 설명했듯이 태스크에서 실행하는 프로세스가 0이 아닌 종료 코드를 반환하는 경우 태스크는 배치 서비스에서 실패했다고 표시됩니다. 태스크가 프로세스를 실행하는 경우 배치는 *프로세스의 반환 코드*를 사용하여 태스크의 종료 코드 속성을 채웁니다. 태스크의 종료 코드는 배치 서비스에 의해 결정되지 **않는**다는 점에 주의해야 합니다. 태스크의 종료 코드는 프로세스 자체 또는 프로세스가 실행되는 운영 체제에 의해 결정됩니다.

### <a name="accounting-for-task-failures-or-interruptions"></a>태스크 실패 또는 중단에 대한 조정
간혹 태스크가 실패하거나 중단될 수 있습니다. 태스크 응용 프로그램 자체가 실패하거나, 태스크를 실행 중인 노드가 다시 부팅되거나, 풀의 할당 취소 정책이 태스크가 완료되기를 기다리지 않고 즉시 노드를 제거하도록 설정된 상태에서 크기 조정 작업 중에 풀에서 노드가 제거되었을 수 있습니다. 어떤 경우든지 태스크는 다른 노드에서 실행되도록 배치에 의해 자동으로 큐에 다시 대기할 수 있습니다.

일시적인 문제로 인해 태스크가 응답하지 않거나 실행하는 데 너무 오래 걸릴 수도 있습니다. 태스크에 대한 최대 실행 간격을 설정할 수 있습니다. 최대 실행 간격을 초과하면 배치 서비스가 태스크 응용 프로그램을 중단합니다.

### <a name="connecting-to-compute-nodes"></a>계산 노드 연결
계산 노드에 원격으로 로그인하여 추가 디버깅 및 문제 해결을 수행할 수 있습니다. Azure 포털을 사용하여 Windows 노드에 RDP(원격 데스크톱 프로토콜) 파일을 다운로드하고 Linux 노드에 SSH(Secure Shell) 연결 정보를 가져올 수 있습니다. 또한 [배치 .NET][net_rdpfile] 또는 [배치 Python](batch-linux-nodes.md#connect-to-linux-nodes)과 같은 배치 API를 사용하여 수행할 수 있습니다.

> [!IMPORTANT]
> RDP 또는 SSH를 통해 노드에 연결하려면 먼저 해당 노드에서 사용자를 만들어야 합니다. 이렇게 하려면 Azure Portal을 사용할 수 있습니다. 배치 REST API를 사용하여 [노드에 사용자 계정을 추가][rest_create_user]하거나 배치 .NET에서 [ComputeNode.CreateComputeNodeUser][net_create_user] 메서드를 호출하거나 배치 Python 모듈에서 [add_user][py_add_user] 메서드를 호출합니다.
>
>

### <a name="troubleshooting-problematic-compute-nodes"></a>문제가 있는 계산 노드 문제 해결
태스크가 실패한 경우 배치 클라이언트 응용 프로그램 또는 서비스는 실패한 작업의 메타데이터를 검사하여 오동작 노드를 식별할 수 있습니다. 풀의 각 노드에는 고유 ID가 지정되며, 태스크가 실행되는 노드는 태스크 메타데이터에 포함됩니다. 문제 노드를 식별하면 다음과 같은 몇 가지 작업을 수행할 수 있습니다.

* **노드 다시 시작**([REST][rest_reboot] | [.NET][net_reboot])

    노드를 다시 시작하면 경우에 따라 충돌 또는 중단 프로세스와 같은 잠재적인 문제를 해소할 수 있습니다. 풀에서 시작 태스크를 사용하거나 작업에서 준비 태스크를 사용하는 경우 노드가 다시 시작될 때 실행됩니다.
* **노드 이미지로 다시 설치**([REST][rest_reimage] | [.NET][net_reimage])

    노드에서 운영 체제를 다시 설치합니다. 노드를 다시 시작할 때와 마찬가지로 태스크를 시작하고 노드가 이미지로 다시 설치된 후에 작업 준비 태스크를 다시 실행합니다.
* **풀에서 노드 제거**([REST][rest_remove] | [.NET][net_remove])

    경우에 따라 풀에서 노드를 완전히 제거해야 합니다.
* **노드에서 태스크 일정 사용 안 함**([REST][rest_offline] | [.NET][net_offline])

    사실상 노드를 오프라인으로 만들기 때문에 해당 노드에 더 이상 작업이 할당되지 않지만 노드가 풀에서 실행되도록 유지할 수 있습니다. 그러면 실패한 태스크에 데이터 손실이나 노드에 추가 작업 오류를 발생시키지 않고 실패의 원인을 조사할 수 있습니다. 예를 들어 노드에서 태스크 일정을 사용하지 않은 다음 [원격으로 로그인](#connecting-to-compute-nodes) 하여 노드의 이벤트 로그를 검사하거나 다른 문제를 해결할 수 있습니다. 조사를 완료하면 태스크 일정([REST][rest_online] | [.NET][net_online])을 사용하여 노드를 다시 온라인 상태로 되돌리거나 앞서 설명한 다른 작업 중 하나를 수행할 수 있습니다.

> [!IMPORTANT]
> 태스크 일정을 다시 시작, 이미지로 다시 설치, 제거, 사용 안 함 등 이 섹션에서 설명한 작업으로 동작을 수행할 때 노드에서 현재 실행 중인 태스크를 처리하는 방법을 지정할 수 있습니다. 예를 들어 배치 .NET 클라이언트 라이브러리를 사용하여 노드에서 태스크 일정을 사용하지 않으면 [DisableComputeNodeSchedulingOption][net_offline_option] 열거형 값을 지정하여 실행 중인 태스크를 **종료**하거나, 다른 노드에서 예약을 **다시 대기**하거나 또는 작업(**TaskCompletion**)을 수행하기 전에 완료할 태스크 실행을 허용할지를 지정할 수 있습니다.
>
>

## <a name="next-steps"></a>다음 단계
* Batch 솔루션을 빌드하는 데 사용할 수 있는 [Batch API 및 도구](batch-apis-tools.md)에 대해 알아봅니다.
* [.NET용 Azure 배치 라이브러리 시작](batch-dotnet-get-started.md)에서 샘플 배치 응용 프로그램을 단계별로 안내합니다. 또한 Linux 계산 노드에서 워크로드를 실행하는 [Python 버전](batch-python-tutorial.md) 자습서가 있습니다.
* 배치 솔루션을 개발하는 동안 사용할 [Batch Explorer][github_batchexplorer] 샘플 프로젝트를 다운로드하여 빌드합니다. Batch Explorer를 사용하여 다음과 같은 작업을 수행할 수 있습니다.

  * 배치 계정 내에서 풀, 작업 및 태스크 모니터링 및 조작
  * 노드에서 `stdout.txt`, `stderr.txt` 및 기타 파일 다운로드
  * 노드에서 사용자를 만들고 원격 로그인을 위한 RDP 파일 다운로드
* [Linux 계산 노드의 풀을 만드는](batch-linux-nodes.md)방법을 알아봅니다.
* MSDN에서 [Azure 배치 포럼][batch_forum] 을 방문하세요. 포럼에서는 배치를 배우는지 아니면 배치를 사용하는 전문가인지를 질문합니다.

[1]: ./media/batch-api-basics/node-folder-structure.png

[azure_storage]: https://azure.microsoft.com/services/storage/
[batch_forum]: https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurebatch
[cloud_service_sizes]: ../cloud-services/cloud-services-sizes-specs.md
[msmpi]: https://msdn.microsoft.com/library/bb524831.aspx
[github_samples]: https://github.com/Azure/azure-batch-samples
[github_sample_taskdeps]:  https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/TaskDependencies
[github_batchexplorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[batch_net_api]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[msdn_env_vars]: https://msdn.microsoft.com/library/azure/mt743623.aspx
[net_cloudjob_jobmanagertask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.jobmanagertask.aspx
[net_cloudjob_priority]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.priority.aspx
[net_cloudpool_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.starttask.aspx
[net_cloudtask_env]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.environmentsettings.aspx
[net_create_cert]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.certificateoperations.createcertificate.aspx
[net_create_user]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.createcomputenodeuser.aspx
[net_getfile_node]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.getnodefile.aspx
[net_getfile_task]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.getnodefile.aspx
[net_job_env]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.commonenvironmentsettings.aspx
[net_multiinstancesettings]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.multiinstancesettings.aspx
[net_onalltaskscomplete]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.onalltaskscomplete.aspx
[net_rdp]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.getrdpfile.aspx
[net_reboot]: https://msdn.microsoft.com/library/azure/mt631495.aspx
[net_reimage]: https://msdn.microsoft.com/library/azure/mt631496.aspx
[net_remove]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.removefrompoolasync.aspx
[net_offline]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.disableschedulingasync.aspx
[net_online]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.enableschedulingasync.aspx
[net_offline_option]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.common.disablecomputenodeschedulingoption.aspx
[net_rdpfile]: https://msdn.microsoft.com/library/azure/Mt272127.aspx
[vnet]: https://msdn.microsoft.com/library/azure/dn820174.aspx#bk_netconf

[py_add_user]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.operations.html#azure.batch.operations.ComputeNodeOperations.add_user

[batch_rest_api]: https://msdn.microsoft.com/library/azure/Dn820158.aspx
[rest_add_job]: https://msdn.microsoft.com/library/azure/mt282178.aspx
[rest_add_pool]: https://msdn.microsoft.com/library/azure/dn820174.aspx
[rest_add_cert]: https://msdn.microsoft.com/library/azure/dn820169.aspx
[rest_add_task]: https://msdn.microsoft.com/library/azure/dn820105.aspx
[rest_create_user]: https://msdn.microsoft.com/library/azure/dn820137.aspx
[rest_get_task_info]: https://msdn.microsoft.com/library/azure/dn820133.aspx
[rest_job_schedules]: https://msdn.microsoft.com/library/azure/mt282179.aspx
[rest_multiinstance]: https://msdn.microsoft.com/library/azure/mt637905.aspx
[rest_multiinstancesettings]: https://msdn.microsoft.com/library/azure/dn820105.aspx#multiInstanceSettings
[rest_update_job]: https://msdn.microsoft.com/library/azure/dn820162.aspx
[rest_rdp]: https://msdn.microsoft.com/library/azure/dn820120.aspx
[rest_reboot]: https://msdn.microsoft.com/library/azure/dn820171.aspx
[rest_reimage]: https://msdn.microsoft.com/library/azure/dn820157.aspx
[rest_remove]: https://msdn.microsoft.com/library/azure/dn820194.aspx
[rest_offline]: https://msdn.microsoft.com/library/azure/mt637904.aspx
[rest_online]: https://msdn.microsoft.com/library/azure/mt637907.aspx

[vm_marketplace]: https://azure.microsoft.com/marketplace/virtual-machines/

