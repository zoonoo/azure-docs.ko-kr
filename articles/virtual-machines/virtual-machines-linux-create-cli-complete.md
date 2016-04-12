<properties
   pageTitle="Azure CLI를 사용하여 Linux VM을 처음부터 새로 만들기 | Microsoft Azure"
   description="Azure CLI를 사용하여 Linux VM, 저장소, 가상 네트워크 및 서브넷, NIC, 공용 IP, 네트워크 보안 그룹을 모두 처음부터 새로 만듭니다."
   services="virtual-machines-linux"
   documentationCenter="virtual-machines"
   authors="vlivech"
   manager="squillace"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="02/10/2016"
   ms.author="v-livech"/>

# Azure CLI를 사용하여 Linux VM을 처음부터 새로 만들기


## 목표

- 리소스 그룹 배포
- 저장소 계정 배포
- 가상 네트워크 및 서브넷 배포
- 네트워크 보안 그룹 및 인바운드 규칙 구성
- NIC에 공용 IP 할당
- NIC에 NSG 할당
- Ubuntu 14.04 LTS VM 배포

## 필수 조건

- Azure 계정
  - [무료 평가판을 사용해보세요.](https://azure.microsoft.com/pricing/free-trial/) 
  - [Azure 포털](https://portal.azure.com)
- JSON 구문 분석 도구: 이 예에서는 [jq](https://stedolan.github.io/jq/)를 사용합니다.


## 소개

이 문서에서는 VNetwork 서브넷 내부에 Linux VM이 한 대 있는 클라우드 서비스 배포와 비슷한 배포를 빌드할 것입니다. 인터넷 어디서나 연결할 수 있는 정상적으로 작동하는 안전한 Linux VM이 생길 때까지 전체 기본 배포를 명령별로 철저하게 살펴보겠습니다.

그 과정에서 리소스 관리자 배포 모델이 제공하는 종속성 계층 구조와 강력한 기능을 이해할 수 있을 것입니다. 시스템이 빌드되는 방식을 살펴본 후에는 더 많은 Azure CLI 명령을 직접 사용하여 훨씬 신속하게 시스템을 다시 빌드하거나(`azure vm quick-create` 명령을 사용하는 거의 동일한 배포는 [여기](virtual-machines-linux-quick-create-cli.md) 참조) [Azure Resource Manager 템플릿](../resource-group-authoring-templates.md)을 사용하여 전체 네트워크 및 응용 프로그램 배포를 설계 및 자동화하고 업데이트하는 방법을 익힐 수 있습니다. 배포의 여러 파트가 서로 어떻게 연결되는지 살펴보면 파트를 자동화하는 템플릿을 좀 더 쉽게 만들 수 있습니다.

개발 및 간단한 계산에 유용한 VM을 사용하여 간단한 네트워크를 빌드하고, 차례대로 설명하겠습니다. 그 후에는 좀 더 복잡한 네트워크 및 배포로 넘어갈 수 있을 것입니다.

## 빠른 명령

_이 빠른 명령 섹션의 이름 지정에는 사용자 고유의 설정으로 바꾸고 필요에 따라 편집해야 하는 여러 예가 포함되어 있습니다._

```bash
# Create the Resource Group
azure group create TestRG westeurope

# Create the Storage Account
azure storage account create \  
--location westeurope \
--resource-group TestRG \
--type GRS \
computeteststore

# Verify the RG using the JSON parser
azure group show testrg --json | jq '.'

# Create the Virtual Network
azure network vnet create -g TestRG -n TestVNet -a 192.168.0.0/16 -l westeurope

# Verify the RG
azure group show testrg --json | jq '.'

# Create the Subnet
azure network vnet subnet create -g TestRG -e TestVNet -n FrontEnd -a 192.168.1.0/24

# Verify the VNet and Subnet
azure network vnet show testrg testvnet --json | jq '.'

# Create the NIC
azure network nic create -g TestRG -n TestNIC -l westeurope -a 192.168.1.101 -m TestVNet -k FrontEnd

# Verify the NIC
azure network nic show testrg testnic --json | jq '.'

# Create the NSG
azure network nsg create testrg testnsg westeurope

# Add an inbound rule for the NSG
azure network nsg rule create --protocol tcp --direction inbound --priority 1000  --destination-port-range 22 --access allow testrg testnsg testnsgrule

# Creat the Public facing NIC
azure network public-ip create -d testsubdomain testrg testpip westeurope

# Verify the NIC
azure network public-ip show testrg testpip --json | jq '.'

# Associate the Public IP to the NIC
azure network nic set --public-ip-name testpip testrg testnic

# Bind the NSG to the NIC
azure network nic set --network-security-group-name testnsg testrg testnic

# Create the Linux VM
azure vm create \            
    --resource-group testrg \
    --name testvm \
    --location westeurope \
    --os-type linux \
    --nic-name testnic \
    --vnet-name testvnet \
    --vnet-subnet-name FrontEnd \
    --storage-account-name computeteststore \
    --image-urn canonical:UbuntuServer:14.04.3-LTS:latest \
    --ssh-publickey-file ~/.ssh/id_rsa.pub \
    --admin-username ops
    
# Verify everything built
azure vm show testrg testvm 

```


## 자세한 연습

### 리소스 그룹을 만들고 배포 위치 선택 

Azure 리소스 그룹은 리소스 배포를 논리적으로 관리할 수 있는 구성 및 기타 메타데이터를 포함하는 논리적 배포 엔터티입니다.

    azure group create TestRG westeurope                        
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

### 저장소 계정 만들기 

무엇보다도 VM 디스크 및 추가하려는 추가 데이터 디스크에 대한 저장소 계정이 필요합니다. 요약하자면, 항상 리소스 그룹을 만든 후 거의 즉시 저장소 계정을 만들게 됩니다.

여기서는 `azure storage account create` 명령을 사용하여 계정 위치, 계정을 제어할 리소스 그룹, 원하는 저장소 지원 유형을 전달합니다.

    azure storage account create \  
    --location westeurope \
    --resource-group TestRG \
    --type GRS \
    computeteststore
    info:    Executing command storage account create
    + Creating storage account
    info:    storage account create command OK
    rasquill•~/workspace/keygen» azure group show testrg 
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
    
`--json` Azure CLI 옵션과 함께 [jq](https://stedolan.github.io/jq/) 도구를 사용하여(**jsawk** 또는 JSON 구문 분석에 선호하는 모든 언어 라이브러리 사용 가능) `azure group show` 명령으로 리소스 그룹을 검사해 보겠습니다.

    azure group show testrg --json | jq '.'                                                                                        
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

CLI를 사용하여 저장소 계정을 확인하려면 먼저 다음 명령을 사용하여 계정 이름 및 키를 설정하고, 이 문서의 저장소 계정 이름을 사용자 고유의 이름으로 바꿔야 합니다.

        AZURE_STORAGE_CONNECTION_STRING="$(azure storage account connectionstring show computeteststore --resource-group testrg --json | jq -r '.string')"

그러면 저장소 정보를 쉽게 볼 수 있습니다.

        azure storage container list 
        info:    Executing command storage container list
        + Getting storage containers
        data:    Name  Public-Access  Last-Modified
        data:    ----  -------------  -----------------------------
        data:    vhds  Off            Sun, 27 Sep 2015 19:03:54 GMT
        info:    storage container list command OK

### 가상 네트워크 및 서브넷 만들기 

VM을 설치할 수 있는 Azure 가상 네트워크 및 서브넷을 만들어야 합니다.

    azure network vnet create -g TestRG -n TestVNet -a 192.168.0.0/16 -l westeurope
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

json 옵션 `azure group show` 및 **jq**를 사용하여 리소스를 빌드하는 방법을 다시 한 번 살펴보겠습니다. 이제 `storageAccounts` 리소스 및 `virtualNetworks` 리소스가 생겼습니다.

    azure group show testrg --json | jq '.'
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

이번에는 VM이 배포될 `TestVnet` 가상 네트워크에 서브넷을 만들겠습니다. 이미 만들어 놓은 `TestRG` 리소스 그룹 및 `TestVNet` 가상 네트워크와 함께 `azure network vnet subnet create` 명령을 사용하고, 다음과 같이 서브넷 이름 `FrontEnd` 및 서브넷 주소 접두사 `192.168.1.0/24`를 추가하겠습니다.

    azure network vnet subnet create -g TestRG -e TestVNet -n FrontEnd -a 192.168.1.0/24
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
  
서브넷은 논리적으로 가상 네트워크 안에 있으므로 약간 다른 `azure network vnet show` 명령을 사용하여 서브넷 정보를 살펴보겠습니다. 하지만 JSON 출력은 여전히 **jq**를 사용하여 검사하겠습니다.

    azure network vnet show testrg testvnet --json | jq '.'
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

### Linux VM과 함께 사용할 NIC 만들기

NIC는 프로그래밍 방식으로 사용할 수도 있습니다. NIC에 사용법 규칙을 적용하고 NIC를 여러 개 만들 수 있습니다.

        azure network nic create -g TestRG -n TestNIC -l westeurope -a 192.168.1.101 -m TestVNet -k FrontEnd
        info:    Executing command network nic create
        + Looking up the network interface "TestNIC"
        + Looking up the subnet "FrontEnd"
        + Creating network interface "TestNIC"
        + Looking up the network interface "TestNIC"
        data:    Id                              : /subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC
        data:    Name                            : TestNIC
        data:    Type                            : Microsoft.Network/networkInterfaces
        data:    Location                        : westeurope
        data:    Provisioning state              : Succeeded
        data:    Enable IP forwarding            : false
        data:    IP configurations:
        data:      Name                          : NIC-config
        data:      Provisioning state            : Succeeded
        data:      Private IP address            : 192.168.1.101
        data:      Private IP Allocation Method  : Static
        data:      Subnet                        : /subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd
        data:
        info:    network nic create command OK

NIC 리소스는 VM 및 네트워크 보안 그룹에 모두 연결되기 때문에 `TestRG` 리소스 그룹을 검사할 때 NIC 리소스를 최상위 리소스로 볼 수 있습니다.

        azure group show testrg --json | jq '.'
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

`azure network nic show` 명령으로 리소스를 직접 검사하여 세부 정보를 볼 수 있습니다.

        azure network nic show testrg testnic --json | jq '.'
        {
        "ipConfigurations": [
            {
            "loadBalancerBackendAddressPools": [],
            "loadBalancerInboundNatRules": [],
            "privateIpAddress": "192.168.1.101",
            "privateIpAllocationMethod": "Static",
            "subnet": {
                "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd"
            },
            "provisioningState": "Succeeded",
            "name": "NIC-config",
            "etag": "W/"4d29b1ca-0207-458c-b258-f298e6fc450f"",
            "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC/ipConfigurations/NIC-config"
            }
        ],
        "tags": {},
        "dnsSettings": {
            "appliedDnsServers": [],
            "dnsServers": []
        },
        "enableIPForwarding": false,
        "provisioningState": "Succeeded",
        "etag": "W/"4d29b1ca-0207-458c-b258-f298e6fc450f"",
        "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC",
        "name": "TestNIC",
        "location": "westeurope"
        }

### 네트워크 보안 그룹 및 규칙 만들기

이번에는 네트워크 보안 그룹(NSG) 및 NIC 액세스를 제어하는 인바운드 규칙을 만들겠습니다.

        azure network nsg create testrg testnsg westeurope

포트 22에 인바운드 연결이 가능하도록(SSH를 지원하기 위해) NSG에 대한 인바운드 규칙을 추가해 봅시다.

        azure network nsg rule create --protocol tcp --direction inbound --priority 1000  --destination-port-range 22 --access allow testrg testnsg testnsgrule

> [AZURE.NOTE] 인바운드 규칙은 인바운드 네트워크 연결에 대한 필터입니다. 이 예에서는 NSG를 VM 가상 네트워크 인터페이스 카드(NIC)에 바인딩할 것입니다. 이는 포트 22에 대한 모든 요청이 VM의 NIC에 전달된다는 뜻입니다. 이 규칙은 클래식 배포처럼 끝점이 아니라 포트를 열기 위한 네트워크 연결에 대한 규칙이므로 **모든** 요청 포트에서 오는 인바운드 요청을 수락할 수 있도록 `--source-port-range`를 기본값인 '*'로 두어야 합니다. 일반적으로 이 값은 동적으로 변합니다.

### PIP(공용 IP 주소) 만들기

이제 인터넷에서 `azure network public-ip create` 명령을 사용하여 VM에 연결할 수 있는 PIP(공용 IP 주소)를 만들어 보겠습니다. 기본값은 동적 주소이므로 `-d testsubdomain` 옵션을 사용하여 **cloudapp.azure.com** 도메인에 명명된 DNS 항목을 만듭니다.

        azure network public-ip create -d testsubdomain testrg testpip westeurope
        info:    Executing command network public-ip create
        + Looking up the public ip "testpip"
        + Creating public ip address "testpip"
        + Looking up the public ip "testpip"
        data:    Id                              : /subscriptions/guid/resourceGroups/testrg/providers/Microsoft.Network/publicIPAddresses/testpip
        data:    Name                            : testpip
        data:    Type                            : Microsoft.Network/publicIPAddresses
        data:    Location                        : westeurope
        data:    Provisioning state              : Succeeded
        data:    Allocation method               : Dynamic
        data:    Idle timeout                    : 4
        data:    Domain name label               : testsubdomain
        data:    FQDN                            : testsubdomain.westeurope.cloudapp.azure.com
        info:    network public-ip create command OK

이 주소 역시 최상위 리소스이므로 `azure group show` 명령을 사용하여 볼 수 있습니다.

        azure group show testrg --json | jq '.'
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

그리고 늘 그렇듯이, 좀 더 완전한 `azure network public-ip show` 명령을 사용하여 하위 도메인 FQDN(정규화된 도메인 이름)을 포함하여 더 많은 리소스 세부 정보를 조사할 수 있습니다. 공용 IP 주소 리소스가 논리적으로 할당되었지만 아직 특정 주소가 할당되지는 않았습니다. 그래서 VM이 필요한데, 아직 만들지 않았습니다.

        azure network public-ip show testrg testpip --json | jq '.'
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

### 공용 IP 및 네트워크 보안 그룹을 NIC에 연결

        azure network nic set --public-ip-name testpip testrg testnic

NSG를 NIC에 바인딩:

        azure network nic set --network-security-group-name testnsg testrg testnic

### Linux VM 만들기

인터넷에서 액세스 가능한 VM을 지원하기 위해 저장소 및 네트워크 리소스를 만들었습니다. 이제 해당 VM을 만들고, 암호 없이 SSH 키를 사용하여 VM을 보호하겠습니다. 이 예에서는 가장 최근의 LTS를 기반으로 Ubuntu VM을 만들겠습니다. [Azure VM 이미지 찾기](virtual-machines-linux-cli-ps-findimage.md)에 설명된 대로, `azure vm image list` 명령을 사용하여 해당 이미지 정보를 찾을 것입니다. 앞에서 `azure vm image list westeurope canonical | grep LTS` 명령을 사용하여 이미지를 선택했는데, 이 예에서는 `canonical:UbuntuServer:14.04.3-LTS:14.04.201509080` 명령을 사용할 것입니다. 하지만 마지막 필드에 대해서는 향후에 항상 가장 최근 빌드를 가져오도록 `latest`를 전달하겠습니다(사용하게 될 문자열은 `canonical:UbuntuServer:14.04.3-LTS:latest`).

> [AZURE.NOTE] 다음 단계는 **ssh-keygen -t rsa -b 2048**을 사용하여 Linux 또는 Mac에서 ssh rsa 공개 키 및 개인 키 쌍을 만든 경험이 있는 분들이라면 아주 익숙할 것입니다. `~/.ssh` 디렉터리에 인증서 키 쌍이 하나도 없으면 다음 방법으로 만들 수 있습니다. <br /> 1. `azure vm create --generate-ssh-keys` 옵션을 사용하여 자동으로 만들기 2. [직접 인증서 키 쌍을 만드는 지침](virtual-machines-linux-ssh-from-linux.md)에 따라 수동으로 만들기 <br /> 또는 VM이 만들어지면 `azure vm create --admin-username --admin-password` 옵션을 사용하여 일반적으로 보안 수준이 낮은 사용자 이름 및 암호로 ssh 연결을 인증하는 방법도 있습니다.

`azure vm create` 명령으로 모든 리소스 및 정보를 합쳐서 VM을 만듭니다.

        azure vm create \            
        --resource-group testrg \
        --name testvm \
        --location westeurope \
        --os-type linux \
        --nic-name testnic \
        --vnet-name testvnet \
        --vnet-subnet-name FrontEnd \
        --storage-account-name computeteststore \
        --image-urn canonical:UbuntuServer:14.04.3-LTS:latest \
        --ssh-publickey-file ~/.ssh/id_rsa.pub \
        --admin-username ops
        info:    Executing command vm create
        + Looking up the VM "testvm"
        info:    Verifying the public key SSH file: /Users/user/.ssh/id_rsa.pub
        info:    Using the VM Size "Standard_A1"
        info:    The [OS, Data] Disk or image configuration requires storage account
        + Looking up the storage account computeteststore
        + Looking up the NIC "testnic"
        info:    Found an existing NIC "testnic"
        info:    Found an IP configuration with virtual network subnet id "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd" in the NIC "testnic"
        info:    This NIC IP configuration has a public ip already configured "/subscriptions/guid/resourcegroups/testrg/providers/microsoft.network/publicipaddresses/testpip", any public ip parameters if provided, will be ignored.
        + Creating VM "testvm"
        info:    vm create command OK

그 즉시, 기본 ssh 키를 사용하여 VM에 연결할 수 있습니다.

        ssh ops@testsubdomain.westeurope.cloudapp.azure.com           
        The authenticity of host 'testsubdomain.westeurope.cloudapp.azure.com (XX.XXX.XX.XXX)' can't be established.
        RSA key fingerprint is b6:a4:7g:4b:cb:cd:76:87:63:2d:84:83:ac:12:2d:cd.
        Are you sure you want to continue connecting (yes/no)? yes
        Warning: Permanently added 'testsubdomain.westeurope.cloudapp.azure.com,XX.XXX.XX.XXX' (RSA) to the list of known hosts.
        Welcome to Ubuntu 14.04.3 LTS (GNU/Linux 3.19.0-28-generic x86_64)
        
        * Documentation:  https://help.ubuntu.com/
        
        System information as of Mon Sep 28 18:45:02 UTC 2015
        
        System load: 0.64              Memory usage: 5%   Processes:       81
        Usage of /:  45.3% of 1.94GB   Swap usage:   0%   Users logged in: 0
        
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
        
        ops@testvm:~$

이제 `azure vm show testrg testvm` 명령을 사용하여 앞에서 만든 것들을 검사할 수 있습니다. 현재 Azure에서 실행 중인 Ubuntu VM이 하나 있습니다. 보유한 ssh 키 쌍을 사용하여 이 VM에 로그인할 수 있으며 암호는 사용할 수 없습니다.

        azure vm show testrg testvm 
        info:    Executing command vm show
        + Looking up the VM "testvm"
        + Looking up the NIC "testnic"
        + Looking up the public ip "testpip"
        data:    Id                              :/subscriptions/guid/resourceGroups/testrg/providers/Microsoft.Compute/virtualMachines/testvm
        data:    ProvisioningState               :Succeeded
        data:    Name                            :testvm
        data:    Location                        :westeurope
        data:    FQDN                            :testsubdomain.westeurope.cloudapp.azure.com
        data:    Type                            :Microsoft.Compute/virtualMachines
        data:
        data:    Hardware Profile:
        data:      Size                          :Standard_A1
        data:
        data:    Storage Profile:
        data:      Image reference:
        data:        Publisher                   :canonical
        data:        Offer                       :UbuntuServer
        data:        Sku                         :14.04.3-LTS
        data:        Version                     :latest
        data:
        data:      OS Disk:
        data:        OSType                      :Linux
        data:        Name                        :cli4eecdddc349d6015-os-1443465824206
        data:        Caching                     :ReadWrite
        data:        CreateOption                :FromImage
        data:        Vhd:
        data:          Uri                       :https://computeteststore.blob.core.windows.net/vhds/cli4eecdddc349d6015-os-1443465824206.vhd
        data:
        data:    OS Profile:
        data:      Computer Name                 :testvm
        data:      User Name                     :ops
        data:      Linux Configuration:
        data:        Disable Password Auth       :true
        data:        SSH Public Keys:
        data:          Public Key #1:
        data:            Path                    :/home/ops/.ssh/authorized_keys
        data:            Key                     :MIIBrTCCAZigAwIBAgIBATALBgkqhkiG9w0BAQUwADAiGA8yMDE1MDkyODE4MzM0
        <snip>
        data:
        data:    Network Profile:
        data:      Network Interfaces:
        data:        Network Interface #1:
        data:          Id                        :/subscriptions/guid/resourceGroups/testrg/providers/Microsoft.Network/networkInterfaces/testnic
        data:          Primary                   :true
        data:          MAC Address               :00-0D-3A-21-8E-AE
        data:          Provisioning State        :Succeeded
        data:          Name                      :testnic
        data:          Location                  :westeurope
        data:            Private IP alloc-method :Dynamic
        data:            Private IP address      :192.168.1.101
        data:            Public IP address       :40.115.48.189
        data:            FQDN                    :testsubdomain.westeurope.cloudapp.azure.com
        data:
        data:    Diagnostics Instance View:
        info:    vm show command OK

### 다음 단계

이제 여러 네트워킹 구성 요소 및 VM을 시작할 준비가 되셨습니다.

<!---HONumber=AcomDC_0330_2016-->