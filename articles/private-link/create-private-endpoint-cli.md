---
title: 빠른 시작 - Azure CLI를 사용하여 Azure 프라이빗 엔드포인트 만들기
description: 이 빠른 시작에서는 Azure 프라이빗 엔드포인트에 대해 알아봅니다.
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: quickstart
ms.date: 09/16/2019
ms.author: allensu
ms.custom: devx-track-azurecli
ms.openlocfilehash: e7c098ba06086781306960f76978aac9e4fa06bc
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/31/2020
ms.locfileid: "87502667"
---
# <a name="quickstart-create-a-private-endpoint-using-azure-cli"></a>빠른 시작: Azure CLI를 사용하여 Azure 프라이빗 엔드포인트 만들기

프라이빗 엔드포인트는 Azure에서 Private Link의 기본 구성 요소입니다. 이를 사용하면 VM(가상 머신)과 같은 Azure 리소스에서 Private Link 리소스와 비공개로 통신할 수 있습니다. 이 빠른 시작에서는 Azure CLI를 사용하여 프라이빗 엔드포인트를 사용하는 SQL Database의 서버인 가상 네트워크에 VM을 만드는 방법에 대해 알아봅니다. 그런 다음, VM에 액세스하여 프라이빗 링크 리소스(여기서는 SQL Database의 프라이빗 서버)에 안전하게 액세스할 수 있습니다.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

대신 Azure CLI를 로컬에 설치하고 사용하려는 경우 이 빠른 시작을 진행하려면 Azure CLI 버전 2.0.28 이상을 사용해야 합니다. 설치된 버전을 확인하려면 `az --version`을 실행합니다. 설치 또는 업그레이드 정보는 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

리소스를 만들려면 먼저 Virtual Network를 호스팅할 리소스 그룹을 만들어야 합니다. [az group create](/cli/azure/group)를 사용하여 리소스 그룹을 만듭니다. 다음 예제에서는 *myResourceGroup*이라는 리소스 그룹을 *westcentralus* 위치에 만듭니다.

```azurecli-interactive
az group create --name myResourceGroup --location westcentralus
```

## <a name="create-a-virtual-network"></a>Virtual Network 만들기

[az network vnet create](/cli/azure/network/vnet)를 사용하여 Virtual Network를 만듭니다. 다음 예제에서는 *mySubnet*이라는 하나의 서브넷이 있는 *myVirtualNetwork*라는 기본 Virtual Network를 만듭니다.

```azurecli-interactive
az network vnet create \
 --name myVirtualNetwork \
 --resource-group myResourceGroup \
 --subnet-name mySubnet
```

## <a name="disable-subnet-private-endpoint-policies"></a>서브넷 프라이빗 엔드포인트 정책 사용 안 함

