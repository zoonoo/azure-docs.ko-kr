<properties urlDisplayName="HDInsight Administration" pageTitle="HDInsight에서 Hadoop 클러스터 프로비전 | Azure" metaKeywords="hdinsight, hdinsight administration, hdinsight administration azure" description="관리 포털, PowerShell 또는 명령줄을 사용하여 Azure HDInsight용 클러스터를 프로비전하는 방법에 대해 알아봅니다." umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" services="hdinsight" documentationCenter="" title="HDInsight에서 Hadoop 클러스터 프로비전" authors="jgao" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/25/2014" ms.author="jgao" />

# 사용자 지정 옵션을 사용하여 HDInsight의 Hadoop 클러스터 프로비전

이 문서에서는 Hadoop cluster Azure 관리 포털, PowerShell, 명령줄 도구 또는 HDInsight .NET SDK를 사용하여 Azure HDInsight에서 사용자 지정 방식으로 Hadoop 클러스터를 프로비전하는 다양한 방법을 알아봅니다. 이 문서에서는 Hadoop 클러스터 프로비전에 대해 설명합니다. HBase 클러스터를 프로비전하는 방법에 대한 지침은 [HDInsight에서 HBase 클러스터 프로비전][HDInsight에서 HBase 클러스터 프로비전](영문)을 참조하세요. [Hadoop와 HBase 간 차이점][Hadoop와 HBase 간 차이점](영문)을 참조하여 두 가지 기능 중 하나를 선택하게 되는 이유를 이해할 수 있습니다.

## HDInsight 클러스터의 정의

Hadoop 또는 BigData에 대해 이야기할 때마다 클러스터를 언급한 이유가 궁금하셨나요? 그것은 바로 Hadoop을 사용하면 여러 다른 클러스터 노드에 분산된 대규모 데이터를 처리할 수 있기 때문입니다. 클러스터는 하나의 마스터(헤드 노드 또는 이름 노드라고도 부름)와 개수에 관계없는 슬레이브(데이터 노드라고도 부름)가 있는 마스터/슬레이브 아키텍처를 갖습니다. 자세한 내용은 [Apache Hadoop][Apache Hadoop]을 참조하세요.

![HDInsight 클러스터][HDInsight 클러스터]

HDInsight 클러스터는 Hadoop 구현 세부 정보를 추출하므로 여러 다른 클러스터 노드와 통신하는 방법을 걱정할 필요가 없습니다. HDInsight 클러스터를 프로비전할 때는 Hadoop과 관련 응용 프로그램을 포함하는 Azure 계산 리소스를 프로비전합니다. 자세한 내용은 [HDInsight의 Hadoop 소개][HDInsight의 Hadoop 소개](영문)를 참조하세요. 변동 대상 데이터는 HDInsight의 컨텍스트에서 Azure Blob 저장소(*Azure 저장소 - Blob* 또는 WASB라고도 함)에 저장됩니다. 자세한 내용은 [HDInsight에서 Azure Blob 저장소 사용][HDInsight에서 Azure Blob 저장소 사용]을 참조하세요.

이 문서에서는 클러스터를 프로비전하는 여러 다른 방법에 대한 지침을 제공합니다. 클러스터를 프로비전하는 빠른 시작 접근 방법을 원하는 경우 [Azure HDInsight 시작][Azure HDInsight 시작](영문)을 참조하세요.

**필수 조건:**

이 문서를 시작하기 전에 다음이 있어야 합니다.

-   Azure 구독. Azure는 구독 기반 플랫폼입니다. HDInsight PowerShell cmdlet은 구독을 사용하여 작업을 수행합니다. 구독을 얻는 방법에 대한 자세한 내용은 [구매 옵션][구매 옵션], [구성원 제공 항목][구성원 제공 항목] 또는 [무료 평가판][무료 평가판]을 참조하세요.

## 이 문서에서는 다음을 수행합니다.

-   [구성 옵션][구성 옵션]
-   [Azure 관리 포털 사용][Azure 관리 포털 사용]
-   [Azure PowerShell 사용][Azure PowerShell 사용]
-   [플랫폼 간 명령줄 사용][플랫폼 간 명령줄 사용]
-   [HDInsight .NET SDK 사용][HDInsight .NET SDK 사용]
-   [다음 단계][다음 단계]

## <span id="configuration"></span></a>구성 옵션

### 저장소 추가

구성 중에는 Azure Blob 저장소 계정과 기본 컨테이너를 지정해야 합니다. 이 컨테이너는 클러스터에서 기본 저장소 위치로 사용됩니다. 필요한 경우 역시 클러스터에 연결되는 추가 Blob도 지정할 수 있습니다.

보조 Blob 저장소 사용에 대한 자세한 내용은 [HDInsight에서 Azure Blob 저장소 사용][1]을 참조하세요.

### 메타 저장소

메타 저장소는 Hive 테이블, 파티션, 스키마, 열 등에 대한 정보를 포함합니다. 이 정보는 Hive가 HDFS 또는 HDInsight용 WASB에서 데이터가 저장되는 위치를 찾는 데 사용됩니다. Hive는 기본적으로 포함된 데이터베이스를 사용하여 이 정보를 저장합니다.

HDInsight 클러스터를 프로비전할 때는 Hive용 메타 저장소를 포함할 SQL 데이터베이스를 지정할 수 있습니다. 그러면 메타데이터 정보가 SQL 데이터베이스에 외부 저장되므로 클러스터를 삭제해도 해당 정보를 보존할 수 있습니다.

### 가상 네트워킹

[Azure 가상 네트워크][Azure 가상 네트워크]에서는 솔루션에 필요한 리소스를 포함하는 안전한 영구 네트워크를 만들 수 있습니다. 가상 네트워크를 통해 다음을 수행할 수 있습니다.

-   개인 네트워크(클라우드 전용)에서 클라우드 리소스 연결

    ![클라우드 전용 구성 다이어그램][클라우드 전용 구성 다이어그램]

