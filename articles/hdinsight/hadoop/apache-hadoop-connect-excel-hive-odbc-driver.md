---
title: Hive ODBC 드라이버로 Apache Hadoop에 Excel 연결 - Azure HDInsight
description: Excel용 Microsoft Hive ODBC 드라이버를 설정하고 Microsoft Excel의 HDInsight 클러스터에서 데이터를 쿼리하는 데 사용하는 방법에 대해 알아봅니다.
keywords: hadoop excel, hive excel, hive odbc
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 05/28/2019
ms.author: hrasheed
ms.openlocfilehash: fcb9171d2285efab0f65e6ab424908bc42c0ea2f
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66391884"
---
# <a name="connect-excel-to-apache-hadoop-in-azure-hdinsight-with-the-microsoft-hive-odbc-driver"></a>Microsoft Hive ODBC 드라이버로 Azure HDInsight의 Apache Hadoop에 Excel 연결

[!INCLUDE [ODBC-JDBC-selector](../../../includes/hdinsight-selector-odbc-jdbc.md)]

Microsoft의 빅 데이터 솔루션은 Microsoft BI(비즈니스 인텔리전스) 구성 요소를 Azure HDInsight에 배포한 Apache Hadoop 클러스터와 통합합니다. 이 통합의 예로 Microsoft Hive ODBC(Open Database Connectivity) 드라이버를 사용하여 HDInsight Hadoop 클러스터의 Hive 데이터 웨어하우스에 Excel을 연결하는 기능을 들 수 있습니다.

Excel에서 Microsoft Excel용 파워 쿼리 추가 기능을 사용하여 HDInsight 클러스터 및 기타 데이터 원본(예: 기타(비 HDInsight) Hadoop 클러스터)과 연결된 데이터를 연결할 수도 있습니다. 설치 하 고 파워 쿼리를 사용 하 여 대 한 자세한 내용은 [파워 쿼리로 HDInsight에 Excel 연결](../hdinsight-connect-excel-power-query.md)합니다.

## <a name="prerequisites"></a>필수 조건

이 문서를 시작하기 전에 다음 항목이 있어야 합니다.

* HDInsight Hadoop 클러스터. 만들려면 [Azure HDInsight 시작](apache-hadoop-linux-tutorial-get-started.md)을 참조하세요.
* Office 2010 Professional Plus 이상 또는 Excel 2010 이상을 포함한 워크스테이션.

