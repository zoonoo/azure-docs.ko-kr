---
title: Azure Blockchain Service 트랜잭션 노드 구성
description: Azure Blockchain Service 트랜잭션 노드를 구성 하는 방법
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: seal
manager: femila
ms.openlocfilehash: dffeb81ae1eb244c38639a1241c0581e6fcdf94a
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65027962"
---
# <a name="configure-azure-blockchain-service-transaction-nodes"></a>Azure Blockchain Service 트랜잭션 노드 구성

Azure Blockchain 서비스와 상호 작용, 이렇게 하면 블록 체인 멤버의 하나 이상의 트랜잭션 노드에 연결 하는 과정입니다.  트랜잭션 노드와 상호 작용을 하기 위해 액세스에 대 한 노드를 구성 해야 합니다.

## <a name="prerequisites"></a>필수 조건

* [Azure Blockchain 멤버를 만들려면](create-member.md)

## <a name="transaction-node-overview"></a>트랜잭션 노드 개요

트랜잭션 노드는 공용 끝점을 통해 Azure 블록 체인에 블록 체인 트랜잭션을 보내는 데 사용 됩니다. 기본 트랜잭션, 블록 체인에 등록 된 Ethereum 계정의 개인 키가 들어 노드와 같이 삭제할 수 없습니다.

기본 트랜잭션 노드 세부 정보를 보려면:

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. Azure Blockchain 서비스 멤버를 이동 합니다. 선택 **트랜잭션 노드**합니다.

    ![기본 트랜잭션 노드를 선택 합니다.](./media/configure-transaction-nodes/nodes.png)

    개요의 세부 정보는 공용 끝점 주소와 공개 키를 포함 합니다.

## <a name="create-transaction-node"></a>트랜잭션 노드 만들기

에 블록 체인 멤버에 10 개의 트랜잭션 노드의 총 9 개의 추가 트랜잭션 노드가까지 추가할 수 있습니다. 트랜잭션 노드를 추가 하 여 확장성을 향상 시킬 수도 있고 부하 분산할 수 있습니다. 예를 들어, 여러 클라이언트 응용 프로그램에 대 한 트랜잭션 노드 끝점이 있을 수 있습니다.

트랜잭션 노드를 추가 합니다.

1. Azure portal에서 Azure Blockchain 서비스 멤버를 이동 하 고 선택 **트랜잭션 노드 > 추가**합니다.
1. 새 트랜잭션 노드에 대 한 설정을 완료 합니다.

    ![트랜잭션 노드를 추가 합니다.](./media/configure-transaction-nodes/add-node.png)

    | 설정 | 설명 |
    |---------|-------------|
    | 이름 | 트랜잭션 노드 이름입니다. 트랜잭션 노드 끝점에 대 한 DNS 주소를 만드는 이름이 사용 됩니다. 예: `newnode-myblockchainmember.blockchain.azure.com`. 만들어진 후에 노드 이름을 변경할 수 없습니다. |
    | 암호 | 강력한 암호를 설정 합니다. 기본 인증을 사용 하 여 트랜잭션 노드 끝점에 액세스 하는 암호를 사용 합니다.

