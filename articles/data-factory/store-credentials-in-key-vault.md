---
title: "Azure Key Vault에 자격 증명 저장 | Microsoft Docs"
description: "Azure Data Factory에서 런타임 시 자동으로 검색할 수 있는 Azure Key Vault에 사용되는 데이터 저장소의 자격 증명을 저장하는 방법을 알아봅니다."
services: data-factory
author: linda33wj
manager: jhubbard
editor: 
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2017
ms.author: jingwang
ms.openlocfilehash: 193d7c77f01384106b3e0d932d02ba6cdff9e750
ms.sourcegitcommit: 43c3d0d61c008195a0177ec56bf0795dc103b8fa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/01/2017
---
# <a name="store-credential-in-azure-key-vault"></a>Azure Key Vault에 자격 증명 저장

[Azure Key Vault](../key-vault/key-vault-whatis.md)에서 데이터 저장소의 자격 증명을 저장할 수 있습니다. Azure Data Factory에서는 데이터 저장소를 사용하는 작업을 실행할 때 자격 증명을 검색합니다. 현재 [Dynamics 커넥터](connector-dynamics-crm-office-365.md)만이 이 기능을 지원합니다.

> [!NOTE]
> 이 문서는 현재 미리 보기 상태인 Data Factory 버전 2에 적용됩니다. 일반 공급(GA)되는 Data Factory 버전 1 서비스를 사용하는 경우 [Data Factory 버전 1 설명서](v1/data-factory-introduction.md)를 참조하세요.

## <a name="steps"></a>단계

데이터 팩터리를 만들 때 팩터리가 만들어지면서 서비스 ID도 생성할 수 있습니다. 서비스 ID는 Azure Activity Directory에 등록된 관리되는 응용 프로그램이고 이 특정 데이터 팩터리를 나타냅니다.

