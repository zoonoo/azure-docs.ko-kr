---
title: 온-프레미스 서비스 계정 관리 | Azure Active Directory
description: 서비스 계정에 대해 계정 수명 주기 프로세스를 만들고 실행하기 위한 가이드입니다.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 2/15/2021
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 10ea524620f810e0bf1dddc230716031bbc10e69
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105642385"
---
# <a name="governing-on-premises-service-accounts"></a>온-프레미스 서비스 계정 관리

Windows Active Directory에는 네 가지 유형의 온-프레미스 서비스 계정이 있습니다.

* [그룹 관리 서비스 계정](service-accounts-group-managed.md)(gMSA)

* [독립 실행형 서비스 계정](service-accounts-standalone-managed.md)(sMSA)

* [컴퓨터 계정](service-accounts-computer.md)

* [서비스 계정으로 작동하는 사용자 계정](service-accounts-user-on-premises.md)


다음을 위해서는 서비스 계정을 밀접하게 관리하는 것이 중요합니다. 

* 사용 사례 요구 사항 및 용도에 따라 서비스 계정을 보호합니다.

* 서비스 계정 및 자격 증명의 수명 주기를 관리합니다.

* 서비스 계정에 노출되는 위험 및 포함된 권한을 기준으로 서비스 계정을 평가합니다. 

* Active Directory 및 Azure Active Directory에 잠재적으로 너무 멀리 떨어진 권한이 포함된 오래된 서비스 계정이 없는지 확인합니다.

## <a name="principles-for-creating-a-new-service-account"></a>새로운 서비스 계정 만들기 원칙

새로운 서비스 계정을 만들 때 다음 기준을 따르세요.

| 원칙| 고려 사항 | 
| - |- | 
| 서비스 계정 매핑| 서비스 계정을 단일 서비스, 애플리케이션 또는 스크립트에 연결합니다. |
| 소유권| 계정에 대해 책임을 요청하고 수행하는 소유자가 있는지 확인합니다. |
| 범위| 서비스 계정의 범위를 명확하게 정의하고 사용 기간을 예상합니다. |
| 목적| 단일 특정 목적에 따라 서비스 계정을 만듭니다. |
| 권한| 최소 권한 원칙을 따릅니다. <br>관리자와 같은 기본 제공 그룹에 할당하지 마십시오.<br> 필요한 경우 로컬 머신 권한을 제거하십시오.<br>액세스 권한을 조정하고 디렉터리 액세스에 대해 Active Directory 위임을 사용합니다.<br>세부적인 액세스 권한을 사용합니다.<br>사용자 기반 서비스 계정에 대해 계정 만료 및 위치 기반 제한을 설정합니다. |
| 사용 모니터 및 감사| 로그인 데이터를 모니터하고 의도된 용도와 일치하는지 확인합니다. 비정상적인 사용에 대해 경고를 설정합니다. |

### <a name="enforce-least-privilege-for-user-accounts-and-limit-account-overuse"></a>사용자 계정에 최소 권한 적용 및 계정 초과 사용 제한

서비스 계정으로 사용되는 사용자 계정에 다음 설정을 사용합니다.

* [**계정 만료**](/powershell/module/activedirectory/set-adaccountexpiration?view=winserver2012-ps&preserve-view=true): 확실히 계속 사용될 필요가 있지 않으면 검토 기간 후 일정 시간에 자동으로 만료되도록 서비스 계정을 설정합니다.

*  **LogonWorkstations**: 서비스 계정이 로그인할수 있는 권한을 제한합니다. 한 컴퓨터에서 로컬로 실행되고 해당 컴퓨터에서만 리소스에 액세스하는 경우 다른 곳에서 로그온하지 못하도록 제한합니다.

* [**암호 변경 불가**](/powershell/module/addsadministration/set-aduser): 매개변수를 false로 설정하여 서비스 계정이 고유 암호를 변경하지 못하도록 방지합니다.

 
## <a name="build-a-lifecycle-management-process"></a>수명 주기 관리 프로세스 빌드

서비스 계정의 보안을 유지 관리하기 위해서는 필요한 시점부터 해제될 때까지 관리해야 합니다. 

서비스 계정의 수명 주기 관리를 위해서는 다음 프로세스를 따르세요.

1. 계정에 대한 사용 정보를 수집합니다.
1. 서비스 계정 및 앱을 CMDB(구성 관리 데이터베이스)에 온보딩합니다.
1. 위험 평가 또는 공식 검토를 수행합니다.
1. 서비스 계정을 만들고 제한 사항을 적용합니다.
1. 반복되는 검토를 예약하고 수행합니다. 필요에 따라 권한 및 범위를 조정합니다.
1. 필요한 경우 계정 프로비전을 해제합니다.

