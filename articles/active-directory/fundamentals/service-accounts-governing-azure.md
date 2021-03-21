---
title: Azure Active Directory 서비스 계정 관리
description: Azure Active Directory에서 서비스 계정의 수명 주기를 관리 하기 위한 원칙과 절차
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 3/1/2021
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7f540ab40a14af09aa8667860286021f572eb6f1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104587902"
---
# <a name="governing-azure-ad-service-accounts"></a>Azure AD 서비스 계정 관리

Azure AD (Azure Active Directory)에는 서비스 계정으로 사용 [되는 관리 되는 id](service-accounts-managed-identities.md), [서비스 주체](service-accounts-principal.md)및 사용자 계정의 세 가지 유형이 있습니다. 자동화 된 사용을 위해 이러한 서비스 계정을 만들면 Azure 및 Azure AD의 리소스에 액세스할 수 있는 권한이 부여 됩니다. 리소스에는 Microsoft 365 서비스, SaaS (software as a service) 응용 프로그램, 사용자 지정 응용 프로그램, 데이터베이스, HR 시스템 등이 포함 될 수 있습니다. Azure AD 서비스 계정을 관리 하는 것은 보안 및 연속성을 보장 하기 위해 생성, 권한 및 수명 주기를 관리 한다는 것을 의미 합니다.

> [!IMPORTANT] 
> 기본적으로 보안 수준이 낮으므로 사용자 계정을 서비스 계정으로 사용 하지 않는 것이 좋습니다. 여기에는 서비스 주체로 변환 되지 않으므로 Azure AD에 동기화 되는 온-프레미스 서비스 계정이 포함 됩니다. 대신 관리 되는 id 또는 서비스 주체를 사용 하는 것이 좋습니다. 이번에는 서비스 사용자가 조건부 액세스 정책을 사용할 수 없지만 기능이 제공 됩니다.


## <a name="plan-your-service-account"></a>서비스 계정 계획

서비스 계정을 만들거나 응용 프로그램을 등록 하기 전에 서비스 계정의 키 정보를 문서화 합니다. 정보를 문서화 하면 계정을 효율적으로 모니터링 하 고 관리할 수 있습니다. 다음 데이터를 수집 하 고 중앙 집중식 CMDB (Configuration Management Database)에서 추적 하는 것이 좋습니다.

| 데이터| Description| 세부 정보 |
| - | - | - |
| 소유자| 서비스 계정을 관리 하 고 모니터링 하는 데 책임이 있는 사용자 또는 그룹입니다.| 계정을 모니터링 하 고 문제를 완화 하는 방법을 구현 하는 데 필요한 권한으로 소유자를 프로 비전 합니다. 문제 완화는 소유자 또는 요청을 통해 수행할 수 있습니다. |
| 용도| 계정이 사용 되는 방법입니다.| 서비스 계정을 특정 서비스, 응용 프로그램 또는 스크립트에 매핑합니다. 다중 사용 서비스 계정을 만들지 마십시오. |
| 사용 권한 (범위)| 예상 사용 권한 집합입니다.| 액세스 하는 리소스 및 해당 리소스에 대 한 사용 권한을 문서화 합니다. |
| CMDB 링크| 액세스할 리소스 및 서비스 계정이 사용 되는 스크립트에 대 한 링크입니다.| 필요한 업스트림 및 다운스트림 변경의 영향을 받을 수 있도록 리소스 및 스크립트 소유자를 문서화 해야 합니다. |
| 위험 평가| 계정이 손상 된 경우의 위험 및 비즈니스 영향.| 이 정보를 사용 하 여 사용 권한 범위를 좁히고 계정 정보에 대 한 액세스 권한이 있는 사용자를 결정 합니다. |
| 검토 기간| 소유자가 서비스 계정을 검토할 일정입니다.| 이를 사용 하 여 검토 통신 및 검토를 예약 합니다. 예약 된 검토 기간 이후에 특정 시간에 검토를 수행 하지 않은 경우 수행 해야 하는 작업을 문서화 합니다. |
| 수명| 예상 최대 계정 수명.| 이를 사용 하 여 소유자에 대 한 통신을 예약 하 고 궁극적으로 계정을 사용 하지 않도록 설정한 다음 삭제 합니다. 가능 하면 자격 증명을 자동으로 롤백할 수 없는 자격 증명에 대 한 만료 날짜를 설정 합니다. |
| 이름| 계정의 표준화 된 이름| 서비스 계정을 쉽게 검색, 정렬 및 필터링 할 수 있도록 모든 서비스 계정에 대 한 명명 스키마를 만듭니다. |


## <a name="use-the-principle-of-least-privileges"></a>최소 권한의 원칙 사용
작업을 수행 하는 데 필요한 권한만 서비스 계정에 부여 합니다. 서비스 계정에 높은 수준의 사용 권한 (예: 전역 관리자 권한)이 필요한 경우 이유를 평가 하 고 필요한 권한을 줄여야 합니다.

서비스 계정 권한에 대 한 다음 사례를 따르는 것이 좋습니다.

**권한**

