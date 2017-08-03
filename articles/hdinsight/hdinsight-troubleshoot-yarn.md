---
title: "YARN 문제 해결 - Azure HDInsight | Microsoft Docs"
description: "Azure HDInsight 플랫폼의 Yarn에 대한 일반적인 질문과 대답을 보려면 Yarn FAQ를 사용하세요."
keywords: "Azure HDInsight, Yarn, FAQ, 문제 해결 가이드, 일반적인 질문"
services: Azure HDInsight
documentationcenter: na
author: arijitt
manager: 
editor: 
ms.assetid: F76786A9-99AB-4B85-9B15-CA03528FC4CD
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 7/7/2017
ms.author: arijitt
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 576d6bfe9697d2abd6f84824960dad62e9ed3c11
ms.contentlocale: ko-kr
ms.lasthandoff: 07/21/2017

---

# <a name="yarn-troubleshooting"></a>YARN 문제 해결

이 문서에서는 Apache Ambari에서 YARN 페이로드를 사용할 때의 주요 문제 및 해결 방법을 설명합니다.

## <a name="how-do-i-create-a-new-yarn-queue-on-a-cluster"></a>클러스터에서 새 Yarn 큐를 만드는 방법

### <a name="issue"></a>문제:

HDInsight 클러스터에 용량 할당으로 새 Yarn 큐를 만듭니다.  

### <a name="resolution-steps"></a>해결 단계: 

Ambari를 통해 다음 단계를 사용하여 새 Yarn 큐를 만들고 모든 큐에 용량이 균형 있게 할당되도록 조정합니다. 

이 예제에서는 새 큐(Spark)가 50% 용량을 가질 수 있도록 두 개의 기존 큐(default 및 thriftsvr)의 용량을 50%에서 25% 용량으로 모두 변경합니다.
| 큐 | 용량 | 최대 용량 |
| --- | --- | --- | --- |
| 기본값 | 25% | 50% |
| thrftsvr | 25% | 50% |
| spark | 50% | 50% |
1. 눈금 패턴인 Ambari Views(Ambari 보기) 아이콘을 클릭하고 **Yarn Queue Manager**(Yarn 큐 관리자)를 선택합니다.

    ![Ambari Views(Ambari 보기) 아이콘 클릭](media/hdinsight-troubleshoot-yarn/create-queue-1.png)
1. **default** 큐를 선택합니다.

    ![default 큐 선택](media/hdinsight-troubleshoot-yarn/create-queue-2.png)
1. **default** 큐의 **Capacity**(용량)를 50%에서 25%로 변경하고 **thriftsvr** 큐의 용량도 25%로 변경합니다.

    ![default 및 thriftsvr 큐의 Capcity(용량)를 25%로 변경](media/hdinsight-troubleshoot-yarn/create-queue-3.png)
1. **Add Queue**(큐 추가)를 클릭하여 새 큐를 만듭니다.

    ![Add Queue(큐 추가) 클릭](media/hdinsight-troubleshoot-yarn/create-queue-4.png)
1. 새 큐 이름을 지정합니다.

    ![큐 이름을 Spark로 지정](media/hdinsight-troubleshoot-yarn/create-queue-5.png)  
1. **Capacity**(용량) 값을 50%로 두고 **Actions**(작업) 단추를 클릭합니다.

    ![Actions(작업) 단추 클릭](media/hdinsight-troubleshoot-yarn/create-queue-6.png)  
1. **Save and Refresh Queues**(큐 저장 및 새로 고침)를 선택합니다.

    ![Save and Refresh Queues(큐 저장 및 새로 고침) 선택](media/hdinsight-troubleshoot-yarn/create-queue-7.png)  

이러한 변경 내용은 Yarn Scheduler UI에 즉시 표시됩니다.

### <a name="further-reading"></a>추가 참고 자료:

