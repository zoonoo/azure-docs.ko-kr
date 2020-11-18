---
title: 자습서 - Azure Private Endpoint를 사용하여 Azure SQL 서버에 연결 - Azure CLI
description: 이 자습서를 통해 Azure CLI를 사용하여 프라이빗 엔드포인트가 있는 Azure SQL 서버를 만드는 방법을 알아봅니다.
services: private-link
author: asudbring
ms.service: private-link
ms.topic: tutorial
ms.date: 11/03/2020
ms.author: allensu
ms.openlocfilehash: d28a3a304a42ec82ff18ad7f5e72518e2b77ec29
ms.sourcegitcommit: 58f12c358a1358aa363ec1792f97dae4ac96cc4b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2020
ms.locfileid: "93280642"
---
# <a name="tutorial---connect-to-an-azure-sql-server-using-an-azure-private-endpoint---azure-cli"></a>자습서 - Azure Private Endpoint를 사용하여 Azure SQL 서버에 연결 - Azure CLI

Azure Private 엔드포인트는 Azure에서 Private Link를 만드는 데 사용되는 기본 구성 요소입니다. 이를 사용하면 VM(가상 머신)과 같은 Azure 리소스에서 Private Link 리소스와 비공개로 통신할 수 있습니다.

이 자습서에서는 다음과 같은 작업을 수행하는 방법을 살펴봅니다.

> [!div class="checklist"]
> * 가상 네트워크 및 베스천 호스트를 만듭니다.
> * 가상 머신을 만듭니다.
> * Azure SQL 서버 및 프라이빗 엔드포인트를 만듭니다.
> * SQL 서버 프라이빗 엔드포인트에 대한 연결을 테스트합니다.

## <a name="prerequisites"></a>사전 요구 사항

* 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Azure Portal에 로그인하고 `az login`을 실행하여 구독이 활성 상태인지 확인합니다.
* 터미널 또는 명령 창에서 `az --version`을 실행하여 Azure CLI 버전을 확인합니다. 최신 버전은 [최신 릴리스 정보](/cli/azure/release-notes-azure-cli?tabs=azure-cli)를 참조하세요.
  * 최신 버전이 없는 경우 [사용 중인 운영 체제 또는 플랫폼의 설치 가이드](/cli/azure/install-azure-cli)에 따라 설치를 업데이트합니다.

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

Azure 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다.

