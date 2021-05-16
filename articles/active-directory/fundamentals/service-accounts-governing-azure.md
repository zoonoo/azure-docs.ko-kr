---
title: Azure Active Directory 서비스 계정 관리
description: Azure Active Directory에서 서비스 계정의 수명 주기를 관리하기 위한 원칙과 프로시저
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
ms.openlocfilehash: aec870e6027d9a6d2e6c3b66363f93c73fad5e78
ms.sourcegitcommit: 5f785599310d77a4edcf653d7d3d22466f7e05e1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/27/2021
ms.locfileid: "108064146"
---
# <a name="governing-azure-ad-service-accounts"></a>Azure AD 서비스 계정의 관리

Azure Active Directory(Azure AD)에는 세 가지 유형의 서비스 계정이 있습니다.: [관리 ID](service-accounts-managed-identities.md), [서비스 사용자](service-accounts-principal.md)및 서비스 계정으로 등록된 사용자 계정 자동화된 사용을 위해 이것들을 만들면 Azure 및 Azure AD의 리소스에 액세스할 수 있는 권한이 부여됩니다. 리소스에는 Microsoft 365 서비스,서비스로서의 소프트웨어(SaaS) 애플리케이션, 사용자 지정 애플리케이션, 데이터베이스, HR 시스템 등이 포함될 수 있습니다. Azure AD 서비스 계정을 관리하는 것은 보안 및 연속성을 보장하기 위해 생성, 권한 및 수명 주기를 관리하는 것을 의미합니다.

> [!IMPORTANT] 
> 기본적으로 보안 수준이 낮으므로 사용자 계정을 서비스 계정으로 사용하지 않는 것이 좋습니다. 여기에는 서비스 주체로 변환되지 않으므로 Azure AD에 동기화되는 온-프레미스 서비스 계정이 포함됩니다. 대신 관리 ID 또는 서비스 주체를 사용하는 것이 좋습니다. 이번에는 서비스 사용자가 조건부 액세스 정책을 사용할 수 없지만 기능이 제공되는 데 주목합니다.


## <a name="plan-your-service-account"></a>서비스 계정 계획

서비스 계정을 만들거나 애플리케이션을 등록하기 전에 서비스 계정의 키 정보를 문서화합니다. 정보를 문서화하면 계정을 효율적으로 모니터링하고 관리할 수 있습니다. 다음 데이터를 수집하고 중앙 집중식 구성 관리 데이터 베이스(CMDB)에서 추적하는 것이 좋습니다.

| 데이터| Description| 세부 정보 |
| - | - | - |
| 소유자| 서비스 계정을 관리하고 모니터링하는 데 책임이 있는 사용자 또는 그룹입니다.| 계정을 모니터링하고 문제를 완화하는 방법을 구현하는 데 필요한 권한으로 소유자를 프로비전합니다. 이슈 완화는 소유자 또는 IT에 요청을 통해 수행할 수 있습니다. |
| 목적| 계정이 사용될 방법입니다.| 서비스 계정을 특정 서비스, 애플리케이션 또는 스크립트에 매핑합니다. 다중 사용 서비스 계정을 만들지 마십시오. |
| 권한(범위)| 예상된 사용 권한 집합.| 액세스할 리소스 및 해당 리소스에 대한 사용 권한을 문서화합니다. |
| CMDB 링크| 액세스될 리소스 및 서비스 계정이 사용되는 스크립트에 대한 링크입니다.| 필요한 업스트림 및 다운스트림 변경의 영향을 통신할 수 있도록 리소스 및 스크립트 소유자를 문서화해야 합니다. |
| 위험 평가| 계정이 손상된 경우의 위험 및 비즈니스 영향.| 이 정보를 사용하여 사용 권한 범위를 좁히고 계정 정보에 대한 액세스 권한이 있는 사용자를 결정합니다. |
| 검토 기간| 소유자가 서비스 계정을 검토할 일정입니다.| 이를 사용하여 검토 통신 및 검토를 예약합니다. 예약된 검토 기간 이후에 특정 시간에 검토를 수행하지 않은 경우 수행해야 하는 일을 문서화합니다. |
| 수명| 예상되는 계정의 최대 수명.| 이를 사용하여 소유자에 대한 통신을 예약하고, 궁극적으로 계정을 사용하지 않도록 설정한 다음 삭제합니다. 가능하면 자격 증명을 자동으로 회전할 수 없는 자격 증명에 대한 만료 날짜를 설정합니다. |
| Name| 계정의 표준화된 이름| 서비스 계정을 쉽게 검색, 정렬 및 필터링할 수 있도록 모든 서비스 계정에 대한 명명 스키마를 만듭니다. |


## <a name="use-the-principle-of-least-privileges"></a>최소 권한 원칙의 적용
작업을 수행하는 데 필요한 권한만 서비스 계정에 부여합니다. 서비스 계정에 예를 들어 전역 관리자 권한처럼 높은 수준의 사용 권한이 필요한 경우, 이유를 평가하고 필요한 권한을 줄여야 합니다.

