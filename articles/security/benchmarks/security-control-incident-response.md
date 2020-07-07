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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "81408428"
---
# <a name="security-control-incident-response"></a>보안 제어: 인시던트 응답

공격을 신속 하 게 검색 한 다음, 공격자의 현재 상태를 eradicating 하 고, 네트워크 및 시스템의 무결성을 복원 하기 위해 인시던트 응답 인프라 (예: 계획, 정의 된 역할, 교육, 통신, 관리 감독)를 개발 하 고 구현 하 여 조직의 정보를 보호 하 고 그 평판을 보호 합니다.

## <a name="101-create-an-incident-response-guide"></a>10.1: 인시던트 대응 지침 만들기

| Azure ID | CIS Id | 책임 |
|--|--|--|
| 10.1 | 19.1, 19.2, 19.3 | Customer |

조직에 대 한 인시던트 대응 가이드를 작성 합니다. 탐지에서 인시던트 사후 검토까지의 인시던트 처리/관리 단계뿐만 아니라 담당자의 모든 역할도 정의하는 인시던트 대응 계획이 작성되어 있는지 확인합니다.  

- [사용자 고유의 보안 인시던트 대응 프로세스를 구축하는 방법에 대한 지침](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Microsoft 보안 대응 센터의 인시던트 분석](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [NIST의 컴퓨터 보안 인시던트 처리 가이드를 활용하여 고유한 인시던트 대응 계획 수립 지원](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

## <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: 인시던트 점수 매기기 및 우선 순위 지정 절차 만들기

| Azure ID | CIS Id | 책임 |
|--|--|--|
| 10.2 | 19.8 | Customer |

Security Center는 각 경고에 심각도를 할당 하 여 먼저 조사 해야 하는 경고의 우선 순위를 지정할 수 있도록 합니다. 심각도는 Security Center에서 경고를 실행하는 데 사용된 결과 또는 분석의 신뢰도 및 경고가 발생된 활동의 배후에 악의적인 의도가 있었음에 대한 신뢰 수준을 기준으로 합니다. 

또한 태그를 사용하여 구독(예: 프로덕션, 비 프로덕션)을 명확하게 표시하고 Azure 리소스, 특히 중요한 데이터를 처리하는 리소스를 명확하게 식별하고 분류하는 명명 시스템을 만듭니다.  인시던트가 발생한 Azure 리소스 및 환경의 중요도에 따라 경고의 수정에 대한 우선 순위를 지정해야 합니다.

- [Azure Security Center의 보안 경고](https://docs.microsoft.com/azure/security-center/security-center-alerts-overview)

- [태그를 사용하여 Azure 리소스 구성](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags).

## <a name="103-test-security-response-procedures"></a>10.3: 보안 대응 프로시저 테스트

| Azure ID | CIS Id | 책임 |
|--|--|--|
| 10.3 | 19 | Customer |

정기적으로 시스템의 인시던트 응답 기능을 테스트 하 여 Azure 리소스를 보호 하는 연습을 수행 합니다. 약점과 결함을 식별하고 필요에 따라 계획을 수정합니다.

- [NIST 게시물 - IT 계획 및 기능에 대한 테스트, 학습 및 연습 프로그램에 대한 가이드](https://csrc.nist.gov/publications/detail/sp/800-84/final)

## <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: 보안 인시던트 연락처 세부 정보 제공 및 보안 인시던트에 대한 경고 알림 구성

| Azure ID | CIS Id | 책임 |
|--|--|--|
| 10.4 | 19.5 | Customer |

Microsoft 보안 대응 센터 (MSRC)에서 불법적인 또는 권한이 없는 당사자가 데이터에 액세스 한 것을 발견 한 경우 Microsoft에서 사용자에 게 연락 하는 보안 인시던트 연락처 정보를 사용 합니다. 문제가 해결되었는지 확인하기 위해 사후에 인시던트를 검토합니다.

- [Azure Security Center 보안 연락처를 설정하는 방법](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details)

## <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: 보안 경고를 인시던트 대응 시스템에 통합

| Azure ID | CIS Id | 책임 |
|--|--|--|
| 10.5 | 19.6 | Customer |

연속 내보내기 기능을 사용 하 여 Azure 리소스에 대 한 위험을 식별 하는 데 도움이 되는 Azure Security Center 경고 및 권장 사항을 내보냅니다. 연속 내보내기를 사용하면 경고 및 추천 사항을 수동으로 또는 지속적으로 내보낼 수 있습니다. Azure Security Center 데이터 커넥터를 사용하여 경고를 Azure Sentinel로 스트림할 수 있습니다.

- [연속 내보내기를 구성하는 방법](https://docs.microsoft.com/azure/security-center/continuous-export)

- [경고를 Azure Sentinel로 스트림하는 방법](https://docs.microsoft.com/azure/sentinel/connect-azure-security-center)

## <a name="106-automate-the-response-to-security-alerts"></a>10.6: 보안 경고에 대한 대응 자동화

| Azure ID | CIS Id | 책임 |
|--|--|--|
| 10.6 | 19 | Customer |

Azure Security Center의 워크플로 자동화 기능을 사용 하 여 Azure 리소스를 보호 하기 위해 보안 경고 및 권장 사항에 대 한 "Logic Apps"를 통해 응답을 자동으로 트리거합니다.

- [워크플로 자동화 및 Logic Apps를 구성하는 방법](https://docs.microsoft.com/azure/security-center/workflow-automation)


## <a name="next-steps"></a>다음 단계

- 다음 보안 제어: [침투 테스트 및 레드 팀 연습](security-control-penetration-tests-red-team-exercises.md) 을 참조 하세요.