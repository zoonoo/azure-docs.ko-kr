---
title: CloudSimple 별 VMware 솔루션-Azure 네트워크 연결
description: Azure 가상 네트워크를 CloudSimple 영역 네트워크에 연결 하는 방법을 알아봅니다.
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: cfd4d65b07cf255ac2b60d6bf8376723a997374e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "77025081"
---
# <a name="azure-network-connections-overview"></a>Azure 네트워크 연결 개요

지역에서 CloudSimple 서비스를 만들고 노드를 만들 때 다음을 수행할 수 있습니다.

* Azure Express 경로 회로를 요청 하 고 해당 지역의 CloudSimple 네트워크에 연결 합니다.
* Azure Express 경로를 사용 하 여 CloudSimple 지역 네트워크를 Azure virtual network 또는 온-프레미스 네트워크에 연결 합니다.
* 사설 클라우드 환경에서 Azure 구독 또는 온-프레미스 네트워크에서 실행 되는 서비스에 대 한 액세스를 제공 합니다.

Express 경로 연결은 낮은 대기 시간으로 높은 대역폭입니다.

## <a name="benefits"></a>이점

Azure 네트워크 연결을 사용 하면 다음을 수행할 수 있습니다.

* 사설 클라우드의 가상 컴퓨터에 대 한 백업 대상으로 Azure를 사용 합니다.
* Azure 구독에 KMS 서버를 배포 하 여 사설 클라우드 vSAN 데이터 저장소를 암호화 합니다.
* 응용 프로그램 및 데이터베이스 계층이 사설 클라우드에서 실행 되는 동안 응용 프로그램의 웹 계층이 공용 클라우드에서 실행 되는 하이브리드 응용 프로그램을 사용 합니다.

## <a name="azure-virtual-network-connection"></a>Azure 가상 네트워크 연결

사설 클라우드는 Express 경로를 사용 하 여 Azure 리소스에 연결할 수 있습니다.  Express 경로 연결을 사용 하 여 사설 클라우드에서 Azure 구독에서 실행 중인 리소스에 액세스할 수 있습니다.  이 연결을 통해 사설 클라우드 네트워크를 Azure 가상 네트워크로 확장할 수 있습니다.  CloudSimple network의 경로는 BGP를 통해 Azure 가상 네트워크와 교환 됩니다.  가상 네트워크 피어 링을 구성한 경우 모든 피어 링 가상 네트워크는 CloudSimple 네트워크에서 액세스할 수 있습니다.

![가상 네트워크에 대 한 Azure Express 경로 연결](media/cloudsimple-azure-network-connection.png)

## <a name="expressroute-connection-to-on-premises-network"></a>온-프레미스 네트워크에 대 한 Express 경로 연결

기존 Azure Express 경로 회로를 CloudSimple 영역에 연결할 수 있습니다. Express 경로 Global Reach 기능은 두 회로를 서로 연결 하는 데 사용 됩니다.  온-프레미스와 CloudSimple Express 경로 회로 간에 연결이 설정 됩니다.  이 연결을 통해 온-프레미스 네트워크를 사설 클라우드 네트워크로 확장할 수 있습니다. CloudSimple 네트워크의 경로는 온-프레미스 네트워크와 BGP를 통해 교환 됩니다.

![온-프레미스 Express 경로 연결-Global Reach](media/cloudsimple-global-reach-connection.png)

## <a name="connection-to-on-premises-network-and-azure-virtual-network"></a>온-프레미스 네트워크 및 Azure virtual network에 연결

온-프레미스 네트워크 및 Azure virtual network에 대 한 연결은 CloudSimple 네트워크에서 공존할 수 있습니다.  연결은 BGP를 사용 하 여 온-프레미스 네트워크, Azure virtual network 및 CloudSimple 네트워크 간에 경로를 교환 합니다.  Global Reach 연결이 설정 된 상태에서 CloudSimple network를 Azure 가상 네트워크에 연결 하면 Azure virtual network 경로가 온-프레미스 네트워크에 표시 됩니다.  경로 교환은 Azure에서에 지 라우터 사이에서 발생 합니다.

![Azure 가상 네트워크 연결을 사용 하 여 온-프레미스 Express 경로 연결](media/cloudsimple-global-reach-and-vnet-connection.png)

### <a name="important-considerations"></a>중요 고려 사항

온-프레미스 네트워크 및 Azure virtual network에서 CloudSimple 네트워크에 연결 하면 모든 네트워크 간에 경로를 교환할 수 있습니다.

* Azure 가상 네트워크는 온-프레미스 네트워크와 CloudSimple 네트워크 모두에서 볼 수 있습니다.
* 온-프레미스 네트워크에서 Azure virtual network에 연결 된 경우 Global Reach를 사용 하 여 CloudSimple 네트워크에 연결 하면 CloudSimple 네트워크에서 가상 네트워크에 액세스할 수 있습니다.
* 서브넷 주소는 연결 된 네트워크 간에 겹치지 **않아야 합니다** .
* CloudSimple은 Express 경로 연결에 대 한 기본 경로를 보급 **하지 않습니다** .
* 온-프레미스 라우터가 기본 경로를 보급 하는 경우 CloudSimple 네트워크 및 Azure virtual network의 트래픽은 보급 된 기본 경로를 사용 합니다.  따라서 공용 IP 주소를 사용 하 여 Azure의 가상 컴퓨터에 액세스할 수 없습니다.

## <a name="next-steps"></a>다음 단계

* [Express 경로를 사용 하 여 Azure 가상 네트워크를 CloudSimple에 연결](virtual-network-connection.md)
* [Express 경로를 사용 하 여 온-프레미스에서 CloudSimple로 연결](on-premises-connection.md)
