---
title: Azure 블록 체인 서비스 트랜잭션 노드 구성
description: Azure 블록 체인 서비스 트랜잭션 노드를 구성하는 방법
ms.date: 11/20/2019
ms.topic: article
ms.reviewer: janders
ms.openlocfilehash: 4a9a4f660dd171e65b600ec4cd66714ca476b091
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79252260"
---
# <a name="configure-azure-blockchain-service-transaction-nodes"></a>Azure 블록 체인 서비스 트랜잭션 노드 구성

트랜잭션 노드는 공용 끝점을 통해 Azure 블록 체인 서비스에 블록 체인 트랜잭션을 보내는 데 사용됩니다. 기본 트랜잭션 노드에는 블록 체인에 등록 된 이더리움 계정의 개인 키가 포함되어 있으며 삭제할 수 없습니다.

기본 트랜잭션 노드 세부 정보를 보려면 다음을 수행합니다.

1. [Azure 포털에](https://portal.azure.com)로그인합니다.
1. Azure Blockchain Service 멤버로 이동합니다. **트랜잭션 노드를 선택합니다.**

    ![기본 트랜잭션 노드 선택](./media/configure-transaction-nodes/nodes.png)

    개요 세부 정보에는 공용 끝점 주소 및 공개 키가 포함됩니다.

## <a name="create-transaction-node"></a>트랜잭션 노드 만들기

총 10개의 트랜잭션 노드에 대해 최대 9개의 트랜잭션 노드를 블록체인 구성원에 추가할 수 있습니다. 트랜잭션 노드를 추가하면 확장성을 높이거나 부하를 분산할 수 있습니다. 예를 들어 다른 클라이언트 응용 프로그램에 대한 트랜잭션 노드 끝점을 가질 수 있습니다.

트랜잭션 노드를 추가하려면 다음을 수행합니다.

1. Azure 포털에서 Azure 블록 체인 서비스 멤버로 이동하여 **추가 > 트랜잭션 노드를**선택합니다.
1. 새 트랜잭션 노드에 대한 설정을 완료합니다.

    ![트랜잭션 노드 추가](./media/configure-transaction-nodes/add-node.png)

    | 설정 | 설명 |
    |---------|-------------|
    | 이름 | 트랜잭션 노드 이름입니다. 트랜잭션 노드 엔드포인트에 대한 DNS 주소를 만드는 데 사용됩니다. `newnode-myblockchainmember.blockchain.azure.com`)을 입력합니다. 노드 이름은 생성된 후에는 변경할 수 없습니다. |
    | 암호 | 강력한 암호를 설정합니다. 암호를 사용하여 기본 인증을 사용하여 트랜잭션 노드 끝점에 액세스합니다.

