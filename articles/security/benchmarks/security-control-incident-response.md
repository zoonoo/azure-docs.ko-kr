---
title: Azure 보안 제어-인시던트 응답
description: Azure 보안 제어 인시던트 응답
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 993793d21e6253188dfc199d8701cbe117503517
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81408428"
---
# <a name="security-control-incident-response"></a>보안 제어: 인시던트 응답

공격을 신속 하 게 검색 한 다음, 공격자의 현재 상태를 eradicating 하 고, 네트워크 및 시스템의 무결성을 복원 하기 위해 인시던트 응답 인프라 (예: 계획, 정의 된 역할, 교육, 통신, 관리 감독)를 개발 하 고 구현 하 여 조직의 정보를 보호 하 고 그 평판을 보호 합니다.

## <a name="101-create-an-incident-response-guide"></a>10.1: 인시던트 응답 가이드 만들기

| Azure ID | CIS Id | 책임 |
|--|--|--|
| 10.1 | 19.1, 19.2, 19.3 | Customer |

조직에 대 한 인시던트 대응 가이드를 작성 합니다. 담당자의 모든 역할을 정의 하는 사고 대응 계획 및 인시던트를 검색 하 고 인시던트를 해결 하는 방법에 대 한 인시던트 처리/관리 단계를 정의 합니다.  

- [사용자 고유의 보안 인시던트 대응 프로세스를 구축 하는 방법에 대 한 지침](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Microsoft 보안 대응 센터의 인시던트 분석](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [NIST의 컴퓨터 보안 인시던트 처리 가이드를 활용 하 여 고유한 인시던트 대응 계획을 만드는 데 도움이 됩니다.](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

## <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: 인시던트 점수 매기기 및 우선 순위 지정 절차 만들기

| Azure ID | CIS Id | 책임 |
|--|--|--|
| 10.2 | 19.8 | Customer |

Security Center는 각 경고에 심각도를 할당 하 여 먼저 조사 해야 하는 경고의 우선 순위를 지정할 수 있도록 합니다. 심각도는 경고를 실행 하는 데 사용 되는 검색 또는 분석에 사용 되는 Security Center의 신뢰도와 경고를 발생 시킨 활동의 악의적인 의도를 가진 신뢰도 수준을 기준으로 합니다. 

또한 구독을 명확 하 게 표시 합니다 (예: 프로덕션, 비 prod) 태그를 사용 하 고, 특히 중요 한 데이터를 처리 하는 Azure 리소스를 명확 하 게 식별 하 고 범주화 하기 위해 명명 시스템을 만듭니다.  사고가 발생 한 Azure 리소스 및 환경의 중요도에 따라 경고 수정의 우선 순위를 지정 하는 것은 사용자의 책임입니다.

- [Azure Security Center의 보안 경고](https://docs.microsoft.com/azure/security-center/security-center-alerts-overview)

- [태그를 사용하여 Azure 리소스 구성](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

## <a name="103-test-security-response-procedures"></a>10.3: 보안 응답 프로시저 테스트

| Azure ID | CIS Id | 책임 |
|--|--|--|
| 10.3 | 19 | Customer |

정기적으로 시스템의 인시던트 응답 기능을 테스트 하 여 Azure 리소스를 보호 하는 연습을 수행 합니다. 필요에 따라 weak 점수와 격차 및 수정 계획을 식별 합니다.

- [NIST의 게시-IT 계획 및 기능에 대 한 테스트, 학습 및 연습 프로그램에 대 한 가이드](https://csrc.nist.gov/publications/detail/sp/800-84/final)

## <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: 보안 인시던트에 대 한 세부 정보를 제공 하 고 보안 인시던트에 대 한 경고 알림을 구성 하십시오.

| Azure ID | CIS Id | 책임 |
|--|--|--|
| 10.4 | 19.5 | Customer |

Microsoft 보안 대응 센터 (MSRC)에서 불법적인 또는 권한이 없는 당사자가 데이터에 액세스 한 것을 발견 한 경우 Microsoft에서 사용자에 게 연락 하는 보안 인시던트 연락처 정보를 사용 합니다. 문제가 해결 되었는지 확인 하기 위해 팩트 이후에 인시던트를 검토 합니다.

- [Azure Security Center 보안 연락처를 설정 하는 방법](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details)

## <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: 보안 경고를 인시던트 응답 시스템에 통합 하세요.

| Azure ID | CIS Id | 책임 |
|--|--|--|
| 10.5 | 19.6 | Customer |

연속 내보내기 기능을 사용 하 여 Azure 리소스에 대 한 위험을 식별 하는 데 도움이 되는 Azure Security Center 경고 및 권장 사항을 내보냅니다. 연속 내보내기를 사용 하면 경고 및 권장 사항을 수동으로 또는 지속적인 지속적인 방식으로 내보낼 수 있습니다. Azure Security Center data connector를 사용 하 여 경고를 Azure 센티널로 스트리밍할 수 있습니다.

- [연속 내보내기를 구성 하는 방법](https://docs.microsoft.com/azure/security-center/continuous-export)

- [Azure 센티널로 경고를 스트리밍하는 방법](https://docs.microsoft.com/azure/sentinel/connect-azure-security-center)

## <a name="106-automate-the-response-to-security-alerts"></a>10.6: 보안 경고에 대 한 응답을 자동화 합니다.

| Azure ID | CIS Id | 책임 |
|--|--|--|
| 10.6 | 19 | Customer |

Azure Security Center의 워크플로 자동화 기능을 사용 하 여 Azure 리소스를 보호 하기 위해 보안 경고 및 권장 사항에 대 한 "Logic Apps"를 통해 응답을 자동으로 트리거합니다.

- [워크플로 자동화를 구성 하 고 Logic Apps 하는 방법](https://docs.microsoft.com/azure/security-center/workflow-automation)


## <a name="next-steps"></a>다음 단계

- 다음 보안 제어: [침투 테스트 및 레드 팀 연습](security-control-penetration-tests-red-team-exercises.md) 을 참조 하세요.