- [Yarn Capacity Scheduler](https://hadoop.apache.org/docs/r2.7.2/hadoop-yarn/hadoop-yarn-site/CapacityScheduler.html)(Yarn 용량 스케줄러)


## <a name="how-do-i-download-yarn-logs-from-a-cluster"></a>클러스터에서 Yarn 로그를 다운로드하는 방법

#### <a name="issue"></a>문제:

HDInsight 클러스터에서 Yarn 응용 프로그램 마스터 및 다른 컨테이너 로그를 다운로드해야 합니다.  

#### <a name="resolution-steps"></a>해결 단계: 

1. SSH(Secure Shell) 클라이언트를 사용하여 HDInsight 클러스터에 연결합니다(아래 추가 참고 자료 섹션 확인).
1. 다음 명령을 사용하여 현재 Yarn 응용 프로그램을 실행 중인 모든 응용 프로그램 ID를 나열합니다.

```apache
yarn top
```
ID는 로그를 다운로드할 `APPLICATIONID` 열에 나열됩니다.

```apache
YARN top - 18:00:07, up 19d, 0:14, 0 active users, queue(s): root
NodeManager(s): 4 total, 4 active, 0 unhealthy, 0 decommissioned, 0 lost, 0 rebooted
Queue(s) Applications: 2 running, 10 submitted, 0 pending, 8 completed, 0 killed, 0 failed
Queue(s) Mem(GB): 97 available, 3 allocated, 0 pending, 0 reserved
Queue(s) VCores: 58 available, 2 allocated, 0 pending, 0 reserved
Queue(s) Containers: 2 allocated, 0 pending, 0 reserved

                  APPLICATIONID USER             TYPE      QUEUE   #CONT  #RCONT  VCORES RVCORES     MEM    RMEM  VCORESECS    MEMSECS %PROGR       TIME NAME
 application_1490377567345_0007 hive            spark  thriftsvr       1       0       1       0      1G      0G    1628407    2442611  10.00   18:20:20 Thrift JDBC/ODBC Server
 application_1490377567345_0006 hive            spark  thriftsvr       1       0       1       0      1G      0G    1628430    2442645  10.00   18:20:20 Thrift JDBC/ODBC Server
```

다음 명령을 사용하여 모든 응용 프로그램 마스터에 대한 Yarn 컨테이너 로그를 다운로드합니다.
   
```apache
yarn logs -applicationIdn logs -applicationId <application_id> -am ALL > amlogs.txt
```

`amlogs.txt`라는 텍스트 형식의 로그 파일이 만들어집니다. 

다음 명령을 사용하여 최신 응용 프로그램 마스터에 대한 Yarn 컨테이너 로그만 다운로드합니다.

```apache
yarn logs -applicationIdn logs -applicationId <application_id> -am -1 > latestamlogs.txt
```

`latestamlogs.txt`라는 텍스트 형식의 로그 파일이 만들어집니다. 

다음 명령을 사용하여 처음 두 개의 응용 프로그램 마스터에 대한 YARN 컨테이너 로그를 다운로드합니다.

```apache
yarn logs -applicationIdn logs -applicationId <application_id> -am 1,2 > first2amlogs.txt 
```

`first2amlogs.txt`라는 텍스트 형식의 로그 파일이 만들어집니다. 

다음 명령을 사용하여 모든 Yarn 컨테이너 로그를 다운로드합니다.

```apache
yarn logs -applicationIdn logs -applicationId <application_id> > logs.txt
```

`logs.txt`라는 텍스트 형식의 로그 파일이 만들어집니다. 

다음 명령을 사용하여 특정 컨테이너에 대한 yarn 컨테이너 로그를 다운로드합니다.

```apache
yarn logs -applicationIdn logs -applicationId <application_id> -containerId <container_id> > containerlogs.txt 
```

`containerlogs.txt`라는 텍스트 형식의 로그 파일이 만들어집니다.

#### <a name="further-readings"></a>추가 참고 자료:

- [SSH를 사용하여 HDInsight 클러스터에 연결](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix)

- [Apache Hadoop Yarn concepts and applications](https://hortonworks.com/blog/apache-hadoop-yarn-concepts-and-applications/)(Apache Hadoop Yarn 개념 및 응용 프로그램)









