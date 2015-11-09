<properties 
   pageTitle="포털을 사용하여 Azure 데이터 레이크 저장소로 HDInsight Hadoop 클러스터 프로비전 | Azure" 
   description="Azure Preview 포털을 사용하여 Azure 데이터 레이크 저장소로 HDInsight Hadoop 클러스터 구성 및 사용" 
   services="data-lake-store" 
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
   ms.date="10/28/2015"
   ms.author="nitinme"/>

# Azure Preview 포털을 사용하여 데이터 레이크 저장소를 사용하는 HDInsight 클러스터 프로비전

> [AZURE.SELECTOR]
- [Using Portal](data-lake-store-hdinsight-hadoop-use-portal.md)
- [Using PowerShell](data-lake-store-hdinsight-hadoop-use-powershell.md)


Azure Preview 포털을 사용하여 Azure 데이터 레이크 저장소와 함께 작동하도록 HDInsight 클러스터(Hadoop, HBase 또는 Storm)를 구성하는 방법에 대해 알아봅니다. 이 릴리스에 대한 일부 중요한 고려 사항: * **Hadoop 및 Storm 클러스터(Windows 및 Linux)의 경우** 데이터 레이크 저장소는 추가 저장소 계정으로만 사용될 수 있습니다. 이러한 클러스터에 대한 기본 저장소 계정은 여전히 Azure 저장소 Blob(WASB)이 됩니다. * **HBase 클러스터(Windows 및 Linux)의 경우 ** 데이터 레이크 저장소는 기본 저장소 또는 추가 저장소로 사용될 수 있습니다.

이 문서에서 데이터 레이크 저장소를 추가 저장소로 사용하여 Hadoop 클러스터를 프로비저닝합니다. Azure Preview 포털을 사용하여 데이터 레이크 저장소와 함께 작동하도록 HDInsight를 구성하는 단계는 다음과 같습니다.

* Azure Active Directory 서비스 주체에 대한 인증을 통해 HDInsight 클러스터 만들기
* 동일한 서비스 주체를 사용하여 데이터 레이크 저장소 액세스 구성
* 클러스터에서 테스트 작업 실행

## 필수 조건

이 자습서를 시작하기 전에 다음이 있어야 합니다.