* 서비스 계정에 기본 제공 역할을 할당 하지 마십시오. 대신 [Microsoft Graph에 대해 OAuth2 권한 부여 모델](/graph/api/resources/oauth2permissiongrant)을 사용 합니다.

* 서비스 사용자에 게 권한 있는 역할을 할당 해야 하는 경우 시간 제한 방식으로 특정 하 고 필요한 권한으로 [사용자 지정 역할](../roles/custom-create.md) 을 할당 하는 것이 좋습니다.

* 관리자 권한으로 서비스 계정을 그룹의 멤버로 포함 하지 마십시오. 

* [PowerShell을 사용 하 여 권한 있는 역할의 멤버를 열거](/powershell/module/azuread/get-azureaddirectoryrolemember)합니다 (예:).   
`Get-AzureADDirectoryRoleMember`및는 objectType "서비스 주체"를 필터링 합니다.

   또는 사용  
`Get-AzureADServicePrincipal | % { Get-AzureADServiceAppRoleAssignment -ObjectId $_ }`

* [OAuth 2.0 범위를 사용](../develop/v2-permissions-and-consent.md) 하 여 서비스 계정에서 리소스에 액세스할 수 있는 기능을 제한 합니다.
* 서비스 사용자 및 관리 되는 id는 로그온 한 사용자를 가장 하는 위임 된 컨텍스트에서 또는 응용 프로그램 컨텍스트의 서비스 계정으로 OAuth 2.0 범위를 사용할 수 있습니다. 응용 프로그램 컨텍스트에는 로그온이 없습니다.

* 리소스가 적절 한지 확인 하기 위해 범위 서비스 계정 요청을 확인 합니다. 예를 들어, 계정에서 파일을 요청 하는 경우에는 실제로 파일만 필요한 경우를 평가 합니다. 모두 읽기. 모두. 사용 권한에 대 한 자세한 내용은 [Microsoft Graph 사용 권한 참조](/graph/permissions-reference)를 참조 하세요.

* 리소스에 요청 된 액세스를 사용 하 여 응용 프로그램 또는 API의 개발자를 신뢰 하는지 확인 합니다.

**기간**

* 서비스 계정 자격 증명 (클라이언트 암호, 인증서)을 예상 사용 기간으로 제한 합니다.

* 정기 일정은 서비스 계정의 사용 및 용도를 검토 합니다. 계정이 만료 되기 전에 검토가 수행 되는지 확인 합니다.

목적, 범위 및 필요한 권한을 명확 하 게 이해 했으면 서비스 계정을 만듭니다. 

[관리 id 만들기 및 사용](../../app-service/overview-managed-identity.md?tabs=dotnet)

[서비스 사용자 만들기 및 사용](../develop/howto-create-service-principal-portal.md)

가능 하면 관리 되는 id를 사용 합니다. 관리 id를 사용할 수 없는 경우 서비스 주체를 사용 합니다. 서비스 주체를 사용할 수 없는 경우에만 Azure AD 사용자 계정을 사용 합니다.

 

## <a name="build-a-lifecycle-process"></a>수명 주기 프로세스 빌드

서비스 계정의 수명 주기를 관리 하는 것은 계획에서 시작 하 고 영구적으로 삭제 됩니다. 

이 문서에서는 이전에 계획 및 생성 부분에 대해 살펴보았습니다. 또한 모니터링 하 고, 사용 권한을 검토 하 고, 계정의 지속적인 사용을 확인 하 고, 궁극적으로 계정의 프로 비전을 해제 해야 합니다.

### <a name="monitor-service-accounts"></a>서비스 계정 모니터링

서비스 계정을 사전에 모니터링 하 여 서비스 계정의 사용 패턴이 의도 한 패턴을 반영 하 고 서비스 계정이 적극적으로 사용 되 고 있는지 확인 합니다.

**다음 방법 중 하나를 사용 하 여 서비스 계정 로그인을 수집 하 고 모니터링 합니다.**

* Azure ad 포털에서 Azure AD Sign-In 로그 사용

* Azure AD Sign-In 로그를 [Azure Storage](../../storage/index.yml), [azure Event Hubs](../../event-hubs/index.yml)또는 [Azure Monitor](../../azure-monitor/logs/data-platform-logs.md)로 내보냅니다.


![서비스 사용자 로그인 화면을 보여 주는 스크린샷](./media/securing-service-accounts/service-accounts-govern-azure-1.png)

**Sign-In 로그에서 확인 해야 하는 인텔리전스는 다음과 같습니다.**

* 더 이상 테 넌 트에 로그인 하지 않는 서비스 계정이 있나요?

* 서비스 계정의 로그인 패턴이 변경 되나요?

Azure AD 로그인 로그를 내보내고 Azure 센티널 등의 기존 SIEM (보안 정보 및 이벤트 관리) 도구로 가져오는 것이 좋습니다. SIEM을 사용 하 여 경고 및 대시보드를 빌드합니다.

### <a name="review-service-account-permissions"></a>서비스 계정 권한 검토

서비스 계정에서 액세스 권한을 부여 하 고 범위를 정기적으로 검토 하 여 제거를 줄일 수 있는지 확인 합니다.

