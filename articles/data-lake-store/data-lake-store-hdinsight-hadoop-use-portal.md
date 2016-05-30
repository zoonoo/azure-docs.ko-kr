<properties
   pageTitle="포털을 사용하여 Azure 데이터 레이크 저장소로 HDInsight Hadoop 클러스터 만들기 | Azure"
   description="Azure 포털을 사용하여 Azure 데이터 레이크 저장소로 HDInsight Hadoop 클러스터 만들기 및 사용"
   services="data-lake-store,hdinsight" 
   documentationCenter=""
   authors="nitinme"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="05/11/2016"
   ms.author="nitinme"/>

# Azure 포털을 사용하여 데이터 레이크 저장소로 HDInsight 클러스터 만들기

> [AZURE.SELECTOR]
- [포털 사용](data-lake-store-hdinsight-hadoop-use-portal.md)
- [PowerShell 사용](data-lake-store-hdinsight-hadoop-use-powershell.md)


Azure 포털을 사용하여 Azure 데이터 레이크 저장소에 대한 액세스 권한을 가진 HDInsight 클러스터(Hadoop, HBase 또는 Storm)를 만드는 방법에 대해 알아봅니다. 이 릴리스에 대한 일부 중요한 고려 사항:

* **Hadoop 클러스터(Windows 및 Linux)의 경우** Data Lake 저장소는 추가 저장소 계정으로만 사용될 수 있습니다. 이러한 클러스터에 대한 기본 저장소 계정은 여전히 Azure 저장소 Blob(WASB)입니다.

