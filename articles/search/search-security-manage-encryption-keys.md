---
title: -미사용 암호화 Azure Key Vault (미리 보기)-Azure Search에서에서 고객 관리 키 사용
description: 인덱스 및 생성 하 고 Azure Key Vault에서 관리 하는 키를 통해 Azure Search에서 동의어 맵을 통해 서버 쪽 암호화를 추가 합니다.
author: NatiNimni
manager: jlembicz
ms.author: natinimn
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.custom: ''
ms.openlocfilehash: 567f32cba76aaf2d1657b2476c4d11596d44dec5
ms.sourcegitcommit: 45e4466eac6cfd6a30da9facd8fe6afba64f6f50
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/07/2019
ms.locfileid: "66753876"
---
# <a name="azure-search-encryption-using-customer-managed-keys-in-azure-key-vault"></a>Azure Key Vault에서 고객 관리 키를 사용 하 여 azure Search 암호화

> [!Note]
> 미리 보기로 제공 되며 프로덕션 사용에 대 한 용도 아니지만 고객 관리 키를 사용 하 여 암호화가 됩니다. 이 기능은 [REST API 버전 2019-05-06-미리 보기](search-api-preview.md)에서 제공됩니다. .NET SDK 버전 8.0-미리 보기를 사용할 수도 있습니다.
>
> 이 기능은 무료 서비스에 사용할 수 없습니다. 2019-01-01 이후에 생성 되는 청구 가능한 검색 서비스를 사용 해야 합니다. 지금은 포털 지원 되지 않습니다.

