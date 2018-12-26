---
title: Ansible을 사용하여 Azure Application Gateway를 통해 웹 트래픽 관리
description: Ansible을 사용하여 웹 트래픽을 관리하도록 Azure Application Gateway를 만들고 구성하는 방법 알아보기
ms.service: ansible
keywords: Ansible, Azure, DevOps, Bash, 플레이북, 애플리케이션 게이트웨이, 부하 분산 장치, 웹 트래픽
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 09/20/2018
ms.openlocfilehash: af7f22ae5c289a01e6876d8ce586cb32383c8d3b
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/11/2018
ms.locfileid: "53253366"
---
# <a name="manage-web-traffic-with-azure-application-gateway-by-using-ansible"></a>Ansible을 사용하여 Azure Application Gateway를 통해 웹 트래픽 관리

[Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/)는 웹 응용 프로그램에 대한 트래픽을 관리할 수 있도록 하는 웹 트래픽 부하 분산 장치입니다.

Ansible을 사용하면 사용자 환경에서 리소스의 배포 및 구성을 자동화할 수 있습니다. 이 문서는 Ansible을 사용하여 응용 프로그램 게이트웨이를 만드는 방법을 보여줍니다. 또한 게이트웨이를 사용하여 Azure 컨테이너 인스턴스에서 실행되는 두 개의 웹 서버에 대한 트래픽을 관리하는 방법을 설명합니다.

이 자습서에서는 다음을 수행하는 방법에 대해 설명합니다.

> [!div class="checklist"]
> * 네트워크 설정
> * HTTPD 이미지를 사용하여 두 개의 Azure 컨테이너 인스턴스 만들기
> * 서버 풀에서 Azure 컨테이너 인스턴스를 사용하여 응용 프로그램 게이트웨이 만들기

## <a name="prerequisites"></a>필수 조건

- **Azure 구독** - Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)을 만듭니다.
- [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation2.md)]

> [!Note]
> Ansible 2.7은 이 자습서에서 다음의 샘플 플레이북을 실행해야 합니다. 

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다.  

다음 예제에서는 **eastus** 위치에 **myResourceGroup**이라는 리소스 그룹을 만듭니다.

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    location: eastus 
  tasks:
    - name: Create a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        location: "{{ location }}"
```

이 플레이북을 *rg.yml*로 저장합니다. 플레이북을 실행하려면 다음과 같이 **ansible-playbook** 명령을 사용합니다.

```bash
ansible-playbook rg.yml
```

## <a name="create-network-resources"></a>네트워크 리소스 만들기

먼저 응용 프로그램 게이트웨이가 다른 리소스와 통신할 수 있도록 가상 네트워크를 만듭니다.

다음 예제에서는 **myVNet**이라는 가상 네트워크, **myAGSubnet**이라는 서브넷 및 **mydomain**이라는 도메인으로 **myAGPublicIPAddress**라는 공용 IP 주소를 만듭니다.

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    location: eastus 
    vnet_name: myVNet 
    subnet_name: myAGSubnet 
    publicip_name: myAGPublicIPAddress
    publicip_domain: mydomain
  tasks:
    - name: Create a virtual network
      azure_rm_virtualnetwork:
        name: "{{ vnet_name }}"
        resource_group: "{{ resource_group }}"
        address_prefixes_cidr:
            - 10.1.0.0/16
            - 172.100.0.0/16
        dns_servers:
            - 127.0.0.1
            - 127.0.0.2

    - name: Create a subnet
      azure_rm_subnet:
        name: "{{ subnet_name }}"
        virtual_network_name: "{{ vnet_name }}"
        resource_group: "{{ resource_group }}"
        address_prefix_cidr: 10.1.0.0/24

    - name: Create a public IP address
      azure_rm_publicipaddress:
        resource_group: "{{ resource_group }}" 
        allocation_method: Dynamic
        name: "{{ publicip_name }}"
        domain_name_label: "{{ publicip_domain }}"
```

이 플레이북을 *vnet_create.yml*로 저장합니다. 플레이북을 실행하려면 다음과 같이 **ansible-playbook** 명령을 사용합니다.

```bash
ansible-playbook vnet_create.yml
```

## <a name="create-servers"></a>서버 만들기

다음 예제에서는 응용 프로그램 게이트웨이에 대한 웹 서버로 사용될 HTTPD 이미지가 있는 두 개의 Azure 컨테이너 인스턴스를 만드는 방법을 보여줍니다.  

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    location: eastus 
    aci_1_name: myACI1
    aci_2_name: myACI2
  tasks:
    - name: Create a container with httpd image 
      azure_rm_containerinstance:
        resource_group: "{{ resource_group }}"
        name: "{{ aci_1_name }}"
        os_type: linux
        ip_address: public
        location: "{{ location }}"
        ports:
          - 80
        containers:
          - name: mycontainer
            image: httpd
            memory: 1.5
            ports:
              - 80

    - name: Create another container with httpd image 
      azure_rm_containerinstance:
        resource_group: "{{ resource_group }}"
        name: "{{ aci_2_name }}"
        os_type: linux
        ip_address: public
        location: "{{ location }}"
        ports:
          - 80
        containers:
          - name: mycontainer
            image: httpd
            memory: 1.5
            ports:
              - 80
