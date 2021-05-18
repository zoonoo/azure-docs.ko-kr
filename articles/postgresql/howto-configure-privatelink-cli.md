---
title: 프라이빗 링크 - Azure CLI - Azure Database for PostgreSQL - 단일 서버
description: Azure CLI에서 Azure Database for PostgreSQL - 단일 서버의 프라이빗 링크를 구성하는 방법 알아보기
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: how-to
ms.date: 01/09/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 410da33b531524f4a6458df13a89807fedd739a2
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107773336"
---
# <a name="create-and-manage-private-link-for-azure-database-for-postgresql---single-server-using-cli"></a>CLI를 사용하여 Azure Database for PostgreSQL - 단일 서버에 대한 프라이빗 링크 만들기 및 관리

프라이빗 엔드포인트는 Azure에서 프라이빗 링크를 만드는 데 사용되는 기본 구성 요소입니다. 프라이빗 엔드포인트는 VM(Virtual Machines) 같은 Azure 리소스가 프라이빗 링크 리소스와 비공개로 통신할 수 있게 해줍니다. 이 문서에서는 Azure CLI를 사용하여 Azure 프라이빗 엔드포인트가 있는 Azure Virtual Network 및 Azure Database for PostgreSQL 서버에서 VM을 만드는 방법을 알아봅니다.

> [!NOTE]
> 프라이빗 링크 기능은 범용 또는 메모리 최적화 가격 책정 계층의 Azure Database for PostgreSQL 서버에만 사용할 수 있습니다. 데이터베이스 서버가 이러한 가격 책정 계층 중 하나에 포함되어 있는지 확인하세요.

## <a name="prerequisites"></a>필수 구성 요소

이 방법 가이드를 단계별로 실행하려면 다음이 필요합니다.

