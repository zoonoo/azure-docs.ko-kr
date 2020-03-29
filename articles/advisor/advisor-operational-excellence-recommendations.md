---
title: Azure Advisor를 통해 Azure 구독의 운영 우수성 향상
description: Advisor를 사용하여 Azure 구독에 대한 운영 우수성을 최적화하고 성숙해보십시오.
ms.topic: article
ms.date: 10/24/2019
ms.openlocfilehash: f34284ba62bd5dea98345ebe73365b332d38ee78
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75443067"
---
# <a name="achieve-operational-excellence-with-azure-advisor"></a>Azure Advisor로 운영 우수성 달성

Azure Advisor 운영 우수성 권장 사항은 프로세스 및 워크플로 효율성, 리소스 관리 용이성 및 배포 모범 사례를 통해 고객에게 도움이 됩니다. 어드바이저 대시보드의 **운영 우수성** 탭에서 어드바이저로부터 이러한 권장 사항을 얻을 수 있습니다.

## <a name="create-azure-service-health-alerts-to-be-notified-when-azure-issues-affect-you"></a>Azure 문제가 영향을 미칠 때 알림을 받도록 Azure Service Health 경고 만들기

Azure 문제가 영향을 미칠 때 알림을 받도록 Azure Service Health 경고를 만드는 것이 좋습니다. [Azure Service Health](https://azure.microsoft.com/features/service-health/)는 Azure 서비스 문제가 영향을 미칠 경우 개인화된 지침과 지원을 제공하는 무료 서비스입니다. Advisor는 경고가 구성되지 않은 구독을 식별하고 구독을 생성하도록 권장합니다.

## <a name="design-your-storage-accounts-to-prevent-hitting-the-maximum-subscription-limit"></a>최대 구독 한도에 부딪히지 않도록 저장소 계정 디자인

Azure 리전은 구독당 최대 250개의 저장소 계정을 지원할 수 있습니다. 한도에 도달하면 해당 지역/구독 조합에서 더 이상 저장소 계정을 만들 수 없습니다. 어드바이저는 최대 한도에 근접한 스토리지 계정 수를 더 적게 설계할 수 있도록 구독 및 표면 권장 사항을 확인합니다.

## <a name="ensure-you-have-access-to-azure-cloud-experts-when-you-need-it"></a>필요할 때 Azure 클라우드 전문가에 대한 액세스 권한이 있는지 확인

중요 비즈니스용 워크로드를 실행할 때 필요한 경우 기술 지원에 대한 액세스 권한이 있어야 합니다. Advisor는 해당 지원 플랜에 포함된 기술 지원이 없는 잠재적 중요 비즈니스용 구독을 식별하고 기술 지원을 포함하는 옵션으로 업그레이드할 것을 권장합니다.

## <a name="repair-invalid-log-alert-rules"></a>잘못된 로그 경고 규칙 복구

Azure Advisor는 해당 조건 섹션에 잘못된 쿼리가 지정된 경고 규칙을 검색합니다. 로그 경고 규칙은 Azure Monitor에서 생성되며 지정된 간격으로 분석 쿼리를 실행하는 데 사용됩니다. 쿼리 결과에 따라 경고를 트리거할 것인지 여부가 결정됩니다. 시간이 지나 참조되는 리소스, 테이블 또는 명령이 변경되면 분석 쿼리가 유효하지 않게 될 수 있습니다. 어드바이저는 경고 규칙에서 쿼리를 수정하여 자동으로 비활성화되지 않도록 하고 Azure에서 리소스의 모니터링 범위를 보장하는 것이 좋습니다. [문제 해결 경고 규칙에 대해 자세히 알아보기](https://aka.ms/aa_logalerts_queryrepair)

## <a name="follow-best-practices-using-azure-policy"></a>Azure 정책을 사용하는 모범 사례 따르기

Azure Policy는 정책을 만들고, 할당하고, 관리하는 데 사용하는 Azure의 서비스입니다. 이러한 정책은 리소스에 대해 다양한 규칙과 효과를 적용합니다. 다음은 운영 우수성을 달성하는 데 도움이 되는 Azure 정책 권장 사항입니다. 
1. Azure 정책을 사용하여 태그 관리: 이 정책은 리소스를 만들거나 업데이트할 때 지정된 태그와 값을 추가하거나 대체합니다. 기존 리소스는 수정 작업을 트리거하여 수정할 수 있습니다. 또한 리소스 그룹에 대한 태그를 수정하지 않습니다.
2. Azure 정책을 사용하여 지리적 준수 요구 사항 적용: 정책을 사용하면 조직에서 리소스를 배포할 때 지정할 수 있는 위치를 제한할 수 있습니다. 
3. 배포에 허용되는 가상 시스템 SCO 지정: 이 정책을 사용하면 조직에서 배포할 수 있는 가상 컴퓨터 SCO 집합을 지정할 수 있습니다.
4. Azure 정책을 사용하여 '관리 디스크를 사용하지 않는 VM 감사' 적용
5. Azure 정책을 사용하여 '리소스 그룹에서 태그 상속' 사용: 리소스를 만들거나 업데이트할 때 정책은 부모 리소스 그룹에서 지정된 태그와 값을 추가하거나 바꿉니다. 기존 리소스는 수정 작업을 트리거하여 수정할 수 있습니다.

## <a name="next-steps"></a>다음 단계

Advisor 권장 사항에 대한 자세한 내용은 다음을 참조하세요.
* [Advisor 소개](advisor-overview.md)
* [시작](advisor-get-started.md)
* [Advisor 비용 권장 사항](advisor-cost-recommendations.md)
* [Advisor 성능 권장 사항](advisor-performance-recommendations.md)
* [Advisor 고가용성 권장 사항](advisor-high-availability-recommendations.md)
* [Advisor 보안 권장 사항](advisor-security-recommendations.md)
