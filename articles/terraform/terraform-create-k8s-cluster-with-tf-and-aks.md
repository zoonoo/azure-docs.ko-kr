---
title: AKS(Azure Kubernetes Service) 및 Terraform을 사용하여 Kubernetes 클러스터 만들기
description: Azure Kubernetes Service 및 Terraform을 사용하여 Kubernetes 클러스터를 만드는 방법을 설명하는 자습서
services: terraform
ms.service: terraform
keywords: terraform, devops, 가상 머신, Azure, kubernetes
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 12/04/2018
ms.openlocfilehash: 57615ecd4c81359360004b732736bf757c0026c3
ms.sourcegitcommit: e51e940e1a0d4f6c3439ebe6674a7d0e92cdc152
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/08/2019
ms.locfileid: "55892068"
---
# <a name="create-a-kubernetes-cluster-with-azure-kubernetes-service-and-terraform"></a>Azure Kubernetes Service 및 Terraform을 사용하여 Kubernetes 클러스터 만들기
[AKS(Azure Kubernetes Service)](/azure/aks/)는 호스팅된 Kubernetes 환경을 관리하므로 컨테이너 오케스트레이션에 대한 전문 지식 없이 컨테이너화된 응용 프로그램을 빠르고 쉽게 배포하고 관리할 수 있습니다. 또한 응용 프로그램을 오프라인으로 변경하지 않고 주문형 리소스를 프로비전하고, 업그레이드하고, 크기 조정하여 진행 중인 작업 및 유지 관리 부담을 제거합니다.

이 자습서에서는 [Terraform](http://terraform.io) 및 AKS를 사용하여 [Kubernetes](https://www.redhat.com/en/topics/containers/what-is-kubernetes) 클러스터를 만드는 과정에서 다음 작업을 수행하는 방법에 대해 설명합니다.

> [!div class="checklist"]
> * HCL(HashiCorp Language)을 사용하여 Kubernetes 클러스터 정의
> * Terraform 및 AKS를 사용하여 Kubernetes 클러스터 만들기
> * kubectl 도구를 사용하여 Kubernetes 클러스터의 가용성 테스트

## <a name="prerequisites"></a>필수 조건

- **Azure 구독**: Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) 을 만듭니다.

- **Terraform 구성**: [Terraform 및 Azure에 액세스 구성](/azure/virtual-machines/linux/terraform-install-configure) 문서의 지침을 따릅니다.

- **Azure 서비스 주체**: [Azure CLI를 사용하여 Azure 서비스 주체 만들기](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest) 문서의 **서비스 주체 만들기** 섹션에 나온 지침을 따릅니다. appId, displayName, 암호, 테넌트를 기록해 둡니다.

## <a name="create-the-directory-structure"></a>디렉터리 구조 만들기
첫 번째 단계는 연습용 Terraform 구성 파일을 넣을 디렉터리를 만드는 것입니다.

