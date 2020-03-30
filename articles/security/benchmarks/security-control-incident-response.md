---
title: Azure 보안 제어 - 인시던트 대응
description: 보안 제어 인시던트 대응
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: fb3560aa2d3fbf48ab63c4da4d3a8d69cb677209
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75934410"
---
# <a name="security-control-incident-response"></a>보안 제어: 사고 대응

공격을 신속하게 발견한 다음 공격을 신속하게 발견한 다음 사고 대응 인프라(예: 계획, 정의된 역할, 교육, 통신, 관리 감독)를 개발 및 구현하여 조직의 정보와 평판을 보호합니다. 피해를 효과적으로 포함하고, 공격자의 존재를 근절하고, 네트워크 및 시스템의 무결성을 복원합니다.

## <a name="101-create-an-incident-response-guide"></a>10.1: 사고 대응 가이드 만들기

| Azure ID | CIS 아이디 | 책임 |
|--|--|--|
| 10.1 | 19.1, 19.2, 19.3 | Customer |

조직에 대한 인시던트 대응 가이드를 작성합니다. 감지에서 사후 검토에 이르는 사고 처리/관리 단계뿐만 아니라 직원의 모든 역할을 정의하는 서면 인시던트 대응 계획이 있는지 확인합니다.

Azure 보안 센터 내에서 워크플로 자동화를 구성하는 방법:

https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide

자체 보안 인시던트 대응 프로세스 구축에 대한 지침:

https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

Microsoft 보안 대응 센터의 사고 해부학:

https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

고객은 NIST의 컴퓨터 보안 사고 처리 가이드를 활용하여 자체 사고 대응 계획을 수립할 수 있습니다.

https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf

## <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: 인시던트 채점 및 우선 순위 지정 절차 만들기

| Azure ID | CIS 아이디 | 책임 |
|--|--|--|
| 10.2 | 19.8 | Customer |

보안 센터는 각 경고에 심각도를 할당하여 먼저 조사할 경고의 우선 순위를 지정합니다. 심각도는 보안 센터가 찾기 또는 경고를 발행하는 데 사용되는 분석 및 경고로 이어진 활동의 배후에 악의적인 의도가 있다는 신뢰 수준에 얼마나 확신을 가지고 있는지에 따라 다집니다.

또한 구독을 명확하게 표시합니다(ex. Azure 리소스를 명확하게 식별하고 분류하는 명명 시스템을 만듭니다.

## <a name="103-test-security-response-procedures"></a>10.3: 보안 대응 절차 테스트

| Azure ID | CIS 아이디 | 책임 |
|--|--|--|
| 10.3 | 19 | Customer |

정기적으로 시스템의 사고 대응 기능을 테스트하기 위한 연습을 수행합니다. 약점과 격차를 식별하고 필요에 따라 계획을 수정합니다.

NIST의 간행물: IT 계획 및 기능에 대한 테스트, 교육 및 운동 프로그램 가이드를 참조하십시오.

https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

## <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: 보안 인시던트 연락처 세부 정보 제공 및 보안 인시던트에 대한 경고 알림 구성

| Azure ID | CIS 아이디 | 책임 |
|--|--|--|
| 10.4 | 19.5 | Customer |

Microsoft 보안 대응 센터(MSRC)에서 고객의 데이터가 불법적이거나 승인되지 않은 당사자가 액세스한 사실을 발견한 경우 Microsoft에서 보안 인시던트 연락처 정보를 사용하여 연락합니다.  사실 이후에 인시던트를 검토하여 문제가 해결되었는지 확인합니다.

Azure 보안 센터 보안 연락처를 설정 하는 방법:

https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

## <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: 보안 경고를 사고 대응 시스템에 통합

| Azure ID | CIS 아이디 | 책임 |
|--|--|--|
| 10.5 | 19.6 | Customer |

지속적인 내보내기 기능을 사용하여 Azure 보안 센터 경고 및 권장 사항을 내보냅니다. 연속 내보내기를 사용하면 수동으로 또는 지속적으로 지속방식으로 경고 및 권장 사항을 내보낼 수 있습니다. Azure 보안 센터 데이터 커넥터를 사용하여 Sentinel 경고를 스트리밍할 수 있습니다.

연속 내보내기를 구성하는 방법:

https://docs.microsoft.com/azure/security-center/continuous-export

Azure Sentinel로 경고를 스트리밍하는 방법:

https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

## <a name="106-automate-the-response-to-security-alerts"></a>10.6: 보안 경고에 대한 응답 자동화

| Azure ID | CIS 아이디 | 책임 |
|--|--|--|
| 10.6 | 19 | Customer |

Azure 보안 센터의 워크플로 자동화 기능을 사용하여 &quot;보안&quot; 경고 및 권장 사항에 대한 논리 앱을 통해 응답을 자동으로 트리거합니다.

워크플로 자동화 및 논리 앱을 구성하는 방법:

https://docs.microsoft.com/azure/security-center/workflow-automation

## <a name="next-steps"></a>다음 단계

다음 보안 제어 보기: [침투 테스트 및 레드 팀 연습](security-control-penetration-tests-red-team-exercises.md)