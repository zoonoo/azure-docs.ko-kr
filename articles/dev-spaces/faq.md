---
title: Azure Dev Spaces에 대한 질문과 대답
services: azure-dev-spaces
ms.date: 01/28/2020
ms.topic: conceptual
description: Azure Dev Spaces에 관해 자주 묻는 질문에 대한 답변을 찾아보세요.
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, 컨테이너, Helm, 서비스 메시, 서비스 메시 라우팅, kubectl, k8s '
ms.openlocfilehash: e83bed86714e4b92c63f4e7b7eb55df7a2a7eaff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96548837"
---
# <a name="frequently-asked-questions-about-azure-dev-spaces"></a>Azure Dev Spaces에 대한 질문과 대답

[!INCLUDE [Azure Dev Spaces deprecation](../../includes/dev-spaces-deprecation.md)]

여기에서는 Azure Dev Spaces에 대한 질문과 대답을 다룹니다.

## <a name="what-versions-of-kubernetes-are-supported-for-azure-dev-spaces"></a>Azure Dev Spaces에 대해 지원되는 Kubernetes 버전은 무엇인가요?

Azure Dev Spaces는 [현재 AKS에서 지원되는 GA(일반 공급) 버전의 Kubernetes를 최대 1.18까지][aks-supported-k8s]지원합니다. AKS의 Kubernetes 1.19 이상에서는 Azure Dev Spaces에서 작동하지 않는 컨테이너 런타임으로 ContainerD를 사용합니다.

## <a name="which-azure-regions-currently-provide-azure-dev-spaces"></a>현재 어떤 Azure 지역에서 Azure Dev Spaces를 제공하나요?

제공하는 지역의 전체 목록은 [지원되는 지역][supported-regions]을 참조하세요.

## <a name="can-i-migrate-my-aks-cluster-with-azure-dev-spaces-to-another-region"></a>Azure Dev Spaces를 사용하여 AKS 클러스터를 다른 지역으로 마이그레이션할 수 있나요?

예, Azure Dev Spaces로 AKS 클러스터를 다른 [지원되는 지역][supported-regions]으로 이동하려면 다른 지역에 새 클러스터를 만든 다음 Azure Dev Spaces를 설치 및 구성하고 새 클러스터에 리소스 및 애플리케이션을 배포하는 것이 좋습니다. AKS 마이그레이션에 대한 자세한 내용은 [AKS(Azure Kubernetes Service)로 마이그레이션][aks-migration]을 참조하세요.

## <a name="can-i-use-azure-dev-spaces-with-existing-dockerfiles-or-helm-charts"></a>기존 Dockerfiles 또는 Helm 차트와 Azure Dev Spaces를 사용할 수 있나요?

예, 프로젝트에 Dockerfile 또는 Helm 차트가 이미 있는 경우 Azure Dev Spaces에서 해당 파일을 사용할 수 있습니다. `azds prep`를 실행하는 경우 `--chart` 매개 변수를 사용하고 차트의 위치를 지정합니다. Azure Dev Spaces는 계속 *azds.yaml* 및 *Dockerfile.develop* 파일을 생성하지만 기존 Dockerfile 또는 Helm 차트를 바꾸거나 수정하지는 않습니다. `azds up`을 실행할 때 기존 애플리케이션에서 모든 것이 올바르게 작동하려면 *azds.yaml* 및 *Dockerfile.develop* 파일을 수정해야 할 수 있습니다.

사용자 고유의 Dockerfile 또는 Helm 차트를 사용하는 경우 다음과 같은 제한 사항이 있습니다.
* Dockerfile을 하나만 사용하는 경우에는 런타임뿐만 아니라 언어 SDK와 같은 개발 시나리오를 사용하도록 설정하는 데 필요한 모든 것이 포함되어야 합니다. Dockerfile.develop과 같이 Azure Dev Spaces에 대해 별도의 Dockerfile을 사용하는 경우 개발 시나리오를 사용하도록 설정하는 데 필요한 모든 것이 Dockerfile에 포함되어야 합니다.
* Helm 차트는 *values.yaml* 의 값으로 이미지 태그의 일부 또는 전체를 전달할 수 있도록 지원해야 합니다.
* 수신으로 모든 항목을 수정하는 경우 Azure Dev Spaces에서 제공하는 수신 솔루션을 사용하도록 Helm 차트를 업데이트할 수도 있습니다.
* [Azure Dev Spaces에서 제공하는 라우팅 기능][dev-spaces-routing]을 사용하려는 경우 개별 프로젝트에 대한 모든 서비스는 단일 Kubernetes 네임스페이스 내에 있어야 하며 *서비스 a* 와 같은 간단한 이름으로 배포해야 합니다. 표준 Helm 차트에서는 *fullnameOverride* 속성 값을 지정하여 이 명명 업데이트를 수행할 수 있습니다.

