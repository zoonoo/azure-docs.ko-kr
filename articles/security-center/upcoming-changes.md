---
title: Azure Security Center에 예정된 중요한 변경
description: 알아야 하고 계획해야 할 수 있는 Azure Security Center에 예정된 변경입니다
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2020
ms.author: memildin
ms.openlocfilehash: df863372cbf7abfb6fee145b7d13bb00d8deb074
ms.sourcegitcommit: 8a1ba1ebc76635b643b6634cc64e137f74a1e4da
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/09/2020
ms.locfileid: "94380168"
---
# <a name="important-upcoming-changes-to-azure-security-center"></a>Azure Security Center에 예정된 중요한 변경

> [!IMPORTANT]
> 이 페이지의 정보는 시험판 제품 또는 기능과 관련이 있으며, 이는 상업적으로 릴리스되기 전에 대폭 수정될 수 있습니다. Microsoft는 여기에 제공된 정보와 관련하여 어떠한 명시적 또는 묵시적 약속 또는 보증도 하지 않습니다.

이 페이지에서는 Security Center에 계획된 변경에 대해 알아봅니다. 보안 점수 또는 워크플로와 같은 항목에 영향을 줄 수 있는 제품에 대한 계획된 수정을 설명합니다.

최신 릴리스 정보를 찾고 있으면 [Azure Security Center의 새로운 기능](release-notes.md)에서 확인할 수 있습니다.


## <a name="planned-changes"></a>계획된 변경

### <a name="system-updates-should-be-installed-on-your-machines-recommendation-getting-sub-recommendations"></a>"시스템 업데이트를 컴퓨터에 설치되어 있어야 합니다." 권장 사항을 가져오는 하위 권장 사항

#### <a name="summary"></a>요약

| 양상 | 세부 정보 |
|---------|---------|
|공지 날짜 | 2020년 11월 9일  |
|이 변경에 대한 날짜  |  2020년 11월 중순 |
|영향     | 이 권장 사항의 현재 버전에서 대체 버전으로 전환하는 동안 보안 점수가 변경될 수 있습니다. |
|  |  |

**시스템 업데이트를 머신에 설치해야 합니다** 권장 사항의 향상된 버전을 릴리스합니다. 새 버전은 시스템 업데이트 적용 보안 제어에서 현재 버전을 *대체* 하고 다음과 같은 개선 사항을 제공합니다.

- 누락된 각 업데이트에 대한 하위 권장 사항
- Azure Portal의 Azure Security Center 페이지에서 새롭게 디자인된 환경
- Azure Resource Graph의 권장 사항에 대한 보강 데이터

#### <a name="transition-period"></a>전환 기간

36시간(약)의 전환 기간이 있습니다. 잠재적 중단을 최소화하기 위해 주말에 업데이트를 수행하도록 예약했습니다. 전환하는 동안 보안 점수가 영향을 받을 수 있습니다.

#### <a name="redesigned-portal-experience"></a>새로 디자인한 포털 환경

**시스템 업데이트는 머신에 설치되어야 합니다** 에 대한 권장 사항 세부 정보 페이지에는 아래와 같은 결과 목록이 포함되어 있습니다. 단일 찾기를 선택하면 재구성 정보 및 영향을 받는 리소스의 목록에 대한 링크가 포함된 세부 정보 창이 열립니다.

:::image type="content" source="./media/upcoming-changes/system-updates-should-be-installed-subassessment.png" alt-text="업데이트된 권장 사항에 대한 포털 환경에서 하위 권장 사항 중 하나 열기":::


#### <a name="richer-data-from-azure-resource-graph"></a>Azure Resource Graph의 다양한 데이터

Azure Resource Graph는 효율적인 리소스 탐색을 제공하도록 설계된 Azure의 서비스입니다. ARG를 사용하여 사용자 환경을 효과적으로 관리할 수 있도록 지정된 구독 세트에서 대규모로 쿼리할 수 있습니다. 

Azure Security Center의 경우 ARG 및 [KQL(Kusto Query Language)](https://docs.microsoft.com/azure/data-explorer/kusto/query/)을 사용하여 다양한 보안 태세 데이터를 쿼리할 수 있습니다.

**시스템 업데이트는 머신에 설치되어 있어야 합니다** 의 현재 버전을 쿼리하는 경우 ARG에서 사용할 수 있는 유일한 정보는 권장 사항을 머신에서 재구성되어야 한다는 것입니다. 업데이트된 버전이 릴리스되면 다음 쿼리는 누락된 각 시스템 업데이트를 머신별로 그룹화하여 반환합니다.

```kusto
securityresources
| where type =~ "microsoft.security/assessments/subassessments"
| where extract(@"(?i)providers/Microsoft.Security/assessments/([^/]*)", 1, id) == "4ab6e3c5-74dd-8b35-9ab9-f61b30875b27"
| where properties.status.code == "Unhealthy"
```

## <a name="next-steps"></a>다음 단계

제품에 대한 모든 최근 변경 내용은 [Azure Security Center의 새로운 기능](release-notes.md)을 참조하세요.