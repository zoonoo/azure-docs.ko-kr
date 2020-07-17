---
title: 기업용 Azure DevTest Labs 도입
description: 이 문서에서는 기업에서 Azure DevTest Labs 사용에 대 한 규범적인 지침을 제공 합니다.
ms.topic: article
ms.date: 06/26/2020
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: 81767c36b2a5b46eb41be1e516a14fb3ebe3fb7f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85476991"
---
# <a name="devtest-labs-in-the-enterprise"></a>기업의 DevTest Labs
기업은 민첩성, 유연성 및 경제를 포함 하는 [이점](/azure/architecture/cloud-adoption/business-strategy/cloud-migration-business-case) 때문에 클라우드를 신속 하 게 채택 하 고 있습니다. 첫 번째 단계는 개발 및 테스트 워크 로드입니다. Azure DevTest Labs는 엔터프라이즈를 활용 하 고 [주요 개발/테스트 시나리오](devtest-lab-guidance-get-started.md)를 지 원하는 [기능](devtest-lab-concepts.md) 을 제공 합니다.

클라우드로 워크 로드를 마이그레이션하는 기업에 대 한 일반적인 문제는 다음과 같습니다.

- [개발/테스트 리소스 보안](devtest-lab-guidance-governance-policy-compliance.md)
- [비용 관리 및 이해](devtest-lab-guidance-governance-cost-ownership.md)
- 엔터프라이즈 보안 및 규정 준수를 손상 시 키 지 않고 개발자에 게 셀프 서비스를 사용 하도록 설정
- 추가 시나리오를 다루는 DevTest Labs 자동화 및 확장
- [DevTest Labs 기반 솔루션을 수천 개의 리소스로 확장](devtest-lab-guidance-scale.md)
- [DevTest Labs의 대규모 배포](devtest-lab-guidance-orchestrate-implementation.md)
- [개념 증명 시작](devtest-lab-guidance-orchestrate-implementation.md)

## <a name="intended-audience"></a>대상 그룹
이 설명서는 배포 및 감독 작업을 설정 하 고 검토 하는 엔터프라이즈 IT 계획자, 설계자 및 관리자를 위한 것입니다. 이러한 문서에서는 전체 프로세스 및 권장 되는 디자인 원칙을 강조 합니다. 목표는 궁극적으로 조직 내에서 Azure DevTest Labs 도입을 유도 하는 안전 하 고 안정적인 개발/테스트 환경을 조성 하는 것입니다.

## <a name="enterprise-customers"></a>기업 고객

많은 최신 DevTest Labs 기업 고객은 개발을 위해 DevTest Labs를 사용 하 고 조직에서 워크 로드를 테스트 합니다. [자세히 알아보기](https://azure.microsoft.com/case-studies/?term=DevTest+labs).

## <a name="next-steps"></a>다음 단계
- [엔터프라이즈에 대 한 참조 아키텍처](devtest-lab-reference-architecture.md)
