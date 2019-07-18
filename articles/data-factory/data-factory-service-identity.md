---
title: Data Factory에 대 한 관리 id | Microsoft Docs
description: Azure Data Factory에 대 한 관리 되는 id에 알아봅니다.
services: data-factory
author: linda33wj
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: jingwang
ms.openlocfilehash: 3c1bb38eb12ce77d172257706cd458cebda4bd8c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66153415"
---
# <a name="managed-identity-for-data-factory"></a>Data Factory에 대한 관리 ID

이 문서에서는 관리 되는 id 수 있는 항목 이해 (이전에으로 관리 되는 서비스/MSI Id)는 Data Factory 및 작동 방식에 대 한 합니다.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>개요

데이터 팩터리를 만들 때 팩터리가 만들어지면서 관리 id는 만들 수 있습니다. 관리 되는 id는 Azure Activity Directory에 등록 된 관리 되는 응용 프로그램 및이 특정 데이터 팩터리를 나타냅니다.

Data Factory에 대 한 관리 되는 id에는 다음 기능 이점:

- [Azure Key Vault에 자격 증명 저장](store-credentials-in-key-vault.md), Azure Key Vault 인증에 대 한 data factory 관리 id가 사용 하는 경우.
- [Azure Blob Storage](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure SQL Database](connector-azure-sql-database.md) 및 [Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md)를 포함하는 커넥터.
- [웹 작업](control-flow-web-activity.md)

## <a name="generate-managed-identity"></a>관리 되는 id를 생성 합니다.

Data Factory에 대 한 관리 되는 id는 다음과 같이 생성 됩니다.

