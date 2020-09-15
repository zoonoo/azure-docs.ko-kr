---
title: 'FAQ: SRE 및 DevOps | Microsoft Docs'
titleSuffix: Azure
description: 'FAQ: SRE와 DevOps 간의 관계 이해'
author: dnblankedelman
manager: ScottCa
ms.service: site-reliability-engineering
ms.topic: article
ms.date: 09/14/2020
ms.author: dnb
ms.openlocfilehash: 3d0698f2780a4ccc41eedbde70fddea1766c5f21
ms.sourcegitcommit: 07166a1ff8bd23f5e1c49d4fd12badbca5ebd19c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/15/2020
ms.locfileid: "90090332"
---
# <a name="frequently-asked-questions-whats-the-relationship-between-sre-and-devops"></a>질문과 대답: SRE와 DevOps 간의 관계는 무엇입니까?

"어떻게 동일 합니까?"와 같은 사이트 안정성 엔지니어링 및 DevOps 간의 관계를 중심으로 하는 일반적인 질문 집합이 있습니다. 어떻게 다를까요? 조직에 둘 다 있을 수 있나요? " 이 문서에서는 SRE 및 DevOps 커뮤니티에서 제공 하는 대답 중 일부를 공유 하려고 시도 하 여이 관계를 이해 하는 데 더 자세히 살펴보겠습니다.

## <a name="how-are-they-the-same"></a>어떻게 동일 합니까?

SRE 및 DevOps는 포함 된 문제에 대 한 응답으로 만들고 개발 된 최신 작업 사례입니다.

- 프로덕션 환경 및 개발 프로세스의 복잡성 증가
- 이러한 환경의 지속적인 작동에 대한 비즈니스 종속성 개선
- 이러한 환경의 규모에 비례하여 직원의 규모를 조정할 수 없음
- 운영 안정성을 유지 하면서 더 빠르게 이동 해야 하는 경우

두 작업 사례는 모니터링/관찰성, 자동화, 설명서 및 공동 작업 소프트웨어 개발 도구와 같은 이러한 문제를 처리 하는 데 중요 한 주제에 주의를 기울여야 합니다.

SRE와 DevOps 간의 작업 영역과 도구에 상당한 중복이 있습니다. _사이트 안정성 통합 문서_ 에서 "Sre는 devops와 동일한 것으로 간주 하지만 약간 다른 이유를 말합니다."

## <a name="three-different-ways-to-compare-the-two-operations-practices"></a>두 가지 작업 사례를 비교 하는 세 가지 방법

SRE와 DevOps 간의 유사성은 명확 하지 않습니다. 여기서는 두 가지 방식이 어떻게 다른 지를 보여 줍니다. 여기서는이 질문에 대 한 일부 nuance를 가져오는 방법으로 관계를 고려 하는 세 가지 방법을 제공 합니다. 이러한 대답에는 동의할 수 없지만 각각은 토론을 위한 좋은 시작 장소를 제공 합니다.

### <a name="class-sre-implements-interface-devops"></a>"클래스 SRE가 인터페이스 DevOps"를 구현 합니다.

_사이트 안정성 통합 문서_ ( [리소스 책 목록](../resources/books.md)에서 언급)는 첫 번째 챕터의 Sre 및 devops에 대해 설명 합니다. 이 장에서는 "class SRE는 인터페이스 DevOps"를 부제목으로 사용 합니다. 이는 DevOps 철학의 특정 구현으로 간주 될 수 있는, 개발자를 대상으로 하는 구를 사용 하 여 제안 하기 위한 것입니다. 챕터를 가리키면 "DevOps는 상세 수준에서 작업을 실행 하는 방법에 대해 비교적 자동으로 수행 되지만, 그 관행은 proscriptive 훨씬 더 효율적입니다. 따라서 두 가지 관련 문제를 해결 하는 방법에 대 한 한 가지 가능한 답은 DevOps의 여러 구현 중 하나로 간주 될 수 있습니다.

### <a name="sre-is-to-reliability-as-devops-is-to-delivery"></a>DevOps가 배달 하는 것 처럼 SRE는 안정성을 보장 합니다.

