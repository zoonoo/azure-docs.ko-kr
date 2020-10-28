---
title: Azure Data Lake Storage 쿼리 가속을 사용 하 여 데이터 필터링 | Microsoft Docs
description: 쿼리 가속을 사용 하 여 저장소 계정에서 데이터 하위 집합을 검색 합니다.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: how-to
ms.date: 09/09/2020
ms.author: normesta
ms.reviewer: jamsbak
ms.custom: devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: b7f566f85ebdb6b481797823cba78aa968747e9f
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/27/2020
ms.locfileid: "92746418"
---
# <a name="filter-data-by-using-azure-data-lake-storage-query-acceleration"></a>Azure Data Lake Storage 쿼리 가속을 사용 하 여 데이터 필터링

이 문서에서는 쿼리 가속을 사용 하 여 저장소 계정에서 데이터 하위 집합을 검색 하는 방법을 보여 줍니다. 

쿼리 가속을 사용 하면 응용 프로그램 및 분석 프레임 워크에서 지정 된 작업을 수행 하는 데 필요한 데이터만 검색 하 여 데이터 처리를 현저 하 게 최적화할 수 있습니다. 자세히 알아보려면 [쿼리 가속 Azure Data Lake Storage](data-lake-storage-query-acceleration.md)을 참조 하세요.

## <a name="prerequisites"></a>필수 구성 요소

- Azure Storage에 액세스하려면 Azure 구독이 있어야 합니다. 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

- 범용 **v2** 저장소 계정입니다. [저장소 계정 만들기를](../common/storage-quickstart-create-account.md)참조 하세요.

