---
title: Azure Cosmos DB의 Always Encrypted에서 클라이언트 쪽 암호화 사용
description: Azure Cosmos DB의 Always Encrypted에서 클라이언트 쪽 암호화를 사용하는 방법 알아보기
ms.service: cosmos-db
ms.topic: how-to
ms.date: 05/25/2021
ms.author: thweiss
author: ThomasWeiss
ms.openlocfilehash: 9135e35e74eda1387dc6dee5ba0875c4152439b0
ms.sourcegitcommit: 82d82642daa5c452a39c3b3d57cd849c06df21b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/07/2021
ms.locfileid: "113356405"
---
# <a name="use-client-side-encryption-with-always-encrypted-for-azure-cosmos-db-preview"></a>Azure Cosmos DB의 Always Encrypted에서 클라이언트 쪽 암호화 사용(미리 보기)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Always Encrypted는 신용 카드 번호나 주민 등록 번호(예: 미국 사회 보장 번호)와 같이 Azure Cosmos DB에 저장된 중요한 데이터를 보호하기 위한 기능입니다. Always Encrypted를 사용하면 클라이언트가 클라이언트 애플리케이션의 중요한 데이터를 암호화하고 암호화 키를 데이터베이스에 표시하지 않을 수 있습니다.

Always Encrypted는 Azure Cosmos DB에 클라이언트 쪽 암호화 기능을 제공합니다. 다음 시나리오에서는 클라이언트 쪽 데이터 암호화가 필요할 수 있습니다.

- **특정 기밀 특성이 있는 중요한 데이터 보호**: Always Encrypted를 사용하면 클라이언트가 애플리케이션 내부의 중요한 데이터를 암호화할 수 있으며 일반 텍스트 데이터 또는 암호화 키를 Azure Cosmos DB 서비스에 공개하지 않습니다.
- **속성별 액세스 제어 구현**: 암호화는 Azure Key Vault에서 소유하고 관리하는 키로 제어되므로 액세스 정책을 적용하여 각 클라이언트가 액세스할 수 있는 민감한 속성을 제어할 수 있습니다.

