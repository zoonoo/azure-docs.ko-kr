---
title: Azure에서 리소스 삭제
description: Azure에서 리소스 삭제
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 46c1df8a175657e20dce984d0c8825ae99170246
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91761654"
---
# <a name="delete-resources-from-azure"></a>Azure에서 리소스 삭제

> [!NOTE]
>  이 문서에서 리소스를 삭제 하는 옵션은 되돌릴 수 없습니다.

> [!NOTE]
>  Azure Arc 사용 가능 데이터 서비스에 대해 제공 되는 연결 모드는 현재 간접 연결 모드 이기 때문에 Kubernetes에서 인스턴스를 삭제 해도 Azure에서 제거 되지 않고 Azure에서 인스턴스를 삭제 해도 Kubernetes에서 제거 되지 않습니다.  현재 리소스를 삭제 하는 과정은 두 단계로 진행 되며 나중에 개선 될 예정입니다.  앞으로 Kubernetes는 사실 이며 Azure가이를 반영 하도록 업데이트 됩니다.

경우에 따라 ARM (Azure Resource Manager)에서 Azure Arc 사용 가능 데이터 서비스 리소스를 수동으로 삭제 해야 할 수도 있습니다.  다음 옵션 중 하나를 사용 하 여 이러한 리소스를 삭제할 수 있습니다.

- [Azure에서 리소스 삭제](#delete-resources-from-azure)
  - [전체 리소스 그룹을 삭제 합니다.](#delete-an-entire-resource-group)
  - [리소스 그룹에서 특정 리소스를 삭제 합니다.](#delete-specific-resources-in-the-resource-group)
  - [Azure CLI를 사용 하 여 리소스 삭제](#delete-resources-using-the-azure-cli)
    - [Azure CLI를 사용 하 여 SQL 관리 되는 인스턴스 리소스를 삭제 합니다.](#delete-sql-managed-instance-resources-using-the-azure-cli)
    - [Azure CLI를 사용 하 여 PostgreSQL Hyperscale server 그룹 리소스를 삭제 합니다.](#delete-postgresql-hyperscale-server-group-resources-using-the-azure-cli)
    - [Azure CLI를 사용 하 여 Azure Arc 데이터 컨트롤러 리소스 삭제](#delete-azure-arc-data-controller-resources-using-the-azure-cli)
    - [Azure CLI를 사용 하 여 리소스 그룹 삭제](#delete-a-resource-group-using-the-azure-cli)

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="delete-an-entire-resource-group"></a>전체 리소스 그룹을 삭제 합니다.
Azure Arc의 전용 리소스 그룹을 사용 하 여 데이터 서비스를 사용 중이 고 리소스 그룹 내의 *모든 항목* 을 삭제 하려는 경우 해당 리소스 그룹 내 모든 항목을 삭제 하는 리소스 그룹을 삭제할 수 있습니다.  

다음을 수행 하 여 Azure Portal에서 리소스 그룹을 삭제할 수 있습니다.

- Azure Arc 사용 데이터 서비스 리소스가 생성 된 Azure Portal에서 리소스 그룹으로 이동 합니다.
- **리소스 그룹 삭제** 단추를 클릭 합니다.
- 리소스 그룹 이름을 입력 하 여 삭제를 확인 하 고 **삭제**를 클릭 합니다.

## <a name="delete-specific-resources-in-the-resource-group"></a>리소스 그룹에서 특정 리소스를 삭제 합니다.

다음을 수행 하 여 Azure Portal의 리소스 그룹에서 특정 Azure Arc 사용 가능 데이터 서비스 리소스를 삭제할 수 있습니다.

- Azure Arc 사용 데이터 서비스 리소스가 생성 된 Azure Portal에서 리소스 그룹으로 이동 합니다.
- 삭제할 모든 리소스를 선택 합니다.
- 삭제 단추를 클릭 합니다.
- ' 예 '를 입력 하 여 삭제를 확인 하 고 **삭제**를 클릭 합니다.

## <a name="delete-resources-using-the-azure-cli"></a>Azure CLI를 사용 하 여 리소스 삭제

Azure CLI를 사용 하 여 특정 Azure Arc 사용 데이터 서비스 리소스를 삭제할 수 있습니다.

### <a name="delete-sql-managed-instance-resources-using-the-azure-cli"></a>Azure CLI를 사용 하 여 SQL 관리 되는 인스턴스 리소스를 삭제 합니다.

Azure CLI를 사용 하 여 Azure에서 SQL 관리 되는 인스턴스 리소스를 삭제 하려면 아래 명령에서 자리 표시자 값을 바꾸고 실행 합니다.

```console
az resource delete --name <sql instance name> --resource-type Microsoft.AzureData/sqlManagedInstances --resource-group <resource group name>

#Example
#az resource delete --name sql1 --resource-type Microsoft.AzureData/sqlManagedInstances --resource-group rg1
```

### <a name="delete-postgresql-hyperscale-server-group-resources-using-the-azure-cli"></a>Azure CLI를 사용 하 여 PostgreSQL Hyperscale server 그룹 리소스를 삭제 합니다.

Azure CLI를 사용 하 여 Azure에서 PostgreSQL Hyperscale server 그룹 리소스를 삭제 하려면 아래 명령에서 자리 표시자 값을 바꾸고 실행 합니다.

```console
az resource delete --name <postgresql instance name> --resource-type Microsoft.AzureData/postgresInstances --resource-group <resource group name>

#Example
#az resource delete --name pg1 --resource-type Microsoft.AzureData/postgresInstances --resource-group rg1
```

### <a name="delete-azure-arc-data-controller-resources-using-the-azure-cli"></a>Azure CLI를 사용 하 여 Azure Arc 데이터 컨트롤러 리소스 삭제

> [!NOTE]
> Azure Arc 데이터 컨트롤러를 삭제 하기 전에 관리 중인 데이터베이스 인스턴스 리소스를 모두 삭제 해야 합니다.

Azure CLI 사용 하 여 Azure에서 Azure Arc 데이터 컨트롤러를 삭제 하려면 아래 명령에서 자리 표시자 값을 바꾸고 실행 합니다.

```console
az resource delete --name <data controller name> --resource-type Microsoft.AzureData/dataControllers --resource-group <resource group name>

#Example
#az resource delete --name dc1 --resource-type Microsoft.AzureData/dataControllers --resource-group rg1
```

### <a name="delete-a-resource-group-using-the-azure-cli"></a>Azure CLI를 사용 하 여 리소스 그룹 삭제

또한 Azure CLI를 사용 하 여 [리소스 그룹을 삭제할](/azure/azure-resource-manager/management/delete-resource-group)수 있습니다.
