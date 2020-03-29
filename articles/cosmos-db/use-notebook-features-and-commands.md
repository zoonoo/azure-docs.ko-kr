---
title: Azure Cosmos DB에서 기본 제공 노트북 명령 및 기능 사용(미리 보기)
description: Azure Cosmos DB의 기본 제공 노트북을 사용하여 기본 제공 명령 및 기능을 사용하여 일반적인 작업을 수행하는 방법을 알아봅니다.
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/07/2019
ms.author: dech
ms.openlocfilehash: 61d46bbf0ccdeb5cd2e95e36e19f1aa81cfeeb48
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76513402"
---
# <a name="use-built-in-notebook-commands-and-features-in-azure-cosmos-db-preview"></a>Azure Cosmos DB에서 기본 제공 노트북 명령 및 기능 사용(미리 보기)

Azure Cosmos DB의 기본 제공 Jupyter 노트북을 사용하면 Azure 포털에서 데이터를 분석하고 시각화할 수 있습니다. 이 문서에서는 기본 제공 Notebook 명령 및 기능을 사용하여 일반적인 작업을 수행하는 방법을 설명합니다.

## <a name="install-a-new-package"></a>새 패키지 설치
Azure Cosmos 계정에 대한 전자 필기장 지원을 사용하도록 설정하면 새 전자 필기장을 열고 패키지를 설치할 수 있습니다.

새 코드 셀에서 다음 코드를 삽입하고 실행하여 원하는 Python 패키지로 대체합니다. ``PackageToBeInstalled``
```python
import sys
!{sys.executable} -m pip install PackageToBeInstalled –user
```
이 패키지는 Azure Cosmos 계정 작업 영역의 모든 전자 필기장에서 사용할 수 있습니다. 