### <a name="collect-usage-information-for-the-service-account"></a>서비스 계정에 대한 사용 정보를 수집합니다.

각 서비스 계정에 대해 관련 비즈니스 정보를 수집합니다. 아래 표에서는 수집할 최소 정보를 보여주지만 계정을 계속 사용하는 데 필요한 모든 정보를 수집해야 합니다.

| 데이터| 세부 정보 |
| - | - |
| 소유자| 서비스 계정을 담당하는 사용자 또는 그룹 |
| 목적| 서비스 계정의 목적 |
| 권한(범위)| 필요한 권한 집합 |
| CMDB(구성 관리 데이터베이스) 링크| 대상 스크립트/애플리케이션 및 소유자가 포함된 교차 연결 서비스 계정 |
| 위험| 보안 위험 평가를 기준으로 점수가 책정되는 위험 및 비즈니스 영향 |
| 수명| 계정 만료 및 재인증 예약을 사용할 수 있는 예상되는 최대 수명 주기 |


 

이상적으로 계정 셀프 서비스에 대한 요청을 수행하고 관련 정보를 요구합니다. 애플리케이션 또는 비즈니스 소유자, IT 구성원 또는 인프라 소유자일 수 있는 소유자. 이 요청 및 연관된 정보를 위해 Microsoft Forms와 같은 도구를 사용하면 계정이 승인되었을 때 이를 CMDB 인벤토리 도구로 쉽게 이식할 수 있습니다.

### <a name="onboard-service-account-to-cmdb"></a>서비스 계정을 CMDB에 온보딩

수집된 정보를 CMDB 유형 애플리케이션에 저장합니다. 비즈니스 정보 외에도 다른 인프라, 앱 및 프로세스에 대한 모든 종속 항목을 포함합니다.  이 중앙 저장소는 다음을 쉽게 수행할 수 있게 해줍니다.

* 위험을 평가합니다.

* 필요한 제한 사항에 따라 서비스 계정을 구성합니다.

* 관련 기능 및 보안 종속 항목을 이해합니다.

* 보안 및 지속적인 요구에 맞게 정기 검토를 수행합니다.

* 서비스 계정 검토, 폐기 및 변경을 위해 소유자에게 연락합니다.

웹 사이트 실행을 위해 사용되고 하나 이상의 SQL 데이터베이스에 연결하기 위한 권한이 포함된 서비스 계정을 고려합니다. 이 서비스 계정에 대해 CMDB에 저장된 정보는 다음과 같을 수 있습니다.

|데이터 | 세부 정보|
| - | - |
| 소유자, 책임자| John Bloom, Anna Mayers |
| 목적| HR 웹 페이지를 실행하고 HR 데이터베이스에 연결합니다. 데이터베이스에 액세스할 때 최종 사용자를 가장할 수 있습니다. |
| 권한, 범위| HR-WEBServer: 로컬로 로그온, 웹 페이지 실행<br>HR-SQL1: 로컬로 로그온, 모든 HR* 데이터베이스 읽기<br>HR-SQL2: 로컬로 로그온, SALARY* 데이터베이스 읽기 |
| Cost Center| 883944 |
| 위험 평가| 중간, 비즈니스 영향: 중간, 비공개 정보, 중간 |
| 계정 제한 사항| 다음 대상에 로그온: 앞서 언급한 서버만, 암호 변경 불가, MBI암호 정책 |
| 수명| 제한 없음 |
| 검토 주기| 격년(소유자, 보안 팀, 개인 정보별) |

### <a name="perform-risk-assessment-or-formal-review-of-service-account-usage"></a>서비스 계정 사용에 대한 위험 평가 또는 공식 검토 수행

해당 권한 및 목적에 따라 계정이 손상되었을 때 연관된 애플리케이션 또는 서비스 및 인프라에 일으킬 수 있는 위험을 평가합니다. 직접 및 간접 위험을 모두 고려합니다. 

* 악의적으로 직접 액세스 권한을 획득하는 목적은 무엇인가요?

* 서비스 계정으로 액세스할 수 있는 다른 정보 또는 시스템은 무엇인가요?

* 계정을 사용하여 추가 권한을 부여할 수 있나요?

* 권한이 변경되었을 때 이를 어떻게 알 수 있나요?

위험 평가를 수행하고 문서화하면 다음 항목에 영향을 줄 수 있습니다.

* 계정 제한 사항

* 계정 수명

* 계정 검토 요구 사항(주기 및 검토자)

### <a name="create-a-service-account-and-apply-account-restrictions"></a>서비스 계정 만들기 및 계정 제한 사항 적용

