---
title: Azure Active Directory 액세스 - Azure Blockchain Service 구성
description: Azure Active Directory 액세스를 사용하여 Azure Blockchain Service를 구성하는 방법
ms.date: 11/22/2019
ms.topic: how-to
ms.reviewer: janders
ms.openlocfilehash: 337d01abc51d310d06aeea3427b770132be4824c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "85208776"
---
# <a name="how-to-configure-azure-active-directory-access-for-azure-blockchain-service"></a>Azure Blockchain Service에 대한 Azure Active Directory 액세스를 구성하는 방법

이 문서에서는 Azure AD(Azure Active Directory) 사용자, 그룹 또는 애플리케이션 ID를 사용하여 액세스 권한을 부여하고 Azure Blockchain Service 노드에 연결하는 방법에 대해 알아봅니다.

Azure AD는 클라우드 기반 ID를 관리하며 전체 엔터프라이즈에서 단일 ID를 사용하고 Azure의 애플리케이션에 액세스할 수 있도록 합니다. Azure Blockchain Service는 Azure AD와 통합하여 ID 페더레이션, Single Sign-On 및 다단계 인증과 같은 이점을 제공합니다.

## <a name="prerequisites"></a>사전 요구 사항

* [Azure Portal을 사용하여 블록체인 멤버 만들기](create-member.md)

## <a name="grant-access"></a>액세스 권한 부여

멤버 수준과 노드 수준 모두에서 액세스 권한을 부여할 수 있습니다. 멤버 수준에서 액세스 권한을 부여하면 해당 멤버 아래의 모든 노드에 대해서도 액세스 권한이 부여됩니다.

### <a name="grant-member-level-access"></a>멤버 수준 액세스 권한 부여

멤버 수준에서 액세스 권한을 부여하려면 다음을 수행합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. **액세스 제어(IAM) > 추가> 역할 할당 추가** 로 이동합니다.
1. **블록체인 멤버 노드 액세스 권한(미리 보기)** 역할을 선택하고 액세스 권한을 부여하려는 Azure AD ID 개체를 추가합니다. Azure AD ID 개체는 다음과 같을 수 있습니다.

    | Azure AD 개체 | 예제 |
    |-----------------|---------|
    | Azure AD 사용자   | `kim@contoso.onmicrosoft.com` |
    | Azure AD 그룹  | `sales@contoso.onmicrosoft.com` |
    | 애플리케이션 ID  | `13925ab1-4161-4534-8d18-812f5ca1ab1e` |

    ![역할 할당 추가](./media/configure-aad/add-role-assignment.png)

1. **저장** 을 선택합니다.

### <a name="grant-node-level-access"></a>노드 수준 액세스 권한 부여

노드 수준에서 액세스 권한을 부여하려면 노드 보안으로 이동하여 액세스 권한을 부여하려는 노드 이름을 클릭합니다.

블록체인 멤버 노드 액세스 권한(미리 보기) 역할을 선택하고 액세스 권한을 부여하려는 Azure AD ID 개체를 추가합니다.

자세한 내용은 [Azure Blockchain Service 트랜잭션 노드 구성](configure-transaction-nodes.md#azure-active-directory-access-control)을 참조하세요.

## <a name="connect-using-azure-blockchain-connector"></a>Azure Blockchain 커넥터를 사용하여 연결

[GitHub에서 Azure Blockchain 커넥터](https://github.com/Microsoft/azure-blockchain-connector/)를 다운로드하거나 복제합니다.

```bash
git clone https://github.com/Microsoft/azure-blockchain-connector.git
```

**readme** 에 있는 빠른 시작 섹션의 지침을 따라 소스 코드에서 커넥터를 빌드합니다.

### <a name="connect-using-an-azure-ad-user-account"></a>Azure AD 사용자 계정을 사용하여 연결

1. 다음 명령을 실행하여 Azure AD 사용자 계정을 사용하여 인증합니다. \<myAADDirectory\>을 Azure AD 도메인으로 바꿉니다. 예들 들어 `yourdomain.onmicrosoft.com`입니다.

    ```
    connector.exe -remote <myMemberName>.blockchain.azure.com:3200 -method aadauthcode -tenant-id <myAADDirectory> 
    ```

1. Azure AD에 자격 증명을 묻는 메시지가 표시됩니다.
1. 사용자 이름과 암호를 사용하여 로그인합니다.
1. 인증에 성공하면 로컬 프록시가 블록체인 노드에 연결됩니다. 이제 로컬 엔드포인트를 사용하여 Geth 클라이언트를 연결할 수 있습니다.

    ```bash
    geth attach http://127.0.0.1:3100
    ```

### <a name="connect-using-an-application-id"></a>애플리케이션 ID를 사용하여 연결

다수의 애플리케이션은 Azure AD 사용자 계정 대신 애플리케이션 ID를 사용하여 Azure AD를 인증합니다.

애플리케이션 ID를 사용 여 노드에 연결하려면 **aadauthcode** 를 **aadclient** 로 바꿉니다.

```
connector.exe -remote <myBlockchainEndpoint>  -method aadclient -client-id <myClientID> -client-secret "<myClientSecret>" -tenant-id <myAADDirectory>
```

| 매개 변수 | 설명 |
|-----------|-------------|
| tenant-id | Azure AD 도메인(예: `yourdomain.onmicrosoft.com`)
| client-id | Azure AD에 등록된 애플리케이션의 클라이언트 ID
| client-secret | Azure AD에 등록된 애플리케이션의 클라이언트 암호

Azure AD에 애플리케이션을 등록하는 방법에 대한 자세한 내용은 [방법: 포털을 사용하여 리소스에 액세스할 수 있는 Azure AD 애플리케이션 및 서비스 주체 만들기](../../active-directory/develop/howto-create-service-principal-portal.md)를 참조하세요.

### <a name="connect-a-mobile-device-or-text-browser"></a>모바일 디바이스 또는 텍스트 브라우저 연결

Azure AD 인증 팝업을 표시할 수 없는 모바일 디바이스 또는 텍스트 기반 브라우저에 대해 Azure AD는 일회성 암호를 생성합니다. 암호를 복사하여 다른 환경에서 Azure AD 인증을 진행할 수 있습니다.

암호를 생성하려면 **aadauthcode** 를 **aaddevice** 로 바꿉니다. \<myAADDirectory\>을 Azure AD 도메인으로 바꿉니다. 예: `yourdomain.onmicrosoft.com`

```
connector.exe -remote <myBlockchainEndpoint>  -method aaddevice -tenant-id <myAADDirectory>
```

## <a name="next-steps"></a>다음 단계

Azure Blockchain Service의 데이터 보안에 대한 자세한 내용은 [Azure Blockchain Service 보안](data-security.md)을 참조하세요.