1. **만들기**를 선택합니다.

    새 트랜잭션 노드를 프로 비전 10 분 정도 걸립니다. 노드 추가 트랜잭션 비용이 발생 합니다. 비용에 대 한 자세한 내용은 참조 하세요. [Azure 가격 책정](https://aka.ms/ABSPricing)합니다.

## <a name="endpoints"></a>엔드포인트

트랜잭션 노드에 고유 DNS 이름 및 공용 끝점에 있어야합니다.

트랜잭션 노드 끝점 세부 정보를 보려면:

1. Azure portal에서 Azure Blockchain 서비스 멤버 트랜잭션 노드 중 하나로 이동 및 선택 **개요**합니다.

    ![엔드포인트](./media/configure-transaction-nodes/endpoints.png)

트랜잭션 노드 끝점이 안전 하 고 인증을 요구 합니다. Azure AD 인증을 HTTPS 기본 인증을 사용 하 고 HTTPS 또는 Websocket을 통해 액세스 키를 사용 하 여 SSL을 통해 트랜잭션 끝점에 연결할 수 있습니다.

### <a name="azure-active-directory-access-control"></a>Azure Active Directory 액세스 제어

Azure Blockchain 서비스 트랜잭션 노드 끝점은 Azure Active Directory (Azure AD) 인증을 지원합니다. 끝점에 Azure AD 사용자, 그룹 및 서비스 주체 액세스를 부여할 수 있습니다.

Azure에 부여할 엔드포인트에 AD 액세스 제어:

1. Azure portal에서 Azure Blockchain 서비스 멤버를 탐색 및 선택 **트랜잭션 노드 > 액세스 제어 (IAM) > 추가 > 역할 할당 추가**합니다.
1. 사용자, 그룹 또는 서비스 주체 (응용 프로그램 역할)에 대 한 새 역할 할당을 만듭니다.

    ![IAM 역할 추가](./media/configure-transaction-nodes/add-role.png)

    | 설정 | 액션(Action) |
    |---------|-------------|
    | 역할 | 선택 **소유자**하십시오 **참가자**, 또는 **판독기**합니다.
    | 다음에 대한 액세스 할당 | 선택 **Azure AD 사용자, 그룹 또는 서비스 주체**합니다.
    | 여기서 | 사용자, 그룹 또는 서비스 주체를 추가 하려는 검색 합니다.

1. 선택 **저장할** 역할 할당을 추가 합니다.

Azure AD access control에 대 한 자세한 내용은 참조 하세요. [RBAC 및 Azure portal을 사용 하 여 Azure 리소스에 대 한 액세스 관리](../../role-based-access-control/role-assignments-portal.md)

Azure AD 인증을 사용 하 여 연결 하는 방법에 대 한 세부 정보를 참조 하세요 [AAD 인증을 사용 하 여 노드에 연결할](configure-aad.md)합니다.

### <a name="basic-authentication"></a>기본 인증

HTTPS 기본 인증용 사용자 이름 및 암호 자격 증명 끝점에 HTTPS 요청의 헤더에 전달 됩니다.

Azure portal에서 트랜잭션 노드의 기본 인증 끝점 세부 정보를 볼 수 있습니다. Azure Blockchain 서비스 멤버 트랜잭션 노드 중 하나를 찾아 **기본 인증** 설정에서 합니다.

![기본 인증](./media/configure-transaction-nodes/basic.png)

사용자 이름 노드의 이름이 며 변경할 수 없습니다.

URL을 사용 하려면 대체 \<암호\> 노드 프로 비전 되었을 때 설정한 암호를 사용 하 여 합니다. 선택 하 여 암호를 업데이트할 수 있습니다 **암호 재설정**합니다.

### <a name="access-keys"></a>액세스 키

액세스 키 인증에 대 한 액세스 키를 끝점 URL에 포함 됩니다. 트랜잭션 노드 프로 비전 되 면 두 개의 액세스 키 생성 됩니다. 인증에 대 한 액세스 키 중 하나를 사용할 수 있습니다. 두 개의 키 사용을 변경 하 고 키를 회전 합니다.

트랜잭션 노드 액세스 키 정보를 볼 수 있으며 액세스 키를 포함 하는 끝점 주소를 복사. Azure Blockchain 서비스 멤버 트랜잭션 노드 중 하나를 찾아 **액세스 키** 설정에서 합니다.

### <a name="firewall-rules"></a>방화벽 규칙

방화벽 규칙을 사용 하 여 트랜잭션 노드 인증을 시도할 수 있는 IP 주소를 제한할 수 있습니다.  방화벽 규칙이 구성 되지 않은 트랜잭션 노드에 대 한, 모든 당사자가 액세스할 수 없습니다.  

트랜잭션 노드의 방화벽 규칙을 보려면 Azure Blockchain 서비스 멤버 트랜잭션 노드 중 하나로 이동 및 선택 **방화벽 규칙** 설정에서 합니다.

규칙 이름을 입력 하 여 방화벽 규칙을 추가할 수 있습니다, 시작 IP 주소 및 끝 IP 주소에 **방화벽 규칙** 표입니다.

![방화벽 규칙](./media/configure-transaction-nodes/firewall-rules.png)

사용 하려면:

* **단일 IP 주소:** 시작 및 끝 IP 주소에 대 한 동일한 IP 주소를 구성 합니다.
* **IP 주소 범위:** 시작 및 끝 IP 주소 범위를 구성 합니다. 예를 들어 10.221.34.0에서 시작 하 고 10.221.34.255 종료 범위는 전체 10.221.34.xxx 서브넷을 수 있습니다.
* **모든 IP 주소를 허용 합니다.** 시작 IP 주소가 0.0.0.0 및 255.255.255.255 끝 IP 주소를 구성 합니다.

## <a name="connection-strings"></a>연결 문자열

Basic에 대 한 트랜잭션 노드에 대 한 연결 문자열 구문은 제공 됩니다 인증 또는 액세스 키를 사용 합니다. HTTPS 및 WebSockets를 통한 액세스 키를 포함 하는 연결 문자열 제공 됩니다.

트랜잭션 노드의 연결 문자열 보기를 끝점 주소를 복사 합니다. Azure Blockchain 서비스 멤버 트랜잭션 노드 중 하나를 찾아 **연결 문자열** 설정에서 합니다.

![연결 문자열](./media/configure-transaction-nodes/connection-strings.png)

## <a name="sample-code"></a>샘플 코드

샘플 코드는 신속 하 게 Web3, Nethereum, Web3js, 및 Truffle 통해 트랜잭션 노드로 연결을 사용 하도록 제공 됩니다.

트랜잭션 노드의 샘플 연결 코드를 볼 수 있으며 인기 있는 개발자 도구를 사용 하 여 사용 하도록 복사. Azure Blockchain 서비스 멤버 트랜잭션 노드 중 하나를 찾아 **샘플 코드** 설정에서 합니다.

사용 하려는 코드 샘플을 보려면 Web3 또는 Nethereum 탭을 선택 합니다.

![샘플 코드](./media/configure-transaction-nodes/sample-code.png)

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure CLI를 사용 하 여 트랜잭션 노드 구성](manage-cli.md)
