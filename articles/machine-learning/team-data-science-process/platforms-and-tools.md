---
title: 데이터 과학 프로젝트용 플랫폼 및 도구-팀 데이터 과학 프로세스
description: 팀 데이터 과학 프로세스를 표준화 하는 기업에서 사용할 수 있는 데이터 및 분석 리소스를 항목별로 요약 하 고 설명 합니다.
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 09/04/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 97dafe292ff3ed0ef5fa46eb895136e3b32e62c5
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74978512"
---
# <a name="platforms-and-tools-for-data-science-projects"></a>데이터 과학 프로젝트용 플랫폼 및 도구

Microsoft는 클라우드 또는 온-프레미스 플랫폼 모두에 대 한 전체 데이터 및 분석 서비스와 리소스를 제공 합니다. 데이터 과학 프로젝트의 실행을 효율적이 고 확장 가능 하 게 하기 위해 배포할 수 있습니다. 추적 가능, 버전 제어 및 공동 작업 방식으로 데이터 과학 프로젝트를 구현 하는 팀에 대 한 지침은 tdsp ( [팀 데이터 과학 프로세스](overview.md) )에서 제공 합니다.  이 프로세스를 표준화 하는 데이터 과학 팀에서 처리 하는 인력 역할 및 관련 작업에 대 한 개요는 [팀 데이터 과학 프로세스 역할 및 작업](roles-tasks.md)을 참조 하세요.

TDSP를 사용 하는 데이터 과학 팀에서 사용할 수 있는 데이터 및 분석 서비스는 다음과 같습니다.

- 데이터 과학 Virtual Machines (Windows 및 Linux CentOS 모두)
- HDInsight Spark 클러스터
- SQL Data Warehouse
- Azure Data Lake
- HDInsight Hive 클러스터
- Azure File Storage
- SQL Server 2016 R Services

이 문서에서는 리소스를 간략하게 설명 하 고 TDSP 팀이 게시 한 자습서 및 연습에 대 한 링크를 제공 합니다. 이를 단계별로 사용 하는 방법을 알아보고이를 사용 하 여 지능형 응용 프로그램을 빌드하는 방법을 배울 수 있습니다. 이러한 리소스에 대 한 자세한 내용은 해당 제품 페이지에서 확인할 수 있습니다. 

## <a name="data-science-virtual-machine-dsvm"></a>Data Science Virtual Machine (DSVM)

Microsoft의 Windows 및 Linux에서 제공 하는 데이터 과학 가상 머신은 데이터 과학 모델링 및 개발 활동에 널리 사용 되는 도구를 포함 합니다. 여기에는 다음과 같은 도구가 포함 됩니다.

- Microsoft R Server Developer Edition 
- Anaconda Python-disztribúció
- Python 및 R 용 jupyter 노트북 
- Visual Studio Community Edition 및 Windows의 Python 및 R Tools 및 Linux의 Eclipse
- Windows 용 데스크톱 Power BI
- Linux의 Windows/Postgres SQL Server 2016 Developer Edition

또한 xgboost, mxnet 및 Vowpal Wabbit와 같은 **ML 및 AI 도구** 를 포함 합니다.

현재 DSVM은 **Windows** 및 **Linux CentOS** 운영 체제에서 사용할 수 있습니다. 실행 하려는 데이터 과학 프로젝트의 요구에 따라 DSVM의 크기 (CPU 코어 수와 메모리 양)를 선택 합니다. 

