---
title: 자습서`:` 관리 ID를 사용하여 Azure Key Vault에 액세스 - Linux - Azure AD
description: Linux VM 시스템 할당 관리 ID를 사용하여 Azure Resource Manager에 액세스하는 프로세스를 단계별로 안내하는 자습서입니다.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: daveba
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/03/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7cfcaec38a939291090da7d2229c4a95f984bf28
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93360443"
---
# <a name="tutorial-use-a-linux-vm-system-assigned-managed-identity-to-access-azure-key-vault"></a>자습서: Linux VM 시스템 할당 관리 ID를 사용하여 Azure Key Vault에 액세스 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

이 자습서에서는 Linux VM(가상 머신)이 시스템 할당 관리 ID를 사용하여 [Azure Key Vault](../../key-vault/general/overview.md)에 액세스하는 방법을 보여줍니다. 부트스트랩 역할을 하는 Key Vault를 사용하면 클라이언트 애플리케이션에서 비밀을 사용하여 Azure AD(Active Directory)로 보호되지 않는 리소스에 액세스할 수 있습니다. 관리되는 서비스 ID는 Azure에서 자동으로 관리되며, 코드에 인증 정보를 포함하지 않고도 Azure AD 인증을 지원하는 서비스에 인증할 수 있습니다.

다음 방법을 알아봅니다.

> [!div class="checklist"]
> * Key Vault에 저장된 비밀 액세스 권한을 VM에 부여 
> * VM ID를 사용하여 액세스 토큰을 가져온 다음 Key Vault에서 비밀을 검색하는 데 사용 
 
## <a name="prerequisites"></a>사전 요구 사항

