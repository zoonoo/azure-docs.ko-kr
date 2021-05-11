---
title: Azure Firewall 정책 DNS 설정
description: DNS 서버 및 DNS 프록시 설정을 사용해 Azure Firewall 정책을 구성할 수 있습니다.
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: how-to
ms.date: 02/17/2021
ms.author: victorh
ms.openlocfilehash: a02879c5322add16f89f77e2da39daec7d2b5f31
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100633651"
---
# <a name="azure-firewall-policy-dns-settings"></a>Azure Firewall 정책 DNS 설정

사용자 지정 DNS 서버를 구성하고 Azure Firewall 정책에 DNS 프록시를 사용하도록 설정할 수 있습니다. 이러한 설정은 **DNS 설정** 페이지에서 방화벽을 배포할 때 또는 나중에 구성할 수 있습니다.

## <a name="dns-servers"></a>DNS 서버

DNS 서버는 도메인 이름을 IP 주소로 유지 관리하고 확인합니다. 기본 설정으로 Azure Firewall에서 이름 확인에 Azure DNS를 사용합니다. **DNS 서버** 설정을 사용하여 Azure Firewall 이름 확인을 위해 자체 DNS 서버를 구성할 수 있습니다. 단일 서버 또는 다중 서버를 구성할 수 있습니다.

### <a name="configure-custom-dns-servers"></a>사용자 지정 DNS 서버 구성

1. 방화벽 정책을 선택합니다.
2. **설정** 아래에서 **DNS 설정** 을 선택합니다.
3. **DNS 서버** 에서 이전에 Virtual Network에 지정된 기존 DNS 서버를 입력하거나 추가할 수 있습니다.
4. **저장** 을 선택합니다.
5. 이제 방화벽에서 이름 확인을 위해 DNS 트래픽을 지정된 DNS 서버로 보냅니다.

## <a name="dns-proxy"></a>DNS 프록시

DNS 프록시 역할을 하도록 Azure Firewall을 구성할 수 있습니다. DNS 프록시는 클라이언트 가상 머신에서 DNS 서버로의 DNS 요청에 대한 중개자 역할을 합니다. 사용자 지정 DNS 서버를 구성하는 경우 DNS 확인 불일치를 예방하기 위해 DNS 프록시를 사용하고, 네트워크 규칙에서 FQDN 필터링을 사용하도록 설정해야 합니다.

DNS 프록시를 사용하지 않는 경우 클라이언트의 DNS 요청이 다른 시간에 DNS 서버로 이동하거나 방화벽의 응답과는 다른 응답을 반환할 수 있습니다. DNS 프록시는 불일치를 방지하기 위해 Azure Firewall을 클라이언트 요청 경로에 배치합니다.

DNS 프록시 구성에는 다음 세 단계가 필요합니다.

1. Azure Firewall DNS 설정에서 DNS 프록시를 사용합니다.
2. 필요에 따라 사용자 지정 DNS 서버를 구성하거나 제공된 기본값을 사용합니다.
3. 마지막으로, 가상 네트워크 DNS 서버 설정에서 Azure Firewall의 개인 IP 주소를 사용자 지정 DNS 주소로 구성해야 합니다. 이렇게 하면 DNS 트래픽이 Azure Firewall로 전송됩니다.

### <a name="configure-dns-proxy"></a>DNS 프록시 구성

DNS 프록시를 구성하려면 방화벽 개인 IP 주소를 사용하도록 가상 네트워크 DNS 서버 설정을 구성해야 합니다. 그런 다음 Azure Firewall 정책 **DNS 설정** 에서 DNS 프록시를 사용하도록 설정합니다.

#### <a name="configure-virtual-network-dns-servers"></a>가상 네트워크 DNS 서버 구성

1. Azure Firewall을 통해 DNS 트래픽이 라우팅되는 가상 네트워크를 선택합니다.
2. **설정** 아래에서 **DNS 서버** 를 선택합니다.
3. **DNS 서버** 에서 **사용자 지정** 을 선택합니다.
4. 방화벽의 개인 IP 주소를 입력합니다.
5. **저장** 을 선택합니다.

#### <a name="enable-dns-proxy"></a>DNS 프록시 사용

1. Azure Firewall 정책을 선택합니다.
2. **설정** 아래에서 **DNS 설정** 을 선택합니다.
3. 기본적으로 **DNS 프록시** 는 사용하지 않는 것으로 설정됩니다. 사용하도록 설정되면 방화벽은 포트 53에서 수신하고 DNS 요청을 구성된 DNS 서버로 전달합니다.
4. **DNS 서버** 구성을 검토하여 해당 설정이 사용자 환경에 적합한지 확인합니다.
5. **저장** 을 선택합니다.

## <a name="next-steps"></a>다음 단계

[네트워크 규칙의 FQDN 필터링](fqdn-filtering-network-rules.md)