DSVM의 Windows 버전에 대 한 자세한 내용은 Azure marketplace의 [Microsoft Data Science Virtual Machine](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-windows) 를 참조 하세요. DSVM의 Linux 버전은 [linux Data Science Virtual Machine](https://azure.microsoft.com/marketplace/partners/microsoft-ads/linux-data-science-vm/)를 참조 하세요.

DSVM에서 몇 가지 일반적인 데이터 과학 작업을 효율적으로 실행 하는 방법을 알아보려면 [데이터 과학 Virtual Machine에서 수행할 수 있는 10 가지](../data-science-virtual-machine/vm-do-ten-things.md) 작업을 참조 하세요.


## <a name="azure-hdinsight-spark-clusters"></a>Azure HDInsight Spark 클러스터

Apache Spark는 메모리 내 처리를 지원 하 여 빅 데이터 분석 응용 프로그램의 성능을 향상 시키는 오픈 소스 병렬 처리 프레임 워크입니다. Spark 처리 엔진은 속도, 사용 편의성 및 정교한 분석을 위해 작성 되었습니다. Spark의 메모리 내 계산 기능을 사용 하면 기계 학습 및 그래프 계산의 반복 알고리즘에 적합 합니다. Spark는 Azure Blob storage (WASB)와도 호환 되므로 Azure에 저장 된 기존 데이터를 Spark를 사용 하 여 쉽게 처리할 수 있습니다.

Amikor Spark-fürtöt hoz létre a HDInsightban, olyan Azure számítási erőforrásokat is létrehoz, amelyeken a Spark telepítve és konfigurálva van. HDInsight에서 Spark 클러스터를 만드는 데 약 10 분이 걸립니다. 처리할 데이터를 Azure Blob 저장소에 저장 합니다. 클러스터와 함께 Azure Blob Storage를 사용 하는 방법에 대 한 자세한 내용은 [HDInsight에서 Hadoop과 HDFS 호환 가능한 Azure Blob 저장소 사용](../../hdinsight/hdinsight-hadoop-use-blob-storage.md)을 참조 하세요.

Microsoft의 TDSP 팀은 Azure HDInsight Spark 클러스터를 사용 하 여 Python 및 기타 Scala를 사용 하는 데이터 과학 솔루션을 빌드하는 방법에 대 한 두 가지 종단 간 연습을 게시 했습니다. Azure HDInsight **Spark 클러스터**에 대 한 자세한 내용은 [개요: HDInsight Linux의 Apache Spark](../../hdinsight/spark/apache-spark-overview.md)를 참조 하세요. Azure HDInsight Spark 클러스터에서 **Python** 을 사용 하 여 데이터 과학 솔루션을 빌드하는 방법에 대해 알아보려면 [Azure HDInsight에서 Spark를 사용 하는 데이터 과학 개요](spark-overview.md)를 참조 하세요. Azure HDInsight Spark 클러스터에서 **Scala** 를 사용 하 여 데이터 과학 솔루션을 빌드하는 방법에 대해 알아보려면 [Azure에서 Scala 및 Spark를 사용 하 여 데이터 과학](scala-walkthrough.md)을 참조 하세요. 


##  <a name="azure-sql-data-warehouse"></a>Azure SQL Data Warehouse

Azure SQL Data Warehouse를 사용 하면 과도 하 게 프로 비전 하거나 과도 하 게 지불 하지 않고도 계산 리소스를 몇 초 내에 쉽게 확장할 수 있습니다. 또한 계산 리소스 사용을 일시 중지 하는 고유한 옵션을 제공 하 여 클라우드 비용을 더욱 효율적으로 관리할 수 있습니다. 확장 가능한 계산 리소스를 배포 하는 기능을 사용 하면 모든 데이터를 Azure SQL Data Warehouse으로 가져올 수 있습니다. 저장소 비용은 최소화 되며 분석 하려는 데이터 집합의 일부에 대해서만 계산을 실행할 수 있습니다. 

Azure SQL Data Warehouse에 대 한 자세한 내용은 [SQL Data Warehouse](https://azure.microsoft.com/services/sql-data-warehouse) 웹 사이트를 참조 하세요. SQL Data Warehouse를 사용 하 여 종단 간 고급 분석 솔루션을 빌드하는 방법에 대 한 자세한 내용은 [실행 중인 팀 데이터 과학 프로세스: SQL Data Warehouse 사용](sqldw-walkthrough.md)을 참조 하세요.


## <a name="azure-data-lake"></a>Azure Data Lake

Azure data lake는 공식적인 요구 사항 또는 스키마가 적용 되기 전에 단일 위치에 수집 된 모든 유형의 데이터에 대 한 엔터프라이즈 수준의 리포지토리입니다. 이러한 유연성으로 인해 크기나 구조 또는 수집 속도와 관계 없이 모든 유형의 데이터를 data lake에 유지할 수 있습니다. 그러면 조직에서 Hadoop 또는 고급 분석을 사용 하 여 이러한 데이터 레이크 패턴을 찾을 수 있습니다. 데이터 레이크 데이터를 데이터 웨어하우스로 이동 하기 전에 데이터를 저렴 한 데이터 준비를 위한 리포지토리로 사용할 수도 있습니다.

Azure Data Lake에 대 한 자세한 내용은 [Azure Data Lake 소개](https://azure.microsoft.com/blog/introducing-azure-data-lake/)를 참조 하세요. Azure Data Lake를 사용 하 여 확장 가능한 종단 간 데이터 과학 솔루션을 빌드하는 방법에 대 한 자세한 내용은 [Azure Data Lake의 확장성 있는 데이터 과학: 종단 간 연습](data-lake-walkthrough.md) 을 참조 하세요.


## <a name="azure-hdinsight-hive-hadoop-clusters"></a>Azure HDInsight Hive (Hadoop) 클러스터

Apache Hive는 SQL과 유사한 쿼리 언어인 HiveQL를 사용 하 여 데이터 요약, 쿼리 및 분석을 가능 하 게 하는 Hadoop 용 데이터 웨어하우스 시스템입니다. Hive를 사용 하 여 데이터를 대화형으로 탐색 하거나 재사용 가능한 일괄 처리 작업을 만들 수 있습니다.

Hive를 사용 하면 대부분의 구조화 되지 않은 데이터에 대 한 프로젝트 구조를 지정할 수 있습니다. 구조를 정의한 후 Hive를 사용 하 여 Java 또는 MapReduce를 사용 하지 않고도 Hadoop 클러스터에서 해당 데이터를 쿼리할 수 있습니다. HiveQL (Hive 쿼리 언어)를 사용 하면 T-sql과 비슷한 문을 사용 하 여 쿼리를 작성할 수 있습니다.

데이터 과학자 경우 Hive는 Hive 쿼리에서 Python Udf (사용자 정의 함수)를 실행 하 여 레코드를 처리할 수 있습니다. 이 기능은 데이터 분석의 Hive 쿼리 기능을 크게 확장 합니다. 특히,이를 통해 데이터 과학자는 SQL과 유사한 HiveQL 및 Python에서 주로 사용 하는 언어로 확장 가능한 기능 엔지니어링을 수행할 수 있습니다. 

Azure HDInsight Hive 클러스터에 대 한 자세한 내용은 [hdinsight에서 Hadoop과 Hive 및 HiveQL 사용](../../hdinsight/hadoop/hdinsight-use-hive.md)을 참조 하세요. Azure HDInsight Hive 클러스터를 사용 하 여 확장 가능한 종단 간 데이터 과학 솔루션을 빌드하는 방법에 대 한 자세한 내용은 [실행 중인 팀 데이터 과학 프로세스: HDInsight Hadoop 클러스터 사용](hive-walkthrough.md)을 참조 하세요.


## <a name="azure-file-storage"></a>Azure File Storage 

Azure File Storage는 표준 SMB (서버 메시지 블록) 프로토콜을 사용 하 여 클라우드에서 파일 공유를 제공 하는 서비스입니다. Az SMB 2.1 és az SMB 3.0 protokollt is támogatja. Az Azure File Storage szolgáltatással költséges újraírások nélkül, gyorsan megoldható a fájlmegosztásra támaszkodó, régi típusú alkalmazások áttelepítése az Azure-ra. Az Azure virtuális gépeken vagy felhőszolgáltatásában, esetleg helyszíni ügyfeleken üzemelő alkalmazások fájlmegosztást csatlakoztathatnak a felhőben. Ez a megosztás hasonló ahhoz a csatlakoztatott SMB-megosztáshoz, amelyet az asztali alkalmazások használnak. Ezután bármennyi alkalmazás-összetevő egyszerre csatlakoztathatja a File Storage-megosztást, és hozzá is férhet.

데이터 과학 프로젝트에 특히 유용 합니다. 프로젝트 팀 멤버와 프로젝트 데이터를 공유 하는 위치로 Azure 파일 저장소를 만들 수 있습니다. 그런 다음 Azure 파일 저장소에 있는 동일한 데이터 복사본에 액세스할 수 있습니다. 또한이 파일 저장소를 사용 하 여 프로젝트를 실행 하는 동안 생성 된 기능 집합을 공유할 수 있습니다. 프로젝트가 클라이언트 engagement 인 경우 클라이언트는 자신의 Azure 구독에 따라 Azure file storage를 만들어 프로젝트 데이터와 기능을 공유할 수 있습니다. 이러한 방식으로 클라이언트는 프로젝트 데이터 자산에 대 한 모든 권한을 가집니다. Azure File Storage에 대 한 자세한 내용은 [Windows에서 Azure File Storage 시작](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-files) 및 [Linux에서 azure File Storage를 사용 하는 방법](../../storage/files/storage-how-to-use-files-linux.md)을 참조 하세요.


## <a name="sql-server-2016-r-services"></a>SQL Server 2016 R Services

R Services (데이터베이스 내)는 새로운 정보를 발견할 수 있는 지능형 응용 프로그램을 개발 하 고 배포 하기 위한 플랫폼을 제공 합니다. R 커뮤니티에서 제공 하는 많은 패키지를 포함 하 여 풍부 하 고 강력한 R 언어를 사용 하 여 모델을 만들고 SQL Server 데이터에서 예측을 생성할 수 있습니다. R Services (데이터베이스 내)는 R 언어를 SQL Server와 통합 하기 때문에 분석은 데이터와 가깝게 유지 되므로 데이터 이동과 관련 된 비용 및 보안 위험을 없앨 수 있습니다.

R Services (데이터베이스 내)는 포괄적인 SQL Server 도구 및 기술 집합을 사용 하 여 오픈 소스 R 언어를 지원 합니다. 뛰어난 성능, 보안, 안정성 및 관리 효율성을 제공 합니다. 편리 하 고 친숙 한 도구를 사용 하 여 R 솔루션을 배포할 수 있습니다. 프로덕션 응용 프로그램은 R 런타임을 호출 하 고 Transact-sql을 사용 하 여 예측 및 시각적 개체를 검색할 수 있습니다. ScaleR 라이브러리를 사용 하 여 R 솔루션의 규모와 성능을 향상 시킬 수도 있습니다. 자세한 내용은 [SQL Server R Services](https://docs.microsoft.com/sql/advanced-analytics/r/sql-server-r-services)를 참조 하세요.

Microsoft의 TDSP 팀은 SQL Server 2016 R Services에서 데이터 과학 솔루션을 빌드하는 방법을 보여 주는 두 가지 종단 간 연습을 게시 했습니다. R 프로그래머를 위한 것이 고 다른 하나는 SQL 개발자 용입니다. **R 프로그래머**의 경우 [데이터 과학 종단 간 연습](https://docs.microsoft.com/sql/advanced-analytics/tutorials/walkthrough-data-science-end-to-end-walkthrough)을 참조 하세요. **Sql 개발자**의 경우 [sql 개발자를 위한 데이터베이스 내 고급 분석 (자습서)](https://docs.microsoft.com/sql/advanced-analytics/tutorials/sqldev-in-database-r-for-sql-developers)을 참조 하세요.


## <a name="appendix"></a>부록: 데이터 과학 프로젝트를 설정 하는 도구

### <a name="install-git-credential-manager-on-windows"></a>Windows에 Git 자격 증명 관리자 설치

**Windows**에서 tdsp를 수행 하는 경우 git 리포지토리와 통신 하기 위해 **GCM (git Credential Manager)** 을 설치 해야 합니다. GCM을 설치 하려면 먼저 **chocolatey**를 설치 해야 합니다. Chocolatey 및 GCM을 설치 하려면 Windows PowerShell에서 **관리자 권한**으로 다음 명령을 실행 합니다.  

    iwr https://chocolatey.org/install.ps1 -UseBasicParsing | iex
    choco install git-credential-manager-for-windows -y
    

### <a name="install-git-on-linux-centos-machines"></a>Linux (CentOS) 컴퓨터에 Git 설치

다음 bash 명령을 실행 하 여 Linux (CentOS) 컴퓨터에 Git를 설치 합니다.

    sudo yum install git


### <a name="generate-public-ssh-key-on-linux-centos-machines"></a>Linux (CentOS) 컴퓨터에 공용 SSH 키 생성

Linux (CentOS) 컴퓨터를 사용 하 여 git 명령을 실행 하는 경우 Azure DevOps Services에서이 컴퓨터를 인식할 수 있도록 컴퓨터의 공용 SSH 키를 Azure DevOps Services에 추가 해야 합니다. 먼저 공용 SSH 키를 생성 하 고 Azure DevOps Services 보안 설정 페이지에서 SSH 공개 키에 키를 추가 해야 합니다. 

1. SSH 키를 생성 하려면 다음 두 명령을 실행 합니다. 

   ```
   ssh-keygen
   cat .ssh/id_rsa.pub
   ```
   
   ![SSH 키를 생성 하는 명령](./media/platforms-and-tools/resources-1-generate_ssh.png)

1. *Ssh-rsa*를 포함 하 여 전체 ssh 키를 복사 합니다. 
1. Azure DevOps Services에 로그인 합니다. 
1. 페이지의 오른쪽 위 모서리에서 **\>이름 <** 클릭 하 고 **보안**을 클릭 합니다. 
    
   ![이름을 클릭 한 다음 보안을 클릭 합니다.](./media/platforms-and-tools/resources-2-user-setting.png)

1. **SSH 공개 키**를 클릭 하 고 **+ 추가**를 클릭 합니다. 

   ![SSH 공개 키를 클릭 한 다음 + 추가를 클릭 합니다.](./media/platforms-and-tools/resources-3-add-ssh.png)

1. 텍스트 상자에 복사한 ssh 키를 붙여넣고 저장 합니다.


## <a name="next-steps"></a>Következő lépések

**특정 시나리오** 에 대 한 프로세스의 모든 단계를 보여 주는 전체 종단 간 연습도 제공 됩니다. [예제 연습](walkthroughs.md) 항목에서 미리 보기 설명과 함께 나열 되 고 연결 됩니다. 클라우드, 온-프레미스 도구 및 서비스를 워크플로 또는 파이프라인에 결합 하 여 지능형 응용 프로그램을 만드는 방법을 보여 줍니다. 

Azure Machine Learning Studio (클래식)를 사용 하 여 팀 데이터 과학 프로세스의 단계를 실행 하는 방법을 보여 주는 예제는 [AZURE ML](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/) 학습 경로를 사용 하 여를 참조 하세요.
