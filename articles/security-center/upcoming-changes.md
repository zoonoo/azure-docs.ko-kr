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
ms.openlocfilehash: a2c29049decc056f0d3c8083d21574456610c124
ms.sourcegitcommit: f82e290076298b25a85e979a101753f9f16b720c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/04/2021
ms.locfileid: "99555143"
---
# <a name="important-upcoming-changes-to-azure-security-center"></a>Azure Security Center에 예정된 중요한 변경

> [!IMPORTANT]
> 이 페이지의 정보는 시험판 제품 또는 기능과 관련이 있으며, 이는 상업적으로 릴리스되기 전에 대폭 수정될 수 있습니다. Microsoft는 여기에 제공된 정보와 관련하여 어떠한 명시적 또는 묵시적 약속 또는 보증도 하지 않습니다.

이 페이지에서는 Security Center에 계획된 변경에 대해 알아봅니다. 보안 점수 또는 워크플로와 같은 항목에 영향을 줄 수 있는 제품에 대한 계획된 수정을 설명합니다.

최신 릴리스 정보를 찾고 있으면 [Azure Security Center의 새로운 기능](release-notes.md)에서 확인할 수 있습니다.


## <a name="planned-changes"></a>계획된 변경

- [더 이상 사용되지 않는 "시스템 업데이트 적용" 보안 제어의 두 가지 권장 사항](#two-recommendations-from-apply-system-updates-security-control-being-deprecated)
- [SQL 데이터 분류 권장 사항 향상](#enhancements-to-sql-data-classification-recommendation)
- [11개의 Azure Defender 경고 사용 중단](#deprecation-of-11-azure-defender-alerts)

### <a name="two-recommendations-from-apply-system-updates-security-control-being-deprecated"></a>더 이상 사용되지 않는 "시스템 업데이트 적용" 보안 제어의 두 가지 권장 사항 

**변경 예상 날짜:** 2021년 2월

다음 두 가지 권장 사항은 2021년 2월에 더 이상 사용되지 않을 예정입니다.

- **시스템 업데이트를 적용하려면 머신을 다시 시작해야 합니다**. 이로 인해 보안 점수에 약간의 영향을 줄 수 있습니다.
- **머신에 모니터링 에이전트를 설치해야 합니다**. 이 권장 사항은 온-프레미스 머신에만 관련되며 해당 논리의 일부는 다른 권장 사항으로 전송됩니다. **Log Analytics 에이전트 상태 문제는 머신에서 해결해야 합니다**. 이로 인해 보안 점수에 약간의 영향을 줄 수 있습니다.

연속 내보내기 및 워크플로 자동화 구성을 확인하여 이러한 권장 사항이 포함되어 있는지 확인하는 것이 좋습니다. 또한 대시보드 또는 이를 사용할 수 있는 기타 모니터링 도구를 적절하게 업데이트해야 합니다.

[보안 추천 사항 참조 페이지](recommendations-reference.md)에서 이러한 권장 사항에 대해 자세히 알아보세요.


### <a name="enhancements-to-sql-data-classification-recommendation"></a>SQL 데이터 분류 권장 사항 향상

**변경 예상 날짜:** Q2 2021

**데이터 분류 적용** 보안 제어에서 **SQL 데이터베이스의 중요 데이터를 분류해야 함** 권장 사항은 Microsoft의 데이터 분류 전략에 맞춰 향상된 새 버전으로 대체됩니다. 그 결과 권장 사항의 ID도 변경됩니다(현재 b0df6f56-862d-4730-8597-38c0fd4ebd59).


### <a name="deprecation-of-11-azure-defender-alerts"></a>11개의 Azure Defender 경고 사용 중단

**변경 예상 날짜:** 2021년 3월

다음 달에 아래 나열된 11개의 Azure Defender 경고는 더 이상 사용되지 않습니다.

- 새 경고는 이러한 두 가지 경고를 대체하고 더 나은 적용 범위를 제공합니다.

    | AlertType                | AlertDisplayName                                                         |
    |--------------------------|--------------------------------------------------------------------------|
    | ARM_MicroBurstDomainInfo | 미리 보기 - MicroBurst 도구 키트 "Get-AzureDomainInfo" 함수 실행이 감지됨 |
    | ARM_MicroBurstRunbook    | 미리 보기 - MicroBurst 도구 키트 "Get-AzurePasswords" 함수 실행이 감지됨  |
    |                          |                                                                          |

- 이러한 9개의 경고는 이미 사용되지 않는 Azure Active Directory ID 보호 커넥터와 관련이 있습니다.

    | AlertType           | AlertDisplayName              |
    |---------------------|-------------------------------|
    | UnfamiliarLocation  | 일반적이지 않은 로그인 속성 |
    | AnonymousLogin      | 익명 IP 주소          |
    | InfectedDeviceLogin | 맬웨어 연결 IP 주소     |
    | ImpossibleTravel    | 비정상적 이동               |
    | MaliciousIP         | 악성 IP 주소          |
    | LeakedCredentials   | 유출된 자격 증명            |
    | PasswordSpray       | 암호 스프레이                |
    | LeakedCredentials   | Azure AD 위협 인텔리전스  |
    | AADAI               | Azure AD AI                   |
    |                     |                               |
 



## <a name="next-steps"></a>다음 단계

제품에 대한 모든 최근 변경 내용은 [Azure Security Center의 새로운 기능](release-notes.md)을 참조하세요.
