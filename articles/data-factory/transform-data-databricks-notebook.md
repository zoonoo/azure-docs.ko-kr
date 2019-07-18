---
title: Databricks Notebook으로 데이터 변환 - Azure | Microsoft Docs
description: Databricks Notebook을 실행하여 데이터를 처리하거나 변환하는 방법을 알아봅니다.
services: data-factory
documentationcenter: ''
ms.assetid: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/15/2018
author: sharonlo101
ms.author: shlo
manager: craigg
ms.openlocfilehash: 8036a8694bb8c8d0db236eba831f13dc2bf47d0a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60311668"
---
# <a name="transform-data-by-running-a-databricks-notebook"></a>Databricks Notebook을 실행하여 데이터 변환

[Data Factory 파이프라인](concepts-pipelines-activities.md)의 Azure Databricks Notebook 활동은 Azure Databricks 작업 영역에서 Databricks Notebook을 실행합니다. 이 문서는 데이터 변환 및 지원되는 변환 활동의 일반적인 개요를 표시하는  [데이터 변환 활동](transform-data.md)  문서에서 작성합니다. Azure Databricks는 Apache Spark를 실행하기 위해 관리되는 플랫폼입니다.

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

|자산|설명|필수|
|---|---|---|
|name|파이프라인의 작업 이름입니다.|예|
|description|작업이 어떤 일을 수행하는지 설명하는 텍스트입니다.|아닙니다.|
|형식|Databricks Notebook 활동의 경우 활동 유형은 DatabricksNotebook입니다.|예|
|linkedServiceName|Databricks Notebook이 실행되는 Databricks 연결된 서비스의 이름입니다. 이 연결된 서비스에 대한 자세한 내용은  [연결된 서비스 Compute](compute-linked-services.md)  문서를 참조하세요.|예|
|notebookPath|Databricks 작업 영역에서 실행할 노트북의 절대 경로입니다. 이 경로는 슬래시로 시작해야 합니다.|예|
|baseParameters|키-값 쌍의 배열입니다. 각 활동 실행에 기본 매개 변수를 사용할 수 있습니다. 노트북에서 지정되지 않은 매개 변수를 사용하는 경우, 노트북의 기본값이 사용됩니다. 매개 변수에 대한 자세한 정보는 [Databricks Notebook](https://docs.databricks.com/api/latest/jobs.html#jobsparampair)을 참조하세요.|아닙니다.|
|라이브러리|작업을 실행할 클러스터에 설치할 라이브러리의 목록입니다. \<문자열, 개체>의 배열일 수 있습니다.|아닙니다.|


## <a name="supported-libraries-for-databricks-activities"></a>Databricks 활동의 지원되는 라이브러리

위의 Databricks 활동 정의에서 *jar*, *egg*, *maven*, *pypi*, *cran* 라이브러리 유형을 지정합니다.

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

## <a name="how-to-upload-a-library-in-databricks"></a>Databricks에서 라이브러리를 업로드하는 방법

#### <a name="using-databricks-workspace-uihttpsdocsazuredatabricksnetuser-guidelibrarieshtmlcreate-a-library"></a>[Azure Databricks 작업 영역 UI 사용](https://docs.azuredatabricks.net/user-guide/libraries.html#create-a-library)

UI를 사용하여 추가된 라이브러리의 dbfs 경로를 얻으려면 [Databricks CLI(설치)](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#install-the-cli)를 사용할 수 있습니다. 

일반적으로 Jar 라이브러리는 UI를 사용하는 동안 dbfs:/FileStore/jars 아래에 저장됩니다. *databricks fs ls dbfs:/FileStore/jars* CLI를 통해 모두 나열할 수 있습니다.



#### <a name="copy-library-using-databricks-clihttpsdocsazuredatabricksnetuser-guidedev-toolsdatabricks-clihtmlcopy-a-file-to-dbfs"></a>[Databricks CLI를 사용하여 라이브러리 복사](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#copy-a-file-to-dbfs)

예제: *databricks fs cp SparkPi-assembly-0.1.jar dbfs:/FileStore/jars*
