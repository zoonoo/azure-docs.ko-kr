---
title: VMware 솔루션 CloudSimple-Azure 네트워크 연결
description: Azure virtual network CloudSimple 지역 네트워크에 연결 하는 방법에 대 한에 대해 알아봅니다.
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: f2ab82b6c1b4b373c186019eaf96f9864861b9d9
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66497607"
---
# <a name="azure-network-connections-overview"></a>Azure 네트워크 연결 개요

특정 지역의 CloudSimple 서비스를 만들면 해당:

* Azure ExpressRoute 회로 만들고 해당 지역에서 서비스에 연결 합니다.
* Azure 가상 네트워크 또는 Azure ExpressRoute를 사용 하 여 온-프레미스 네트워크에 CloudSimple 지역 네트워크에서 연결할 수 있습니다.
* Azure 구독 또는 사설 클라우드 환경에서 온-프레미스 네트워크에서 실행 되는 액세스 서비스를 제공 합니다.

연결이 됩니다.

* 보안
* 프라이빗
* 높은 대역폭
* 짧은 대기 시간

## <a name="benefits"></a>이점

Azure 네트워크 연결을 사용 하면 수 있습니다.

* 백업 대상으로 Azure를 사용 하 여 사설 클라우드에 가상 컴퓨터에 대 한 합니다.
* 사설 클라우드 vSAN 데이터 저장소를 암호화 하기 위해 Azure 구독에 KMS 서버를 배포 합니다.
* 여기서 응용 프로그램 하는 동안 공용 클라우드에서 실행 되는 응용 프로그램의 웹 계층 및 데이터베이스 계층을 실행할 사설 클라우드에서 하이브리드 응용 프로그램을 사용 합니다.

## <a name="azure-virtual-network-connection"></a>Azure 가상 네트워크 연결

사설 클라우드는 ExpressRoute를 사용 하 여 Azure 리소스에 연결할 수 있습니다.  이 연결을 사용 하 여 사설 클라우드를 Azure 구독에서 실행 하는 다른 리소스에 액세스할 수 있습니다.  이 연결을 사용 하면 Azure 가상 네트워크를 사설 클라우드 네트워크를 확장할 수 있습니다.

![가상 네트워크에 azure ExpressRoute 연결](media/cloudsimple-azure-network-connection.png)

## <a name="expressroute-connection-to-on-premises-network"></a>온-프레미스 네트워크를 ExpressRoute 연결

기존 Azure ExpressRoute 회로 CloudSimple 지역을 연결할 수 있습니다. ExpressRoute 글로벌 환경 기능 두 회로 서로 연결 됩니다.  온-프레미스와 CloudSimple ExpressRoute 회로 간의 연결 됩니다.  이 연결을 사용 하면 사설 클라우드 네트워크에 온-프레미스 네트워크를 확장할 수 있습니다.

![온-프레미스 ExpressRoute 연결-글로벌 환경](media/cloudsimple-global-reach-connection.png)

## <a name="next-steps"></a>다음 단계

* [Azure virtual network CloudSimple 연결에 대 한 피어 링 정보를 가져오려면](https://docs.azure.cloudsimple.com/virtual-network-connection)
* [ExpressRoute를 사용 하 여 CloudSimple 연결할 온-프레미스에서](https://docs.azure.cloudsimple.com/on-premises-connection)