-   VPN(가상 사설망)을 사용하여 클라우드 리소스를 로컬 데이터 센터 네트워크에 연결(사이트 간 또는 지점 및 사이트 간)

    사이트 간 구성에서는 하드웨어 VPN 또는 라우팅 및 원격 액세스 서비스를 사용하여 데이터 센터의 여러 리소스를 Azure 가상 네트워크에 연결할 수 있습니다.

    ![사이트 간 구성 다이어그램][사이트 간 구성 다이어그램]

    지점 및 사이트 간 구성에서는 소프트웨어 VPN을 사용하여 Azure 가상 네트워크에 특정 리소스를 연결할 수 있습니다.

    ![지점 및 사이트 간 구성 다이어그램][지점 및 사이트 간 구성 다이어그램]

가상 네트워크의 기능과 이점에 대한 자세한 내용은 [Azure 가상 네트워크 개요][Azure 가상 네트워크 개요]를 참조하세요.

> [WACOM.NOTE] HDInsight 클러스터를 프로비전하기 전에 Azure 가상 네트워크를 만들어야 합니다. 자세한 내용은 [가상 네트워크 구성 작업][가상 네트워크 구성 작업]을 참조하세요.
>
> Azure HDInsight는 위치 기반 가상 네트워크만 지원하며 현재 선호도 그룹 기반 가상 네트워크와는 연동되지 않습니다.

> [WACOM.NOTE] 각 클러스터에 단일 서브넷을 지정하는 것이 좋습니다.

## <span id="portal"></span></a> Azure 관리 포털 사용

HDInsight 클러스터는 Azure Blob 저장소 컨테이너를 기본 파일 시스템으로 사용합니다. HDInsight 클러스터를 만들려면 먼저 동일한 데이터 센터에 위치한 Azure 저장소 계정이 필요합니다. 자세한 내용은 [HDInsight에서 Azure Blob 저장소 사용][HDInsight에서 Azure Blob 저장소 사용]을 참조하세요. Azure 저장소 계정 만들기에 대한 자세한 내용은 [저장소 계정을 만드는 방법][저장소 계정을 만드는 방법]을 참조하세요.

> [WACOM.NOTE] 현재 **동아시아**, **동남아시아**, **북유럽**, **서유럽**, **미국 동부**, **미국 서부**, **미국 북중부**, **미국 남중부** 지역에서만 HDInsight 클러스터를 호스트할 수 있습니다.

**사용자 지정 만들기 옵션을 사용하여 HDInsight 클러스터를 만들려면**

1.  [Azure 관리 포털][Azure 관리 포털]에 로그인합니다.
2.  페이지 아래에서 **+ 새로 만들기**를 클릭한 후 **데이터 서비스**, **HDInsight**, **사용자 지정 만들기**를 차례로 클릭합니다.
3.  **클러스터 세부 정보** 페이지에서 다음 값을 입력하거나 선택합니다.

    ![HDI.CustomCreateCluster][HDI.CustomCreateCluster]

    <table>
    <colgroup>
    <col width="50%" />
    <col width="50%" />
    </colgroup>
    <thead>
    <tr class="header">
    <th align="left">속성</th>
    <th align="left">값</th>
    </tr>
    </thead>
    <tbody>
    <tr class="odd">
    <td align="left">클러스터 이름</td>
    <td align="left"><p>클러스터의 이름</p>
    <ul>
    <li>DNS 이름은 영숫자로 시작되고 끝나야 하며 대시를 포함할 수 있습니다.</li>
    <li>필드는 3자에서 63자의 문자열이어야 합니다.</li>
    </ul></td>
    </tr>
    <tr class="even">
    <td align="left">클러스터 유형</td>
    <td align="left">클러스터 유형으로 <strong>Hadoop</strong>을 선택합니다.</td>
    </tr>
    <tr class="odd">
    <td align="left">HDInsight 버전</td>
    <td align="left">버전 선택. Hadoop의 경우 기본값은 Hadoop 2.4를 사용하는 HDInsight 버전 3.1입니다.</td>
    </tr>
    </tbody>
    </table>

    테이블에 표시되는 대로 값을 입력하거나 선택하고 오른쪽 화살표를 클릭합니다.

