---
title: Kafka에서 Apache Spark 구조적 스트림 - Azure HDInsight | Microsoft Docs
description: Apache Spark 스트림(DStream)을 사용하여 Apache Kafka 간에 데이터를 이동하는 방법을 알아봅니다. 이 예제에서는 HDInsight의 Spark에서 Jupyter Notebook을 사용하여 데이터를 스트리밍합니다.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: cgronlun
editor: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: ''
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/04/2018
ms.author: larryfr
ms.openlocfilehash: 49c13bbea537d7de60ecf509bc28675191c0b34d
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2018
---
# <a name="use-spark-structured-streaming-with-kafka-on-hdinsight"></a>HDInsight의 Kafka에서 Spark 구조적 스트리밍 사용

Azure HDInsight의 Apache Kafka에서 데이터를 읽는 Spark 구조적 스트림을 사용하는 방법을 알아봅니다.

Spark 구조적 스트림은 Spark SQL에서 작성된 스트림 처리 엔진입니다. 정적 데이터에 대한 일괄 처리 계산과 동일하게 스트리밍 계산을 표현할 수 있습니다. 구조적 스트림에 대한 자세한 내용은 Apache.org에서 [구조적 스트림 프로그래밍 가이드[알파]](http://spark.apache.org/docs/2.2.0/structured-streaming-programming-guide.html)를 참조하세요.

> [!IMPORTANT]
> 이 예제에서는 HDInsight 3.6에서 Spark 2.2를 사용합니다.
>
> 이 문서의 단계는 HDInsight의 Spark와 HDInsight의 Kafka 클러스터를 모두 포함하는 Azure 리소스 그룹을 만듭니다. 이러한 클러스터는 모두 Azure Virtual Network에 있으며, 여기서는 Spark 클러스터와 Kafka 클러스터 간에 직접 통신할 수 있습니다.
>
> 이 문서의 단계를 완료하는 경우 과도한 요금이 청구되지 않도록 클러스터를 삭제해야 합니다.

## <a name="create-the-clusters"></a>클러스터 만들기

HDInsight의 Apache Kafka는 공용 인터넷을 통한 액세스를 Kafka broker에 제공하지 않습니다. Kafka를 사용하는 경우 동일한 Azure 가상 네트워크에 있어야 합니다. 이 자습서에서는 Kafka 클러스터와 Spark 클러스터가 동일한 Azure 가상 네트워크에 있습니다. 

다음 다이어그램은 Spark와 Kafka 사이의 통신 흐름을 보여줍니다.

![Azure 가상 네트워크에 있는 Spark 및 Kafka 클러스터 다이어그램](./media/hdinsight-apache-spark-with-kafka/spark-kafka-vnet.png)

> [!NOTE]
> Kafka 서비스는 가상 네트워크 내에서 통신으로 제한됩니다. SSH 및 Ambari와 같은 클러스터의 다른 서비스는 인터넷을 통해 액세스할 수 있습니다. HDInsight에서 사용할 수 있는 공용 포트에 대한 자세한 내용은 [HDInsight에서 사용하는 포트 및 URI](hdinsight-hadoop-port-settings-for-services.md)를 참조하세요.

편의를 위해 다음 단계에서는 Azure Resource Manager 템플릿을 사용하여 가상 네트워크 내에 Kafka 및 Spark 클러스터를 만듭니다.

1. Azure에 로그인하고 Azure Portal에서 템플릿을 열려면 다음 단추를 사용합니다.
    
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fhdinsight-spark-kafka-structured-streaming%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apache-spark-with-kafka/deploy-to-azure.png" alt="Deploy to Azure"></a>
    
    Azure Resource Manager 템플릿의 위치는 **https://raw.githubusercontent.com/Azure-Samples/hdinsight-spark-kafka-structured-streaming/master/azuredeploy.json**입니다.

    이 템플릿은 다음과 같은 리소스를 만듭니다.

    * HDInsight 3.6 클러스터의 Kafka
    * HDInsight 3.6 클러스터의 Spark 2.2.0
    * HDInsight 클러스터를 포함하는 Azure Virtual Network

    > [!IMPORTANT]
    > 이 예에서 사용된 구조적 스트림 Notebook은 HDInsight 3.6의 Spark가 필요합니다. HDInsight에서 이전 버전의 Spark를 사용하면 Notebook을 사용하는 경우 발생하는 오류가 발생합니다.

2. 다음 정보를 사용하여 **사용자 지정된 템플릿** 섹션의 항목을 채웁니다.

    | 설정 | 값 |
    | --- | --- |
    | 구독 | Azure 구독 |
    | 리소스 그룹 | 리소스를 포함하는 리소스 그룹입니다. |
    | 위치 | 리소스가 만들어지는 Azure 지역입니다. |
    | Spark 클러스터 이름 | Spark 클러스터의 이름입니다. |
    | Kafka 클러스터 이름 | Kafka 클러스터의 이름입니다. |
    | 클러스터 로그인 사용자 이름 | 클러스터의 관리 사용자 이름입니다. |
    | 클러스터 로그인 암호 | 클러스터의 관리 사용자 암호입니다. |
    | SSH 사용자 이름 | 클러스터용으로 만들 SSH 사용자입니다. |
    | SSH 암호 | SSH 사용자에 대한 암호입니다. |
   
    ![사용자 지정된 템플릿의 스크린샷](./media/hdinsight-apache-kafka-spark-structured-streaming/spark-kafka-template.png)

4. 마지막으로 **대시보드에 고정**을 선택한 다음 **구매**를 선택합니다. 

> [!NOTE]
> 클러스터를 만드는 데 최대 20분이 걸릴 수 있습니다.

## <a name="get-the-notebook"></a>Notebook 가져오기

이 문서에 설명된 예제에 대한 코드는 [https://github.com/Azure-Samples/hdinsight-spark-kafka-structured-streaming](https://github.com/Azure-Samples/hdinsight-spark-kafka-structured-streaming)에서 지원됩니다.

## <a name="upload-the-notebooks"></a>Notebooks 업로드

프로젝트에서 HDInsight 클러스터의 Spark로 Notebooks을 업로드하려면 다음 단계를 사용합니다.

1. 웹 브라우저의 Spark 클러스터에 있는 Jupyter Notebook에 연결합니다. 다음 URL에서 `CLUSTERNAME`을 __Spark__ 클러스터의 이름으로 바꿉니다.

        https://CLUSTERNAME.azurehdinsight.net/jupyter

    메시지가 표시되면 클러스터를 만들 때 사용한 클러스터 로그인(관리자) 이름과 암호를 입력합니다.

2. 페이지의 오른쪽 위에서 __업로드__ 단추를 사용하여 __spark-structured-streaming-kafka.ipynb__ 파일을 클러스터에 업로드합니다. __열기__를 선택하여 업로드를 시작합니다.

    ![업로드 단추를 사용하여 Notebook을 선택하고 업로드](./media/hdinsight-apache-kafka-spark-structured-streaming/upload-button.png)

    ![KafkaStreaming.ipynb 파일 선택](./media/hdinsight-apache-kafka-spark-structured-streaming/select-notebook.png)

3. Notebook 목록에서 __spark-structured-streaming-kafka.ipynb__ 항목을 찾아 그 옆에 있는 __업로드__ 단추를 선택합니다.

    ![Notebook을 업로드하려면 KafkaStreaming.ipynb 항목의 업로드 단추를 사용합니다.](./media/hdinsight-apache-kafka-spark-structured-streaming/upload-notebook.png)


## <a name="use-the-notebook"></a>Notebook 사용

파일이 업로드되면 __spark-structured-streaming-kafka.ipynb__ 항목을 선택하여 Notebook을 엽니다. HDInsight의 Kafka에서 Spark 구조적 스트리밍을 사용하는 방법을 알아 보려면 Notebook의 지침을 따릅니다.

## <a name="clean-up-resources"></a>리소스 정리

이 자습서에서 만든 리소스를 정리하려면 리소스 그룹을 삭제합니다. 리소스 그룹을 삭제하면 연결된 HDInsight 클러스터 및 리소스 그룹에 연결된 다른 모든 리소스가 함께 삭제됩니다.

Azure Portal을 사용하여 리소스 그룹을 제거하려면:

1. Azure Portal에서 왼쪽의 메뉴를 확장하여 서비스 메뉴를 연 다음 __리소스 그룹__을 선택하여 리소스 그룹 목록을 표시합니다.
2. 삭제할 리소스 그룹을 찾은 다음 목록 오른쪽에 있는 __자세히__ 단추(...)를 마우스 오른쪽 단추로 클릭합니다.
3. __리소스 그룹 삭제__를 선택한 다음 확인합니다.

> [!WARNING]
> 클러스터가 만들어지면 HDInsight 클러스터 청구가 시작되고 클러스터가 삭제되면 중지됩니다. 분 단위로 청구되므로 더 이상 사용하지 않으면 항상 클러스터를 삭제해야 합니다.
> 
> HDInsight 클러스터의 Kafka를 삭제하면 Kafka에 저장된 데이터가 모두 삭제됩니다.

## <a name="next-steps"></a>다음 단계

이제 Spark 구조적 스트림을 사용하는 방법을 배웠으므로 Spark 및 Kafka에서 작업하는 방법에 대한 자세한 내용을 보려면 다음 문서를 참조하세요.

* [Kafka에서 Spark 스트림(DStream)을 사용하는 방법](hdinsight-apache-spark-with-kafka.md)
* [Jupyter Notebook 및 HDInsight의 Spark 시작](spark/apache-spark-jupyter-spark-sql.md)