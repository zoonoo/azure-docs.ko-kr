---
title: Azure Active Directory에 서비스 주체 만들기
description: 서비스 주체를 찾아 평가하고 보안합니다.
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
ms.openlocfilehash: 297c0a53fb2ab4ee0b2c5291cabf5a63c8841664
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105604705"
---
# <a name="securing-service-principals"></a>서비스 주체 보안

Azure Active Directory(Azure AD) [서비스 주체](../develop/app-objects-and-service-principals.md)는 단일 테넌트 또는 디렉터리의 애플리케이션 개체에 대한 로컬 표현입니다.  ‎애플리케이션 인스턴스의 ID로 작동합니다. 서비스 주체는 애플리케이션에 액세스할 수 있는 사용자와 애플리케이션이 액세스할 수 있는 리소스를 정의합니다. 서비스 주체는 애플리케이션이 사용되는 각 테넌트에 만들어지고 전역적으로 고유한 애플리케이션 개체를 참조합니다. 테넌트는 서비스 주체의 로그인 및 리소스에 대한 액세스를 보호합니다.  

### <a name="tenant-service-principal-relationships"></a>테넌트-서비스 주체 관계
단일 테넌트 애플리케이션에는 해당 홈 테넌트에 하나의 서비스 주체만 있습니다. 다중 테넌트 웹 애플리케이션 또는 API에는 각 테넌트의 서비스 주체가 있어야 합니다. 서비스 주체는 해당 테넌트의 사용자가 애플리케이션이나 API의 사용에 동의할 때 생성됩니다. 이 동의는 다중 테넌트 애플리케이션과 연결된 서비스 주체 간에 일 대 다 관계를 만듭니다.

다중 테넌트 애플리케이션은 단일 테넌트에 속하며 다른 테넌트의 인스턴스를 갖도록 설계되었습니다. 대부분의 SaaS(software-as-a-service) 애플리케이션은 다중 테넌트 용으로 설계되었습니다. 서비스 주체를 사용하여 단일 테넌트와 다중 테넌트 사용 사례 모두에서 애플리케이션 및 사용자의 적합한 보안 상태를 확인합니다.

## <a name="applicationid-and-objectid"></a>ApplicationID 및 ObjectID

지정된 애플리케이션 인스턴스에는 ApplicationID(ClientID 라고도 함) 및 ObjectID의 두 가지 고유한 속성이 있습니다.

> [!NOTE] 
> 인증 관련 작업의 맥락에서 엄밀하지 않은 애플리케이션을 나타낼 때 애플리케이션과 서비스 주체라는 용어를 같은 의미로 사용한다는 것을 알 수 있습니다. 그러나 Azure AD에서는 애플리케이션을 나타내는 두 가지 표현이 있습니다.
 

ApplicationID는 전역 애플리케이션을 나타내며, 테넌트의 모든 애플리케이션 인스턴스에 대해 동일합니다. ObjectID는 애플리케이션 개체의 고유한 값이며 서비스 주체를 나타냅니다. 사용자, 그룹 및 기타 리소스와 마찬가지로 ObjectID는 Azure AD에서 애플리케이션 인스턴스를 고유하게 식별하는 데 도움이 됩니다.

이 토픽에 대한 자세한 내용은 [애플리케이션 및 서비스 주체 관계](../develop/app-objects-and-service-principals.md)를 참조하세요.

Azure PowerShell, Azure CLI, Microsoft Graph, Azure Portal 및 기타 도구를 사용하여 테넌트에서 애플리케이션 및 해당 서비스 주체 개체(ObjectID)를 만들 수도 있습니다. 

![애플리케이션 ID 및 개체 ID 필드가 강조 표시된 새 애플리케이션 등록을 보여 주는 스크린샷.](./media/securing-service-accounts/secure-principal-image-1.png)

## <a name="service-principal-authentication"></a>서비스 주체 인증

서비스 주체를 사용하는 인증에는 클라이언트 인증서와 클라이언트 암호라는 두 가지 메커니즘이 있습니다. 

![ 강조 표시된 인증서 및 클라이언트 암호 영역을 보여 주는 새 앱 페이지의 스크린샷.](./media/securing-service-accounts/secure-principal-certificates.png)

인증서가 더 안전합니다. 가능한 경우 클라이언트 인증서를 사용하십시오. 클라이언트 암호와 달리 클라이언트 인증서는 실수로 코드에 포함될 수 없습니다. 가능하면 인증서 및 암호 관리를 위해 Azure Key Vault로 하드웨어 보안 모듈에서 보호하는 키를 사용하여 다음 자산을 암호화하십시오.

* 인증 키

* 스토리지 계정 키

* 데이터 암호화 키

* .pfx 파일

* 암호 

Azure Key Vault 및 인증서와 암호 관리에 이를 사용하는 방법에 대한 자세한 내용은 [Azure Key Vault 정보](../../key-vault/general/overview.md) 및 [Azure Portal을 사용하여 Key Vault 액세스 정책 할당](../../key-vault/general/assign-access-policy-portal.md)을 참조하세요. 

 ### <a name="challenges-and-mitigations"></a>과제 및 해결 방법
