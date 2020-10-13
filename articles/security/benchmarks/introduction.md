---
title: Azure 보안 벤치마크 소개
description: 보안 벤치 마크 소개
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/16/2019
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: f3b492534bedde50d2413b88fa38cd6325ed5df7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91328261"
---
# <a name="azure-security-benchmark-introduction"></a>Azure 보안 벤치 마크 소개

새로운 서비스 및 기능이 Azure에서 매일 릴리스됩니다. 개발자는 이러한 서비스를 기반으로 하는 새로운 클라우드 응용 프로그램을 신속 하 게 게시할 수 있으며 공격자는 항상 잘못 구성 된 리소스를 활용 하는 새로운 방법을 찾고 있습니다. 클라우드는 빠르게 이동 하 고 개발자는 빠르게 이동 하며 공격자는 항상 이동 하 고 있습니다. 클라우드 배포가 안전 하 게 보호 되 고 있는지 확인 하려면 어떻게 해야 하나요? 클라우드 시스템의 보안 사례는 온-프레미스 시스템과 다른 이유는 무엇 인가요? 여러 독립 개발 팀에서 일관성을 모니터링 하려면 어떻게 해야 하나요?

Microsoft는 *보안 벤치 마크* 를 사용 하 여 클라우드 배포를 신속 하 게 보호 하는 데 도움을 줍니다. 클라우드 서비스 공급자의 벤치 마크 권장 사항은 사용자 환경에서 특정 보안 구성 설정을 선택 하 고 조직에 대 한 위험을 신속 하 게 줄일 수 있는 시작 지점을 제공 합니다.

Azure 보안 벤치 마크에는 Azure에서 사용 하는 서비스를 보호 하는 데 사용할 수 있는 높은 영향을 주는 보안 권장 사항 컬렉션이 포함 되어 있습니다.

- **보안 제어**: 이러한 권장 사항은 일반적으로 azure 테 넌 트 및 azure 서비스 전체에 적용 됩니다. 각 권장 사항은 벤치 마크의 계획, 승인 또는 구현에 일반적으로 관련 된 관련자의 목록을 식별 합니다. 
- **서비스 기준**: 개별 Azure 서비스에 컨트롤을 적용 하 여 해당 서비스의 보안 구성에 대 한 권장 사항을 제공 합니다.

## <a name="implement-the-azure-security-benchmark"></a>Azure 보안 벤치 마크 구현
- 엔터프라이즈 컨트롤 및 서비스별 기준에 대 한 [설명서](overview.md) 를 검토 하 여 컨트롤 프레임 워크를 계획 하 고 Ci (컨트롤 v 7.1) 및 NIST (SP800) 프레임 워크와 같은 지침에 매핑하는 방법을 설명 하 여 Azure 보안 벤치 마크 구현을 **계획** 합니다.
- Azure Security Center [규제 준수 대시보드](../../security-center/security-center-compliance-dashboard.md)를 사용 하 여 Azure 보안 벤치 마크 상태 (및 기타 제어 집합)에 대 한 준수를 **모니터링** 합니다.
- Azure 청사진 및 Azure Policy을 사용 하 여 보안 구성을 자동화 하 고 Azure 보안 벤치 마크 및 조직의 기타 요구 사항에 따라 규정 준수를 적용 하도록 **guardrails를 설정** 합니다.
 
Azure security 벤치 마크 v 2는 microsoft의 Azure 보안 권장 사항에 대 한 단일 통합 보기를 제공 하기 위해 [Microsoft 보안 모범 사례](/security/compass/microsoft-security-compass-introduction) (이전의 Azure security 나침반)에 맞춰져 있습니다.

## <a name="common-use-cases"></a>일반적인 사용 사례

Azure 보안 벤치 마크는 다음과 같은 고객 또는 서비스 파트너에 대 한 일반적인 문제를 해결 하는 데 자주 사용 됩니다.
- Azure를 처음 접하는 후 보안을 유지 하기 위한 보안 모범 사례를 찾고 있습니다.
- 기존 Azure 배포의 보안 상태를 개선 하 여 상위 위험과 완화의 우선 순위를 지정 합니다.
- 특정 보안 지침을 충족 하기 위해 기술 및 비즈니스에서 사용할 수 있도록 Azure 서비스를 승인 합니다.
- 금융, 의료 또는 이러한 고객을 위해 시스템을 구축 해야 하는 서비스 공급 업체와 같은 정부 또는 숙련 된 산업에서 제공 되는 고객을 위한 규정 요구 사항을 충족 합니다. 이러한 고객은 Azure의 구성이 CI, NIST 또는 PCI와 같은 업계 프레임 워크에서 지정 된 보안 기능을 충족 하는지 확인 해야 합니다. Azure Security 벤치 마크는 이러한 산업 벤치 마크에 이미 미리 매핑되어 있는 컨트롤을 사용 하는 효율적인 방법을 제공 합니다.

## <a name="terminology"></a>용어

"제어", "벤치 마크" 및 "기준" 이라는 용어는 Azure 보안 벤치 마크 설명서에서 자주 사용 되며, Azure에서 이러한 용어를 사용 하는 방법을 이해 하는 것이 중요 합니다.


| 용어 | 설명 | 예제 |
|--|--|--|
| 컨트롤 | 컨트롤은 기술 또는 구현과 관련이 없는 기능이 나 작업에 대 한 개략적인 설명입니다. | 데이터 보호는 보안 제어 중 하나입니다. 이 컨트롤은 데이터를 보호 하기 위해 해결 해야 하는 특정 작업을 포함 합니다. |
| 벤치마크 | 벤치 마크에는 Azure와 같은 특정 기술에 대 한 보안 권장 사항이 포함 되어 있습니다. 권장 사항은 자신이 속한 컨트롤에 의해 분류 됩니다. | Azure 보안 벤치 마크는 Azure 플랫폼과 관련 된 보안 권장 사항을 구성 합니다. |
| 기초 | 기준은 개별 Azure 서비스에 대 한 벤치 마크의 구현입니다. 각 조직은 벤치 마크 권장 사항을 결정 하 고 Azure 구현 범위에서 해당 구성이 필요 합니다. | Contoso 회사는 Azure SQL 보안 기준에 권장 되는 구성을 수행 하 여 Azure SQL 보안 기능을 사용 하도록 설정 하는 것으로 보입니다.

Azure 보안 벤치 마크에 대 한 피드백을 환영 합니다! 아래의 피드백 영역에 의견을 제공 하는 것이 좋습니다. Azure 보안 벤치 마크 팀에서 입력을 보다 개인적으로 공유 하려는 경우 다음 양식을 작성 하는 것이 좋습니다. https://aka.ms/AzSecBenchmark