* **Storm 클러스터(Windows 및 Linux)의 경우** Data Lake 저장소는 Storm 토폴로지에서 데이터를 쓰는 데 사용될 수 있습니다. 데이터 레이크 저장소는 Storm 토폴로지에서 읽을 수 있는 참조 데이터를 저장하는 데도 사용될 수 있습니다. 자세한 내용은 [Storm 토폴로지에서 Data Lake 저장소 사용](#use-data-lake-store-in-a-storm-topology)을 참조하세요.

* **HBase 클러스터(Windows 및 Linux)의 경우** 데이터 레이크 저장소는 기본 저장소나 추가 저장소로 사용될 수 있습니다. 자세한 내용은 [HBase 클러스터에서 Data Lake 저장소 사용](#use-data-lake-store-with-hbase-clusters)을 참조하세요.

> [AZURE.NOTE] Data Lake 저장소에 액세스할 수 있는 HDInsight 클러스터를 만드는 옵션은 HDInsight 버전 3.2(Windows 및 Linux의 경우)에만 사용할 수 있습니다.


## 필수 조건

이 자습서를 시작하기 전에 다음이 있어야 합니다.

- **Azure 구독**. [Azure 무료 평가판](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.
- 데이터 레이크 저장소 공개 미리 보기를 위해 **Azure 구독을 사용하도록 설정합니다**. [지침](data-lake-store-get-started-portal.md#signup)을 참조하세요.
- **Azure Data Lake 저장소 계정** [Azure 포털을 사용하여 Azure 데이터 레이크 저장소 시작](data-lake-store-get-started-portal.md)의 지침을 따릅니다. 계정을 만든 후 다음 작업을 수행하여 몇몇 샘플 데이터를 업로드합니다. 데이터 레이크 저장소의 데이터에 액세스하는 HDInsight 클러스터에서 작업을 실행하려면 자습서의 뒷부분에서 이 데이터가 필요합니다.

	* [데이터 레이크 저장소에 폴더를 만듭니다](data-lake-store-get-started-portal.md#createfolder).
	* [Data Lake 저장소에 파일을 업로드합니다](data-lake-store-get-started-portal.md#uploaddata). 업로드할 일부 샘플 데이터를 찾는 경우 [Azure 데이터 레이크 Git 리포지토리](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData)의 **Ambulance Data** 폴더에 있을 수 있습니다.

## 비디오로 빠르게 배우시겠습니까?

Data Lake 저장소에 대한 액세스와 HDInsight 클러스터를 프로비전하는 방법을 이해하려면 다음 비디오를 시청하세요.

* [Data Lake 저장소에 대한 액세스와 HDInsight 클러스터 만들기](https://mix.office.com/watch/l93xri2yhtp2)
* 클러스터가 설정된 후, [Hive 및 Pig 스크립트를 사용하여 Data Lake 저장소의 데이터에 액세스](https://mix.office.com/watch/1n9g5w0fiqv1q)

## Azure 데이터 레이크 저장소 계정에 액세스할 수 있는 HDInsight 클러스터를 만듭니다.

이 섹션에서는 추가 저장소로 데이터 레이크 저장소를 사용하는 HDInsight Hadoop 클러스터를 만듭니다. 이 릴리스에서 Hadoop 클러스터의 경우 데이터 레이크 저장소는 클러스터에 대해 추가 저장소로만 사용될 수 있습니다. 기본 저장소는 여전히 Azure 저장소 Blob(WASB)이 됩니다. 따라서 먼저 클러스터에 필요한 저장소 계정 및 저장소 컨테이너를 만들어 보겠습니다.

1. 새로운 [Azure 포털](https://portal.azure.com)에 로그인합니다.

2. [HDInsight에서 Hadoop 클러스터 만들기](../hdinsight/hdinsight-provision-clusters.md#create-using-the-preview-portal)에서 단계를 따라 HDInsight 클러스터 프로비전을 시작합니다.

3. **선택적 구성** 블레이드에서 **데이터 원본**을 클릭합니다. **데이터 원본** 블레이드에서 저장소 계정 및 저장소 컨테이너에 대한 세부 정보를 지정하고 **위치**를 **미국 동부 2**로 지정한 다음 **클러스터 AAD ID**를 클릭합니다.

	![HDInsight 클러스터에 서비스 주체 추가](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.1.png "HDInsight 클러스터에 서비스 주체 추가")

4. **클러스터 AAD ID** 블레이드에서 기존 서비스 주체를 선택하거나 새로 만들기를 선택할 수 있습니다.

	* **새 서비스 주체를 만듭니다.**

		* **클러스터 AAD ID** 블레이드에서 **새로 만들기**, **서비스 주체**를 차례로 클릭한 다음 **서비스 주체 만들기** 블레이드에서 값을 제공하여 새 서비스 주체를 만듭니다. 이 작업의 일부로 인증서와 Azure Active Directory 응용 프로그램도 생성됩니다. **만들기**를 클릭합니다.

			![HDInsight 클러스터에 서비스 주체 추가](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.2.png "HDInsight 클러스터에 서비스 주체 추가")

		* **클러스터 AAD ID** 블레이드에서 **ADLS 액세스 관리**를 클릭합니다. 창에 구독과 연결된 데이터 레이크 저장소가 표시됩니다. 그러나 만든 계정에 대해서만 사용 권한을 설정할 수 있습니다. HDInsight 클러스터와 연결할 계정에 대한 읽기/쓰기/실행 사용 권한을 선택한 다음 **사용 권한 저장**을 클릭합니다.

			![HDInsight 클러스터에 서비스 주체 추가](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3.png "HDInsight 클러스터에 서비스 주체 추가")

		* **클러스터 AAD ID** 블레이드에서 **인증서 다운로드**를 클릭하여 만든 서비스 주체와 연결된 인증서를 다운로드합니다. 추가 HDInsight 클러스터를 만드는 동안 나중에 동일한 서비스 주체를 사용하려는 경우에 유용합니다. **선택**을 클릭합니다.

			![HDInsight 클러스터에 서비스 주체 추가](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.4.png "HDInsight 클러스터에 서비스 주체 추가")


	* **기존 서비스 주체를 선택합니다**.

		* **클러스터 AAD ID** 블레이드에서 **기존 정보 사용**, **서비스 주체**를 차례로 클릭한 다음 **서비스 주체 선택** 블레이드에서 기존 서비스 주체를 검색합니다. 서비스 주체 이름을 클릭한 다음 **선택**을 클릭합니다.

			![HDInsight 클러스터에 서비스 주체 추가](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.5.png "HDInsight 클러스터에 서비스 주체 추가")

		* **클러스터 AAD ID** 블레이드에서 선택한 서비스 주체와 연결된 인증서(.pfx)를 업로드한 다음 인증서 암호를 제공합니다.

		* **ADLS 액세스 관리**를 클릭합니다. 창에 구독과 연결된 데이터 레이크 저장소가 표시됩니다. 그러나 만든 계정에 대해서만 사용 권한을 설정할 수 있습니다. HDInsight 클러스터와 연결할 계정에 대한 읽기/쓰기/실행 사용 권한을 선택한 다음 **사용 권한 저장**을 클릭합니다.

			![HDInsight 클러스터에 서비스 주체 추가](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.5.existing.save.png "HDInsight 클러스터에 서비스 주체 추가")

		* **사용 권한 저장**, **선택**을 차례로 클릭합니다.

6. **데이터 원본** 블레이드에서 **선택**을 클릭하고 [HDInsight에서 Hadoop 클러스터 만들기](../hdinsight/hdinsight-provision-clusters.md#create-using-the-preview-portal)에 설명된 것처럼 클러스터 프로비저닝을 계속합니다.

7. 클러스터가 프로비전된 후 서비스 주체가 HDInsight 클러스터와 연결되어 있음을 확인할 수 있습니다. 이렇게 하려면 클러스터 블레이드에서 **설정**, **클러스터 AAD ID**를 차례로 클릭하고 연결된 서비스 주체를 확인해야 합니다.

	![HDInsight 클러스터에 서비스 주체 추가](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.6.png "HDInsight 클러스터에 서비스 주체 추가")

## HDInsight 클러스터에서 테스트 작업을 실행하여 Azure 데이터 레이크 저장소 사용

HDInsight 클러스터를 구성한 후에 클러스터에서 테스트 작업을 실행하여 HDInsight 클러스터가 Azure 데이터 레이크 저장소의 데이터에 액세스할 수 있는지 테스트할 수 있습니다. 이렇게 하려면 데이터 레이크 저장소를 대상으로 하는 일부 hive 쿼리를 실행합니다.

### Linux 클러스터의 경우

1. 방금 프로비전한 클러스터에 대한 클러스터 블레이드를 연 다음 **대시보드**를 클릭합니다. Linux 클러스터에 대한 Ambari를 엽니다. Ambari에 액세스할 때 사이트에 인증하라는 메시지가 표시됩니다. 클러스터를 만들 때 사용한 관리자(기본 관리자), 계정 이름 및 암호를 입력합니다.

	![클러스터 대시보드 시작](./media/data-lake-store-hdinsight-hadoop-use-portal/hdiadlcluster1.png "클러스터 대시보드 시작")

	웹 브라우저의 https://CLUSTERNAME.azurehdinsight.net으로 이동하여 Ambari로 직접 탐색할 수도 있습니다(여기서 **CLUSTERNAME**은 HDInsight 클러스터의 이름임).

2. Hive 뷰를 엽니다. 사용 가능한 뷰를 나열하려면 페이지 메뉴(**관리자** 링크 옆 및 페이지 오른쪽의 단추)에서 사각형 집합을 선택합니다. **Hive** 뷰를 선택합니다.

	![Ambari 뷰 선택](./media/data-lake-store-hdinsight-hadoop-use-portal/selecthiveview.png)

3. 다음과 유사한 결과가 표시됩니다.

	![쿼리 편집기 섹션이 포함된 Hive 뷰 페이지 이미지](./media/data-lake-store-hdinsight-hadoop-use-portal/hiveview.png)

4. 페이지의 **쿼리 편집기** 섹션에서 다음 HiveQL 문을 워크시트에 붙여넣습니다.

		CREATE EXTERNAL TABLE vehicles (str string) LOCATION 'adl://mydatalakestore.azuredatalakestore.net:443/mynewfolder'

5. **쿼리 편집기** 하단의 **실행** 단추를 클릭하여 쿼리를 시작합니다. **쿼리 편집기** 아래에 **쿼리 프로세스 결과** 섹션이 나타나고 작업에 대한 정보가 표시될 것입니다.

6. 쿼리가 완료되면 **쿼리 프로세스 결과** 섹션에 작업 결과가 표시됩니다. **결과** 탭에는 다음 정보가 표시됩니다.

7. 다음 쿼리를 실행하여 테이블이 생성되었는지 확인합니다.

		SHOW TABLES;

	**결과** 탭은 다음을 표시합니다.

		hivesampletable
		vehicles

	**vehicles**는 앞에서 만든 테이블입니다. **hivesampletable**은 모든 HDInsight 클러스터에서 기본으로 사용할 수 있는 샘플 테이블입니다.

8. 쿼리를 실행하여 **차량** 테이블에서 데이터를 검색할 수도 있습니다.

		SELECT * FROM vehicles LIMIT 5;

### Windows 클러스터의 경우

1. 방금 프로비전한 클러스터에 대한 클러스터 블레이드를 연 다음 **대시보드**를 클릭합니다.

	![클러스터 대시보드 시작](./media/data-lake-store-hdinsight-hadoop-use-portal/hdiadlcluster1.png "클러스터 대시보드 시작")

	메시지가 표시되면 클러스터에 대한 관리자 자격 증명을 입력합니다.

2. Microsoft Azure HDInsight 쿼리 콘솔이 열립니다. **Hive 편집기**를 클릭합니다.

	![Hive 편집기 열기](./media/data-lake-store-hdinsight-hadoop-use-portal/hdiadlcluster2.png "Hive 편집기 열기")

3. Hive 편집기에서 다음 쿼리를 입력한 다음 **제출**을 클릭합니다.

		CREATE EXTERNAL TABLE vehicles (str string) LOCATION 'adl://mydatalakestore.azuredatalakestore.net:443/mynewfolder'

	이 Hive 쿼리에서 데이터 레이크 저장소에 저장된 데이터에서 테이블을 `adl://mydatalakestore.azuredatalakestore.net:443/mynewfolder`에 만듭니다. 이 위치에는 이전에 업로드했어야 하는 샘플 데이터 파일이 있습니다.

	아래의 **작업 세션** 테이블은 **초기화 중**에서 **실행 중**, **완료됨**으로 작업 변경 상태를 보여 줍니다. **세부 정보 보기**를 클릭하여 완료된 작업에 대한 자세한 정보를 볼 수 있습니다.

	![테이블 만들기](./media/data-lake-store-hdinsight-hadoop-use-portal/hdiadlcluster3.png "테이블 만들기")

4. 다음 쿼리를 실행하여 테이블이 생성되었는지 확인합니다.

		SHOW TABLES;

	이 쿼리에 해당하는 **세부 정보 보기**를 클릭하면 출력에 다음이 표시됩니다.

		hivesampletable
		vehicles

	**vehicles**는 앞에서 만든 테이블입니다. **hivesampletable**은 모든 HDInsight 클러스터에서 기본으로 사용할 수 있는 샘플 테이블입니다.

5. 쿼리를 실행하여 **차량** 테이블에서 데이터를 검색할 수도 있습니다.

		SELECT * FROM vehicles LIMIT 5;


## HDFS 명령을 사용한 액세스 데이터 레이크 저장소

데이터 레이크 저장소를 사용하도록 HDInsight 클러스터를 구성한 후 HDFS 셸 명령을 사용하여 저장소에 액세스할 수 있습니다.

### Linux 클러스터의 경우

이 섹션에서 클러스터로 SSH하고 HDFS 명령을 실행합니다. Windows에는 SSH 클라이언트가 기본 제공되지 않습니다. **PuTTY**를 사용하는 것이 좋습니다([http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)에서 다운로드할 수 있음).

PuTTY 사용에 대한 자세한 내용은 [Windows에서 HDInsight의 Linux 기반 Hadoop과 SSH 사용](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md)을 참조하세요.

연결되면 다음 HDFS 파일 시스템 명령을 사용하여 데이터 레이크 저장소에 파일을 나열합니다.

	hdfs dfs -ls adl://<Data Lake Store account name>.azuredatalakestore.net:443/

데이터 레이크 저장소에 이전에 업로드한 파일이 나열되어야 합니다.

	15/09/17 21:41:15 INFO web.CaboWebHdfsFileSystem: Replacing original urlConnectionFactory with org.apache.hadoop.hdfs.web.URLConnectionFactory@21a728d6
	Found 1 items
	-rwxrwxrwx   0 NotSupportYet NotSupportYet     671388 2015-09-16 22:16 adl://mydatalakestore.azuredatalakestore.net:443/mynewfolder

`hdfs dfs -put` 명령을 사용하여 일부 파일을 데이터 레이크 저장소에 업로드한 다음 `hdfs dfs -ls`을(를) 사용하여 파일이 성공적으로 업로드되었는지 여부를 확인할 수도 있습니다.


### Windows 클러스터의 경우

1. 새로운 [Azure 포털](https://portal.azure.com)에 로그인합니다.

2. **찾아보기**를 클릭하고 **HDInsight 클러스터**를 클릭한 다음 만든 HDInsight 클러스터를 클릭합니다.

3. 클러스터 블레이드에서 **원격 데스크톱**을 클릭한 다음 **원격 데스크톱** 블레이드에서 **연결**을 클릭합니다.

	![HDI 클러스터로 원격](./media/data-lake-store-hdinsight-hadoop-use-portal/ADL.HDI.PS.Remote.Desktop.png "Azure 리소스 그룹 만들기")

	메시지가 표시되면 원격 데스크톱 사용자에 대해 제공된 자격 증명을 입력합니다.

4. 원격 세션에서 Windows PowerShell을 시작하고 HDFS 파일 시스템 명령을 사용하여 Azure 데이터 레이크 저장소의 파일을 나열합니다.

	 	hdfs dfs -ls adl://<Data Lake Store account name>.azuredatalakestore.net:443/

	데이터 레이크 저장소에 이전에 업로드한 파일이 나열되어야 합니다.

		15/09/17 21:41:15 INFO web.CaboWebHdfsFileSystem: Replacing original urlConnectionFactory with org.apache.hadoop.hdfs.web.URLConnectionFactory@21a728d6
		Found 1 items
		-rwxrwxrwx   0 NotSupportYet NotSupportYet     671388 2015-09-16 22:16 adl://mydatalakestore.azuredatalakestore.net:443/mynewfolder

	`hdfs dfs -put` 명령을 사용하여 일부 파일을 데이터 레이크 저장소에 업로드한 다음 `hdfs dfs -ls`을(를) 사용하여 파일이 성공적으로 업로드되었는지 여부를 확인할 수도 있습니다.


## Storm 토폴로지에서 데이터 레이크 저장소 사용

데이터 레이크 저장소를 사용하여 Storm 토폴로지에서 데이터를 쓸 수 있습니다. 이 시나리오를 달성하는 방법에 대한 자세한 내용은 [HDInsight에서 Apache Storm으로 Azure Data Lake 저장소 사용](../hdinsight/hdinsight-storm-write-data-lake-store.md)을 참조하세요.

## HBase 클러스터에서 Data Lake 저장소 사용

HBase 클러스터에서 추가 저장소 외에 기본 저장소로 Data Lake 저장소를 사용할 수 있습니다. 이렇게 하려면 다음을 수행합니다.

1.  **데이터 원본** 블레이드에서 **HBase 데이터 위치**로 **Data Lake 저장소**를 선택합니다.
2.  사용할 Data Lake 저장소의 이름을 선택하거나 새 Data Lake 저장소를 만듭니다.
3.  마지막으로 데이터 Data Lake 저장소 내에 **HBase 루트 폴더**를 지정합니다. Data Lake 저장소 계정에 루트 폴더가 없으면 새로 만듭니다.

	![Data Lake 저장소와 HBase](./media/data-lake-store-hdinsight-hadoop-use-portal/hbase-data-lake-store.png "Azure 리소스 그룹 만들기")

### Data Lake 저장소를 HBase 클러스터에 대한 기본 저장소로 사용할 경우 고려 사항

* 둘 이상의 HBase 클러스터에 대해 동일한 Data Lake 저장소 계정을 사용할 수 있습니다. 그러나 클러스터에 대해 제공하는 **HBase 루트 폴더**(위 화면 캡처의 4단계)는 고유해야 합니다. 서로 다른 두 개의 HBase 클러스터에서 동일한 루트 폴더를 사용하면 **안 됩니다**.
* Data Lake 저장소 계정을 기본 저장소로 사용하더라도 HBase 클러스터 로그 파일은 클러스터와 연결된 Azure 저장소 Blob(WASB)에 그대로 저장됩니다. 이 내용은 위 화면 캡처에 파란색 상자로 강조 표시되어 있습니다.



## 참고 항목

* [PowerShell: HDInsight 클러스터를 만들어 데이터 레이크 저장소 사용](data-lake-store-hdinsight-hadoop-use-powershell.md)

[makecert]: https://msdn.microsoft.com/library/windows/desktop/ff548309(v=vs.85).aspx
[pvk2pfx]: https://msdn.microsoft.com/library/windows/desktop/ff550672(v=vs.85).aspx

<!---HONumber=AcomDC_0518_2016-->