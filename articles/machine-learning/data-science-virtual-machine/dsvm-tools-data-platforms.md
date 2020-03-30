---
title: 지원되는 데이터 플랫폼
titleSuffix: Azure Data Science Virtual Machine
description: Azure 데이터 과학 가상 머신에 대해 지원되는 데이터 플랫폼 및 도구에 대해 알아봅니다.
keywords: 데이터 과학 도구, 데이터 과학 가상 머신, 데이터 과학용 도구, linux 데이터 과학
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: cd787881957d78f179107e46b2650de4618c7724
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80282327"
---
# <a name="data-platforms-supported-on-the-data-science-virtual-machine"></a>Data Science Virtual Machine에서 지원되는 데이터 플랫폼

DSVM(데이터 과학 가상 머신)을 사용하면 광범위한 데이터 플랫폼에 대해 분석을 구축할 수 있습니다. 원격 데이터 플랫폼에 대 한 인터페이스를 외에도 DSVM 신속 하 게 개발 및 프로토타입 생성에 대 한 로컬 인스턴스를 제공합니다.

다음 데이터 플랫폼 도구는 DSVM에서 지원됩니다.

## <a name="sql-server-developer-edition"></a>SQL Server Developer 버전

| | |
| ------------- | ------------- |
| 무엇인가요?   | 로컬 관계형 데이터베이스 인스턴스      |
| 지원되는 DSVM 에디션      | 윈도우 2016: SQL 서버 2017, 윈도우 2019: SQL 서버 2019      |
| 일반적인 사용 용도      | 더 작은 데이터 세트를 사용하여 로컬에서 빠른 개발 <br/> In-database R 실행   |
| 샘플에 대한 링크      |    뉴욕시 데이터 집합의 작은 샘플이 SQL 데이터베이스에 로드됩니다.<br/>  `nyctaxi` <br/> Microsoft 기계 학습 서버 및 데이터베이스 내 분석을 보여주는 Jupyter 샘플은 다음 에서 찾을 수 있습니다.<br/> `~notebooks/SQL_R_Services_End_to_End_Tutorial.ipynb`  |
| DSVM의 관련 도구       | SQL Server Management Studio <br/> ODBC/JDBC 드라이버<br/> pyodbc, RODBC<br />Apache Drill      |

> [!NOTE]
> SQL Server 개발자 에디션은 개발 및 테스트 목적으로만 사용할 수 있습니다. 프로덕션에서 실행하려면 라이선스나 SQL Server VM 중 하나가 필요합니다.


### <a name="setup"></a>설치 프로그램

데이터베이스 서버가 이미 미리 구성되어 있으며 SQL Server와 `SQL Server (MSSQLSERVER)`관련된 Windows 서비스(예: )가 자동으로 실행되도록 설정됩니다. 유일한 수동 단계는 Microsoft 기계 학습 서버를 사용하여 데이터베이스 내 분석을 사용하도록 설정하는 것입니다. SQL Server 관리 스튜디오(SSMS)에서 다음 명령을 일회성 작업으로 실행하여 분석을 활성화할 수 있습니다. 컴퓨터 관리자로 로그인하고 SSMS에서 새 쿼리를 열고 선택한 데이터베이스가 `master`다음과 같은지 확인한 후 이 명령을 실행합니다.

        CREATE LOGIN [%COMPUTERNAME%\SQLRUserGroup] FROM WINDOWS 

        (Replace %COMPUTERNAME% with your VM name.)
       
SQL Server 관리 스튜디오를 실행하려면 프로그램 목록에서 "SQL Server 관리 스튜디오"를 검색하거나 Windows 검색을 사용하여 SQL Server 관리 스튜디오를 찾아 실행할 수 있습니다. 자격 증명을 입력하라는 메시지가 표시되면 Windows ```localhost``` **인증을** 선택하고 컴퓨터 이름 또는 SQL Server **이름** 필드에서 사용합니다.

### <a name="how-to-use-and-run-it"></a>그것을 사용하고 실행하는 방법

기본적으로 기본 데이터베이스 인스턴스가 있는 데이터베이스 서버가 자동으로 실행됩니다. Microsoft SQL Server 데이터베이스에 로컬로 액세스하려면 VM에서 SQL Server Management Studio와 같은 도구를 사용할 수 있습니다. 로컬 관리자 계정에는 데이터베이스에 대한 관리자 액세스 권한이 있습니다.

또한 DSVM에는 ODBC 및 JDBC 드라이버가 함께 제공되어 파이썬 및 기계 학습 서버를 비롯한 여러 언어로 작성된 응용 프로그램에서 SQL Server, Azure SQL 데이터베이스 및 Azure SQL 데이터 웨어하우스와 통신할 수 있습니다.

### <a name="how-is-it-configured-and-installed-on-the-dsvm"></a>DSVM에 어떻게 구성되고 설치되어 있습니까? 

 SQL Server는 표준 방식으로 설치됩니다. 그것은 `C:\Program Files\Microsoft SQL Server`에서 찾을 수 있습니다. 데이터베이스 내 기계 학습 서버 인스턴스는 에서 찾을 수 있습니다. `C:\Program Files\Microsoft SQL Server\MSSQL13.MSSQLSERVER\R_SERVICES` DSVM에는 에 설치된 별도의 독립 실행형 기계 학습 `C:\Program Files\Microsoft\R Server\R_SERVER`서버 인스턴스도 있습니다. 이 두 기계 학습 서버 인스턴스는 라이브러리를 공유하지 않습니다.


## <a name="apache-spark-2x-standalone"></a>Apache Spark 2.x (독립 실행형)

