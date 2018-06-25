---
title: Azure CLI를 사용하여 Azure Database for PostgreSQL VNet 서비스 엔드포인트 및 규칙 만들기 및 관리 | Microsoft Docs
description: 이 문서에서는 Azure CLI 명령줄을 사용하여 Azure Database for PostgreSQL VNet 서비스 엔드포인트 및 규칙을 만들고 관리하는 방법을 설명합니다.
services: postgresql
author: mbolz
ms.author: mbolz
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.devlang: azure-cli
ms.topic: article
ms.date: 06/01/2018
ms.openlocfilehash: 7312000d1f22af3eb0091b46caac2c9607231513
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2018
ms.locfileid: "34736629"
---
# <a name="create-and-manage-azure-database-for-postgresql-vnet-service-endpoints-using-azure-cli"></a>Azure CLI를 사용하여 Azure Database for PostgreSQL VNet 서비스 엔드포인트 만들기 및 관리
VNet(가상 네트워크) 서비스 엔드포인트 및 규칙이 가상 네트워크의 개인 주소 공간을 Azure Database for PostgreSQL 서버로 확장합니다. 편리한 Azure CLI(명령줄 인터페이스) 명령을 사용하면 서버를 관리하는 VNet 서비스 엔드포인트 및 규칙을 만들고, 업데이트하고, 삭제하며, 표시할 수 있습니다. 제한을 포함하여 Azure Database for PostgreSQL VNet 서비스 엔드포인트에 대한 개요는 [Azure Database for PostgreSQL 서버 VNet 서비스 엔드포인트](concepts-data-access-and-security-vnet.md)를 참조하세요. VNet 서비스 엔드포인트는 Azure Database for PostgreSQL에 대한 지원되는 모든 지역의 공용 미리 보기에서 사용할 수 있습니다.

## <a name="prerequisites"></a>필수 조건
이 방법 가이드를 단계별로 실행하려면 다음이 필요합니다.
- [Azure CLI 2.0](/cli/azure/install-azure-cli) 명령줄 유틸리티를 설치하거나, 브라우저에서 Azure Cloud Shell을 사용합니다.
- [PostgreSQL용 Azure Database 서버 및 데이터베이스](quickstart-create-server-database-azure-cli.md)

> [!NOTE]
> VNet 서비스 엔드포인트는 범용 및 메모리 최적화 서버에 대해서만 지원됩니다.

## <a name="configure-vnet-service-endpoints-for-azure-database-for-postgresql"></a>Azure Database for PostgreSQL에 대한 Vnet 서비스 엔드포인트 구성
가상 네트워크를 구성하는 데 [az network vnet](https://docs.microsoft.com/cli/azure/network/vnet?view=azure-cli-latest) 명령이 사용됩니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

CLI를 로컬로 설치하여 사용하도록 선택하는 경우 이 문서에서 Azure CLI 버전 2.0 이상을 실행해야 합니다. 설치된 버전을 확인하려면 `az --version` 명령을 실행합니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 2.0 설치]( /cli/azure/install-azure-cli)를 참조하세요. 

CLI를 로컬로 실행 중인 경우 [az login](/cli/azure/authenticate-azure-cli?view=interactive-log-in) 명령을 사용하여 사용자 계정에 로그인해야 합니다. 해당 구독 이름에 대한 명령 출력에서 **id** 속성을 참고합니다.
```azurecli-interactive
az login
```
`az extension add --name rdbms-vnet` 명령을 사용하여 Azure Database for PostgreSQL VNet 서비스 엔드포인트에 대한 CLI 확장을 설치합니다. 
```azurecli-interactive
az extension add --name rdbms-vnet
```

`az extension list` 명령을 실행하여 CLI 확장의 설치를 확인합니다.
```azurecli-interactive
az extension list
```
명령의 출력은 설치된 모든 확장을 나열합니다. Azure Database for PostgreSQL CLI 확장은 다음과 같습니다.

 { "extensionType": "whl", "name": "rdbms-vnet", "version": "10.0.0" }

> [!NOTE]
> CLI 확장을 제거하려면 `az extension remove -n rdbms-vnet` 명령을 실행합니다. 

