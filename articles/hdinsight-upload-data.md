<properties 
	pageTitle="HDInsight에서 Hadoop 작업용 데이터 업로드 | Microsoft Azure" 
	description="Azure 저장소 탐색기, Azure PowerShell, Hadoop 명령줄 또는 Sqoop을 사용하여 HDInsight에서 데이터를 업로드 및 액세스하는 방법에 대해 알아봅니다." 
	services="storage, hdinsight" 
	documentationCenter="" 
	authors="mumian" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/31/2015" 
	ms.author="jgao"/>



#HDInsight에서 Hadoop 작업용 데이터 업로드

Azure HDInsight는 Azure Blob 저장소를 통해 모든 기능을 갖춘 HDFS(Hadoop Distributed File System)를 제공합니다. 고객에게 원활한 환경을 제공하기 위해 HDFS를 확장하여 설계되었습니다. 이를 통해 Hadoop 에코 시스템에서 구성 요소의 전체 집합이 관리하는 데이터에서 직접 작동하도록 합니다. Azure Blob 저장소와 HDFS는 데이터의 저장소 및 해당 데이터의 계산을 최적화하는 별개의 파일 시스템입니다. Azure Blob 저장소 사용의 이점에 대한 자세한 내용은 [HDInsight에서 Azure Blob 저장소 사용][hdinsight-storage]을 참조하십시오.

Azure HDInsight 클러스터는 일반적으로 MapReduce 작업 실행을 위해 배포되며 이러한 작업이 완료되면 삭제됩니다. 계산이 완료된 후 HDFS 클러스터에 데이터를 유지하면 데이터 저장에 비용이 많이 들 수 있습니다. Azure Blob 저장소는 HDInsight를 사용하여 처리할 데이터를 위한 옵션으로서 확장성, 가용성, 용량이 뛰어나고 경제적이며 공유할 수 있습니다. Blob에 데이터를 저장하면 계산에 사용할 HDInsight 클러스터를 데이터 손실 없이 안전하게 릴리스할 수 있습니다.

[AzCopy][azure-azcopy], [Azure PowerShell][azure-powershell], [Azure Storage Client Library for .NET][azure-storage-client-library], [Mac, Linux 및 Windows용 Azure 명령줄 인터페이스][xplatcli] 또는 탐색 도구를 통해 Azure Blob 저장소에 액세스할 수 있습니다. 사용 가능한 도구는 다음과 같습니다:

