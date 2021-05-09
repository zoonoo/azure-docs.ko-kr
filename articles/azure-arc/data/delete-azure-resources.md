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
ms.openlocfilehash: ce46b7afe7344fabde03805dc2a0977411be5811
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/07/2021
ms.locfileid: "107716082"
---
# <a name="delete-resources-from-azure"></a>Azure에서 리소스 삭제

이 문서에서는 Azure에서 리소스를 삭제하는 방법을 설명합니다.

> [!WARNING]
> 이 문서에 설명된 대로 리소스를 삭제하면 이러한 작업은 되돌릴 수 없습니다.

간접 연결 모드에서는 Kubernetes에서 인스턴스를 삭제해도 Azure에서는 인스턴스가 제거되지 않으며 반대로 Azure에서 인스턴스를 삭제해도 Kubernetes에서는 제거되지 않습니다. 간접 연결 모드의 경우 리소스를 삭제하는 과정은 두 단계로 진행되며 나중에 개선될 예정입니다. Kubernetes는 신뢰할 수 있는 원본이 되며 포털은 이를 반영하도록 업데이트됩니다.

경우에 따라 Azure에서 Azure Arc 지원 데이터 서비스 리소스를 수동으로 삭제해야 할 수도 있습니다.  다음 옵션 중 하나를 사용하여 이러한 리소스를 삭제할 수 있습니다.

- [Azure에서 리소스 삭제](#delete-resources-from-azure)
  - [전체 리소스 그룹 삭제](#delete-an-entire-resource-group)
  - [리소스 그룹에서 특정 리소스 삭제](#delete-specific-resources-in-the-resource-group)
  - [Azure CLI를 사용하여 리소스 삭제](#delete-resources-using-the-azure-cli)
    - [Azure CLI를 사용하여 SQL 관리되는 인스턴스 리소스를 삭제](#delete-sql-managed-instance-resources-using-the-azure-cli)
    - [Azure CLI를 사용하여 PostgreSQL 하이퍼스케일 서버 그룹 리소스 삭제](#delete-postgresql-hyperscale-server-group-resources-using-the-azure-cli)
    - [Azure CLI를 사용하여 Azure Arc 데이터 컨트롤러 리소스 삭제](#delete-azure-arc-data-controller-resources-using-the-azure-cli)
    - [Azure CLI를 사용하여 리소스 그룹 삭제](#delete-a-resource-group-using-the-azure-cli)

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="delete-an-entire-resource-group"></a>전체 리소스 그룹 삭제

Azure Arc 지원 데이터 서비스를 위해 특정 전용 리소스 그룹을 사용 중이고, 리소스 그룹 내의 *모든 항목* 을 삭제하려는 경우 해당 리소스 그룹 내 모든 항목을 삭제하는 리소스 그룹을 삭제할 수 있습니다.  

다음을 수행하여 Azure Portal의 리소스 그룹을 삭제할 수 있습니다.

- Azure Arc 지원 데이터 서비스 리소스가 생성된 Azure Portal의 리소스 그룹으로 이동합니다.
- **리소스 그룹 삭제** 단추를 클릭합니다.
- 리소스 그룹의 이름을 입력하여 삭제를 확인하고 **삭제** 를 선택합니다.

## <a name="delete-specific-resources-in-the-resource-group"></a>리소스 그룹에서 특정 리소스를 삭제합니다.

다음을 수행하여 Azure Portal의 리소스 그룹에서 특정 Azure Arc 지원이 가능한 데이터 서비스 리소스를 삭제할 수 있습니다.

- Azure Arc 지원 데이터 서비스 리소스가 생성된 Azure Portal의 리소스 그룹으로 이동합니다.
- 삭제할 모든 리소스를 선택합니다.
- 삭제 단추를 클릭합니다.
- 'yes'를 입력하여 삭제를 확인하고 **삭제** 를 클릭합니다.

## <a name="delete-resources-using-the-azure-cli"></a>Azure CLI를 사용하여 리소스 삭제

Azure CLI를 사용하여 특정 Azure Arc 지원 데이터 서비스 리소스를 삭제할 수 있습니다.

### <a name="delete-sql-managed-instance-resources-using-the-azure-cli"></a>Azure CLI를 사용하여 SQL 관리되는 인스턴스 리소스를 삭제합니다.

Azure CLI를 사용하여 Azure에서 SQL 관리되는 인스턴스 리소스를 삭제하려면 아래 명령에서 자리 표시자 값을 바꾸고 실행합니다.

```azurecli
az resource delete --name <sql instance name> --resource-type Microsoft.AzureArcData/sqlManagedInstances --resource-group <resource group name>

#Example
#az resource delete --name sql1 --resource-type Microsoft.AzureArcData/sqlManagedInstances --resource-group rg1
```

### <a name="delete-postgresql-hyperscale-server-group-resources-using-the-azure-cli"></a>Azure CLI를 사용하여 PostgreSQL 하이퍼스케일 서버 그룹 리소스를 삭제합니다.

Azure CLI를 사용하여 Azure에서 PostgreSQL 하이퍼스케일 서버 그룹 리소스를 삭제하려면 아래 명령에서 자리 표시자 값을 바꾸고 실행합니다.

```azurecli
az resource delete --name <postgresql instance name> --resource-type Microsoft.AzureArcData/postgresInstances --resource-group <resource group name>

#Example
#az resource delete --name pg1 --resource-type Microsoft.AzureArcData/postgresInstances --resource-group rg1
```

### <a name="delete-azure-arc-data-controller-resources-using-the-azure-cli"></a>Azure CLI를 사용하여 Azure Arc 데이터 컨트롤러 리소스 삭제

> [!NOTE]
> Azure Arc 데이터 컨트롤러를 삭제하기 전에 관리 중인 데이터베이스 인스턴스 리소스를 모두 삭제해야 합니다.

Azure CLI를 사용하여 Azure에서 Azure Arc 데이터 컨트롤러를 삭제하려면 아래 명령에서 자리 표시자 값을 바꾸고 실행합니다.

```azurecli
az resource delete --name <data controller name> --resource-type Microsoft.AzureArcData/dataControllers --resource-group <resource group name>

#Example
#az resource delete --name dc1 --resource-type Microsoft.AzureArcData/dataControllers --resource-group rg1
```

### <a name="delete-a-resource-group-using-the-azure-cli"></a>Azure CLI를 사용하여 리소스 그룹 삭제

Azure CLI를 사용하여 [리소스 그룹을 삭제](../../azure-resource-manager/management/delete-resource-group.md)할 수도 있습니다.