---
title: 라이브러리 설치 오류 문제 해결
description: 이 자습서에서는 라이브러리 설치 오류 문제를 해결하는 방법을 개략적으로 설명합니다.
services: synapse-analytics
author: midesa
ms.author: midesa
ms.service: synapse-analytics
ms.subservice: spark
ms.topic: conceptual
ms.date: 01/04/2021
ms.openlocfilehash: 006abf62c605c2ca34fd1adeadee8e29ae0fb8fb
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/17/2021
ms.locfileid: "107588312"
---
# <a name="troubleshoot-library-installation-errors"></a>라이브러리 설치 오류 문제 해결 
애플리케이션에서 타사 또는 로컬로 빌드된 코드를 사용할 수 있도록 하려면 서버리스 Apache Spark 풀 중 하나에 라이브러리를 설치하면 됩니다. requirements.txt 파일에 나열된 패키지는 풀을 시작할 때 PyPi에서 다운로드됩니다. 이 요구 사항 파일은 Spark 인스턴스를 Spark 풀에서 만들 때마다 사용됩니다. Spark 풀에 대해 라이브러리를 설치한 후에는 동일한 풀을 사용하는 모든 세션에서 사용할 수 있습니다. 

경우에 따라 Apache Spark 풀에 라이브러리가 나타나지 않을 수도 있습니다. 이 경우는 일반적으로 제공된 requirements.txt 또는 지정된 라이브러리에 오류가 있을 때 발생합니다. 라이브러리 설치 프로세스에서 오류가 발생하면 Apache Spark 풀은 Synapse 기본 런타임에 지정된 라이브러리로 되돌아갑니다.

이 문서의 목표는 일반적인 문제를 제공하고 라이브러리 설치 오류를 디버그하는 데 도움을 주는 것입니다.

## <a name="force-update-your-apache-spark-pool"></a>Apache Spark 풀 강제 업데이트
Apache Spark 풀에서 라이브러리를 업데이트하면 이 변경 내용은 풀이 다시 시작된 후 선택됩니다. 활성 작업이 있는 경우 해당 작업은 원래 버전의 Spark 풀에서 계속 실행됩니다.

**새 설정 적용** 옵션을 선택하여 변경 내용을 강제로 적용할 수 있습니다. 이 설정은 선택한 Spark 풀의 모든 현재 세션을 종료합니다. 세션이 종료되면 풀이 다시 시작될 때까지 기다려야 합니다. 

![Python 라이브러리 추가](./media/apache-spark-azure-portal-add-libraries/update-libraries.png "Python 라이브러리 추가")

## <a name="track-installation-progress"></a>설치 진행률 추적
시스템 예약 Spark 작업은 풀이 새 라이브러리 세트로 업데이트될 때마다 시작됩니다. 이 Spark 작업은 라이브러리 설치 상태를 모니터링하는 데 도움이 됩니다. 라이브러리 충돌이나 기타 문제로 인해 설치에 실패하는 경우 Spark 풀이 이전 또는 기본 상태로 되돌아갑니다. 

또한 사용자는 설치 로그를 검사하여 종속성 충돌을 식별하거나 풀 업데이트 중에 설치된 라이브러리를 확인할 수 있습니다.

이 로그를 보려면:
1. **모니터** 탭에서 Spark 애플리케이션 목록으로 이동합니다. 
2. 풀 업데이트에 해당하는 시스템 Spark 애플리케이션 작업을 선택합니다. 이 시스템 작업은 *SystemReservedJob-LibraryManagement* 제목으로 실행됩니다.
   ![시스템 예약 라이브러리 작업을 강조 표시하는 스크린샷](./media/apache-spark-azure-portal-add-libraries/system-reserved-library-job.png "시스템 라이브러리 작업 보기")
3. 전환하여 **driver** 및 **stdout** 로그를 봅니다. 
4. 결과 내에서 패키지의 설치와 관련된 로그를 확인합니다.
    ![시스템 예약 라이브러리 작업 결과를 강조 표시하는 스크린샷](./media/apache-spark-azure-portal-add-libraries/system-reserved-library-job-results.png "시스템 라이브러리 작업 진행률 보기")

## <a name="validate-your-permissions"></a>권한 유효성 검사
라이브러리를 설치하고 업데이트하려면 Azure Synapse Analytics 작업 영역에 연결된 기본 Azure Data Lake Storage Gen2 스토리지 계정에 대한 **Storage Blob 데이터 기여자** 또는 **Storage Blob 데이터 소유자** 권한이 있어야 합니다.

