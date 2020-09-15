---
title: Azure Monitor (미리 보기)의 간단한 로그 환경 | Microsoft Docs
description: 단순 로그 환경에서는 KQL와 직접 상호 작용 하지 않고도 Azure Monitor에서 기본 쿼리를 만들 수 있습니다.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/12/2019
ms.openlocfilehash: 4926e18aa6b00fe36608843ea5253903ace774e2
ms.sourcegitcommit: 07166a1ff8bd23f5e1c49d4fd12badbca5ebd19c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/15/2020
ms.locfileid: "90089111"
---
# <a name="simple-logs-experience-in-azure-monitor-preview"></a>Azure Monitor의 간단한 로그 환경 (미리 보기)
Azure Monitor KQL 언어를 사용 하 여 [로그 쿼리](log-query-overview.md) 를 만들기 위한 [다양 한 환경을](get-started-portal.md) 제공 합니다. KQL의 전체 성능이 필요 하지 않을 수 있으며 기본 쿼리 요구 사항에 대 한 간단한 환경을 선호 합니다. 간단한 로그 환경에서는 KQL와 직접 상호 작용 하지 않고도 기본 쿼리를 만들 수 있습니다. 보다 정교한 쿼리가 필요 하므로 간단한 로그를 KQL에 대 한 학습 도구로 사용할 수도 있습니다.

> [!NOTE]
> 단순 로그는 현재 Cosmos DB 및 키 자격 증명 모음에 대 한 테스트로만 구현 됩니다. [사용자](https://feedback.azure.com/forums/913690-azure-monitor) 의견을 통해 Microsoft와 경험을 공유 하 여이 기능을 확장 하 고 릴리스할 지 여부를 확인 하는 데 도움을 주세요.


## <a name="scope"></a>범위
단순 로그 환경은 선택한 리소스의 *Azurediagnostics*, *Azurediagnostics*및 *azurediagnostics* 테이블에서 데이터를 검색 합니다. 

## <a name="using-simple-logs"></a>단순 로그 사용
[Log Analytics 작업 영역에서 로그를 수집 하도록 구성 된 진단 설정을](../platform/resource-logs.md#send-to-azure-storage)사용 하 여 Azure 구독에서 Cosmos DB 또는 Key Vault로 이동 합니다. **모니터링** 메뉴에서 **로그** 를 클릭 하 여 단순 로그 환경을 엽니다.

![로그를 선택 하 여 모니터링 메뉴를 보여 주는 스크린샷](media/simple-logs/menu.png)

**필드** 와 **연산자** 를 선택 하 고 비교할 **값** 을 지정 합니다. 를 클릭 하 **+** 고 **및/또는** 를 지정 하 여 추가 조건을 추가 합니다.

![로그 창에서 단순 로그가 선택 된 검색을 보여 주는 스크린샷](media/simple-logs/criteria.png)

**실행** 을 클릭 하 여 쿼리 결과를 확인 합니다.

## <a name="view-and-edit-kql"></a>KQL 보기 및 편집
**쿼리 편집기** 를 선택 하 여 전체 Log Analytics 환경에서 단순 로그 쿼리에 의해 생성 된 KQL를 엽니다. 

![쿼리 편집기](media/simple-logs/query-editor.png)

KQL를 직접 편집 하 고 필터와 같은 Log Analytics의 다른 기능을 사용 하 여 결과를 더 구체화할 수 있습니다.

![KQL 편집](media/simple-logs/edit-kql.png)


## <a name="next-steps"></a>다음 단계

- [Azure Portal에서 Log Analytics를 사용 하는 방법](get-started-portal.md)에 대 한 자습서를 완료 합니다.
- [로그 쿼리 작성](get-started-portal.md)에 대 한 자습서를 완료 합니다.
