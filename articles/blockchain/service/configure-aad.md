---
title: Azure Active Directory 액세스 구성 - Azure 블록 체인 서비스
description: Azure Active Directory 액세스를 사용하여 Azure 블록 체인 서비스를 구성하는 방법
ms.date: 11/22/2019
ms.topic: article
ms.reviewer: janders
ms.openlocfilehash: 682ab282036fcd592e66942d08a84cdce46d8915
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74455856"
---
# <a name="how-to-configure-azure-active-directory-access-for-azure-blockchain-service"></a>Azure 블록 체인 서비스에 대한 Azure Active Directory 액세스를 구성하는 방법

이 문서에서는 Azure Active Directory(Azure AD) 사용자, 그룹 또는 응용 프로그램 아이디를 사용하여 액세스 권한을 부여하고 Azure 블록 체인 서비스 노드에 연결하는 방법을 배웁니다.

Azure AD는 클라우드 기반 ID 관리를 제공하며 전체 엔터프라이즈에서 단일 ID를 사용하고 Azure의 응용 프로그램에 액세스할 수 있습니다. Azure 블록 체인 서비스는 Azure AD와 통합되어 있으며 ID 페더레이션, 단일 사인온 및 다단계 인증과 같은 이점을 제공합니다.

## <a name="prerequisites"></a>사전 요구 사항

* [Azure 포털을 사용하여 블록 체인 멤버 만들기](create-member.md)

## <a name="grant-access"></a>액세스 권한 부여

멤버 수준과 노드 수준 모두에서 액세스 권한을 부여할 수 있습니다. 멤버 수준에서 액세스 권한을 부여하면 멤버 아래의 모든 노드에 대한 액세스 권한이 부여됩니다.

### <a name="grant-member-level-access"></a>권한 부여 구성원 수준 액세스

멤버 수준에서 액세스 권한을 부여합니다.

1. [Azure 포털에](https://portal.azure.com)로그인합니다.
1. 액세스 **제어(IAM) > 역할 할당 추가>** 로 이동합니다.
1. 블록 **체인 회원 노드 액세스 (미리 보기)** 역할을 선택 하 고 에 대 한 액세스를 부여 하려는 Azure AD ID 개체를 추가 합니다. Azure AD ID 개체는 다음과 같은 것일 수 있습니다.

    | Azure AD 개체 | 예제 |
    |-----------------|---------|
    | Azure AD 사용자   | `kim@contoso.onmicrosoft.com` |
    | Azure AD 그룹  | `sales@contoso.onmicrosoft.com` |
    | 애플리케이션 UI  | `13925ab1-4161-4534-8d18-812f5ca1ab1e` |

    ![역할 할당 추가](./media/configure-aad/add-role-assignment.png)

1. **저장**을 선택합니다.

### <a name="grant-node-level-access"></a>권한 부여 노드 수준 액세스

노드 보안으로 이동하여 노드 수준에서 액세스 권한을 부여하고 액세스 권한을 부여하려는 노드 이름을 클릭할 수 있습니다.

블록 체인 회원 노드 액세스 (미리 보기) 역할을 선택 하 고 에 대 한 액세스를 부여 하려는 Azure AD ID 개체를 추가 합니다.

자세한 내용은 [Azure 블록 체인 서비스 트랜잭션 노드 구성을](configure-transaction-nodes.md#azure-active-directory-access-control)참조하십시오.

## <a name="connect-using-azure-blockchain-connector"></a>Azure 블록체인 커넥터를 사용하여 연결

[GitHub에서 Azure 블록 체인 커넥터를](https://github.com/Microsoft/azure-blockchain-connector/)다운로드하거나 복제합니다.

```bash
git clone https://github.com/Microsoft/azure-blockchain-connector.git
```

**readme의** 빠른 시작 섹션을 따라 소스 코드에서 커넥터를 빌드합니다.

### <a name="connect-using-an-azure-ad-user-account"></a>Azure AD 사용자 계정을 사용하여 연결

1. 다음 명령을 실행하여 Azure AD 사용자 계정을 사용하여 인증합니다. \<myAADDirectory를\> Azure AD 도메인으로 바꿉습니다. `yourdomain.onmicrosoft.com`)을 입력합니다.

    ```
    connector.exe -remote <myMemberName>.blockchain.azure.com:3200 -method aadauthcode -tenant-id <myAADDirectory> 
    ```

1. Azure AD는 자격 증명을 묻는 메시지를 표시합니다.
1. 사용자 이름과 암호로 로그인합니다.
1. 인증이 성공하면 로컬 프록시가 블록 체인 노드에 연결됩니다. 이제 로컬 끝점에 Geth 클라이언트를 연결할 수 있습니다.

    ```bash
    geth attach http://127.0.0.1:3100
    ```

### <a name="connect-using-an-application-id"></a>응용 프로그램 ID를 사용하여 연결

대부분의 응용 프로그램은 Azure AD 사용자 계정 대신 응용 프로그램 ID를 사용하여 Azure AD로 인증합니다.

응용 프로그램 ID를 사용하여 노드에 연결하려면 **aadauthcode를** **aadclient로**바꿉니다.

```
connector.exe -remote <myBlockchainEndpoint>  -method aadclient -client-id <myClientID> -client-secret "<myClientSecret>" -tenant-id <myAADDirectory>
```

| 매개 변수 | 설명 |
|-----------|-------------|
| 테넌트 ID | Azure AD 도메인, 예를 들어,`yourdomain.onmicrosoft.com`
| 클라이언트 ID | Azure AD에 등록된 응용 프로그램의 클라이언트 ID
| 클라이언트 비밀 | Azure AD에 등록된 응용 프로그램의 클라이언트 보안

Azure AD에서 응용 프로그램을 등록하는 방법에 대한 자세한 내용은 [포털을 사용하여 리소스에 액세스할 수 있는 Azure AD 응용 프로그램 및 서비스 주체를 만드는 방법을](../../active-directory/develop/howto-create-service-principal-portal.md) 참조하세요.

### <a name="connect-a-mobile-device-or-text-browser"></a>모바일 장치 또는 텍스트 브라우저 연결

Azure AD 인증 팝업 표시가 불가능한 모바일 장치 또는 텍스트 기반 브라우저의 경우 Azure AD는 일회용 암호를 생성합니다. 암호를 복사하고 다른 환경에서 Azure AD 인증을 진행할 수 있습니다.

암호를 생성하려면 **aadauthcode를** **aaddevice로**바꿉니다. \<myAADDirectory를\> Azure AD 도메인으로 바꿉습니다. `yourdomain.onmicrosoft.com`)을 입력합니다.

```
connector.exe -remote <myBlockchainEndpoint>  -method aaddevice -tenant-id <myAADDirectory>
```

## <a name="next-steps"></a>다음 단계

Azure 블록 체인 서비스의 데이터 보안에 대한 자세한 내용은 [Azure 블록 체인 서비스 보안을](data-security.md)참조하십시오.