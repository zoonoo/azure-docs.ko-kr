---
title: Azure Active Directory에서 서비스 사용자 보안
description: 서비스 사용자를 찾아 평가 하 고 보안 합니다.
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
ms.openlocfilehash: eaad4995abcf54e9ca7040d570c7a9abbdbc9cf5
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102032418"
---
# <a name="securing-service-principals"></a>서비스 사용자 보안

Azure Active Directory (Azure AD) [서비스 주체](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals) 는 단일 테 넌 트 또는 디렉터리의 응용 프로그램 개체에 대 한 로컬 표현입니다.  응용 프로그램 인스턴스의 id로 작동 합니다. 서비스 주체는 응용 프로그램에 액세스할 수 있는 사용자와 응용 프로그램이 액세스할 수 있는 리소스를 정의 합니다. 응용 프로그램이 사용 되는 각 테 넌 트에 서비스 주체가 만들어지고 전역적으로 고유한 응용 프로그램 개체를 참조 합니다. 테 넌 트는 서비스 사용자의 로그인 및 리소스에 대 한 액세스를 보호 합니다.  

### <a name="tenant-service-principal-relationships"></a>테 넌 트-서비스 주체 관계
단일 테 넌 트 응용 프로그램의 홈 테 넌 트에는 하나의 서비스 사용자만 있습니다. 다중 테 넌 트 웹 응용 프로그램 또는 API에는 각 테 넌 트의 서비스 주체가 필요 합니다. 서비스 사용자는 해당 테 넌 트의 사용자가 응용 프로그램 또는 API의 사용에 동의한 때 생성 됩니다. 이 동의는 다중 테 넌 트 응용 프로그램과 연결 된 서비스 주체 간에 일 대 다 관계를 만듭니다.

다중 테 넌 트 응용 프로그램은 단일 테 넌 트에 속하며 다른 테 넌 트의 인스턴스를 갖도록 설계 되었습니다. SaaS (software as a service) 응용 프로그램 대부분은 다중 테 넌 트 용으로 설계 되었습니다. 서비스 주체를 사용 하 여 단일 테 넌 트 및 다중 테 넌 트 사용 사례에서 응용 프로그램 및 사용자의 올바른 보안 상태를 확인 합니다.

## <a name="applicationid-and-objectid"></a>ApplicationID 및 ObjectID

지정 된 응용 프로그램 인스턴스에는 ApplicationID (ClientID 라고도 함) 및 ObjectID의 두 가지 고유한 속성이 있습니다.

> [!NOTE] 
> 인증 관련 작업의 컨텍스트에서 응용 프로그램을 느슨하게 참조할 때 응용 프로그램 및 서비스 주체 라는 용어를 교대로 사용 한다는 것을 알 수 있습니다. 그러나 Azure AD에서는 두 가지 응용 프로그램의 두 가지 표현이 있습니다.
 

ApplicationID는 전역 응용 프로그램을 나타내며, 테 넌 트의 모든 응용 프로그램 인스턴스에 대해 동일 합니다. ObjectID는 응용 프로그램 개체에 대 한 고유한 값 이며 서비스 주체를 나타냅니다. 사용자, 그룹 및 기타 리소스와 마찬가지로 ObjectID는 Azure AD에서 응용 프로그램 인스턴스를 고유 하 게 식별 하는 데 도움이 됩니다.

이 항목에 대 한 자세한 내용은 [응용 프로그램 및 서비스 사용자 관계](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals)를 참조 하세요.

Azure PowerShell, Azure CLI, Microsoft Graph, Azure Portal 및 기타 도구를 사용 하 여 테 넌 트에서 응용 프로그램 및 해당 ObjectID (서비스 주체 개체)를 만들 수도 있습니다. 

![응용 프로그램 ID 및 개체 ID 필드가 강조 표시 된 새 응용 프로그램 등록을 보여 주는 스크린샷](./media/securing-service-accounts/secure-principal-image-1.png)

## <a name="service-principal-authentication"></a>서비스 주체 인증

서비스 주체를 사용 하는 인증에는 클라이언트 인증서와 클라이언트 비밀 이라는 두 가지 메커니즘이 있습니다. 

![ 강조 표시 된 인증서 및 클라이언트 암호 영역을 보여 주는 새 앱 페이지의 스크린샷](./media/securing-service-accounts/secure-principal-certificates.png)

인증서가 더 안전 합니다. 가능한 경우 클라이언트 인증서를 사용 합니다. 클라이언트 암호와 달리 클라이언트 인증서는 실수로 코드에 포함 될 수 없습니다. 가능 하면 인증서 및 비밀 관리를 위해 Azure Key Vault를 사용 하 여 하드웨어 보안 모듈에서 보호 하는 키를 사용 하 여 다음 자산을 암호화 합니다.

* 인증 키

* 저장소 계정 키

* 데이터 암호화 키

* .pfx 파일

* 암호 

