---
title: Azure Synapse Analytics용 Apache Spark에서 연결된 서비스로 액세스 자격 증명 보호
description: 이 문서에서는 연결된 서비스 및 토큰 라이브러리를 사용하여 Synapse Analytics용 Apache Spark를 다른 서비스와 안전하게 통합하는 개념을 제공합니다.
services: synapse-analytics
author: mlee3gsd
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: spark
ms.date: 08/26/2020
ms.author: martinle
ms.reviewer: euang
ms.openlocfilehash: 1cdb010e34674d52ebe2135ad1591a163a078708
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93324955"
---
# <a name="securing-your-credentials-through-linked-services-with-the-tokenlibrary"></a>TokenLibrary와 연결된 서비스를 통해 자격 증명 보호
외부 소스에서 데이터에 액세스하는 것은 일반적인 패턴입니다. 외부 데이터 원본에서 익명 액세스를 허용하지 않는 한, 자격 증명, 비밀 또는 연결 문자열로 연결을 보호해야 할 수도 있습니다.  

Azure Synapse Analytics는 연결된 서비스 또는 Azure Key Vault에 연결 세부 정보를 저장하여 통합 프로세스를 간소화하는 연결된 서비스를 제공합니다. 연결된 서비스를 만든 후에는 Apache Spark에서 연결된 서비스를 참조하여 연결 정보를 코드에 적용할 수 있습니다. 

자세한 내용은 [연결된 서비스](../../data-factory/concepts-linked-services.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)를 참조하세요.
> [!NOTE]
> 작업 영역 내 Azure Data Lake Storage에서 파일에 액세스하는 경우 인증을 위해 AAD 통과를 사용하므로 TokenLibrary를 사용하지 않아도 됩니다. 


## <a name="prerequisite"></a>필수 조건
* 연결된 서비스 - 외부 데이터 원본에 연결된 서비스를 만들고 토큰 라이브러리에서 연결된 서비스를 참조해야 합니다. [연결된 서비스](../../data-factory/concepts-linked-services.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)에 대해 자세히 알아봅니다.


## <a name="connect-to-adls-gen2-outside-of-synapse-workspace"></a>Synapse 작업 영역 외부에서 ADLS Gen2에 연결

Synapse는 Azure Data Lake Storage Gen2에 대한 통합 연결된 서비스 환경을 제공합니다.

```scala
// Scala code
val sc = spark.sparkContext
sc.hadoopConfiguration.set("spark.storage.synapse.linkedServiceName", "<LINKED SERVICE NAME>")
sc.hadoopConfiguration.set("fs.azure.account.auth.type", "SAS")
sc.hadoopConfiguration.set("fs.azure.sas.token.provider.type", "com.microsoft.azure.synapse.tokenlibrary.LinkedServiceBasedSASProvider")

val df = spark.read.csv("abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<DIRECTORY PATH>")

df.show()
```

```python
# Python code
sc._jsc.hadoopConfiguration().set("spark.storage.synapse.linkedServiceName", "<lINKED SERVICE NAME>")
sc._jsc.hadoopConfiguration().set("fs.azure.account.auth.type", "SAS")
sc._jsc.hadoopConfiguration().set("fs.azure.sas.token.provider.type", "com.microsoft.azure.synapse.tokenlibrary.LinkedServiceBasedSASProvider")

df = spark.read.csv("abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<DIRECTORY PATH>")

df.show()
```
## <a name="use-the-token-library"></a>토큰 라이브러리 사용

다른 연결된 서비스에 연결하려면 TokenLibrary를 직접 호출하면 됩니다.

### <a name="getconnectionstring"></a>GetConnectionString
 연결 문자열을 검색하려면 <b>getConnectionString</b> 함수를 사용하고 <b>연결된 서비스 이름</b>을 전달합니다.

```scala
// Scala
// retrieve connectionstring from TokenLibrary

import com.microsoft.azure.synapse.tokenlibrary.TokenLibrary

val connectionString: String = TokenLibrary.getConnectionString("<LINKED SERVICE NAME>")
println(connectionString)
```

```python
# Python
# retrieve connectionstring from TokenLibrary

from pyspark.sql import SparkSession

sc = SparkSession.builder.getOrCreate()
token_library = sc._jvm.com.microsoft.azure.synapse.tokenlibrary.TokenLibrary
connection_string = token_library.getConnectionString("<LINKED SERVICE NAME>")
print(connection_string)
```
```csharp
// C#
// retrieve connectionstring from TokenLibrary

using Microsoft.Spark.Extensions.Azure.Synapse.Analytics.Utils;

string connectionString = TokenLibrary.GetConnectionString(<LINKED SERVICE NAME>);
Console.WriteLine(connectionString);
```

### <a name="getconnectionstringasmap"></a>GetConnectionStringAsMap
연결 문자열의 <i>key=value</i> 쌍에서 특정 값을 구문 분석하려면 다음과 같이 수행합니다. 

<i>DefaultEndpointsProtocol=https;AccountName=\<AccountName>;AccountKey=\<AccountKey></i>

<b>getConnectionStringAsMap</b> 함수를 사용하고 키를 전달하여 값을 반환합니다.
```scala
// Linked services can be used for storing and retreiving credentials (e.g, account key)
// Example connection string (for storage): "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
import com.microsoft.azure.synapse.tokenlibrary.TokenLibrary

val accountKey: String = TokenLibrary.getConnectionStringAsMap("<LINKED SERVICE NAME">).get("<KEY NAME>")
println(accountKey)
```

```python
# Linked services can be used for storing and retreiving credentials (e.g, account key)
# Example connection string (for storage): "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
from pyspark.sql import SparkSession

sc = SparkSession.builder.getOrCreate()
token_library = sc._jvm.com.microsoft.azure.synapse.tokenlibrary.TokenLibrary
accountKey = token_library.getConnectionStringAsMap("<LINKED SERVICE NAME>").get("<KEY NAME>")
print(accountKey)
```

## <a name="next-steps"></a>다음 단계

- [전용 SQL 풀에 쓰기](./synapse-spark-sql-pool-import-export.md)

