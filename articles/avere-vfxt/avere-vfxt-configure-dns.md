---
title: Avere vFXT DNS - Azure
description: Avere vFXT for Azure를 사용하여 라운드 로빈 부하 분산을 위한 DNS 서버를 구성합니다.
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: 9fd9eaf1e62d063026e0e656346baaaade87064f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60410338"
---
# <a name="avere-cluster-dns-configuration"></a>Avere 클러스터 DNS 구성

이 섹션에서는 Avere vFXT 클러스터의 부하 분산을 위한 DNS 시스템 구성의 기본 사항에 대해 설명합니다. 

이 문서에는 Azure 환경에서 DNS 서버를 설정하고 관리하는 방법에 대한 지침이 *포함되어 있지 않습니다*. 

라운드 로빈 DNS를 사용하여 Azure에서 vFXT 클러스터의 부하를 분산하는 대신, 수동 방법을 사용하여 탑재할 때 클라이언트 간에 IP 주소를 균등하게 할당하는 것이 좋습니다. 몇 가지 방법은 [Avere 클러스터 탑재](avere-vfxt-mount-clients.md)에서 설명하고 있습니다. 

DNS 서버의 사용 여부를 결정할 때 다음 사항에 유의하세요. 

* NFS 클라이언트만 시스템에 액세스하는 경우 DNS를 사용할 필요가 없습니다. 숫자 IP 주소를 사용하여 모든 네트워크 주소를 지정할 수 있습니다. 

* 시스템에서 SMB(CIFS) 액세스를 지원하는 경우 Active Directory 서버에 대한 DNS 도메인을 지정해야 하므로 DNS가 필요합니다.

* Kerberos 인증을 사용하려면 DNS가 필요합니다.

## <a name="load-balancing"></a>부하 분산

전체 부하를 분산시키려면 클라이언트 측 IP 주소에 대해 라운드 로빈 부하 분산을 사용하도록 DNS 도메인을 구성합니다.

## <a name="configuration-details"></a>구성 세부 정보

클라이언트에서 클러스터에 액세스하는 경우 RRDNS는 사용 가능한 모든 인터페이스 간에 요청을 자동으로 분산시킵니다.

최적의 성능을 위해 다음 다이어그램과 같이 클라이언트 측 클러스터 주소를 처리하도록 DNS 서버를 구성합니다.

vserver 클러스터는 왼쪽에 표시되고, IP 주소는 가운데와 오른쪽에 표시되어 있습니다. 그림과 같이 A 레코드와 포인터를 사용하여 각 클라이언트 액세스 지점을 구성합니다.

![Avere 클러스터 라운드 로빈 DNS 다이어그램](media/avere-vfxt-rrdns-diagram.png) 
<!--- separate text description file provided  [diagram text description](avere-vfxt-rrdns-alt-text.md) -->

각 클라이언트 측 IP 주소에는 클러스터에서 내부적으로 사용할 수 있도록 고유한 이름이 있어야 합니다. (이 다이어그램에서 클라이언트 IP는 명확성을 위해 vs1-client-IP-*로 명명되었지만, 프로덕션에서는 client*와 같이 좀 더 간결하게 사용해야 합니다.)

클라이언트는 vserver 이름을 서버 인수로 사용하여 클러스터를 탑재합니다. 

DNS 서버의 ``named.conf`` 파일을 수정하여 vserver에 대한 쿼리의 순환 순서를 설정합니다. 이 옵션을 사용하면 사용 가능한 모든 값이 순환됩니다. 다음과 같은 명령문을 추가합니다.

```
options {
    rrset-order {
        class IN A name "vserver1.example.com" order cyclic;
    };
};
```

다음 nsupdate 명령은 DNS를 올바르게 구성하는 예제를 제공합니다.

```
update add vserver1.example.com. 86400 A 10.0.0.10
update add vserver1.example.com. 86400 A 10.0.0.11
update add vserver1.example.com. 86400 A 10.0.0.12
update add vs1-client-IP-10.example.com. 86400 A 10.0.0.10
update add vs1-client-IP-11.example.com. 86400 A 10.0.0.11
update add vs1-client-IP-12.example.com. 86400 A 10.0.0.12
update add 10.0.0.10.in-addr.arpa. 86400 PTR vs1-client-IP-10.example.com
update add 11.0.0.10.in-addr.arpa. 86400 PTR vs1-client-IP-11.example.com
update add 12.0.0.10.in-addr.arpa. 86400 PTR vs1-client-IP-12.example.com
```

## <a name="cluster-dns-settings"></a>클러스터 DNS 설정

**클러스터** > **관리 네트워크** 설정 페이지에서 vFXT 클러스터가 사용하는 DNS 서버를 지정합니다. 해당 페이지의 설정은 다음과 같습니다.

* DNS 서버 주소
* DNS 도메인 이름
* DNS 검색 도메인

이 페이지를 사용하는 방법에 대한 자세한 내용은 Avere 클러스터 구성 가이드의 [DNS 설정](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_admin_network.html#gui-dns>)을 참조하세요.


