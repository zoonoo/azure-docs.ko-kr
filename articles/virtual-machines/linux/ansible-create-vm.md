---
title: Azure에서 Ansible을 사용하여 Linux 가상 머신 만들기
description: Azure에서 Ansible을 사용하여 Linux 가상 머신을 만드는 방법 알아보기
ms.service: ansible
keywords: ansible, azure, devops, 가상 머신
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.topic: quickstart
ms.date: 08/22/2018
ms.openlocfilehash: bf2116d7fc143e66309e3bbbdefc50bfa33f1521
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46974564"
---
# <a name="use-ansible-to-create-a-linux-virtual-machine-in-azure"></a>Azure에서 Ansible을 사용하여 Linux 가상 머신 만들기
선언적 언어인 Ansible을 사용하면 Ansible *플레이북*을 통해 자동으로 Azure 리소스를 만들고, 구성하고, 배포할 수 있습니다. 이 문서의 각 섹션에서는 Ansible 플레이북의 각 섹션이 Linux 가상 머신의 여러 측면을 만들고 구성하기 위해 어떻게 사용되는지 보여줍니다. [전체 Ansible 플레이북](#complete-sample-ansible-playbook)은 이 문서의 마지막 부분에 나열되어 있습니다.

## <a name="prerequisites"></a>필수 조건

- **Azure 구독** - Azure 구독이 아직 없는 경우 [무료 계정](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)을 만듭니다.

- [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation1.md](../../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation1.md)]

## <a name="create-a-resource-group"></a>리소스 그룹 만들기
Ansible을 사용하려면 리소스가 배포되는 그룹이 필요합니다. 다음 샘플 Ansible 플레이북 섹션에서는 `eastus` 위치에 `myResourceGroup`이라는 리소스 그룹을 만듭니다.

```yaml
- name: Create resource group
    azure_rm_resourcegroup:
      name: myResourceGroup
      location: eastus
```

## <a name="create-a-virtual-network"></a>가상 네트워크 만들기
Azure 가상 머신을 만들 때 [가상 네트워크](/azure/virtual-network/virtual-networks-overview)를 만들거나 기존 가상 네트워크를 사용해야 합니다. 또한 가상 네트워크에서 가상 머신에 액세스하는 방법도 결정해야 합니다. 다음 샘플 Ansible 플레이북 섹션에서는 `10.0.0.0/16` 주소 공간에 `myVnet`이라는 가상 네트워크를 만듭니다.

```yaml
- name: Create virtual network
  azure_rm_virtualnetwork:
    resource_group: myResourceGroup
    name: myVnet
    address_prefixes: "10.0.0.0/16"
```

가상 네트워크에 배포된 모든 Azure 리소스는 가상 네트워크 내의 [서브넷](/azure/virtual-network/virtual-network-manage-subnet)에 배포됩니다. 

다음 샘플 Ansible 플레이북 섹션에서는 `myVnet` 가상 네트워크에 `mySubnet`이라는 가상 네트워크를 만듭니다.

```yaml
- name: Add subnet
  azure_rm_subnet:
    resource_group: myResourceGroup
    name: mySubnet
    address_prefix: "10.0.1.0/24"
    virtual_network: myVnet
```

## <a name="create-a-public-ip-address"></a>공용 IP 주소 만들기
[공용 IP 주소](/azure/virtual-network/virtual-network-ip-addresses-overview-arm)를 통해 인터넷 리소스가 Azure 리소스에 대한 인바운드와 통신할 수 있습니다. 또한 공용 IP 주소를 사용하면 Azure 리소스가 리소스에 할당된 IP 주소에 인터넷 및 공용 Azure 서비스에 대한 아웃바운드와 통신할 수 있습니다. 주소는 사용자가 할당 해제하지 않는 한 리소스에 전용됩니다. 공용 IP 주소가 리소스에 할당되지 않은 경우, 리소스는 여전히 인터넷에 대한 아웃바운드와 통신할 수 있지만, Azure는 리소스에 전용되지 않는 사용 가능한 IP 주소를 동적으로 할당 합니다. 

