---
title: "Azure Batch의 서비스 할당량 및 제한 | Microsoft Docs"
description: "기본 Azure 배치 할당량, 한도 및 제약 조건에 대해 알아보고 할당량 증가를 요청하는 방법에 대해 알아봅니다."
services: batch
documentationcenter: 
author: tamram
manager: timlt
editor: 
ms.assetid: 28998df4-8693-431d-b6ad-974c2f8db5fb
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2017
ms.author: tamram
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 3716c7699732ad31970778fdfa116f8aee3da70b
ms.openlocfilehash: f3f69ed8d3a985afe07e648e7512a88b25278ced
ms.contentlocale: ko-kr
ms.lasthandoff: 06/30/2017


---
# <a name="batch-service-quotas-and-limits"></a>배치 서비스 할당량 및 제한

다른 Azure 서비스와 마찬가지로 배치 서비스와 관련하여 특정 리소스에 대한 제한이 있습니다. 이러한 제한 대부분은 Azure 구독 또는 계정 수준에서 적용되는 기본 할당량입니다. 이 문서는 그러한 기본값을 설명하고 할당량 증가를 요청하는 방법을 설명합니다.

배치 워크로드를 디자인하고 강화할 때 이 할당량에 주의합니다. 예를 들어, 풀이 지정한 계산 노드의 대상 수에 도달하지 않는 경우 배치 계정의 주요 할당량 한도 또는 구독에 대한 지역별 VM 코어 할당량에 도달했을 수 있습니다.

단일 배치 계정에서 여러 배치 워크로드를 실행하거나 다른 Azure 지역이 아닌 동일한 구독에 있는 배치 계정 간에 워크로드를 배포할 수 있습니다.

