---
title: 온-프레미스 서비스 계정 관리 | Azure Active Directory
description: 서비스 계정에 대 한 계정 수명 주기 프로세스를 만들고 실행 하는 방법에 대 한 가이드
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
ms.openlocfilehash: 88fdfa1f449a0b65861ee09f2e78055a606c99d3
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101649209"
---
# <a name="governing-on-premises-service-accounts"></a>온-프레미스 서비스 계정 관리

Windows Active Directory에는 다음과 같은 네 가지 유형의 온-프레미스 서비스 계정이 있습니다.

* Gmsas ( [그룹 관리 서비스 계정](service-accounts-group-managed.md) )

* smsas ( [독립 실행형 관리 서비스 계정](service-accounts-standalone-managed.md) )

* [컴퓨터 계정](service-accounts-computer.md)

* [서비스 계정으로 작동 하는 사용자 계정](service-accounts-user-on-premises.md)


서비스 계정을 관리 하는 데 매우 중요 합니다. 

* 사용 사례 요구 사항 및 용도에 따라 서비스 계정을 보호 합니다.

* 서비스 계정 및 해당 자격 증명의 수명 주기를 관리 합니다.

* 노출 되는 위험 및 사용 권한을 기반으로 서비스 계정을 평가 합니다. 

* Active Directory 및 Azure Active Directory에 잠재적으로 도달할 수 있는 오래 된 서비스 계정이 없는지 확인 합니다.

## <a name="principles-for-creating-a-new-service-account"></a>새 서비스 계정 만들기에 대 한 원칙

새 서비스 계정을 만들 때 사용 하는 조건은 다음과 같습니다.

| 원칙| 고려 사항 | 
| - |- | 
| 서비스 계정 매핑| 서비스 계정을 단일 서비스, 응용 프로그램 또는 스크립트에 연결 합니다. |
| 소유권| 계정에 대 한 책임을 요청 하 고 가정 하는 소유자가 있는지 확인 합니다. |
| Scope| 서비스 계정에 대 한 사용 기간의 범위를 명확 하 게 정의 합니다. |
| 목적| 단일 용도로 서비스 계정을 만듭니다. |
| 권한| 최소 권한의 원칙을 다음과 같이 적용 합니다. <br>관리자와 같은 기본 제공 그룹에는 할당 하지 마십시오.<br> 필요한 경우 로컬 컴퓨터 권한 제거<br>액세스를 조정 하 고 디렉터리 액세스를 위한 Active Directory 위임을 사용 합니다.<br>세부적인 액세스 권한 사용.<br>사용자 기반 서비스 계정에 대 한 계정 만료 및 위치 기반 제한 설정 |
| 사용 모니터링 및 감사| 로그인 데이터를 모니터링 하 고 의도 된 사용량과 일치 하는지 확인 합니다. 비정상 사용에 대 한 경고를 설정 합니다. |

### <a name="enforce-least-privilege-for-user-accounts-and-limit-account-overuse"></a>사용자 계정에 최소 권한 적용 및 계정 사용 제한

서비스 계정으로 사용 되는 사용자 계정에는 다음 설정을 사용 합니다.

* [**계정 만료**](/powershell/module/activedirectory/set-adaccountexpiration?view=winserver2012-ps): 계속 해야 하는 것으로 확인 된 경우를 제외 하 고는 서비스 계정이 검토 기간 후 설정 된 시간을 자동으로 만료 하도록 설정 합니다.

*  **Logonworkstations**: 서비스 계정에서 로그인 할 수 있는 권한을 제한 합니다. 컴퓨터에서 로컬로 실행 되 고 해당 컴퓨터의 리소스에만 액세스 하는 경우에는 다른 위치에 로그온 할 수 없도록 제한 합니다.

* [**암호를 변경할 수 없음**](/powershell/module/addsadministration/set-aduser?view=win10-ps): 매개 변수를 false로 설정 하 여 서비스 계정이 자신의 암호를 변경 하지 못하도록 합니다.

 
## <a name="build-a-lifecycle-management-process"></a>수명 주기 관리 프로세스 빌드

서비스 계정에 대 한 보안을 유지 하려면 서비스를 해제할 때까지 필요한 사항을 확인 해야 합니다. 

서비스 계정의 수명 주기 관리를 위해 다음 프로세스를 사용 합니다.

1. 계정에 대 한 사용 정보를 수집 합니다.
1. 서비스 계정 및 앱을 CMDB (configuration management database)에 등록
1. 위험 평가 또는 공식 검토 수행
1. 서비스 계정을 만들고 제한을 적용 합니다.
1. 반복 검토를 예약 하 고 수행 합니다. 필요에 따라 사용 권한 및 범위를 조정 합니다.
1. 해당 하는 경우 계정 프로 비전을 해제 합니다.

