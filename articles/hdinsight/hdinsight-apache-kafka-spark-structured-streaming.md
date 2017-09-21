---
title: "Kafka에서 Apache Spark 구조적 스트림 - Azure HDInsight | Microsoft Docs"
description: "Apache Spark 스트림(DStream)을 사용하여 Apache Kafka 간에 데이터를 이동하는 방법을 알아봅니다. 이 예제에서는 HDInsight의 Spark에서 Jupyter Notebook을 사용하여 데이터를 스트리밍합니다."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/06/2017
ms.author: larryfr
ms.translationtype: HT
ms.sourcegitcommit: 2c6cf0eff812b12ad852e1434e7adf42c5eb7422
ms.openlocfilehash: 565f840e0ac7ec1a68361ac1613da5d42459c979
ms.contentlocale: ko-kr
ms.lasthandoff: 09/13/2017

---
# <a name="use-spark-structured-streaming-with-kafka-preview-on-hdinsight"></a>HDInsight의 Kafka(미리 보기)에서 Spark 구조적 스트림 사용

Azure HDInsight의 Apache Kafka에서 데이터를 읽는 Spark 구조적 스트림을 사용하는 방법을 알아봅니다.

Spark 구조적 스트림은 Spark SQL에서 작성된 스트림 처리 엔진입니다. 정적 데이터에 대한 일괄 처리 계산과 동일하게 스트리밍 계산을 표현할 수 있습니다. 구조적 스트림에 대한 자세한 내용은 Apache.org에서 [구조적 스트림 프로그래밍 가이드[알파]](http://spark.apache.org/docs/2.1.0/structured-streaming-programming-guide.html)를 참조하세요.

> [!IMPORTANT]
> 이 예제에서는 HDInsight 3.6에서 Spark 2.1을 사용했습니다. 구조적 스트림은 Spark 2.1에서 __알파__로 간주합니다.
>
> 이 문서의 단계는 HDInsight의 Spark와 HDInsight의 Kafka 클러스터를 모두 포함하는 Azure 리소스 그룹을 만듭니다. 이러한 클러스터는 모두 Azure Virtual Network에 있으며, 여기서는 Spark 클러스터와 Kafka 클러스터 간에 직접 통신할 수 있습니다.
>
> 이 문서의 단계를 완료하는 경우 과도한 요금이 청구되지 않도록 클러스터를 삭제해야 합니다.

## <a name="create-the-clusters"></a>클러스터 만들기

HDInsight의 Apache Kafka는 공용 인터넷을 통한 액세스를 Kafka broker에 제공하지 않습니다. Kafka와 통신하는 대상은 Kafka 클러스터의 노드와 동일한 Azure 가상 네트워크에 있어야 합니다. 여기서는 Kafka 클러스터와 Spark 클러스터가 모두 Azure 가상 네트워크에 있습니다. 클러스터 간의 통신 흐름을 보여 주는 다이어그램은 다음과 같습니다.

![Azure 가상 네트워크에 있는 Spark 및 Kafka 클러스터 다이어그램](./media/hdinsight-apache-spark-with-kafka/spark-kafka-vnet.png)

> [!NOTE]
> Kafka 서비스는 가상 네트워크 내에서 통신으로 제한됩니다. SSH 및 Ambari와 같은 클러스터의 다른 서비스는 인터넷을 통해 액세스할 수 있습니다. HDInsight에서 사용할 수 있는 공용 포트에 대한 자세한 내용은 [HDInsight에서 사용하는 포트 및 URI](hdinsight-hadoop-port-settings-for-services.md)를 참조하세요.

Azure 가상 네트워크, Kafka 클러스터 및 Spark 클러스터를 수동으로 만들 수 있지만 Azure Resource Manager 템플릿을 사용하는 것이 더 쉽습니다. 다음 단계에 따라 Azure 가상 네트워크, Kafka 클러스터 및 Spark 클러스터를 Azure 구독에 배포합니다.

1. Azure에 로그인하고 Azure Portal에서 템플릿을 열려면 다음 단추를 사용합니다.
    
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-kafka-spark-cluster-in-vnet-v4.1.json" target="_blank"><img src="./media/hdinsight-apache-spark-with-kafka/deploy-to-azure.png" alt="Deploy to Azure"></a>
    
    Azure Resource Manager 템플릿은 **https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-spark-cluster-in-vnet-v4.1.json**에 있습니다.

    이 템플릿은 다음과 같은 리소스를 만듭니다.

    * HDInsight 3.5 클러스터의 Kafka
    * HDInsight 3.6 클러스터의 Spark
    * HDInsight 클러스터를 포함하는 Azure Virtual Network

    > [!IMPORTANT]
    > 이 예에서 사용된 구조적 스트림 Notebook은 HDInsight 3.6의 Spark가 필요합니다. HDInsight에서 이전 버전의 Spark를 사용하면 Notebook을 사용하는 경우 발생하는 오류가 발생합니다.

2. 다음 정보를 사용하여 **사용자 지정 배포** 섹션의 항목을 채웁니다.
   
    ![HDInsight 사용자 지정 배포](./media/hdinsight-apache-spark-with-kafka/parameters.png)
   
    * **리소스 그룹**: 그룹을 만들거나 기존 그룹을 선택합니다. 이 그룹에는 HDInsight 클러스터가 포함됩니다.

    * **위치**: 지리적으로 가까운 위치를 선택합니다.

    * **기본 클러스터 이름**: 이 값은 Spark 및 Kafka 클러스터의 기본 이름으로 사용됩니다. 예를 들어, **hdi**를 입력하면 spark-hdi__라는 Spark 클러스터와 **kafka-hdi**라는 Kafka 클러스터가 만들어집니다.

    * **클러스터 로그인 사용자 이름**: Spark 및 Kafka 클러스터의 관리자 이름입니다.

    * **클러스터 로그인 암호**: Spark 및 Kafka 클러스터의 관리자 사용자 암호입니다.

    * **SSH 사용자 이름**: Spark 및 Kafka 클러스터에 만들 SSH 사용자입니다.

    * **SSH 암호**: Spark 및 Kafka 클러스터에 대한 SSH 사용자의 암호입니다.

3. **사용 약관**을 읽은 다음 **위에 명시된 사용 약관에 동의함**을 선택합니다.

4. 마지막으로 **대시보드에 고정**을 선택한 다음 **구매**를 선택합니다. 클러스터를 만드는 데 약 20분이 걸립니다.

리소스를 만든 후에 요약 페이지가 표시됩니다.

![VNet 및 클러스터에 대한 리소스 그룹 정보](./media/hdinsight-apache-spark-with-kafka/groupblade.png)

> [!IMPORTANT]
> HDInsight 클러스터의 이름은 **spark-BASENAME** 및 **kafka-BASENAME**이며, 여기서 BASENAME은 템플릿에 제공된 이름입니다. 이후 단계에서 클러스터에 연결할 때 이러한 이름을 사용합니다.

## <a name="get-the-kafka-brokers"></a>Kafka broker를 가져옵니다.

이 예제의 코드는 Kafka 클러스터에 있는 Kafka broker 호스트에 연결합니다. 두 Kafka broker 호스트의 주소를 찾으려면 다음 PowerShell 또는 Bash 예제를 사용합니다.

```powershell
$creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"
$clusterName = Read-Host -Prompt "Enter the Kafka cluster name"
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/KAFKA/components/KAFKA_BROKER" `
    -Credential $creds
$respObj = ConvertFrom-Json $resp.Content
$brokerHosts = $respObj.host_components.HostRoles.host_name[0..1]
($brokerHosts -join ":9092,") + ":9092"
```

```bash
curl -u admin -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER" | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2
```

메시지가 표시되면 클러스터 로그인(관리자) 계정에 대한 암호를 입력합니다.

> [!NOTE]
> 이 예에서는 `$CLUSTERNAME`에 Kafka 클러스터의 이름을 포함하도록 합니다.
>
> 이 예제에서는 [jq](https://stedolan.github.io/jq/) 유틸리티를 사용하여 JSON 문서에서 데이터를 구문 분석합니다.

다음 텍스트와 유사하게 출력됩니다.

`wn0-kafka.0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net:9092,wn1-kafka.0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net:9092`

이 문서의 다음 단계에서 사용되기 때문에 이 정보를 저장합니다.

## <a name="get-the-notebooks"></a>Notebooks 가져오기

이 문서에서 설명한 예제의 코드는 [https://github.com/Azure-Samples/hdinsight-spark-kafka-structured-streaming](https://github.com/Azure-Samples/hdinsight-spark-kafka-structured-streaming)에서 제공됩니다.

## <a name="upload-the-notebooks"></a>Notebooks 업로드

프로젝트에서 HDInsight 클러스터의 Spark로 Notebooks을 업로드하려면 다음 단계를 사용합니다.

1. 웹 브라우저의 Spark 클러스터에 있는 Jupyter Notebook에 연결합니다. 다음 URL에서 `CLUSTERNAME`을 Kafka 클러스터의 이름으로 바꿉니다.

        https://CLUSTERNAME.azurehdinsight.net/jupyter

    메시지가 표시되면 클러스터를 만들 때 사용한 클러스터 로그인(관리자) 이름과 암호를 입력합니다.

2. 페이지의 오른쪽 위에서 __업로드__ 단추를 사용하여 __Stream-Tweets-To_Kafka.ipynb__ 파일을 클러스터에 업로드합니다. __열기__를 선택하여 업로드를 시작합니다.

    ![업로드 단추를 사용하여 Notebook을 선택하고 업로드](./media/hdinsight-apache-kafka-spark-structured-streaming/upload-button.png)

    ![KafkaStreaming.ipynb 파일 선택](./media/hdinsight-apache-kafka-spark-structured-streaming/select-notebook.png)

3. Notebook 목록에서 __Stream-Tweets-To_Kafka.ipynb__ 항목을 찾아 그 옆에 있는 __업로드__ 단추를 선택합니다.

    ![Notebook을 업로드하려면 KafkaStreaming.ipynb 항목의 업로드 단추를 사용합니다.](./media/hdinsight-apache-kafka-spark-structured-streaming/upload-notebook.png)

4. 1~3 단계를 반복하여 __Spark-Structured-Streaming-From-Kafka.ipynb__ Notebook을 로드합니다.

## <a name="load-tweets-into-kafka"></a>Kafka로 트윗 로드

파일이 업로드되면 __Stream-Tweets-To_Kafka.ipynb__ 항목을 선택하여 Notebook을 엽니다. Notebook의 단계를 따라 Kafka로 트윗을 로드합니다.

## <a name="process-tweets-using-spark-structured-streaming"></a>Spark 구조적 스트림을 사용하여 트윗 프로세스

Jupyter Notebook 홈페이지에서 __Spark-Structured-Streaming-From-Kafka.ipynb__ 항목을 선택합니다. Notebook의 단계를 따라 Spark 구조적 스트림을 사용하여 트윗을 로드합니다.

## <a name="next-steps"></a>다음 단계

이제 Spark 구조적 스트림을 사용하는 방법을 배웠으므로 Spark 및 Kafka에서 작업하는 방법에 대한 자세한 내용을 보려면 다음 문서를 참조하세요.

* [Kafka에서 Spark 스트림(DStream)을 사용하는 방법](hdinsight-apache-spark-with-kafka.md)
* [Jupyter Notebook 및 HDInsight의 Spark 시작](hdinsight-apache-spark-jupyter-spark-sql.md)
