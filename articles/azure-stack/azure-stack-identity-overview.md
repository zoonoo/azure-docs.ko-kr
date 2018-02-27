---
title: "Azure 스택 대 한 Id의 개요 | Microsoft Docs"
description: "Azure 스택 함께 사용할 수 있습니다 Id 시스템에 알아봅니다."
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 2/22/2018
ms.author: brenduns
ms.reviewer: 
ms.openlocfilehash: 609ea3300806f3cfed4a3285a096f59be491c684
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/24/2018
---
# <a name="overview-of-identity-for-azure-stack"></a>Azure 스택 대 한 Id의 개요

Azure 스택 Azure AD가 필요한 또는 Active Directory 페더레이션 서비스 (AD FS)으로 AD (Active Directory)를 id 공급자로 지원 합니다. 다음과 같은 개념 및 권한 부여 정보가 유용 id 공급자 사이 항목을 선택 합니다. 공급자의 선택은 Azure 스택을 처음 배포할 때 결정할 일회성 의사 결정 합니다.  

선택한 Azure 간의 광고 및 AD FS Azure 스택 배포 하는 모드에 의해 제한 될 수 있습니다. 
- 연결 된 모드에서 배포할 때는 Azure AD 또는 AD FS 사용할 수 있습니다. 
- 인터넷에 연결 하지 않고 연결이 끊어진된 모드에서 배포할 AD FS에만 사용할 수 있습니다.

