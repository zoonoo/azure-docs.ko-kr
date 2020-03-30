---
title: 클라우드에 의한 VM웨어 솔루션 간단함 - Azure 네트워크 연결
description: Azure 가상 네트워크를 CloudSimple 리전 네트워크에 연결하는 방법에 대해 알아보기
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: cfd4d65b07cf255ac2b60d6bf8376723a997374e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025081"
---
# <a name="azure-network-connections-overview"></a>Azure 네트워크 연결 개요

리전에서 CloudSimple 서비스를 만들고 노드를 만들 때 다음을 수행할 수 있습니다.

* Azure ExpressRoute 회로를 요청하고 해당 지역의 CloudSimple 네트워크에 연결합니다.
* Azure ExpressRoute를 사용하여 CloudSimple 지역 네트워크를 Azure 가상 네트워크 또는 온-프레미스 네트워크에 연결합니다.
* 프라이빗 클라우드 환경에서 Azure 구독 또는 온-프레미스 네트워크에서 실행되는 서비스에 대한 액세스를 제공합니다.

ExpressRoute 연결은 대기 시간이 짧은 높은 대역폭입니다.

## <a name="benefits"></a>이점

Azure 네트워크 연결을 사용하면 다음을 수행할 수 있습니다.

* Azure를 프라이빗 클라우드의 가상 시스템에 대한 백업 대상으로 사용합니다.
* Azure 구독에 KMS 서버를 배포하여 프라이빗 클라우드 vSAN 데이터 스토어를 암호화합니다.
* 응용 프로그램 및 데이터베이스 계층이 프라이빗 클라우드에서 실행되는 동안 응용 프로그램의 웹 계층이 퍼블릭 클라우드에서 실행되는 하이브리드 응용 프로그램을 사용합니다.

## <a name="azure-virtual-network-connection"></a>Azure 가상 네트워크 연결

프라이빗 클라우드는 ExpressRoute를 사용하여 Azure 리소스에 연결할 수 있습니다.  ExpressRoute 연결을 사용하면 프라이빗 클라우드에서 Azure 구독에서 실행 중인 리소스에 액세스할 수 있습니다.  이 연결을 사용하면 Azure 가상 네트워크로 사설 클라우드 네트워크를 확장할 수 있습니다.  CloudSimple 네트워크의 경로는 BGP를 통해 Azure 가상 네트워크와 교환됩니다.  가상 네트워크 피어링이 구성된 경우 모든 피어링된 가상 네트워크는 CloudSimple 네트워크에서 액세스할 수 있습니다.

![Azure 익스프레스라우팅 가상 네트워크에 연결](media/cloudsimple-azure-network-connection.png)

## <a name="expressroute-connection-to-on-premises-network"></a>익스프레스루트 온-프레미스 네트워크에 연결

기존 Azure ExpressRoute 회로를 CloudSimple 리전에 연결할 수 있습니다. ExpressRoute 글로벌 리치 기능은 두 회로를 서로 연결하는 데 사용됩니다.  온-프레미스 와 CloudSimple 익스프레스루트 회로 간에 연결이 설정됩니다.  이 연결을 사용하면 온-프레미스 네트워크를 프라이빗 클라우드 네트워크로 확장할 수 있습니다. CloudSimple 네트워크의 경로는 온-프레미스 네트워크와 BGP를 통해 교환됩니다.

![온-프레미스 익스프레스루트 연결 - 글로벌 도달 범위](media/cloudsimple-global-reach-connection.png)

## <a name="connection-to-on-premises-network-and-azure-virtual-network"></a>온-프레미스 네트워크 및 Azure 가상 네트워크에 연결

온-프레미스 네트워크 및 Azure 가상 네트워크에 대한 연결은 CloudSimple 네트워크에서 공존할 수 있습니다.  연결은 BGP를 사용하여 온-프레미스 네트워크, Azure 가상 네트워크 및 CloudSimple 네트워크 간의 경로를 교환합니다.  전역 Reach 연결이 있는 경우 CloudSimple 네트워크를 Azure 가상 네트워크에 연결하면 Azure 가상 네트워크 경로가 온-프레미스 네트워크에 표시됩니다.  경로 교환은 에지 라우터 간의 Azure에서 발생합니다.

![온-프레미스 익스프레스라우팅 Azure 가상 네트워크 연결을 통해 연결](media/cloudsimple-global-reach-and-vnet-connection.png)

### <a name="important-considerations"></a>중요 고려 사항

온-프레미스 네트워크와 Azure 가상 네트워크에서 CloudSimple 네트워크에 연결하면 모든 네트워크 간에 경로 교환이 가능합니다.

* Azure 가상 네트워크는 온-프레미스 네트워크와 CloudSimple 네트워크 모두에서 볼 수 있습니다.
* 온-프레미스 네트워크에서 Azure 가상 네트워크에 연결한 경우 전역 리치를 사용하여 CloudSimple 네트워크에 연결하면 CloudSimple 네트워크에서 가상 네트워크에 액세스할 수 있습니다.
* 서브넷 주소는 연결된 네트워크 간에 겹치지 **않아야 합니다.**
* CloudSimple은 익스프레스루트 연결에 대한 기본 경로를 **보급하지 않습니다.**
* 온-프레미스 라우터가 기본 경로를 보급하는 경우 CloudSimple 네트워크 및 Azure 가상 네트워크의 트래픽은 보급된 기본 경로를 사용합니다.  따라서 공용 IP 주소를 사용하여 Azure의 가상 시스템에 액세스할 수 없습니다.

## <a name="next-steps"></a>다음 단계

* [익스프레스루트를 사용하여 Azure 가상 네트워크를 클라우드단순에 연결](virtual-network-connection.md)
* [익스프레스루트를 사용하여 온-프레미스에서 클라우드로 연결](on-premises-connection.md)