SRE와 DevOps에 대 한 정의가 여러 개 있기 때문에 이러한 비교는 약간 muddied 하지만 여전히 유용 합니다. "각 작업 사례를 핵심 관심사를 반영 하는 하나 또는 두 개의 단어로 변환 해야 하는 경우" 질문으로 시작 합니다.

[사이트 안정성 엔지니어링 허브](../index.yml)에서 다음의 sre 정의를 사용 하는 경우:

> 사이트 안정성 엔지니어링은 조직이 해당 시스템, 서비스 및 제품에서 ‘적절한’ 수준의 안정성을 지속적으로 달성하도록 지원하는 엔지니어링 분야입니다.

그러면 SRE 라는 단어가 "안정성" 이라고 말할 수 있습니다. 이름 중간에 바로 있는 경우이 클레임에 대 한 몇 가지 뛰어난 증거를 제공 합니다.

[Azure Devops 리소스 센터](https://docs.microsoft.com/azure/devops/learn/)에서이 devops 정의를 사용 하는 경우:

> DevOps는 최종 사용자에게 지속적으로 가치를 업데이트할 수 있는 사람, 프로세스 및 제품의 합집합입니다.

그러면 DevOps에 대 한 유사한 추출을 "배달" 일 수 있습니다.

따라서 "DevOps를 전달 하는 것은 안정성을 보장 합니다."

### <a name="direction-of-attention"></a>주목 방향

이 답변은 Thomas Limoncelli가 [리소스 책 목록](../resources/books.md)에서 언급 한 paraphrased에 의해 참여 _하는 사람_ 에 게 따옴표를 묶거나 약간 합니다. DevOps 엔지니어가 가끔 프로덕션 운영 책임을 두고 소프트웨어 개발 수명 주기 파이프라인에 초점을 맞춘 반면, SREs는 가끔 SDLC 파이프라인 업무를 통해 프로덕션 작업에 집중 하는 것입니다.

그러나 중요 한 점은 한 쪽에서 소프트웨어 개발 프로세스로 시작 하는 다이어그램을 그리고 프로덕션 작업이 다른 작업에 대해 작동 하는 것입니다. 이 둘은 개발자의 코드를 사용 하 여 원하는 수의 테스트 및 단계를 shepherd 다음 해당 코드를 프로덕션으로 이동 하도록 빌드된 일반적인 파이프라인을 통해 연결 됩니다.

DevOps 엔지니어가 개발 환경에서 시작 하 고 프로덕션 단계를 자동화 하는 것을 Limoncelli. 완료 되 면 병목 현상 최적화로 돌아갑니다.

반면에는 프로덕션 작업에 집중 하 고 최종 결과를 개선 하는 수단으로 (기본적으로 반대 방향으로 작업 하는) 파이프라인에 깊이 도달 합니다.

이는 SRE 및 DevOps 포커스의 방향에 차이가 있으며, 이러한 차이를 구분 하는 데 도움이 될 수 있습니다.

## <a name="coexistence-in-the-same-organization"></a>동일한 조직에서 공존

해결 하고자 하는 마지막 질문은 "동일한 조직에서 SRE와 DevOps를 모두 사용할 수 있나요?"입니다.

이 질문에 대 한 답은 emphatic "yes!"입니다.

이전 답변은 두 가지 작업 사례가 겹치는 방법 및 겹치지 않는 경우 포커스가 어떻게 다른 지를 이해 하는 데 도움이 될 것입니다. 구성 된 DevOps 방법을 가진 조직은 SRE 또는 팀을 만드는 작업을 커밋하지 않고도 작은 규모의 (예: SLIs 및 Slo 시도)를 사용해 볼 수 있습니다. 이 패턴은 매우 일반적입니다.

## <a name="next-steps"></a>다음 단계

사이트 안정성 엔지니어링 또는 DevOps에 대해 자세히 알고 싶으십니까? [사이트 안정성 엔지니어링 허브](../index.yml) 및 [Azure devops 리소스 센터](https://docs.microsoft.com/azure/devops/learn/)를 확인 하세요.
