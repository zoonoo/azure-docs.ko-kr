---
title: Key vault에 대한 액세스 보안
description: Active Directory 인증 및 리소스 끝점을 포함 하는 Azure Key Vault에 대 한 액세스 모델입니다.
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 10/07/2020
ms.author: sudbalas
ms.openlocfilehash: d110630ad3291473aee395259d1aaa623a935f5f
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/07/2020
ms.locfileid: "91825469"
---
# <a name="secure-access-to-a-key-vault"></a>Key vault에 대한 액세스 보안

Azure Key Vault는 암호화 키와 비밀(예: 인증서, 연결 문자열 및 암호)을 보호하는 클라우드 서비스입니다. 이 데이터는 민감하고 업무상 중요하기 때문에 권한이 부여된 애플리케이션과 사용자만 허용하여 Key Vault에 대한 액세스를 보호해야 합니다. 이 문서에서는 Key Vault 액세스 모델에 대한 개요를 제공합니다. 여기서는 인증 및 권한 부여에 대해 설명하고 Key Vault 액세스의 보안을 유지하는 방법을 설명합니다.

Key Vault에 대한 자세한 내용은 [Azure Key Vault 정보](overview.md)를 참조하세요. 키 자격 증명 모음에 저장할 수 있는 항목에 대한 자세한 내용은 [키, 비밀 및 인증서 정보](about-keys-secrets-certificates.md)를 참조하세요.

## <a name="access-model-overview"></a>액세스 모델 개요

Key Vault에 대한 액세스는 두 인터페이스, 즉 **관리 평면** 및 **데이터 평면**을 통해 제어됩니다. 관리 평면에서는 Key Vault 자체를 관리합니다. 이 평면의 작업에는 Key Vault 만들기와 삭제, Key Vault 속성 검색 및 액세스 정책 업데이트가 포함됩니다. 데이터 평면에서는 Key Vault에 저장된 데이터로 작업을 수행합니다. 키, 비밀 및 인증서를 추가, 삭제 및 수정할 수 있습니다.

