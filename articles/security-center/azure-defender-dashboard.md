---
title: Azure Defender의 대시보드 및 기능
description: Azure Defender 대시보드의 기능에 대해 알아봅니다.
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 35469c7a11d47e586187b55bde1e8ad8a0c94f5f
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91448418"
---
# <a name="the-azure-defender-dashboard"></a>Azure Defender 대시보드

Azure Defender 대시보드는 다음을 제공 합니다.

- 다양 한 리소스 종류의 Azure Defender 적용 범위에 대 한 가시성
- 고급 위협 방지 기능을 구성 하는 링크
- 온 보 딩 상태 및 에이전트 설치
- Azure Defender 위협 검색 경고 

Azure Defender 대시보드에 액세스 하려면 Security Center의 메뉴에 있는 클라우드 보안 섹션에서 **Azure defender** 를 선택 합니다.

## <a name="whats-shown-on-the-dashboard"></a>대시보드에 표시 되는 내용

:::image type="content" source="./media/azure-defender-dashboard/sample-defender-dashboard-numbered.png" alt-text="Azure Defender 대시보드의 예" lightbox="./media/azure-defender-dashboard/sample-defender-dashboard-numbered.png":::

대시보드에는 다음과 같은 섹션이 포함 되어 있습니다.

1. **Azure defender 검사** -여기에서 구독에 있고 Azure defender에서 보호할 수 있는 리소스 유형을 확인할 수 있습니다. 언제 든 지 업그레이드 하는 옵션도 있습니다. 모든 가능한 적격 리소스를 업그레이드 하려면 **모두 업그레이드**를 선택 합니다.

1. **보안 경고 영역** -Azure Defender가 사용자 환경의 모든 영역에서 위협을 발견 하면 경고를 생성 합니다. 이러한 경고는 영향을 받는 리소스의 세부 정보, 제안된 수정 단계 및 경우에 따라 응답으로 논리 앱을 트리거하는 옵션을 설명합니다. 이 그래프에서 아무 곳 이나 선택 하면 **보안 경고 페이지가**열립니다.

1. **고급 보호** -Azure Defender에는 가상 컴퓨터, SQL 데이터베이스, 컨테이너, 웹 응용 프로그램, 네트워크 등에 대 한 다양 한 고급 위협 방지 기능이 포함 되어 있습니다. 이 고급 보호 섹션에서는 이러한 각 보호에 대해 선택한 구독의 리소스 상태를 볼 수 있습니다. 그 중 하나를 선택 하 여 해당 보호 유형의 구성 영역으로 직접 이동 합니다.

1. **통찰력** -뉴스, 제안 된 읽기 및 높은 우선 순위 경고의이 롤링 창에서는 사용자와 구독과 관련 된 보안 문제를 Security Center 하는 것에 대 한 정보를 제공 합니다. 취약점 분석 도구를 통해 Vm에서 검색 된 높은 심각도 CVEs의 목록 이거나 Security Center 팀의 구성원이 새 블로그 게시물 인지 여부에 관계 없이 **Azure Defender 대시보드의**Insights 창에서 찾을 수 있습니다.




## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure Defender 대시보드에 대해 알아보았습니다. 

Azure Defender에 대 한 자세한 내용은 [Azure Defender 소개](azure-defender.md) 를 참조 하세요.

> [!div class="nextstepaction"]
> [Azure Defender 사용](security-center-pricing.md)