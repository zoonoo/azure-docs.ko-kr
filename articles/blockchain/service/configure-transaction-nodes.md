---
title: Azure Blockchain Service 트랜잭션 노드 구성
description: Azure Blockchain Service 트랜잭션 노드를 구성하는 방법
ms.date: 05/11/2021
ms.topic: how-to
ms.reviewer: janders
ms.openlocfilehash: 436c7721bac29e8a18a333e385f12a70e0701ba5
ms.sourcegitcommit: 32ee8da1440a2d81c49ff25c5922f786e85109b4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/12/2021
ms.locfileid: "122642102"
---
# <a name="configure-azure-blockchain-service-transaction-nodes"></a>Azure Blockchain Service 트랜잭션 노드 구성

트랜잭션 노드는 퍼블릭 엔드포인트를 통해 Azure Blockchain Service에 블록체인 트랜잭션을 보내는 데 사용됩니다. 기본 트랜잭션 노드에는 블록체인에 등록된 Ethereum 계정의 프라이빗 키가 포함되어 있으므로 삭제할 수 없습니다.

[!INCLUDE [Retirement note](./includes/retirement.md)]

기본 트랜잭션 노드 세부 정보를 보려면 다음을 수행합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. Azure Blockchain Service 멤버로 이동합니다. **트랜잭션 노드** 를 선택합니다.

    ![기본 트랜잭션 노드 선택](./media/configure-transaction-nodes/nodes.png)

    개요 세부 정보에는 퍼블릭 엔드포인트 주소와 퍼블릭 키가 포함됩니다.

## <a name="create-transaction-node"></a>트랜잭션 노드 만들기

총 10개의 트랜잭션 노드를 위해 블록체인 구성원에 최대 9개의 추가 트랜잭션 노드를 추가할 수 있습니다. 트랜잭션 노드를 추가하여 확장성을 늘리거나 부하를 분산할 수 있습니다. 예를 들어 다른 클라이언트 응용 프로그램에 대한 트랜잭션 노드 엔드포인트가 있을 수 있습니다.

트랜잭션 노드를 추가하려면:

1. Azure portal에서 Azure Blockchain Service 멤버로 이동하여 **트랜잭션 노드 > 추가** 를 선택합니다.
1. 새 트랜잭션 노드에 대한 설정을 완료합니다.

    ![트랜잭션 노드 추가하기](./media/configure-transaction-nodes/add-node.png)

    | 설정 | 설명 |
    |---------|-------------|
    | 이름 | 트랜잭션 노드 이름입니다. 트랜잭션 노드 엔드포인트에 대한 DNS 주소를 만드는 데 사용됩니다. 예들 들어 `newnode-myblockchainmember.blockchain.azure.com`입니다. 노드 이름은 만들고 나면 변경할 수 없습니다. |
    | 암호 | 강력한 암호를 사용합니다. 암호를 사용하여 기본 인증으로 트랜잭션 노드 엔드포인트에 액세스합니다.

