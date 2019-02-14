---
title: Azure Active Directory ID 보호 | Microsoft Docs
description: Azure AD ID 보호를 사용하여 손상된 ID 및 디바이스를 악용하는 공격자의 능력을 제한하고 이전에 손상이 우려되거나 손상된 ID 또는 디바이스를 보호할 수 있는 방법을 알아봅니다.
services: active-directory
keywords: Azure Active Directory ID 보호, 클라우드 앱 검색, 애플리케이션 관리, 보안, 위험, 위험 수준, 취약점, 보안 정책
documentationcenter: ''
author: MarkusVi
manager: daveba
ms.assetid: e7434eeb-4e98-4b6b-a895-b5598a6cccf1
ms.service: active-directory
ms.subservice: identity-protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/29/2019
ms.author: markvi
ms.reviewer: raluthra
ms.collection: M365-identity-device-management
ms.openlocfilehash: eecdf4382fff441ca7c7d6bd4c63d12b87a73ccf
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56169703"
---
# <a name="what-is-azure-active-directory-identity-protection"></a>Azure Active Directory Identity Protection이란?

Azure Active Directory ID 보호는 Azure AD Premium P2 버전의 기능으로, 다음 작업을 가능하게 합니다.

- 조직의 ID에 영향을 미치는 잠재적인 취약점을 검색

- 조직 ID와 관련된 검색된 의심스러운 작업에 대한 자동화된 응답 구성  

- 의심스러운 인시던트 조사 및 해결을 위한 작업 수행   


## <a name="get-started"></a>시작하기

Microsoft는 10년 넘게 클라우드 기반 ID를 보호하고 있습니다. Azure Active Directory ID 보호를 사용하는 경우 사용자 환경에서 Microsoft가 ID 보호를 위해 사용하는 것과 동일한 보호 시스템을 사용할 수 있습니다.

대부분의 보안 침해는 공격자가 사용자의 ID를 도용하여 환경에 대한 액세스 권한을 얻을 때 발생합니다. 시간이 지나면서 공격자는 점점 더 효과적으로 제3자 침해를 활용하고 정교한 피싱 공격을 사용하고 있습니다. 일단 공격자가 더 낮은 권한을 가진 사용자 계정에 대한 액세스 권한을 얻게 되면 측면 이동을 통해 비교적 간단하게 중요한 회사 리소스에 대한 액세스 권한을 얻게 됩니다.

이에 따라 다음을 수행해야 합니다.

- 해당 권한 수준에 관계 없이 모든 ID 보호

- 손상된 ID가 남용되지 않도록 사전에 방지

손상된 ID를 검색하는 것은 쉬운 작업이 아닙니다. Azure Active Directory는 적응 Machine Learning 알고리즘 및 추론을 사용하여 잠재적으로 손상된 ID를 나타낼 수 있는 비정상 상태 및 의심스러운 인시던트를 검색합니다. ID 보호는 이 데이터를 사용하여 검색된 문제를 평가하고 적절한 완화 또는 수정 작업을 수행할 수 있도록 하는 보고서 및 경고를 생성합니다.

