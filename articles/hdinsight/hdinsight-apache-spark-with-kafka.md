---
title: "Azure HDInsight의 Kafka에서 Apache Spark 사용 | Microsoft Docs"
description: "HDInsight에서 Spark를 사용하여 HDInsight 클러스터의 Kafka에 데이터를 읽고 쓰는 방법에 대해 알아봅니다. 이 예제에서는 Jupyter Notebook의 Scala를 사용하여 HDInsight의 Kafka에 임의의 데이터를 쓴 다음 Spark 스트리밍을 사용하여 해당 데이터를 다시 읽습니다."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: dd8f53c1-bdee-4921-b683-3be4c46c2039
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/13/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: c56decc1f7603795e027ce20363c387c593999ae
ms.lasthandoff: 03/25/2017

---
# <a name="use-apache-spark-with-kafka-preview-on-hdinsight"></a>HDInsight의 Kafka(미리 보기)에서 Apache Spark 사용

Apache Spark를 사용하여 Apache Kafka에(서) 데이터를 스트리밍할 수 있습니다. 이 문서에서는 HDInsight의 Spark에서 Jupyter Notebook을 사용하여 Kafka에(서) 데이터를 스트리밍하는 방법에 대해 알아봅니다.

> [!NOTE]
> 이 문서의 단계는 HDInsight의 Spark와 HDInsight의 Kafka 클러스터를 모두 포함하는 Azure 리소스 그룹을 만듭니다. 이러한 클러스터는 모두 Azure Virtual Network에 있으며, 여기서는 Spark 클러스터와 Kafka 클러스터 간에 직접 통신할 수 있습니다.
> 
> 이 문서의 단계를 완료하는 경우 과도한 요금이 청구되지 않도록 클러스터를 삭제해야 합니다.

## <a name="prerequisites"></a>필수 조건

* Azure 구독

* SSH 클라이언트(`ssh` 및 `scp` 명령 필요) - 자세한 내용은 [HDInsight와 함께 SSH 사용](hdinsight-hadoop-linux-use-ssh-unix.md)을 참조합니다.

