<properties linkid="manage-services-hdinsight-howto-monitor-hdinsight" urlDisplayName="Monitor" pageTitle="Monitor HDInsight | Azure" metaKeywords="" description="Learn how to monitor an HDInsight cluster and view Hadoop job history through the Azure management portal." metaCanonical="" services="hdinsight" documentationCenter="" title="How to Monitor HDInsight" authors="jgao" solutions="" manager="paulettm" editor="mollybos" />

HDInsight를 모니터링하는 방법
=============================

이 항목에서는 HDInsight 클러스터를 모니터링하는 방법에 대해 알아봅니다.

목차
----

-   [방법: HDInsight 클러스터 모니터링](#monitorcluster)
-   [방법: Hadoop 작업 기록 보기](#jobhistory)

방법: HDInsight 클러스터 모니터링
---------------------------------

HDInsight 클러스터와 해당 클러스터에서 실행 중인 Hadoop 작업의 상태를 모니터링하려면 HDInsight 대시보드에 연결하고 클러스터 모니터링 타일을 클릭할 수 있습니다.

![HDI.TileMonitorCluster](./media/hdinsight-monitor/HDI.TileMonitorCluster.PNG)

모니터 페이지는 다음과 유사하게 표시됩니다.

![HDI.MonitorPage](./media/hdinsight-monitor/HDI.MonitorPage.PNG)

오른쪽에서 Namenode 및 작업 추적이 가동 중이며 4개의 데이터 노드가 정상 상태로 실행 중인 것을 보여 줍니다.

왼쪽에는 지난 30분간의 Map Reduce 메트릭이 표시됩니다. 모니터링 기간을 30분, 1시간, 3시간, 12시간, 1일, 2일, 1주 및 2주로 변경할 수 있습니다.

방법: Hadoop 작업 기록 보기
---------------------------

Hadoop 작업 기록을 보려면 HDInsight 대시보드에 연결한 후 작업 기록 타일을 클릭합니다.

![HDI.TileJobHistory](./media/hdinsight-monitor/HDI.TileJobHistory.PNG)

이 타일은 실행된 작업의 수를 표시합니다. 예를 들어 위의 이미지는 6개의 작업에 대한 작업 기록을 사용할 수 있음을 나타냅니다. 작업 기록 페이지는 다음과 유사하게 표시됩니다.

![HDI.JobHistoryPage](./media/hdinsight-monitor/HDI.JobHistoryPage.PNG)

참고 항목
---------

-   [방법: 관리자 HDInsight](/en-us/manage/services/hdinsight/howto-administer-hdinsight/)
-   [방법: 프로그래밍 방식으로 HDInsight 클러스터 배포](/en-us/manage/services/hdinsight/howto-deploy-cluster/)
-   [방법: 프로그래밍 방식으로 HDInsight 클러스터에서 원격 작업 실행](/en-us/manage/services/hdinsight/howto-execute-jobs-programmatically/)
-   [자습서: Azure HDInsight 시작](/en-us/manage/services/hdinsight/get-started-hdinsight/)