### <a name="collect-usage-information-for-the-service-account"></a>서비스 계정에 대 한 사용 정보를 수집 합니다.

각 서비스 계정에 대 한 관련 비즈니스 정보를 수집 합니다. 아래 표에서는 수집할 최소 정보를 보여 주지만 계정 존재를 위한 비즈니스 사례를 만드는 데 필요한 모든 항목을 수집 해야 합니다.

| 데이터| 세부 정보 |
| - | - |
| 소유자| 서비스 계정에 대 한 책임이 있는 사용자 또는 그룹 |
| 목적| 서비스 계정의 용도 |
| 사용 권한 (범위)| 필요한 권한 집합 |
| CMDB (구성 관리 데이터베이스) 링크| 대상 스크립트/응용 프로그램 및 소유자를 사용 하는 교차 연결 서비스 계정 |
| 위험| 보안 위험 평가에 따른 위험 및 비즈니스 영향 점수 매기기 |
| 수명| 계정 만료 또는 재인증 예약을 사용 하는 데 예상 되는 최대 수명 |


 

계정 셀프 서비스에 대 한 요청을 수행 하 고 관련 정보를 요구 하는 것이 가장 좋습니다. 응용 프로그램 또는 비즈니스 소유자, IT 구성원 또는 인프라 소유자 일 수 있는 소유자입니다. 이 요청 및 관련 정보에 대 한 Microsoft forms와 같은 도구를 사용 하면 계정이 승인 되 면 CMDB 인벤토리 도구로 쉽게 이식할 수 있습니다.

### <a name="onboard-service-account-to-cmdb"></a>서비스 계정을 CMDB에 등록

수집 된 정보를 CMDB 유형 응용 프로그램에 저장 합니다. 비즈니스 정보 외에도 다른 인프라, 앱 및 프로세스에 대 한 모든 종속성을 포함 합니다.  이 중앙 리포지토리를 사용 하면 다음과 같은 작업을 쉽게 수행할 수 있습니다.

* 위험을 평가 합니다.

* 필요한 제한 사항을 사용 하 여 서비스 계정을 구성 합니다.

* 관련 기능 및 보안 종속성을 이해 합니다.

* 보안 및 지속적인 요구에 대 한 정기적인 검토를 수행 합니다.

* 서비스 계정을 검토 하 고, 사용 중지 하 고, 변경 하려면 소유자에 게 문의 하세요.

웹 사이트를 실행 하는 데 사용 되는 서비스 계정 및 하나 이상의 SQL 데이터베이스에 연결할 수 있는 권한이 있다고 가정 합니다. 이 서비스 계정에 대해 CMDB에 저장 된 정보는 다음과 같을 수 있습니다.

|데이터 | 세부 정보|
| - | - |
| 소유자, Deputy| John 블 룸, Anna Mayers |
| 목적| HR 웹 페이지를 실행 하 고 HR 데이터베이스에 연결 합니다. 데이터베이스에 액세스할 때 최종 사용자를 가장할 수 있습니다. |
| 권한, 범위| HR 웹 서버: 로컬로 로그온, 웹 페이지 실행<br>HR-SQL1: 로컬로 로그온, 모든 HR * 데이터베이스에서 읽기<br>HR-SQL2: 로컬로 로그온 하 고 급여 * 데이터베이스를 읽습니다. |
| Cost Center| 883944 |
| 위험 평가| 중간 비즈니스 영향: 중형; 개인 정보 중간 |
| 계정 제한| 다음에 로그온: 앞서 언급 한 서버에만 암호를 변경할 수 없습니다. MBI-Password 정책 |
| 수명| 무제한 |
| 검토 주기| Bi-연간 (소유자, 보안 팀, 개인 정보) |

### <a name="perform-risk-assessment-or-formal-review-of-service-account-usage"></a>서비스 계정 사용에 대 한 위험 평가 또는 공식 검토 수행

해당 사용 권한과 용도가 지정 된 경우 계정이 연결 된 응용 프로그램 또는 서비스 및 인프라 (손상 된 경우)에 발생할 수 있는 위험을 평가 합니다. 직접 및 간접 위험을 모두 고려해 야 합니다. 

* 악의적 사용자가에 대 한 직접 액세스 권한을 얻을 수 있는 것은 무엇 인가요?

* 서비스 계정에 액세스할 수 있는 다른 정보 또는 시스템은 무엇 인가요?

* 추가 사용 권한을 부여 하는 데 계정을 사용할 수 있나요?

* 사용 권한이 변경 되는 경우를 어떻게 알 수 있나요?

위험 평가를 수행 하 고 문서화 한 후에는 다음에 영향을 줄 수 있습니다.

* 계정 제한

* 계정 수명

* 계정 검토 요구 사항 (흐름 및 검토자)

### <a name="create-a-service-account-and-apply-account-restrictions"></a>서비스 계정 만들기 및 계정 제한 적용