이 권한이 있는지 유효성을 검사하기 위해 다음 코드를 실행할 수 있습니다.

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
오류가 발생한다면 필요한 권한이 없는 것일 수 있습니다. 필요한 권한을 얻는 방법을 알아보려면 [Storage Blob 데이터 기여자 또는 Storage Blob 데이터 소유자 권한 할당](../../storage/common/storage-auth-aad-rbac-portal.md#assign-an-azure-built-in-role) 문서를 참조하세요.

또한 파이프라인을 실행하는 경우 작업 영역 MSI에는 Storage Blob 데이터 소유자 또는 Storage Blob 데이터 기여자 권한도 있어야 합니다. 작업 영역 ID에 이 권한을 부여하는 방법을 알아보려면 [작업 영역 관리 ID에 권한 부여](../security/how-to-grant-workspace-managed-identity-permissions.md)를 참조하세요.

## <a name="check-the-environment-configuration-file"></a>환경 구성 파일 확인
환경 구성 파일을 사용하여 Conda 환경을 업그레이드할 수 있습니다. Python 풀 관리의 이 허용되는 파일 형식은 [여기](./apache-spark-manage-python-packages.md)에 나열되어 있습니다.

다음 제한 사항에 유의해야 합니다.
   -  요구 사항 파일의 콘텐츠에는 추가 빈 줄이나 문자가 포함되면 안 됩니다. 
   -  [Synapse 런타임](apache-spark-version-support.md)에는 모든 서버리스 Apache Spark 풀에 미리 설치된 라이브러리 세트가 포함됩니다. 기본 런타임에 미리 설치되어 제공되는 패키지는 제거할 수 없습니다.
   -  PySpark, Python, Scala/Java, .NET 또는 Spark 버전은 변경할 수 없습니다.
   -  Python 세션 범위 라이브러리는 YML 확장명이 있는 파일만 허용합니다.

## <a name="validate-wheel-files"></a>휠 파일 유효성 검사
Synapse 서버리스 Apache Spark 풀은 Linux 배포를 기반으로 합니다. PyPI에서 직접 휠 파일을 다운로드하고 설치하는 경우 Linux에서 빌드되고 Spark 풀과 동일한 Python 버전에서 실행되는 버전을 선택해야 합니다.

>[!IMPORTANT]
>세션 간에 사용자 지정 패키지를 추가하거나 수정할 수 있습니다. 그러나 업데이트된 패키지를 보려면 풀과 세션이 다시 시작될 때까지 기다려야 합니다.

## <a name="check-for-dependency-conflicts"></a>종속성 충돌 확인
 일반적으로 Python 종속성 확인은 관리하기 까다로울 수 있습니다. 종속성 충돌을 로컬에서 디버그하려면 Synapse 런타임을 기반으로 고유한 가상 환경을 만들고 변경 내용의 유효성을 검사할 수 있습니다.

환경을 다시 만들고 업데이트의 유효성을 검사하려면:
 1. 템플릿을 [다운로드](https://github.com/Azure-Samples/Synapse/blob/main/Spark/Python/base_environment.yml)하여 Synapse 런타임을 로컬에서 다시 만듭니다. 템플릿과 실제 Synapse 환경 간에 약간 차이가 있을 수 있습니다.
   
 2. [다음 지침](https://conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html#activating-an-environment)을 사용하여 가상 환경을 만듭니다. 이 환경에서는 지정된 라이브러리 목록을 사용하여 격리된 Python 설치를 만들 수 있습니다. 
    
    ```
    conda myenv create -f environment.yml
    conda activate myenv
    ```
   
 3. ``pip install -r <provide your req.txt file>``를 사용하여 지정된 패키지로 가상 환경을 업데이트합니다. 설치로 인해 오류가 발생하는 경우 Synapse 기본 런타임에 미리 설치된 항목과 제공된 요구 사항 파일에 지정된 항목 간에 충돌이 발생할 수 있습니다. 서버리스 Apache Spark 풀에서 업데이트된 라이브러리를 가져오려면 이 종속성 충돌을 해결해야 합니다.

>[!IMPORTANT]
>pip와 conda를 함께 사용하는 경우 문제가 발생할 수 있습니다. pip와 conda를 결합하는 경우 이 [권장 모범 사례](https://conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html#activating-an-environment)를 따르는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계
- 기본 라이브러리 보기: [Apache Spark 버전 지원](apache-spark-version-support.md)
