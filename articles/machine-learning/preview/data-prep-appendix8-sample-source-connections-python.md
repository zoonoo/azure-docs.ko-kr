---
title: "Azure Machine Learning 데이터 준비를 사용하여 가능한 추가 원본 데이터 연결 예제 | Microsoft Docs"
description: "이 문서에서는 Azure ML 데이터 준비를 사용하여 가능한 원본 데이터 연결의 예제 집합을 제공합니다."
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: 
ms.service: machine-learning
ms.workload: data-services
ms.custom: 
ms.devlang: 
ms.topic: article
ms.date: 09/11/2017
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 550cca100314009f63eec2136e8c65426d8bf07f
ms.contentlocale: ko-kr
ms.lasthandoff: 09/25/2017

---
# <a name="sample-of-custom-source-connections-python"></a>사용자 지정 원본 연결 샘플(Python) 
이 부록을 읽기 전에 [Python 확장성 개요](data-prep-python-extensibility-overview.md)를 참조하세요.

## <a name="loading-data-from-dataworld"></a>data.world에서 데이터 로드

### <a name="prerequisites"></a>필수 조건

#### <a name="register-yourself-at-dataworld"></a>data.world에서 등록
data.world 웹 사이트에서 API 토큰을 얻어야 합니다.

#### <a name="install-dataworld-library"></a>data.world 라이브러리 설치

_파일->명령줄 인터페이스 열기_에서 Azure Machine Learning Workbench 명령줄 인터페이스를 엽니다.

```console
pip install git+git://github.com/datadotworld/data.world-py.git
```

그런 다음 명령줄에서 `dw configure`를 실행하면 토큰을 묻는 메시지가 표시됩니다. 토큰을 입력하면 홈 디렉터리에 .dw/ 디렉터리가 만들어지고 여기에 토큰이 저장됩니다.

```
API token (obtained at: https://data.world/settings/advanced): <enter API token here>
```
이제 data.world 라이브러리를 가져올 수 있습니다.

#### <a name="load-data-into-data-preparation"></a>데이터 준비로 데이터 로드

새 스크립트 기반 데이터 흐름을 만들고 다음 스크립트를 사용하여 data.world에서 데이터를 로드합니다.

```python
#paths = df['Path'].tolist()

import datadotworld as dw

#load the dataset
lds = dw.load_dataset('data-society/the-simpsons-by-the-data')

#Load specific data frame from the dataset
df = lds.dataframes['simpsons_episodes']

```

## <a name="load-cosmosdb-data-into-data-preparation"></a>데이터 준비로 CosmosDB 데이터 로드

새 스크립트 기반 데이터 흐름을 만들고 다음 스크립트를 사용하여 CosmosDB에서 데이터를 로드합니다(위에 링크된 참조 문서를 참조하여 라이브러리를 먼저 설치해야 함).

```python
import pydocumentdb
import pydocumentdb.document_client as document_client

import pandas as pd

config = { 
    'ENDPOINT': '<Endpoint>',
    'MASTERKEY': '<Key>',
    'DOCUMENTDB_DATABASE': '<DBName>',
    'DOCUMENTDB_COLLECTION': '<collectionname>'
};

# Initialize the Python DocumentDB client
client = document_client.DocumentClient(config['ENDPOINT'], {'masterKey': config['MASTERKEY']})

# Read databases and take first since id should not be duplicated.
db = next((data for data in client.ReadDatabases() if data['id'] == config['DOCUMENTDB_DATABASE']))

# Read collections and take first since id should not be duplicated.
coll = next((coll for coll in client.ReadCollections(db['_self']) if coll['id'] == config['DOCUMENTDB_COLLECTION']))

docs = client.ReadDocuments(coll['_self'])

df = pd.DataFrame(list(docs))
```