기본적으로 Azure Search는 사용자 콘텐츠를 사용 하 여 미사용 암호화 [서비스 관리 키](https://docs.microsoft.com/azure/security/azure-security-encryption-atrest#data-encryption-models)합니다. 기본 암호화 키를 생성 하 고 Azure Key Vault에서 관리를 사용 하 여 추가 암호화 계층을 사용 하 여 보완할 수 있습니다. 이 문서의 단계를 안내합니다.

서버 쪽 암호화의 통합을 통해 지원 됩니다 [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)합니다. 사용자 고유의 암호화 키를 만들고 Azure Key Vault에 저장할 수도 있고 Azure Key Vault의 API를 사용하여 암호화 키를 생성할 수도 있습니다. Azure Key Vault를 사용 하 여 키 사용을 감사할 수도 있습니다. 

해당 개체를 만들면 인덱스 또는 동의어 맵 수준 및 검색 서비스 수준에 없는 고객 관리 키를 사용 하 여 암호화가 구성 됩니다. 이미 존재 하는 콘텐츠를 암호화할 수 없습니다. 

다른 키 자격 증명 모음에서 다른 키를 사용할 수 있습니다. 즉, 단일 검색 서비스는 잠재적 고객 관리 키를 사용 하 여 암호화 되지 않은 indexes\synonym 지도 함께 다른 고객 관리 키를 사용 하 여 암호화 각각 여러 암호화 indexes\synonym 맵을 호스트할 수 있습니다. 

## <a name="prerequisites"></a>필수 조건

다음 서비스는이 예제에서 사용 됩니다. 

+ [Azure Search 서비스를 만들거나](search-create-service-portal.md) 현재 구독에서 [기존 서비스를 찾습니다](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices). 이 자습서에서는 체험 서비스를 사용할 수 있습니다.

+ [Azure Key Vault 리소스 만들기](https://docs.microsoft.com/azure/key-vault/quick-create-portal#create-a-vault) 구독의 기존 자격 증명 모음을 찾거나 합니다.

+ [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) 나 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) 구성 작업에 사용 됩니다.

+ [Postman](search-fiddler.md), [Azure PowerShell](search-create-index-rest-api.md) 하 고 [Azure Search SDK](https://aka.ms/search-sdk-preview) 미리 보기 REST API를 호출할 수 있습니다. 포털 또는 지금은 고객이 관리 하는 암호화에 대 한.NET SDK를 지원 하지 있습니다.

## <a name="1---enable-key-recovery"></a>1--키 복구를 사용 하는 중

이 단계는 선택 사항 이지만 권장 됩니다. Azure Key Vault 리소스를 만든 후 사용 하도록 설정 **일시 삭제** 하 고 **보호 제거** 다음 PowerShell 또는 Azure CLI 명령을 실행 하 여 선택한 Key vault에서:   

```powershell
$resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "<vault_name>").ResourceId

$resource.Properties | Add-Member -MemberType NoteProperty -Name "enableSoftDelete" -Value 'true'

$resource.Properties | Add-Member -MemberType NoteProperty -Name "enablePurgeProtection" -Value 'true'

Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
```

```azurecli-interactive
az keyvault update -n <vault_name> -g <resource_group> --enable-soft-delete --enable-purge-protection
```

>[!Note]
> 고객 관리 키 기능을 사용 하 여 암호화를 매우 특성으로 인해 Azure Search 됩니다 Azure 키 자격 증명 모음 키 삭제 되 면 데이터를 검색할 수 있습니다. Key Vault 키 실수로 인해 발생 하는 데이터 손실을 방지 하기 위해 선택한 key vault에서 일시 삭제 및 보호 제거를 사용 것이 좋습니다. 자세한 내용은 [Azure Key Vault 일시 삭제](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete)합니다.   

## <a name="2---create-a-new-key"></a>2-새 키 만들기

기존 키를 Azure Search 콘텐츠를 암호화를 사용 하는 경우이 단계를 건너뜁니다.

1. [Azure portal에 로그인](https://portal.azure.com) 키 자격 증명 모음 대시보드로 이동 합니다.

1. 선택 된 **키** 왼쪽된 탐색 창에서 설정 하 고 클릭 **+ 생성/가져오기**합니다.

1. 에 **키를 만듭니다** 창의 목록에서 **옵션**, 키를 만드는 데 사용할 방법을 선택 합니다. 할 수 있습니다 **생성** 새 키를 **업로드** 기존 키를 사용할지 **백업 복원** 키의 백업을 선택 합니다.

1. 입력 한 **이름을** 키 및 필요에 따라 다른 키 속성을 선택 합니다.

1. 클릭 합니다 **만들기** 단추 배포를 시작 합니다.

키 식별자를 기록해 –에서 구성 된이 **키 값 Uri**, **키 이름**, 및 **키 버전**합니다. Azure Search에서 암호화 된 인덱스를 정의 하도록 해야 합니다.
 
![새 키 자격 증명 모음 키를 만듭니다](./media/search-manage-encryption-keys/create-new-key-vault-key.png "새 키 자격 증명 모음 키 만들기")

## <a name="3---create-a-service-identity"></a>3-서비스 id 만들기

Search 서비스에 id 할당 search 서비스에 Key Vault 액세스 권한을 부여할 수 있습니다. 검색 서비스는 Azure Key vault를 사용 하 여 인증을 해당 id를 사용 합니다.

Azure Search에서는 identity를 할당 하기 위한 두 가지 방법으로 지원 합니다: 관리 되는 id 또는 외부에서 관리 되는 Azure Active Directory 응용 프로그램입니다. 

가능 하면 관리 되는 id를 사용 합니다. Search 서비스에 id를 할당 하는 가장 간단한 방법은 이며 대부분의 시나리오에서 작동 해야 합니다. 인덱스에 동의어 맵을 여러 키를 사용 하는 경우 솔루션 작업이 인증 id를 기반으로 하는 분산된 아키텍처에서 사용 하 여 고급 경우 [외부에서 관리 되는 Azure Active Directory 방식을](#aad-app)이 문서의 끝에 설명 되어 있습니다.

 일반적으로 관리 되는 id를 코드에 자격 증명을 저장 하지 않고 Azure Key Vault에 인증 하 여 검색 서비스를 활성화 합니다. 이 유형의 관리 되는 id의 수명 주기는 하나의 관리 되는 id를 하나만 사용할 수 있는 검색 서비스의 수명에 연결 됩니다. [관리 되는 id에 대 한 자세한](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)합니다.

1. 관리 되는 id를 만드는 [toAzure 포털에 로그인](https://portal.azure.com) search 서비스 대시보드를 엽니다. 

1. 클릭 **Identity** 왼쪽된 탐색 창에서 해당 상태를 변경할 **에**를 클릭 하 고 **저장**합니다.

![관리 되는 id를 사용 하도록 설정](./media/search-enable-msi/enable-identity-portal.png "관리 id를 사용 하도록 설정")

## <a name="4---grant-key-access-permissions"></a>4-키 액세스 권한을 부여 합니다.

검색을 부여 해야 키 자격 증명 모음 키를 사용 하 여 검색 서비스를 사용 하려면 특정 권한이 서비스입니다.

언제 든 지 액세스 권한은 취소할 수 없습니다. 해지 되 면 모든 검색 서비스 인덱스 또는 동의어 맵 해당 key vault를 사용 하는 없게 됩니다. 나중에 키 자격 증명 모음 액세스 권한을 복원 index\synonym 맵 액세스를 복원 됩니다. 자세한 내용은 [key vault에 대 한 액세스 보호](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault)합니다.

1. [Azure portal에 로그인](https://portal.azure.com) 키 자격 증명 모음 개요 페이지를 엽니다. 

1. 선택 된 **액세스 정책** 왼쪽된 탐색 창에서 설정 하 고 클릭 **+ 새로 추가**합니다.

   ![새 키 자격 증명 모음 액세스 정책 추가](./media/search-manage-encryption-keys/add-new-key-vault-access-policy.png "새 키 자격 증명 모음 액세스 정책 추가")

1. 클릭 **보안 주체 선택** Azure Search 서비스를 선택 합니다. 이름이 나 관리 되는 id를 사용 하도록 설정한 후 표시 된 개체 ID를 검색할 수 있습니다.

   ![선택 키 자격 증명 모음 액세스 정책 주체](./media/search-manage-encryption-keys/select-key-vault-access-policy-principal.png "선택 키 자격 증명 모음 액세스 정책 주체")

1. 클릭할 **키 권한** 선택한 *가져오기*를 *래핑 해제 키* 및 *래핑할 키*합니다. 사용할 수는 *Azure Data Lake Storage 또는 Azure Storage* 템플릿을 신속 하 게 필요한 권한을 선택 합니다.

   다음을 사용 하 여 azure search를 얻어야 [액세스 권한을](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-operations):

   * *가져올* -Key Vault에서 키의 공개 부분을 검색 하려면 검색 서비스를 허용 합니다.
   * *키 래핑* -내부 암호화 키를 보호 하기 위해 키를 사용 하도록 검색 서비스를 허용 합니다.
   * *키 래핑 해제* -검색 서비스에 키를 사용 하 여 내부 암호화 키 래핑 해제를 허용 합니다.

   ![키 자격 증명 모음 액세스 정책 키 사용 권한 선택](./media/search-manage-encryption-keys/select-key-vault-access-policy-key-permissions.png "키 자격 증명 모음 액세스 정책 키 사용 권한 선택")

1. 클릭 **확인** 하 고 **저장** 액세스 정책 변경 내용이 있습니다.

> [!Important]
> Azure search에서 암호화 된 콘텐츠는 특정 특정 Azure Key Vault 키를 사용 하도록 구성 된 **버전**합니다. 인덱스 또는 동의어 맵을 새 key\version 사용 하도록 업데이트 해야 합니다 키 또는 버전을 변경한 경우 **하기 전에** 이전 key\version를 삭제 합니다. 이렇게 하면 인덱스 렌더링 됩니다 또는 동의어 사용할 수 없는 지도 반드시 없게 키 액세스 손실 되 면 콘텐츠를 암호 해독 합니다.   

## <a name="5---encrypt-content"></a>5-콘텐츠를 암호화 합니다.

고객 관리 키를 사용 하 여 암호화 하는 인덱스 또는 동의어 맵을 만들는 아직 Azure portal을 사용 하 여 없습니다. 인덱스 또는 동의어 맵 이러한 만드는 Azure Search REST API를 사용 합니다.

인덱스와 동의어 지도 지 원하는 새로운 최상위 **encryptionKey** 속성 키를 지정 하는 데 사용 합니다. 

사용 하 여는 **키 자격 증명 모음 Uri**, **키 이름** 하며 **키 버전** 에 키 자격 증명 모음 키를 만들 수 있습니다는 **encryptionKey** 정의:

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
> 이러한 키 자격 증명 모음 세부 정보를 하나도 암호 라고 하며 Azure portal에서 관련 Azure Key Vault 키 페이지로 이동 하 여 쉽게 검색할 수 없습니다.

AAD 응용 프로그램을 관리 되는 id를 사용 하는 대신 Key Vault 인증에 대 한를 사용 하는 경우에 AAD 응용 프로그램 추가 **자격 증명에 액세스할** 암호화 키: 
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

## <a name="example-index-encryption"></a>예제: 인덱스 암호화
REST API를 통해 새 인덱스를 만드는 세부 정보를 찾을 수 없습니다 [Create Index (Azure Search 서비스 REST API)](https://docs.microsoft.com/rest/api/searchservice/create-index), 여기에서 유일한 차이점 인덱스 정의의 일부로 암호화 키 세부 정보를 지정 됩니다. 

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
이제 인덱스 만들기 요청을 보낼 수 있으며 일반적으로 인덱스를 사용할 수 있습니다.

## <a name="example-synonym-map-encryption"></a>예제: 동의어 맵 암호화

REST API를 통해 새 동의어 맵 만들기의 세부 정보를 찾을 수 있습니다 [동의어 맵 만들기 (Azure Search 서비스 REST API)](https://docs.microsoft.com/rest/api/searchservice/create-synonym-map), 여기에서 유일한 차이점 동의어 맵 정의의 일부로 암호화 키 세부 정보를 지정 됩니다. 

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
이제 동의어 맵 만들기 요청을 보낼 수 있으며 그런 다음 일반적으로 사용을 시작할 수 있습니다.

>[!Important] 
> 하는 동안 **encryptionKey** 기존 Azure Search 인덱스에 추가할 수 없습니다 또는 동의어 맵을, 세 가지 주요 자격 증명 모음 세부 정보 (예: 키 버전 업데이트)에 대 한 다른 값을 제공 하 여 업데이트할 수 있습니다. 새 Key Vault 키 또는 새 키 버전을 변경 하는 경우 키를 사용 하는 모든 Azure Search 인덱스 또는 동의어 맵을 먼저 수 사용 하도록 업데이트 해야 새 key\version **하기 전에** 이전 key\version를 삭제 합니다. 이렇게 하면 인덱스 렌더링 됩니다 또는 동의어 맵을 사용할 수 없는 암호를 해독할 콘텐츠 키에 한 번 액세스 수는 없습니다 손실 됩니다.   
> 나중에 키 자격 증명 모음 액세스 권한을 복원 콘텐츠 액세스를 복원 됩니다.

## <a name="aad-app"></a> 고급: 외부에서 관리 되는 Azure Active Directory 응용 프로그램 사용

경우 관리 되는 id를 만들면 Azure Active Directory 응용 프로그램 보안을 사용 하 여 주 Azure Search 서비스에 대 한 수는 없습니다. 특히 id를 관리 되는 이러한 조건에서 실행 가능한 아닙니다.

* 부여할 수는 없습니다 직접 검색 서비스 액세스 키 자격 증명 모음 (예를 들어 검색 서비스가 있는 경우 Azure Key Vault는 다른 Active Directory 테 넌 트)입니다.

* 단일 검색 서비스는 각각 다른 Key vault에서 다른 키를 사용 하 여 각 키 자격 증명 모음을 사용 해야 여러 암호화 indexes\synonym 맵을 호스트 하는 데 필요한 **다른 id** 인증에 대 한 합니다. 다른 id를 사용 하는 경우 다른 키 자격 증명 모음을 관리 하는 요구 사항이 아니며, 위의 관리 id 옵션을 사용 하는 것이 좋습니다.  

이러한 토폴로지를 수용할 수 있도록 Azure Key Vault 고 검색 서비스 간 인증에 대 한 Azure Active Directory (AAD) 응용 프로그램을 사용 하 여 지원을 검색 합니다.    
포털에서 AAD 응용 프로그램을 만들려면:

1. [Azure Active Directory 애플리케이션을 만듭니다](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#create-an-azure-active-directory-application).

1. [응용 프로그램 ID 및 인증 키 가져오기](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) 암호화 된 인덱스를 만드는 데 필요한 있을 것입니다. 값을 제공 해야 합니다 **응용 프로그램 ID** 하 고 **인증 키**합니다.

>[!Important]
> 를 관리 되는 id를 대신 인증의 AAD 응용 프로그램을 사용 하도록 결정 하는 경우 Azure Search는 사용자를 대신해, AAD 응용 프로그램을 관리할 수 있는 권한이 없습니다 이므로 정기적으로 회전 같은 AAD 응용 프로그램을 관리 하는 사실을 고려해합니다 응용 프로그램 인증 키입니다.
> AAD 응용 프로그램 또는 해당 인증 키를 변경할 때 해당 응용 프로그램을 사용 하는 모든 Azure Search 인덱스 또는 동의어 맵을 먼저 업데이트 해야 ID\key 새 응용 프로그램을 사용 하 여 **하기 전에** 이전 응용 프로그램을 삭제 또는 권한 부여 키 및 Key Vault 액세스를 취소 하기 전에 합니다.
> 이렇게 하면 인덱스 렌더링 됩니다 또는 동의어 맵을 사용할 수 없는 암호를 해독할 콘텐츠 키에 한 번 액세스 수는 없습니다 손실 됩니다.   

## <a name="next-steps"></a>다음 단계

Azure 보안 아키텍처를 잘 모르는 경우 검토 합니다 [Azure 보안 설명서](https://docs.microsoft.com/azure/security/), 특히,이 문서:

> [!div class="nextstepaction"]
> [미사용 데이터 암호화](https://docs.microsoft.com/azure/security/azure-security-encryption-atrest)
