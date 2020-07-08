---
title: 파일 포함
description: 포함 파일
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 6/2/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 3af8ecb049717da789df96f7dcc4e46668938ff8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84465161"
---
서비스 엔드포인트를 사용하여 스토리지 계정의 퍼블릭 엔드포인트에 대한 액세스를 특정 가상 네트워크로 제한하려면 먼저 스토리지 계정 및 가상 네트워크에 대한 정보를 수집해야 합니다. 이 정보를 수집하기 위해 `<storage-account-resource-group>`, `<storage-account-name>`, `<vnet-resource-group-name>`, `<vnet-name>` 및 `<subnet-name>`을 입력합니다.

```bash
storageAccountResourceGroupName="<storage-account-resource-group>"
storageAccountName="<storage-account-name>"
restrictToVirtualNetworkResourceGroupName="<vnet-resource-group-name>"
restrictToVirtualNetworkName="<vnet-name>"
subnetName="<subnet-name>"

storageAccount=$(az storage account show \
        --resource-group $storageAccountResourceGroupName \
        --name $storageAccountName \
        --query "id" | \
    tr -d '"')

virtualNetwork=$(az network vnet show \
        --resource-group $restrictToVirtualNetworkResourceGroupName \
        --name $restrictToVirtualNetworkName \
        --query "id" | \
    tr -d '"')

subnet=$(az network vnet subnet show \
        --resource-group $restrictToVirtualNetworkResourceGroupName \
        --vnet-name $restrictToVirtualNetworkName \
        --name $subnetName \
        --query "id" | \
    tr -d '"')
```

스토리지 계정 퍼블릭 엔드포인트로 이동하기 위해 가상 네트워크의 트래픽을 Azure 네트워크 패브릭에서 허용하려면 가상 네트워크 서브넷의 `Microsoft.Storage` 서비스 엔드포인트가 공개되어야 합니다. 다음 CLI 명령은 서브넷에 `Microsoft.Storage` 서비스 엔드포인트가 아직 추가되지 않은 경우 이 서비스 엔드포인트를 추가합니다.

```bash
serviceEndpoints=$(az network vnet subnet show \
        --resource-group $restrictToVirtualNetworkResourceGroupName \
        --vnet-name $restrictToVirtualNetworkName \
        --name $subnetName \
        --query "serviceEndpoints[].service" \
        --output tsv)

foundStorageServiceEndpoint=false
for serviceEndpoint in $serviceEndpoints
do
    if [ $serviceEndpoint = "Microsoft.Storage" ]
    then
        foundStorageServiceEndpoint=true
    fi
done

if [ $foundStorageServiceEndpoint = false ] 
then
    serviceEndpointList=""

    for serviceEndpoint in $serviceEndpoints
    do
        serviceEndpointList+=$serviceEndpoint
        serviceEndpointList+=" "
    done
    
    serviceEndpointList+="Microsoft.Storage"

    az network vnet subnet update \
            --ids $subnet \
            --service-endpoints $serviceEndpointList \
            --output none
fi
```

트래픽을 스토리지 계정으로 제한하기 위한 마지막 단계는 네트워킹 규칙을 만들어서 스토리지 계정의 네트워크 규칙 세트에 추가하는 것입니다.

```bash
az storage account network-rule add \
        --resource-group $storageAccountResourceGroupName \
        --account-name $storageAccountName \
        --subnet $subnet \
        --output none

az storage account update \
        --resource-group $storageAccountResourceGroupName \
        --name $storageAccountName \
        --bypass "AzureServices" \
        --default-action "Deny" \
        --output none
```