> [!TIP]
> 전자 필기장에 사용자 지정 패키지가 필요한 경우 [작업 영역을 재설정하면](#reset-notebooks-workspace)패키지가 제거될 때 전자 필기장에 셀을 추가하여 패키지를 설치하는 것이 좋습니다.  

## <a name="run-a-sql-query"></a>SQL 쿼리 실행

``%%sql`` 매직 명령을 사용하여 계정의 모든 컨테이너에 대해 [SQL 쿼리를](sql-query-getting-started.md) 실행할 수 있습니다. 구문을 사용하십시오.

```bash
%%sql --database {database_id} --container {container_id}
{Query text}
```

- Cosmos 계정의 데이터베이스 및 컨테이너 이름을 교체합니다. ``{database_id}`` ``{container_id}`` 및 인수가 제공되지 않으면 [쿼리는 기본 데이터베이스 및 컨테이너에서](#set-default-database-for-queries)실행됩니다. ``--container`` ``--database``
- Azure Cosmos DB에서 유효한 모든 SQL 쿼리를 실행할 수 있습니다. 쿼리 텍스트는 새 줄에 있어야 합니다.

예를 들어: 
```bash
%%sql --database RetailDemo --container WebsiteData
SELECT c.Action, c.Price as ItemRevenue, c.Country, c.Item FROM c
```
셀에서 실행하여 ```%%sql?``` 전자 필기장의 sql magic 명령에 대한 도움말 설명서를 확인합니다.

## <a name="run-a-sql-query-and-output-to-a-pandas-dataframe"></a>SQL 쿼리를 실행하고 팬더 데이터 프레임에 출력

쿼리 결과를 팬더 DataFrame로 출력할 수 [있습니다.](https://pandas.pydata.org/pandas-docs/stable/getting_started/dsintro.html#dataframe) ``%%sql`` 구문을 사용하십시오. 

```bash
%%sql --database {database_id} --container {container_id} --output {outputDataFrameVar}
{Query text}
```
- Cosmos 계정의 데이터베이스 및 컨테이너 이름을 교체합니다. ``{database_id}`` ``{container_id}`` 및 인수가 제공되지 않으면 [쿼리는 기본 데이터베이스 및 컨테이너에서](#set-default-database-for-queries)실행됩니다. ``--container`` ``--database``
- 결과를 ``{outputDataFrameVar}`` 포함하는 DataFrame 변수의 이름으로 바꿉니다.
- Azure Cosmos DB에서 유효한 모든 SQL 쿼리를 실행할 수 있습니다. 쿼리 텍스트는 새 줄에 있어야 합니다. 

예를 들어:

```bash
%%sql --database RetailDemo --container WebsiteData --output df_cosmos
SELECT c.Action, c.Price as ItemRevenue, c.Country, c.Item FROM c
```
```bash
df_cosmos.head(10)

    Action  ItemRevenue Country Item
0   Viewed  9.00    Tunisia Black Tee
1   Viewed  19.99   Antigua and Barbuda Flannel Shirt
2   Added   3.75    Guinea-Bissau   Socks
3   Viewed  3.75    Guinea-Bissau   Socks
4   Viewed  55.00   Czech Republic  Rainjacket
5   Viewed  350.00  Iceland Cosmos T-shirt
6   Added   19.99   Syrian Arab Republic    Button-Up Shirt
7   Viewed  19.99   Syrian Arab Republic    Button-Up Shirt
8   Viewed  33.00   Tuvalu  Red Top
9   Viewed  14.00   Cape Verde  Flip Flop Shoes
```
## <a name="upload-json-items-to-a-container"></a>컨테이너에 JSON 항목 업로드
``%%upload`` 매직 명령을 사용하여 JSON 파일에서 지정된 Azure Cosmos 컨테이너로 데이터를 업로드할 수 있습니다. 다음 명령을 사용하여 항목을 업로드합니다.

```bash
%%upload --databaseName {database_id} --containerName {container_id} --url {url_location_of_file}
```

- Azure Cosmos 계정의 데이터베이스 및 컨테이너 이름을 대체합니다. ``{database_id}`` ``{container_id}`` 및 인수가 제공되지 않으면 [쿼리는 기본 데이터베이스 및 컨테이너에서](#set-default-database-for-queries)실행됩니다. ``--container`` ``--database``
- JSON 파일의 위치로 바꿉습니다. ``{url_location_of_file}`` 파일은 유효한 JSON 개체의 배열이어야 하며 공용 인터넷을 통해 액세스할 수 있어야 합니다.

예를 들어:

```bash
%%upload --database databaseName --container containerName --url 
https://contoso.com/path/to/data.json
```
```bash
Documents successfully uploaded to ContainerName
Total number of documents imported : 2654
Total time taken : 00:00:38.1228087 hours
Total RUs consumed : 25022.58
```
출력 통계를 사용하여 항목을 업로드하는 데 사용되는 효과적인 RU/s를 계산할 수 있습니다. 예를 들어 38초 동안 25,000RU가 소비된 경우 유효 RU/s는 25,000RU/ 38초 = 658RU/s입니다.

## <a name="set-default-database-for-queries"></a>쿼리에 대한 기본 데이터베이스 설정
전자 필기장에 ```%%sql``` 사용할 기본 데이터베이스 명령을 설정할 수 있습니다. ```{database_id}```를 해당 데이터베이스의 이름으로 바꿉니다.

```bash
%database {database_id}
```
셀에서 실행하여 ```%database?``` 전자 필기장의 설명서를 확인합니다.

## <a name="set-default-container-for-queries"></a>쿼리에 대한 기본 컨테이너 설정
전자 필기장에 ```%%sql``` 사용할 기본 컨테이너 명령을 설정할 수 있습니다. 컨테이너 ```{container_id}``` 의 이름으로 바꿉니다.

```bash
%container {container_id}
```
셀에서 실행하여 ```%container?``` 전자 필기장의 설명서를 확인합니다.

## <a name="use-built-in-nteract-data-explorer"></a>내장 된 nteract 데이터 탐색기 사용
기본 제공 [nteract 데이터 탐색기를](https://blog.nteract.io/designing-the-nteract-data-explorer-f4476d53f897) 사용하여 DataFrame을 필터링하고 시각화할 수 있습니다. 이 기능을 사용하려면 옵션을 ``pd.options.display.html.table_schema`` ``True`` 원하는 ``pd.options.display.max_rows`` 값으로 설정하고(모든 ``pd.options.display.max_rows`` 결과를 ``None`` 표시하도록 설정할 수 있음).

```python
import pandas as pd
pd.options.display.html.table_schema = True
pd.options.display.max_rows = None

df_cosmos.groupby("Item").size()
```
![nteract 데이터 탐색기](media/use-notebook-features-and-commands/nteract-built-in-chart.png)

## <a name="use-the-built-in-python-sdk"></a>내장 파이썬 SDK 사용
[SQL API용 Azure 코스모스 DB 파이썬 SDK의](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos) 버전 4가 설치되고 Azure 코스모스 계정의 노트북 환경에 포함됩니다.

기본 제공 ``cosmos_client`` 인스턴스를 사용하여 모든 SDK 작업을 실행합니다. 

예를 들어:

```python
## Import modules as needed
from azure.cosmos.partition_key import PartitionKey

## Create a new database if it doesn't exist
database = cosmos_client.create_database_if_not_exists('RetailDemo')

## Create a new container if it doesn't exist
container = database.create_container_if_not_exists(id='WebsiteData', partition_key=PartitionKey(path='/CartID'))
```
[파이썬 SDK 샘플을](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos/samples)참조하십시오. 

> [!IMPORTANT]
> 기본 제공 파이썬 SDK는 SQL (코어) API 계정에 대해서만 지원됩니다. 다른 API의 경우 API에 해당하는 [관련 파이썬 드라이버를 설치해야](#install-a-new-package) 합니다. 

## <a name="create-a-custom-instance-of-cosmos_client"></a>사용자 지정 인스턴스 만들기``cosmos_client``
유연성을 높이기 위해 다음을 수행하도록 ``cosmos_client`` 사용자 지정 인스턴스를 만들 수 있습니다.

- [연결 정책](https://docs.microsoft.com/python/api/azure-cosmos/azure.cosmos.documents.connectionpolicy?view=azure-python-preview) 사용자 지정
- 현재 있는 Azure Cosmos 계정과 다른 Azure Cosmos 계정에 대해 작업을 실행합니다.

[환경 변수를](#access-the-account-endpoint-and-primary-key-env-variables)통해 현재 계정의 연결 문자열 및 기본 키에 액세스할 수 있습니다. 

```python
import os
import azure.cosmos.cosmos_client as cosmos
import azure.cosmos.documents as documents

# These should be set to a region you've added for Azure Cosmos DB
region_1 = "Central US" 
region_2 = "East US 2"

custom_connection_policy = documents.ConnectionPolicy()
custom_connection_policy.PreferredLocations = [region_1, region_2] # Set the order of regions the SDK will route requests to. The regions should be regions you've added for Cosmos, otherwise this will error.

# Create a new instance of CosmosClient, getting the endpoint and key from the environment variables
custom_client = cosmos.CosmosClient(url=os.environ["COSMOS_ENDPOINT"], credential=os.environ["COSMOS_KEY"], connection_policy=custom_connection_policy)
```
## <a name="access-the-account-endpoint-and-primary-key-env-variables"></a>계정 끝점 및 기본 키 env 변수에 액세스
```python
import os

endpoint = os.environ["COSMOS_ENDPOINT"]
primary_key = os.environ["COSMOS_KEY"]
```
> [!IMPORTANT]
> ``COSMOS_ENDPOINT`` 및 ``COSMOS_KEY`` 환경 변수는 SQL API에만 적용할 수 있습니다. 다른 API의 경우 Azure Cosmos 계정의 **연결 문자열** 또는 **키** 블레이드에서 끝점과 키를 찾습니다.  

## <a name="reset-notebooks-workspace"></a>노트북 작업 공간 재설정
노트북 작업 영역을 기본 설정으로 재설정하려면 명령 모음에서 **작업 영역 재설정을** 선택합니다. 이렇게 하면 사용자 지정 설치 패키지가 제거되고 Jupyter 서버가 다시 시작됩니다. 전자 필기장, 파일 및 Azure Cosmos 리소스는 영향을 받지 않습니다.  

![노트북 작업 공간 재설정](media/use-notebook-features-and-commands/reset-workspace.png)

## <a name="next-steps"></a>다음 단계

- [Azure 코스모스 DB 주피터 노트북의](cosmosdb-jupyter-notebooks.md) 이점에 대해 알아보기
- [SQL API용 Azure 코스모스 DB 파이썬 SDK에](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos) 대해 알아보기
