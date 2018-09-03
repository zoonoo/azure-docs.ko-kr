---
title: Azure Kubernetes Service 소개
description: Azure Kubernetes Service를 통해 Azure에서 컨테이너 기반 응용 프로그램을 간단히 배포 및 관리할 수 있습니다.
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: overview
ms.date: 06/13/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 161ea6698ecc46e50d1c70f922e2fb505e78278c
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2018
ms.locfileid: "43087707"
---
# <a name="azure-kubernetes-service-aks"></a>AKS(Azure Kubernetes Service)

AKS(Azure Kubernetes Service)를 사용하면 Azure에서 관리되는 Kubernetes 클러스터를 간단하게 배포할 수 있습니다. AKS는 대부분의 부담을 Azure에 오프로딩하여 Kubernetes를 관리하는 복잡성 및 운영 과부하를 감소시킵니다. 호스팅되는 Kubernetes 서비스인 Azure는 상태 모니터링 및 유지 관리 같은 중요 작업을 처리합니다. 또한 서비스가 무료이므로 마스터가 아니라 클러스터 내의 에이전트 노드에 대해서만 지불합니다.

이 문서에서는 AKS(Azure Kubernetes Service) 기능에 대한 개요를 제공합니다.

## <a name="flexible-deployment-options"></a>유연한 배포 옵션

Azure Kubernetes Service는 포털, 명령줄 및 템플릿 기반 배포 옵션(Resource Manager 템플릿 및 Terraform)을 제공합니다. AKS 클러스터를 배포할 때 Kubernetes 마스터 및 모든 노드가 배포되고 자동으로 구성됩니다. 배포 프로세스 중에 고급 네트워킹, Azure Active Directory 통합 및 모니터링 등의 추가 기능을 구성할 수도 있습니다.

자세한 내용은 [AKS 포털 빠른 시작] [aks-portal] 또는 [AKS CLI 빠른 시작][aks-cli]을 참조하세요.

## <a name="identity-and-security-management"></a>ID 및 보안 관리

AKS 클러스터는 [RBAC(역할 기반 액세스 제어)][kubernetes-rbac]를 지원합니다. Azure Active Directory와 통합하도록 AKS 클러스터를 구성할 수도 있습니다. 이 구성에서 Kubernetes 액세스는 Azure Active Directory ID 및 그룹 멤버 자격에 따라 구성할 수 있습니다.

자세한 내용은 [AKS와 Azure Active Directory 통합][aks-aad]을 참조하세요.

## <a name="integrated-logging-and-monitoring"></a>통합된 로깅 및 모니터링

컨테이너 상태는 메트릭 컨테이너, 노드 및 컨트롤러에서 메모리 및 프로세서 메트릭을 수집하여 성능 가시성을 제공합니다. 컨테이너 로그도 수집됩니다. 이 데이터는 Log Analytics 작업 영역에 저장되고 Azure Portal, Azure CLI 또는 REST 엔드포인트를 통해 제공됩니다.

자세한 내용은 [Azure Kubernetes Service 컨테이너 상태 모니터링][container-health].을 참조하세요.

## <a name="cluster-node-scaling"></a>클러스터 노드 크기 조정

리소스에 대한 요구가 증가하면 AKS 클러스터의 노드 규모도 맞게 확장될 수 있습니다. 리소스 수요가 감소하는 경우 클러스터의 규모를 조정하여 노드를 제거할 수 있습니다. Azure Portal 또는 Azure CLI를 사용하여 AKS 크기 조정 작업을 완료할 수 있습니다.

자세한 내용은 [AKS(Azure Kubernetes Service) 클러스터의 규모 조정][aks-scale]을 참조하세요.

## <a name="cluster-node-upgrades"></a>클러스터 노드 업그레이드

Azure Kubernetes Service는 여러 Kubernetes 버전을 제공합니다. AKS에서 새 버전을 사용할 수 있으므로 Azure Portal 또는 Azure CLI를 사용하여 클러스터를 업그레이드할 수 있습니다. 업그레이드 프로세스 중에는 노드를 신중하게 통제하고 드레이닝하여 실행 중인 응용 프로그램의 중단을 최소화합니다.

자세한 내용은 [AKS(Azure Kubernetes Service) 클러스터 업그레이드][aks-upgrade]를 참조하세요.

## <a name="http-application-routing"></a>HTTP 응용 프로그램 라우팅

HTTP 응용 프로그램 라우팅 솔루션을 사용하면 AKS 클러스터에 배포된 응용 프로그램에 쉽게 액세스할 수 있습니다. HTTP 응용 프로그램 라우팅 솔루션이 설정되면 AKS 클러스터에 수신 컨트롤러를 구성합니다. 응용 프로그램이 배포되면 공개적으로 액세스할 수 있는 DNS 이름이 자동으로 구성됩니다.

자세한 내용은 [HTTP 응용 프로그램 라우팅][aks-http-routing]을 참조하세요.

## <a name="gpu-enabled-nodes"></a>GPU 사용 노드

