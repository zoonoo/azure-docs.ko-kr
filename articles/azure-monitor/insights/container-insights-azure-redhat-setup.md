---
title: Configure Azure Red Hat OpenShift clusters with Azure Monitor for containers | Microsoft Docs
description: This article describes how you can configure Azure Monitor for containers to monitor Kubernetes clusters hosted on Azure Red Hat OpenShift.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 11/21/2019
ms.openlocfilehash: 965ebdb5b6450a0826872ac31d96d8e61b7542f5
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74382253"
---
# <a name="configure-azure-red-hat-openshift-clusters-with-azure-monitor-for-containers"></a>Configure Azure Red Hat OpenShift clusters with Azure Monitor for containers

Azure Monitor for containers provides rich monitoring experience for the Azure Kubernetes Service (AKS) and AKS Engine clusters. This article describes how to enable monitoring of Kubernetes clusters hosted on [Azure Red Hat OpenShift](../../openshift/intro-openshift.md) to achieve a similar monitoring experience.

>[!NOTE]
>Support for Red Hat OpenShift is a feature in public preview at this time.
>

Azure Monitor for containers can be enabled for new, or one or more existing deployments of Azure Red Hat OpenShift using the following supported methods:

- For an existing cluster from the Azure portal or using Azure Resource Manager template
- For a new cluster using Azure Resource Manager template 

## <a name="supported-and-unsupported-features"></a>Supported and unsupported features

Azure Monitor for containers supports monitoring Azure Red Hat OpenShift as described in the [Overview](container-insights-overview.md) article, except for the following features:

- Live data
- Prometheus metrics scraping
- [Collect metrics](container-insights-update-metrics.md) from cluster nodes and pods and storing them in the Azure Monitor metrics database
- Health feature

## <a name="prerequisites"></a>전제 조건

