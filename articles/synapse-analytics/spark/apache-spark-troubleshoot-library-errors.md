---
title: 라이브러리 설치 오류 문제 해결
description: 이 자습서에서는 라이브러리 설치 오류 문제를 해결 하는 방법에 대 한 개요를 제공 합니다.
services: synapse-analytics
author: midesa
ms.author: midesa
ms.service: synapse-analytics
ms.subservice: spark
ms.topic: conceptual
ms.date: 01/04/2021
ms.openlocfilehash: e812fa47d35889a9cf8c671a4df6034812272a6a
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101670625"
---
# <a name="troubleshoot-library-installation-errors"></a>라이브러리 설치 오류 문제 해결 
응용 프로그램에서 타사 또는 로컬로 빌드된 코드를 사용할 수 있도록 하려면 서버를 사용 하지 않는 Apache Spark 풀 중 하나에 라이브러리를 설치 하면 됩니다. requirements.txt 파일에 나열 된 패키지는 풀을 시작할 때 PyPi에서 다운로드 됩니다. 이 요구 사항 파일은 spark 인스턴스를 Spark 풀에서 만들 때마다 사용 됩니다. Spark 풀에 대해 라이브러리를 설치한 후에는 동일한 풀을 사용 하는 모든 세션에 대해 사용할 수 있습니다. 

경우에 따라 설치 하려는 라이브러리가 Apache Spark 풀에 표시 되지 않을 수 있습니다. 이 경우는 제공 된 requirements.txt 또는 지정 된 라이브러리에 오류가 있을 때 주로 발생 합니다. 라이브러리 설치 프로세스에 오류가 발생 하면 Apache Spark 풀이 Synapse base runtime에 지정 된 라이브러리로 돌아갑니다.

이 문서의 목표는 일반적인 문제를 제공 하 고 라이브러리 설치 오류를 디버그 하는 데 도움을 주는 것입니다.

## <a name="force-update-your-apache-spark-pool"></a>Apache Spark 풀 강제 업데이트
Apache Spark 풀에서 라이브러리를 업데이트 하는 경우 풀이 다시 시작 되 면 이러한 변경 내용이 선택 됩니다. 활성 작업이 있는 경우 이러한 작업은 원래 버전의 spark 풀에서 계속 실행 됩니다.

**새 설정을 강제로** 적용 하는 옵션을 선택 하 여 변경 내용을 강제로 적용할 수 있습니다. 이 설정은 선택한 Spark 풀의 모든 현재 세션을 종료 합니다. 세션이 종료 되 면 풀이 다시 시작 될 때까지 기다려야 합니다. 

![Python 라이브러리 추가](./media/apache-spark-azure-portal-add-libraries/update-libraries.png "Python 라이브러리 추가")

## <a name="validate-your-permissions"></a>권한 유효성 검사
라이브러리를 설치 하 고 업데이트 하려면 Azure Synapse Analytics 작업 영역에 연결 된 기본 Azure Data Lake Storage Gen2 저장소 계정에 대 한 **저장소 Blob 데이터 참가자** 또는 **저장소 blob 데이터 소유자** 권한이 있어야 합니다.

이러한 권한이 있는지 확인 하려면 다음 코드를 실행할 수 있습니다.