## <a name="can-i-modify-the-files-generated-by-azure-dev-spaces"></a>Azure Dev Spaces에서 생성된 파일을 수정할 수 있나요?

예, [프로젝트를 준비할 때 Azure Dev Spaces에 의해 생성된][dev-spaces-prep] *azds.yaml* 파일, Dockerfile 및 Helm 차트를 수정할 수 있습니다. 이러한 파일을 수정하면 프로젝트가 빌드되고 실행되는 방식이 변경됩니다.

## <a name="can-i-use-azure-dev-spaces-without-a-public-ip-address"></a>공용 IP 주소 없이 Azure Dev Spaces를 사용할 수 있나요?

아니요, 공용 IP 없이는 AKS 클러스터에 Azure Dev Spaces를 프로비전할 수 없습니다. [라우팅을 위해서는 Azure Dev Spaces][dev-spaces-routing]에 공용 IP가 필요합니다.

## <a name="can-i-use-my-own-ingress-with-azure-dev-spaces"></a>Azure Dev Spaces에서 자체 수신을 사용할 수 있나요?

예, 수신 Azure Dev Spaces를 생성하는 측에 따라 자체 수신을 구성할 수 있습니다. 예를 들어 [traefik][ingress-traefik] 또는 [NGINX][ingress-nginx]를 사용할 수 있습니다.

## <a name="can-i-use-https-with-azure-dev-spaces"></a>Azure Dev Spaces에서 HTTPS를 사용할 수 있나요?

예, [traefik][ingress-https-traefik] 또는 [NGINX][ingress-https-nginx]를 사용하여 HTTPS로 자체 수신을 구성할 수 있습니다.

## <a name="can-i-use-azure-dev-spaces-on-a-cluster-that-uses-cni-rather-than-kubenet"></a>Kubenet이 아닌 CNI를 사용하는 클러스터에서 Azure Dev Spaces를 사용할 수 있나요? 

