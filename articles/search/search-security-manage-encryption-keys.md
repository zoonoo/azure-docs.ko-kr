---
title: 고객 관리형 키를 사용하는 미사용 시 암호화
titleSuffix: Azure Cognitive Search
description: Azure Key Vault에서 만들고 관리하는 키를 사용하여 Azure Cognitive Search 내 인덱스 및 동의어 맵에 대한 서버 쪽 암호화를 보충합니다.
manager: nitinme
author: NatiNimni
ms.author: natinimn
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/02/2020
ms.custom: references_regions
ms.openlocfilehash: 9679157e7871b043711fff688a8cbb69cf9bb4d8
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107813617"
---
# <a name="configure-customer-managed-keys-for-data-encryption-in-azure-cognitive-search"></a>Azure Cognitive Search에서 데이터 암호화에 사용할 고객 관리형 키 구성

Azure Cognitive Search는 [서비스 관리형 키](../security/fundamentals/encryption-atrest.md#azure-encryption-at-rest-components)를 사용하여 미사용 인덱싱된 콘텐츠를 자동으로 암호화합니다. 보호가 더 필요하면 Azure Key Vault에서 만들고 관리하는 키를 사용하여 추가적인 암호화 계층으로 기본 암호화를 보완할 수 있습니다. 이 문서에서는 고객이 관리하는 키 암호화를 설정하는 단계를 안내합니다.

고객 관리 키 암호화는 [Azure Key Vault](../key-vault/general/overview.md)에 따라 달라집니다. 사용자 고유의 암호화 키를 만들고 Azure Key Vault에 저장할 수도 있고 Azure Key Vault의 API를 사용하여 암호화 키를 생성할 수도 있습니다. Azure Key Vault를 사용하면 [로깅 사용을 설정](../key-vault/general/logging.md)하는 경우 키 사용량을 감사할 수도 있습니다.  

고객 관리형 키를 사용하는 암호화는 해당 개체가 생성될 때 개별 인덱스나 동의어 맵에 적용되며 검색 서비스 수준 자체에는 지정되지 않습니다. 새 개체만 암호화할 수 있습니다. 이미 존재하는 콘텐츠는 암호화할 수 없습니다.

키가 모두 동일한 키 자격 증명 모음에 있을 필요는 없습니다. 단일 검색 서비스는 서로 다른 키 자격 증명 모음에 저장된 고유한 고객 관리형 암호화 키로 각각 암호화된 여러 암호화된 인덱스 또는 동의어 맵을 호스트할 수 있습니다. 고객 관리형 키를 사용하여 암호화되지 않은 동일한 서비스에 인덱스 및 동의어 맵을 포함할 수도 있습니다.

>[!Important]
> 고객 관리형 키를 구현하는 경우 주요 자격 증명 모음 키 및 Active Directory 애플리케이션 암호 및 등록을 정기적으로 회전하는 동안 엄격한 절차를 수행해야 합니다. 이전 암호 및 키를 삭제하기 전에 새 비밀 및 키를 사용하도록 모든 암호화된 콘텐츠를 항상 업데이트합니다. 이 단계를 수행하지 않은 경우 콘텐츠의 암호를 해독할 수 없습니다.

## <a name="double-encryption"></a>이중 암호화

2020년 8월 1일 이후에 생성된 서비스의 경우, 특정 지역에서의 고객 관리형 키 암호화의 범위는 임시 디스크를 포함하여 [전체 이중 암호화](search-security-overview.md#double-encryption)를 제공하며, 이 지역에서는 현재 제공 가능합니다. 

+ 미국 서부 2
+ 미국 동부
+ 미국 중남부
+ US Gov 버지니아
+ US Gov 애리조나

다른 지역 또는 8월 1일 이전에 만든 서비스를 사용하는 경우 관리형 키 암호화는 서비스에서 사용하는 임시 디스크를 제외하고 데이터 디스크로만 제한됩니다.

## <a name="prerequisites"></a>사전 요구 사항

이 시나리오에서 사용되는 도구와 서비스는 다음과 같습니다.

+ [청구 가능한 계층](search-sku-tier.md#tier-descriptions)의 [Azure Cognitive Search](search-create-service-portal.md)(기본 이상, 모든 지역).
+ [Azure Key Vault](../key-vault/general/overview.md), [Azure Portal](../key-vault//general/quick-create-portal.md) [Azure CLI](../key-vault//general/quick-create-cli.md) 또는 [Azure PowerShell](../key-vault//general/quick-create-powershell.md)을 사용하여 키 자격 증명 모음을 만들 수 있습니다. Azure Cognitive Search와 동일한 구독. 키 자격 증명 모음에는 **일시 삭제** 및 **보호 제거** 가 설정되어 있어야 합니다.
+ [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) 새 테넌트를 설치하지 않은 경우에는 [새 테넌트를 설정](../active-directory/develop/quickstart-create-new-tenant.md)합니다.

암호화된 개체를 만들 수 있는 검색 애플리케이션이 있어야 합니다. 이 코드에서 주요 자격 증명 모음 키 및 Active Directory 등록 정보를 참조합니다. 이 코드는 작업 중인 앱 또는 [C# 코드 샘플 DotNetHowToEncryptionUsingCMK](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToEncryptionUsingCMK)과 같은 프로토타입 코드일 수 있습니다.

> [!TIP]
> [Postman](search-get-started-rest.md), [Visual Studio Code](search-get-started-vs-code.md)또는 [Azure PowerShell](./search-get-started-powershell.md)를 사용하여 암호화 키 매개 변수를 포함하는 인덱스 및 동의어 맵을 만드는 REST API를 호출할 수 있습니다. 현재는 인덱스 또는 동의어 맵에 키를 추가할 수 있는 포털이 지원되지 않습니다.

## <a name="1---enable-key-recovery"></a>1-키 복구 사용

고객 관리형 키를 사용하는 암호화의 특성으로 인해 Azure 키 자격 증명 모음 키가 삭제되면 아무도 데이터를 검색할 수 없습니다. Key Vault 실수로 키 삭제로 인한 데이터 손실을 방지하려면 키 자격 증명 모음에 대해 일시 삭제 및 제거 보호를 사용하도록 설정해야 합니다. 일시 삭제는 기본적으로 사용하도록 설정되어 있으므로 의도적으로 사용하지 않도록 설정한 경우에만 문제가 발생합니다. 제거 보호는 기본적으로 사용하도록 설정되어 있지 않지만 Cognitive Search에서 고객이 관리하는 키 암호화에 필요합니다. 자세한 내용은 [일시 삭제](../key-vault/general/soft-delete-overview.md) 및 [보호 제거](../key-vault/general/soft-delete-overview.md#purge-protection) 개요를 참조하세요.

Portal, PowerShell 또는 Azure CLI 명령을 사용하여 두 속성을 모두 설정할 수 있습니다.

### <a name="using-azure-portal"></a>Azure Portal 사용

1. [Azure Portal에 로그인](https://portal.azure.com)하고 키 자격 증명 모음 개요 페이지를 엽니다.

1. **개요** 페이지의 **Essentials** 에서 **일시 삭제** 및 **보호 제거** 를 사용하도록 설정합니다.

### <a name="using-powershell"></a>PowerShell 사용

1. `Connect-AzAccount`을 실행하여 Azure 자격 증명을 설정합니다.

1. 다음 명령을 실행하여 키 자격 증명 모음에 연결하고 `<vault_name>`을 유효한 이름으로 바꿉니다.

   ```powershell
   $resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "<vault_name>").ResourceId
   ```

1. 일시 삭제를 사용하여 Azure Key Vault를 만듭니다. 자격 증명 모음에서 사용하지 않도록 설정된 경우 다음 명령을 실행합니다.

   ```powershell
   $resource.Properties | Add-Member -MemberType NoteProperty -Name "enableSoftDelete" -Value 'true'
   ```

1. 제거 방지 사용:

   ```powershell
   $resource.Properties | Add-Member -MemberType NoteProperty -Name "enablePurgeProtection" -Value 'true'
   ```

1. 업데이트를 저장:

   ```powershell
   Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
   ```

### <a name="using-azure-cli"></a>Azure CLI 사용

+ [Azure CLI](/cli/azure/install-azure-cli)을 설치한 경우 다음 명령을 실행하여 필수 속성을 사용하도록 설정할 수 있습니다.

   ```azurecli-interactive
   az keyvault update -n <vault_name> -g <resource_group> --enable-soft-delete --enable-purge-protection
   ```

## <a name="2---create-a-key-in-key-vault"></a>2 - Key Vault에 키 만들기

Azure Key Vault에 키가 이미 있는 경우이 단계를 건너뜁니다.

1. [Azure Portal에 로그인](https://portal.azure.com)하고 키 자격 증명 모음 개요 페이지를 엽니다.

1. 왼쪽에서 **키** 를 선택하고 **+생성/가져오기** 를 선택합니다.

1. **키 만들기** 창에 있는 **옵션** 목록에서 키를 만드는 데 사용할 방법을 선택합니다. d새 키를 **생성** 하거나 기존 키를 **업로드** 하거나 **복원 백업을** 사용하여 키 백업을 선택할 수 있습니다.

1. 키의 **이름을** 입력하고 필요에 따라 다른 키 속성을 선택합니다.

1. **만들기** 를 선택하여 배포를 시작합니다.

1. 키 식별자를 적어둡니다. **키 값 Uri**, **키 이름** 및 **키 버전** 으로 구성됩니다. Azure Cognitive Search에서 암호화된 인덱스를 정의하려면 식별자가 필요합니다.

   :::image type="content" source="media/search-manage-encryption-keys/cmk-key-identifier.png" alt-text="새 키 자격 증명 모음을 만듭니다":::

## <a name="3---register-an-app-in-active-directory"></a>3-Active Directory에 앱 등록

1. [Azure Portal](https://portal.azure.com)에서 구독에 대한 Azure Active Directory 리소스를 찾습니다.

1. 왼쪽의 **관리** 에서 **앱 등록** 을 선택한 다음 **새 등록** 을 선택하세요.

1. 검색 애플리케이션 이름과 유사한 이름으로 등록 이름을 지정하세요. **등록** 을 선택합니다.

1. 앱 등록을 생성하면 애플리케이션 ID를 복사하세요. 애플리케이션에 이 문자열을 제공해야 합니다. 

   [DotNetHowToEncryptionUsingCMK](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToEncryptionUsingCMK)를 단계별로 실행하는 경우 이 값을 **appsettings.js** 파일에 붙여 넣습니다.

   :::image type="content" source="media/search-manage-encryption-keys/cmk-application-id.png" alt-text="Essentials 섹션의 애플리케이션 ID":::

1. 그런 다음 왼쪽 메뉴에서 **인증서 및 암호** 를 선택합니다.

1. **새 클라이언트 비밀** 을 선택합니다. 표시 이름으로 비밀을 지정하고 **추가** 를 선택합니다.

1. 애플리케이션 비밀 복사하기. 샘플을 단계별로 실행하는 경우 이 값을  **의appsettings.js** 파일에 붙여 넣습니다.

   :::image type="content" source="media/search-manage-encryption-keys/cmk-application-secret.png" alt-text="애플리케이션 암호":::

## <a name="4---grant-key-access-permissions"></a>4-키 액세스 권한 부여

이 단계에서는 Key Vault에서 액세스 정책을 만듭니다. 이 정책은 사용자가 등록한 애플리케이션에 고객 관리형 키를 사용할 수 있도록 Active Directory 권한을 부여합니다.

지정된 시간에 액세스 권한이 해지될 수 있습니다. 해지되면 해당 키 자격 증명 모음을 사용하는 모든 검색 서비스 인덱스 또는 동의어 맵을 사용할 수 없게 됩니다. 나중에 Key vault 액세스 권한을 복원하면 index\synonym 맵 액세스가 복원됩니다. 자세한 내용은 [키 자격 증명 모음에 대한 보안 액세스](../key-vault/general/security-features.md)를 참조하세요.

1. 계속 Azure Portal에서 키 자격 증명 모음 **개요** 페이지를 엽니다. 

1. 왼쪽에서 **액세스 정책을** 선택하고 **+액세스 정책 추가** 를 선택합니다.

   :::image type="content" source="media/search-manage-encryption-keys/cmk-add-access-policy.png" alt-text="키 자격 증명 모음 액세스 정책 추가":::

1. **보안 주체 선택** 을 선택하고 Active Directory에 등록한 애플리케이션을 선택합니다. 이름순으로 검색할 수 있습니다.

   :::image type="content" source="media/search-manage-encryption-keys/cmk-access-policy-permissions.png" alt-text="키 자격 증명 모음 액세스 정책 보안 주체를 선택합니다":::

1. **키 권한** 에서 *가져오기*, *키 래핑 해제* 및 *키 래핑* 으로 설정합니다.

1. **비밀 권한** 에서 *가져오기* 를 선택합니다.

1. **인증서 권한** 에서 *가져오기* 를 선택합니다.

1. **추가** 를 선택하고 **저장** 을 선택합니다.

> [!Important]
> Azure Cognitive Search의 암호화된 콘텐츠는 특정 **버전** 의 특정 Azure Key Vault 키를 사용하도록 구성됩니다. 키 또는 버전을 변경하는 경우 이전 key\version을 삭제하기 **전에** 새 key\version을 사용하도록 인덱스 또는 동의어 맵을 업데이트해야 합니다. 이렇게 하지 않으면 인덱스 또는 동의어 맵을 사용할 수 없게 되며 키 액세스가 손실된 후에는 콘텐츠의 암호를 해독할 수 없습니다.

<a name="encrypt-content"></a>

## <a name="5---encrypt-content"></a>5-콘텐츠 암호화

인덱스, 데이터 원본, 기술, 인덱서 또는 동의어 맵에 고객 관리형 키를 추가하려면 [검색 REST API](/rest/api/searchservice/) 또는 SDK를 사용해야 합니다. 포털은 동의어 맵 또는 암호화 속성을 노출하지 않습니다. 유효한 API 인덱스를 사용하는 경우 데이터 원본, 기술 세트, 인덱서 및 동의어 맵이 최상위 **encryptionKey** 속성을 지원합니다.

이 예제에서는 Azure Key Vault 및 Azure Active Directory에 대한 값과 함께 REST API를 사용합니다.

```json
{
  "encryptionKey": {
    "keyVaultUri": "https://demokeyvault.vault.azure.net",
    "keyVaultKeyName": "myEncryptionKey",
    "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660",
    "accessCredentials": {
      "applicationId": "00000000-0000-0000-0000-000000000000",
      "applicationSecret": "myApplicationSecret"
    }
  }
}
```

> [!Note]
> 이러한 주요 자격 증명 모음 세부 정보는 비밀로 간주되지 않으며 Azure Portal의 관련 Azure Key Vault 키 페이지로 이동하여 쉽게 검색할 수 있습니다.

## <a name="example-index-encryption"></a>예제: 인덱스 암호화

[Index Azure Cognitive Search REST API 만들기](/rest/api/searchservice/create-index)를 사용하여 암호화된 인덱스를 만듭니다. 사용할 암호화 키를 지정하려면 `encryptionKey` 속성을 사용합니다.
> [!Note]
> 이러한 주요 자격 증명 모음 세부 정보는 비밀로 간주되지 않으며 Azure Portal의 관련 Azure Key Vault 키 페이지로 이동하여 쉽게 검색할 수 있습니다.

## <a name="rest-examples"></a>REST 예제

이 섹션에서는 암호화된 인덱스 및 동의어 맵에 대한 전체 JSON을 보여줍니다

### <a name="index-encryption"></a>인덱스 암호화

REST API를 통해 새 인덱스를 만드는 방법에 대한 자세한 내용은 [인덱스 만들기(REST API)](/rest/api/searchservice/create-index)에서 찾을 수 있습니다. 여기서 유일한 차이점은 인덱스 정의의 일부로 암호화 키 세부 정보를 지정하는 것입니다.

```json
{
 "name": "hotels",
 "fields": [
  {"name": "HotelId", "type": "Edm.String", "key": true, "filterable": true},
  {"name": "HotelName", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": true, "facetable": false},
  {"name": "Description", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "en.lucene"},
  {"name": "Description_fr", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene"},
  {"name": "Category", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true},
  {"name": "Tags", "type": "Collection(Edm.String)", "searchable": true, "filterable": true, "sortable": false, "facetable": true},
  {"name": "ParkingIncluded", "type": "Edm.Boolean", "filterable": true, "sortable": true, "facetable": true},
  {"name": "LastRenovationDate", "type": "Edm.DateTimeOffset", "filterable": true, "sortable": true, "facetable": true},
  {"name": "Rating", "type": "Edm.Double", "filterable": true, "sortable": true, "facetable": true},
  {"name": "Location", "type": "Edm.GeographyPoint", "filterable": true, "sortable": true},
 ],
  "encryptionKey": {
    "keyVaultUri": "https://demokeyvault.vault.azure.net",
    "keyVaultKeyName": "myEncryptionKey",
    "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660",
    "accessCredentials": {
      "applicationId": "00000000-0000-0000-0000-000000000000",
      "applicationSecret": "myApplicationSecret"
    }
  }
}
```

이제 인덱스 생성 요청을 보낸 다음 인덱스를 정상적으로 사용하기 시작할 수 있습니다.

### <a name="synonym-map-encryption"></a>동의어 맵 암호화

[동의어 맵 Azure Cognitive Search REST API 만들기](/rest/api/searchservice/create-synonym-map)를 사용하여 암호화된 동의어 맵을 만듭니다. 사용할 암호화 키를 지정하려면 `encryptionKey` 속성을 사용합니다.

```json
{
  "name" : "synonymmap1",
  "format" : "solr",
  "synonyms" : "United States, United States of America, USA\n
  Washington, Wash. => WA",
  "encryptionKey": {
    "keyVaultUri": "https://demokeyvault.vault.azure.net",
    "keyVaultKeyName": "myEncryptionKey",
    "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660",
    "accessCredentials": {
      "applicationId": "00000000-0000-0000-0000-000000000000",
      "applicationSecret": "myApplicationSecret"
    }
  }
}
```

이제 동의어 맵 만들기 요청을 보낸 다음 정상적으로 사용을 시작할 수 있습니다.

## <a name="example-data-source-encryption"></a>예제: 데이터 원본 암호화

[데이터 원본 만들기(Azure Cognitive Search REST API)](/rest/api/searchservice/create-data-source)를 사용하여 암호화된 데이터 원본을 만듭니다. 사용할 암호화 키를 지정하려면 `encryptionKey` 속성을 사용합니다.

```json
{
  "name" : "datasource1",
  "type" : "azureblob",
  "credentials" :
  { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=datasource;AccountKey=accountkey;EndpointSuffix=core.windows.net"
  },
  "container" : { "name" : "containername" },
  "encryptionKey": {
    "keyVaultUri": "https://demokeyvault.vault.azure.net",
    "keyVaultKeyName": "myEncryptionKey",
    "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660",
    "accessCredentials": {
      "applicationId": "00000000-0000-0000-0000-000000000000",
      "applicationSecret": "myApplicationSecret"
    }
  }
}
```

이제 데이터 원본 만들기 요청을 보낸 다음 정상적으로 사용을 시작할 수 있습니다.

## <a name="example-skillset-encryption"></a>예제: 기술 세트 암호화

[기술 세트 Azure Cognitive Search REST API 만들기](/rest/api/searchservice/create-skillset)를 사용하여 암호화된 기술을 만듭니다. 사용할 암호화 키를 지정하려면 `encryptionKey` 속성을 사용합니다.

```json
{
  "name" : "datasource1",
  "type" : "azureblob",
  "credentials" :
  { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=datasource;AccountKey=accountkey;EndpointSuffix=core.windows.net"
  },
  "container" : { "name" : "containername" },
  "encryptionKey": {
    "keyVaultUri": "https://demokeyvault.vault.azure.net",
    "keyVaultKeyName": "myEncryptionKey",
    "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660",
    "accessCredentials": {
      "applicationId": "00000000-0000-0000-0000-000000000000",
      "applicationSecret": "myApplicationSecret"
    }
  }
}
```

이제 기술 세트 만들기 요청을 보낸 다음 정상적으로 사용을 시작할 수 있습니다.

## <a name="example-indexer-encryption"></a>예제: 인덱서 암호화

[인덱서 Azure Cognitive Search REST API 만들기](/rest/api/searchservice/create-indexer)를 사용하여 암호화된 인덱서를 만듭니다. 사용할 암호화 키를 지정하려면 `encryptionKey` 속성을 사용합니다.

```json
{
  "name": "indexer1",
  "dataSourceName": "datasource1",
  "skillsetName": "skillset1",
  "parameters": {
      "configuration": {
          "imageAction": "generateNormalizedImages"
      }
  },
  "encryptionKey": {
    "keyVaultUri": "https://demokeyvault.vault.azure.net",
    "keyVaultKeyName": "myEncryptionKey",
    "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660",
    "accessCredentials": {
      "applicationId": "00000000-0000-0000-0000-000000000000",
      "applicationSecret": "myApplicationSecret"
    }
  }
}
```

이제 인덱서 만들기 요청을 보낸 다음 정상적으로 사용을 시작할 수 있습니다.

>[!Important]
> `encryptionKey`은 기존 검색 인덱스 또는 동의어 맵에 추가할 수 없지만 세 가지 주요 자격 증명 모음 세부 정보(예: 키 버전 업데이트)에 대해 다른 값을 제공하여 업데이트할 수 있습니다. 새 Key Vault 키 또는 새 키 버전으로 변경하는 경우 이전 key\version을 **삭제하기 전에** 먼저 새 key\version을 사용하도록 해당 키를 사용하는 모든 검색 인덱스나 동의어 맵을 업데이트해야 합니다. 이렇게 하지 않으면 인덱스 또는 동의어 맵을 사용할 수 없게 되며 키 액세스가 손실된 후에는 콘텐츠의 암호를 해독할 수 없습니다. 키 자격 증명 모음 액세스 권한을 나중에 복원해도 콘텐츠 액세스가 복원됩니다.

## <a name="simpler-alternative-trusted-service"></a>더 간단한 대안: 신뢰할 수 있는 서비스

테넌트 구성 및 인증 요구 사항에 따라 주요 자격 증명 모음 키에 액세스하기 위한 더 간단한 방법을 구현할 수 있습니다. Active Directory 애플리케이션을 만들고 사용하는 대신 시스템 관리 ID를 사용하도록 설정하여 검색 서비스를 신뢰할 수 있는 서비스로 만들 수 있습니다. 그런 다음 키 자격 증명 모음 키에 액세스하기 위해 AD에 등록된 애플리케이션이 아닌 보안 원칙으로 신뢰할 수 있는 검색 서비스를 사용합니다.

이 방법을 사용하면 애플리케이션 등록 및 애플리케이션 암호에 대한 단계를 생략하고 키 자격 증명 모음 구성 요소(URI, 자격 증명 모음 이름, 키 버전)에만 암호화 키 정의를 단순화할 수 있습니다.

일반적으로 관리되는 ID를 사용하면 검색 서비스에서 코드에 자격 증명(ApplicationID 또는 ApplicationSecret)을 저장하지 않고 Azure Key Vault에 인증할 수 있습니다. 이러한 유형의 관리되는 ID의 수명 주기는 관리 ID를 하나만 포함할 수 있는 검색 서비스의 수명 주기에 연결됩니다. 관리 ID의 작동 방식에 대한 자세한 내용은 [Azure 리소스의 관리 ID 개요](../active-directory/managed-identities-azure-resources/overview.md)를 참조하세요.

1. 검색 서비스를 신뢰할 수 있는 서비스로 설정합니다.
   ![시스템 할당 관리 ID 켜기](./media/search-managed-identities/turn-on-system-assigned-identity.png "시스템 할당 관리 ID 켜기")

1. Azure Key Vault에서 액세스 정책을 설정할 때 AD에 등록된 애플리케이션 대신 신뢰받는 검색 서비스를 보안 주체로 선택합니다. 액세스 키 권한 부여 단계에 설명된 것처럼 동일한 권한 (여러 개의 가져오기, 래핑, 래핑 해제)을 할당합니다.

1. Active Directory 속성을 생략하는 `encryptionKey`의 단순화된 생성을 사용합니다.

    ```json
    {
      "encryptionKey": {
        "keyVaultUri": "https://demokeyvault.vault.azure.net",
        "keyVaultKeyName": "myEncryptionKey",
        "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660"
      }
    }
    ```

이 단순화된 방법을 채택할 수 없게 되는 조건은 다음과 같습니다.

+ 검색 서비스가 Azure Key Vault와 다른 Active Directory 테넌트에 있는 경우와 같이 검색 서비스에 대한 액세스 권한을 키 자격 증명 모음에 직접 부여할 수는 없습니다.

+ 단일 검색 서비스는 각각 다른 키 자격 증명 모음에서 다른 키를 사용하는 여러 암호화된 indexes\synonym 맵을 호스트해야 합니다. 여기서 각 키 자격 증명 모음이 인증을 위해 **다른 ID** 를 사용해야 합니다. 검색 서비스에는 관리 ID가 하나만 있을 수 있기 때문에 여러 ID에 대한 요구 사항은 시나리오에 대한 단순화된 접근 방식을 규정하지 않습니다.  

## <a name="work-with-encrypted-content"></a>암호화된 열로 작업

고객 관리형 키 암호화를 사용하여 추가 암호화/암호 해독 작업으로 인해 인덱싱 및 쿼리 모두에 대한 대기 시간을 확인할 수 있습니다. Azure Cognitive Search는 암호화 작업을 기록하지 않지만 키 자격 증명 모음 로깅을 통해 키 액세스를 모니터링할 수 있습니다. 키 자격 증명 모음 구성의 일부로 [로깅을 사용](../key-vault/general/logging.md)하는 것이 좋습니다.

키 회전은 시간이 지남에 따라 발생합니다. 키를 회전할 때마다 다음 시퀀스를 따르는 것이 중요합니다.

1. [인덱스 또는 동의어 맵에 사용되는 키를 확인합니다](search-security-get-encryption-keys.md).
1. [키 자격 증명 모음에 새 키를 만들](../key-vault/keys/quick-create-portal.md)지만 원래 키를 사용할 수 있는 상태로 둡니다.
1. 인덱스 또는 동의어 맵의 [encryptionKey 속성을 업데이트](/rest/api/searchservice/update-index)하여 새 값을 사용합니다. 원래 이 속성을 사용하여 만든 개체만 다른 값을 사용하도록 업데이트할 수 있습니다.
1. 키 자격 증명 모음에서 이전 키를 사용하지 않도록 설정하거나 삭제합니다. 키 액세스를 모니터링하여 새 키가 사용되고 있는지 확인합니다.

성능상의 이유로 검색 서비스는 최대 몇 시간 동안 키를 캐시합니다. 새 키를 제공하지 않고 키를 사용하지 않도록 설정하거나 삭제한 경우에는 캐시가 만료될 때까지 쿼리가 일시적으로 계속 작동합니다. 그러나 검색 서비스에서 콘텐츠 암호를 해독할 수 없는 경우 다음 메시지가 표시됩니다. "액세스가 금지되었습니다. 사용된 쿼리 키가 해지되었을 수 있습니다. 다시 시도하십시오." 

## <a name="next-steps"></a>다음 단계

Azure 보안 아키텍처에 익숙하지 않은 경우 [Azure 보안 설명서](../security/index.yml), 특히 이 문서를 검토합니다.

> [!div class="nextstepaction"]
> [미사용 데이터 암호화](../security/fundamentals/encryption-atrest.md)