다음 샘플 Ansible 플레이북 섹션에서는 `myPublicIP`라는 공용 IP 주소를 만듭니다.

```yaml
- name: Create public IP address
  azure_rm_publicipaddress:
    resource_group: myResourceGroup
    allocation_method: Static
    name: myPublicIP
```

## <a name="create-a-network-security-group"></a>네트워크 보안 그룹 만들기
[네트워크 보안 그룹](/azure/virtual-network/security-overview)을 사용하면 Azure 가상 네트워크의 Azure 리소스와 주고받는 네트워크 트래픽을 필터링할 수 있습니다. 네트워크 보안 그룹에는 여러 종류의 Azure 리소스에서 오는 인바운드 트래픽 또는 이러한 리소스로 나가는 아웃바운드 네트워크 트래픽을 허용하거나 거부하는 보안 규칙이 포함됩니다. 

다음 샘플 Ansible 플레이북 섹션에서는 `myNetworkSecurityGroup`이라는 네트워크 보안 그룹을 만들고 TCP 포트 22에서 SSH 트래픽을 허용하는 규칙을 정의합니다.

```yaml
- name: Create Network Security Group that allows SSH
  azure_rm_securitygroup:
    resource_group: myResourceGroup
    name: myNetworkSecurityGroup
    rules:
      - name: SSH
        protocol: Tcp
        destination_port_range: 22
        access: Allow
        priority: 1001
        direction: Inbound
```


## <a name="create-a-virtual-network-interface-card"></a>가상 네트워크 인터페이스 카드 만들기
가상 네트워크 인터페이스 카드는 지정된 가상 네트워크, 공용 IP 주소 및 네트워크 보안 그룹에 가상 머신을 연결합니다. 

다음 샘플 Ansible 플레이북 섹션에서는 사용자가 만든 가상 네트워킹 리소스에 연결되는 `myNIC`라는 가상 네트워크 인터페이스 카드를 만듭니다.

```yaml
- name: Create virtual network inteface card
  azure_rm_networkinterface:
    resource_group: myResourceGroup
    name: myNIC
    virtual_network: myVnet
    subnet: mySubnet
    public_ip_name: myPublicIP
    security_group: myNetworkSecurityGroup
```

## <a name="create-a-virtual-machine"></a>가상 머신 만들기
마지막 단계는 이 문서의 이전 섹션에서 만든 모든 리소스를 사용하는 가상 머신을 만드는 것입니다. 

이 섹션에 제공된 샘플 Ansible 플레이북 섹션에서는 `myVM`이라는 가상 머신을 만들어서 `myNIC`라는 가상 네트워크 인터페이스 카드에 연결합니다. &lt;your-key-data> 자리 표시자를 본인의 완전한 공개 키 데이터로 바꿉니다.

```yaml
- name: Create VM
  azure_rm_virtualmachine:
    resource_group: myResourceGroup
    name: myVM
    vm_size: Standard_DS1_v2
    admin_username: azureuser
    ssh_password_enabled: false
    ssh_public_keys:
      - path: /home/azureuser/.ssh/authorized_keys
        key_data: <your-key-data>
    network_interfaces: myNIC
    image:
      offer: CentOS
      publisher: OpenLogic
      sku: '7.5'
      version: latest
```

## <a name="complete-sample-ansible-playbook"></a>전체 샘플 Ansible 플레이북

이 섹션에서는 이 문서의 과정에서 작성한 전체 샘플 Ansible 플레이북을 나열합니다. 