Azure Key Vault에 대 한 자세한 내용과 인증서 및 비밀 관리에이를 사용 하는 방법에 대 한 자세한 내용은 [Azure Key Vault 정보](https://docs.microsoft.com/azure/key-vault/general/overview) 및 [Azure Portal를 사용 하 여 Key Vault 액세스 정책 할당](https://docs.microsoft.com/azure/key-vault/general/assign-access-policy-portal)을 참조 하세요. 

 ### <a name="challenges-and-mitigations"></a>과제 및 완화
다음 표에서는 서비스 주체를 사용 하는 경우 발생할 수 있는 문제를 완화 하는 방법을 보여 줍니다.


| 과제| 해결 방법 |
| - | - |
| 권한 있는 역할에 할당 된 서비스 사용자에 대 한 액세스 검토| 이 기능은 미리 보기 상태 이며 아직 광범위 하 게 사용할 수 없습니다. |
| 서비스 사용자의 액세스 검토| Azure Portal를 사용 하 여 리소스의 액세스 제어 목록을 수동으로 확인 합니다. |
| Permissioned 서비스 주체 초과| Automation 서비스 계정 또는 서비스 주체를 만들 때 작업에 필요한 권한만 제공 합니다. 기존 서비스 주체를 평가 하 여 권한을 낮출 수 있는지 확인 합니다. |
|서비스 사용자의 자격 증명 또는 인증 방법에 대 한 수정 사항을 식별 합니다. |이 문제를 완화 하는 데 도움이 되는 중요 한 운영 보고서 통합 문서를 사용 합니다. [이 블로그 게시물의 설명을 참조](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/azure-ad-workbook-to-help-you-assess-solorigate-risk/ba-p/2010718)하세요.|

## <a name="find-accounts-using-service-principals"></a>서비스 주체를 사용 하 여 계정 찾기
다음 명령을 실행 하 여 서비스 주체를 사용 하는 계정을 찾습니다.

Azure CLI 사용


`az ad sp list`

PowerShell 사용

`Get-AzureADServicePrincipal -All:$true` 


자세한 내용은 [get-azureadserviceprincipal](https://docs.microsoft.com/powershell/module/azuread/get-azureadserviceprincipal?view=azureadps-2.0) 를 참조 하세요.

## <a name="assess-service-principal-security"></a>서비스 사용자 보안 평가

서비스 사용자의 보안을 평가 하려면 권한 및 자격 증명 저장소를 평가 해야 합니다.

다음 정보를 사용 하 여 잠재적인 문제를 완화 합니다.
|과제 | 해결 방법|
| - | - |
| 다중 테 넌 트 앱에 동의한 사용자를 검색 하 고 다중 테 넌 트 앱에 대 한 불법 승인 부여를 검색 합니다. | 다음 PowerShell을 실행 하 여 다중 테 넌 트 앱을 찾습니다.<br>`Get-AzureADServicePrincipal -All:$true ? {$_.Tags -eq WindowsAzureActiveDirectoryIntegratedApp"}`<br>사용자 동의를 사용 하지 않습니다. <br>선택한 권한에 대해 확인 된 게시자의 사용자 동의 허용 (권장) <br> 조건부 액세스를 사용 하 여 신뢰할 수 없는 위치의 서비스 사용자를 차단할 수 있습니다. 사용자 컨텍스트에서 구성 하 고 해당 토큰을 사용 하 여 서비스 주체를 트리거합니다.|
|서비스 주체를 사용 하는 스크립트에서 하드 코드 된 공유 암호 사용|인증서를 사용 하거나 Azure Key Vault 합니다.|
|인증서 또는 암호를 사용 하는 사용자 추적| Azure AD 로그인 로그를 사용 하 여 서비스 주체의 로그인을 모니터링 합니다.|
조건부 액세스를 사용 하 여 서비스 사용자의 로그인을 관리할 수 없습니다.| Azure AD 로그인 로그를 사용 하 여 로그인 모니터링
| 기본 Azure RBAC 역할은 기여자입니다. |요구 사항을 평가 하 고이 요구를 충족 하기 위해 가장 가능성이 낮은 권한이 있는 역할을 적용 합니다.|

## <a name="move-from-a-user-account-to-a-service-principal"></a>사용자 계정에서 서비스 주체로 이동  
Azure 사용자 계정을 서비스 사용자로 사용 하는 경우 [관리 되는 id](https://docs.microsoft.com/azure/app-service/overview-managed-identity?tabs=dotnet) 또는 서비스 주체로 이동할 수 있는지 평가 합니다. 관리 id를 사용할 수 없는 경우 필요한 작업을 실행할 수 있는 충분 한 권한 및 범위가 있는 서비스 주체를 프로 비전 합니다. [응용 프로그램을 등록](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)하거나 [PowerShell](https://docs.microsoft.com/azure/active-directory/develop/howto-authenticate-service-principal-powershell)을 사용 하 여 서비스 주체를 만들 수 있습니다.

Microsoft Graph를 사용 하는 경우 [이 예제와 같이](/powershell/azure/create-azure-service-principal-azureps)특정 API의 설명서를 확인 하 고 응용 프로그램에 대 한 사용 권한 유형이 지원 되는 것으로 표시 되는지 확인 합니다.

## <a name="next-steps"></a>다음 단계

**서비스 사용자에 대 한 자세한 내용은 다음을 확인 하세요.**

[서비스 주체 만들기](../develop/howto-create-service-principal-portal.md)

 [서비스 사용자 로그인 모니터링](../reports-monitoring/concept-all-sign-ins.md#sign-ins-report)

**서비스 계정을 보호 하는 방법에 대 한 자세한 내용은 다음을 확인 하세요.**

[Azure 서비스 계정 소개](service-accounts-introduction-azure.md)

[관리 되는 id 보안](service-accounts-managed-identities.md)

[Azure 서비스 계정 관리](service-accounts-governing-azure.md)

[온-프레미스 서비스 계정 소개](service-accounts-on-premises.md)