4.  **클러스터 구성** 페이지에서 다음 값을 입력하거나 선택합니다.

    <table>
    <colgroup>
    <col width="50%" />
    <col width="50%" />
    </colgroup>
    <thead>
    <tr class="header">
    <th align="left">이름</th>
    <th align="left">값</th>
    </tr>
    </thead>
    <tbody>
    <tr class="odd">
    <td align="left">데이터 노드</td>
    <td align="left">배포하려는 데이터 노드 수입니다. 테스트 목적으로 단일 노드 클러스터를 만듭니다.<br />클러스터 크기 제한은 Azure 구독에 따라 다릅니다. 제한을 늘리려면 Azure 청구 지원 팀에 문의하세요.</td>
    </tr>
    <tr class="even">
    <td align="left">지역/가상 네트워크</td>
    <td align="left"><p>마지막 절차에서 만든 저장소 계정과 동일한 지역을 선택합니다. HDInsight는 저장소 계정이 동일한 지역에 있어야 합니다. 구성의 뒷부분에서 여기서 지정한 지역과 동일한 지역에 있는 저장소 계정만 선택할 수 있습니다.</p>
    <p>사용 가능한 지역은 <strong>동아시아</strong>, <strong>동남아시아</strong>, <strong>북유럽</strong>, <strong>서유럽</strong>, <strong>미국 동부</strong>, <strong>미국 서부</strong>, <strong>미국 북중부</strong>, <strong>미국 남중부</strong>입니다.<br />Azure 가상 네트워크를 만든 경우 HDInsight 클러스터에서 사용하도록 구성할 네트워크를 선택할 수 있습니다.</p>
    <p>Azure 가상 네트워크 만들기에 대한 자세한 내용은 [가상 네트워크 구성 작업](http://msdn.microsoft.com/ko-kr/library/azure/jj156206.aspx)을 참조하세요.</p></td>
    </tr>
    </tbody>
    </table>

5.  **클러스터 사용자 구성** 페이지에서 다음 값을 제공합니다.

    ![HDI.CustomCreateCluster.ClusterUser][HDI.CustomCreateCluster.ClusterUser]

    | 속성                      | 값                                                                                                                                                                                                                                                                |
    |---------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | 사용자 이름               | HDInsight 클러스터 사용자 이름 지정                                                                                                                                                                                                                               |
    | 암호/암호 확인            | HDInsight 클러스터 사용자 암호 지정                                                                                                                                                                                                                               |
    | Hive/Oozie Metastore 입력 | 클러스터와 동일한 데이터 센터에서 Hive/Oozie 메타스토어로 사용할 SQL 데이터베이스를 지정하려면 이 확인란을 선택합니다. 이 확인란은 클러스터가 삭제된 후에도 Hive/Oozie 작업에 대한 메타데이터를 유지하려는 경우에 유용합니다.                                     |
    | 메타스토어 데이터베이스   | Hive/OOzie용 메타스토어로 사용할 Azure SQL 데이터베이스 지정. 이 SQL 데이터베이스는 HDInsight 클러스터와 동일한 데이터 센터에 있어야 합니다. 목록 상자에는 **클러스터 세부 정보** 페이지에서 지정한 것과 동일한 데이터 센터에 있는 SQL 데이터베이스만 나열됩니다. |
    | 데이터베이스 사용자       | 데이터베이스에 연결하는 데 사용할 SQL 데이터베이스 사용자 지정                                                                                                                                                                                                    |
    | 데이터베이스 사용자 암호  | SQL 데이터베이스 사용자 암호 지정                                                                                                                                                                                                                                 |

    > [WACOM.NOTE] 메타스토어에 사용되는 Azure SQL 데이터베이스는 Azure HDInsight를 비롯한 다른 Azure 서비스로의 연결을 허용해야 합니다. Azure SQL 데이터베이스 대시보드의 오른쪽에서 서버 이름을 클릭합니다. 이 서버는 SQL 데이터베이스 인스턴스가 실행되는 서버입니다. 서버 보기에서 **구성**을 클릭하고 **Microsoft Azure 서비스**에 대해 **예**를 클릭한 다음 **저장**을 클릭합니다.

    오른쪽 화살표를 클릭합니다.

6.  **저장소 계정** 페이지에서 다음 값을 제공합니다.

    ![HDI.CustomCreateCluster.StorageAccount][HDI.CustomCreateCluster.StorageAccount]

    <table>
    <colgroup>
    <col width="50%" />
    <col width="50%" />
    </colgroup>
    <thead>
    <tr class="header">
    <th align="left">속성</th>
    <th align="left">값</th>
    </tr>
    </thead>
    <tbody>
    <tr class="odd">
    <td align="left">저장소 계정</td>
    <td align="left">HDInsight 클러스터의 기본 파일 시스템으로 사용할 Azure 저장소 계정을 지정합니다. 세 가지 옵션 중 하나를 선택할 수 있습니다.
    <ul>
    <li>기존 저장소 사용</li>
    <li>새 저장소 만들기</li>
    <li>다른 구독의 저장소 사용</li>
    </ul></td>
    </tr>
    <tr class="even">
    <td align="left">계정 이름</td>
    <td align="left"><ul>
    <li>기존 저장소를 사용하도록 선택한 경우 <strong>계정 이름</strong>에 대해 기존 저장소 계정을 선택합니다. 드롭다운에는 클러스터를 프로비전하도록 선택한 동일한 데이터 센터에 있는 저장소 계정만 표시됩니다.</li>
    <li><strong>새 저장소 만들기</strong> 또는 <strong>다른 구독의 저장소 사용</strong> 옵션을 선택한 경우에는 저장소 계정 이름을 제공해야 합니다.</li>
    </ul></td>
    </tr>
    <tr class="odd">
    <td align="left">계정 키</td>
    <td align="left"><strong>다른 구독의 저장소 사용</strong> 옵션을 선택한 경우 해당 저장소 계정의 계정 키를 지정합니다.</td>
    </tr>
    <tr class="even">
    <td align="left">기본 컨테이너</td>
    <td align="left"><p>HDInsight 클러스터의 기본 파일 시스템으로 사용할 저장소 계정의 기본 컨테이너를 지정합니다. <strong>저장소 계정</strong> 필드에 대해 <strong>기존 저장소 사용</strong>을 선택했으며 해당 계정에 기존 컨테이너가 없는 경우 기본적으로 클러스터 이름과 같은 이름의 컨테이너가 만들어집니다. 클러스터의 이름을 가진 컨테이너가 이미 있는 경우에는 컨테이너 이름에 시퀀스 번호가 추가됩니다. 예를 들어 mycontainer1, mycontainer2 등과 같습니다. 그렇지만 기존 저장소 계정에 지정한 클러스터 이름과는 다른 이름의 컨테이너가 들어 있으면 해당 컨테이너도 사용할 수 있습니다.</p>
    <p>새 저장소를 만들거나 다른 Azure 구독의 저장소를 사용하도록 선택한 경우 기본 컨테이너 이름을 지정해야 합니다.</p></td>
    </tr>
    <tr class="odd">
    <td align="left">추가 저장소 계정</td>
    <td align="left">HDInsight는 여러 저장소 계정을 지원합니다. 클러스터에서 사용할 수 있는 추가 저장소 계정의 수에는 제한이 없습니다. 하지만 관리 포털을 사용하여 클러스터를 만드는 경우에는 UI 제약으로 인해 7개로 제한됩니다. 지정하는 각 추가 저장소 계정은 마법사에 계정 정보를 지정할 수 있는 저장소 계정 페이지를 더합니다. 예를 들어 위의 스크린샷에서 하나의 추가 저장소 계정이 선택되면 5페이지가 대화 상자에 추가됩니다.</td>
    </tr>
    </tbody>
    </table>

    오른쪽 화살표를 클릭합니다.

7.  **저장소 계정** 페이지에서 추가 저장소 계정에 대해 계정 정보를 입력합니다.

    ![HDI.CustomCreateCluster.AddOnStorage][HDI.CustomCreateCluster.AddOnStorage]

    여기서 다시 기존 저장소에서 선택하거나, 새 저장소를 만들거나, 다른 Azure 구독의 저장소를 사용하기 위한 옵션이 제공됩니다. 값을 제공하는 절차는 이전 단계와 비슷합니다.

    확인 표시를 클릭하여 클러스터 프로비전을 시작합니다. 프로비전이 완료되면 상태 열에 **실행 중**이 표시됩니다.

    > [WACOM.NOTE] HDInsight 클러스터의 Azure 저장소 계정을 선택하고 나면 계정을 삭제할 수 없으며 다른 계정으로 변경할 수도 없습니다.

## <span id="powershell"></span></a> Azure PowerShell 사용

Azure PowerShell은 Azure에서 작업의 배포와 관리를 제어 및 자동화하기 위해 사용할 수 있는 강력한 스크립팅 환경입니다. 이 섹션에서는 HDInsight 클러스터를 프로비전하는 방법에 대한 지침을 제공합니다. HDInsight Powershell cmdlet을 실행하도록 워크스테이션을 구성하는 방법에 대한 자세한 내용은 [Azure PowerShell 설치 및 구성][Azure PowerShell 설치 및 구성](영문)을 참조하세요. HDInsight와 함께 PowerShell을 사용하는 방법에 대한 자세한 내용은 [PowerShell을 사용하여 HDInsight 관리][PowerShell을 사용하여 HDInsight 관리]를 참조하세요. HDInsight PowerShell cmdlet의 목록은 [HDInsight cmdlet 참조][HDInsight cmdlet 참조]를 참조하세요.

> [WACOM.NOTE] 이 섹션의 스크립트는 Azure 가상 네트워크에서 HDInsight 클러스터를 구성하는 데 사용할 수는 있지만 Azure 가상 네트워크를 만들지는 않습니다. Azure 가상 네트워크 만들기에 대한 자세한 내용은 [가상 네트워크 구성 작업][가상 네트워크 구성 작업]을 참조하세요.

PowerShell을 사용하여 HDInsight 클러스터를 프로비전하려면 다음 절차가 필요합니다.

-   Azure 저장소 계정 만들기
-   Azure Blob 컨테이너 만들기
-   HDInsight 클러스터 만들기

HDInsight는 Azure Blob 저장소 컨테이너를 기본 파일 시스템으로 사용합니다. HDInsight 클러스터를 만들려면 먼저 Azure 저장소 계정 및 저장소 컨테이너가 필요합니다. 저장소 계정은 HDInsight 클러스터와 동일한 데이터 센터에 있어야 합니다.

**Azure 저장소 계정을 만들려면**

-   Azure PowerShell 콘솔 창에서 다음 명령을 실행합니다.

        $storageAccountName = "<StorageAcccountName>"   # Provide a storage account name 
        $location = "<MicrosoftDataCenter>"             # For example, "West US"

        # Create an Azure storage account
        New-AzureStorageAccount -StorageAccountName $storageAccountName -Location $location

    저장소 계정이 이미 있지만 계정 이름과 계정 키를 모르는 경우 다음 PowerShell 명령을 사용하여 정보를 검색할 수 있습니다.

        # List storage accounts for the current subscription
        Get-AzureStorageAccount

        # List the keys for a storage account
        Get-AzureStorageKey "<StorageAccountName>"

**Azure Blob 저장소 컨테이너를 만들려면**

-   Azure PowerShell 콘솔 창에서 다음 명령을 실행합니다.

        $storageAccountName = "<StorageAccountName>"    # Provide the storage account name 
        $storageAccountKey = "<StorageAccountKey>"      # Provide either primary or secondary key
        $containerName="<ContainerName>"                # Provide a container name

        # Create a storage context object
        $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName 
                                               -StorageAccountKey $storageAccountKey  

        # Create a Blob storage container
        New-AzureStorageContainer -Name $containerName -Context $destContext

저장소 계정 및 Blob 컨테이너가 준비되면 클러스터를 만들 준비가 되었습니다.

**HDInsight 클러스터를 프로비전하려면**

> [WACOM.NOTE] HDInsight 클러스터에서 구성 변수를 변경할 때는 PowerShell cmdlet만 사용하는 것이 좋습니다. 원격 데스크톱을 통해 클러스터에 연결된 상태로 Hadoop 구성 파일을 변경하면 클러스터 패치 시 해당 변경 내용을 덮어쓸 수 있습니다. 반면 PowerShell을 통해 설정된 구성 값은 클러스터를 패치해도 보존됩니다.

-   Azure PowerShell 콘솔 창에서 다음 명령을 실행합니다.

        $subscriptionName = "<SubscriptionName>"        # Name of the Azure subscription.
        $storageAccountName = "<StorageAccountName>"    # Azure storage account that hosts the default container. 
        $containerName = "<ContainerName>"              # Azure Blob container that is used as the default file system for the HDInsight cluster.

        $clusterName = "<HDInsightClusterName>"         # The name you will name your HDInsight cluster.
        $location = "<MicrosoftDataCenter>"             # The location of the HDInsight cluster. It must in the same data center as the storage account.
        $clusterNodes = <ClusterSizeInNodes>            # The number of nodes in the HDInsight cluster.

        # Get the storage primary key based on the account name
        Select-AzureSubscription $subscriptionName
        $storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }

        # Create a new HDInsight cluster
        New-AzureHDInsightCluster -Name $clusterName -Location $location -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainerName $containerName  -ClusterSizeInNodes $clusterNodes

    메시지가 나타나면 클러스터에 대한 자격 증명을 입력합니다. 클러스터 프로비전이 완료되는 데 몇 분 정도 걸릴 수 있습니다.

    ![HDI.CLI.Provision][HDI.CLI.Provision]

