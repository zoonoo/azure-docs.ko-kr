---
title: 아파치 피닉스와 Azure HDInsight에서 아파치 베이스 쿼리를 실행
description: 아파치 제플린을 사용하여 피닉스에서 아파치 베이스 쿼리를 실행하는 방법에 대해 알아봅니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/14/2019
ms.openlocfilehash: 28eeb446e55213f1ffa0a638878f6432fd15a05a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "72392242"
---
# <a name="use-apache-zeppelin-to-run-apache-phoenix-queries-over-apache-hbase-in-azure-hdinsight"></a>아파치 제플린을 사용 하 여 아파치 피닉스 쿼리를 실행 하는 아파치 HBase Azure HDInsight에서

Apache Phoenix는 HBase에서 구축되는 오픈 소스 대규모 병렬 관계형 데이터베이스 계층입니다. 피닉스는 HBase를 통해 쿼리와 같은 SQL을 사용할 수 있습니다. Phoenix는 아래 JDBC 드라이버를 사용하여 SQL 테이블, 인덱스, 뷰 및 시퀀스를 생성, 삭제, 변경할 수 있습니다.  Phoenix를 사용하여 행을 개별적으로 일괄적으로 업데이트할 수도 있습니다. Phoenix는 MapReduce를 사용하여 쿼리를 컴파일하는 대신 NOSQL 네이티브 컴파일을 사용하여 HBase 위에 대기 시간이 짧은 응용 프로그램을 만들 수 있습니다.

아파치 제플린은 대화형 데이터 분석과 SQL 및 Scala와 같은 언어를 사용하여 데이터 기반의 협업 문서를 만들 수 있는 오픈 소스 웹 기반 노트북입니다. 데이터 개발자가 데이터 & 데이터 과학자가 데이터 조작을 위한 코드를 개발, 구성, 실행 및 공유할 수 있도록 지원합니다. 명령줄을 참조하거나 클러스터 세부 정보를 필요로 하지 않고 결과를 시각화할 수 있습니다.

HDInsight 사용자는 아파치 제플린을 사용하여 피닉스 테이블을 쿼리할 수 있습니다. 아파치 제플린은 HDInsight 클러스터와 통합되어 있으며 이를 사용할 추가 단계가 없습니다. JDBC 인터프리터로 제플린 노트북을 만들고 피닉스 SQL 쿼리 작성을 시작하기만 하면 됩니다.

## <a name="prerequisites"></a>사전 요구 사항

HDInsight의 아파치 HBase 클러스터. [아파치 HBase로 시작하기 를](./apache-hbase-tutorial-get-started-linux.md)참조하십시오.

## <a name="create-an-apache-zeppelin-note"></a>Apache Zeppelin 노트 만들기

1. `CLUSTERNAME`을 다음 URL `https://CLUSTERNAME.azurehdinsight.net/zeppelin`에서 클러스터의 이름으로 바꿉니다. 그런 다음, 웹 브라우저에 URL을 입력합니다. 클러스터 로그인 사용자 이름 및 암호를 입력합니다.

1. Zeppelin 페이지에서 새 **메모 만들기를**선택합니다.

    ![HDInsight 대화형 쿼리 zeppelin](./media/apache-hbase-phoenix-zeppelin/hbase-zeppelin-create-note.png)

1. **새 메모 만들기** 대화 상자에서 다음 값을 입력하거나 선택합니다.

    - 참고 이름: 메모의 이름을 입력합니다.
    - 기본 인터프리터: 드롭다운 목록에서 **jdbc를** 선택합니다.

    그런 다음 **메모 만들기를**선택합니다.

1. 노트북 헤더에 연결된 상태가 표시되는지 확인합니다. 오른쪽 상단에 녹색 점이 표시됩니다.

    ![Zeppelin 노트북 상태](./media/apache-hbase-phoenix-zeppelin/hbase-zeppelin-connected.png "Zeppelin 노트북 상태")

1. HBase 테이블을 만듭니다. 다음 명령을 입력한 다음 Shift + Enter키프트를 **누릅니다.**

    ```sql
    %jdbc(phoenix)
    CREATE TABLE Company (
        company_id INTEGER PRIMARY KEY,
        name VARCHAR(225)
    );
    ```

    첫 번째 줄의 **%jdbc(피닉스)** 문은 노트북에 피닉스 JDBC 인터프리터를 사용하도록 지시합니다.

1. 생성된 테이블을 봅니다.

    ```sql
    %jdbc(phoenix)
    SELECT DISTINCT table_name
    FROM SYSTEM.CATALOG
    WHERE table_schem is null or table_schem <> 'SYSTEM';
    ```

1. 테이블에 값을 삽입합니다.

    ```sql
    %jdbc(phoenix)
    UPSERT INTO dbo.Company VALUES(1, 'Microsoft');
    UPSERT INTO dbo.Company (name, company_id) VALUES('Apache', 2);
    ```

1. 테이블을 쿼리합니다.

    ```sql
    %jdbc(phoenix)
    SELECT * FROM dbo.Company;
    ```

1. 레코드를 삭제합니다.

    ```sql
    %jdbc(phoenix)
    DELETE FROM dbo.Company WHERE COMPANY_ID=1;
    ```

1. 테이블을 삭제합니다.

    ```sql
    %jdbc(phoenix)
    DROP TABLE dbo.Company;
    ```

## <a name="next-steps"></a>다음 단계

- [아파치 피닉스는 이제 Azure HDInsight에서 제플린을 지원합니다](https://blogs.msdn.microsoft.com/ashish/2018/08/17/apache-phoenix-now-supports-zeppelin-in-azure-hdinsight/)
- [아파치 피닉스 문법](https://phoenix.apache.org/language/index.html)
