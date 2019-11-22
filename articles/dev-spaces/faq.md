---
title: Azure Dev Spaces에 대 한 질문과 대답
services: azure-dev-spaces
ms.date: 09/25/2019
ms.topic: conceptual
description: Azure Dev Spaces에 대 한 일반적인 질문에 대 한 답변을 찾습니다.
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers, Helm, service mesh, service mesh routing, kubectl, k8s '
ms.openlocfilehash: 3c7335f1656d304d231c2146c8b7496ea43f0b4c
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/21/2019
ms.locfileid: "74280251"
---
# <a name="frequently-asked-questions-about-azure-dev-spaces"></a>Azure Dev Spaces에 대 한 질문과 대답

이는 Azure Dev Spaces에 대 한 질문과 대답을 다룹니다.

## <a name="which-azure-regions-currently-provide-azure-dev-spaces"></a>현재 Azure Dev Spaces을 제공 하는 Azure 지역은 무엇 인가요?

사용 가능한 지역에 대 한 전체 목록은 [지원 되는 지역 및 구성][supported-regions]을 참조 하세요.

## <a name="can-i-use-azure-dev-spaces-without-a-public-ip-address"></a>공용 IP 주소 없이 Azure Dev Spaces를 사용할 수 있나요?

아니요, 공용 IP 없이 AKS 클러스터에 Azure Dev Spaces를 프로 비전 할 수 없습니다. [라우팅 Azure Dev Spaces][dev-spaces-routing]에는 공용 IP가 필요 합니다.

## <a name="can-i-use-my-own-ingress-with-azure-dev-spaces"></a>Azure Dev Spaces에서 자체 수신을 사용할 수 있나요?

예, Azure Dev Spaces 만든 사용자와 함께 직접 수신을 구성할 수 있습니다. 예를 들어 [traefik][ingress-traefik]를 사용할 수 있습니다.

## <a name="can-i-use-https-with-azure-dev-spaces"></a>Azure Dev Spaces에서 HTTPS를 사용할 수 있나요?

예, [traefik][ingress-https-traefik]를 사용 하 여 HTTPS로 직접 수신을 구성할 수 있습니다.

## <a name="can-i-use-azure-dev-spaces-on-a-cluster-that-uses-cni-rather-than-kubenet"></a>Kubenet가 아닌 CNI를 사용 하는 클러스터에서 Azure Dev Spaces를 사용할 수 있나요? 

예, 네트워킹에 CNI를 사용 하는 AKS 클러스터에서 Azure Dev Spaces를 사용할 수 있습니다. 예를 들어 네트워킹에 CNI를 사용 하는 [기존 Windows 컨테이너][windows-containers]와 AKS 클러스터에서 Azure Dev Spaces를 사용할 수 있습니다.

## <a name="can-i-use-azure-dev-spaces-with-windows-containers"></a>Windows 컨테이너에 Azure Dev Spaces를 사용할 수 있나요?

현재 Azure Dev Spaces는 Linux pod 및 노드에서만 실행 하기 위한 것 이지만 [기존 Windows 컨테이너][windows-containers]를 사용 하 여 AKS 클러스터에서 Azure Dev Spaces을 실행할 수 있습니다.

## <a name="can-i-use-azure-dev-spaces-on-aks-clusters-with-api-server-authorized-ip-address-ranges-enabled"></a>API 서버 권한이 부여 된 IP 주소 범위를 사용 하 여 AKS 클러스터에서 Azure Dev Spaces를 사용할 수 있나요?

예, [API 서버 권한이 부여 된 IP 주소 범위][aks-auth-range] 를 사용 하 여 AKS 클러스터에서 Azure Dev Spaces를 사용할 수 있습니다. 클러스터를 [만들][aks-auth-range-create] 때 [해당 지역에 따라 추가 범위를 허용][aks-auth-range-ranges]해야 합니다. 또한 기존 클러스터를 [업데이트][aks-auth-range-update] 하 여 이러한 추가 범위를 허용할 수 있습니다.

### <a name="can-i-use-azure-dev-spaces-on-aks-clusters-with-restricted-egress-traffic-for-cluster-nodes"></a>클러스터 노드에 대해 제한 된 송신 트래픽이 있는 AKS 클러스터에서 Azure Dev Spaces를 사용할 수 있나요?

예, 다음 Fqdn이 허용 되 면 사용 하도록 설정 된 [클러스터 노드에 대해 제한 된 송신 트래픽을][aks-restrict-egress-traffic] 사용 하는 AKS 클러스터에서 Azure Dev Spaces를 사용할 수 있습니다.

| FQDN                                    | 포트      | 이후      |
|-----------------------------------------|-----------|----------|
| cloudflare.docker.com | HTTPS:443 | Linux 알파인 및 기타 Azure Dev Spaces 이미지를 꺼내려면 |
| gcr.io | HTTP: 443 | 투구/tiller 이미지를 꺼내려면|
| storage.googleapis.com | HTTP: 443 | 투구/tiller 이미지를 꺼내려면|


[aks-auth-range]: ../aks/api-server-authorized-ip-ranges.md
[aks-auth-range-create]: ../aks/api-server-authorized-ip-ranges.md#create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled
[aks-auth-range-ranges]: https://github.com/Azure/dev-spaces/tree/master/public-ips
[aks-auth-range-update]: ../aks/api-server-authorized-ip-ranges.md#update-a-clusters-api-server-authorized-ip-ranges
[aks-restrict-egress-traffic]: ../aks/limit-egress-traffic.md
[dev-spaces-routing]: how-dev-spaces-works.md#how-routing-works
[ingress-traefik]: how-to/ingress-https-traefik.md#configure-a-custom-traefik-ingress-controller
[ingress-https-traefik]: how-to/ingress-https-traefik.md#configure-the-traefik-ingress-controller-to-use-https
[supported-regions]: about.md#supported-regions-and-configurations
[windows-containers]: how-to/run-dev-spaces-windows-containers.md