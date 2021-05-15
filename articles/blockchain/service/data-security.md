---
title: Azure Blockchain Service 보안
description: Azure Blockchain Service 데이터 액세스 및 보안 개념
ms.date: 11/22/2019
ms.topic: conceptual
ms.reviewer: janders
ms.openlocfilehash: 989d9f2afad30517a85185878d694c0b6640e987
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "80879601"
---
# <a name="azure-blockchain-service-security"></a>Azure Blockchain Service 보안

Azure Blockchain Service는 데이터를 안전하고 사용 가능하게 유지하기 위해 여러 Azure 기능을 사용합니다. 데이터의 보안은 격리, 암호화, 인증을 사용하여 유지됩니다.

## <a name="isolation"></a>격리

Azure Blockchain Service 리소스는 프라이빗 가상 네트워크에서 격리됩니다. 각 트랜잭션 및 유효성 검사 노드는 VM(가상 머신)입니다. 하나의 가상 네트워크의 VM은 다른 가상 네트워크의 VM과 직접 통신할 수 없습니다. 격리는 가상 네트워크 내에서 통신이 프라이빗 상태로 유지되도록 합니다. Azure 가상 네트워크 격리에 대한 자세한 내용은 [Azure 퍼블릭 클라우드에서 격리](../../security/fundamentals/isolation-choices.md#networking-isolation)를 참조하세요.

![VNET 다이어그램](./media/data-security/vnet.png)

## <a name="encryption"></a>암호화

사용자 데이터는 Azure 스토리지에 저장됩니다. 사용자 데이터는 사용 중이든 아니든 보안 및 기밀성을 위해 암호화됩니다. 자세한 내용은 [Azure Storage 보안 가이드](../../storage/blobs/security-recommendations.md)를 참조하세요.

## <a name="authentication"></a>인증

트랜잭션은 RPC 엔드포인트를 통해 블록체인 노드로 전송될 수 있습니다. 클라이언트는 사용자 인증을 처리하고 TLS를 통해 데이터를 암호화하는 역방향 프록시 서버를 사용하여 트랜잭션 노드와 통신합니다.

![인증 다이어그램](./media/data-security/authentication.png)

RPC 액세스에는 세 가지 인증 모드가 있습니다.

### <a name="basic-authentication"></a>기본 인증

기본 인증에서는 사용자 이름 및 암호를 포함하는 HTTP 인증 헤더를 사용합니다. 사용자 이름은 블록체인 노드의 이름입니다. 멤버 또는 노드를 프로비저닝하는 동안 암호를 설정합니다. Azure Portal 또는 CLI를 사용하여 암호를 변경할 수 있습니다.

### <a name="access-keys"></a>액세스 키

액세스 키는 엔드포인트 URL에 포함된 임의로 생성된 문자열을 사용합니다. 키 회전을 사용하는 데 도움이 되는 두 가지 액세스 키가 있습니다. Azure Portal 및 CLI에서 키를 다시 생성할 수 있습니다.

### <a name="azure-active-directory"></a>Azure Active Directory

Azure AD(Azure Active Directory)는 Azure AD 사용자 자격 증명을 사용하여 Azure AD에서 사용자를 인증하는 클레임 기반 인증 메커니즘을 사용합니다. Azure AD는 클라우드 기반 ID 관리를 제공하며 고객이 전체 기업에서 단일 ID를 사용하고 클라우드의 애플리케이션에 액세스할 수 있도록 합니다. Azure Blockchain Service는 ID 페더레이션, Single Sign-On, 다단계 인증을 사용할 수 있도록 하는 Azure AD와 통합됩니다. 조직의 사용자, 그룹, 애플리케이션 역할을 블록체인 멤버 및 노드 액세스에 할당할 수 있습니다.

Azure AD 클라이언트 프록시는 [GitHub](https://github.com/Microsoft/azure-blockchain-connector/releases)에서 사용할 수 있습니다. 클라이언트 프록시는 사용자를 Azure AD 로그인 페이지로 안내하고 인증에 성공하면 전달자 토큰을 가져옵니다. 그런 다음 사용자는 Geth 또는 Truffle과 같은 Ethereum 클라이언트 애플리케이션을 클라이언트 프록시의 엔드포인트에 연결합니다. 마지막으로 트랜잭션이 제출되면 클라이언트 프록시는 HTTP 헤더에 전달자 토큰을 삽입하고 역방향 프록시는 OAuth 프로토콜을 사용하여 토큰의 유효성을 검사합니다.

## <a name="keys-and-ethereum-accounts"></a>키 및 Ethereum 계정

Azure Blockchain Service 멤버를 프로비저닝할 때 Ethereum 계정 및 퍼블릭 키와 프라이빗 키 쌍이 생성됩니다. 프라이빗 키를 사용하여 블록체인으로 트랜잭션을 보냅니다. Ethereum 계정은 퍼블릭 키 해시의 마지막 20바이트입니다. Ethereum 계정은 지갑이라고도 합니다.

프라이빗 및 퍼블릭 키 쌍은 JSON 형식의 키 파일로 저장됩니다. 프라이빗 키는 블록체인 원장 서비스를 만들 때 입력한 암호를 사용하여 암호화됩니다.

프라이빗 키는 트랜잭션에 디지털로 서명을 하는 데 사용됩니다. 프라이빗 블록체인에서 프라이빗 키로 서명된 스마트 계약은 서명자의 ID를 나타냅니다. 서명의 유효성을 확인하기 위해 받는 사람은 서명자의 퍼블릭 키를 서명에서 컴퓨팅된 주소와 비교할 수 있습니다.

Constellation 키는 Quorum 노드를 고유하게 식별하는 데 사용됩니다. Constellation 키는 노드 프로비저닝 시 생성되고 Quorum에서 프라이빗 트랜잭션의 privateFor 매개 변수에 지정됩니다.

## <a name="next-steps"></a>다음 단계

[Azure Blockchain Service에 대한 Azure Active Directory 액세스를 구성하는 방법](configure-aad.md)을 참조하세요.