```yaml
- name: Create Azure VM
  hosts: localhost
  connection: local
  tasks:
  - name: Create resource group
    azure_rm_resourcegroup:
      name: myResourceGroup
      location: eastus
  - name: Create virtual network
    azure_rm_virtualnetwork:
      resource_group: myResourceGroup
      name: myVnet
      address_prefixes: "10.0.0.0/16"
  - name: Add subnet
    azure_rm_subnet:
      resource_group: myResourceGroup
      name: mySubnet
      address_prefix: "10.0.1.0/24"
      virtual_network: myVnet
  - name: Create public IP address
    azure_rm_publicipaddress:
      resource_group: myResourceGroup
      allocation_method: Static
      name: myPublicIP
    register: output_ip_address
  - name: Dump public IP for VM which will be created
    debug:
      msg: "The public IP is {{ output_ip_address.state.ip_address }}."
  - name: Create Network Security Group that allows SSH
    azure_rm_securitygroup:
      resource_group: myResourceGroup
      name: myNetworkSecurityGroup
      rules:
        - name: SSH
          protocol: Tcp
          destination_port_range: 22
          access: Allow
          priority: 1001
          direction: Inbound
  - name: Create virtual network inteface card
    azure_rm_networkinterface:
      resource_group: myResourceGroup
      name: myNIC
      virtual_network: myVnet
      subnet: mySubnet
      public_ip_name: myPublicIP
      security_group: myNetworkSecurityGroup
  - name: Create VM
    azure_rm_virtualmachine:
      resource_group: myResourceGroup
      name: myVM
      vm_size: Standard_DS1_v2
      admin_username: azureuser
      ssh_password_enabled: false
      ssh_public_keys:
        - path: /home/azureuser/.ssh/authorized_keys
          key_data: <your-key-data>
      network_interfaces: myNIC
      image:
        offer: CentOS
        publisher: OpenLogic
        sku: '7.5'
        version: latest
```

## <a name="run-the-sample-ansible-playbook"></a>샘플 Ansible 플레이북 실행

이 섹션에서는 이 문서에 제공된 샘플 Ansible 플레이북을 실행하는 방법을 안내합니다.

1. [Azure 포털](http://go.microsoft.com/fwlink/p/?LinkID=525040)에 로그인합니다.

1. [Cloud Shell](/azure/cloud-shell/overview)을 엽니다.

1. 다음과 같이 플레이북을 포함할 `azure_create_complete_vm.yml`이라는 파일을 만들고 VI 편집기에서 엽니다.

  ```azurecli-interactive
  vi azure_create_complete_vm.yml
  ```

1. **I** 키를 선택하여 삽입 모드를시작합니다.

1. 편집기에 [전체 샘플 Ansible 플레이북](#complete-sample-ansible-playbook)을 붙여넣습니다.

1. **Esc** 키를 선택하여 삽입 모드를 종료합니다.

1. 파일을 저장하고 다음 명령을 입력하여 vi 편집기를 종료합니다.

    ```bash
    :wq
    ```

1. 샘플 Ansible 플레이북을 실행합니다.

  ```bash
  ansible-playbook azure_create_complete_vm.yml
  ```

1. 출력은 가상 머신이 성공적으로 만들어졌음을 보여주는 다음 예제와 유사합니다.

  ```bash
  PLAY [Create Azure VM] ****************************************************

  TASK [Gathering Facts] ****************************************************
  ok: [localhost]

  TASK [Create resource group] *********************************************
  changed: [localhost]

  TASK [Create virtual network] *********************************************
  changed: [localhost]

  TASK [Add subnet] *********************************************************
  changed: [localhost]

  TASK [Create public IP address] *******************************************
  changed: [localhost]

  TASK [Dump public IP for VM which will be created] ********************************************************************
  ok: [localhost] => {
      "msg": "The public IP is <ip-address>."
  }

  TASK [Create Network Security Group that allows SSH] **********************
  changed: [localhost]

  TASK [Create virtual network inteface card] *******************************
  changed: [localhost]

  TASK [Create VM] **********************************************************
  changed: [localhost]

  PLAY RECAP ****************************************************************
  localhost                  : ok=8    changed=7    unreachable=0    failed=0
  ```

1. SSH 명령은 Linux VM에 액세스하는 데 사용됩니다. &lt;ip-address> 자리 표시자를 이전 단계의 IP 주소로 바꿉니다.

  ```bash
  ssh azureuser@<ip-address>
  ```

## <a name="next-steps"></a>다음 단계
> [!div class="nextstepaction"] 
> [Azure에서 Ansible을 사용하여 Linux 가상 머신 관리](./ansible-manage-linux-vm.md)