- **Azure Portal 또는 PowerShell**을 통해 데이터 팩터리를 만들 때 서비스 ID가 공개 미리 보기이므로 항상 자동으로 생성됩니다.
- **SDK**를 통해 데이터 팩터리를 만들 때, 생성할 팩터리 개체에서 "Identity = new FactoryIdentity()"를 지정하는 경우에만 서비스 ID가 생성됩니다. [.NET 빠른 시작 - 데이터 팩터리 만들기](quickstart-create-data-factory-dot-net.md#create-a-data-factory)에서 예제를 참조하세요.
- **REST API**를 통해 데이터 팩터리를 만들 때, 요청 본문에 "identity" 섹션을 지정하는 경우에만 서비스 ID가 생성됩니다. [REST 빠른 시작 - 데이터 팩터리 만들기](quickstart-create-data-factory-rest-api.md#create-a-data-factory)에서 예제를 참조하세요.

Azure Key Vault에 저장된 자격 증명을 참조하려면 다음을 수행해야 합니다.

1. 데이터 팩터리와 함께 생성된 "서비스 ID 응용 프로그램 ID"를 복사합니다. [서비스 ID 검색](#retrieve-service-identity)을 참조하세요.
2. Azure Key Vault에 대한 서비스 ID 액세스 권한을 부여합니다. 키 자격 증명 모음 -> 액세스 제어 -> 추가 ->에서 이 서비스 ID 응용 프로그램 ID를 검색하여 읽기 권한자 권한을 추가합니다. 그러면 이 지정된 팩터리가 키 자격 증명 모음에 있는 비밀에 액세스할 수 있습니다.
3. Azure Key Vault를 가리키는 연결된 서비스를 만듭니다. [Azure Key Vault 연결된 서비스](#azure-key-vault-linked-service)를 참조합니다.
4. 해당 비밀을 키 자격 증명 모음에 저장한 참조 내에서 데이터 저장소 연결된 서비스를 만듭니다. [키 자격 증명 모음에 저장된 참조 자격 증명](#reference-credential-stored-in-key-vault)을 참조하세요.

## <a name="retrieve-service-identity"></a>서비스 ID 검색

Azure Portal에서 또는 프로그래밍 방식으로 서비스 ID를 검색할 수 있습니다. 다음 섹션에서는 몇 가지 샘플을 보여 줍니다.

>[!TIP]
> 서비스 ID가 표시되지 않으면 팩터리를 업데이트하여 [서비스 ID를 생성](#generate-service-identity)합니다.

### <a name="using-azure-portal"></a>Azure Portal 사용

Azure Portal -> 데이터 팩터리 -> 설정 -> 속성에서 서비스 ID 정보를 찾을 수 있습니다.

- 서비스 ID ID
- 서비스 ID 테넌트
- **서비스 ID 응용 프로그램 ID** > 이 값을 복사하여 Key Vault에서 액세스 권한 부여

![서비스 ID 검색](media/store-credentials-in-key-vault/retrieve-service-identity-portal.png)

### <a name="using-powershell"></a>PowerShell 사용

다음과 같이 특정 데이터 팩터리를 가져오는 경우 서비스 ID의 보안 주체 ID 및 테넌트 ID가 반환됩니다.

```powershell
PS C:\WINDOWS\system32> (Get-AzureRmDataFactoryV2 -ResourceGroupName <resourceGroupName> -Name <dataFactoryName>).Identity

PrincipalId                          TenantId
-----------                          --------
765ad4ab-XXXX-XXXX-XXXX-51ed985819dc 72f988bf-XXXX-XXXX-XXXX-2d7cd011db47
```

보안 주체 ID를 복사한 다음, Key Vault에서 액세스 권한 부여하는 데 사용할**ApplicationId**를 가져오기 위해, 보안 주체 ID를 매개 변수로 사용하여 아래 Azure Active Directory 명령을 실행합니다.

```powershell
PS C:\WINDOWS\system32> Get-AzureRmADServicePrincipal -ObjectId 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc

ServicePrincipalNames : {76f668b3-XXXX-XXXX-XXXX-1b3348c75e02, https://identity.azure.net/P86P8g6nt1QxfPJx22om8MOooMf/Ag0Qf/nnREppHkU=}
ApplicationId         : 76f668b3-XXXX-XXXX-XXXX-1b3348c75e02
DisplayName           : ADFV2DemoFactory
Id                    : 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc
Type                  : ServicePrincipal
```

## <a name="azure-key-vault-linked-service"></a>Azure Key Vault 연결된 서비스

Azure Key Vault 연결된 서비스에 다음 속성이 지원됩니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 형식 속성은 **AzureKeyVault**로 설정되어야 합니다. | 예 |
| baseUrl | Azure Key Vault URL을 지정합니다. | 예 |

**예제:**

```json
{
    "name": "AzureKeyVaultLinkedService",
    "properties": {
        "type": "AzureKeyVault",
        "typeProperties": {
        "baseUrl": "https://<azureKeyVaultName>.vault.azure.net"
        }
    }
}
```

## <a name="reference-credential-stored-in-key-vault"></a>키 자격 증명 모음에 저장된 참조 자격 증명

키 자격 증명 모음 암호를 참조하는 연결된 서비스에서 필드를 구성할 때 다음 속성이 지원됩니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 필드의 형식 속성을 **AzureKeyVaultSecret**으로 설정해야 합니다. | 예 |
| secretName | Azure Key Vault의 비밀 이름입니다. | 예 |
| secretVersion | Azure Key Vault의 비밀 버전입니다.<br/>지정하지 않으면 항상 최신 버전의 비밀을 사용합니다.<br/>지정하는 경우 지정된 버전을 사용합니다.| 아니요 |
| store | 자격 증명을 저장하는 데 사용하는 Azure Key Vault 연결된 서비스를 나타냅니다. | 예 |

**예: ("암호" 섹션 참조)**

```json
{
    "name": "DynamicsLinkedService",
    "properties": {
        "type": "Dynamics",
        "typeProperties": {
            "deploymentType": "<>",
            "organizationName": "<>",
            "authenticationType": "<>",
            "username": "<>",
            "password": {
                "type": "AzureKeyVaultSecret",
                "secretName": "mySecret",
                "store":{
                    "linkedServiceName": "<Azure Key Vault linked service>",
                    "type": "LinkedServiceReference"
                }
            }
        }
    }
}
```

## <a name="generate-service-identity"></a>서비스 ID 생성

데이터 팩터리에 다음 [서비스 ID 검색](#retrieve-service-identity) 지침과 관련된 서비스 ID가 없는 것이 확인되면 데이터 팩터리를 프로그래밍 방식으로 ID 초기자로 업데이트하여 하나 생성할 수 있습니다.

> [!NOTE]
> - **서비스 ID를 변경할 수 없습니다**. 이미 서비스 ID가 있는 데이터 팩터리를 업데이트해도 영향은 없으며 서비스 ID는 변경되지 않고 유지됩니다.
> - **서비스 ID를 삭제할 수 없습니다**. 팩터리 개체에 "identity" 매개 변수를 지정하지 않거나 REST 요청 본문에 "identity" 섹션을 지정하지 않고 서비스 ID가 이미 있는 데이터 팩터리를 업데이트하면 오류가 발생합니다.

다음 섹션에서는 서비스 ID가 없는 경우 팩터리에 대한 서비스 ID를 생성하는 몇 가지 샘플을 보여 줍니다.

### <a name="using-powershell"></a>PowerShell 사용

**Set-AzureRmDataFactoryV2** 명령을 다시 호출하면 새로 생성된 "Identity" 필드가 표시됩니다.

```powershell
PS C:\WINDOWS\system32> Set-AzureRmDataFactoryV2 -ResourceGroupName <resourceGroupName> -Name <dataFactoryName> -Location <region>

DataFactoryName   : ADFV2DemoFactory
DataFactoryId     : /subscriptions/<subsID>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/ADFV2DemoFactory
ResourceGroupName : <resourceGroupName>
Location          : East US
Tags              : {}
Identity          : Microsoft.Azure.Management.DataFactory.Models.FactoryIdentity
ProvisioningState : Succeeded
```

### <a name="using-rest-api"></a>REST API 사용

요청 본문의 "identity" 섹션에서 아래 API를 호출합니다.

```
PATCH https://management.azure.com/subscriptions/<subsID>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<data factory name>?api-version=2017-09-01-preview
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

**응답**: 서비스 ID가 자동으로 생성되고 "identity" 섹션이 그에 따라 채워집니다.

```json
{
    "name": "ADFV2DemoFactory",
    "tags": {},
    "properties": {
        "provisioningState": "Succeeded",
        "loggingStorageAccountKey": "**********",
        "createTime": "2017-09-26T04:10:01.1135678Z",
        "version": "2017-09-01-preview"
    },
    "identity": {
        "type": "SystemAssigned",
        "principalId": "765ad4ab-XXXX-XXXX-XXXX-51ed985819dc",
        "tenantId": "72f988bf-XXXX-XXXX-XXXX-2d7cd011db47"
    },
    "id": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/ADFV2DemoFactory",
    "type": "Microsoft.DataFactory/factories",
    "location": "EastUS"
}
```

### <a name="using-sdk"></a>SDK 사용

Identity=new FactoryIdentity()를 사용하여 데이터 팩터리 create_or_update 함수를 호출합니다. .NET을 사용하는 샘플 코드:

```csharp
Factory dataFactory = new Factory
{
    Location = <region>,
    Identity = new FactoryIdentity()
};
client.Factories.CreateOrUpdate(resourceGroup, dataFactoryName, dataFactory);
```

## <a name="next-steps"></a>다음 단계
Azure Data Factory에서 복사 작업의 원본 및 싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md##supported-data-stores-and-formats)를 참조하세요.