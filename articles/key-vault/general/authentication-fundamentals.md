---
title: Azure Key Vault 인증 기본 사항
description: 키 자격 증명 모음 인증 모델의 작동 방식에 대한 자세한 정보
author: ShaneBala-keyvault
ms.author: sudbalas
ms.date: 09/25/2020
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.openlocfilehash: 25f00024fb7371fd08bf6c4ceec3177cfaca029b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103572811"
---
# <a name="key-vault-authentication-fundamentals"></a>Key Vault 인증 기본 사항

Azure Key Vault를 사용하면 중앙 및 보안 클라우드 리포지토리에서 비밀, 키, 인증서와 같은 애플리케이션 자격 증명을 안전하게 저장하고 관리할 수 있습니다. Key Vault를 사용하면 자격 증명을 애플리케이션에 저장할 필요가 없습니다. 애플리케이션은 런타임에 Key Vault에 인증하여 자격 증명을 검색할 수 있습니다.

관리자는 키 자격 증명 모음에 액세스할 수 있는 사용자 및 애플리케이션을 제어할 수 있으며, 수행하는 작업을 제한하고 감사할 수 있습니다. 이 문서에서는 주요 자격 증명 모음 액세스 모델의 기본 개념을 설명합니다. 여기서는 소개 수준의 정보를 제공하고, 사용자 또는 애플리케이션을 주요 자격 증명 모음에 인증하는 방법을 처음부터 끝까지 보여 줍니다.

## <a name="required-knowledge"></a>필수 정보

이 문서에서는 사용자가 다음 개념에 대해 잘 알고 있다고 가정합니다. 관련 개념에 익숙하지 않은 경우 계속하기 전에 도움말 링크를 참조하세요.