예, 네트워킹에 CNI를 사용하는 AKS 클러스터에서 Azure Dev Spaces를 사용할 수 있습니다. 예를 들어 네트워킹에 CNI를 사용하는 [기존 Windows 컨테이너][windows-containers]와 AKS 클러스터에서 Azure Dev Spaces를 사용할 수 있습니다. Azure Dev Spaces에서 네트워킹에 CNI를 사용하는 방법에 대한 자세한 내용은 [여기](configure-networking.md#using-azure-cni)에서 확인하세요.

## <a name="can-i-use-azure-dev-spaces-with-windows-containers"></a>Windows 컨테이너에 Azure Dev Spaces를 사용할 수 있나요?

현재 Azure Dev Spaces는 Linux Pod 및 노드에서만 실행하도록 만들어졌지만 [기존 Windows 컨테이너][windows-containers]를 사용하여 AKS 클러스터에서 Azure Dev Spaces를 실행할 수 있습니다.

## <a name="can-i-use-azure-dev-spaces-on-aks-clusters-with-api-server-authorized-ip-address-ranges-enabled"></a>API 서버 권한이 부여된 IP 주소 범위를 사용하여 AKS 클러스터에서 Azure Dev Spaces를 사용할 수 있나요?

예, [API 서버 권한이 부여된 IP 주소 범위][aks-auth-range]를 사용하여 AKS 클러스터에서 Azure Dev Spaces를 사용할 수 있습니다. Azure DevSpace에서 API 서버 권한이 부여된 IP 주소 범위를 사용하도록 설정하여 AKS 클러스터 사용하는 것에 대한 자세한 내용은 [여기](configure-networking.md#using-api-server-authorized-ip-ranges)에서 확인하세요.

## <a name="can-i-use-azure-dev-spaces-on-aks-clusters-with-restricted-egress-traffic-for-cluster-nodes"></a>클러스터 노드에 대해 송신 트래픽을 제한하여 AKS 클러스터에서 Azure Dev Spaces를 사용할 수 있나요?

예, 올바른 FQDN이 허용되면 사용하도록 설정된 [클러스터 노드에 대해 제한된 송신 트래픽][aks-restrict-egress-traffic]이 있는 AKS 클러스터에서 Azure Dev Spaces를 사용할 수 있습니다. Azure Dev Spaces를 사용하도록 설정된 클러스터 노드에 대해 제한된 송신 트래픽이 있는 AKS 클러스터를 사용하는 방법에 대한 자세한 내용은 [여기](configure-networking.md#ingress-and-egress-network-traffic-requirements)에서 확인하세요.

## <a name="can-i-use-azure-dev-spaces-on-kubernetes-rbac-enabled-aks-clusters"></a>Kubernetes RBAC를 사용하는 AKS 클러스터에서 Azure Dev Spaces를 사용할 수 있나요?

예, Kubernetes RBAC(역할 기반 액세스 제어)를 사용하거나 또는 사용하지 않고도 AKS 클러스터에서 Azure Dev Spaces를 사용할 수 있습니다.

## <a name="what-happens-when-i-enable-ingress-for-project-in-visual-studio"></a>Visual Studio에서 프로젝트를 수신하도록 설정하면 어떻게 되나요?

Visual Studio를 사용하여 프로젝트를 준비할 때 서비스에 대한 수신을 사용하도록 설정하는 옵션이 있습니다. 수신을 사용하도록 설정하면 AKS 클러스터에서 실행할 때 서비스에 액세스할 수 있는 공용 엔드포인트가 생성됩니다(선택 사항). 수신을 사용하지 않는 경우 서비스는 AKS 클러스터 내에서만 액세스할 수 있습니다.

## <a name="can-i-use-pod-managed-identities-with-azure-dev-spaces"></a>Azure Dev Spaces에서 Pod 관리 ID를 사용할 수 있나요?

예, Azure Dev Spaces를 사용하도록 설정된 AKS 클러스터에서 [Pod 관리 ID][aks-pod-managed-id]를 사용할 수 있지만, Pod 관리 ID를 사용하여 클러스터에서 Azure Dev Spaces를 사용하도록 설정한 후에는 [추가 구성 단계][dev-spaces-pod-managed-id-steps]가 있습니다. Pod 관리 ID가 설치되어 있고 이를 제거하려는 경우 [제거 노트][aks-pod-managed-id-uninstall]에서 자세한 내용을 확인할 수 있습니다.

## <a name="can-i-use-azure-dev-spaces-with-multiple-microservices-in-an-application"></a>애플리케이션에서 여러 마이크로 서비스에 Azure Dev Spaces를 사용할 수 있나요?

예, 여러 마이크로 서비스가 있는 애플리케이션에서 Azure Dev Spaces를 사용할 수 있지만 루트에서 개별 마이크로 서비스를 준비하고 실행해야 합니다. Azure Dev Spaces CLI, Azure Dev Spaces VS Code 확장 및 Visual Studio Azure Development 워크로드는 *azds.yaml* 파일이 실행하고 디버그하기 위해 마이크로 서비스의 루트에 있을 것으로 예상합니다. 단일 애플리케이션의 여러 마이크로 서비스에 대한 예제는 [자전거 공유 샘플 애플리케이션][bike-sharing]을 참조하세요.

Visual Studio Code에서는 [개별 프로젝트를 단일 작업 영역에서 열고][vs-code-multi-root-workspaces] Azure Dev Spaces를 통해 개별적으로 디버그할 수 있습니다. 각 프로젝트는 자체 포함되고 Azure Dev Spaces에 대해 준비되어야 합니다.

Visual Studio에서는 Azure Dev Spaces를 통해 디버그하는 데 필요한 .NET Core 솔루션을 구성할 수 있습니다.

## <a name="can-i-use-azure-dev-spaces-with-a-service-mesh"></a>서비스 메시에 Azure Dev Spaces를 사용할 수 있나요?

현재는 [Istio][istio] 또는 [Linkerd][linkerd]와 같은 서비스 메시에 Azure Dev Spaces를 사용할 수 없습니다. Azure Dev Spaces와 서비스 메시를 동일한 AKS 클러스터에서 실행할 수 있지만 Azure Dev Spaces와 서비스 메시를 동일한 네임스페이스에서 사용할 수는 없습니다.

[aks-auth-range]: ../aks/api-server-authorized-ip-ranges.md
[aks-auth-range-create]: ../aks/api-server-authorized-ip-ranges.md#create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled
[aks-auth-range-update]: ../aks/api-server-authorized-ip-ranges.md#update-a-clusters-api-server-authorized-ip-ranges
[aks-migration]: ../aks/aks-migration.md
[aks-pod-managed-id]: ../aks/developer-best-practices-pod-security.md#use-pod-managed-identities
[aks-pod-managed-id-uninstall]: https://github.com/Azure/aad-pod-identity#uninstall-notes
[aks-restrict-egress-traffic]: ../aks/limit-egress-traffic.md
[aks-supported-k8s]: ../aks/supported-kubernetes-versions.md#azure-portal-and-cli-versions
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
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[vs-code-multi-root-workspaces]: https://code.visualstudio.com/docs/editor/multi-root-workspaces
[windows-containers]: how-to/run-dev-spaces-windows-containers.md