AKS는 GPU 사용 노드 풀 만들기를 지원합니다. Azure는 현재, 단일 또는 여러 GPU 사용 VM을 제공합니다. GPU 사용 VM은 계산 집약적이며 그래픽 집약적인 시각화 워크로드용으로 설계되었습니다.

자세한 내용은 [AKS에서 GPU 사용][aks-gpu]을 참조하세요.

## <a name="development-tooling-integration"></a>개발 도구 통합

Kubernetes에는 다양한 Visual Studio Code용 개발 및 관리 도구(예: Helm, Draft 및 Kubernetes 확장)가 있습니다. 이러한 도구는 Azure Kubernetes Service에서 원활하게 작동합니다.

또한 Azure Dev Spaces는 팀에게 신속하고 반복적인 Kubernetes 개발 환경을 제공합니다. 최소한의 구성을 통해 AKS(Azure Kubernetes Service)에서 바로 컨테이너를 실행하고 디버그할 수 있습니다.

자세한 내용은 [Azure Dev Spaces][azure-dev-spaces]를 참조하세요.

Azure DevOps 프로젝트는 기존 코드 및 Git 리포지토리를 Azure로 가져오는 간단한 솔루션을 제공합니다. DevOps 프로젝트는 자동으로 AKS와 같은 Azure 리소스, CI의 빌드 정의를 포함한 VSTS의 릴리스 파이프라인을 만들고, CD에 대한 릴리스 정의를 설정한 다음, 모니터링을 위해 Azure Application Insights 리소스를 만듭니다.

자세한 내용은 [Azure DevOps 프로젝트][azure-devops]를 참조하세요.

## <a name="virtual-network-integration"></a>가상 네트워크 통합

AKS 클러스터는 기존 VNet에 배포될 수 있습니다. 이 구성에서 클러스터의 모든 Pod는 VNet의 IP 주소가 할당되고, 클러스터의 다른 Pod 및 VNet의 다른 노드와 직접 통신할 수 있습니다. 또한 Pod는 피어링된 VNet의 다른 서비스에 연결되고, ExpressRoute와 S2S(사이트 간) VPN 연결을 통해 온-프레미스 네트워크에 연결될 수 있습니다.

자세한 내용은 [AKS 네트워킹 개요][aks-networking]를 참조하세요.

## <a name="private-container-registry"></a>개인 컨테이너 레지스트리

Docker 이미지의 개인 저장소의 ACR(Azure Container Registry)와 통합합니다.

자세한 내용은 [ACR(Azure Container Registry)][acr-docs]를 참조하세요.

## <a name="storage-volume-support"></a>저장소 볼륨 지원

AKS(Azure Kubernetes Service)는 영구 데이터에 저장소 볼륨을 탑재하도록 지원합니다. AKS 클러스터는 Azure Files 및 Azure Disks에 대한 지원을 통해 생성됩니다.

자세한 내용은 [Azure Files][azure-files] 및 [Azure Disks][azure-disk]를 참조하세요.

## <a name="docker-image-support"></a>Docker 이미지 지원

AKS(Azure Kubernetes Service)는 Docker 이미지 형식을 지원합니다.

## <a name="kubernetes-certification"></a>Kubernetes 인증

AKS(Azure Kubernetes Service)는 Kubernetes 규칙을 따르는 CNCF로 인증되었습니다.

## <a name="regulatory-compliance"></a>규정 준수

AKS(Azure Kubernetes Service)는 SOC 및 ISO와 호환됩니다.

## <a name="next-steps"></a>다음 단계

AKS 빠른 시작으로 AKS 배포 및 관리에 대해 자세히 알아 봅니다.

> [!div class="nextstepaction"]
> [AKS 빠른 시작][aks-cli]

<!-- LINKS - external -->
[acs-engine]: https://github.com/Azure/acs-engine
[draft]: https://github.com/Azure/draft
[helm]: https://helm.sh/
[kubectl-overview]: https://kubernetes.io/docs/user-guide/kubectl-overview/
[kubernetes-rbac]: https://kubernetes.io/docs/reference/access-authn-authz/rbac/

<!-- LINKS - internal -->
[acr-docs]: ../container-registry/container-registry-intro.md
[aks-aad]: ./aad-integration.md
[aks-cli]: ./kubernetes-walkthrough.md
[aks-gpu]: ./gpu-cluster.md
[aks-http-routing]: ./http-application-routing.md
[aks-networking]: ./networking-overview.md
[aks-portal]: ./kubernetes-walkthrough-portal.md
[aks-scale]: ./scale-cluster.md
[aks-upgrade]: ./upgrade-cluster.md
[azure-dev-spaces]: https://docs.microsoft.com/en-us/azure/dev-spaces/azure-dev-spaces
[azure-devops]: https://docs.microsoft.com/en-us/azure/devops-project/overview
[azure-disk]: ./azure-disks-dynamic-pv.md
[azure-files]: ./azure-files-dynamic-pv.md
[container-health]: ../monitoring/monitoring-container-health.md

