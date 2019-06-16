---
title: 컨테이너에 대 한 Azure Monitor를 사용 하는 방법 | Microsoft Docs
description: 이 문서에서는 사용 하는 방법을 설명 하 고 컨테이너를 수행 하는 방법 및 성능 관련 문제는 확인 된 이해할 수 있도록 컨테이너에 대 한 Azure Monitor를 구성 합니다.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/25/2019
ms.author: magoedte
ms.openlocfilehash: 5e149fa96e0a62656804906b52adf10273321d17
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65521907"
---
# <a name="how-to-enable-azure-monitor-for-containers"></a>컨테이너에 대 한 Azure Monitor를 사용 하는 방법  

이 문서에서는 Kubernetes 환경에 배포 되 고에서 호스트 되는 워크 로드의 성능을 모니터링 하려면 컨테이너에 대 한 Azure Monitor를 설치 하는 옵션에 간략하게 [Azure Kubernetes Service](https://docs.microsoft.com/azure/aks/)합니다.

컨테이너용 Azure Monitor는 다음과 같은 지원되는 방법을 사용하여 AKS의 새 배포 또는 하나 이상의 기존 배포에 사용할 수 있습니다.

* Azure 포털, Azure PowerShell 또는 Azure CLI를 사용 하 여
* [Terraform 및 AKS](../../terraform/terraform-create-k8s-cluster-with-tf-and-aks.md) 사용

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>필수 조건 
시작하기 전에 다음 항목이 있는지 확인하십시오.

- **Log Analytics 작업 영역입니다.** 새 AKS 클러스터 모니터링을 사용하도록 설정할 때 만들거나 온보드 환경에서 AKS 클러스터 구독의 기본 리소스 그룹에 기본 작업 영역을 만들도록 할 수 있습니다. 직접 만들려면 [Azure Resource Manager](../platform/template-workspace-configuration.md)를 통해 만들거나 [PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)을 통해 만들거나 [Azure Portal](../learn/quick-create-workspace.md)에서 만들 수 있습니다.
- 멤버인 사용자가 합니다 **Log Analytics 참가자 역할** 컨테이너 모니터링을 사용 하도록 설정 합니다. Log Analytics 작업 영역에 대한 액세스를 제어하는 방법에 대한 자세한 내용은 [작업 영역 관리](../platform/manage-access.md)를 참조하세요.
- 멤버인 사용자가 합니다 **[소유자](../../role-based-access-control/built-in-roles.md#owner)** AKS 클러스터 리소스에는 역할입니다. 

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

## <a name="components"></a>구성 요소 

성능을 모니터링하는 기능은 컨테이너용 Azure Monitor를 위해 특별히 개발된 Linux용 컨테이너화 Log Analytics 에이전트에 따라 달라집니다. 이 특수 에이전트는 클러스터의 모든 노드에서 성능 및 이벤트 데이터를 수집하며, 배포 과정에서 에이전트가 지정된 Log Analytics 작업 영역에 자동으로 배포 및 등록됩니다. 에이전트 버전은 microsoft/oms:ciprod04202018 이상이며, *mmddyyyy* 형식의 날짜로 표시됩니다. 

>[!NOTE]
>AKS에 대 한 Windows Server 지원의 미리 보기 릴리스를 사용 하 여 Windows 서버 노드를 사용 하 여 AKS 클러스터는 데이터를 수집 하 여 Azure Monitor로 전달할 설치 된 에이전트를 갖지 않습니다. 대신 표준 배포의 일환으로 클러스터에 자동으로 배포 된 Linux 노드에 수집 하 고 클러스터의 모든 Windows 노드를 대신 하 여 Azure Monitor에 데이터를 전달 합니다.  
>

새로운 버전의 에이전트가 릴리스되면 AKS(Azure Kubernetes Service)에 호스트된 관리형 Kubernetes 클러스터에서 에이전트가 자동으로 업그레이드됩니다. 릴리스된 버전을 따르려면 [에이전트 릴리스 알림](https://github.com/microsoft/docker-provider/tree/ci_feature_prod)을 참조하세요. 

>[!NOTE] 
>AKS 클러스터를 이미 배포한 경우, 이 문서의 뒷부분에 설명된 대로, Azure CLI 또는 제공된 Azure Resource Manager 템플릿을 사용하여 모니터링을 사용하도록 설정할 수 있습니다. `kubectl`을 사용하여 에이전트를 업그레이드, 삭제, 다시 배포 또는 배포할 수 없습니다. 템플릿을 클러스터와 동일한 리소스 그룹에 배포해야 합니다.

컨테이너에 대 한 다음 표에 설명 된 다음 방법 중 하나를 사용 하 여 Azure Monitor를 사용 합니다.

| 배포 상태 | 방법 | 설명 | 
|------------------|--------|-------------| 
| 새 AKS 클러스터 | [Azure CLI를 사용 하 여 클러스터 만들기](../../aks/kubernetes-walkthrough.md#create-aks-cluster)| Azure CLI를 사용 하 여 만든 새 AKS 클러스터의 모니터링을 사용할 수 있습니다. | 
| | [Terraform을 사용 하 여 클러스터 만들기](container-insights-enable-new-cluster.md#enable-using-terraform)| Terraform이 오픈 소스 도구를 사용 하 여 만든 새 AKS 클러스터의 모니터링을 사용할 수 있습니다. | 
| 기존 AKS 클러스터 | [Azure CLI를 사용 하 여 사용 하도록 설정](container-insights-enable-existing-clusters.md#enable-using-azure-cli) | 이미 Azure CLI를 사용 하 여 배포 된 AKS 클러스터의 모니터링을 사용할 수 있습니다. | 
| |[Terraform을 사용 하 여 사용 하도록 설정](container-insights-enable-existing-clusters.md#enable-using-terraform) | 이미 오픈 소스 도구 Terraform을 사용 하 여 배포 된 AKS 클러스터의 모니터링을 사용할 수 있습니다. | 
| | [Azure Monitor에서 사용 하도록 설정](container-insights-enable-existing-clusters.md#enable-from-azure-monitor-in-the-portal)| Azure Monitor에서 AKS 클러스터 다중 페이지에서 이미 배포에 대 한 하나 이상의 AKS 클러스터의 모니터링을 사용할 수 있습니다. | 
| | [AKS 클러스터에서 사용 하도록 설정](container-insights-enable-existing-clusters.md#enable-directly-from-aks-cluster-in-the-portal)| Azure portal에서 AKS 클러스터에서 직접 모니터링을 사용할 수 있습니다. | 
| | [Azure Resource Manager 템플릿을 사용 하 여 사용 하도록 설정](container-insights-enable-existing-clusters.md#enable-using-an-azure-resource-manager-template)| 미리 구성 된 Azure Resource Manager 템플릿 사용 하 여 AKS 클러스터의 모니터링을 사용할 수 있습니다. | 

## <a name="next-steps"></a>다음 단계

* 모니터링을 사용하여 AKS 클러스터 노드와 Pod에 관한 상태 메트릭을 캡처하면, Azure Portal에서 해당 상태 메트릭을 사용할 수 있습니다. 컨테이너용 Azure Monitor 사용 방법을 알아보려면 [Azure Kubernetes Service 상태 보기](container-insights-analyze.md)를 참조하세요.
