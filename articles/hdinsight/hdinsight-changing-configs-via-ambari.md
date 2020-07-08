---
title: Azure HDInsight에서 Apache Ambari를 사용 하 여 클러스터 최적화
description: Apache Ambari 웹 UI를 사용 하 여 Azure HDInsight 클러스터를 구성 하 고 최적화 합니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,seoapr2020
ms.date: 05/04/2020
ms.openlocfilehash: a819c3a57efdc0ae87cf969fd7471818c51895f6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "82793162"
---
# <a name="optimize-clusters-with-apache-ambari-in-azure-hdinsight"></a>Azure HDInsight에서 Apache Ambari를 사용 하 여 클러스터 최적화

HDInsight는 대규모 데이터 처리 애플리케이션을 위한 Apache Hadoop 클러스터를 제공합니다. 이러한 복잡 한 다중 노드 클러스터를 관리, 모니터링 및 최적화 하는 것은 어려울 수 있습니다. Apache Ambari는 HDInsight Linux 클러스터를 관리 하 고 모니터링 하는 웹 인터페이스입니다.

Ambari Web UI 사용에 대한 소개는 [Apache Ambari Web UI를 사용하여 HDInsight 클러스터 관리](hdinsight-hadoop-manage-ambari.md)를 참조하세요.

`https://CLUSTERNAME.azurehdidnsight.net`에서 클러스터 자격 증명을 사용하여 Ambari에 로그인합니다. 초기 화면에 개요 대시보드가 표시됩니다.

![Apache Ambari 사용자 대시보드가 표시 됨](./media/hdinsight-changing-configs-via-ambari/apache-ambari-dashboard.png)

Ambari 웹 UI는 호스트, 서비스, 경고, 구성 및 보기를 관리 하는 데 사용 됩니다. Ambari를 사용 하 여 HDInsight 클러스터를 만들거나 서비스를 업그레이드할 수 없습니다. 또한 스택 및 버전을 관리 하거나, 호스트를 서비스 해제 또는 다시 작성 하거나, 클러스터에 서비스를 추가할 수 없습니다.

## <a name="manage-your-clusters-configuration"></a>클러스터 구성 관리

구성 설정은 특정 서비스를 조정하는 데 도움이 됩니다. 서비스의 구성 설정을 수정 하려면 왼쪽의 **서비스** 사이드바에서 서비스를 선택 합니다. 그런 다음 서비스 세부 정보 페이지에서 **Configs** 탭으로 이동 합니다.

![Apache Ambari Services 사이드바](./media/hdinsight-changing-configs-via-ambari/ambari-services-sidebar.png)

## <a name="modify-namenode-java-heap-size"></a>NameNode Java 힙 크기 수정

NameNode Java 힙 크기는 클러스터의 부하와 같은 여러 요인에 따라 달라 집니다. 또한 파일 수 및 블록 수입니다. 기본 크기인 1GB는 대부분의 클러스터에서 잘 작동하지만, 일부 워크로드에는 메모리가 더 많이 또는 적게 필요할 수 있습니다.

NameNode Java 힙 크기를 수정하려면:

1. 서비스 사이드바에서 **HDFS**를 선택하고 **Configs**(구성) 탭으로 이동합니다.

    ![Apache Ambari HDFS 구성](./media/hdinsight-changing-configs-via-ambari/ambari-apache-hdfs-config.png)

1. **NameNode Java heap size**(NameNode Java 힙 크기) 설정을 찾습니다. **필터** 텍스트 상자에 특정 설정을 입력하여 찾을 수도 있습니다. 설정 이름 옆에 있는 **펜** 아이콘을 선택합니다.

    ![Apache Ambari NameNode Java 힙 크기](./media/hdinsight-changing-configs-via-ambari/ambari-java-heap-size.png)

1. 텍스트 상자에 새 값을 입력한 다음 **Enter** 키를 눌러 변경 내용을 저장합니다.

    ![Ambari Edit NameNode Java heap 고 있습니다](./media/hdinsight-changing-configs-via-ambari/java-heap-size-edit1.png)

1. NameNode Java 힙 크기는 2gb에서 1gb로 변경 됩니다.

    ![편집 NameNode Java heap size2](./media/hdinsight-changing-configs-via-ambari/java-heap-size-edited.png)

1. 구성 화면 위쪽에서 **저장** 단추를 클릭하여 변경 내용을 저장합니다.

    ![' Apache Ambari 구성 저장 '](./media/hdinsight-changing-configs-via-ambari/ambari-save-changes1.png)

## <a name="next-steps"></a>다음 단계

* [Apache Ambari Web UI로 HDInsight 클러스터 관리](hdinsight-hadoop-manage-ambari.md)
* [Apache Ambari REST API](hdinsight-hadoop-manage-ambari-rest-api.md)
* [Apache HBase 최적화](./optimize-hbase-ambari.md)
* [Apache Hive 최적화](./optimize-hive-ambari.md)
* [Apache Pig 최적화](./optimize-pig-ambari.md)