Azure는 리소스를 가상 네트워크 내의 서브넷에 배포하므로 프라이빗 엔드포인트 네트워크 정책을 사용하지 않도록 서브넷을 만들거나 업데이트해야 합니다. [az network vnet subnet update](https://docs.microsoft.com/cli/azure/network/vnet/subnet?view=azure-cli-latest#az-network-vnet-subnet-update)를 사용하여 *mySubnet*이라는 서브넷 구성을 업데이트합니다.

```azurecli-interactive
az network vnet subnet update \
 --name mySubnet \
 --resource-group myResourceGroup \
 --vnet-name myVirtualNetwork \
 --disable-private-endpoint-network-policies true
```

## <a name="create-the-vm"></a>VM 만들기

az vm create를 사용하여 VM을 만듭니다. 메시지가 표시되면 VM에 대한 로그인 자격 증명으로 사용할 암호를 제공합니다. 다음 예제에서는 *myVm*이라는 VM을 만듭니다.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm \
  --image Win2019Datacenter
```

VM의 공용 IP 주소를 적어둡니다. 이 주소를 사용하여 다음 단계에서 인터넷을 통해 VM에 연결합니다.

## <a name="create-a-server-in-sql-database"></a>SQL Database에서 서버 만들기

az sql server create 명령을 사용하여 SQL Database에 서버를 만듭니다. 서버의 이름은 Azure 전체에서 고유해야 하므로 괄호 안의 자리 표시자 값을 사용자 고유의 값으로 바꿉니다.

```azurecli-interactive
# Create a server in the resource group
az sql server create \
    --name "myserver"\
    --resource-group myResourceGroup \
    --location WestUS \
    --admin-user "sqladmin" \
    --admin-password "CHANGE_PASSWORD_1"

# Create a database in the server with zone redundancy as false
az sql db create \
    --resource-group myResourceGroup  \
    --server myserver \
    --name mySampleDatabase \
    --sample-name AdventureWorksLT \
    --edition GeneralPurpose \
    --family Gen4 \
    --capacity 1
```

서버 ID는  ```/subscriptions/subscriptionId/resourceGroups/myResourceGroup/providers/Microsoft.Sql/servers/myserver.```와 비슷합니다. 이 서버 ID는 다음 단계에서 사용합니다.

## <a name="create-the-private-endpoint"></a>프라이빗 엔드포인트 만들기

Virtual Network에서 논리 SQL 서버에 대한 프라이빗 엔드포인트를 만듭니다.

```azurecli-interactive
az network private-endpoint create \  
    --name myPrivateEndpoint \  
    --resource-group myResourceGroup \  
    --vnet-name myVirtualNetwork  \  
    --subnet mySubnet \  
    --private-connection-resource-id "<server ID>" \  
    --group-ids sqlServer \  
    --connection-name myConnection  
 ```

## <a name="configure-the-private-dns-zone"></a>프라이빗 DNS 영역 구성

SQL Database 도메인에 대한 프라이빗 DNS 영역을 만들고, Virtual Network와 연결 링크를 만들고, DNS 영역 그룹을 만들어 프라이빗 엔드포인트를 프라이빗 DNS 영역과 연결합니다. 

```azurecli-interactive
az network private-dns zone create --resource-group myResourceGroup \
   --name  "privatelink.database.windows.net"
az network private-dns link vnet create --resource-group myResourceGroup \
   --zone-name  "privatelink.database.windows.net"\
   --name MyDNSLink \
   --virtual-network myVirtualNetwork \
   --registration-enabled false
az network private-endpoint dns-zone-group create \
   --resource-group myResourceGroup \
   --endpoint-name myPrivateEndpoint \
   --name MyZoneGroup \
   --private-dns-zone "privatelink.database.windows.net" \
   --zone-name sql
```

## <a name="connect-to-a-vm-from-the-internet"></a>인터넷에서 VM에 연결

다음과 같이 인터넷에서 *myVm* VM에 연결합니다.

1. 포털의 검색 창에 *myVm*을 입력합니다.

1. **연결** 단추를 선택합니다. **연결** 단추를 선택하면 **가상 머신에 연결**이 열립니다.

1. **RDP 파일 다운로드**를 선택합니다. Azure에서 원격 데스크톱 프로토콜( *.rdp*) 파일을 만들고, 컴퓨터에 다운로드합니다.

1. 다운로드 한 .rdp* 파일을 엽니다.

    1. 메시지가 표시되면 **연결**을 선택합니다.

    1. VM을 만들 때 지정한 사용자 이름과 암호를 입력합니다.

        > [!NOTE]
        > **추가 선택 사항** > **다른 계정 사용**을 선택하여 VM을 만들 때 입력한 자격 증명을 지정해야 할 수도 있습니다.

1. **확인**을 선택합니다.

1. 로그인 프로세스 중에 인증서 경고가 나타날 수 있습니다. 인증서 경고가 표시되면 **예** 또는 **계속**을 선택합니다.

1. VM 데스크톱이 나타나면 최소화하여 로컬 데스크톱으로 돌아갑니다.  

## <a name="access-sql-database-privately-from-the-vm"></a>VM에서 비공개로 SQL Database에 액세스

이 섹션에서는 프라이빗 엔드포인트를 사용하여 VM에서 SQL Database에 연결합니다.

1. *myVM*의 원격 데스크톱에서 PowerShell을 엽니다.
2. nslookup myserver.database.windows.net을 입력합니다.

   다음과 유사한 메시지가 표시됩니다.

    ```
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    myserver.privatelink.database.windows.net
    Address:  10.0.0.5
    Aliases:  myserver.database.windows.net
    ```

3. SQL Server Management Studio를 설치합니다.
4. [서버에 연결]에서 다음 정보를 입력하거나 선택합니다.

   - 서버 유형: [데이터베이스 엔진]을 선택합니다.
   - 서버 이름: myserver.database.windows.net을 선택합니다.
   - 사용자 이름: 만드는 중에 제공된 사용자 이름을 입력합니다.
   - 암호: 만드는 중에 제공된 암호를 입력합니다.
   - 암호 저장: [예]를 선택합니다.

5. **연결**을 선택합니다.
6. 왼쪽 메뉴에서 **데이터베이스**를 찾습니다.
7. (선택 사항) *mydatabase*에서 정보를 만들거나 쿼리합니다.
8. *myVm*에 대한 원격 데스크톱 연결을 닫습니다.

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 az group delete를 사용하여 리소스 그룹 및 해당 그룹에 포함된 모든 리소스를 제거할 수 있습니다.

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>다음 단계

[Azure Private Link](private-link-overview.md)에 대해 자세히 알아봅니다.
