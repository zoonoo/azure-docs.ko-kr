---
title: Azure 모니터의 간단한 로그 환경(미리 보기) | 마이크로 소프트 문서
description: 간단한 로그 환경을 사용하면 KQL과 직접 상호 작용하지 않고도 Azure Monitor에서 기본 쿼리를 만들 수 있습니다.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/12/2019
ms.openlocfilehash: 59bcb42edaf7d46498a3514b4f1c919c6e8cc0c4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77660260"
---
# <a name="simple-logs-experience-in-azure-monitor-preview"></a>Azure 모니터의 간단한 로그 환경(미리 보기)
Azure Monitor는 KQL 언어를 사용하여 [로그 쿼리를](log-query-overview.md) 만들기 위한 [풍부한 환경을](get-started-portal.md) 제공합니다. 하지만 KQL의 전체 기능을 필요로 하지 않을 수 있습니다 및 기본 쿼리 요구 사항에 대 한 단순화 된 환경을 선호 합니다. 간단한 로그 환경을 사용하면 KQL과 직접 상호 작용하지 않고도 기본 쿼리를 만들 수 있습니다. 더 정교한 쿼리가 필요하므로 단순 로그를 KQL학습 도구로 사용할 수도 있습니다.

> [!NOTE]
> 간단한 로그는 현재 코스모스 DB와 키 볼트에 대한 테스트로 구현됩니다. [사용자 음성을](https://feedback.azure.com/forums/913690-azure-monitor) 통해 Microsoft와 사용자 환경을 공유하여 이 기능을 확장하고 릴리스할지 여부를 결정하십시오.


## <a name="scope"></a>Scope
단순 로그 환경은 선택한 리소스에 대한 *AzureDiagnostics,* AzureMetrics 및 *AzureActivity* 테이블에서 데이터를 *검색합니다.* 

## <a name="using-simple-logs"></a>간단한 로그 사용
[로그 분석 작업 영역에서 로그를 수집하도록 구성된 진단 설정을](../platform/resource-logs-collect-storage.md)사용하여 Azure 구독의 Cosmos DB 또는 키 자격 증명 모음으로 이동합니다. **모니터링** 메뉴에서 **로그를** 클릭하여 간단한 로그 환경을 엽니다.

![메뉴](media/simple-logs/menu.png)

**필드와** **연산자를** 선택하고 비교할 **값을** 지정합니다. 추가 **+** 조건을 추가하려면 **And/Or를** 클릭하고 지정합니다.

![조건](media/simple-logs/criteria.png)

**실행을** 클릭하여 쿼리 결과를 봅니다.

## <a name="view-and-edit-kql"></a>KQL 보기 및 편집
전체 로그 분석 환경에서 단순 로그 쿼리에서 생성된 KQL을 열려면 **쿼리 편집기를** 선택합니다. 

![쿼리 편집기](media/simple-logs/query-editor.png)

KQL을 직접 편집하고 필터와 같은 로그 분석의 다른 기능을 사용하여 결과를 더욱 구체화할 수 있습니다.

![KQL 편집](media/simple-logs/edit-kql.png)


## <a name="next-steps"></a>다음 단계

- [Azure 포털에서 로그 분석 사용에 대한 자습서를 완료합니다.](get-started-portal.md)
- [로그 쿼리 작성에 대한 자습서를 완료합니다.](get-started-portal.md)