* [Azure 저장소 탐색기](http://azurestorageexplorer.codeplex.com/)
* [클라우드 저장소 스튜디오 2](http://www.cerebrata.com/Products/CloudStorageStudio/)
* [CloudXplorer](http://clumsyleaf.com/products/cloudxplorer)
* [Azure 탐색기](http://www.cloudberrylab.com/free-microsoft-azure-explorer.aspx)
* [Azure 탐색기 PRO](http://www.cloudberrylab.com/microsoft-azure-explorer-pro.aspx)

##필수 조건

시작하기 전에 다음 요구 사항을 확인하십시오:

* Azure HDInsight 클러스터를 만듭니다. 자세한 내용은 [Azure HDInsight 시작][hdinsight-get-started] 또는 [HDInsight 클러스터 프로비전][hdinsight-provision]을 참조하세요.


##<a id="azcopy"></a>AzCopy를 사용하여 Azure Blob 저장소에 데이터 업로드##

AzCopy는 데이터를 Azure 저장소 계정으로 보내고 받는 작업을 간소화하도록 설계된 명령줄 도구입니다. 이 유틸리티는 독립 실행형 도구로 사용할 수도 있고 기존 응용 프로그램에 통합할 수도 있습니다. [AzCopy를 다운로드][azure-azcopy-download]하십시오.

AzCopy 구문은 다음과 같습니다.

	AzCopy <Source> <Destination> [filePattern [filePattern...]] [Options]

자세한 내용은 [AzCopy - Azure Blob용 파일 업로드/다운로드][azure-azcopy]를 참조하십시오.

##<a id="powershell"></a>Azure PowerShell을 사용하여 Azure Blob 저장소에 데이터 업로드##

Azure PowerShell은 Azure에서 작업의 배포와 관리를 제어 및 자동화하기 위해 사용할 수 있는 강력한 스크립팅 환경입니다. Azure PowerShell을 사용하면, MapReduce 작업에서 처리할 수 있도록 데이터를 Azure Blob 저장소에 업로드할 수 있습니다. Azure PowerShell을 실행하기 위해 워크스테이션을 구성하는 방법에 대한 자세한 내용은 [Azure PowerShell 설치 및 구성](powershell-install-configure.md)을 참조하십시오.

**Azure Blob 저장소에 로컬 파일을 업로드하려면**

1. [Azure PowerShell 설치 및 구성](powershell-install-configure.md)의 지침에 따라 Azure PowerShell 콘솔을 엽니다.
2. 다음 스크립트에서 처음 5개의 변수 값을 설정합니다.

		$subscriptionName = "<AzureSubscriptionName>"
		$storageAccountName = "<StorageAccountName>"
		$containerName = "<ContainerName>"
		
		$fileName ="<LocalFileName>"
		$blobName = "<BlobName>"
		
		# Get the storage account key
		Select-AzureSubscription $subscriptionName
		$storageaccountkey = get-azurestoragekey $storageAccountName | %{$_.Primary}
		
		# Create the storage context object
		$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageaccountkey
		
		# Copy the file from local workstation to the Blob container		
		Set-AzureStorageBlobContent -File $fileName -Container $containerName -Blob $blobName -context $destContext
		
3. Azure PowerShell 콘솔에 스크립트를 붙여넣고 실행합니다.

Azure Blob 저장소 컨테이너는 키/값 쌍으로 데이터를 저장하며, 디렉터리 계층 구조는 없습니다. 그러나 파일이 디렉터리 구조 내에 저장된 것처럼 보이도록 키 이름에 "/" 문자를 사용할 수 있습니다. 예를 들어 Blob의 키 이름을 *input/log1.txt*로 지정할 수 있습니다. 실제로 "input" 디렉터리는 없지만 키 이름에 "/" 문자가 있으므로 파일 경로처럼 보입니다. 예를 들면 *$blobname="myfolder/myfile.txt"*처럼 이전 스크립트에서는 **$blobname** 변수를 설정하여 파일에 폴더 구조를 제공할 수 있습니다. 

Azure 탐색기 도구를 사용하면 몇몇 0바이트 파일을 발견할 수 있습니다. 이러한 파일의 목적은 두 가지입니다.

- 빈 폴더가 있으면 폴더의 존재를 표시합니다. Azure Blob 저장소는 지능적이어서 foo/bar라는 Blob가 있으면 **foo**라는 폴더도 있다는 사실을 압니다. 그러나 **foo**라는 빈 폴더를 보여주려는 경우 유일한 방법은 이 특별한 0바이트 파일을 만들어 두는 것입니다.
- Hadoop 파일 시스템에 필요한 몇몇 특수 메타데이터, 특히 폴더에 대한 사용 권한 및 소유자를 보유합니다.


##<a id="xplatcli"></a>Azure CLI를 사용하여 Azure Blob 저장소에 데이터 업로드

Mac, Linux 및 Windows용 Azure CLI는 Azure 서비스를 관리할 수 있도록 하는 크로스 플랫폼 도구입니다. Azure Blob 저장소에 데이터를 업로드하려면 다음 단계를 사용합니다:

1. [Mac, Linux 및 Windows용 Azure CLI의 설치 및 구성](xplat-cli.md)

2. 명령 프롬프트, bash 또는 다른 셸을 열고 다음을 사용하여 Azure 구독을 인증합니다.

		azure login

	메시지가 표시되면 구독하려는 사용자 이름 및 암호를 입력합니다.

3. 다음 명령을 사용하여 구독하려는 저장소 계정을 나열합니다:

		azure storage account list

4. 작업하려는 blob가 포함된 저장소 계정을 선택한 후에 다음 명령을 사용하여 이 계정에 대한 키를 검색합니다.

		azure storage account keys list <storage-account-name>

	**기본** 및 **보조** 키가 반환되어야 합니다. 다음 단계에서 사용되므로 **기본** 키 값을 복사합니다.

5. 저장소 계정 내에서 blob 컨테이너의 목록을 검색하려면 다음 명령을 사용합니다:

		azure storage container list -a <storage-account-name> -k <primary-key>

6. 파일을 blob에 업로드 및 다운로드하려면 다음 명령을 사용합니다:

	* 파일을 업로드하려면:

			azure storage blob upload -a <storage-account-name> -k <primary-key> <source-file> <container-name> <blob-name>

	* 파일을 다운로드하려면:

			azure storage blob download -a <storage-account-name> -k <primary-key> <container-name> <blob-name> <destination-file>

> [AZURE.NOTE]항상 동일한 저장소 계정으로 작업하는 경우 모든 명령에 대한 키를 지정하는 대신 다음의 환경 변수를 설정할 수 있습니다:
> 
> * **AZURE_STORAGE_ACCOUNT**: 저장소 계정 이름
> 
> * **AZURE_STORAGE_ACCESS_KEY**: 저장소 계정 키

##<a id="storageexplorer"></a>Azure 저장소 탐색기를 사용하여 Blob 저장소에 데이터 업로드

*Azure 저장소 탐색기*는 Azure 저장소의 데이터를 검사하고 변경하기 위한 유용한 도구로서 CodePlex: [Azure 저장소 탐색기](http://azurestorageexplorer.codeplex.com/ "Azure 저장소 탐색기")에서 무료로 다운로드할 수 있습니다.

이 도구를 사용하기 전에 Azure 저장소 계정 이름과 계정 키를 알아야 합니다. 더 자세한 정보를 얻을 지침을 보려면 [저장소 계정 생성, 관리 또는 삭제][azure-create-storage-account]에서 "방법: 저장소 액세스 키 보기, 복사 및 다시 생성” 섹션을 참조하십시오.

1. Azure 저장소 탐색기를 실행합니다.

	![HDI.AzureStorageExplorer][image-azure-storage-explorer]

2. **계정 추가**를 클릭합니다. Azure 저장소 탐색기에 계정을 추가한 후에는 이 단계를 다시 수행할 필요가 없습니다.

	![HDI.ASEAddAccount][image-ase-addaccount]

3. **저장소 계정 이름** 및 **저장소 계정 키**를 입력한 다음 **저장소 계정 추가**를 클릭합니다. 저장소 계정을 여러 개 추가할 수 있으며 각 계정은 각 탭에 표시됩니다.
4. **저장소 형식**에서 **Blob**을 선택합니다.

	![HDI.ASEBlob][image-ase-blob]

5. **컨테이너**에서 HDInsight 클러스터에 연결된 컨테이너 이름을 클릭합니다. HDInsight 클러스터를 만들 때 컨테이너를 지정해야 합니다. 그렇게 하지 않은 경우 클러스터 만들기 과정에서 자동으로 생성됩니다.
6. **Blob**에서 **업로드**를 클릭합니다.
7. 업로드할 파일을 지정한 다음 **열기**를 클릭합니다.








































































##<a id="commandline"></a> Hadoop 명령줄을 사용하여 Azure Blob 저장소에 데이터 업로드

Hadoop 명령줄을 사용하기 전에 먼저 원격 데스크톱을 사용하여 클러스터에 연결해야 합니다:



1. [Azure 포털][azure-management-portal]에 로그인합니다.
2. **HDINSIGHT**를 클릭합니다. 배포된 Hadoop 클러스터의 목록이 표시됩니다.
3. 데이터를 업로드할 HDInsight 클러스터를 클릭합니다.
4. 페이지 맨 위에서 **구성**을 클릭합니다.
5. 원격 데스크톱을 사용하도록 설정하지 않은 경우 **원격 사용**을 클릭하고 지침을 따릅니다. 그렇지 않은 경우 다음 단계로 건너뜁니다.
4. 페이지 맨 아래에 있는 **연결**을 클릭합니다.
7. **열기**를 클릭합니다.
8. 자격 증명을 입력한 다음 **확인**을 클릭합니다.
9. **예**를 클릭합니다.
10. 데스크톱에서 **Hadoop 명령줄**을 클릭합니다.
12. 다음 코드 샘플은 HDInsight 헤드 노드의 로컬 파일 시스템에서 /example/data 디렉터리로 davinci.txt 파일을 복사하는 방법을 보여 줍니다.

		hadoop dfs -copyFromLocal C:\temp\davinci.txt /example/data/davinci.txt

	기본 파일 시스템은 Azure Blob 저장소에 있으므로 /example/datadavinci.txt는 실제로 Azure Blob 저장소에 있습니다. 파일이 다음과 같을 수도 있습니다.

		wasb:///example/data/davinci.txt 

	또는

		wasbs://<ContainerName>@<StorageAccountName>.blob.core.windows.net/example/data/davinci.txt

	*wasbs*를 사용할 때에는 정규화된 도메인 이름이 필요합니다.

13. 다음 명령을 사용하여 업로드된 파일을 나열합니다.

		hadoop dfs -lsr /example/data


##<a id="sqoop"></a> Sqoop을 사용하여 Azure SQL 데이터베이스/SQL Server에서 HDFS로 데이터 가져오기

Sqoop은 Hadoop과 관계형 데이터베이스 간 데이터 전송을 위해 설계된 도구입니다. 이 도구를 사용하면 SQL Server, MySQL, Oracle 등의 관계형 데이터베이스 관리 시스템(RDBMS)에서 Hadoop 분산형 파일 시스템(HDFS)으로 데이터를 가져오고, MapReduce 또는 Hive로 Hadoop의 데이터를 변환한 후 데이터를 RDBMS로 다시 내보낼 수 있습니다. 자세한 내용은 [Sqoop 사용자 가이드][apache-sqoop-guide](영문)를 참조하십시오.

데이터를 가져오기 전에 Azure SQL 데이터베이스를 호스팅하는 서버 이름, 데이터베이스 계정 이름, 계정 암호 및 데이터베이스 이름을 알아야 합니다.

기본적으로 Azure SQL 데이터베이스는 Azure HDInsight 같은 Azure 서비스로부터의 연결을 허용합니다. 이 방화벽 설정을 사용하지 않도록 설정한 경우 Azure 포털에서 사용하도록 설정해야 합니다. Azure SQL 데이터베이스 만들기 및 방화벽 규칙 구성에 대한 지침은 [SQL 데이터베이스 만들기 및 구성][sqldatabase-create-configure]을 참조하십시오.

다음 절차에서는 Azure PowerShell을 사용하여 Sqoop 작업을 제출합니다.

**Sqoop 및 Azure PowerShell을 사용하여 HDInsight로 데이터를 가져오려면**

1. [Azure PowerShell 설치 및 구성](powershell-install-configure.md)의 지침에 따라 Azure PowerShell 콘솔을 엽니다.
2. 다음 스크립트에서 처음 8개의 변수 값을 설정합니다.

		$subscriptionName = "<AzureSubscriptionName>"
		$clusterName = "<HDInsightClusterName>"
		
		$sqlDatabaseServerName = "<SQLDatabaseServerName>"
		$sqlDatabaseUserName = "<SQLDatabaseDatabaseName>"
		$sqlDatabasePassword = "<SQLDatabasePassword>"
		$sqlDatabaseDatabaseName = "<SQLDatabaseDatabaseName>"
		$tableName = "<SQLDatabaseTableName>"
		
		$hdfsOutputDir = "<OutputPath>"  # This is the HDFS path for the output file, for example "/lineItemData".
		
		Select-AzureSubscription $subscriptionName		
		$sqoopDef = New-AzureHDInsightSqoopJobDefinition -Command "import --connect jdbc:sqlserver://$sqlDatabaseServerName.database.windows.net;user=$sqlDatabaseUserName@$sqlDatabaseServerName;password=$sqlDatabasePassword;database=$sqlDatabaseDatabaseName --table $tableName --target-dir $hdfsOutputDir -m 1" 

		$sqoopJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $sqoopDef #-Debug -Verbose
		Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 -Job $sqoopJob
		
		Write-Host "Standard Error" -BackgroundColor Green
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardError
		Write-Host "Standard Output" -BackgroundColor Green
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardOutput

3. Azure PowerShell 콘솔에 스크립트를 붙여넣고 실행합니다. Azure Blob 저장소에서 데이터 파일을 검색하기 위한 샘플을 보려면 [HDInsight 시작][hdinsight-get-started]을 참조하십시오.

Sqoop 사용에 대한 자세한 내용은 [HDInsight에서 Sqoop 사용][hdinsight-use-sqoop]을 참조하십시오.

## 다음 단계
이제 HDInsight로 데이터를 가져오는 방법을 익혔으니 다음 문서를 읽고 분석을 수행하는 방법을 알아보십시오:

* [Azure HDInsight 시작][hdinsight-get-started]
* [프로그래밍 방식으로 Hadoop 작업 제출][hdinsight-submit-jobs]
* [HDInsight에서 Hive 사용][hdinsight-use-hive]
* [HDInsight에서 Pig 사용][hdinsight-use-pig]




[azure-management-portal]: https://manage.windowsazure.com
[azure-powershell]: http://msdn.microsoft.com/library/windowsazure/jj152841.aspx

[azure-storage-client-library]: /develop/net/how-to-guides/blob-storage/
[azure-create-storage-account]: storage-create-storage-account.md
[azure-azcopy-download]: storage-use-azcopy.md
[azure-azcopy]: storage-use-azcopy.md

[hdinsight-use-sqoop]: hdinsight-use-sqoop.md

[hdinsight-storage]: hdinsight-use-blob-storage.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-get-started]: hdinsight-get-started.md

[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-provision]: hdinsight-provision-clusters.md

[sqldatabase-create-configure]: sql-database-create-configure.md

[apache-sqoop-guide]: http://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html

[Powershell-install-configure]: powershell-install-configure.md

[xplatcli]: xplat-cli.md


[image-azure-storage-explorer]: ./media/hdinsight-upload-data/HDI.AzureStorageExplorer.png
[image-ase-addaccount]: ./media/hdinsight-upload-data/HDI.ASEAddAccount.png
[image-ase-blob]: ./media/hdinsight-upload-data/HDI.ASEBlob.png


<!--HONumber=54-->