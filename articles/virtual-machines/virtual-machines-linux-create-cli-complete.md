---
title: "Azure CLI 2.0을 사용하여 Linux 환경 만들기 | Microsoft Docs"
description: "Azure CLI 2.0미리 보기)을 사용하여 저장소, Linux VM, 가상 네트워크 및 서브넷, 부하 분산 장치, NIC, 공용 IP, 네트워크 보안 그룹을 모두 처음부터 새로 만듭니다."
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 4ba4060b-ce95-4747-a735-1d7c68597a1a
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/8/2016
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 39ce158ae52b978b74161cdadb4b886a7ddbf87a
ms.openlocfilehash: a00936df023ddbb13f5765f2e78900a68cccdb88


---
# <a name="create-a-complete-linux-environment-by-using-the-azure-cli-20-preview"></a>Azure CLI 2.0(미리 보기)을 사용하여 완전한 Linux 환경 만들기
이 문서에서는 개발 및 간단한 계산에 유용한 부하 분산 장치와 한 쌍의 VM을 사용하여 간단한 네트워크를 빌드해 보겠습니다. 인터넷 어디에서나 안전하게 실행되는 두 개의 Linux VM에 연결할 수 있을 때까지 프로세스를 명령별로 진행합니다. 그 후에는 좀 더 복잡한 네트워크 및 환경으로 넘어갈 수 있습니다.

그 과정에서 Resource Manager 배포 모델이 제공하는 종속성 계층 구조와 강력한 기능을 이해할 수 있을 것입니다. 시스템이 빌드되는 방식을 이해하면 [Azure Resource Manager 템플릿](../azure-resource-manager/resource-group-authoring-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)을 사용하여 시스템을 훨씬 더 빠르게 다시 빌드할 수 있습니다. 또한 환경의 여러 부분이 서로 어떻게 연결되는지 파악하고 나면 이러한 환경 부분을 자동화하는 템플릿을 더 쉽게 만들 수 있습니다.

환경에는 다음이 포함됩니다.

* 가용성 집합에 포함된 두 VM
* 포트 80에서 부하 분산 규칙이 있는 부하 분산 장치
* 원치 않는 트래픽으로부터 VM을 보호하기 위한 NSG(네트워크 보안 그룹) 규칙

![기본 환경 개요](./media/virtual-machines-linux-create-cli-complete/environment_overview.png)

## <a name="cli-versions-to-complete-the-task"></a>태스크를 완료하기 위한 CLI 버전
다음 CLI 버전 중 하나를 사용하여 태스크를 완료할 수 있습니다.

