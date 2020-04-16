---
title: Azure 개발자 공간에 대해 자주 묻는 질문
services: azure-dev-spaces
ms.date: 01/28/2020
ms.topic: conceptual
description: Azure 개발자 공간에 대한 몇 가지 일반적인 질문에 대한 답변 찾기
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, 컨테이너, Helm, 서비스 메시, 서비스 메시 라우팅, kubectl, k8s '
ms.openlocfilehash: b5a380f20640b9bc328aa30289ff7f915cc0b73c
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414306"
---
# <a name="frequently-asked-questions-about-azure-dev-spaces"></a>Azure 개발자 공간에 대해 자주 묻는 질문

Azure 개발자 공간에 대해 자주 묻는 질문을 해결합니다.

## <a name="what-versions-of-kubernetes-are-supported-for-azure-dev-spaces"></a>Azure 개발자 공간에서 어떤 버전의 Kubernetes가 지원됩니까?

Azure 개발자 공간은 현재 지원되는 모든 [GA(일반 공급) 버전의 Kubernetes를 지원합니다.][aks-supported-k8s]

## <a name="which-azure-regions-currently-provide-azure-dev-spaces"></a>현재 Azure 개발자 공간을 제공하는 Azure 지역은 무엇입니까?

[지원되는 리전의][supported-regions] 전체 목록을 참조하십시오.

## <a name="can-i-migrate-my-aks-cluster-with-azure-dev-spaces-to-another-region"></a>Azure 개발자 공간으로 AKS 클러스터를 다른 지역으로 마이그레이션할 수 있습니까?

예. Azure 개발자 공간을 사용하여 AKS 클러스터를 [다른 지원 지역으로][supported-regions]이동하려면 다른 리전에서 새 클러스터를 만든 다음 Azure 개발자 공간을 설치 및 구성하고 리소스 및 응용 프로그램을 새 클러스터에 배포하는 것이 좋습니다. AKS 마이그레이션에 대한 자세한 내용은 [AKS(Azure Kubernetes Service)로 마이그레이션을][aks-migration]참조하십시오.

## <a name="can-i-use-azure-dev-spaces-with-existing-dockerfiles-or-helm-charts"></a>기존 Dockerfiles 또는 Helm 차트에서 Azure 개발자 공간을 사용할 수 있습니까?

예. 프로젝트에 이미 Dockerfile 또는 Helm 차트가 있는 경우 Azure 개발자 공간에서 해당 파일을 사용할 수 있습니다. 실행할 `azds prep`때 매개 `--chart` 변수를 사용하고 차트의 위치를 지정합니다. Azure 개발자 공간은 여전히 *azds.yaml* 및 *Dockerfile.develop* 파일을 생성하지만 기존 Dockerfile 또는 Helm 차트를 대체하거나 수정하지는 않습니다. 을 실행할 `azds up`때 모든 것이 기존 응용 프로그램과 올바르게 작동하려면 *azds.yaml* 및 *Dockerfile.develop* 파일을 수정해야 할 수 있습니다.

고유한 Dockerfile 또는 Helm 차트를 사용하는 경우 다음과 같은 제한 사항이 있습니다.
* Dockerfile을 하나만 사용하는 경우 런타임뿐만 아니라 SDK 언어와 같은 개발 시나리오를 활성화하는 데 필요한 모든 것을 포함해야 합니다. Dockerfile.develop와 같은 Azure 개발자 공간에 대해 별도의 Dockerfile을 사용하는 경우 개발 시나리오를 활성화하는 데 필요한 모든 것이 Dockerfile에 포함되어야 합니다.
* Helm 차트는 *value.yaml*에서 값으로 일부 또는 전체 이미지 태그를 전달하는 것을 지원해야 합니다.
* 받는 것으로 모든 것을 수정하는 경우 Helm 차트를 업데이트하여 Azure 개발자 공간에서 제공하는 받는 해결 방법을 사용할 수도 있습니다.
* [Azure Dev Spaces에서 제공하는 라우팅 기능을][dev-spaces-routing]사용하려면 개별 프로젝트의 모든 서비스가 단일 Kubernetes 네임스페이스에 맞아야 하며 *서비스-a*와 같은 간단한 이름으로 배포해야 합니다. 표준 헬름 차트에서 이 명명 업데이트는 *전체 nameOverride* 속성에 대한 값을 지정하여 수행할 수 있습니다.

고유한 Dockerfile 또는 Helm 차트를 Azure 개발자 공간에서 작동하는 기존 버전과 비교하려면 [빠른 시작에서][quickstart-cli]생성된 파일을 검토합니다.


## <a name="can-i-modify-the-files-generated-by-azure-dev-spaces"></a>Azure 개발자 공간에서 생성된 파일을 수정할 수 있습니까?

예. 프로젝트를 준비할 때 Azure Dev Spaces에서 생성된 *azds.yaml* 파일, Dockerfile 및 헬름 차트를 [수정할 수 있습니다.][dev-spaces-prep] 이러한 파일을 수정할 때 프로젝트를 빌드하고 실행하는 방식이 변경됩니다.