* [PowerShell](/powershell/module/azuread/get-azureadserviceprincipaloauth2permissiongrant) 을 사용 하 여 서비스 계정에 대 한 동의가 부여 되는 범위를 [확인 하 고 문서화](https://gist.github.com/psignoret/41793f8c6211d2df5051d77ca3728c09) 하는 자동화를 빌드합니다.

* PowerShell을 사용 하 여 [기존 서비스 사용자의 자격 증명을 검토](https://github.com/AzureAD/AzureADAssessment) 하 고 유효성을 확인 합니다.

* 서비스 사용자의 자격 증명을 "기간 제한 없음"으로 설정 하지 마십시오.

* 가능 하면 Azure KeyVault에 저장 된 인증서 또는 자격 증명을 사용 합니다.

Microsoft의 free PowerShell 샘플은 서비스 주체의 OAuth2 권한 부여 및 자격 증명 정보를 수집 하 고, CSV (쉼표로 구분 된 값) 파일에 기록 하 고, 데이터를 해석 하 고 사용 하는 Power BI 샘플 대시보드를 수집 합니다. 자세한 내용은 [AzureAD/AzureADAssessment: AZURE AD 테 넌 트 상태 및 구성 평가를 위한 도구 (github.com)](https://github.com/AzureAD/AzureADAssessment) 를 참조 하세요.

### <a name="recertify-service-account-use"></a>다시 인증할 서비스 계정 사용

서비스 계정이 정기적으로 소유자와 보안 또는 IT 팀에 의해 정기적으로 검토 되도록 검토 프로세스를 설정 합니다. 

**프로세스는 다음을 포함 해야 합니다.**

* 각 서비스 계정의 검토 주기를 확인 하는 방법 (CMDB에 문서화 되어야 함)

* 검토를 시작 하기 전에 소유자 및 보안 또는 IT 팀에 대 한 통신을 시작 합니다.

* 검토가 누락 된 경우 경고 통신의 타이밍과 내용

* 소유자가 검토 하거나 응답 하지 못할 경우 수행할 작업에 대 한 지침입니다. 예를 들어 검토가 완료 될 때까지 계정을 비활성화 (삭제 하지는 않음) 할 수 있습니다.

* 업스트림 및 다운스트림 종속성을 확인 하 고 다른 리소스 소유자에 게 모든 효과를 알리는 방법에 대 한 지침입니다.

**검토에는 소유자 및 IT 파트너의 인증을 포함 해야 합니다.**

* 계정이 여전히 필요 합니다.

* 계정에 부여 된 권한이 적절 하 고 필요 하거나 변경이 요청 되었습니다.

* 계정 및 해당 자격 증명에 대 한 액세스가 제어 됩니다.

* 계정에서 사용 하는 자격 증명은 계정이 평가 된 위험 (자격 증명 형식 및 자격 증명 수명 모두)에 대해 적절 합니다.

* 계정의 위험 점수가 마지막 재인증 이후 변경 되지 않았습니다.

* 계정의 예상 수명 및 다음 재인증 날짜에 대 한 업데이트입니다.

### <a name="deprovision-service-accounts"></a>서비스 계정 프로 비전 해제

* * 다음과 같은 경우에는 서비스 계정을 프로 비전 해제 합니다. * * * *

* 서비스 계정이 만들어진 스크립트나 응용 프로그램이 사용 중지 되었습니다.

* 서비스 계정 (예: 특정 리소스에 대 한 액세스 권한)을 사용 하는 스크립트 또는 응용 프로그램 내의 함수는 사용이 중지 됩니다.


* 서비스 계정이 다른 서비스 계정으로 대체 됩니다.

* 자격 증명이 만료 되었거나, 계정이 작동 하지 않거나, 불만 사항이 없습니다.

**프로 비전 해제에 대 한 프로세스에는 다음 태스크가 포함 됩니다.**

1. 연결 된 응용 프로그램 또는 스크립트가 프로 비전 해제 면 서비스 계정에서 로그인 및 리소스 액세스를 [모니터링](../reports-monitoring/concept-sign-ins.md#sign-ins-report) 합니다.

   * 계정이 아직 활성 상태인 경우 후속 단계를 수행 하기 전에 해당 계정이 사용 되는 방법을 확인 합니다.
 
2. 관리 서비스 id 인 경우 서비스 계정에서 로그인을 사용 하지 않도록 설정 하지만 디렉터리에서 제거 하지는 않습니다.

3. 서비스 계정에 대 한 역할 할당 및 OAuth2 동의 부여를 취소 합니다.

4. 정의 된 기간 후 소유자에 게 경고를 발생 한 후 디렉터리에서 서비스 계정을 삭제 합니다.

## <a name="next-steps"></a>다음 단계
Azure 서비스 계정을 보호 하는 방법에 대 한 자세한 내용은 다음을 참조 하세요.

[Azure 서비스 계정 소개](service-accounts-introduction-azure.md)

[관리 ID 보안](service-accounts-managed-identities.md)

[서비스 원칙 보안](service-accounts-principal.md)




 