1. **만들기**를 선택합니다.

    새 트랜잭션 노드를 프로비저닝하는 데 약 10분이 걸립니다. 추가 트랜잭션 노드에는 비용이 발생합니다. 비용에 대한 자세한 내용은 [Azure 가격 책정](https://aka.ms/ABSPricing)을 참조하십시오.

## <a name="endpoints"></a>엔드포인트

트랜잭션 노드에는 고유한 DNS 이름과 공용 끝점이 있습니다.

트랜잭션 노드의 끝점 세부 정보를 보려면 다음을 수행합니다.

1. Azure 포털에서 Azure 블록 체인 서비스 구성원 트랜잭션 노드 중 하나로 이동하여 **개요를**선택합니다.

    ![엔드포인트](./media/configure-transaction-nodes/endpoints.png)

트랜잭션 노드 끝점은 안전하며 인증이 필요합니다. Azure AD 인증, HTTPS 기본 인증을 사용하고 SSL을 통해 HTTPS 또는 Websocket을 통해 액세스 키를 사용하여 트랜잭션 끝점에 연결할 수 있습니다.

### <a name="azure-active-directory-access-control"></a>Azure Active Directory 액세스 제어

Azure 블록 체인 서비스 트랜잭션 노드 끝점은 Azure Active Directory (Azure AD) 인증을 지원합니다. Azure AD 사용자, 그룹 및 서비스 주체 액세스 권한을 엔드포인트에 부여할 수 있습니다.

끝점에 Azure AD 액세스 제어를 부여하려면 다음을 수행하십시오.

1. Azure 포털에서 Azure Blockchain Service 구성원으로 이동하여 **IAM(액세스 제어)> 트랜잭션 노드를 선택 >하여 역할 할당 추가>** 추가하기.
1. 사용자, 그룹 또는 서비스 주체(응용 프로그램 역할)에 대한 새 역할 할당을 만듭니다.

    ![IAM 역할 추가](./media/configure-transaction-nodes/add-role.png)

    | 설정 | 작업 |
    |---------|-------------|
    | 역할 | **소유자,** **기여자**또는 **독자를**선택합니다.
    | 다음에 대한 액세스 할당 | **Azure AD 사용자, 그룹 또는 서비스 주체를 선택합니다.**
    | 선택 | 추가할 사용자, 그룹 또는 서비스 주체를 검색합니다.

1. 역할 할당을 추가하려면 **저장을** 선택합니다.

Azure AD 액세스 제어에 대한 자세한 내용은 [RBAC 및 Azure 포털을 사용하여 Azure 리소스에 대한 액세스 관리를](../../role-based-access-control/role-assignments-portal.md) 참조하세요.

Azure AD 인증을 사용하여 연결하는 방법에 대한 자세한 내용은 [AAD 인증을 사용하여 노드에 연결합니다.](configure-aad.md)

### <a name="basic-authentication"></a>기본 인증

HTTPS 기본 인증의 경우 사용자 이름과 암호 자격 증명은 요청의 HTTPS 헤더에 엔드포인트로 전달됩니다.

Azure 포털에서 트랜잭션 노드의 기본 인증 끝점 세부 정보를 볼 수 있습니다. Azure 블록 체인 서비스 구성원 트랜잭션 노드 중 하나로 이동 하 고 설정에서 **기본 인증을** 선택 합니다.

![기본 인증](./media/configure-transaction-nodes/basic.png)

사용자 이름은 노드의 이름이며 변경할 수 없습니다.

URL을 사용하려면 \<노드가 프로비전될 때 암호를\> 암호 집합으로 바꿉습니다. 암호 **재설정을**선택하여 암호를 업데이트할 수 있습니다.

### <a name="access-keys"></a>액세스 키

액세스 키 인증의 경우 액세스 키가 끝점 URL에 포함됩니다. 트랜잭션 노드가 프로비전되면 두 개의 액세스 키가 생성됩니다. 두 액세스 키 중 하나를 인증에 사용할 수 있습니다. 두 개의 키를 사용하면 키를 변경하고 회전할 수 있습니다.

트랜잭션 노드의 액세스 키 세부 정보를 보고 액세스 키가 포함된 끝점 주소를 복사할 수 있습니다. Azure 블록 체인 서비스 구성원 트랜잭션 노드 중 하나로 이동하고 설정에서 **액세스 키를 선택합니다.**

### <a name="firewall-rules"></a>방화벽 규칙

방화벽 규칙을 사용하면 트랜잭션 노드에 대한 인증을 시도할 수 있는 IP 주소를 제한할 수 있습니다.  트랜잭션 노드에 대해 방화벽 규칙이 구성되지 않은 경우 어떤 당사자도 액세스할 수 없습니다.  

트랜잭션 노드의 방화벽 규칙을 보려면 Azure Blockchain Service 구성원 트랜잭션 노드 중 하나로 이동하여 설정에서 **방화벽 규칙을** 선택합니다.

방화벽 규칙 그리드에 규칙 이름 입력, IP 주소 시작 및 끝 IP 주소를 입력하여 **방화벽 규칙을** 추가할 수 있습니다.

![방화벽 규칙](./media/configure-transaction-nodes/firewall-rules.png)

사용하려면 다음을 수행하십시오.

* **단일 IP 주소:** 시작 및 종료 IP 주소에 대해 동일한 IP 주소를 구성합니다.
* **IP 주소 범위:** 시작 및 종료 IP 주소 범위를 구성합니다. 예를 들어 10.221.34.0에서 시작하여 10.221.34.255로 끝나는 범위는 전체 10.221.34.xxx 서브넷을 활성화합니다.
* **모든 IP 주소 허용:** 시작 IP 주소를 0.0.0.0으로 구성하고 종료 IP 주소를 255.255.255.255로 구성합니다.

## <a name="connection-strings"></a>연결 문자열

트랜잭션 노드에 대한 연결 문자열 구문은 기본 인증 또는 액세스 키 사용에 대해 제공됩니다. HTTPS 및 WebSocket을 통해 액세스 키를 포함한 연결 문자열이 제공됩니다.

트랜잭션 노드의 연결 문자열을 보고 끝점 주소를 복사할 수 있습니다. Azure 블록 체인 서비스 구성원 트랜잭션 노드 중 하나로 이동하고 설정에서 **연결 문자열을 선택합니다.**

![연결 문자열](./media/configure-transaction-nodes/connection-strings.png)

## <a name="sample-code"></a>예제 코드

샘플 코드는 Web3, Nethereum, Web3js 및 트러플을 통해 트랜잭션 노드에 빠르게 연결할 수 있도록 제공됩니다.

트랜잭션 노드의 샘플 연결 코드를 보고 복사하여 인기 있는 개발자 도구와 함께 사용할 수 있습니다. Azure 블록 체인 서비스 구성원 트랜잭션 노드 중 하나로 이동하여 설정에서 **샘플 코드를** 선택합니다.

사용하려는 코드 샘플을 보려면 Web3, 네테리움, 트러플 또는 Web3j 탭을 선택합니다.

![예제 코드](./media/configure-transaction-nodes/sample-code.png)

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure CLI를 사용하여 트랜잭션 노드 구성](manage-cli.md)
