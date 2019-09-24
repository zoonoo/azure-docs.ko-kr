---
title: Azure CLI를 사용 하 여 Azure 개인 끝점 만들기 Microsoft Docs
description: Azure 개인 끝점에 대 한 자세한 정보
services: private-link
author: KumudD
ms.service: private-link
ms.topic: article
ms.date: 09/16/2019
ms.author: kumud
ms.openlocfilehash: 30994133b19c4f59ae9e8be26caffe14348638f6
ms.sourcegitcommit: 3fa4384af35c64f6674f40e0d4128e1274083487
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2019
ms.locfileid: "71219378"
---
# <a name="create-a-private-endpoint-using-azure-cli"></a>Azure CLI를 사용 하 여 개인 끝점 만들기
개인 끝점은 Azure의 개인 링크를 위한 기본 구성 요소입니다. Vm (가상 머신)과 같은 Azure 리소스가 개인 링크 리소스와 개인적으로 통신할 수 있도록 합니다. 이 빠른 시작에서는 Azure CLI를 사용 하 여 개인 끝점이 있는 SQL Database 서버, 가상 네트워크에서 VM을 만드는 방법에 대해 알아봅니다. 그런 다음 VM에 액세스 하 고 개인 링크 리소스 (이 예제에서는 개인 Azure SQL Database 서버)에 안전 하 게 액세스할 수 있습니다. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

대신 Azure CLI를 로컬에 설치하고 사용하려는 경우 이 빠른 시작을 진행하려면 Azure CLI 버전 2.0.28 이상을 사용해야 합니다. 설치된 버전을 확인하려면 `az --version`을 실행합니다. 설치 또는 업그레이드 정보는 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

리소스를 만들려면 먼저 Virtual Network를 호스트할 리소스 그룹을 만들어야 합니다. [az group create](/cli/azure/group)를 사용하여 리소스 그룹을 만듭니다. 이 예제에서는 *westcentralus* 위치에 *myresourcegroup* 이라는 리소스 그룹을 만듭니다.

```azurecli-interactive
az group create --name myResourceGroup --location westcentralus
```

## <a name="create-a-virtual-network"></a>가상 네트워크 만들기
[Az Network vnet create](/cli/azure/network/vnet)를 사용 하 여 Virtual Network를 만듭니다. 이 예제에서는 *Mysubnet*이라는 하나의 서브넷을 사용 하 여 *myVirtualNetwork* 이라는 기본 Virtual Network를 만듭니다.

