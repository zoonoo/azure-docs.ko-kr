---
title: Ansible을 사용하여 Azure 경로 테이블 만들기, 변경 또는 삭제
description: Ansible을 사용하여 경로 테이블을 만들거나, 변경하거나, 삭제하는 방법을 알아봅니다.
ms.service: ansible
keywords: Ansible, Azure, DevOps, Bash, 플레이북, 네트워킹, 경로, 경로 테이블
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 12/17/2018
ms.openlocfilehash: 5d7a16ebda088d0ed42021e9def20a398e914ad9
ms.sourcegitcommit: 33091f0ecf6d79d434fa90e76d11af48fd7ed16d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/09/2019
ms.locfileid: "54158129"
---
# <a name="create-change-or-delete-an-azure-route-table-using-ansible"></a>Ansible을 사용하여 Azure 경로 테이블 만들기, 변경 또는 삭제
Azure는 Azure 서브넷, 가상 네트워크 및 온-프레미스 네트워크 간에 트래픽을 자동으로 라우트합니다. Azure에서 기본 라우팅 중 하나를 변경하려면 [경로 테이블](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview)을 만들어 변경합니다.

Ansible을 사용하면 사용자 환경에서 리소스의 배포 및 구성을 자동화할 수 있습니다. 이 문서에서는 Azure 경로 테이블을 만들거나, 변경하거나, 삭제하고 경로 테이블을 서브넷에 연결하는 방법을 보여 줍니다. 

## <a name="prerequisites"></a>필수 조건
- **Azure 구독** - Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)을 만듭니다.
- [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation2.md)]

> [!Note]
> 이 자습서에서 다음 플레이북 샘플을 실행하려면 Ansible 2.7이 필요합니다.

