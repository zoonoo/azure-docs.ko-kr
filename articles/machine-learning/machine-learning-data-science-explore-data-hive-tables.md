---
title: "Hive 쿼리를 사용하여 Hive 테이블의 데이터 탐색 | Microsoft Docs"
description: "Hive 쿼리를 사용하여 Hive 테이블의 데이터를 탐색합니다."
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 0d46cea5-2b4c-4384-9bfa-fa20f6f75148
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/24/2017
ms.author: bradsev
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: 7795e6fd5cc175e3a0cc91e35a5b1900fbeb2fdc
ms.lasthandoff: 03/29/2017


---
# <a name="explore-data-in-hive-tables-with-hive-queries"></a>Hive 쿼리를 사용하여 Hive 테이블의 데이터 탐색
이 문서는 HDInsight Hadoop 클러스터의 Hive 테이블에서 데이터를 탐색하는 데 사용된 샘플 Hive 스크립트를 제공합니다.

다음 **메뉴** 는 다양한 저장소 환경에서 데이터를 탐색하기 위해 도구를 사용하는 방법을 설명하는 토픽에 연결되는 링크입니다.

[!INCLUDE [cap-explore-data-selector](../../includes/cap-explore-data-selector.md)]

## <a name="prerequisites"></a>필수 조건
이 문서에서는 사용자가 다음 작업을 수행한 것으로 가정합니다.

* Azure 저장소 계정을 만들었습니다. 지침이 필요한 경우 [Azure Storage 계정 만들기](../storage/storage-create-storage-account.md#create-a-storage-account)를 참조하세요.
* 사용자 지정된 Hadoop 클러스터에 HDInsight 서비스를 프로비전했습니다. 지침이 필요한 경우 [고급 분석을 위한 Azure HDInsight Hadoop 클러스터 사용자 지정](machine-learning-data-science-customize-hadoop-cluster.md)을 참조하세요.
* Azure HDInsight Hadoop 클러스터의 Hive 테이블에 데이터가 업로드되었습니다. 업로드되지 않은 경우 [데이터를 만들어서 Hive 테이블에 로드](machine-learning-data-science-move-hive-tables.md) 의 지침에 따라 먼저 Hive 테이블에 데이터를 업로드하세요.
* 클러스터에 대한 원격 액세스가 설정되었습니다. 지침이 필요한 경우 [Hadoop 클러스터의 헤드 노드에 액세스](machine-learning-data-science-customize-hadoop-cluster.md#headnode)를 참조하세요.
* Hive 쿼리를 제출하는 방법에 대한 지침이 필요한 경우 [Hive 쿼리를 제출하는 방법](machine-learning-data-science-move-hive-tables.md#submit)

## <a name="example-hive-query-scripts-for-data-exploration"></a>데이터 탐색에 대한 예제 Hive 쿼리 스크립트
1. 파티션당 관찰 수 가져오기  `SELECT <partitionfieldname>, count(*) from <databasename>.<tablename> group by <partitionfieldname>;`
2. 일별 관찰 수 가져오기  `SELECT to_date(<date_columnname>), count(*) from <databasename>.<tablename> group by to_date(<date_columnname>);`
3. 범주 열의 수준 가져오기   
    `SELECT  distinct <column_name> from <databasename>.<tablename>`
4. 두 범주 열 조합의 수준 수 가져오기  `SELECT <column_a>, <column_b>, count(*) from <databasename>.<tablename> group by <column_a>, <column_b>`
5. 숫자 열의 분포 가져오기   
    `SELECT <column_name>, count(*) from <databasename>.<tablename> group by <column_name>`
6. 두 조인 테이블의 레코드 추출
   
        SELECT
            a.<common_columnname1> as <new_name1>,
            a.<common_columnname2> as <new_name2>,
            a.<a_column_name1> as <new_name3>,
            a.<a_column_name2> as <new_name4>,
            b.<b_column_name1> as <new_name5>,
            b.<b_column_name2> as <new_name6>
        FROM
            (
            SELECT <common_columnname1>,
                <common_columnname2>,
                <a_column_name1>,
                <a_column_name2>,
            FROM <databasename>.<tablename1>
            ) a
            join
            (
            SELECT <common_columnname1>,
                <common_columnname2>,
                <b_column_name1>,
                <b_column_name2>,
            FROM <databasename>.<tablename2>
            ) b
            ON a.<common_columnname1>=b.<common_columnname1> and a.<common_columnname2>=b.<common_columnname2>

## <a name="additional-query-scripts-for-taxi-trip-data-scenarios"></a>택시 여정 데이터 시나리오에 대한 추가 쿼리 스크립트
또한 [NYC Taxi Trip Data](http://chriswhong.com/open-data/foil_nyc_taxi/) 시나리오에 대한 쿼리 예제가 [GitHub 리포지토리](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts)에 제공됩니다. 이러한 쿼리는 이미 데이터 스키마가 지정되어 있으며 바로 제출하여 실행할 수 있습니다.


