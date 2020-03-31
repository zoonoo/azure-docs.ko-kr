---
title: 고객 관리 키를 사용하여 미사용 암호화
titleSuffix: Azure Cognitive Search
description: Azure Key Vault에서 만들고 관리하는 키를 사용하여 Azure Cognitive Search의 인덱스 및 동의어 맵에 대한 서버 측 암호화를 보완합니다.
manager: nitinme
author: NatiNimni
ms.author: natinimn
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: cb17fe24339ad618229b3456ece15c206f79bdb7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76899939"
---
# <a name="encryption-at-rest-of-content-in-azure-cognitive-search-using-customer-managed-keys-in-azure-key-vault"></a>Azure 키 자격 증명 모음에서 고객 관리 키를 사용하여 Azure 인지 검색의 나머지 부분에 있는 콘텐츠에 대한 암호화

기본적으로 Azure Cognitive Search는 [서비스 관리 키를](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest#data-encryption-models)사용하여 미사용 인덱싱된 콘텐츠를 암호화합니다. Azure Key Vault에서 만들고 관리하는 키를 사용하여 기본 암호화를 추가 암호화 계층으로 보완할 수 있습니다. 이 문서에서는 단계를 안내합니다.

서버 측 암호화는 Azure [키 자격 증명 모음과의](https://docs.microsoft.com/azure/key-vault/key-vault-overview)통합을 통해 지원됩니다. 사용자 고유의 암호화 키를 만들고 Azure Key Vault에 저장할 수도 있고 Azure Key Vault의 API를 사용하여 암호화 키를 생성할 수도 있습니다. Azure 키 자격 증명 모음을 사용하면 키 사용량을 감사할 수도 있습니다. 

고객 관리 키로 암호화는 검색 서비스 수준이 아니라 해당 개체를 만들 때 인덱스 또는 동의어 맵 수준에서 구성됩니다. 이미 있는 콘텐츠는 암호화할 수 없습니다. 

키가 모두 동일한 키 볼트에 있을 필요는 없습니다. 단일 검색 서비스는 서로 다른 Key Vault에 저장된 자체 고객 관리 암호화 키로 암호화된 여러 암호화된 인덱스 또는 동의어 맵을 호스팅할 수 있습니다.  또한 고객 관리 키를 사용하여 암호화되지 않은 동일한 서비스에 인덱스 및 동의어 맵을 가질 수도 있습니다. 

> [!IMPORTANT] 
> 이 기능은 REST [API 버전 2019-05-06](https://docs.microsoft.com/rest/api/searchservice/) 및 [.NET SDK 버전 8.0 미리 보기에서](search-dotnet-sdk-migration-version-9.md)사용할 수 있습니다. 현재 Azure 포털에서 고객 관리 암호화 키를 구성할 수 있는 지원이 없습니다. 검색 서비스는 2019년 1월 이후에 만들어야 하며 무료(공유) 서비스일 수 없습니다.

## <a name="prerequisites"></a>사전 요구 사항

이 예제에서는 다음 서비스가 사용됩니다. 

+ [Azure Cognitive Search 서비스를 만들거나](search-create-service-portal.md) 현재 구독에서 [기존 서비스를 찾습니다](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices). 

+ [Azure Key Vault 리소스를 만들거나](https://docs.microsoft.com/azure/key-vault/quick-create-portal#create-a-vault) 구독 아래에 있는 기존 자격 증명 모음을 찾습니다.

+ [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) 또는 [Azure CLI는](https://docs.microsoft.com/cli/azure/install-azure-cli) 구성 작업에 사용됩니다.

+ [우체부,](search-get-started-postman.md) [Azure PowerShell](search-create-index-rest-api.md) 및 [Azure 인지 검색 SDK는](https://aka.ms/search-sdk-preview) REST API를 호출하는 데 사용할 수 있습니다. 현재 고객 관리 암호화에 대한 포털 지원은 없습니다.

>[!Note]
> 고객 관리 키 기능을 사용한 암호화의 특성으로 인해 Azure 키 자격 증명 모음 키가 삭제된 경우 Azure Cognitive Search에서 데이터를 검색할 수 없습니다. 실수로 키 볼트 키 삭제로 인한 데이터 손실을 방지하려면 키 자격 증명 모음에서 소프트 삭제 및 지우기 보호를 사용하도록 **설정해야** 사용가능합니다. 자세한 내용은 [Azure 키 볼트 소프트 삭제](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete)를 참조하십시오.   

## <a name="1---enable-key-recovery"></a>1 - 키 복구 활성화

Azure 키 볼트 리소스를 만든 후 다음 PowerShell 또는 Azure CLI 명령을 실행하여 선택한 키 자격 증명 모음에서 **소프트 삭제** 및 **제거 보호를** 사용하도록 설정합니다.   

```powershell
$resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "<vault_name>").ResourceId

$resource.Properties | Add-Member -MemberType NoteProperty -Name "enableSoftDelete" -Value 'true'

$resource.Properties | Add-Member -MemberType NoteProperty -Name "enablePurgeProtection" -Value 'true'

Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
```

```azurecli-interactive
az keyvault update -n <vault_name> -g <resource_group> --enable-soft-delete --enable-purge-protection
```

## <a name="2---create-a-new-key"></a>2 - 새 키 만들기

기존 키를 사용하여 Azure 인지 검색 콘텐츠를 암호화하는 경우 이 단계를 건너뜁니다.

1. [Azure 포털에 로그인하고](https://portal.azure.com) 키 자격 증명 모음 대시보드로 이동합니다.

1. 왼쪽 탐색 창에서 **키** 설정을 선택하고 **+ 생성/가져오기**를 클릭합니다.

1. **옵션**목록에서 **키 만들기** 창에서 키를 만드는 데 사용할 방법을 선택합니다. 새 키를 **생성하거나,** 기존 키를 **업로드하거나,** **백업 복원을** 사용하여 키 백업을 선택할 수 있습니다.

1. 키의 **이름을** 입력하고 선택적으로 다른 키 속성을 선택합니다.

1. **만들기** 단추를 클릭하여 배포를 시작합니다.

키 식별자를 기록합니다 - 키 값 **Uri,** 키 **이름**및 **키 버전에서**구성됩니다. Azure Cognitive Search에서 암호화된 인덱스를 정의하려면 이러한 인덱스가 필요합니다.
 
![새 키 자격 증명 모음 키 만들기](./media/search-manage-encryption-keys/create-new-key-vault-key.png "새 키 자격 증명 모음 키 만들기")

## <a name="3---create-a-service-identity"></a>3 - 서비스 ID 생성

검색 서비스에 ID를 할당하면 검색 서비스에 Key Vault 액세스 권한을 부여할 수 있습니다. 검색 서비스는 해당 ID를 사용하여 Azure Key 자격 증명 모음을 사용하여 인증합니다.

Azure Cognitive Search는 ID를 할당하는 두 가지 방법, 즉 관리되는 ID 또는 외부에서 관리되는 Azure Active Directory 응용 프로그램을 지원합니다. 

가능하면 관리되는 ID를 사용합니다. 검색 서비스에 ID를 할당하는 가장 간단한 방법이며 대부분의 시나리오에서 작동해야 합니다. 인덱스 및 동의어 맵에 여러 키를 사용하거나 솔루션이 ID 기반 인증을 자격을 상실하는 분산 아키텍처에 있는 경우 이 문서의 끝에 설명된 고급 [외부 관리 Azure Active Directory 접근 방식을](#aad-app) 사용합니다.

 일반적으로 관리되는 ID를 사용하면 코드에 자격 증명을 저장하지 않고 검색 서비스를 Azure Key Vault에 인증할 수 있습니다. 이러한 유형의 관리ID의 수명 주기는 하나의 관리되는 ID만 가질 수 있는 검색 서비스의 수명 주기와 연결됩니다. [관리되는 ID에 대해 자세히 알아봅니다.](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)

1. 관리되는 ID를 만들려면 [Azure 포털에 로그인하고](https://portal.azure.com) 검색 서비스 대시보드를 엽니다. 

1. 왼쪽 탐색 창에서 **ID를** 클릭하고 상태를 **켜기로**변경하고 **저장을**클릭합니다.

![관리되는 ID 사용](./media/search-enable-msi/enable-identity-portal.png "망가지 ID 사용")

## <a name="4---grant-key-access-permissions"></a>4 - 키 액세스 권한 부여

검색 서비스에서 Key Vault 키를 사용하도록 설정하려면 검색 서비스에 특정 액세스 권한을 부여해야 합니다.

액세스 권한은 언제든지 취소될 수 있습니다. 해지되면 해당 키 자격 증명 모음을 사용하는 검색 서비스 인덱스 또는 동의어 맵을 사용할 수 없게 됩니다. 나중에 키 자격 증명 모음 액세스 권한을 복원하면 인덱스\동의어 맵 액세스가 복원됩니다. 자세한 내용은 [키 자격 증명 모음에 대한 보안 액세스를](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault)참조하십시오.

1. [Azure 포털에 로그인하고](https://portal.azure.com) 키 자격 증명 모음 개요 페이지를 엽니다. 

1. 왼쪽 탐색 창에서 **액세스 정책** 설정을 선택하고 **+Add new**을 클릭합니다.

   ![새 키 자격 증명 모음 액세스 정책 추가](./media/search-manage-encryption-keys/add-new-key-vault-access-policy.png "새 키 자격 증명 모음 액세스 정책 추가")

1. 보안 주체 선택을 클릭하고 Azure 인지 검색 서비스를 **선택합니다.** 이름 또는 관리되는 ID를 사용하도록 설정한 후 표시된 개체 ID로 검색할 수 있습니다.

   ![키 자격 증명 모음 액세스 정책 보안 주체 선택](./media/search-manage-encryption-keys/select-key-vault-access-policy-principal.png "키 자격 증명 모음 액세스 정책 보안 주체 선택")

1. 키 **권한을** 클릭하고 *받기*, *키 줄 바꿈* 및 *둘러싸기 키를*선택합니다. Azure Data *Lake 저장소 또는 Azure 저장소* 템플릿을 사용하여 필요한 권한을 빠르게 선택할 수 있습니다.

   Azure 인지 검색은 다음 [액세스 권한으로](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-operations)부여되어야 합니다.

   * *Get* - 검색 서비스에서 키 볼트에서 키의 공용 부분을 검색할 수 있습니다.
   * *키 감싸기* - 검색 서비스에서 키를 사용하여 내부 암호화 키를 보호할 수 있습니다.
   * *키 래핑 해제* - 검색 서비스에서 키를 사용하여 내부 암호화 키의 래핑을 해제할 수 있습니다.

   ![키 자격 증명 모음 액세스 정책 키 권한 선택](./media/search-manage-encryption-keys/select-key-vault-access-policy-key-permissions.png "키 자격 증명 모음 액세스 정책 키 권한 선택")

1. **확인을** 클릭하고 액세스 정책 변경 내용 **저장을** 클릭합니다.

> [!Important]
> Azure Cognitive Search의 암호화된 콘텐츠는 특정 **버전의**특정 Azure 키 볼트 키를 사용하도록 구성됩니다. 키 또는 버전을 변경하는 경우 이전 키\버전을 삭제하기 **전에** 새 키\버전을 사용하도록 인덱스 또는 동의어 맵을 업데이트해야 합니다. 이렇게 하지 않으면 인덱스 또는 동의어 맵을 사용할 수 없게 되므로 키 액세스가 손실되면 콘텐츠를 해독할 수 없습니다.   

## <a name="5---encrypt-content"></a>5 - 콘텐츠 암호화

Azure 포털을 사용하여 고객 관리 키로 암호화된 인덱스 또는 동의어 맵을 만드는 것은 아직 불가능합니다. Azure 인지 검색 REST API를 사용하여 이러한 인덱스 또는 동의어 맵을 만듭니다.

인덱스 맵과 동의어 맵 모두 키를 지정하는 데 사용되는 새 최상위 **암호화Key** 속성을 지원합니다. 

키 **볼트 Uri키** **키 이름과** 키 볼트 키의 **키 버전을** 사용하여 암호화 **키** 정의를 만들 수 있습니다.

```json
{
  "encryptionKey": {
    "keyVaultUri": "https://demokeyvault.vault.azure.net",
    "keyVaultKeyName": "myEncryptionKey",
    "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660"
  }
}
```
> [!Note] 
> 이러한 키 자격 증명 모음 세부 정보는 비밀로 간주되지 않으며 Azure Portal의 관련 Azure Key Vault 키 페이지를 탐색하여 쉽게 검색할 수 있습니다.

관리되는 ID를 사용하는 대신 키 볼트 인증에 AAD 응용 프로그램을 사용하는 경우 암호화 키에 AAD 응용 프로그램 **액세스 자격 증명을** 추가합니다. 
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

## <a name="example-index-encryption"></a>예: 인덱스 암호화
REST API를 통해 새 인덱스를 만드는 세부 정보는 [인덱스 만들기(Azure Cognitive Search REST API)에서](https://docs.microsoft.com/rest/api/searchservice/create-index)찾을 수 있으며 여기서 유일한 차이점은 인덱스 정의의 일부로 암호화 키 세부 정보를 지정하는 것입니다. 

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
   "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660"
 }
}
```
이제 인덱스 생성 요청을 보낸 다음 인덱스를 정상적으로 사용할 수 있습니다.

## <a name="example-synonym-map-encryption"></a>예: 동의어 맵 암호화

REST API를 통해 새 동의어 맵을 만드는 세부 정보는 [동의어 맵 만들기(Azure Cognitive Search REST API)에서](https://docs.microsoft.com/rest/api/searchservice/create-synonym-map)확인할 수 있으며, 여기서 유일한 차이점은 동의어 맵 정의의 일부로 암호화 키 세부 정보를 지정하는 것입니다. 

```json
{   
  "name" : "synonymmap1",  
  "format" : "solr",  
  "synonyms" : "United States, United States of America, USA\n
  Washington, Wash. => WA",
  "encryptionKey": {
    "keyVaultUri": "https://demokeyvault.vault.azure.net",
    "keyVaultKeyName": "myEncryptionKey",
    "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660"
  }
}
```
이제 동의어 맵 생성 요청을 보낸 다음 정상적으로 사용할 수 있습니다.

>[!Important] 
> **암호화 키는** 기존 Azure Cognitive Search 인덱스 또는 동의어 맵에 추가할 수 없지만 세 가지 키 자격 증명 모음 세부 정보(예: 키 버전 업데이트)에 대해 서로 다른 값을 제공하여 업데이트할 수 있습니다. 새 키 볼트 키 또는 새 키 버전으로 변경할 때 키를 사용하는 모든 Azure Cognitive Search 인덱스 또는 동의어 맵을 먼저 업데이트하여 이전 키\버전을 **삭제하기 전에** 새 키\버전을 사용해야 합니다. 이렇게 하지 않으면 키 액세스가 손실되면 콘텐츠를 해독할 수 없으므로 인덱스 또는 동의어 맵을 사용할 수 없게 됩니다.   
> 나중에 Key 자격 증명 모음 액세스 권한을 복원하면 콘텐츠 액세스가 복원됩니다.

## <a name="advanced-use-an-externally-managed-azure-active-directory-application"></a><a name="aad-app"></a>고급: 외부에서 관리되는 Azure Active Directory 응용 프로그램 사용

관리되는 ID를 사용할 수 없는 경우 Azure Cognitive Search 서비스에 대한 보안 보안 주체가 있는 Azure Active Directory 응용 프로그램을 만들 수 있습니다. 특히 관리되는 ID는 다음 조건에서 실행 가능하지 않습니다.

* 검색 서비스가 Azure 키 자격 증명 모음과 다른 Active Directory 테넌트에 있는 경우 키 자격 증명 모음에 검색 서비스 액세스 권한을 직접 부여할 수 없습니다.

* 단일 검색 서비스는 각 키 자격 증명 모음이 인증을 위해 **다른 ID를** 사용해야 하는 다른 키 자격 증명 모음의 다른 키를 사용하여 여러 암호화된 인덱스\동의어 맵을 호스트하는 데 필요합니다. 다른 ID를 사용하여 다른 Key 자격 증명 모음을 관리하는 것이 필수 사항이 아닌 경우 위의 관리되는 ID 옵션을 사용하는 것이 좋습니다.  

이러한 토폴로지에 맞게 Azure Cognitive Search는 검색 서비스와 키 볼트 간의 인증을 위해 AAD(Azure Active Directory) 응용 프로그램을 사용합니다.    
포털에서 AAD 응용 프로그램을 만들려면 다음을 수행합니다.

1. [Azure Active 디렉터리 응용 프로그램을 만듭니다.](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#create-an-azure-active-directory-application)

1. 암호화된 인덱스를 만드는 데 필요한 [응용 프로그램 ID 및 인증 키를 가져옵니다.](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) 제공해야 하는 값에는 **응용 프로그램 ID** 및 **인증 키가**포함됩니다.

>[!Important]
> 관리되는 ID 대신 인증의 AAD 응용 프로그램을 사용하기로 결정할 때Azure Cognitive Search가 귀하를 대신하여 AAD 응용 프로그램을 관리할 권한이 없으며 정기적인 AAD 응용 프로그램을 관리하는 것은 사용자의 결정입니다. 응용 프로그램 인증 키를 회전합니다.
> AAD 응용 프로그램 또는 인증 키를 변경할 때 해당 응용 프로그램을 사용하는 모든 Azure Cognitive Search 인덱스 또는 동의어 맵은 이전 응용 프로그램 또는 권한 부여 키를 **삭제하기 전에** 키 볼트 액세스 권한을 취소하기 전에 새 응용 프로그램 ID\키를 사용하도록 먼저 업데이트해야 합니다.
> 이렇게 하지 않으면 키 액세스가 손실되면 콘텐츠를 해독할 수 없으므로 인덱스 또는 동의어 맵을 사용할 수 없게 됩니다.   

## <a name="next-steps"></a>다음 단계

Azure 보안 아키텍처에 익숙하지 않은 경우 [Azure 보안 설명서](https://docs.microsoft.com/azure/security/)및 특히 이 문서를 검토합니다.

> [!div class="nextstepaction"]
> [미사용 데이터 암호화](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest)