1. **만들기** 를 선택합니다.

    새 트랜잭션 노드를 프로비저닝하는 데 약 10분이 걸립니다. 트랜잭션 노드를 추가하면 비용이 발생합니다. 비용과 관련된 자세한 사항은 [Azure 가격 책정](https://aka.ms/ABSPricing)을 참조하세요.

## <a name="endpoints"></a>엔드포인트

트랜잭션 노드에는 고유한 DNS 이름 및 퍼블릭 엔드포인트가 있습니다.

트랜잭션 노드의 엔드포인트 세부 정보를 보려면 다음을 수행합니다.

1. Azure Portal에서 Azure Blockchain Service 구성원 트랜잭션 노드 중 하나로 이동하고 **개요** 를 선택합니다.

    ![블록체인 구성원의 트랜잭션 노드에 대한 개요를 보여 주는 화면 캡처](./media/configure-transaction-nodes/endpoints.png)

트랜잭션 노드 엔드포인트는 안전하며 인증을 요구합니다. Azure AD 인증, HTTPS 기본 인증 및 HTTPS를 통한 액세스 키 또는 TLS를 통한 Websocket을 통해 트랜잭션 엔드포인트에 연결할 수 있습니다.

### <a name="azure-active-directory-access-control"></a>Azure Active Directory 액세스 제어

Azure Blockchain Service 트랜잭션 노드 엔드포인트는 Azure Active Directory(Azure AD) 인증을 지원합니다. Azure AD 사용자, 그룹 및 서비스 사용자에게 엔드포인트에 대한 액세스 권한을 부여할 수 있습니다.

엔드포인트에 Azure AD 액세스 제어를 부여하려면:

1. Azure Portal에서 Azure Blockchain Service 구성원으로 이동한 후 **트랜잭션 노드 > IAM(액세스 제어) > 추가 > 역할 할당 추가** 를 선택합니다.
1. 사용자, 그룹 또는 서비스 사용자(애플리케이션 역할)에 대한 새 역할 할당을 만듭니다.

    ![IAM 역할 추가하기](./media/configure-transaction-nodes/add-role.png)

    | 설정 | 작업 |
    |---------|-------------|
    | 역할 | **Owner**, **Contributor**, 또는 **Reader** 를 선택합니다.
    | 다음에 대한 액세스 할당 | **Azure AD 사용자, 그룹 또는 서비스 보안 주체** 를 선택합니다.
    | 선택 | 추가하려는 사용자, 그룹 또는 서비스 주체를 검색합니다.

1. **저장** 을 선택하여 역할 할당을 추가합니다.

Azure AD 액세스 제어에 대한 자세한 내용은 [Azure Portal을 사용하여 Azure 역할 할당](../../role-based-access-control/role-assignments-portal.md)을 참조하세요.

Azure AD 인증을 사용하여 연결하는 방법에 대한 세부 정보는 [AAD 인증을 사용하여 노드에 연결](configure-aad.md)을 참조하세요.

### <a name="basic-authentication"></a>기본 인증

HTTPS 기본 인증의 경우, 사용자 이름 및 암호 자격 증명은 요청의 HTTPS 헤더에서 엔드포인트로 전달됩니다.

Azure Portal에서 트랜잭션 노드의 기본 인증 엔드포인트 세부 정보를 볼 수 있습니다. Azure Blockchain Service 구성원 트랜잭션 노드 중 하나로 이동하고 설정에서 **기본 인증** 을 선택합니다.

![기본 인증](./media/configure-transaction-nodes/basic.png)

사용자 이름은 노드의 이름이며 변경할 수 없습니다.

URL을 사용하려면 \<password\>을 노드가 프로비저닝됐을 때 설정된 암호로 바꿉니다. **암호 재설정** 을 선택하여 암호를 업데이트할 수 있습니다.

### <a name="access-keys"></a>액세스 키

액세스 키 인증의 경우 액세스 키가 엔드포인트 URL에 포함됩니다. 트랜잭션 노드가 프로비저닝되면 두 개의 액세스 키가 생성됩니다. 인증에는 두 키 모두 사용 가능합니다. 두 개의 키를 사용하여 키를 변경하고 회전할 수 있습니다.

트랜잭션 노드의 액세스 키 세부 정보를 확인하고 액세스 키를 포함하는 엔드포인트 주소를 복사할 수 있습니다. Azure Blockchain Serivce 구성원 트랜잭션 노드 중 하나로 이동하고 설정에서 **액세스 키** 를 선택합니다.

### <a name="firewall-rules"></a>방화벽 규칙

방화벽 규칙을 사용하면 트랜잭션 노드에 인증을 시도할 수 있는 IP 주소를 제한할 수 있습니다.  트랜잭션 노드에 대해 구성된 방화벽 규칙이 없는 경우 어떤 파티도 액세스할 수 없습니다.

트랜잭션 노드의 방화벽 규칙을 보려면 Azure Blockchain Service 구성원 트랜잭션 노드 중 하나로 이동하고 설정에서 **방화벽 규칙** 을 선택합니다.

**방화벽 규칙** 그리드에서 규칙 이름, 시작 IP 주소 및 끝 IP 주소를 입력하여 방화벽 규칙을 추가할 수 있습니다.

![방화벽 규칙](./media/configure-transaction-nodes/firewall-rules.png)

사용하도록 설정하려면:

* **단일 IP 주소:** 시작 IP 주소와 끝 IP 주소에 대해 동일한 IP 주소를 구성합니다.
* **IP 주소 범위:** 시작 IP 주소와 끝 IP 주소 범위를 구성합니다. 예를 들어, 10.221.34.0에서 시작하여 10.221.34.255에서 끝나는 범위는 전체 10.221.34.xxx 서브넷을 사용하도록 설정합니다.
* **모든 IP 주소 허용:** 시작 IP 주소를 0.0.0.0으로 구성하고 끝 IP 주소를 255.255.255.255로 구성합니다.

## <a name="connection-strings"></a>연결 문자열

트랜잭션 노드에 대한 연결 문자열 구문은 기본 인증 또는 액세스 키를 사용하기 위해 제공됩니다. HTTPS 및 WebSockets를 통한 액세스 키를 포함한 연결 문자열이 제공됩니다.

트랜잭션 노드의 연결 문자열을 보고 엔드포인트 주소를 복사할 수 있습니다. Azure Blockchain Service 구성원 트랜잭션 노드 중 하나로 이동하고 설정에서 **연결 문자열** 을 선택합니다.

![연결 문자열](./media/configure-transaction-nodes/connection-strings.png)

## <a name="sample-code"></a>샘플 코드

샘플 코드는 Web3, Nethereum, Web3js 및 Truffle을 통해 트랜잭션 노드에 대한 연결을 신속하게 설정하기 위해 제공됩니다.

트랜잭션 노드의 샘플 연결 코드를 보고 인기 개발자 도구에서 사용할 수 있도록 복사할 수 있습니다. Azure Blockchain Service 구성원 트랜잭션 노드 중 하나로 이동하고 설정에서 **샘플 코드** 를 선택합니다.

Web3, Nethereum, Truffle 또는 Web3j 탭을 선택하여 사용하려는 코드 샘플을 확인합니다.

![예제 코드](./media/configure-transaction-nodes/sample-code.png)

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure CLI를 사용하여 트랜잭션 노드 구성](manage-cli.md)
