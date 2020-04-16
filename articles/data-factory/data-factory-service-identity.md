---
title: Data Factory에 대한 관리 ID
description: Azure 데이터 팩터리의 관리ID에 대해 자세히 알아봅니다.
services: data-factory
author: linda33wj
manager: shwang
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/16/2020
ms.author: jingwang
ms.openlocfilehash: d47450f3252074d3bae8df97766bf8858fca5972
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416590"
---
# <a name="managed-identity-for-data-factory"></a>Data Factory에 대한 관리 ID

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

이 문서에서는 데이터 팩터리(이전의 관리 서비스 ID/MSI)에 대해 관리되는 ID와 작동 방식을 이해하는 데 도움이 됩니다.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>개요

데이터 팩터리를 만들 때 팩터리 생성과 함께 관리되는 ID를 만들 수 있습니다. 관리되는 ID는 Azure Active Directory에 등록된 관리되는 응용 프로그램이며 이 특정 데이터 팩터리를 나타냅니다.

데이터 팩터리에 대한 관리되는 ID는 다음과 같은 기능을 제공합니다.

- [Azure Key Vault에 자격 증명을 저장하며,](store-credentials-in-key-vault.md)이 경우 데이터 팩터리 관리 ID가 Azure Key Vault 인증에 사용됩니다.
- [Azure Blob Storage](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure SQL Database](connector-azure-sql-database.md) 및 [Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md)를 포함하는 커넥터.
- [웹 활동](control-flow-web-activity.md).

## <a name="generate-managed-identity"></a>관리되는 ID 생성

데이터 팩터리에 대한 관리ID는 다음과 같이 생성됩니다.

