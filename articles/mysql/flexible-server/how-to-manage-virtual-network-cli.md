---
title: 가상 네트워크 관리-Azure CLI-Azure Database for MySQL 유연한 서버
description: Azure CLI를 사용 하 여 Azure Database for MySQL 유연한 서버를 위한 가상 네트워크 만들기 및 관리
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: how-to
ms.date: 9/21/2020
ms.openlocfilehash: 4e7d75bc1a2acd805e573d4c6ad80f9892045551
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90937316"
---
# <a name="create-and-manage-virtual-networks-for-azure-database-for-mysql---flexible-server-using-the-azure-cli"></a>Azure CLI를 사용 하 여 Azure Database for MySQL 유연한 서버를 위한 가상 네트워크 만들기 및 관리

> [!IMPORTANT]
> Azure Database for MySQL 유연한 서버는 현재 공개 미리 보기로 제공됩니다.

Azure Database for MySQL 유동 서버는 유동 서버에 연결하는 두 가지 유형의 상호 배타적인 네트워크 연결 방법을 지원합니다. 다음은 두 가지 옵션입니다.

- 퍼블릭 액세스(허용된 IP 주소)
- 프라이빗 액세스(VNet 통합)

이 문서에서는 Azure CLI를 사용 하 여 **개인 액세스 (VNet 통합)** 로 MySQL 서버를 만드는 방법에 중점을 둡니다. *개인 액세스 (VNet 통합)* 를 사용 하 여 유연한 서버를 [Azure Virtual Network](../../virtual-network/virtual-networks-overview.md)에 배포할 수 있습니다. Azure 가상 네트워크는 개인 및 보안 네트워크 통신을 제공 합니다. 개인 액세스에서 MySQL 서버에 대 한 연결은 가상 네트워크 내 에서만 사용할 수 있습니다. 자세히 알아보려면 [개인 액세스 (VNet 통합)](./concepts-networking.md#private-access-vnet-integration)를 참조 하세요.

Azure Database for MySQL 유연한 서버에서 서버를 만드는 동안에만 서버를 가상 네트워크 및 서브넷에 배포할 수 있습니다. 유연한 서버가 가상 네트워크 및 서브넷에 배포 된 후에는 다른 가상 네트워크, 서브넷 또는 *공용 액세스 (허용 된 IP 주소)* 로 이동할 수 없습니다.

## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell 시작

[Azure Cloud Shell](../../cloud-shell/overview.md)은 이 문서의 단계를 실행하는 데 무료로 사용할 수 있는 대화형 셸입니다. 공용 Azure 도구가 사전 설치되어 계정에서 사용하도록 구성되어 있습니다.

Cloud Shell을 열려면 코드 블록의 오른쪽 위 모서리에 있는 **사용해 보세요**를 선택하기만 하면 됩니다. 또한 [https://shell.azure.com/bash](https://shell.azure.com/bash)로 이동하여 별도의 브라우저 탭에서 Cloud Shell을 열 수도 있습니다. **복사**를 선택하여 코드 블록을 복사하여 Cloud Shell에 붙여넣고, **Enter**를 선택하여 실행합니다.

CLI를 로컬로 설치하고 사용하려면 이 빠른 시작에서 Azure CLI 버전 2.0 이상이 필요합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)를 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

[az login](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest#az-login) 명령을 사용하여 계정에 로그인해야 합니다. Azure 계정의 **구독 id** 를 참조 하는 **id** 속성을 기록해 둡니다.

```azurecli-interactive
az login
```

[az account set](https://docs.microsoft.com/cli/azure/account?view=azure-cli-latest#az-account-set) 명령을 사용하여 계정에 속한 특정 구독을 선택합니다. 명령에서 **subscription** 인수 값으로 사용 하려면 **az Login** output에서 **ID** 값을 적어 둡니다. 구독이 여러 개인 경우 리소스가 과금되어야 할 적절한 구독을 선택합니다. 모든 구독을 가져오려면 [az account list](https://docs.microsoft.com/cli/azure/account?view=azure-cli-latest#az-account-list)를 사용합니다.

```azurecli
az account set --subscription <subscription id>
```

## <a name="create-azure-database-for-mysql-flexible-server-using-cli"></a>CLI를 사용 하 여 유연한 서버 Azure Database for MySQL 만들기
명령을 사용 하 여 `az mysql flexible-server` *개인 액세스 (VNet 통합)* 로 유연한 서버를 만들 수 있습니다. 이 명령은 기본 연결 방법으로 개인 액세스 (VNet 통합)를 사용 합니다. 가상 네트워크 및 서브넷이 제공 되지 않은 경우 생성 됩니다. 서브넷 ID를 사용 하 여 기존 가상 네트워크 및 서브넷을 제공할 수도 있습니다. <!-- You can provide the **vnet**,**subnet**,**vnet-address-prefix** or**subnet-address-prefix** to customize the virtual network and subnet.--> 아래 예제와 같이 CLI를 사용 하 여 유연한 서버를 만드는 다양 한 옵션이 있습니다.

>[!Important]
> 이 명령을 사용 하면 서브넷이 **flexibleServers**에 위임 됩니다. 이렇게 위임하면 Azure Database for MySQL 유동 서버에서만 해당 서브넷을 사용할 수 있습니다. 다른 Azure 리소스 유형은 위임된 서브넷에 있을 수 없습니다.
>

Azure CLI 참조 설명서를 참조 하세요. <!--FIXME --> 구성 가능한 CLI 매개 변수의 전체 목록을 참조하세요. 예를 들어 아래 명령에서 필요에 따라 리소스 그룹을 지정할 수 있습니다.

- 기본 가상 네트워크, 기본 주소 접두사가 포함 된 서브넷을 사용 하 여 유연한 서버 만들기
    ```azurecli-interactive
    az mysql flexible-server create
    ```
<!--- Create a flexible server using already existing virtual network and subnet
    ```azurecli-interactive
    az mysql flexible-server create --vnet myVnet --subnet mySubnet
    ```-->
- 이미 존재 하는 가상 네트워크, 서브넷 및 서브넷 ID를 사용 하 여 유연한 서버를 만듭니다. 제공 된 서브넷은 다른 리소스를 배포 하지 않아야 하 고이 서브넷은 **Microsoft. DBforMySQL/flexibleServers**에 위임 됩니다 (아직 위임 하지 않은 경우).
    ```azurecli-interactive
    az mysql flexible-server create --subnet /subscriptions/{SubID}/resourceGroups/{ResourceGroup}/providers/Microsoft.Network/virtualNetworks/{VNetName}/subnets/{SubnetName}
    ```
    > [!Note]
    > 가상 네트워크 및 서브넷은 유연한 서버와 동일한 지역 및 구독에 있어야 합니다.
<!--
- Create a flexible server using new virtual network, subnet with non-default address prefix
    ```azurecli-interactive
    az mysql flexible-server create --vnet myVnet --vnet-address-prefix 10.0.0.0/24 --subnet mySubnet --subnet-address-prefix 10.0.0.0/24
    ```-->
Azure CLI 참조 설명서를 참조 하세요. <!--FIXME --> 구성 가능한 CLI 매개 변수의 전체 목록을 참조하세요.


## <a name="next-steps"></a>다음 단계
- [유연한 서버 Azure Database for MySQL의 네트워킹](./concepts-networking.md)에 대해 자세히 알아보세요.
- [Azure Portal을 사용하여 Azure Database for MySQL 유연한 서버 가상 네트워크 만들기 및 관리](./how-to-manage-virtual-network-portal.md).
- [유연한 서버 가상 네트워크 Azure Database for MySQL](./concepts-networking.md#private-access-vnet-integration)에 대해 자세히 알아봅니다.