CMDB에 관련 정보가 기술되고 위험 평가를 수행한 다음에만 서비스 계정을 만듭니다. 계정 제한 사항은 위험 평가에 맞춰야 합니다. 해당 평가와 관련된 경우 다음 제한 사항을 고려하세요.

* [계정 만료](/powershell/module/activedirectory/set-adaccountexpiration?view=winserver2012-ps&preserve-view=true)

   * 서비스 계정으로 사용되는 모든 사용자 계정에 대해 현실적이고 제한적인 사용 종료 날짜를 정의합니다. “계정 만료” 플래그를 사용하여 이를 설정합니다. 자세한 내용은 [Set-ADAccountExpiration](/powershell/module/addsadministration/set-adaccountexpiration)을 참조하세요. 

* 로그온 대상([LogonWorkstation](/powershell/module/addsadministration/set-aduser))

* [암호 정책](../../active-directory-domain-services/password-policy.md) 요구 사항

* 권한 있는 사용자에 대해서만 관리를 보장하는 [OU 위치](/windows-server/identity/ad-ds/plan/delegating-administration-of-account-ous-and-resource-ous)에 만듭니다.

* 서비스 계정에 대한 [변경 사항을 감지하고](/windows/security/threat-protection/auditing/audit-directory-service-changes) [서비스 계정 사용](https://www.manageengine.com/products/active-directory-audit/how-to/audit-kerberos-authentication-events.html)을 감지하는 감사를 설정하고 수집합니다.

프로덕션에 사용할 준비가 되었으면 서비스 계정에 대해 액세스 권한을 안전하게 부여합니다. 

### <a name="schedule-regular-reviews-of-service-accounts"></a>서비스 계정의 정기 검토 예약

중간 및 높은 위험으로 분류된 서비스 계정에 대해 정기적인 검토를 설정합니다. 검토에는 다음이 포함되어야 합니다. 

* 지속적인 계정 요구에 대한 사용자 증명과 권한 및 범위의 근거.

* 업스트림 및 다운스트림 연결에 대한 평가를 포함하여 개인 정보 및 보안 팀의 검토.

* 의도된 목적으로만 사용되는지 확인하기 위한 감사 데이터.

### <a name="deprovision-service-accounts"></a>서비스 계정 프로비전 해제

프로비전을 해제할 때는 먼저 권한 및 모니터를 제거한 후 필요에 따라 계정을 제거합니다.

다음 경우에 서비스 계정 프로비전을 해제합니다.

* 서비스 계정이 생성된 스크립트 또는 애플리케이션이 사용 중지된 경우입니다.

* 서비스 계정이 사용되는 스크립트 또는 애플리케이션 내의 함수(예: 특정 리소스에 대한 액세스)가 사용 중지된 경우입니다.

* 서비스 계정이 다른 서비스 계정으로 대체된 경우입니다.

모든 권한을 제거한 후 이 프로세스에 따라 계정을 제거합니다.

1. 연결된 애플리케이션 또는 스크립트가 프로비전 해제된 후 다른 프로세스에 사용되지 않도록 연결된 서비스 계정에 대한 로그인 및 리소스 액세스를 모니터합니다. 더 이상 필요하지 않으면 다음 단계로 이동합니다.

2. 서비스 계정에 로그인하지 못하도록 하고 더 이상 필요하지 않은지 확인합니다. 계정을 사용하지 않음으로 유지해야 하는 시간에 대해 비즈니스 정책을 만듭니다.

3. 사용하지 않음으로 유지 정책이 수행된 후의 서비스 계정을 삭제합니다. 

   * MSA의 경우 PowerShell을 사용하여 [삭제](/powershell/module/activedirectory/uninstall-adserviceaccount?view=winserver2012-ps&preserve-view=true)하거나 관리되는 서비스 계정 컨테이너에서 수동으로 삭제할 수 있습니다.

   * 컴퓨터 또는 사용자 계정의 경우 Active Directory에서 계정을 수동으로 삭제할 수 있습니다.

## <a name="next-steps"></a>다음 단계
서비스 계정 보안에 대해서는 다음 문서를 참조하세요.

* [온-프레미스 서비스 계정 소개](service-accounts-on-premises.md)

* [보안 그룹 관리 서비스 계정](service-accounts-group-managed.md)

* [보안 독립형 관리 서비스 계정](service-accounts-standalone-managed.md)

* [보안 컴퓨터 계정](service-accounts-computer.md)

* [보안 사용자 계정](service-accounts-user-on-premises.md)

* [온-프레미스 서비스 계정 관리](service-accounts-govern-on-premises.md)