서비스 계정 권한에 대한 다음 관례를 따르는 것이 좋습니다.

**권한**

* 서비스 계정에 기본 제공 역할을 할당하지 마십시오. 대신 [Microsoft Graph에 대해 OAuth2 권한 부여 모델](/graph/api/resources/oauth2permissiongrant)을 사용합니다.

* 서비스 사용자에게 권한 있는 역할을 할당해야 하는 경우, 특정 필수 권한을 가진 [사용자 지정 역할](../roles/custom-create.md)을 시간 바인딩 방식으로 할당하는 것이 좋습니다.

* 관리자 권한으로 서비스 계정을 어느 그룹의 멤버로 포함하지 마십시오. 

* [PowerShell을 사용하여 권한 있는 역할의 멤버를 열거합니다](/powershell/module/azuread/get-azureaddirectoryrolemember), 예를 들자면   
‎`Get-AzureADDirectoryRoleMember` 및 objectType "서비스 주체"를 필터링합니다.

   또는 사용합니다.  
‎   `Get-AzureADServicePrincipal | % { Get-AzureADServiceAppRoleAssignment -ObjectId $_ }`

* [OAuth 2.0 범위를 사용](../develop/v2-permissions-and-consent.md)하여 서비스 계정에서 리소스에 액세스할 수 있는 기능을 제한합니다.
* 서비스 사용자 및 관리 ID는 로그온한 사용자를 가장하는 위임된 컨텍스트에서 또는 애플리케이션 컨텍스트의 서비스 계정으로 OAuth 2.0 범위를 사용할 수 있습니다. 애플리케이션 컨텍스트에 no는 로그온되어 있습니다.

* 리소스가 적절한지 확인하기 위해 범위 서비스 계정 요청을 확인합니다. 예를 들어, 계정에서 Files.ReadWrite.All 파일을 요청하는 경우에는 실제로 File.Read.All 파일만 필요한 경우를 평가합니다. 사용 권한에 대한 자세한 내용은 [Microsoft Graph 사용 권한 참조](/graph/permissions-reference)를 참조하세요.

* 리소스에 요청된 액세스를 사용하여 애플리케이션 또는 API의 개발자를 신뢰하는지 확인합니다.

**기간**

* 서비스 계정 자격 증명(클라이언트 암호, 인증서)을 예상 사용 기간으로 제한합니다.

* 정기 일정은 서비스 계정의 사용 및 목적을 검토합니다. 계정이 만료되기 전에 검토가 수행되는지 확인합니다.

일단 목적, 범위 및 필요한 권한을 명확하게 이해했으면, 서비스 계정을 만듭니다. 

[관리 ID 생성 및 사용](../../app-service/overview-managed-identity.md?tabs=dotnet)

[서비스 사용자 생성 및 사용](../develop/howto-create-service-principal-portal.md)

가능하면 관리 ID를 사용합니다. 관리 ID를 사용할 수 없는 경우 서비스 주체를 사용합니다. 서비스 주체를 사용할 수 없는 경우에만 Azure AD 사용자 계정을 사용합니다.

 

## <a name="build-a-lifecycle-process"></a>수명 주기 프로세스 빌드

서비스 계정의 수명 주기를 관리하는 것은 계획으로 시작하고 영구적 삭제로 마칩니다. 

이 문서에서는 이전에 계획 및 생성 부분에 대해 살펴보았습니다. 또한 모니터링하고, 사용 권한을 검토하고, 계정의 지속적인 사용을 확인하고, 궁극적으로 계정의 프로비전을 해제해야 합니다.

### <a name="monitor-service-accounts"></a>서비스 계정 모니터링

서비스 계정을 사전에 모니터링하여 서비스 계정의 사용 패턴이 의도한 패턴을 반영하고 서비스 계정이 적극적으로 사용되고 있는지 확인합니다.

**다음 방법 중 하나를 사용하여 서비스 계정 로그인을 수집하고 모니터링합니다.**

* Azure AD 포털에서 Azure AD Sign-In 로그 사용

* Azure AD Sign-In 로그를 [Azure Storage](../../storage/index.yml), [azure Event Hubs](../../event-hubs/index.yml)또는 [Azure Monitor](../../azure-monitor/logs/data-platform-logs.md)로 내보냅니다.


![화면에서 서비스 사용자 로그인을 보여 주는 스크린샷](./media/securing-service-accounts/service-accounts-govern-azure-1.png)

**Sign-In 로그에서 확인해야 하는 인텔리전스는 다음과 같습니다.**

* 더 이상 테넌트에 로그인하지 않는 서비스 계정이 있나요?

* 서비스 계정의 로그인 패턴에 변경이 있나요?

Azure AD sign-In 로그를 내보내고 그것들을 Azure Sentinel 등의 기존 보안 정보 및 이벤트 관리(SIEM) 도구로 가져오는 것이 좋습니다. SIEM을 사용하여 경고 및 대시보드를 빌드합니다.

