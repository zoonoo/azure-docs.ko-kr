<properties 
	pageTitle="플랫폼 간 명령줄을 사용하여 Hadoop 클러스터 관리 | Azure" 
	description="크로스 플랫폼 명령줄 인터페이스를 사용하여 Windows, Mac, Linux 등 Node.js를 지원하는 플랫폼의 HDinsight에서 Hadoop 클러스터를 관리하는 방법에 대해 알아봅니다.." 
	services="hdinsight" 
	editor="cgronlun" 
	manager="paulettm" 
	authors="mumian" 
	documentationCenter=""/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/21/2014" 
	ms.author="jgao"/>

# 크로스 플랫폼 명령줄 인터페이스를 사용하여 HDInsight에서 Hadoop 클러스터 관리

이 문서에서는 크로스 플랫폼 명령줄 인터페이스를 사용하여 HDInsight에서 Hadoop 클러스터를 관리하는 방법에 대해 알아봅니다. 명령줄 도구는Node.js로 구현되며 Windows, Mac, Linux를 포함하여 Node.js를 지원하는 플랫폼에서 사용할 수 있습니다. 

명령줄 도구는 오픈 소스입니다.  소스 코드는 GitHub에서 <a href= "https://github.com/WindowsAzure/azure-sdk-tools-xplat">https://github.com/WindowsAzure/azure-sdk-tools-xplat</a>관리됩니다. 

이 문서에서는 Windows에서 명령줄 인터페이스를 사용하는 방법만 다룹니다. 명령줄 인터페이스 사용 방법에 대한 일반적인 설명은 [Mac 및 Linux에서 Azure 명령줄 도구를 사용하는 방법][azure-command-line-tools]을 참조하세요. 포괄적인 참조 설명서는 [Mac 및 Linux용 Azure 명령줄 도구][azure-command-line-tool]를 참조하세요.


**필수 조건:**

이 문서를 시작하기 전에 다음이 있어야 합니다.

- **Azure 구독**. Azure는 구독 기반 플랫폼입니다. 구독을 예약하는 방법에 대한 자세한 내용은 [구매 옵션][azure-purchase-options], [구성원 제공 항목][azure-member-offers] 또는 [무료 평가판][azure-free-trial]을 참조하세요.

##이 문서에서는 다음을 수행합니다.

