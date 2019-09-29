---
title: Azure Cosmos DB의 기본 제공 노트북 명령 및 기능 사용
description: 기본 제공 명령 및 기능을 사용 하 여 Azure Cosmos DB의 기본 제공 노트북을 사용 하는 일반적인 작업을 수행 하는 방법을 알아봅니다.
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: dech
ms.openlocfilehash: 4a9bd554e0858024d656dbf35d6fb00995e6f4bd
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2019
ms.locfileid: "71672481"
---
# <a name="use-built-in-notebook-commands-and-features-in-azure-cosmos-db"></a>Azure Cosmos DB의 기본 제공 노트북 명령 및 기능 사용

Azure Cosmos DB의 기본 제공 Jupyter 노트북을 사용 하 여 Azure Portal에서 데이터를 분석 하 고 시각화할 수 있습니다. 이 문서에서는 기본 제공 노트북 명령 및 기능을 사용 하 여 일반적인 작업을 수행 하는 방법을 설명 합니다.

## <a name="install-a-new-package"></a>새 패키지 설치
Azure Cosmos 계정에 대해 노트북 지원을 사용 하도록 설정한 후 새 노트북을 열고 패키지를 설치할 수 있습니다.

새 코드 셀에서 다음 코드를 삽입 하 고 실행 하 여를 ``PackageToBeInstalled`` 원하는 Python 패키지로 바꿉니다.
```python
import sys
!{sys.executable} -m pip install PackageToBeInstalled –user
```
이 패키지는 Azure Cosmos 계정 작업 영역에 있는 모든 노트북에서 사용할 수 있습니다. 

