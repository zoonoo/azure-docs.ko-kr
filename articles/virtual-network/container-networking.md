---
title: 컨테이너에 대한 Azure Virtual Network | Microsoft Docs
description: 가상 네트워크에서 컨테이너를 컨테이너 간 및 다른 리소스와 통신하도록 하는 Kubernetes 클러스터에 대한 CNI 플러그 인에 대해 알아봅니다.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: ''
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/18/2017
ms.author: jdial
ms.openlocfilehash: f70afa8ff69b6c79363313c0f2df3b6785da8d81
ms.sourcegitcommit: c87e036fe898318487ea8df31b13b328985ce0e1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/19/2017
ms.locfileid: "26766401"
---
# <a name="container-networking"></a>컨테이너 네트워킹

Azure는 기본 Azure 네트워킹 기능으로 사용자 고유의 Kubernetes 클러스터를 배포하고 관리할 수 있도록 하는 [CNI(컨테이너 네트워킹 인터페이스) 플러그 인](https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md)을 제공합니다. [Azure Container Service 엔진](https://github.com/Azure/acs-engine)(또는 ACS 엔진)을 사용하여 Kubernetes 클러스터를 배포할 때 플러그 인은 기본적으로 활성화됩니다.

## <a name="networking-capabilities"></a>네트워킹 기능

컨테이너는 가상 네트워크가 제공하는 다음과 같은 다양한 기능 집합을 활용할 수 있습니다.
-   클러스터에 대한 별도 가상 네트워크를 만들거나 기존 가상 네트워크에서 클러스터를 배포할 수 있습니다. 
-   클러스터의 모든 Pod는 가상 네트워크 내에서 IP 주소를 받고 클러스터의 다른 Pod 및 가상 네트워크의 모든 가상 머신과 직접 통신할 수 있습니다. 
-   Pod는 피어링된 가상 네트워크에서 다른 Pod 및 가상 머신에 ExpressRoute 및 사이트 간 VPN 연결을 통해 온-프레미스 네트워크에 연결할 수 있습니다. 온-프레미스 리소스는 Pod와 통신할 수 있습니다. 
-   Azure Load Balancer를 통해 인터넷에 Kubernetes 서비스를 노출할 수 있습니다.  
-   서비스 엔드포인트가 활성화된 서브넷의 Pod는 Azure 서비스(예: Storage 및 SQL Database)에 안전하게 연결할 수 있습니다.
-   사용자 정의 경로를 사용하여 Pod에서 네트워크 가상 어플라이언스로 트래픽을 라우팅할 수 있습니다. 
-   Pod는 인터넷에서 공용 리소스에 액세스할 수 있습니다.
-   Pod에 DNS 이름으로 연결될 수 있는 공용 IP 주소를 할당할 수 있습니다.
 
## <a name="limits"></a>제한
플러그 인을 사용할 때 클러스터당 총 16,000개 Pod의 한도로 Kubernetes 클러스터에 최대 4,000개의 노드와 노드당 250개의 Pod를 배포할 수 있습니다.

## <a name="constraints"></a>제약 조건
- [Azure Container Service(AKS)](../aks/intro-kubernetes.md?toc=%2fazure%2fvirtual-network%2ftoc.json)로 Kubernetes 클러스터를 배포하거나 Kubernetes로 [ACS](../container-service/kubernetes/container-service-intro-kubernetes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 클러스터를 배포하는 경우 플러그 인은 활성화되지 않습니다.
- DNS 또는 액세스 정책을 포함한 Kubernetes 네트워크 정책에 대한 기본 지원은 없습니다.
- 플러그 인은 Pod당 네트워크 정책을 지원하지 않습니다.

## <a name="pricing"></a>가격
CNI 플러그 인 사용에 대한 요금은 없습니다.

## <a name="next-steps"></a>다음 단계

[자체 가상 네트워크](https://github.com/Azure/acs-engine/blob/master/docs/kubernetes/features.md#using-azure-integrated-networking-cni)에 [Kubernetes 클러스터를 배포](https://github.com/Azure/acs-engine/blob/master/docs/kubernetes/deploy.md)하는 방법을 알아봅니다.