두 평면 모두 인증에 [Azure Active Directory (AZURE AD)](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis) 를 사용 합니다. 권한 부여의 경우 관리 평면은 [azure 역할 기반 액세스 제어 (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview) 를 사용 하 고 데이터 평면은 [Key Vault 데이터 평면 작업 (미리 보기)에 대 한](https://docs.microsoft.com/azure/key-vault/general/rbac-guide) [KEY VAULT 액세스 정책](https://docs.microsoft.com/azure/key-vault/general/assign-access-policy-portal) 및 Azure rbac를 사용 합니다.

두 평면에서 key vault에 액세스하려면 모든 호출자(사용자 또는 애플리케이션)에게 적절한 인증 및 권한이 있어야 합니다. 인증은 호출자의 ID를 설정합니다. 권한은 호출자가 실행할 수 있는 작업을 결정합니다. Key Vault를 사용한 인증은 [Azure AD(Azure Active Directory)](/azure/active-directory/fundamentals/active-directory-whatis)와 함께 작동하며, 이는 지정된 **보안 주체**의 ID를 인증하는 역할을 담당합니다.

보안 주체는 사용자, 그룹 또는 Azure 리소스에 대한 액세스를 요청하는 애플리케이션을 나타내는 개체입니다. Azure는 모든 보안 주체에 고유한 **개체 ID**를 할당합니다.

* **사용자** 보안 주체는 Azure Active Directory에 프로필이 있는 개인을 식별합니다.

* **그룹** 보안 주체는 Azure Active Directory에서 만든 사용자 세트를 식별합니다. 그룹에 할당된 모든 역할 또는 사용 권한은 그룹 내의 모든 사용자에 부여됩니다.

* **서비스 주체**는 애플리케이션 또는 서비스를 식별하는 보안 주체의 유형입니다. 즉, 사용자 또는 그룹이 아닌 코드 조각입니다. 서비스 주체의 개체 ID를 **클라이언트 ID**라고 하며, 사용자 이름처럼 작동합니다. 서비스 주체의 **클라이언트** 암호 또는 **인증서** 는 암호 처럼 작동 합니다. 많은 Azure 서비스는 **클라이언트 ID** 및 **인증서**의 자동화 된 관리를 통해 [관리 되는 id](/azure/active-directory/managed-identities-azure-resources/overview) 할당을 지원 합니다. 관리 id는 Azure 내에서 인증에 가장 안전 하 고 권장 되는 옵션입니다.

Key Vault 인증에 대 한 자세한 내용은 [Azure Key Vault에 인증](authentication.md) 을 참조 하세요.

## <a name="key-vault-authentication-options"></a>Key Vault 인증 옵션

Azure 구독에 Key Vault을 만들 때 해당 구독의 Azure AD 테넌트에 자동으로 연결됩니다. 두 평면의 모든 호출자가 이 테넌트에 등록해야 하고, 해당 Key Vault에 액세스하기 위해 인증을 받아야 합니다. 두 경우 모두 애플리케이션에서 다음과 같은 두 가지 방법으로 Key Vault에 액세스할 수 있습니다.

- **응용 프로그램 전용**: 응용 프로그램은 서비스 주체 또는 관리 id를 나타냅니다. 이 id는 정기적으로 키 자격 증명 모음에서 인증서, 키 또는 암호에 액세스 해야 하는 응용 프로그램에 가장 일반적으로 사용 되는 시나리오입니다. 이 시나리오가 작동 하려면 `objectId` 응용 프로그램의를 액세스 정책에 지정 하 고을 `applicationId` 지정 _하지_ 않아야 하거나를 지정 해야 합니다 `null` .
- **사용자 전용**: 사용자는 테 넌 트에 등록 된 응용 프로그램에서 키 자격 증명 모음에 액세스 합니다. Azure PowerShell과 Azure Portal이 이러한 액세스 유형의 예제입니다. 이 시나리오가 작동 하려면 `objectId` 사용자의을 액세스 정책에 지정 하 고을 `applicationId` 지정 _하지_ 않아야 하거나를 지정 해야 합니다 `null` .
- **응용 프로그램-사용자** ( _복합 id_라고도 함): 사용자는 특정 응용 프로그램에서 키 자격 증명 모음에 액세스 해야 _하며,_ 응용 프로그램은 사용자를 가장 하기 위해 obo (인증 시) 흐름을 사용 해야 합니다. 이 시나리오가 작동 하려면 `applicationId` 및 모두 `objectId` 액세스 정책에서 지정 해야 합니다. 는 `applicationId` 필수 응용 프로그램을 식별 하 고는 `objectId` 사용자를 식별 합니다. 현재이 옵션은 데이터 평면 Azure RBAC (미리 보기)에서 사용할 수 없습니다.

모든 유형의 액세스에서 응용 프로그램은 Azure AD를 사용 하 여 인증 합니다. 애플리케이션은 애플리케이션 유형에 따라 [지원되는 인증 방법](../../active-directory/develop/authentication-scenarios.md)을 사용합니다. 애플리케이션은 액세스 권한을 부여하기 위해 평면의 리소스에 대해 토큰을 획득합니다. 리소스는 Azure 환경에 따라 관리 또는 데이터 평면의 엔드포인트입니다. 애플리케이션은 해당 토큰을 사용하고 REST API 요청을 Key Vault에 보냅니다. 자세한 내용은 [전체 인증 흐름](../../active-directory/develop/v2-oauth2-auth-code-flow.md)을 참조하세요.

두 평면에 대한 인증에 단일 메커니즘을 사용할 경우 다음과 같은 몇 가지 이점이 있습니다.

- 조직에서는 조직의 모든 Key Vault에 대한 액세스를 중앙에서 제어할 수 있습니다.
- 사용자가 떠나는 경우 곧바로 조직의 모든 Key Vault에 대한 액세스 권한을 잃게 됩니다.
- 조직은 Azure AD에 있는 옵션(예: 추가 보안을 위해 다단계 인증 사용)을 사용하여 인증을 사용자 지정할 수 있습니다.

## <a name="resource-endpoints"></a>리소스 엔드포인트

애플리케이션은 엔드포인트를 통해 평면에 액세스합니다. 두 평면에 대한 액세스 제어는 독립적으로 작동합니다. 키 자격 증명 모음에서 키를 사용 하는 응용 프로그램 액세스 권한을 부여 하려면 Key Vault 액세스 정책 또는 Azure RBAC (미리 보기)를 사용 하 여 데이터 평면 액세스를 부여 합니다. 사용자에게 Key Vault 속성 및 태그에 대한 액세스 권한을 부여하려고 하지만 데이터(키, 비밀 또는 인증서)에 대한 액세스 권한은 부여하지 않으려면 RBAC를 사용하여 관리 평면 액세스 권한을 부여합니다.

다음 표에서는 관리 및 데이터 평면에 대한 엔드포인트를 보여 줍니다.

| 액세스&nbsp;평면 | 액세스 엔드포인트 | 작업 | 액세스&nbsp;제어 메커니즘 |
| --- | --- | --- | --- |
| 관리 평면 | **전역:**<br> management.azure.com:443<br><br> **Azure China 21Vianet:**<br> management.chinacloudapi.cn:443<br><br> **Azure 미국 정부:**<br> management.usgovcloudapi.net:443<br><br> **Azure 독일:**<br> management.microsoftazure.de:443 | Key Vault 만들기, 읽기, 업데이트 및 삭제<br><br>Key Vault 액세스 정책 설정<br><br>Key Vault 태그 설정 | Azure RBAC |
| 데이터 평면 | **전역:**<br> &lt;vault-name&gt;.vault.azure.net:443<br><br> **Azure China 21Vianet:**<br> &lt;vault-name&gt;.vault.azure.cn:443<br><br> **Azure 미국 정부:**<br> &lt;vault-name&gt;.vault.usgovcloudapi.net:443<br><br> **Azure 독일:**<br> &lt;vault-name&gt;.vault.microsoftazure.de:443 | 키: 암호화, 암호 해독, wrapKey, unwrapKey, 서명, 확인, 가져오기, 목록, 만들기, 업데이트, 가져오기, 삭제, 복구, 백업, 복원, 제거<br><br> 인증서: managecontacts, getissuers, listissuers, setissuers, deleteissuers, manageissuers, 가져오기, 목록, 만들기, 가져오기, 업데이트, 삭제, 복구, 백업, 복원, 제거<br><br>  비밀: get, list, set, delete, 복구, 백업, 복원, 제거 | Key Vault 액세스 정책 또는 Azure RBAC (미리 보기)|

## <a name="management-plane-and-azure-rbac"></a>관리 평면 및 Azure RBAC

관리 평면에서 [AZURE RBAC (역할 기반 액세스 제어)](https://docs.microsoft.com/azure/role-based-access-control/overview) 를 사용 하 여 호출자가 실행할 수 있는 작업에 권한을 부여 합니다. Azure RBAC 모델에서 각 Azure 구독은 Azure AD의 인스턴스를 포함 합니다. 이 디렉터리에서 사용자, 그룹 및 애플리케이션에 대해 액세스 권한을 부여합니다. Resource Manager 배포 모델을 사용하는 Azure 구독의 리소스를 관리할 수 있는 액세스 권한을 부여합니다.

Azure AD를 사용하여 리소스 그룹에 key vault를 만들고 액세스를 관리합니다. 사용자 또는 그룹에 리소스 그룹에서 key vault를 관리하는 기능을 부여합니다. 적절 한 Azure 역할을 할당 하 여 특정 범위 수준에서 액세스 권한을 부여 합니다. key vault를 관리하기 위해 사용자에게 액세스 권한을 부여하려면 특정 범위에 속한 사용자에게 미리 정의된 `key vault Contributor` 역할을 할당합니다. Azure 역할에 할당 될 수 있는 범위 수준은 다음과 같습니다.

- **구독**: 구독 수준에서 할당 된 Azure 역할은 해당 구독 내의 모든 리소스 그룹 및 리소스에 적용 됩니다.
- **리소스 그룹**: 리소스 그룹 수준에서 할당 된 Azure 역할은 해당 리소스 그룹의 모든 리소스에 적용 됩니다.
- **특정 리소스**: 특정 리소스에 할당 된 Azure 역할이 해당 리소스에 적용 됩니다. 이 경우 리소스는 특정 Key Vault입니다.

미리 정의된 몇 가지 역할이 있습니다. 미리 정의된 역할이 요구에 맞지 않는 경우 고유한 역할을 정의할 수 있습니다. 자세한 정보는 [Azure 기본 제공 역할](../../role-based-access-control/built-in-roles.md)을 참조하세요. 

`Microsoft.Authorization/roleAssignments/write` `Microsoft.Authorization/roleAssignments/delete` [사용자 액세스 관리자](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles.md#user-access-administrator) 또는 [소유자](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles.md#owner) 와 같은 및 사용 권한이 있어야 합니다.

> [!IMPORTANT]
> 사용자에게 Key Vault 관리 평면에 대한 `Contributor` 사용 권한이 있는 경우 이 사용자는 Key Vault 액세스 정책을 설정하여 스스로 데이터 평면에 대한 액세스 권한을 부여할 수 있습니다. Key Vault에 대한 `Contributor` 역할 액세스 권한이 있는 사용자를 긴밀하게 제어해야 합니다. 권한이 있는 사람만 키 자격 증명 모음, 키, 비밀 및 인증서에 액세스하고 관리할 수 있는지 확인합니다.
>

<a id="data-plane-access-control"></a>
## <a name="data-plane-and-access-policies"></a>데이터 평면 및 액세스 정책

키 자격 증명 모음에 대 한 액세스 정책 Key Vault 설정 하 여 데이터 평면 액세스 권한을 부여할 수 있습니다. 이러한 액세스 정책을 설정하려면 사용자, 그룹 또는 애플리케이션은 해당 Key Vault에 대한 관리 평면에서 `Key Vault Contributor` 권한이 있어야 합니다.

Key Vault의 키 또는 비밀에 대해 특정 작업을 실행하기 위해 사용자, 그룹 또는 애플리케이션에 액세스 권한을 부여합니다. 각 Key Vault는 최대 1024개 액세스 정책 항목을 지원합니다. 여러 사용자에게 데이터 평면 액세스 권한을 부여하려면 Azure AD 보안 그룹을 만들고, 해당 그룹에 사용자를 추가합니다.

[Key Vault 작업 참조](https://docs.microsoft.com/rest/api/keyvault/#vault-operations) 에서 자격 증명 모음 및 암호 작업의 전체 목록을 볼 수 있습니다.

<a id="key-vault-access-policies"></a> Key Vault 액세스 정책은 키, 비밀 및 인증서에 대한 권한을 별도로 부여합니다.  키, 비밀 및 인증서에 대한 액세스 권한은 자격 증명 모음 수준에서 지정됩니다. 

키 자격 증명 모음 액세스 정책을 사용 하는 방법에 대 한 자세한 내용은 [Key Vault 액세스 정책 할당](assign-access-policy-portal.md) 을 참조 하세요.

> [!IMPORTANT]
> Key Vault 액세스 정책은 자격 증명 모음 수준에 적용됩니다. 사용자에게 키를 만들고 삭제하는 권한이 부여되면 해당 사용자는 Key Vault의 모든 키에 대해 이러한 작업을 수행할 수 있습니다.
Key Vault 액세스 정책은 특정 키, 비밀 또는 인증서와 같은 세분화된 개체 수준 권한을 지원하지 않습니다. 
>

## <a name="data-plane-and-azure-rbac-preview"></a>데이터 평면 및 Azure RBAC (미리 보기)

Azure 역할 기반 액세스 제어는 개별 키 자격 증명 모음에서 사용 하도록 설정할 수 있는 Azure Key Vault 데이터 평면에 대 한 액세스를 제어 하는 대체 권한 모델입니다. Azure RBAC 권한 모델은 배타적 이며, 설정 되 면 자격 증명 모음 액세스 정책이 비활성화 됩니다. Azure Key Vault는 키, 암호 또는 인증서에 액세스 하는 데 사용 되는 일반 권한 집합을 포함 하는 Azure 기본 제공 역할 집합을 정의 합니다.

Azure AD 보안 주체에 azure 역할을 할당 하는 경우 Azure는 해당 보안 주체에 대 한 해당 리소스에 대 한 액세스 권한을 부여 합니다. 액세스 범위는 구독, 리소스 그룹, 주요 자격 증명 모음 또는 개별 키, 비밀 또는 인증서의 수준으로 지정할 수 있습니다. Azure AD 보안 주체는 사용자, 그룹, 응용 프로그램 서비스 주체 또는 [azure 리소스에 대 한 관리 되는 id](../../active-directory/managed-identities-azure-resources/overview.md)일 수 있습니다.

자격 증명 모음 액세스 정책에 대 한 Azure RBAC 권한을 사용 하는 경우의 주요 이점은 중앙 액세스 제어 관리 이며 [PIM (Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure))과의 통합입니다. Privileged Identity Management는 중요한 리소스에 대한 과도한, 불필요한 또는 잘못 사용된 액세스 권한의 위험을 완화할 수 있도록 시간 기반 및 승인 기반 역할 활성화를 제공합니다.

RBAC를 사용 하 여 데이터 평면 Key Vault 하는 방법에 대 한 자세한 내용은 [Azure 역할 기반 액세스 제어를 사용 하 여 키, 인증서 및 암호 Key Vault (미리 보기)](rbac-guide.md) 를 참조 하세요.

## <a name="firewalls-and-virtual-networks"></a>방화벽 및 가상 네트워크

추가 보안 계층의 경우 방화벽 및 가상 네트워크 규칙을 구성할 수 있습니다. 기본적으로 모든 네트워크(인터넷 트래픽 포함)의 트래픽에 대한 액세스를 거부하도록 Key Vault 방화벽 및 가상 네트워크를 구성할 수 있습니다. 특정 [Azure 가상 네트워크](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) 및 공용 인터넷 IP 주소 범위에서 트래픽에 대 한 액세스 권한을 부여 하 여 응용 프로그램에 대 한 보안 네트워크 경계를 구축할 수 있습니다.

서비스 엔드포인트를 사용할 수 있는 방법의 몇 가지 예는 다음과 같습니다.

* Key Vault를 사용하여 암호화 키, 애플리케이션 비밀 및 인증서를 저장하고, 공용 인터넷에서 키 자격 증명 모음에 대한 액세스를 차단하려고 합니다.
* 사용자 애플리케이션 또는 지정된 호스트의 간단한 목록만 키 자격 증명 모음에 연결할 수 있도록 키 자격 증명 모음에 대한 액세스를 잠그려고 합니다.
* Azure 가상 네트워크에서 실행되는 애플리케이션이 있으며, 이 가상 네트워크는 모든 인바운드 및 아웃바운드 트래픽에 대해 잠겨 있습니다. 애플리케이션은 비밀 또는 인증서를 가져오거나 암호화 키를 사용하려면 여전히 Key Vault에 연결해야 합니다.

방화벽 및 가상 네트워크를 Key Vault 하는 방법에 대 한 자세한 내용은 [Azure Key Vault 방화벽 및 가상 네트워크 구성](network-security.md) 을 참조 하세요.

> [!NOTE]
> Key Vault 방화벽 및 가상 네트워크 규칙은 Key Vault의 데이터 평면에만 적용됩니다. Key Vault 제어 평면 작업(예: 작업 만들기, 삭제 및 수정 그리고 액세스 정책 설정, 방화벽 및 가상 네트워크 규칙 설정)은 방화벽 및 가상 네트워크 규칙의 영향을 받지 않습니다.

## <a name="private-endpoint-connection"></a>개인 끝점 연결

공개에 대 한 Key Vault 노출을 완전히 차단 해야 하는 경우 Azure 개인 끝점을 사용할 수 있습니다. Azure 프라이빗 엔드포인트는 Azure Private Link에서 제공하는 서비스에 비공개로 안전하게 연결하는 네트워크 인터페이스입니다. 프라이빗 엔드포인트는 VNet의 개인 IP 주소를 사용하여 서비스를 VNet으로 효과적으로 가져옵니다. 서비스에 대한 모든 트래픽은 프라이빗 엔드포인트를 통해 라우팅할 수 있으므로 게이트웨이, NAT 디바이스, ExpressRoute 또는 VPN 연결 또는 공용 IP 주소가 필요하지 않습니다. 가상 네트워크와 서비스 간의 트래픽은 Microsoft 백본 네트워크를 통해 이동하여 공용 인터넷에서 노출을 제거합니다. Azure 리소스의 인스턴스에 연결하여 액세스 제어에서 가장 높은 수준의 세분성을 제공할 수 있습니다.

Azure 서비스에 대 한 개인 링크 사용에 대 한 일반적인 시나리오는 다음과 같습니다.

- **Azure 플랫폼에서 서비스에 비공개로 액세스**: 원본 또는 대상의 공용 IP 주소 없이 Azure 서비스에 가상 네트워크를 연결할 수 있습니다. 서비스 공급자는 자체 가상 네트워크에서 서비스를 렌더링할 수 있으며, 소비자는 로컬 가상 네트워크에서 이러한 서비스에 액세스할 수 있습니다. Private Link 플랫폼은 Azure 백본 네트워크를 통해 소비자와 서비스 간의 연결을 처리합니다. 
 
- **온-프레미스 및 피어링된 네트워크** 프라이빗 엔드포인트를 사용하여 온-프레미스에서 ExpressRoute 프라이빗 피어링, VPN 터널 및 피어링된 가상 네트워크를 통해 Azure에서 실행되는 서비스에 액세스할 수 있습니다. 공용 피어 링을 설정 하거나 인터넷을 통과 하 여 서비스에 연결 하지 않아도 됩니다. Private Link는 워크로드를 Azure로 안전하게 마이그레이션하는 방법을 제공합니다.
 
- **데이터 유출 방지**: 프라이빗 엔드포인트는 전체 서비스가 아닌 PaaS 리소스 인스턴스에 매핑됩니다. 소비자는 특정 리소스에만 연결할 수 있습니다. 서비스의 다른 리소스에 대한 액세스는 차단됩니다. 이 메커니즘은 데이터 유출 위험을 방지합니다. 
 
- **글로벌 환경**: 다른 Azure 지역에서 실행되는 서비스에 비공개로 연결할 수 있습니다. 소비자의 가상 네트워크는 A 지역에 있으며, B 지역의 Private Link 뒤에 있는 서비스에 연결할 수 있습니다.  
 
- **사용자 고유의 서비스로 확장**: 동일한 환경과 기능을 사용하여 자체 서비스를 Azure의 소비자에게 비공개로 렌더링할 수 있습니다. 서비스를 표준 Azure Load Balancer 뒤에 배치하여 Private Link에 사용할 수 있습니다. 그러면 소비자는 자체 가상 네트워크에서 프라이빗 엔드포인트를 사용하여 서비스에 직접 연결할 수 있습니다. 승인 호출 흐름을 사용하여 연결 요청을 관리할 수 있습니다. Azure Private Link는 서로 다른 Azure Active Directory 테넌트에 속한 소비자 및 서비스에 대해 작동합니다. 

개인 끝점에 대 한 자세한 내용은 [Azure 개인 링크를 사용 하 여 Key Vault](https://docs.microsoft.com/azure/key-vault/general/private-link-service) 을 참조 하세요.

## <a name="example"></a>예제

이 예제에서는 TLS/SSL에 인증서를 사용 하는 응용 프로그램을 개발 하 고, Azure Storage 데이터를 저장 하 고, Azure Storage 데이터를 암호화 하기 위한 RSA 2048 비트 키를 개발 합니다. 애플리케이션은 Azure VM(Virtual Machine)(또는 가상 머신 확장 집합)에서 실행됩니다. Key Vault를 사용하여 애플리케이션 비밀을 저장할 수 있습니다. 애플리케이션이 Azure AD에서 인증을 받는 데 사용하는 부트스트랩 인증서를 저장할 수 있습니다.

다음 저장된 키와 비밀에 액세스해야 합니다.
- **TLS/SSL 인증서**: TLS/SSL에 사용됩니다.
- **스토리지 키**: 스토리지 계정에 액세스하는 데 사용합니다.
- **RSA 2048 비트 키**: Azure Storage 여 데이터 암호화 키 래핑/래핑에 사용 됩니다.
- **응용 프로그램 관리 id**: Azure AD를 인증 하는 데 사용 됩니다. Key Vault에 대 한 액세스 권한이 부여 되 면 응용 프로그램은 저장소 키와 인증서를 가져올 수 있습니다.

애플리케이션을 관리, 배포 및 감사할 수 있는 사용자를 지정하려면 다음 역할을 정의해야 합니다.
- **보안 팀**: CSO(최고 보안 책임자) 사무실 IT 직원 또는 비슷한 참가자입니다. 이 보안 팀은 비밀의 적절한 보호를 담당합니다. 암호에는 TLS/SSL 인증서, 암호화를 위한 RSA 키, 연결 문자열 및 저장소 계정 키가 포함 될 수 있습니다.
- **개발자 및 운영자**: 애플리케이션을 개발하고 Azure에 배포하는 직원입니다. 이 팀의 멤버는 보안 담당자에 속하지 않습니다. 이러한 직원은 TLS/SSL 인증서 및 RSA 키와 같은 중요한 데이터에 액세스할 수 없어야 합니다. 배포하는 애플리케이션에서만 중요한 데이터에 액세스할 수 있어야 합니다.
- **감사자**: 이 역할은 개발 팀의 멤버 또는 일반 IT 직원이 아닌 참가자입니다. 이들은 인증서, 키 및 비밀의 사용 및 유지 관리를 검토하여 보안 표준을 준수하도록 합니다.

애플리케이션의 범위 외부에 있는 다른 역할, 즉 구독(또는 리소스 그룹) 관리자 역할이 있습니다. 구독 관리자는 보안 팀의 초기 액세스 권한을 설정합니다. 이들은 애플리케이션에 필요한 리소스가 있는 리소스 그룹을 사용하여 보안 팀에 액세스 권한을 부여합니다.

역할에 대해 다음과 같은 작업 권한을 부여해야 합니다.

**보안 팀**
- Key Vault를 만듭니다.
- Key Vault 로깅을 켭니다.
- 키 및 비밀을 추가합니다.
- 재해 복구를 위해 키 백업을 만듭니다.
- 액세스 정책 Key Vault 설정 하 고 역할을 할당 하 여 특정 작업에 대 한 사용자 및 응용 프로그램에 권한을 부여 합니다.
- 키 및 비밀을 주기적으로 롤링합니다.

**개발자 및 운영자**
- 줄 바꿈/래핑 해제를 위해 보안 팀에서 부트스트랩 및 TLS/SSL 인증서 (지문), 저장소 키 (비밀 URI) 및 RSA 키 (키 URI)에 대 한 참조를 가져옵니다.
- 응용 프로그램을 개발 하 고 배포 하 여 프로그래밍 방식으로 인증서 및 비밀에 액세스 합니다.

**감사자**
- Key Vault 로그를 검토하여 적절한 키/비밀 사용 및 데이터 보안 표준 규정 준수를 확인합니다.

다음 표에서는 역할과 애플리케이션에 대한 액세스 권한을 요약해서 설명합니다.

| 역할 | 관리 평면 사용 권한 | 데이터 평면 권한-자격 증명 모음 액세스 정책 | 데이터 평면 권한-Azure RBAC (미리 보기)  |
| --- | --- | --- | --- |
| 보안 팀 | Key Vault 참가자 | 인증서: 모든 작업 <br> 키: 모든 작업 <br> 비밀: 모든 작업 | Key Vault 관리자 (미리 보기) |
| 개발자 및&nbsp;운영자 | Key Vault 배포 권한<br><br> **참고**: 이 권한이 있으면 배포된 VM이 Key Vault에서 비밀을 가져올 수 있습니다. | None | None |
| 감사자 | None | 인증서: 목록 <br> 키: 목록 표시<br>암호: 목록 표시<br><br> **참고**: 이 권한이 있으면 감사자는 로그에서 내보내지 않은 키 및 비밀의 특성(태그, 활성화 날짜, 만료 날짜)을 검사할 수 있습니다. | Key Vault 판독기 (미리 보기) |
| Azure Storage 계정 | None | 키: get, list, wrapKey, unwrapKey <br> | 암호화 서비스 암호화 Key Vault |
| 애플리케이션 | None | 비밀: get, list <br> 인증서: get, list | Key Vault 판독기 (미리 보기), Key Vault 비밀 사용자 (미리 보기) |

이 세 가지 팀 역할은 Key Vault 사용 권한과 함께 다른 리소스에 대한 액세스 권한이 필요합니다. Vm (또는 Azure App Service의 Web Apps 기능)을 배포 하려면 개발자와 운영자에 게 배포 액세스 권한이 있어야 합니다. 감사자에게는 Key Vault 로그를 저장할 스토리지 계정에 대한 읽기 액세스 권한이 필요합니다.

이 예제에서는 간단한 시나리오를 설명합니다. 실제 시나리오는 더 복잡할 수 있습니다. 필요에 따라 key vault에 대한 사용 권한을 조정할 수 있습니다. 보안 팀에서 DevOps 직원이 애플리케이션에서 사용하는 키 및 비밀 참조(URI 및 지문)를 제공한다고 가정합니다. 개발자 및 운영자에게는 데이터 평면 액세스가 필요하지 않습니다. Key Vault를 보호하는 방법을 집중적으로 살펴보았습니다.

> [!NOTE]
> 이 예제에서는 프로덕션 환경에서 Key Vault 액세스를 잠그는 방법을 보여줍니다. 개발자는 애플리케이션을 개발하는 자격 증명 모음, VM 및 스토리지 계정을 관리할 수 있는 모든 권한을 갖춘 구독 또는 리소스 그룹을 가지고 있어야 합니다.

## <a name="resources"></a>리소스

* [Privileged Identity Management](../../active-directory/privileged-identity-management/pim-configure.md)

## <a name="next-steps"></a>다음 단계

[Azure Key Vault에 인증](authentication.md)

[Key Vault 액세스 정책 할당](assign-access-policy-portal.md)

[키, 암호 및 인증서에 대 한 액세스에 Azure 역할 할당](rbac-guide.md)

[Key Vault 방화벽 및 가상 네트워크 구성](network-security.md)

[Key Vault에 대 한 개인 링크 연결 설정](private-link-service.md)
