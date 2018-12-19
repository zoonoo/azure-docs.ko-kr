---
title: Azure Stack에 대 한 id 개요 | Microsoft Docs
description: Azure Stack과 함께 사용할 수는 id 시스템에 알아봅니다.
services: azure-stack
documentationcenter: ''
author: PatAltimore
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/09/2018
ms.author: patricka
ms.reviewer: ''
ms.openlocfilehash: 1bc4fcda360a899fb2f58e2ac26270d160227a65
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/10/2018
ms.locfileid: "48902844"
---
# <a name="overview-of-identity-for-azure-stack"></a>Azure Stack에 대 한 id 개요

Azure Stack에는 Azure Active Directory (Azure AD) 또는 Active Directory Federation Services (AD FS)를 id 공급자로 Active Directory에서 지원 해야 합니다. 공급자의 선택은 Azure Stack을 처음 배포할 때를 확인 하는 한 번만 결정 합니다. 개념 및이 문서에서 권한 부여 정보 도움이 id 공급자 중에서 선택 됩니다.

Azure AD 또는 AD FS 여러분이 Azure Stack을 배포 하는 모드를 결정할 수 있습니다:

- 연결된 된 모드에서 배포할 때 Azure AD 또는 AD FS 사용할 수 있습니다.
- 인터넷에 연결 하지 않고 연결이 끊어진된 모드에서 배포 하는 경우에 AD FS는 지원 됩니다.

Azure Stack 환경에 의존 하는 옵션에 대 한 자세한 내용은 다음 문서를 참조 합니다.