| | |
| ------------- | ------------- |
| 무엇인가요?   | 인기있는 아파치 스파크 플랫폼의 독립 실행형(단일 노드 내 프로세스) 인스턴스; 빠른 대규모 데이터 처리 및 기계 학습을 위한 시스템     |
| 지원되는 DSVM 에디션      | Linux     |
| 일반적인 사용 용도      | * Azure HDInsight와 같은 대규모 스파크 클러스터에 더 작은 데이터 집합및 이후 배포를 통해 스파크/PySpark 응용 프로그램을 로컬로 신속하게 개발<br/> * 테스트 마이크로 소프트 머신 러닝 서버 스파크 컨텍스트 <br />* ML 응용 프로그램을 구축하기 위해 스파크ML 또는 마이크로 소프트의 오픈 소스 [MMLSpark](https://github.com/Azure/mmlspark) 라이브러리를 사용하여 |
| 샘플에 대한 링크      |    Jupyter 샘플: <br />&nbsp;&nbsp;* ~/notebooks/SparkML/pySpark <br /> &nbsp;&nbsp;* ~/notebooks/MMLSpark <br /> 마이크로소프트 기계 학습 서버 (스파크 컨텍스트): /dsvm/샘플/MRS/MRSSparkContextSample.R |
| DSVM의 관련 도구       | PySpark, Scala<br/>Jupyter (Spark/PySpark 커널)<br/>마이크로소프트 기계 학습 서버, 스파클러, 스파클러 <br />Apache Drill      |

### <a name="how-to-use-it"></a>사용 방법
`spark-submit` 또는 `pyspark` 명령을 실행하여 명령줄에 Spark 작업을 제출할 수 있습니다. 또한 Spark 커널로 새 노트북을 만듦으로써 Jupyter 노트북을 만들 수 있습니다.

DSVM에서 사용할 수 있는 SparkR, Sparklyr 및 Microsoft 기계 학습 서버와 같은 라이브러리를 사용하여 R에서 스파크를 사용할 수 있습니다. 앞의 표에서 샘플에 대한 포인터를 참조하세요.

### <a name="setup"></a>설치 프로그램
우분투 리눅스 DSVM 에디션의 Microsoft 기계 학습 서버에서 스파크 컨텍스트에서 실행하기 전에 로컬 단일 노드 Hadoop HDFS 및 원사 인스턴스를 활성화하려면 일회성 설정 단계를 완료해야 합니다. 기본적으로 Hadoop 서비스는 설치되지만 DSVM에서 사용하지 않도록 설정됩니다. 이를 사용하려면 다음 명령을 루트로 처음 실행합니다.

    echo -e 'y\n' | ssh-keygen -t rsa -P '' -f ~hadoop/.ssh/id_rsa
    cat ~hadoop/.ssh/id_rsa.pub >> ~hadoop/.ssh/authorized_keys
    chmod 0600 ~hadoop/.ssh/authorized_keys
    chown hadoop:hadoop ~hadoop/.ssh/id_rsa
    chown hadoop:hadoop ~hadoop/.ssh/id_rsa.pub
    chown hadoop:hadoop ~hadoop/.ssh/authorized_keys
    systemctl start hadoop-namenode hadoop-datanode hadoop-yarn

을 실행하여 ```systemctl stop hadoop-namenode hadoop-datanode hadoop-yarn```더 이상 필요하지 않을 때 Hadoop 관련 서비스를 중지할 수 있습니다.

원격 스파크 컨텍스트(DSVM의 독립 실행형 스파크 인스턴스)에서 MRS를 개발하고 테스트하는 방법을 보여 주는 `/dsvm/samples/MRS` 샘플이 제공되어 디렉터리에서 사용할 수 있습니다.


### <a name="how-is-it-configured-and-installed-on-the-dsvm"></a>DSVM에 어떻게 구성되고 설치되어 있습니까? 
|플랫폼|설치 위치($SPARK_HOME)|
|:--------|:--------|
|Linux   | /dsvm/tools/spark-X.X.X-bin-hadoopX.X|


Microsoft MMLSpark 기계 학습 라이브러리를 사용하여 Azure Blob 저장소 또는 Azure Data Lake 저장소의 데이터에 액세스하는 라이브러리는 $SPARK_HOME/jars에 사전 설치되어 있습니다. 이러한 JAR은 Apache Spark가 시작될 때 자동으로 로드됩니다. 기본적으로 Spark는 로컬 디스크의 데이터를 사용합니다. 

DSVM의 스파크 인스턴스가 Blob 저장소 또는 Azure Data Lake 저장소에 저장된 데이터에 `core-site.xml` 액세스하려면 $SPARK_HOME/conf/core-site.xml.template에 있는 템플릿을 기반으로 파일을 만들고 구성해야 합니다. 또한 Blob 저장소 및 Azure Data Lake 저장소에 액세스하려면 적절한 자격 증명이 있어야 합니다. 템플릿 파일은 Blob 저장소 및 Azure Data Lake 저장소 구성에 자리 표시자를 사용합니다.

Azure Data Lake 저장소 서비스 자격 증명을 만드는 것에 대한 자세한 내용은 [Azure Data Lake Storage Gen1을 사용하여 인증을](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-authenticate-using-active-directory)참조하세요. Blob 저장소 또는 Azure Data Lake 저장소에 대한 자격 증명을 core-site.xml 파일에 입력한 후 wasb:// 또는 adl:// URI 접두사를 통해 해당 원본에 저장된 데이터를 참조할 수 있습니다.