구독이 여러 개인 경우 리소스가 과금되어야 할 적절한 구독을 선택합니다. [az account set](/cli/azure/account#az_account_set) 명령을 사용하여 계정에 속한 특정 구독 ID를 선택합니다. 구독에 대한 **az login** 출력의 **id** 속성을 구독 ID 자리 표시자로 바꿉니다.

- 계정에는 가상 네트워크 및 서비스 엔드포인트를 만드는 데 필요한 사용 권한이 있어야 합니다.

가상 네트워크에 대한 쓰기 액세스 권한이 있는 사용자는 가상 네트워크에서 독립적으로 서비스 엔드포인트를 구성할 수 있습니다.

VNet에 대한 Azure 서비스 리소스를 보호하려면 사용자는 추가되는 서브넷의 "Microsoft.Network/JoinServicetoaSubnet"에 대한 사용 권한이 있어야 합니다. 이 권한은 기본적으로 기본 제공 서비스 관리자 역할에 포함되고 사용자 지정 역할을 만들어서 수정될 수 있습니다.

[기본 제공 역할](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles) 및 [사용자 지정 역할](https://docs.microsoft.com/azure/active-directory/role-based-access-control-custom-roles)에 특정 권한 할당에 대해 자세히 알아보세요.

VNet 및 Azure 서비스 리소스가 동일한 구독이나 다른 구독에 있을 수 있습니다. VNet 및 Azure 서비스 리소스가 다른 구독에 있는 경우 이 미리 보기에서는 리소스가 동일한 AD(Active Directory) 테넌트 아래에 있어야 합니다.

> [!IMPORTANT]
> 아래 샘플 스크립트를 실행하거나 서비스 엔드포인트를 구성하기 전에 서비스 엔드포인트 및 고려 사항에 대한 이 문서를 읽어보는 것이 매우 좋습니다. **Virtual Network 서비스 엔드포인트:** [Virtual Network 서비스 엔드포인트](../virtual-network/virtual-network-service-endpoints-overview.md)는 속성 값에 하나 이상의 정식 Azure 서비스 유형 이름이 포함된 서브넷입니다. VNet 서비스 엔드포인트는 SQL Database라는 Azure 서비스를 나타내는 서비스 형식 이름 **Microsoft.Sql**을 사용합니다. 이 서비스 태그는 Azure SQL Database, Azure Database for PostgreSQL 및 MySQL 서비스에도 적용됩니다. **Microsoft.Sql** 서비스 태그를 VNet 서비스 엔드포인트에 적용하는 경우 Azure SQL Database, Azure Database for PostgreSQL 및 Azure Database for MySQL을 포함하는 모든 Azure Database 서비스에 대한 서비스 엔드포인트 트래픽을 서브넷에서 구성합니다. 
> 

### <a name="sample-script-to-create-an-azure-database-for-postgresql-database-create-a-vnet-vnet-service-endpoint-and-secure-the-server-to-the-subnet-with-a-vnet-rule"></a>Azure Database for PostgreSQL 데이터베이스를 만들고, VNet, VNet 서비스 엔드포인트를 만들고 VNet 규칙으로 서브넷에 대한 서버를 보호하는 샘플 스크립트
이 샘플 스크립트에서 강조 표시된 줄을 변경하여 관리자 사용자 이름 및 암호를 사용자 지정합니다. `az account set --subscription` 명령에 사용된 구독 ID를 자신의 구독 ID로 바꿉니다.
[!code-azurecli-interactive[main](../../cli_scripts/postgresql/create-postgresql-server-vnet/create-postgresql-server.sh?highlight=5,20 "Create an Azure Database for PostgreSQL, VNet, VNet service endpoint, and VNet rule.")]

## <a name="clean-up-deployment"></a>배포 정리
스크립트 샘플을 실행한 후에 다음 명령을 사용하여 리소스 그룹 및 관련된 모든 리소스를 제거할 수 있습니다.
[!code-azurecli-interactive[main](../../cli_scripts/postgresql/create-postgresql-server-vnet/delete-postgresql.sh "Delete the resource group.")]
