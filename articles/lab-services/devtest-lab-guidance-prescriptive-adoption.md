---
title: 기업용 Azure DevTest Labs 도입
description: 이 문서에서는 Azure DevTest Labs를 사용 하 여 기업에서에 대 한 규범적인 지침을 제공 합니다.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/12/2019
ms.author: spelluru
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: 32e828f420d41d7a67e4f41ac8a4a0698711ce2f
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/30/2019
ms.locfileid: "64916402"
---
# <a name="devtest-labs-in-the-enterprise"></a>기업에서 DevTest Labs
기업 때문에 클라우드를 채택 하 고 신속 하 게 됩니다 [혜택](/azure/architecture/cloud-adoption/business-strategy/cloud-migration-business-case) 민첩성, 유연성 및 경제성을 포함 하는 합니다. 첫 번째 단계는 종종 개발 및 테스트 워크 로드. Azure DevTest Labs 제공 [기능](devtest-lab-concepts.md) enterprise 및 지원 혜택입니다 [주요 개발/테스트 시나리오](devtest-lab-guidance-get-started.md)합니다.

워크 로드를 클라우드로 마이그레이션하는 회사에 대 한 일반적인 문제는 다음과 같습니다.

- [개발/테스트용 리소스 보안 유지](devtest-lab-guidance-governance-policy-compliance.md)
- [관리 및 비용을 이해](devtest-lab-guidance-governance-cost-ownership.md)
- 엔터프라이즈 보안 및 규정 준수를 손상 시 키 지 않고 개발자를 위한 셀프 서비스를 사용 하도록 설정
- 자동화 하 고 추가 시나리오를 처리 하기 위해 DevTest Labs를 확장 합니다.
- [수천 개의 리소스에 DevTest Labs 기반 솔루션의 확장](devtest-lab-guidance-scale.md)
- [DevTest Labs의 대규모 배포](devtest-lab-guidance-orchestrate-implementation.md)
- [개념 증명을 사용 하 여 시작](devtest-lab-guidance-orchestrate-implementation.md)

## <a name="intended-audience"></a>대상 그룹
이 문서는 기업의 IT 기획자, 설계자 및 관리자 설정 및 배포를 검토 하 고 작업을 감독 하는 일을 담당 합니다. 이러한 문서는 전체 프로세스 및 권장 되는 디자인 원칙을 강조합니다. 궁극적으로 조직 내에서 Azure DevTest Labs의 도입을 안전 하 고 안정적인 개발/테스트 환경을 촉진 목표가입니다.

## <a name="enterprise-customers"></a>기업 고객

성공적으로 많은 현재 DevTest Labs 기업 고객 조직에서 워크 로드를 테스트 및 개발에 대 한 DevTest Labs를 사용 합니다. [자세히 알아보기](https://azure.microsoft.com/case-studies/?term=DevTest+labs).

## <a name="next-steps"></a>다음 단계
- [엔터프라이즈에 대 한 참조 아키텍처](devtest-lab-reference-architecture.md)