> [!TIP]
> 노트북에 사용자 지정 패키지가 필요한 경우 [작업 영역을 다시 설정](#reset-notebooks-workspace)하는 경우 패키지가 제거 되므로 노트북에 셀을 추가 하 여 패키지를 설치 하는 것이 좋습니다.  

## <a name="run-a-sql-query"></a>SQL 쿼리 실행

``%%sql`` 매직 명령을 사용 하 여 계정의 모든 컨테이너에 대해 [SQL 쿼리](sql-query-getting-started.md) 를 실행할 수 있습니다. 다음 구문을 사용 합니다.

```bash
%%sql --database {database_id} --container {container_id}
{Query text}
```

- ``{database_id}`` 및``{container_id}`` 를 Cosmos 계정의 데이터베이스 및 컨테이너의 이름으로 바꿉니다. 및 인수를 제공 하지 않으면 [기본 데이터베이스 및 컨테이너](#set-default-database-for-queries)에서 쿼리가 실행 됩니다. ``--container`` ``--database``
- Azure Cosmos DB에서 유효한 SQL 쿼리를 실행할 수 있습니다. 쿼리 텍스트는 새 줄에 있어야 합니다.

예를 들어 다음과 같은 가치를 제공해야 합니다. 
```bash
%%sql --database RetailDemo --container WebsiteData
SELECT c.Action, c.Price as ItemRevenue, c.Country, c.Item FROM c
```
셀 ```%%sql?``` 에서를 실행 하 여 노트북의 sql 매직 명령에 대 한 도움말 설명서를 확인 합니다.

## <a name="run-a-sql-query-and-output-to-a-pandas-dataframe"></a>SQL 쿼리를 실행 하 고 Pandas 데이터 프레임에 출력 합니다.

``%%sql`` [Pandas 데이터 프레임](https://pandas.pydata.org/pandas-docs/stable/getting_started/dsintro.html#dataframe)에 쿼리 결과를 출력할 수 있습니다. 다음 구문을 사용 합니다. 

```bash
%%sql --database {database_id} --container {container_id} --output {outputDataFrameVar}
{Query text}
```
- ``{database_id}`` 및``{container_id}`` 를 Cosmos 계정의 데이터베이스 및 컨테이너의 이름으로 바꿉니다. 및 인수를 제공 하지 않으면 [기본 데이터베이스 및 컨테이너](#set-default-database-for-queries)에서 쿼리가 실행 됩니다. ``--container`` ``--database``
- 결과 ``{outputDataFrameVar}`` 를 포함 하는 데이터 프레임 변수 이름으로 대체 합니다.
- Azure Cosmos DB에서 유효한 SQL 쿼리를 실행할 수 있습니다. 쿼리 텍스트는 새 줄에 있어야 합니다. 

예를 들어 다음과 같은 가치를 제공해야 합니다.

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

## <a name="set-default-database-for-queries"></a>쿼리에 대 한 기본 데이터베이스 설정
노트북에 사용 되는 기본 ```%%sql``` 데이터베이스 명령을 설정할 수 있습니다. 를 ```{database_id}``` 데이터베이스의 이름으로 바꿉니다.

```bash
%database {database_id}
```
노트 ```%database?``` 에서 설명서를 보려면 셀에서를 실행 합니다.

## <a name="set-default-container-for-queries"></a>쿼리에 대 한 기본 컨테이너 설정
노트북에 사용 되는 기본 ```%%sql``` 컨테이너 명령을 설정할 수 있습니다. 를 ```{container_id}``` 컨테이너의 이름으로 바꿉니다.

```bash
%container {container_id}
```
노트 ```%container?``` 에서 설명서를 보려면 셀에서를 실행 합니다.

## <a name="use-the-built-in-python-sdk"></a>기본 제공 Python SDK 사용
[SQL API 용 Azure Cosmos DB PYTHON SDK](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos) 버전 4가 설치 되어 Cosmos 계정에 대 한 노트북 환경에 포함 되어 있습니다.

기본 제공 ``cosmos_client`` 인스턴스를 사용 하 여 모든 SDK 작업을 실행할 수 있습니다. 

예를 들어 다음과 같은 가치를 제공해야 합니다.

```python
## Import modules as needed
from azure.cosmos.partition_key import PartitionKey

## Create a new database if it doesn't exist
database = cosmos_client.create_database_if_not_exists('RetailDemo')

## Create a new container if it doesn't exist
container = database.create_container_if_not_exists(id='WebsiteData', partition_key=PartitionKey(path='/CartID'))
```
[PYTHON SDK 샘플](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos/samples)을 참조 하세요. 

> [!IMPORTANT]
> 기본 제공 Python SDK는 SQL (Core) API 계정에 대해서만 지원 됩니다. 다른 Api의 경우 API에 해당 하는 [관련 Python 드라이버를 설치](#install-a-new-package) 해야 합니다. 

## <a name="create-a-custom-instance-of-cosmos_client"></a>사용자 지정 인스턴스 만들기``cosmos_client``
더 많은 유연성을 위해 다음을 위해의 ``cosmos_client`` 사용자 지정 인스턴스를 만들 수 있습니다.

- [연결 정책](https://docs.microsoft.com/python/api/azure-cosmos/azure.cosmos.documents.connectionpolicy?view=azure-python-preview) 사용자 지정
- 사용자가 있는 계정이 아닌 다른 Cosmos 계정에 대해 작업 실행

[환경 변수](#access-the-account-endpoint-and-primary-key-env-variables)를 통해 현재 계정의 연결 문자열 및 기본 키에 액세스할 수 있습니다. 

```python
import os
import azure.cosmos.cosmos_client as cosmos
import azure.cosmos.documents as documents

# These should be set to a region you've added for Cosmos DB
region_1 = "Central US" 
region_2 = "East US 2"

custom_connection_policy = documents.ConnectionPolicy()
custom_connection_policy.PreferredLocations = [region_1, region_2] # Set the order of regions the SDK will route requests to. The regions should be regions you've added for Cosmos, otherwise this will error.

# Create a new instance of CosmosClient, getting the endpoint and key from the environment variables
custom_client = cosmos.CosmosClient(os.environ["COSMOS_ENDPOINT"], {'masterKey': os.environ["COSMOS_KEY"]}, connection_policy=custom_connection_policy)
```
## <a name="access-the-account-endpoint-and-primary-key-env-variables"></a>계정 끝점 및 기본 키 env 변수에 액세스
```python
import os

endpoint = os.environ["COSMOS_ENDPOINT"]
primary_key = os.environ["COSMOS_KEY"]
```
> [!IMPORTANT]
> ``COSMOS_ENDPOINT`` 및``COSMOS_KEY`` 환경 변수는 SQL API에만 적용 됩니다. 다른 Api의 경우 Cosmos 계정의 **연결 문자열** 또는 **키** 블레이드에서 끝점 및 키를 찾습니다.  

## <a name="reset-notebooks-workspace"></a>전자 필기장 작업 영역 다시 설정
전자 필기장 작업 영역을 기본 설정으로 다시 설정 하려면 명령 모음에서 **작업 영역 다시 설정** 을 선택 합니다. 그러면 설치 된 모든 사용자 지정 패키지가 제거 되 고 Jupyter 서버가 다시 시작 됩니다. 노트북, 파일 및 Cosmos 리소스는 영향을 받지 않습니다.  

![전자 필기장 작업 영역 다시 설정](media/use-notebook-features-and-commands/reset-workspace.png)

## <a name="next-steps"></a>다음 단계

- [Jupyter 노트북 Azure Cosmos DB](cosmosdb-jupyter-notebooks.md) 의 이점에 대해 알아봅니다.
- [SQL API 용 PYTHON SDK Azure Cosmos DB](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos) 에 대해 알아보기
