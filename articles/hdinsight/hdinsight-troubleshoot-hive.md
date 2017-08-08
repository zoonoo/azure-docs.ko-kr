---
title: "Hive 문제 해결 - Azure HDInsight | Microsoft Docs"
description: "Azure HDInsight 플랫폼의 Hive에 대한 일반적인 질문과 대답을 보려면 Hive FAQ를 사용하세요."
keywords: "Azure HDInsight, Hive, FAQ, 문제 해결 가이드, 일반적인 질문"
services: Azure HDInsight
documentationcenter: na
author: dharmeshkakadia
manager: 
editor: 
ms.assetid: 15B8D0F3-F2D3-4746-BDCB-C72944AA9252
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 7/7/2017
ms.author: dharmeshkakadia
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 67b096c0585f7b73a57b784683944052d1cfaa33
ms.contentlocale: ko-kr
ms.lasthandoff: 07/21/2017

---

# <a name="hive-troubleshooting"></a>Hive 문제 해결

이 문서에서는 Apache Ambari에서 Hive 페이로드를 사용할 때의 주요 문제 및 해결 방법을 설명합니다.

## <a name="how-do-i-export-a-hive-metastore-and-import-it-on-another-cluster"></a>Hive metastore를 내보내고 다른 클러스터에서 가져오는 방법

### <a name="issue"></a>문제:

Hive metastore를 내보내고 다른 HDInsight 클러스터에서 가져와야 합니다.  

### <a name="resolution-steps"></a>해결 단계: 

1. SSH(Secure Shell) 클라이언트를 사용하여 HDInsight 클러스터에 연결합니다(아래 추가 참고 자료 섹션 확인).
1. metastore를 내보내려는 HDInsight 클러스터에서 다음 명령을 실행합니다.

```apache
for d in `hive -e "show databases"`; do echo "create database $d; use $d;" >> alltables.sql ; for t in `hive --database $d -e "show tables"` ; do ddl=`hive --database $d -e "show create table $t"`; echo "$ddl ;" >> alltables.sql ; echo "$ddl" | grep -q "PARTITIONED\s*BY" && echo "MSCK REPAIR TABLE $t ;" >> alltables.sql ; done; done
```

`allatables.sql`이라는 파일이 생성됩니다.

- `alltables.sql` 파일을 새 HDInsight 클러스터에 복사하고 다음 명령을 실행합니다.

```apache
hive -f alltables.sql
```

이 코드에서는 새 클러스터의 데이터 경로가 이전 클러스터와 같다고 가정합니다. 그렇지 않으면, 생성된  
`alltables.sql` 파일을 수동으로 편집하여 변경 사항을 반영하세요.

### <a name="further-reading"></a>추가 참고 자료:

- [SSH를 사용하여 HDInsight 클러스터에 연결](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix)


## <a name="how-do-locate-hive-logs-on-a-cluster"></a>클러스터에서 Hive 로그를 찾는 방법

### <a name="issue"></a>문제:

HDInsight 클러스터에서 Hive 클라이언트, metastore 및 hiveserver를 찾아야 합니다.  

### <a name="resolution-steps"></a>해결 단계: 

- SSH(Secure Shell) 클라이언트를 사용하여 HDInsight 클러스터에 연결합니다(아래 추가 참고 자료 섹션 확인).
- Hive 클라이언트 로그는 다음 위치에서 찾을 수 있습니다.

```apache
/tmp/<username>/hive.log 
```

- Hive metastore 로그는 다음 위치에서 찾을 수 있습니다.

```apache
/var/log/hive/hivemetastore.log 
```

- Hiveserver 로그는 다음 위치에서 찾을 수 있습니다.

```apache
/var/log/hive/hiveserver2.log 
```

### <a name="further-reading"></a>추가 참고 자료:

- [SSH를 사용하여 HDInsight 클러스터에 연결](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix)

## <a name="how-do-i-launch-hive-shell-with-specific-configurations-on-a-cluster"></a>클러스터에서 특정 구성으로 Hive 셸을 시작하는 방법

### <a name="issue"></a>문제:

HDInsight 클러스터에서 시작 시 Hive 셸 구성을 재정의하거나 지정해야 합니다.  

### <a name="resolution-steps"></a>해결 단계: 