* [cURL](https://curl.haxx.se/) - HTTP 요청을 만들기 위한 플랫폼 간 유틸리티입니다.

* [jq](https://stedolan.github.io/jq/) - JSON 문서를 구문 분석하기 위한 플랫폼 간 유틸리티입니다.

## <a name="create-the-clusters"></a>클러스터 만들기

HDInsight의 Apache Kafka는 공용 인터넷을 통한 액세스를 Kafka broker에 제공하지 않습니다. Kafka와 통신하는 대상은 Kafka 클러스터의 노드와 동일한 Azure 가상 네트워크에 있어야 합니다. 여기서는 Kafka 클러스터와 Spark 클러스터가 모두 Azure 가상 네트워크에 있습니다. 클러스터 간의 통신 흐름을 보여 주는 다이어그램은 다음과 같습니다.

![Azure 가상 네트워크에 있는 Spark 및 Kafka 클러스터 다이어그램](./media/hdinsight-apache-spark-with-kafka/spark-kafka-vnet.png)

> [!NOTE]
> Kafka 자체는 가상 네트워크 내의 통신으로 제한되지만, 클러스터의 다른 서비스(예: SSH, Ambari)는 인터넷을 통해 액세스할 수 있습니다. HDInsight에서 사용할 수 있는 공용 포트에 대한 자세한 내용은 [HDInsight에서 사용하는 포트 및 URI](hdinsight-hadoop-port-settings-for-services.md)를 참조하세요.

Azure 가상 네트워크, Kafka 클러스터 및 Spark 클러스터를 수동으로 만들 수 있지만 Azure Resource Manager 템플릿을 사용하는 것이 더 쉽습니다. 다음 단계에 따라 Azure 가상 네트워크, Kafka 클러스터 및 Spark 클러스터를 Azure 구독에 배포합니다.

1. Azure에 로그인하고 Azure Portal에서 템플릿을 열려면 다음 단추를 사용합니다.
    
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-kafka-spark-cluster-in-vnet.json" target="_blank"><img src="./media/hdinsight-apache-spark-with-kafka/deploy-to-azure.png" alt="Deploy to Azure"></a>
    
    Azure Resource Manager 템플릿은 **https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-spark-cluster-in-vnet.json**에 있습니다.

2. 다음 정보를 사용하여 **사용자 지정 배포** 블레이드의 항목을 채웁니다.
   
    ![HDInsight 사용자 지정 배포](./media/hdinsight-apache-spark-with-kafka/parameters.png)
   
    * **리소스 그룹**: 그룹을 만들거나 기존 그룹을 선택합니다. 이 그룹에는 HDInsight 클러스터가 포함됩니다.

    * **위치**: 지리적으로 가까운 위치를 선택합니다. 이 위치는 __SETTINGS__ 섹션의 위치와 일치해야 합니다.

    * **기본 클러스터 이름**: 이 값은 Spark 및 Kafka 클러스터의 기본 이름으로 사용됩니다. 예를 들어, **hdi**를 입력하면 spark-hdi__라는 Spark 클러스터와 **kafka-hdi**라는 Kafka 클러스터가 만들어집니다.

    * **클러스터 로그인 사용자 이름**: Spark 및 Kafka 클러스터의 관리자 이름입니다.

    * **클러스터 로그인 암호**: Spark 및 Kafka 클러스터의 관리자 사용자 암호입니다.

    * **SSH 사용자 이름**: Spark 및 Kafka 클러스터에 만들 SSH 사용자입니다.

    * **SSH 암호**: Spark 및 Kafka 클러스터에 대한 SSH 사용자의 암호입니다.

    * **위치**: 클러스터가 만들어지는 지역입니다.

3. **사용 약관**을 읽은 다음 **위에 명시된 사용 약관에 동의함**을 선택합니다.

4. 마지막으로 **대시보드에 고정**을 선택한 다음 **구매**를 선택합니다. 클러스터를 만드는 데 약 20분이 걸립니다.

리소스를 만들면 클러스터 및 웹 대시보드를 포함하는 리소스 그룹에 대한 블레이드로 리디렉션됩니다.

![vnet 및 클러스터에 대한 리소스 그룹 블레이드](./media/hdinsight-apache-spark-with-kafka/groupblade.png)

> [!IMPORTANT]
> HDInsight 클러스터의 이름은 **spark-BASENAME** 및 **kafka-BASENAME**이며, 여기서 BASENAME은 템플릿에 제공된 이름입니다. 이후 단계에서 클러스터에 연결할 때 이러한 이름을 사용합니다.

## <a name="get-the-code"></a>코드 가져오기

이 문서에서 설명하는 예제 코드는 [https://github.com/Azure-Samples/hdinsight-spark-scala-kafka](https://github.com/Azure-Samples/hdinsight-spark-scala-kafka)에서 제공됩니다.

## <a name="understand-the-code"></a>코드 이해

이 예제에서는 Scala 응용 프로그램을 Jupyter Notebook에는 사용합니다. Notebook의 코드에서 사용하는 데이터 조각은 다음과 같습니다.

* __Kafka broker__: broker 프로세스는 Kafka 클러스터의 각 작업 노드에서 실행됩니다. broker 목록은 Kafka에 데이터를 쓰는 생산자 구성 요소에서 필요합니다.

* __Zookeeper 호스트__: Kafka에서 데이터를 사용할(읽을) 때 Kafka 클러스터의 Zookeeper 호스트가 사용됩니다.

* __토픽 이름__: 데이터를 쓰고 읽는 토픽의 이름입니다. 이 예제에서는 `sparktest`라는 토픽이 필요합니다.

Kafka broker 및 Zookeeper 호스트 정보를 얻는 방법에 대해서는 [Kafka 호스트 정보](#kafkahosts) 섹션을 참조하세요.

Notebook의 코드에서 수행하는 작업은 다음과 같습니다.

* `sparktest`라는 Kafka 토픽에서 데이터를 읽은 다음 데이터의 각 단어를 계산하고, `wordcounts`라는 임시 테이블에 단어와 개수를 저장하는 소비자를 만듭니다.

* `sparktest`이라는 Kafka 토픽에 임의의 문장을 쓰는 생산자를 만듭니다.

* `wordcounts` 테이블에서 데이터를 선택하여 개수를 표시합니다.

프로젝트의 각 셀에는 코드 수행 작업을 설명하는 주석 또는 텍스트 섹션이 있습니다.

##<a id="kafkahosts"></a> Kafka 호스트 정보

HDInsight에서 Kafka와 작동하는 응용 프로그램을 만들 때는 무엇보다도 먼저 Kafka 클러스터에 대한 Kafka broker와 Zookeeper 호스트 정보를 가져와야 합니다. 이는 클라이언트 응용 프로그램에서 Kafka와 통신하는 데 사용됩니다.

> [!NOTE]
> Kafka broker와 Zookeeper 호스트는 인터넷을 통해 직접 액세스 할 수 없습니다. Kafka를 사용하는 모든 응용 프로그램은 Kafka 클러스터에서 실행하거나 Kafka 클러스터와 동일한 Azure Virtual Network에서 실행해야 합니다. 이 경우 예제는 동일한 가상 네트워크에 있는 HDInsight 클러스터의 Spark에서 실행됩니다.

개발 환경에서 다음 명령을 사용하여 broker 및 Zookeeper 정보를 검색합니다. __PASSWORD__는 클러스터를 만들 때 사용한 로그인(관리자) 암호로 바꿉니다. __BASENAME__은 클러스터를 만들 때 사용한 기본 이름으로 바꿉니다.

* __Kafka broker__ 정보를 가져오려면

        curl -u admin:PASSWORD -G "https://kafka-BASENAME.azurehdinsight.net/api/v1/clusters/kafka-BASENAME/services/KAFKA/components/KAFKA_BROKER" | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")'

    > [!IMPORTANT]
    > Windows PowerShell에서 이 명령을 사용하면 셸 인용 방법 오류가 발생할 수 있습니다. 이 경우 사용할 명령: `curl -u admin:PASSWORD -G "https://kafka-BASENAME.azurehdinsight.net/api/v1/clusters/kafka-BASENAME/services/KAFKA/components/KAFKA_BROKER" | jq -r '["""\(.host_components[].HostRoles.host_name):9092"""] | join(""",""")'`

* __Zookeeper 호스트__ 정보를 가져오려면

        curl -u admin:PASSWORD -G "https://kafka-BASENAME.azurehdinsight.net/api/v1/clusters/kafka-BASENAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")'
    
    > [!IMPORTANT]
    > Windows PowerShell에서 이 명령을 사용하면 셸 인용 방법 오류가 발생할 수 있습니다. 이 경우 사용할 명령: `curl -u admin:PASSWORD -G "https://kafka-BASENAME.azurehdinsight.net/api/v1/clusters/kafka-BASENAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" | jq -r '["""\(.host_components[].HostRoles.host_name):2181"""] | join(""",""")'`

두 명령 모두 다음 텍스트와 비슷한 정보를 반환합니다.

* __Kafka broker__: `wn0-kafka.4rf4ncirvydube02fuj0gpxp4e.ex.internal.cloudapp.net:9092,wn1-kafka.4rf4ncirvydube02fuj0gpxp4e.ex.internal.cloudapp.net:9092`

* __Zookeeper 호스트__: `zk0-kafka.4rf4ncirvydube02fuj0gpxp4e.ex.internal.cloudapp.net:2181,zk1-kafka.4rf4ncirvydube02fuj0gpxp4e.ex.internal.cloudapp.net:2181,zk2-kafka.4rf4ncirvydube02fuj0gpxp4e.ex.internal.cloudapp.net:2181`

> [!IMPORTANT]
> 이 문서의 여러 단계에서 사용되기 때문에 이 정보를 저장해야 합니다.

## <a name="use-the-jupyter-notebook"></a>Jupyter Notebook 사용

Jupyter Notebook 예제를 사용하려면 Spark 클러스터의 Jupyter Notebook 서버에 이 예제를 업로드해야 합니다. 다음 단계에 따라 Notebook을 업로드합니다.

1. 웹 브라우저에서 다음 URL을 사용하여 Spark 클러스터의 Jupyter Notebook 서버에 연결합니다. __BASENAME__은 클러스터를 만들 때 사용한 기본 이름으로 바꿉니다.

        https://spark-BASENAME.azurehdinsight.net/jupyter

    메시지가 표시되면 클러스터를 만들 때 사용한 클러스터 로그인(관리자) 이름과 암호를 입력합니다.

2. 페이지의 오른쪽 위에서 __업로드__ 단추를 사용하여 `KafkaStreaming.ipynb` 파일을 업로드합니다. 파일 브라우저 대화 상자에서 파일을 선택하고 __열기__를 선택합니다. 

    ![업로드 단추를 사용하여 Notebook을 선택하고 업로드](./media/hdinsight-apache-spark-with-kafka/upload-button.png)

    ![KafkaStreaming.ipynb 파일 선택](./media/hdinsight-apache-spark-with-kafka/select-notebook.png)

3. Notebook 목록에서 __KafkaStreaming.ipynb__ 항목을 찾아 그 옆에 있는 __업로드__ 단추를 선택합니다.

    ![KafkaStreaming.ipynb 항목 옆의 업로드 단추를 사용하여 Notebook 서버에 업로드](./media/hdinsight-apache-spark-with-kafka/upload-notebook.png)

4. 파일이 업로드되면 __KafkaStreaming.ipynb__ 항목을 선택하여 Notebook을 엽니다. 이 예제를 완료하려면 Notebook의 지시 사항을 따릅니다.

## <a name="delete-the-cluster"></a>클러스터 삭제

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

이 문서의 단계를 수행하면 동일한 Azure 리소스 그룹에 두 클러스터가 모두 만들어지므로 Azure Portal에서 해당 리소스 그룹을 삭제할 수 있습니다. 그룹을 삭제하면 이 문서의 단계를 수행하여 만들어진 모든 리소스, Azure Virtual Network 및 클러스터에서 사용하는 저장소 계정이 제거됩니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Spark을 사용하여 Kafka에(서) 쓰고 읽는 방법을 배웠습니다. Kafka를 사용하는 다른 방법을 찾으려면 다음 링크를 사용하세요.

* [HDInsight에서 Apache Kafka 시작](hdinsight-apache-kafka-get-started.md)
* [MirrorMaker를 사용하여 HDInsight에 Kafka 복제본 만들기](hdinsight-apache-kafka-mirroring.md)
* [HDInsight의 Kafka에서 Apache Storm 사용](hdinsight-apache-storm-with-kafka.md)