## <a name="can-i-use-azure-dev-spaces-without-a-public-ip-address"></a>공용 IP 주소없이 Azure 개발자 공간을 사용할 수 있습니까?

아니요, 공용 IP 없이는 AKS 클러스터에 Azure 개발자 공간을 프로비전할 수 없습니다. 라우팅을 위해 Azure 개발자 공간에서 공용 [IP가 필요합니다.][dev-spaces-routing]

## <a name="can-i-use-my-own-ingress-with-azure-dev-spaces"></a>Azure 개발자 공간에서 내 고유의 사용 을 사용할 수 있습니까?

예. Azure 개발자 공간에서 생성하는 로그인 을 따라 사용자 고유의 설치 를 구성할 수 있습니다. 예를 [들어, traefik][ingress-traefik] 또는 [NGINX를][ingress-nginx]사용할 수 있습니다.

## <a name="can-i-use-https-with-azure-dev-spaces"></a>Azure 개발자 공간에서 HTTPS를 사용할 수 있습니까?

예. [traefik][ingress-https-traefik] 또는 [NGINX를][ingress-https-nginx]사용하여 HTTPS로 사용자 고유의 사용 인서를 구성할 수 있습니다.

## <a name="can-i-use-azure-dev-spaces-on-a-cluster-that-uses-cni-rather-than-kubenet"></a>kubenet대신 CNI를 사용하는 클러스터에서 Azure 개발자 공간을 사용할 수 있습니까? 