- To enable and access the features in Azure Monitor for containers, at a minimum you need to be a member of the Azure *Contributor* role in the Azure subscription, and a member of the [*Log Analytics Contributor*](../platform/manage-access.md#manage-access-using-azure-permissions) role of the Log Analytics workspace configured with Azure Monitor for containers.

- To view the monitoring data, you are a member of the [*Log Analytics reader*](../platform/manage-access.md#manage-access-using-azure-permissions) role permission with the Log Analytics workspace configured with Azure Monitor for containers.

## <a name="enable-for-a-new-cluster-using-an-azure-resource-manager-template"></a>Enable for a new cluster using an Azure Resource Manager template

Perform the following steps to deploy an Azure Red Hat OpenShift cluster with monitoring enabled. Before proceeding, review the tutorial [Create an Azure Red Hat OpenShift cluster](../../openshift/tutorial-create-cluster.md#prerequisites) to understand the dependencies that you need to configure so your environment is set up correctly.

이 메서드는 두 가지 JSON 템플릿을 포함합니다. One template specifies the configuration to deploy the cluster with monitoring enabled, and the other contains parameter values that you configure to specify the following:

- The Azure Red Hat OpenShift cluster resource ID. 

- The resource group the cluster is deployed in.

- [Azure Active Directory tenant ID](../../openshift/howto-create-tenant.md#create-a-new-azure-ad-tenant) noted after performing the steps to create one or one already created.

- [Azure Active Directory client application ID](../../openshift/howto-aad-app-configuration.md#create-an-azure-ad-app-registration) noted after performing the steps to create one or one already created.

- [Azure Active Directory Client secret](../../openshift/howto-aad-app-configuration.md#create-a-client-secret) noted after performing the steps to create one or one already created.

- [Azure AD security group](../../openshift/howto-aad-app-configuration.md#create-an-azure-ad-security-group) noted after performing the steps to create one or one already created.

- Resource ID of an existing Log Analytics workspace.

- The number of master nodes to create in the cluster.

- The number of compute nodes in the agent pool profile.

- The number of infrastructure nodes in the agent pool profile. 

템플릿을 사용하여 리소스를 배포하는 개념에 익숙하지 않은 경우 다음을 참조하십시오.

- [Resource Manager 템플릿과 Azure PowerShell로 리소스 배포](../../azure-resource-manager/resource-group-template-deploy.md)

- [Resource Manager 템플릿과 Azure CLI로 리소스 배포](../../azure-resource-manager/resource-group-template-deploy-cli.md)

Azure CLI를 사용하도록 선택한 경우, 먼저 CLI를 로컬에 설치하고 사용해야 합니다. You must be running the Azure CLI version 2.0.65 or later. 버전을 확인하려면 `az --version`을 실행합니다. Azure CLI를 설치하거나 업그레이드해야 하는 경우 [Azure CLI 설치](https://docs.microsoft.com/cli/azure/install-azure-cli)를 참조하세요. 

The Log Analytics workspace has to be created before you enable monitoring using Azure PowerShell or CLI. 작업 영역을 만들려면 [Azure Resource Manager](../../azure-monitor/platform/template-workspace-configuration.md)나 [PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)을 통해 또는 [Azure Portal](../../azure-monitor/learn/quick-create-workspace.md)에서 설정할 수 있습니다.

1. Download and save to a local folder, the Azure Resource Manager template and parameter file, to create a cluster with the monitoring add-on using the following commands:

    `curl -LO https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/aro/enable_monitoring_to_new_cluster/newClusterWithMonitoring.json`

    `curl -LO https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/aro/enable_monitoring_to_new_cluster/newClusterWithMonitoringParam.json` 

2. Azure에 로그인 

    ```azurecli
    az login    
    ```
    
    여러 구독에 액세스할 수 있으면 `az account set -s {subscription ID}`에서 `{subscription ID}`를 사용하려는 구독으로 바꿔서 실행합니다.
 
3. Create a resource group for your cluster if you don't already have one. For a list of Azure regions that supports OpenShift on Azure, see [Supported Regions](../../openshift/supported-resources.md#azure-regions). 

    ```azurecli
    az group create -g <clusterResourceGroup> -l <location> 
    ```

4. Edit the JSON parameter file **newClusterWithMonitoringParam.json** and update the following values:

    - *위치*
    - *clusterName*
    - *aadTenantId*
    - *aadClientId*
    - *aadClientSecret* 
    - *aadCustomerAdminGroupId* 
    - *workspaceResourceId*
    - *masterNodeCount*
    - *computeNodeCount*
    - *infraNodeCount*

5. The following step deploys the cluster with monitoring enabled by using the Azure CLI. 

    ```azurecli
    az group deployment create --resource-group <ClusterResourceGroupName> --template-file ./newClusterWithMonitoring.json --parameters @./newClusterWithMonitoringParam.json 
    ```
 
    출력은 다음과 유사합니다.

    ```azurecli
    provisioningState       : Succeeded
    ```

## <a name="enable-for-an-existing-cluster"></a>Enable for an existing cluster

Perform the following steps to enable monitoring of an Azure Red Hat OpenShift cluster deployed in Azure. You can accomplish this from the Azure portal or using the provided templates.

### <a name="from-the-azure-portal"></a>Azure Portal에서
 
1. [Azure portal](https://portal.azure.com)에 로그인합니다.

2. On the Azure portal menu or from the Home page, select **Azure Monitor**. **인사이트** 섹션에서 **컨테이너**를 선택합니다. 

3. **모니터 - 컨테이너** 페이지에서 **모니터링되지 않는 클러스터**를 선택합니다.

4. From the list of non-monitored clusters, find the cluster in the list and click **Enable**. You can identify the results in the list by looking for the value **ARO** under the column **CLUSTER TYPE**.

5. 클러스터와 동일한 구독에 기존 Log Analytics 작업 영역이 있는 경우 **컨테이너용 Azure Monitor에 온보딩** 페이지의 드롭다운 목록에서 해당 작업 영역을 선택합니다.  
    The list preselects the default workspace and location that the cluster is deployed to in the subscription. 

    ![Enable monitoring for non-monitored clusters](./media/container-insights-onboard/kubernetes-onboard-brownfield-01.png)

    >[!NOTE]
    >클러스터의 모니터링 데이터를 저장하기 위해 새 Log Analytics 작업 영역을 만들려면 [Log Analytics 작업 영역 만들기](../../azure-monitor/learn/quick-create-workspace.md)를 참조하세요. Be sure to create the workspace in the same subscription that the RedHat OpenShift cluster is deployed to. 
 
모니터링을 사용하도록 설정하고 약 15분 후에 클러스터에 대한 상태 메트릭을 볼 수 있습니다. 

### <a name="enable-using-an-azure-resource-manager-template"></a>Enable using an Azure Resource Manager template

이 메서드는 두 가지 JSON 템플릿을 포함합니다. 한 가지 템플릿은 모니터링을 사용하도록 구성을 지정하고, 다른 템플릿은 다음을 지정하도록 구성하는 매개 변수 값을 포함합니다.

- The Azure RedHat OpenShift cluster resource ID. 

- The resource group the cluster is deployed in.

- Log Analytics 작업 영역.

템플릿을 사용하여 리소스를 배포하는 개념에 익숙하지 않은 경우 다음을 참조하십시오.

- [Resource Manager 템플릿과 Azure PowerShell로 리소스 배포](../../azure-resource-manager/resource-group-template-deploy.md)

- [Resource Manager 템플릿과 Azure CLI로 리소스 배포](../../azure-resource-manager/resource-group-template-deploy-cli.md)

Azure CLI를 사용하도록 선택한 경우, 먼저 CLI를 로컬에 설치하고 사용해야 합니다. You must be running the Azure CLI version 2.0.65 or later. 버전을 확인하려면 `az --version`을 실행합니다. Azure CLI를 설치하거나 업그레이드해야 하는 경우 [Azure CLI 설치](https://docs.microsoft.com/cli/azure/install-azure-cli)를 참조하세요. 

The Log Analytics workspace has to be created before you enable monitoring using Azure PowerShell or CLI. 작업 영역을 만들려면 [Azure Resource Manager](../../azure-monitor/platform/template-workspace-configuration.md)나 [PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)을 통해 또는 [Azure Portal](../../azure-monitor/learn/quick-create-workspace.md)에서 설정할 수 있습니다.

1. Download the template and parameter file to update your cluster with the monitoring add-on using the following commands:

    `curl -LO https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/aro/enable_monitoring_to_existing_cluster/existingClusterOnboarding.json`

    `curl -LO https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/aro/enable_monitoring_to_existing_cluster/existingClusterParam.json` 

2. Azure에 로그인 

    ```azurecli
    az login    
    ```

    여러 구독에 액세스할 수 있으면 `az account set -s {subscription ID}`에서 `{subscription ID}`를 사용하려는 구독으로 바꿔서 실행합니다.

3. Specify the subscription of the Azure RedHat OpenShift cluster.

    ```azurecli
    az account set --subscription "Subscription Name"  
    ```

4. Run the following command to identify the cluster location and resource ID:

    ```azurecli
    az openshift show -g <clusterResourceGroup> -n <clusterName> 
    ```

5. Edit the JSON parameter file **existingClusterParam.json** and update the values *araResourceId* and *araResoruceLocation*. **workspaceResourceId** 값은 Log Analytics 작업 영역의 전체 리소스 ID 이며, 작업 영역 이름을 포함합니다. 

6. To deploy with Azure CLI, run the following commands: 

    ```azurecli
    az group deployment create --resource-group <ClusterResourceGroupName> --template-file ./ExistingClusterOnboarding.json --parameters @./existingClusterParam.json 
    ```

    출력은 다음과 유사합니다.

    ```azurecli
    provisioningState       : Succeeded
    ```

## <a name="next-steps"></a>다음 단계

- With monitoring enabled to collect health and resource utilization of your RedHat OpenShift cluster and workloads running on them, learn [how to use](container-insights-analyze.md) Azure Monitor for containers.

- To learn how to stop monitoring your cluster with Azure Monitor for containers, see [How to Stop Monitoring Your Azure Red Hat OpenShift cluster](container-insights-optout-openshift.md).