- [PostgreSQL용 Azure Database 서버 및 데이터베이스](quickstart-create-server-database-azure-cli.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

대신 Azure CLI를 로컬에 설치하고 사용하려는 경우 이 빠른 시작을 진행하려면 Azure CLI 버전 2.0.28 이상을 사용해야 합니다. 설치된 버전을 확인하려면 `az --version`을 실행합니다. 설치 또는 업그레이드 정보는 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

리소스를 만들려면 먼저 Virtual Network를 호스팅할 리소스 그룹을 만들어야 합니다. [az group create](/cli/azure/group)를 사용하여 리소스 그룹을 만듭니다. 이 예제에서는 *westeurope* 위치에 *myResourceGroup* 이라는 리소스 그룹을 만듭니다.

```azurecli-interactive
az group create --name myResourceGroup --location westeurope
```

## <a name="create-a-virtual-network"></a>Virtual Network 만들기
[az network vnet create](/cli/azure/network/vnet)를 사용하여 Virtual Network를 만듭니다. 다음 예제에서는 *mySubnet* 이라는 하나의 서브넷이 있는 *myVirtualNetwork* 라는 기본 Virtual Network를 만듭니다.

```azurecli-interactive
az network vnet create \
 --name myVirtualNetwork \
 --resource-group myResourceGroup \
 --subnet-name mySubnet
```

## <a name="disable-subnet-private-endpoint-policies"></a>서브넷 프라이빗 엔드포인트 정책 사용 안 함 
Azure는 리소스를 가상 네트워크 내의 서브넷에 배포하므로 프라이빗 엔드포인트 [네트워크 정책](../private-link/disable-private-endpoint-network-policy.md)을 사용하지 않도록 서브넷을 만들거나 업데이트해야 합니다. [az network vnet subnet update](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update)를 사용하여 *mySubnet* 이라는 서브넷 구성을 업데이트합니다.

```azurecli-interactive
az network vnet subnet update \
 --name mySubnet \
 --resource-group myResourceGroup \
 --vnet-name myVirtualNetwork \
 --disable-private-endpoint-network-policies true
```
## <a name="create-the-vm"></a>VM 만들기 
az vm create를 사용하여 VM을 만듭니다. 메시지가 표시되면 VM에 대한 로그인 자격 증명으로 사용할 암호를 제공합니다. 다음 예제에서는 *myVm* 이라는 VM을 만듭니다. 
```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm \
  --image Win2019Datacenter
```

 VM의 공용 IP 주소를 적어둡니다. 이 주소를 사용하여 다음 단계에서 인터넷을 통해 VM에 연결합니다.

## <a name="create-an-azure-database-for-postgresql---single-server"></a>Azure Database for PostgreSQL - 단일 서버 만들기 
az postgres server create 명령을 사용하여 Azure Database for PostgreSQL를 만듭니다. PostgreSQL Server의 이름은 Azure 전체에서 고유해야 하므로 자리 표시자 값을 위에서 사용한 고유의 값으로 바꿉니다. 

```azurecli-interactive
# Create a server in the resource group 
az postgres server create \
--name mydemoserver \
--resource-group myresourcegroup \
--location westeurope \
--admin-user mylogin \
--admin-password <server_admin_password> \
--sku-name GP_Gen5_2
```

## <a name="create-the-private-endpoint"></a>프라이빗 엔드포인트 만들기 
Virtual Network에서 PostgreSQL 서버의 프라이빗 엔드포인트를 만듭니다. 

```azurecli-interactive
az network private-endpoint create \  
    --name myPrivateEndpoint \  
    --resource-group myResourceGroup \  
    --vnet-name myVirtualNetwork  \  
    --subnet mySubnet \  
    --private-connection-resource-id $(az resource show -g myResourcegroup -n mydemoserver --resource-type "Microsoft.DBforPostgreSQL/servers" --query "id" -o tsv) \    
    --group-id postgresqlServer \  
    --connection-name myConnection  
 ```

## <a name="configure-the-private-dns-zone"></a>프라이빗 DNS 영역 구성 
PostgreSQL 서버 도메인의 프라이빗 DNS 영역을 만들고, Virtual Network와의 연결 링크를 만듭니다. 

```azurecli-interactive
az network private-dns zone create --resource-group myResourceGroup \ 
   --name  "privatelink.postgres.database.azure.com" 
az network private-dns link vnet create --resource-group myResourceGroup \ 
   --zone-name  "privatelink.postgres.database.azure.com"\ 
   --name MyDNSLink \ 
   --virtual-network myVirtualNetwork \ 
   --registration-enabled false 

#Query for the network interface ID  
networkInterfaceId=$(az network private-endpoint show --name myPrivateEndpoint --resource-group myResourceGroup --query 'networkInterfaces[0].id' -o tsv)
 
 
az resource show --ids $networkInterfaceId --api-version 2019-04-01 -o json 
# Copy the content for privateIPAddress and FQDN matching the Azure database for PostgreSQL name 
 
 
#Create DNS records 
az network private-dns record-set a create --name myserver --zone-name privatelink.postgres.database.azure.com --resource-group myResourceGroup  
az network private-dns record-set a add-record --record-set-name myserver --zone-name privatelink.postgres.database.azure.com --resource-group myResourceGroup -a <Private IP Address>
```

> [!NOTE] 
> 고객 DNS 설정의 FQDN이 구성된 개인 IP로 확인되지 않습니다. [여기](../dns/dns-operations-recordsets-portal.md)에 나온 대로 구성된 FQDN의 DNS 영역을 설정해야 합니다.

> [!NOTE]
> Azure Database for PostgreSQL 및 VNet 서브넷이 서로 다른 구독에 있는 경우도 있습니다. 이러한 경우에는 다음과 같은 구성을 확인해야 합니다.
> - 두 구독 모두에 **Microsoft.DBforPostgreSQL** 리소스 공급자를 등록해야 합니다. 자세한 내용은 [리소스 공급자](../azure-resource-manager/management/resource-providers-and-types.md)를 참조하세요.

## <a name="connect-to-a-vm-from-the-internet"></a>인터넷에서 VM에 연결

다음과 같이 인터넷에서 *myVm* VM에 연결합니다.

1. 포털의 검색 창에 *myVm* 을 입력합니다.

1. **연결** 단추를 선택합니다. **연결** 단추를 선택하면 **가상 머신에 연결** 이 열립니다.

1. **RDP 파일 다운로드** 를 선택합니다. Azure에서 원격 데스크톱 프로토콜( *.rdp*) 파일을 만들고, 컴퓨터에 다운로드합니다.

1. *downloaded.rdp* 파일을 엽니다.

    1. 메시지가 표시되면 **연결** 을 선택합니다.

    1. VM을 만들 때 지정한 사용자 이름과 암호를 입력합니다.

        > [!NOTE]
        > **추가 선택 사항** > **다른 계정 사용** 을 선택하여 VM을 만들 때 입력한 자격 증명을 지정해야 할 수도 있습니다.

1. **확인** 을 선택합니다.

1. 로그인 프로세스 중에 인증서 경고가 나타날 수 있습니다. 인증서 경고가 표시되면 **예** 또는 **계속** 을 선택합니다.

1. VM 데스크톱이 나타나면 최소화하여 로컬 데스크톱으로 돌아갑니다.  

## <a name="access-the-postgresql-server-privately-from-the-vm"></a>VM에서 비공개로 PostgreSQL 서버에 액세스

1. *myVM* 의 원격 데스크톱에서 PowerShell을 엽니다.

2.  `nslookup mydemopostgresserver.privatelink.postgres.database.azure.com`를 입력합니다. 

   다음과 유사한 메시지가 표시됩니다.

   ```azurepowershell
   Server:  UnKnown
   Address:  168.63.129.16
   Non-authoritative answer:
   Name:    mydemopostgresserver.privatelink.postgres.database.azure.com
   Address:  10.1.3.4
   ```

3. 사용 가능한 모든 클라이언트를 사용하여 PostgreSQL 서버의 프라이빗 링크 연결을 테스트합니다. 다음 예제에서는 [Azure Data Studio](/sql/azure-data-studio/download)를 사용하여 작업을 수행합니다.

4. **새 연결** 에서 다음 정보를 입력하거나 선택합니다.

   | 설정 | 값 |
   | ------- | ----- |
   | 서버 유형| **PostgreSQL** 을 선택합니다.|
   | 서버 이름| *mydemopostgresserver.privatelink.postgres.database.azure.com* 선택 |
   | 사용자 이름 | PostgreSQL 서버 생성 중에 제공되는 username@servername으로 사용자 이름을 입력합니다. |
   |암호 |PostgreSQL 서버 생성 중에 제공한 암호를 입력합니다. |
   |SSL|**필수** 를 선택합니다.|
   ||

5. 연결을 선택합니다.

6. 왼쪽 메뉴에서 데이터베이스를 찾아봅니다.

7. (선택 사항) postgreSQL 서버에서 정보를 만들거나 쿼리합니다.

8. myVm에 대한 원격 데스크톱 연결을 닫습니다.

## <a name="clean-up-resources"></a>리소스 정리 
더 이상 필요하지 않은 경우 az group delete를 사용하여 리소스 그룹 및 해당 그룹에 포함된 모든 리소스를 제거할 수 있습니다. 

```azurecli-interactive
az group delete --name myResourceGroup --yes 
```

## <a name="next-steps"></a>다음 단계
- [Azure 프라이빗 엔드포인트란?](../private-link/private-endpoint-overview.md)에 대한 자세한 정보
