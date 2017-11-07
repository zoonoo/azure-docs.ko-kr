---
title: "CLI를 사용하여 Azure Key Vault 관리 | Microsoft Docs"
description: "이 자습서에서는 CLI를 사용하여 키 자격 증명 모음의 일반 작업을 자동화하는 방법을 설명합니다."
services: key-vault
documentationcenter: 
author: BrucePerlerMS
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: 66be6e44-684a-411b-802e-884628458ae7
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2017
ms.author: bruceper
ms.openlocfilehash: c2565a742ce4f6ab5f7639a54c4a475f00cbc260
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="manage-key-vault-using-cli"></a>CLI를 사용하여 키 자격 증명 모음 관리

Azure 키 자격 증명 모음은 대부분 지역에서 사용할 수 있습니다. 자세한 내용은 [키 자격 증명 모음 가격 책정 페이지](https://azure.microsoft.com/pricing/details/key-vault/)를 참조하세요.

## <a name="introduction"></a>소개

이 자습서를 사용하면 Azure 키 자격 증명 모음으로 시작하여 확정된 컨테이너(자격 증명 모음)를 Azure에 만들고 Azure에서 암호화 키와 비밀을 저장하고 관리하는 데 도움이 됩니다. Azure 플랫폼 간 명령줄 인터페이스를 사용하여 Azure 응용 프로그램과 함께 사용할 수 있는 키 또는 암호를 포함하는 자격 증명 모음을 만드는 과정을 안내합니다. 응용 프로그램이 수 해당 키 또는 암호를 사용할 수 있는 방법을 나타냅니다.

**예상 완료 시간:** 20분

> [!NOTE]
> 이 자습서에는 단계 중 하나에 포함되는 Azure 응용 프로그램을 작성하는 방법에 대한 지침을 포함하지 않으며, 키 자격 증명 모음에서 키나 비밀을 사용하도록 응용 프로그램을 인증하는 방법을 나타냅니다.
> 
> 현재는 Azure 포털에서 Azure 키 자격 증명 모음을 구성할 수 없습니다. 대신 이러한 플랫폼 간 명령줄 인터페이스 지침을 사용합니다. 또는 Azure PowerShell 지침의 경우 [이 자습서](key-vault-get-started.md)를 참조합니다.
> 
> 

Azure 키 자격 증명 모음에 대한 개요는 [Azure 키 자격 증명 모음이란?](key-vault-whatis.md)

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

다음 자습서를 통해 Azure 플랫폼 간 명령줄 인터페이스의 Azure 리소스 관리자를 학습할 수도 있습니다.

* [Azure 플랫폼 간 명령줄 인터페이스를 설치 및 구성하는 방법](../cli-install-nodejs.md)
* [Azure 리소스 관리자와 함께 Azure 플랫폼 간 명령줄 인터페이스 사용](../xplat-cli-azure-resource-manager.md)

## <a name="connect-to-your-subscriptions"></a>구독에 연결

조직 계정을 사용하여 로그인하려면 다음 명령을 사용합니다.

    azure login -u username -p password

또는 대화형으로 입력하여 로그인하려는 경우

    azure login

> [!NOTE]
> 로그인 방법은 조직 계정에서만 작동합니다. 조직 계정은 조직에서 관리되고 조직 Azure Active Directory 테넌트에 정의된 사용자입니다.
> 
> 

현재 조직 계정이 없고 Microsoft 계정을 사용하여 Azure 구독에 로그인하는 경우 다음 단계에 따라 쉽게 만들 수 있습니다.

1. [Azure 관리 포털](https://manage.windowsazure.com/)에 로그인한 후 Active Directory를 클릭합니다.
2. 디렉터리가 없는 경우 디렉터리 만들기를 선택하고 요청된 정보를 제공합니다.
3. 디렉터리를 선택하고 새 사용자를 추가합니다. 새 사용자는 조직 계정입니다. 사용자를 만드는 동안 사용자의 전자 메일 주소와 임시 암호가 제공됩니다. 이 정보는 다른 단계에서 사용되므로 저장해 두십시오.
4. 포털에서 설정을 선택한 후 관리자를 선택합니다. 추가를 선택하고 새 사용자를 공동 관리자로 추가합니다. 그러면 조직 계정이 Azure 구독을 관리할 수 있습니다.
5. 마지막으로, Azure 포털에서 로그아웃한 후 새 조직 계정을 사용하여 다시 로그인합니다. 이 계정으로 처음 로그인하는 경우 암호를 변경하라는 메시지가 표시됩니다.

Microsoft Azure의 조직 계정 사용에 대한 자세한 내용은 [조직으로 Microsoft Azure에 등록](../active-directory/sign-up-organization.md)을 참조하세요.

여러 구독이 있는 경우 Azure 키 자격 증명 모음에 사용할 특정 하나를 지정하려면, 다음을 입력하여 계정에 대한 구독을 표시합니다.

    azure account list

그런 후, 사용할 구독을 지정하려면 다음을 입력합니다.

    azure account set <subscription name>

Azure 플랫폼 간 명령줄 인터페이스 구성에 대한 자세한 내용은 [Azure 플랫폼 간 명령줄 인터페이스를 설치 및 구성하는 방법](../cli-install-nodejs.md)을 참조하세요.

## <a name="switch-to-using-azure-resource-manager"></a>Azure 리소스 관리자로 전환
키 자격 증명 모음에는 Azure 리소스 관리자가 필요로 하므로 다음을 입력하여 Azure 리소스 관리자 모드로 전환합니다.

    azure config mode arm

## <a name="create-a-new-resource-group"></a>새 리소스 그룹 만들기
Azure 리소스 관리자를 사용하면 관련된 모든 리소스는 리소스 그룹의 내부에 만들어집니다. 이 자습서에서는 'ContosoResourceGroup'이라는 새 리소스 그룹을 만듭니다.

    azure group create 'ContosoResourceGroup' 'East Asia'

첫 번째 매개 변수는 리소스 그룹 이름이고, 두 번째 매개 변수 위치입니다. 위치의 경우, `azure location list` 명령을 사용하여 이 예에서 대체 위치를 지정하는 방법을 식별합니다. 자세한 정보가 필요한 경우 `azure help location`

## <a name="register-the-key-vault-resource-provider"></a>키 자격 증명 모음 리소스 공급자 등록
키 자격 증명 모음 리소스 공급자가 구독에 등록되어 있는지 확인합니다.

`azure provider register Microsoft.KeyVault`

구독당 한 번만 수행하면 됩니다.

## <a name="create-a-key-vault"></a>키 자격 증명 모음 만들기

`azure keyvault create` 명령을 사용하여 키 자격 증명 모음을 만듭니다. 이 스크립트에는 3개의 필수 매개 변수, 리소스 그룹 이름, 키 자격 증명 모음 이름 및 지리적 위치가 있습니다.

예를 들어, 이름이 ContosoKeyVault이고, 리소스 그룹 이름이 ContosoResourceGroup이며 위치가 동아시아인 형식의 자격 증명 모음인 경우, 다음을 입력합니다.

    azure keyvault create --vault-name 'ContosoKeyVault' --resource-group 'ContosoResourceGroup' --location 'East Asia'

이 명령의 출력에서는 방금 만든 자격 증명 모음의 속성을 보여 줍니다. 가장 중요한 두 개의 속성은 다음과 같습니다.

* **Name**: ContosoKeyVault이는 예제에서입니다. 다른 키 자격 증명 모음 cmdlet에 대해 이 이름을 사용합니다.
* **vaultUri**: 예제에서는 https://contosokeyvault.vault.azure.net입니다. REST API를 통해 사용자 자격 증명 모음을 사용하는 응용 프로그램은 URI를 사용해야 합니다.

Azure 계정은 이제 이 키 자격 증명 모음에서 모든 작업을 수행할 권한을 가지게 됩니다. 아직 다른 사람은 권한이 없습니다.

## <a name="add-a-key-or-secret-to-the-key-vault"></a>키 또는 암호를 키 자격 증명 모음에 추가

소프트웨어 보호 키를 만들도록 Azure 키 자격 증명 모음을 사용하려면, `azure key create` 명령을 사용하여 다음을 입력합니다.

    azure keyvault key create --vault-name 'ContosoKeyVault' --key-name 'ContosoFirstKey' --destination software

그러나 Azure 키 자격 증명 모음에 업로드하려는 softkey.pem이라는 파일에 로컬 파일로 저장된 .pem 파일의 기존 키가 있는 경우 다음을 입력하여 키 자격 증명 모음 서비스에서 소프트웨어로 키를 보호하는 .PEM 파일에서 키를 가져옵니다.

    azure keyvault key import --vault-name 'ContosoKeyVault' --key-name 'ContosoFirstKey' --pem-file './softkey.pem' --password 'PaSSWORD' --destination software

이제 해당 URI를 사용하여 Azure 키 자격 증명 모음을 만들거나 업로드하는 이 키를 참조할 수 있습니다. **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey**를 사용하여 현재 버전을 가져오고 **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey/cgacf4f763ar42ffb0a1gca546aygd87**을 사용하여 이 특정 버전을 가져옵니다.

자격 증명 모음에 암호를 추가하려면 다음을 입력합니다. 암호는 SQLPassword이며 Azure 키 자격 증명 모음에 대한 Pa$$w0rd 값을 가집니다.

    azure keyvault secret set --vault-name 'ContosoKeyVault' --secret-name 'SQLPassword' --value 'Pa$$w0rd'

이제 해당 URI를 사용하여 Azure 키 자격 증명 모음에 추가한 이 암호를 참조할 수 있습니다. **https://ContosoVault.vault.azure.net/secrets/SQLPassword**를 사용하여 현재 버전을 가져오고 **https://ContosoVault.vault.azure.net/secrets/SQLPassword/90018dbb96a84117a0d2847ef8e7189d**를 사용하여 이 특정 버전을 가져옵니다.

방금 만든 키 또는 암호를 확인해 보겠습니다.

* 사용자의 키를 보려면 다음을 입력 합니다. `azure keyvault key list --vault-name 'ContosoKeyVault'`
* 사용자의 비밀을 보려면 다음을 입력 합니다. `azure keyvault secret list --vault-name 'ContosoKeyVault'`

## <a name="register-an-application-with-azure-active-directory"></a>Azure Active Directory에 응용 프로그램 등록

이 단계는 일반적으로 별도의 컴퓨터에서 개발자가 수행할 수 있습니다. Azure 키 자격 증명 모음에 특정한 것은 아니지만 완전성을 위해 포함됩니다.

> [!IMPORTANT]
> 이 단계에서 등록된 자습서, 계정, 해당 자격 증명 모음 및 응용 프로그램을 완료하려면, 모두 동일한 Azure 디렉터리에 있어야 합니다.
> 
> 

자격 증명 모음 키를 사용하는 응용 프로그램은 Azure Active Directory에서 토큰을 사용하여 인증해야 합니다. 이렇게 하려면 응용 프로그램의 소유자가 Azure Active Directory에 먼저 응용 프로그램을 등록해야 합니다. 등록 끝에 응용 프로그램 소유자는 다음 값을 가져옵니다.

* **응용 프로그램 ID**(클라이언트 ID라고도 함) 및 **인증 키**(공유 비밀이라고도 함). 응용 프로그램은 토큰을 가져올 Azure Active Directory에 이 두 값 모두가 있어야 합니다. 응용 프로그램 구성 방식은 응용 프로그램에 따라 달라집니다. 키 자격 증명 모음 샘플 응용 프로그램의 경우, 응용 프로그램 소유자는 app.config 파일에서 이러한 값을 설정합니다.

Azure Active Directory에 응용 프로그램을 등록하려면:

1. Azure 포털에 로그인합니다.
2. 왼쪽에서 **Active Directory**를 클릭한 다음 응용 프로그램을 등록할 디렉터리를 선택합니다. <br> <br> 

>[!NOTE] 
> 주요 자격 증명 모음을 만든 Azure 구독을 포함하는 동일한 디렉터리를 선택해야 합니다. 이 디렉터리를 모르는 경우, **설정**을 클릭하고, 사용자 키 자격 증명 모음을 만든 구독을 식별하고 마지막 열에 표시되는 디렉터리의 이름을 확인합니다.

3. **APPLICATIONS**를 클릭합니다. 디렉터리에 추가한 앱이 없는 경우,이 페이지는 **앱 추가** 링크만을 표시합니다. 링크를 클릭하거나 명령 모음에서 **추가** 를 클릭할 수 있습니다.
4. **응용 프로그램 추가** 마법사에서 **무엇을 하고 싶나요?** 페이지를 클릭하고, **조직에서 개발 중인 응용 프로그램 추가**를 클릭합니다.
5. **응용 프로그램에 대해 알려주세요** 페이지에서, 응용 프로그램 이름을 지정하고 **웹 응용 프로그램 및/또는 Web API**(기본값)을 선택합니다. 다음 아이콘을 클릭합니다.
6. **앱 속성** 페이지에서 웹 응용 프로그램에 대한 **로그인 URL** 및 **앱 ID URI**를 지정합니다. 응용 프로그램에 이러한 값이 없는 경우 이 단계에서 만들 수 있습니다(예를 들어, 두 상자에 대해 http://test1.contoso.com을 지정할 수 있음). 이러한 사이트가 존재하는 지가 중요하지 않습니다. 사용자 디렉토리의 각 응용 프로그램에 대해 각 응용 프로그램의 앱 ID URI가 다르다는 것이 중요합니다. 디렉터리는 이 문자열을 사용하여 앱을 식별합니다.
7. 마법사에서 변경 내용을 저장하려면 전체 아이콘을 클릭합니다.
8. 빠른 시작 페이지에서 **CONFIGURE**를 클릭합니다.
9. **키** 섹션으로 스크롤하고 기간을 선택한 다음 **저장**을 클릭합니다. 페이지가 새로 고쳐지고 이제 키 값을 표시합니다. 이 키 값 및 **클라이언트 ID** 가 있는 응용 프로그램을 구성해야 합니다. (이 구성에 대한 지침은 응용 프로그램에 특정된 것입니다.)
10. 사용자 자격 증명 모음에 사용 권한을 설정하려면 다음 단계에서 사용하는 이 페이지에서 클라이언트 ID 값을 복사 합니다.

## <a name="authorize-the-application-to-use-the-key-or-secret"></a>응용 프로그램에 키 또는 암호를 사용하도록 권한 부여
응용 프로그램이 자격 증명 모음의 키 또는 암호에 대한 액세스를 인증하려면 `azure keyvault set-policy` 명령을 사용합니다.

예를 들어, 자격 증명 모음 이름은 ContosoKeyVault이고 권한을 부여하려는 응용 프로그램에 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed의 클라이언트 ID가 있고 자격 증명 모음에 있는 키로 서명하고 암호 해제하도록 응용 프로그램을 인증하려면, 다음을 실행합니다.

    azure keyvault set-policy --vault-name 'ContosoKeyVault' --spn 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed --perms-to-keys '[\"decrypt\",\"sign\"]'

> [!NOTE]
> Windows 명령 프롬프트에서 실행하는 경우 작은따옴표를 큰따옴표로 바꾸고 내부 큰따옴표를 이스케이프해야 예: "[\"해독\",\"기호\"]"
> 
> 

자격 증명 모음에서 기밀 정보를 읽기 위해 동일한 응용 프로그램에 권한을 부여하려면 다음을 실행합니다.

    azure keyvault set-policy --vault-name 'ContosoKeyVault' --spn 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed --perms-to-secrets '[\"get\"]'

## <a name="if-you-want-to-use-a-hardware-security-module-hsm"></a>하드웨어 보안 모듈(HSM)을 사용하려는 경우
추가된 보증을 위해, HSM 경계를 절대로 떠나지 않는 하드웨어 보안 모듈(HSMs)에서 키를 가져오거나 생성할 수 있습니다. HSM은 FIPS 140-2 Level 2 유효성 검사가 적용됩니다. 이 요구 사항이 사용자에게 적용되지 않는 경우, 이 섹션을 건너뛰고 [키 자격 증명 모음 및 연결된 키와 암호 삭제](#delete-the-key-vault-and-associated-keys-and-secrets)로 이동합니다.

이러한 HSM 보호되는 키를 만들려면, HSM 보호되는 키를 지원하는 자격 증명 모음 구독이 있어야 합니다.

keyvault를 만들 때 'sku' 매개 변수를 추가합니다.

    azure azure keyvault create --vault-name 'ContosoKeyVaultHSM' --resource-group 'ContosoResourceGroup' --location 'East Asia' --sku 'Premium'

이 자격 증명이 모음에 소프트웨어 보호 키(이전에 표시된 대로)와 HSM 보호되는 키를 추가할 수 있습니다. HSM으로 보호되는 키를 만들려면, 대상 매개 변수를 'HSM'으로 설정합니다.

    azure keyvault key create --vault-name 'ContosoKeyVaultHSM' --key-name 'ContosoFirstHSMKey' --destination 'HSM'

다음 명령을 사용하여 사용자 컴퓨터의 .pem 파일에서 키를 가져올 수 있습니다. 이 명령은 키 자격 증명 모음 서비스에서 HSM으로 키를 가져옵니다.

    azure keyvault key import --vault-name 'ContosoKeyVaultHSM' --key-name 'ContosoFirstHSMKey' --pem-file '/.softkey.pem' --destination 'HSM' --password 'PaSSWORD'

다음 명령은 "bring your own key"(BYOK) 패키지를 가져옵니다. 이렇게 하면 로컬 HSM에 키를 생성하고 키가 HSM 경계를 벗어나지 않고 이를 키 자격 증명 모음 서비스의 HSM에 전달합니다.

    azure keyvault key import --vault-name 'ContosoKeyVaultHSM' --key-name 'ContosoFirstHSMKey' --byok-file './ITByok.byok' --destination 'HSM'

이 BYOK 패키지를 생성하는 방법에 대한 자세한 내용은 [Azure 키 자격 증명 모음과 HSM-보호된 키를 사용하는 방법](key-vault-hsm-protected-keys.md)을 참조하세요.

## <a name="delete-the-key-vault-and-associated-keys-and-secrets"></a>키 자격 증명 모음 및 연결된 키와 암호 삭제
키 자격 증명 모음 및 이를 포함하는 키나 비밀이 더 이상 필요하지 않은 경우, azure keyvault delete 명령을 사용하여 키 자격 증명 모음을 삭제할 수 있습니다.

    azure keyvault delete --vault-name 'ContosoKeyVault'

또는 키 자격 증명 모음 및 해당 그룹에 포함된 다른 모든 리소스 및 키를 포함하는 전체 Azure 리소스 그룹을 삭제할 수 있습니다.

    azure group delete --name 'ContosoResourceGroup'


## <a name="other-azure-cross-platform-command-line-interface-commands"></a>다른 Azure 플랫폼 간 명령줄 인터페이스 명령
Azure 키 자격 증명 모음을 관리하는 데 유용할 수 있는 다른 명령입니다.

이 명령은 모든 키와 선택한 속성을 테이블 형식으로 나열합니다.

    azure keyvault key list --vault-name 'ContosoKeyVault'

이 명령은 지정된 키에 대한 속성의 전체 목록을 표시합니다.

    azure keyvault key show --vault-name 'ContosoKeyVault' --key-name 'ContosoFirstKey'

이 명령은 모든 비밀 이름과 선택한 속성을 테이블 형식으로 표시합니다.

    azure keyvault secret list --vault-name 'ContosoKeyVault'

다음은 특정 키를 제거하는 방법에 대한 예제입니다.

    azure keyvault key delete --vault-name 'ContosoKeyVault' --key-name 'ContosoFirstKey'

다음은 특정 암호를 제거하는 방법에 대한 예제입니다.

    azure keyvault secret delete --vault-name 'ContosoKeyVault' --secret-name 'SQLPassword'


## <a name="next-steps"></a>다음 단계
프로그래밍 참조는 [Azure 주요 자격 증명 모음 개발자 가이드](key-vault-developers-guide.md)를 참조하세요.

