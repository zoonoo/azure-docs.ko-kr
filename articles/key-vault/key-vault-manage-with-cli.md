---
title: "CLI를 사용하여 Azure Key Vault 관리 | Microsoft Docs"
description: "이 자습서에서는 CLI를 사용하여 키 자격 증명 모음의 일반 작업을 자동화하는 방법을 설명합니다."
services: key-vault
documentationcenter: 
author: barclayn
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: 66be6e44-684a-411b-802e-884628458ae7
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/14/2017
ms.author: barclayn
ms.openlocfilehash: 94ea95e7f40c8d47dd18422a9c0795655dae365b
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/16/2017
---
# <a name="manage-key-vault-using-cli"></a>CLI를 사용하여 키 자격 증명 모음 관리

이 자습서를 사용하면 Azure Key Vault를 시작하여 Azure에 확정된 컨테이너(자격 증명 모음)를 만드는 데 도움이 됩니다. Azure Key Vault는 암호화 키 및 암호를 저장하고 관리하는 데 사용됩니다. 이 문서에서는 자격 증명 모음을 만들기 위해 Azure 플랫폼 간 명령줄 인터페이스를 사용하는 과정을 설명합니다. 그런 다음 다양한 공통 작업을 수행하기 위해 자격 증명 모음을 사용합니다. 

