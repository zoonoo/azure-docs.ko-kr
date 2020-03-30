---
title: 컨테이너에 대한 Azure 모니터를 사용하도록 설정하는 방법 | 마이크로 소프트 문서
description: 이 문서에서는 컨테이너의 성능과 확인된 성능 관련 문제를 이해할 수 있도록 컨테이너에 대해 Azure Monitor를 활성화하고 구성하는 방법을 설명합니다.
ms.topic: conceptual
ms.date: 11/18/2019
ms.openlocfilehash: 7aad7e7dd5ec2569377f9276c2e4793c7afd631a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275309"
---
# <a name="how-to-enable-azure-monitor-for-containers"></a>컨테이너에 대한 Azure 모니터를 사용하도록 설정하는 방법

이 문서에서는 Kubernetes 환경에 배포되고 호스팅되는 워크로드의 성능을 모니터링하기 위해 컨테이너에 대한 Azure Monitor를 설정하는 데 사용할 수 있는 옵션에 대한 개요를 제공합니다.

- [Azure Kubernetes](https://docs.microsoft.com/azure/aks/) 서비스(AKS)

- [AKS 엔진을](https://github.com/Azure/aks-engine)사용하여 Azure에서 호스팅되는 자체 관리 Kubernetes 클러스터.

- AKS 엔진을 사용하여 [Azure 스택](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1910) 또는 온-프레미스에서 호스팅되는 자체 관리 Kubernetes 클러스터입니다.

- [Azure Red Hat OpenShift](../../openshift/intro-openshift.md)

컨테이너에 대한 Azure Monitor는 다음 지원되는 방법을 사용하여 Kubernetes의 새 배포 또는 하나 이상의 기존 배포에 대해 활성화할 수 있습니다.

- Azure 포털, Azure PowerShell 또는 Azure CLI를 통해

- [Terraform 및 AKS](../../terraform/terraform-create-k8s-cluster-with-tf-and-aks.md) 사용

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>사전 요구 사항

시작하기 전에 다음 항목이 있는지 확인하십시오.

- **로그 분석 작업 영역입니다.**

    컨테이너용 Azure Monitor는 지역별로 Azure [Products에](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor)나열된 리전에서 로그 분석 작업 영역을 지원합니다.

    새 AKS 클러스터의 모니터링을 사용하도록 설정하거나 온보딩 환경이 AKS 클러스터 구독의 기본 리소스 그룹에 기본 작업 영역을 만들도록 할 때 작업 영역을 만들 수 있습니다. 직접 만들려면 [Azure Resource Manager](../platform/template-workspace-configuration.md)를 통해 만들거나 [PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)을 통해 만들거나 [Azure Portal](../learn/quick-create-workspace.md)에서 만들 수 있습니다. 기본 작업 영역에 사용되는 지원되는 매핑 쌍 목록은 [컨테이너에 대한 Azure Monitor의 지역 매핑을](container-insights-region-mapping.md)참조하십시오.

- 컨테이너 모니터링을 사용하도록 설정하는 **Log Analytics 기여자 역할의** 구성원입니다. Log Analytics 작업 영역에 대한 액세스를 제어하는 방법에 대한 자세한 내용은 [작업 영역 관리](../platform/manage-access.md)를 참조하세요.

- AKS 클러스터 리소스의 **[소유자](../../role-based-access-control/built-in-roles.md#owner)** 역할의 구성원입니다.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

* 프로메테우스 메트릭은 기본적으로 수집되지 않습니다. 에이전트를 수집하도록 [구성하기](container-insights-prometheus-integration.md) 전에 Prometheus [설명서를](https://prometheus.io/) 검토하여 스크랩할 수 있는 내용과 지원되는 방법을 이해하는 것이 중요합니다.

## <a name="supported-configurations"></a>지원되는 구성

다음은 컨테이너에 대한 Azure 모니터에서 공식적으로 지원됩니다.

- 환경: Azure Red Hat 오픈시프트, Kubernetes 온-프레미스 및 Azure 및 Azure 스택의 AKS 엔진. 자세한 내용은 [Azure 스택의 AKS 엔진을](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1908)참조하십시오.
- Kubernetes 및 지원 정책의 버전은 [지원되는 AKS](../../aks/supported-kubernetes-versions.md)버전과 동일합니다. 

## <a name="network-firewall-requirements"></a>네트워크 방화벽 요구 사항

다음 표의 정보는 컨테이너화된 에이전트가 컨테이너에 대해 Azure Monitor와 통신하는 데 필요한 프록시 및 방화벽 구성 정보를 나열합니다. 에이전트의 모든 네트워크 트래픽은 Azure Monitor로 아웃바운드됩니다.

|에이전트 리소스|포트 |
|--------------|------|
| *.ods.opinsights.azure.com | 443 |  
| *.oms.opinsights.azure.com | 443 | 
| \*.blob.core.windows.net | 443 |
| dc.services.visualstudio.com | 443 |
| *.microsoftonline.com | 443 |
| *.monitoring.azure.com | 443 |
| login.microsoftonline.com | 443 |

다음 표의 정보는 Azure China의 프록시 및 방화벽 구성 정보를 나열합니다.

|에이전트 리소스|포트 |설명 | 
|--------------|------|-------------|
| *.ods.opinsights.azure.cn | 443 | 데이터 수집 |
| *oms.opinsights.azure.cn | 443 | OMS 온보딩 |
| \*.blob.core.windows.net | 443 | 아웃바운드 연결을 모니터링하는 데 사용됩니다. |
| microsoft.com | 80 | 네트워크 연결에 사용됩니다. 에이전트 이미지 버전이 ciprod009262019 이상인 경우에만 이 됩니다. |
| dc.services.visualstudio.com | 443 | Azure 퍼블릭 클라우드 응용 프로그램 인사이트를 사용하는 에이전트 원격 분석용. |

다음 표의 정보는 Azure 미국 정부의 프록시 및 방화벽 구성 정보를 나열합니다.

|에이전트 리소스|포트 |설명 | 
|--------------|------|-------------|
| *.ods.opinsights.azure.us | 443 | 데이터 수집 |
| *.oms.opinsights.azure.us | 443 | OMS 온보딩 |
| \*.blob.core.windows.net | 443 | 아웃바운드 연결을 모니터링하는 데 사용됩니다. |
| microsoft.com | 80 | 네트워크 연결에 사용됩니다. 에이전트 이미지 버전이 ciprod009262019 이상인 경우에만 이 됩니다. |
| dc.services.visualstudio.com | 443 | Azure 퍼블릭 클라우드 응용 프로그램 인사이트를 사용하는 에이전트 원격 분석용. |

## <a name="components"></a>구성 요소

성능을 모니터링하는 기능은 컨테이너용 Azure Monitor용으로 특별히 개발된 Linux용 컨테이너화된 로그 분석 에이전트에 의존합니다. 이 특수 에이전트는 클러스터의 모든 노드에서 성능 및 이벤트 데이터를 수집하며, 배포 과정에서 에이전트가 지정된 Log Analytics 작업 영역에 자동으로 배포 및 등록됩니다. 에이전트 버전은 microsoft/oms:ciprod04202018 이상이며, *mmddyyyy* 형식의 날짜로 표시됩니다.

>[!NOTE]
>AKS에 대한 Windows Server 지원의 미리 보기 릴리스를 통해 Windows Server 노드가 있는 AKS 클러스터에는 데이터를 수집하고 Azure Monitor로 전달하기 위해 에이전트가 설치되어 있지 않습니다. 대신 표준 배포의 일부로 클러스터에 자동으로 배포되는 Linux 노드는 클러스터의 모든 Windows 노드를 대신하여 데이터를 수집하고 Azure Monitor로 전달합니다.  
>

새로운 버전의 에이전트가 릴리스되면 AKS(Azure Kubernetes Service)에 호스트된 관리형 Kubernetes 클러스터에서 에이전트가 자동으로 업그레이드됩니다. 릴리스된 버전을 따르려면 [에이전트 릴리스 알림](https://github.com/microsoft/docker-provider/tree/ci_feature_prod)을 참조하세요.

>[!NOTE]
>AKS 클러스터를 이미 배포한 경우, 이 문서의 뒷부분에 설명된 대로, Azure CLI 또는 제공된 Azure Resource Manager 템플릿을 사용하여 모니터링을 사용하도록 설정할 수 있습니다. `kubectl`을 사용하여 에이전트를 업그레이드, 삭제, 다시 배포 또는 배포할 수 없습니다.
>템플릿을 클러스터와 동일한 리소스 그룹에 배포해야 합니다.

다음 표에 설명된 다음 방법 중 하나를 사용하여 컨테이너에 대한 Azure Monitor를 사용하도록 설정합니다.

| 배포 상태 | 방법 | 설명 |
|------------------|--------|-------------|
| 새로운 AKS 쿠베네츠 클러스터 | [Azure CLI를 사용하여 AKS 클러스터 만들기](../../aks/kubernetes-walkthrough.md#create-aks-cluster)| Azure CLI를 사용하여 만드는 새 AKS 클러스터의 모니터링을 활성화할 수 있습니다. |
| | [테라폼을 사용하여 AKS 클러스터 만들기](container-insights-enable-new-cluster.md#enable-using-terraform)| 오픈 소스 도구 Terraform을 사용하여 만든 새 AKS 클러스터를 모니터링할 수 있습니다. |
| | [Azure 리소스 관리자 템플릿을 사용하여 OpenShift 클러스터 만들기](container-insights-azure-redhat-setup.md#enable-for-a-new-cluster-using-an-azure-resource-manager-template) | 미리 구성된 Azure 리소스 관리자 템플릿으로 만든 새 OpenShift 클러스터의 모니터링을 활성화할 수 있습니다. |
| | [Azure CLI를 사용하여 오픈시프트 클러스터 만들기](https://docs.microsoft.com/cli/azure/openshift?view=azure-cli-latest#az-openshift-create) | Azure CLI를 사용하여 새 OpenShift 클러스터를 배포하는 동안 모니터링을 활성화할 수 있습니다. |
| 기존 AKS 쿠베네츠 클러스터 | [Azure CLI를 사용하여 AKS 클러스터에 대해 사용](container-insights-enable-existing-clusters.md#enable-using-azure-cli) | Azure CLI를 사용하여 이미 배포된 AKS 클러스터의 모니터링을 활성화할 수 있습니다. |
| |[테라폼을 사용하여 AKS 클러스터에 사용 가능](container-insights-enable-existing-clusters.md#enable-using-terraform) | 오픈 소스 도구 Terraform을 사용하여 이미 배포된 AKS 클러스터를 모니터링할 수 있습니다. |
| | [Azure 모니터에서 AKS 클러스터에 대해 사용](container-insights-enable-existing-clusters.md#enable-from-azure-monitor-in-the-portal)| Azure Monitor의 다중 클러스터 페이지에서 이미 배포된 하나 이상의 AKS 클러스터를 모니터링하도록 설정할 수 있습니다. |
| | [AKS 클러스터에서 사용](container-insights-enable-existing-clusters.md#enable-directly-from-aks-cluster-in-the-portal)| Azure 포털의 AKS 클러스터에서 직접 모니터링을 활성화할 수 있습니다. |
| | [Azure 리소스 관리자 템플릿을 사용하여 AKS 클러스터에 대해 사용](container-insights-enable-existing-clusters.md#enable-using-an-azure-resource-manager-template)| 미리 구성된 Azure 리소스 관리자 템플릿을 사용하여 AKS 클러스터를 모니터링할 수 있습니다. |
| | [하이브리드 Kubernetes 클러스터에 대한 사용](container-insights-hybrid-setup.md) | Azure 스택 또는 온-프레미스에서 호스팅되는 Kubernetes에 대해 호스팅되는 AKS 엔진의 모니터링을 활성화할 수 있습니다. |
| | [Azure 리소스 관리자 템플릿을 사용하여 OpenShift 클러스터에 대해 사용](container-insights-azure-redhat-setup.md#enable-using-an-azure-resource-manager-template) | 미리 구성된 Azure 리소스 관리자 템플릿을 사용하여 기존 OpenShift 클러스터를 모니터링할 수 있습니다. |
| | [Azure 모니터에서 OpenShift 클러스터에 대해 사용](container-insights-azure-redhat-setup.md#from-the-azure-portal) | Azure Monitor의 다중 클러스터 페이지에서 이미 배포된 하나 이상의 OpenShift 클러스터를 모니터링하도록 설정할 수 있습니다. |

## <a name="next-steps"></a>다음 단계

- 모니터링을 사용하도록 설정하면 AKS(Azure Kubernetes Service), Azure 스택 또는 기타 환경에서 호스팅되는 Kubernetes 클러스터의 성능을 분석할 수 있습니다. 컨테이너에 Azure 모니터를 사용하는 방법에 대해 알아보려면 [Kubernetes 클러스터 성능 보기를](container-insights-analyze.md)참조하십시오.
