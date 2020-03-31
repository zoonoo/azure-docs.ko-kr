---
title: 기업용 Azure DevTest Labs 도입
description: 이 문서에서는 기업에서 Azure DevTest 랩을 사용하기 위한 규범적인 지침을 제공합니다.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "64916402"
---
# <a name="devtest-labs-in-the-enterprise"></a>기업의 DevTest Labs
기업은 민첩성, 유연성 및 경제성을 포함하는 [이점](/azure/architecture/cloud-adoption/business-strategy/cloud-migration-business-case) 때문에 클라우드를 빠르게 채택하고 있습니다. 첫 번째 단계는 종종 개발 및 테스트 워크로드입니다. Azure DevTest Labs는 엔터프라이즈에 이점을 제공하고 [주요 개발/테스트 시나리오를](devtest-lab-guidance-get-started.md)지원하는 [기능을](devtest-lab-concepts.md) 제공합니다.

워크로드를 클라우드로 마이그레이션하는 기업의 일반적인 관심사는 다음과 같습니다.

- [개발/테스트 리소스 확보](devtest-lab-guidance-governance-policy-compliance.md)
- [비용 관리 및 이해](devtest-lab-guidance-governance-cost-ownership.md)
- 엔터프라이즈 보안 및 규정 준수를 손상시키지 않으면서 개발자를 위한 셀프 서비스 지원
- 추가 시나리오를 다루기 위해 DevTest 랩자동화 및 확장
- [DevTest Labs 기반 솔루션을 수천 개의 리소스로 확장](devtest-lab-guidance-scale.md)
- [데브테스트 연구소의 대규모 배포](devtest-lab-guidance-orchestrate-implementation.md)
- [개념 증명을 시작하기](devtest-lab-guidance-orchestrate-implementation.md)

## <a name="intended-audience"></a>대상 그룹
이 설명서는 배포를 수립하고 검토하고 운영을 감독하는 엔터프라이즈 IT 기획자, 설계자 및 관리자를 위한 설명입니다. 이 문서에서는 전체 프로세스및 권장 설계 원칙을 강조합니다. 목표는 안전하고 안정적인 개발/테스트 환경을 촉진하는 것이며, 궁극적으로 조직 내에서 Azure DevTest Labs의 채택을 촉진합니다.

## <a name="enterprise-customers"></a>엔터프라이즈 고객

현재 많은 DevTest Labs 엔터프라이즈 고객이 DevTest Labs를 사용하여 개발 및 조직의 워크로드 테스트에 성공했습니다. [자세히 알아봅니다](https://azure.microsoft.com/case-studies/?term=DevTest+labs).

## <a name="next-steps"></a>다음 단계
- [엔터프라이즈용 참조 아키텍처](devtest-lab-reference-architecture.md)