- **Azure 포털 또는 PowerShell을**통해 데이터 팩터리를 만들 때 관리되는 ID는 항상 자동으로 만들어집니다.
- **SDK를**통해 데이터 팩터리를 만들 때 관리되는 ID는 생성할 팩터리 개체에 "ID = 새 FactoryIdentity()"를 지정하는 경우에만 만들어집니다. [.NET 빠른 시작 - 데이터 팩터리 만들기](quickstart-create-data-factory-dot-net.md#create-a-data-factory)에서 예제를 참조하세요.
- **REST API를**통해 데이터 팩터리를 만들 때 관리되는 ID는 요청 본문에 "ID" 섹션을 지정하는 경우에만 만들어집니다. [REST 빠른 시작 - 데이터 팩터리 만들기](quickstart-create-data-factory-rest-api.md#create-a-data-factory)에서 예제를 참조하세요.

데이터 팩터리에 [다음 검색 관리](#retrieve-managed-identity) ID 명령과 관련된 관리 ID가 없는 경우 ID 리티튜터를 프로그래밍 방식으로 업데이트하여 데이터 팩터리를 명시적으로 생성할 수 있습니다.

- [PowerShell을 사용하여 관리되는 ID 생성](#generate-managed-identity-using-powershell)
- [REST API를 사용하여 관리되는 ID 생성](#generate-managed-identity-using-rest-api)
- [Azure 리소스 관리자 템플릿을 사용하여 관리되는 ID 생성](#generate-managed-identity-using-an-azure-resource-manager-template)
- [SDK를 사용하여 관리되는 ID 생성](#generate-managed-identity-using-sdk)

>[!NOTE]
>- 관리되는 ID는 수정할 수 없습니다. 이미 관리되는 ID가 있는 데이터 팩터리 업데이트는 아무영향도 미치지 않고 관리되는 ID는 변경되지 않습니다.
>- 팩터리 개체에 "ID" 매개 변수를 지정하지 않고 또는 REST 요청 본문에 "ID" 섹션을 지정하지 않고 이미 관리되는 ID가 있는 데이터 팩터리를 업데이트하면 오류가 발생합니다.
>- 데이터 팩터리를 삭제하면 연결된 관리 ID가 함께 삭제됩니다.

### <a name="generate-managed-identity-using-powershell"></a>PowerShell을 사용하여 관리되는 ID 생성

**Set-AzDataFactoryV2** 명령을 다시 호출하면 "ID" 필드가 새로 생성되는 것을 볼 수 있습니다.

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

### <a name="generate-managed-identity-using-rest-api"></a>REST API를 사용하여 관리되는 ID 생성

요청 본문의 "identity" 섹션에서 아래 API를 호출합니다.

```
PATCH https://management.azure.com/subscriptions/<subsID>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<data factory name>?api-version=2018-06-01
```

**요청 본문**: "identity": { "type": "SystemAssigned" }를 추가합니다.

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

**응답**: 관리되는 ID가 자동으로 만들어지고 그에 따라 "ID" 섹션이 채워집니다.

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

### <a name="generate-managed-identity-using-an-azure-resource-manager-template"></a>Azure 리소스 관리자 템플릿을 사용하여 관리되는 ID 생성

**템플릿**: "identity": { "type": "SystemAssigned" }를 추가합니다.

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

### <a name="generate-managed-identity-using-sdk"></a>SDK를 사용하여 관리되는 ID 생성

Identity=new FactoryIdentity()를 사용하여 데이터 팩터리 create_or_update 함수를 호출합니다. .NET을 사용하는 샘플 코드:

```csharp
Factory dataFactory = new Factory
{
    Location = <region>,
    Identity = new FactoryIdentity()
};
client.Factories.CreateOrUpdate(resourceGroup, dataFactoryName, dataFactory);
```

## <a name="retrieve-managed-identity"></a>관리되는 ID 검색

Azure 포털에서 또는 프로그래밍 방식으로 관리되는 ID를 검색할 수 있습니다. 다음 섹션에서는 몇 가지 샘플을 보여 줍니다.

>[!TIP]
> 관리되는 ID가 표시되지 않으면 팩터리를 업데이트하여 [관리되는 ID를 생성합니다.](#generate-managed-identity)

### <a name="retrieve-managed-identity-using-azure-portal"></a>Azure 포털을 사용하여 관리되는 ID 검색

Azure 포털 -> 데이터 팩터리 -> 속성에서 관리되는 ID 정보를 찾을 수 있습니다.

- 관리되는 ID 개체 ID
- 관리되는 ID 테넌트
- 관리되는 ID 응용 프로그램 ID

Azure Blob, Azure Data Lake Storage, Azure Key Vault 등과 같이 관리되는 ID 인증을 지원하는 연결된 서비스를 만들 때도 관리되는 ID 정보가 표시됩니다.

권한을 부여할 때 개체 ID 또는 데이터 팩터리 이름(관리되는 ID 이름)을 사용하여 이 ID를 찾습니다.

### <a name="retrieve-managed-identity-using-powershell"></a>PowerShell을 사용하여 관리되는 ID 검색

관리되는 ID 주체 ID 와 테넌트 ID는 다음과 같이 특정 데이터 팩터리를 받으면 반환됩니다. **PrincipalId를** 사용하여 액세스 권한을 부여합니다.

```powershell
PS C:\WINDOWS\system32> (Get-AzDataFactoryV2 -ResourceGroupName <resourceGroupName> -Name <dataFactoryName>).Identity

PrincipalId                          TenantId
-----------                          --------
765ad4ab-XXXX-XXXX-XXXX-51ed985819dc 72f988bf-XXXX-XXXX-XXXX-2d7cd011db47
```

주 체 ID 위에 복사한 다음 주 체ID를 매개 변수로 사용하는 Azure Active Directory 명령 아래에서 실행하여 응용 프로그램 ID를 얻을 수 있습니다.

```powershell
PS C:\WINDOWS\system32> Get-AzADServicePrincipal -ObjectId 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc

ServicePrincipalNames : {76f668b3-XXXX-XXXX-XXXX-1b3348c75e02, https://identity.azure.net/P86P8g6nt1QxfPJx22om8MOooMf/Ag0Qf/nnREppHkU=}
ApplicationId         : 76f668b3-XXXX-XXXX-XXXX-1b3348c75e02
DisplayName           : ADFV2DemoFactory
Id                    : 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc
Type                  : ServicePrincipal
```

## <a name="next-steps"></a>다음 단계
데이터 팩터리 관리 ID를 사용하는 시기와 방법을 소개하는 다음 항목을 참조하십시오.

- [Azure Key Vault에 자격 증명 저장](store-credentials-in-key-vault.md)
- [Azure 리소스 인증을 위해 관리 ID를 사용하여 Azure Data Lake Store 간에 데이터 복사](connector-azure-data-lake-store.md)

[Azure 리소스에 대한 관리되는 ID 개요를](/azure/active-directory/managed-identities-azure-resources/overview) 참조하여 관리되는 Azure 리소스에 대한 관리되는 ID에 대한 자세한 배경 정보(데이터 팩터리 관리 ID를 기반으로 하는 데이터 팩터리 ID)를 참조하세요. 
