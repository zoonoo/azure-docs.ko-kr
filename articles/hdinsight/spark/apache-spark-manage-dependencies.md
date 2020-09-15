---
title: Azure HDInsight에서 Spark 응용 프로그램 종속성 관리
description: 이 문서에서는 PySpark 및 Scala 응용 프로그램에 대 한 HDInsight Spark 클러스터에서 Spark 종속성을 관리 하는 방법을 소개 합니다.
author: yanancai
ms.author: yanacai
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 09/09/2020
ms.openlocfilehash: dafb4485ae9b10d89fa36bd790dcf3a799054de3
ms.sourcegitcommit: 51df05f27adb8f3ce67ad11d75cb0ee0b016dc5d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/14/2020
ms.locfileid: "90064177"
---
# <a name="manage-spark-application-dependencies"></a>Spark 응용 프로그램 종속성 관리

이 문서에서는 HDInsight에서 실행 되는 Spark 응용 프로그램에 대 한 종속성을 관리 하는 방법을 알아봅니다. Spark 응용 프로그램 및 클러스터 범위에서 Scala 및 PySpark를 모두 다룹니다.

빠른 링크를 사용 하 여 사용자 사례에 따라 섹션으로 이동 합니다.
* [Jupyter 노트북을 사용 하 여 Spark 작업 jar 종속성 설정](#use-jupyter-notebook)
* [Use Azure Toolkit for IntelliJ를 사용 하 여 Spark 작업 jar 종속성 설정](#use-azure-toolkit-for-intellij)
* [Spark 클러스터에 대 한 jar 종속성 구성](#jar-libs-for-cluster)
* [Jar 종속성을 안전 하 게 관리](#safely-manage-jar-dependencies)
* [Jupyter 노트북을 사용 하 여 Spark 작업 Python 패키지 설정](#use-jupyter-notebook-1)
* [Spark 클러스터에 대 한 Python 패키지 안전 하 게 관리](#python-packages-for-cluster)

## <a name="jar-libs-for-one-spark-job"></a>단일 Spark 작업에 대 한 Jar 라이브러리
### <a name="use-jupyter-notebook"></a>Jupyter 노트북 사용
Spark 세션이 Scala에 대 한 Spark 커널의 Jupyter Notebook에서 시작 되는 경우 다음에서 패키지를 구성할 수 있습니다.

* [Maven 리포지토리](https://search.maven.org/)또는 [Spark 패키지](https://spark-packages.org/)의 커뮤니티 제공 패키지.
* Jar 파일은 클러스터의 기본 저장소에 저장 됩니다.

매직을 사용 하 여 `%%configure` 외부 패키지를 사용 하도록 노트북을 구성 합니다. 외부 패키지를 사용하는 Notebook에서 `%%configure` Magic을 호출해야 합니다. 이렇게 하면 커널은 세션이 시작되기 전에 패키지를 사용하도록 구성됩니다.

>
>[!IMPORTANT]  
>첫 번째 셀에서 커널을 구성하는 것을 잊은 경우 `%%configure`과 `-f` 매개 변수를 사용할 수 있지만 세션이 다시 시작되고 모든 진행률이 손실됩니다.

**Maven 리포지토리 또는 Spark 패키지의 패키지에 대 한 샘플**

Maven 리포지토리에서 패키지를 찾은 후 **GroupId**, **ArtifactId**및 **Version**에 대 한 값을 수집 합니다. 콜론(**:**)으로 구분된 세 개의 값을 연결합니다.

   ![패키지 스키마 연결](./media/apache-spark-manage-dependencies/spark-package-schema.png "패키지 스키마 연결")

수집 하는 값이 클러스터와 일치 하는지 확인 합니다. 이 경우 Scala 2.11에는 Spark Cosmos DB 커넥터 패키지를 사용 하 고 HDInsight 3.6 Spark 클러스터에는 Spark 2.3을 사용 하 고 있습니다. 확실 하지 않은 경우 `scala.util.Properties.versionString` Spark 커널의 코드 셀에서를 실행 하 여 클러스터 Scala 버전을 가져옵니다. `sc.version`을 실행 하 여 클러스터 Spark 버전을 가져옵니다.

```
%%configure { "conf": {"spark.jars.packages": "com.microsoft.azure:azure-cosmosdb-spark_2.3.0_2.11:1.3.3" }}
```

**기본 저장소에 저장 된 Jar에 대 한 샘플**

클러스터 기본 저장소에서 jar 파일에 대 한 [URI 체계](../hdinsight-hadoop-linux-information.md#URI-and-scheme) 를 사용 합니다. Azure Storage는 `wasb://`, Azure Data Lake Storage Gen2는 `abfs://`, Azure Data Lake Storage Gen1은 `adl://`입니다. Azure Storage 또는 Data Lake Storage Gen2에 대해 보안 전송이 사용 되는 경우 URI는 `wasbs://` 또는 `abfss://` 입니다. [보안 전송](../../storage/common/storage-require-secure-transfer.md)을 참조 하세요.

여러 jar 파일의 경우 쉼표로 구분 된 jar 경로 목록을 사용 Glob 허용 됩니다. Jar은 driver 및 executor 클래스 경로에 포함 되어 있습니다.

```
%%configure { "conf": {"spark.jars": "wasb://mycontainer@mystorageaccount.blob.core.windows.net/libs/azure-cosmosdb-spark_2.3.0_2.11-1.3.3.jar" }}
```

외부 패키지를 구성한 후 코드 셀에서 가져오기를 실행 하 여 패키지가 제대로 배치 되었는지 확인할 수 있습니다.

```scala
import com.microsoft.azure.cosmosdb.spark._
```

### <a name="use-azure-toolkit-for-intellij"></a>Azure Toolkit for IntelliJ 사용
[Azure Toolkit for IntelliJ 플러그](./apache-spark-intellij-tool-plugin.md) 인은 HDInsight 클러스터에 Spark Scala 응용 프로그램을 제출 하는 UI 환경을 제공 합니다. `Referenced Jars` `Referenced Files` Spark 응용 프로그램을 제출할 때 jar 라이브러리 경로를 구성 하는 및 속성을 제공 합니다. [HDInsight 용 Azure Toolkit for IntelliJ 플러그 인을 사용 하는 방법](./apache-spark-intellij-tool-plugin.md#run-a-spark-scala-application-on-an-hdinsight-spark-cluster)에 대 한 자세한 내용을 참조 하세요.

![Spark 제출 대화 상자](./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-app-02.png)

## <a name="jar-libs-for-cluster"></a>클러스터용 Jar 라이브러리
일부 경우에는 기본적으로 모든 응용 프로그램을 동일한 종속성으로 설정할 수 있도록 클러스터 수준에서 jar 종속성을 구성 하는 것이 좋습니다. 이 방법은 Spark 드라이버 및 executor 클래스 경로에 jar 경로를 추가 하는 것입니다.

1. 아래 샘플 스크립트 작업을 실행 하 여 jar 파일을 기본 저장소에서 `wasb://mycontainer@mystorageaccount.blob.core.windows.net/libs/*` 클러스터 로컬 파일 시스템으로 복사 `/usr/libs/sparklibs` 합니다. Linux는를 사용 하 여 `:` 클래스 경로 목록을 구분 하지만, HDInsight는와 같은 스키마를 가진 저장소 경로만 지원 하기 때문에이 단계가 필요 `wasb://` 합니다. 원격 저장소 경로는 클래스 경로에 직접 추가 하는 경우 제대로 작동 하지 않습니다.

    ```bash
    sudo mkdir -p /usr/libs/sparklibs
    sudo hadoop fs -copyToLocal wasb://mycontainer@mystorageaccount.blob.core.windows.net/libs/*.* /usr/libs/sparklibs
    ```

2. Ambari에서 Spark 서비스 구성을 변경 하 여 클래스 경로를 업데이트 합니다. **Ambari > Spark > Configs > Custom Spark2-defaults**로 이동 합니다. 다음과 같이 **속성을 추가** 합니다. `:`하나 이상의 경로를 추가할 수 있는 경우를 사용 하 여 경로를 구분 합니다. Glob를 사용할 수 있습니다.

    ```
    spark.driver.extraClassPath=/usr/libs/sparklibs/*
    spark.executor.extraClassPath=/usr/libs/sparklibs/*
    ```

   ![Spark 기본 구성 변경](./media/apache-spark-manage-dependencies/change-spark-default-config.png "Spark 기본 구성 변경")

3. 변경 된 구성을 저장 하 고 영향을 받는 서비스를 다시 시작 합니다.

   ![영향을 받는 서비스 다시 시작](./media/apache-spark-manage-dependencies/restart-impacted-services.png "영향을 받는 서비스 다시 시작")

[스크립트 작업](../hdinsight-hadoop-customize-cluster-linux.md)을 사용 하 여 단계를 자동화할 수 있습니다. [Hive 사용자 지정 라이브러리를 추가](https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh) 하는 스크립트 작업은 좋은 참조입니다. Spark 서비스 configs를 변경 하는 경우 구성 파일을 직접 수정 하는 대신 Ambari Api를 사용 해야 합니다. 

## <a name="safely-manage-jar-dependencies"></a>Jar 종속성을 안전 하 게 관리
HDInsight 클러스터에는 기본 제공 jar 종속성이 있으며 이러한 jar 버전에 대 한 업데이트는 시간에서 발생 합니다. 기본 제공 jar과 참조를 위해 가져오는 jar 간의 버전 충돌을 방지 하려면 [응용 프로그램 종속성을 음영](./safely-manage-jar-dependency.md)처리 하십시오.

## <a name="python-packages-for-one-spark-job"></a>단일 Spark 작업에 대 한 Python 패키지
### <a name="use-jupyter-notebook"></a>Jupyter 노트북 사용
HDInsight Jupyter 노트북 PySpark 커널은 PyPi 또는 Anaconda 패키지 리포지토리에서 Python 패키지를 직접 설치 하는 것을 지원 하지 않습니다. `.zip`, `.egg` 또는 종속성이 있고 `.py` 하나의 Spark 세션에 대해 참조 하려는 경우 아래 단계를 따르세요.

1. 아래 샘플 스크립트 작업을 실행 하 `.zip` 여 `.egg` 또는 `.py` 기본 저장소에서 `wasb://mycontainer@mystorageaccount.blob.core.windows.net/libs/*` 클러스터 로컬 파일 시스템으로 파일을 복사 `/usr/libs/pylibs` 합니다. Linux를 사용 하 여 `:` 검색 경로 목록을 구분 하는 데에는이 단계가 필요 하지만, HDInsight는와 같은 체계의 저장소 경로만 지원 `wasb://` 합니다. 를 사용 하는 경우 원격 저장소 경로가 제대로 작동 하지 않습니다 `sys.path.insert` .

    ```bash
    sudo mkdir -p /usr/libs/pylibs
    sudo hadoop fs -copyToLocal wasb://mycontainer@mystorageaccount.blob.core.windows.net/libs/*.* /usr/libs/pylibs
    ```

2. 노트북에서 PySpark 커널을 사용 하 여 코드 셀에 아래 코드를 실행 합니다.

   ```python
   import sys
   sys.path.insert(0, "/usr/libs/pylibs/pypackage.zip")
   ```

3. `import`을 실행 하 여 패키지가 성공적으로 포함 되었는지 확인 합니다.  

## <a name="python-packages-for-cluster"></a>클러스터에 대 한 Python 패키지
스크립트 작업을 통해 conda 명령을 사용 하 여 Anaconda에서 클러스터로 Python 패키지를 설치할 수 있습니다. 설치 된 패키지는 클러스터 수준에 있으며 모든 응용 프로그램에 적용 됩니다. 

HDInsight Spark 클러스터에는 두 가지 기본 제공 Python 설치, Anaconda Python 2.7 및 Anaconda Python 3.5이 있습니다. 서비스의 기본 Python 설정에 대해 자세히 이해 하 고 클러스터를 중단 하지 않고 외부 Python 패키지를 안전 하 게 설치 하는 방법에 대 한 자세한 내용은 [클러스터에 대 한 Python 종속성을 안전 하 게 관리](./apache-spark-python-package-installation.md)