예. 네트워킹에 CNI를 사용하는 AKS 클러스터에서 Azure 개발자 공간을 사용할 수 있습니다. 예를 들어 네트워킹에 CNI를 사용하는 기존 [Windows 컨테이너가][windows-containers]있는 AKS 클러스터에서 Azure 개발자 공간을 사용할 수 있습니다. Azure 개발자 공간과의 네트워킹을 위해 CNI를 사용하는 방법에 대한 자세한 내용은 [여기에서](configure-networking.md#using-azure-cni)확인할 수 있습니다.

## <a name="can-i-use-azure-dev-spaces-with-windows-containers"></a>Windows 컨테이너에서 Azure 개발자 공간을 사용할 수 있습니까?

현재 Azure 개발자 공간은 Linux 포드 및 노드에서만 실행되지만 [기존 Windows 컨테이너가][windows-containers]있는 AKS 클러스터에서 Azure 개발자 공간을 실행할 수 있습니다.

## <a name="can-i-use-azure-dev-spaces-on-aks-clusters-with-api-server-authorized-ip-address-ranges-enabled"></a>API 서버 권한 IP 주소 범위를 사용하도록 설정한 AKS 클러스터에서 Azure 개발자 공간을 사용할 수 있습니까?

예. [API 서버에서 승인된 IP 주소 범위를][aks-auth-range] 사용하도록 설정한 AKS 클러스터에서 Azure 개발자 공간을 사용할 수 있습니다. Azure 개발자 공간에서 활성화된 API 서버 승인 IP 주소 범위와 함께 AKS 클러스터사용에 대한 자세한 내용은 [여기에서](configure-networking.md#using-api-server-authorized-ip-ranges)확인할 수 있습니다.

## <a name="can-i-use-azure-dev-spaces-on-aks-clusters-with-restricted-egress-traffic-for-cluster-nodes"></a>클러스터 노드에 대한 송신 트래픽이 제한된 AKS 클러스터에서 Azure 개발자 공간을 사용할 수 있습니까?

예. 올바른 FQDN이 허용되면 [클러스터 노드에 대해 제한된 송신 트래픽이][aks-restrict-egress-traffic] 있는 AKS 클러스터에서 Azure 개발자 공간을 사용할 수 있습니다. Azure 개발자 공간에서 사용하도록 설정된 클러스터 노드에 대한 제한된 송신 트래픽이 있는 AKS 클러스터 사용에 대한 자세한 내용은 [여기에서](configure-networking.md#ingress-and-egress-network-traffic-requirements)확인할 수 있습니다.

## <a name="can-i-use-azure-dev-spaces-on-rbac-enabled-aks-clusters"></a>RBAC 지원 AKS 클러스터에서 Azure 개발자 공간을 사용할 수 있습니까?

예. RBAC를 사용하거나 사용하지 않고 AKS 클러스터에서 Azure 개발자 공간을 사용할 수 있습니다.

## <a name="what-happens-when-i-enable-ingress-for-project-in-visual-studio"></a>Visual Studio에서 프로젝트에 대한 인서를 사용하도록 설정하면 어떻게 되나요?

Visual Studio를 사용하여 프로젝트를 준비할 때 서비스에 대한 수레를 사용하도록 설정하는 옵션이 있습니다. 인그레스를 사용하도록 설정하면 AKS 클러스터에서 실행할 때 서비스에 액세스할 수 있는 공용 끝점이 만들어집니다. 인서를 활성화하지 않으면 AKS 클러스터 내에서만 서비스에 액세스할 수 있습니다.

## <a name="can-i-use-pod-managed-identities-with-azure-dev-spaces"></a>Azure 개발자 공간에서 포드 관리 ID를 사용할 수 있습니까?

예. Azure 개발자 공간을 사용하도록 설정한 경우 AKS 클러스터에서 [포드 관리 ID를][aks-pod-managed-id] 사용할 수 있지만 포드 관리 ID를 사용하여 클러스터에서 Azure 개발자 공간을 사용하도록 설정한 후 [추가 구성 단계가][dev-spaces-pod-managed-id-steps] 있습니다. 포드 관리 ID가 설치되어 있고 제거하려는 경우 [제거 노트에서][aks-pod-managed-id-uninstall]자세한 내용을 찾을 수 있습니다.

## <a name="can-i-use-azure-dev-spaces-with-multiple-microservices-in-an-application"></a>응용 프로그램에서 여러 마이크로 서비스와 함께 Azure 개발자 공간을 사용할 수 있습니까?

예. 여러 마이크로 서비스가 있는 응용 프로그램에서 Azure 개발자 공간을 사용할 수 있지만 루트에서 개별 마이크로 서비스를 준비하고 실행해야 합니다. Azure 개발자 공간 CLI, Azure 개발자 공간 VS 코드 확장 및 Visual Studio Azure 개발 워크로드는 *azds.yaml* 파일이 실행 및 디버깅을 위해 마이크로 서비스의 루트에 있을 것으로 예상합니다. 단일 응용 프로그램에서 여러 마이크로 서비스의 예는 [Bike 공유 샘플 응용][bike-sharing] 프로그램을 참조하십시오.

Visual Studio 코드에서는 단일 [작업 영역에서 별도의 프로젝트를 열고][vs-code-multi-root-workspaces] Azure 개발자 공간을 통해 별도로 디버깅할 수 있습니다. 각 프로젝트는 Azure 개발자 공간에 대해 독립적이고 준비되어야 합니다.

Visual Studio에서 Azure 개발자 공간을 통해 디버깅하기 위한 .NET Core 솔루션을 구성할 수 있습니다.

## <a name="can-i-use-azure-dev-spaces-with-a-service-mesh"></a>서비스 메시와 함께 Azure 개발자 공간을 사용할 수 있습니까?

현재 [는 Istio][istio] 또는 [Linkerd][linkerd]와 같은 서비스 메시와 함께 Azure 개발자 공간을 사용할 수 없습니다. 동일한 AKS 클러스터에서 Azure 개발자 공간 및 서비스 메시를 실행할 수 있지만 동일한 네임스페이스에서 Azure 개발자 공간과 서비스 메시를 모두 사용할 수 없습니다.

[aks-auth-range]: ../aks/api-server-authorized-ip-ranges.md
[aks-auth-range-create]: ../aks/api-server-authorized-ip-ranges.md#create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled
[aks-auth-range-ranges]: https://github.com/Azure/dev-spaces/tree/master/public-ips
[aks-auth-range-update]: ../aks/api-server-authorized-ip-ranges.md#update-a-clusters-api-server-authorized-ip-ranges
[aks-migration]: ../aks/aks-migration.md
[aks-pod-managed-id]: ../aks/developer-best-practices-pod-security.md#use-pod-managed-identities
[aks-pod-managed-id-uninstall]: https://github.com/Azure/aad-pod-identity#uninstall-notes
[aks-restrict-egress-traffic]: ../aks/limit-egress-traffic.md
[aks-supported-k8s]: ../aks/supported-kubernetes-versions.md#list-currently-supported-versions
[bike-sharing]: https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp
[dev-spaces-pod-managed-id-steps]: troubleshooting.md#error-no-azureassignedidentity-found-for-podazdsazds-webhook-deployment-id-in-assigned-state
[dev-spaces-prep]: how-dev-spaces-works-prep.md
[dev-spaces-routing]: how-dev-spaces-works-routing.md#how-routing-works
[ingress-nginx]: how-to/ingress-https-nginx.md#configure-a-custom-nginx-ingress-controller
[ingress-traefik]: how-to/ingress-https-traefik.md#configure-a-custom-traefik-ingress-controller
[ingress-https-nginx]: how-to/ingress-https-nginx.md#configure-the-nginx-ingress-controller-to-use-https
[ingress-https-traefik]: how-to/ingress-https-traefik.md#configure-the-traefik-ingress-controller-to-use-https
[istio]: https://istio.io/
[linkerd]: https://linkerd.io/
[quickstart-cli]: quickstart-cli.md
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[vs-code-multi-root-workspaces]: https://code.visualstudio.com/docs/editor/multi-root-workspaces
[windows-containers]: how-to/run-dev-spaces-windows-containers.md