> [!IMPORTANT]
> Azure Cosmos DB의 Always Encrypted는 현재 미리 보기로 제공됩니다. 이 미리 보기 버전은 Service Level Agreement(서비스 수준 약정) 없이 제공되며, 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 자세한 내용은 Microsoft Azure Preview에 대한 [추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소

Azure Cosmos DB의 Always Encrypted 미리 보기 사용을 시작하기 위해 다음을 수행할 수 있습니다.

- 2\.11.13.0 이상 버전의 [Azure Cosmos DB 로컬 에뮬레이터](local-emulator.md)를 사용합니다.
- [이 양식](https://ncv.microsoft.com/poTcF52I6N)을 작성하여 Azure Cosmos DB 계정에서 사용하도록 미리 보기를 요청합니다.

> [!TIP]
> Azure Cosmos DB의 Always Encrypted 미리 보기와 관련하여 공유할 의견이 있나요? [azurecosmosdbcse@service.microsoft.com](mailto:azurecosmosdbcse@service.microsoft.com)에 연락합니다.

## <a name="concepts"></a>개념

Azure Cosmos DB의 Always Encrypted는 클라이언트 쪽 암호화 구성과 관련된 몇 가지 새로운 개념을 도입합니다.

### <a name="encryption-keys"></a>암호화 키

#### <a name="data-encryption-keys"></a>데이터 암호화 키

Always Encrypted를 사용하는 경우 데이터는 미리 만들어야 하는 DEK(데이터 암호화 키)로 암호화됩니다. 이러한 DEK는 Azure Cosmos DB 서비스에 저장되고 데이터베이스 수준에서 정의되므로 여러 컨테이너에서 DEK를 공유할 수 있습니다. DEK 생성은 Azure Cosmos DB SDK를 사용하여 클라이언트 쪽에서 수행됩니다.

다음 작업을 수행할 수 있습니다.

- 속성당 하나의 DEK를 만들어 암호화하거나
- 동일한 DEK를 사용하여 여러 속성을 암호화합니다.

#### <a name="customer-managed-keys"></a>고객 관리형 키

DEK는 Azure Cosmos DB에 저장되기 전에 CMK(고객 관리 키)로 래핑됩니다. CMK는 DEK 래핑 및 래핑 해제를 제어하여 해당 DEK로 암호화된 데이터에 대한 액세스를 효과적으로 제어합니다. CMK 스토리지는 확장 가능/플러그 인 모델로 설계되었으며, 이에 대한 기본 구현은 Azure Key Vault에 저장됩니다.

:::image type="content" source="./media/how-to-always-encrypted/encryption-keys.png" alt-text="암호화 키" border="true":::

### <a name="encryption-policy"></a>암호화 정책

[인덱싱 정책](index-policy.md)과 마찬가지로 암호화 정책은 JSON 속성을 암호화해야 하는 방법을 설명하는 컨테이너 수준 사양입니다. 이 정책은 컨테이너가 만들어지고 변경할 수 없을 때 제공되어야 합니다. 현재 릴리스에서는 암호화 정책을 업데이트할 수 없습니다.

암호화하려는 각 속성에 대해 암호화 정책은 다음을 정의합니다.

- `/property` 양식에 있는 속성 경로입니다. 현재 최상위 수준 경로만 지원되며 `/path/to/property`와 같은 중첩 경로는 지원되지 않습니다.
- 속성을 암호화하고 암호 해독할 때 사용할 [DEK](#data-encryption-keys)의 ID입니다.
- 암호화 유형입니다. 임의 또는 결정적일 수 있습니다.
- 속성을 암호화할 때 사용할 암호화 알고리즘입니다. 지정된 알고리즘은 호환되는 경우 키를 만들 때 정의된 알고리즘을 재정의할 수 있습니다.

> [!NOTE]
> 다음 속성은 암호화할 수 없습니다.
> - ID
> - 컨테이너의 파티션 키

#### <a name="randomized-vs-deterministic-encryption"></a>임의 암호화와 결정적 암호화

Azure Cosmos DB 서비스에서는 Always Encrypted로 암호화된 속성의 일반 텍스트를 볼 수 없습니다. 그러나 속성에 사용되는 암호화 유형에 따라 암호화된 데이터에 대한 일부 쿼리 기능은 계속 지원됩니다. Always Encrypted는 다음 두 가지 유형의 암호화를 지원합니다.

- **결정적 암호화:** 주어진 일반 텍스트 값 및 암호화 구성에 대해 항상 동일한 암호화된 값을 생성합니다. 결정적 암호화를 사용하면 쿼리가 암호화된 속성에 대해 동일한 필터를 수행할 수 있습니다. 그러나 공격자는 암호화된 속성의 패턴을 검사하여 암호화된 값에 대한 정보를 추측할 수 있습니다. True/False 또는 North/South/East/West 지역과 같이 있을 수 있는 암호화된 값의 작은 집합이 있는 경우 특히 그렇습니다.

- **임의 암호화:** 예측하기 어려운 방식으로 데이터를 암호화하는 방법을 사용합니다. 임의 암호화는 더 안전하지만 암호화된 속성에 대한 쿼리가 필터링되지 못하도록 방지합니다.

Always Encrypted의 결정적 및 임의 암호화에 관해 자세히 알아보려면 [IV(초기화 벡터) 생성](/sql/relational-databases/security/encryption/always-encrypted-cryptography#step-1-generating-the-initialization-vector-iv)을 참조하세요.

## <a name="setup-azure-key-vault"></a>Azure Key Vault 설정

Always Encrypted를 시작하는 첫 번째 단계는 Azure Key Vault에서 CMK를 만드는 것입니다.

1. 새 Azure Key Vault 인스턴스를 만들거나 기존 인스턴스를 찾습니다.
1. **키** 섹션에서 새 키를 만듭니다.
1. 키를 만들었으면 현재 버전으로 이동하여 전체 키 식별자를 복사합니다.<br>`https://<my-key-vault>.vault.azure.net/keys/<key>/<version>`. 키 식별자의 끝에서 키 버전을 생략하면 키의 최신 버전이 사용됩니다.

다음으로 Azure Cosmos DB SDK가 Azure Key Vault 인스턴스에 액세스하는 방법을 구성해야 합니다. 이 인증은 Azure AD(Active Directory) ID를 통해 수행됩니다. 대부분의 경우 모든 종류의 ID를 사용할 수 있지만 Azure AD 애플리케이션의 ID 또는 [관리 ID](../active-directory/managed-identities-azure-resources/overview.md)를 클라이언트 코드와 Azure Key Vault 인스턴스 간의 프록시로 사용합니다. 다음 단계를 사용하여 Azure AD 애플리케이션을 프록시로 사용합니다.

1. [이 빠른 시작](../active-directory/develop/quickstart-register-app.md)에 설명된 대로 새 애플리케이션을 만들고 클라이언트 비밀을 추가합니다.

1. Azure Key Vault 인스턴스로 돌아가 **액세스 정책** 섹션으로 이동하여 새 정책을 추가합니다.

   1. **키 권한** 에서 **가져오기**, **나열**, **키 래핑 해제**, **키 래핑**, **확인** 및 **서명** 을 선택합니다.
   1. **주체 선택** 에서 이전에 만든 AAD 애플리케이션을 검색합니다.

### <a name="protect-your-cmk-from-accidental-deletion"></a>CMK가 실수로 삭제되지 않도록 보호

CMK를 실수로 삭제한 후 암호화된 데이터에 대한 액세스 권한을 잃지 않도록 Azure Key Vault 인스턴스의 두 가지 속성, **일시 삭제** 및 **보호 제거** 속성을 설정하는 것이 좋습니다.

새 Azure Key Vault 인스턴스를 만드는 경우 생성 중에 이러한 속성을 사용하도록 설정합니다.

:::image type="content" source="./media/how-to-setup-cmk/portal-akv-prop.png" alt-text="새 Azure Key Vault 인스턴스에 대해 일시 삭제 및 제거 보호를 사용하도록 설정":::

기존 Azure Key Vault 인스턴스를 사용하는 경우 Azure Portal에서 **속성** 섹션을 보면 이러한 속성을 사용하도록 설정되어 있는지 확인할 수 있습니다. 이러한 속성 중 하나라도 사용하도록 설정되어 있지 않으면 다음 문서 중 하나에서 "일시 삭제를 사용하도록 설정" 및 "제거 보호 활성화" 섹션을 참조하세요.

- [PowerShell에서 일시 삭제를 사용하는 방법](../key-vault/general/key-vault-recovery.md)
- [Azure CLI에서 일시 삭제를 사용하는 방법](../key-vault/general/key-vault-recovery.md)

## <a name="initialize-the-sdk"></a>SDK 초기화

> [!NOTE]
> 현재 Azure Cosmos DB의 Always Encrypted가 다음에서 지원됩니다.
> - [Microsoft.Azure.Cosmos.Encryption 패키지](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Encryption)가 있는 **.NET**
> - [azure.cosmos.encryption 패키지](https://mvnrepository.com/artifact/com.azure/azure-cosmos-encryption)를 사용하는 **Java**

Always Encrypted를 사용하려면 `EncryptionKeyStoreProvider`의 인스턴스를 Azure Cosmos DB SDK 인스턴스에 연결해야 합니다. 이 개체는 CMK를 호스트하는 키 저장소와 상호 작용하는 데 사용됩니다. Azure Key Vault의 기본 키 저장소 공급자 이름은 `AzureKeyVaultKeyStoreProvider`로 지정됩니다.

다음 코드 조각에서는 클라이언트 비밀과 함께 Azure AD 애플리케이션의 ID를 사용하는 방법을 보여 줍니다. 다양한 종류의 `TokenCredential` 클래스를 만드는 예를 찾을 수 있습니다.

- [.NET](/dotnet/api/overview/azure/identity-readme#credential-classes)
- [Java에서](/java/api/overview/azure/identity-readme#credential-classes)

# <a name="net"></a>[.NET](#tab/dotnet)

> [!NOTE]
> .NET에서 `AzureKeyVaultKeyStoreProvider` 클래스에 액세스하려면 추가 [Microsoft.Data.Encryption.AzureKeyVaultProvider 패키지](https://www.nuget.org/packages/Microsoft.Data.Encryption.AzureKeyVaultProvider)가 필요합니다.

```csharp
var tokenCredential = new ClientSecretCredential(
    "<aad-app-tenant-id>", "<aad-app-client-id>", "<aad-app-secret>");
var keyStoreProvider = new AzureKeyVaultKeyStoreProvider(tokenCredential);
var client = new CosmosClient("<connection-string>")
    .WithEncryption(keyStoreProvider);
```

# <a name="java"></a>[Java](#tab/java)

```java
TokenCredential tokenCredential = new ClientSecretCredentialBuilder()
    .authorityHost("https://login.microsoftonline.com")
    .tenantId("<aad-app-tenant-id>")
    .clientId("<aad-app-client-id>")
    .clientSecret("<aad-app-secret>")
    .build();
AzureKeyVaultKeyStoreProvider encryptionKeyStoreProvider =
    new AzureKeyVaultKeyStoreProvider(tokenCredential);
CosmosAsyncClient client = new CosmosClientBuilder()
    .endpoint("<endpoint>")
    .key("<primary-key>")
    .buildAsyncClient();
EncryptionAsyncCosmosClient encryptionClient =
    EncryptionAsyncCosmosClient.buildEncryptionAsyncClient(client, encryptionKeyStoreProvider);
```
---

## <a name="create-a-data-encryption-key"></a>데이터 암호화 키 만들기

컨테이너에서 데이터를 암호화하려면 먼저 상위 데이터베이스에 [데이터 암호화 키](#data-encryption-keys)를 만들어야 합니다. 이 작업은 `CreateClientEncryptionKeyAsync` 메서드를 호출하고 다음을 전달하여 수행됩니다.

- 데이터베이스에서 키를 고유하게 식별하는 문자열 식별자입니다.
- 키와 함께 사용할 암호화 알고리즘입니다. 현재 하나의 알고리즘만 지원됩니다.
- Azure Key Vault에 저장된 [CMK](#customer-managed-keys)의 키 식별자입니다. 이 매개 변수는 일반 `EncryptionKeyWrapMetadata` 개체로 전달됩니다. 여기서 `name`은 원하는 식별 이름이 될 수 있으며 `value`는 키 식별자여야 합니다.

# <a name="net"></a>[.NET](#tab/dotnet)

```csharp
var database = client.GetDatabase("my-database");
await database.CreateClientEncryptionKeyAsync(
    "my-key",
    DataEncryptionKeyAlgorithm.AEAD_AES_256_CBC_HMAC_SHA256,
    new EncryptionKeyWrapMetadata(
        keyStoreProvider.ProviderName,
        "akvKey",
        "https://<my-key-vault>.vault.azure.net/keys/<key>/<version>"));
```

# <a name="java"></a>[Java](#tab/java)

```java
EncryptionCosmosAsyncDatabase database =
    client.getEncryptedCosmosAsyncDatabase("my-database");
database.createClientEncryptionKey(
    "my-key",
    CosmosEncryptionAlgorithm.AEAES_256_CBC_HMAC_SHA_256,
    new EncryptionKeyWrapMetadata(
        "akvKey",
        "https://<my-key-vault>.vault.azure.net/keys/<key>/<version>"));
```
---

## <a name="create-a-container-with-encryption-policy"></a>암호화 정책을 사용하여 컨테이너 만들기

컨테이너를 만들 때 컨테이너 수준 암호화 정책을 지정합니다.

# <a name="net"></a>[.NET](#tab/dotnet)

```csharp
var path1 = new ClientEncryptionIncludedPath
{
    Path = "/property1",
    ClientEncryptionKeyId = "my-key",
    EncryptionType = EncryptionType.Deterministic.ToString(),
    EncryptionAlgorithm = DataEncryptionKeyAlgorithm.AEAD_AES_256_CBC_HMAC_SHA256.ToString()
};
var path2 = new ClientEncryptionIncludedPath
{
    Path = "/property2",
    ClientEncryptionKeyId = "my-key",
    EncryptionType = EncryptionType.Randomized.ToString(),
    EncryptionAlgorithm = DataEncryptionKeyAlgorithm.AEAD_AES_256_CBC_HMAC_SHA256.ToString()
};
await database.DefineContainer("my-container", "/partition-key")
    .WithClientEncryptionPolicy()
    .WithIncludedPath(path1)
    .WithIncludedPath(path2)
    .Attach()
    .CreateAsync();
```

# <a name="java"></a>[Java](#tab/java)

```java
ClientEncryptionIncludedPath path1 = new ClientEncryptionIncludedPath();
path1.clientEncryptionKeyId = "my-key":
path1.path = "/property1";
path1.encryptionType = CosmosEncryptionType.DETERMINISTIC;
path1.encryptionAlgorithm = CosmosEncryptionAlgorithm.AEAES_256_CBC_HMAC_SHA_256;

ClientEncryptionIncludedPath path2 = new ClientEncryptionIncludedPath();
path2.clientEncryptionKeyId = "my-key":
path2.path = "/property2";
path2.encryptionType = CosmosEncryptionType.RANDOMIZED;
path2.encryptionAlgorithm = CosmosEncryptionAlgorithm.AEAES_256_CBC_HMAC_SHA_256;

List<ClientEncryptionIncludedPath> paths = new ArrayList<>();
paths.add(path1);
paths.add(path2);

CosmosContainerProperties containerProperties =
    new CosmosContainerProperties("my-container", "/id");
containerProperties.setClientEncryptionPolicy(new ClientEncryptionPolicy(paths));
database.createEncryptionContainerAsync(containerProperties);
```
---

## <a name="read-and-write-encrypted-data"></a>암호화된 데이터 읽기 및 쓰기

### <a name="how-data-gets-encrypted"></a>데이터가 암호화되는 방법

문서가 Azure Cosmos DB에 기록될 때마다 SDK는 암호화 정책을 검색하여 암호화해야 하는 속성과 방법을 파악합니다. 암호화 결과는 base 64 문자열입니다.

**복잡한 유형의 암호화**:

- 암호화할 속성이 JSON 배열인 경우 해당 배열의 모든 항목이 암호화됩니다.

- 암호화할 속성이 JSON 개체인 경우 해당 개체의 리프 값만 암호화됩니다. 중간 하위 속성 이름은 일반 텍스트 형식으로 유지됩니다.

### <a name="read-encrypted-items"></a>암호화된 항목 읽기

지점 읽기(ID 및 파티션 키로 단일 항목 가져오기) 또는 쿼리를 실행하거나 변경 피드를 읽을 때 암호화된 속성의 암호를 해독하는 데 명시적인 작업이 필요하지 않습니다. 그 이유는 다음과 같습니다.

- SDK는 암호 해독이 필요한 속성을 파악하기 위해 암호화 정책을 조회합니다.
- 암호화 결과는 값의 원래 JSON 형식을 포함합니다.

암호화된 속성의 확인과 후속 암호 해독은 요청에서 반환된 결과만을 기준으로 합니다. 예를 들어 `property1`이 암호화되었지만 `property2`(`SELECT property1 AS property2 FROM c`)에 프로젝션되는 경우 SDK에서 수신할 때 암호화된 속성으로 식별되지 않습니다.

### <a name="filter-queries-on-encrypted-properties"></a>암호화된 속성에 대한 쿼리 필터링

암호화된 속성을 필터링하는 쿼리를 작성할 때 `AddParameterAsync` 메서드를 사용하여 쿼리 매개 변수의 값을 전달해야 합니다. 이 메서드에서 사용하는 인수는 다음과 같습니다.

- 쿼리 매개 변수의 이름입니다.
- 쿼리에서 사용할 값입니다.
- 암호화된 속성의 경로(암호화 정책에 정의됨).

> [!IMPORTANT]
> 암호화된 속성은 같음 필터(`WHERE c.property = @Value`)에서만 사용할 수 있습니다. 이와 다르게 사용하면 예측할 수 없고 잘못된 쿼리 결과를 반환합니다. 이 제약 조건은 SDK의 다음 버전에서 더 효과적으로 적용됩니다.

# <a name="net"></a>[.NET](#tab/dotnet)

```csharp
var queryDefinition = container.CreateQueryDefinition(
    "SELECT * FROM c where c.property1 = @Property1");
await queryDefinition.AddParameterAsync(
    "@Property1",
    1234,
    "/property1");
```

# <a name="java"></a>[Java](#tab/java)

```java
EncryptionSqlQuerySpec encryptionSqlQuerySpec = new EncryptionSqlQuerySpec(
    new SqlQuerySpec("SELECT * FROM c where c.property1 = @Property1"), container);
encryptionSqlQuerySpec.addEncryptionParameterAsync(
    new SqlParameter("@Property1", 1234), "/property1")
```
---

### <a name="reading-documents-when-only-a-subset-of-properties-can-be-decrypted"></a>속성의 하위 집합만 해독할 수 있는 경우 문서 읽기

클라이언트에 속성을 암호화하는 데 사용되는 모든 CMK에 대한 액세스 권한이 없는 경우에는 데이터를 다시 읽을 때 속성의 하위 집합만 암호 해독할 수 있습니다. 예를 들어, `property1`이 key1로 암호화되고 `property2`가 key2로 암호화된 경우 key1에 대한 액세스 권한만 있는 클라이언트 애플리케이션은 여전히 데이터를 읽을 수 있지만 `property2`는 읽을 수 없습니다. 이 경우 SQL 쿼리를 통해 데이터를 읽고 클라이언트가 암호 해독할 수 없는 속성을 제거해야 합니다. `SELECT c.property1, c.property3 FROM c`

## <a name="cmk-rotation"></a>CMK 회전

현재 CMK가 손상되었다고 의심되는 경우 CMK를 "회전"할 수 있습니다(즉, 현재 CMK 대신 새 CMK 사용). CMK를 정기적으로 회전하는 일반적인 보안 방법이기도 합니다. 이 회전을 수행하려면 특정 DEK를 래핑하는 데 사용해야 하는 새 CMK의 키 식별자만 제공하면 됩니다. 이 작업은 데이터 암호화에 영향을 미치지 않고 DEK의 보호에 영향을 미칩니다. 회전이 완료될 때까지 이전 CMK에 대한 액세스 권한을 취소하면 안 됩니다.

# <a name="net"></a>[.NET](#tab/dotnet)

```csharp
await database.RewrapClientEncryptionKeyAsync(
    "my-key",
    new EncryptionKeyWrapMetadata(
        keyStoreProvider.ProviderName,
        "akvKey",
        " https://<my-key-vault>.vault.azure.net/keys/<new-key>/<version>"));
```

# <a name="java"></a>[Java](#tab/java)

```java
database. rewrapClientEncryptionKey(
    "my-key",
    new EncryptionKeyWrapMetadata(
        "akvKey", " https://<my-key-vault>.vault.azure.net/keys/<new-key>/<version>"));
```
---

## <a name="next-steps"></a>다음 단계

- [Cosmos DB의 데이터에 대한 보안 액세스](secure-access-to-data.md) 개요를 살펴봅니다.
- [고객 관리형 키](how-to-setup-cmk.md)에 대해 자세히 알아봅니다.