---
title: Azure Batch AI의 서비스 할당량 및 한도 | Microsoft Docs
description: 기본 Azure Batch AI 할당량, 한도 및 제약 조건에 대해 알아보고 할당량 증가를 요청하는 방법을 알아봅니다.
services: batch-ai
documentationcenter: ''
author: johnwu10
manager: jeconnoc
editor: ''
ms.service: batch-ai
ms.topic: article
ms.date: 08/08/2018
ms.author: danlep
ms.custom: mvc
ROBOTS: NOINDEX
ms.openlocfilehash: 1f9bd8503c2e8f2aa3c54d41b8585c6fbf81c92b
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/07/2019
ms.locfileid: "57542087"
---
# <a name="batch-ai-service-quotas-and-limits"></a>Batch AI 서비스 할당량 및 한도

[!INCLUDE [batch-ai-retiring](../../includes/batch-ai-retiring.md)]

다른 Azure 서비스와 마찬가지로 Batch AI 서비스와 관련하여 특정 리소스에 대한 한도가 있습니다. Batch AI에서 이러한 한도는 서비스를 [사용할 수 있는](https://azure.microsoft.com/global-infrastructure/services/) 각 지역에 대해 구독 수준에서 적용되는 기본 할당량입니다. 이 문서는 그러한 기본값을 설명하고 할당량 증가를 요청하는 방법을 설명합니다.

Batch AI 리소스를 설계 및 강화할 때 이 할당량에 주의합니다. 예를 들어 클러스터가 지정된 대상 노드 수에 도달하지 않으면 구독의 Batch AI 코어 한도에 도달한 것일 수 있습니다.

Batch AI에서 프로덕션 작업을 실행하려고 계획 중인 경우, 위 기본값의 할당량 중 두 개 이상을 늘려야 할 수 있습니다.

> [!NOTE]
> 할당량은 신용 한도액일 뿐이며 용량을 보장하는 것은 아닙니다. 대규모 용량이 필요한 경우 Azure 지원에 문의하세요.
> 
> 

## <a name="resource-quotas"></a>리소스 할당량

[!INCLUDE [azure-batch-ai-limits](../../includes/azure-batch-ai-limits.md)]

## <a name="other-limits"></a>기타 제한

다음은 적중되고 나면 초과할 수 없는 엄격한 한도입니다.

| **리소스** | **최대 한도** |
| --- | --- |
| 리소스 그룹당 최대 작업 영역 | 800 |
| 최대 클러스터 크기 | 100개 노드 |
| 노드당 최대 GPU MPI 프로세스 | 1-4 |
| 노드당 최대 GPU 작업자 수 | 1-4 |
| 최대 작업 수명 | 7일<sup>1</sup> |
| 노드당 최대 매개 변수 서버 수 | 1 |

<sup>1</sup> 최대 수명은 작업 실행이 시작되는 시간과 완료되는 시간을 나타냅니다. 완료된 작업은 무기한 저장됩니다. 최대 수명 이내에 완료되지 않은 작업에 대한 데이터는 액세스할 수 없습니다.

## <a name="view-batch-ai-quotas"></a>Batch AI 할당량 보기

[Azure Portal][portal]에서 현재 Batch AI 구독 할당량을 봅니다.

1. 왼쪽 창에서 **모든 서비스**를 클릭합니다. 그런 다음, **Batch AI**를 검색하고 서비스를 클릭하여 엽니다.
2. Batch AI 메뉴에서 **사용량 + 할당량**을 클릭합니다.
3. 구독을 선택하여 할당량 한도를 봅니다.

## <a name="increase-a-batch-ai-cores-quota"></a>Batch AI 코어 할당량 증가

[Azure Portal][portal]을 사용하여 Batch AI 구독에 대한 할당량 증가를 요청하려면 다음 단계를 따르세요. 

1. 왼쪽 창에서 **모든 서비스**를 클릭합니다. 그런 다음, **Batch AI**를 검색하고 서비스를 클릭하여 엽니다.
2. Batch AI 메뉴에서 **새 지원 요청**을 클릭합니다.
3. **기본 사항**에서
   
    a. **문제 형식** > **할당량**
   
    b. **구독** > 해당 구독을 선택합니다.
   
    다. **할당량 유형** > **Batch AI**
   
    d. **지원 계획** > 해당 지원 계획을 선택합니다.

    **다음**을 클릭합니다.
4. **문제**에서
   
    a. [비즈니스 영향][support_sev]에 따라 **심각도**를 선택합니다.
   
    b. **할당량 세부 정보**에서 위치, 할당량 유형 및 리소스 유형을 지정합니다. 요청할 새 한도를 지정합니다. **저장 후 계속**을 클릭합니다.

    다. 선택 사항 - 증가 이유에 대한 자세한 정보가 포함된 관련 파일을 업로드합니다.
   
    **다음**을 클릭합니다.
5. **연락처 정보**에서
   
    a. **기본 연락 방법**을 선택합니다.
   
    b. 필수 연락처 세부 정보를 확인하고 입력합니다.
   
    **만들기** 를 클릭하여 지원 요청을 제출합니다.

지원 요청을 제출하면 Azure 지원 팀에서 연락을 드릴 것입니다. 요청 완료까지 업무일 기준 최대 2일이 걸릴 수 있습니다.


## <a name="next-steps"></a>다음 단계

할당량 한도에 익숙해지면 Batch AI를 사용하여 시작하는 방법에 대한 다음 문서를 확인합니다.

> [!div class="nextstepaction"]
> [Batch AI 빠른 시작 자습서](quickstart-tensorflow-training-cli.md)
> [Batch AI 레시피](https://github.com/Azure/BatchAI/tree/master/recipes)
> [Batch AI 리소스에 대한 자세한 정보](resource-concepts.md)

[portal]: https://portal.azure.com
[support_sev]: https://aka.ms/supportseverity