- Azure Stack 배포 키트: [Id 고려 사항](azure-stack-datacenter-integration.md#identity-considerations)합니다.
- Azure Stack 통합 시스템: [배포 계획 결정 for Azure Stack 통합 시스템](azure-stack-deployment-decisions.md)입니다.

## <a name="common-concepts-for-identity"></a>Id에 대 한 일반적인 개념

다음 섹션에서는 id 공급자 및 Azure Stack에서 사용 하는 방법에 대 한 일반적인 개념을 설명합니다.

![Id 공급자에 대 한 용어](media/azure-stack-identity-overview/terminology.png)

### <a name="directory-tenants-and-organizations"></a>디렉터리 테 넌 트 및 조직

디렉터리는 컨테이너에 대 한 정보를 포함 하는 *사용자*, *응용 프로그램*하십시오 *그룹*, 및 *서비스 주체*.

디렉터리 테 넌 트는 *조직*예: Microsoft 또는 고유한 회사입니다.

- Azure AD는 다중 테 넌 트를 지원 하 고 자체 디렉터리에 각각 여러 조직을 지원할 수 있습니다. Azure AD를 사용 하 고 여러 테 넌 트가 응용 프로그램과 동일한 디렉터리의 다른 테 넌 트에 사용자가 하나의 테 넌 트 액세스를 부여할 수 있습니다.
- AD FS 지원만 단일 테 넌 트 이며, 따라서 단일 조직만 합니다.

### <a name="users-and-groups"></a>개요

사용자 계정 (identities)는 사용자 ID 및 암호를 사용 하 여 개인을 인증 하는 표준 계정입니다. 그룹은 사용자 또는 다른 그룹에 포함할 수 있습니다.

만들고 사용자 및 그룹 관리에 사용할 id 솔루션에 따라 달라 집니다.

Azure stack에서 사용자 계정:

- 생성 되는 *username@domain* 형식. AD FS의 사용을 지원 하지 않는 AD FS는 Active Directory 인스턴스에 사용자 계정 매핑되지만 합니다  *\\ \<도메인 >\\\<별칭 >* 형식입니다.
- 다단계 인증을 사용 하도록를 설정할 수 있습니다.
- 조직의 디렉터리가 있는 디렉터리를 먼저 등록을 제한 됩니다.
- 온-프레미스 디렉터리에서 가져올 수 있습니다. 자세한 내용은 [Azure Active Directory와 온-프레미스 디렉터리 통합](/azure/active-directory/connect/active-directory-aadconnect)합니다.

조직의 테 넌 트 포털에 로그인 할 때 사용 합니다 *https://portal.local.azurestack.external* URL입니다. Azure Stack 등록에 사용 된 도메인 이름을 Azure Stack 등록을 사용 하는 것 이외의 도메인에서 Azure Stack 포털에 로그인 하는 경우 포털에 추가 해야 url입니다. 예를 들어 fabrikam.onmicrosoft.com Azure Stack에 등록 된 경우 사용자 계정 로그인 이며 admin@contoso.com에 사용자 포털에 로그인 하는 데 url: https://portal.local.azurestack.external/fabrikam.onmicrosoft.com합니다.

### <a name="guest-users"></a>게스트 사용자

게스트 사용자는 디렉터리의 리소스에 대 한 액세스 권한이 부여 된 다른 디렉터리 테 넌 트의 사용자 계정입니다. 게스트 사용자를 지원 하기 위해 Azure AD를 사용 하 고 다중 테 넌 트에 대 한 지원을 사용 하도록 설정 합니다. 지원 되는 경우에 외부 조직과 공동 작업을 사용 하도록 설정 하는 디렉터리 테 넌 트에서 리소스에 액세스 하는 게스트 사용자를 초대할 수 있습니다.

게스트 사용자를 초대 하려면 클라우드 운영자 및 사용자가 사용할 수 있습니다 [Azure AD B2B 공동 작업](/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)합니다. 초대 된 사용자 디렉터리에서 문서, 리소스 및 응용 프로그램에 대 한 액세스를 가져오고, 사용자 고유의 리소스 및 데이터에 대 한 제어를 유지 관리 합니다. 

게스트 사용자로 다른 조직의 디렉터리 테 넌 트에 로그인 하는 것이 있습니다. 해당 조직의 디렉터리 이름을 포털에 추가 하면이 위해 URL입니다. 예를 들어 Contoso 조직에 속해야 하 고 Fabrikam 디렉터리에 로그인 하려고 할 경우 사용할 https://portal.local.azurestack.external/fabrikam.onmicrosoft.com합니다.

### <a name="applications"></a>애플리케이션

응용 프로그램을 Azure AD 또는 AD FS 등록 하 고 조직에서 사용자에 게 응용 프로그램을 제공할 수 있습니다.

응용 프로그램에는 다음이 포함 됩니다.

- **웹 응용 프로그램**: Azure portal 및 Azure Resource Manager 예제를 포함 합니다. 웹 API 호출을 지원합니다.
- **네이티브 클라이언트**: Azure PowerShell, Visual Studio 및 Azure CLI 예제를 포함 합니다.

응용 프로그램 두 가지 유형의 테 넌 트를 지원할 수 있습니다.

- **단일 테 넌 트**: 사용자 및 서비스 응용 프로그램 등록 되어 있는 동일한 디렉터리 에서만에서 지원 됩니다.

  > [!NOTE]
  > AD FS에서 단일 디렉터리를 지원 하므로 AD FS 토폴로지를에서 만든 응용 프로그램은 기본적으로 단일 테 넌 트 응용 프로그램.

- **다중 테 넌 트**: 사용자 및 응용 프로그램 등록 되어 있는 디렉터리와 추가 테 넌 트 디렉터리에서 서비스 사용을 지원 합니다. 다중 테 넌 트 응용 프로그램을 사용 하 여 다른 테 넌 트 디렉터리 (다른 Azure AD 테 넌 트) 수의 사용자가 응용 프로그램에 로그인합니다. 

  다중 테 넌 트에 대 한 자세한 내용은 참조 하세요. [다중 테 넌 트 활성화](azure-stack-enable-multitenancy.md)합니다.

  다중 테 넌 트 앱을 개발 하는 방법에 대 한 자세한 내용은 참조 하세요. [다중 테 넌 트 앱](/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview)합니다.

응용 프로그램을 등록 하는 경우 두 개체를 만듭니다.

- **응용 프로그램 개체**: 모든 테 넌 트에서 응용 프로그램에 대 한 글로벌 표현입니다. 이 관계는 소프트웨어 응용 프로그램을 사용 하 여 한 일 및 응용 프로그램이 처음으로 등록 되어 있는 디렉터리에만 존재 합니다.

- **서비스 주체 개체**: 응용 프로그램이 처음으로 등록 되어 있는 디렉터리에 응용 프로그램에 대해 만든 자격 증명입니다. 서비스 주체는 응용 프로그램 사용 되는 각 추가 테 넌 트의 디렉터리에 만들어집니다. 이 관계는 일대다 소프트웨어 응용 프로그램을 사용 하 여 될 수 있습니다.

응용 프로그램 및 서비스 주체 개체에 대 한 자세한 내용은 참조 하세요 [응용 프로그램 및 Azure Active Directory에서 서비스 주체 개체](/azure/active-directory/develop/active-directory-application-objects)합니다.

### <a name="service-principals"></a>서비스 원칙

서비스 주체는 집합이 *자격 증명* 응용 프로그램 또는 Azure Stack에서 리소스에 대 한 액세스 권한을 부여 하는 서비스에 대 한 합니다. 서비스 주체를 사용 하 여 응용 프로그램의 사용자 권한에서 응용 프로그램 사용 권한을 구분합니다.

서비스 주체는 응용 프로그램이 사용은 각 테 넌 트에 생성 됩니다. 서비스 주체 id에 로그인 하 고 해당 테 넌 트로 보호 되는 리소스 (예: 사용자)에 대 한 액세스를 설정 합니다.

- 단일 테 넌 트 응용 프로그램을 처음 만들어질 디렉터리에 하나의 서비스 주체만 이 서비스 주체 생성 되 고가 동의 하면 응용 프로그램을 등록 하는 동안 사용 되 고 있습니다.
- 다중 테 넌 트 웹 응용 프로그램 또는 API를 각 테 넌 트에서 만든 서비스 주체에 해당 테 넌 트에서 사용자가 응용 프로그램의 사용에 동의 하는 위치에 있습니다.

Azure portal을 통해 생성 되는 키 또는 인증서를 서비스 주체에 대 한 자격 증명 수 있습니다. 인증서 키 보다 더 안전한 것으로 간주 되므로 자동화에 대 한 인증서를 사용 하는 적합 합니다. 

> [!NOTE]
> Azure Stack을 사용 하 여 AD FS를 사용 하는 경우 관리자만 서비스 주체를 만들 수 있습니다. AD FS를 사용 하 여 서비스 주체 인증서가 필요 하 고 권한 있는 끝점 (PEP)을 통해 생성 됩니다. 자세한 내용은 [Azure Stack에 대 한 응용 프로그램 액세스할](azure-stack-create-service-principals.md)합니다.

Azure Stack에 대 한 서비스 주체를 알아보려면 [서비스 주체를 만들](azure-stack-create-service-principals.md)합니다.

### <a name="services"></a>Services

Id 공급자와 상호 작용 하는 Azure Stack에서 서비스는 id 공급자를 사용 하 여 응용 프로그램으로 등록 됩니다. 응용 프로그램과 마찬가지로 등록 id 시스템을 사용 하 여 인증 하는 서비스를 활성화 합니다.

모든 Azure 서비스 사용 [OpenID Connect](/azure/active-directory/develop/active-directory-protocols-openid-connect-code) 프로토콜 및 [JSON 웹 토큰](/azure/active-directory/develop/active-directory-token-and-claims) 해당 id를 설정 합니다. 때문에 Azure AD 및 AD FS 프로토콜을 일관 되 게 사용, 사용할 수 있습니다 [Azure Active Directory 인증 라이브러리](/azure/active-directory/develop/active-directory-authentication-libraries) 온-프레미스 인증 (ADAL) 또는 Azure (연결 된 시나리오에서). Adal을 사용 하면 클라우드 간 및 온-프레미스 리소스 관리에 대 한 Azure PowerShell 및 Azure CLI와 같은 도구를 사용할 수도 있습니다.

### <a name="identities-and-your-identity-system"></a>Id 및 id 시스템

Azure Stack에 대 한 id는 사용자 계정, 그룹 및 서비스 주체를 포함 합니다.

Azure Stack, 여러 기본 제공 응용 프로그램 및 서비스를 자동으로 설치 하는 경우 디렉터리 테 넌 트에서 id 공급자를 등록 합니다. 등록 하는 일부 서비스 관리에 사용 됩니다. 다른 서비스 사용자가 사용할 수 있습니다. 기본 등록 나중에 추가 하는 id를 사용 하 여 및 서로 상호 작용할 수 있는 핵심 서비스 id를 제공 합니다.

다중 테 넌 트를 사용 하 여 Azure AD를 설정 하는 경우 일부 응용 프로그램을 새 디렉터리에 전파 됩니다.

## <a name="authentication-and-authorization"></a>인증 및 권한 부여

### <a name="authentication-by-applications-and-users"></a>응용 프로그램 및 사용자 인증

![Azure Stack의 레이어 간의 id](media/azure-stack-identity-overview/identity-layers.png)

Azure Stack의 아키텍처는 사용자와 응용 프로그램에 대 한 네 가지 계층으로 설명 되어 있습니다. 이러한 각 계층 간의 상호 작용 서로 다른 유형의 인증을 사용할 수 있습니다.

|계층    |계층 간 인증  |
|---------|---------|
|도구 및 관리 포털 등의 클라이언트     | 도구 액세스 하거나 Azure Stack에서 리소스를 수정 하 고 클라이언트 사용을 [JSON Web Token](/azure/active-directory/develop/active-directory-token-and-claims) Azure Resource Manager에 대 한 호출을 배치 합니다. <br>Azure Resource Manager JSON 웹 토큰의 유효성을 검사 하 고 관찰 합니다 *클레임* 발급된 된 토큰의 권한 부여 수준을 예상 하는 사용자 또는 서비스 주체에 Azure Stack에서. |
|Azure Resource Manager 및 해당 코어 서비스     |Azure Resource Manager는 사용자의 통신을 전송 하려면 리소스 공급자와 통신 합니다. <br> 사용 하 여 전송 *직접 명령적* 호출 또는 *선언적* 를 통해 호출 [Azure Resource Manager 템플릿](/azure/azure-stack/user/azure-stack-arm-templates)합니다.|
|리소스 공급자     |인증서 기반 인증을 사용 하 여 보안 리소스 공급자에 전달 되는 통화입니다. <br>그런 다음 azure Resource Manager 및 리소스 공급자 API 통해 통신 유지합니다. Azure Resource Manager에서 수신 되는 모든 호출에 대해 리소스 공급자는 해당 인증서를 사용 하 여 호출을 확인 합니다.|
|인프라 및 비즈니스 논리     |리소스 공급자가 선택한 인증 모드를 사용 하 여 비즈니스 논리 및 인프라와 통신 합니다. 이 통신을 보호 하려면 Windows 인증을 사용 하는 Azure Stack과 함께 제공 되는 기본 리소스 공급자입니다.|

![인증에 필요한 정보](media/azure-stack-identity-overview/authentication.png)

### <a name="authenticate-to-azure-resource-manager"></a>Azure Resource Manager로 인증

Id 공급자를 사용 하 여 인증 및 JSON 웹 토큰을 받기는 다음 정보가 있어야 합니다.

1. **Id 시스템 (인증 기관)에 대 한 URL**: URL에 id 공급자를 연결할 수 있습니다. 예를 들어 *https://login.windows.net*입니다.
2. **Azure Resource Manager에 대 한 앱 ID URI**: 고유 식별자에 대 한 Azure Resource Manager id 공급자를 사용 하 여 등록 합니다. 각 Azure Stack 설치에 고유한 이기도합니다.
3. **자격 증명**: id 공급자를 사용 하 여 인증에 사용할 자격 증명입니다.
4. **Azure Resource Manager에 대 한 URL**: URL은 Azure Resource Manager 서비스의 위치입니다. 예를 들어 *https://management.azure.com* 하거나 *https://management.local.azurestack.external*합니다.

보안 주체 (클라이언트, 응용 프로그램 또는 사용자)의 리소스 액세스에 인증을 요청 하면 요청 포함 해야 합니다.

- 보안 주체의 자격 증명입니다.
- 앱의 보안 주체에 액세스 하려고 하는 리소스 ID URI입니다.

자격 증명 id 공급자에 의해 유효성이 검사 됩니다. 또한 id 공급자 앱 ID URI는 등록 된 응용 프로그램 및 보안 주체에 해당 리소스에 대 한 토큰을 가져올 수 있는 올바른 권한이 있는지 유효성을 검사 합니다. 요청이 유효한 경우 JSON 웹 토큰 부여 됩니다.

토큰을 전달 해야 합니다 다음 요청 헤더에 Azure Resource Manager로. Azure Resource Manager는 순서에 관계 없이 다음을 수행 합니다.

- 유효성을 검사 합니다 *발급자* (iss) 클레임 올바른 id 공급자 로부터 토큰 인지 확인 합니다.
- 유효성을 검사 합니다 *대상* Azure Resource Manager로 토큰을 발급 된 것인지 (aud) 클레임 합니다.
- 유효성을 검사 JSON Web Token가 서명 된 OpenID를 통해 구성 되는 인증서를 사용 하 여 Azure Resource Manager로 알려져 있습니다.
- 검토 합니다 *발급 시간* (iat) 및 *만료* (exp) 클레임을 토큰 활성 상태 인지 확인 및 사용할 수 있습니다.

Azure 리소스 관리자를 사용 하는 모든 유효성 검사가 완료 되 면 합니다 *개체* (oid) 및 *그룹* 클레임 보안 주체에 액세스할 수 있는 리소스의 목록을 확인 합니다.

![토큰 교환 프로토콜의 다이어그램](media/azure-stack-identity-overview/token-exchange.png)

> [!NOTE]
> 배포 후 Azure Active Directory 전역 관리자 권한이 필요 하지 않습니다. 그러나 일부 작업에는 전역 관리자 자격 증명을 필요할 수 있습니다. 예를 들어, 리소스 공급자 설치 관리자 스크립트 또는 권한 부여 요청을 새로운 기능. 일시적으로 계정의 전역 관리자 권한이 다시 설정 하거나의 소유자가 별도 전역 관리자 계정을 사용 합니다 *공급자 구독을 기본*입니다.

### <a name="use-role-based-access-control"></a>역할 기반 Access Control 사용

역할 기반 Access Control (RBAC) Azure Stack에는 Microsoft Azure의 구현은 일치 합니다. 사용자, 그룹 및 응용 프로그램에 적절 한 RBAC 역할을 할당 하 여 리소스에 대 한 액세스를 관리할 수 있습니다.
Azure Stack을 사용 하 여 RBAC를 사용 하는 방법에 대 한 내용은 다음 문서를 참조 합니다.

- [Azure Portal에서 역할 기반 액세스 제어로 시작합니다](/azure/role-based-access-control/overview).
- [역할 기반 Access Control을 사용 하 여 Azure 구독 리소스에 대 한 액세스를 관리할](/azure/role-based-access-control/role-assignments-portal)합니다.
- [Azure 역할 기반 Access Control에 대 한 사용자 지정 역할 만들기](/azure/role-based-access-control/custom-roles)합니다.
- [역할 기반 Access Control 관리](azure-stack-manage-permissions.md) Azure Stack의 합니다.

### <a name="authenticate-with-azure-powershell"></a>Azure PowerShell을 사용 하 여 인증

Azure PowerShell을 사용 하 여 Azure Stack을 사용 하 여 인증 하는 방법에 대 한 세부 정보에서 찾을 수 있습니다 [Azure Stack 사용자의 PowerShell 환경을 구성](azure-stack-powershell-configure-user.md)합니다.

### <a name="authenticate-with-azure-cli"></a>Azure CLI를 사용 하 여 인증

Azure PowerShell을 사용 하 여 Azure Stack을 사용 하 여 인증 하는 방법에 대 한 내용은 [설치 및 Azure Stack과 함께 사용 하 여 Azure CLI 구성](/azure/azure-stack/user/azure-stack-connect-cli)합니다.

## <a name="next-steps"></a>다음 단계

- [Id 아키텍처](azure-stack-identity-architecture.md)
- [데이터 센터 통합-identity](azure-stack-integrate-identity.md)
