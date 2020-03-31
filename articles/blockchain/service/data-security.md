---
title: Azure 블록체인 서비스 보안
description: Azure 블록 체인 서비스 데이터 액세스 및 보안 개념
ms.date: 11/22/2019
ms.topic: conceptual
ms.reviewer: janders
ms.openlocfilehash: 06bf4e0fa4037b07505a4f816fc7af56c14576d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75982236"
---
# <a name="azure-blockchain-service-security"></a>Azure 블록체인 서비스 보안

Azure 블록 체인 서비스는 여러 Azure 기능을 사용하여 데이터를 안전하게 유지하고 사용할 수 있습니다. 데이터는 격리, 암호화 및 인증을 사용하여 보호됩니다.

## <a name="isolation"></a>격리

Azure 블록 체인 서비스 리소스는 개인 가상 네트워크에서 격리됩니다. 각 트랜잭션 및 유효성 검사 노드는 가상 시스템(VM)입니다. 하나의 가상 네트워크의 VM은 다른 가상 네트워크의 VM과 직접 통신할 수 없습니다. 격리를 통해 가상 네트워크 내에서 통신이 비공개로 유지됩니다. Azure 가상 네트워크 격리에 대한 자세한 내용은 [Azure 공용 클라우드에서 격리를](../../security/fundamentals/isolation-choices.md#networking-isolation)참조하십시오.

![VNET 다이어그램](./media/data-security/vnet.png)

## <a name="encryption"></a>암호화

사용자 데이터는 Azure 저장소에 저장됩니다. 사용자 데이터는 보안 및 기밀유지를 위해 이동 중및 미사용 으로 암호화됩니다. 자세한 내용은 Azure [저장소 보안 가이드를](../../storage/blobs/security-recommendations.md)참조하십시오.

## <a name="authentication"></a>인증

거래는 RPC 엔드포인트를 통해 블록체인 노드로 전송될 수 있습니다. 클라이언트는 사용자 인증을 처리하고 SSL을 통해 데이터를 암호화하는 역방향 프록시 서버를 사용하여 트랜잭션 노드와 통신합니다.

![인증 다이어그램](./media/data-security/authentication.png)

RPC 액세스에 대한 인증에는 세 가지 모드가 있습니다.

### <a name="basic-authentication"></a>기본 인증

기본 인증은 사용자 이름과 암호를 포함하는 HTTP 인증 헤더를 사용합니다. 사용자 이름은 블록 체인 노드의 이름입니다. 암호는 구성원 또는 노드를 프로비전하는 동안 설정됩니다. Azure 포털 또는 CLI를 사용하여 암호를 변경할 수 있습니다.

### <a name="access-keys"></a>액세스 키

액세스 키는 끝점 URL에 포함된 임의로 생성된 문자열을 사용합니다. 두 개의 액세스 키가 키 회전을 활성화하는 데 도움이 됩니다. Azure 포털 및 CLI에서 키를 다시 생성할 수 있습니다.

### <a name="azure-active-directory"></a>Azure Active Directory

Azure Active Directory(Azure AD)는 Azure AD 사용자 자격 증명을 사용하여 Azure AD에 의해 인증되는 클레임 기반 인증 메커니즘을 사용합니다. Azure AD는 클라우드 기반 ID 관리를 제공하며 고객이 전체 엔터프라이즈에서 단일 ID를 사용하고 클라우드의 응용 프로그램에 액세스할 수 있도록 합니다. Azure 블록 체인 서비스는 ID 페더레이션, 단일 사인온 및 다단계 인증을 사용하도록 설정하는 Azure AD와 통합됩니다. 조직에서 블록 체인 구성원 및 노드 액세스를 위해 사용자, 그룹 및 응용 프로그램 역할을 할당 할 수 있습니다.

Azure AD 클라이언트 프록시는 [GitHub](https://github.com/Microsoft/azure-blockchain-connector/releases)에서 사용할 수 있습니다. 클라이언트 프록시는 사용자를 Azure AD 로그인 페이지로 안내하고 성공적인 인증시 베어러 토큰을 가져옵니다. 그런 다음 사용자는 Geth 또는 Truffle과 같은 이더리움 클라이언트 응용 프로그램을 클라이언트 프록시의 끝점에 연결합니다. 마지막으로 트랜잭션이 제출되면 클라이언트 프록시는 http 헤더에 베어러 토큰을 삽입하고 역방향 프록시는 OAuth 프로토콜을 사용하여 토큰의 유효성을 검사합니다.

## <a name="keys-and-ethereum-accounts"></a>키 및 이더리움 계정

Azure 블록 체인 서비스 멤버를 프로비전할 때 이더리움 계정과 공개 및 개인 키 쌍이 생성됩니다. 개인 키는 블록 체인에 트랜잭션을 보내는 데 사용됩니다. 이더리움 계정은 공개 키 해시의 마지막 20바이트입니다. 이더리움 계정은 지갑이라고도 합니다.

개인 및 공개 키 쌍은 JSON 형식으로 키 파일로 저장됩니다. 개인 키는 블록 체인 원장 서비스가 생성 될 때 입력 된 암호를 사용하여 암호화됩니다.

개인 키는 트랜잭션에 디지털 서명하는 데 사용됩니다. 개인 블록 체인에서 개인 키로 서명 된 스마트 계약은 서명자의 신원을 나타냅니다. 서명의 유효성을 확인하기 위해 수신자는 서명자의 공개 키와 서명에서 계산된 주소를 비교할 수 있습니다.

별자리 키는 쿼럼 노드를 고유하게 식별하는 데 사용됩니다. 별자리 키는 노드 프로비저닝 시 생성되며 쿼럼의 개인 트랜잭션의 privateFor 매개 변수에 지정됩니다.

## <a name="next-steps"></a>다음 단계

[Azure 블록 체인 서비스에 대한 Azure Active Directory 액세스를 구성하는 방법을](configure-aad.md)참조하십시오.
