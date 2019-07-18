---
title: Azure Blockchain 서비스 보안
description: Azure Blockchain 서비스 데이터 액세스 및 보안 개념
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: seal
manager: femila
ms.openlocfilehash: dd0a33364ed9395a85478798e47352c533bd47dc
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65028202"
---
# <a name="azure-blockchain-service-security"></a>Azure Blockchain 서비스 보안

Azure Blockchain 서비스는 안전 하 고 사용할 수 있는 데이터를 유지 하려면 여러 Azure 기능을 사용 합니다. 데이터 격리, 암호화 및 인증을 사용 하 여 보호 됩니다.

## <a name="isolation"></a>격리

Azure Blockchain 서비스 리소스는 개인 가상 네트워크에서 격리 됩니다. 각 트랜잭션 및 유효성 검사 노드 가상 머신 (VM)를입니다. 하나의 가상 네트워크의 Vm은 다른 가상 네트워크의 Vm에 직접 통신할 수 없습니다. 격리 하면 통신이 가상 네트워크 안에서 비공개 상태를 유지 합니다. Azure 가상 네트워크 격리에 대 한 자세한 내용은 참조 하세요. [Azure 공용 클라우드에서 격리](../../security/azure-isolation.md#networking-isolation)합니다.

![VNET 다이어그램](./media/data-security/vnet.png)

## <a name="encryption"></a>암호화

사용자 데이터는 Azure storage에 저장 됩니다. 사용자 데이터는 미사용에서 및 보안과 기밀성에 대 한 미사용 암호화 됩니다. 자세한 내용은 다음을 참조하세요. [Azure Storage 보안 가이드](../../storage/common/storage-security-guide.md)합니다.

## <a name="authentication"></a>Authentication

트랜잭션 블록 체인 노드는 RPC 끝점을 통해 보낼 수 있습니다. 클라이언트는 사용자 인증을 처리 하는 역방향 프록시 서버를 사용 하 여 트랜잭션 노드와 통신 하 고 SSL을 통해 데이터를 암호화 합니다.

![인증 다이어그램](./media/data-security/authentication.png)

세 가지 모드 RPC 액세스에 인증 합니다.

### <a name="basic-authentication"></a>기본 인증

기본 인증 사용자 이름 및 암호를 포함 하는 HTTP 인증 헤더를 사용 합니다. 사용자 이름은 blockchain 노드의 이름이입니다. 암호는 멤버 또는 노드를 프로 비전 하는 동안 설정 됩니다. Azure portal 또는 CLI를 사용 하 여 암호를 변경할 수 있습니다.

### <a name="access-keys"></a>액세스 키

액세스 키에는 끝점 URL에 포함 된 임의로 생성 된 문자열을 사용 합니다. 두 개의 액세스 키 도움말 키 회전을 사용 하도록 설정 합니다. Azure portal 및 CLI에서 키를 다시 생성할 수 있습니다.

### <a name="azure-active-directory"></a>Azure Active Directory

Azure Active Directory (Azure AD) 사용 하 여 클레임 기반 인증 메커니즘 Azure AD 사용자 자격 증명을 사용 하 여 Azure AD에서 사용자가 인증 하는 위치입니다. Azure AD는 클라우드 기반 id 관리를 제공 하며 고객이 클라우드에서 전체 엔터프라이즈 및 액세스 응용 프로그램에서 단일 id를 사용 하 여 합니다. Azure Blockchain 서비스 ID 페더레이션과 single sign on 및 다단계 인증을 사용 하도록 설정 하는 Azure AD와 통합 합니다. 블록 체인 멤버 및 노드 액세스를 위해 조직에서 사용자, 그룹 및 응용 프로그램 역할을 할당할 수 있습니다.

Azure AD 클라이언트 프록시는에서 사용할 수 있습니다 [GitHub](https://github.com/Microsoft/azure-blockchain-connector/releases)합니다. 클라이언트 프록시를 Azure AD 로그인 페이지로 사용자를 전달 하 고 인증이 성공 하면 전달자 토큰을 가져옵니다. 그런 다음 사용자 클라이언트 프록시의 끝점에 Geth 등 Truffle Ethereum 클라이언트 응용 프로그램을 연결합니다. 마지막으로 트랜잭션을 전송 되 면 클라이언트 프록시는 http 헤더에서 전달자 토큰을 삽입 하 고 역방향 프록시 OAuth 프로토콜을 사용 하 여 토큰의 유효성을 검사 합니다.

## <a name="keys-and-ethereum-accounts"></a>Ethereum 계정과 키

Azure Blockchain 서비스 멤버를 프로 비전, Ethereum 계정 및 공용 및 개인 키 쌍 생성 됩니다. 개인 키 블록 체인 트랜잭션을 전송에 사용 됩니다. Ethereum 공개 키 해시의 마지막 20 바이트입니다. Ethereum 계정에는 wallet을 라고도 합니다.

개인 및 공개 키 쌍 파일을 JSON 형식에서으로 저장 됩니다. 개인 키 블록 체인 원장의 서비스를 만들 때 입력 한 암호를 사용 하 여 암호화 됩니다.

개인 키는 트랜잭션에 디지털 서명 사용 됩니다. 개인 얻게 개인 키로 서명 된 스마트 계약 서명자의 id를 나타냅니다. 서명의 유효성을 확인 하려면 수신자가 서명에서 계산 하는 주소를 사용 하 여 서명자의 공개 키를 비교할 수 있습니다.

Constellation 키는 쿼럼 노드를 고유 하 게 식별 하는 데 사용 됩니다. Constellation 키 노드 프로 비전 시 생성 되 고 쿼럼 개인 트랜잭션의 privateFor 매개 변수에 지정 됩니다.

## <a name="next-steps"></a>다음 단계

[Azure Blockchain Service 트랜잭션 노드 구성](configure-transaction-nodes.md)
