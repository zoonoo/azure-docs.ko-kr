---
title: Ansible을 사용하여 Azure에서 Azure Kubernetes Service 클러스터 만들기 및 구성
description: Ansible을 사용하여 Azure에서 Azure Kubernetes Service 클러스터를 만들고 관리하는 방법 알아보기
ms.service: ansible
keywords: ansible, azure, devops, bash, cloudshell, 플레이북, aks, 컨테이너, Kubernetes
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 08/23/2018
ms.openlocfilehash: f17998957db6fca5b109b5a05aa624e238af8dca
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/11/2018
ms.locfileid: "53257542"
---
# <a name="create-and-configure-azure-kubernetes-service-clusters-in-azure-using-ansible"></a>Ansible을 사용하여 Azure에서 Azure Kubernetes Service 클러스터 만들기 및 구성
Ansible을 사용하면 사용자 환경에서 리소스의 배포 및 구성을 자동화할 수 있습니다. Ansible을 사용하여 AKS(Azure Kubernetes Service)를 관리할 수 있습니다. 이 문서에서는 Ansible을 사용하여 Azure에서 Azure Kubernetes Service 클러스터를 만들고 구성하는 방법을 보여줍니다.

## <a name="prerequisites"></a>필수 조건
- **Azure 구독** - Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)을 만듭니다.
- **Azure 서비스 주체** - [서비스 주체를 만들](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest#create-the-service-principal) 경우 다음 값, **appId**, **displayName**, **암호**  및 **테넌트**를 참고합니다.

- [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation2.md)]

> [!Note]
> Ansible 2.6은 이 자습서에서 다음의 샘플 플레이북을 실행해야 합니다. 

## <a name="create-a-managed-aks-cluster"></a>관리되는 AKS 클러스터 만들기
다음 샘플 Ansible 플레이북은 리소스 그룹 및 리소스 그룹에 상주하는 AKS 클러스터를 만듭니다.

  ```yaml
  - name: Create Azure Kubernetes Service
    hosts: localhost
    connection: local
    vars:
      resource_group: myResourceGroup
      location: eastus
      aks_name: myAKSCluster
      username: azureuser
      ssh_key: "your_ssh_key"
      client_id: "your_client_id"
      client_secret: "your_client_secret"
    tasks:
    - name: Create resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        location: "{{ location }}"
    - name: Create a managed Azure Container Services (AKS) cluster
      azure_rm_aks:
        name: "{{ aks_name }}"
        location: "{{ location }}"
        resource_group: "{{ resource_group }}"
        dns_prefix: "{{ aks_name }}"
        linux_profile:
          admin_username: "{{ username }}"
          ssh_key: "{{ ssh_key }}"
        service_principal:
          client_id: "{{ client_id }}"
          client_secret: "{{ client_secret }}"
        agent_pool_profiles:
          - name: default
            count: 2
            vm_size: Standard_D2_v2
        tags:
          Environment: Production
  ```

다음 글머리 기호는 앞의 Ansible 플레이북 코드를 설명하는 데 도움이 됩니다.
- **작업** 내 첫 번째 섹션은 **미국 동부** 위치 내에서 **myResourceGroup**이라는 리소스 그룹을 정의합니다. 
- **작업** 내 두 번째 섹션은 **myResourceGroup** 리소스 그룹 내에서 **myAKSCluster**라는 AKS 클러스터를 정의합니다. 

Ansible을 사용하여 AKS 클러스터를 만들려면 위의 샘플 플레이북을 `azure_create_aks.yml`로 저장하고 다음 명령을 사용하여 플레이북을 실행합니다.

  ```bash
  ansible-playbook azure_create_aks.yml
  ```

**ansible-playbook* 명령의 출력은 AKS 클러스터가 성공적으로 만들어졌음을 보여주는 다음과 비슷합니다.

  ```Output
  PLAY [Create AKS] ****************************************************************************************

  TASK [Gathering Facts] ********************************************************************************************
  ok: [localhost]

  TASK [Create resource group] **************************************************************************************
  changed: [localhost]

  TASK [Create a Azure Container Services (AKS) cluster] ***************************************************
  changed: [localhost]

  PLAY RECAP *********************************************************************************************************
  localhost                  : ok=3    changed=2    unreachable=0    failed=0
  ```

