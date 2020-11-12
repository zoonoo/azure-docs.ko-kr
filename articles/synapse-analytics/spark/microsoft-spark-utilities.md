---
title: Microsoft Spark 유틸리티 소개
description: Azure Synapse Analytics 노트북에서 MSSparkutils를 사용 하는 방법에 대 한 자습서입니다.
author: ruxu
services: synapse-analytics
ms.service: synapse-analytics
ms.topic: reference
ms.subservice: spark
ms.date: 09/10/2020
ms.author: ruxu
ms.reviewer: ''
zone_pivot_groups: programming-languages-spark-all-minus-sql
ms.openlocfilehash: c03d8e744598386db3d6d03a71e4d1b735d9d71f
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2020
ms.locfileid: "94533279"
---
# <a name="introduction-of-microsoft-spark-utilities"></a>Microsoft Spark 유틸리티 소개

Microsoft Spark 유틸리티 (MSSparkUtils)는 일반적인 작업을 쉽게 수행할 수 있도록 하는 기본 제공 패키지입니다. MSSparkUtils를 사용 하 여 파일 시스템 작업을 수행 하 고, 환경 변수를 가져오고, 암호를 사용할 수 있습니다. MSSparkUtils는 `PySpark (Python)` , `Scala` 및 `.NET Spark (C#)` 노트북 및 Synapse 파이프라인에서 사용할 수 있습니다.

## <a name="pre-requisites"></a>필수 구성 요소

### <a name="configure-access-to-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2에 대 한 액세스 구성 

Synapse 노트북은 azure AD (Azure active directory) 통과를 사용 하 여 ADLS Gen2 계정에 액세스 합니다. ADLS Gen2 계정 (또는 폴더)에 액세스 하려면 **Blob Storage 참가자** 여야 합니다. 

Synapse 파이프라인은 MSI (작업 영역 id)를 사용 하 여 저장소 계정에 액세스 합니다. 파이프라인 활동에서 MSSparkUtils를 사용 하려면 ADLS Gen2 계정 (또는 폴더)에 액세스 하려면 작업 영역 id를 **참가자 Blob Storage** 해야 합니다.