- **Azure 구독**. [Azure 무료 평가판](https://azure.microsoft.com/ko-KR/pricing/free-trial/)을 참조하세요.
- 데이터 레이크 저장소 공개 미리 보기를 위해 **Azure 구독을 사용하도록 설정합니다**. [지침](data-lake-store-get-started-portal.md#signup)을 참조하세요.


## Azure Active Directory 서비스 주체에 대한 인증을 통해 HDInsight 클러스터 만들기

이 섹션에서는 추가 저장소로 데이터 레이크 저장소를 사용하는 HDInsight Hadoop 클러스터를 만듭니다. 이 릴리스에서 Hadoop 클러스터의 경우 데이터 레이크 저장소는 클러스터에 대해 추가 저장소로만 사용될 수 있습니다. 기본 저장소는 여전히 Azure 저장소 Blob(WASB)이 됩니다. 따라서 먼저 클러스터에 필요한 저장소 계정 및 저장소 컨테이너를 만들어 보겠습니다.

1. 새로운 [Azure Preview 포털](https://portal.azure.com)에 로그인합니다.

2. [HDInsight에서 Hadoop 클러스터 만들기](../hdinsight/hdinsight-provision-clusters.md#create-using-the-preview-portal)에서 단계를 따라 HDInsight 클러스터 프로비전을 시작합니다.
 
3. **선택적 구성** 블레이드에서 **데이터 원본**을 클릭합니다. **데이터 원본** 블레이드에서 저장소 계정 및 저장소 컨테이너에 대한 세부 정보를 지정하고 **위치**를 **미국 동부 2**로 지정한 다음 **클러스터 AAD ID**를 클릭합니다.

	![HDInsight 클러스터에 서비스 주체 추가](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.1.png "HDInsight 클러스터에 서비스 주체 추가")

4. **클러스터 AAD ID** 블레이드에서 기존 서비스 주체를 선택하거나 새로 만들기를 선택할 수 있습니다.
	
	* **새 서비스 주체를 만듭니다**. **클러스터 AAD ID** 블레이드에서 **새로 만들기**를 클릭합니다. **서비스 주체**를 클릭한 다음 **서비스 주체 만들기** 블레이드에서 새 서비스 주체를 만들기 위한 값을 제공합니다. 이 작업의 일부로 인증서와 Azure Active Directory 응용 프로그램도 생성됩니다. **만들기**를 클릭합니다.

		![HDInsight 클러스터에 서비스 주체 추가](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.4.png "HDInsight 클러스터에 서비스 주체 추가")

		**클러스터 AAD ID** 블레이드에서 **선택**을 클릭하여 생성되는 서비스 주체로 진행합니다.

		![HDInsight 클러스터에 서비스 주체 추가](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.5.png "HDInsight 클러스터에 서비스 주체 추가")


	* **기존 서비스 주체를 선택합니다**. **클러스터 AAD ID** 블레이드에서 **기존 사용**을 클릭합니다. **서비스 주체**를 클릭한 다음 **서비스 주체 선택** 블레이드에서 기존 서비스 주체를 검색합니다. 서비스 주체 이름을 클릭한 다음 **선택**을 클릭합니다.

		![HDInsight 클러스터에 서비스 주체 추가](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.2.png "HDInsight 클러스터에 서비스 주체 추가")

		**클러스터 AAD ID** 블레이드에서 앞에서 만든 인증서(.pfx)를 업로드하고 인증서를 만드는데 사용한 암호를 제공합니다. **선택**을 클릭합니다. HDInsight 클러스터에 대한 Azure Active Directory 구성이 완료됩니다.

		![HDInsight 클러스터에 서비스 주체 추가](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3.png "HDInsight 클러스터에 서비스 주체 추가")

6. **데이터 원본** 블레이드에서 **선택**을 클릭하고 [HDInsight에서 Hadoop 클러스터 만들기](../hdinsight/hdinsight-provision-clusters.md#create-using-the-preview-portal)에 설명된 것처럼 클러스터 프로비저닝을 계속합니다.

7. 클러스터가 프로비전된 후 서비스 주체가 HDInsight 클러스터와 연결되어 있음을 확인할 수 있습니다. 이렇게 하려면 클러스터 블레이드에서 **설정**, **클러스터 AAD ID**를 차례로 클릭하고 연결된 서비스 주체를 확인해야 합니다.

	![HDInsight 클러스터에 서비스 주체 추가](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.6.png "HDInsight 클러스터에 서비스 주체 추가")

## <a name="acl"></a>데이터 레이크 저장소 파일 시스템에 액세스하기 위한 서비스 주체 구성

1. 새로운 [Azure Preview 포털](https://portal.azure.com)에 로그인합니다.

2. 데이터 레이크 저장소 계정이 없는 경우 만듭니다. [Azure Preview 포털을 사용하여 Azure 데이터 레이크 저장소 시작](data-lake-store-get-started-portal.md)의 지침을 따릅니다.

	데이터 레이크 저장소 계정이 있는 경우 왼쪽 창에서 **찾아보기**, **데이터 레이크 저장소**를 차례로 클릭한 다음 액세스 권한을 부여하려는 계정 이름을 클릭합니다.

	데이터 레이크 저장소 계정에서 다음 작업을 수행합니다.

	* [데이터 레이크 저장소에 폴더를 만듭니다](data-lake-store-get-started-portal.md#createfolder).
	* [데이터 레이크 저장소에 파일을 업로드합니다](data-lake-store-get-started-portal.md#uploaddata). 업로드할 일부 샘플 데이터를 찾는 경우 [Azure 데이터 레이크 Git 리포지토리](https://github.com/MicrosoftBigData/ProjectKona/tree/master/SQLIPSamples/SampleData/AmbulanceData)의 **Ambulance Data** 폴더에 있을 수 있습니다.

	나중에 HDInsight 클러스터로 데이터 레이크 저장소 계정을 테스트할 때 업로드된 파일을 사용합니다.

3. 데이터 레이크 저장소 블레이드에서 **데이터 탐색기**를 클릭합니다.

	![데이터 탐색기](./media/data-lake-store-hdinsight-hadoop-use-portal/adl.start.data.explorer.png "데이터 탐색기")

4. **데이터 탐색기** 블레이드에서 계정 루트를 클릭한 다음 계정 블레이드에서 **액세스** 아이콘을 클릭합니다.

	![데이터 레이크 파일 시스템에 ACL 설정](./media/data-lake-store-hdinsight-hadoop-use-portal/adl.acl.1.png "데이터 레이크 파일 시스템에 ACL 설정")

5. **액세스** 블레이드는 루트에 이미 할당된 표준 액세스 및 사용자 지정 액세스를 나열합니다. **추가** 아이콘을 클릭하여 사용자 지정 수준 ACL을 추가하고 앞에서 만든 서비스 주체를 포함시킵니다.

	![표준 및 사용자 지정 액세스 나열](./media/data-lake-store-hdinsight-hadoop-use-portal/adl.acl.2.png "표준 및 사용자 지정 액세스 나열")

6. **추가** 아이콘을 클릭하여 **사용자 지정 액세스 추가** 블레이드를 엽니다. 이 블레이드에서 **사용자 또는 그룹 선택**을 클릭한 다음 **사용자 또는 그룹 선택** 블레이드에서 Azure Active Director에서 이전에 만든 서비스 주체를 검색합니다. 앞에서 만든 서비스 주체의 이름은 **HDIADL**입니다. 서비스 주체 이름을 클릭한 다음 **선택**을 클릭합니다.

	![그룹 추가](./media/data-lake-store-hdinsight-hadoop-use-portal/adl.acl.3.png "그룹 추가")

7. **권한 선택**을 클릭하고 서비스 주체에 할당하려는 권한을 선택한 다음 **확인**을 클릭합니다.

	![그룹에 권한 할당](./media/data-lake-store-hdinsight-hadoop-use-portal/adl.acl.4.png "그룹에 권한 할당")

8. **사용자 지정 액세스 추가** 블레이드에서 **확인**을 클릭합니다. 이제 연결된 권한으로 새롭게 추가된 그룹이 **액세스** 블레이드에 나열됩니다.

	![그룹에 권한 할당](./media/data-lake-store-hdinsight-hadoop-use-portal/adl.acl.5.png "그룹에 권한 할당")

7. 필요한 경우 서비스 주체를 추가한 후에 액세스 권한을 수정할 수도 있습니다. 해당 권한을 제거하거나 할당할지 여부에 따라 각 권한 형식(읽기, 쓰기, 실행)에 대한 확인란을 취소하거나 선택합니다. **저장**을 클릭하여 변경 내용을 저장하거나 **취소**를 클릭하여 변경 내용을 취소합니다.



## HDInsight 클러스터에서 테스트 작업을 실행하여 Azure 데이터 레이크 저장소 사용

HDInsight 클러스터를 구성한 후에 클러스터에서 테스트 작업을 실행하여 HDInsight 클러스터가 Azure 데이터 레이크 저장소의 데이터에 액세스할 수 있는지 테스트할 수 있습니다. 이렇게 하려면 데이터 레이크 저장소를 대상으로 하는 일부 hive 쿼리를 실행합니다.

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

	이 쿼리에 해당하는 세부 정보 보기를 클릭하고 출력은 다음을 표시해야 합니다.

		hivesampletable
		vehicles

	**vehicles**는 앞에서 만든 테이블입니다. **hivesampletable**은 모든 HDInsight 클러스터에서 기본으로 사용할 수 있는 샘플 테이블입니다.

5. 쿼리를 실행하여 **vehicles**에서 데이터를 검색할 수도 있습니다.

		SELECT * FROM vehicles LIMIT 5;

## HDFS 명령을 사용한 액세스 데이터 레이크 저장소

데이터 레이크 저장소를 사용하도록 HDInsight 클러스터를 구성한 후 HDFS 셸 명령을 사용하여 저장소에 액세스할 수 있습니다.

1. 새로운 [Azure Preview 포털](https://portal.azure.com)에 로그인합니다.

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

## 데이터 레이크 저장소를 기본 저장소로 사용하는 HBase 클러스터 프로비전에 대한 고려 사항

HBase 클러스터의 경우 데이터 레이크 저장소 계정을 기본 저장소로 사용할 수 있습니다. 이렇게 하도록 선택한 경우 클러스터를 성공적으로 프로비전하려면 클러스터와 연결된 서비스 주체는 **반드시** 데이터 레이크 저장소 계정에 대한 액세스가 있어야 합니다. 두 가지 방법으로 확인할 수 있습니다.

* **기존 서비스 주체를 사용하는 경우** 클러스터 프로비전을 시작하기 전에 서비스 주체가 데이터 레이크 저장소 파일 시스템의 루트 수준에서 ACL에 추가되었는지 확인해야 합니다.
* **새 서비스 주체를 만들도록 선택한 경우** 클러스터 프로비전의 일부로 클러스터가 프로비전을 시작하는 즉시 새로 만든 서비스 주체를 데이터 레이크 저장소 파일 시스템의 루트 수준에 추가해야 합니다. 이렇게 하지 않으면 클러스터는 프로비전되지만 HBase 서비스는 시작에 실패합니다. 이 문제를 해결하려면 서비스 주체를 데이터 레이크 저장소 계정의 ACL에 추가한 다음 Ambari 웹 UI를 사용하여 HBase 서비스를 다시 시작해야 합니다.

서비스 주체를 데이터 레이크 저장소 파일 시스템에 추가하는 방법에 대한 지침은 [서비스 주체를 구성하여 데이터 레이크 저장소 파일 시스템에 액세스](#acl)를 참조하세요.



## 참고 항목

* [PowerShell: HDInsight 클러스터를 만들어 데이터 레이크 저장소 사용](data-lake-store-hdinsight-hadoop-use-powershell.md)

[makecert]: https://msdn.microsoft.com/ko-KR/library/windows/desktop/ff548309(v=vs.85).aspx
[pvk2pfx]: https://msdn.microsoft.com/ko-KR/library/windows/desktop/ff550672(v=vs.85).aspx

<!---HONumber=Nov15_HO1-->