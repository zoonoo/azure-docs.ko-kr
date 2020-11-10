---
title: Azure Key Vault 인증 기본 사항
description: 키 자격 증명 모음 인증 모델의 작동 방식에 대해 알아보기
author: ShaneBala-keyvault
ms.author: sudbalas
ms.date: 09/25/2020
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.openlocfilehash: f7f9acd18da57bd83e688249600b8468cc4ebbe5
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94445560"
---
# <a name="key-vault-authentication-fundamentals"></a>Key Vault 인증 기본 사항

Azure Key Vault를 사용 하면 중앙 및 보안 클라우드 리포지토리에서 비밀, 키 및 인증서와 같은 응용 프로그램 자격 증명을 안전 하 게 저장 하 고 관리할 수 있습니다. Key Vault를 통해 응용 프로그램에 자격 증명을 저장할 필요가 없습니다. 응용 프로그램은 런타임에 Key Vault에 인증 하 여 자격 증명을 검색할 수 있습니다.

관리자는 키 자격 증명 모음에 액세스할 수 있는 사용자 및 응용 프로그램을 긴밀 하 게 제어할 수 있으며, 수행 하는 작업을 제한 하 고 감사할 수 있습니다. 이 문서에서는 주요 자격 증명 모음 액세스 모델의 기본 개념을 설명 합니다. 이를 통해 소개 수준의 정보를 제공 하 고, 시작부터 끝까지 주요 자격 증명 모음에 사용자 또는 응용 프로그램을 인증할 수 있는 방법을 보여 줍니다.

## <a name="required-knowledge"></a>필요한 정보

이 문서에서는 다음 개념을 잘 알고 있다고 가정 합니다. 이러한 개념에 익숙하지 않은 경우 계속 하기 전에 도움말 링크를 따르세요.

