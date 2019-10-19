---
title: 컨테이너에 대해 Azure Monitor를 사용 하도록 설정 하는 방법 | Microsoft Docs
description: 이 문서에서는 컨테이너의 작동 방식 및 식별 된 성능 관련 문제를 이해할 수 있도록 컨테이너에 대해 Azure Monitor를 사용 하도록 설정 하 고 구성 하는 방법을 설명 합니다.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 07/12/2019
ms.openlocfilehash: 44cdc2d6b93ac9a62f96875ca6c679fbb97d85a9
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72555389"
---
# <a name="how-to-enable-azure-monitor-for-containers"></a>컨테이너에 대해 Azure Monitor를 사용 하도록 설정 하는 방법

이 문서에서는 Kubernetes 환경에 배포 되 고 [Azure Kubernetes Service](https://docs.microsoft.com/azure/aks/)에서 호스팅되는 워크 로드의 성능을 모니터링 하는 컨테이너에 대 한 설치 Azure Monitor에서 사용할 수 있는 옵션의 개요를 제공 합니다.

컨테이너용 Azure Monitor는 다음과 같은 지원되는 방법을 사용하여 AKS의 새 배포 또는 하나 이상의 기존 배포에 사용할 수 있습니다.

* Azure Portal, Azure PowerShell, Azure CLI
* [Terraform 및 AKS](../../terraform/terraform-create-k8s-cluster-with-tf-and-aks.md) 사용

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>전제 조건
시작하기 전에 다음 항목이 있는지 확인하십시오.

* **Log Analytics 작업 영역입니다.**

    컨테이너 Azure Monitor는 [지역별 Azure 제품](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor)에 나열 된 지역에서 Log Analytics 작업 영역을 지원 합니다.

    새 AKS 클러스터의 모니터링을 사용 하도록 설정 하거나 온 보 딩 환경에서 AKS 클러스터 구독의 기본 리소스 그룹에 기본 작업 영역을 만들 때 작업 영역을 만들 수 있습니다. 직접 만들려면 [Azure Resource Manager](../platform/template-workspace-configuration.md)를 통해 만들거나 [PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)을 통해 만들거나 [Azure Portal](../learn/quick-create-workspace.md)에서 만들 수 있습니다. 기본 작업 영역에 사용 되는 지원 되는 매핑 쌍 목록은 [컨테이너의 Azure Monitor에 대 한 지역 매핑](container-insights-region-mapping.md)을 참조 하세요.

* 컨테이너 모니터링을 사용 하도록 설정 하는 **Log Analytics 참여자 역할** 의 구성원입니다. Log Analytics 작업 영역에 대한 액세스를 제어하는 방법에 대한 자세한 내용은 [작업 영역 관리](../platform/manage-access.md)를 참조하세요.

* AKS 클러스터 리소스에 대 한 **[소유자](../../role-based-access-control/built-in-roles.md#owner)** 역할의 구성원입니다.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

* 기본적으로는 프로메테우스 메트릭이 수집 되지 않습니다. 에이전트를 수집 하도록 [에이전트를 구성](container-insights-agent-config.md) 하기 전에 먼저 프로메테우스 [설명서](https://prometheus.io/) 를 검토 하 여 정의할 수 있는 항목을 이해 하는 것이 중요 합니다.

## <a name="components"></a>구성 요소

성능을 모니터링하는 기능은 컨테이너용 Azure Monitor를 위해 특별히 개발된 Linux용 컨테이너화 Log Analytics 에이전트에 따라 달라집니다. 이 특수 에이전트는 클러스터의 모든 노드에서 성능 및 이벤트 데이터를 수집하며, 배포 과정에서 에이전트가 지정된 Log Analytics 작업 영역에 자동으로 배포 및 등록됩니다. 에이전트 버전은 microsoft/oms:ciprod04202018 이상이며, *mmddyyyy* 형식의 날짜로 표시됩니다.

>[!NOTE]
>AKS에 대 한 Windows Server 지원의 미리 보기 릴리스에서 Windows Server 노드가 있는 AKS 클러스터에는 데이터를 수집 하 고 Azure Monitor 전달 하기 위한 에이전트가 설치 되어 있지 않습니다. 대신, 표준 배포의 일부로 클러스터에 자동으로 배포 된 Linux 노드가 클러스터의 모든 Windows 노드를 대신 하 여 Azure Monitor 데이터를 수집 하 고 전달 합니다.  
>

새로운 버전의 에이전트가 릴리스되면 AKS(Azure Kubernetes Service)에 호스트된 관리형 Kubernetes 클러스터에서 에이전트가 자동으로 업그레이드됩니다. 릴리스된 버전을 따르려면 [에이전트 릴리스 알림](https://github.com/microsoft/docker-provider/tree/ci_feature_prod)을 참조하세요.

>[!NOTE]
>AKS 클러스터를 이미 배포한 경우, 이 문서의 뒷부분에 설명된 대로, Azure CLI 또는 제공된 Azure Resource Manager 템플릿을 사용하여 모니터링을 사용하도록 설정할 수 있습니다. `kubectl`을 사용하여 에이전트를 업그레이드, 삭제, 다시 배포 또는 배포할 수 없습니다.
>템플릿을 클러스터와 동일한 리소스 그룹에 배포해야 합니다.

다음 표에서 설명 하는 다음 방법 중 하나를 사용 하 여 컨테이너에 대 한 Azure Monitor를 사용 하도록 설정 합니다.

| 배포 상태 | 방법 | 설명 |
|------------------|--------|-------------|
| 새 AKS 클러스터 | [Azure CLI를 사용 하 여 클러스터 만들기](../../aks/kubernetes-walkthrough.md#create-aks-cluster)| Azure CLI를 사용 하 여 만든 새 AKS 클러스터의 모니터링을 사용 하도록 설정할 수 있습니다. |
| | [Terraform을 사용 하 여 클러스터 만들기](container-insights-enable-new-cluster.md#enable-using-terraform)| 오픈 소스 도구인 Terraform을 사용 하 여 만든 새 AKS 클러스터의 모니터링을 사용 하도록 설정할 수 있습니다. |
| 기존 AKS 클러스터 | [Azure CLI 사용 설정](container-insights-enable-existing-clusters.md#enable-using-azure-cli) | Azure CLI를 사용 하 여 이미 배포 된 AKS 클러스터의 모니터링을 사용 하도록 설정할 수 있습니다. |
| |[Terraform을 사용 하도록 설정](container-insights-enable-existing-clusters.md#enable-using-terraform) | 오픈 소스 도구 Terraform을 사용 하 여 이미 배포 된 AKS 클러스터의 모니터링을 사용 하도록 설정할 수 있습니다. |
| | [Azure Monitor에서 사용](container-insights-enable-existing-clusters.md#enable-from-azure-monitor-in-the-portal)| Azure Monitor의 AKS 다중 클러스터 페이지에서 이미 배포 된 하나 이상의 AKS 클러스터에 대 한 모니터링을 사용 하도록 설정할 수 있습니다. |
| | [AKS 클러스터에서 사용](container-insights-enable-existing-clusters.md#enable-directly-from-aks-cluster-in-the-portal)| Azure Portal의 AKS 클러스터에서 직접 모니터링을 사용 하도록 설정할 수 있습니다. |
| | [Azure Resource Manager 템플릿을 사용 하도록 설정](container-insights-enable-existing-clusters.md#enable-using-an-azure-resource-manager-template)| 미리 구성 된 Azure Resource Manager 템플릿을 사용 하 여 AKS 클러스터의 모니터링을 사용 하도록 설정할 수 있습니다. |

## <a name="next-steps"></a>다음 단계

* 모니터링을 사용하여 AKS 클러스터 노드와 Pod에 관한 상태 메트릭을 캡처하면, Azure Portal에서 해당 상태 메트릭을 사용할 수 있습니다. 컨테이너용 Azure Monitor 사용 방법을 알아보려면 [Azure Kubernetes Service 상태 보기](container-insights-analyze.md)를 참조하세요.
