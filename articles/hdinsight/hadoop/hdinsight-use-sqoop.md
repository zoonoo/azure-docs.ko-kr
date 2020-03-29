---
title: Azure HDInsight(Apache Hadoop)를 사용하여 Apache Sqoop 작업 실행
description: 워크스테이션에서 Azure PowerShell을 사용하여 Hadoop 클러스터와 Azure SQL 데이터베이스 간에 Sqoop 가져오기 및 내보내기를 실행하는 방법을 알아봅니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/06/2019
ms.openlocfilehash: 8353c0fba034022a79570d09b320b7b5c4c3e60a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74951856"
---
# <a name="use-apache-sqoop-with-hadoop-in-hdinsight"></a>HDInsight에서 Hadoop과 Apache Sqoop 사용

[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

HDInsight에서 아파치 Sqoop을 사용하여 HDInsight 클러스터와 Azure SQL 데이터베이스 간에 데이터를 가져오고 내보내는 방법을 알아봅니다.

Apache Hadoop은 로그 및 파일과 같은 구조화되지 않은 반구조화 데이터를 처리하기 위한 자연스러운 선택이지만 관계형 데이터베이스에 저장된 구조화 된 데이터를 처리해야 할 수도 있습니다.

[Apache Sqoop](https://sqoop.apache.org/docs/1.99.7/user.html)은 Hadoop 클러스터와 관계형 데이터베이스 간 데이터 전송을 위해 설계된 도구입니다. 이 도구를 사용하면 SQL Server, MySQL, Oracle 등의 RDBMS(관계형 데이터베이스 관리 시스템)에서 HDFS(Hadoop 분산 파일 시스템)로 데이터를 가져오고, MapReduce 또는 Apache Hive로 Hadoop의 데이터를 변환한 후 데이터를 RDBMS로 다시 내보낼 수 있습니다. 이 문서에서는 관계형 데이터베이스에 SQL Server 데이터베이스를 사용하고 있습니다.

> [!IMPORTANT]  
> 이 문서에서는 데이터 전송을 수행할 테스트 환경을 설정합니다. 그런 다음 아래 섹션 [실행 Sqoop 작업의](#run-sqoop-jobs)방법 중 하나에서 이 환경에 대한 데이터 전송 방법을 선택합니다.

HDInsight 클러스터에서 지원되는 Sqoop 버전의 경우 [HDInsight에서 제공하는 클러스터 버전의 새로운 내용을 참조하세요.](../hdinsight-component-versioning.md)

## <a name="understand-the-scenario"></a>시나리오 이해

HDInsight 클러스터는 일부 샘플 데이터와 함께 제공됩니다. 다음 두 샘플을 사용합니다.

* 에 있는 아파치 Log4j 로그 `/example/data/sample.log`파일입니다. 이 파일에서 다음 로그가 추출됩니다.

```text
2012-02-03 18:35:34 SampleClass6 [INFO] everything normal for id 577725851
2012-02-03 18:35:34 SampleClass4 [FATAL] system problem at id 1991281254
2012-02-03 18:35:34 SampleClass3 [DEBUG] detail for id 1304807656
...
```

* 에 있는 `/hive/warehouse/hivesampletable`데이터 `hivesampletable`파일을 참조하는 Hive 테이블은 이 테이블에는 일부 모바일 디바이스 데이터가 포함되어 있습니다.
  
  | 필드 | 데이터 형식 |
  | --- | --- |
  | clientid |문자열 |
  | querytime |문자열 |
  | market |문자열 |
  | deviceplatform |문자열 |
  | devicemake |문자열 |
  | devicemodel |문자열 |
  | state |문자열 |
  | country |문자열 |
  | querydwelltime |double |
  | sessionid |bigint |
  | sessionpagevieworder |bigint |

이 문서에서는 이 두 데이터 집합을 사용하여 Sqoop 가져오기 및 내보내기를 테스트합니다.

## <a name="set-up-test-environment"></a><a name="create-cluster-and-sql-database"></a>테스트 환경 설정

클러스터, SQL 데이터베이스 및 기타 개체는 Azure 리소스 관리자 템플릿을 사용하여 Azure 포털을 통해 만들어집니다. 템플릿은 [Azure 빠른 시작 템플릿에서](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-with-sql-database/)찾을 수 있습니다. 리소스 관리자 템플릿은 bacpac 패키지를 호출하여 테이블 스키마를 SQL 데이터베이스에 배포합니다.  백업 패키지는 공용 Blob 컨테이너, https://hditutorialdata.blob.core.windows.net/usesqoop/SqoopTutorial-2016-2-23-11-2.bacpac에 있습니다. Bacpac 파일에 대한 프라이빗 컨테이너를 사용하려는 경우 템플릿에 다음 값을 사용합니다.

```json
"storageKeyType": "Primary",
"storageKey": "<TheAzureStorageAccountKey>",
```

> [!NOTE]  
> 템플릿 또는 Azure Portal을 사용하여 가져오기는 Azure Blob Storage에서 BACPAC 파일을 가져오는 것만 지원합니다.

1. Azure 포털에서 리소스 관리자 템플릿을 열려면 다음 이미지를 선택합니다.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-with-sql-database%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-use-sqoop/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

2. 다음과 같은 속성을 입력합니다.

    |필드 |값 |
    |---|---|
    |Subscription |드롭다운 목록에서 Azure 구독을 선택합니다.|
    |Resource group |드롭다운 목록에서 리소스 그룹을 선택하거나 리소스 그룹을 새로 만듭니다.|
    |위치 |드롭다운 목록에서 지역을 선택합니다.|
    |클러스터 이름 |Hadoop 클러스터에 사용할 이름을 입력합니다. 소문자만 사용하십시오.|
    |클러스터 로그인 사용자 이름 |미리 채워진 값을 `admin`유지합니다.|
    |클러스터 로그인 암호 |암호를 입력합니다.|
    |Ssh 사용자 이름 |미리 채워진 값을 `sshuser`유지합니다.|
    |Ssh 암호 |암호를 입력합니다.|
    |Sql 관리자 로그인 |미리 채워진 값을 `sqluser`유지합니다.|
    |Sql 관리자 암호 |암호를 입력합니다.|
    |_artifacts 위치 | 다른 위치에서 자신의 bacpac 파일을 사용하려는 경우가 아니면 기본값을 사용합니다.|
    |_artifacts 위치 Sas 토큰 |비워 둡니다.|
    |바팍 파일 이름 |고유한 bacpac 파일을 사용하지 않으려면 기본값을 사용합니다.|
    |위치 |기본값을 사용합니다.|

    Azure SQL Server 이름은 `<ClusterName>dbserver`. 데이터베이스 이름은 입니다. `<ClusterName>db` 기본 저장소 계정 이름은 `e6qhezrh2pdqu`입니다.

3. **위에 명시된 사용 약관에 동의함**을 선택합니다.

4. **구매**를 선택합니다. 템플릿 배포에 배포 제출 중이라는 제목의 새 타일이 표시됩니다. 클러스터 및 SQL 데이터베이스를 만들려면 20분 정도가 걸립니다.

## <a name="run-sqoop-jobs"></a>Sqoop 작업 실행

HDInsight는 다양한 메서드를 사용하여 Sqoop 작업을 실행할 수 있습니다. 어떤 메서드가 적합한지 결정하는 다음 테이블을 사용하여 연습할 수 있는 링크를 따르세요.

| **이것을 사용** 하세요... | ... **대화형** 셸 | ... **배치** 처리 | ... **클라이언트 운영 체제** |
|:--- |:---:|:---:|:--- |:--- |
| [SSH](apache-hadoop-use-sqoop-mac-linux.md) |? |? |Linux, Unix, Mac OS X, 또는 Windows |
| [Hadoop용 .NET SDK](apache-hadoop-use-sqoop-dotnet-sdk.md) |&nbsp; |?  |Windows(당분간) |
| [Azure 파워쉘](apache-hadoop-use-sqoop-powershell.md) |&nbsp; |? |Windows |

## <a name="limitations"></a>제한 사항

* 대량 내보내기 - Linux 기반 HDInsight를 사용하면 데이터를 Microsoft SQL Server 또는 Azure SQL Database로 내보내는 데 사용되는 Sqoop 커넥터는 현재 대량 삽입을 지원하지 않습니다.
* Batch - Linux 기반 HDInsight에서 삽입을 수행할 때 `-batch` 스위치를 사용하는 경우 Sqoop는 삽입 작업을 일괄 처리하는 대신 여러 번의 삽입 작업을 수행합니다.

## <a name="next-steps"></a>다음 단계

이제 Sqoop을 사용하는 방법을 배웠습니다. 자세한 내용은 다음을 참조하세요.

* [HDInsight에서 Apache Hive 사용](../hdinsight-use-hive.md)
* [HDInsight에 데이터 업로드](../hdinsight-upload-data.md): HDInsight/Azure Blob Storage에 데이터를 업로드하는 다른 방법을 찾습니다.
* [Apache Sqoop을 사용하여 HDInsight의 Hadoop과 SQL Database 간에 데이터 가져오기 및 내보내기](./apache-hadoop-use-sqoop-mac-linux.md)