다음 표에서는 서비스 주체를 사용할 때 발생할 수 있는 과제를 해결하는 방법을 보여 줍니다.


| 과제| 해결 방법 |
| - | - |
| 권한 있는 역할에 할당된 서비스 주체에 대한 액세스를 검토합니다.| 이 기능은 미리 보기 상태이며 아직 광범위하게 사용할 수 없습니다. |
| 서비스 주체의 액세스 검토| Azure Portal를 사용하여 리소스의 액세스 제어 목록을 수동으로 확인합니다. |
| 권한이 과다하게 부여된 서비스 주체| 자동화 서비스 계정이나 서비스 주체를 만들 때는 작업에 필요한 권한만 제공합니다. 기존 서비스 주체를 평가하여 권한을 낮출 수 있는지 확인합니다. |
|서비스 주체의 자격 증명 또는 인증 방법에 대한 수정 사항 식별 |중요 작업 보고서 통합 문서를 사용하면 이 문제를 해결하는 데 도움이 됩니다. [이 블로그 게시물의 설명을 참조하세요](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/azure-ad-workbook-to-help-you-assess-solorigate-risk/ba-p/2010718).|

## <a name="find-accounts-using-service-principals"></a>서비스 주체를 사용하여 계정 찾기
다음 명령을 실행하여 서비스 주체를 사용하는 계정을 찾습니다.

Azure CLI 사용


`az ad sp list`

PowerShell 사용

`Get-AzureADServicePrincipal -All:$true` 


자세한 내용은 [Get-AzureADServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal)을 참조하세요.

## <a name="assess-service-principal-security"></a>서비스 주체 보안 평가

서비스 주체의 보안을 평가하려면 권한 및 자격 증명 스토리지를 평가해야 합니다.

다음 정보를 사용하여 잠재적인 문제를 해결합니다.

|과제 | 해결 방법|
| - | - |
| 다중 테넌트 앱에 동의한 사용자를 검색하고 다중 테넌트 앱에 대한 불법 승인 부여를 검색 | 다음 PowerShell을 실행하여 다중 테넌트 앱을 찾습니다.<br>`Get-AzureADServicePrincipal -All:$true ? {$_.Tags -eq WindowsAzureActiveDirectoryIntegratedApp"}`<br>사용자 동의를 사용하지 않습니다. <br>선택한 권한에 대해 확인된 게시자의 사용자 동의 허용(권장) <br> 조건부 액세스를 사용하여 신뢰할 수 없는 위치의 서비스 주체를 차단하십시오. 사용자 컨텍스트에 따라 구성하고 해당 토큰을 사용하여 서비스 주체를 트리거합니다.|
|서비스 주체를 사용하는 스크립트에서 하드 코드된 공유 암호를 사용합니다.|인증서나 Azure Key Vault를 사용합니다.|
|인증서 또는 암호를 사용하는 사용자 추적| Azure AD 로그인 로그를 사용하여 서비스 주체의 로그인을 모니터링합니다.|
조건부 액세스를 사용하여 서비스 주체의 로그인을 관리할 수 없습니다.| Azure AD 로그인 로그를 사용하여 로그인 모니터링
| 기본 Azure RBAC 역할은 기여자입니다. |요구 사항을 평가하고 이 요구를 충족하기 위해 가장 가능성이 낮은 권한이 있는 역할을 적용합니다.|

## <a name="move-from-a-user-account-to-a-service-principal"></a>사용자 계정에서 서비스 주체로 이동  
‎Azure 사용자 계정을 서비스 주체로 사용하는 경우 [관리 ID](../../app-service/overview-managed-identity.md?tabs=dotnet) 또는 서비스 주체로 이동할 수 있는지 평가합니다. 관리 ID를 사용할 수 없는 경우 필요한 작업을 실행할 수 있는 충분한 권한 및 범위가 있는 서비스 주체를 프로비전합니다. [애플리케이션을 등록](../develop/howto-create-service-principal-portal.md)하거나 [PowerShell](../develop/howto-authenticate-service-principal-powershell.md)을 사용하여 서비스 주체를 만들 수 있습니다.

Microsoft Graph를 사용하는 경우 [이 예제](/powershell/azure/create-azure-service-principal-azureps)와 같이 특정 API의 설명서를 확인하고 ‎애플리케이션에 대한 권한 유형이 지원되는 것으로 표시되는지 확인합니다.

## <a name="next-steps"></a>다음 단계

**서비스 주체에 대해 자세히 알아보려면 다음과 같이 합니다.**

[서비스 주체 만들기](../develop/howto-create-service-principal-portal.md)

 [서비스 주체 로그인 모니터링](../reports-monitoring/concept-sign-ins.md#sign-ins-report)

**서비스 계정을 보호하는 방법에 대한 자세한 내용은 다음을 확인하세요.**

[Azure 서비스 계정 소개](service-accounts-introduction-azure.md)

[관리 ID 보안](service-accounts-managed-identities.md)

[Azure 서비스 계정 관리](service-accounts-governing-azure.md)

[온-프레미스 서비스 계정 소개](service-accounts-on-premises.md)