## <a name="scale-aks-nodes"></a>AKS 노드 크기 조정

이전 섹션에서 샘플 플레이북은 두 노드를 정의합니다. 클러스터에 대한 컨테이너 워크로드를 더 늘리거나 줄일 필요가 있는 경우 노드 수를 쉽게 조정할 수 있습니다. 이 섹션의 샘플 플레이북은 노드 수를 2개에서 3개로 증가시킵니다. 노드 수 수정은 **agent_pool_profiles** 블록에서 **개수** 값을 변경하여 완료합니다. 

**service_principal** 블록에 `ssh_key`, `client_id` 및 `client_secret`을 입력합니다.

```yaml
- name: Scale AKS cluster
  hosts: localhost
  connection: local
  vars:
    resource_group: myResourceGroup
    location: eastus
    aks_name: myAKSCluster
    username: azureuser
    ssh_key: "your_ssh_key"
    client_id: "your_client_id"
    client_secret: "your_client_secret"
  tasks:
  - name: Scaling an existed AKS cluster
    azure_rm_aks:
        name: "{{ aks_name }}"    
        location: "{{ location }}"
        resource_group: "{{ resource_group }}" 
        dns_prefix: "{{ aks_name }}" 
        linux_profile:
          admin_username: "{{ username }}"
          ssh_key: "{{ ssh_key }}"
        service_principal:
          client_id: "{{ client_id }}"
          client_secret: "{{ client_secret }}"
        agent_pool_profiles:
          - name: default
            count: 3
            vm_size: Standard_D2_v2
```

Ansible 사용하여 Azure Kubernetes Service 클러스터 크기를 조정하려면 이전 플레이북을 *azure_configure_aks.yml*로 저장하고 다음과 같이 플레이북을 실행합니다.

  ```bash
  ansible-playbook azure_configure_aks.yml
  ```

다음 출력은 AKS 클러스터가 성공적으로 만들어졌음을 보여줍니다.

  ```Output
  PLAY [Scale AKS cluster] ***************************************************************

  TASK [Gathering Facts] ******************************************************************
  ok: [localhost]

  TASK [Scaling an existed AKS cluster] **************************************************
  changed: [localhost]

  PLAY RECAP ******************************************************************************
  localhost                  : ok=2    changed=1    unreachable=0    failed=0
  ```
## <a name="delete-a-managed-aks-cluster"></a>관리되는 AKS 클러스터 삭제

다음 샘플 Ansible 플레이북 섹션에서는 AKS 클러스터를 삭제하는 방법을 보여줍니다.

  ```yaml
  - name: Delete a managed Azure Container Services (AKS) cluster
    hosts: localhost
    connection: local
    vars:
      resource_group: myResourceGroup
      aks_name: myAKSCluster
    tasks:
    - name: 
      azure_rm_aks:
        name: "{{ aks_name }}"
        resource_group: "{{ resource_group }}"
        state: absent
   ```

Ansible 사용하여 Azure Kubernetes Service 클러스터를 삭제하려면 이전 플레이북을 *azure_delete_aks.yml*으로 저장하고, 다음과 같이 플레이북을 실행합니다.

  ```bash
  ansible-playbook azure_delete_aks.yml
  ```

다음 출력은 AKS 클러스터가 성공적으로 삭제되었음을 보여줍니다.
  ```Output
PLAY [Delete a managed Azure Container Services (AKS) cluster] ****************************

TASK [Gathering Facts] ********************************************************************
ok: [localhost]

TASK [azure_rm_aks] *********************************************************************

PLAY RECAP *********************************************************************
localhost                  : ok=2    changed=1    unreachable=0    failed=0
  ```
  
## <a name="next-steps"></a>다음 단계
> [!div class="nextstepaction"] 
> [자습서: AKS(Azure Kubernetes Service)에서 애플리케이션 크기 조정](https://docs.microsoft.com/azure/aks/tutorial-kubernetes-scale)
