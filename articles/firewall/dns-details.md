---
title: Azure Firewall DNS 프록시 세부 정보
description: Azure Firewall DNS 프록시의 작동 방식 알아보기
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 05/26/2021
ms.author: victorh
ms.openlocfilehash: f6f17823b51200c21ee0975f50487ba1e3f58ced
ms.sourcegitcommit: 9ad20581c9fe2c35339acc34d74d0d9cb38eb9aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2021
ms.locfileid: "110547929"
---
# <a name="azure-firewall-dns-proxy-details"></a>Azure Firewall DNS 프록시 세부 정보

DNS 프록시 역할을 하도록 Azure Firewall을 구성할 수 있습니다. DNS 프록시는 클라이언트 가상 머신에서 DNS 서버로의 DNS 요청에 대한 중개자입니다.

다음 정보는 Azure Firewall DNS 프록시에 대한 몇 가지 구현 세부 정보를 설명합니다.

## <a name="fqdns-with-multiple-a-records"></a>여러 A 레코드가 있는 FQDN

Azure Firewall은 표준 DNS 클라이언트로 작동합니다. 응답에 여러 A 레코드가 있는 경우 방화벽은 모든 레코드를 캐시에 저장합니다. 응답당 하나의 레코드가 있는 경우 방화벽은 단일 레코드만 저장합니다. 클라이언트가 응답에서 예상해야 할 A 레코드가 하나인지 여러 개인지를 미리 알 수 있는 방법은 없습니다.

## <a name="fqdn-time-to-live-ttl"></a>FQDN TTL(Time to Live)

FQDN TTL(Time to Live)이 만료될 때 레코드는 TTL에 따라 캐시되고 만료됩니다. 프리페치가 사용되지 않으므로 방화벽은 TTL 만료 전에 조회를 수행하여 레코드를 새로 고치지 않습니다.

## <a name="clients-not-configured-to-use-the-firewall-dns-proxy"></a>방화벽 DNS 프록시를 사용하도록 구성되지 않은 클라이언트

클라이언트 컴퓨터가 방화벽 DNS 프록시가 아닌 DNS 서버를 사용하도록 구성된 경우 결과를 예측할 수 없습니다.

예를 들어 클라이언트 워크로드가 미국 동부에 있고 미국 동부에서 호스트되는 주 DNS 서버를 사용한다고 가정합니다. Azure Firewall DNS 서버 설정은 미국 서부에서 호스트되는 보조 DNS 서버에 대해 구성됩니다. 미국 서부에 호스트된 방화벽의 DNS 서버는 미국 동부에 있는 클라이언트와 다른 응답을 생성합니다.

이는 일반적인 시나리오인데, 이런 이유로 클라이언트가 방화벽의 DNS 프록시 기능을 사용해야 합니다. 네트워크 규칙에서 FQDN을 사용하는 경우 클라이언트는 방화벽을 확인자로 사용해야 합니다. 클라이언트 및 방화벽 자체에서 IP 주소 확인 일관성을 보장할 수 있습니다.

이 예제에서 FQDN이 네트워크 규칙에 구성된 경우 방화벽은 FQDN을 IP1(IP 주소 1)로 확인하여 IP1에 대한 액세스를 허용하도록 네트워크 규칙을 업데이트합니다. DNS 응답의 차이로 인해 클라이언트가 동일한 FQDN을 IP2로 확인하면 연결 시도가 방화벽의 규칙과 일치하지 않게 되고 거부됩니다. 

애플리케이션 규칙의 HTTP/S FQDN의 경우 방화벽은 호스트 또는 SNI 헤더에서 FQDN을 구문 분석하고, 해결한 다음, 해당 IP 주소에 연결합니다. 클라이언트가 연결하려 했던 대상 IP 주소는 무시됩니다.

## <a name="next-steps"></a>다음 단계

- [Azure Firewall DNS 설정](dns-settings.md)