---
title: Azure Active Directory 액세스를 구성 하는 방법
description: Azure Active Directory 액세스를 사용 하 여 Azure Blockchain 서비스를 구성 하는 방법
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: seal
ms.date: 05/02/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: seal
manager: femila
ms.openlocfilehash: 616e342f1d52179c40c225c5dafc9de13ce85e06
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028217"
---
# <a name="how-to-configure-azure-active-directory-access"></a>Azure Active Directory 액세스를 구성 하는 방법

이 문서에서는 액세스 권한을 부여 하 여 Azure Active Directory (Azure AD) 사용자, 그룹 또는 응용 프로그램 Id를 사용 하 여 Azure Blockchain 서비스 노드를 연결 하는 방법을 알아봅니다.

Azure AD는 클라우드 기반 id 관리 기능을 제공 하 고 Azure에서 전체 엔터프라이즈 및 액세스 응용 프로그램에서 단일 id를 사용할 수 있습니다. Azure Blockchain 서비스는 Azure AD와 통합 하 고 ID 페더레이션과 single sign on 및 다단계 인증 같은 이점을 제공 합니다.

## <a name="prerequisites"></a>필수 조건

* [Azure portal을 사용 하 여 블록 체인 멤버를 만들려면](create-member.md)

## <a name="grant-access"></a>액세스 권한 부여

멤버 수준 및 노드 수준 모두에서 액세스를 부여할 수 있습니다. 멤버 수준에 대 한 액세스 권한을 부여 하는 멤버 아래에서 모든 노드에 대 한 액세스를 부여 다시 합니다.

### <a name="grant-member-level-access"></a>멤버 수준 액세스 권한 부여

멤버 수준에서 액세스 권한을 부여 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 이동할 **액세스 제어 (IAM) > 추가 > 역할 할당 추가**합니다.
1. 선택 된 **Blockchain 멤버 노드 액세스 (미리 보기)** 역할에 대 한 액세스 권한을 부여 하려는 Azure AD ID 개체를 추가 합니다. Azure AD ID 개체 수 있습니다.

    | Azure AD 개체 | 예 |
    |-----------------|---------|
    | Azure AD 사용자   | `frank@contoso.onmicrosoft.com` |
    | Azure AD 그룹  | `sales@contoso.onmicrosoft.com` |
    | 애플리케이션 UI  | `13925ab1-4161-4534-8d18-812f5ca1ab1e` |

    ![역할 할당 추가](./media/configure-aad/add-role-assignment.png)

1. **저장**을 선택합니다.

### <a name="grant-node-level-access"></a>노드 수준 액세스 권한 부여

1. 노드 간 보안으로 이동 하 여 노드 수준에서 액세스를 부여 하 고 액세스 권한을 부여 하려는 노드의 이름을 클릭 수 있습니다.
1. Blockchain 멤버 노드 액세스 (미리 보기)에 역할을 선택 하 고에 대 한 액세스 권한을 부여 하려는 Azure AD ID 개체를 추가 합니다. 

## <a name="connect-using-azure-blockchain-connector"></a>Azure Blockchain 커넥터를 사용 하 여 연결

다운로드 하거나 복제 합니다 [GitHub에서 Azure Blockchain 커넥터](https://github.com/Microsoft/azure-blockchain-connector/)합니다.

```bash
git clone https://github.com/Microsoft/azure-blockchain-connector.git
```

다음 빠른 시작 섹션을 **readme** 소스 코드에서 커넥터를 빌드하려면.

### <a name="connect-using-an-azure-ad-user-account"></a>Azure AD 사용자 계정을 사용 하 여 연결

1. Azure AD 사용자 계정을 사용 하 여 인증 하려면 다음 명령을 실행 합니다. 바꿉니다 \<myAADDirectory\> Azure AD 도메인을 포함 합니다. 예: `yourdomain.onmicrosoft.com`.

    ```
    connector.exe -remote <myMemberName>.blockchain.azure.com:3200 -method aadauthcode -tenant-id <myAADDirectory> 
    ```

1. Azure AD 자격 증명을 묻습니다.
1. 사용자 이름 및 암호를 로그인 합니다.
1. 인증이 성공 하면 로컬 프록시는 블록 체인 노드를 연결합니다. 이제 로컬 끝점과 Geth 클라이언트를 연결할 수 있습니다.

    ```bash
    geth attach http://127.0.0.1:3100
    ```

### <a name="connect-using-an-application-id"></a>응용 프로그램 ID를 사용 하 여 연결

대부분의 응용 프로그램 응용 프로그램 ID를 사용 하 여 Azure AD 사용자 계정을 대신 하는 Azure AD로 인증 합니다.

응용 프로그램 ID를 사용 하 여 노드를 연결 하려면 바꿉니다 **aadauthcode** 사용 하 여 **aadclient**합니다.

```
connector.exe -remote <myBlockchainEndpoint>  -method aadclient -client-id <myClientID> -client-secret "<myClientSecret>" -tenant-id <myAADDirectory>
```

| 매개 변수 | 설명 |
|-----------|-------------|
| tenant-id | Azure AD 도메인에 예를 들어, `yourdomain.onmicrosoft.com`
| client-id | Azure AD에 등록된 된 응용 프로그램의 클라이언트 ID
| client-secret | Azure AD에 등록된 된 응용 프로그램의 클라이언트 암호

Azure AD에서 응용 프로그램을 등록 하는 방법에 대 한 자세한 내용은 참조 하세요. [방법: 포털을 사용하여 리소스에 액세스할 수 있는 Azure AD 애플리케이션 및 서비스 주체 만들기](../../active-directory/develop/howto-create-service-principal-portal.md)

### <a name="connect-a-mobile-device-or-text-browser"></a>모바일 장치 또는 텍스트 브라우저 연결

모바일 장치 또는 텍스트 기반 브라우저를 Azure AD 인증 팝업 표시 수 없는 경우 Azure AD는 일회성 암호를 생성 합니다. 암호를 복사 하 고 다른 환경에서 Azure AD 인증을 진행할 수 있습니다.

암호를 생성 하려면 바꿉니다 **aadauthcode** 사용 하 여 **aaddevice**합니다. 바꿉니다 \<myAADDirectory\> Azure AD 도메인을 포함 합니다. 예: `yourdomain.onmicrosoft.com`.

```
connector.exe -remote <myBlockchainEndpoint>  -method aaddevice -tenant-id <myAADDirectory>
```

## <a name="next-steps"></a>다음 단계

Azure Blockchain Service의 데이터 보안에 대 한 자세한 내용은 다음을 참조 하세요.

> [!div class="nextstepaction"]
> [Azure Blockchain 서비스 보안](data-security.md)