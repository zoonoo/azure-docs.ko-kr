---
title: 데이터브릭스 노트북으로 데이터 변환
description: Databricks Notebook을 실행하여 데이터를 처리하거나 변환하는 방법을 알아봅니다.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
author: nabhishek
ms.author: abnarain
manager: shwang
ms.reviewer: maghan
ms.topic: conceptual
ms.date: 03/15/2018
ms.openlocfilehash: c7a2aec35511ef066033c3d6462143ac31660e76
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74923053"
---
# <a name="transform-data-by-running-a-databricks-notebook"></a>Databricks Notebook을 실행하여 데이터 변환

[데이터 팩터리 파이프라인의](concepts-pipelines-activities.md) Azure Databricks 전자 필기장 작업은 Azure Databricks 작업 영역에서 Databricks 전자 필기장을 실행합니다. 이 문서에서는 데이터 변환 및 지원되는 변환 활동에 대한 일반적인 개요를 제공하는 [데이터 변환 활동](transform-data.md) 문서를 기반으로 합니다.Azure Databricks는 Apache Spark를 실행하기 위해 관리되는 플랫폼입니다.

## <a name="databricks-notebook-activity-definition"></a>Databricks Notebook 활동 정의

Databricks Notebook 활동에 대한 샘플 JSON 정의는 다음과 같습니다.

```json
{
    "activity": {
        "name": "MyActivity",
        "description": "MyActivity description",
        "type": "DatabricksNotebook",
        "linkedServiceName": {
            "referenceName": "MyDatabricksLinkedservice",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "notebookPath": "/Users/user@example.com/ScalaExampleNotebook",
            "baseParameters": {
                "inputpath": "input/folder1/",
                "outputpath": "output/"
            },
            "libraries": [
                {
                "jar": "dbfs:/docs/library.jar"
                }
            ]
        }
    }
}
```

## <a name="databricks-notebook-activity-properties"></a>Databricks Notebook 활동 속성

다음 표에서는 JSON 정의에 사용하는 JSON 속성을 설명합니다.

|속성|설명|필수|
|---|---|---|
|name|파이프라인의 작업 이름입니다.|yes|
|description|작업이 어떤 일을 수행하는지 설명하는 텍스트입니다.|예|
|type|Databricks Notebook 활동의 경우 활동 유형은 DatabricksNotebook입니다.|yes|
|linkedServiceName|Databricks Notebook이 실행되는 Databricks 연결된 서비스의 이름입니다. 이 연결된 서비스에 대한 자세한 내용은 [연결된 서비스](compute-linked-services.md) 계산 문서를 참조하십시오.|yes|
|notebookPath|Databricks 작업 영역에서 실행할 노트북의 절대 경로입니다. 이 경로는 슬래시로 시작해야 합니다.|yes|
|baseParameters|키-값 쌍의 배열입니다. 각 활동 실행에 기본 매개 변수를 사용할 수 있습니다. 노트북에서 지정되지 않은 매개 변수를 사용하는 경우, 노트북의 기본값이 사용됩니다. 매개 변수에 대한 자세한 정보는 [Databricks Notebook](https://docs.databricks.com/api/latest/jobs.html#jobsparampair)을 참조하세요.|예|
|라이브러리|작업을 실행할 클러스터에 설치할 라이브러리의 목록입니다. \<문자열, 개체>의 배열일 수 있습니다.|예|


## <a name="supported-libraries-for-databricks-activities"></a>Databricks 활동의 지원되는 라이브러리

위의 Databricks 활동 정의에서 이러한 라이브러리 유형을 지정합니다: *항아리,* *계란,* *whl,* *maven,* *pypi,* *cran*.

```json
{
    "libraries": [
        {
            "jar": "dbfs:/mnt/libraries/library.jar"
        },
        {
            "egg": "dbfs:/mnt/libraries/library.egg"
        },
    {
            "whl": "dbfs:/mnt/libraries/mlflow-0.0.1.dev0-py2-none-any.whl"
        },
        {
            "whl": "dbfs:/mnt/libraries/wheel-libraries.wheelhouse.zip"
        },
        {
            "maven": {
                "coordinates": "org.jsoup:jsoup:1.7.2",
                "exclusions": [ "slf4j:slf4j" ]
            }
        },
        {
            "pypi": {
                "package": "simplejson",
                "repo": "http://my-pypi-mirror.com"
            }
        },
        {
            "cran": {
                "package": "ada",
                "repo": "https://cran.us.r-project.org"
            }
        }
    ]
}

```

자세한 내용은 라이브러리 유형에 대한 [Databricks 설명서](https://docs.azuredatabricks.net/api/latest/libraries.html#managedlibrarieslibrary)를 참조하세요.

## <a name="passing-parameters-between-notebooks-and-data-factory"></a>노트북과 데이터 팩터리 간에 매개 변수 전달

databricks 활동에서 *baseParameters* 속성을 사용하여 데이터 팩터리 매개 변수를 노트북에 전달할 수 있습니다. 

경우에 따라 데이터 팩터리에서 제어 흐름(조건부 검사)에 사용하거나 다운스트림 활동(크기 제한은 2MB)에 사용할 수 있는 데이터 팩터리로 노트북에서 특정 값을 다시 전달해야 할 수 있습니다. 

1. 노트북에서 [dbutils.notebook.exit("returnValue")를](https://docs.azuredatabricks.net/user-guide/notebooks/notebook-workflows.html#notebook-workflows-exit) 호출할 수 있으며 해당 "returnValue"는 데이터 팩터리로 반환됩니다.

2. `'@activity('databricks notebook activity name').output.runOutput'`와 같은 식을 사용하여 데이터 팩터리에서 출력을 사용할 수 있습니다. 

   > [!IMPORTANT]
   > JSON 개체를 전달하는 경우 속성 이름을 더하여 값을 검색할 수 있습니다. 예: `'@activity('databricks notebook activity name').output.runOutput.PropertyName'`

## <a name="how-to-upload-a-library-in-databricks"></a>Databricks에서 라이브러리를 업로드하는 방법

#### <a name="using-databricks-workspace-ui"></a>[Azure Databricks 작업 영역 UI 사용](https://docs.azuredatabricks.net/user-guide/libraries.html#create-a-library)

UI를 사용하여 추가된 라이브러리의 dbfs 경로를 얻으려면 [Databricks CLI(설치)](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#install-the-cli)를 사용할 수 있습니다. 

일반적으로 Jar 라이브러리는 UI를 사용하는 동안 dbfs:/FileStore/jars 아래에 저장됩니다. *databricks fs ls dbfs:/FileStore/jars* CLI를 통해 모두 나열할 수 있습니다.



#### <a name="copy-library-using-databricks-cli"></a>[Databricks CLI를 사용하여 라이브러리 복사](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#copy-a-file-to-dbfs)

예제: *databricks fs cp SparkPi-assembly-0.1.jar dbfs:/FileStore/jars*
