---
title: VMware 솔루션 CloudSimple-서비스에서
description: CloudSimple 서비스 및 개념의 개요를 설명합니다.
author: sharaths-cs
ms.author: b-shsury
ms.date: 4/24/19
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: f7b4be0ff3997e27dd5b5321dd44b5006ae52102
ms.sourcegitcommit: 8e76be591034b618f5c11f4e66668f48c090ddfd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/29/2019
ms.locfileid: "66358110"
---
# <a name="cloudsimple-service-overview"></a>CloudSimple 서비스 개요

CloudSimple 서비스를 사용 하면 Azure CloudSimple VMware 솔루션을 사용할 수 있습니다.  서비스를 만들고, 노드를 구입 하 고, 노드를 예약 하 고, 사설 클라우드를 만들 수 있습니다.  CloudSimple 서비스를 사용할 수 있는 각 Azure 지역에서 CloudSimple 서비스를 만듭니다.  서비스는 CloudSimple 하 여 Azure VMware 솔루션의 경계 네트워크를 정의합니다.  경계 네트워크는 VPN, ExpressRoute 및 인터넷에 연결 하 여 사설 클라우드를 포함 하는 서비스를 지원 합니다.

## <a name="gateway-subnet"></a>게이트웨이 서브넷 

게이트웨이 서브넷을 CloudSimple 서비스별 필요 하며 생성 된 지역에 고유 합니다. 게이트웨이 서브넷은 경계 네트워크를 만들 때 사용 하며 을/28 CIDR 블록입니다.  게이트웨이 서브넷 주소 공간은 고유 해야 합니다. CloudSimple 환경과 통신 하는 네트워크와 겹치지 않아야 합니다. CloudSimple와 통신 하는 네트워크는 온-프레미스 네트워크와 Azure 가상 네트워크를 포함 합니다.  만들어진 후에 게이트웨이 서브넷을 삭제할 수 없습니다.  게이트웨이 서브넷은 서비스가 삭제 되 면 제거 됩니다.

## <a name="next-steps"></a>다음 단계

* 자세한 방법 [Azure에서 CloudSimple 서비스 만들기](quickstart-create-cloudsimple-service.md)