---
title: Ansible을 사용하여 Azure에서 가상 머신 확장 집합 만들기
description: Azure에서 Ansible을 사용하여 가상 머신 확장 집합을 만들고 구성하는 방법 알아보기
ms.service: ansible
keywords: ansible, azure, devops, bash, 플레이북, 가상 머신, 가상 머신 확장 집합, vmss
author: tomarcher
manager: jpconnock
editor: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.date: 07/11/2018
ms.author: tarcher
ms.openlocfilehash: 5f915f7b1b425a3bd6e5d62eb70bb3f633b7eda8
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/13/2018
ms.locfileid: "39011711"
---
# <a name="create-virtual-machine-scale-sets-in-azure-using-ansible"></a>Ansible을 사용하여 Azure에서 가상 머신 확장 집합 만들기
Ansible을 사용하면 사용자 환경에서 리소스의 배포 및 구성을 자동화할 수 있습니다. Ansible을 사용하여 다른 Azure 리소스와 동일한 방식으로 Azure에서 가상 머신 확장 집합(VMSS)을 관리할 수 있습니다. 이 문서에서는 Ansible을 사용하여 가상 머신 확장 집합을 만들고 확장하는 방법을 보여줍니다. 

## <a name="prerequisites"></a>필수 조건
- **Azure 구독** - Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)을 만듭니다.
- **Ansible 구성** - [Azure 자격 증명 만들기 및 Ansible 구성](../virtual-machines/linux/ansible-install-configure.md#create-azure-credentials)
- **Ansible 및 Azure Python SDK 모듈** 
  - [CentOS 7.4](../virtual-machines/linux/ansible-install-configure.md#centos-74)
  - [Ubuntu 16.04 LTS](../virtual-machines/linux/ansible-install-configure.md#ubuntu-1604-lts)
  - [SLES 12 SP2](../virtual-machines/linux/ansible-install-configure.md#sles-12-sp2)

> [!Note]
> Ansible 2.6은 이 자습서에서 다음의 샘플 플레이북을 실행해야 합니다. 

## <a name="create-a-vmss"></a>VMSS 만들기
이 섹션에서는 다음 리소스를 정의하는 샘플 Ansible 플레이북을 제공합니다.
- 모든 리소스가 배포될 **리소스 그룹**
- 10.0.0.0/16 주소 공간의 **가상 네트워크**
- 가상 네트워크 내의 **서브넷**
- 인터넷을 통해 리소스에 액세스할 수 있는 **공용 IP 주소**
- 가상 머신 확장 집합 내/외부 네트워크 트래픽의 흐름을 제어하는 **네트워크 보안 그룹**
- 부하 분산 장치 규칙을 사용하여 정의된 VM 집합 전역에 트래픽을 분산하는 **부하 분산 장치**
- 생성된 모든 리소스를 사용하는 **가상 머신 확장 집합**

*admin_password* 값에 사용자 고유의 암호를 입력합니다.

  ```yaml
  - hosts: localhost
    vars:
      resource_group: myResourceGroup
      vmss_name: myVMSS
      vmss_lb_name: myVMSSlb
      location: eastus
      admin_username: azureuser
      admin_password: "your_password"
    tasks:
      - name: Create a resource group
        azure_rm_resourcegroup:
          name: "{{ resource_group }}"
          location: "{{ location }}"
      - name: Create virtual network
        azure_rm_virtualnetwork:
          resource_group: "{{ resource_group }}"
          name: "{{ vmss_name }}"
          address_prefixes: "10.0.0.0/16"
      - name: Add subnet
        azure_rm_subnet:
          resource_group: "{{ resource_group }}"
          name: "{{ vmss_name }}"
          address_prefix: "10.0.1.0/24"
          virtual_network: "{{ vmss_name }}"
      - name: Create public IP address
        azure_rm_publicipaddress:
          resource_group: "{{ resource_group }}"
          allocation_method: Static
          name: "{{ vmss_name }}"
      - name: Create Network Security Group that allows SSH
        azure_rm_securitygroup:
          resource_group: "{{ resource_group }}"
          name: "{{ vmss_name }}"
          rules:
            - name: SSH
              protocol: Tcp
              destination_port_range: 22
              access: Allow
              priority: 1001
              direction: Inbound

      - name: Create a load balancer
        azure_rm_loadbalancer:
          name: "{{ vmss_lb_name }}"
          location: "{{ location }}"
          resource_group: "{{ resource_group }}"
          public_ip: "{{ vmss_name }}"
          probe_protocol: Tcp
          probe_port: 8080
          probe_interval: 10
          probe_fail_count: 3
          protocol: Tcp
          load_distribution: Default
          frontend_port: 80
          backend_port: 8080
          idle_timeout: 4
          natpool_frontend_port_start: 50000
          natpool_frontend_port_end: 50040
          natpool_backend_port: 22
          natpool_protocol: Tcp

      - name: Create VMSS
        azure_rm_virtualmachine_scaleset:
          resource_group: "{{ resource_group }}"
          name: "{{ vmss_name }}"
          vm_size: Standard_DS1_v2
          admin_username: "{{ admin_username }}"
          admin_password: "{{ admin_password }}"
          ssh_password_enabled: true
          capacity: 2
          virtual_network_name: "{{ vmss_name }}"
          subnet_name: "{{ vmss_name }}"
          upgrade_policy: Manual
          tier: Standard
          managed_disk_type: Standard_LRS
          os_disk_caching: ReadWrite
          image:
            offer: UbuntuServer
            publisher: Canonical
            sku: 16.04-LTS
            version: latest
          load_balancer: "{{ vmss_lb_name }}"
          data_disks:
            - lun: 0
              disk_size_gb: 20
              managed_disk_type: Standard_LRS
              caching: ReadOnly
            - lun: 1
              disk_size_gb: 30
              managed_disk_type: Standard_LRS
              caching: ReadOnly
  ```

Ansible을 사용하여 가상 머신 확장 집합 환경을 만들려면 이전 플레이북을 `vmss-create.yml`로 저장하거나, [샘플 Ansible 플레이북을 다운로드](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss/vmss-create.yml)합니다.

Ansible 플레이북을 실행하려면 다음과 같이 **ansible-playbook** 명령을 사용합니다.

  ```bash
  ansible-playbook vmss-create.yml
  ```

플레이북을 실행한 후 다음 예제와 비슷한 출력에서 가상 머신 확장 집합이 성공적으로 만들어졌음을 보여줍니다.

  ```bash
  PLAY [localhost] ***********************************************************

  TASK [Gathering Facts] *****************************************************
  ok: [localhost]

  TASK [Create a resource group] ****************************************************************************
  changed: [localhost]

  TASK [Create virtual network] ****************************************************************************
  changed: [localhost]

  TASK [Add subnet] **********************************************************
  changed: [localhost]

  TASK [Create public IP address] ****************************************************************************
  changed: [localhost]

  TASK [Create Network Security Group that allows SSH] ****************************************************************************
  changed: [localhost]

  TASK [Create a load balancer] ****************************************************************************
  changed: [localhost]

  TASK [Create VMSS] *********************************************************
  changed: [localhost]

  PLAY RECAP *****************************************************************
  localhost                  : ok=8    changed=7    unreachable=0    failed=0

  ```

## <a name="scale-out-a-vmss"></a>VMSS 확장
만든 가상 머신 확장 집합에는 두 개의 인스턴스가 있습니다. Azure Portal에서 가상 머신 확장 집합으로 이동하는 경우 **Standard_DS1_v2(2 instances)** 를 참조합니다. Cloud Shell 내에서 다음 명령을 실행하여 [Azure Cloud Shell](https://shell.azure.com/)을 사용할 수도 있습니다.

  ```azurecli-interactive
  az vmss show -n myVMSS -g myResourceGroup --query '{"capacity":sku.capacity}' 
  ```

다음과 유사하게 출력되어야 합니다.

  ```bash
  {
    "capacity": 2,
  }
  ```

이제 두 개의 인스턴스에서 세 개의 인스턴스로 확장해보겠습니다. 다음 Ansible 플레이북 코드는 가상 머신 확장에 대한 정보를 검색하고 해당 용량을 2에서 3으로 변경합니다. 

  ```yaml
  - hosts: localhost
    vars:
      resource_group: myResourceGroup
      vmss_name: myVMSS
    tasks: 
      - name: Get scaleset info
        azure_rm_virtualmachine_scaleset_facts:
          resource_group: "{{ resource_group }}"
          name: "{{ vmss_name }}"
          format: curated
        register: output_scaleset

      - name: Dump scaleset info
        debug:
          var: output_scaleset

      - name: Modify scaleset (change the capacity to 3)
        set_fact:
          body: "{{ output_scaleset.ansible_facts.azure_vmss[0] | combine({'capacity': 3}, recursive=True) }}"

      - name: Update something in that VMSS
        azure_rm_virtualmachine_scaleset: "{{ body }}"
  ```

만든 가상 머신 확장 집합을 확장하려면 이전 플레이북을 `vmss-scale-out.yml`로 저장하거나, [샘플 Ansible 플레이북을 다운로드](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss/vmss-scale-out.yml)합니다. 

다음 명령이 플레이북을 실행합니다.

  ```bash
  ansible-playbook vmss-scale-out.yml
  ```

Ansible 플레이북을 실행한 결과는 가상 머신 확장 집합이 성공적으로 확장됐음을 보여줍니다.

  ```bash
  PLAY [localhost] **********************************************************

  TASK [Gathering Facts] ****************************************************
  ok: [localhost]

  TASK [Get scaleset info] ***************************************************************************
  ok: [localhost]

  TASK [Dump scaleset info] ***************************************************************************
  ok: [localhost] => {
      "output_scaleset": {
          "ansible_facts": {
              "azure_vmss": [
                  {
                      ......
                  }
              ]
          },
          "changed": false,
          "failed": false
      }
  }

  TASK [Modify scaleset (set upgradePolicy to Automatic and capacity to 3)] ***************************************************************************
  ok: [localhost]

  TASK [Update something in that VMSS] ***************************************************************************
  changed: [localhost]

  PLAY RECAP ****************************************************************
  localhost                  : ok=5    changed=1    unreachable=0    failed=0
  ```

Azure Portal에서 구성한 가상 머신 확장 집합으로 이동하는 경우 **Standard_DS1_v2(3 instances)** 를 참조합니다. 다음 명령을 실행하여 [Azure Cloud Shell](https://shell.azure.com/)에서 변경을 확인할 수도 있습니다.

  ```azurecli-interactive
  az vmss show -n myVMSS -g myResourceGroup --query '{"capacity":sku.capacity}' 
  ```

Cloud Shell에서 명령을 실행한 결과는 이제 세 개의 인스턴스가 있음을 보여줍니다. 

  ```bash
  {
    "capacity": 3,
  }
  ```

## <a name="next-steps"></a>다음 단계
> [!div class="nextstepaction"] 
> [VMSS에 대한 Ansible 샘플 플레이북](https://github.com/Azure-Samples/ansible-playbooks/tree/master/vmss)