---
title: Azure 보안 제어 - 인시던트 대응
description: Azure 보안 제어 인시던트 대응
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: fb4c4c5a0cf6610af17aabc562c42d2e0eb4e6a4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94409096"
---
# <a name="security-control-incident-response"></a>보안 제어: 인시던트 대응

공격을 신속하게 발견한 다음 피해를 효과적으로 억제하며 공격자의 존재를 근절하고 네트워크 및 시스템의 무결성을 복원하는 인시던트 대응 인프라(예: 계획, 정의된 역할, 학습, 커뮤니케이션, 관리 감독)를 개발하고 구현하여 조직의 정보와 평판을 보호합니다.

## <a name="101-create-an-incident-response-guide"></a>10.1: 인시던트 대응 지침 만들기

| Azure ID | CIS ID | 책임 |
|--|--|--|
| 10.1 | 19.1, 19.2, 19.3 | Customer |

조직에 대한 인시던트 대응 지침을 작성합니다. 탐지에서 인시던트 사후 검토까지의 인시던트 처리/관리 단계뿐만 아니라 담당자의 모든 역할도 정의하는 인시던트 대응 계획이 작성되어 있는지 확인합니다.  

- [사용자 고유의 보안 인시던트 대응 프로세스를 구축하는 방법에 대한 지침](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Microsoft 보안 대응 센터의 인시던트 분석](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [NIST의 컴퓨터 보안 인시던트 처리 가이드를 활용하여 고유한 인시던트 대응 계획 수립 지원](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

## <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: 인시던트 점수 매기기 및 우선 순위 지정 절차 만들기

| Azure ID | CIS ID | 책임 |
|--|--|--|
| 10.2 | 19.8 | Customer |

Security Center에서 심각도를 각 경고에 할당하여 먼저 조사해야 하는 경고에 대한 우선 순위를 지정합니다. 심각도는 Security Center에서 경고를 실행하는 데 사용된 결과 또는 분석의 신뢰도 및 경고가 발생된 활동의 배후에 악의적인 의도가 있었음에 대한 신뢰 수준을 기준으로 합니다. 

또한 태그를 사용하여 구독(예: 프로덕션, 비 프로덕션)을 명확하게 표시하고 Azure 리소스, 특히 중요한 데이터를 처리하는 리소스를 명확하게 식별하고 분류하는 명명 시스템을 만듭니다.  인시던트가 발생한 Azure 리소스 및 환경의 중요도에 따라 경고의 수정에 대한 우선 순위를 지정해야 합니다.

- [Azure Security Center의 보안 경고](../../security-center/security-center-alerts-overview.md)

- [태그를 사용하여 Azure 리소스 구성](../../azure-resource-manager/management/tag-resources.md).

## <a name="103-test-security-response-procedures"></a>10.3: 보안 대응 프로시저 테스트

| Azure ID | CIS ID | 책임 |
|--|--|--|
| 10.3 | 19 | Customer |

Azure 리소스를 보호하는 데 도움이 되도록 시스템의 인시던트 대응 기능을 정기적으로 테스트합니다. 약점과 결함을 식별하고 필요에 따라 계획을 수정합니다.

- [NIST 게시물 - IT 계획 및 기능에 대한 테스트, 학습 및 연습 프로그램에 대한 가이드](https://csrc.nist.gov/publications/detail/sp/800-84/final)

## <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: 보안 인시던트 연락처 세부 정보 제공 및 보안 인시던트에 대한 경고 알림 구성

| Azure ID | CIS ID | 책임 |
|--|--|--|
| 10.4 | 19.5 | Customer |

MSRC(Microsoft 보안 대응 센터)에서 불법적이거나 권한이 없는 당사자가 데이터에 액세스했다고 검색하는 경우 Microsoft에서 보안 인시던트 연락처 정보를 사용하여 사용자에게 연락합니다. 문제가 해결되었는지 확인하기 위해 사후에 인시던트를 검토합니다.

- [Azure Security Center 보안 연락처를 설정하는 방법](../../security-center/security-center-provide-security-contact-details.md)

## <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: 보안 경고를 인시던트 대응 시스템에 통합

| Azure ID | CIS ID | 책임 |
|--|--|--|
| 10.5 | 19.6 | Customer |

Azure 리소스에 대한 위험을 식별하는 데 도움이 되도록 연속 내보내기 기능을 사용하여 Azure Security Center 경고 및 추천 사항을 내보냅니다. 연속 내보내기를 사용하면 경고 및 추천 사항을 수동으로 또는 지속적으로 내보낼 수 있습니다. Azure Security Center 데이터 커넥터를 사용하여 경고를 Azure Sentinel로 스트림할 수 있습니다.

- [연속 내보내기를 구성하는 방법](../../security-center/continuous-export.md)

- [경고를 Azure Sentinel로 스트림하는 방법](../../sentinel/connect-azure-security-center.md)

## <a name="106-automate-the-response-to-security-alerts"></a>10.6: 보안 경고에 대한 대응 자동화

| Azure ID | CIS ID | 책임 |
|--|--|--|
| 10.6 | 19 | Customer |

Azure Security Center의 워크플로 자동화 기능을 사용하여 “Logic Apps”를 통해 보안 경고 및 권장 사항에 대한 응답을 자동으로 실행합니다.

- [워크플로 자동화와 Logic Apps를 구성하는 방법](../../security-center/workflow-automation.md)


## <a name="next-steps"></a>다음 단계

- 다음 보안 제어: [침투 테스트 및 레드 팀 연습](security-control-penetration-tests-red-team-exercises.md)을 참조하세요.