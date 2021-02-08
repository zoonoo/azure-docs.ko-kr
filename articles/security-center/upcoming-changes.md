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
ms.date: 01/25/2021
ms.author: memildin
ms.openlocfilehash: d5de16c8156762a229d6c707080bc197dc206a7c
ms.sourcegitcommit: 445ecb22233b75a829d0fcf1c9501ada2a4bdfa3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/02/2021
ms.locfileid: "99475593"
---
# <a name="important-upcoming-changes-to-azure-security-center"></a>Azure Security Center에 예정된 중요한 변경

> [!IMPORTANT]
> 이 페이지의 정보는 시험판 제품 또는 기능과 관련이 있으며, 이는 상업적으로 릴리스되기 전에 대폭 수정될 수 있습니다. Microsoft는 여기에 제공된 정보와 관련하여 어떠한 명시적 또는 묵시적 약속 또는 보증도 하지 않습니다.

이 페이지에서는 Security Center에 계획된 변경에 대해 알아봅니다. 보안 점수 또는 워크플로와 같은 항목에 영향을 줄 수 있는 제품에 대한 계획된 수정을 설명합니다.

최신 릴리스 정보를 찾고 있으면 [Azure Security Center의 새로운 기능](release-notes.md)에서 확인할 수 있습니다.


## <a name="planned-changes"></a>계획된 변경

- [GA(일반 공급)에 대한 Kubernetes 워크로드 보호 권장 사항이 곧 출시될 예정입니다.](#kubernetes-workload-protection-recommendations-will-soon-be-released-for-general-availability-ga)
- [더 이상 사용되지 않는 "시스템 업데이트 적용" 보안 제어의 두 가지 권장 사항](#two-recommendations-from-apply-system-updates-security-control-being-deprecated)
- [SQL 데이터 분류 권장 사항 향상](#enhancements-to-sql-data-classification-recommendation)


### <a name="kubernetes-workload-protection-recommendations-will-soon-be-released-for-general-availability-ga"></a>GA(일반 공급)에 대한 Kubernetes 워크로드 보호 권장 사항이 곧 출시될 예정입니다.

**변경 예상 날짜:** 2021년 2월

[Kubernetes 워크로드 보호](kubernetes-workload-protections.md)에 설명된 Kubernetes 워크로드 보호 권장 사항은 현재 미리 보기로 제공됩니다. 권장 사항이 미리 보기로 제공되는 동안에는 리소스가 비정상으로 렌더링하지 않으며 보안 점수 계산에 포함되지 않습니다.

이러한 권장 사항은 곧 GA(일반 공급)용으로 출시될 예정이므로 점수 계산에 *포함됩니다*. 아직 수정하지 않은 경우 보안 점수에 약간의 영향을 줄 수 있습니다.

가능한 경우 언제든지 수정합니다([Azure Security Center의 권장 사항 수정](security-center-remediate-recommendations.md)에서 방법 알아보기).

Kubernetes 워크로드 보호 권장 사항은 다음과 같습니다.

- 클러스터에 Kubernetes용 Azure Policy 추가 기능을 설치하고 사용하도록 설정해야 함
- 컨테이너 CPU 및 메모리 한도를 적용해야 함
- 권한 있는 컨테이너를 피해야 함
- 변경 불가능한(읽기 전용) 루트 파일 시스템을 컨테이너에 적용해야 함
- 권한 상승을 포함하는 컨테이너를 사용하지 않아야 함
- 컨테이너를 루트 사용자로 실행하지 않아야 함
- 중요한 호스트 네임스페이스를 공유하는 컨테이너를 사용하지 않아야 함
- 최소 권한 Linux 기능을 컨테이너에 적용해야 함
- Pod HostPath 볼륨 탑재 사용은 알려진 목록으로 제한되어야 함
- 컨테이너는 허용되는 포트에서만 수신 대기해야 함
- 서비스는 허용되는 포트에서만 수신 대기해야 함
- 호스트 네트워킹 및 포트 사용을 제한해야 함
- 컨테이너 AppArmor 프로필의 재정의 또는 비활성화를 제한해야 함
- 컨테이너 이미지는 신뢰할 수 있는 레지스트리에서만 배포해야 함             

[Kubernetes 워크로드 보호](kubernetes-workload-protections.md)에서 이러한 권장 사항에 대해 자세히 알아보세요.

### <a name="two-recommendations-from-apply-system-updates-security-control-being-deprecated"></a>더 이상 사용되지 않는 "시스템 업데이트 적용" 보안 제어의 두 가지 권장 사항 

**변경 예상 날짜:** 2021년 2월

다음 두 가지 권장 사항은 2021년 2월에 더 이상 사용되지 않을 예정입니다.

- **시스템 업데이트를 적용하려면 머신을 다시 시작해야 합니다**. 이로 인해 보안 점수에 약간의 영향을 줄 수 있습니다.
- **머신에 모니터링 에이전트를 설치해야 합니다**. 이 권장 사항은 온-프레미스 머신에만 관련되며 해당 논리의 일부는 다른 권장 사항으로 전송됩니다. **Log Analytics 에이전트 상태 문제는 머신에서 해결해야 합니다**. 이로 인해 보안 점수에 약간의 영향을 줄 수 있습니다.

연속 내보내기 및 워크플로 자동화 구성을 확인하여 이러한 권장 사항이 포함되어 있는지 확인하는 것이 좋습니다. 또한 대시보드 또는 이를 사용할 수 있는 기타 모니터링 도구를 적절하게 업데이트해야 합니다.

[보안 추천 사항 참조 페이지](recommendations-reference.md)에서 이러한 권장 사항에 대해 자세히 알아보세요.


### <a name="enhancements-to-sql-data-classification-recommendation"></a>SQL 데이터 분류 권장 사항 향상

**변경 예상 날짜:** Q2 2021

**데이터 분류 적용** 보안 제어에서 권장 사항 **SQL 데이터베이스의 중요 데이터를 분류해야 함** 의 현재 버전은 Microsoft의 데이터 분류 전략에 맞춰 향상된 새 버전으로 대체됩니다. 결과는 다음과 같습니다.

- 권장 사항은 더 이상 보안 점수에 영향을 주지 않습니다.
- 보안 제어("데이터 분류 적용")는 더 이상 보안 점수에 영향을 주지 않습니다.
- 권장 사항의 ID도 변경됩니다(현재 b0df6f56-862d-4730-8597-38c0fd4ebd59).



## <a name="next-steps"></a>다음 단계

제품에 대한 모든 최근 변경 내용은 [Azure Security Center의 새로운 기능](release-notes.md)을 참조하세요.