Azure Active Directory ID 보호는 모니터링 및 보고 도구 이상입니다. 조직의 ID를 보호하려면 지정된 위험 수준에 도달했을 때 검색된 문제에 자동으로 대응하는 위험 기반 정책을 구성할 수 있습니다. 이러한 정책은 Azure Active Directory 및 EMS([Enterprise Mobility + Security](https://docs.microsoft.com/enterprise-mobility-security/))에서 제공되는 다른 조건부 액세스 제어 정책과 더불어 암호 재설정 및 Multi-Factor Authentication 적용을 포함하는 적응형 수정 작업을 자동으로 차단하거나 시작할 수 있습니다.


#### <a name="identity-protection-capabilities"></a>ID 보호 기능

**취약점 및 위험 계정 검색:**  

* 취약점을 강조 표시하여 전반적인 보안 포스처를 개선하도록 사용자 지정 권장 사항 제공
* 로그인 위험 수준 계산
* 사용자 위험 수준 계산


**위험 이벤트 조사:**

* 위험 이벤트에 대한 알림 보내기
* 관련된 컨텍스트 정보를 사용하여 위험 이벤트 조사
* 기본 워크플로를 제공하여 조사 추적
* 암호 재설정 등 수정 작업에 쉬운 액세스 제공

**위험 기준 조건부 액세스 정책:**

* 로그인을 차단하거나 다단계 인증 질문을 요구하여 위험한 로그인을 완화하는 정책입니다.
* 위험한 사용자 계정 차단 및 보호 정책
* Multi-Factor Authentication에 사용자 등록 요구 정책



## <a name="identity-protection-roles"></a>ID 보호 역할

ID 보호 구현에 관련된 관리 작업의 부하를 분산하기 위해 몇 가지 역할을 할당할 수 있습니다. Azure AD ID 보호는 3가지 디렉터리 역할을 지원합니다.

| 역할                         | 가능한 작업                          | 불가능한 작업
| :--                          | ---                                |  ---   |
| 전역 관리자         | ID 보호에 대한 완전한 액세스, ID 보호 등록| |
| 보안 관리자       | ID 보호에 대한 완전한 액세스 | ID 보호 등록, 사용자의 암호 재설정 |
| 보안 판독기              | ID 보호에 대한 읽기 전용 액세스 | Identity Protection 등록, 사용자 재구성, 정책 구성, 암호 재설정 |




자세한 내용은 [Azure Active Directory에서 관리자 역할 할당](../users-groups-roles/directory-assign-admin-roles.md)을 참조하세요.



## <a name="detection"></a>감지

### <a name="vulnerabilities"></a>취약점

Azure Active Directory ID 보호는 구성을 분석하고 사용자의 ID에 영향을 줄 수 있는 취약점을 검색합니다. 자세한 내용은 [Azure Active Directory ID 보호에서 검색되는 취약성](vulnerabilities.md)을 참조하세요.

### <a name="risk-events"></a>위험 이벤트

Azure Active Directory는 적응 Machine Learning 알고리즘 및 추론을 사용하여 사용자의 ID와 관련된 의심스러운 작업을 검색합니다. 시스템은 검색된 각 의심스러운 작업에 대한 레코드를 만듭니다. 이러한 레코드를 위험 이벤트라고도 합니다.  
자세한 내용은 [Azure Active Directory 위험 이벤트](../active-directory-identity-protection-risk-events.md)를 참조하세요.


## <a name="investigation"></a>조사

ID 보호를 통한 이동은 일반적으로 ID 보호 대시보드를 시작합니다.

![수정](./media/overview/1000.png "수정")

대시보드는 다음에 대한 액세스를 제공합니다.

* **위험 플래그가 지정된 사용자**, **위험 이벤트** 및 **취약성**과 같은 보고서
* **보안 정책**, **알림** 및 **다단계 인증 등록** 구성과 같은 설정

일반적으로 조사를 위한 시작점으로, 위험 이벤트와 관련된 활동, 로그 및 기타 관련 정보를 검토하여 재구성 또는 완화 단계가 필요한지 여부, ID가 손상된 방식 및 손상된 ID가 사용된 방식을 결정하는 과정입니다.

Azure Active Directory 보호에서 전자 메일을 통해 보내는 [알림](notifications.md) 에 조사 활동을 연결할 수 있습니다.



## <a name="policies"></a>정책

Azure Active Directory Identity Protection은 자동 응답을 구현하기 위해 다음 세 가지 정책을 제공합니다.

- [다단계 인증 등록 정책](howto-mfa-policy.md)

- [사용자 위험 정책](howto-user-risk-policy.md)

- [로그인 위험 정책](howto-sign-in-risk-policy.md)


## <a name="next-steps"></a>다음 단계

- [Channel 9: Azure AD 및 ID 쇼: ID 보호 미리 보기](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)

- [Azure Active Directory ID 보호 활성화](enable.md)

