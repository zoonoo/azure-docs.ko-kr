---
title: "Azure Network Watcher에서 다음 홉 소개 | Microsoft Docs"
description: "이 페이지는 Network Watcher 다음 홉 기능에 대한 개요를 제공합니다."
services: network-watcher
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
ms.assetid: febf7bca-e0b7-41d5-838f-a5a40ebc5aac
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 2f03ba60d81e97c7da9a9fe61ecd419096248763
ms.openlocfilehash: 864185e62fb6c3cef4116824b36ee7e5d3447662
ms.lasthandoff: 03/04/2017

---

# <a name="introduction-to-next-hop-in-azure-network-watcher"></a>Azure Network Watcher에서 다음 홉 소개

VM의 트래픽은 NIC와 연결된 유효 경로에 따라 대상에 전송됩니다. 다음 홉은 특정 가상 컴퓨터 및 NIC에서 다음 홉 유형 및 패킷의 IP 주소를 가져옵니다. 이를 통해 패킷이 대상으로 연결되거나 블랙홀이 되는 트래픽인 경우를 확인할 수 있습니다. 사용자에 의한 부적절한 경로의 구성으로 연결 문제가 발생할 수 있습니다. 여기서 트래픽은 온-프레미스 위치 또는 가상 어플라이언스로 향합니다. 또한 다음 홉은 다음 홉과 연결된 경로 테이블을 반환합니다. 다음 홉을 쿼리할 때 경로가 사용자 정의 경로로 정의된 경우 해당 경로가 반환됩니다. 그렇지 않은 경우 다음 홉은 "시스템 경로"를 반환합니다.

![다음 홉 개요][1]

다음은 다음 홉을 쿼리할 때 반환될 수 있는 다음 홉 형식의 목록입니다.

* 인터넷
* VirtualAppliance
* VirtualNetworkGateway
* VnetLocal
* HyperNetGateway
* VnetPeering
* 없음

### <a name="next-steps"></a>다음 단계

[VM에서 다음 홉 확인](network-watcher-check-next-hop-portal.md)을 방문하여 다음 홉을 사용하여 네트워크 연결 문제를 찾는 방법에 대해 알아보기

<!--Image references-->
[1]: ./media/network-watcher-next-hop-overview/figure1.png