이러한 선택 항목에 대 한 자세한 내용은 Azure 스택 환경에 따라 다음 문서를 봅니다.
- Azure 스택 배포 키트 참조 [Identity 고려 사항](azure-stack-datacenter-integration.md#identity-considerations)합니다.
- Azure 스택 통합 시스템 참조 [배포 계획에 대 한 Azure 스택 결정 통합 시스템](azure-stack-deployment-decisions.md)합니다.

 
## <a name="common-concepts-for-identity"></a>Id에 대 한 일반적인 개념
다음 섹션에서는 id 공급자 및 Azure 스택에서 사용 되는 일반적인 개념을 설명 합니다.
![Id 공급자에 대 한 용어](media/azure-stack-identity-overview/terminology.png)


### <a name="directories-tenants-and-organizations"></a>디렉터리 테 넌 트 및 조직
디렉터리에 대 한 정보를 보유 하는 컨테이너는 *사용자*, *응용 프로그램*, *그룹*, 및 *서비스 보안 주체*합니다.
 
디렉터리 테 넌는 *조직*와 같은 Microsoft 소프트웨어 또는 고유한 회사입니다. 
- Azure AD 여러 테 넌 트를 지원 하며 여러 조직이 자신의 디렉터리에 각각를 지원할 수 있습니다. Azure AD를 사용 하 고 여러 테 넌 트가 응용 프로그램 및 한 테 넌 트 액세스의 사용자가 동일한 해당 디렉터리의 다른 테 넌 트에 부여할 수 있습니다.
- AD FS만 단일 테 넌 트, 및는 단일 조직을 지원합니다. 

### <a name="users-and-groups"></a>사용자 및 그룹
사용자 계정 (identities)는 사용자 ID와 암호를 사용 하 여 개별 사용자를 인증 하는 표준 계정. 그룹은 사용자 또는 다른 그룹에 포함할 수 있습니다. 

만들고 사용자 및 그룹 관리 사용 하면 id 솔루션에 따라 다릅니다. 

사용자 계정 Azure 스택: 
- 만든는  *username@domain*  형식입니다. AD FS의 사용을 지원 하지 않는 AD FS는 Active Directory에 사용자 계정이 매핑되지만  _&lt;도메인 >\<별칭 >_ 형식입니다. 
- 다단계 인증을 사용 하도록를 설정할 수 있습니다. 
- 디렉터리 처음 등록할 수 있는, 조직 디렉터리의 제한 됩니다.
- 온-프레미스 디렉터리에서 가져올 수 있습니다. 자세한 내용은 참조 [Azure Active Directory와 온-프레미스 디렉터리 통합](/azure/active-directory/connect/active-directory-aadconnect) Azure 설명서에서.  

조직 테 넌 트 포털에 로그인 하면 https://portal.local.azurestack.external을 사용 합니다. 

### <a name="guest-users"></a>게스트 사용자
게스트 사용자는 디렉터리에 대 한 액세스 권한이 부여 된 다른 디렉터리 테 넌 트의 사용자 계정입니다. 게스트 사용자를 지원 하기 위해 Azure AD를 사용 하 고 다중 테 넌 트에 대 한 지원을 사용 하도록 설정 해야 합니다. 지원 되는 경우 디렉터리 테 넌 트 외부 조직와의 공동 작업을 수 있는 리소스에 액세스 하려면 게스트 사용자를 초대할 수 있습니다. 

게스트 사용자를 초대 하기 위해 클라우드 운영자 및 사용자 צ ְ ײ [Azure AD B2B 공동 작업](/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)합니다. 사용자 고유의 리소스 및 데이터에 대 한 제어를 유지 관리 하는 동안 사용자가 액세스 권한을 얻을 문서, 리소스 및 응용 프로그램에 디렉터리에서 하도록 초대 받았습니다. 

게스트 사용자로 다른 조직의 디렉터리 테 넌 트에 로그인 할 수 있습니다. 포털에 해당 조직의 디렉터리 이름을 추가 해야 하는를 위해 URL입니다.  예를 들어 https://portal.local.azurestack.external/fabrikam.onmicrosoft.com을 사용 하 여 contoso.com에 속해야 하지만 Fabrikam 디렉터리에 로그인 합니다.

### <a name="applications"></a>응용 프로그램
응용 프로그램을 Azure AD 또는 AD FS 등록할 수 있으며 다음 조직의 사용자에 게 제공할 수 있습니다. 

응용 프로그램은 다음과 같습니다.
- **웹 응용 프로그램** – 예로 Azure 포털 및 Azure 리소스 관리자입니다.  Web API 호출을 지원. 
- **네이티브 클라이언트** – 예로 Azure PowerShell, Visual Studio 및 Azure CLI (명령줄 인터페이스).

응용 프로그램에는 두 가지 유형의 테 넌 트 지원할 수 있습니다. 
- **단일 테 넌 트** 응용 프로그램은 사용자 및 서비스 응용 프로그램 등록 되어 있는 디렉터리에서만 지원 합니다. 

  > [!NOTE]    
  > AD FS는 단일 디렉터리에만 지원 하므로 AD FS 토폴로지에서 만들어야 하는 응용 프로그램이 기본적으로 단일 테 넌 트 응용 프로그램 않습니다.
- **다중 테 넌 트** 사용자 및 서비스 응용 프로그램 등록 되어 있는 디렉터리 모두와 추가 테 넌 트 디렉터리에서 응용 프로그램 지원 사용 합니다.  다중 테 넌 트 응용 프로그램과 함께 다른 테 넌 트 디렉터리 (다른 Azure AD 테 넌) 수의 사용자가 응용 프로그램에 로그인합니다.     

  다중 테 넌 트에 대 한 자세한 내용은 참조 [다중 테 넌 트를 사용 하도록 설정](azure-stack-enable-multitenancy.md)합니다.  

  다중 테 넌 트 응용 프로그램을 개발 하는 방법에 대 한 자세한 내용은 참조 [다중 테 넌 트 앱](/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview)합니다.

응용 프로그램을 등록 하면 두 개체가 생성 됩니다.
- **응용 프로그램 개체** – 응용 프로그램 개체는 모든 테 넌 트 간에 응용 프로그램의 전역 표현 합니다. 이 관계 일대일 소프트웨어 응용 프로그램과 함께 사용 되며 여기서 응용 프로그램은 첫 번째 레지스터 디렉터리에만 존재 합니다.

 
   
- **서비스 사용자 개체** -서비스 사용자는 응용 프로그램이 처음 등록 될 디렉터리의 응용 프로그램에 대해 만들어진 자격 증명입니다. 서비스 사용자는 해당 응용 프로그램 사용 되는 각 추가 테 넌 트의 디렉터리에 만들어집니다. 이 관계는-다 소프트웨어 응용 프로그램 사용 될 수 있습니다.   

응용 프로그램 및 서비스 사용자 개체에 대 한 자세한 참조 [응용 프로그램 및 Azure Active Directory (Azure AD)에서 서비스 보안 주체 개체](/azure/active-directory/develop/active-directory-application-objects)합니다. 

### <a name="service-principals"></a>서비스 원칙 
서비스 사용자의 집합이 *자격 증명* 스택에서 Azure 리소스에 대 한 액세스 권한을 부여 하는 서비스 또는 응용 프로그램입니다. 서비스 사용자의 사용 응용 프로그램을 사용 하는 사용자의 사용 권한에서 응용 프로그램 사용 권한을 구분 합니다.

서비스 사용자는 각 테 넌 트 응용 프로그램 사용 되는 위치에 생성 됩니다. 서비스 사용자는 로그인 및 해당 테 넌 트에 의해 보안 되는 리소스 (예: 사용자)에 대 한 액세스에 대 한 id를 설정 합니다.   

- 단일 테 넌 트 응용 프로그램 하나만 서비스 사용자를 처음 만들어질 디렉터리에 있습니다.  이 서비스 사용자는 생성 되 고 응용 프로그램을 등록 하는 동안 사용할 동의. 
- 다중 테 넌 트 웹 응용 프로그램이 나 API에는 각 테 넌 트에서 만든 서비스 사용자는 해당 테 넌 트의 사용자는 응용 프로그램의 사용에 동의 하는 위치 있습니다.  

서비스 사용자에 대 한 자격 증명 (Azure 포털을 통해 생성) 키 또는 인증서 일 수 있습니다.  인증서를 사용 하는 인증서 키의 사용 보다 더 안전한 것으로 간주 되므로 자동화 적합 합니다. 


> [!NOTE]    
> Azure 스택을 AD FS를 사용 하면 관리자만 서비스 사용자를 만들 수 있습니다. AD FS와 함께 서비스 사용자는 인증서가 필요 하 고 권한 있는 끝점 (PEP)을 통해 생성 됩니다. 자세한 내용은 참조, [Azure 스택에 응용 프로그램 액세스 권한을 제공](azure-stack-create-service-principals.md)합니다.

Azure 스택에 대 한 서비스 사용자에 대 한 자세한 내용은 [서비스 사용자를 만들](azure-stack-create-service-principals.md)합니다.


### <a name="services"></a>Services
Azure 스택에 Id 공급자와 상호 작용 하는 서비스는 id 공급자와 응용 프로그램으로 등록 됩니다. 응용 프로그램과 마찬가지로 등록 id 시스템을 인증 하는 서비스를 활성화 합니다. 

모든 Azure 서비스를 사용 하 여 [OpenID Connect](/azure/active-directory/develop/active-directory-protocols-openid-connect-code) 프로토콜 및 [JSON 웹 토큰](/azure/active-directory/develop/active-directory-token-and-claims) (JWT) 자신의 id를 설정 합니다. Azure AD에서 프로토콜의 일관 된 사용으로 인해 AD FS를 Azure를 사용할 수 있습니다 [Active Directory 인증 라이브러리](/azure/active-directory/develop/active-directory-authentication-libraries) (ADAL) 온-프레미스 인증을 또는 azure (에 연결 된 시나리오). ADAL 또한 하면 클라우드 간에 대 한 Azure PowerShell 및 CLI와 같은 도구를 사용 하 고 온-프레미스 리소스를 관리 합니다. 


### <a name="identities-and-your-identity-system"></a>Id 및 id 시스템 
Azure 스택에 대 한 id는 사용자 계정, 그룹 및 서비스 사용자를 포함 합니다. Azure 스택, 여러 기본 제공 응용 프로그램 및 서비스를 자동으로 설치 하는 경우에 디렉터리 테 넌 트에서 id 공급자와 등록 합니다. 등록 하는 일부 서비스 관리에 사용 됩니다. 다른 서비스는 사용자가 사용할 수 있습니다. 기본 등록 서로 상호 작용할 수 있는 핵심 서비스 id를 지정 하 고 id가 나중에 추가할 수 있습니다.
다중 테 넌 트와 Azure AD를 설정 하면 일부 응용 프로그램을 새 디렉터리에 전파 합니다.  

## <a name="authentication-and-authorization"></a>인증 및 권한 부여
 

### <a name="authentication-by-applications-and-users"></a>응용 프로그램 및 사용자 인증
  
![Azure 스택 계층 사이 identity](media/azure-stack-identity-overview/identity-layers.png)

응용 프로그램 및 사용자에 대 한 Azure 스택 아키텍처 4 단계 계층으로 설명 합니다. 이러한 각 계층 간의 상호 작용 유형의 인증을 사용할 수 있습니다.


|계층    |레이어 간의 인증  |
|---------|---------|
|도구와 클라이언트는 관리 포털 등     | 도구에 액세스 하거나 스택에서 Azure 리소스를 수정 하 고 클라이언트가 사용 하는 [JSON 웹 토큰](/azure/active-directory/develop/active-directory-token-and-claims) (JWT) Azure 리소스 관리에 대 한 호출을 배치할 수 있습니다.  <br><br> Azure 리소스 관리자 JWT의 유효성을 검사 하 고 관찰 된 *클레임* 수준의 권한 부여를 예측 하려면 발급된 된 토큰에서 해당 사용자 또는 서비스 사용자가 Azure 스택의 합니다.        |
|Azure 리소스 관리자와 핵심 서비스     |Azure 리소스 관리자는 사용자의 통신을 전송 하는 리소스 공급자와 통신 합니다. <br><br> 사용 하 여 전송 *직접 명령적* 호출 또는 *선언적* 통해 호출 [Azure 리소스 관리자 템플릿을](/azure/azure-stack/user/azure-stack-arm-templates.md)합니다.|
|리소스 공급자     |리소스 공급자에 전달 된 호출 인증서 기반 인증으로 보호 됩니다. <br><br>Azure 리소스 관리자 및 리소스 공급자는 다음 API 통해 통신에 남아 있습니다. Azure 리소스 관리자에서 받은 모든 호출에 대 한 리소스 공급자는 해당 인증서를 사용 하 여 호출을 확인 합니다.|
|인프라 및 비즈니스 논리     |리소스 공급자가 선택한 인증 모드를 사용 하 여 비즈니스 논리 및 인프라와 통신 합니다. 기본 리소스 제공 되는 공급자와 Azure 스택 보안이 통신을 위해 Windows 인증을 사용 합니다.|

![인증에 필요한 정보](media/azure-stack-identity-overview/authentication.png)


### <a name="authenticate-to-azure-resource-manager"></a>Azure 리소스 관리자 인증
Id 공급자를 사용 하 여 인증 및 JWT 받기는 다음 정보가 있어야 합니다. 
1.  **Id 시스템 (인증 기관)에 대 한 URL** – id 공급자를 연결할 수 있는 URL입니다. 예를 들어  *&lt;https://login.windows.net>*합니다. 
2.  **Azure 리소스 관리자에 대 한 앱 ID URI** – 각 Azure 스택 설치에 고유한 id 공급자에 등록 되는 Azure 리소스 관리자에 대 한 고유 식별자입니다.
3.  **자격 증명** -이 id 공급자와 인증을 사용 중인 자격 증명입니다.  
4.  **Azure 리소스 관리자에 대 한 URL** -URL이 Azure 리소스 관리자 서비스의 위치입니다. 예를 들어  *&lt;https://management.azure.com>* 또는  *&lt;https://management.local.azurestack.external>*합니다.

보안 주체 (클라이언트, 응용 프로그램 또는 사용자)는 리소스에 액세스 하는 인증 요청을 하면 해당 요청에 포함 되어야 합니다.
- 사용자의 자격 증명입니다.
- 액세스 하려는 리소스의 앱 ID URI입니다.  

자격 증명 id 공급자에 의해 유효성이 검사 됩니다. Id 공급자에는 또한 등록된 된 응용 프로그램에 대 한 앱 ID URI 이며 주 서버에 토큰을 가져올 해당 리소스에 대 한 올바른 권한이 있는지 확인 합니다. 요청이 유효한 경우 JSON 웹 토큰 부여 됩니다. 

토큰 다음 통과 해야는 요청의 헤더에서 Azure 리소스 관리자에 합니다. Azure 리소스 관리자를 사용 하는 순서에 관계 없이 다음 유효성 검사가 수행 됩니다.
- 유효성 검사는 *발급자* (iss) 클레임에 올바른 id 공급자 로부터 토큰 있는지 확인 합니다. 
- 유효성 검사는 *audience* (aud) 클레임 토큰이 Azure 리소스 관리자에 발급 되었는지 확인 합니다. 
- JWT가 OpenID, 통해 구성 된 Azure 리소스 관리자에 알려져 있는 인증서로 서명 되었는지 확인 합니다. 
- 검토는 *에서 발급 된* (iat) 및 *만료* (exp) 토큰 활성 상태 인지 확인 하 고 허용 될 수 있습니다. 

Azure 리소스 관리자를 사용 하는 모든 유효성 검사 완료 되 면는 *할 때* (oid) 및 *그룹* 주장 하는 보안 주체가 액세스할 수 있는 리소스의 목록을 만듭니다. 

![토큰 교환 프로토콜의 다이어그램](media/azure-stack-identity-overview/token-exchange.png)


### <a name="use-role-based-access-control"></a>역할 기반 Access Control 사용  
역할 기반 액세스 제어 (RBAC) Azure 스택의 Microsoft Azure의 구현은 일치 합니다.  사용자, 그룹 및 응용 프로그램에서 RBAC에 적절 한 역할을 할당 하 여 리소스에 대 한 액세스를 관리할 수 있습니다. Azure 스택 함께 RBAC를 사용 하는 방법에 대 한 내용은 다음 문서를 검토 합니다.
- [Azure 포털에서 역할 기반 액세스 제어를 시작 하려면](/azure/active-directory/role-based-access-control-what-is)합니다.
- [역할 기반 액세스 제어를 사용 하 여 Azure 구독 리소스에 대 한 액세스를 관리 하려면](/azure/active-directory/role-based-access-control-configure)합니다.
- [신속히 알아봅니다 액세스 제어에 대 한 사용자 지정 역할 만들기](/azure/active-directory/role-based-access-control-custom-roles)합니다.
- [역할 기반 액세스 제어 관리](azure-stack-manage-permissions.md) Azure 스택의 합니다.


### <a name="authenticate-with-azure-powershell"></a>Azure PowerShell을 사용 하 여 인증  
Azure 스택 인증에 Azure PowerShell을 사용 하는 방법에 대 한 세부 정보에서 찾을 수 있습니다 [Azure 스택 사용자의 PowerShell 환경을 구성](azure-stack-powershell-configure-user.md)합니다.

### <a name="authenticate-with-azure-cli"></a>Azure CLI를 사용 하 여 인증
Azure 스택 인증에 Azure PowerShell을 사용 하는 방법에 대 한 세부 정보에서 찾을 수 있습니다 [설치 Azure 스택과 함께 사용 하기 위해 CLI를 구성 하 고](/azure/azure-stack/user/azure-stack-connect-cli.md)합니다.

## <a name="next-steps"></a>다음 단계
- [Id 아키텍처](azure-stack-identity-architecture.md)   
- [데이터 센터 통합-Identity](azure-stack-integrate-identity.md)




