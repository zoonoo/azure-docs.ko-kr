---
title: "HDInsight에서 대화형 Hive 사용 | Microsoft Docs"
description: "HDInsight에서 대화형 Hive(LLAP에서 Hive)를 사용 하는 방법에 대해 알아봅니다."
keywords: 
services: hdinsight
documentationcenter: 
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 0957643c-4936-48a3-84a3-5dc83db4ab1a
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/06/2017
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 6971e123b388eab689e02a5c67809509c2ad29c6


---
# <a name="use-interactive-hive-in-hdinsight-preview"></a>HDInsight에서 대화형 Hive 사용(미리 보기)
대화형 Hive(즉, [Live Long and Process](https://cwiki.apache.org/confluence/display/Hive/LLAP))는 새로운 HDInsight [클러스터 유형](hdinsight-hadoop-provision-linux-clusters.md#cluster-types)입니다.  대화형 Hive에서는 메모리 내 캐싱이 가능하여 Hive 쿼리의 대화형 방식을 강화하고 빠르게 만들 수 있습니다. 이 새로운 기능은 HDInsight를 세계에서 가장 효율적이고 유연하며, 메모리 내 캐시(Hive 및 Spark 사용) 및 R 서비스와 긴밀히 통합된 고급 분석을 갖춘 클라우드 기반의 개방된 빅 데이터 솔루션으로 만드는 요소 중 하나입니다. 

대화형 Hive 클러스터는 Hadoop 클러스터와는 다릅니다. Hive 서비스만 포함합니다. 

> [!NOTE]
> MapReduce, Pig, Sqoop, Oozie 및 기타 서비스는 이 클러스터 유형에서 곧 제거될 예정입니다.
> 대화형 Hive 클러스터의 Hive 서비스는 Ambari Hive View, Beeline 및 Hive ODBC를 통해서만 액세스할 수 있습니다. Hive 콘솔, Templeton, Azure CLI 및 Azure PowerShell을 통해서는 액세스할 수 없습니다. 
> 
> 

## <a name="create-an-interactive-hive-cluster"></a>대화형 Hive 클러스터 만들기
대화형 Hive 클러스터는 Linux 기반 클러스터에서만 지원됩니다. HDInsight 클러스터를 만드는 방법에 대한 자세한 내용은 [HDInsight에서 Linux 기반 Hadoop 클러스터 만들기](hdinsight-hadoop-provision-linux-clusters.md)를 참조하세요.

## <a name="execute-hive-from-interactive-hive"></a>대화형 Hive에서 Hive 실행
Hive 쿼리를 실행하는 방식에 대한 다양한 옵션이 있습니다.

* Ambari Hive View를 사용하여 Hive 실행
  
    Hive View 사용에 대한 자세한 내용은 [HDInsight에서 Hadoop과 Hive View 사용](hdinsight-hadoop-use-hive-ambari-view.md)을 참조하세요.
* Beeline을 사용하여 Hive 실행
  
    HDInsight에서 Beeline 사용에 대한 자세한 내용은 [Beeline을 사용하여 HDInsight에서 Hadoop과 Hive 사용](hdinsight-hadoop-use-hive-beeline.md)을 참조하세요.
  
    헤드 노드 또는 빈 에지 노드에서 Beeline을 사용합니다.  빈 에지 노드에서 Beeline을 사용하는 것이 좋습니다.  빈 에지 노드로 HDInsight 클러스터 만들기에 대한 자세한 내용은 [HDInsight에서 빈 에지 노드 사용](hdinsight-apps-use-edge-node.md)을 참조하세요.
* Hive ODBC를 사용하여 Hive 실행
  
    Hive ODBC 사용에 대한 자세한 내용은 [Microsoft Hive ODBC 드라이버로 Hadoop에 Excel 연결](hdinsight-connect-excel-hive-odbc-driver.md)을 참조하세요.

**JDBC 연결 문자열을 찾으려면:**

1. 다음 URL을 사용하여 Ambari에 로그인합니다. https://<ClusterName>.AzureHDInsight.net.
2. 왼쪽 메뉴에서 **Hive** 를 클릭합니다.
3. 강조 표시된 아이콘을 클릭하여 URL을 복사합니다.
   
   ![HDInsight Hadoop 대화형 Hive LLAP JDBC](./media/hdinsight-hadoop-use-interactive-hive/hdinsight-hadoop-use-interactive-hive-jdbc.png)

## <a name="see-also"></a>참고 항목
* [HDInsight에서 Linux 기반 Hadoop 클러스터 만들기](hdinsight-hadoop-provision-linux-clusters.md): HDInsight에서 대화형 Hive 클러스터를 만드는 방법을 알아봅니다.
* [Beeline를 사용하여 HDInsight에서 Hadoop과 Hive 사용](hdinsight-hadoop-use-hive-beeline.md): Beeline을 사용하여 Hive 쿼리를 제출하는 방법을 알아봅니다.
* [Microsoft Hive ODBC 드라이버로 Hadoop에 Excel 연결](hdinsight-connect-excel-hive-odbc-driver.md): Excel을 Hive에 연결하는 방법을 알아봅니다.




<!--HONumber=Nov16_HO3-->