- 통해 데이터 팩터리를 만들 때 **Azure portal 또는 PowerShell**관리 되는 id를 자동으로 항상 생성 됩니다.
- 통해 데이터 팩터리를 만들 때 **SDK**관리 되는 id가 지정 하는 경우에 생성 됩니다 "Identity = new factoryidentity ()"를 만들기 위한 팩터리 개체에 있습니다. [.NET 빠른 시작 - 데이터 팩터리 만들기](quickstart-create-data-factory-dot-net.md#create-a-data-factory)에서 예제를 참조하세요.
- 통해 데이터 팩터리를 만들 때 **REST API**관리 되는 요청 본문에 "identity" 섹션을 지정 하는 경우에 id가 생성 됩니다. [REST 빠른 시작 - 데이터 팩터리 만들기](quickstart-create-data-factory-rest-api.md#create-a-data-factory)에서 예제를 참조하세요.

데이터 팩터리에 다음 연결 된 관리 되는 id가 없습니다 경우 찾을 [관리 되는 id를 검색할](#retrieve-managed-identity) 명령을 생성할 수 있습니다 명시적으로 id 초기자를 사용 하 여 데이터 팩터리를 프로그래밍 방식으로 업데이트 하 여:

- [PowerShell을 사용 하 여 관리 되는 id를 생성 합니다.](#generate-managed-identity-using-powershell)
- [REST API를 사용 하 여 관리 되는 id를 생성 합니다.](#generate-managed-identity-using-rest-api)
- [Azure Resource Manager 템플릿을 사용 하는 관리 되는 id를 생성 합니다.](#generate-managed-identity-using-an-azure-resource-manager-template)
- [SDK를 사용 하 여 관리 되는 id를 생성 합니다.](#generate-managed-identity-using-sdk)

>[!NOTE]
>- 관리 되는 id는 수정할 수 없습니다. 영향은 없으며 관리 되는 id에 이미 있는 데이터 팩터리를 업데이트, 관리 되는 id는 변경 되지 않은 상태로 유지 됩니다.
>- 팩터리 개체에 "identity" 매개 변수를 지정 하지 않거나 REST 요청 본문에 "identity" 섹션을 지정 하지 않고 이미 관리 되는 id가 있는 데이터 팩터리를 업데이트 하는 경우 오류가 발생 합니다.
>- 데이터 팩터리를 삭제 하면 연결 된 관리 되는 id가 함께 삭제 됩니다.

### <a name="generate-managed-identity-using-powershell"></a>PowerShell을 사용 하 여 관리 되는 id를 생성 합니다.

호출 **집합 AzDataFactoryV2** 명령을 다시 새로 생성 된 "Identity" 필드가 표시:

```powershell
PS C:\WINDOWS\system32> Set-AzDataFactoryV2 -ResourceGroupName <resourceGroupName> -Name <dataFactoryName> -Location <region>

DataFactoryName   : ADFV2DemoFactory
DataFactoryId     : /subscriptions/<subsID>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/ADFV2DemoFactory
ResourceGroupName : <resourceGroupName>
Location          : East US
Tags              : {}
Identity          : Microsoft.Azure.Management.DataFactory.Models.FactoryIdentity
ProvisioningState : Succeeded
```

### <a name="generate-managed-identity-using-rest-api"></a>REST API를 사용 하 여 관리 되는 id를 생성 합니다.

요청 본문의 "identity" 섹션에서 아래 API를 호출합니다.

```
PATCH https://management.azure.com/subscriptions/<subsID>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<data factory name>?api-version=2018-06-01
```

**요청 본문**: add "identity": { "type": "SystemAssigned" }.

```json
{
    "name": "<dataFactoryName>",
    "location": "<region>",
    "properties": {},
    "identity": {
        "type": "SystemAssigned"
    }
}
```

**응답**: 관리 되는 id가 자동으로 생성 하 고 "identity" 섹션이 그에 따라 채워집니다.

```json
{
    "name": "<dataFactoryName>",
    "tags": {},
    "properties": {
        "provisioningState": "Succeeded",
        "loggingStorageAccountKey": "**********",
        "createTime": "2017-09-26T04:10:01.1135678Z",
        "version": "2018-06-01"
    },
    "identity": {
        "type": "SystemAssigned",
        "principalId": "765ad4ab-XXXX-XXXX-XXXX-51ed985819dc",
        "tenantId": "72f988bf-XXXX-XXXX-XXXX-2d7cd011db47"
    },
    "id": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/ADFV2DemoFactory",
    "type": "Microsoft.DataFactory/factories",
    "location": "<region>"
}
```

### <a name="generate-managed-identity-using-an-azure-resource-manager-template"></a>Azure Resource Manager 템플릿을 사용 하는 관리 되는 id를 생성 합니다.

**템플릿**: add "identity": { "type": "SystemAssigned" }.

```json
{
    "contentVersion": "1.0.0.0",
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "resources": [{
        "name": "<dataFactoryName>",
        "apiVersion": "2018-06-01",
        "type": "Microsoft.DataFactory/factories",
        "location": "<region>",
        "identity": {
            "type": "SystemAssigned"
        }
    }]
}
```

### <a name="generate-managed-identity-using-sdk"></a>SDK를 사용 하 여 관리 되는 id를 생성 합니다.

Identity=new FactoryIdentity()를 사용하여 데이터 팩터리 create_or_update 함수를 호출합니다. .NET을 사용하는 샘플 코드:

```csharp
Factory dataFactory = new Factory
{
    Location = <region>,
    Identity = new FactoryIdentity()
};
client.Factories.CreateOrUpdate(resourceGroup, dataFactoryName, dataFactory);
```

## <a name="retrieve-managed-identity"></a>관리 되는 id를 검색 합니다.

Azure portal에서 또는 프로그래밍 방식으로 관리 되는 id를 검색할 수 있습니다. 다음 섹션에서는 몇 가지 샘플을 보여 줍니다.

>[!TIP]
> 관리 서비스 id를 표시 되지 않는 경우 [관리 되는 id 생성](#generate-managed-identity) 팩터리를 업데이트 하 여 합니다.

### <a name="retrieve-managed-identity-using-azure-portal"></a>Azure portal을 사용 하 여 관리 되는 id를 검색 합니다.

-> Azure portal에서 관리 되는 id 정보를 찾을 수 있습니다 데이터 팩터리-> 속성:

- 개체 ID를 관리합니다.
- 관리 되는 Id 테 넌 트
- **응용 프로그램 ID를 관리 되는** >이 값을 복사

![관리 되는 id를 검색 합니다.](media/data-factory-service-identity/retrieve-service-identity-portal.png)

### <a name="retrieve-managed-identity-using-powershell"></a>PowerShell을 사용 하 여 관리 되는 id를 검색 합니다.

보안 주체 ID 및 테 넌 트 ID를 관리 id는 다음과 같이 특정 데이터 팩터리를 가져올 때 반환 됩니다.

```powershell
PS C:\WINDOWS\system32> (Get-AzDataFactoryV2 -ResourceGroupName <resourceGroupName> -Name <dataFactoryName>).Identity

PrincipalId                          TenantId
-----------                          --------
765ad4ab-XXXX-XXXX-XXXX-51ed985819dc 72f988bf-XXXX-XXXX-XXXX-2d7cd011db47
```

보안 주체 ID를 복사하고 보안 주체 ID를 매개 변수로 사용하여 아래 Azure Active Directory 명령을 실행합니다. 그러면 액세스 권한 부여하는 데 사용할 **ApplicationId**를 가져올 수 있습니다.

```powershell
PS C:\WINDOWS\system32> Get-AzADServicePrincipal -ObjectId 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc

ServicePrincipalNames : {76f668b3-XXXX-XXXX-XXXX-1b3348c75e02, https://identity.azure.net/P86P8g6nt1QxfPJx22om8MOooMf/Ag0Qf/nnREppHkU=}
ApplicationId         : 76f668b3-XXXX-XXXX-XXXX-1b3348c75e02
DisplayName           : ADFV2DemoFactory
Id                    : 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc
Type                  : ServicePrincipal
```

## <a name="next-steps"></a>다음 단계
소개 하는 경우는 다음 항목을 참조 하 고 data factory를 사용 하는 방법을 관리 id:

- [Azure Key Vault에 자격 증명 저장](store-credentials-in-key-vault.md)
- [Azure 리소스 인증을 위해 관리 ID를 사용하여 Azure Data Lake Store 간에 데이터 복사](connector-azure-data-lake-store.md)

참조 [Azure 리소스 개요에 대 한 Id 관리](/azure/active-directory/managed-identities-azure-resources/overview) 에 Azure 리소스를 관리 되는 id는 data factory에 대 한 관리 되는 id에 대 한 자세한 배경 기반으로 합니다. 