다음 단계를 수행 하 여 Azure AD 및 작업 영역 MSI가 ADLS Gen2 계정에 액세스할 수 있도록 합니다.
1. [Azure Portal](https://portal.azure.com/) 및 액세스 하려는 저장소 계정을 엽니다. 액세스 하려는 특정 컨테이너를 탐색할 수 있습니다.
2. 왼쪽 패널에서 **액세스 제어 (IAM)** 를 선택 합니다.
3. 아직 할당 되지 않은 경우 **AZURE AD 계정** 및 **작업 영역 id** (작업 영역 이름과 동일)를 저장소 계정의 **저장소 Blob 데이터 참가자** 역할에 할당 합니다. 
4. **저장** 을 선택합니다.

다음 URL을 통해 Synapse Spark를 사용 하 여 ADLS Gen2의 데이터에 액세스할 수 있습니다.

<code>abfss://<container_name>@<storage_account_name>.dfs.core.windows.net/<path></code>

### <a name="configure-access-to-azure-blob-storage"></a>Azure Blob Storage에 대 한 액세스 구성 

Synapse **SAS (공유 액세스 서명)** 를 활용 하 여 Azure Blob Storage에 액세스 합니다. 코드에서 SAS 키를 노출 하지 않도록 하려면 Synapse 작업 영역에서 액세스 하려는 Azure Blob Storage 계정에 대 한 새 연결 된 서비스를 만드는 것이 좋습니다.

Azure Blob Storage 계정에 대 한 새 연결 된 서비스를 추가 하려면 다음 단계를 수행 합니다.

1. [Azure Synapse Studio](https://web.azuresynapse.net/)를 엽니다.
2. 왼쪽 패널에서 **관리** 를 선택 하 고 **외부 연결** 아래에서 **연결 된 서비스** 를 선택 합니다.
3. 오른쪽의 **새 연결 된 서비스** 패널에서 **Azure Blob Storage** 를 검색 합니다.
4. **계속** 을 선택합니다.
5. 연결 된 서비스 이름에 액세스 하 고 구성 하려면 Azure Blob Storage 계정을 선택 합니다. **인증 방법** 에 **계정 키** 를 사용 하는 것이 좋습니다.
6. **연결 테스트** 를 선택 하 여 설정이 올바른지 확인 합니다.
7. 먼저 **만들기** 를 선택 하 고 **모두 게시** 를 클릭 하 여 변경 내용을 저장 합니다. 

다음 URL을 통해 Synapse Spark를 사용 하 여 Azure Blob Storage의 데이터에 액세스할 수 있습니다.

<code>wasb[s]://<container_name>@<storage_account_name>.blob.core.windows.net/<path></code>

다음은 코드 예제입니다.


:::zone pivot = "programming-language-python"

```python
from pyspark.sql import SparkSession

# Azure storage access info
blob_account_name = 'Your account name' # replace with your blob name
blob_container_name = 'Your container name' # replace with your container name
blob_relative_path = 'Your path' # replace with your relative folder path
linkedServiceName = 'Your linked service name' # replace with your linked service name

blob_sas_token = mssparkutils.credentials.getConnectionStringOrCreds(linkedServiceName)

# Allow SPARK to access from Blob remotely

wasb_path = 'wasbs://%s@%s.blob.core.windows.net/%s' % (blob_container_name, blob_account_name, blob_relative_path)

spark.conf.set('fs.azure.sas.%s.%s.blob.core.windows.net' % (blob_container_name, blob_account_name), blob_sas_token)
print('Remote blob path: ' + wasb_path)
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala

```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

```

::: zone-end
 
###  <a name="configure-access-to-azure-key-vault"></a>Azure Key Vault에 대 한 액세스 구성

Azure Key Vault를 연결 된 서비스로 추가 하 여 Synapse에서 자격 증명을 관리할 수 있습니다. 다음 단계를 수행 하 여 Azure Key Vault를 Synapse 연결 된 서비스로 추가 합니다.
1. [Azure Synapse Studio](https://web.azuresynapse.net/)를 엽니다.
2. 왼쪽 패널에서 **관리** 를 선택 하 고 **외부 연결** 아래에서 **연결 된 서비스** 를 선택 합니다.
3. 오른쪽의 **새 연결 된 서비스** 패널에서 **Azure Key Vault** 를 검색 합니다.
4. 연결 된 서비스 이름에 액세스 하 고 구성 하려면 Azure Key Vault 계정을 선택 합니다.
5. **연결 테스트** 를 선택 하 여 설정이 올바른지 확인 합니다.
6. 먼저 **만들기** 를 선택 하 고 **모두 게시** 를 클릭 하 여 변경 내용을 저장 합니다. 

Synapse 노트북은 azure AD (Azure active directory) 통과를 사용 하 여 Azure Key Vault에 액세스 합니다. Synapse 파이프라인은 MSI (작업 영역 id)를 사용 하 여 Azure Key Vault에 액세스 합니다. 노트북 및 Synapse 파이프라인에서 모두 코드가 작동 하는지 확인 하려면 Azure AD 계정 및 작업 영역 id에 대해 비밀 액세스 권한을 부여 하는 것이 좋습니다.

작업 영역 id에 대 한 비밀 액세스 권한을 부여 하려면 다음 단계를 수행 합니다.
1. [Azure Portal](https://portal.azure.com/) 및 액세스 하려는 Azure Key Vault를 엽니다. 
2. 왼쪽 패널에서 **액세스 정책을** 선택 합니다.
3. **액세스 정책 추가** 를 선택 합니다. 
    - **키, 비밀, & 인증서 관리** 를 구성 템플릿으로 선택 합니다.
    - 보안 주체 선택에서 **AZURE AD 계정** 및 **작업 영역 id** (작업 영역 이름과 동일)를 선택 하거나 이미 할당 되어 있는지 확인 합니다. 
4. **선택** 및 **추가** 를 선택 합니다.
5. **저장** 단추를 선택 하 여 변경 내용을 커밋합니다.  

## <a name="file-system-utilities"></a>파일 시스템 유틸리티

`mssparkutils.fs` Azure Data Lake Storage Gen2 (ADLS Gen2) 및 Azure Blob Storage를 포함 하 여 다양 한 파일 시스템을 사용 하기 위한 유틸리티를 제공 합니다. [Azure Data Lake Storage Gen2](#configure-access-to-azure-data-lake-storage-gen2) 및 [Azure Blob Storage](#configure-access-to-azure-blob-storage) 에 대 한 액세스를 적절 하 게 구성 해야 합니다.

사용 가능한 메서드에 대 한 개요를 보려면 다음 명령을 실행 합니다.

:::zone pivot = "programming-language-python"

```python
from notebookutils import mssparkutils
mssparkutils.fs.help()
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.fs.help()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
using Microsoft.Spark.Extensions.Azure.Synapse.Analytics.Notebook.MSSparkUtils;
FS.Help()
```

::: zone-end

결과:
```
mssparkutils.fs provides utilities for working with various FileSystems.

Below is overview about the available methods:

cp(from: String, to: String, recurse: Boolean = false): Boolean -> Copies a file or directory, possibly across FileSystems
mv(from: String, to: String, recurse: Boolean = false): Boolean -> Moves a file or directory, possibly across FileSystems
ls(dir: String): Array -> Lists the contents of a directory
mkdirs(dir: String): Boolean -> Creates the given directory if it does not exist, also creating any necessary parent directories
put(file: String, contents: String, overwrite: Boolean = false): Boolean -> Writes the given String out to a file, encoded in UTF-8
head(file: String, maxBytes: int = 1024 * 100): String -> Returns up to the first 'maxBytes' bytes of the given file as a String encoded in UTF-8
append(file: String, content: String, createFileIfNotExists: Boolean): Boolean -> Append the content to a file
rm(dir: String, recurse: Boolean = false): Boolean -> Removes a file or directory

Use mssparkutils.fs.help("methodName") for more info about a method.

```

### <a name="list-files"></a>파일 나열
디렉터리의 콘텐츠를 나열 합니다.


:::zone pivot = "programming-language-python"

```python
mssparkutils.fs.ls('Your directory path')
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.fs.ls("Your directory path")
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
FS.Ls("Your directory path")
```

::: zone-end


### <a name="view-file-properties"></a>파일 속성 보기
파일 이름, 파일 경로, 파일 크기 및 디렉터리와 파일 인지 여부를 포함 하는 파일 속성을 반환 합니다.

:::zone pivot = "programming-language-python"

```python
files = mssparkutils.fs.ls('Your directory path')
for file in files:
    print(file.name, file.isDir, file.isFile, file.path, file.size)
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
val files = mssparkutils.fs.ls("/")
files.foreach{
    file => println(file.name,file.isDir,file.isFile,file.size)
}
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
var Files = FS.Ls("/");
foreach(var File in Files) {
    Console.WriteLine(File.Name+" "+File.IsDir+" "+File.IsFile+" "+File.Size);
}
```

::: zone-end

### <a name="create-new-directory"></a>새 디렉터리 만들기

지정 된 디렉터리 (존재 하지 않는 경우) 및 필요한 부모 디렉터리를 만듭니다.

:::zone pivot = "programming-language-python"

```python
mssparkutils.fs.mkdirs('new directory name')
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.fs.mkdirs("new directory name")
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
FS.Mkdirs("new directory name")
```

::: zone-end

### <a name="copy-file"></a>파일 복사

파일이 나 디렉터리를 복사 합니다. 파일 시스템 간 복사를 지원 합니다.

:::zone pivot = "programming-language-python"

```python
mssparkutils.fs.cp('source file or directory', 'destination file or directory', True)# Set the third parameter as True to copy all files and directories recursively
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.fs.cp("source file or directory", "destination file or directory", true) // Set the third parameter as True to copy all files and directories recursively
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
FS.Cp("source file or directory", "destination file or directory", true) // Set the third parameter as True to copy all files and directories recursively
```

::: zone-end

### <a name="preview-file-content"></a>파일 콘텐츠 미리 보기

지정 된 파일의 첫 번째 ' maxBytes ' 바이트를 u t f-8로 인코딩된 문자열로 반환 합니다.

:::zone pivot = "programming-language-python"

```python
mssparkutils.fs.head('file path', maxBytes to read)
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.fs.head("file path", maxBytes to read)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
FS.Head("file path", maxBytes to read)
```

::: zone-end

### <a name="move-file"></a>파일 이동

파일이 나 디렉터리를 이동 합니다. 에서는 파일 시스템 간 이동을 지원 합니다.

:::zone pivot = "programming-language-python"

```python
mssparkutils.fs.mv('source file or directory', 'destination directory', True) # Set the last parameter as True to firstly create the parent directory if it does not exist
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.fs.mv("source file or directory", "destination directory", true) // Set the last parameter as True to firstly create the parent directory if it does not exist
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
FS.Mv("source file or directory", "destination directory", true)
```

::: zone-end

### <a name="write-file"></a>파일 쓰기

지정 된 문자열을 u t f-8로 인코딩된 파일에 씁니다.

:::zone pivot = "programming-language-python"

```python
mssparkutils.fs.put("file path", "content to write", True) # Set the last parameter as True to overwrite the file if it existed already
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.fs.put("file path", "content to write", true) // Set the last parameter as True to overwrite the file if it existed already
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
FS.Put("file path", "content to write", true) // Set the last parameter as True to overwrite the file if it existed already
```

::: zone-end

### <a name="append-content-to-a-file"></a>파일에 콘텐츠 추가

지정 된 문자열을 u t f-8로 인코딩된 파일에 추가 합니다.

:::zone pivot = "programming-language-python"

```python
mssparkutils.fs.append('file path','content to append',True) # Set the last parameter as True to create the file if it does not exist
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.fs.append("file path","content to append",true) // Set the last parameter as True to create the file if it does not exist
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
FS.Append("file path","content to append",true) // Set the last parameter as True to create the file if it does not exist
```

::: zone-end

### <a name="delete-file-or-directory"></a>파일 또는 디렉터리 삭제

파일이 나 디렉터리를 제거 합니다.

:::zone pivot = "programming-language-python"

```python
mssparkutils.fs.rm('file path', True) # Set the last parameter as True to remove all files and directories recursively 
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.fs.rm("file path", true) // Set the last parameter as True to remove all files and directories recursively 
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
FS.Rm("file path", true) // Set the last parameter as True to remove all files and directories recursively 
```

::: zone-end


## <a name="credentials-utilities"></a>자격 증명 유틸리티

MSSparkUtils 자격 증명 유틸리티를 사용 하 여 Azure Key Vault에서 연결 된 서비스의 액세스 토큰을 가져오고 암호를 관리할 수 있습니다. 

다음 명령을 실행 하 여 사용 가능한 방법에 대 한 개요를 가져옵니다.

:::zone pivot = "programming-language-python"

```python
mssparkutils.credentials.help()
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.credentials.help()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

```

::: zone-end

결과 가져오기:

```
getToken(audience, name): returns AAD token for a given audience, name (optional)
isValidToken(token): returns true if token hasn't expired
getConnectionStringOrCreds(linkedService): returns connection string or credentials for linked service
getSecret(akvName, secret, linkedService): returns AKV secret for a given AKV linked service, akvName, secret key
getSecret(akvName, secret): returns AKV secret for a given akvName, secret key
putSecret(akvName, secretName, secretValue, linkedService): puts AKV secret for a given akvName, secretName
putSecret(akvName, secretName, secretValue): puts AKV secret for a given akvName, secretName
```

### <a name="get-token"></a>토큰 가져오기

지정 된 대상 그룹에 대 한 Azure AD 토큰을 반환 합니다 (선택 사항). 다음 표에서는 사용 가능한 모든 대상 그룹 유형을 나열 합니다. 

|대상 유형|대상 키|
|--|--|
|대상 그룹 확인 유형|그룹|
|저장소 대상 리소스|저장할|
|데이터 웨어하우스 대상 그룹 리소스|DW|
|Data Lake 대상 리소스|' AzureManagement '|
|자격 증명 모음 대상 리소스|DataLakeStore|
|Azure OSSDB 대상 리소스|' AzureOSSDB '|
|Azure Synapse 리소스|'Synapse'|
|Azure Data Factory 리소스|ADF|

:::zone pivot = "programming-language-python"

```python
mssparkutils.credentials.getToken('audience Key')
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.credentials.getToken("audience Key")
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

```

::: zone-end


### <a name="validate-token"></a>토큰 유효성 검사

토큰이 만료 되지 않은 경우 true를 반환 합니다.

:::zone pivot = "programming-language-python"

```python
mssparkutils.credentials.isValidToken('your token')
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.credentials.isValidToken("your token")
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

```

::: zone-end


### <a name="get-connection-string-or-credentials-for-linked-service"></a>연결 된 서비스에 대 한 연결 문자열 또는 자격 증명 가져오기

연결 된 서비스에 대 한 연결 문자열 또는 자격 증명을 반환 합니다. 

:::zone pivot = "programming-language-python"

```python
mssparkutils.credentials.getConnectionStringOrCreds('linked service name')
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.credentials.getConnectionStringOrCreds("linked service name")
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

```

::: zone-end


### <a name="get-secret-using-workspace-identity"></a>작업 영역 id를 사용 하 여 비밀 가져오기

작업 영역 id를 사용 하 여 지정 된 Azure Key Vault 이름, 비밀 이름 및 연결 된 서비스 이름에 대 한 Azure Key Vault 암호를 반환 합니다. [Azure Key Vault](#configure-access-to-azure-key-vault) 에 대 한 액세스를 적절 하 게 구성 해야 합니다.

:::zone pivot = "programming-language-python"

```python
mssparkutils.credentials.getSecret('azure key vault name','secret name','linked service name')
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.credentials.getSecret("azure key vault name","secret name","linked service name")
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

```

::: zone-end


### <a name="get-secret-using-user-credentials"></a>사용자 자격 증명을 사용 하 여 비밀 가져오기

사용자 자격 증명을 사용 하 여 지정 된 Azure Key Vault 이름, 비밀 이름 및 연결 된 서비스 이름에 대 한 Azure Key Vault 암호를 반환 합니다. 

:::zone pivot = "programming-language-python"

```python
mssparkutils.credentials.getSecret('azure key vault name','secret name')
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.credentials.getSecret("azure key vault name","secret name")
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

```

::: zone-end

### <a name="put-secret-using-workspace-identity"></a>작업 영역 id를 사용 하 여 비밀 배치

작업 영역 id를 사용 하 여 지정 된 Azure Key Vault 이름, 비밀 이름 및 연결 된 서비스 이름에 대 한 Azure Key Vault 암호를 넣습니다. [Azure Key Vault](#configure-access-to-azure-key-vault) 에 대 한 액세스를 적절 하 게 구성 해야 합니다.

:::zone pivot = "programming-language-python"

```python
mssparkutils.credentials.putSecret('azure key vault name','secret name','secret value','linked service name')
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.credentials.putSecret("azure key vault name","secret name","secret value","linked service name")
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

```

::: zone-end


### <a name="put-secret-using-user-credentials"></a>사용자 자격 증명을 사용 하 여 암호 입력

사용자 자격 증명을 사용 하 여 지정 된 Azure Key Vault 이름, 비밀 이름 및 연결 된 서비스 이름에 대 한 Azure Key Vault 비밀을 배치 합니다. 

:::zone pivot = "programming-language-python"

```python
mssparkutils.credentials.putSecret('azure key vault name','secret name','secret value')
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.credentials.putSecret("azure key vault name","secret name","secret value")
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

```

::: zone-end


## <a name="environment-utilities"></a>환경 유틸리티 

다음 명령을 실행 하 여 사용 가능한 방법에 대 한 개요를 가져옵니다.

:::zone pivot = "programming-language-python"

```python
mssparkutils.env.help()
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

```

::: zone-end

결과 가져오기:
```
getUserName(): returns user name
getUserId(): returns unique user id
getJobId(): returns job id
getWorkspaceName(): returns workspace name
getPoolName(): returns Spark pool name
getClusterId(): returns cluster id
```

### <a name="get-user-name"></a>사용자 이름 가져오기

현재 사용자 이름을 반환 합니다.

:::zone pivot = "programming-language-python"

```python
mssparkutils.env.getUserName()
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.env.getUserName()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

```

::: zone-end

### <a name="get-user-id"></a>사용자 ID 가져오기

현재 사용자 ID를 반환 합니다.

:::zone pivot = "programming-language-python"

```python
mssparkutils.env.getUserId()
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.env.getUserId()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

```

::: zone-end

### <a name="get-job-id"></a>작업 ID 가져오기

작업 ID를 반환 합니다.

:::zone pivot = "programming-language-python"

```python
mssparkutils.env.getJobId()
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.env.getJobId()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

```

::: zone-end

### <a name="get-workspace-name"></a>작업 영역 이름 가져오기

작업 영역 이름을 반환 합니다.

:::zone pivot = "programming-language-python"

```python
mssparkutils.env.getWorkspaceName()
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.env.getWorkspaceName()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

```

::: zone-end

### <a name="get-pool-name"></a>풀 이름 가져오기

Spark 풀 이름을 반환 합니다.

:::zone pivot = "programming-language-python"

```python
mssparkutils.env.getPoolName()
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.env.getPoolName()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

```

::: zone-end

### <a name="get-cluster-id"></a>클러스터 ID 가져오기

현재 클러스터 ID를 반환 합니다.

:::zone pivot = "programming-language-python"

```python
mssparkutils.env.getClusterId()
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.env.getClusterId()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

```

::: zone-end

## <a name="next-steps"></a>다음 단계

- [Synapse 샘플 노트북 확인](https://github.com/Azure-Samples/Synapse/tree/master/Notebooks)
- [빠른 시작: 웹 도구를 사용하여 Azure Synapse Analytics에서 Apache Spark 풀(미리 보기) 만들기](../quickstart-apache-spark-notebook.md)
- [Azure Synapse Analytics의 Apache Spark란?](apache-spark-overview.md)
- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)