**사용자 지정 구성 옵션을 사용하여 HDInsight 클러스터를 프로비전하려면**

클러스터를 프로비전할 때 둘 이상의 Azure Blob 저장소에 연결하거나 가상 네트워크를 사용하거나 Hive 및 Oozie 메타 저장소에 대해 Azure SQL 데이터베이스를 사용하는 것과 같은 기타 구성 옵션을 사용할 수 있습니다. 이 기능을 사용하면 데이터 및 메타데이터의 수명을 클러스터의 수명과 분리할 수 있습니다.

> [WACOM.NOTE] HDInsight 클러스터에서 구성 변수를 변경할 때는 PowerShell cmdlet만 사용하는 것이 좋습니다. 원격 데스크톱을 통해 클러스터에 연결된 상태로 Hadoop 구성 파일을 변경하면 클러스터 패치 시 해당 변경 내용을 덮어쓸 수 있습니다. 반면 PowerShell을 통해 설정된 구성 값은 클러스터를 패치해도 보존됩니다.

-   Windows PowerShell 창에서 다음 명령을 실행합니다.

        $subscriptionName = "<SubscriptionName>"
        $clusterName = "<ClusterName>"
        $location = "<MicrosoftDataCenter>"
        $clusterNodes = <ClusterSizeInNodes>

        $storageAccountName_Default = "<DefaultFileSystemStorageAccountName>"
        $containerName_Default = "<DefaultFileSystemContainerName>"

        $storageAccountName_Add1 = "<AdditionalStorageAccountName>"

        $hiveSQLDatabaseServerName = "<SQLDatabaseServerNameForHiveMetastore>"
        $hiveSQLDatabaseName = "<SQLDatabaseDatabaseNameForHiveMetastore>"
        $oozieSQLDatabaseServerName = "<SQLDatabaseServerNameForOozieMetastore>"
        $oozieSQLDatabaseName = "<SQLDatabaseDatabaseNameForOozieMetastore>"

        # Get the virtual network ID and subnet name
        $vnetID = "<AzureVirtualNetworkID>"
        $subNetName = "<AzureVirtualNetworkSubNetName>" 

        # Get the storage account keys
        Select-AzureSubscription $subscriptionName
        $storageAccountKey_Default = Get-AzureStorageKey $storageAccountName_Default | %{ $_.Primary }
        $storageAccountKey_Add1 = Get-AzureStorageKey $storageAccountName_Add1 | %{ $_.Primary }

        $oozieCreds = Get-Credential -Message "Oozie metastore"
        $hiveCreds = Get-Credential -Message "Hive metastore"

        # Create a Blob storage container
        $dest1Context = New-AzureStorageContext -StorageAccountName $storageAccountName_Default -StorageAccountKey $storageAccountKey_Default  
        New-AzureStorageContainer -Name $containerName_Default -Context $dest1Context

        # Create a new HDInsight cluster
        $config = New-AzureHDInsightClusterConfig -ClusterSizeInNodes $clusterNodes |
            Set-AzureHDInsightDefaultStorage -StorageAccountName "$storageAccountName_Default.blob.core.windows.net" -StorageAccountKey $storageAccountKey_Default -StorageContainerName $containerName_Default |
            Add-AzureHDInsightStorage -StorageAccountName "$storageAccountName_Add1.blob.core.windows.net" -StorageAccountKey $storageAccountKey_Add1 |
            Add-AzureHDInsightMetastore -SqlAzureServerName "$hiveSQLDatabaseServerName.database.windows.net" -DatabaseName $hiveSQLDatabaseName -Credential $hiveCreds -MetastoreType HiveMetastore |
            Add-AzureHDInsightMetastore -SqlAzureServerName "$oozieSQLDatabaseServerName.database.windows.net" -DatabaseName $oozieSQLDatabaseName -Credential $oozieCreds -MetastoreType OozieMetastore |
                New-AzureHDInsightCluster -Name $clusterName -Location $location -VirtualNetworkId $vnetID -SubnetName $subNetName

    > [WACOM.NOTE] 메타 저장소에 사용되는 Azure SQL 데이터베이스는 Azure HDInsight를 비롯한 다른 Azure 서비스로의 연결을 허용해야 합니다. Azure SQL 데이터베이스 대시보드의 오른쪽에서 서버 이름을 클릭합니다. 이 서버는 SQL 데이터베이스 인스턴스가 실행되는 서버입니다. 서버 보기에서 **구성**을 클릭하고 **Microsoft Azure 서비스**에 대해 **예**를 클릭한 다음 **저장**을 클릭합니다.