## <a name="create-a-route-table"></a>경로 테이블 만들기
이 섹션에서는 경로 테이블을 만드는 Ansible 플레이북 샘플이 제공됩니다. Azure 위치와 구독별로 만들 수 있는 경로 테이블 수에 제한이 있습니다. 자세한 내용은 [Azure 제한](https://docs.microsoft.com/azure/azure-subscription-service-limits?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)을 참조하세요. 

```yml
- hosts: localhost
  vars:
    route_table_name: myRouteTable
    resource_group: myResourceGroup
  tasks:
    - name: Create a route table
      azure_rm_routetable:
        name: "{{ route_table_name }}"
        resource_group: "{{ resource_group }}"
```

이 플레이북을 `route_table_create.yml`로 저장합니다. 플레이북을 실행하려면 다음과 같이 **ansible-playbook** 명령을 사용합니다.

```bash
ansible-playbook route_table_create.yml
```

## <a name="associate-a-route-table-to-a-subnet"></a>서브넷에 경로 테이블 연결
서브넷에는 0개 또는 1개의 경로 테이블이 연결될 수 있습니다. 경로 테이블은 0개 또는 여러 개의 서브넷에 연결할 수 있습니다. 경로 테이블은 가상 네트워크에 연결되지 않기 때문에 경로 테이블을 연결하려는 각 서브넷에 경로 테이블을 연결해야 합니다. 서브넷에서 나가는 모든 트래픽은 경로 테이블 내에서 만든 경로인 [기본 경로](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#default)를 기반으로 라우트되고, 가상 네트워크가 Azure 가상 네트워크 게이트웨이에 연결된 경우에는 온-프레미스 네트워크에서 전파된 경로(ExpressRoute, VPN Gateway에서 BGP를 사용하는 경우에는 VPN)를 기반으로 라우트됩니다. 경로 테이블과 동일한 Azure 위치 및 구독에 있는 가상 네트워크의 서브넷에만 경로 테이블을 연결할 수 있습니다.

이 섹션에서는 가상 네트워크와 제출을 만든 다음, 경로 테이블을 서브넷에 연결하는 Ansible 플레이북 샘플이 제공됩니다.

```yml
- hosts: localhost
  vars:
    subnet_name: mySubnet
    virtual_network_name: myVirtualNetwork 
    route_table_name: myRouteTable
    resource_group: myResourceGroup
  tasks:
    - name: Create virtual network
      azure_rm_virtualnetwork:
        name: "{{ virtual_network_name }}"
        resource_group: "{{ resource_group }}"
        address_prefixes_cidr:
        - 10.1.0.0/16
        - 172.100.0.0/16
        dns_servers:
        - 127.0.0.1
        - 127.0.0.3
    - name: Create a subnet with route table
      azure_rm_subnet:
        name: "{{ subnet_name }}"
        virtual_network_name: "{{ virtual_network_name }}"
        resource_group: "{{ resource_group }}"
        address_prefix_cidr: "10.1.0.0/24"
        route_table: "{ route_table_name }"
```

이 플레이북을 `route_table_associate.yml`로 저장합니다. Ansible 플레이북을 실행하려면 다음과 같이 **ansible-playbook** 명령을 사용합니다.

```bash
ansible-playbook route_table_associate.yml
```

## <a name="dissociate-a-route-table-from-a-subnet"></a>서브넷에서 경로 테이블 분리
서브넷에서 경로 테이블을 분리하는 경우 서브넷의 `route_table`을 `None`으로 설정하면 됩니다. Ansible 플레이북 샘플은 다음과 같습니다. 

```yml
- hosts: localhost
  vars:
    subnet_name: mySubnet
    virtual_network_name: myVirtualNetwork 
    resource_group: myResourceGroup
  tasks:
    - name: Dissociate a route table
      azure_rm_subnet:
        name: "{{ subnet_name }}"
        virtual_network_name: "{{ virtual_network_name }}"
        resource_group: "{{ resource_group }}"
        address_prefix_cidr: "10.1.0.0/24"
```

이 플레이북을 `route_table_dissociate.yml`로 저장합니다. Ansible 플레이북을 실행하려면 다음과 같이 **ansible-playbook** 명령을 사용합니다.

```bash
ansible-playbook route_table_dissociate.yml
```

## <a name="create-a-route"></a>경로 만들기
이 섹션에서는 경로 테이블 아래에 경로를 만드는 Ansible 플레이북 샘플이 제공됩니다. `virtual_network_gateway`를 `next_hop_type`으로 정의하고, `10.1.0.0/16`을 `address_prefix`로 정의합니다. 접두사는 경로 테이블 내 둘 이상의 경로에서 중복될 수 없지만 다른 접두사 내에서는 중복 가능합니다. Azure에서 경로를 선택하는 방법과 모든 다음 홉 유형에 대한 자세한 내용은 [라우팅 개요](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview)를 참조하세요.

```yml
- hosts: localhost
  vars:
    route_name: myRoute
    route_table_name: myRouteTable
    resource_group: myResourceGroup
  tasks:
    - name: Create route
      azure_rm_route:
        name: "{{ route_name }}"
        resource_group: "{{ resource_group }}"
        next_hop_type: virtual_network_gateway
        address_prefix: "10.1.0.0/16"
        route_table_name: "{{ route_table_name }}"
```
이 플레이북을 `route_create.yml`로 저장합니다. Ansible 플레이북을 실행하려면 다음과 같이 **ansible-playbook** 명령을 사용합니다.

```bash
ansible-playbook route_create.yml
```

## <a name="delete-a-route"></a>경로 삭제
이 섹션에서는 경로 테이블에서 경로를 삭제하는 Ansible 플레이북 샘플이 제공됩니다.

```yml
- hosts: localhost
  vars:
    route_name: myRoute
    route_table_name: myRouteTable
    resource_group: myResourceGroup
  tasks:
    - name: Remove route
      azure_rm_route:
        name: "{{ route_name }}"
        resource_group: "{{ resource_group }}"
        route_table_name: "{{ route_table_name }}"
        state: absent
```

이 플레이북을 `route_delete.yml`로 저장합니다. Ansible 플레이북을 실행하려면 다음과 같이 **ansible-playbook** 명령을 사용합니다.

```bash
ansible-playbook route_delete.yml
```

## <a name="get-information-of-a-route-table"></a>경로 테이블 정보 가져오기
`azure_rm_routetable_facts`라는 Ansible 모듈을 통해 route_table의 세부 정보를 볼 수 있습니다. facts 모듈은 모든 경로가 연결된 경로 테이블의 정보를 반환합니다.
Ansible 플레이북 샘플은 다음과 같습니다. 

```yml
- hosts: localhost
  vars:
    route_table_name: myRouteTable
    resource_group: myResourceGroup
  tasks: 
    - name: Get route table information
      azure_rm_routetable_facts:
         resource_group: "{{ resource_group }}"
         name: "{{ route_table_name }}"
      register: query
    
    - debug:
         var: query.route_tables[0]
```

이 플레이북을 `route_table_facts.yml`로 저장합니다. Ansible 플레이북을 실행하려면 다음과 같이 **ansible-playbook** 명령을 사용합니다.

```bash
ansible-playbook route_table_facts.yml
```

## <a name="delete-a-route-table"></a>경로 테이블 삭제
경로 테이블이 모든 서브넷에 연결된 경우에는 경로 테이블을 삭제할 수 없습니다. 경로 테이블을 삭제하기 전에 모든 서브넷에서 [분리](#dissociate-a-route-table-from-a-subnet)합니다.

경로 테이블은 모든 경로와 함께 삭제할 수 있습니다. Ansible 플레이북 샘플은 다음과 같습니다. 

```yml
- hosts: localhost
  vars:
    route_table_name: myRouteTable
    resource_group: myResourceGroup
  tasks:
    - name: Create a route table
      azure_rm_routetable:
        name: "{{ route_table_name }}"
        resource_group: "{{ resource_group }}"
        state: absent
```

이 플레이북을 `route_table_delete.yml`로 저장합니다. Ansible 플레이북을 실행하려면 다음과 같이 **ansible-playbook** 명령을 사용합니다.

```bash
ansible-playbook route_table_delete.yml
```

## <a name="next-steps"></a>다음 단계
> [!div class="nextstepaction"] 
> [Azure의 Ansible](https://docs.microsoft.com/azure/ansible/)