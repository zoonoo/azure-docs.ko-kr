
* [Azure에서 가상 컴퓨터 빨리 만들기](#quick-create-a-vm-in-azure)
* [템플릿에서 Azure의 가상 컴퓨터 배포](#deploy-a-vm-in-azure-from-a-template)
* [사용자 지정 이미지에서 가상 컴퓨터 만들기](#create-a-custom-vm-image)
* [가상 네트워크 및 부하 분산 장치를 사용하는 가상 컴퓨터 배포](#deploy-a-multi-vm-application-that-uses-a-virtual-network-and-an-external-load-balancer)
* [리소스 그룹 제거](#remove-a-resource-group)
* [리소스 그룹 배포에 대한 로그 표시](#show-the-log-for-a-resource-group-deployment)
* [가상 컴퓨터에 대한 정보 표시](#display-information-about-a-virtual-machine)
* [Linux 기반 가상 컴퓨터에 연결](#log-on-to-a-linux-based-virtual-machine)
* [가상 컴퓨터 중지](#stop-a-virtual-machine)
* [가상 컴퓨터 시작](#start-a-virtual-machine)
* [데이터 디스크 연결](#attach-a-data-disk)

## <a name="getting-ready"></a>준비
Azure 리소스 그룹에서 Azure CLI를 사용하려면 올바른 Azure CLI 버전 및 Azure 계정이 있어야 합니다. Azure CLI가 없으면 [설치](../articles/cli-install-nodejs.md)하세요.

### <a name="update-your-azure-cli-version-to-090-or-later"></a>0.9.0 이상으로 Azure CLI 버전 업데이트
0.9.0 버전 이상을 이미 설치했는지 확인하려면 `azure --version` 을 입력합니다.

```azurecli
azure --version
0.9.0 (node: 0.10.25)
```

버전이 0.9.0 이상이 아닌 경우 기본 설치 관리자 중 하나를 사용하거나 `npm update -g azure-cli`를 입력하여 **npm**을 통해 업데이트해야 합니다.

다음 [Docker 이미지](https://registry.hub.docker.com/u/microsoft/azure-cli/)를 사용하여 Docker 컨테이너로 Azure CLI를 실행할 수도 있습니다. Docker 호스트에서 다음 명령을 실행합니다.

```bash
docker run -it microsoft/azure-cli
```

### <a name="set-your-azure-account-and-subscription"></a>Azure 계정 및 구독 설정
Azure 구독은 아직 없지만 MSDN 구독은 있는 경우 [MSDN 구독자 혜택](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)을 활성화할 수 있습니다. 또는 [무료 평가판](https://azure.microsoft.com/pricing/free-trial/)에 등록할 수 있습니다.

이제 `azure login`를 입력하여 [Azure 계정에 대화식으로 로그인](../articles/xplat-cli-connect.md#scenario-1-azure-login-with-interactive-login)하고 Azure 계정의 대화식 로그인 환경에 대한 메시지를 따릅니다. 

> [!NOTE]
> 회사 또는 학교 ID가 있고 2단계 인증이 활성화되지 않은 경우에는 대화형 세션 *없이* 로그인하도록 회사 또는 학교 ID와 함께 `azure login -u`를 사용할 **수도** 있습니다. 회사 또는 학교 ID가 없는 경우, 같은 방식으로 로그인하려면 [개인 Microsoft 계정에서 회사 또는 학교 ID를 만들 수](../articles/virtual-machines/virtual-machines-windows-create-aad-work-id.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 있습니다.
>
>

계정에는 둘 이상의 구독이 있을 수 있습니다. `azure account list`를 입력하여 구독을 나열할 수 있으며, 다음과 같이 표시될 수 있습니다.

```azurecli
azure account list
info:    Executing command account list
data:    Name                              Id                                    Tenant Id                            Current
data:    --------------------------------  ------------------------------------  ------------------------------------  -------
data:    Contoso Admin                     xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx  xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx  true
data:    Fabrikam dev                      xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx  xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx  false  
data:    Fabrikam test                     xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx  xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx  false  
data:    Contoso production                xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx  xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx  false  
```

다음을 입력하여 현재 Azure 구독을 설정할 수 있습니다. 관리하려는 리소스가 있는 구독 이름 또는 ID를 사용합니다.

```azurecli
azure account set <subscription name or ID> true
```

### <a name="switch-to-the-azure-cli-resource-group-mode"></a>Azure CLI 리소스 그룹 모드로 전환
기본적으로 Azure CLI는 서비스 관리 모드(**asm** 모드)로 시작됩니다. 다음을 입력하여 리소스 그룹 모드로 전환합니다.

```azurecli
azure config mode arm
```

## <a name="understanding-azure-resource-templates-and-resource-groups"></a>Azure 리소스 템플릿 및 리소스 그룹 이해
대부분의 응용 프로그램은 다양한 리소스 유형(예: 하나 이상의 VM 및 저장소 계정, SQL 데이터베이스, 가상 네트워크 또는 콘텐츠 배달 네트워크)의 조합으로 구축되었습니다. 기본 Azure 서비스 관리 API 및 Azure 클래식 포털에서는 서비스 단위 접근 방식을 사용하여 이러한 항목을 나타냈습니다. 이 접근 방식에서는 하나의 논리적인 배포 단위가 아니라 개별적으로 각 서비스를 배포하고 관리(또는 이러한 작업을 수행하는 다른 도구를 찾아야 함)해야 합니다.

하지만 *Azure 리소스 관리자 템플릿*을 사용하면 선언적 방식으로 이러한 다양한 리소스를 하나의 논리적 배포 단위로 배포하고 관리할 수 있습니다. 명령을 통해 차례로 배포할 항목을 Azure에 지시하는 대신 JSON 파일에서 전체 배포(모든 리소스와 관련된 구성 및 배포 매개 변수)를 설명하고 이러한 리소스를 하나의 그룹으로 배포하도록 Azure에 지시합니다.

그런 다음 Azure CLI 리소스 관리 명령을 사용하여 그룹 리소스의 전체 수명 주기를 관리할 수 있습니다.

* 그룹 내의 모든 리소스를 한 번에 중지, 시작 또는 삭제합니다.
* RBAC(역할 기반 액세스 제어) 규칙을 적용하여 리소스에 대한 보안 권한을 잠급니다.
* 작업을 감사합니다.
* 추가 메타데이터로 리소스에 태그를 지정하여 추적을 개선합니다.

Azure 리소스 그룹 및 기능에 대한 자세한 내용은 [Azure Resource Manager 개요](../articles/azure-resource-manager/resource-group-overview.md)에서 확인할 수 있습니다. 템플릿 작성에 관심이 있다면 [Azure 리소스 관리자 템플릿 작성](../articles/resource-group-authoring-templates.md)을 참조하세요.

## <a id="quick-create-a-vm-in-azure"></a>작업: Azure에서 VM 빠르게 만들기
경우에 따라 필요한 이미지를 알고 있고 해당 이미지로 만든 VM이 당장 필요할 수 있지만 인프라에 대해서는 너무 염려하지 않아도 됩니다. 새 VM에서 테스트할 수도 있습니다. 이때 `azure vm quick-create` 명령을 사용하고 VM 및 해당 인프라를 만드는 데 필요한 인수를 전달하면 됩니다.

먼저 리소스 그룹을 만듭니다.

```azurecli
azure group create coreos-quick westus
info:    Executing command group create
+ Getting resource group coreos-quick
+ Creating resource group coreos-quick
info:    Created resource group coreos-quick
data:    Id:                  /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/coreos-quick
data:    Name:                coreos-quick
data:    Location:            westus
data:    Provisioning State:  Succeeded
data:    Tags:
data:
info:    group create command OK
```

두 번째로 이미지가 필요합니다. Azure CLI를 사용하여 이미지를 찾으려면 [PowerShell 및 Azure CLI를 사용하여 Azure 가상 컴퓨터 이미지 탐색 및 선택](../articles/virtual-machines/virtual-machines-linux-cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)을 참조하세요. 그러나 이 문서에서는 다음과 같이 많이 사용되는 간단한 이미지 목록을 제공합니다. 이 quick-create에서는 CoreOS의 Stable 이미지를 사용합니다.

> [!NOTE]
> ComputeImageVersion의 경우, 템플릿 언어 및 Azure CLI 모두에서 매개 변수로 단순히 '최신'을 제공할 수도 있습니다. 이렇게 하면 사용자 스크립트 또는 템플릿을 수정하지 않고도 최신 및 패치가 적용된 버전의 이미지를 항상 사용할 수 있습니다. 다음과 같습니다.
>
>

| PublisherName | 제안 | SKU | 버전 |
|:--- |:--- |:--- |:--- |
| OpenLogic |CentOS |7 |7.0.201503 |
| OpenLogic |CentOS |7.1 |7.1.201504 |
| CoreOS |CoreOS |베타 |647.0.0 |
| CoreOS |CoreOS |Stable |633.1.0 |
| MicrosoftDynamicsNAV |DynamicsNAV |2015 |8.0.40459 |
| MicrosoftSharePoint |MicrosoftSharePointServer |2013 |1.0.0 |
| msopentech |Oracle-Database-12c-Weblogic-Server-12c |Standard |1.0.0 |
| msopentech |Oracle-Database-12c-Weblogic-Server-12c |Enterprise |1.0.0 |
| MicrosoftSQLServer |SQL2014-WS2012R2 |Enterprise-Optimized-for-DW |12.0.2430 |
| MicrosoftSQLServer |SQL2014-WS2012R2 |Enterprise-Optimized-for-OLTP |12.0.2430 |
| Canonical |UbuntuServer |12.04.5-LTS |12.04.201504230 |
| Canonical |UbuntuServer |14.04.2-LTS |14.04.201503090 |
| MicrosoftWindowsServer |WindowsServer |2012-Datacenter |3.0.201503 |
| MicrosoftWindowsServer |WindowsServer |2012-R2-Datacenter |4.0.201503 |
| MicrosoftWindowsServer |WindowsServer |Windows-Server-Technical-Preview |5.0.201504 |
| MicrosoftWindowsServerEssentials |WindowsServerEssentials |WindowsServerEssentials |1.0.141204 |
| MicrosoftWindowsServerHPCPack |WindowsServerHPCPack |2012R2 |4.3.4665 |

`azure vm quick-create` 명령을 입력하고 프롬프트를 준비하여 VM을 만들면 됩니다. 다음과 같이 표시됩니다.

```azurecli
azure vm quick-create
info:    Executing command vm quick-create
Resource group name: coreos-quick
Virtual machine name: coreos
Location name: westus
Operating system Type [Windows, Linux]: linux
ImageURN (format: "publisherName:offer:skus:version"): coreos:coreos:stable:latest
User name: ops
Password: *********
Confirm password: *********
+ Looking up the VM "coreos"
info:    Using the VM Size "Standard_A1"
info:    The [OS, Data] Disk or image configuration requires storage account
+ Retrieving storage accounts
info:    Could not find any storage accounts in the region "westus", trying to create new one
+ Creating storage account "cli9fd3fce49e9a9b3d14302" in "westus"
+ Looking up the storage account cli9fd3fce49e9a9b3d14302
+ Looking up the NIC "coreo-westu-1430261891570-nic"
info:    An nic with given name "coreo-westu-1430261891570-nic" not found, creating a new one
+ Looking up the virtual network "coreo-westu-1430261891570-vnet"
info:    Preparing to create new virtual network and subnet
/ Creating a new virtual network "coreo-westu-1430261891570-vnet" [address prefix: "10.0.0.0/16"] with subnet "coreo-westu-1430261891570-sne+" [address prefix: "10.0.1.0/24"]
+ Looking up the virtual network "coreo-westu-1430261891570-vnet"
+ Looking up the subnet "coreo-westu-1430261891570-snet" under the virtual network "coreo-westu-1430261891570-vnet"
info:    Found public ip parameters, trying to setup PublicIP profile
+ Looking up the public ip "coreo-westu-1430261891570-pip"
info:    PublicIP with given name "coreo-westu-1430261891570-pip" not found, creating a new one
+ Creating public ip "coreo-westu-1430261891570-pip"
+ Looking up the public ip "coreo-westu-1430261891570-pip"
+ Creating NIC "coreo-westu-1430261891570-nic"
+ Looking up the NIC "coreo-westu-1430261891570-nic"
+ Creating VM "coreos"
+ Looking up the VM "coreos"
+ Looking up the NIC "coreo-westu-1430261891570-nic"
+ Looking up the public ip "coreo-westu-1430261891570-pip"
data:    Id                              :/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/coreos-quick/providers/Microsoft.Compute/virtualMachines/coreos
data:    ProvisioningState               :Succeeded
data:    Name                            :coreos
data:    Location                        :westus
data:    FQDN                            :coreo-westu-1430261891570-pip.westus.cloudapp.azure.com
data:    Type                            :Microsoft.Compute/virtualMachines
data:
data:    Hardware Profile:
data:      Size                          :Standard_A1
data:
data:    Storage Profile:
data:      Image reference:
data:        Publisher                   :coreos
data:        Offer                       :coreos
data:        Sku                         :stable
data:        Version                     :633.1.0
data:
data:      OS Disk:
data:        OSType                      :Linux
data:        Name                        :cli9fd3fce49e9a9b3d-os-1430261892283
data:        Caching                     :ReadWrite
data:        CreateOption                :FromImage
data:        Vhd:
data:          Uri                       :https://cli9fd3fce49e9a9b3d14302.blob.core.windows.net/vhds/cli9fd3fce49e9a9b3d-os-1430261892283.vhd
data:
data:    OS Profile:
data:      Computer Name                 :coreos
data:      User Name                     :ops
data:      Linux Configuration:
data:        Disable Password Auth       :false
data:
data:    Network Profile:
data:      Network Interfaces:
data:        Network Interface #1:
data:          Id                        :/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/coreos-quick/providers/Microsoft.Network/networkInterfaces/coreo-westu-1430261891570-nic
data:          Primary                   :true
data:          MAC Address               :00-0D-3A-30-72-E3
data:          Provisioning State        :Succeeded
data:          Name                      :coreo-westu-1430261891570-nic
data:          Location                  :westus
data:            Private IP alloc-method :Dynamic
data:            Private IP address      :10.0.1.4
data:            Public IP address       :104.40.24.124
data:            FQDN                    :coreo-westu-1430261891570-pip.westus.cloudapp.azure.com
info:    vm quick-create command OK
```

이제 새 VM으로 전환하면 됩니다.

## <a id="deploy-a-vm-in-azure-from-a-template"></a>작업: 템플릿에서 Azure의 VM 배포
Azure CLI 및 템플릿을 사용하여 새 Azure VM을 배포하려면 이러한 섹션의 지침을 사용하세요. 이 템플릿에서는 단일 서브넷을 사용하는 새 가상 네트워크에 단일 가상 컴퓨터를 만들고 `azure vm quick-create`와 달리 원하는 항목을 정확하게 설명하고 오류 없이 반복할 수 있도록 합니다. 다음은 이 템플릿에서 만드는 항목입니다.

![](./media/virtual-machines-common-cli-deploy-templates/new-vm.png)

### <a name="step-1-examine-the-json-file-for-the-template-parameters"></a>1단계: JSON 파일에서 템플릿 매개 변수 검사
다음은 템플릿에 대한 JSON 파일의 내용입니다. (템플릿은 [GitHub](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-linux/azuredeploy.json)에도 있습니다.)

템플릿은 유연하므로 디자이너는 많은 매개 변수를 제공하도록 선택하거나 좀더 수정된 템플릿을 만들어 몇 개의 매개 변수만 제공하도록 선택할 수 있습니다. 템플릿을 매개 변수로 전달하는 데 필요한 정보를 수집하려면 템플릿 파일(이 항목에는 아래의 인라인에 템플릿이 있음)을 열고 **매개 변수** 값을 검사합니다.

이 경우 아래의 템플릿에서 다음 사항을 요청합니다.

* 고유한 저장소 계정 이름
* VM의 관리 사용자 이름
* 암호
* 사용할 외부 세계의 도메인 이름
* Ubuntu Server 버전 번호 -- 목록 중 하나만 허용

자세한 내용은 [사용자 이름 및 암호 요구 사항](../articles/virtual-machines/virtual-machines-linux-faq.md#what-are-the-username-requirements-when-creating-a-vm)을 참조하세요.

이러한 값을 확인했으면 해당 그룹을 만들고 Azure 구독에 이 템플릿을 배포할 수 있습니다.

```json
{
"$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
"contentVersion": "1.0.0.0",
"parameters": {
    "newStorageAccountName": {
    "type": "string",
    "metadata": {
        "description": "Unique DNS name for the storage account where the virtual machine's disks will be placed."
    }
    },
    "adminUsername": {
    "type": "string",
    "metadata": {
        "description": "User name for the virtual machine."
    }
    },
    "adminPassword": {
    "type": "securestring",
    "metadata": {
        "description": "Password for the virtual machine."
    }
    },
    "dnsNameForPublicIP": {
    "type": "string",
    "metadata": {
        "description": "Unique DNS name for the public IP used to access the virtual machine."
    }
    },
    "ubuntuOSVersion": {
    "type": "string",
    "defaultValue": "14.04.2-LTS",
    "allowedValues": [
        "12.04.5-LTS",
        "14.04.2-LTS",
        "15.04"
    ],
    "metadata": {
        "description": "The Ubuntu version for the VM. This will pick a fully patched image of this given Ubuntu version. Allowed values: 12.04.5-LTS, 14.04.2-LTS, 15.04."
    }
    }
},
"variables": {
    "location": "West US",
    "imagePublisher": "Canonical",
    "imageOffer": "UbuntuServer",
    "OSDiskName": "osdiskforlinuxsimple",
    "nicName": "myVMNic",
    "addressPrefix": "10.0.0.0/16",
    "subnetName": "Subnet",
    "subnetPrefix": "10.0.0.0/24",
    "storageAccountType": "Standard_LRS",
    "publicIPAddressName": "myPublicIP",
    "publicIPAddressType": "Dynamic",
    "vmStorageAccountContainerName": "vhds",
    "vmName": "MyUbuntuVM",
    "vmSize": "Standard_D1",
    "virtualNetworkName": "MyVNET",
    "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',variables('virtualNetworkName'))]",
    "subnetRef": "[concat(variables('vnetID'),'/subnets/',variables('subnetName'))]"
},
"resources": [
    {
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[parameters('newStorageAccountName')]",
    "apiVersion": "2015-05-01-preview",
    "location": "[variables('location')]",
    "properties": {
        "accountType": "[variables('storageAccountType')]"
    }
    },
    {
    "apiVersion": "2015-05-01-preview",
    "type": "Microsoft.Network/publicIPAddresses",
    "name": "[variables('publicIPAddressName')]",
    "location": "[variables('location')]",
    "properties": {
        "publicIPAllocationMethod": "[variables('publicIPAddressType')]",
        "dnsSettings": {
        "domainNameLabel": "[parameters('dnsNameForPublicIP')]"
        }
    }
    },
    {
    "apiVersion": "2015-05-01-preview",
    "type": "Microsoft.Network/virtualNetworks",
    "name": "[variables('virtualNetworkName')]",
    "location": "[variables('location')]",
    "properties": {
        "addressSpace": {
        "addressPrefixes": [
            "[variables('addressPrefix')]"
        ]
        },
        "subnets": [
        {
            "name": "[variables('subnetName')]",
            "properties": {
            "addressPrefix": "[variables('subnetPrefix')]"
            }
        }
        ]
    }
    },
    {
    "apiVersion": "2015-05-01-preview",
    "type": "Microsoft.Network/networkInterfaces",
    "name": "[variables('nicName')]",
    "location": "[variables('location')]",
    "dependsOn": [
        "[concat('Microsoft.Network/publicIPAddresses/', variables('publicIPAddressName'))]",
        "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"
    ],
    "properties": {
        "ipConfigurations": [
        {
            "name": "ipconfig1",
            "properties": {
            "privateIPAllocationMethod": "Dynamic",
            "publicIPAddress": {
                "id": "[resourceId('Microsoft.Network/publicIPAddresses',variables('publicIPAddressName'))]"
            },
            "subnet": {
                "id": "[variables('subnetRef')]"
            }
            }
        }
        ]
    }
    },
    {
    "apiVersion": "2015-05-01-preview",
    "type": "Microsoft.Compute/virtualMachines",
    "name": "[variables('vmName')]",
    "location": "[variables('location')]",
    "dependsOn": [
        "[concat('Microsoft.Storage/storageAccounts/', parameters('newStorageAccountName'))]",
        "[concat('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
    ],
    "properties": {
        "hardwareProfile": {
        "vmSize": "[variables('vmSize')]"
        },
        "osProfile": {
        "computername": "[variables('vmName')]",
        "adminUsername": "[parameters('adminUsername')]",
        "adminPassword": "[parameters('adminPassword')]"
        },
        "storageProfile": {
        "imageReference": {
            "publisher": "[variables('imagePublisher')]",
            "offer": "[variables('imageOffer')]",
            "sku": "[parameters('ubuntuOSVersion')]",
            "version": "latest"
        },
        "osDisk": {
            "name": "osdisk",
            "vhd": {
            "uri": "[concat('http://',parameters('newStorageAccountName'),'.blob.core.windows.net/',variables('vmStorageAccountContainerName'),'/',variables('OSDiskName'),'.vhd')]"
            },
            "caching": "ReadWrite",
            "createOption": "FromImage"
        }
        },
        "networkProfile": {
        "networkInterfaces": [
            {
            "id": "[resourceId('Microsoft.Network/networkInterfaces',variables('nicName'))]"
            }
        ]
        }
    }
    }
]
}
```

### <a name="step-2-create-the-virtual-machine-by-using-the-template"></a>2단계: 템플릿을 사용하여 가상 컴퓨터 만들기
매개 변수 값을 준비했으면 템플릿 배포를 위한 리소스 그룹을 만든 다음 템플릿을 배포해야 합니다.

리소스 그룹을 만들려면 원하는 그룹 이름 및 배포할 데이터 센터 위치를 사용하여 `azure group create <group name> <location>` 을 입력합니다. 이 작업은 신속하게 수행됩니다.

```azurecli
azure group create myResourceGroup westus
info:    Executing command group create
+ Getting resource group myResourceGroup
+ Creating resource group myResourceGroup
info:    Created resource group myResourceGroup
data:    Id:                  /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup
data:    Name:                myResourceGroup
data:    Location:            westus
data:    Provisioning State:  Succeeded
data:    Tags:
data:
info:    group create command OK
```

이제 배포를 만들려면 `azure group deployment create` 를 호출하고 다음을 전달합니다.

* 템플릿 파일(위의 JSON 템플릿을 로컬 파일에 저장한 경우)
* 템플릿 URI(Github 또는 다른 웹 주소에 있는 파일을 가리키려는 경우)
* 배포에 사용할 리소스 그룹
* 배포 이름(선택 사항)

JSON 파일의 "parameters" 섹션에 매개 변수 값을 제공하라는 메시지가 표시됩니다. 모든 매개 변수 값을 지정하면 배포가 시작됩니다.

다음은 예제입니다.

```azurecli
azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-linux/azuredeploy.json myResourceGroup firstDeployment
info:    Executing command group deployment create
info:    Supply values for the following parameters
newStorageAccountName: storageaccount
adminUsername: ops
adminPassword: password
dnsNameForPublicIP: newdomainname
```

다음과 같은 유형의 정보가 제공됩니다.

```azurecli
+ Initializing template configurations and parameters
+ Creating a deployment
info:    Created template deployment "firstDeployment"
+ Registering providers
info:    Registering provider microsoft.storage
info:    Registering provider microsoft.network
info:    Registering provider microsoft.compute
+ Waiting for deployment to complete
data:    DeploymentName     : firstDeployment
data:    ResourceGroupName  : myResourceGroup
data:    ProvisioningState  : Succeeded
data:    Timestamp          : 2015-04-28T07:53:55.1828878Z
data:    Mode               : Incremental
data:    TemplateLink       : https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-simple-linux-vm/azuredeploy.json
data:    ContentVersion     : 1.0.0.0
data:    Name                   Type          Value
data:    ---------------------  ------------  -------------
data:    newStorageAccountName  String        storageaccount
data:    adminUsername          String        ops
data:    adminPassword          SecureString  undefined
data:    dnsNameForPublicIP     String        newdomainname
data:    ubuntuOSVersion        String        14.10
info:    group deployment create command OK
```


## <a id="create-a-custom-vm-image"></a>작업: 사용자 지정 VM 이미지 만들기
위에서 템플릿의 기본 사용법을 확인했으므로 이제 유사한 지침을 사용하여 Azure의 특정 .vhd 파일에서 Azure CLI를 통해 템플릿을 사용하여 사용자 지정 VM을 만들 수 있습니다. 여기서 차이점은 이 템플릿의 경우 특정 VHD(가상 하드 디스크)에서 단일 가상 컴퓨터를 만든다는 점입니다.

### <a name="step-1-examine-the-json-file-for-the-template"></a>1단계: JSON 파일에서 템플릿 검사
다음은 이 섹션에서 예로 사용하는 템플릿에 대한 JSON 파일의 내용입니다. (템플릿은 [GitHub](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-from-user-image/azuredeploy.json)에도 있습니다.)

또한 기본값이 없는 매개 변수에 대해 입력할 값을 찾아야 합니다. `azure group deployment create` 명령을 실행하면 해당 값을 입력하라는 메시지가 Azure CLI에 표시됩니다.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
    "contentVersion": "1.0.0.0",
    "parameters" : {
        "userImageStorageAccountName": {
            "type": "string",
            "defaultValue" : "userImageStorageAccountName"
        },
        "userImageStorageContainerName" : {
            "type" : "string",
            "defaultValue" : "userImageStorageContainerName"
        },
        "userImageVhdName" : {
            "type" : "string",
            "defaultValue" : "userImageVhdName"
        },
        "dnsNameForPublicIP" : {
            "type" : "string",
            "defaultValue": "uniqueDnsNameForPublicIP"
        },
        "adminUserName": {
            "type": "string"
        },
        "adminPassword": {
            "type": "securestring"
        },
        "osType" : {
            "type" : "string",
            "allowedValues" : [
                "windows",
                "linux"
            ]
        },
        "subscriptionId":{
            "type" : "string"
        },
        "location": {
            "type": "String",
            "defaultValue" : "West US"
        },
        "vmSize": {
            "type": "string",
            "defaultValue": "Standard_A2"
        },
        "publicIPAddressName": {
            "type": "string",
            "defaultValue" : "myPublicIP"
        },
        "vmName": {
            "type": "string",
            "defaultValue" : "myVM"
        },
        "virtualNetworkName":{
            "type" : "string",
            "defaultValue" : "myVNET"
        },
        "nicName":{
            "type" : "string",
            "defaultValue":"myNIC"
        }
    },
    "variables": {
        "addressPrefix":"10.0.0.0/16",
        "subnet1Name": "Subnet-1",
        "subnet2Name": "Subnet-2",
        "subnet1Prefix" : "10.0.0.0/24",
        "subnet2Prefix" : "10.0.1.0/24",
        "publicIPAddressType" : "Dynamic",
        "vnetID":"[resourceId('Microsoft.Network/virtualNetworks',parameters('virtualNetworkName'))]",
        "subnet1Ref" : "[concat(variables('vnetID'),'/subnets/',variables('subnet1Name'))]",
        "userImageName" : "[concat('http://',parameters('userImageStorageAccountName'),'.blob.core.windows.net/',parameters('userImageStorageContainerName'),'/',parameters('userImageVhdName'))]",
        "osDiskVhdName" : "[concat('http://',parameters('userImageStorageAccountName'),'.blob.core.windows.net/',parameters('userImageStorageContainerName'),'/',parameters('vmName'),'osDisk.vhd')]"
    },
    "resources": [
    {
        "apiVersion": "2014-12-01-preview",
        "type": "Microsoft.Network/publicIPAddresses",
        "name": "[parameters('publicIPAddressName')]",
        "location": "[parameters('location')]",
        "properties": {
            "publicIPAllocationMethod": "[variables('publicIPAddressType')]",
            "dnsSettings": {
                "domainNameLabel": "[parameters('dnsNameForPublicIP')]"
            }
        }
    },
    {
        "apiVersion": "2014-12-01-preview",
        "type": "Microsoft.Network/virtualNetworks",
        "name": "[parameters('virtualNetworkName')]",
        "location": "[parameters('location')]",
        "properties": {
        "addressSpace": {
            "addressPrefixes": [
            "[variables('addressPrefix')]"
            ]
        },
        "subnets": [
            {
            "name": "[variables('subnet1Name')]",
            "properties" : {
                "addressPrefix": "[variables('subnet1Prefix')]"
            }
            },
            {
            "name": "[variables('subnet2Name')]",
            "properties" : {
                "addressPrefix": "[variables('subnet2Prefix')]"
            }
            }
        ]
        }
    },
    {
        "apiVersion": "2014-12-01-preview",
        "type": "Microsoft.Network/networkInterfaces",
        "name": "[parameters('nicName')]",
        "location": "[parameters('location')]",
        "dependsOn": [
            "[concat('Microsoft.Network/publicIPAddresses/', parameters('publicIPAddressName'))]",
            "[concat('Microsoft.Network/virtualNetworks/', parameters('virtualNetworkName'))]"
        ],
        "properties": {
            "ipConfigurations": [
            {
                "name": "ipconfig1",
                "properties": {
                    "privateIPAllocationMethod": "Dynamic",
                    "publicIPAddress": {
                        "id": "[resourceId('Microsoft.Network/publicIPAddresses',parameters('publicIPAddressName'))]"
                    },
                    "subnet": {
                        "id": "[variables('subnet1Ref')]"
                    }
                }
            }
            ]
        }
    },
    {
        "apiVersion": "2014-12-01-preview",
        "type": "Microsoft.Compute/virtualMachines",
        "name": "[parameters('vmName')]",
        "location": "[parameters('location')]",
        "dependsOn": [
            "[concat('Microsoft.Network/networkInterfaces/', parameters('nicName'))]"
        ],
        "properties": {
            "hardwareProfile": {
                "vmSize": "[parameters('vmSize')]"
            },
            "osProfile": {
                "computername": "[parameters('vmName')]",
                "adminUsername": "[parameters('adminUsername')]",
                "adminPassword": "[parameters('adminPassword')]"
            },
            "storageProfile": {
                "osDisk" : {
                    "name" : "[concat(parameters('vmName'),'-osDisk')]",
                    "osType" : "[parameters('osType')]",
                    "caching" : "ReadWrite",
                    "image" : {
                        "uri" : "[variables('userImageName')]"
                    },
                    "vhd" : {
                        "uri" : "[variables('osDiskVhdName')]"
                    }
                }
            },
            "networkProfile": {
                "networkInterfaces" : [
                {
                    "id": "[resourceId('Microsoft.Network/networkInterfaces',parameters('nicName'))]"
                }
                ]
            }
        }
    }
    ]
}
```

### <a name="step-2-obtain-the-vhd"></a>2단계: VHD 얻기
이 경우 .vhd가 반드시 필요합니다. 이미 Azure에 있는 VHD를 사용하거나 업로드할 수 있습니다.

Windows 기반 가상 컴퓨터의 경우 [Windows Server VHD를 만들어서 Azure에 업로드](../articles/virtual-machines/windows/classic/createupload-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)를 참조하세요.

Linux 기반 가상 컴퓨터의 경우 [Linux 운영 체제를 포함하는 가상 하드 디스크 만들기 및 업로드](../articles/virtual-machines/linux/classic/create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)를 참조하세요.

### <a name="step-3-create-the-virtual-machine-by-using-the-template"></a>3단계: 템플릿을 사용하여 가상 컴퓨터 만들기
이제 .vhd에 기반을 둔 새 가상 컴퓨터를 만들 수 있습니다. `azure group create <location>`을 사용하여 배포할 그룹을 만듭니다.

```azurecli
azure group create myResourceGroupUser eastus
info:    Executing command group create
+ Getting resource group myResourceGroupUser
+ Creating resource group myResourceGroupUser
info:    Created resource group myResourceGroupUser
data:    Id:                  /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroupUser
data:    Name:                myResourceGroupUser
data:    Location:            eastus
data:    Provisioning State:  Succeeded
data:    Tags:
data:
info:    group create command OK
```

그런 다음 `--template-uri` 옵션을 사용하여 템플릿에서 직접 호출하여 배포를 만들거나 `--template-file` 옵션을 사용하여 로컬에 저장한 파일을 사용할 수 있습니다. 템플릿에는 기본값이 지정되어 있으므로 몇 가지 항목만 입력하면 됩니다. 템플릿을 다른 위치에 배포하는 경우 기본값에서 이름이 충돌하는 경우를 확인할 수 있습니다(특히 직접 만든 DNS 이름).

```azurecli
azure group deployment create \
> --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-from-user-image/azuredeploy.json \
> myResourceGroup \
> customVhdDeployment
info:    Executing command group deployment create
info:    Supply values for the following parameters
adminUserName: ops
adminPassword: password
osType: linux
subscriptionId: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

출력은 다음과 같이 표시됩니다.

```azurecli
+ Initializing template configurations and parameters
+ Creating a deployment
info:    Created template deployment "customVhdDeployment"
+ Registering providers
info:    Registering provider microsoft.network
info:    Registering provider microsoft.compute
+ Waiting for deployment to complete
error:   Deployment provisioning state was not successful
data:    DeploymentName     : customVhdDeployment
data:    ResourceGroupName  : myResourceGroupUser
data:    ProvisioningState  : Succeeded
data:    Timestamp          : 2015-04-28T14:55:48.0963829Z
data:    Mode               : Incremental
data:    TemplateLink       : https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-from-user-image/azuredeploy.json
data:    ContentVersion     : 1.0.0.0
data:    Name                           Type          Value
data:    -----------------------------  ------------  ------------------------------------
data:    userImageStorageAccountName    String        userImageStorageAccountName
data:    userImageStorageContainerName  String        userImageStorageContainerName
data:    userImageVhdName               String        userImageVhdName
data:    dnsNameForPublicIP             String        uniqueDnsNameForPublicIP
data:    adminUserName                  String        ops
data:    adminPassword                  SecureString  undefined
data:    osType                         String        linux
data:    subscriptionId                 String        xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
data:    location                       String        West US
data:    vmSize                         String        Standard_A2
data:    publicIPAddressName            String        myPublicIP
data:    vmName                         String        myVM
data:    virtualNetworkName             String        myVNET
data:    nicName                        String        myNIC
info:    group deployment create command OK
```

## <a id="deploy-a-multi-vm-application-that-uses-a-virtual-network-and-an-external-load-balancer"></a>작업: 가상 네트워크 및 외부 부하 분산 장치를 사용하는 여러 VM 응용 프로그램 배포
이 템플릿에서는 하나의 부하 분산 장치 아래에 2개의 가상 컴퓨터를 만들고 포트 80에서 부하 분산 규칙을 구성할 수 있습니다. 또한 이 템플릿에서는 저장소 계정, 가상 네트워크, 공용 IP 주소, 가용성 집합 및 네트워크 인터페이스를 배포합니다.

![](./media/virtual-machines-common-cli-deploy-templates/multivmextlb.png)

Azure PowerShell 명령을 통해 Github 템플릿 리포지토리의 리소스 관리자 템플릿을 사용하여 가상 네트워크 및 부하 분산 장치를 사용하는 여러 VM 응용 프로그램을 배포하려면 다음 단계를 수행하세요.

### <a name="step-1-examine-the-json-file-for-the-template"></a>1단계: JSON 파일에서 템플릿 검사
다음은 템플릿에 대한 JSON 파일의 내용입니다. 최신 버전은 [템플릿의 GitHub 리포지토리](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-2-vms-loadbalancer-lbrules/azuredeploy.json)에 있습니다. 이 항목에서는 `--template-uri` 스위치를 사용하여 템플릿에서 호출하지만 `--template-file` 스위치를 사용하여 로컬 버전을 전달할 수도 있습니다.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "metadata": {
                "description": "Location of resources"
            }
        },
        "storageAccountName": {
            "type": "string",
            "metadata": {
                "description": "Name of storage account"
            }
        },
        "adminUsername": {
            "type": "string",
            "metadata": {
                "description": "Admin user name"
            }
        },
        "adminPassword": {
            "type": "securestring",
            "metadata": {
                "description": "Admin password"
            }
        },
        "dnsNameforLBIP": {
            "type": "string",
            "metadata": {
                "description": "DNS for load balancer IP"
            }
        },
        "backendPort": {
            "type": "int",
            "defaultValue": 3389,
            "metadata": {
                "description": "Back-end port"
            }
        },
        "vmNamePrefix": {
            "type": "string",
            "defaultValue": "myVM",
            "metadata": {
                "description": "Prefix to use for VM names"
            }
        },
        "vmSourceImageName": {
            "type": "string",
            "defaultValue": "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-201412.01-en.us-127GB.vhd"
        },
        "lbName": {
            "type": "string",
            "defaultValue": "myLB",
            "metadata": {
                "description": "Load balancer name"
            }
        },
        "nicNamePrefix": {
            "type": "string",
            "defaultValue": "nic",
            "metadata": {
                "description": "Network interface name prefix"
            }
        },
        "publicIPAddressName": {
            "type": "string",
            "defaultValue": "myPublicIP",
            "metadata": {
                "description": "Public IP name"
            }
        },
        "vnetName": {
            "type": "string",
            "defaultValue": "myVNET",
            "metadata": {
                "description": "Virtual network name"
            }
        },
        "vmSize": {
            "type": "string",
            "defaultValue": "Standard_A1",
            "metadata": {
                "description": "Size of the VM"
            }
        }
    },
    "variables": {
        "storageAccountType": "Standard_LRS",
        "vmStorageAccountContainerName": "vhds",
        "availabilitySetName": "myAvSet",
        "addressPrefix": "10.0.0.0/16",
        "subnetName": "Subnet-1",
        "subnetPrefix": "10.0.0.0/24",
        "publicIPAddressType": "Dynamic",
        "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',parameters('vnetName'))]",
        "subnetRef": "[concat(variables('vnetID'),'/subnets/',variables ('subnetName'))]",
        "publicIPAddressID": "[resourceId('Microsoft.Network/publicIPAddresses',parameters('publicIPAddressName'))]",
        "lbID": "[resourceId('Microsoft.Network/loadBalancers',parameters('lbName'))]",
        "numberOfInstances": 2,
        "nicId1": "[resourceId('Microsoft.Network/networkInterfaces',concat(parameters('nicNamePrefix'), 0))]",
        "nicId2": "[resourceId('Microsoft.Network/networkInterfaces',concat(parameters('nicNamePrefix'), 1))]",
        "frontEndIPConfigID": "[concat(variables('lbID'),'/frontendIPConfigurations/LBFE')]",
        "backEndIPConfigID1": "[concat(variables('nicId1'),'/ipConfigurations/ipconfig1')]",
        "backEndIPConfigID2": "[concat(variables('nicId2'),'/ipConfigurations/ipconfig1')]",
        "sourceImageName": "[concat('/', subscription().subscriptionId,'/services/images/',parameters('vmSourceImageName'))]",
        "lbPoolID": "[concat(variables('lbID'),'/backendAddressPools/LBBE')]",
        "lbProbeID": "[concat(variables('lbID'),'/probes/tcpProbe')]"
    },
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[parameters('storageAccountName')]",
            "apiVersion": "2015-05-01-preview",
            "location": "[parameters('location')]",
            "properties": {
                "accountType": "[variables('storageAccountType')]"
            }
        },
        {
            "type": "Microsoft.Compute/availabilitySets",
            "name": "[variables('availabilitySetName')]",
            "apiVersion": "2015-05-01-preview",
            "location": "[parameters('location')]",
            "properties": { }
        },
        {
            "apiVersion": "2015-05-01-preview",
            "type": "Microsoft.Network/publicIPAddresses",
            "name": "[parameters('publicIPAddressName')]",
            "location": "[parameters('location')]",
            "properties": {
                "publicIPAllocationMethod": "[variables('publicIPAddressType')]",
                "dnsSettings": {
                    "domainNameLabel": "[parameters('dnsNameforLBIP')]"
                }
            }
        },
        {
            "apiVersion": "2015-05-01-preview",
            "type": "Microsoft.Network/virtualNetworks",
            "name": "[parameters('vnetName')]",
            "location": "[parameters('location')]",
            "properties": {
                "addressSpace": {
                    "addressPrefixes": [
                        "[variables('addressPrefix')]"
                    ]
                },
                "subnets": [
                    {
                        "name": "[variables('subnetName')]",
                        "properties": {
                            "addressPrefix": "[variables('subnetPrefix')]"
                        }
                    }
                ]
            }
        },
        {
            "apiVersion": "2015-05-01-preview",
            "type": "Microsoft.Network/networkInterfaces",
            "name": "[concat(parameters('nicNamePrefix'), copyindex())]",
            "location": "[parameters('location')]",
            "copy": {
                "name": "nicLoop",
                "count": "[variables('numberOfInstances')]"
            },
            "dependsOn": [
                "[concat('Microsoft.Network/virtualNetworks/', parameters('vnetName'))]"
            ],
            "properties": {
                "ipConfigurations": [
                    {
                        "name": "ipconfig1",
                        "properties": {
                            "privateIPAllocationMethod": "Dynamic",
                            "subnet": {
                                "id": "[variables('subnetRef')]"
                            }
                        },
                        "loadBalancerBackendAddressPools": [
                            {
                                "id": "[concat('Microsoft.Network/loadBalancers/',parameters('lbName'),'/backendAddressPools/LBBE')]"
                            }
                        ],
                        "loadBalancerInboundNatRules": [
                            {
                                "id": "[concat('Microsoft.Network/loadBalancers/',parameters('lbName'),'/inboundNatRule/RDP-VM', copyindex())]"
                            }
                        ]


                    }
                ]

            }
        },
        {
            "apiVersion": "2015-05-01-preview",
            "name": "[parameters('lbName')]",
            "type": "Microsoft.Network/loadBalancers",
            "location": "[parameters('location')]",
            "dependsOn": [
                "nicLoop",
                "[concat('Microsoft.Network/publicIPAddresses/', parameters('publicIPAddressName'))]"
            ],
            "properties": {
                "frontendIPConfigurations": [
                    {
                        "name": "LBFE",
                        "properties": {
                            "publicIPAddress": {
                                "id": "[variables('publicIPAddressID')]"
                            }
                        }
                    }
                ],
                "backendAddressPools": [
                    {
                        "name": "LBBE"

                    }
                ],
                "inboundNatRules": [
                    {
                        "name": "RDP-VM1",
                        "properties": {
                            "frontendIPConfiguration":
                                {
                                    "id": "[variables('frontEndIPConfigID')]"
                                },
                            "protocol": "tcp",
                            "frontendPort": 50001,
                            "backendPort": 3389,
                            "enableFloatingIP": false
                        }
                    },
                    {
                        "name": "RDP-VM2",
                        "properties": {
                            "frontendIPConfiguration":
                                {
                                    "id": "[variables('frontEndIPConfigID')]"
                                },
                            "protocol": "tcp",
                            "frontendPort": 50002,
                            "backendPort": 3389,
                            "enableFloatingIP": false
                        }
                    }
                ],
                "loadBalancingRules": [
                    {
                        "name": "LBRule",
                        "properties": {
                            "frontendIPConfiguration": {
                                "id": "[variables('frontEndIPConfigID')]"
                            },
                            "backendAddressPool": {
                                "id": "[variables('lbPoolID')]"
                            },
                            "protocol": "tcp",
                            "frontendPort": 80,
                            "backendPort": 80,
                            "enableFloatingIP": false,
                            "idleTimeoutInMinutes": 5,
                            "probe": {
                                "id": "[variables('lbProbeID')]"
                            }
                        }
                    }
                ],
                "probes": [
                    {
                        "name": "tcpProbe",
                        "properties": {
                            "protocol": "tcp",
                            "port": 80,
                            "intervalInSeconds": "5",
                            "numberOfProbes": "2"
                        }
                    }
                ]
            }
        },
        {
            "apiVersion": "2015-05-01-preview",
            "type": "Microsoft.Compute/virtualMachines",
            "name": "[concat(parameters('vmNamePrefix'), copyindex())]",
            "copy": {
                "name": "virtualMachineLoop",
                "count": "[variables('numberOfInstances')]"
            },
            "location": "[parameters('location')]",
            "dependsOn": [
                "[concat('Microsoft.Storage/storageAccounts/', parameters('storageAccountName'))]",
                "[concat('Microsoft.Network/networkInterfaces/', parameters('nicNamePrefix'), copyindex())]",
                "[concat('Microsoft.Compute/availabilitySets/', variables('availabilitySetName'))]"
            ],
            "properties": {
                "availabilitySet": {
                    "id": "[resourceId('Microsoft.Compute/availabilitySets',variables('availabilitySetName'))]"
                },
                "hardwareProfile": {
                    "vmSize": "[parameters('vmSize')]"
                },
                "osProfile": {
                    "computername": "[concat(parameters('vmNamePrefix'), copyIndex())]",
                    "adminUsername": "[parameters('adminUsername')]",
                    "adminPassword": "[parameters('adminPassword')]"
                },
                "storageProfile": {
                    "sourceImage": {
                        "id": "[variables('sourceImageName')]"
                    },
                    "destinationVhdsContainer": "[concat('http://',parameters('storageAccountName'),'.blob.core.windows.net/',variables('vmStorageAccountContainerName'),'/')]"
                },
                "networkProfile": {
                    "networkInterfaces": [
                        {
                            "id": "[resourceId('Microsoft.Network/networkInterfaces',concat(parameters('nicNamePrefix'),copyindex()))]"
                        }
                    ]
                }
            }
        }
    ]
}
```

### <a name="step-2-create-the-deployment-by-using-the-template"></a>2단계: 템플릿을 사용하여 배포 만들기
`azure group create <location>`을 사용하여 템플릿에 대한 리소스 그룹을 만듭니다. 그런 다음 `azure group deployment create` 을 사용하여 리소스 그룹, 배포 이름을 전달하고 기본값이 없는 템플릿의 매개 변수에 대한 프롬프트에 응답하여 해당 리소스 그룹에 대한 배포를 만듭니다.

```azurecli
azure group create lbgroup westus
info:    Executing command group create
+ Getting resource group lbgroup
+ Creating resource group lbgroup
info:    Created resource group lbgroup
data:    Id:                  /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/lbgroup
data:    Name:                lbgroup
data:    Location:            westus
data:    Provisioning State:  Succeeded
data:    Tags:
data:
info:    group create command OK
```

이제 `azure group deployment create` 명령 및 `--template-uri` 옵션을 사용하여 템플릿을 배포합니다. 아래와 같이 메시지가 표시되면 매개 변수 값을 입력할 수 있습니다.

```azurecli
azure group deployment create \
> --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-2-vms-loadbalancer-lbrules/azuredeploy.json \
> lbgroup \
> newdeployment
info:    Executing command group deployment create
info:    Supply values for the following parameters
location: westus
newStorageAccountName: storagename
adminUsername: ops
adminPassword: password
dnsNameforLBIP: lbdomainname
+ Initializing template configurations and parameters
+ Creating a deployment
info:    Created template deployment "newdeployment"
+ Registering providers
info:    Registering provider microsoft.storage
info:    Registering provider microsoft.compute
info:    Registering provider microsoft.network
+ Waiting for deployment to complete
data:    DeploymentName     : newdeployment
data:    ResourceGroupName  : lbgroup
data:    ProvisioningState  : Succeeded
data:    Timestamp          : 2015-04-28T20:58:40.1678876Z
data:    Mode               : Incremental
data:    TemplateLink       : https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-2-vms-loadbalancer-lbrules/azuredeploy.json
data:    ContentVersion     : 1.0.0.0
data:    Name                   Type          Value
data:    ---------------------  ------------  ----------------------
data:    location               String        westus
data:    newStorageAccountName  String        storagename
data:    adminUsername          String        ops
data:    adminPassword          SecureString  undefined
data:    dnsNameforLBIP         String        lbdomainname
data:    backendPort            Int           3389
data:    vmNamePrefix           String        myVM
data:    imagePublisher         String        MicrosoftWindowsServer
data:    imageOffer             String        WindowsServer
data:    imageSKU               String        2012-R2-Datacenter
data:    lbName                 String        myLB
data:    nicNamePrefix          String        nic
data:    publicIPAddressName    String        myPublicIP
data:    vnetName               String        myVNET
data:    vmSize                 String        Standard_A1
info:    group deployment create command OK
```

이 템플릿은 Windows Server 이미지를 배포하지만 Linux 이미지로 간단하게 대체할 수 있습니다. 여러 swarm 관리자로 Docker Cluster를 만들려고 하시나요? [가능합니다](https://azure.microsoft.com/documentation/templates/docker-swarm-cluster/).

## <a id="remove-a-resource-group"></a>작업: 리소스 그룹 제거
리소스 그룹에 다시 배포할 수 있습니다. 그러나 사용을 마친 경우에는 `azure group delete <group name>`을 사용하여 삭제합니다.

```azurecli
azure group delete myResourceGroup
info:    Executing command group delete
Delete resource group myResourceGroup? [y/n] y
+ Deleting resource group myResourceGroup
info:    group delete command OK
```

## <a id="show-the-log-for-a-resource-group-deployment"></a>작업: 리소스 그룹 배포에 대한 로그 표시
템플릿을 만들거나 사용할 때 일반적인 작업입니다. 그룹에 대한 배포 로그를 표시하려면 `azure group log show <groupname>`을 호출합니다. 그러면 특정 작업이 수행되거나 수행되지 않은 이유를 이해하는 데 유용한 많은 정보가 표시됩니다. (배포 문제 해결에 대한 자세한 내용 및 문제에 대한 기타 정보는 [Azure Resource Manager를 사용한 일반적인 Azure 배포 오류 해결](../articles/azure-resource-manager/resource-manager-common-deployment-errors.md)을 참조하세요.)

예를 들어 특정 오류를 대상으로 지정하려면 **jq** 와 같은 도구를 사용하여 해결해야 하는 개별 오류와 같은 항목을 좀더 정확하게 쿼리할 수 있습니다. 다음 예제에서는 **jq**를 사용하여 **lbgroup**에 대한 배포 로그를 구문 분석하고 오류를 찾습니다.

```azurecli
azure group log show lbgroup -l --json | jq '.[] | select(.status.value == "Failed") | .properties'
```
잘못되어 수정하거나 다시 시도할 항목을 매우 신속하게 검색할 수 있습니다. 다음과 같은 경우 템플릿에서 두 개의 VM을 동시에 만들고 .vhd에 잠금을 만들었습니다. 템플릿을 수정한 후 바로 배포가 성공했습니다.

```json
{
    "statusCode": "Conflict",
    "statusMessage": "{\"status\":\"Failed\",\"error\":{\"code\":\"ResourceDeploymentFailure\",\"message\":\"The resource operation completed with terminal provisioning state 'Failed'.\",\"details\":[{\"code\":\"AcquireDiskLeaseFailed\",\"message\":\"Failed to acquire lease while creating disk 'osdisk' using blob with URI http://storage.blob.core.windows.net/vhds/osdisk.vhd.\"}]}}"
}
```

## <a id="display-information-about-a-virtual-machine"></a>작업: 가상 컴퓨터에 대한 정보 표시
`azure vm show <groupname> <vmname>` 명령을 사용하여 리소스 그룹의 특정 VM에 대한 정보를 확인할 수 있습니다. 그룹에 VM이 둘 이상 있는 경우 `azure vm list <groupname>`을 사용하여 한 그룹의 VM을 먼저 나열해야 할 수 있습니다.

```azurecli
azure vm list zoo
info:    Executing command vm list
+ Getting virtual machines
data:    Name   ProvisioningState  Location  Size
data:    -----  -----------------  --------  -----------
data:    myVM0  Succeeded          westus    Standard_A1
data:    myVM1  Failed             westus    Standard_A1
```

그런 다음 myVM1을 조회합니다.

```azurecli
azure vm show zoo myVM1
info:    Executing command vm show
+ Looking up the VM "myVM1"
+ Looking up the NIC "nic1"
data:    Id                              :/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/zoo/providers/Microsoft.Compute/virtualMachines/myVM1
data:    ProvisioningState               :Failed
data:    Name                            :myVM1
data:    Location                        :westus
data:    Type                            :Microsoft.Compute/virtualMachines
data:
data:    Hardware Profile:
data:      Size                          :Standard_A1
data:
data:    Storage Profile:
data:      Image reference:
data:        Publisher                   :MicrosoftWindowsServer
data:        Offer                       :WindowsServer
data:        Sku                         :2012-R2-Datacenter
data:        Version                     :latest
data:
data:      OS Disk:
data:        OSType                      :Windows
data:        Name                        :osdisk
data:        Caching                     :ReadWrite
data:        CreateOption                :FromImage
data:        Vhd:
data:          Uri                       :http://zoostorageralph.blob.core.windows.net/vhds/osdisk.vhd
data:
data:    OS Profile:
data:      Computer Name                 :myVM1
data:      User Name                     :ops
data:      Windows Configuration:
data:        Provision VM Agent          :true
data:        Enable automatic updates    :true
data:
data:    Network Profile:
data:      Network Interfaces:
data:        Network Interface #1:
data:          Id                        :/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/zoo/providers/Microsoft.Network/networkInterfaces/nic1
data:          Primary                   :false
data:          Provisioning State        :Succeeded
data:          Name                      :nic1
data:          Location                  :westus
data:            Private IP alloc-method :Dynamic
data:            Private IP address      :10.0.0.5
data:
data:    AvailabilitySet:
data:      Id                            :/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/zoo/providers/Microsoft.Compute/availabilitySets/MYAVSET
info:    vm show command OK
```

> [!NOTE]
> 콘솔 명령의 출력을 프로그래밍 방식으로 저장하고 조작하려는 경우 **[jq](https://github.com/stedolan/jq)**, **[jsawk](https://github.com/micha/jsawk)** 또는 작업에 적합한 언어 라이브러리 같은 JSON 구문 분석 도구를 사용할 수 있습니다.
>
>

## <a id="log-on-to-a-linux-based-virtual-machine"></a>작업: Linux 기반 가상 컴퓨터에 로그온
일반적으로 Linux 컴퓨터는 SSH를 통해 연결됩니다. 자세한 내용은 [Azure에서 Linux와 함께 SSH를 사용하는 방법](../articles/virtual-machines/virtual-machines-linux-mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)을 참조하세요.

## <a id="stop-a-virtual-machine"></a>작업: VM 중지
다음 명령을 실행합니다.

```azurecli
azure vm stop <group name> <virtual machine name>
```

> [!IMPORTANT]
> 해당 vnet의 마지막 VM인 경우 이 매개 변수를 사용하여 vnet의 VIP(가상 IP)를 유지합니다. <br><br> `StayProvisioned` 매개 변수를 사용하는 경우에도 VM에 대한 요금이 청구됩니다.
>
>

## <a id="start-a-virtual-machine"></a>작업: VM 시작
다음 명령을 실행합니다.

```azurecli
azure vm start <group name> <virtual machine name>
```

## <a id="attach-a-data-disk"></a>작업: 데이터 디스크 연결
또한 새 디스크를 연결할지 데이터를 포함하는 디스크를 연결할지를 결정해야 합니다. 새 디스크의 경우 명령에서 .vhd 파일을 만들고 동일한 명령으로 디스크를 연결합니다.

새 디스크를 연결하려면 다음 명령을 실행합니다.

```azurecli
    azure vm disk attach-new <resource-group> <vm-name> <size-in-gb>
```

기존 데이터 디스크를 연결하려면 다음 명령을 실행합니다.

```azurecli
azure vm disk attach <resource-group> <vm-name> [vhd-url]
```

그런 다음 일반적으로 Linux에서처럼 디스크를 탑재해야 합니다.

## <a name="next-steps"></a>다음 단계
**arm** 모드의 Azure CLI 사용에 대한 더 많은 예제는 [Azure 리소스 관리자에 Mac, Linux 및 Windows용 Azure CLI 사용](../articles/xplat-cli-azure-resource-manager.md)을 참조하세요. Azure 리소스 및 해당 개념에 대한 자세한 내용은 [Azure Resource Manager 개요](../articles/azure-resource-manager/resource-group-overview.md)를 참조하세요.

사용할 수 있는 더 많은 템플릿은 [Azure 빠른 시작 템플릿](https://azure.microsoft.com/documentation/templates/) 및 [템플릿을 사용하는 응용 프로그램 프레임워크](../articles/virtual-machines/virtual-machines-linux-app-frameworks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)를 참조하세요.