- Hive 셸을 시작하는 동안 구성 키-값 쌍을 지정합니다(아래 추가 참고 자료 섹션 확인).

```apache
hive -hiveconf a=b 
```

- 다음 명령을 사용하여 Hive 셸에서 모든 유효 구성을 나열합니다.

```apache
hive> set;
```

예를 들어 다음 명령을 사용하여 콘솔에서 디버그 로깅이 사용하도록 설정된 상태로 Hive 셸을 시작합니다.
             
```apache
hive -hiveconf hive.root.logger=ALL,console 
```

### <a name="further-reading"></a>추가 참고 자료:

- [Hive configuration properties](https://cwiki.apache.org/confluence/display/Hive/Configuration+Properties)(Hive 구성 속성)


## <a name="how-do-i-analyze-tez-dag-data-on-a-cluster-critical-path"></a>클러스터 중요 경로에서 Tez DAG 데이터를 분석하는 방법

### <a name="issue"></a>문제:

HDInsight 클러스터의 특히 중요 경로에서 Tez DAG(방향성 비순환 그래프) 정보를 분석해야 합니다.

### <a name="resolution-steps"></a>해결 단계:
 
- SSH(Secure Shell) 클라이언트를 사용하여 HDInsight 클러스터에 연결합니다(아래 추가 참고 자료 섹션 확인).

- 명령 프롬프트에서 다음 명령을 실행합니다.
   
```apache
hadoop jar /usr/hdp/current/tez-client/tez-job-analyzer-*.jar CriticalPath --saveResults --dagId <DagId> --eventFileName <DagData.zip> 
```

- 다음 명령을 사용하여 Tez DAG를 분석하는 데 사용할 수 있는 다른 분석기를 나열합니다.

```apache
hadoop jar /usr/hdp/current/tez-client/tez-job-analyzer-*.jar
```

예제 프로그램을 첫 번째 인수로 지정해야 합니다.

유효한 프로그램 이름: ContainerReuseAnalyzer: DAG CriticalPath의 컨테이너 다시 사용 세부 정보 인쇄: DAG LocalityAnalyzer의 중요 경로 찾기: DAG ShuffleTimeAnalyzer의 지역 세부 정보 인쇄: DAG SkewAnalyzer의 순서 섞기 시간 세부 정보 인쇄: DAG SlowNodeAnalyzer의 기울이기 세부 정보 분석: DAG SlowTaskIdentifier의 노드 세부 정보 인쇄: DAG SlowestVertexAnalyzer의 느린 작업 세부 정보 인쇄: DAG SpillAnalyzer의 가장 느린 꼭짓점 세부 정보 인쇄: DAG TaskConcurrencyAnalyzer의 분산 세부 정보 인쇄: DAG VertexLevelCriticalPathAnalyzer의 작업 동시성 세부 정보 인쇄: DAG의 꼭짓점 수준에서 중요 경로 찾기


### <a name="further-reading"></a>추가 참고 자료:

- [SSH를 사용하여 HDInsight 클러스터에 연결](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix)



## <a name="how-do-i-download-tez-dag-data-from-a-cluster"></a>클러스터에서 Tez DAG 데이터를 다운로드하는 방법

#### <a name="issue"></a>문제:

HDInsight 클러스터에서 Tez DAG(방향성 비순환 그래프) 정보를 다운로드해야 합니다.

#### <a name="resolution-steps"></a>해결 단계:

Tez DAG 데이터를 수집하는 방법은 두 가지가 있습니다.

- 명령줄에서
 
    SSH(Secure Shell) 클라이언트를 사용하여 HDInsight 클러스터에 연결합니다. 명령 프롬프트에서 다음 명령을 실행합니다.
   
```apache
hadoop jar /usr/hdp/current/tez-client/tez-history-parser-*.jar org.apache.tez.history.ATSImportTool -downloadDir . -dagId <DagId> 
```

- 또는 Ambari Tez 보기를 사용할 수 있습니다.
   
Ambari로 이동 --> Tez view(Tez 보기)로 이동(오른쪽 위 모서리의 타일 아이콘 아래에 숨겨져 있음) --> 관심 있는 DAG 클릭 --> Download data(데이터 다운로드) 클릭.

#### <a name="further-reading"></a>추가 참고 자료:

1) [SSH를 사용하여 HDInsight 클러스터에 연결](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix)








