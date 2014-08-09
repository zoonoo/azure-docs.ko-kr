<properties linkid="manage-services-hdinsight-get-started-hdinsight" urlDisplayName="Get Started" pageTitle="Get started using HDInsight | Azure" metaKeywords="" description="Get started with HDInsight, a big data solution. Learn how to provision clusters, run MapReduce jobs, and output data to Excel for analysis." metaCanonical="" services="hdinsight" documentationCenter="" title="Get started using Azure HDInsight" authors="jgao" solutions="" manager="paulettm" editor="cgronlun" />

Azure HDInsight를 사용하여 시작
===============================

HDInsight는 클라우드에서 [Apache Hadoop](http://hadoop.apache.org/)을 서비스로 사용할 수 있게 해 줍니다. HDInsight를 통해 더 단순하고 확장 가능하며 비용 효과적인 Azure 환경에서 MapReduce 소프트웨어 프레임워크를 사용할 수 있습니다. 또한 HDInsight는 Azure Blob 저장소를 사용하여 데이터를 관리 및 저장하는 비용 효과적인 방법을 제공합니다.

이 자습서에서는 Azure 관리 포털을 사용하여 HDInsight 클러스터를 프로비전하고 PowerShell을 사용하여 Hadoop MapReduce 작업을 제출한 후 검사를 위해 MapReduce 작업 출력 데이터를 Excel로 가져옵니다.

> [WACOM.NOTE]
> 이 자습서는 HDInsight에서 Hadoop 1.2 클러스터를 사용하는 경우를 다룹니다. HDInsight에서 Hadoop 2.2 클러스터를 사용하는 경우에 대한 자습서는 [HDInsight와 함께 Hadoop 2.2 클러스터를 사용하여 시작](/ko-kr/documentation/articles/hdinsight-get-started-30/)을 참조하십시오.

Azure HDInsight의 일반적인 가용성과 더불어 Microsoft는 Azure용 HDInsight Emulator(이전의 Microsoft HDInsight Developer Preview)도 릴리스했습니다. 이 제품은 개발자 시나리오를 대상으로 하기 때문에 단일 노드 배포만 지원합니다. HDInsight Emulator 사용에 대한 자세한 내용은 [HDInsight Emulator 시작](/ko-kr/documentation/articles/hdinsight-get-started-emulator/)을 참조하십시오.

**필수 구성 요소:**

이 자습서를 시작하기 전에 다음이 있어야 합니다.

-   Azure 구독. 구독을 얻는 방법에 대한 자세한 내용은 [구매 옵션](https://www.windowsazure.com/en-us/pricing/purchase-options/), [구성원 제공 항목](https://www.windowsazure.com/en-us/pricing/member-offers/) 또는 [무료 평가판](https://www.windowsazure.com/en-us/pricing/free-trial/)을 참조하십시오.
-   Windows 8, Windows 7, Windows Server 2012 또는 Windows Server 2008 R2를 실행하는 컴퓨터. 이 컴퓨터는 MapReduce 작업을 제출하는 데 사용됩니다.
-   Office 2013 Professional Plus, Office 365 Pro Plus, Excel 2013 Standalone 또는 Office 2010 Professional Plus

**예상 완료 시간:** 30분

자습서 내용
-----------

-   [PowerShell 실행을 위한 로컬 환경 설정](#setup)
-   [HDInsight 클러스터 프로비전](#provision)
-   [WordCount MapReduce 프로그램 실행](#sample)
-   [Microsoft 비즈니스 인텔리전스 도구에 연결](#powerquery)
-   [다음 단계](#nextsteps)

PowerShell 실행을 위한 로컬 환경 설정
-------------------------------------

MapReduce 작업을 HDInsight에 제출하는 몇 가지 방법이 있습니다. 이 자습서에서는 Azure PowerShell을 사용합니다. Azure PowerShell을 설치하려면 [Microsoft 웹 플랫폼 설치 관리자](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409)를 실행합니다. **실행**을 클릭하고 메시지가 표시되면 **설치**를 클릭한 후 지침을 따릅니다. 자세한 내용은 [Azure PowerShell 설치 및 구성](/ko-kr/documentation/articles/install-configure-powershell/)을 참조하십시오.

서비스 관리에 사용할 수 있도록 PowerShell cmdlet에 구독 정보가 필요합니다.

**Azure AD를 사용하여 구독에 연결하려면**

1.  [방법: Azure PowerShell 설치](/ko-kr/documentation/articles/install-configure-powershell/#install)의 지침에 따라 Azure PowerShell 콘솔을 엽니다.
2.  다음 명령을 실행합니다.

         Add-AzureAccount

3.  창에서 계정과 연결된 전자 메일 주소 및 암호를 입력합니다. Azure가 자격 증명 정보를 인증 및 저장한 후 창을 닫습니다.

구독에 연결하는 다른 방법은 인증서 방법을 사용하는 것입니다. 지침은 [Azure PowerShell 설치 및 구성](/ko-kr/documentation/articles/install-configure-powershell/)을 참조하십시오.

HDInsight 클러스터 프로비전
---------------------------

HDInsight 프로비전 프로세스에서는 Azure 저장소 계정을 기본 파일 시스템으로 사용해야 합니다. 저장소 계정은 HDInsight 컴퓨터 리소스와 동일한 데이터 센터에 있어야 합니다. 현재 다음 데이터 센터에서만 HDInsight 클러스터를 프로비전할 수 있습니다.

-   동남 아시아
-   북유럽
-   서유럽
-   미국 동부
-   미국 서부

Azure 저장소 계정에 대해 5개 데이터 센터 중 하나를 선택해야 합니다.

**Azure 저장소 계정을 만들려면**

1.  [Azure 관리 포털](https://manage.windowsazure.com/)에 로그인합니다.
2.  왼쪽 아래에서 **새로 만들기**를 클릭하고 **데이터 서비스**, **저장소**를 차례로 가리킨 후 **빠른 생성**을 클릭합니다.

    ![HDI.StorageAccount.QuickCreate](./media/hdinsight-get-started/HDI.StorageAccount.QuickCreate.png)

3.  **URL**, **위치** 및 **복제**를 입력하고 **저장소 계정 만들기**를 클릭합니다. 선호도 그룹은 지원되지 않습니다. 저장소 목록에 새 저장소 계정이 표시됩니다.
4.  새 저장소 계정의 **상태**가 **온라인**으로 변경될 때까지 기다립니다.
5.  목록에서 새 저장소 계정을 클릭하여 선택합니다.
6.  페이지 맨 아래에서 **액세스 키 관리**를 클릭합니다.
7.  **저장소 계정 이름** 및 **기본 액세스 키**를 적어 둡니다. 이 정보는 자습서의 뒷부분에서 필요합니다.

자세한 지침은 [저장소 계정을 만드는 방법](/ko-kr/documentation/articles/storage-create-storage-account/) 및 [HDInsight와 함께 Azure Blob 저장소 사용](/ko-kr/documentation/articles/hdinsight-use-blob-storage/)을 참조하십시오.

**HDInsight 클러스터를 프로비전하려면**

1.  [Azure 관리 포털](https://manage.windowsazure.com/)에 로그인합니다.

2.  왼쪽에서 **HDInsight**를 클릭하여 계정의 클러스터 상태를 나열합니다. 다음 스크린샷에는 기존 HDInsight 클러스터가 없습니다.

    ![HDI.ClusterStatus](./media/hdinsight-get-started/HDI.ClusterStatus.png)

3.  왼쪽 아래에서 **새로 만들기**를 클릭한 후 **데이터 서비스**, **HDInsight**, **빠른 생성**을 차례로 클릭합니다.

    ![HDI.QuickCreateCluster](./media/hdinsight-get-started/HDI.QuickCreateCluster.png)

4.  다음 값을 입력하거나 선택합니다.

    <table border="1">
    <tr>
      <th>이름</th>
      <th>값</th>
    </tr>
    <tr>
      <td>클러스터 이름</td>
      <td>클러스터의 이름입니다.</td>
    </tr>
    <tr>
	  <td>클러스터 크기</td>
	  <td>배포하려는 데이터 노드 수입니다. 기본값은 4개입니다. 그러나 드롭다운 메뉴에서 8, 16 및 32개 데이터 노드 클러스터를 사용할 수도 있습니다. <strong>사용자 지정 만들기</strong> 옵션을 사용하는 경우 데이터 노드 수를 임의로 지정할 수 있습니다. 다양한 클러스터 크기의 청구 요금에 대한 가격 책정 정보를 사용할 수 있습니다. 드롭다운 상자 위의 <strong>?</strong> 기호를 클릭하고 팝업의 링크를 따르십시오.</td>
    </tr>
    </tbody>
    </table>

5.  오른쪽 아래에서 **HDInsight 클러스터 만들기**를 클릭합니다. 프로비전 프로세스가 완료되면 상태 열에 **실행 중**이 표시됩니다.

**사용자 지정 만들기** 옵션 사용에 대한 자세한 내용은 [HDInsight 클러스터 프로비전](/ko-kr/documentation/articles/hdinsight-provision-clusters/)을 참조하십시오.

WordCount MapReduce 작업 실행
-----------------------------

이제 HDInsight 클러스터가 프로비전되었습니다. 다음 단계는 MapReduce 작업을 실행하여 텍스트 파일의 단어 수를 계산하는 것입니다.

MapReduce 작업을 실행하려면 다음 요소가 필요합니다.

-   MapReduce 프로그램. 이 자습서에서는 직접 작성할 필요가 없도록 HDInsight 클러스터 배포와 함께 제공된 WordCount 샘플을 사용합니다. 이 샘플은 */example/jars/hadoop-examples.jar*에 있습니다. 고유한 MapReduce 작업을 작성하는 방법에 대한 지침은 [HDInsight용 Java MapReduce 프로그램 개발](/ko-kr/documentation/articles/hdinsight-develop-deploy-java-mapreduce/)을 참조하십시오.

-   입력 파일. */example/data/gutenberg/davinci.txt*를 입력 파일로 사용합니다. 파일 업로드에 대한 자세한 내용은 [HDInsight에 데이터 업로드](/ko-kr/documentation/articles/hdinsight-upload-data/)를 참조하십시오.
-   출력 파일 폴더. */example/data/WordCountOutput*을 출력 파일 폴더로 사용합니다. 폴더가 없는 경우 시스템에서 새로 만듭니다.

Blob 저장소의 파일에 액세스하기 위한 URI 체계는 다음과 같습니다.

    wasb[s]://<containername>@<storageaccountname>.blob.core.windows.net/<path>

> [WACOM.NOTE] 기본 파일 시스템에 사용되는 Blob 컨테이너는 기본적으로 HDInsight 클러스터와 이름이 같습니다.

URI 체계는 *wasb:* 접두사를 사용한 암호화되지 않은 액세스와 WASBS를 사용한 SSL 암호화된 액세스를 둘 다 제공합니다. 동일한 Azure 데이터 센터에 있는 데이터에 액세스하는 경우에도 가능하면 wasbs를 사용하는 것이 좋습니다.

HDInsight는 Blob 저장소 컨테이너를 기본 파일 시스템으로 사용하기 때문에 상대 또는 절대 경로를 사용하여 기본 파일 시스템 내의 파일과 디렉터리를 참조할 수 있습니다.

예를 들어 hadoop-examples.jar에 액세스하려면 다음 옵션 중 하나를 사용할 수 있습니다.

    ● wasb://<containername>@<storageaccountname>.blob.core.windows.net/example/jars/hadoop-examples.jar
    ● wasb:///example/jars/hadoop-examples.jar
    ● /example/jars/hadoop-examples.jar

이러한 파일의 경로에 *wasb://* 접두사 사용. Azure Blob 저장소가 입력 및 출력 파일에 사용됨을 나타내는 데 필요합니다. 출력 디렉터리는 *wasb:///user/&lt;username\>* 폴더에 상대적인 기본 경로를 사용합니다.

자세한 내용은 [HDInsight와 함께 Azure Blob 저장소 사용](/ko-kr/documentation/articles/hdinsight-use-blob-storage/)을 참조하십시오.

**WordCount 샘플을 실행하려면**

1.  **Azure PowerShell**을 엽니다. Azure PowerShell 콘솔 창을 여는 방법에 대한 지침은 [Azure PowerShell 설치 및 구성](/ko-kr/documentation/articles/install-configure-powershell/)을 참조하십시오.

2.  다음 명령을 실행하여 변수를 설정합니다.

         $subscriptionName = "<SubscriptionName>" 
         $clusterName = "<HDInsightClusterName>"        

3.  다음 명령을 실행하여 MapReduce 작업 정의를 만듭니다.

         # Define the MapReduce job
         $wordCountJobDefinition = New-AzureHDInsightMapReduceJobDefinition -JarFile "wasb:///example/jars/hadoop-examples.jar" -ClassName "wordcount" -Arguments "wasb:///example/data/gutenberg/davinci.txt", "wasb:///example/data/WordCountOutput"

    hadoop-examples.jar 파일은 HDInsight 클러스터 배포와 함께 제공됩니다. MapReduce 작업에 대한 두 개의 인수가 있습니다. 첫 번째 인수는 원본 파일 이름이고, 두 번째 인수는 출력 파일 경로입니다. 원본 파일은 HDInsight 클러스터 배포와 함께 제공되고 출력 파일 경로는 런타임 시 만들어집니다.

4.  다음 명령을 실행하여 MapReduce 작업을 제출합니다.

         # Submit the job
         Select-AzureSubscription $subscriptionName
         $wordCountJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $wordCountJobDefinition 

    MapReduce 작업 정의뿐 아니라 MapReduce 작업을 실행하려는 HDInsight 클러스터 이름도 제공해야 합니다.

    *Start-AzureHDInsightJob*은 비동기 호출입니다. 작업 완료를 확인하려면 *Wait-AzureHDInsightJob* cmdlet을 사용합니다.

5.  다음 명령을 실행하여 MapReduce 작업 완료를 확인합니다.

         Wait-AzureHDInsightJob -Job $wordCountJob -WaitTimeoutInSeconds 3600 

6.  다음 명령을 실행하여 MapReduce 작업 실행 오류를 확인합니다.

         # Get the job output
         Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $wordCountJob.JobId -StandardError

    다음 스크린샷은 성공한 실행 출력을 보여 줍니다. 그렇지 않으면 오류 메시지가 표시됩니다.

    ![HDI.GettingStarted.RunMRJob](./media/hdinsight-get-started/HDI.GettingStarted.RunMRJob.png)

**MapReduce 작업 결과를 검색하려면**

1.  **Azure PowerShell**을 엽니다.
2.  다음 명령을 실행하여 C:\\Tutorials 폴더를 만들고 디렉터리를 해당 폴더로 변경합니다.

         mkdir \Tutorials
         cd \Tutorials

    기본 Azure Powershell 디렉터리는 *C:\\Windows\\System32\\WindowsPowerShell\\v1.0*입니다. 기본적으로 이 폴더에 대한 쓰기 권한은 없습니다. 쓰기 권한이 있는 폴더로 디렉터리를 변경해야 합니다.

3.  다음 명령에 있는 변수 3개를 설정한 후 실행합니다.

         $subscriptionName = "<SubscriptionName>"       
         $storageAccountName = "<StorageAccountName>"   
         $containerName = "<ContainerName>"              

    Azure 저장소 계정은 자습서의 앞부분에서 만든 계정입니다. 저장소 계정은 기본 HDInsight 클러스터 파일 시스템으로 사용되는 Blob 컨테이너를 호스트하는 데 사용됩니다. 일반적으로 Blob 저장소 컨테이너 이름은 클러스터를 프로비전할 때 다른 이름을 지정하지 않을 경우 HDInsight 클러스터와 동일한 이름을 공유합니다.

4.  다음 명령을 실행하여 Azure 저장소 컨텍스트 개체를 만듭니다.

         # Create the storage account context object
         Select-AzureSubscription $subscriptionName
         $storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
         $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  

    *Select-AzureSubscription*은 여러 구독이 있을 경우 현재 구독을 설정하는 데 사용되며, 기본 구독은 사용할 구독이 아닙니다.

5.  다음 명령을 실행하여 Blob 컨테이너의 MapReduce 작업 출력을 워크스테이션으로 다운로드합니다.

         # Download the job output to the workstation
         Get-AzureStorageBlobContent -Container $ContainerName -Blob example/data/WordCountOutput/part-r-00000 -Context $storageContext -Force

    *example/data/WordCountOutput* 폴더는 MapReduce 작업을 실행할 때 지정된 출력 폴더입니다. *part-r-00000*은 MapReduce 작업 출력의 기본 파일 이름입니다. 로컬 폴더의 동일한 폴더 구조에 파일이 다운로드됩니다. 예를 들어 다음 스크린샷에서 현재 폴더는 C 루트 폴더입니다. *C:\\example\\data\\WordCountOutput\\* 폴더에 파일이 다운로드됩니다.

6.  다음 명령을 실행하여 MapReduce 작업 출력 파일을 인쇄합니다.

         cat ./example/data/WordCountOutput/part-r-00000 | findstr "there"

    ![HDI.GettingStarted.MRJobOutput](./media/hdinsight-get-started/HDI.GettingStarted.MRJobOutput.png)

    MapReduce 작업에서 단어와 개수를 사용하여 *part-r-00000*이라는 파일을 생성합니다. 스크립트는 findstr 명령을 사용하여 *"there"*가 포함된 모든 단어를 나열합니다.

> [WACOM.NOTE] MapReduce 작업의 여러 줄 출력인 *./example/data/WordCountOutput/part-r-00000*을 메모장에서 열면 줄 바꿈이 제대로 렌더링되지 않는 것을 확인할 수 있습니다. 이것은 예상된 동작입니다.

Microsoft 비즈니스 인텔리전스 도구에 연결
-----------------------------------------

Excel의 파워 쿼리 추가 기능을 사용하여 HDInsight의 출력을 Excel로 내보낼 수 있습니다. 그런 다음 Excel에서 Microsoft BI(비즈니스 인텔리전스) 도구를 사용하여 추가로 처리하거나 결과를 표시할 수 있습니다. HDInsight 클러스터를 만든 경우 클러스터와 이름이 같은 기본 컨테이너가 생성 시 연결된 저장소 계정에 만들어져 있습니다. 이 컨테이너에 자동으로 파일 집합이 채워집니다. 해당 파일 중 하나는 샘플 Hive 테이블입니다. 이 섹션에서는 조회 및 추가 처리를 위해 이 테이블에 포함된 데이터를 Excel로 가져오는 방법을 보여 줍니다.

이 자습서 부분을 완료하려면 Excel 2010 또는 2013이 설치되어 있어야 합니다. 여기서는 HDInsight에 포함된 기본 Hive 테이블을 가져옵니다.

**Excel용 Microsoft 파워 쿼리를 다운로드하려면**

-   [Microsoft 다운로드 센터](http://www.microsoft.com/en-us/download/details.aspx?id=39379)에서 Excel용 Microsoft 파워 쿼리를 다운로드하여 설치합니다.

**HDInsight 데이터를 가져오려면**

1.  Excel을 열고 새로운 빈 통합 문서를 만듭니다.
2.  **파워 쿼리** 메뉴를 클릭하고 **기타 원본**을 클릭한 후 **Azure HDInsight에서**를 클릭합니다.

    ![HDI.GettingStarted.PowerQuery.ImportData](./media/hdinsight-get-started/HDI.GettingStarted.PowerQuery.ImportData.png)

3.  클러스터와 연결된 Azure Blob 저장소 계정의 **계정 이름**을 입력하고 **확인**을 클릭합니다. 이 계정은 자습서의 앞부분에서 만든 저장소 계정입니다.
4.  Azure Blob 저장소 계정의 **계정 키**를 입력하고 **저장**을 클릭합니다.
5.  오른쪽의 탐색 창에서 Blob 저장소 컨테이너 이름을 두 번 클릭합니다. 기본적으로 컨테이너 이름은 클러스터 이름과 같습니다.

6.  **이름** 열에서 **part-r-00000**(경로는 *.../example/data/WordCountOutput*임)을 찾은 후 **part-r-00000** 왼쪽에 있는 **이진**을 클릭합니다.

    ![HDI.GettingStarted.PowerQuery.ImportData2](./media/hdinsight-get-started/HDI.GettingStarted.PowerQuery.ImportData2.png)

7.  **Column1.1**을 마우스 오른쪽 단추로 클릭하고 **이름 바꾸기**를 선택합니다.
8.  이름을 **Word**로 변경합니다.
9.  프로세스를 반복하여 이름 **Column1.2**를 **Count**로 바꿉니다.

    ![HDI.GettingStarted.PowerQuery.ImportData3](./media/hdinsight-get-started/HDI.GettingStarted.PowerQuery.ImportData3.png)

10. 왼쪽 위에서 **적용 후 닫기**를 클릭합니다. 그러면 쿼리에서 Hive 테이블을 Excel로 가져옵니다.

다음 단계
---------

이 자습서에서는 HDInsight를 사용하여 클러스터를 프로비전하고 클러스터에서 MapReduce 작업을 실행한 후 BI 도구를 사용하여 추가 처리하고 그래픽으로 표시할 수 있는 Excel로 결과를 가져오는 방법을 배웠습니다. 자세한 내용은 다음 문서를 참조하십시오.

-   [HDInsight와 함께 Hadoop 2.2 클러스터를 사용하여 시작](/ko-kr/documentation/articles/hdinsight-get-started-30/)
-   [HDInsight Emulator 시작](/ko-kr/documentation/articles/hdinsight-get-started-emulator/)
-   [HDInsight와 함께 Azure Blob 저장소 사용](/ko-kr/documentation/articles/hdinsight-use-blob-storage/)
-   [PowerShell을 사용하여 HDInsight 관리](/ko-kr/documentation/articles/hdinsight-administer-use-powershell/)
-   [HDInsight에 데이터 업로드](/ko-kr/documentation/articles/hdinsight-upload-data/)
-   [HDInsight와 함께 MapReduce 사용](/ko-kr/documentation/articles/hdinsight-use-mapreduce)
-   [HDInsight와 함께 Hive 사용](/ko-kr/documentation/articles/hdinsight-use-hive/)
-   [HDInsight와 함께 Pig 사용](/ko-kr/documentation/articles/hdinsight-use-pig/)
-   [HDInsight와 함께 Oozie 사용](/ko-kr/documentation/articles/hdinsight-use-oozie/)
-   [HDInsight용 C\# Hadoop 스트리밍 프로그램 개발](/ko-kr/documentation/articles/hdinsight-hadoop-develop-deploy-streaming-jobs/)
-   [HDInsight용 Java MapReduce 프로그램 개발](/ko-kr/documentation/articles/hdinsight-develop-deploy-java-mapreduce/)


[hdinsight-get-started-30]: /ko-kr/documentation/articles/hdinsight-get-started-30/
[hdinsight-provision]: /ko-kr/documentation/articles/hdinsight-provision-clusters/
[hdinsight-admin-powershell]: /ko-kr/documentation/articles/hdinsight-administer-use-powershell/
[hdinsight-upload-data]: /ko-kr/documentation/articles/hdinsight-upload-data/
[hdinsight-mapreduce]: /ko-kr/documentation/articles/hdinsight-use-mapreduce
[hdinsight-hive]: /ko-kr/documentation/articles/hdinsight-use-hive/
[hdinsight-pig]: /ko-kr/documentation/articles/hdinsight-use-pig/
[hdinsight-oozie]: /ko-kr/documentation/articles/hdinsight-use-oozie/
[hdinsight-storage]: /ko-kr/documentation/articles/hdinsight-use-blob-storage/
[hdinsight-emulator]: /ko-kr/documentation/articles/hdinsight-get-started-emulator/
[hdinsight-develop-streaming]: /ko-kr/documentation/articles/hdinsight-hadoop-develop-deploy-streaming-jobs/
[hdinsight-develop-mapreduce]: /ko-kr/documentation/articles/hdinsight-develop-deploy-java-mapreduce/

[azure-purchase-options]: https://www.windowsazure.com/en-us/pricing/purchase-options/
[azure-member-offers]: https://www.windowsazure.com/en-us/pricing/member-offers/
[azure-free-trial]: https://www.windowsazure.com/en-us/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: /ko-kr/documentation/articles/storage-create-storage-account/ 

[apache-hadoop]: http://hadoop.apache.org/

[powershell-download]: http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409
[powershell-install-configure]: /ko-kr/documentation/articles/install-configure-powershell/
[powershell-open]: /ko-kr/documentation/articles/install-configure-powershell/#install

[image-hdi-storageaccount-quickcreate]: ./media/hdinsight-get-started/HDI.StorageAccount.QuickCreate.png
[image-hdi-clusterstatus]: ./media/hdinsight-get-started/HDI.ClusterStatus.png
[image-hdi-quickcreatecluster]: ./media/hdinsight-get-started/HDI.QuickCreateCluster.png
[image-hdi-wordcountdiagram]: ./media/hdinsight-get-started/HDI.WordCountDiagram.gif
[image-hdi-gettingstarted-mrjoboutput]: ./media/hdinsight-get-started/HDI.GettingStarted.MRJobOutput.png
[image-hdi-gettingstarted-runmrjob]: ./media/hdinsight-get-started/HDI.GettingStarted.RunMRJob.png
[image-hdi-gettingstarted-powerquery-importdata]: ./media/hdinsight-get-started/HDI.GettingStarted.PowerQuery.ImportData.png
[image-hdi-gettingstarted-powerquery-importdata2]: ./media/hdinsight-get-started/HDI.GettingStarted.PowerQuery.ImportData2.png
[image-hdi-gettingstarted-powerquery-importdata3]: ./media/hdinsight-get-started/HDI.GettingStarted.PowerQuery.ImportData3.png