1. [Azure Portal](http://portal.azure.com)로 이동합니다.

1. [Azure Cloud Shell](/azure/cloud-shell/overview)을 엽니다. 이전에 환경을 선택하지 않은 경우 환경으로 **Bash**를 선택합니다.

    ![Cloud Shell 프롬프트](./media/terraform-create-k8s-cluster-with-tf-and-aks/azure-portal-cloud-shell-button-min.png)

1. 디렉터리를 `clouddrive` 디렉터리로 변경합니다.

    ```bash
    cd clouddrive
    ```

1. 이름이 `terraform-aks-k8s`인 디렉터리를 만듭니다.

    ```bash
    mkdir terraform-aks-k8s
    ```

1. 디렉터리를 새 디렉터리로 변경합니다.

    ```bash
    cd terraform-aks-k8s
    ```

## <a name="declare-the-azure-provider"></a>Azure 공급자 선언
Azure 공급자를 선언하는 Terraform 구성 파일을 만듭니다.

1. Cloud Shell에서 이름이 `main.tf`인 파일을 만듭니다.

    ```bash
    vi main.tf
    ```

1. I 키를 선택하여 삽입 모드를 시작합니다.

1. 다음 코드를 편집기에 붙여 넣습니다.

    ```JSON
    provider "azurerm" {
        version = "~>1.5"
    }

    terraform {
        backend "azurerm" {}
    }
    ```

1. **Esc** 키를 선택하여 삽입 모드를 종료합니다.

1. 파일을 저장하고 다음 명령을 입력하여 vi 편집기를 종료합니다.

    ```bash
    :wq
    ```

## <a name="define-a-kubernetes-cluster"></a>Kubernetes 클러스터 정의
Kubernetes 클러스터용 리소스를 선언하는 Terraform 구성 파일을 만듭니다.

1. Cloud Shell에서 이름이 `k8s.tf`인 파일을 만듭니다.

    ```bash
    vi k8s.tf
    ```

1. I 키를 선택하여 삽입 모드를 시작합니다.

1. 다음 코드를 편집기에 붙여 넣습니다.

    ```JSON
    resource "azurerm_resource_group" "k8s" {
        name     = "${var.resource_group_name}"
        location = "${var.location}"
    }

    resource "azurerm_log_analytics_workspace" "test" {
        name                = "${var.log_analytics_workspace_name}"
        location            = "${var.log_analytics_workspace_location}"
        resource_group_name = "${azurerm_resource_group.k8s.name}"
        sku                 = "${var.log_analytics_workspace_sku}"
    }

    resource "azurerm_log_analytics_solution" "test" {
        solution_name         = "ContainerInsights"
        location              = "${azurerm_log_analytics_workspace.test.location}"
        resource_group_name   = "${azurerm_resource_group.k8s.name}"
        workspace_resource_id = "${azurerm_log_analytics_workspace.test.id}"
        workspace_name        = "${azurerm_log_analytics_workspace.test.name}"

        plan {
            publisher = "Microsoft"
            product   = "OMSGallery/ContainerInsights"
        }
    }

    resource "azurerm_kubernetes_cluster" "k8s" {
        name                = "${var.cluster_name}"
        location            = "${azurerm_resource_group.k8s.location}"
        resource_group_name = "${azurerm_resource_group.k8s.name}"
        dns_prefix          = "${var.dns_prefix}"

        linux_profile {
            admin_username = "ubuntu"

            ssh_key {
                key_data = "${file("${var.ssh_public_key}")}"
            }
        }

        agent_pool_profile {
            name            = "agentpool"
            count           = "${var.agent_count}"
            vm_size         = "Standard_DS1_v2"
            os_type         = "Linux"
            os_disk_size_gb = 30
        }

        service_principal {
            client_id     = "${var.client_id}"
            client_secret = "${var.client_secret}"
        }

        addon_profile {
            oms_agent {
            enabled                    = true
            log_analytics_workspace_id = "${azurerm_log_analytics_workspace.test.id}"
            }
        }

        tags {
            Environment = "Development"
        }
    }
    ```

    위의 코드는 클러스터의 이름, 위치 및 resource_group_name을 설정합니다. 클러스터에 액세스하는 데 사용되는 FQDN(정규화된 도메인 이름)의 일부를 구성하는 dns_prefix 값도 설정합니다.

    **linux_profile** 레코드를 사용하면 SSH를 사용하여 작업자 노드에 로그인하도록 만드는 설정을 구성할 수 있습니다.

    AKS를 사용하면 작업자 노드에 대해서만 지불합니다. **agent_pool_profile** 레코드는 이러한 작업자 노드에 대한 세부 정보를 구성합니다. **agent_pool_profile** 레코드에는 만들 작업자 노드 수와 작업자 노드 유형이 포함됩니다. 나중에 클러스터를 확장하거나 축소해야 하는 경우, 이 레코드의 **count** 값을 수정합니다.

1. **Esc** 키를 선택하여 삽입 모드를 종료합니다.

1. 파일을 저장하고 다음 명령을 입력하여 vi 편집기를 종료합니다.

    ```bash
    :wq
    ```

## <a name="declare-the-variables"></a>변수 선언

1. Cloud Shell에서 이름이 `variables.tf`인 파일을 만듭니다.

    ```bash
    vi variables.tf
    ```

1. I 키를 선택하여 삽입 모드를 시작합니다.

1. 다음 코드를 편집기에 붙여 넣습니다.

    ```JSON
    variable "client_id" {}
    variable "client_secret" {}

    variable "agent_count" {
        default = 3
    }

    variable "ssh_public_key" {
        default = "~/.ssh/id_rsa.pub"
    }

    variable "dns_prefix" {
        default = "k8stest"
    }

    variable cluster_name {
        default = "k8stest"
    }

    variable resource_group_name {
        default = "azure-k8stest"
    }

    variable location {
        default = "Central US"
    }

    variable log_analytics_workspace_name {
        default = "testLogAnalyticsWorkspaceName"
    }

    # refer https://azure.microsoft.com/global-infrastructure/services/?products=monitor for log analytics available regions
    variable log_analytics_workspace_location {
        default = "eastus"
    }

   # refer https://azure.microsoft.com/pricing/details/monitor/ for log analytics pricing 
   variable log_analytics_workspace_sku {
        default = "PerGB2018"
   }
    ```

1. **Esc** 키를 선택하여 삽입 모드를 종료합니다.

1. 파일을 저장하고 다음 명령을 입력하여 vi 편집기를 종료합니다.

    ```bash
    :wq
    ```

## <a name="create-a-terraform-output-file"></a>Terraform 출력 파일 만들기
[Terraform 출력](https://www.terraform.io/docs/configuration/outputs.html)을 사용하면 Terraform이 계획을 적용할 때 사용자에게 강조 표시되는 값 및 `terraform output` 명령을 사용하여 쿼리할 수 있는 값을 정의할 수 있습니다. 이 섹션에서는 [kubectl](https://kubernetes.io/docs/reference/kubectl/overview/) 도구를 사용하여 클러스터 액세스를 허용하는 출력 파일을 만듭니다.

1. Cloud Shell에서 이름이 `output.tf`인 파일을 만듭니다.

    ```bash
    vi output.tf
    ```

1. I 키를 선택하여 삽입 모드를 시작합니다.

1. 다음 코드를 편집기에 붙여 넣습니다.

    ```JSON
    output "client_key" {
        value = "${azurerm_kubernetes_cluster.k8s.kube_config.0.client_key}"
    }

    output "client_certificate" {
        value = "${azurerm_kubernetes_cluster.k8s.kube_config.0.client_certificate}"
    }

    output "cluster_ca_certificate" {
        value = "${azurerm_kubernetes_cluster.k8s.kube_config.0.cluster_ca_certificate}"
    }

    output "cluster_username" {
        value = "${azurerm_kubernetes_cluster.k8s.kube_config.0.username}"
    }

    output "cluster_password" {
        value = "${azurerm_kubernetes_cluster.k8s.kube_config.0.password}"
    }

    output "kube_config" {
        value = "${azurerm_kubernetes_cluster.k8s.kube_config_raw}"
    }

    output "host" {
        value = "${azurerm_kubernetes_cluster.k8s.kube_config.0.host}"
    }
    ```

1. **Esc** 키를 선택하여 삽입 모드를 종료합니다.

1. 파일을 저장하고 다음 명령을 입력하여 vi 편집기를 종료합니다.

    ```bash
    :wq
    ```

## <a name="set-up-azure-storage-to-store-terraform-state"></a>Terraform 상태를 저장하도록 Azure 저장소 설정
Terraform은 `terraform.tfstate` 파일을 통해 로컬로 상태를 추적합니다. 이 패턴은 단일 작업자 환경에서 잘 작동합니다. 그러나 실제의 다중 작업자 환경에서는 [Azure 저장소](/azure/storage/)를 사용하는 서버에서 상태를 추적해야 합니다. 이 섹션에서는 필요한 저장소 계정 정보(계정 이름 및 계정 키)를 검색하고 Terraform 상태 정보를 저장할 저장소 컨테이너를 만듭니다.

1. Azure Portal에서 왼쪽 메뉴에 있는 **모든 서비스**를 선택합니다.

1. **저장소 계정**을 선택합니다.

1. **저장소 계정** 탭에서 Terraform가 상태를 저장하도록 설정할 저장소 계정의 이름을 선택합니다. 예를 들어, Cloud Shell을 처음 열 때 만들어진 저장소 계정을 사용할 수 있습니다.  Cloud Shell에서 만든 저장소 계정 이름은 일반적으로 `cs`로 시작되고 그 뒤에 숫자 및 문자로 이루어진 임의의 문자열이 나옵니다. **선택한 저장소 계정의 이름은 나중에 필요하므로 기억해 둡니다.**

1. [저장소 계정] 탭에서 **액세스 키**를 선택합니다.

    ![저장소 계정 메뉴](./media/terraform-create-k8s-cluster-with-tf-and-aks/storage-account.png)

1. **key1** **key** 값을 기록해 웁니다. (키 오른쪽에 있는 아이콘을 선택하면 값이 클립보드에 복사됩니다.)

    ![저장소 계정 액세스 키](./media/terraform-create-k8s-cluster-with-tf-and-aks/storage-account-access-key.png)

1. Cloud Shell에서 Azure Storage 계정에 컨테이너를 만듭니다. &lt;YourAzureStorageAccountName> 및 &lt;YourAzureStorageAccountAccessKey> 자리 표시자를 Azure Storage 계정에 해당하는 값으로 바꾸면 됩니다.

    ```bash
    az storage container create -n tfstate --account-name <YourAzureStorageAccountName> --account-key <YourAzureStorageAccountKey>
    ```

## <a name="create-the-kubernetes-cluster"></a>Kubernetes 클러스터 만들기
이 섹션에서는 `terraform init` 명령을 사용하여 이전 섹션에서 만든 구성 파일을 정의하는 리소스를 만드는 방법을 보여 줍니다.

1. Cloud Shell에서 Terraform을 초기화합니다. &lt;YourAzureStorageAccountName> 및 &lt;YourAzureStorageAccountAccessKey> 자리 표시자를 Azure Storage 계정에 해당하는 값으로 바꾸면 됩니다.

    ```bash
    terraform init -backend-config="storage_account_name=<YourAzureStorageAccountName>" -backend-config="container_name=tfstate" -backend-config="access_key=<YourStorageAccountAccessKey>" -backend-config="key=codelab.microsoft.tfstate" 
    ```
    
    `terraform init` 명령은 백 엔드 및 공급자 플러그인 초기화 성공을 표시합니다.

    ![“terraform init” 결과의 예](./media/terraform-create-k8s-cluster-with-tf-and-aks/terraform-init-complete.png)

1. 서비스 주체 자격 증명 내보내기 &lt;your-client-id> 및 &lt;your-client-secret> 자리 표시자를 각각 서비스 주체와 연결된 **appId** 및 **password** 값으로 바꿉니다.

    ```bash
    export TF_VAR_client_id=<your-client-id>
    export TF_VAR_client_secret=<your-client-secret>
    ```

1. `terraform plan` 명령을 실행하여 인프라 요소를 정의하는 Terraform 계획을 만듭니다. 

    ```bash
    terraform plan -out out.plan
    ```

    `terraform plan` 명령은 `terraform apply` 명령 실행 시 만들어질 리소스를 표시합니다.

    ![“terraform plan” 결과의 예](./media/terraform-create-k8s-cluster-with-tf-and-aks/terraform-plan-complete.png)

1. `terraform apply` 명령을 실행하여 Kubernetes 클러스터를 만들 계획에 적용합니다. Kubernetes 클러스터를 만드는 프로세스는 몇 분 정도 소요될 수도 있으며 이 경우 Cloud Shell 세션 시간이 초과됩니다. Cloud Shell 세션의 시간이 초과되면 "Cloud Shell 시간 초과에서 복구" 섹션에 나온 단계에 따라 자습서를 완료할 수 있습니다.

    ```bash
    terraform apply out.plan
    ```

    `terraform apply` 명령은 구성 파일에 정의된 리소스 만들기의 결과를 표시합니다.

    ![“terraform apply” 결과의 예](./media/terraform-create-k8s-cluster-with-tf-and-aks/terraform-apply-complete.png)

1. Azure Portal에서 왼쪽 메뉴에 있는 **모든 서비스**를 선택합니다. 그러면 새 Kubernetes 클러스터에 대해 만든 리소스가 표시됩니다.

    ![Cloud Shell 프롬프트](./media/terraform-create-k8s-cluster-with-tf-and-aks/k8s-resources-created.png)

## <a name="recover-from-a-cloud-shell-timeout"></a>Cloud Shell 시간 초과에서 복구
Cloud Shell 세션의 시간이 초과되면 다음 단계를 수행하여 복구할 수 있습니다.

1. Cloud Shell 세션을 시작합니다.

1. Terraform 구성 파일이 있는 디렉터리로 변경합니다.

    ```bash
    cd /clouddrive/terraform-aks-k8s
    ```

1. 다음 명령 실행:

    ```bash
    export KUBECONFIG=./azurek8s
    ```
    
## <a name="test-the-kubernetes-cluster"></a>Kubernetes 클러스터 테스트
새로 만든 클러스터를 Kubernetes 도구를 사용해 확인할 수 있습니다.

1. Terraform 상태에서 Kubernetes 구성을 가져오고 이를 kubectl이 읽을 수 있는 파일에 저장합니다.

    ```bash
    echo "$(terraform output kube_config)" > ./azurek8s
    ```

1. kubectl이 올바른 구성을 선택할 수 있도록 환경 변수를 설정합니다.

    ```bash
    export KUBECONFIG=./azurek8s
    ```

1. 클러스터 상태를 확인합니다.

    ```bash
    kubectl get nodes
    ```

    작업자 노드의 세부 정보가 표시됩니다. 노드는 다음 이미지에 나온 대로 모두 **Ready**상태여야 합니다.

    ![kubectl 도구를 사용하여 Kubernetes 클러스터의 상태 확인](./media/terraform-create-k8s-cluster-with-tf-and-aks/kubectl-get-nodes.png)

## <a name="monitor-health-and-logs"></a>상태 및 로그 모니터링
AKS 클러스터가 생성될 때 클러스터 노드와 Pod의 상태 메트릭을 캡처하기 위해 모니터링이 설정되었습니다. 이 상태 메트릭은 Azure Portal에서 사용할 수 있습니다. 컨테이너 상태 모니터링에 대한 자세한 내용은 [Azure Kubernetes Service 상태 모니터링](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-overview)을 참조하세요.

## <a name="next-steps"></a>다음 단계
이 문서에서는 Terraform 및 AKS를 사용하여 Kubernetes 클러스터를 만드는 방법을 알아봤습니다. Azure의 Terraform에 대해 자세히 알아볼 수 있는 몇 가지 추가 리소스는 다음과 같습니다. 

 [Microsoft.com의 Terraform 허브](https://docs.microsoft.com/azure/terraform/)  
 [Terraform Azure 공급자 설명서](https://aka.ms/terraform)  
 [Terraform Azure 공급자 원본](https://aka.ms/tfgit)  
 [Terraform Azure 모듈](https://aka.ms/tfmodules)
