---
title: Azure 방화벽 정책 DNS 설정 (미리 보기)
description: DNS 서버 및 DNS 프록시 설정을 사용 하 여 Azure 방화벽 정책을 구성할 수 있습니다.
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: how-to
ms.date: 06/30/2020
ms.author: victorh
ms.openlocfilehash: bf189e4c9853d9f2ff6e8495423f4f36f14f41d4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "85611801"
---
# <a name="azure-firewall-policy-dns-settings-preview"></a>Azure 방화벽 정책 DNS 설정 (미리 보기)

> [!IMPORTANT]
> Azure 방화벽 DNS 설정은 현재 공개 미리 보기로 제공 됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

사용자 지정 DNS 서버를 구성 하 고 Azure 방화벽 정책에 DNS 프록시를 사용 하도록 설정할 수 있습니다. 이러한 설정은 **DNS 설정** 페이지에서 방화벽을 배포할 때 또는 나중에 구성할 수 있습니다.

## <a name="dns-servers"></a>DNS 서버

DNS 서버는 도메인 이름을 IP 주소에 유지 관리 하 고 확인 합니다. 기본적으로 Azure 방화벽은 이름 확인에 Azure DNS를 사용 합니다. **Dns 서버** 설정을 사용 하 여 Azure 방화벽 이름 확인을 위해 자체 dns 서버를 구성할 수 있습니다. 단일 서버 또는 다중 서버를 구성할 수 있습니다.

### <a name="configure-custom-dns-servers"></a>사용자 지정 DNS 서버 구성

1. 방화벽 정책을 선택 합니다.
2. **설정**아래에서 **DNS 설정**을 선택 합니다.
3. **Dns 서버**에서 이전에 Virtual Network에 지정 된 기존 dns 서버를 입력 하거나 추가할 수 있습니다.
4. **저장**을 선택합니다.
5. 이제 방화벽에서 이름 확인을 위해 DNS 트래픽을 지정 된 DNS 서버로 보냅니다.

## <a name="dns-proxy-preview"></a>DNS 프록시 (미리 보기)

DNS 프록시 역할을 하도록 Azure 방화벽을 구성할 수 있습니다. Dns 프록시는 클라이언트 가상 컴퓨터에서 DNS 서버로의 DNS 요청에 대 한 중개자 역할을 합니다. 사용자 지정 DNS 서버를 구성 하는 경우 dns 확인이 일치 하지 않도록 DNS 프록시를 사용 하도록 설정 하 고 네트워크 규칙에서 FQDN 필터링을 사용 하도록 설정 해야 합니다.

DNS 프록시를 사용 하지 않는 경우 클라이언트의 DNS 요청이 다른 시간에 DNS 서버로 이동 하거나 방화벽의 경우와 비교 하 여 다른 응답을 반환할 수 있습니다. DNS 프록시는 불일치를 방지 하기 위해 Azure 방화벽을 클라이언트 요청의 경로에 배치 합니다.

DNS 프록시 구성에는 다음 세 단계가 필요 합니다.
1. Azure 방화벽 DNS 설정에서 DNS 프록시를 사용 하도록 설정 합니다.
2. 필요에 따라 사용자 지정 DNS 서버를 구성 하거나 제공 된 기본값을 사용 합니다.
3. 마지막으로, 가상 네트워크 DNS 서버 설정에서 Azure 방화벽의 개인 IP 주소를 사용자 지정 DNS 주소로 구성 해야 합니다. 이렇게 하면 DNS 트래픽이 Azure 방화벽으로 전송 됩니다.

### <a name="configure-dns-proxy-preview"></a>DNS 프록시 구성 (미리 보기)

DNS 프록시를 구성 하려면 방화벽 개인 IP 주소를 사용 하도록 가상 네트워크 DNS 서버 설정을 구성 해야 합니다. 그런 다음 Azure 방화벽 정책 **dns 설정**에서 dns 프록시를 사용 하도록 설정 합니다.

#### <a name="configure-virtual-network-dns-servers"></a>가상 네트워크 DNS 서버 구성

1. Azure 방화벽을 통해 DNS 트래픽이 라우팅되는 가상 네트워크를 선택 합니다.
2. **설정** 아래에서 **DNS 서버**를 선택합니다.
3. **DNS 서버**아래에서 **사용자 지정** 을 선택 합니다.
4. 방화벽의 개인 IP 주소를 입력 합니다.
5. **저장**을 선택합니다.

#### <a name="enable-dns-proxy-preview"></a>DNS 프록시 사용 (미리 보기)

1. Azure 방화벽 정책을 선택 합니다.
2. **설정**아래에서 **DNS 설정**을 선택 합니다.
3. 기본적으로 **DNS 프록시** 는 사용 되지 않습니다. 사용 하도록 설정 되 면 방화벽은 포트 53에서 수신 하 고 DNS 요청을 구성 된 DNS 서버로 전달 합니다.
4. **DNS 서버** 구성을 검토 하 여 해당 설정이 사용자 환경에 적합 한지 확인 합니다.
5. **저장**을 선택합니다.

## <a name="next-steps"></a>다음 단계

[네트워크 규칙의 FQDN 필터링](fqdn-filtering-network-rules.md)