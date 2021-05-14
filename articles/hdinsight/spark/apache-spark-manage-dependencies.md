---
title: Azure HDInsight에서 Spark 애플리케이션 종속성 관리
description: 이 문서에서는 PySpark 및 Scala 애플리케이션을 위한 HDInsight Spark 클러스터의 스파크 종속성을 관리하는 방법에 관해 소개합니다.
author: yanancai
ms.author: yanacai
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 09/09/2020
ms.openlocfilehash: f4940da47b832c2b3c39ab2fa225a229d1d730bf
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106062712"
---
# <a name="manage-spark-application-dependencies"></a>Spark 애플리케이션 종속성 관리

이 문서에서는 HDInsight에서 실행되는 Spark 애플리케이션의 종속성을 관리하는 방법에 관해 알아봅니다. Spark 애플리케이션 및 클러스터 범위에서 Scala와 PySpark를 모두 지원합니다.

빠른 링크를 사용하여 사용자 사례에 따라 섹션으로 이동합니다.
* [Jupyter Notebook을 사용하여 Spark 작업 jar 종속성 설정](#use-jupyter-notebook)
* [Azure Toolkit for IntelliJ를 사용하여 Spark 작업 jar 종속성 설정](#use-azure-toolkit-for-intellij)
* [Spark 클러스터에 대한 jar 종속성 구성](#jar-libs-for-cluster)
* [안전하게 jar 종속성 관리](#safely-manage-jar-dependencies)
* [Jupyter Notebook을 사용하여 Spark 작업 Python 패키지 설정](#use-jupyter-notebook-1)
* [Spark 클러스터에 대한 Python 패키지를 안전하게 관리](#python-packages-for-cluster)

## <a name="jar-libs-for-one-spark-job"></a>단일 Spark 작업에 대한 Jar 라이브러리
### <a name="use-jupyter-notebook"></a>Jupyter Notebook 사용
Scala용 Spark 커널의 Jupyter Notebook에서 Spark 세션이 시작되면 다음에서 패키지를 구성할 수 있습니다.

* [Maven 리포지토리](https://search.maven.org/) 또는 [Spark 패키지](https://spark-packages.org/)의 커뮤니티 제공 패키지
* 클러스터의 기본 스토리지에 저장된 Jar 파일

`%%configure` 매직을 사용하여 외부 패키지를 사용하도록 Notebook을 구성합니다. 외부 패키지를 사용하는 Notebook에서 `%%configure` Magic을 호출해야 합니다. 이렇게 하면 커널은 세션이 시작되기 전에 패키지를 사용하도록 구성됩니다.

>
>[!IMPORTANT]  
>첫 번째 셀에서 커널을 구성하는 것을 잊은 경우 `%%configure`과 `-f` 매개 변수를 사용할 수 있지만 세션이 다시 시작되고 모든 진행률이 손실됩니다.

**Maven 리포지토리 또는 Spark 패키지의 패키지 샘플**

Maven 리포지토리에서 패키지를 찾은 후 **GroupId**, **ArtifactId**, **Version** 에 대한 값을 수집합니다. 콜론(**:**)으로 구분된 세 개의 값을 연결합니다.

   :::image type="content" source="./media/apache-spark-manage-dependencies/spark-package-schema.png " alt-text="패키지 스키마 연결" border="true":::kage schema” border=“true”:::

수집하는 값이 클러스터와 일치하는지 확인합니다. 이 경우 Scala 2.11에는 Spark Cosmos DB 커넥터 패키지를 사용하고 HDInsight 3.6 Spark 클러스터에는 Spark 2.3을 사용하고 있습니다. 확실하지 않은 경우 Spark 커널의 코드 셀에서 `scala.util.Properties.versionString`을 실행하여 클러스터 Scala 버전을 가져옵니다. `sc.version`을 실행하여 클러스터 Spark 버전을 가져옵니다.

```
%%configure { "conf": {"spark.jars.packages": "com.microsoft.azure:azure-cosmosdb-spark_2.3.0_2.11:1.3.3" }}
```

**기본 스토리지에 저장된 Jar에 대한 샘플**

클러스터 기본 스토리지의 jar 파일에 [URI 체계](../hdinsight-hadoop-linux-information.md#URI-and-scheme)를 사용합니다. Azure Storage는 `wasb://`, Azure Data Lake Storage Gen2는 `abfs://`, Azure Data Lake Storage Gen1은 `adl://`입니다. Azure Storage 또는 Data Lake Storage Gen2에 보안 전송을 사용하는 경우 URI는 `wasbs://` 또는 `abfss://`가 됩니다. [보안 전송](../../storage/common/storage-require-secure-transfer.md)을 참조하세요.

여러 jar 파일의 경우 쉼표로 구분된 jar 경로 목록을 사용합니다. GLOB를 사용할 수 있습니다. Jar는 드라이버 및 실행기 클래스 경로에 포함되어 있습니다.

```
%%configure { "conf": {"spark.jars": "wasb://mycontainer@mystorageaccount.blob.core.windows.net/libs/azure-cosmosdb-spark_2.3.0_2.11-1.3.3.jar" }}
```

외부 패키지를 구성한 후 코드 셀에서 가져오기를 실행하여 패키지가 올바르게 배치되었는지 확인할 수 있습니다.

```scala
import com.microsoft.azure.cosmosdb.spark._
```

### <a name="use-azure-toolkit-for-intellij"></a>Azure Toolkit for IntelliJ 사용
[Azure Toolkit for IntelliJ 플러그 인](./apache-spark-intellij-tool-plugin.md)은 Spark Scala 애플리케이션을 HDInsight 클러스터에 제출하는 UI 환경을 제공합니다. 또한 Spark 애플리케이션을 제출할 때 jar 라이브러리 경로를 구성할 수 있는 `Referenced Jars` 및 `Referenced Files` 속성을 제공합니다. [HDInsight에 대해 Azure Toolkit for IntelliJ 플러그 인을 사용하는 방법](./apache-spark-intellij-tool-plugin.md#run-a-spark-scala-application-on-an-hdinsight-spark-cluster)에 관한 자세한 내용을 참조하세요.

:::image type="content" source="./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-app-02.png" alt-text="Spark 제출 대화 상자" border="true":::

## <a name="jar-libs-for-cluster"></a>클러스터용 Jar 라이브러리
일부 경우에는 기본적으로 모든 애플리케이션이 동일한 종속성으로 설정될 수 있도록 클러스터 수준에서 jar 종속성을 구성할 수 있습니다. 이 방식은 Spark 드라이버 및 실행기 클래스 경로에 jar 경로를 추가하는 것입니다.

1. 아래 샘플 스크립트 작업을 실행하여 기본 스토리지에서 `wasb://mycontainer@mystorageaccount.blob.core.windows.net/libs/*`를 클러스터 로컬 파일 시스템 `/usr/libs/sparklibs`로 jar 파일을 복사합니다. Linux에서 클래스 경로 목록을 분리하기 위해 `:`을 사용하지만 HDInsight는 `wasb://`와 같은 체계를 가진 스토리지 경로만 지원하기 때문에 이 단계가 필요합니다. 원격 스토리지 경로를 클래스 경로에 직접 추가하면 올바르게 작동하지 않습니다.

    ```bash
    sudo mkdir -p /usr/libs/sparklibs
    sudo hadoop fs -copyToLocal wasb://mycontainer@mystorageaccount.blob.core.windows.net/libs/*.* /usr/libs/sparklibs
    ```

2. Ambari에서 Spark 서비스 구성을 변경하여 클래스 경로를 업데이트합니다. **Ambari > Spark > Configs > Custom Spark2-defaults** 로 이동합니다. 다음과 같이 **속성을 추가** 합니다. 추가할 경로가 둘 이상인 경우 `:`을 사용하여 경로를 구분합니다. GLOB를 사용할 수 있습니다.

    ```
    spark.driver.extraClassPath=/usr/libs/sparklibs/*
    spark.executor.extraClassPath=/usr/libs/sparklibs/*
    ```

   :::image type="content" source="./media/apache-spark-manage-dependencies/change-spark-default-config.png " alt-text="Spark 기본 구성 변경" border="true":::ult config” border=“true”:::

3. 변경된 구성을 저장하고 영향을 받는 서비스를 다시 시작합니다.

   :::image type="content" source="./media/apache-spark-manage-dependencies/restart-impacted-services.png " alt-text="영향을 받는 서비스 다시 시작" border="true":::ted services” border=“true”:::

[스크립트 작업](../hdinsight-hadoop-customize-cluster-linux.md)을 사용하여 단계를 자동화할 수 있습니다. [Hive 사용자 지정 라이브러리를 추가](https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh)하기 위한 스크립트 작업은 좋은 참조입니다. Spark 서비스 구성을 변경할 때는 구성 파일을 직접 수정하는 대신 Ambari API를 사용해야 합니다. 

## <a name="safely-manage-jar-dependencies"></a>안전하게 jar 종속성 관리
HDInsight 클러스터는 기본 제공 jar 종속성이 있으며 이러한 jar 버전에 대한 업데이트는 때때로 발생합니다. 기본 제공 jar와 참조를 위해 가져온 jar 간의 버전 충돌을 방지하려면 [애플리케이션 종속성을 음영으로 처리](./safely-manage-jar-dependency.md)합니다.

## <a name="python-packages-for-one-spark-job"></a>단일 Spark 작업에 대한 Python 패키지
### <a name="use-jupyter-notebook"></a>Jupyter Notebook 사용
HDInsight Jupyter Notebook PySpark 커널은 PyPi 또는 Anaconda 패키지 리포지토리에서 Python 패키지를 직접 설치하는 것을 지원하지 않습니다. `.zip`, `.egg` 또는 `.py` 종속성이 있고 단일 Spark 세션에서 이러한 종속성을 참조하려면 다음 단계를 따릅니다.

1. 아래 샘플 스크립트 작업을 실행하여 기본 스토리지 `wasb://mycontainer@mystorageaccount.blob.core.windows.net/libs/*`에서 클러스터 로컬 파일 시스템 `/usr/libs/pylibs`로 `.zip`, `.egg` 또는 `.py` 파일을 복사합니다. Linux에서 검색 경로 목록을 분리하기 위해 `:`을 사용하지만 HDInsight는 `wasb://`와 같은 체계를 가진 스토리지 경로만 지원하기 때문에 이 단계가 필요합니다. `sys.path.insert`를 사용하면 원격 스토리지 경로가 올바르게 작동하지 않습니다.

    ```bash
    sudo mkdir -p /usr/libs/pylibs
    sudo hadoop fs -copyToLocal wasb://mycontainer@mystorageaccount.blob.core.windows.net/libs/*.* /usr/libs/pylibs
    ```

2. Notebook에서 PySpark 커널을 사용하여 코드 셀에서 아래 코드를 실행합니다.

   ```python
   import sys
   sys.path.insert(0, "/usr/libs/pylibs/pypackage.zip")
   ```

3. `import`를 실행하여 패키지가 성공적으로 포함되었는지 확인합니다.  

## <a name="python-packages-for-cluster"></a>클러스터에 대한 Python 패키지
스크립트 작업을 통해 Conda 명령을 사용하여 Anaconda에서 클러스터로 Python 패키지를 설치할 수 있습니다. 설치된 패키지는 클러스터 수준이며 모든 애플리케이션에 적용됩니다. 

HDInsight Spark 클러스터에는 두 개의 기본 제공 Python 설치(Anaconda Python 2.7 및 Anaconda Python 3.5)가 있습니다. 서비스의 기본 Python 설정과 클러스터를 중단하지 않고 외부 Python 패키지를 안전하게 설치하는 방법에 대한 자세한 내용은 [클러스터의 Python 종속성을 안전하게 관리](./apache-spark-python-package-installation.md)를 참조하세요.