* Azure Active Directory [링크](../../active-directory/fundamentals/active-directory-whatis.md)
* 보안 주체 [링크](./authentication.md#app-identity-and-security-principals)

## <a name="key-vault-configuration-steps-summary"></a>Key Vault 구성 단계 요약

1. Azure Active Directory에서 사용자 또는 응용 프로그램을 보안 주체로 등록 합니다.
1. Azure Active Directory에서 보안 주체에 대 한 역할 할당을 구성 합니다.
1. 보안 주체에 대 한 key vault 액세스 정책을 구성 합니다.
1. 키 자격 증명 모음에 대 한 Key Vault 방화벽 액세스를 구성 합니다 (선택 사항).
1. 키 자격 증명 모음에 액세스 하는 보안 주체의 기능을 테스트 합니다.

## <a name="register-a-user-or-application-in-azure-active-directory-as-a-security-principal"></a>Azure Active Directory의 사용자 또는 응용 프로그램을 보안 주체로 등록

사용자 또는 응용 프로그램이 key vault에 대 한 요청을 수행 하는 경우 먼저 Azure Active Directory에서 요청을 인증 해야 합니다. 이 작업을 수행 하려면 사용자 또는 응용 프로그램을 보안 주체로 Azure Active Directory에 등록 해야 합니다.

Azure Active Directory에서 사용자 또는 응용 프로그램을 등록 하는 방법을 이해 하려면 아래 설명서 링크를 따르세요.
**응용 프로그램에 대 한 사용자 등록 및 클라이언트 인증서 자격 증명에 대 한 암호를 만들어야 합니다.**

* Azure Active Directory [링크](../../active-directory/fundamentals/add-users-azure-active-directory.md) 에 사용자 등록
* Azure Active Directory [링크](../../active-directory/develop/quickstart-register-app.md) 에 응용 프로그램 등록

## <a name="assign-your-security-principal-a-role"></a>보안 주체에 역할 할당

Azure RBAC (역할 기반 액세스 제어)를 사용 하 여 보안 주체에 권한을 할당할 수 있습니다. 이러한 사용 권한을 역할 할당 이라고 합니다.

키 자격 증명 모음의 컨텍스트에서 이러한 역할 할당은 키 자격 증명 모음의 관리 평면 (제어 평면이 라고도 함)에 대 한 보안 주체의 액세스 수준을 결정 합니다. 이러한 역할 할당은 데이터 평면 비밀에 직접 액세스 하는 것을 제공 하지 않지만 key vault의 속성을 관리 하는 액세스 권한을 제공 합니다. 예를 들어 **독자 역할이** 할당 된 사용자 또는 응용 프로그램은 주요 자격 증명 모음 방화벽 설정을 변경할 수 없으며, **참가자 역할이** 할당 된 사용자 또는 응용 프로그램은 변경 작업을 수행할 수 있습니다. 두 역할 모두 키 자격 증명 모음 데이터 평면에 대 한 액세스 권한이 할당 될 때까지 해당 값을 만들거나 검색 하는 등의 방법으로 암호, 키 및 인증서에 대 한 작업을 수행할 수 없습니다. 이에 대해서는 다음 단계에서 설명 합니다.

>[!IMPORTANT]
> 참가자 또는 소유자 역할을 가진 사용자는 기본적으로 키 자격 증명 모음에 저장 된 암호에 대 한 작업을 수행할 수 있는 권한이 없지만, 참가자 및 소유자 역할은 키 자격 증명 모음에 저장 된 암호에 대 한 액세스 정책을 추가 하거나 제거할 수 있는 권한을 제공 합니다. 따라서 이러한 역할 할당을 가진 사용자는 키 자격 증명 모음의 암호에 액세스할 수 있는 권한을 자신에 게 부여할 수 있습니다. 따라서 관리자만 참가자 또는 소유자 역할에 액세스할 수 있도록 하는 것이 좋습니다. 키 자격 증명 모음에서 비밀을 검색 해야 하는 사용자와 응용 프로그램에는 읽기 권한자 역할을 부여 해야 합니다. **자세한 내용은 다음 섹션을 참조 하세요.**

>[!NOTE]
> Azure Active Directory 테 넌 트 수준에서 사용자에 게 역할 할당을 할당 하는 경우이 권한 집합은 할당 범위 내에 있는 모든 구독, 리소스 그룹 및 리소스에 trickle 됩니다. 최소 권한 보안 주체를 따르려면 보다 세분화 된 범위에서이 역할 할당을 수행할 수 있습니다. 예를 들어 사용자에 게 구독 수준에서 읽기 권한자 역할을 할당 하 고 단일 키 자격 증명 모음에 대 한 소유자 역할을 할당할 수 있습니다. 구독, 리소스 그룹 또는 주요 자격 증명 모음의 IAM (Id 액세스 관리) 설정으로 이동 하 여 역할 할당을 보다 세분화 된 범위에서 만듭니다.

* Azure 역할 [링크](../../role-based-access-control/built-in-roles.md) 에 대 한 자세한 내용은
* 역할 [할당 할당](../../role-based-access-control/role-assignments-portal.md) 또는 제거에 대 한 자세한 내용은

## <a name="configure-key-vault-access-policies-for-your-security-principal"></a>보안 주체에 대 한 key vault 액세스 정책 구성

키 자격 증명 모음에 액세스 하기 위해 사용자 및 응용 프로그램에 대 한 액세스 권한을 부여 하기 전에 주요 자격 증명 모음에서 수행할 수 있는 다양 한 유형의 작업을 이해 하는 것이 중요 합니다. 주요 자격 증명 모음 작업에는 두 가지 주요 유형인 관리 평면 (제어 평면이 라고도 함) 및 데이터 평면 작업이 있습니다.

다음 표에서는 관리 평면 및 데이터 평면에 의해 제어 되는 다양 한 작업의 몇 가지 예를 보여 줍니다. Key vault의 속성을 변경 하는 작업은 관리 평면 작업입니다. 키 자격 증명 모음에 저장 된 암호의 값을 변경 하거나 검색 하는 작업은 데이터 평면 작업입니다.

|관리 평면 작업 (예제)|데이터 평면 작업 (예제)|
| --- | --- |
| 주요 자격 증명 모음 만들기 | 키, 암호, 인증서 만들기
| Key Vault 삭제 | 키, 암호, 인증서를 삭제 합니다.
| 역할 할당 Key Vault 추가 또는 제거 | 키, 암호, 인증서의 값 나열 및 가져오기
| Key Vault 액세스 정책 추가 또는 제거 | 키, 암호, 인증서 백업 및 복원
| Key Vault 방화벽 설정 수정 | 키, 암호, 인증서 갱신
| Key Vault 복구 설정 수정 | 일시 삭제 된 키, 암호, 인증서 제거 또는 복구
| Key Vault 진단 로그 설정 수정

### <a name="management-plane-access--azure-active-directory-role-assignments"></a>관리 평면 액세스 & Azure Active Directory 역할 할당

역할 할당 Azure Active Directory 키 자격 증명 모음에 대 한 관리 평면 작업을 수행할 수 있는 액세스 권한을 부여 합니다. 이 액세스 권한은 일반적으로 응용 프로그램이 아닌 사용자에 게 부여 됩니다. 사용자의 역할 할당을 변경 하 여 사용자가 수행할 수 있는 관리 평면 작업을 제한할 수 있습니다.

예를 들어 사용자에 게 사용자에 게 Key Vault 읽기 권한자 역할을 할당 하면 액세스 정책 등의 주요 자격 증명 모음에 대 한 속성을 볼 수 있지만 변경할 수는 없습니다. 사용자를 할당 하면 소유자 역할은 키 자격 증명 모음 관리 평면 설정 변경에 대 한 모든 권한을 허용 합니다.

역할 할당은 키 자격 증명 모음 Access Control (IAM) 블레이드에서 제어 됩니다. 특정 사용자가 읽기 권한자 이거나 여러 주요 자격 증명 모음 리소스의 관리자가 될 수 있도록 하려면 자격 증명 모음, 리소스 그룹 또는 구독 수준에서 역할 할당을 만들 수 있으며, 역할 할당은 할당 범위 내의 모든 리소스에 추가 됩니다.

키 자격 증명 모음에 저장 된 키, 암호 및 인증서에 대 한 작업을 수행 하려면 데이터 평면 액세스 또는 다음 두 가지 방법 중 하나로 추가할 수 있습니다.

### <a name="data-plane-access-option-1-classic-key-vault-access-policies"></a>데이터 평면 액세스 옵션 1: 클래식 Key Vault 액세스 정책

키 자격 증명 모음 액세스 정책은 사용자 및 응용 프로그램에 키 자격 증명 모음에 대 한 데이터 평면 작업을 수행할 수 있는 권한을 부여 합니다.

> [!NOTE]
> 이 액세스 모델은 아래에 설명 된 key vault에 대 한 Azure RBAC (옵션 2)와 호환 되지 않습니다. 하나를 선택 해야 합니다. 키 자격 증명 모음의 액세스 정책 탭을 클릭 하면이 항목을 선택할 수 있습니다.

클래식 액세스 정책은 세분화 되어 있으며,이는 각 개별 사용자 또는 응용 프로그램이 키 자격 증명 모음 내에서 개별 작업을 수행 하는 기능을 허용 하거나 거부할 수 있음을 의미 합니다. 다음은 몇 가지 예입니다.

* 보안 주체 1은 모든 키 작업을 수행할 수 있지만 비밀 또는 인증서 작업을 수행할 수는 없습니다.
* 보안 주체 2는 모든 키, 암호 및 인증서를 나열 하 고 읽을 수 있지만 생성, 삭제 또는 갱신 작업은 수행할 수 없습니다.
* 보안 주체 3은 모든 비밀을 백업 및 복원할 수 있지만 비밀의 값을 읽을 수 없습니다.

그러나 클래식 액세스 정책은 개체별 수준 사용 권한을 허용 하지 않으며 할당 된 사용 권한은 개별 key vault의 범위에 적용 됩니다. 예를 들어 특정 키 자격 증명 모음에서 보안 주체에 게 "Secret Get" 액세스 정책 사용 권한을 부여 하는 경우 보안 주체는 해당 특정 키 자격 증명 모음 내에서 모든 암호를 가져올 수 있습니다. 그러나이 "비밀 가져오기" 권한은 다른 키 자격 증명 모음으로 자동으로 확장 되지 않으므로 명시적으로 할당 해야 합니다.

> [!IMPORTANT]
> 클래식 키 자격 증명 모음 액세스 정책 및 Azure Active Directory 역할 할당은 서로 독립적입니다. 보안 주체를 구독 수준에서 ' 참가자 ' 역할로 할당 하면 보안 주체가 구독 범위 내의 모든 key vault에서 데이터 평면 작업을 수행 하는 기능을 자동으로 허용 하지 않습니다. 데이터 평면 작업을 수행할 수 있는 보안 주체를 계속 부여 하거나 자신에 게 액세스 정책 권한을 부여 해야 합니다.

### <a name="data-plane-access-option-2--azure-rbac-for-key-vault-preview"></a>데이터 평면 액세스 옵션 2: Key Vault에 대 한 Azure RBAC (미리 보기)

키 자격 증명 모음에 대 한 액세스 권한을 부여 하는 새로운 방법은 키 자격 증명 모음에 대 한 Azure RBAC (역할 기반 액세스 제어)를 사용 하는 것입니다.

> [!NOTE]
> 이 액세스 모델은 위에 표시 된 key vault 클래식 액세스 정책과 호환 되지 않습니다. 하나를 선택 해야 합니다. 키 자격 증명 모음의 액세스 정책 탭을 클릭 하면이 항목을 선택할 수 있습니다.

역할 할당 Key Vault 키, 암호 및 인증서에 액세스 하는 데 사용 되는 일반 권한 집합을 포함 하는 Azure 기본 제공 역할 할당 집합입니다. 또한이 권한 모델을 사용 하면 클래식 키 자격 증명 모음 액세스 정책 모델에서 사용할 수 없는 추가 기능을 사용할 수 있습니다.

* 사용자가 구독, 리소스 그룹 또는 개별 키 자격 증명 모음 수준에서 이러한 역할을 할당할 수 있도록 허용 하 여 Azure RBAC 사용 권한을 대규모로 관리할 수 있습니다. 사용자에 게는 Azure RBAC 할당 범위 내에서 모든 key vault에 대 한 데이터 평면 권한이 있습니다. 이렇게 하면 키 자격 증명 모음 당 사용자/응용 프로그램 마다 개별 액세스 정책 권한을 할당할 필요가 없습니다.

* Azure RBAC 권한은 Privileged Identity Management 또는 PIM과 호환 됩니다. 이를 통해 Key Vault 관리자와 같은 권한 있는 역할에 대 한 just-in-time 액세스 제어를 구성할 수 있습니다. 이는 최상의 보안 방법 이며 주요 자격 증명 모음에 대 한 액세스를 제거 하 여 최소 권한 보안 주체를 따릅니다.

* Azure RBAC 권한은 개체별 세부적인 권한과 호환 되므로 일부 주요 자격 증명 모음 개체에 대 한 작업만 수행 하도록 사용자를 제한할 수 있습니다. 이렇게 하면 여러 응용 프로그램에서 응용 프로그램 간의 액세스를 격리 하면서 단일 키 자격 증명 모음을 공유할 수 있습니다.

Key Vault에 대 한 Azure RBAC에 대해 자세히 알아보려면 다음 문서를 참조 하세요.

* Key Vault에 대 한 Azure RBAC [링크](./secure-your-key-vault.md#management-plane-and-azure-rbac)
* Key Vault 역할 (미리 보기) [링크](../../role-based-access-control/built-in-roles.md#key-vault-administrator-preview) 에 대 한 Azure RBAC

## <a name="configure-key-vault-firewall"></a>Key Vault 방화벽 구성

기본적으로 key vault는 공용 인터넷의 트래픽이 공용 끝점을 통해 키 자격 증명 모음에 도달 하도록 허용 합니다. 추가 보안 계층의 경우 키 자격 증명 모음 공용 끝점에 대 한 액세스를 제한 하도록 Azure Key Vault 방화벽을 구성할 수 있습니다.

키 자격 증명 모음 방화벽을 사용 하도록 설정 하려면 키 자격 증명 모음 포털에서 네트워킹 탭을 클릭 하 고 라디오 단추를 선택 하 여 "개인 끝점 및 선택한 네트워크"의 액세스를 허용 합니다. 키 자격 증명 모음 방화벽을 사용 하도록 선택 하는 경우 키 자격 증명 모음 방화벽을 통한 트래픽을 허용 하는 방법이 있습니다.

* 키 자격 증명 모음 방화벽 허용 목록에 IPv4 주소를 추가 합니다. 이 옵션은 고정 IP 주소를 가진 응용 프로그램에 가장 적합 합니다.

* 키 자격 증명 모음 방화벽에 가상 네트워크를 추가 합니다. 이 옵션은 Virtual Machines와 같은 동적 IP 주소가 있는 Azure 리소스에 가장 적합 합니다. Azure 리소스를 가상 네트워크에 추가 하 고 키 자격 증명 모음 방화벽 허용 목록에 가상 네트워크를 추가할 수 있습니다. 이 옵션은 가상 네트워크 내의 개인 IP 주소인 서비스 끝점을 사용 합니다. 이는 추가 보호 계층을 제공 하므로 key vault와 가상 네트워크 간의 트래픽이 공용 인터넷을 통해 라우팅되지 않습니다. 서비스 끝점에 대해 자세히 알아보려면 다음 설명서를 참조 하세요. [링크나](./network-security.md)

* 키 자격 증명 모음에 개인 링크 연결을 추가 합니다. 이 옵션은 가상 네트워크를 key vault의 특정 인스턴스에 직접 연결 하 여 키 자격 증명 모음을 가상 네트워크 내에 효과적으로 제공 합니다. Key vault에 대 한 개인 끝점 연결을 구성 하는 방법에 대 한 자세한 내용은 다음 [링크](./private-link-service.md) 를 참조 하세요.

## <a name="test-your-service-principals-ability-to-access-key-vault"></a>키 자격 증명 모음에 액세스 하는 서비스 주체의 기능 테스트

위의 모든 단계를 수행한 후에는 key vault에서 비밀을 설정 하 고 검색할 수 있습니다.

### <a name="authentication-process-for-users-examples"></a>사용자에 대 한 인증 프로세스 (예제)

* 사용자는 Azure Portal에 로그인 하 여 key vault를 사용할 수 있습니다. [Key Vault 포털 빠른 시작](./quick-create-portal.md)

* 사용자는 Azure CLI를 사용 하 여 key vault를 사용할 수 있습니다. [Key Vault Azure CLI 빠른 시작](./quick-create-cli.md)

* 사용자는 Azure PowerShell를 사용 하 여 key vault를 사용할 수 있습니다. [Key Vault Azure PowerShell 빠른 시작](./quick-create-powershell.md)

### <a name="azure-active-directory-authentication-process-for-applications-or-services-examples"></a>응용 프로그램 또는 서비스에 대 한 Azure Active Directory 인증 프로세스 (예제)

* 응용 프로그램은 Azure Active Directory 토큰을 가져오기 위해 함수에 클라이언트 암호와 클라이언트 ID를 제공 합니다. 

* 응용 프로그램은 Azure Active Directory 토큰을 가져오기 위한 인증서를 제공 합니다. 

* Azure 리소스는 MSI 인증을 사용 하 여 Azure Active Directory 토큰을 가져옵니다. 

* MSI 인증 [링크](../../active-directory/managed-identities-azure-resources/overview.md) 에 대 한 자세한 정보

### <a name="authentication-process-for-application-python-example"></a>응용 프로그램에 대 한 인증 프로세스 (Python 예제)

다음 코드 샘플을 사용하여 애플리케이션에서 구성한 서비스 주체를 사용하여 키 자격 증명 모음에서 비밀을 검색할 수 있는지 여부를 테스트합니다.

>[!NOTE]
>이 샘플은 데모용 이며 테스트 목적 으로만 사용 됩니다. **프로덕션 환경에서 클라이언트 암호 인증 사용 안 함** 이는 보안 설계 방법이 아닙니다. 클라이언트 인증서 또는 MSI 인증을 사용 하는 것이 가장 좋습니다.

```python
from azure.identity import ClientSecretCredential
from azure.keyvault.secrets import SecretClient

tenant_id = "{ENTER YOUR TENANT ID HERE}"                          ##ENTER AZURE TENANT ID
vault_url = "https://{ENTER YOUR VAULT NAME}.vault.azure.net/"     ##ENTER THE URL OF YOUR KEY VAULT
client_id = "{ENTER YOUR CLIENT ID HERE}"                          ##ENTER THE CLIENT ID OF YOUR SERVICE PRINCIPAL
cert_path = "{ENTER YOUR CLIEND SECRET HERE}"                      ##ENTER THE CLIENT SECRET OF YOUR SERVICE PRINCIPAL

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

주요 자격 증명 모음 인증에 대해 자세히 알아보려면 다음 문서를 참조 하세요. [키 자격 증명 모음 인증](./authentication.md)