Azure Key Vault는 대부분 지역에서 사용할 수 있습니다. 자세한 내용은 [키 자격 증명 모음 가격 책정 페이지](https://azure.microsoft.com/pricing/details/key-vault/)를 참조하세요.

**예상 완료 시간:** 20분

> [!NOTE]
> 이 자습서에서는 단계 중 하나에 포함된 Azure 응용 프로그램을 작성하는 방법에 대한 지침을 다루지 않습니다. 이 응용 프로그램 예제는 키 자격 증명 모음에 저장된 키 또는 암호를 사용하도록 응용 프로그램에 권한을 부여하는 방법을 보여주는 데 사용됩니다.
> 이 문서는 플랫폼 간 명령줄 인터페이스를 사용하여 Azure Key Vault를 구성하는 방법에 중점을 둡니다. Azure PowerShell 지침은 [이 자습서](key-vault-get-started.md)를 참조하세요.

Azure Key Vault에 대한 개요는 [Azure Key Vault란?](key-vault-whatis.md)

## <a name="prerequisites"></a>필수 조건

이 자습서를 완료하려면 다음이 필요합니다.

* Microsoft Azure 구독. 아직 구독하지 않은 경우 [무료 평가판](https://azure.microsoft.com/pricing/free-trial)에 등록할 수 있습니다.
* 명령줄 인터페이스 버전 0.9.1 이상. 최신 버전을 설치하고 Azure 구독에 연결하려면 [Azure 플랫폼 간 명령줄 인터페이스 설치 및 구성](../cli-install-nodejs.md)을 참조하세요.
* 이 자습서에서 만드는 키 또는 암호를 사용하여 구성되는 응용 프로그램입니다. 샘플 응용 프로그램은 [Microsoft 다운로드 센터](http://www.microsoft.com/download/details.aspx?id=45343)에서 사용할 수 있습니다. 자세한 내용은 해당 추가 정보 파일을 참조하세요.

## <a name="getting-help-with-azure-cross-platform-command-line-interface"></a>Azure 플랫폼 간 명령줄 인터페이스 도움말 보기

이 자습서에서는 명령줄 인터페이스(Bash, 터미널, 명령 프롬프트)를 잘 알고 있다고 가정합니다.

--help 또는 -h 매개 변수를 사용하여 특정 명령에 대한 도움말을 볼 수 있습니다. 또는 azure help [command] [options] 형식을 사용하여 동일한 정보를 반환할 수도 있습니다. 예를 들어 다음 명령은 모두 동일한 정보를 반환합니다.

    azure account set --help

    azure account set -h

    azure help account set

명령에 필요한 매개 변수가 확실하지 않은 경우 --help, -h 또는 azure help [command]를 사용하여 도움말을 참조하십시오.

다음 자습서를 참고하여 Azure 플랫폼 간 명령줄 인터페이스에서 Azure Resource Manager 배포 모델에 대해 알아볼 수도 있습니다.

* [Azure 플랫폼 간 명령줄 인터페이스를 설치 및 구성하는 방법](../cli-install-nodejs.md)
* [Azure 리소스 관리자와 함께 Azure 플랫폼 간 명령줄 인터페이스 사용](../xplat-cli-azure-resource-manager.md)

## <a name="connect-to-your-subscriptions"></a>구독에 연결

다음 명령을 사용하여 Azure에 로그인하려면:

```azurecli-interactive
azure login -u username -p password
```

또는 대화형으로 입력하여 로그인하려는 경우

```azurecli-interactive
azure login
```

여러 구독이 있는 경우 Azure Key Vault에 사용할 특정 하나를 지정하려면, 다음을 입력하여 계정에 대한 구독을 표시합니다.

```azurecli-interactive
azure account list
```

그런 후, 사용할 구독을 지정하려면 다음을 입력합니다.

```azurecli-interactive
azure account set <subscription name>
```

Azure 플랫폼 간 명령줄 인터페이스 구성에 대한 자세한 내용은 [Azure 플랫폼 간 명령줄 인터페이스를 설치 및 구성하는 방법](../cli-install-nodejs.md)을 참조하세요.

## <a name="switch-to-using-azure-resource-manager"></a>Azure 리소스 관리자로 전환
키 자격 증명 모음에는 Azure 리소스 관리자가 필요로 하므로 다음을 입력하여 Azure 리소스 관리자 모드로 전환합니다.

```azurecli-interactive
azure config mode arm
```

## <a name="create-a-new-resource-group"></a>새 리소스 그룹 만들기
Azure 리소스 관리자를 사용하면 관련된 모든 리소스는 리소스 그룹의 내부에 만들어집니다. 이 자습서에서는 'ContosoResourceGroup'이라는 새 리소스 그룹을 만듭니다.

```azurecli-interactive
azure group create 'ContosoResourceGroup' 'East Asia'
```

첫 번째 매개 변수는 리소스 그룹 이름이고, 두 번째 매개 변수 위치입니다. 위치의 경우, `azure location list` 명령을 사용하여 이 예에서 대체 위치를 지정하는 방법을 식별합니다. 자세한 정보가 필요한 경우 `azure help location`

## <a name="register-the-key-vault-resource-provider"></a>키 자격 증명 모음 리소스 공급자 등록
키 자격 증명 모음 리소스 공급자가 구독에 등록되어 있는지 확인합니다.

```azurecli-interactive
azure provider register Microsoft.KeyVault
```

구독당 한 번만 수행하면 됩니다.

## <a name="create-a-key-vault"></a>키 자격 증명 모음 만들기

`azure keyvault create` 명령을 사용하여 키 자격 증명 모음을 만듭니다. 이 스크립트에는 3개의 필수 매개 변수, 리소스 그룹 이름, 키 자격 증명 모음 이름 및 지리적 위치가 있습니다.

예:
- **ContosoKeyVault**의 자격 증명 모음 이름을 사용하는 경우
- **ContosoResourceGroup**의 리소스 그룹 이름
- **동아시아** 형식의 위치:

```azurecli-interactive
azure keyvault create --vault-name 'ContosoKeyVault' --resource-group 'ContosoResourceGroup' --location 'East Asia'
```

이 명령의 출력에서는 방금 만든 자격 증명 모음의 속성을 보여 줍니다. 가장 중요한 두 개의 속성은 다음과 같습니다.

* **Name**: ContosoKeyVault이는 예제에서입니다. 다른 키 자격 증명 모음 cmdlet에 대해 이 이름을 사용합니다.
* **vaultUri**: 예제에서는 https://contosokeyvault.vault.azure.net입니다. REST API를 통해 사용자 자격 증명 모음을 사용하는 응용 프로그램은 URI를 사용해야 합니다.

Azure 계정은 이제 이 키 자격 증명 모음에서 모든 작업을 수행할 권한을 가지게 됩니다. 아직 다른 사람은 권한이 없습니다.

## <a name="add-a-key-or-secret-to-the-key-vault"></a>키 또는 암호를 키 자격 증명 모음에 추가

소프트웨어 보호 키를 만들도록 Azure Key Vault를 사용하려면, `azure key create` 명령을 사용하여 다음을 입력합니다.

```azurecli-interactive
azure keyvault key create --vault-name 'ContosoKeyVault' --key-name 'ContosoFirstKey' --destination software
````

그러나 Azure Key Vault에 업로드하려는 softkey.pem이라는 파일에 로컬 파일로 저장된 .pem 파일의 기존 키가 있는 경우 다음을 입력하여 Key Vault 서비스에서 소프트웨어로 키를 보호하는 .PEM 파일에서 키를 가져옵니다.

```azurecli-interactive
azure keyvault key import --vault-name 'ContosoKeyVault' --key-name 'ContosoFirstKey' --pem-file './softkey.pem' --password 'PaSSWORD' --destination software
```

이제 해당 URI를 사용하여 Azure Key Vault를 만들거나 업로드하는 이 키를 참조할 수 있습니다. **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey**를 사용하여 현재 버전을 가져오고 **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey/cgacf4f763ar42ffb0a1gca546aygd87**을 사용하여 이 특정 버전을 가져옵니다.

자격 증명 모음에 암호를 추가하려면 다음을 입력합니다. 암호는 SQLPassword이며 Azure Key Vault에 대한 Pa$$w0rd 값을 가집니다.

```azurecli-interactive
azure keyvault secret set --vault-name 'ContosoKeyVault' --secret-name 'SQLPassword' --value 'Pa$$w0rd'
```

이제 해당 URI를 사용하여 Azure Key Vault에 추가한 이 암호를 참조할 수 있습니다. **https://ContosoVault.vault.azure.net/secrets/SQLPassword**를 사용하여 현재 버전을 가져오고 **https://ContosoVault.vault.azure.net/secrets/SQLPassword/90018dbb96a84117a0d2847ef8e7189d**를 사용하여 이 특정 버전을 가져옵니다.

방금 만든 키 또는 암호를 확인해 보겠습니다.

* 사용자의 키를 보려면 다음을 입력 합니다. `azure keyvault key list --vault-name 'ContosoKeyVault'`
* 사용자의 비밀을 보려면 다음을 입력 합니다. `azure keyvault secret list --vault-name 'ContosoKeyVault'`

## <a name="register-an-application-with-azure-active-directory"></a>Azure Active Directory에 응용 프로그램 등록

이 단계는 일반적으로 별도의 컴퓨터에서 개발자가 수행할 수 있습니다. Azure Key Vault에 특정한 것은 아니지만 완전성을 위해 포함됩니다.

> [!IMPORTANT]
> 이 단계에서 등록된 자습서, 계정, 해당 자격 증명 모음 및 응용 프로그램을 완료하려면, 모두 동일한 Azure Active Directory에 있어야 합니다.

자격 증명 모음 키를 사용하는 응용 프로그램은 Azure Active Directory에서 토큰을 사용하여 인증해야 합니다. 이렇게 하려면 응용 프로그램의 소유자가 Azure Active Directory에 먼저 응용 프로그램을 등록해야 합니다. 등록 끝에 응용 프로그램 소유자는 다음 값을 가져옵니다.

- **응용 프로그램 ID** 
- **인증 키**(또한 공유 암호라고도 함) 

응용 프로그램은 토큰을 가져올 Azure Active Directory에 이 두 값 모두가 있어야 합니다. 응용 프로그램 구성 방식은 응용 프로그램에 따라 달라집니다. [Key Vault 샘플 응용 프로그램](https://www.microsoft.com/download/details.aspx?id=45343)의 경우, 응용 프로그램 소유자는 app.config 파일에서 이러한 값을 설정합니다.

Azure Active Directory를 사용하여 응용 프로그램을 등록하는 자세한 단계를 알아보려면 [Azure Active Directory를 사용하여 응용 프로그램 통합](../active-directory/develop/active-directory-integrating-applications.md) 또는 [포털을 사용하여 리소스에 액세스할 수 있는 Active Directory 응용 프로그램 및 서비스 주체 만들기](../azure-resource-manager/resource-group-create-service-principal-portal.md)라는 문서를 검토해야 합니다. Azure Active Directory에서 응용 프로그램을 등록하려면:

1. [Azure 포털](https://portal.azure.com)에 로그인합니다.
2. 왼쪽에서 **앱 등록**을 클릭합니다. 앱 등록을 볼 수 없다면 **더 많은 서비스**를 클릭하여 찾습니다.  
    >[!NOTE]
    주요 자격 증명 모음을 만든 Azure 구독을 포함하는 동일한 디렉터리를 선택해야 합니다. 
3. **새 응용 프로그램 등록**을 클릭합니다.
4. **만들기** 블레이드에 응용 프로그램의 이름을 제공한 다음 **웹 응용 프로그램 및/또는 Web API**(기본값)를 선택하고 웹 응용 프로그램에 대한 **로그온 URL**을 지정합니다. 현재 이 정보가 없다면 이 설정에 대해 만들 수 있습니다.(예를 들어 http://test1.contoso.com를 지정할 수 있습니다.) 이러한 사이트가 존재하는 경우 중요하지 않습니다. 

   ![새 응용 프로그램 등록](./media/key-vault-manage-with-cli/new-application-registration.png)

5. **만들기** 단추를 클릭합니다.
6. 앱 등록을 완료하면 등록된 앱 목록을 볼 수 있습니다. 방금 등록한 앱을 찾아 클릭합니다.
7. **등록된 앱** 블레이드를 클릭하고 **응용 프로그램 ID**를 복사합니다.
8. **모든 설정**을 클릭합니다.
9. **설정** 블레이드에서 **키**를 클릭합니다.
10. **키 설명** 상자에 설명을 입력한 다음 기간을 선택하고 **저장**을 클릭합니다. 페이지가 새로 고쳐지고 이제 키 값을 표시합니다. 
11. 자격 증명 모음에 사용 권한을 설정하려면 다음 단계에서 **응용 프로그램 ID** 및 **키** 정보를 사용합니다.

## <a name="authorize-the-application-to-use-the-key-or-secret"></a>응용 프로그램에 키 또는 암호를 사용하도록 권한 부여
응용 프로그램이 자격 증명 모음의 키 또는 암호에 액세스하도록 권한을 부여하려면 다음을 사용합니다.

```azurecli-interactive
azure keyvault set-policy
```

예를 들어, 자격 증명 모음 이름은 ContosoKeyVault이고 권한을 부여하려는 응용 프로그램에 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed의 클라이언트 ID가 있고 자격 증명 모음에 있는 키로 서명하고 암호 해제하도록 응용 프로그램을 인증하려면, 다음을 실행합니다.

```azurecli-interactive
azure keyvault set-policy --vault-name 'ContosoKeyVault' --spn 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed --perms-to-keys '[\"decrypt\",\"sign\"]'
```

> [!NOTE]
> Windows 명령 프롬프트에서 실행하는 경우 작은따옴표를 큰따옴표로 바꾸고 내부 큰따옴표를 이스케이프해야 예: "[\"해독\",\"기호\"]"
> 

자격 증명 모음에서 기밀 정보를 읽기 위해 동일한 응용 프로그램에 권한을 부여하려면 다음을 실행합니다.

```azurecli-interactive
azure keyvault set-policy --vault-name 'ContosoKeyVault' --spn 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed --perms-to-secrets '[\"get\"]'
```

## <a name="if-you-want-to-use-a-hardware-security-module-hsm"></a>하드웨어 보안 모듈(HSM)을 사용하려는 경우
추가된 보증을 위해, HSM 경계를 절대로 떠나지 않는 하드웨어 보안 모듈(HSMs)에서 키를 가져오거나 생성할 수 있습니다. HSM은 FIPS 140-2 Level 2 유효성 검사가 적용됩니다. 이 요구 사항이 사용자에게 적용되지 않는 경우, 이 섹션을 건너뛰고 [키 자격 증명 모음 및 연결된 키와 암호 삭제](#delete-the-key-vault-and-associated-keys-and-secrets)로 이동합니다.

이러한 HSM 보호되는 키를 만들려면, HSM 보호되는 키를 지원하는 자격 증명 모음 구독이 있어야 합니다.

keyvault를 만들 때 'sku' 매개 변수를 추가합니다.

```azurecli-interactive
azure azure keyvault create --vault-name 'ContosoKeyVaultHSM' --resource-group 'ContosoResourceGroup' --location 'East Asia' --sku 'Premium'
```

이 자격 증명이 모음에 소프트웨어 보호 키(이전에 표시된 대로)와 HSM 보호되는 키를 추가할 수 있습니다. HSM으로 보호되는 키를 만들려면, 대상 매개 변수를 'HSM'으로 설정합니다.

```azurecli-interactive
azure keyvault key create --vault-name 'ContosoKeyVaultHSM' --key-name 'ContosoFirstHSMKey' --destination 'HSM'
```

다음 명령을 사용하여 사용자 컴퓨터의 .pem 파일에서 키를 가져올 수 있습니다. 이 명령은 키 자격 증명 모음 서비스에서 HSM으로 키를 가져옵니다.

```azurecli-interactive
azure keyvault key import --vault-name 'ContosoKeyVaultHSM' --key-name 'ContosoFirstHSMKey' --pem-file '/.softkey.pem' --destination 'HSM' --password 'PaSSWORD'
```

다음 명령은 "bring your own key"(BYOK) 패키지를 가져옵니다. 이렇게 하면 로컬 HSM에 키를 생성하고 키가 HSM 경계를 벗어나지 않고 이를 키 자격 증명 모음 서비스의 HSM에 전달합니다.

```azurecli-interactive
azure keyvault key import --vault-name 'ContosoKeyVaultHSM' --key-name 'ContosoFirstHSMKey' --byok-file './ITByok.byok' --destination 'HSM'
```

이 BYOK 패키지를 생성하는 방법에 대한 자세한 내용은 [Azure Key Vault와 HSM-보호된 키를 사용하는 방법](key-vault-hsm-protected-keys.md)을 참조하세요.

## <a name="delete-the-key-vault-and-associated-keys-and-secrets"></a>키 자격 증명 모음 및 연결된 키와 암호 삭제
키 자격 증명 모음 및 이를 포함하는 키나 비밀이 더 이상 필요하지 않은 경우, azure keyvault delete 명령을 사용하여 키 자격 증명 모음을 삭제할 수 있습니다.

```azurecli-interactive
azure keyvault delete --vault-name 'ContosoKeyVault'
```

또는 키 자격 증명 모음 및 해당 그룹에 포함된 다른 모든 리소스 및 키를 포함하는 전체 Azure 리소스 그룹을 삭제할 수 있습니다.

```azurecli-interactive
azure group delete --name 'ContosoResourceGroup'
```


## <a name="other-azure-cross-platform-command-line-interface-commands"></a>다른 Azure 플랫폼 간 명령줄 인터페이스 명령
Azure Key Vault를 관리하는 데 유용할 수 있는 다른 명령입니다.

이 명령은 모든 키와 선택한 속성을 테이블 형식으로 나열합니다.

```azurecli-interactive
azure keyvault key list --vault-name 'ContosoKeyVault'
```

이 명령은 지정된 키에 대한 속성의 전체 목록을 표시합니다.

```azurecli-interactive
azure keyvault key show --vault-name 'ContosoKeyVault' --key-name 'ContosoFirstKey'
```

이 명령은 모든 비밀 이름과 선택한 속성을 테이블 형식으로 표시합니다.

```azurecli-interactive
azure keyvault secret list --vault-name 'ContosoKeyVault'
```

다음은 특정 키를 제거하는 방법에 대한 예제입니다.

```azurecli-interactive
azure keyvault key delete --vault-name 'ContosoKeyVault' --key-name 'ContosoFirstKey'
```

다음은 특정 암호를 제거하는 방법에 대한 예제입니다.

```azurecli-interactive
azure keyvault secret delete --vault-name 'ContosoKeyVault' --secret-name 'SQLPassword'
```


## <a name="next-steps"></a>다음 단계
- 프로그래밍 참조는 [Azure Key Vault 개발자 가이드](key-vault-developers-guide.md)를 참조하세요.
- Azure Key Vault에 대한 개요는 [Azure Key Vault란?](key-vault-whatis.md)
- PowerShell을 사용하여 Azure Key Vault를 만드는 방법 [Azure Key Vault 시작](key-vault-get-started.md)