- SDK 관련 필수 구성 요소를 보려면 탭을 선택 합니다.

  ### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

  해당 없음

  ### <a name="net"></a>[.NET](#tab/dotnet)

  [.NET SDK](https://dotnet.microsoft.com/download) 

  ### <a name="java"></a>[Java](#tab/java)

  - [JDK(Java Development Kit)](/java/azure/jdk/?view=azure-java-stable&preserve-view=true), 버전 8 이상

  - [Apache Maven](https://maven.apache.org/download.cgi) 

    > [!NOTE] 
    > 이 문서에서는 Apache Maven를 사용 하 여 Java 프로젝트를 만들었다고 가정 합니다. Apache Maven를 사용 하 여 프로젝트를 만드는 방법에 대 한 예제는 [설정](storage-quickstart-blobs-java.md#setting-up)을 참조 하세요.
  
  ### <a name="python"></a>[Python](#tab/python)

  [Python](https://www.python.org/downloads/) 3.8 이상.

  ### <a name="nodejs"></a>[Node.JS](#tab/nodejs)

  Node.js SDK를 사용 하는 데 필요한 추가 필수 구성 요소가 없습니다.

---

## <a name="enable-query-acceleration"></a>쿼리 가속 사용

쿼리 가속을 사용 하려면 구독에 쿼리 가속 기능을 등록 해야 합니다. 기능이 등록 되었는지 확인 한 후 Azure Storage 리소스 공급자를 등록 해야 합니다. 

### <a name="step-1-register-the-query-acceleration-feature"></a>1 단계: 쿼리 가속 기능 등록

쿼리 가속을 사용 하려면 먼저 구독에 쿼리 가속 기능을 등록 해야 합니다. 

#### <a name="powershell"></a>[PowerShell](#tab/powershell)

1. Windows PowerShell 명령 창을 엽니다.

1. `Connect-AzAccount` 명령을 사용하여 Azure 구독에 로그인하고 화면의 지시를 따릅니다.

   ```powershell
   Connect-AzAccount
   ```

2. Id가 둘 이상의 구독과 연결 된 경우 활성 구독을 설정 합니다.

   ```powershell
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

   `<subscription-id>` 자리 표시자 값을 구독의 ID로 바꿉니다.

3. [AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature) 명령을 사용 하 여 쿼리 가속 기능을 등록 합니다.

   ```powershell
   Register-AzProviderFeature -ProviderNamespace Microsoft.Storage -FeatureName BlobQuery
   ```

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview)을 열거나 Azure CLI를 로컬로 [설치](https://docs.microsoft.com/cli/azure/install-azure-cli)한 경우 Windows PowerShell과 같은 명령 콘솔 애플리케이션을 엽니다.

2. Id가 둘 이상의 구독과 연결 된 경우 활성 구독을 저장소 계정의 구독으로 설정 합니다.

   ```azurecli-interactive
   az account set --subscription <subscription-id>
   ```

   `<subscription-id>` 자리 표시자 값을 구독의 ID로 바꿉니다.

3. [Az feature register](/cli/azure/feature#az-feature-register) 명령을 사용 하 여 쿼리 가속 기능을 등록 합니다.

   ```azurecli
   az feature register --namespace Microsoft.Storage --name BlobQuery
   ```

---

### <a name="step-2-verify-that-the-feature-is-registered"></a>2 단계: 기능이 등록 되었는지 확인

#### <a name="powershell"></a>[PowerShell](#tab/powershell)

등록이 완료 되었는지 확인 하려면 [AzProviderFeature](/powershell/module/az.resources/get-azproviderfeature) 명령을 사용 합니다.

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage -FeatureName BlobQuery
```

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

등록이 완료 되었는지 확인 하려면 [az feature](/cli/azure/feature#az-feature-show) 명령을 사용 합니다.

```azurecli
az feature show --namespace Microsoft.Storage --name BlobQuery
```

---

### <a name="step-3-register-the-azure-storage-resource-provider"></a>3 단계: Azure Storage 리소스 공급자 등록

등록이 승인 된 후 Azure Storage 리소스 공급자를 다시 등록 해야 합니다. 

#### <a name="powershell"></a>[PowerShell](#tab/powershell)

리소스 공급자를 등록 하려면 [AzResourceProvider](/powershell/module/az.resources/register-azresourceprovider) 명령을 사용 합니다.

```powershell
Register-AzResourceProvider -ProviderNamespace 'Microsoft.Storage'
```

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

리소스 공급자를 등록 하려면 [az provider register](/cli/azure/provider#az-provider-register) 명령을 사용 합니다.

```azurecli
az provider register --namespace 'Microsoft.Storage'
```

---

## <a name="set-up-your-environment"></a>환경 설정

### <a name="step-1-install-packages"></a>1 단계: 패키지 설치 

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Az module version 4.6.0 이상을 설치 합니다.

```powershell
Install-Module -Name Az -Repository PSGallery -Force
```

이전 버전의 Az에서 업데이트 하려면 다음 명령을 실행 합니다.

```powershell
Update-Module -Name Az
```

#### <a name="net"></a>[.NET](#tab/dotnet)

1. 명령 프롬프트를 열고 디렉터리를 프로젝트 폴더로 변경 합니다 ( `cd` 예:).

   ```console
   cd myProject
   ```

2. `12.5.0-preview.6`명령을 사용 하 여 .net 용 Azure Blob storage 클라이언트 라이브러리 버전을 설치 합니다 `dotnet add package` . 

   ```console
   dotnet add package Azure.Storage.Blobs -v 12.6.0
   ```

3. 이 문서에 표시 된 예제에서는 [CsvHelper](https://www.nuget.org/packages/CsvHelper/) 라이브러리를 사용 하 여 CSV 파일을 구문 분석 합니다. 해당 라이브러리를 사용 하려면 다음 명령을 사용 합니다.

   ```console
   dotnet add package CsvHelper
   ```

#### <a name="java"></a>[Java](#tab/java)

1. 텍스트 편집기에서 프로젝트의 *pom.xml* 파일을 엽니다. 종속성 그룹에 다음 종속성 요소를 추가합니다. 

   ```xml
   <!-- Request static dependencies from Maven -->
   <dependency>
       <groupId>com.azure</groupId>
       <artifactId>azure-core</artifactId>
       <version>1.6.0</version>
   </dependency>
    <dependency>
        <groupId>org.apache.commons</groupId>
        <artifactId>commons-csv</artifactId>
        <version>1.8</version>
    </dependency>    
    <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-storage-blob</artifactId>
      <version>12.8.0-beta.1</version>
    </dependency>
   ```

#### <a name="python"></a>[Python](#tab/python)

[Pip](https://pypi.org/project/pip/)를 사용 하 여 Python 용 Azure Data Lake Storage 클라이언트 라이브러리를 설치 합니다.

```
pip install azure-storage-blob==12.4.0
```

#### <a name="nodejs"></a>[Node.JS](#tab/nodejs)

터미널 창을 열고 다음 명령을 입력 하 여 JavaScript 용 Data Lake 클라이언트 라이브러리를 설치 합니다.

```javascript
    npm install @azure/storage-blob
    npm install @fast-csv/parse
```

---

### <a name="step-2-add-statements"></a>2 단계: 문 추가

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

해당 없음

#### <a name="net"></a>[.NET](#tab/dotnet)

이러한 `using` 문을 코드 파일의 맨 위에 추가 합니다.

```csharp
using Azure.Storage.Blobs;
using Azure.Storage.Blobs.Models;
using Azure.Storage.Blobs.Specialized;
```

쿼리 가속은 CSV 및 Json 형식의 데이터를 검색 합니다. 따라서 사용 하도록 선택한 모든 CSV 또는 Json 구문 분석 라이브러리에 대해 using 문을 추가 해야 합니다. 이 문서에 표시 된 예제에서는 NuGet에서 사용할 수 있는 [CsvHelper](https://www.nuget.org/packages/CsvHelper/) 라이브러리를 사용 하 여 CSV 파일을 구문 분석 합니다. 따라서 `using` 코드 파일의 맨 위에 이러한 문을 추가 합니다.

```csharp
using CsvHelper;
using CsvHelper.Configuration;
```

이 문서에 나와 있는 예제를 컴파일하려면 이러한 문도 추가 해야 `using` 합니다.

```csharp
using System.Threading.Tasks;
using System.IO;
using System.Globalization;
```

#### <a name="java"></a>[Java](#tab/java)

이러한 `import` 문을 코드 파일의 맨 위에 추가 합니다.

```java
import com.azure.storage.blob.*;
import com.azure.storage.blob.options.*;
import com.azure.storage.blob.models.*;
import com.azure.storage.common.*;
import java.io.*;
import java.util.function.Consumer;
import org.apache.commons.csv.*;
```

#### <a name="python"></a>[Python](#tab/python)

이러한 import 문을 코드 파일의 맨 위에 추가 합니다.

```python
import sys, csv
from azure.storage.blob import BlobServiceClient, ContainerClient, BlobClient, DelimitedTextDialect, BlobQueryError
```

### <a name="nodejs"></a>[Node.JS](#tab/nodejs)

`storage-blob`이 문을 코드 파일의 맨 위에 배치 하 여 모듈을 포함 합니다. 

```javascript
const { BlobServiceClient } = require("@azure/storage-blob");
```

쿼리 가속은 CSV 및 Json 형식의 데이터를 검색 합니다. 따라서 사용 하도록 선택한 모든 CSV 또는 Json 구문 분석 모듈에 대해 문을 추가 해야 합니다. 이 문서에 표시 된 예제에서는 [fast csv](https://www.npmjs.com/package/fast-csv) 모듈을 사용 하 여 csv 파일을 구문 분석 합니다. 따라서이 문을 코드 파일의 맨 위에 추가 합니다.

```javascript
const csv = require('@fast-csv/parse');
```

---

## <a name="retrieve-data-by-using-a-filter"></a>필터를 사용 하 여 데이터 검색

SQL을 사용 하 여 쿼리 가속 요청에서 행 필터 조건자 및 열 프로젝션을 지정할 수 있습니다. 다음 코드는 저장소의 CSV 파일을 쿼리하고 세 번째 열이 값과 일치 하는 모든 데이터 행을 반환 합니다 `Hemingway, Ernest` . 

- SQL 쿼리에서 키워드는 `BlobStorage` 쿼리 중인 파일을 나타내는 데 사용 됩니다.

- 열 참조는 `_N` 첫 번째 열이 인 위치로 지정 됩니다 `_1` . 원본 파일에 머리글 행이 포함 된 경우 머리글 행에 지정 된 이름으로 열을 참조할 수 있습니다. 

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell
Function Get-QueryCsv($ctx, $container, $blob, $query, $hasheaders) {
    $tempfile = New-TemporaryFile
    $informat = New-AzStorageBlobQueryConfig -AsCsv -HasHeader:$hasheaders
    Get-AzStorageBlobQueryResult -Context $ctx -Container $container -Blob $blob -InputTextConfiguration $informat -OutputTextConfiguration (New-AzStorageBlobQueryConfig -AsCsv -HasHeader) -ResultFile $tempfile.FullName -QueryString $query -Force
    Get-Content $tempfile.FullName
}

$container = "data"
$blob = "csv/csv-general/seattle-library.csv"
Get-QueryCsv $ctx $container $blob "SELECT * FROM BlobStorage WHERE _3 = 'Hemingway, Ernest, 1899-1961'" $false

```

### <a name="net"></a>[.NET](#tab/dotnet)

비동기 메서드는 쿼리 `BlobQuickQueryClient.QueryAsync` 가속 API로 쿼리를 전송 하 고 결과를 [Stream](https://docs.microsoft.com/dotnet/api/system.io.stream) 개체로 응용 프로그램에 다시 스트리밍합니다.

```cs
static async Task QueryHemingway(BlockBlobClient blob)
{
    string query = @"SELECT * FROM BlobStorage WHERE _3 = 'Hemingway, Ernest, 1899-1961'";
    await DumpQueryCsv(blob, query, false);
}

private static async Task DumpQueryCsv(BlockBlobClient blob, string query, bool headers)
{
    try
    {
        var options = new BlobQueryOptions() {
            InputTextConfiguration = new BlobQueryCsvTextOptions() { HasHeaders = headers },
            OutputTextConfiguration = new BlobQueryCsvTextOptions() { HasHeaders = true },
            ProgressHandler = new Progress<long>((finishedBytes) => Console.Error.WriteLine($"Data read: {finishedBytes}"))
        };
        options.ErrorHandler += (BlobQueryError err) => {
            Console.ForegroundColor = ConsoleColor.Red;
            Console.Error.WriteLine($"Error: {err.Position}:{err.Name}:{err.Description}");
            Console.ResetColor();
        };
        // BlobDownloadInfo exposes a Stream that will make results available when received rather than blocking for the entire response.
        using (var reader = new StreamReader((await blob.QueryAsync(
                query,
                options)).Value.Content))
        {
            using (var parser = new CsvReader(reader, new CsvConfiguration(CultureInfo.CurrentCulture) { HasHeaderRecord = true }))
            {
                while (await parser.ReadAsync())
                {
                    Console.Out.WriteLine(String.Join(" ", parser.Context.Record));
                }
            }
        }
    }
    catch (Exception ex)
    {
        Console.Error.WriteLine("Exception: " + ex.ToString());
    }
}

```

### <a name="java"></a>[Java](#tab/java)

메서드는 쿼리 `BlobQuickQueryClient.openInputStream()` 가속 API로 쿼리를 보낸 다음 그 결과를 `InputStream` 다른 InputStream 개체와 같이 읽을 수 있는 개체로 응용 프로그램에 다시 스트리밍합니다.

```java
static void QueryHemingway(BlobClient blobClient) {
    String expression = "SELECT * FROM BlobStorage WHERE _3 = 'Hemingway, Ernest, 1899-1961'";
    DumpQueryCsv(blobClient, expression, true);
}

static void DumpQueryCsv(BlobClient blobClient, String query, Boolean headers) {
    try {
        BlobQuerySerialization input = new BlobQueryDelimitedSerialization()
            .setRecordSeparator('\n')
            .setColumnSeparator(',')
            .setHeadersPresent(headers)
            .setFieldQuote('\0')
            .setEscapeChar('\\');
        BlobQuerySerialization output = new BlobQueryDelimitedSerialization()
            .setRecordSeparator('\n')
            .setColumnSeparator(',')
            .setHeadersPresent(true)
            .setFieldQuote('\0')
            .setEscapeChar('\n');
        Consumer<BlobQueryError> errorConsumer = System.out::println;
        Consumer<BlobQueryProgress> progressConsumer = progress -> System.out.println("total bytes read: " + progress.getBytesScanned());
        BlobQueryOptions queryOptions = new BlobQueryOptions(query)
            .setInputSerialization(input)
            .setOutputSerialization(output)
            .setErrorConsumer(errorConsumer)
            .setProgressConsumer(progressConsumer);            

        /* Open the query input stream. */
        InputStream stream = blobClient.openQueryInputStream(queryOptions).getValue();
        try (BufferedReader reader = new BufferedReader(new InputStreamReader(stream))) {
            /* Read from stream like you normally would. */
            for (CSVRecord record : CSVParser.parse(reader, CSVFormat.EXCEL.withHeader())) {
                System.out.println(record.toString());
            }
        }
    } catch (Exception e) {
        System.err.println("Exception: " + e.toString());
        e.printStackTrace(System.err);
    }
}
```

### <a name="python"></a>[Python](#tab/python)

```python
def query_hemingway(blob: BlobClient):
    query = "SELECT * FROM BlobStorage WHERE _3 = 'Hemingway, Ernest, 1899-1961'"
    dump_query_csv(blob, query, False)

def dump_query_csv(blob: BlobClient, query: str, headers: bool):
    qa_reader = blob.query_blob(query, blob_format=DelimitedTextDialect(has_header=headers), on_error=report_error, encoding='utf-8')
    # records() returns a generator that will stream results as received. It will not block pending all results.
    csv_reader = csv.reader(qa_reader.records())
    for row in csv_reader:
        print("*".join(row))
```

### <a name="nodejs"></a>[Node.JS](#tab/nodejs)

이 예에서는 쿼리 가속 API로 쿼리를 전송 하 고 결과를 다시 스트리밍합니다.

```javascript
async function queryHemingway(blob)
{
    const query = "SELECT * FROM BlobStorage WHERE _3 = 'Hemingway, Ernest, 1899-1961'";
    await dumpQueryCsv(blob, query, false);
}

async function dumpQueryCsv(blob, query, headers)
{
    var response = await blob.query(query, {
        inputTextConfiguration: {
            kind: "csv",
            recordSeparator: '\n',
            hasHeaders: headers
        },
        outputTextConfiguration: {
            kind: "csv",
            recordSeparator: '\n',
            hasHeaders: true
        },
        onProgress: (progress) => console.log(`Data read: ${progress.loadedBytes}`),
        onError: (err) => console.error(`Error: ${err.position}:${err.name}:${err.description}`)});
    return new Promise(
        function (resolve, reject) {
            csv.parseStream(response.readableStreamBody)
                .on('data', row => console.log(row))
                .on('error', error => {
                    console.error(error);
                    reject(error);
                })
                .on('end', rowCount => resolve());
    });
}
```

---

## <a name="retrieve-specific-columns"></a>특정 열 검색

열의 하위 집합으로 결과의 범위를 지정할 수 있습니다. 이렇게 하면 지정 된 계산을 수행 하는 데 필요한 열만 검색할 수 있습니다. 따라서 네트워크를 통해 전송 되는 데이터가 줄어들기 때문에 응용 프로그램 성능을 향상 시키고 비용을 절감할 수 있습니다. 

이 코드는 `BibNum` 데이터 집합의 모든 책에 대 한 열만 검색 합니다. 또한 원본 파일의 머리글 행에 있는 정보를 사용 하 여 쿼리의 열을 참조 합니다.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell
Function Get-QueryCsv($ctx, $container, $blob, $query, $hasheaders) {
    $tempfile = New-TemporaryFile
    $informat = New-AzStorageBlobQueryConfig -AsCsv -HasHeader:$hasheaders
    Get-AzStorageBlobQueryResult -Context $ctx -Container $container -Blob $blob -InputTextConfiguration $informat -OutputTextConfiguration (New-AzStorageBlobQueryConfig -AsCsv -HasHeader) -ResultFile $tempfile.FullName -QueryString $query -Force
    Get-Content $tempfile.FullName
}

$container = "data"
$blob = "csv/csv-general/seattle-library-with-headers.csv"
Get-QueryCsv $ctx $container $blob "SELECT BibNum FROM BlobStorage" $true

```

### <a name="net"></a>[.NET](#tab/dotnet)

```cs
static async Task QueryBibNum(BlockBlobClient blob)
{
    string query = @"SELECT BibNum FROM BlobStorage";
    await DumpQueryCsv(blob, query, true);
}
```

### <a name="java"></a>[Java](#tab/java)

```java
static void QueryBibNum(BlobClient blobClient)
{
    String expression = "SELECT BibNum FROM BlobStorage";
    DumpQueryCsv(blobClient, expression, true);
}
```

### <a name="python"></a>[Python](#tab/python)

```python
def query_bibnum(blob: BlobClient):
    query = "SELECT BibNum FROM BlobStorage"
    dump_query_csv(blob, query, True)
```

### <a name="nodejs"></a>[Node.JS](#tab/nodejs)

```javascript
async function queryBibNum(blob)
{
    const query = "SELECT BibNum FROM BlobStorage";
    await dumpQueryCsv(blob, query, true);
}
```

---

다음 코드에서는 행 필터링 및 열 프로젝션을 동일한 쿼리로 결합 합니다. 

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell
Get-QueryCsv $ctx $container $blob $query $true

Function Get-QueryCsv($ctx, $container, $blob, $query, $hasheaders) {
    $tempfile = New-TemporaryFile
    $informat = New-AzStorageBlobQueryConfig -AsCsv -HasHeader:$hasheaders
    Get-AzStorageBlobQueryResult -Context $ctx -Container $container -Blob $blob -InputTextConfiguration $informat -OutputTextConfiguration (New-AzStorageBlobQueryConfig -AsCsv -HasHeader) -ResultFile $tempfile.FullName -QueryString $query -Force
    Get-Content $tempfile.FullName
}

$container = "data"
$query = "SELECT BibNum, Title, Author, ISBN, Publisher, ItemType 
            FROM BlobStorage 
            WHERE ItemType IN 
                ('acdvd', 'cadvd', 'cadvdnf', 'calndvd', 'ccdvd', 'ccdvdnf', 'jcdvd', 'nadvd', 'nadvdnf', 'nalndvd', 'ncdvd', 'ncdvdnf')"

```

### <a name="net"></a>[.NET](#tab/dotnet)

```cs
static async Task QueryDvds(BlockBlobClient blob)
{
    string query = @"SELECT BibNum, Title, Author, ISBN, Publisher, ItemType 
        FROM BlobStorage 
        WHERE ItemType IN 
            ('acdvd', 'cadvd', 'cadvdnf', 'calndvd', 'ccdvd', 'ccdvdnf', 'jcdvd', 'nadvd', 'nadvdnf', 'nalndvd', 'ncdvd', 'ncdvdnf')";
    await DumpQueryCsv(blob, query, true);
}
```

### <a name="java"></a>[Java](#tab/java)

```java
static void QueryDvds(BlobClient blobClient)
{
    String expression = "SELECT BibNum, Title, Author, ISBN, Publisher, ItemType " +
                        "FROM BlobStorage " +
                        "WHERE ItemType IN " +
                        "   ('acdvd', 'cadvd', 'cadvdnf', 'calndvd', 'ccdvd', 'ccdvdnf', 'jcdvd', 'nadvd', 'nadvdnf', 'nalndvd', 'ncdvd', 'ncdvdnf')";
    DumpQueryCsv(blobClient, expression, true);
}
```

### <a name="python"></a>[Python](#tab/python)

```python
def query_dvds(blob: BlobClient):
    query = "SELECT BibNum, Title, Author, ISBN, Publisher, ItemType "\
        "FROM BlobStorage "\
        "WHERE ItemType IN "\
        "   ('acdvd', 'cadvd', 'cadvdnf', 'calndvd', 'ccdvd', 'ccdvdnf', 'jcdvd', 'nadvd', 'nadvdnf', 'nalndvd', 'ncdvd', 'ncdvdnf')"
    dump_query_csv(blob, query, True)
```

### <a name="nodejs"></a>[Node.JS](#tab/nodejs)

```javascript
async function queryDvds(blob)
{
    const query = "SELECT BibNum, Title, Author, ISBN, Publisher, ItemType " +
                  "FROM BlobStorage " +
                  "WHERE ItemType IN " + 
                  " ('acdvd', 'cadvd', 'cadvdnf', 'calndvd', 'ccdvd', 'ccdvdnf', 'jcdvd', 'nadvd', 'nadvdnf', 'nalndvd', 'ncdvd', 'ncdvdnf')";
    await dumpQueryCsv(blob, query, true);
}
```

---

## <a name="next-steps"></a>다음 단계

- [Azure Data Lake Storage 쿼리 가속](data-lake-storage-query-acceleration.md)
- [쿼리 가속 SQL 언어 참조](query-acceleration-sql-reference.md)