### <a name="review-service-account-permissions"></a>서비스 계정 권한 검토

서비스 계정이 허용하는 액세스 범위 및 권한을 정기적으로 검토하여 축소할 수 있는지 확인합니다.

* [PowerShell](/powershell/module/azuread/get-azureadserviceprincipaloauth2permissiongrant)을 사용하여 서비스 계정에 대한 동의가 부여되는 범위를 확인하고 [문서화와 확인을 자동화를 빌드](https://gist.github.com/psignoret/41793f8c6211d2df5051d77ca3728c09)합니다.

* PowerShell을 사용하여 [기존 서비스 사용자의 자격 증명을 검토](https://github.com/AzureAD/AzureADAssessment)하고 유효성을 확인합니다.

* 서비스 사용자의 자격 증명을 "만료 없음"으로 설정하지 마십시오.

* 가능하면 Azure KeyVault에 저장된 인증서 또는 자격 증명을 사용합니다.

Microsoft의 free PowerShell 샘플은 서비스 주체의 OAuth2 권한 부여 및 자격 증명 정보를 수집하고, 쉼표로 구분된 값(CSV) 파일에 기록하고, 데이터를 해석하고 사용하는 Power BI 샘플 대시보드를 수집합니다. 자세한 내용은 [AzureAD/AzureADAssessment: Azure AD 테넌트 상태 및 구성 평가를 위한 도구 (github.com)](https://github.com/AzureAD/AzureADAssessment)를 참조하세요.

### <a name="recertify-service-account-use"></a>다시 인증할 서비스 계정 사용

서비스 계정이 정기적으로 소유자와 보안 또는 IT 팀에 의해 정기적으로 검토되도록 검토 프로세스를 설정합니다. 

**이 프로세스는 다음을 포함합니다.:**

* 각 서비스 계정의 검토 주기를 확인하는 방법 (CMDB에 문서화되어야 함)

* 검토를 시작하기 전에 소유자 및 보안 또는 IT 팀에 대한 통신을 시작합니다.

* 검토가 누락된 경우 경고 통신의 타이밍과 내용.

* 소유자가 검토하거나 응답하지 못할 경우 수행할 작업에 대한 지침입니다. 예를 들어 검토가 완료될 때까지 계정을 비활성화 (삭제하지는 않음)할 수 있습니다.

* 업스트림 및 다운스트림 종속성을 확인하고 다른 리소스 소유자에게 모든 효과를 알리는 방법에 대한 지침입니다.

**검토에는 다음을 인증하는 소유자와 해당 IT 파트너가 포함되어야 합니다.**

* 계정이 여전히 필요합니다.

* 계정에 부여된 권한이 적절하고 필요하며, 혹은 변경이 요청되었습니다.

* 계정 및 해당 자격 증명에 대한 액세스가 제어됩니다.

* 계정에서 사용하는 자격 증명은 계정이 평가된 위험 (자격 증명 형식 및 자격 증명 수명 모두)에 대해 적절합니다.

* 계정의 위험 점수가 마지막 재인증 이후 변경되지 않았습니다.

* 계정의 예상 수명 및 다음 재인증 날짜에 대한 업데이트.

### <a name="deprovision-service-accounts"></a>서비스 계정 프로비전 해제

**다음과 같은 경우에는 서비스 계정을 프로비전 해제합니다.****

* 서비스 계정이 생성된 스크립트 또는 애플리케이션이 폐기됩니다.

* 서비스 계정이 사용되는 스크립트 또는 애플리케이션 내의 기능(예: 특정 리소스에 대한 액세스)이 폐기됩니다.


* 서비스 계정이 다른 서비스 계정으로 대체된 경우입니다.

* 자격 증명이 만료되었거나, 계정이 작동하지 않거나, 불만 사항이 없습니다.

**프로비전 해제에 대한 프로세스에는 다음 태스크가 포함됩니다.**

1. 일단 연결된 애플리케이션 또는 스크립트가 프로비전 해제되면, 서비스 계정에서 로그인 및 리소스 액세스를 [모니터링 로그인](../reports-monitoring/concept-sign-ins.md)을 합니다.

   * 계정이 아직 활성 상태인 경우 후속 단계를 수행하기 전에 해당 계정이 사용되는 방법을 확인합니다.
 
2. 관리 서비스 ID 인 경우 서비스 계정에서 로그인을 사용하지 않도록 설정하지만 디렉터리에서 제거하지는 않습니다.

3. 서비스 계정에 대한 역할 할당 및 OAuth2 동의 부여를 취소합니다.

4. 정해진 기간 후 소유자에게 경고를 발생한 후 디렉터리에서 서비스 계정을 삭제합니다.

## <a name="next-steps"></a>다음 단계
Azure 서비스 계정에 대한 자세한 내용은 다음을 참조하십시오:

[Azure 서비스 계정 소개](service-accounts-introduction-azure.md)

[관리 ID 보안](service-accounts-managed-identities.md)

[서비스 주체 보안](service-accounts-principal.md)




 

