<properties
   pageTitle="Azure CLI를 사용하여 Linux VM을 처음부터 새로 만들기 | Microsoft Azure"
   description="Azure CLI를 사용하여 Linux VM, 저장소, 가상 네트워크 및 서브넷, NIC, 공용 IP, 네트워크 보안 그룹을 모두 처음부터 새로 만듭니다."
   services="virtual-machines-linux"
   documentationCenter="virtual-machines"
   authors="iainfoulds"
   manager="squillace"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="04/29/2016"
   ms.author="iainfou"/>

# Azure CLI를 사용하여 Linux VM을 처음부터 새로 만들기

Linux VM을 만들려면 Resource Manager 모드(`azure config mode arm`)의 [Azure CLI](../xplat-cli-install.md)와 JSON 구문 분석 도구가 필요합니다. 이 문서에서는 [jq](https://stedolan.github.io/jq/)를 사용합니다.

## 빠른 명령

리소스 그룹 만들기

```bash
azure group create TestRG -l westeurope
```

JSON 구문 분석기를 사용하여 RG 확인

```bash
azure group show TestRG --json | jq '.'
```

저장소 계정 만들기

```bash
azure storage account create -g TestRG -l westeurope --kind Storage --sku-name GRS computeteststore
```

JSON 구문 분석기를 사용하여 저장소 확인

```bash
azure storage account show -g TestRG computeteststore --json | jq '.'
```

가상 네트워크 만들기

```bash
azure network vnet create -g TestRG -n TestVNet -a 192.168.0.0/16 -l westeurope
```

서브넷 만들기

```bash
azure network vnet subnet create -g TestRG -e TestVNet -n FrontEnd -a 192.168.1.0/24
```

JSON 구문 분석기를 사용하여 VNet 및 서브넷 확인

```bash
azure network vnet show TestRG TestVNet --json | jq '.'
```

공용 IP 만들기

```bash
azure network public-ip create -g TestRG -n TestLBPIP -l westeurope -d testlb -a static -i 4
```

부하 분산 장치 만들기

```bash
azure network lb create -g TestRG -n TestLB -l westeurope
```

부하 분산 장치에 대한 프런트 엔드 IP 풀을 만들고 Microsoft 공용 IP에 연결

```bash
azure network lb frontend-ip create -g TestRG -l TestLB -n TestFrontEndPool -i TestLBPIP
```

부하 분산 장치에 대한 백 엔드 IP 풀 만들기

```bash
azure network lb address-pool create -g TestRG -l TestLB -n TestBackEndPool
```

부하 분산 장치의 SSH 인바운드 NAT 규칙 만들기

```bash
azure network lb inbound-nat-rule create -g TestRG -l TestLB -n VM1-SSH -p tcp -f 4222 -b 22
azure network lb inbound-nat-rule create -g TestRG -l TestLB -n VM2-SSH -p tcp -f 4223 -b 22
```

부하 분산 장치의 웹 인바운드 NAT 규칙 만들기

```bash
azure network lb rule create -g TestRG -l TestLB -n WebRule -p tcp -f 80 -b 80 \
     -t TestFrontEndPool -o TestBackEndPool
```

부하 분산 장치 상태 프로브 만들기

```bash
azure network lb probe create -g TestRG -l TestLB -n HealthProbe -p "http" -f healthprobe.aspx -i 15 -c 4
```

JSON 구문 분석기를 사용하여 부하 분산 장치, IP 풀, NAT 규칙 확인

```bash
azure network lb show -g TestRG -n TestLB --json | jq '.'
```

첫 번째 NIC 만들기

```bash
azure network nic create -g TestRG -n LB-NIC1 -l westeurope --subnet-vnet-name TestVNet --subnet-name FrontEnd
    -d "/subscriptions/########-####-####-####-############/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/backendAddressPools/TestBackEndPool"
    -e "/subscriptions/########-####-####-####-############/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/inboundNatRules/VM1-SSH"
```

두 번째 NIC 만들기

```bash
azure network nic create -g TestRG -n LB-NIC2 -l westeurope --subnet-vnet-name TestVNet --subnet-name FrontEnd
    -d "/subscriptions/########-####-####-####-############/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/backendAddressPools/TestBackEndPool"
    -e "/subscriptions/########-####-####-####-############/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/inboundNatRules/VM2-SSH"
```

JSON 구문 분석기를 사용하여 NIC 확인

```bash
azure network nic show TestRG LB-NIC1 --json | jq '.'
azure network nic show TestRG LB-NIC2 --json | jq '.'
```

NSG 만들기

```bash
azure network nsg create -g TestRG -n TestNSG -l westeurope
```

NSG에 대한 인바운드 규칙 추가

```bash
azure network nsg rule create --protocol tcp --direction inbound --priority 1000 \
    --destination-port-range 22 --access allow -g TestRG -a TestNSG -n SSHRule
azure network nsg rule create --protocol tcp --direction inbound --priority 1001 \
    --destination-port-range 80 --access allow -g TestRG -a TestNSG -n HTTPRule
```

JSON 구문 분석기를 사용하여 NSG 및 인바운드 규칙 확인

```bash
azure network nsg show -g TestRG -n TestNSG --json | jq '.'
```

NSG를 NIC에 바인딩

```bash
azure network nic set -g TestRG -n LB-NIC1 -o TestNSG
azure network nic set -g TestRG -n LB-NIC2 -o TestNSG
```

가용성 집합 만들기

```bash
azure availset create -g TestRG -n TestAvailSet -l westeurope
```

첫 번째 Linux VM 만들기

```bash
azure vm create \            
    --resource-group TestRG \
    --name TestVM1 \
    --location westeurope \
    --os-type linux \
    --availset-name TestAvailSet \
    --nic-name LB-NIC1 \
    --vnet-name TestVnet \
    --vnet-subnet-name FrontEnd \
    --storage-account-name computeteststore \
    --image-urn canonical:UbuntuServer:14.04.4-LTS:latest \
    --ssh-publickey-file ~/.ssh/id_rsa.pub \
    --admin-username ops
```

두 번째 Linux VM 만들기

```bash
azure vm create \            
    --resource-group TestRG \
    --name TestVM2 \
    --location westeurope \
    --os-type linux \
    --availset-name TestAvailSet \
    --nic-name LB-NIC2 \
    --vnet-name TestVnet \
    --vnet-subnet-name FrontEnd \
    --storage-account-name computeteststore \
    --image-urn canonical:UbuntuServer:14.04.4-LTS:latest \
    --ssh-publickey-file ~/.ssh/id_rsa.pub \
    --admin-username ops
```

JSON 구문 분석기를 사용하여 모든 항목 빌드 여부 확인

```bash
azure vm show -g TestRG -n TestVM1 --json | jq '.'
azure vm show -g TestRG -n TestVM2 --json | jq '.'
```

## 자세한 연습

### 소개

이 문서는 부하 분산 장치의 두 가지 Linux VM을 사용하여 배포하는 방법에 대해 살펴봅니다. 인터넷 어디에서나 연결할 수 있는 안전한 Linux VM에 연결할 수 있을 때까지 기본적인 모든 배포 과정을 명령별로 안내합니다.

그 과정에서 리소스 관리자 배포 모델이 제공하는 종속성 계층 구조와 강력한 기능을 이해할 수 있을 것입니다. 시스템 빌드 방식을 살펴본 후에는 추가적으로 직접적인 Azure CLI 명령을 사용하여 훨씬 빠르게 시스템을 다시 빌드하거나(`azure vm quick-create` 명령을 사용하는 거의 동일한 배포는 [여기](virtual-machines-linux-quick-create-cli.md) 참조) [Azure Resource Manager 템플릿](../resource-group-authoring-templates.md)을 사용하여 전체 네트워크 및 응용 프로그램 배포를 디자인 및 자동화하고 업데이트하는 방법을 배울 수 있습니다. 배포의 여러 파트가 서로 어떻게 연결되는지 살펴보면 파트를 자동화하는 템플릿을 좀 더 쉽게 만들 수 있습니다.

개발 및 간단한 계산에 유용한 VM 2개를 사용하여 간단한 네트워크와 부하 분산 장치를 빌드하면서 차례대로 설명하겠습니다. 그 후에는 좀 더 복잡한 네트워크 및 배포로 넘어갈 수 있을 것입니다.

## 리소스 그룹을 만들고 배포 위치 선택

Azure 리소스 그룹은 리소스 배포를 논리적으로 관리할 수 있는 구성 및 기타 메타데이터를 포함하는 논리적 배포 엔터티입니다.

```bash
azure group create TestRG westeurope
```

출력

```bash                        
info:    Executing command group create
+ Getting resource group TestRG
+ Creating resource group TestRG
info:    Created resource group TestRG
data:    Id:                  /subscriptions/<yoursub>/resourceGroups/TestRG
data:    Name:                TestRG
data:    Location:            westeurope
data:    Provisioning State:  Succeeded
data:    Tags: null
data:
info:    group create command OK
```

## 저장소 계정 만들기

무엇보다도 VM 디스크 및 추가하려는 추가 데이터 디스크에 대한 저장소 계정이 필요합니다. 요약하자면, 항상 리소스 그룹을 만든 후 거의 즉시 저장소 계정을 만들게 됩니다.

여기서는 `azure storage account create` 명령을 사용하여 계정 위치, 계정을 제어할 리소스 그룹, 원하는 저장소 지원 유형을 전달합니다.

```bash
azure storage account create \  
--location westeurope \
--resource-group TestRG \
--kind Storage --sku-name GRS \
computeteststore
```

출력

```bash
info:    Executing command storage account create
+ Creating storage account
info:    storage account create command OK
ahmet•~/workspace/keygen» azure group show testrg
info:    Executing command group show
+ Listing resource groups
+ Listing resources for the group
data:    Id:                  /subscriptions/<guid>/resourceGroups/TestRG
data:    Name:                TestRG
data:    Location:            westeurope
data:    Provisioning State:  Succeeded
data:    Tags: null
data:    Resources:
data:
data:      Id      : /subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Storage/storageAccounts/computeteststore
data:      Name    : computeteststore
data:      Type    : storageAccounts
data:      Location: westeurope
data:      Tags    :
data:
data:    Permissions:
data:      Actions: *
data:      NotActions:
data:
info:    group show command OK
```

`--json` Azure CLI 옵션과 함께 [jq](https://stedolan.github.io/jq/) 도구를 사용하여(**jsawk** 또는 JSON 구문 분석에 선호하는 모든 언어 라이브러리 사용 가능) `azure group show` 명령으로 리소스 그룹을 검사해 보겠습니다.

```bash
azure group show TestRG --json | jq                                                                                      
```


출력

```bash
{
  "tags": {},
  "id": "/subscriptions/<guid>/resourceGroups/TestRG",
  "name": "TestRG",
  "provisioningState": "Succeeded",
  "location": "westeurope",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "resources": [
    {
      "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Storage/storageAccounts/computeteststore",
      "name": "computeteststore",
      "type": "storageAccounts",
      "location": "westeurope",
      "tags": null
    }
  ],
  "permissions": [
    {
      "actions": [
        "*"
      ],
      "notActions": []
    }
  ]
}
```

CLI를 사용하여 저장소 계정을 확인하려면 먼저 다음 명령을 사용하여 계정 이름 및 키를 설정하고, 이 문서의 저장소 계정 이름을 사용자 고유의 이름으로 바꿔야 합니다.

```
AZURE_STORAGE_CONNECTION_STRING="$(azure storage account connectionstring show computeteststore --resource-group testrg --json | jq -r '.string')"
```

그러면 저장소 정보를 쉽게 볼 수 있습니다.

```bash
azure storage container list
```

출력

```bash
info:    Executing command storage container list
+ Getting storage containers
data:    Name  Public-Access  Last-Modified
data:    ----  -------------  -----------------------------
data:    vhds  Off            Sun, 27 Sep 2015 19:03:54 GMT
info:    storage container list command OK
```

## 가상 네트워크 및 서브넷 만들기

VM을 설치할 수 있는 Azure 가상 네트워크 및 서브넷을 만들어야 합니다.

```bash
azure network vnet create -g TestRG -n TestVNet -a 192.168.0.0/16 -l westeurope
```

출력

```bash
info:    Executing command network vnet create
+ Looking up virtual network "TestVNet"
+ Creating virtual network "TestVNet"
+ Loading virtual network state
data:    Id                              : /subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet
data:    Name                            : TestVNet
data:    Type                            : Microsoft.Network/virtualNetworks
data:    Location                        : westeurope
data:    ProvisioningState               : Succeeded
data:    Address prefixes:
data:      192.168.0.0/16
info:    network vnet create command OK
```

json 옵션 `azure group show` 및 **jq**를 사용하여 리소스를 빌드하는 방법을 다시 한 번 살펴보겠습니다. 이제 `storageAccounts` 리소스 및 `virtualNetworks` 리소스가 생겼습니다.

```bash
azure group show TestRG --json | jq '.'
```

출력

```bash
{
  "tags": {},
  "id": "/subscriptions/<guid>/resourceGroups/TestRG",
  "name": "TestRG",
  "provisioningState": "Succeeded",
  "location": "westeurope",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "resources": [
    {
      "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet",
      "name": "TestVNet",
      "type": "virtualNetworks",
      "location": "westeurope",
      "tags": null
    },
    {
      "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Storage/storageAccounts/computeteststore",
      "name": "computeteststore",
      "type": "storageAccounts",
      "location": "westeurope",
      "tags": null
    }
  ],
  "permissions": [
    {
      "actions": [
        "*"
      ],
      "notActions": []
    }
  ]
}
```

이번에는 VM을 배포할 `TestVnet` 가상 네트워크에 서브넷을 만들겠습니다. 이미 만들어 놓은 `TestRG` 리소스 그룹 및 `TestVNet` 가상 네트워크와 함께 `azure network vnet subnet create` 명령을 사용하고 다음과 같이 서브넷 이름 `FrontEnd` 및 서브넷 주소 접두사 `192.168.1.0/24`를 추가하겠습니다.

```bash
azure network vnet subnet create -g TestRG -e TestVNet -n FrontEnd -a 192.168.1.0/24
```

출력

```bash
info:    Executing command network vnet subnet create
+ Looking up the subnet "FrontEnd"
+ Creating subnet "FrontEnd"
+ Looking up the subnet "FrontEnd"
data:    Id                              : /subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd
data:    Type                            : Microsoft.Network/virtualNetworks/subnets
data:    ProvisioningState               : Succeeded
data:    Name                            : FrontEnd
data:    Address prefix                  : 192.168.1.0/24
data:
info:    network vnet subnet create command OK
```

서브넷은 논리적으로 가상 네트워크 안에 있으므로 약간 다른 `azure network vnet show` 명령을 사용하여 서브넷 정보를 살펴보겠습니다. 하지만 JSON 출력은 여전히 **jq**를 사용하여 검사합니다.

```bash
azure network vnet show TestRG TestVNet --json | jq '.'
```

출력

```bash
{
  "subnets": [
    {
      "ipConfigurations": [],
      "addressPrefix": "192.168.1.0/24",
      "provisioningState": "Succeeded",
      "name": "FrontEnd",
      "etag": "W/"974f3e2c-028e-4b35-832b-a4b16ad25eb6"",
      "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd"
    }
  ],
  "tags": {},
  "addressSpace": {
    "addressPrefixes": [
      "192.168.0.0/16"
    ]
  },
  "dhcpOptions": {
    "dnsServers": []
  },
  "provisioningState": "Succeeded",
  "etag": "W/"974f3e2c-028e-4b35-832b-a4b16ad25eb6"",
  "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet",
  "name": "TestVNet",
  "location": "westeurope"
}
```

## PIP(공용 IP 주소) 만들기

이제 인터넷에서 `azure network public-ip create` 명령을 사용하여 부하 분산 장치에 할당하고 VM에 연결할 수 있는 PIP(공용 IP 주소)를 만들어 보겠습니다. 기본값은 동적 주소이기 때문에 `-d testsubdomain` 옵션을 사용하여 **cloudapp.azure.com** 도메인에 명명된 DNS 항목을 만듭니다.

```bash
azure network public-ip create -d testsubdomain TestRG TestPIP westeurope
```

출력

```bash
info:    Executing command network public-ip create
+ Looking up the public ip "TestPIP"
+ Creating public ip address "TestPIP"
+ Looking up the public ip "TestPIP"
data:    Id                              : /subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/publicIPAddresses/TestPIP
data:    Name                            : TestPIP
data:    Type                            : Microsoft.Network/publicIPAddresses
data:    Location                        : westeurope
data:    Provisioning state              : Succeeded
data:    Allocation method               : Dynamic
data:    Idle timeout                    : 4
data:    Domain name label               : testsubdomain
data:    FQDN                            : testsubdomain.westeurope.cloudapp.azure.com
info:    network public-ip create command OK
```

이 주소도 최상위 리소스이므로 `azure group show` 명령을 사용하여 볼 수 있습니다.

```bash
azure group show TestRG --json | jq '.'
```

출력

```bash
{
"tags": {},
"id": "/subscriptions/guid/resourceGroups/TestRG",
"name": "TestRG",
"provisioningState": "Succeeded",
"location": "westeurope",
"properties": {
    "provisioningState": "Succeeded"
},
"resources": [
    {
    "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC",
    "name": "TestNIC",
    "type": "networkInterfaces",
    "location": "westeurope",
    "tags": null
    },
    {
    "id": "/subscriptions/guid/resourceGroups/testrg/providers/Microsoft.Network/publicIPAddresses/testpip",
    "name": "testpip",
    "type": "publicIPAddresses",
    "location": "westeurope",
    "tags": null
    },
    {
    "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet",
    "name": "TestVNet",
    "type": "virtualNetworks",
    "location": "westeurope",
    "tags": null
    },
    {
    "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Storage/storageAccounts/computeteststore",
    "name": "computeteststore",
    "type": "storageAccounts",
    "location": "westeurope",
    "tags": null
    }
],
"permissions": [
    {
    "actions": [
        "*"
    ],
    "notActions": []
    }
]
}
```

이 경우에도 더 완전한 `azure network public-ip show` 명령을 사용하여 하위 도메인 FQDN(정규화된 도메인 이름)을 포함하여 더 많은 리소스 세부 정보를 조사할 수 있습니다. 공용 IP 주소 리소스가 논리적으로 할당되었지만 아직 특정 주소가 할당되지는 않았습니다. 여기에 부하 분산 장치가 필요하지만 아직 만들지 않았습니다.

```bash
azure network public-ip show TestRG TestPIP --json | jq '.'
```

출력

```bash
{
"tags": {},
"publicIpAllocationMethod": "Dynamic",
"dnsSettings": {
    "domainNameLabel": "testsubdomain",
    "fqdn": "testsubdomain.westeurope.cloudapp.azure.com"
},
"idleTimeoutInMinutes": 4,
"provisioningState": "Succeeded",
"etag": "W/"c63154b3-1130-49b9-a887-877d74d5ebc5"",
"id": "/subscriptions/guid/resourceGroups/testrg/providers/Microsoft.Network/publicIPAddresses/testpip",
"name": "testpip",
"location": "westeurope"
}
```

## 부하 분산 장치 및 IP 풀 만들기
부하 분산 장치를 만들면 웹 응용 프로그램을 실행할 때 여러 VM에 트래픽을 분산할 수 있습니다. 또한 유지 보수 중 또는 부하가 많을 경우 사용자 요청에 대응하는 여러 VM을 실행하여 응용 프로그램에 중복성을 제공합니다.

다음을 사용하여 부하 분산 장치를 만듭니다.

```bash
azure network lb create -g TestRG -n TestLB -l westeurope
```

```bash
azure network lb create -g TestRG -n TestLB -l westeurope
```

출력

```bash
info:    Executing command network lb create
+ Looking up the load balancer "TestLB"
+ Creating load balancer "TestLB"
data:    Id                              : /subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB
data:    Name                            : TestLB
data:    Type                            : Microsoft.Network/loadBalancers
data:    Location                        : westeurope
data:    Provisioning state              : Succeeded
info:    network lb create command OK
```
이 부하 분산 장치는 비어 있는 상태이므로 몇 개의 IP 풀을 만들겠습니다. 부하 분산 장치에 프런트 엔드용과 백 엔드용으로 두 개의 IP 풀을 만들려고 합니다. 프런트 엔드 IP 풀은 공개적으로 볼 수 있고 앞에서 만든 PIP를 할당하는 위치입니다. 그런 다음 백 엔드 풀은 트래픽이 부하 분산 장치를 통과하여 VM으로 연결되도록 하는 데 사용합니다.

가장 먼저 프런트 엔드 IP 풀을 만들겠습니다.

```bash
azure network lb frontend-ip create -g TestRG -l TestLB -n TestFrontEndPool -i TestLBPIP
```

출력

```bash
info:    Executing command network lb frontend-ip create
+ Looking up the load balancer "TestLB"
+ Looking up the public ip "TestLBPIP"
+ Updating load balancer "TestLB"
data:    Name                            : TestFrontEndPool
data:    Provisioning state              : Succeeded
data:    Private IP allocation method    : Dynamic
data:    Public IP address id            : /subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/publicIPAddresses/TestLBPIP
info:    network lb frontend-ip create command OK
```

앞에서 만든 TestLBPIP를 전달하기 위해 `--public-ip-name` 스위치를 어떻게 사용했는지 확인하십시오. 이 스위치는 인터넷에서 VM에 연결하기 위해 부하 분산 장치에 공용 IP 주소를 할당합니다.

다음으로 백 엔드 트래픽에 사용할 두 번째 IP 풀을 만들겠습니다.

```bash
azure network lb address-pool create -g TestRG -l TestLB -n TestBackEndPool
```

출력

```bash
info:    Executing command network lb address-pool create
+ Looking up the load balancer "TestLB"
+ Updating load balancer "TestLB"
data:    Name                            : TestBackEndPool
data:    Provisioning state              : Succeeded
info:    network lb address-pool create command OK
```

`azure network lb show`를 사용하여 부하 분산 장치가 어떻게 표시되며 JSON 출력을 검사하는지 확인할 수 있습니다.

```bash
azure network lb show TestRG TestLB --json | jq '.'
```

출력

```bash
{
  "etag": "W/"29c38649-77d6-43ff-ab8f-977536b0047c"",
  "provisioningState": "Succeeded",
  "resourceGuid": "f1446acb-09ba-44d9-b8b6-849d9983dc09",
  "outboundNatRules": [],
  "inboundNatPools": [],
  "inboundNatRules": [],
  "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB",
  "name": "TestLB",
  "type": "Microsoft.Network/loadBalancers",
  "location": "westeurope",
  "frontendIPConfigurations": [
    {
      "etag": "W/"29c38649-77d6-43ff-ab8f-977536b0047c"",
      "name": "TestFrontEndPool",
      "provisioningState": "Succeeded",
      "publicIPAddress": {
        "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/publicIPAddresses/TestLBPIP"
      },
      "privateIPAllocationMethod": "Dynamic",
      "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/frontendIPConfigurations/TestFrontEndPool"
    }
  ],
  "backendAddressPools": [
    {
      "etag": "W/"29c38649-77d6-43ff-ab8f-977536b0047c"",
      "name": "TestBackEndPool",
      "provisioningState": "Succeeded",
      "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/backendAddressPools/TestBackEndPool"
    }
  ],
  "loadBalancingRules": [],
  "probes": []
}
```

## 부하 분산 장치 NAT 규칙 만들기
실제로 부하 분산 장치에 트래픽을 통과시키려면 인바운드 또는 아웃바운드 작업을 지정하는 NAT 규칙을 만들어야 합니다. 사용 중인 프로토콜을 지정한 다음 외부 포트를 내부 포트로 원하는 대로 매핑할 수 있습니다. 이 환경에서는 SSH가 이 부하 분산 장치를 통해 VM에 연결하도록 하는 몇 가지 규칙을 만들겠습니다. TCP 포트 4222와 4223이 나중에 만들 VM의 TCP 포트 22로 연결되도록 설정하겠습니다.

```bash
azure network lb inbound-nat-rule create -g TestRG -l TestLB -n VM1-SSH -p tcp -f 4222 -b 22
```

출력

```bash
info:    Executing command network lb inbound-nat-rule create
+ Looking up the load balancer "TestLB"
warn:    Using default enable floating ip: false
warn:    Using default idle timeout: 4
warn:    Using default frontend IP configuration "TestFrontEndPool"
+ Updating load balancer "TestLB"
data:    Name                            : VM1-SSH
data:    Provisioning state              : Succeeded
data:    Protocol                        : Tcp
data:    Frontend port                   : 4222
data:    Backend port                    : 22
data:    Enable floating IP              : false
data:    Idle timeout in minutes         : 4
data:    Frontend IP configuration id    : /subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/frontendIPConfigurations/TestFrontEndPool
info:    network lb inbound-nat-rule create command OK
```

SSH의 두 번째 NAT 규칙에 대해 같은 절차를 반복합니다.

```bash
azure network lb inbound-nat-rule create -g TestRG -l TestLB -n VM2-SSH -p tcp -f 4223 -b 22
```

또한 TCP 포트 80에 대해 NAT 규칙을 만들고 IP 풀에 규칙을 연결하겠습니다. VM에 규칙을 개별적으로 연결하지 않을 경우 IP 풀에서 VM을 간단히 추가 또는 제거하고 부하 분산 장치가 트래픽 흐름을 자동으로 조정할 수 있습니다.

```bash
azure network lb rule create -g TestRG -l TestLB -n WebRule -p tcp -f 80 -b 80 \
     -t TestFrontEndPool -o TestBackEndPool
```

출력

```bash
info:    Executing command network lb rule create
+ Looking up the load balancer "TestLB"
warn:    Using default idle timeout: 4
warn:    Using default enable floating ip: false
warn:    Using default load distribution: Default
+ Updating load balancer "TestLB"
data:    Name                            : WebRule
data:    Provisioning state              : Succeeded
data:    Protocol                        : Tcp
data:    Frontend port                   : 80
data:    Backend port                    : 80
data:    Enable floating IP              : false
data:    Load distribution               : Default
data:    Idle timeout in minutes         : 4
data:    Frontend IP configuration id    : /subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/frontendIPConfigurations/TestFrontEndPool
data:    Backend address pool id         : /subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/backendAddressPools/TestBackEndPool
info:    network lb rule create command OK
```

## 부하 분산 장치 상태 프로브 만들기

상태 프로브는 부하 분산 장치에 설정된 VM을 주기적으로 검사하여 VM이 정의된 대로 작동하며 요청에 응답하는지 확인합니다. 그렇지 않을 경우 사용자가 오류가 발생한 VM으로 연결되지 않도록 작동을 중단합니다. 상태 프로브에 대한 사용자 지정 검사, 간격, 시간 제한 값을 정의할 수 있습니다. 상태 프로브에 대한 자세한 내용은 [부하 분산 장치 프로브](../load-balancer/load-balancer-custom-probe-overview.md)를 참조하십시오.

```bash
azure network lb probe create -g TestRG -l TestLB -n HealthProbe -p "http" -f healthprobe.aspx -i 15 -c 4
```

출력

```bash
info:    Executing command network lb probe create
warn:    Using default probe port: 80
+ Looking up the load balancer "TestLB"
+ Updating load balancer "TestLB"
data:    Name                            : HealthProbe
data:    Provisioning state              : Succeeded
data:    Protocol                        : Http
data:    Port                            : 80
data:    Interval in seconds             : 15
data:    Number of probes                : 4
info:    network lb probe create command OK
```

여기서는 15초의 상태 검사 간격을 지정했으며 부하 분산 장치가 호스트가 더 이상 작동하지 않는 것으로 판단할 때까지 최대 4개의 프로브(1분)를 놓칠 수 있습니다.

## 부하 분산 장치 확인
이제 부하 분산 장치의 모든 구성을 마쳤습니다. 부하 분산 장치를 만들고 프런트 엔드 IP 풀을 만든 다음 여기에 공용 IP 풀을 할당했으며 VM이 연결할 백 엔드 IP 풀을 만들었습니다. 그런 다음 관리를 위해 SSH가 VM에 연결할 수 있는 NAT 규칙을 만들고 웹앱이 TCP 포트 80을 사용할 수 있도록 하는 규칙을 만들었습니다. 마지막으로 사용자들이 더 이상 작동하지 않고 콘텐츠를 제공하지 않는 VM에 액세스 시도를 하지 않도록 VM을 주기적으로 검사하는 상태 프로브를 추가했습니다.

이제 부하 분산 장치가 어떻게 표시되는지 살펴보겠습니다.

```bash
azure network lb show -g TestRG -n TestLB --json | jq '.'
```

출력

```bash
{
  "etag": "W/"62a7c8e7-859c-48d3-8e76-5e078c5e4a02"",
  "provisioningState": "Succeeded",
  "resourceGuid": "f1446acb-09ba-44d9-b8b6-849d9983dc09",
  "outboundNatRules": [],
  "inboundNatPools": [],
  "inboundNatRules": [
    {
      "etag": "W/"62a7c8e7-859c-48d3-8e76-5e078c5e4a02"",
      "name": "VM1-SSH",
      "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/inboundNatRules/VM1-SSH",
      "frontendIPConfiguration": {
        "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/frontendIPConfigurations/TestFrontEndPool"
      },
      "protocol": "Tcp",
      "frontendPort": 4222,
      "backendPort": 22,
      "idleTimeoutInMinutes": 4,
      "enableFloatingIP": false,
      "provisioningState": "Succeeded"
    },
    {
      "etag": "W/"62a7c8e7-859c-48d3-8e76-5e078c5e4a02"",
      "name": "VM2-SSH",
      "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/inboundNatRules/VM2-SSH",
      "frontendIPConfiguration": {
        "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/frontendIPConfigurations/TestFrontEndPool"
      },
      "protocol": "Tcp",
      "frontendPort": 4223,
      "backendPort": 22,
      "idleTimeoutInMinutes": 4,
      "enableFloatingIP": false,
      "provisioningState": "Succeeded"
    }
  ],
  "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB",
  "name": "TestLB",
  "type": "Microsoft.Network/loadBalancers",
  "location": "westeurope",
  "frontendIPConfigurations": [
    {
      "etag": "W/"62a7c8e7-859c-48d3-8e76-5e078c5e4a02"",
      "name": "TestFrontEndPool",
      "provisioningState": "Succeeded",
      "publicIPAddress": {
        "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/publicIPAddresses/TestLBPIP"
      },
      "privateIPAllocationMethod": "Dynamic",
      "loadBalancingRules": [
        {
          "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/loadBalancingRules/WebRule"
        }
      ],
      "inboundNatRules": [
        {
          "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/inboundNatRules/VM1-SSH"
        },
        {
          "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/inboundNatRules/VM2-SSH"
        }
      ],
      "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/frontendIPConfigurations/TestFrontEndPool"
    }
  ],
  "backendAddressPools": [
    {
      "etag": "W/"62a7c8e7-859c-48d3-8e76-5e078c5e4a02"",
      "name": "TestBackEndPool",
      "provisioningState": "Succeeded",
      "loadBalancingRules": [
        {
          "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/loadBalancingRules/WebRule"
        }
      ],
      "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/backendAddressPools/TestBackEndPool"
    }
  ],
  "loadBalancingRules": [
    {
      "etag": "W/"62a7c8e7-859c-48d3-8e76-5e078c5e4a02"",
      "name": "WebRule",
      "provisioningState": "Succeeded",
      "enableFloatingIP": false,
      "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/loadBalancingRules/WebRule",
      "frontendIPConfiguration": {
        "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/frontendIPConfigurations/TestFrontEndPool"
      },
      "backendAddressPool": {
        "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/backendAddressPools/TestBackEndPool"
      },
      "protocol": "Tcp",
      "loadDistribution": "Default",
      "frontendPort": 80,
      "backendPort": 80,
      "idleTimeoutInMinutes": 4
    }
  ],
  "probes": [
    {
      "etag": "W/"62a7c8e7-859c-48d3-8e76-5e078c5e4a02"",
      "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/probes/HealthProbe",
      "protocol": "Http",
      "port": 80,
      "intervalInSeconds": 15,
      "numberOfProbes": 4,
      "requestPath": "healthprobe.aspx",
      "provisioningState": "Succeeded",
      "name": "HealthProbe"
    }
  ]
}
```

## Linux VM과 함께 사용할 NIC 만들기

NIC는 프로그래밍 방식으로 사용할 수도 있습니다. NIC에 사용법 규칙을 적용하고 NIC를 여러 개 만들 수 있습니다. 다음 `azure network nic create` 명령에서 NIC를 부하 백 엔드 IP 풀에 연결하고 SSH 트래픽을 허용하기 위한 NAT 규칙을 연결했습니다. 이 작업을 하려면 `<GUID>` 대신 Azure 구독의 구독 ID를 지정해야 합니다.

```bash
azure network nic create -g TestRG -n LB-NIC1 -l westeurope --subnet-vnet-name TestVNet --subnet-name FrontEnd -d
```

출력

```bash 
/subscriptions/<GUID>/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/backendAddressPools/TestBackEndPool -e /subscriptions/<GUID>/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/inboundNatRules/VM1-SSH
info:    Executing command network nic create
+ Looking up the subnet "FrontEnd"
+ Looking up the network interface "LB-NIC1"
+ Creating network interface "LB-NIC1"
data:    Id                              : /subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/LB-NIC1
data:    Name                            : LB-NIC1
data:    Type                            : Microsoft.Network/networkInterfaces
data:    Location                        : westeurope
data:    Provisioning state              : Succeeded
data:    Enable IP forwarding            : false
data:    IP configurations:
data:      Name                          : Nic-IP-config
data:      Provisioning state            : Succeeded
data:      Private IP address            : 192.168.1.4
data:      Private IP allocation method  : Dynamic
data:      Subnet                        : /subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd
data:      Load balancer backend address pools:
data:        Id                          : /subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/backendAddressPools/TestBackEndPool
data:      Load balancer inbound NAT rules:
data:        Id                          : /subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/inboundNatRules/VM1-SSH
data:
info:    network nic create command OK
```

`azure network nic show` 명령으로 리소스를 직접 검사하여 세부 정보를 볼 수 있습니다.

```bash
azure network nic show TestRG LB-NIC1 --json | jq '.'
```

출력

```bash
{
  "etag": "W/"fc1eaaa1-ee55-45bd-b847-5a08c7f4264a"",
  "provisioningState": "Succeeded",
  "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/LB-NIC1",
  "name": "LB-NIC1",
  "type": "Microsoft.Network/networkInterfaces",
  "location": "westeurope",
  "ipConfigurations": [
    {
      "etag": "W/"fc1eaaa1-ee55-45bd-b847-5a08c7f4264a"",
      "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/LB-NIC1/ipConfigurations/Nic-IP-config",
      "loadBalancerBackendAddressPools": [
        {
          "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/backendAddressPools/TestBackEndPool"
        }
      ],
      "loadBalancerInboundNatRules": [
        {
          "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/inboundNatRules/VM1-SSH"
        }
      ],
      "privateIPAddress": "192.168.1.4",
      "privateIPAllocationMethod": "Dynamic",
      "subnet": {
        "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd"
      },
      "provisioningState": "Succeeded",
      "name": "Nic-IP-config"
    }
  ],
  "dnsSettings": {
    "appliedDnsServers": [],
    "dnsServers": []
  },
  "enableIPForwarding": false,
  "resourceGuid": "a20258b8-6361-45f6-b1b4-27ffed28798c"
}
```

계속해서 두 번째 NIC를 만들고 백 엔드 IP 풀에 다시 연결하겠습니다. 여기서는 두 번째 NAT 규칙을 설정하여 SSH 트래픽을 허용합니다.

```bash
azure network nic create -g TestRG -n LB-NIC2 -l westeurope --subnet-vnet-name TestVNet --subnet-name FrontEnd -d
```

출력

```bash
 /subscriptions/<GUID>/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/backendAddressPools/TestBackEndPool -e /subscriptions/<GUID>/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/inboundNatRules/VM2-SSH
```

## 네트워크 보안 그룹 및 규칙 만들기

이번에는 네트워크 보안 그룹(NSG) 및 NIC 액세스를 제어하는 인바운드 규칙을 만들겠습니다.

```bash
azure network nsg create TestRG TestNSG westeurope
```

포트 22에 인바운드 연결이 가능하도록(SSH를 지원하기 위해) NSG에 대한 인바운드 규칙을 추가하겠습니다.

```bash
azure network nsg rule create --protocol tcp --direction inbound --priority 1000 \
    --destination-port-range 22 --access allow TestRG TestNSG SSHRule
```

```bash
azure network nsg rule create --protocol tcp --direction inbound --priority 1001 \
    --destination-port-range 80 --access allow -g TestRG -a TestNSG -n HTTPRule
```

> [AZURE.NOTE] 인바운드 규칙은 인바운드 네트워크 연결에 대한 필터입니다. 이 예제에서는 NSG를 VM 가상 네트워크 인터페이스 카드(NIC)에 바인딩합니다. 이 경우 포트 22에 대한 모든 요청이 VM의 NIC를 통과합니다. 이 규칙은 클래식 배포처럼 끝점이 아니라 포트를 열기 위한 네트워크 연결에 대한 규칙이므로 **모든** 요청 포트에서 오는 인바운드 요청을 수락할 수 있도록 `--source-port-range`를 기본값인 '*'로 두어야 합니다. 일반적으로 이 값은 동적으로 변합니다.

## NIC에 바인딩

NSG를 NIC에 바인딩:

```bash
azure network nic set -g TestRG -n LB-NIC1 -o TestNSG
```

```bash
azure network nic set -g TestRG -n LB-NIC2 -o TestNSG
```

## 가용성 집합 만들기
가용성 집합은 장애 도메인 및 업그레이드 도메인에 VM에 분산하는 데 유용합니다. VM의 가용성 집합을 만들겠습니다.

```bash
azure availset create -g TestRG -n TestAvailSet -l westeurope
```

장애 도메인은 공통의 전원 및 네트워크 스위치를 공유하는 가상 컴퓨터 그룹을 정의합니다. 기본적으로 가용성 집합 안에 구성된 가상 컴퓨터는 최대 3개의 장애 도메인에 분산되어 있습니다. 이러한 장애 도메인 중 하나에서 발생한 하드웨어 문제가 앱을 실행 중인 모든 VM에 영향을 미치지 않는 것이 가장 좋습니다. Azure는 가용성 집합에 VM을 배치할 때 VM을 전체 장애 도메인에 자동으로 분산합니다.

업그레이드 도메인은 동시에 재부팅할 수 있는 가상 컴퓨터 그룹과 기본 물리적 하드웨어를 나타냅니다. 재부팅하는 업그레이드 도메인의 순서는 계획된 유지 보수 중 순차적으로 진행할 수 없으며 한 번에 하나의 업그레이드만 재부팅됩니다. Azure는 VM을 가용성 사이트 안에 배치할 때 VM을 전체 업그레이드 도메인에 자동으로 분산합니다.

[VM의 가용성 관리](./virtual-machines-linux-manage-availability.md)에 대한 자세한 내용을 참조하세요.

## Linux VM 만들기

인터넷에서 액세스 가능한 VM을 지원하기 위해 저장소 및 네트워크 리소스를 만들었습니다. 이제 해당 VM을 만들고 암호 없이 SSH 키를 사용하여 VM을 보호하겠습니다. 이 예에서는 가장 최근의 LTS를 기반으로 Ubuntu VM을 만들겠습니다. [Azure VM 이미지 찾기](virtual-machines-linux-cli-ps-findimage.md)에 설명된 대로 `azure vm image list` 명령을 사용하여 해당 이미지 정보를 찾을 것입니다. 앞에서 `azure vm image list westeurope canonical | grep LTS` 명령을 사용하여 이미지를 선택했는데 이 예에서는 `canonical:UbuntuServer:14.04.4-LTS:14.04.201604060` 명령을 사용합니다. 하지만 마지막 필드에 대해서는 향후에 항상 가장 최근 빌드를 가져오도록 `latest`를 전달하겠습니다(사용할 문자열은 `canonical:UbuntuServer:14.04.4-LTS:14.04.201604060`).

> [AZURE.NOTE] 다음 단계는 **ssh-keygen -t rsa -b 2048**을 사용하여 Linux 또는 Mac에서 ssh rsa 공개 키 및 개인 키 쌍을 만든 경험이 있는 사용자에게 익숙할 것입니다. `~/.ssh` 디렉터리에 인증서 키 쌍이 하나도 없으면 다음 방법으로 만들 수 있습니다. <br /> 1. `azure vm create --generate-ssh-keys` 옵션을 사용하여 자동으로 만들기 2. [직접 인증서 키 쌍을 만드는 지침](virtual-machines-linux-ssh-from-linux.md)에 따라 수동으로 만들기 <br /> 또는 VM이 만들어지면 `azure vm create --admin-username --admin-password` 옵션을 사용하여 일반적으로 보안 수준이 낮은 사용자 이름 및 암호로 ssh 연결을 인증하는 방법도 있습니다.

`azure vm create` 명령으로 모든 리소스 및 정보를 결합하여 VM을 만듭니다.

```bash
azure vm create \            
    --resource-group TestRG \
    --name TestVM1 \
    --location westeurope \
    --os-type linux \
    --availset-name TestAvailSet \
    --nic-name LB-NIC1 \
    --vnet-name TestVnet \
    --vnet-subnet-name FrontEnd \
    --storage-account-name computeteststore \
    --image-urn canonical:UbuntuServer:14.04.4-LTS:latest \
    --ssh-publickey-file ~/.ssh/id_rsa.pub \
    --admin-username ops
```

출력

```bash
info:    Executing command vm create
+ Looking up the VM "TestVM1"
info:    Verifying the public key SSH file: /home/ifoulds/.ssh/id_rsa.pub
info:    Using the VM Size "Standard_A1"
info:    The [OS, Data] Disk or image configuration requires storage account
+ Looking up the storage account computeteststore
+ Looking up the availability set "TestAvailSet"
info:    Found an Availability set "TestAvailSet"
+ Looking up the NIC "LB-NIC1"
info:    Found an existing NIC "LB-NIC1"
info:    Found an IP configuration with virtual network subnet id "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd" in the NIC "LB-NIC1"
info:    This is an NIC without publicIP configured
info:    The storage URI 'https://computeteststore.blob.core.windows.net/' will be used for boot diagnostics settings, and it can be overwritten by the parameter input of '--boot-diagnostics-storage-uri'.
info:    vm create command OK
```

기본 SSH 키를 사용하여 VM에 즉시 연결할 수 있습니다. 이 경우 부하 분산 장치를 통과하므로 적절한 포트를 지정하십시오(첫 번째 VM의 경우 포트 4222를 VM에 전달하는 NAT 규칙을 설정했습니다).

```bash
 ssh ops@testlb.westeurope.cloudapp.azure.com -p 4222
```

출력

```bash
The authenticity of host '[testlb.westeurope.cloudapp.azure.com]:4222 ([xx.xx.xx.xx]:4222)' can't be established.
ECDSA key fingerprint is 94:2d:d0:ce:6b:fb:7f:ad:5b:3c:78:93:75:82:12:f9.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added '[testlb.westeurope.cloudapp.azure.com]:4222,[xx.xx.xx.xx]:4222' (ECDSA) to the list of known hosts.
Welcome to Ubuntu 14.04.4 LTS (GNU/Linux 3.19.0-58-generic x86_64)

 * Documentation:  https://help.ubuntu.com/

  System information as of Wed Apr 27 23:44:06 UTC 2016

  System load: 0.37              Memory usage: 5%   Processes:       81
  Usage of /:  37.3% of 1.94GB   Swap usage:   0%   Users logged in: 0

  Graph this data and manage this system at:
    https://landscape.canonical.com/

  Get cloud support with Ubuntu Advantage Cloud Guest:
    http://www.ubuntu.com/business/services/cloud

0 packages can be updated.
0 updates are security updates.



The programs included with the Ubuntu system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
applicable law.

ops@TestVM1:~$
```

이제 같은 방식으로 두 번째 VM을 만드십시오.

```bash
azure vm create \            
    --resource-group TestRG \
    --name TestVM2 \
    --location westeurope \
    --os-type linux \
    --availset-name TestAvailSet \
    --nic-name LB-NIC2 \
    --vnet-name TestVnet \
    --vnet-subnet-name FrontEnd \
    --storage-account-name computeteststore \
    --image-urn canonical:UbuntuServer:14.04.4-LTS:latest \
    --ssh-publickey-file ~/.ssh/id_rsa.pub \
    --admin-username ops
```

이제 `azure vm show testrg testvm` 명령을 사용하여 앞에서 만든 항목을 검사할 수 있습니다. 이 시점에는 Azure의 부하 분산 장치에서 Ubuntu VM을 실행 중이며 보유하고 있는 SSH 키 쌍을 사용하여 로그인할 수 있습니다. 이때 암호는 비활성화되어 있습니다. hginx 또는 httpd를 설치하고 웹앱을 배포한 다음 트래픽이 부하 분산 장치를 통해 두 VM에 연결되는 것을 확인할 수 있습니다.

```bash
azure vm show TestRG TestVM1
```

출력

```bash
info:    Executing command vm show
+ Looking up the VM "TestVM1"
+ Looking up the NIC "LB-NIC1"
data:    Id                              :/subscriptions/8fa5cd83-7fbb-431a-af16-4a20dede8802/resourceGroups/testrg/providers/Microsoft.Compute/virtualMachines/TestVM1
data:    ProvisioningState               :Succeeded
data:    Name                            :TestVM1
data:    Location                        :westeurope
data:    Type                            :Microsoft.Compute/virtualMachines
data:
data:    Hardware Profile:
data:      Size                          :Standard_A1
data:
data:    Storage Profile:
data:      Image reference:
data:        Publisher                   :canonical
data:        Offer                       :UbuntuServer
data:        Sku                         :14.04.4-LTS
data:        Version                     :latest
data:
data:      OS Disk:
data:        OSType                      :Linux
data:        Name                        :cli1cca1d20a1dcf56c-os-1461800591317
data:        Caching                     :ReadWrite
data:        CreateOption                :FromImage
data:        Vhd:
data:          Uri                       :https://computeteststore.blob.core.windows.net/vhds/cli1cca1d20a1dcf56c-os-1461800591317.vhd
data:
data:    OS Profile:
data:      Computer Name                 :TestVM1
data:      User Name                     :ops
data:      Linux Configuration:
data:        Disable Password Auth       :true
data:
data:    Network Profile:
data:      Network Interfaces:
data:        Network Interface #1:
data:          Primary                   :true
data:          MAC Address               :00-0D-3A-20-F8-8B
data:          Provisioning State        :Succeeded
data:          Name                      :LB-NIC1
data:          Location                  :westeurope
data:
data:    AvailabilitySet:
data:      Id                            :/subscriptions/guid/resourceGroups/testrg/providers/Microsoft.Compute/availabilitySets/TESTAVAILSET
data:
data:    Diagnostics Profile:
data:      BootDiagnostics Enabled       :true
data:      BootDiagnostics StorageUri    :https://computeteststore.blob.core.windows.net/
data:
data:      Diagnostics Instance View:
info:    vm show command OK
```

## 다음 단계

이제 여러 네트워킹 구성 요소 및 VM을 시작할 준비가 되셨습니다.

<!---HONumber=AcomDC_0608_2016-->