* [설치](#installation)
* [Azure 계정 publishsettings 다운로드 및 가져오기](#importsettings)
* [클러스터 프로비전](#provision)
* [구성 파일을 사용하여 클러스터 프로비전](#provisionconfigfile)
* [클러스터 나열 및 표시](#listshow)
* [클러스터 삭제](#delete)
* [다음 단계](#nextsteps)

##<a id="installation"></a> 설치
명령줄 인터페이스는  *Node.js Package Manager (NPM)* 또는 Windows Installer를 사용하여 설치할 수 있습니다.

**NPM을 사용하여 명령줄 인터페이스를 설치하려면**

1.	**www.nodejs.org**로 이동합니다.
2.	**INSTALL**을 클릭하고 기본 설정을 사용하여 지침을 따릅니다.
3.	워크스테이션에서 **명령 프롬프트**(또는 *Azure Command Prompt* 또는  *Developer Command Prompt for VS2012*)를 엽니다.
4.	명령 프롬프트 창에서 다음 명령을 실행합니다.

		npm install -g azure-cli

	> [AZURE.NOTE] NPM 명령을 찾을 수 없다는 오류 메시지가 나타나는 경우 PATH 환경 변수에 있는 다음 경로를 확인합니다. <i>C:\Program Files (x86)\nodejs;C:\Users\[username]\AppData\Roaming\npm</i> 또는 <i>C:\Program Files\nodejs;C:\Users\[username]\AppData\Roaming\npm</i>

5.	다음 명령을 실행하여 설치를 확인합니다.

		azure hdinsight -h

	여러 수준에서 *-h* 스위치를 사용하여 도움말 정보를 표시할 수 있습니다.  예를 들면 다음과 같습니다.
		
		azure -h
		azure hdinsight -h
		azure hdinsight cluster -h
		azure hdinsight cluster create -h

**Windows Installer를 사용하여 명령줄 인터페이스를 설치하려면**

1.	**http://azure.microsoft.com/ko-kr/downloads/**로 이동합니다.
2.	**명령줄 도구** 섹션으로 스크롤하여 **크로스 플랫폼 명령줄 인터페이스**를 클릭하고 웹 플랫폼 설치 관리자 마법사를 따릅니다.

##<a id="importsettings"></a> Azure 계정 publishsettings 다운로드 및 가져오기

명령줄 인터페이스를 사용하려면 먼저 워크스테이션과 Azure 사이의 연결을 구성해야 합니다. Azure 구독 정보는 명령줄 인터페이스에서 계정에 연결하는 데 사용됩니다. 이 정보는 Azure의 publishsettings 파일에서 가져올 수 있습니다. 그런 다음 publishsettings 파일을 영구적 로컬 구성 설정으로 가져와서 명령줄 인터페이스에서 후속 작업에 사용할 수 있습니다. publishsettings는 한 번만 가져오면 됩니다.

> [AZURE.NOTE] publishsettings 파일에는 중요 정보가 포함되어 있습니다. 파일을 삭제하거나 파일이 포함된 사용자 폴더를 암호화하는 추가 단계를 수행하는 것이 좋습니다. Windows에서 폴더 속성을 수정하거나 BitLocker를 사용합니다.


**publishsettings를 다운로드하고 가져오려면**

1.	**명령 프롬프트**를 엽니다.
2.	다음 명령을 실행하여 publishsettings 파일을 다운로드합니다.

		azure account download
 
	![HDI.CLIAccountDownloadImport][image-cli-account-download-import]

	이 명령은 URL을 비롯하여 파일 다운로드 지침을 보여 줍니다.

3.	**Internet Explorer**를 열고 명령 프롬프트 창에 나열된 URL로 이동합니다.
4.	**저장**을 클릭하여 워크스테이션에 파일을 저장합니다.
5.	명령 프롬프트 창에서 다음 명령을 실행하여 publishsettings 파일을 가져옵니다.

		azure account import <file>

	이전 스크린샷에서 publishsettings 파일은 워크스테이션의 C:\HDInsight 폴더에 저장되었습니다.


##<a id="provision"></a> HDInsight 클러스터 프로비전

[AZURE.INCLUDE [provisioningnote](../includes/hdinsight-provisioning.md)]


HDInsight는 Azure Blob 저장소 컨테이너를 기본 파일 시스템으로 사용합니다. HDInsight 클러스터를 만들려면 먼저 Azure 저장소 계정이 필요합니다. 

publishsettings 파일을 가져온 후 다음 명령을 사용하여 저장소 계정을 만들 수 있습니다.

	azure account storage create [options] <StorageAccountName>


> [AZURE.NOTE] 저장소 계정은 동일한 데이터 센터에 배치해야 합니다. 현재 다음 데이터 센터에서만 HDInsight 클러스터를 프로비전할 수 있습니다.

><ul>
<li>동남아시아</li>
<li>북유럽</li>
<li>서유럽</li>
<li>미국 동부</li>
<li>미국 서부</li>
</ul>


Azure 관리 포털을 사용하여 Azure 저장소 계정을 만드는 방법에 대한 자세한 내용은 [저장소 계정 만들기, 관리 또는 삭제][azure-create-storageaccount]를 참조하세요.

저장소 계정이 이미 있지만 계정 이름과 계정 키를 모르는 경우 다음 명령을 사용하여 정보를 검색할 수 있습니다.

	-- lists storage accounts
	azure account storage list
	-- Shows a storage account
	azure account storage show <StorageAccountName>
	-- Lists the keys for a storage account
	azure account storage keys list <StorageAccountName>

관리 포털을 사용하여 정보를 가져오는 방법에 대한 자세한 내용은 [저장소 계정 만들기, 관리 또는 삭제][azure-create-storageaccount]의 *방법: 저장소 액세스 키 보기, 복사 및 다시 생성* 섹션을 참조하세요.


 *azure hdinsight cluster create* 명령은 컨테이너가 존재하지 않는 경우 컨테이너를 만듭니다. 미리 컨테이너를 만들도록 선택하는 경우 다음 명령을 사용할 수 있습니다.

	azure storage container create --account-name <StorageAccountName> --account-key <StorageAccountKey> [ContainerName]
		
저장소 계정 및 Blob 컨테이너가 준비되면 다음과 같이 클러스터를 만들 준비가 된 것입니다. 

	azure hdinsight cluster create --clusterName <ClusterName> --storageAccountName <StorageAccountName> --storageAccountKey <storageAccountKey> --storageContainer <StorageContainer> --nodes <NumberOfNodes> --location <DataCenterLocation> --username <HDInsightClusterUsername> --clusterPassword <HDInsightClusterPassword>

![HDI.CLIClusterCreation][image-cli-clustercreation]

















##<a id="provisionconfigfile"></a> 구성 파일을 사용하여 HDInsight 클러스터 프로비전
일반적으로 HDInsight 클러스터를 프로비전하고 해당 클러스터에서 작업을 실행한 후에 비용을 줄이기 위해 클러스터를 삭제합니다. 명령줄 인터페이스에는 클러스터를 프로비전할 때마다 다시 사용할 수 있도록 구성을 파일에 저장하는 옵션이 있습니다.  
 
	azure hdinsight cluster config create <file>
	 
	azure hdinsight cluster config set <file> --clusterName <ClusterName> --nodes <NumberOfNodes> --location "<DataCenterLocation>" --storageAccountName "<StorageAccountName>.blob.core.windows.net" --storageAccountKey "<StorageAccountKey>" --storageContainer "<BlobContainerName>" --username "<Username>" --clusterPassword "<UserPassword>"
	 
	azure hdinsight cluster config storage add <file> --storageAccountName "<StorageAccountName>.blob.core.windows.net"
	       --storageAccountKey "<StorageAccountKey>"
	 
	azure hdinsight cluster config metastore set <file> --type "hive" --server "<SQLDatabaseName>.database.windows.net"
	       --database "<HiveDatabaseName>" --user "<Username>" --metastorePassword "<UserPassword>"
	 
	azure hdinsight cluster config metastore set <file> --type "oozie" --server "<SQLDatabaseName>.database.windows.net"
	       --database "<OozieDatabaseName>" --user "<SQLUsername>" --metastorePassword "<SQLPassword>"
	 
	azure hdinsight cluster create --config <file>
		 


![HDI.CLIClusterCreationConfig][image-cli-clustercreation-config]


##<a id="listshow"></a> 클러스터 세부 정보 나열 및 표시
클러스터 세부 정보를 나열하고 표시하려면 다음 명령을 사용합니다.
	
	azure hdinsight cluster list
	azure hdinsight cluster show <ClusterName>
	
![HDI.CLIListCluster][image-cli-clusterlisting]


##<a id="delete"></a> 클러스터 삭제
클러스터를 삭제하려면 다음 명령을 사용합니다.

	azure hdinsight cluster delete <ClusterName>




##<a id="nextsteps"></a> 다음 단계
이 문서에서는 HDInsight 클러스터 관리 작업을 수행하는 여러 방법에 대해 알아보았습니다. 자세한 내용은 다음 문서를 참조하세요.

* [관리 포털을 사용하여 HDInsight 관리][hdinsight-admin-portal]
* [PowerShell을 사용하여 HDInsight 관리][hdinsight-admin-powershell]
* [Azure HDInsight 시작][hdinsight-get-started]
* [Mac 및 Linux에서 Azure 명령줄 도구를 사용하는 방법][azure-command-line-tools]
* [Mac 및 Linux용 Azure 명령줄 도구][azure-command-line-tool]


[azure-command-line-tools]: ../xplat-cli/
[azure-command-line-tool]: ../command-line-tools/
[azure-create-storageaccount]: ../storage-create-storage-account/ 
[azure-purchase-options]: http://azure.microsoft.com/ko-kr/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/ko-kr/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/ko-kr/pricing/free-trial/


[hdinsight-admin-portal]: ../hdinsight-administer-use-management-portal/
[hdinsight-admin-powershell]: ../hdinsight-administer-use-powershell/
[hdinsight-get-started]: ../hdinsight-get-started/

[image-cli-account-download-import]: ./media/hdinsight-administer-use-command-line/HDI.CLIAccountDownloadImport.png 
[image-cli-clustercreation]: ./media/hdinsight-administer-use-command-line/HDI.CLIClusterCreation.png
[image-cli-clustercreation-config]: ./media/hdinsight-administer-use-command-line/HDI.CLIClusterCreationConfig.png
[image-cli-clusterlisting]: ./media/hdinsight-administer-use-command-line/HDI.CLIListClusters.png "List and show clusters"

<!--HONumber=42-->
