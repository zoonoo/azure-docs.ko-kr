---
title: 가상 네트워크 규칙 사용 - Azure CLI - Azure Database for PostgreSQL - 단일 서버
description: 이 문서에서는 Azure CLI 명령줄을 사용하여 Azure Database for PostgreSQL용 VNet 서비스 엔드포인트 및 규칙을 만들고 관리하는 방법을 설명합니다.
author: niklarin
ms.author: nlarin
ms.service: postgresql
ms.devlang: azurecli
ms.topic: how-to
ms.date: 5/6/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: 07e0f7cf2834b3984d9207fa18f3b0e32340e216
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94660885"
---
# <a name="create-and-manage-vnet-service-endpoints-for-azure-database-for-postgresql---single-server-using-azure-cli"></a>Azure Database for PostgreSQL용 VNet 서비스 엔드포인트 만들기 및 관리 - Azure CLI를 사용하여 단일 서버
VNet(가상 네트워크) 서비스 엔드포인트 및 규칙이 가상 네트워크의 프라이빗 주소 공간을 Azure Database for PostgreSQL 서버로 확장합니다. 편리한 Azure CLI(명령줄 인터페이스) 명령을 사용하면 서버를 관리하는 VNet 서비스 엔드포인트 및 규칙을 만들고, 업데이트하고, 삭제하며, 표시할 수 있습니다. 제한을 포함하여 Azure Database for PostgreSQL VNet 서비스 엔드포인트에 대한 개요는 [Azure Database for PostgreSQL 서버 VNet 서비스 엔드포인트](concepts-data-access-and-security-vnet.md)를 참조하세요. VNet 서비스 엔드포인트는 Azure Database for PostgreSQL에 대한 지원되는 모든 지역에서 사용할 수 있습니다.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>필수 구성 요소
이 방법 가이드를 단계별로 실행 하려면 다음을 수행 합니다.
- [Azure CLI](/cli/azure/install-azure-cli)를 설치하거나 브라우저에서 Azure Cloud Shell을 사용합니다.
- [Azure Database for PostgreSQL 서버와 데이터베이스](quickstart-create-server-database-azure-cli.md)를 만듭니다.

    > [!NOTE]
    > VNet 서비스 엔드포인트는 범용 및 메모리 최적화 서버에 대해서만 지원됩니다.
    > VNet 피어링의 경우 서비스 엔드포인트가 있는 공통 VNet 게이트웨이를 통해 트래픽이 이동하며, 피어로 이동되어야 하는 경우 게이트웨이 VNet의 Azure Virtual Machines가 Azure Database for PostgreSQL 서버에 액세스할 수 있도록 허용하는 ACL/VNet 규칙을 만드세요.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- 이 문서에는 Azure CLI 버전 2.0 이상이 필요 합니다. Azure Cloud Shell을 사용하는 경우 최신 버전이 이미 설치되어 있습니다.

## <a name="configure-vnet-service-endpoints-for-azure-database-for-postgresql"></a>Azure Database for PostgreSQL에 대한 Vnet 서비스 엔드포인트 구성
가상 네트워크를 구성하는 데 [az network vnet](/cli/azure/network/vnet) 명령이 사용됩니다.

구독이 여러 개인 경우 리소스가 과금되어야 할 적절한 구독을 선택합니다. [az account set](/cli/azure/account#az-account-set) 명령을 사용하여 계정에 속한 특정 구독 ID를 선택합니다. 구독에 대한 **az login** 출력의 **id** 속성을 구독 ID 자리 표시자로 바꿉니다.

- 계정에는 가상 네트워크 및 서비스 엔드포인트를 만드는 데 필요한 사용 권한이 있어야 합니다.

가상 네트워크에 대한 쓰기 액세스 권한이 있는 사용자는 가상 네트워크에서 독립적으로 서비스 엔드포인트를 구성할 수 있습니다.

VNet에 대한 Azure 서비스 리소스를 보호하려면 사용자는 추가되는 서브넷의 "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/"에 대한 사용 권한을 갖고 있어야 합니다. 이 권한은 기본적으로 기본 제공 서비스 관리자 역할에 포함되고 사용자 지정 역할을 만들어서 수정될 수 있습니다.

[기본 제공 역할](../role-based-access-control/built-in-roles.md) 및 [사용자 지정 역할](../role-based-access-control/custom-roles.md)에 특정 권한 할당에 대해 자세히 알아보세요.

VNet 및 Azure 서비스 리소스가 동일한 구독이나 다른 구독에 있을 수 있습니다. VNet 및 Azure 서비스 리소스가 서로 다른 구독에 있는 경우 리소스가 동일한 AD(Active Directory) 테넌트에 있어야 합니다. 두 구독 모두에 **Microsoft .Sql** 리소스 공급자가 등록되어 있는지 확인합니다. 자세한 내용은 [resource manager-등록][resource-manager-portal]을 참조 하세요.

> [!IMPORTANT]
> 아래 샘플 스크립트를 실행하거나 서비스 엔드포인트를 구성하기 전에 서비스 엔드포인트 및 고려 사항에 대한 이 문서를 읽어보는 것이 매우 좋습니다. **Virtual Network 서비스 엔드포인트:** [Virtual Network 서비스 엔드포인트](../virtual-network/virtual-network-service-endpoints-overview.md)는 속성 값에 하나 이상의 정식 Azure 서비스 유형 이름이 포함된 서브넷입니다. VNet 서비스 엔드포인트는 SQL Database라는 Azure 서비스를 나타내는 서비스 형식 이름 **Microsoft.Sql** 을 사용합니다. 이 서비스 태그는 Azure SQL Database, Azure Database for PostgreSQL 및 MySQL 서비스에도 적용됩니다. **Microsoft.Sql** 서비스 태그를 VNet 서비스 엔드포인트에 적용하는 경우 Azure SQL Database, Azure Database for PostgreSQL 및 Azure Database for MySQL을 포함하는 모든 Azure Database 서비스에 대한 서비스 엔드포인트 트래픽을 서브넷에서 구성합니다. 
> 

### <a name="sample-script-to-create-an-azure-database-for-postgresql-database-create-a-vnet-vnet-service-endpoint-and-secure-the-server-to-the-subnet-with-a-vnet-rule"></a>Azure Database for PostgreSQL 데이터베이스를 만들고, VNet, VNet 서비스 엔드포인트를 만들고 VNet 규칙으로 서브넷에 대한 서버를 보호하는 샘플 스크립트
이 샘플 스크립트에서 강조 표시된 줄을 변경하여 관리자 사용자 이름 및 암호를 사용자 지정합니다. `az account set --subscription` 명령에 사용된 구독 ID를 자신의 구독 ID로 바꿉니다.
[!code-azurecli-interactive[main](../../cli_scripts/postgresql/create-postgresql-server-vnet/create-postgresql-server.sh?highlight=5,20 "Create an Azure Database for PostgreSQL, VNet, VNet service endpoint, and VNet rule.")]

## <a name="clean-up-deployment"></a>배포 정리
스크립트 샘플을 실행한 후에 다음 명령을 사용하여 리소스 그룹 및 관련된 모든 리소스를 제거할 수 있습니다.
[!code-azurecli-interactive[main](../../cli_scripts/postgresql/create-postgresql-server-vnet/delete-postgresql.sh "Delete the resource group.")]

<!-- Link references, to text, Within this same GitHub repo. --> 
[resource-manager-portal]: ../azure-resource-manager/management/resource-providers-and-types.md