```

이 플레이북을 *aci_create.yml*로 저장합니다. 플레이북을 실행하려면 다음과 같이 **ansible-playbook** 명령을 사용합니다.

```bash
ansible-playbook aci_create.yml
```

## <a name="create-the-application-gateway"></a>Application Gateway 만들기

다음 예제에서는 백 엔드, 프런트 엔드 및 HTTP에 대한 구성으로 **myAppGateway**라는 응용 프로그램 게이트웨이를 만듭니다.  

* **appGatewayIP**는 **gateway_ip_configurations** 블록에서 정의됩니다. 서브넷 참조는 게이트웨이의 IP 구성에 필요합니다.
* **appGatewayBackendPool**은 **backend_address_pools** 블록에서 정의됩니다. 응용 프로그램 게이트웨이에 백 엔드 주소 풀이 하나 이상 있어야 합니다.
* **appGatewayBackendHttpSettings**는 **backend_http_settings_collection** 블록에서 정의됩니다. 포트 80 및 HTTP 프로토콜을 통신에 사용하도록 지정합니다.
* **appGatewayHttpListener**는 **backend_http_settings_collection** 블록에서 정의됩니다. appGatewayBackendPool에 연결되는 기본 수신기입니다.
* **appGatewayFrontendIP**는 **frontend_ip_configurations** 블록에서 정의됩니다. myAGPublicIPAddress를 appGatewayHttpListener에 할당합니다.
* **rule1**은 **request_routing_rules** 블록에서 정의됩니다. appGatewayHttpListener에 연결되는 기본 회람 규칙입니다.

```yml
- hosts: localhost
  connection: local
  vars:
    resource_group: myResourceGroup
    vnet_name: myVNet
    subnet_name: myAGSubnet
    location: eastus
    publicip_name: myAGPublicIPAddress
    appgw_name: myAppGateway
    aci_1_name: myACI1
    aci_2_name: myACI2
  tasks:
    - name: Get info of Subnet
      azure_rm_resource_facts:
        api_version: '2018-08-01'
        resource_group: "{{ resource_group }}"
        provider: network
        resource_type: virtualnetworks
        resource_name: "{{ vnet_name }}"
        subresource:
          - type: subnets
            name: "{{ subnet_name }}"
      register: subnet

    - name: Get info of backend server 2
      azure_rm_resource_facts:
        api_version: '2018-04-01'
        resource_group: "{{ resource_group }}"
        provider: containerinstance
        resource_type: containergroups
        resource_name: "{{ aci_1_name }}"
      register: aci_1_output
    - name: Get info of backend server 2
      azure_rm_resource_facts:
        api_version: '2018-04-01'
        resource_group: "{{ resource_group }}"
        provider: containerinstance
        resource_type: containergroups
        resource_name: "{{ aci_2_name }}"
      register: aci_2_output

    - name: Create instance of Application Gateway
      azure_rm_appgateway:
        resource_group: "{{ resource_group }}"
        name: "{{ appgw_name }}"
        sku:
          name: standard_small
          tier: standard
          capacity: 2
        gateway_ip_configurations:
          - subnet:
              id: "{{ subnet.response[0].id }}"
            name: appGatewayIP
        frontend_ip_configurations:
          - public_ip_address: "{{ publicip_name }}"
            name: appGatewayFrontendIP
        frontend_ports:
          - port: 80
            name: appGatewayFrontendPort
        backend_address_pools:
          - backend_addresses:
              - ip_address: "{{ aci_1_output.response[0].properties.ipAddress.ip }}"
              - ip_address: "{{ aci_2_output.response[0].properties.ipAddress.ip }}"
            name: appGatewayBackendPool
        backend_http_settings_collection:
          - port: 80
            protocol: http
            cookie_based_affinity: enabled
            name: appGatewayBackendHttpSettings
        http_listeners:
          - frontend_ip_configuration: appGatewayFrontendIP
            frontend_port: appGatewayFrontendPort
            name: appGatewayHttpListener
        request_routing_rules:
          - rule_type: Basic
            backend_address_pool: appGatewayBackendPool
            backend_http_settings: appGatewayBackendHttpSettings
            http_listener: appGatewayHttpListener
            name: rule1
```

이 플레이북을 *appgw_create.yml*로 저장합니다. 플레이북을 실행하려면 다음과 같이 **ansible-playbook** 명령을 사용합니다.

```bash
ansible-playbook appgw_create.yml
```

응용 프로그램 게이트웨이가 생성될 때까지 몇 분 정도 걸릴 수 있습니다.

## <a name="test-the-application-gateway"></a>애플리케이션 게이트웨이 테스트

네트워크 리소스에 대한 샘플 플레이북에서 **미국 동부**에 도메인 **mydomain**을 만들었습니다. 브라우저에서 `http://mydomain.eastus.cloudapp.azure.com`으로 이동합니다. 다음 페이지가 표시되면 응용 프로그램 게이트웨이가 예상대로 작동합니다.

![작업 응용 프로그램 게이트웨이의 성공적인 테스트](media/ansible-create-configure-application-gateway/applicationgateway.PNG)

## <a name="clean-up-resources"></a>리소스 정리

이러한 리소스가 필요하지 않은 경우 다음 코드를 실행하여 삭제할 수 있습니다. **myResourceGroup**이라는 리소스 그룹을 삭제합니다.

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
  tasks:
    - name: Delete a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        state: absent
```

이 플레이북을 *rg_delete*.yml로 저장합니다. 플레이북을 실행하려면 다음과 같이 **ansible-playbook** 명령을 사용합니다.

```bash
ansible-playbook rg_delete.yml
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure의 Ansible](https://docs.microsoft.com/azure/ansible/)