```python
from pyspark.sql.types import StructType,StructField, StringType, IntegerType
data2 = [("James","Smith","Joe","4355","M",3000),
    ("Michael","Rose","Edward","40288","F",4000)
  ]

schema = StructType([ \
    StructField("firstname",StringType(),True), \
    StructField("middlename",StringType(),True), \
    StructField("lastname",StringType(),True), \
    StructField("id", StringType(), True), \
    StructField("gender", StringType(), True), \
    StructField("salary", IntegerType(), True) \
  ])
 
df = spark.createDataFrame(data=data2,schema=schema)

df.write.csv("abfss://<<ENTER NAME OF FILE SYSTEM>>@<<ENTER NAME OF PRIMARY STORAGE ACCOUNT>>.dfs.core.windows.net/validate_permissions.csv")

```
오류가 발생 하는 경우 필요한 권한이 없는 것일 수 있습니다. 필요한 권한을 얻는 방법에 대 한 자세한 내용은 [저장소 Blob 데이터 참가자 또는 저장소 Blob 데이터 소유자 권한 할당](../../storage/common/storage-auth-aad-rbac-portal.md#assign-an-azure-built-in-role)문서를 참조 하세요.

또한 파이프라인을 실행 하는 경우 작업 영역 MSI에는 저장소 Blob 데이터 소유자 또는 저장소 Blob 데이터 참가자 권한도 있어야 합니다. 작업 영역 id에이 권한을 부여 하는 방법을 알아보려면 [작업 영역 관리 id에 권한 부여](../security/how-to-grant-workspace-managed-identity-permissions.md)를 참조 하세요.

## <a name="check-the-requirements-file"></a>요구 사항 파일 확인
***requirements.txt*** 파일 (pip 고정 명령의 출력)은 가상 환경을 업그레이드 하는 데 사용할 수 있습니다. 이 파일은 [pip 고정](https://pip.pypa.io/en/stable/reference/pip_freeze/) 참조 설명서에 설명 된 형식을 따릅니다.

다음 제한 사항에 유의 해야 합니다.
   -  PyPI 패키지 이름은 정확한 버전과 함께 나열 되어야 합니다. 
   -  요구 사항 파일의 내용에는 추가 빈 줄 이나 문자를 포함 하지 않아야 합니다. 
   -  [Synapse 런타임은](apache-spark-version-support.md) 서버를 사용 하지 않는 모든 Apache Spark 풀에 미리 설치 된 라이브러리 집합을 포함 합니다. 기본 런타임에 미리 설치 된 패키지를 다운 그레이드할 수 없습니다. 패키지는 추가 하거나 업그레이드할 수만 있습니다.
   -  PySpark, Python, Scala/Java, .NET 또는 Spark 버전을 변경 하는 것은 지원 되지 않습니다.

다음 코드 조각에서는 요구 사항 파일에 필요한 형식을 보여 줍니다.

```
absl-py==0.7.0
adal==1.2.1
alabaster==0.7.10
```

## <a name="validate-wheel-files"></a>휠 파일 유효성 검사
Synapse 서버를 사용 하지 않는 Apache Spark 풀은 Linux 배포를 기반으로 합니다. PyPI에서 직접 휠 파일을 다운로드 하 고 설치 하는 경우 Linux에서 빌드된 버전을 선택 하 고 Spark 풀과 동일한 Python 버전에서 실행 해야 합니다.

>[!IMPORTANT]
>세션 간에 사용자 지정 패키지를 추가 하거나 수정할 수 있습니다. 그러나 업데이트 된 패키지를 보려면 풀과 세션이 다시 시작 될 때까지 기다려야 합니다.

## <a name="check-for-dependency-conflicts"></a>종속성 충돌 확인
 일반적으로 Python 종속성 확인은 관리 하기 어려울 수 있습니다. 종속성 충돌을 로컬로 디버그 하려면 Synapse 런타임을 기반으로 사용자 고유의 가상 환경을 만들고 변경 내용의 유효성을 검사할 수 있습니다.

환경을 다시 만들고 업데이트의 유효성을 검사 하려면 다음을 수행 합니다.
 1. 템플릿을 [다운로드](https://github.com/Azure-Samples/Synapse/blob/main/Spark/Python/base_environment.yml) 하 여 Synapse 런타임을 로컬에서 다시 만듭니다. 템플릿과 실제 Synapse 환경 간에 약간의 차이가 있을 수 있습니다.
   
 2. [다음 지침](https://docs.conda.io/projects/conda/latest/user-guide/tasks/manage-environments.html)을 사용 하 여 가상 환경을 만듭니다. 이 환경에서는 지정 된 라이브러리 목록을 사용 하 여 격리 된 Python 설치를 만들 수 있습니다. 
    
    ```
    conda myenv create -f environment.yml
    conda activate myenv
    ```
   
 3. 를 사용 하 여 지정 된 패키지를 사용 하 여 ``pip install -r <provide your req.txt file>`` 가상 환경을 업데이트 합니다. 설치로 인해 오류가 발생 하는 경우 Synapse base runtime에 미리 설치 된 것과 제공 된 요구 사항 파일에 지정 된 항목 간에 충돌이 발생할 수 있습니다. 서버를 사용 하지 않는 Apache Spark 풀에서 업데이트 된 라이브러리를 가져오려면 이러한 종속성 충돌을 해결 해야 합니다.

## <a name="next-steps"></a>다음 단계
- 기본 라이브러리 보기: [Apache Spark 버전 지원](apache-spark-version-support.md)