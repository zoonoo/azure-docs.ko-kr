---
title: Azure Stack Edge Pro 디바이스의 Kubernetes 네트워킹 이해 | Microsoft Docs
description: Kubernetes 네트워킹이 Azure Stack Edge Pro 디바이스에서 작동하는 방법을 설명합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 02/21/2021
ms.author: alkohli
ms.openlocfilehash: 139eeaa83629f3ef51f8eda414622f7da54dfab4
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105560253"
---
# <a name="kubernetes-networking-in-your-azure-stack-edge-pro-gpu-device"></a>Azure Stack Edge Pro GPU 디바이스의 Kubernetes 네트워킹

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Azure Stack Edge Pro 디바이스에서 컴퓨팅 역할을 구성할 때 Kubernetes 클러스터가 만들어집니다. Kubernetes 클러스터를 만든 후 Pod의 Kubernetes 클러스터에 컨테이너화된 애플리케이션을 배포할 수 있습니다. Kubernetes 클러스터의 Pod에 네트워킹을 사용하는 고유한 방법이 있습니다. 

이 문서에서는 일반적인 Kubernetes 클러스터의 네트워킹에 대해 설명하고 Azure Stack Edge Pro 디바이스의 컨텍스트에서 자세히 설명합니다. 

## <a name="networking-requirements"></a>네트워킹 요구 사항

다음은 Kubernetes 클러스터에 배포되는 일반적인 2계층 앱의 예입니다.

- 앱에는 백 엔드에 웹 서버 프런트 엔드 및 데이터베이스 애플리케이션이 있습니다. 
- 모든 Pod에는 IP가 할당되지만 IP는 Pod의 다시 시작 및 장애 조치(failover) 시 변경될 수 있습니다. 
- 각 앱은 여러 Pod으로 구성되며, 모든 Pod 복제본에 트래픽의 부하가 분산됩니다. 

![Kubernetes 네트워킹 요구 사항](./media/azure-stack-edge-gpu-kubernetes-networking/kubernetes-networking-1.png)

위의 시나리오의 네트워킹 요구 사항은 다음과 같은 같습니다.

 - 애플리케이션 사용자가 이름 또는 IP 주소를 통해 Kubernetes 클러스터 외에 외부 대상 애플리케이션에 액세스할 수 있어야 합니다. 
 - Kubernetes 클러스터 내의 애플리케이션(예: 여기서 프런트 엔드 및 백 엔드 Pod)은 서로 통신할 수 있어야 합니다.

위의 요구 사항을 모두 해결하기 위해 Kubernetes 서비스를 도입했습니다. 


## <a name="networking-services"></a>네트워킹 서비스

Kubernetes 서비스는 다음 두 가지 유형이 있습니다. 

- **클러스터 IP 서비스** - 애플리케이션 Pod에 대한 상수 엔드포인트를 제공한다고 생각합니다. 서비스와 연결된 모든 Pod은 Kubernetes 클러스터 외부에서 액세스할 수 없습니다. 서비스에 사용되는 IP 주소는 개인 네트워크의 주소 공간에서 제공됩니다. 
- **Load balancer IP** - 클러스터 IP 서비스와 비슷하지만 연결된 IP는 외부 네트워크에서 제공되며 Kubernetes 클러스터 외부에서 액세스할 수 있습니다.


<!--## Networking example for an app


![Kubernetes networking example](./media/azure-stack-edge-gpu-kubernetes-networking/kubernetes-networking-2.png)

Each of these applications pods has a label associated with it. For example, the web server application pods have a label `app = WS` and the service has a label selector which the same as `app = WS`. Whenever a service of type load balancer or cluster IP is created, there is a control loop that runs in the master and publishes an endpoint corresponding to this service. This service uses a combination of labels and label selectors to discover the pods associated with this service. As a pod gets created, the new endpoint for the pod is added to the endpoint mapping. Whenever a pod is deleted, it gets deleted from the endpoint mapping. Using this endpoint controller, the service has a most up-to-date view of the pods that make up this application.

For discovery of applications within the cluster, Kubernetes cluster has an inbuilt DNS server pod. This is a cluster DNS that resolves service names to cluster IP. Anytime a cluster IP service is created, a DNS record is added to the DNS server that maps the name of the service to the cluster internal IP. That is how the applications within the cluster can discover each other. For load balancing, there is also the `kube-proxy`. This runs on every node and captures the traffic that comes in through the cluster IP and then distributes the traffic across the pods. 

When an application or the end user would first use the IP address associated with the service of type load balancer to discover the service. Then it would use the label select `app = WS` to discover the pods associated with the application. The `kube-proxy` component would then distribute the traffic and ensure that it hits one of the web server application pods. If the web server app wanted to talk to the database app, then it would simply use the name of the service and using the name and the DNS server pod, resolve the name to an IP address. Again using labels and label selector, it would discover the pods associated with the database application. The `kube-proxy` would then distribute the traffic across each of the database app nodes.-->


## <a name="kubernetes-networking-on-azure-stack-edge-pro"></a>Azure Stack Edge Pro의 Kubernetes 네트워킹

Calico, Metallb, Core DNS는 모두 Azure Stack Edge Pro에서 네트워킹에 대해 설치되는 구성 요소입니다. 

- **Calico** 는 개인 IP 범위에서 모든 Pod에 IP 주소를 할당하고, Pod에 대한 네트워킹을 구성하여 한 노드의 Pod이 다른 노드의 Pod과 통신할 수 있도록 합니다. 
- **Metallb** 는 클러스터 내 Pod에서 실행되며, 부하 분산 장치 유형의 서비스에 IP 주소를 할당합니다. Load Balancer IP 주소는 로컬 UI를 통해 제공되는 서비스 IP 범위에서 선택됩니다. 
- **핵심 DNS** - 이 추가 기능은 DNS 레코드 매핑 서비스 이름을 클러스터 IP 주소에 구성합니다.

Kubernetes 노드와 외부 서비스에 사용되는 IP 주소는 디바이스의 로컬 UI에서 **컴퓨팅 네트워크** 페이지를 통해 제공됩니다.

![로컬 UI에 Kubernetes IP 할당](./media/azure-stack-edge-gpu-kubernetes-networking/kubernetes-ip-assignment-local-ui-1.png)

IP 할당은 다음에 대한 것입니다.

- **Kubernetes 노드 IP**:이 IP 범위는 Kubernetes 마스터 및 작업자 노드에 사용됩니다. IP는 Kubernetes 노드가 서로 통신할 때 사용됩니다.

- **Kubernetes 외부 서비스 IP**:이 IP 범위는 Kubernetes 클러스터 외부에 노출되는 외부 서비스(Load Balancer 서비스라고도 함)에 사용됩니다.  


## <a name="next-steps"></a>다음 단계

Azure Stack Edge Pro에 Kubernetes 네트워킹을 구성하려면 다음을 참조하세요.

- [IoT Edge를 통해 Azure Stack Edge Pro에 외부에서 상태 비저장 애플리케이션 노출](azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module.md).

- [을 통해 Azure Stack Edge Pro에 외부에서 상태 비저장 애플리케이션 노출](./azure-stack-edge-gpu-deploy-stateless-application-kubernetes.md).