---
title: "Azure 빠른 시작 - VM 만들기 CLI | Microsoft Docs"
description: "Azure CLI를 사용하여 가상 컴퓨터를 만드는 방법을 빠르게 이해합니다."
services: virtual-machines-linux
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: hero-article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/03/2017
ms.author: nepeters
ms.translationtype: Human Translation
ms.sourcegitcommit: be3ac7755934bca00190db6e21b6527c91a77ec2
ms.openlocfilehash: 08fcde4f5bddccb9de5564455937a637054ebb60
ms.contentlocale: ko-kr
ms.lasthandoff: 05/03/2017

---

# <a name="create-a-linux-virtual-machine-with-the-azure-cli"></a>Azure CLI를 사용하여 Linux 가상 컴퓨터 만들기

명령줄 또는 스크립트에서 Azure 리소스를 만들고 관리하는 데 Azure CLI가 사용됩니다. 이 가이드에서는 Azure CLI를 사용하여 Ubuntu 16.04 LTS가 실행되는 가상 컴퓨터를 배포하는 방법을 자세히 설명합니다. 서버가 배포되면 NGINX를 설치하기 위해 SSH를 사용하여 VM에 연결합니다. 

Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 을 만듭니다.

또한 Azure CLI가 설치되었는지 확인합니다. 자세한 내용은 [Azure CLI 설치 가이드](https://docs.microsoft.com/cli/azure/install-azure-cli)를 참조하세요. 

## <a name="log-in-to-azure"></a>Azure에 로그인 

[az login](/cli/azure/#login) 명령으로 Azure 구독에 로그인하고 화면의 지시를 따릅니다.

```azurecli
az login
```

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

[az group create](/cli/azure/group#create) 명령을 사용하여 리소스 그룹을 만듭니다. Azure 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다. 

다음 예제에서는 *westeurope* 위치에 *myResourceGroup*이라는 리소스 그룹을 만듭니다.

```azurecli
az group create --name myResourceGroup --location westeurope
```

## <a name="create-virtual-machine"></a>가상 컴퓨터 만들기

[az vm create](/cli/azure/vm#create) 명령을 사용하여 VM을 만듭니다. 

다음 예제에서는 *myVM*이라는 VM을 만들고 기본 키 위치에 SSH 키가 없는 경우 이 키를 만듭니다. 특정 키 집합을 사용하려면 `--ssh-key-value` 옵션을 사용합니다.  

```azurecli
az vm create --resource-group myResourceGroup --name myVM --image UbuntuLTS --generate-ssh-keys
```

VM을 만든 경우 Azure CLI는 다음 예제와 비슷한 정보를 표시합니다. `publicIpAddress`을 기록해 둡니다. 이 주소는 VM에 액세스하는 데 사용됩니다.

```azurecli
{
  "fqdns": "",
  "id": "/subscriptions/d5b9d4b7-6fc1-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "westeurope",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "myResourceGroup"
}
```

## <a name="open-port-80-for-web-traffic"></a>웹 트래픽에 대해 포트 80 열기 

기본적으로 Azure에 배포된 Linux 가상 컴퓨터에는 SSH 연결만이 가능합니다. 이 VM이 웹 서버가 되는 경우 인터넷에서 포트 80을 열어야 합니다. [az vm open-port](/cli/azure/vm#open-port)] 명령을 사용하여 원하는 포트를 엽니다.  
 
 ```azurecli 
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```

## <a name="ssh-into-your-vm"></a>VM에 SSH 수행

다음 명령을 사용하여 가상 컴퓨터와의 SSH 세션을 만듭니다. *<publicIpAddress>*을 가상 컴퓨터의 올바른 공용 IP 주소로 바꿔야 합니다.  예제에서 IP 주소는 *40.68.254.142*입니다.

```bash 
ssh <publicIpAddress>
```

## <a name="install-nginx"></a>NGINX 설치

다음 bash 스크립트를 사용하여 패키지 원본을 업데이트하고 최신 NGINX 패키지를 설치합니다. 

```bash 
#!/bin/bash

# update package source
apt-get -y update

# install NGINX
apt-get -y install nginx
```

## <a name="view-the-ngix-welcome-page"></a>NGIX 시작 페이지 보기

NGINX를 설치하고 현재 포트 80이 인터넷에서 VM에 열려 있으면 사용자가 선택한 웹 브라우저를 사용하여 기본 NGINX 시작 페이지를 볼 수 있습니다. 위에 설명한 *publicIpAddress*를 사용하여 기본 페이지를 방문해야 합니다. 

![NGINX 기본 사이트](./media/quick-create-cli/nginx.png) 


## <a name="delete-virtual-machine"></a>가상 컴퓨터 삭제

더 이상 필요하지 않은 경우 [az group delete](/cli/azure/group#delete) 명령을 사용하여 리소스 그룹, VM 및 모든 관련된 리소스를 제거할 수 있습니다.

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>다음 단계

[고가용성 가상 컴퓨터 만들기 자습서](create-cli-complete.md)

[VM 배포 CLI 샘플 탐색](cli-samples.md)