배치에서 프로덕션 작업을 실행하려고 계획하는 경우, 위 기본값의 할당량 중 두 개 이상을 늘려야 할 수 있습니다. 할당량을 늘리려면 무료 온라인 [고객지원 요청](#increase-a-quota) 을 개설합니다.

> [!NOTE]
> 할당량은 신용 한도액일 뿐이며 용량을 보장하는 것은 아닙니다. 대규모 용량이 필요한 경우 Azure 지원에 문의하세요.
> 
> 

## <a name="resource-quotas"></a>리소스 할당량
[!INCLUDE [azure-batch-limits](../../includes/azure-batch-limits.md)]

## <a name="quotas-in-user-subscription-mode"></a>사용자 구독 모드에서 할당량

풀 할당 모드가 **사용자 구독**으로 설정된 배치 계정의 경우 풀이 만들어질 때 배치 VM 및 기타 리소스(예: 저장소 계정)가 구독에 직접 생성됩니다. Azure Batch 코어 할당량은 이 모드에서 생성된 계정에는 적용되지 않습니다. 대신, 지역별 계산 코어 및 기타 리소스에 대한 구독의 할당량이 적용됩니다. [Azure 구독 및 서비스 제한, 할당량 및 제약 조건](../azure-subscription-service-limits.md)에서 이러한 할당량에 대해 자세히 알아보세요.

사용자 구독 모드에서 만든 계정에 대한 리소스 사용을 계획 중인 경우 Linux VM 40대 또는 Windows VM 20대마다 계산 코어 외에도 다음 Batch 리소스가 필요합니다.

| 리소스 | 할당량 | 공급자 |
| --- | ---| --- |
| 저장소 계정 1개 | 저장소 계정 | Microsoft.Storage |
| 공용 IP 주소 1개 | 공용 IP 주소 | Microsoft.Network | 
| 가상 네트워크 1개 | 가상 네트워크 | Microsoft.Network | 
| 네트워크 보안 그룹 1개 | 네트워크 보안 그룹 | Microsoft.Network | 
| 가상 컴퓨터 확장 집합 1개 | 가상 컴퓨터 확장 집합 | Microsoft.Compute | 
| 부하 분산 장치 1개 | 부하 분산 장치 | Microsoft.Network | 

지역 수준 또는 VM 제품군별 코어 할당량은 Batch 풀 또는 풀에 필요한 VM 크기에 따라 설정해야 합니다.

| 할당량 | 공급자 |
| --- | ---- |
| 총 지역별 코어 수 | Microsoft.Compute |
| … 제품군 코어 수 | Microsoft.Compute |



## <a name="other-limits"></a>기타 제한
| **리소스** | **최대 제한** |
| --- | --- |
| [동시 작업](batch-parallel-node-tasks.md)  |4 x 노드 코어 수 |
| [응용 프로그램](batch-application-packages.md)  |20 |
| 응용 프로그램당 응용 프로그램 패키지 |40 |
| 각 응용 프로그램 패키지 크기 |약 195GB<sup>1</sup> |
| 최대 시작 태스크 크기 | 32768자<sup>2</sup> |

<sup>1</sup> 최대 블록 Blob 크기에 대한 Azure 저장소 용량 한도<br />
<sup>2</sup> 리소스 파일 및 환경 변수를 포함합니다.

## <a name="view-batch-quotas"></a>배치 할당량 보기
[Azure 포털][portal]에서 Batch 계정 할당량을 봅니다.

1. 포털에서 **배치 계정** 을 선택한 다음 관심 있는 배치 계정을 선택합니다.
2. 배치 계정의 메뉴 블레이드에서 **속성**을 선택합니다.
3. 속성 블레이드에서 현재 배치 계정에 적용되는 **할당량** 을 표시합니다.
   
    ![배치 계정 할당량][account_quotas]

사용자 구독 모드에서 생성된 배치 계정에 대해 Azure Portal에서 관련 구독 할당량을 확인합니다.

1. **구독**을 선택하고 배치 계정에 사용할 구독을 선택합니다.

2. **구독** 블레이드에서 **사용량 + 할당량**을 선택합니다.



## <a name="increase-a-quota"></a>할당량 증가
[Azure Portal][portal]을 사용하여 배치 계정 또는 구독에 대해 할당량 증가를 요청하려면 다음 단계를 수행합니다. 할당량 증가 유형은 배치 계정의 풀 할당 모드에 따라 다릅니다.

### <a name="increase-a-batch-cores-quota"></a>배치 코어 할당량 증가 

배치 계정이 **Batch 서비스** 모드에서 생성된 경우 다음 단계에 따라 Batch 코어 할당량 증가를 요청합니다.

1. 포털 대시보드에서 **도움말 + 지원** 타일을 선택하거나 포털 오른쪽 위 모서리에 있는 물음표(**?**)를 선택합니다.
2. **새 기본 지원 요청** > **기본**을 클릭합니다.
3. **기본 사항** 블레이드:
   
    a. **문제 형식** > **할당량**
   
    b. 사용 중인 구독을 선택합니다.
   
    c. **할당량 유형** > **배치**
   
    d. **지원 계획** > **할당량 지원 - 포함됨**
   
    **다음**을 누릅니다.
4. **문제** 블레이드:
   
    a. [비즈니스 영향][support_sev]에 따라 **심각도**를 선택합니다.
   
    b. **세부 정보**에서 변경하려는 각 할당량과 배치 계정 이름, 새로운 제한을 지정합니다.
   
    **다음**을 누릅니다.
5. **연락처 정보** 블레이드:
   
    a. **기본 연락 방법**을 선택합니다.
   
    b. 필수 연락처 세부 정보를 확인하고 입력합니다.
   
    **만들기** 를 클릭하여 지원 요청을 제출합니다.

지원 요청을 제출하면 Azure 지원 팀에서 연락을 드릴 것입니다. 참고로 요청 완료까지 업무일 기준 최대 2일이 걸릴 수 있습니다.

### <a name="increase-a-subscription-cores-quota"></a>구독 코어 할당량 증가

배치 계정이 **사용자 구독** 모드에서 생성되었으며 추가 지역 또는 VM 제품군 코어가 필요한 경우 구독에서 할당량 증가를 요청합니다. 해당 단계는 [Resource Manager 코어 할당량 증가 요청](../azure-supportability/resource-manager-core-quotas-request.md)을 참조하세요.



## <a name="related-topics"></a>관련된 항목
* [Azure 포털을 사용하여 Azure 배치 계정 만들기](batch-account-create-portal.md)
* [Azure 배치 기능 개요](batch-api-basics.md)
* [Azure 구독 및 서비스 제한, 할당량 및 제약 조건](../azure-subscription-service-limits.md)

[portal]: https://portal.azure.com
[portal_classic_increase]: https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/
[support_sev]: http://aka.ms/supportseverity

[account_quotas]: ./media/batch-quota-limit/accountquota_portal.PNG