- 관리 ID에 대한 이해. Azure 리소스에 대한 관리 ID 기능이 익숙하지 않은 경우 [개요](overview.md)를 참조하세요. 
- Azure 계정, [체험 계정에 등록](https://azure.microsoft.com/free/)합니다.
- 적절한 범위(사용자 구독 또는 리소스 그룹)에서 필요한 리소스 생성 및 역할 관리 단계를 수행할 수 있는 "소유자" 권한. 역할 할당에 관한 도움이 필요한 경우 [역할 기반 액세스 제어를 사용하여 Azure 구독 리소스에 대한 액세스 관리](../../role-based-access-control/role-assignments-portal.md)를 참조하세요.
- 시스템 할당 관리 ID가 활성화된 Linux 가상 머신도 필요합니다.
  - 이 자습서에 대한 가상 머신을 만들어야 하는 경우 [Azure Portal을 사용하여 Linux 가상 머신 만들기](../../virtual-machines/linux/quick-create-portal.md#create-virtual-machine)라는 제목의 문서를 수행하면 됩니다.


## <a name="create-a-key-vault"></a>주요 자격 증명 모음 만들기  

이 섹션에서는 Key Vault에 저장된 비밀에 대한 액세스 권한을 VM에 부여하는 방법을 보여줍니다. Azure 리소스에 대한 관리 ID를 사용하면 코드에서 Azure AD 인증을 지원하는 리소스에 인증하기 위한 액세스 토큰을 가져올 수 있습니다.그러나 모든 Azure 서비스가 Azure AD 인증을 지원하지는 않습니다. 이러한 서비스에서 Azure 리소스에 대한 관리 ID를 사용하려면 Azure Key Vault에 서비스 자격 증명을 저장하고 VM의 관리 ID로 Key Vault에 액세스하여 자격 증명을 검색합니다.

먼저 Key Vault를 만들고, VM의 시스템 할당 관리 ID 액세스 권한을 Key Vault에 부여해야 합니다.

1. Azure [Portal](https://portal.azure.com/) 열기
1. 왼쪽 탐색 모음 맨 위에서 **리소스 만들기** 를 선택합니다.  
1. **Marketplace 검색** 상자에 **Key Vault** 를 입력하고 **Enter** 를 누릅니다.  
1. 결과 목록에서 **Key Vault** 를 선택합니다.
1. **만들기** 를 선택합니다.
1. 새 Key Vault의 **이름** 을 입력합니다.

    ![키 자격 증명 모음 화면 만들기](./media/tutorial-linux-vm-access-nonaad/create-key-vault.png)

1. 이 자습서에 사용 중인 가상 머신을 만든 구독 및 리소스 그룹을 선택하도록 필요한 모든 정보를 입력합니다.
1. **검토 + 만들기** 를 선택합니다.
1. **만들기** 를 선택합니다.

## <a name="grant-access"></a>액세스 권한 부여

가상 머신에서 사용하는 관리 ID에는 Key Vault에 저장할 비밀을 읽을 수 있는 액세스 권한이 부여되어야 합니다.

1. 새로 만든 Key Vault로 이동합니다.
1. 왼쪽 메뉴에서 **액세스 정책** 을 선택합니다.
1. **액세스 정책 추가** 를 선택합니다.

   ![키 자격 증명 모음 만들기 액세스 정책 화면](./media/tutorial-linux-vm-access-nonaad/key-vault-access-policy.png)

1. **템플릿에서 구성(선택 사항)** 아래의 **액세스 정책 추가** 섹션의 풀다운 메뉴에서 **비밀 관리** 를 선택합니다.
1. **주체 선택** 을 선택하고 검색 필드에 앞에서 만든 VM의 이름을 입력합니다.  결과 목록에서 VM을 선택하고 **선택** 을 선택합니다.
1. **추가** 를 선택합니다.
1. **저장** 을 선택합니다.

## <a name="create-a-secret"></a>비밀 만들기

다음으로, Key Vault에 비밀을 추가하면 나중에 VM에서 실행 중인 코드를 사용하여 비밀을 검색할 수 있습니다. 이 자습서에서는 PowerShell을 사용하지만 이 가상 머신에서 실행되는 모든 코드에 동일한 개념이 적용됩니다.

1. 새로 만든 Key Vault로 이동합니다.
1. **비밀** 을 선택하고 **추가** 를 클릭합니다.
1. **생성/가져오기** 를 선택합니다.
1. **업로드 옵션 **의** 비밀 만들기** 화면에서 **수동** 을 선택한 상태로 둡니다.
1. 비밀의 이름과 값을 입력합니다.  원하는 어떤 값이나 입력할 수 있습니다. 
1. 활성화 날짜와 만료 날짜는 비워 두고 **사용 가능** 은 **예** 로 유지합니다. 
1. **만들기** 를 클릭하여 비밀을 만듭니다.

   ![비밀 만들기](./media/tutorial-linux-vm-access-nonaad/create-secret.png)
 
## <a name="access-data"></a>데이터 액세스

아래의 단계를 완료하려면 SSH 클라이언트가 필요합니다.  Windows를 사용 중인 경우 [Linux용 Windows 하위 시스템](/windows/wsl/about)에서 SSH 클라이언트를 사용할 수 있습니다. SSH 클라이언트의 키 구성에 대한 도움이 필요하면 [Azure에서 Windows를 통해 SSH 키를 사용하는 방법](../../virtual-machines/linux/ssh-from-windows.md) 또는 [Azure에서 Linux VM용 SSH 공개 및 프라이빗 키 쌍을 만들고 사용하는 방법](../../virtual-machines/linux/mac-create-ssh-keys.md)을 참조하세요.
 
1. Portal에서 Linux VM으로 이동한 다음 **개요** 에서 **연결** 을 클릭합니다. 
2. 선택한 SSH 클라이언트를 사용하여 VM에 **연결** 합니다. 
3. 터미널 창에서 CURL을 사용하여 Azure 리소스에 대한 로컬 관리 ID 엔드포인트에 대한 요청을 만들어서 Azure Key Vault에 대한 액세스 토큰을 가져옵니다.  
 
    액세스 토큰에 대한 CURL 요청은 다음과 같습니다.  
    
    ```bash
    curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net' -H Metadata:true  
    ```
    응답에는 Resource Manager에 액세스하는 데 필요한 액세스 토큰이 포함되어 있습니다. 
    
    응답:  
    
    ```bash
    {"access_token":"eyJ0eXAi...",
    "refresh_token":"",
    "expires_in":"3599",
    "expires_on":"1504130527",
    "not_before":"1504126627",
    "resource":"https://vault.azure.net",
    "token_type":"Bearer"} 
    ```
    
    이 액세스 토큰을 사용하여 Azure Key Vault에 인증할 수 있습니다.  다음 CURL 요청은 CURL 및 Key Vault REST API를 사용하여 Key Vault에서 비밀을 읽는 방법을 보여 줍니다.  Key Vault **개요** 페이지의 **기본 정보** 섹션에 있는 Key Vault의 URL이 필요합니다.  또한 이전 호출에서 확인한 액세스 토큰도 필요합니다. 
        
    ```bash
    curl https://<YOUR-KEY-VAULT-URL>/secrets/<secret-name>?api-version=2016-10-01 -H "Authorization: Bearer <ACCESS TOKEN>" 
    ```
    
    응답은 다음과 같습니다. 
    
    ```bash
    {"value":"p@ssw0rd!","id":"https://mytestkeyvault.vault.azure.net/secrets/MyTestSecret/7c2204c6093c4d859bc5b9eff8f29050","attributes":{"enabled":true,"created":1505088747,"updated":1505088747,"recoveryLevel":"Purgeable"}} 
    ```
    
Key Vault에서 비밀을 검색한 후에는 이름과 암호가 필요한 서비스에 인증하는 데 비밀을 사용할 수 있습니다.

## <a name="clean-up-resources"></a>리소스 정리

리소스를 정리하려면 [Azure Portal](https://portal.azure.com)을 방문하여 **리소스 그룹** 을 선택하고 이 자습서의 프로세스에서 만든 리소스 그룹(예: `mi-test`)을 찾아서 선택한 다음, **리소스 그룹 삭제** 명령을 사용합니다.

또는 [PowerShell 또는 CLI](../../azure-resource-manager/management/delete-resource-group.md)를 통해 이 작업을 수행할 수도 있습니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Linux VM 시스템 할당 관리 ID를 사용하여 Azure Key Vault에 액세스하는 방법을 알아보았습니다.  Azure Key Vault에 대한 자세한 내용은 다음을 참조하세요.

> [!div class="nextstepaction"]
>[Azure Key Vault](../../key-vault/general/overview.md)