**HDInsight 클러스터를 나열하려면**

-   Azure PowerShell 콘솔 창에서 다음 명령을 실행하여 HDInsight 클러스터를 나열한 후 해당 클러스터가 성공적으로 프로비전되었는지 확인합니다.

        Get-AzureHDInsightCluster -Name <ClusterName>

## <span id="cli"></span></a> 플랫폼 간 명령줄 사용

> [WACOM.NOTE] 2014/8/29일 현재 플랫폼 간 명령줄 인터페이스를 사용하여 클러스터를 Azure 가상 네트워크와 연결할 수는 없습니다.

HDInsight 클러스터를 프로비전하는 다른 옵션은 플랫폼 간 명령줄 인터페이스입니다. 명령줄 도구는Node.js로 구현되며 Windows, Mac, Linux를 포함하여 Node.js를 지원하는 플랫폼에서 사용할 수 있습니다. 명령줄 도구는 오픈 소스입니다. 소스 코드는 GitHub(<https://github.com/Azure/azure-sdk-tools-xplat>)에서 관리합니다. 명령줄 인터페이스 사용 방법에 대한 일반적인 설명은 [Mac 및 Linux에서 Azure 명령줄 도구를 사용하는 방법][Mac 및 Linux에서 Azure 명령줄 도구를 사용하는 방법]을 참조하세요. 포괄적인 참조 설명서는 [Mac 및 Linux용 Azure 명령줄 도구][Mac 및 Linux용 Azure 명령줄 도구]를 참조하세요. 이 문서에서는 Windows에서 명령줄 인터페이스를 사용하는 방법만 다룹니다.

플랫폼 간 명령줄을 사용하여 HDInsight 클러스터를 프로비전하려면 다음 절차가 필요합니다.

-   플랫폼 간 명령줄 설치
-   Azure 계정 게시 설정 다운로드 및 가져오기
-   Azure 저장소 계정 만들기
-   클러스터 프로비전

명령줄 인터페이스는 *NPM(Node.js 패키지 관리자)* 또는 Windows Installer를 사용하여 설치할 수 있습니다. 두 옵션 중 하나만 사용해서 설치하는 것이 좋습니다.

**NPM을 사용하여 명령줄 인터페이스를 설치하려면**

1.  **www.nodejs.org**로 이동합니다.
2.  **INSTALL**을 클릭하고 기본 설정을 사용하여 지침을 따릅니다.
3.  워크스테이션에서 **명령 프롬프트**(또는 *Azure 명령 프롬프트* 또는 *VS2012용 개발자 명령 프롬프트*)를 엽니다.
4.  명령 프롬프트 창에서 다음 명령을 실행합니다.

        npm install -g azure-cli

    > [WACOM.NOTE] NPM 명령을 찾을 수 없다는 오류 메시지가 나타나는 경우 PATH 환경 변수에 있는 다음 경로를 확인합니다. *C:\\Program Files (x86)\\nodejs;C:\\Users[username]\\AppData\\Roaming\\npm* 또는 *C:\\Program Files\\nodejs;C:\\Users[username]\\AppData\\Roaming\\npm*

5.  다음 명령을 실행하여 설치를 확인합니다.

        azure hdinsight -h

    여러 수준에서 *-h* 스위치를 사용하여 도움말 정보를 표시할 수 있습니다. 예를 들면 다음과 같습니다.

        azure -h
        azure hdinsight -h
        azure hdinsight cluster -h
        azure hdinsight cluster create -h

**Windows Installer를 사용하여 명령줄 인터페이스를 설치하려면**

1.  **http://azure.microsoft.com/ko-kr/downloads/**로 이동합니다.
2.  **명령줄 도구** 섹션으로 스크롤하여 **플랫폼 간 명령줄 인터페이스**를 클릭하고 웹 플랫폼 설치 관리자 마법사를 따릅니다.

**게시 설정을 다운로드하거나 가져오려면**

명령줄 인터페이스를 사용하려면 먼저 워크스테이션과 Azure 사이의 연결을 구성해야 합니다. Azure 구독 정보는 명령줄 인터페이스에서 계정에 연결하는 데 사용됩니다. 이 정보는 Azure의 게시 설정 파일에서 구할 수 있습니다. 그런 다음 게시 설정 파일을 영구적 로컬 구성 설정으로 가져와서 명령줄 인터페이스에서 후속 작업에 사용할 수 있습니다. 게시 설정을 한 번만 가져와야 합니다.

> [WACOM.NOTE] 게시 설정 파일에는 중요 정보가 포함되어 있습니다. 파일을 삭제하거나 파일이 포함된 사용자 폴더를 암호화하는 추가 단계를 수행하는 것이 좋습니다. Windows에서 폴더 속성을 수정하거나 BitLocker를 사용합니다.

1.  **명령 프롬프트**를 엽니다.
2.  다음 명령을 실행하여 게시 설정 파일을 다운로드합니다.

        azure account download

    ![HDI.CLIAccountDownloadImport][HDI.CLIAccountDownloadImport]

    이 명령은 게시 설정 파일을 다운로드할 웹 페이지를 시작합니다.

3.  파일을 저장하라는 메시지가 표시되면 **저장**을 클릭하고 파일을 저장해야 하는 위치를 제공합니다.
4.  명령 프롬프트 창에서 다음 명령을 실행하여 게시 설정 파일을 가져옵니다.

        azure account import <file>

    이전 스크린샷에서 게시 설정 파일은 워크스테이션의 C:\\HDInsight 폴더에 저장되었습니다.

**Azure 저장소 계정을 만들려면**

HDInsight는 Azure Blob 저장소 컨테이너를 기본 파일 시스템으로 사용합니다. HDInsight 클러스터를 만들려면 먼저 Azure 저장소 계정이 필요합니다. 저장소 계정은 동일한 데이터 센터에 위치해야 합니다.

-   명령 프롬프트 창에서 다음 명령을 실행하여 Azure 저장소 계정을 만듭니다.

        azure storage account create [options] <StorageAccountName>

    위치를 묻는 메시지가 표시되면 HDINsight 클러스터를 프로비전할 수 있는 위치를 선택합니다. 저장소는 HDInsight 클러스터와 동일한 위치에 있어야 합니다. 현재 **동아시아**, **동남아시아**, **북유럽**, **서유럽**, **미국 동부**, **미국 서부**, **미국 북중부**, **미국 남중부** 지역에서만 HDInsight 클러스터를 호스트할 수 있습니다.

Azure 관리 포털을 사용하여 Azure 저장소 계정을 만드는 방법에 대한 자세한 내용은 [저장소 계정을 만드는 방법][저장소 계정을 만드는 방법]을 참조하세요.

저장소 계정이 이미 있지만 계정 이름과 계정 키를 모르는 경우 다음 명령을 사용하여 정보를 검색할 수 있습니다.

    -- lists storage accounts
    azure storage account list

    -- Shows information for a storage account
    azure storage account show <StorageAccountName>

    -- Lists the keys for a storage account
    azure storage account keys list <StorageAccountName>

관리 포털을 사용하여 정보를 가져오는 방법에 대한 자세한 내용은 *방법: 저장소 액세스 키 보기, 복사 및 다시 생성* 섹션([저장소 계정을 관리하는 방법][저장소 계정을 관리하는 방법])을 참조하세요.

또한 HDInsight 클러스터를 사용하려면 저장소 계정 내에 컨테이너가 있어야 합니다. 제공한 저장소 계정에 아직 컨테이너가 없을 때 *azure hdinsight cluster create*를 실행하면 컨테이너 이름을 묻는 메시지가 표시되고 해당 이름으로 컨테이너가 만들어집니다. 그렇지만 미리 컨테이너를 만들려면 다음 명령을 사용할 수 있습니다.

    azure storage container create --account-name <StorageAccountName> --account-key <StorageAccountKey> [ContainerName]
        

저장소 계정 및 Blob 컨테이너가 준비되면 클러스터를 만들 준비가 되었습니다.

**HDInsight 클러스터를 프로비전하려면**

-   명령 프롬프트 창에서 다음 명령을 실행합니다.

        azure hdinsight cluster create --clusterName <ClusterName> --storageAccountName "<StorageAccountName>.blob.core.windows.net" --storageAccountKey <storageAccountKey> --storageContainer <SorageContainerName> --nodes <NumberOfNodes> --location <DataCenterLocation> --username <HDInsightClusterUsername> --clusterPassword <HDInsightClusterPassword>

    ![HDI.CLIClusterCreation][HDI.CLIClusterCreation]

**구성 파일을 사용하여 HDInsight 클러스터를 프로비전하려면**

일반적으로 HDInsight 클러스터를 프로비전하고, 해당 작업을 실행한 후에 비용을 줄이기 위해 클러스터를 삭제합니다. 명령줄 인터페이스에는 클러스터를 프로비전할 때마다 다시 사용할 수 있도록 구성을 파일에 저장하는 옵션이 있습니다.

-   명령 프롬프트 창에서 다음 명령을 실행합니다.

        #Create the config file
        azure hdinsight cluster config create <file> 

        #Add commands to create a basic cluster
        azure hdinsight cluster config set <file> --clusterName <ClusterName> --nodes <NumberOfNodes> --location "<DataCenterLocation>" --storageAccountName "<StorageAccountName>.blob.core.windows.net" --storageAccountKey "<StorageAccountKey>" --storageContainer "<BlobContainerName>" --username "<Username>" --clusterPassword "<UserPassword>"

        #If requred, include commands to use additional blob storage with the cluster
        azure hdinsight cluster config storage add <file> --storageAccountName "<StorageAccountName>.blob.core.windows.net"
               --storageAccountKey "<StorageAccountKey>"

        #If required, include commands to use a SQL database as a Hive metastore
        azure hdinsight cluster config metastore set <file> --type "hive" --server "<SQLDatabaseName>.database.windows.net"
               --database "<HiveDatabaseName>" --user "<Username>" --metastorePassword "<UserPassword>"

        #If required, include commands to use a SQL database as an Oozie metastore 
        azure hdinsight cluster config metastore set <file> --type "oozie" --server "<SQLDatabaseName>.database.windows.net"
               --database "<OozieDatabaseName>" --user "<SQLUsername>" --metastorePassword "<SQLPassword>"

        #Run this command to create a cluster using the config file     
        azure hdinsight cluster create --config <file>

    > [WACOM.NOTE] 메타스토어에 사용되는 Azure SQL 데이터베이스는 Azure HDInsight를 비롯한 다른 Azure 서비스로의 연결을 허용해야 합니다. Azure SQL 데이터베이스 대시보드의 오른쪽에서 서버 이름을 클릭합니다. 이 서버는 SQL 데이터베이스 인스턴스가 실행되는 서버입니다. 서버 보기에서 **구성**을 클릭하고 **Microsoft Azure 서비스**에 대해 **예**를 클릭한 다음 **저장**을 클릭합니다.

    ![HDI.CLIClusterCreationConfig][HDI.CLIClusterCreationConfig]

**클러스터 세부 정보를 나열하고 표시하려면**

-   클러스터 세부 정보를 나열하고 표시하려면 다음 명령을 사용합니다.

        azure hdinsight cluster list
        azure hdinsight cluster show <ClusterName>

    ![HDI.CLIListCluster][HDI.CLIListCluster]

**클러스터를 삭제하려면**

-   클러스터를 삭제하려면 다음 명령을 사용합니다.

        azure hdinsight cluster delete <ClusterName>

## <span id="sdk"></span></a> HDInsight .NET SDK 사용

HDInsight .NET SDK는 .NET 응용 프로그램에서 HDInsight로 더 쉽게 작업하도록 지원하는 .NET 클라이언트 라이브러리를 제공합니다.

SDK를 사용하여 HDInsight 클러스터를 프로비전하려면 다음 절차를 수행해야 합니다.

-   HDInsight .NET SDK 설치
-   자체 서명된 인증서 만들기
-   콘솔 응용 프로그램 만들기
-   응용 프로그램 실행

**HDInsight .NET SDK를 설치하려면**

[NuGet][NuGet](영문)에서 가장 최근에 게시된 SDK 빌드를 설치할 수 있습니다. 지침은 다음 절차에서 설명합니다.

**자체 서명된 인증서를 만들려면**

자체 서명 인증서를 만들어 워크스테이션에 설치한 다음 Azure 구독에 업로드합니다. 관련 지침은 [자체 서명된 인증서 만들기][자체 서명된 인증서 만들기]를 참조하세요.

**Visual Studio 콘솔 응용 프로그램을 만들려면**

1.  Visual Studio 2013을 엽니다.

2.  파일 메뉴에서 **새로 만들기**와 **프로젝트**를 차례로 클릭합니다.

3.  새 프로젝트에서 다음 값을 입력하거나 선택합니다.

    | 속성     | 값                           |
    |----------|------------------------------|
    | Category | Templates/Visual C#/Windows |
    | 템플릿   | Console Application          |
    | 이름     | CreateHDICluster             |

4.  **확인**을 클릭하여 프로젝트를 만듭니다.

5.  **도구** 메뉴에서 **Nuget 패키지 관리자**, **패키지 관리자 콘솔**을 차례로 클릭합니다.

6.  콘솔에서 다음 명령을 실행하여 패키지를 설치합니다.

        Install-Package Microsoft.WindowsAzure.Management.HDInsight

    .NET 라이브러리 및 이에 대한 참조가 현재의 Visual Studio 프로젝트에 추가됩니다.

7.  솔루션 탐색기에서 **Program.cs**를 두 번 클릭하여 엽니다.

8.  파일 맨 위에 다음 using 문을 추가합니다.

        using System.Security.Cryptography.X509Certificates;
        using Microsoft.WindowsAzure.Management.HDInsight;
        using Microsoft.WindowsAzure.Management.HDInsight.ClusterProvisioning;

9.  Main() 함수에서 다음 코드를 복사하여 붙여넣습니다.

        string certfriendlyname = "<CertificateFriendlyName>";     // Friendly name for the certificate your created earlier  
        string subscriptionid = "<AzureSubscriptionID>";
        string clustername = "<HDInsightClusterName>";
        string location = "<MicrosoftDataCenter>";
        string storageaccountname = "<AzureStorageAccountName>.blob.core.windows.net";
        string storageaccountkey = "<AzureStorageAccountKey>";
        string containername = "<HDInsightDefaultContainerName>";
        string username = "<HDInsightUsername>";
        string password = "<HDInsightUserPassword>";
        int clustersize = <NumberOfNodesInTheCluster>;

        // Get the certificate object from certificate store using the friendly name to identify it
        X509Store store = new X509Store();
        store.Open(OpenFlags.ReadOnly);
        X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>().First(item => item.FriendlyName == certfriendlyname);

        // Create the storage account if it doesn't exist.

        // Create the container if it doesn't exist.

        // Create an HDInsightClient object
        HDInsightCertificateCredential creds = new HDInsightCertificateCredential(new Guid(subscriptionid), cert);
        var client = HDInsightClient.Connect(creds);

        // Supply th cluster information
        ClusterCreateParameters clusterInfo = new ClusterCreateParameters()
        {
            Name = clustername,
            Location = location,
            DefaultStorageAccountName = storageaccountname,
            DefaultStorageAccountKey = storageaccountkey,
            DefaultStorageContainer = containername,
            UserName = username,
            Password = password,
            ClusterSizeInNodes = clustersize
        };

        // Create the cluster
        Console.WriteLine("Creating the HDInsight cluster ...");

        ClusterDetails cluster = client.CreateCluster(clusterInfo);

        Console.WriteLine("Created cluster: {0}.", cluster.ConnectionUrl);
        Console.WriteLine("Press ENTER to continue.");
        Console.ReadKey();

10. main() 함수 시작 부분의 변수를 바꿉니다.

**응용 프로그램을 실행하려면**

Visual Studio에 응용 프로그램이 열려 있을 때 **F5**를 눌러 응용 프로그램을 실행합니다. 콘솔 창이 열리고 응용 프로그램의 상태가 표시되며 HDInsight 클러스터를 만드는 데는 몇 분 정도 걸릴 수 있습니다.

## <span id="nextsteps"></span></a>다음 단계

이 문서에서는 HDInsight 클러스터를 프로비전하는 여러 가지 방법에 대해 알아보았습니다. 자세한 내용은 다음 문서를 참조하세요.

-   [Azure HDInsight 시작][Azure HDInsight 시작]
-   [PowerShell을 사용하여 HDInsight 관리][PowerShell을 사용하여 HDInsight 관리]
-   [프로그래밍 방식으로 Hadoop 작업 제출][프로그래밍 방식으로 Hadoop 작업 제출]
-   [Azure HDInsight SDK 문서][Azure HDInsight SDK 문서]

  [HDInsight에서 HBase 클러스터 프로비전]: http://azure.microsoft.com/ko-kr/documentation/articles/hdinsight-hbase-get-started/
  [Hadoop와 HBase 간 차이점]: http://go.microsoft.com/fwlink/?LinkId=510237
  [Apache Hadoop]: http://go.microsoft.com/fwlink/?LinkId=510084
  [HDInsight 클러스터]: ./media/hdinsight-provision-clusters/HDI.Cluster.png
  [HDInsight의 Hadoop 소개]: ../hdinsight-hadoop-introduction/
  [HDInsight에서 Azure Blob 저장소 사용]: ../hdinsight-use-blob-storage/
  [Azure HDInsight 시작]: ../hdinsight-get-started/
  [구매 옵션]: http://azure.microsoft.com/ko-kr/pricing/purchase-options/
  [구성원 제공 항목]: http://azure.microsoft.com/ko-kr/pricing/member-offers/
  [무료 평가판]: http://azure.microsoft.com/ko-kr/pricing/free-trial/
  [구성 옵션]: #configuration
  [Azure 관리 포털 사용]: #portal
  [Azure PowerShell 사용]: #powershell
  [플랫폼 간 명령줄 사용]: #cli
  [HDInsight .NET SDK 사용]: #sdk
  [다음 단계]: #nextsteps
  [1]: http://azure.microsoft.com/ko-kr/documentation/articles/hdinsight-use-blob-storage/
  [Azure 가상 네트워크]: http://azure.microsoft.com/ko-kr/documentation/services/virtual-network/
  [클라우드 전용 구성 다이어그램]: .\media\hdinsight-provision-clusters\cloud-only.png
  [사이트 간 구성 다이어그램]: .\media\hdinsight-provision-clusters\site-to-site.png
  [지점 및 사이트 간 구성 다이어그램]: .\media\hdinsight-provision-clusters\point-to-site.png
  [Azure 가상 네트워크 개요]: http://msdn.microsoft.com/library/azure/jj156007.aspx
  [가상 네트워크 구성 작업]: http://msdn.microsoft.com/ko-kr/library/azure/jj156206.aspx
  [저장소 계정을 만드는 방법]: ../storage-create-storage-account/
  [Azure 관리 포털]: https://manage.windowsazure.com/
  [HDI.CustomCreateCluster]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.png
  [HDI.CustomCreateCluster.ClusterUser]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.ClusterUser.png
  [HDI.CustomCreateCluster.StorageAccount]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.StorageAccount.png
  [HDI.CustomCreateCluster.AddOnStorage]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.AddOnStorage.png
  [Azure PowerShell 설치 및 구성]: ../install-configure-powershell/
  [PowerShell을 사용하여 HDInsight 관리]: ../hdinsight-administer-use-powershell/
  [HDInsight cmdlet 참조]: http://msdn.microsoft.com/ko-kr/library/windowsazure/dn479228.aspx
  [HDI.CLI.Provision]: ./media/hdinsight-provision-clusters/HDI.ps.provision.png
  [Mac 및 Linux에서 Azure 명령줄 도구를 사용하는 방법]: ../xplat-cli/
  [Mac 및 Linux용 Azure 명령줄 도구]: ../command-line-tools/
  [HDI.CLIAccountDownloadImport]: ./media/hdinsight-provision-clusters/HDI.CLIAccountDownloadImport.png
  [저장소 계정을 관리하는 방법]: ../storage-manage-storage-account/
  [HDI.CLIClusterCreation]: ./media/hdinsight-provision-clusters/HDI.CLIClusterCreation.png
  [HDI.CLIClusterCreationConfig]: ./media/hdinsight-provision-clusters/HDI.CLIClusterCreationConfig.png
  [HDI.CLIListCluster]: ./media/hdinsight-provision-clusters/HDI.CLIListClusters.png "클러스터 나열 및 표시"
  [NuGet]: http://nuget.codeplex.com/wikipage?title=Getting%20Started
  [자체 서명된 인증서 만들기]: http://go.microsoft.com/fwlink/?LinkId=511138
  [프로그래밍 방식으로 Hadoop 작업 제출]: ../hdinsight-submit-hadoop-jobs-programmatically/
  [Azure HDInsight SDK 문서]: http://msdn.microsoft.com/ko-kr/library/dn479185.aspx