## <a name="install-microsoft-hive-odbc-driver"></a>Microsoft Hive ODBC 드라이버 설치
다운로드 및 설치 합니다 [Microsoft Hive ODBC 드라이버](https://go.microsoft.com/fwlink/?LinkID=286698) ODBC 드라이버가 사용할 응용 프로그램의 버전과 일치 하는 버전입니다.  이 자습서에서는 Office Excel에서 드라이버를 사용합니다.

## <a name="create-apache-hive-odbc-data-source"></a>Apache Hive ODBC 데이터 원본 만들기
다음 단계에 따라 Hive ODBC 데이터 원본을 만들 수 있습니다.

1. Windows에서 시작 > Windows 관리 도구 > ODBC 데이터 원본(32비트)/(64비트)으로 이동합니다.  그러면 **ODBC 데이터 원본 관리자** 창이 열립니다.

    ![OBDC 데이터 원본 관리자](./media/apache-hadoop-connect-excel-hive-odbc-driver/HDI.SimbaHiveOdbc.DataSourceAdmin1.png "ODBC 데이터 원본 관리자를 사용하여 DSN 구성")

1. **사용자 DSN** 탭에서 **추가**를 선택하여 **새 데이터 원본 만들기** 창을 엽니다.

1. **Microsoft Hive ODBC 드라이버**를 선택한 다음, **마침**을 선택하여 **Microsoft Hive ODBC 드라이버 DSN 설정** 창을 엽니다.

1. 다음 값을 입력하거나 선택합니다.

   | 자산 | 설명 |
   | --- | --- |
   |  데이터 원본 이름 |데이터 원본에 이름 지정 |
   |  호스트 |[https://slack.botframework.com](`HDInsightClusterName.azurehdinsight.net`) 을 입력합니다. 예를 들어 `myHDICluster.azurehdinsight.net` |
   |  Port |**443**을 사용합니다. (이 포트는 563에서 443으로 변경됨) |
   |  데이터베이스 |**기본값**을 사용합니다. |
   |  메커니즘 |선택 **Windows Azure HDInsight 서비스** |
   |  사용자 이름 |HDInsight 클러스터 HTTP 사용자의 사용자 이름을 입력합니다. 기본 사용자 이름은 **admin**입니다. |
   |  암호 |HDInsight 클러스터 사용자 암호 입력 확인란을 선택 **암호 저장 (암호화)** 합니다.|

1. 선택 사항: **고급 옵션...** 을 선택합니다.  

   | 매개 변수 | 설명 |
   | --- | --- |
   |  Use Native Query |선택하면 ODBC 드라이버가 TSQL을 HiveQL로 변환하지 않습니다. 순수 HiveQL 문을 제출하는 것이 확실한 경우에만 이 옵션을 사용합니다. SQL Server 또는 Azure SQL Database에 연결하는 경우에는 이 옵션을 선택 취소한 상태로 둬야 합니다. |
   |  Rows fetched per block |많은 수의 레코드를 가져오는 경우 최적의 성능을 위해 이 매개 변수를 조정해야 할 수 있습니다. |
   |  Default string column length, Binary column length, Decimal column scale |데이터 형식 길이 및 정밀도는 데이터가 반환되는 방식에 영향을 줄 수 있습니다. 정밀도 손실 및/또는 잘림으로 인해 잘못된 정보가 반환될 수 있습니다. |

    ![고급 옵션](./media/apache-hadoop-connect-excel-hive-odbc-driver/HDI.HiveOdbc.DataSource.AdvancedOptions1.png "고급 DSN 구성 옵션")

1. **테스트**를 선택하여 데이터 원본을 테스트합니다. 데이터 원본이 올바르게 구성된 경우 테스트 결과가 **성공!** 으로 표시됩니다.  

1. **확인**을 선택하여 테스트 창을 닫습니다.  

1. **확인**을 선택하여 **Microsoft Hive ODBC Driver DSN 설정** 창을 닫습니다.  

1. **확인**을 선택하여 **ODBC 데이터 원본 관리자** 창을 닫습니다.  

## <a name="import-data-into-excel-from-hdinsight"></a>HDInsight에서 Excel로 데이터 가져오기

다음 단계는 이전 섹션에서 만든 ODBC 데이터 원본을 사용하여 Hive 테이블에서 Excel 통합 문서로 데이터를 가져오는 방법을 설명합니다.

1. Excel에서 새 통합 문서나 기존 통합 문서를 엽니다.

2. **데이터** 탭에서 **데이터 가져오기** > **기타 원본에서** > **ODBC에서**로 이동하여 **ODBC에서** 창을 시작합니다.

    ![데이터 연결 마법사 열기](./media/apache-hadoop-connect-excel-hive-odbc-driver/HDI.SimbaHiveOdbc.Excel.DataConnection1.png "데이터 연결 마법사 열기")

3. 드롭다운 목록에서 마지막 섹션에서 만든 데이터 원본 이름을 선택 하 고 선택한 **확인**합니다.

4. 첫 번째 사용,는 **ODBC 드라이버** 대화 상자가 열립니다. 선택 **Windows** 왼쪽된 메뉴에서. 선택한 **Connect** 열려는 합니다 **탐색기** 창입니다.

5. **탐색기**에서 **HIVE** > **기본값** > **hivesampletable**로 이동한 다음, **로드**를 선택합니다. 데이터를 Excel로 가져올 때까지 잠시 기다립니다.

    ![HDInsight Hive ODBC 탐색기](./media/apache-hadoop-connect-excel-hive-odbc-driver/hdinsight.hive.odbc.navigator.png "데이터 연결 열기 마법사")

## <a name="next-steps"></a>다음 단계

이 문서에서는 Microsoft Hive ODBC 드라이버를 사용하여 HDInsight Service에서 Excel로 데이터를 가져오는 방법을 알아보았습니다. 마찬가지로 HDInsight Service에서 SQL Database로 데이터를 가져올 수 있습니다. 데이터를 HDInsight Service에 업로드할 수도 있습니다. 자세한 내용은 다음을 참조하세요.

* [Azure HDInsight에서 Microsoft Power BI를 사용하여 Apache Hive 데이터 시각화](apache-hadoop-connect-hive-power-bi.md)
* [Azure HDInsight에서 Power BI를 사용하여 대화형 쿼리 Hive 데이터 시각화](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md)
* [Azure HDInsight에서 Apache Zeppelin을 사용하여 Apache Hive 쿼리 실행](../interactive-query/hdinsight-connect-hive-zeppelin.md)
* [파워 쿼리를 사용하여 Apache Hadoop에 Excel 연결](apache-hadoop-connect-excel-power-query.md)
* [Data Lake Tools for Visual Studio를 사용하여 Azure HDInsight에 연결 및 Apache Hive 쿼리 실행](apache-hadoop-visual-studio-tools-get-started.md)
* [Azure HDInsight Tool for Visual Studio Code 사용](../hdinsight-for-vscode.md)
* [HDInsight에 데이터 업로드](./../hdinsight-upload-data.md)