- [Azure CLI 1.0](virtual-machines-linux-create-cli-complete-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) - 클래식 및 리소스 관리 배포 모델용 CLI
- [Azure CLI 2.0(미리 보기)](#quick-commands) - 리소스 관리 배포 모델용 차세대 CLI(이 문서)

## <a name="quick-commands"></a>빠른 명령
작업을 빠르게 완료해야 하는 경우 다음 섹션에서 Azure에 VM을 업로드하는 기본 명령에 대해 자세히 알아보세요. 각 단계에 대한 보다 자세한 내용 및 상황 설명은 [여기](#detailed-walkthrough)서부터 문서 끝까지 참조하세요.

다음 예제에서 매개 변수 이름을 고유한 값으로 바꿉니다. 예제 매개 변수 이름에 `myResourceGroup`, `mystorageaccount` 및 `myVM`이 포함됩니다.

이 사용자 지정 환경을 만들려면 최신 [Azure CLI 2.0(미리 보기)](/cli/azure/install-az-cli2)을 설치하고 [az login](/cli/azure/#login)을 사용하여 로그인해야 합니다.

먼저 [az group create](/cli/azure/group#create)을 사용하여 리소스 그룹을 만듭니다. 다음 예제에서는 `westeurope` 위치에 `myResourceGroup`이라는 리소스 그룹을 만듭니다.

```azurecli
az group create --name myResourceGroup --location westeurope
```

이 다음 단계는 옵션입니다. Azure CLI 2.0(미리 보기)를 사용하여 VM을 만들 때 기본 작업은 Azure Managed Disks를 사용하는 것입니다. Azure Managed Disks에 대한 자세한 내용은 [Azure Managed Disks 개요](../storage/storage-managed-disks-overview.md)를 참조하세요. 대신 관리되지 않는 디스크를 사용하려는 경우 [az storage account create](/cli/azure/storage/account#create)를 사용하여 저장소 계정을 만들어야 합니다. 다음 예제에서는 `mystorageaccount`라는 저장소 계정을 만듭니다. 저장소 계정 이름은 고유해야 하므로 자신만의 이름을 제공하세요.

```azurecli
az storage account create --resource-group myResourceGroup --location westeurope \
  --name mystorageaccount --kind Storage --sku Standard_LRS
```

[az network vnet create](/cli/azure/network/vnet#create)를 사용하여 가상 네트워크를 만듭니다. 다음 예제에서는 `myVnet`이라는 가상 네트워크와 `mySubnet`이라는 서브넷을 만듭니다.

```azurecli
az network vnet create --resource-group myResourceGroup --location westeurope --name myVnet \
  --address-prefix 192.168.0.0/16 --subnet-name mySubnet --subnet-prefix 192.168.1.0/24
```

[az network public-ip create](/cli/azure/network/public-ip#create)를 사용하여 공용 IP를 만듭니다. 다음 예제는 `mypublicdns`라는 DNS 이름으로 `myPublicIP`라는 공용 IP를 만듭니다. DNS 이름은 고유해야 하므로 자신만의 이름을 제공하세요.

```azurecli
az network public-ip create --resource-group myResourceGroup --location westeurope \
  --name myPublicIP --dns-name mypublicdns --allocation-method static --idle-timeout 4
```

[az network lb create](/cli/azure/network/lb#create)를 사용하여 부하 분산 장치를 만듭니다. 다음 예제를 참조하세요.

- `myLoadBalancer`라는 부하 분산 장치 집합을 만듭니다.
- 공용 IP `myPublicIP`를 연결합니다.
- `mySubnetPool`라는 프런트 엔드 IP 풀을 만듭니다.
- `myBackEndPool`라는 백 엔드 IP 풀을 만듭니다.

```azurecli
az network lb create --resource-group myResourceGroup --location westeurope \
  --name myLoadBalancer --public-ip-address myPublicIP \
  --frontend-ip-name myFrontEndPool --backend-pool-name myBackEndPool
```

[az network lb inbound-nat-rule create](/cli/azure/network/lb/inbound-nat-rule#create)를 사용하여 부하 분산 장치에 대한 SSH 인바운드 NAT(Network Address Translation) 규칙을 만듭니다. 다음 예제는 `myLoadBalancerRuleSSH1` 및 `myLoadBalancerRuleSSH2`라는 두 개의 부하 분산 장치를 만듭니다.

```azurecli
az network lb inbound-nat-rule create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myLoadBalancerRuleSSH1 --protocol tcp \
  --frontend-port 4222 --backend-port 22 --frontend-ip-name myFrontEndPool
az network lb inbound-nat-rule create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myLoadBalancerRuleSSH2 --protocol tcp \
  --frontend-port 4223 --backend-port 22 --frontend-ip-name myFrontEndPool
```

[az network lb probe create](/cli/azure/network/lb/probe#create)를 사용하여 부하 분산 장치 상태 프로브를 만듭니다. 다음 예제에는 `myHealthProbe`라는 TCP 프로브를 만듭니다.

```azurecli
az network lb probe create --resource-group myResourceGroup --lb-name myLoadBalancer \
  --name myHealthProbe --protocol tcp --port 80 --interval 15 --threshold 4
```

[az network lb rule create](/cli/azure/network/lb/rule#create)를 사용하여 부하 분산 장치에 대한 웹 인바운드 NAT 규칙을 만듭니다. 다음 예제는 `myLoadBalancerRuleWeb`이라는 부하 분산 장치 규칙을 만든 후 `myHealthProbe` 프로브에 연결합니다.

```azurecli
az network lb rule create --resource-group myResourceGroup --lb-name myLoadBalancer \
  --name myLoadBalancerRuleWeb --protocol tcp --frontend-port 80 --backend-port 80 \
  --frontend-ip-name myFrontEndPool --backend-pool-name myBackEndPool \
  --probe-name myHealthProbe
```

[az network lb show](/cli/azure/network/lb#show)를 사용하여 부하 분산 장치, IP 풀, NAT 규칙을 확인합니다.

```azurecli
az network lb show --resource-group myResourceGroup --name myLoadBalancer
```

[az network nsg create](/cli/azure/network/nsg#create)를 사용하여 네트워크 보안 그룹을 만듭니다. 다음 예제에서는 `myNetworkSecurityGroup`이라는 네트워크 보안 그룹을 만듭니다.

```azurecli
az network nsg create --resource-group myResourceGroup --location westeurope \
  --name myNetworkSecurityGroup
```

[az network nsg rule create](/cli/azure/network/nsg/rule#create)를 사용하여 네트워크 보안 그룹에 대해 인바운드 규칙을 두 개 추가합니다. 다음 예제는 `myNetworkSecurityGroupRuleSSH` 및 `myNetworkSecurityGroupRuleHTTP`라는 두 개의 규칙을 만듭니다.

```azurecli
az network nsg rule create --resource-group myResourceGroup \
  --nsg-name myNetworkSecurityGroup --name myNetworkSecurityGroupRuleSSH \
  --protocol tcp --direction inbound --priority 1000 --source-address-prefix '*' \
  --source-port-range '*' --destination-address-prefix '*' --destination-port-range 22 \
  --access allow
az network nsg rule create --resource-group myResourceGroup \
  --nsg-name myNetworkSecurityGroup --name myNetworkSecurityGroupRuleHTTP \
  --protocol tcp --direction inbound --priority 1001 --source-address-prefix '*' \
  --source-port-range '*' --destination-address-prefix '*' --destination-port-range 80 \
  --access allow
```

[az network nic create](/cli/azure/network/nic#create)를 사용하여 첫 번째 NIC(네트워크 인터페이스 카드)를 만듭니다. 다음 예제에서는 `myNic1`이라는 NIC를 만들고 부하 분산 장치 `myLoadBalancer` 및 해당 풀에 연결하고 `myNetworkSecurityGroup`에도 연결합니다.

```azurecli
az network nic create --resource-group myResourceGroup --location westeurope --name myNic1 \
  --vnet-name myVnet --subnet mySubnet --network-security-group myNetworkSecurityGroup \
  --lb-name myLoadBalancer --lb-address-pools myBackEndPool \
  --lb-inbound-nat-rules myLoadBalancerRuleSSH1
```

다시 **az network nic create**를 사용하여 두 번째 NIC를 만듭니다. 다음 예제는 `myNic2`라는 NIC를 만듭니다.

```azurecli
az network nic create --resource-group myResourceGroup --location westeurope --name myNic2 \
  --vnet-name myVnet --subnet mySubnet --network-security-group myNetworkSecurityGroup \
  --lb-name myLoadBalancer --lb-address-pools myBackEndPool \
  --lb-inbound-nat-rules myLoadBalancerRuleSSH2
```

[az vm availability-set create](/cli/azure/vm/availability-set#create)를 사용하여 가용성 집합을 만듭니다. 다음 예제는 `myAvailabilitySet`라는 가용성 집합을 만듭니다.

```azurecli
az vm availability-set create --resource-group myResourceGroup --location westeurope \
  --name myAvailabilitySet
```

[az vm create](/cli/azure/vm#create)를 사용하여 첫 번째 Linux VM을 만듭니다. 다음 예제는 Azure Managed Disks를 사용하여 `myVM1`이라는 VM을 만듭니다. 관리되지 않는 디스크를 사용하려는 경우 아래의 추가 정보를 참조하세요.

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM1 \
    --location westeurope \
    --availability-set myAvailabilitySet \
    --nics myNic1 \
    --vnet myVnet \
    --subnet-name mySubnet \
    --nsg myNetworkSecurityGroup \
    --image UbuntuLTS \
    --ssh-key-value ~/.ssh/id_rsa.pub \
    --admin-username azureuser
```

Azure Managed Disks를 사용하는 경우 이 단계를 건너뜁니다. 관리되지 않는 디스크를 사용하려 하고 이전 단계에서 저장소 계정을 만든 경우 일부 추가 매개 변수를 진행 명령에 추가해야 합니다. 다음과 같은 추가 매개 변수를 진행 명령에 추가하여 `mystorageaccount`라는 저장소 계정에 관리되지 않는 디스크를 만듭니다. 

```azurecli
  --use-unmanaged-disk \
  --storage-account mystorageaccount
```

다시 **az vm create**를 사용하여 두 번째 Linux VM을 만듭니다. 다음 예제는 `myVM2`이라는 VM을 만듭니다.

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM2 \
    --location westeurope \
    --availability-set myAvailabilitySet \
    --nics myNic2 \
    --vnet myVnet \
    --subnet-name mySubnet \
    --nsg myNetworkSecurityGroup \
    --image UbuntuLTS \
    --ssh-key-value ~/.ssh/id_rsa.pub \
    --admin-username azureuser
```

다시 기본 Azure Managed Disks를 사용하지 않는 경우 다음과 같은 추가 매개 변수를 진행 명령에 추가하여 `mystorageaccount`라는 저장소 계정에 관리되지 않는 디스크를 만듭니다.

```azurecli
  --use-unmanaged-disk \
  --storage-account mystorageaccount
``` 

[az vm show](/cli/azure/vm#show)를 사용하여 모든 항목이 제대로 빌드되었는지 확인합니다.

```azurecli
az vm show --resource-group myResourceGroup --name myVM1
az vm show --resource-group myResourceGroup --name myVM2
```

[az group export](/cli/azure/group#export)를 사용하여 새 환경을 템플릿으로 내보내 새 인스턴스를 빠르게 다시 만듭니다.

```azurecli
az group export --name myResourceGroup > myResourceGroup.json
```

## <a name="detailed-walkthrough"></a>자세한 연습
다음에 나오는 자세한 단계는 작업 환경을 빌드할 때 각 명령이 수행하는 작업을 설명합니다. 이러한 개념은 개발 또는 프로덕션에 맞는 고유한 사용자 지정 환경을 빌드할 때 도움이 됩니다.

최신 [Azure CLI 2.0(미리 보기)](/cli/azure/install-az-cli2)을 설치했고 [az login](/cli/azure/#login)을 사용하여 Azure 계정에 로그인했는지 확인합니다.

다음 예제에서 매개 변수 이름을 고유한 값으로 바꿉니다. 예제 매개 변수 이름에 `myResourceGroup`, `mystorageaccount` 및 `myVM`가 포함됩니다.

## <a name="create-resource-groups-and-choose-deployment-locations"></a>리소스 그룹 만들기 및 배포 위치 선택
Azure 리소스 그룹은 리소스 배포를 논리적으로 관리할 수 있는 구성 정보 및 메타데이터를 포함하는 논리적 배포 엔터티입니다. [az group create](/cli/azure/group#create)을 사용하여 리소스 그룹을 만듭니다. 다음 예제에서는 `westeurope` 위치에 `myResourceGroup`이라는 리소스 그룹을 만듭니다.

```azurecli
az group create --name myResourceGroup --location westeurope
```

기본적으로 출력은 JSON(JavaScript Object Notation)으로 제공됩니다. 예를 들어 목록 또는 테이블로 출력하려면 [az configure --output](/cli/azure/#configure)을 사용합니다. 출력 형식을&1;번 변경하기 위해 명령에 `--output`을 추가할 수도 있습니다. 다음 예제에서는 **az group create** 명령에서 JSON 출력을 표시합니다.

```json                       
{
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup",
  "location": "westeurope",
  "name": "myResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

## <a name="create-a-storage-account"></a>저장소 계정 만들기
이 다음 단계는 옵션입니다. Azure CLI 2.0(미리 보기)를 사용하여 VM을 만들 때 기본 작업은 Azure Managed Disks를 사용하는 것입니다. 이들 디스크는 Azure 플랫폼을 통해 처리되며 디스크를 저장할 위치나 준비가 필요하지 않습니다. Azure Managed Disks에 대한 자세한 내용은 [Azure Managed Disks 개요](../storage/storage-managed-disks-overview.md)를 참조하세요. Azure Managed Disks를 사용하려는 경우 [가상 네트워크 및 서브넷 만들기](#create-a-virtual-network-and-subnet)로 건너뜁니다. 

관리되지 않는 디스크를 사용하려는 경우 VM 디스크 및 추가하려는 추가 데이터 디스크에 대한 저장소 계정을 만들어야 합니다.

여기서는 [az storage account create](/cli/azure/storage/account#create)를 사용하고 계정 위치, 계정을 제어할 리소스 그룹, 원하는 저장소 지원 형식을 전달합니다. 다음 예제에서는 `mystorageaccount`라는 저장소 계정을 만듭니다.

```azurecli
az storage account create --resource-group myResourceGroup --location westeurope \
  --name mystorageaccount --kind Storage --sku Standard_LRS
```

출력

```json
{
  "accessTier": null,
  "creationTime": "2016-12-07T17:59:50.090092+00:00",
  "customDomain": null,
  "encryption": null,
  "id": "/subscriptions/guid/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
  "kind": "Storage",
  "lastGeoFailoverTime": null,
  "location": "westeurope",
  "name": "mystorageaccount",
  "primaryEndpoints": {
    "blob": "https://mystorageaccount.blob.core.windows.net/",
    "file": "https://mystorageaccount.file.core.windows.net/",
    "queue": "https://mystorageaccount.queue.core.windows.net/",
    "table": "https://mystorageaccount.table.core.windows.net/"
  },
  "primaryLocation": "westeurope",
  "provisioningState": "Succeeded",
  "resourceGroup": "myresourcegroup",
  "secondaryEndpoints": null,
  "secondaryLocation": null,
  "sku": {
    "name": "Standard_LRS",
    "tier": "Standard"
  },
  "statusOfPrimary": "Available",
  "statusOfSecondary": null,
  "tags": {},
  "type": "Microsoft.Storage/storageAccounts"
}
```

CLI를 사용하여 저장소 계정을 조사하려면 먼저 계정 이름과 키를 설정해야 합니다. [az storage account show-connection-string](/cli/azure/storage/account#show-connection-string)을 사용합니다. 다음 예제의 저장소 계정 이름을 원하는 이름으로 바꿉니다.

```bash
export AZURE_STORAGE_CONNECTION_STRING="$(az storage account show-connection-string --resource-group myResourceGroup --name mystorageaccount --query connectionString)"
```

그런 후 [az storage container list](/cli/azure/storage/container#list)를 사용하여 저장소 정보를 볼 수 있습니다.

```azurecli
az storage container list
```

출력

```azurecli
[
  {
    "metadata": null,
    "name": "vhds",
    "properties": {
      "etag": "\"0x8D41F912D472F94\"",
      "lastModified": "2016-12-08T17:39:35+00:00",
      "lease": {
        "duration": null,
        "state": null,
        "status": null
      },
      "leaseDuration": "infinite",
      "leaseState": "leased",
      "leaseStatus": "locked"
    }
  }
]
```

## <a name="create-a-virtual-network-and-subnet"></a>가상 네트워크 및 서브넷 만들기
다음에는 Azure에서 실행되는 가상 네트워크와 VM을 만들 수 있는 서브넷을 만들어야 합니다. 다음 예제에서는 [az network vnet create](/cli/azure/network/vnet#create)를 사용하여 `192.168.0.0/16` 주소 접두사를 갖는 `myVnet`이라는 가상 네트워크와 서브넷 주소 접두사 `192.168.1.0/24`를 갖는 `mySubnet`이라는 서브넷을 만듭니다.

```azurecli
az network vnet create --resource-group myResourceGroup --location westeurope \
  --name myVnet --address-prefix 192.168.0.0/16 \
  --subnet-name mySubnet --subnet-prefix 192.168.1.0/24
```

출력에는 가상 네트워크 내에 논리적으로 만들어진 서브넷이 표시됩니다.

```json
{
  "addressSpace": {
    "addressPrefixes": [
      "192.168.0.0/16"
    ]
  },
  "dhcpOptions": {
    "dnsServers": []
  },
  "etag": "W/\"e95496fc-f417-426e-a4d8-c9e4d27fc2ee\"",
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet",
  "location": "westeurope",
  "name": "myVnet",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "resourceGuid": "ed62fd03-e9de-430b-84df-8a3b87cacdbb",
  "subnets": [
    {
      "addressPrefix": "192.168.1.0/24",
      "etag": "W/\"e95496fc-f417-426e-a4d8-c9e4d27fc2ee\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet",
      "ipConfigurations": null,
      "name": "mySubnet",
      "networkSecurityGroup": null,
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "resourceNavigationLinks": null,
      "routeTable": null
    }
  ],
  "tags": {},
  "type": "Microsoft.Network/virtualNetworks",
  "virtualNetworkPeerings": null
}
```


## <a name="create-a-public-ip-address"></a>공용 IP 주소 만들기
이제 부하 분산 장치에 할당할 PIP(공용 IP 주소)를 만들어 보겠습니다. 이 주소가 있으면 [az network public-ip create](/cli/azure/network/public-ip#create) 명령을 사용하여 인터넷에서 VM에 연결할 수 있습니다. 기본 주소는 동적이기 때문에 `--domain-name-label` 옵션을 사용하여 **cloudapp.azure.com** 도메인에 명명된 DNS 항목을 만듭니다. 다음 예제는 `mypublicdns`라는 DNS 이름으로 `myPublicIP`라는 공용 IP를 만듭니다. DNS 이름은 고유해야 하므로 자신만의 DNS 이름을 제공하세요.

```azurecli
az network public-ip create --resource-group myResourceGroup --location westeurope \
  --name myPublicIP --dns-name mypublicdns --allocation-method static --idle-timeout 4
```

출력

```json
{
  "publicIp": {
    "dnsSettings": {
      "domainNameLabel": "mypublicdns",
      "fqdn": "mypublicdns.westeurope.cloudapp.azure.com",
      "reverseFqdn": ""
    },
    "idleTimeoutInMinutes": 4,
    "ipAddress": "52.174.48.109",
    "provisioningState": "Succeeded",
    "publicIPAllocationMethod": "Static",
    "resourceGuid": "78218510-ea54-42d7-b2c2-44773fc14af5"
  }
}
```

공용 IP 주소 리소스가 논리적으로 할당되었지만 아직 특정 주소가 할당되지는 않았습니다. IP 주소를 획득하려면 아직 만들지 않은 부하 분산 장치가 필요합니다.

## <a name="create-a-load-balancer-and-ip-pools"></a>부하 분산 장치 및 IP 풀 만들기
부하 분산 장치를 만들면 여러 VM에 트래픽을 분산할 수 있습니다. 또한 유지 보수 중 또는 부하가 많을 경우 사용자 요청에 대응하는 여러 VM을 실행하여 응용 프로그램에 중복성을 제공합니다. 다음 예제에서는 [az network lb create](/cli/azure/network/lb#create)를 사용하여 `myLoadBalancer`이라는 부하 분산 장치, `myFrontEndPool`이라는 프런트 엔드 IP 풀을 만든 후 `myPublicIP` 리소스를 연결합니다.

```azurecli
az network lb create --resource-group myResourceGroup --location westeurope \
  --name myLoadBalancer --public-ip-address myPublicIP --frontend-ip-name myFrontEndPool
```

출력

```json
{
  "loadBalancer": {
    "backendAddressPools": [
      {
        "etag": "W/\"7a05df7a-5ee2-4581-b411-4b6f048ab291\"",
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myLoadBalancerbepool",
        "name": "myLoadBalancerbepool",
        "properties": {
          "provisioningState": "Succeeded"
        },
        "resourceGroup": "myResourceGroup"
      }
    ],
    "frontendIPConfigurations": [
      {
        "etag": "W/\"7a05df7a-5ee2-4581-b411-4b6f048ab291\"",
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/frontendIPConfigurations/myFrontEndPool",
        "name": "myFrontEndPool",
        "properties": {
          "privateIPAllocationMethod": "Dynamic",
          "provisioningState": "Succeeded",
          "publicIPAddress": {
            "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP",
            "resourceGroup": "myResourceGroup"
          }
        },
        "resourceGroup": "myResourceGroup"
      }
    ],
    "inboundNatPools": [],
    "inboundNatRules": [],
    "loadBalancingRules": [],
    "outboundNatRules": [],
    "probes": [],
    "provisioningState": "Succeeded",
    "resourceGuid": "f4879d84-5ae8-4e06-8b9b-1419baa875d9"
  }
}
```

앞에서 만든 `myPublicIP`를 전달하기 위해 `--public-ip-address` 스위치를 어떻게 사용했는지 확인하세요. 부하 분산 장치에 공용 IP 주소를 할당하면 인터넷에서 VM에 연결할 수 있습니다.

VM을 연결할 위치로 백 엔드 풀을 사용합니다. 이런 방식으로 트래픽은 부하 분산 장치를 통해 VM에 전달될 수 있습니다. [az network lb address-pool create](/cli/azure/network/lb/address-pool#create)를 사용하여 백 엔드 트래픽용 IP 풀을 만들어 보겠습니다. 다음 예제는 `myBackEndPool`이라는 백 엔드 풀을 만듭니다.

```azurecli
az network lb address-pool create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myBackEndPool
```

출력 일부:

```json
  "backendAddressPools": [
    {
      "backendIpConfigurations": null,
      "etag": "W/\"a61814bc-ce4c-4fb2-9d6a-5b1949078345\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myLoadBalancerbepool",
      "loadBalancingRules": null,
      "name": "myLoadBalancerbepool",
      "outboundNatRule": null,
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup"
    },
    {
      "backendIpConfigurations": null,
      "etag": "W/\"a61814bc-ce4c-4fb2-9d6a-5b1949078345\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool",
      "loadBalancingRules": null,
      "name": "myBackEndPool",
      "outboundNatRule": null,
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup"
    }
  ],
  "etag": "W/\"a61814bc-ce4c-4fb2-9d6a-5b1949078345\"",
```


## <a name="create-load-balancer-nat-rules"></a>부하 분산 장치 NAT 규칙 만들기
부하 분산 장치에 트래픽을 통과시키려면 인바운드 또는 아웃바운드 동작을 지정하는 NAT(Network Address Translation) 규칙을 만들어야 합니다. 사용할 프로토콜을 지정한 다음 외부 포트를 내부 포트로 원하는 대로 매핑할 수 있습니다. 이 환경에서는 [az network lb inbound-nat-rule create](/cli/azure/network/lb/inbound-nat-rule#create)를 사용하여 SSH가 이 부하 분산 장치를 통해 VM에 연결하도록 하는 몇 가지 규칙을 만들겠습니다. TCP 포트 4222와 4223이 나중에 만들 VM의 TCP 포트 22로 연결되도록 설정하겠습니다. 다음 예제는 TCP 포트 4222를 포트 22로 매핑하기 위해 `myLoadBalancerRuleSSH1`이라는 규칙을 만듭니다.

```azurecli
az network lb inbound-nat-rule create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myLoadBalancerRuleSSH1 --protocol tcp \
  --frontend-port 4222 --backend-port 22 --frontend-ip-name myFrontEndPool
```

출력:

```json
{
  "backendIpConfiguration": null,
  "backendPort": 22,
  "enableFloatingIp": false,
  "etag": "W/\"72843cf8-b5fb-4655-9ac8-9cbbbbf1205a\"",
  "frontendIpConfiguration": {
    "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/frontendIPConfigurations/myFrontEndPool",
    "resourceGroup": "myResourceGroup"
  },
  "frontendPort": 4222,
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1",
  "idleTimeoutInMinutes": 4,
  "name": "myLoadBalancerRuleSSH1",
  "protocol": "Tcp",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup"
}
```

SSH의 두 번째 NAT 규칙에 대해 같은 절차를 반복합니다. 다음 예제는 TCP 포트 4223을 포트 22로 매핑하기 위해 `myLoadBalancerRuleSSH2`라는 규칙을 만듭니다.

```azurecli
az network lb inbound-nat-rule create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myLoadBalancerRuleSSH2 --protocol tcp \
  --frontend-port 4223 --backend-port 22 --frontend-ip-name myFrontEndPool
```

## <a name="create-a-load-balancer-health-probe"></a>부하 분산 장치 상태 프로브 만들기
상태 프로브는 부하 분산 장치에 설정된 VM을 주기적으로 검사하여 VM이 정의된 대로 작동하며 요청에 응답하는지 확인합니다. 그렇지 않을 경우 사용자가 오류가 발생한 VM으로 연결되지 않도록 작동을 중단합니다. 상태 프로브에 대한 사용자 지정 검사, 간격, 시간 제한 값을 정의할 수 있습니다. 상태 프로브에 대한 자세한 내용은 [부하 분산 장치 프로브](../load-balancer/load-balancer-custom-probe-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)를 참조하세요. 다음 예제에서는 [az network lb probe create](/cli/azure/network/lb/probe#create)를 사용하여 `myHealthProbe`라는 TCP 상태 프로브를 만듭니다.

```azurecli
az network lb probe create --resource-group myResourceGroup --lb-name myLoadBalancer \
  --name myHealthProbe --protocol tcp --port 80 --interval 15 --threshold 4
```

출력

```json
{
  "etag": "W/\"757018f6-b70a-4651-b717-48b511d82ba0\"",
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/probes/myHealthProbe",
  "intervalInSeconds": 15,
  "loadBalancingRules": null,
  "name": "myHealthProbe",
  "numberOfProbes": 4,
  "port": 80,
  "protocol": "Tcp",
  "provisioningState": "Succeeded",
  "requestPath": null,
  "resourceGroup": "myResourceGroup"
}
```

여기서는 상태 검사 간격으로 15초를 지정했습니다. 부하 분산 장치에서 호스트가 더 이상 작동하지 않는 것으로 판단할 때까지 최대&4;개의 프로브(1분)를 놓칠 수 있습니다.

또한 웹 트래픽을 위해 TCP 포트 80에 대해 NAT 규칙을 만들고 IP 풀에 규칙을 연결하겠습니다. VM에 규칙을 개별적으로 연결하는 대신, IP 풀에 규칙을 연결하는 경우 IP 풀에서 VM을 추가 또는 제거할 수 있습니다. 부하 분산 장치가 자동으로 트래픽의 흐름을 조정합니다. 다음 예제에서는 [az network lb rule create](/cli/azure/network/lb/rule#create)를 통해 `myLoadBalancerRuleWeb`이라는 규칙을 만들어 TCP 포트 80을 포트 80에 매핑하고 `myHealthProbe`라는 상태 프로브를 연결합니다.

```azurecli
az network lb rule create --resource-group myResourceGroup --lb-name myLoadBalancer \
  --name myLoadBalancerRuleWeb --protocol tcp --frontend-port 80 --backend-port 80 \
  --frontend-ip-name myFrontEndPool --backend-pool-name myBackEndPool \
  --probe-name myHealthProbe
```

출력

```json
{
  "backendAddressPool": {
    "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool",
    "resourceGroup": "myResourceGroup"
  },
  "backendPort": 80,
  "enableFloatingIp": false,
  "etag": "W/\"f0d77680-bf42-4d11-bfab-5d2c541bee56\"",
  "frontendIpConfiguration": {
    "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/frontendIPConfigurations/myFrontEndPool",
    "resourceGroup": "myResourceGroup"
  },
  "frontendPort": 80,
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/loadBalancingRules/myLoadBalancerRuleWeb",
  "idleTimeoutInMinutes": 4,
  "loadDistribution": "Default",
  "name": "myLoadBalancerRuleWeb",
  "probe": {
    "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/probes/myHealthProbe",
    "resourceGroup": "myResourceGroup"
  },
  "protocol": "Tcp",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup"
}
```

## <a name="verify-the-load-balancer"></a>부하 분산 장치 확인
이제 부하 분산 장치 구성이 완료되었습니다. 다음과 같은 단계가 수행되었습니다.

1. 부하 분산 장치를 만들었습니다.
2. 프런트 엔드 IP 풀을 만들고 공용 IP를 할당했습니다.
3. VM을 연결할 수 있는 백 엔드 IP 풀을 만들었습니다.
4. 관리를 위해 SSH가 VM에 연결할 수 있는 NAT 규칙을 만들고 웹앱이 TCP 포트 80을 사용할 수 있도록 하는 규칙을 만들었습니다.
5. VM을 주기적으로 확인하기 위한 상태 프로브를 추가했습니다. 이 상태 프로브는 사용자가 더 이상 작동하지 않거나 콘텐츠를 제공하지 않는 VM에 액세스하는 일이 없게 합니다.

이제 [az network lb show](/cli/azure/network/lb#show)를 사용하여 부하 분산 장치가 어떻게 표시되는지 살펴보겠습니다.

```azurecli
az network lb show --resource-group myResourceGroup --name myLoadBalancer
```

출력

```json
{
  "backendAddressPools": [
    {
      "backendIpConfigurations": null,
      "etag": "W/\"a0e313a1-6de1-48be-aeb6-df57188d708c\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myLoadBalancerbepool",
      "loadBalancingRules": null,
      "name": "myLoadBalancerbepool",
      "outboundNatRule": null,
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup"
    },
    {
      "backendIpConfigurations": null,
      "etag": "W/\"a0e313a1-6de1-48be-aeb6-df57188d708c\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool",
      "loadBalancingRules": null,
      "name": "myBackEndPool",
      "outboundNatRule": null,
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup"
    }
  ],
  "etag": "W/\"a0e313a1-6de1-48be-aeb6-df57188d708c\"",
  "frontendIpConfigurations": [
    {
      "etag": "W/\"a0e313a1-6de1-48be-aeb6-df57188d708c\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/frontendIPConfigurations/LoadBalancerFrontEnd",
      "inboundNatPools": null,
      "inboundNatRules": null,
      "loadBalancingRules": null,
      "name": "LoadBalancerFrontEnd",
      "outboundNatRules": null,
      "privateIpAddress": null,
      "privateIpAllocationMethod": "Dynamic",
      "provisioningState": "Succeeded",
      "publicIpAddress": {
        "dnsSettings": null,
        "etag": null,
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/PublicIPmyLoadBalancer",
        "idleTimeoutInMinutes": null,
        "ipAddress": null,
        "ipConfiguration": null,
        "location": null,
        "name": null,
        "provisioningState": null,
        "publicIpAddressVersion": null,
        "publicIpAllocationMethod": null,
        "resourceGroup": "myResourceGroup",
        "resourceGuid": null,
        "tags": null,
        "type": null
      },
      "resourceGroup": "myResourceGroup",
      "subnet": null
    },
    {
      "etag": "W/\"a0e313a1-6de1-48be-aeb6-df57188d708c\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/frontendIPConfigurations/myFrontEndPool",
      "inboundNatPools": null,
      "inboundNatRules": null,
      "loadBalancingRules": null,
      "name": "myFrontEndPool",
      "outboundNatRules": null,
      "privateIpAddress": null,
      "privateIpAllocationMethod": "Dynamic",
      "provisioningState": "Succeeded",
      "publicIpAddress": {
        "dnsSettings": null,
        "etag": null,
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP",
        "idleTimeoutInMinutes": null,
        "ipAddress": null,
        "ipConfiguration": null,
        "location": null,
        "name": null,
        "provisioningState": null,
        "publicIpAddressVersion": null,
        "publicIpAllocationMethod": null,
        "resourceGroup": "myResourceGroup",
        "resourceGuid": null,
        "tags": null,
        "type": null
      },
      "resourceGroup": "myResourceGroup",
      "subnet": null
    }
  ],
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer",
  "inboundNatPools": [],
  "inboundNatRules": [],
  "loadBalancingRules": [],
  "location": "westeurope",
  "name": "myLoadBalancer",
  "outboundNatRules": [],
  "probes": [],
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "resourceGuid": "b5815801-b53d-4c22-aafc-2a9064f90f3c",
  "tags": {},
  "type": "Microsoft.Network/loadBalancers"
}
```

## <a name="create-a-network-security-group-and-rules"></a>네트워크 보안 그룹 및 규칙 만들기
이번에는 네트워크 보안 그룹 및 NIC 액세스를 제어하는 인바운드 규칙을 만들겠습니다. 네트워크 보안 그룹은 NIC 또는 서브넷에 적용될 수 있습니다. VM 내부 및 외부로 트래픽의 흐름을 제어하도록 규칙을 정의합니다. 다음 예제에서는 [az network nsg create](/cli/azure/network/nsg#create)를 사용하여 `myNetworkSecurityGroup`이라는 네트워크 보안 그룹을 만듭니다.

```azurecli
az network nsg create --resource-group myResourceGroup --location westeurope \
  --name myNetworkSecurityGroup
```

[az network nsg rule create](/cli/azure/network/nsg/rule#create)를 사용하여 포트 22에 인바운드 연결이 가능하도록(SSH를 지원하기 위해) NSG에 대한 인바운드 규칙을 추가하겠습니다. 다음 예제는 포트 22에서 TCP를 허용하도록 `myNetworkSecurityGroupRuleSSH`라는 규칙을 만듭니다.

```azurecli
az network nsg rule create --resource-group myResourceGroup \
  --nsg-name myNetworkSecurityGroup --name myNetworkSecurityGroupRuleSSH \
  --protocol tcp --direction inbound --priority 1000 \
  --source-address-prefix '*' --source-port-range '*' \
  --destination-address-prefix '*' --destination-port-range 22 --access allow
```

이제 포트 80에 인바운드 연결이 가능하도록(웹 트래픽을 지원하기 위해) NSG에 대한 인바운드 규칙을 추가하겠습니다. 다음 예제는 포트 80에서 TCP를 허용하도록 `myNetworkSecurityGroupRuleHTTP`라는 규칙을 만듭니다.

```azurecli
az network nsg rule create --resource-group myResourceGroup \
  --nsg-name myNetworkSecurityGroup --name myNetworkSecurityGroupRuleHTTP \
  --protocol tcp --direction inbound --priority 1001 \
  --source-address-prefix '*' --source-port-range '*' \
  --destination-address-prefix '*' --destination-port-range 80 --access allow
```

> [!NOTE]
> 인바운드 규칙은 인바운드 네트워크 연결에 대한 필터입니다. 이 예제에서는 NSG를 VM 가상 NIC에 바인딩합니다. 이 경우 포트 22에 대한 모든 요청이 VM의 NIC로 통과합니다. 이것은 끝점이 아닌 네트워크 연결에 대한 인바운드 규칙으로, 클래식 배포와 관련이 있습니다. 포트를 열려면 `--source-port-range`를 '\*'(기본값)로 설정하여 **모든** 요청 포트의 인바운드 요청을 수락해야 합니다. 포트는 일반적으로 동적입니다.

[az network nsg show](/cli/azure/network/nsg#show)를 사용하여 네트워크 보안 그룹 및 규칙을 검사합니다.

```azurecli
az network nsg show --resource-group myResourceGroup --name myNetworkSecurityGroup
```

출력

```json
{
  "defaultSecurityRules": [
    {
      "access": "Allow",
      "description": "Allow inbound traffic from all VMs in VNET",
      "destinationAddressPrefix": "VirtualNetwork",
      "destinationPortRange": "*",
      "direction": "Inbound",
      "etag": "W/\"2b656589-f332-4ba4-92f3-afb3be5c192c\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/defaultSecurityRules/AllowVnetInBound",
      "name": "AllowVnetInBound",
      "priority": 65000,
      "protocol": "*",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "VirtualNetwork",
      "sourcePortRange": "*"
    },
    {
      "access": "Allow",
      "description": "Allow inbound traffic from azure load balancer",
      "destinationAddressPrefix": "*",
      "destinationPortRange": "*",
      "direction": "Inbound",
      "etag": "W/\"2b656589-f332-4ba4-92f3-afb3be5c192c\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/defaultSecurityRules/AllowAzureLoadBalancerInBound",
      "name": "AllowAzureLoadBalancerInBound",
      "priority": 65001,
      "protocol": "*",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "AzureLoadBalancer",
      "sourcePortRange": "*"
    },
    {
      "access": "Deny",
      "description": "Deny all inbound traffic",
      "destinationAddressPrefix": "*",
      "destinationPortRange": "*",
      "direction": "Inbound",
      "etag": "W/\"2b656589-f332-4ba4-92f3-afb3be5c192c\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/defaultSecurityRules/DenyAllInBound",
      "name": "DenyAllInBound",
      "priority": 65500,
      "protocol": "*",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "*",
      "sourcePortRange": "*"
    },
    {
      "access": "Allow",
      "description": "Allow outbound traffic from all VMs to all VMs in VNET",
      "destinationAddressPrefix": "VirtualNetwork",
      "destinationPortRange": "*",
      "direction": "Outbound",
      "etag": "W/\"2b656589-f332-4ba4-92f3-afb3be5c192c\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/defaultSecurityRules/AllowVnetOutBound",
      "name": "AllowVnetOutBound",
      "priority": 65000,
      "protocol": "*",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "VirtualNetwork",
      "sourcePortRange": "*"
    },
    {
      "access": "Allow",
      "description": "Allow outbound traffic from all VMs to Internet",
      "destinationAddressPrefix": "Internet",
      "destinationPortRange": "*",
      "direction": "Outbound",
      "etag": "W/\"2b656589-f332-4ba4-92f3-afb3be5c192c\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/defaultSecurityRules/AllowInternetOutBound",
      "name": "AllowInternetOutBound",
      "priority": 65001,
      "protocol": "*",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "*",
      "sourcePortRange": "*"
    },
    {
      "access": "Deny",
      "description": "Deny all outbound traffic",
      "destinationAddressPrefix": "*",
      "destinationPortRange": "*",
      "direction": "Outbound",
      "etag": "W/\"2b656589-f332-4ba4-92f3-afb3be5c192c\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/defaultSecurityRules/DenyAllOutBound",
      "name": "DenyAllOutBound",
      "priority": 65500,
      "protocol": "*",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "*",
      "sourcePortRange": "*"
    }
  ],
  "etag": "W/\"2b656589-f332-4ba4-92f3-afb3be5c192c\"",
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup",
  "location": "westeurope",
  "name": "myNetworkSecurityGroup",
  "networkInterfaces": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "resourceGuid": "79c2c293-ee3e-4616-bf9c-4741ff1f708a",
  "securityRules": [
    {
      "access": "Allow",
      "description": null,
      "destinationAddressPrefix": "*",
      "destinationPortRange": "22",
      "direction": "Inbound",
      "etag": "W/\"2b656589-f332-4ba4-92f3-afb3be5c192c\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/securityRules/myNetworkSecurityGroupRuleSSH",
      "name": "myNetworkSecurityGroupRuleSSH",
      "priority": 1000,
      "protocol": "tcp",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "*",
      "sourcePortRange": "*"
    },
    {
      "access": "Allow",
      "description": null,
      "destinationAddressPrefix": "*",
      "destinationPortRange": "80",
      "direction": "Inbound",
      "etag": "W/\"2b656589-f332-4ba4-92f3-afb3be5c192c\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/securityRules/myNetworkSecurityGroupRuleHTTP",
      "name": "myNetworkSecurityGroupRuleHTTP",
      "priority": 1001,
      "protocol": "tcp",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "*",
      "sourcePortRange": "*"
    }
  ],
  "subnets": null,
  "tags": {},
  "type": "Microsoft.Network/networkSecurityGroups"
}
```

## <a name="create-an-nic-to-use-with-the-linux-vm"></a>Linux VM과 함께 사용할 NIC 만들기
NIC는 사용할 때 규칙을 적용할 수 있으므로 프로그래밍 방식으로 사용할 수 있습니다. 2개 이상 있을 수도 있습니다. 다음 [az network nic create](https://docs.microsoft.com/en-us/cli/azure/network/nic#create) 명령에서 NIC를 부하 백 엔드 IP 풀에 연결하고 SSH 트래픽 및 네트워크 보안 그룹을 허용하기 위한 NAT 규칙을 연결했습니다.

다음 예제는 `myNic1`라는 NIC를 만듭니다.

```azurecli
az network nic create --resource-group myResourceGroup --location westeurope --name myNic1 \
  --vnet-name myVnet --subnet mySubnet --network-security-group myNetworkSecurityGroup \
  --lb-name myLoadBalancer --lb-address-pools myBackEndPool \
  --lb-inbound-nat-rules myLoadBalancerRuleSSH1
```

출력

```json
{
  "newNIC": {
    "dnsSettings": {
      "appliedDnsServers": [],
      "dnsServers": []
    },
    "enableIPForwarding": false,
    "ipConfigurations": [
      {
        "etag": "W/\"a76b5c0d-14e1-4a99-afd4-5cd8ac0465ca\"",
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic1/ipConfigurations/ipconfig1",
        "name": "ipconfig1",
        "properties": {
          "loadBalancerBackendAddressPools": [
            {
              "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool",
              "resourceGroup": "myResourceGroup"
            }
          ],
          "loadBalancerInboundNatRules": [
            {
              "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1",
              "resourceGroup": "myResourceGroup"
            }
          ],
          "primary": true,
          "privateIPAddress": "192.168.1.4",
          "privateIPAllocationMethod": "Dynamic",
          "provisioningState": "Succeeded",
          "subnet": {
            "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet",
            "resourceGroup": "myResourceGroup"
          }
        },
        "resourceGroup": "myResourceGroup"
      }
    ],
    "networkSecurityGroup": {
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup",
      "resourceGroup": "myResourceGroup"
    },
    "provisioningState": "Succeeded",
    "resourceGuid": "838977cb-cf4b-4c4a-9a32-0ddec7dc818b"
  }
}
```

이제 두 번째 NIC를 만들고 백 엔드 IP 풀에 다시 연결합니다. 이번에는 두 번째 NAT 규칙이 SSH 트래픽을 허용합니다. 다음 예제는 `myNic2`라는 NIC를 만듭니다.

```azurecli
az network nic create --resource-group myResourceGroup --location westeurope --name myNic2 \
  --vnet-name myVnet --subnet mySubnet --network-security-group myNetworkSecurityGroup \
  --lb-name myLoadBalancer --lb-address-pools myBackEndPool \
  --lb-inbound-nat-rules myLoadBalancerRuleSSH2
```


## <a name="create-an-availability-set"></a>가용성 집합 만들기
가용성 집합은 장애 도메인 및 업그레이드 도메인에 걸쳐 VM을 분산하는 데 유용합니다. [az vm availability-set create](/cli/azure/vm/availability-set#create)를 사용하여 VM에 대한 가용성 집합을 만들어 보겠습니다. 다음 예제는 `myAvailabilitySet`라는 가용성 집합을 만듭니다.

```azurecli
az vm availability-set create --resource-group myResourceGroup --location westeurope \
  --name myAvailabilitySet
```

장애 도메인은 공통의 전원 및 네트워크 스위치를 공유하는 가상 컴퓨터 그룹을 정의합니다. 기본적으로 가용성 집합 안에 구성된 가상 컴퓨터는 최대&3;개의 장애 도메인에 분산되어 있습니다. 이러한 장애 도메인 중 하나에서 발생한 하드웨어 문제가 앱을 실행 중인 모든 VM에 영향을 미치지 않는 것이 가장 좋습니다. Azure는 가용성 집합에 VM을 배치할 때 VM을 전체 장애 도메인에 자동으로 분산합니다.

업그레이드 도메인은 동시에 재부팅할 수 있는 가상 컴퓨터 그룹과 기본 물리적 하드웨어를 나타냅니다. 업그레이드 도메인의 재부팅 순서는 계획된 유지 보수 중 순차적으로 진행되지 않을 수 있으며, 한 번에 하나의 업그레이드만 재부팅됩니다. 또한 Azure는 가용성 집합에 VM을 배치할 때 VM을 업그레이드 도메인에 자동으로 분산합니다.

[VM의 가용성 관리](virtual-machines-linux-manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)에 대한 자세한 내용을 참조하세요.


## <a name="create-the-linux-vms"></a>Linux VM 만들기
인터넷에서 액세스 가능한 VM을 지원하기 위해 네트워크 리소스를 만들었습니다. 이제 해당 VM을 만들고 암호 없이 SSH 키를 사용하여 VM을 보호하겠습니다. 이 예에서는 가장 최근의 LTS를 기반으로 Ubuntu VM을 만들겠습니다. [Azure VM 이미지 찾기](virtual-machines-linux-cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)에 설명된 대로 [az vm image list](/cli/azure/vm/image#list)를 사용하여 해당 이미지 정보를 찾을 것입니다.

인증에 사용할 SSH 키도 지정합니다. SSH 키가 없으면 [다음 지침](virtual-machines-linux-mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)을 사용하여 만들 수 있습니다. 또는 VM이 만들어진 후 `--admin-password` 메서드를 사용하여 SSH 연결을 인증할 수 있습니다. 이 메서드는 일반적으로 보안 수준이 낮습니다.

[az vm create](/cli/azure/vm#create) 명령으로 모든 리소스 및 정보를 결합하여 VM을 만듭니다. 다음 예제는 Azure Managed Disks를 사용하여 `myVM1`이라는 VM을 만듭니다. 관리되지 않는 디스크를 사용하려는 경우 아래의 추가 정보를 참조하세요.

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM1 \
    --location westeurope \
    --availability-set myAvailabilitySet \
    --nics myNic1 \
    --vnet myVnet \
    --subnet-name mySubnet \
    --nsg myNetworkSecurityGroup \
    --image UbuntuLTS \
    --ssh-key-value ~/.ssh/id_rsa.pub \
    --admin-username azureuser
```

Azure Managed Disks를 사용하는 경우 이 단계를 건너뜁니다. 관리되지 않는 디스크를 사용하려 하고 이전 단계에서 저장소 계정을 만든 경우 일부 추가 매개 변수를 진행 명령에 추가해야 합니다. 다음과 같은 추가 매개 변수를 진행 명령에 추가하여 `mystorageaccount`라는 저장소 계정에 관리되지 않는 디스크를 만듭니다. 

```azurecli
  --use-unmanaged-disk \
  --storage-account mystorageaccount
```

출력

```json
{
  "fqdn": "",
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM1",
  "macAddress": "",
  "privateIpAddress": "",
  "publicIpAddress": "",
  "resourceGroup": "myResourceGroup"
}
```

기본 SSH 키를 사용하여 VM에 즉시 연결할 수 있습니다. 부하 분산 장치를 통과하게 되므로 적절한 포트를 지정하는지 확인합니다. (첫 번째 VM의 경우 포트 4222를 VM에 전달하도록 NAT 규칙을 설정합니다.)

```bash
ssh ops@mypublicdns.westeurope.cloudapp.azure.com -p 4222 -i ~/.ssh/id_rsa.pub
```

출력

```bash
The authenticity of host '[mypublicdns.westeurope.cloudapp.azure.com]:4222 ([xx.xx.xx.xx]:4222)' can't be established.
ECDSA key fingerprint is 94:2d:d0:ce:6b:fb:7f:ad:5b:3c:78:93:75:82:12:f9.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added '[mypublicdns.westeurope.cloudapp.azure.com]:4222,[xx.xx.xx.xx]:4222' (ECDSA) to the list of known hosts.
Welcome to Ubuntu 16.04.1 LTS (GNU/Linux 4.4.0-34-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  Get cloud support with Ubuntu Advantage Cloud Guest:
    http://www.ubuntu.com/business/services/cloud

0 packages can be updated.
0 updates are security updates.

ops@myVM1:~$
```

이제 같은 방식으로 두 번째 VM을 만드십시오.

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM2 \
    --location westeurope \
    --availability-set myAvailabilitySet \
    --nics myNic2 \
    --vnet myVnet \
    --subnet-name mySubnet \
    --nsg myNetworkSecurityGroup \
    --image UbuntuLTS \
    --ssh-key-value ~/.ssh/id_rsa.pub \
    --admin-username azureuser
```

다시 기본 Azure Managed Disks를 사용하지 않는 경우 다음과 같은 추가 매개 변수를 진행 명령에 추가하여 `mystorageaccount`라는 저장소 계정에 관리되지 않는 디스크를 만듭니다.

```azurecli
  --use-unmanaged-disk \
  --storage-account mystorageaccount
``` 

이 시점에는 암호가 사용되지 않도록 설정되어 있으므로 Azure의 부하 분산 장치에서 Ubuntu VM을 실행 중이며 보유하고 있는 SSH 키 쌍을 사용하여 로그인할 수 있습니다. nginx 또는 httpd를 설치하고 웹앱을 배포한 다음 트래픽이 부하 분산 장치를 통해 두 VM에 연결되는 것을 확인할 수 있습니다.


## <a name="export-the-environment-as-a-template"></a>환경을 템플릿으로 내보내기
지금까지 이 환경을 빌드했습니다. 동일한 매개 변수를 사용하여 추가 개발 환경을 만들려고 하거나 일치하는 프로덕션 환경을 만들려면 어떻게 해야 할까요? Resource Manager는 사용자 환경에 대한 모든 매개 변수를 정의하는 JSON 템플릿을 사용합니다. 이 JSON 템플릿을 참조하여 전체 환경을 빌드합니다. [JSON 템플릿을 수동으로 빌드](../azure-resource-manager/resource-group-authoring-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)하거나 기존 환경을 내보내 JSON 템플릿을 만들 수 있습니다. 다음과 같이 [az group export](/cli/azure/group#export)를 사용하여 리소스 그룹을 내보냅니다.

```azurecli
az group export --name myResourceGroup > myResourceGroup.json
```

이 명령을 실행하면 `myResourceGroup.json` 파일이 현재 작업 디렉터리에 만들어집니다. 그런 다음 이 템플릿에서 환경을 만들 경우 부하 분산 장치, 네트워크 인터페이스 또는 VM의 이름과 같은 모든 리소스 이름을 지정하라는 메시지가 표시됩니다. 앞에 나온 **az group export** 명령에 `--include-parameter-default-value` 매개 변수를 추가하여 템플릿 파일에 이러한 이름을 채울 수 있습니다. JSON 템플릿을 편집하여 리소스 이름을 지정하거나 리소스 이름을 지정하는 [parameters.json 파일을 만듭니다](../azure-resource-manager/resource-group-authoring-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) .

템플릿에서 환경을 만들려면 다음과 같이 [az group deployment create](/cli/azure/group/deployment#create)를 사용합니다.

```azurecli
az group deployment create --resource-group myNewResourceGroup \
  --template-file myResourceGroup.json
```

[템플릿에서 배포하는 방법에 대해 자세히 읽어볼 수 있습니다](../azure-resource-manager/resource-group-template-deploy-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 환경을 점진적으로 업데이트하고, 매개 변수 파일을 사용하고, 단일 저장소 위치에서 템플릿에 액세스하는 방법을 알아봅니다.

## <a name="next-steps"></a>다음 단계
이제 여러 네트워킹 구성 요소 및 VM을 사용할 준비가 되셨습니다. 이 샘플 환경에 사용하여 여기에 소개된 핵심 구성 요소로 응용 프로그램을 빌드할 수 있습니다.



<!--HONumber=Feb17_HO2-->