```azurecli-interactive
az network vnet create \
 --name myVirtualNetwork \
 --resource-group myResourceGroup \
 --subnet-name mySubnet
```
## <a name="disable-subnet-private-endpoint-policies"></a>서브넷 개인 끝점 정책 사용 안 함 
Azure는 리소스를 가상 네트워크 내의 서브넷에 배포 하므로 개인 끝점 네트워크 정책을 사용 하지 않도록 설정 하려면 서브넷을 만들거나 업데이트 해야 합니다. [Az network vnet subnet update](https://docs.microsoft.com/cli/azure/network/vnet/subnet?view=azure-cli-latest#az-network-vnet-subnet-update)를 사용 하 여 * mysubnet * * 이라는 서브넷 구성을 업데이트 합니다.

```azurecli-interactive
az network vnet subnet update \
 --name mySubnet \
 --resource-group myResourceGroup \
 --vnet-name myVirtualNetwork \
 --disable-private-endpoint-network-policies true
```
## <a name="create-the-vm"></a>VM 만들기 
Az vm create를 사용 하 여 VM을 만듭니다. 메시지가 표시 되 면 VM에 대 한 로그인 자격 증명으로 사용할 암호를 제공 합니다. 이 예제에서는 *myvm*이라는 VM을 만듭니다. 
```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm \
  --image Win2019Datacenter
```
 VM의 공용 IP 주소를 적어둡니다. 이 주소를 사용하여 다음 단계에서 인터넷을 통해 VM에 연결합니다.

## <a name="create-a-sql-database-server"></a>SQL Database 서버 만들기 
Az SQL server create 명령을 사용 하 여 SQL Database 서버를 만듭니다. SQL Server 이름은 Azure에서 고유 해야 하므로 괄호 안의 자리 표시자 값을 고유한 값으로 바꿉니다. 

```azurecli-interactive
# Create a logical server in the resource group 
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

참고 SQL Server id는 다음 단계에서 ```/subscriptions/subscriptionId/resourceGroups/myResourceGroup/providers/Microsoft.Sql/servers/myserver.``` SQL Server id를 사용 하는 것과 비슷합니다. 

## <a name="create-the-private-endpoint"></a>개인 끝점 만들기 
Virtual Network에서 SQL Database 서버에 대 한 개인 끝점을 만듭니다. 
```azurecli-interactive
az network private-endpoint create \  
    --name myPrivateEndpoint \  
    --resource-group myResourceGroup \  
    --vnet-name myVirtualNetwork  \  
    --subnet mySubnet \  
    --private-connection-resource-id "<SQL Server ID>" \  
    --group-ids sqlServer \  
    --connection-name myConnection  
 ```
## <a name="configure-the-private-dns-zone"></a>사설 DNS 영역 구성 
SQL Database 서버 도메인에 대 한 사설 DNS 영역을 만들고 Virtual Network 연결 링크를 만듭니다. 
```azurecli-interactive
az network private-dns zone create --resource-group myResourceGroup \ 
   --name  "privatelink.database.windows.net" 
az network private-dns link vnet create --resource-group myResourceGroup \ 
   --zone-name  "privatelink.database.windows.net"\ 
   --name MyDNSLink \ 
   --virtual-network myVirtualNetwork \ 
   --registration-enabled false 

#Query for the network interface ID  
networkInterfaceId=$(az network private-endpoint show --name myPrivateEndpoint --resource-group myResourceGroup --query 'networkInterfaces[0].id' -o tsv)
 
 
az resource show --ids $networkInterfaceId --api-version 2019-04-01 -o json 
# Copy the content for privateIPAddress and FQDN matching the SQL server name 
 
 
#Create DNS records 
az network private-dns record-set a create --name myserver --zone-name privatelink.database.windows.net --resource-group myResourceGroup  
az network private-dns record-set a add-record --record-set-name myserver --zone-name privatelink.database.windows.net --resource-group myResourceGroup -a <Private IP Address>
```

## <a name="connect-to-a-vm-from-the-internet"></a>인터넷에서 VM에 연결

다음과 같이 인터넷에서 VM *myvm* 에 연결 합니다.

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

## <a name="access-sql-database-server-privately-from-the-vm"></a>VM에서 전용 SQL Database 서버 액세스

이 섹션에서는 개인 끝점을 사용 하 여 VM에서 SQL Database 서버에 연결 합니다.

 1.  *myVM*의 원격 데스크톱에서 PowerShell을 엽니다.
 2. Nslookup myserver.database.windows.net  을 입력 하면 다음과 유사한 메시지가 표시 됩니다. 

```
      Server:  UnKnown 
      Address:  168.63.129.16 
      Non-authoritative answer: 
      Name:    myserver.privatelink.database.windows.net 
      Address:  10.0.0.5 
      Aliases:  myserver.database.windows.net 
```
 3. SQL Server Management Studio 설치 
 4. 서버에 연결에서 다음 정보를 입력 하거나 선택 합니다. 서버 유형: 데이터베이스 엔진를 선택 합니다.
 서버 이름: Myserver.database.windows.net 사용자 이름 선택: 만드는 동안 제공 된 사용자 이름을 입력 합니다.
 암호: 만드는 동안 제공 된 암호를 입력 합니다.
 암호 기억을: 예를 선택 합니다.
 
 5.  **연결**을 선택 합니다.
 6. 왼쪽 메뉴에서 **데이터베이스** 를 검색 합니다.
 7. 생략할 *Mydatabase* 에서 정보 만들기 또는 쿼리
 8. *Myvm*에 대 한 원격 데스크톱 연결을 닫습니다.

## <a name="clean-up-resources"></a>리소스 정리 
더 이상 필요 하지 않은 경우 az group delete를 사용 하 여 리소스 그룹 및 해당 그룹에 포함 된 모든 리소스를 제거할 수 있습니다. 

```azurecli-interactive
az group delete --name myResourceGroup --yes 
```

## <a name="next-steps"></a>다음 단계
- [Azure 개인 링크](private-link-overview.md) 에 대 한 자세한 정보
 