관련 정보가 CMDB에 문서화 된 후에만 서비스 계정을 만들고 위험 평가를 수행 합니다. 계정 제한은 위험 평가에 맞춰야 합니다. 평가와 관련 하 여 다음과 같은 제한 사항을 고려 하세요.

* [계정 만료](/powershell/module/activedirectory/set-adaccountexpiration?view=winserver2012-ps)

   * 서비스 계정으로 사용 되는 모든 사용자 계정에 대해 사용 하기 위한 현실적인 종료 날짜를 정의 합니다. "계정 만료" 플래그를 사용 하 여이를 설정 합니다. 자세한 내용은[ 설정-ADAccountExpiration](/powershell/module/addsadministration/set-adaccountexpiration?view=win10-ps)를 참조 하세요. 

* 로그온 ([Logonworkstation](/powershell/module/addsadministration/set-aduser?view=win10-ps))

* [암호 정책](../../active-directory-domain-services/password-policy.md) 요구 사항

* 권한 있는 사용자에 대해서만 관리를 보장 하는 [OU 위치](/windows-server/identity/ad-ds/plan/delegating-administration-of-account-ous-and-resource-ous) 에서 만들기

* 서비스 계정 및 [서비스 계정](https://www.manageengine.com/products/active-directory-audit/how-to/audit-kerberos-authentication-events.html)에 대 한 [변경 내용을 검색 하는](/windows/security/threat-protection/auditing/audit-directory-service-changes) 감사를 설정 하 고 수집 합니다.

프로덕션 환경에 추가할 준비가 되 면 서비스 계정에 안전 하 게 액세스 권한을 부여 합니다. 

### <a name="schedule-regular-reviews-of-service-accounts"></a>서비스 계정의 정기적인 검토 예약

보통 및 높은 위험으로 분류 된 서비스 계정의 정기적인 검토를 설정 합니다. 검토에는 다음이 포함 되어야 합니다. 

* 계정에 대 한 지속적인 요구와 권한 및 범위의 근거에 대 한 소유자 증명입니다.

* 업스트림 및 다운스트림 연결 평가를 포함 하 여 개인 정보 및 보안 팀에서 검토 합니다.

* 의도 된 목적 으로만 사용 되 고 있는 감사 데이터

### <a name="deprovision-service-accounts"></a>서비스 계정 프로 비전 해제

프로 비전 해제 프로세스에서 먼저 권한 및 모니터를 제거 하 고 해당 하는 경우 계정을 제거 합니다.

다음 경우에 서비스 계정 프로 비전 해제:

* 서비스 계정이 만들어진 스크립트나 응용 프로그램이 사용 중지 되었습니다.

* 서비스 계정 (예: 특정 리소스에 대 한 액세스 권한)을 사용 하는 스크립트 또는 응용 프로그램 내의 함수는 사용이 중지 됩니다.

* 서비스 계정이 다른 서비스 계정으로 바뀌었습니다.

모든 사용 권한을 제거한 후에는이 프로세스를 사용 하 여 계정을 제거 합니다.

1. 연결 된 응용 프로그램 또는 스크립트가 프로 비전 해제 면 연결 된 서비스 계정에 대 한 로그인 및 리소스 액세스를 모니터링 하 여 다른 프로세스에서 사용 되지 않도록 합니다. 더 이상 필요 하지 않은 경우 다음 단계로 이동 합니다.

2. 서비스 계정에서 로그인을 사용 하지 않도록 설정 하 고 더 이상 필요 하지 않도록 합니다. 계정을 사용 하지 않도록 설정 된 상태로 유지 해야 하는 기간에 대 한 비즈니스 정책을 만듭니다.

3. 사용 하지 않도록 설정 된 정책 유지를 수행한 후 서비스 계정을 삭제 합니다. 

   * MSAs의 경우 PowerShell을 사용 하 여 [제거](/powershell/module/activedirectory/uninstall-adserviceaccount?view=winserver2012-ps) 하거나 관리 서비스 계정 컨테이너에서 수동으로 삭제할 수 있습니다.

   * 컴퓨터 또는 사용자 계정의 경우 Active Directory에서 계정을 수동으로 삭제할 수 있습니다.

## <a name="next-steps"></a>다음 단계
서비스 계정 보안 설정에 대 한 다음 문서를 참조 하세요.

* [온-프레미스 서비스 계정 소개](service-accounts-on-premises.md)

* [보안 그룹 관리 서비스 계정](service-accounts-group-managed.md)

* [독립 실행형 관리 서비스 계정 보안](service-accounts-standalone-managed.md)

* [보안 컴퓨터 계정](service-accounts-computer.md)

* [보안 사용자 계정](service-accounts-user-on-premises.md)

* [온-프레미스 서비스 계정 관리](service-accounts-govern-on-premises.md)