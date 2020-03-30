---
title: 아파치 하두프 샌드 박스, 에뮬레이터를 사용하는 방법을 배웁니다 - Azure HDInsight
description: 'Apache Hadoop 에코 시스템을 사용하는 방법에 대해 알아보려면 Azure 가상 머신에서 Hortonworks의 Hadoop 샌드박스를 설정할 수 있습니다. '
keywords: Hadoop 에뮬레이터, Hadoop 샌드박스
ms.reviewer: jasonh
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 05/29/2019
ms.author: hrasheed
ms.openlocfilehash: 47ee66393e3e1678576b12a70b767f35cb3bc635
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73044764"
---
# <a name="get-started-with-an-apache-hadoop-sandbox-an-emulator-on-a-virtual-machine"></a>가상 머신의 에뮬레이터인 아파치 하두롭 샌드박스를 시작하세요.

Hadoop 에코 시스템에 대해 알아보기 위해 가상 머신에서 Hortonworks의 Apache Hadoop 샌드박스를 설치하는 방법을 알아봅니다. 이 샌드박스는 Hadoop, HDFS(Hadoop Distributed File System) 및 작업 제출에 대해 알아보는 로컬 개발 환경을 제공합니다. Hadoop에 익숙해졌으면 HDInsight 클러스터를 만들어 Azure에서 Hadoop 사용을 시작할 수 있습니다. 시작 방법에 대한 자세한 내용은 [HDInsight에서 Hadoop 시작](apache-hadoop-linux-tutorial-get-started.md)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

* [오라클 버추얼 박스](https://www.virtualbox.org/). [여기](https://www.virtualbox.org/wiki/Downloads)에서 다운로드하여 설치합니다.

## <a name="download-and-install-the-virtual-machine"></a>가상 컴퓨터 다운로드 및 설치

1. [Cloudera 다운로드로 이동합니다.](https://www.cloudera.com/downloads/hortonworks-sandbox/hdp.html)

1. 설치 유형 **선택** 에서 **버추얼 박스를** 클릭하여 VM에서 최신 호튼 웍스 샌드 박스를 다운로드하십시오. 로그인하거나 제품 관심 양식을 작성합니다.

1. 버튼을 클릭하여 **HDP 샌드박스(최신)를** 클릭하여 다운로드를 시작합니다.

샌드박스 설정에 대한 지침은 [샌드박스 배포 및 설치 가이드를](https://hortonworks.com/tutorial/sandbox-deployment-and-install-guide/section/1/)참조하십시오.

이전 HDP 버전 샌드박스를 다운로드하려면 **이전 버전의**링크를 참조하십시오.

## <a name="start-the-virtual-machine"></a>가상 머신 시작합

1. Open Oracle VM VirtualBox를 엽니다.
1. **파일** 메뉴에서 **Import Appliance(어플라이언스 가져오기)** 를 클릭한 다음 Hortonworks Sandbox 이미지를 지정합니다.
1. Hortonworks Sandbox를 선택한 다음 **시작**, **일반 시작**을 차례로 선택합니다. 가상 머신이 부팅 프로세스를 완료하면 로그인 지침이 표시됩니다.

    ![버추얼 박스 관리자 일반 시작](./media/apache-hadoop-emulator-get-started/virtualbox-normal-start.png)

1. 웹 브라우저를 열고 표시된 URL(일반적으로)으로 `http://127.0.0.1:8888`이동합니다.

## <a name="set-sandbox-passwords"></a>샌드박스 암호 설정

1. Hortonworks Sandbox 페이지의 **시작** 단계에서 **고급 옵션 보기**를 선택합니다. 이 페이지의 정보를 사용하여 SSH를 통해 샌드박스에 로그인합니다. 제공된 이름 및 암호를 사용합니다.

   > [!NOTE]
   > SSH 클라이언트가 설치되어 있지 않은 경우 에서 **http://localhost:4200/** 가상 컴퓨터에서 제공하는 웹 기반 SSH를 사용할 수 있습니다.

    SSH를 통해 처음 연결하는 경우 루트 계정의 암호를 변경하라는 메시지가 표시됩니다. 새 암호를 입력합니다. 이 암호는 SSH를 사용하여 로그인할 때 사용됩니다.

2. 일단 로그인하면 다음 명령을 입력합니다.

        ambari-admin-password-reset

    대화 상자가 나타나면 Ambari 관리자 계정에 대한 암호를 입력합니다. Ambari 웹 UI에 액세스할 때 사용됩니다.

## <a name="use-hive-commands"></a>Hive 명령 사용

1. 샌드박스에 대한 SSH 연결에서 다음 명령을 사용하여 Hive 셸을 시작합니다.

        hive
2. 셸이 시작되면 다음을 사용하여 샌드박스와 함께 제공되는 테이블을 봅니다.

        show tables;
3. 다음을 사용하여 `sample_07` 테이블에서 10개의 행을 검색합니다

        select * from sample_07 limit 10;

## <a name="next-steps"></a>다음 단계

* [Hortonworks Sandbox와 Visual Studio를 사용하는 방법 알아보기](../hdinsight-hadoop-emulator-visual-studio.md)

* [Hortonworks Sandbox의 방법 알아보기](https://hortonworks.com/hadoop-tutorial/learning-the-ropes-of-the-hortonworks-sandbox/)

* [Hadoop 자습서 - HDP 시작](https://hortonworks.com/hadoop-tutorial/hello-world-an-introduction-to-hadoop-hcatalog-hive-and-pig/)