[az group create](/cli/azure/group#az_group_create)를 사용하여 리소스 그룹을 만듭니다.

* 이름을 **CreateSQLEndpointTutorial-rg** 로 지정합니다. 
* 위치: **eastus**

```azurecli-interactive
az group create \
    --name CreateSQLEndpointTutorial-rg \
    --location eastus
```

## <a name="create-a-virtual-network-and-bastion-host"></a>가상 네트워크 및 베스천 호스트 만들기

이 섹션에서는 가상 네트워크, 서브넷 및 베스천 호스트를 만듭니다. 

베스천 호스트는 가상 머신에 안전하게 연결하여 프라이빗 엔드포인트를 테스트하는 데 사용됩니다.

[az network vnet create](/cli/azure/network/vnet#az_network_vnet_create)를 사용하여 가상 네트워크를 만듭니다.

* 이름: **myVNet**
* **10.0.0.0/16** 의 주소 접두사.
* 서브넷: **myBackendSubnet**
* **10.0.0.0/24** 의 서브넷 접두사.
* **CreateSQLEndpointTutorial-rg** 리소스 그룹에서
* 위치: **eastus**.

```azurecli-interactive
az network vnet create \
    --resource-group CreateSQLEndpointTutorial-rg\
    --location eastus \
    --name myVNet \
    --address-prefixes 10.0.0.0/16 \
    --subnet-name myBackendSubnet \
    --subnet-prefixes 10.0.0.0/24
```

[az network vnet subnet update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update)를 사용하여 프라이빗 엔드포인트에 대한 프라이빗 엔드포인트 네트워크 정책을 사용하지 않도록 서브넷을 업데이트합니다.

```azurecli-interactive
az network vnet subnet update \
    --name myBackendSubnet \
    --resource-group CreateSQLEndpointTutorial-rg \
    --vnet-name myVNet \
    --disable-private-endpoint-network-policies true
```

[az network public-ip create](/cli/azure/network/public-ip#az-network-public-ip-create)를 사용하여 베스천 호스트에 대한 공용 IP 주소를 만듭니다.

* **myBastionIP** 라는 표준 영역 중복 공용 IP 주소를 만듭니다.
* **CreateSQLEndpointTutorial-rg** 에서

```azurecli-interactive
az network public-ip create \
    --resource-group CreateSQLEndpointTutorial-rg \
    --name myBastionIP \
    --sku Standard
```

[az network vnet subnet create](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create)를 사용하여 베스천 서브넷을 만듭니다.

* 이름은 **AzureBastionSubnet** 입니다.
* **10.0.1.0/24** 의 주소 접두사.
* 가상 네트워크: **myVNet**
* 리소스 그룹 **CreateSQLEndpointTutorial-rg** 에서

```azurecli-interactive
az network vnet subnet create \
    --resource-group CreateSQLEndpointTutorial-rg \
    --name AzureBastionSubnet \
    --vnet-name myVNet \
    --address-prefixes 10.0.1.0/24
```

[az network bastion create](/cli/azure/network/bastion#az-network-bastion-create)를 사용하여 베스천 호스트를 만듭니다.

* 이름은 **myBastionHost** 입니다.
* **CreateSQLEndpointTutorial-rg** 에서
* 공용 IP **myBastionIP** 와 연결됩니다.
* 가상 네트워크 **myVNet** 과 연결됩니다.
* 위치: **eastus**.

```azurecli-interactive
az network bastion create \
    --resource-group CreateSQLEndpointTutorial-rg \
    --name myBastionHost \
    --public-ip-address myBastionIP \
    --vnet-name myVNet \
    --location eastus
```

Azure Bastion 호스트를 배포하는 데 몇 분 정도 걸릴 수 있습니다.

## <a name="create-test-virtual-machine"></a>테스트 가상 머신 만들기

이 섹션에서는 프라이빗 엔드포인트를 테스트하는 데 사용할 가상 머신을 만듭니다.

 [az vm create](/cli/azure/vm#az_vm_create)를 사용하여 VM을 만듭니다. 메시지가 표시되면 VM에 대한 자격 증명으로 사용할 암호를 제공합니다.

* 이름을 **myVM** 으로 지정합니다.
* **CreateSQLEndpointTutorial-rg** 에서
* 네트워크 **myVNet** 에서
* 서브넷: **myBackendSubnet**
* 서버 이미지 **Win2019Datacenter**.

```azurecli-interactive
az vm create \
    --resource-group CreateSQLEndpointTutorial-rg \
    --name myVM \
    --image Win2019Datacenter \
    --public-ip-address "" \
    --vnet-name myVNet \
    --subnet myBackendSubnet \
    --admin-username azureuser
```

## <a name="create-an-azure-sql-server"></a>Azure SQL 서버 만들기

이 섹션에서는 SQL 서버 및 데이터베이스를 만듭니다.

[az sql server create](/cli/azure/sql/server#az_sql_server_create)를 사용하여 SQL 서버를 만듭니다.

* **\<sql-server-name>** 을 고유한 서버 이름으로 바꿉니다.
* **\<your-password>** 를 암호로 바꿉니다.
* **CreateSQLEndpointTutorial-rg** 에서
* **eastus** 지역에서

```azurecli-interactive
az sql server create \
    --name <sql-server-name> \
    --resource-group CreateSQLEndpointTutorial-rg \
    --location eastus \
    --admin-user sqladmin \
    --admin-password <your-password>
```

[az sql db create](/cli/azure/sql/db#az_sql_db_create)를 사용하여 데이터베이스를 만듭니다.

* 이름을 **myDataBase** 로 지정합니다.
* **CreateSQLEndpointTutorial-rg** 에서
* **\<sql-server-name>** 을 고유한 서버 이름으로 바꿉니다.

```azurecli-interactive
az sql db create \
    --resource-group CreateSQLEndpointTutorial-rg  \
    --server <sql-server-name> \
    --name myDataBase \
    --sample-name AdventureWorksLT
```

## <a name="create-private-endpoint"></a>프라이빗 엔드포인트 만들기

이 섹션에서는 프라이빗 엔드포인트를 만듭니다.

[az sql server list](/cli/azure/sql/server#az_sql_server_list)를 사용하여 SQL 서버의 리소스 ID를 셸 변수에 넣습니다.

[az network private-endpoint create](/cli/azure/network/private-endpoint#az_network_private_endpoint_create)를 사용하여 엔드포인트 및 연결을 만듭니다.

* 이름을 **myPrivateEndpoint** 로 지정합니다.
* 리소스 그룹 **CreateSQLEndpointTutorial-rg** 에서
* 가상 네트워크: **myVNet**
* 서브넷: **myBackendSubnet**
* **myConnection** 이라는 연결입니다.

```azurecli-interactive
id=$(az sql server list \
    --resource-group CreateSQLEndpointTutorial-rg \
    --query '[].[id]' \
    --output tsv)

az network private-endpoint create \
    --name myPrivateEndpoint \
    --resource-group CreateSQLEndpointTutorial-rg \
    --vnet-name myVNet --subnet myBackendSubnet \
    --private-connection-resource-id $id \
    --group-ids sqlServer \
    --connection-name myConnection  
```

## <a name="configure-the-private-dns-zone"></a>프라이빗 DNS 영역 구성

이 섹션에서는 [az network private-dns zone create](/cli/azure/ext/privatedns/network/private-dns/zone#ext_privatedns_az_network_private_dns_zone_create)를 사용하여 프라이빗 DNS 영역을 만들고 구성합니다.  

[az network private-dns link vnet create](/cli/azure/ext/privatedns/network/private-dns/link/vnet#ext_privatedns_az_network_private_dns_link_vnet_create)를 사용하여 dns 영역에 대한 가상 네트워크 링크를 만듭니다.

[az network private-endpoint dns-zone-group create](/cli/azure/network/private-endpoint/dns-zone-group#az_network_private_endpoint_dns_zone_group_create)를 사용하여 dns 영역 그룹을 만듭니다.

* **privatelink.database.windows.net** 이라는 영역
* 가상 네트워크: **myVNet**
* 리소스 그룹 **CreateSQLEndpointTutorial-rg** 에서
* **myDNSLink** 라는 DNS 링크.
* **myPrivateEndpoint** 와 연결됩니다.
* **MyZoneGroup** 이라는 영역 그룹.

```azurecli-interactive
az network private-dns zone create \
    --resource-group CreateSQLEndpointTutorial-rg \
    --name "privatelink.database.windows.net"

az network private-dns link vnet create \
    --resource-group CreateSQLEndpointTutorial-rg \
    --zone-name "privatelink.database.windows.net" \
    --name MyDNSLink \
    --virtual-network myVNet \
    --registration-enabled false

az network private-endpoint dns-zone-group create \
   --resource-group CreateSQLEndpointTutorial-rg \
   --endpoint-name myPrivateEndpoint \
   --name MyZoneGroup \
   --private-dns-zone "privatelink.database.windows.net" \
   --zone-name sql
```

## <a name="test-connectivity-to-private-endpoint"></a>프라이빗 엔드포인트에 연결 테스트

이 섹션에서는 이전 단계에서 만든 가상 머신을 사용하여 프라이빗 엔드포인트에서 SQL 서버에 연결합니다.

1. [Azure 포털](https://portal.azure.com) 
 
2. 왼쪽 탐색 창에서 **리소스 그룹** 을 선택합니다.

3. **CreateSQLEndpointTutorial-rg** 를 선택합니다.

4. **myVM** 을 선택합니다.

5. **myVM** 에 대한 개요 페이지에서 **연결** 을 선택한 다음, **Bastion** 을 선택합니다.

6. 파란색 **Bastion 사용** 단추를 선택합니다.

7. 가상 머신 만들기에서 입력한 사용자 이름과 암호를 입력합니다.

8. 연결한 후 서버에서 Windows PowerShell을 엽니다.

9. `nslookup <sqlserver-name>.database.windows.net`를 입력합니다. **\<sqlserver-name>** 을 이전 단계에서 만든 SQL 서버의 이름으로 바꿉니다.  아래 표시된 것과 유사한 메시지가 표시됩니다.

    ```powershell
    Server:  UnKnown
    Address:  168.63.129.16

    Non-authoritative answer:
    Name:    mysqlserver8675.privatelink.database.windows.net
    Address:  10.0.0.5
    Aliases:  mysqlserver8675.database.windows.net
    ```

    SQL 서버 이름에 대해 **10.0.0.5** 의 개인 IP 주소가 반환됩니다.  이 주소는 이전에 만든 가상 네트워크의 서브넷에 있습니다.


10. [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017&preserve-view=true)를 **myVM** 에 설치합니다.

11. **SQL Server Management Studio** 를 엽니다.

12. **서버에 연결** 에서 다음 정보를 입력하거나 선택합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | 서버 유형 | **데이터베이스 엔진** 을 선택합니다.|
    | 서버 이름 | **\<sql-server-name>.database.windows.net** 을 입력합니다. |
    | 인증 | **SQL Server 인증** 을 선택합니다. |
    | 사용자 이름 | 서버를 만드는 동안 입력한 사용자 이름을 입력합니다. |
    | 암호 | 서버를 만드는 동안 입력한 암호를 입력합니다. |
    | 암호 기억 | **예** 를 선택합니다. |

13. **연결** 을 선택합니다.

14. 왼쪽 메뉴에서 데이터베이스를 찾습니다.

15. (선택 사항) **mysqldatabase** 에서 정보를 만들거나 쿼리합니다.

16. **myVM** 에 대한 베스천 연결을 닫습니다. 

## <a name="clean-up-resources"></a>리소스 정리 

프라이빗 엔드포인트, SQL Server 및 VM을 다 사용했으면 리소스 그룹과 리소스 그룹에 포함된 모든 리소스를 삭제합니다. 

```azurecli-interactive
az group delete \
    --name CreateSQLEndpointTutorial-rg
```

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음을 만들었습니다.

* 가상 네트워크 및 베스천 호스트
* 가상 머신
* 프라이빗 엔드포인트를 사용하는 Azure SQL 서버

가상 머신을 사용하여 프라이빗 엔드포인트에서 SQL 서버에 대한 연결을 안전하게 테스트했습니다.

Private Link 서비스를 만드는 방법을 알아봅니다.
> [!div class="nextstepaction"]
> [Private Link 서비스 만들기](create-private-link-service-portal.md)