* Azure Active Directory [링크](../../active-directory/fundamentals/active-directory-whatis.md)
* 보안 주체 [링크](./authentication.md#app-identity-and-security-principals)

## <a name="key-vault-configuration-steps-summary"></a>Key Vault 구성 단계 요약

1. Azure Active Directory에서 사용자 또는 애플리케이션을 보안 주체로 등록합니다.
1. Azure Active Directory에서 보안 주체에 대한 역할 할당을 구성합니다.
1. 보안 주체에 대한 키 자격 증명 모음 액세스 정책을 구성합니다.
1. 키 자격 증명 모음에 대한 Key Vault 방화벽 액세스를 구성합니다(선택 사항).
1. 키 자격 증명 모음에 액세스하는 보안 주체의 능력을 테스트합니다.

## <a name="register-a-user-or-application-in-azure-active-directory-as-a-security-principal"></a>Azure Active Directory의 사용자 또는 애플리케이션을 보안 주체로 등록

사용자 또는 애플리케이션의 키 자격 증명 모음에 대한 요청을 수행하는 경우 먼저 Azure Active Directory에서 해당 요청을 인증해야 합니다. 이 작업을 수행하려면 사용자 또는 애플리케이션을 보안 주체로 Azure Active Directory에 등록해야 합니다.

Azure Active Directory에서 사용자 또는 애플리케이션을 등록하는 방법을 이해하려면 아래 설명서 링크를 따르세요.
**사용자 등록을 위한 암호와 애플리케이션에 대한 클라이언트 암호 또는 클라이언트 인증서 자격 증명을 만들어야 합니다.**

* Azure Active Directory에서 사용자 등록 [링크](../../active-directory/fundamentals/add-users-azure-active-directory.md)
* Azure Active Directory에서 애플리케이션 등록 [링크](../../active-directory/develop/quickstart-register-app.md)

## <a name="assign-your-security-principal-a-role"></a>보안 주체에 역할 할당

Azure RBAC(Azure 역할 기반 액세스 제어)를 통해 보안 주체에 사용 권한을 할당할 수 있습니다. 해당 사용 권한을 역할 할당이라고 합니다.

키 자격 증명 모음의 컨텍스트에서 역할 할당은 키 자격 증명 모음의 관리 평면(컨트롤 플레인이라고도 함)에 대한 보안 주체의 액세스 수준을 결정합니다. 역할 할당은 데이터 평면 비밀에 직접 액세스하지는 않지만, 키 자격 증명 모음의 속성을 관리할 수 있는 액세스를 제공합니다. 예를 들어 **reader 역할** 이 할당된 사용자 또는 애플리케이션은 주요 자격 증명 모음 방화벽 설정을 변경할 수 없으며, **contributor 역할** 이 할당된 사용자 또는 애플리케이션은 변경 작업을 수행할 수 있습니다. 두 역할 모두 키 자격 증명 모음 데이터 평면에 대한 액세스 권한이 할당될 때까지 해당 값을 만들거나 검색하는 등의 방법으로 암호, 키, 인증서에 대한 작업을 직접 수행할 수 없습니다. 이는 다음 단계에서 다룹니다.

>[!IMPORTANT]
> Contributor 또는 Owner 역할을 가진 사용자는 기본적으로 키 자격 증명 모음에 저장된 암호에 대한 작업을 수행할 수 있는 권한이 없지만, Contributor 또는 Owner 역할은 키 자격 증명 모음에 저장된 암호에 대한 액세스 정책을 추가하거나 제거할 수 있는 권한을 제공합니다. 따라서 해당 역할 할당을 가진 사용자는 키 자격 증명 모음의 비밀에 액세스할 수 있는 권한을 자신에게 부여할 수 있습니다. 따라서 관리자만 Contributor 또는 Owner 역할에 액세스할 수 있도록 하는 것이 좋습니다. 키 자격 증명 모음에서 비밀을 검색해야 하는 사용자와 애플리케이션에는 Reader 역할을 부여해야 합니다. **자세한 내용은 다음 섹션을 참조하세요.**

>[!NOTE]
> Azure Active Directory 테넌트 수준에서 사용자에게 역할을 할당하는 경우 이 권한 집합은 할당 범위 내에 있는 모든 구독, 리소스 그룹, 리소스에 확장됩니다. 최소 권한 원칙을 준수하기 위해 더욱 세분화된 범위에서 이 역할 할당을 수행할 수 있습니다. 예를 들어 사용자에게 구독 수준에서 Reader 역할을 할당하고 단일 키 자격 증명 모음에 대해 Owner 역할을 할당할 수 있습니다. 구독, 리소스 그룹 또는 주요 자격 증명 모음의 IAM(ID 및 액세스 관리) 설정으로 이동하여 더욱 세분화된 범위에서 역할 할당을 수행합니다.

* Azure 역할에 대한 자세한 정보 [링크](../../role-based-access-control/built-in-roles.md)
* 역할 할당 및 제거에 대한 자세한 정보 [링크](../../role-based-access-control/role-assignments-portal.md)

## <a name="configure-key-vault-access-policies-for-your-security-principal"></a>보안 주체에 대한 키 자격 증명 모음 액세스 정책 구성

사용자 및 애플리케이션에 키 자격 증명 모음에 대한 액세스를 부여하기 전에, 주요 자격 증명 모음에서 수행할 수 있는 다양한 유형의 작업을 이해하는 것이 중요합니다. 주요 자격 증명 모음 작업에는 두 가지 주요 유형인 관리 평면(컨트롤 플레인이라고도 함) 및 데이터 평면 작업이 있습니다.

다음 표에서는 관리 평면 및 데이터 평면에 의해 제어되는 다양한 작업의 몇 가지 예를 보여 줍니다. 관리 평면 작업은 키 자격 증명 모음의 속성을 변경합니다. 데이터 평면 작업은 키 자격 증명 모음에 저장된 비밀의 값을 변경하거나 검색합니다.

|관리 평면 작업(예제)|데이터 평면 작업(예제)|
| --- | --- |
| 주요 자격 증명 모음 만들기 | 키, 암호, 인증서 만들기
| 키 자격 증명 모음 삭제 | 키, 암호, 인증서 삭제
| Key Vault 역할 할당 추가 또는 제거 | 키, 암호, 인증서의 값 나열 및 가져오기
| Key Vault 액세스 정책 추가 또는 제거 | 키, 암호, 인증서 백업 및 복원
| Key Vault 방화벽 설정 수정 | 키, 암호, 인증서 갱신
| Key Vault 복구 설정 수정 | 일시 삭제된 키, 암호, 인증서 제거 또는 복구
| Key Vault 진단 로그 설정 수정

### <a name="management-plane-access--azure-active-directory-role-assignments"></a>관리 평면 액세스 및 Azure Active Directory 역할 할당

Azure Active Directory 역할 할당은 키 자격 증명 모음에 대한 관리 평면 작업을 수행할 수 있는 액세스 권한을 부여합니다. 이 액세스는 일반적으로 애플리케이션이 아닌 사용자에게 부여됩니다. 사용자의 역할 할당을 변경하여 사용자가 수행할 수 있는 관리 평면 작업을 제한할 수 있습니다.

예를 들어 사용자에게 Key Vault Reader 역할을 할당하면 액세스 정책과 같은 주요 자격 증명 모음의 속성을 볼 수 있지만 변경할 수는 없습니다. 사용자에게 Owner 역할을 할당하면 사용자는 키 자격 증명 모음 관리 평면 설정을 변경할 수 있는 권한을 갖습니다.

역할 할당은 키 자격 증명 모음 Access Control(IAM) 블레이드에서 제어됩니다. 특정 사용자가 Reader 또는 여러 주요 자격 증명 모음 리소스의 관리자가 되도록 하려면 자격 증명 모음, 리소스 그룹 또는 구독 수준에서 역할을 할당하면 되고, 해당 역할 할당은 할당 범위 내의 모든 리소스에 추가됩니다.

데이터 평면 액세스 또는 키 자격 증명 모음에 저장된 키, 암호, 인증서에 대한 작업을 수행하기 위한 액세스는 다음 두 가지 방법 중 하나로 추가할 수 있습니다.

### <a name="data-plane-access-option-1-classic-key-vault-access-policies"></a>데이터 평면 액세스 옵션 1: 클래식 키 자격 증명 모음 액세스 정책

키 자격 증명 모음 액세스 정책은 사용자 및 애플리케이션에 키 자격 증명 모음에 대한 데이터 평면 작업을 수행할 수 있는 권한을 부여합니다.

> [!NOTE]
> 이 액세스 모델은 아래에 설명된 키 자격 증명 모음에 대한 Azure RBAC(옵션 2)와 호환되지 않습니다. 하나의 항목을 선택해야 합니다. 키 자격 증명 모음의 액세스 정책 탭을 클릭하면 이 항목을 선택할 수 있습니다.

클래식 액세스 정책은 세분화되어 있어 개별 사용자 또는 애플리케이션이 키 자격 증명 모음 내에서 개별 작업을 수행하는 능력을 허용하거나 거부할 수 있습니다. 다음은 몇 가지 예입니다.

* 보안 주체 1은 모든 키 작업을 수행할 수 있지만 암호 또는 인증서 작업을 수행할 수는 없습니다.
* 보안 주체 2는 모든 키, 암호, 인증서를 나열하고 읽을 수 있지만 생성, 삭제 또는 갱신 작업은 수행할 수 없습니다.
* 보안 주체 3은 모든 비밀을 백업 및 복원할 수 있지만 비밀의 값을 읽을 수 없습니다.

그러나 클래식 액세스 정책은 개체별 수준 사용 권한을 허용하지 않으며 할당된 사용 권한은 개별 키 자격 증명 모음의 범위에 적용됩니다. 예를 들어 특정 키 자격 증명 모음의 보안 주체에게 “Secret Get” 액세스 정책 사용 권한을 부여하는 경우 보안 주체는 해당 특정 키 자격 증명 모음 내에서 모든 비밀을 가져올 수 있습니다. 그러나 이 “비밀 가져오기” 권한은 다른 키 자격 증명 모음으로 자동으로 확장되지 않으므로 명시적으로 할당해야 합니다.

> [!IMPORTANT]
> 클래식 키 자격 증명 모음 액세스 정책 및 Azure Active Directory 역할 할당은 서로 독립적입니다. 구독 수준에서 보안 주체에게 ‘Contributor’ 역할을 할당하더라도 구독 범위 내의 모든 키 자격 증명 모음에 대한 데이터 평면 작업을 수행하는 능력이 보안 주체에게 자동으로 부여되지 않습니다. 보안 주체에게는 데이터 평면 작업을 수행할 수 있는 액세스 정책 권한이 부여되거나 스스로가 해당 권한을 부여해야 합니다.

### <a name="data-plane-access-option-2--azure-rbac-for-key-vault-preview"></a>데이터 평면 액세스 옵션 2: Key Vault에 대한 Azure RBAC(미리 보기)

키 자격 증명 모음 데이터 평면에 대한 액세스를 부여하는 새로운 방법은 키 자격 증명 모음에 대한 Azure RBAC(역할 기반 액세스 제어)를 사용하는 것입니다.

> [!NOTE]
> 이 액세스 모델은 앞서 설명한 키 자격 증명 모음 클래식 액세스 정책과 호환되지 않습니다. 하나의 항목을 선택해야 합니다. 키 자격 증명 모음의 액세스 정책 탭을 클릭하면 이 항목을 선택할 수 있습니다.

Key Vault 역할 할당은 Azure 기본 제공 역할 할당 집합으로 키, 암호 및 인증서에 액세스하는 데 사용되는 일반 권한 집합을 포함합니다. 또한 이 권한 모델을 사용하면 클래식 키 자격 증명 모음 액세스 정책 모델에서 사용할 수 없는 추가 기능을 사용할 수 있습니다.

* Azure RBAC 권한은 사용자가 해당 역할을 구독, 리소스 그룹 또는 개별 키 자격 증명 모음 수준에서 할당할 수 있도록 하여 대규모로 관리할 수 있습니다. 사용자는 Azure RBAC 할당 범위 내에서 모든 키 자격 증명 모음에 대한 데이터 평면 권한을 가집니다. 이렇게 하면 키 자격 증명 모음 당 사용자/애플리케이션마다 개별 액세스 정책 권한을 할당할 필요가 없습니다.

* Azure RBAC 권한은 Privileged Identity Management 또는 PIM과 호환됩니다. 이를 통해 Key Vault 관리자와 같은 권한 있는 역할에 대한 Just-In-Time 액세스 제어를 구성할 수 있습니다. 이는 최상의 보안 방법이며 주요 자격 증명 모음에 대한 고정 액세스를 제거하여 최소 권한 원칙을 따릅니다.

Key Vault에 대한 Azure RBAC에 관해 자세히 알아보려면 다음 문서를 참조하세요.

* Key Vault에 대한 Azure RBAC [링크](./secure-your-key-vault.md#management-plane-and-azure-rbac)
* Key Vault 역할에 대한 Azure RBAC [링크](../../role-based-access-control/built-in-roles.md#key-vault-administrator)

## <a name="configure-key-vault-firewall"></a>Key Vault 방화벽 구성

기본적으로 키 자격 증명 모음은 공용 인터넷의 트래픽이 퍼블릭 엔드포인트를 통해 키 자격 증명 모음에 도달하도록 허용합니다. 추가 보안 계층의 경우 키 자격 증명 모음 퍼블릭 엔드포인트에 대한 액세스를 제한하도록 Azure Key Vault 방화벽을 구성할 수 있습니다.

키 자격 증명 모음 방화벽을 사용하도록 설정하려면 키 자격 증명 모음 포털에서 네트워킹 탭을 클릭하고 라디오 단추를 선택하여 “프라이빗 엔드포인트 및 선택한 네트워크”의 액세스를 허용합니다. 키 자격 증명 모음 방화벽을 사용하도록 선택하는 경우, 키 자격 증명 모음 방화벽을 통한 트래픽을 허용하는 방법이 있습니다.

* 키 자격 증명 모음 방화벽 허용 목록에 IPv4 주소를 추가합니다. 이 옵션은 고정 IP 주소를 가진 애플리케이션에 가장 적합합니다.

* 키 자격 증명 모음 방화벽에 가상 네트워크를 추가합니다. 이 옵션은 Virtual Machines와 같은 동적 IP 주소가 있는 Azure 리소스에 가장 적합합니다. Azure 리소스를 가상 네트워크에 추가하고 키 자격 증명 모음 방화벽 허용 목록에 가상 네트워크를 추가할 수 있습니다. 이 옵션은 가상 네트워크 내의 개인 IP 주소인 서비스 엔드포인트를 사용합니다. 이는 추가 보호 계층을 제공하므로 키 자격 증명 모음과 가상 네트워크 간의 트래픽이 공용 인터넷을 통해 라우팅되지 않습니다. 서비스 엔드포인트에 대해 자세히 알아보려면 다음 문서를 참조하세요. [링크](./network-security.md)

* 키 자격 증명 모음에 대한 프라이빗 링크 연결을 추가합니다. 이 옵션은 가상 네트워크를 키 자격 증명 모음의 특정 인스턴스에 직접 연결하여 가상 네트워크 내에 키 자격 증명 모음을 효과적으로 가져옵니다. 키 자격 증명 모음에 대한 프라이빗 엔드포인트 연결을 구성하는 방법에 대한 자세한 내용은 다음 [링크](./private-link-service.md)를 참조하세요.

## <a name="test-your-service-principals-ability-to-access-key-vault"></a>키 자격 증명 모음에 액세스하는 서비스 주체의 기능 테스트

위의 모든 단계를 수행한 후에는 키 자격 증명 모음에서 비밀을 설정하고 검색할 수 있습니다.

### <a name="authentication-process-for-users-examples"></a>사용자를 위한 인증 프로세스(예제)

* 사용자는 Azure Portal에 로그인하여 키 자격 증명 모음을 사용할 수 있습니다. [Key Vault 포털 빠른 시작](./quick-create-portal.md)

* 사용자는 Azure CLI를 사용하여 키 자격 증명 모음을 사용할 수 있습니다. [Key Vault Azure CLI 빠른 시작](./quick-create-cli.md)

* 사용자는 Azure PowerShell을 사용하여 키 자격 증명 모음을 사용할 수 있습니다. [Key Vault Azure PowerShell 빠른 시작](./quick-create-powershell.md)

### <a name="azure-active-directory-authentication-process-for-applications-or-services-examples"></a>애플리케이션 또는 서비스에 대한 Azure Active Directory 인증 프로세스(예제)

* 애플리케이션은 Azure Active Directory 토큰을 가져오기 위해 함수에 클라이언트 암호와 클라이언트 ID를 제공합니다. 

* 애플리케이션은 Azure Active Directory 토큰을 가져오기 위한 인증서를 제공합니다. 

* Azure 리소스는 MSI 인증을 사용하여 Azure Active Directory 토큰을 가져옵니다. 

* MSI 인증에 대한 자세한 정보 [링크](../../active-directory/managed-identities-azure-resources/overview.md)

### <a name="authentication-process-for-application-python-example"></a>애플리케이션에 대한 인증 프로세스(Python 예제)

다음 코드 샘플을 사용하여 애플리케이션에서 구성한 서비스 주체를 사용하여 키 자격 증명 모음에서 비밀을 검색할 수 있는지 여부를 테스트합니다.

>[!NOTE]
>이 샘플은 데모용이며 테스트 목적으로만 사용됩니다. **프로덕션에서 클라이언트 암호 인증 사용 안 함** 이는 보안 설계 방법이 아닙니다. 클라이언트 인증서 또는 MSI 인증을 모범 사례로 사용해야 합니다.

```python
from azure.identity import ClientSecretCredential
from azure.keyvault.secrets import SecretClient

tenant_id = "{ENTER YOUR TENANT ID HERE}"                          ##ENTER AZURE TENANT ID
vault_url = "https://{ENTER YOUR VAULT NAME}.vault.azure.net/"     ##ENTER THE URL OF YOUR KEY VAULT
client_id = "{ENTER YOUR CLIENT ID HERE}"                          ##ENTER THE CLIENT ID OF YOUR SERVICE PRINCIPAL
cert_path = "{ENTER YOUR CLIENT SECRET HERE}"                      ##ENTER THE CLIENT SECRET OF YOUR SERVICE PRINCIPAL

def main():

    #AUTHENTICATION TO Azure Active Directory USING CLIENT ID AND CLIENT CERTIFICATE (GET Azure Active Directory TOKEN)
    token = ClientSecretCredential(tenant_id=tenant_id, client_id=client_id, client_secret=client_secret)

    #AUTHENTICATION TO KEY VAULT PRESENTING Azure Active Directory TOKEN
    client = SecretClient(vault_url=vault_url, credential=token)

    #CALL TO KEY VAULT TO GET SECRET
    #ENTER NAME OF A SECRET STORED IN KEY VAULT
    secret = client.get_secret('{SECRET_NAME}')

    #GET PLAINTEXT OF SECRET
    print(secret.value)

#CALL MAIN()
if __name__ == "__main__":
    main()
```

## <a name="next-steps"></a>다음 단계

주요 자격 증명 모음 인증에 대해 자세히 알아보려면 다음 문서를 참조하세요. [키 자격 증명 모음 인증](./authentication.md)
