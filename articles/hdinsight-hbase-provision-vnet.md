<properties urlDisplayName="Provision HBase clusters on Azure Virtual Network" pageTitle="Azure 가상 네트워크에 HBase 클러스터 프로비전 | Azure" metaKeywords="" description="Azure 가상 네트워크에 HDInsight 클러스터를 만드는 방법을 알아봅니다." metaCanonical="" services="hdinsight" documentationCenter="" title="Azure 가상 네트워크에 HBase 클러스터 프로비전" authors="jgao" solutions="big-data" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/21/2014" ms.author="jgao" />

# Azure 가상 네트워크에 HBase 클러스터 프로비전

Azure 가상 네트워크에 HDInsight 클러스터를 만드는 방법을 알아봅니다.

가상 네트워크 통합을 사용하면 응용 프로그램이 HBase와 직접 통신할 수 있도록 응용 프로그램과 동일한 가상 네트워크에 HBase 클러스터를 배포할 수 있습니다. 이점은 다음과 같습니다.

-   웹 응용 프로그램을 HBase 클러스터의 노드에 직접 연결하므로 HBase Java RPC API를 사용하여 통신할 수 있습니다.
-   트래픽이 여러 게이트웨이 및 부하 분산 장치를 통과하지 않도록 하여 성능을 향상시킵니다.
-   공용 끝점을 노출하지 않고 중요한 정보를 보다 안전한 방식으로 처리합니다.

## 이 문서에서는 다음을 수행합니다.

-   [필수 조건][필수 조건]
-   [가상 네트워크에 HBase 클러스터 프로비전][가상 네트워크에 HBase 클러스터 프로비전]
-   [HBase Java RPC API를 사용하여 가상 네트워크에 프로비전된 HBase 클러스터에 연결][HBase Java RPC API를 사용하여 가상 네트워크에 프로비전된 HBase 클러스터에 연결]
-   [PowerShell을 사용하여 HBase 클러스터 프로비전][PowerShell을 사용하여 HBase 클러스터 프로비전]
-   [다음 단계][다음 단계]

## <span id="prerequisites"></span></a>필수 조건

이 자습서를 시작하기 전에 다음이 있어야 합니다.

-   **Azure 구독**. Azure는 구독 기반 플랫폼입니다. 구독을 얻는 방법에 대한 자세한 내용은 [구매 옵션][구매 옵션], [구성원 제공 항목][구성원 제공 항목] 또는 [무료 평가판][무료 평가판]을 참조하세요.

-   **Azure PowerShell이 설치 및 구성된 워크스테이션**. 자세한 내용은 [Azure PowerShell 설치 및 구성][Azure PowerShell 설치 및 구성]을 참조하세요. PowerShell 스크립트를 실행하려면 관리자로 Azure PowerShell을 실행하고 실행 정책을 *RemoteSigned*로 설정해야 합니다. [Windows PowerShell 스크립트 실행][Windows PowerShell 스크립트 실행](영문)을 참조하세요.

    PowerShell 스크립트를 실행하기 전에 cmdlet을 사용하여 Azure 구독에 연결되어 있는지 확인합니다.

        Add-AzureAccount

    여러 Azure 구독이 있는 경우 다음 cmdlet을 사용하여 현재 구독을 설정합니다.

        Select-AzureSubscription <AzureSubscriptionName>

## <span id="hbaseprovision"></span></a>가상 네트워크에 HBase 클러스터 프로비전

**관리 포털을 사용하여 가상 네트워크를 만들려면**

1.  [Azure 관리 포털][Azure 관리 포털]에 로그인합니다.
2.  페이지 맨 아래에서 **새로 만들기**, **네트워크 서비스**, **가상 네트워크**, **빠른 생성**을 차례로 클릭합니다.
3.  다음 값을 입력하거나 선택합니다.

    -   **이름**: 가상 네트워크의 이름입니다.
    -   **주소 공간**: 클러스터의 모든 노드에 대한 주소를 제공하기에 충분히 큰 가상 네트워크의 주소 공간을 선택합니다. 그렇지 않으면 프로비전에 실패합니다.
    -   **최대 VM 수**: 최대 VM 수 중 하나를 선택합니다.
    -   **위치**: 위치는 만들 HBase 클러스터와 동일해야 합니다.
    -   **DNS 서버**: 이 문서에서는 Azure에서 제공하는 내부 DNS 서버를 사용하므로 **없음**을 선택하면 됩니다. 사용자 지정 DNS 서버가 포함된 고급 네트워킹 구성도 지원됩니다. 자세한 지침은 <http://msdn.microsoft.com/library/azure/jj156088.aspx>를 참조하세요.

4.  **가상 네트워크 만들기**를 클릭합니다. 새 가상 네트워크 이름이 목록에 나타납니다. 상태 열에 **생성됨**이 표시될 때까지 기다립니다.
5.  기본 창에서 방금 만든 가상 네트워크를 클릭합니다.
6.  페이지 맨 위에서 **대시보드**를 클릭합니다.
7.  **간략 상태** 아래에서 **가상 네트워크 ID**를 적어 둡니다. HBase 클러스터를 프로비전할 때 필요합니다.
8.  페이지 맨 위에서 **구성**을 클릭합니다.
9.  페이지 맨 아래에서 기본 서브넷 이름은 **Subnet-1**입니다. 선택적으로 서브넷의 이름을 바꾸거나 HBase 클러스터의 새 서브넷을 추가할 수 있습니다. 서브넷 이름을 적어 둡니다. 클러스터를 프로비전할 때 필요합니다.
10. 클러스터에 사용할 서브넷의 **CIDR(주소 수)**을 확인합니다. 주소 수는 작업자 노드 수에 7을 더한 값보다 커야 합니다(게이트웨이: 2, 헤드 노드: 2, Zookeeper: 3). 예를 들어 10개 노드 HBase 클러스터가 필요한 경우 서브넷의 주소 수는 17(10+7)보다 커야 합니다. 그렇지 않으면 배포에 실패합니다.

    > [WACOM.NOTE] 각 클러스터에 단일 서브넷을 지정하는 것이 좋습니다.

11. 서브넷 값을 업데이트했으면 페이지 맨 아래에서 **저장**을 클릭합니다.

> [WACOM.NOTE] HDInsight 클러스터는 Azure Blob 저장소를 사용하여 데이터를 저장합니다. 자세한 내용은 [HDInsight에서 Hadoop와 함께 Azure Blob 저장소 사용][HDInsight에서 Hadoop와 함께 Azure Blob 저장소 사용]을 참조하세요. 저장소 계정과 Blob 저장소 컨테이너가 필요합니다. 저장소 계정 위치는 가상 네트워크 위치 및 클러스터 위치와 일치해야 합니다.

**Azure 저장소 계정 및 Blob 저장소 컨테이너를 만들려면**

1.  [Azure 관리 포털][Azure 관리 포털]에 로그인합니다.
2.  왼쪽 아래에서 **새로 만들기**를 클릭하고 **데이터 서비스**, **저장소**를 차례로 가리킨 후 **빠른 생성**을 클릭합니다.

3.  다음 값을 입력하거나 선택합니다.

    -   **URL**: 저장소 계정의 이름입니다.
    -   **위치**: 저장소 계정의 위치입니다. 가상 네트워크 위치와 일치해야 합니다. 선호도 그룹은 지원되지 않습니다.
    -   **복제**: 테스트 목적으로 **로컬 중복**을 사용하여 비용을 줄입니다.

4.  **CREATE STORAGE ACCOUNT**를 클릭합니다. 저장소 목록에 새 저장소 계정이 표시됩니다.
5.  새 저장소 계정의 **상태**가 **온라인**으로 변경될 때까지 기다립니다.
6.  목록에서 새 저장소 계정을 클릭하여 선택합니다.
7.  페이지 맨 아래에서 **액세스 키 관리**를 클릭합니다.
8.  **저장소 계정 이름** 및 **기본 액세스 키**(또는 **보조 액세스 키**)를 적어 둡니다. 키 중 하나가 작동합니다. 이 정보는 자습서의 뒷부분에서 필요합니다.
9.  페이지 맨 위에서 **컨테이너**를 클릭합니다.
10. 페이지 맨 아래에서 **추가**를 클릭합니다.
11. 컨테이너 이름을 입력합니다. 이 컨테이너는 HBase 클러스터의 기본 컨테이너로 사용됩니다. 기본적으로 기본 컨테이너 이름은 클러스터 이름과 일치합니다. **액세스** 필드를 **비공개**로 유지합니다.
12. 확인 아이콘을 클릭하여 컨테이너를 만듭니다.

**Azure 포털을 사용하여 HBase 클러스터를 프로비전하려면**

> [WACOM.NOTE] PowerShell을 사용하여 새 HBase 클러스터를 프로비전하는 방법에 대한 자세한 내용은 [PowerShell을 사용하여 HBase 클러스터 프로비전][PowerShell을 사용하여 HBase 클러스터 프로비전]을 참조하세요.

1.  [Azure 관리 포털][Azure 관리 포털]에 로그인합니다.
2.  왼쪽 아래에서 **새로 만들기**를 클릭하고 **데이터 서비스**, **HDInsight**를 차례로 가리킨 후 **사용자 지정 만들기**를 클릭합니다.
3.  **클러스터 이름**을 입력하고 클러스터에 사용할 **HDInsight 버전**을 선택합니다.

    ![클러스터 이름 및 버전 필드][클러스터 이름 및 버전 필드]

4.  클러스터에 대해 만들 **데이터 노드**의 수를 선택하고 이 클러스터에 사용할 하위 지역 또는 Azure 가상 네트워크(**하위 지역/가상 네트워크**)를 선택합니다.

    ![노드 수 및 하위 지역 필드][노드 수 및 하위 지역 필드]

5.  이 클러스터에 사용할 관리자의 **사용자 이름** 및 **암호**를 입력합니다.

    ![관리자 이름 및 암호 필드][관리자 이름 및 암호 필드]

6.  새 저장소 계정을 사용할지 아니면 기존 저장소 계정을 사용할지를 선택합니다. 새 저장소 계정을 사용하려는 경우 만들 **계정 이름**과 **기본 컨테이너**를 입력합니다. 마지막으로 확인 표시를 선택하여 클러스터를 만듭니다.

    ![저장소 계정 선택][저장소 계정 선택]

새 HBase 클러스터 사용을 시작하려는 경우 [HDInsight에서 Hadoop을 통해 HBase 사용 시작][HDInsight에서 Hadoop을 통해 HBase 사용 시작]의 절차를 사용할 수 있습니다.

## <span id="connect"></span></a>HBase Java RPC API를 사용하여 가상 네트워크에 프로비전된 HBase 클러스터에 연결

1.  동일한 Azure 가상 네트워크와 동일한 서브넷에 IaaS 가상 컴퓨터를 프로비전합니다. 따라서 가상 컴퓨터와 HBase 클러스터가 둘 다 동일한 내부 DNS 서버를 사용하여 호스트 이름을 확인합니다. 이렇게 하려면 갤러리에서 옵션을 선택한 다음 데이터 센터 대신 가상 네트워크를 선택해야 합니다. 지침은 [Windows Server를 실행하는 가상 컴퓨터 만들기][Windows Server를 실행하는 가상 컴퓨터 만들기]를 참조하세요. 작은 VM 크기의 표준 Windows Server 2012 이미지면 충분합니다.

2.  Java 응용 프로그램을 사용하여 HBase에 원격으로 연결할 때는 FQDN(정규화된 도메인 이름)을 사용해야 합니다. FQDN을 확인하려면 HBase 클러스터의 연결별 DNS 접미사를 가져와야 합니다. 이렇게 하려면 Curl을 사용하여 Ambari를 쿼리하거나 원격 데스크톱을 사용하여 클러스터에 연결합니다.

    -   **Curl** - 다음 명령을 사용합니다.

            curl -u <username>:<password> -k https://<clustername>.azurehdinsight.net/ambari/api/v1/clusters/<clustername>.azurehdinsight.net/services/hbase/components/hbrest

        반환되는 JSON 데이터에서 "host\_name" 항목을 찾습니다. 이 항목에 클러스터의 노드 FQDN(정규화된 도메인 이름)이 포함되어 있습니다. 예를 들면 다음과 같습니다.

            ...
            "host_name": "wordkernode0.<clustername>.b1.cloudapp.net
            ...

        클러스터 이름으로 시작하는 도메인 이름 부분이 DNS 접미사입니다. 예를 들면 mycluster.b1.cloudapp.net과 같습니다.

    -   **PowerShell** - 다음 PowerShell 스크립트를 사용하여 DNS 접미사를 반환하는 데 사용할 수 있는 **Get-ClusterDetail** 함수를 등록합니다.

            function Get-ClusterDetail(
                [String]
                [Parameter( Position=0, Mandatory=$true )]
                $ClusterDnsName,
                [String]
                [Parameter( Position=1, Mandatory=$true )]
                $Username,
                [String]
                [Parameter( Position=2, Mandatory=$true )]
                $Password,
                [String]
                [Parameter( Position=3, Mandatory=$true )]
                $PropertyName
                )
            {
            <#
                .SYNOPSIS 
                 Displays information to facilitate HDInsight cluster to cluster scinario within same virtual network.
                .Description
                 This command shows following 4 properties of an HDInsight cluster.
                 1. ZookeeperQuorum (only support HBase type cluster)
                    Shows the value of hbase property "hbase.zookeeper.quorum".
                 2. ZookeeperClientPort (only support HBase type cluster)
                    Shows the value of hbase property "hbase.zookeeper.property.clientPort".
                 3. HBaseRestServers (only support HBase type cluster)
                    Shows a list of host FQDNs which run HBase rest server.
                 4. FQDNSuffix (support all type cluster)
                    Shows FQDN suffix of hosts in the cluster.
                .EXAMPLE
                 Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName ZookeeperQuorum
                 This command shows the value of hbase property "hbase.zookeeper.quorum".
                .EXAMPLE
                 Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName ZookeeperClientPort
                 This command shows the value of hbase property "hbase.zookeeper.property.clientPort".
                .EXAMPLE
                 Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName HBaseRestServers
                 This command shows a list of host FQDNs which run HBase rest server.
                .EXAMPLE
                 Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName FQDNSuffix
                 This command shows FQDN suffix of hosts in the cluster.
            #>

                $DnsSuffix = ".azurehdinsight.net"

                $ClusterFQDN = $ClusterDnsName + $DnsSuffix
                $webclient = new-object System.Net.WebClient
                $webclient.Credentials = new-object System.Net.NetworkCredential($Username, $Password)

                if($PropertyName -eq "ZookeeperQuorum")
                {
                    $Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.zookeeper.quorum"
                    $Response = $webclient.DownloadString($Url)
                    $JsonObject = $Response | ConvertFrom-Json
                    Write-host $JsonObject.items[0].properties.'hbase.zookeeper.quorum'
                }
                if($PropertyName -eq "ZookeeperClientPort")
                {
                    $Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.zookeeper.property.clientPort"
                    $Response = $webclient.DownloadString($Url)
                    $JsonObject = $Response | ConvertFrom-Json
                    Write-host $JsonObject.items[0].properties.'hbase.zookeeper.property.clientPort'
                }
                if($PropertyName -eq "HBaseRestServers")
                {
                    $Url1 = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.rest.port"
                    $Response1 = $webclient.DownloadString($Url1)
                    $JsonObject1 = $Response1 | ConvertFrom-Json
                    $PortNumber = $JsonObject1.items[0].properties.'hbase.rest.port'

                    $Url2 = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/services/hbase/components/hbrest"
                    $Response2 = $webclient.DownloadString($Url2)
                    $JsonObject2 = $Response2 | ConvertFrom-Json
                    foreach ($host_component in $JsonObject2.host_components)
                    {
                        $ConnectionString = $host_component.HostRoles.host_name + ":" + $PortNumber
                        Write-host $ConnectionString
                    }
                }
                if($PropertyName -eq "FQDNSuffix")
                {
                    $Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/services/yarn/components/resourcemanager"
                    $Response = $webclient.DownloadString($Url)
                    $JsonObject = $Response | ConvertFrom-Json
                    $FQDN = $JsonObject.host_components[0].HostRoles.host_name
                    $pos = $FQDN.IndexOf(".")
                    $Suffix = $FQDN.Substring($pos + 1)
                    Write-host $Suffix
                }
            }

        PowerShell 스크립트를 실행한 후 다음 명령을 사용하여 Get-ClusterDetail 함수를 통해 DNS 접미사를 반환합니다. 이 명령을 사용할 때는 HDInsight HBase 클러스터 이름, 관리자 이름 및 관리자 암호를 지정합니다.

            Get-ClusterDetail -ClusterDnsName <yourclustername> -PropertyName FQDNSuffix -Username <clusteradmin> -Password <clusteradminpassword>

        그러면 DNS 접미사가 반환됩니다. 예를 들면 **yourclustername.b4.internal.cloudapp.net**과 같습니다.

    > [WACOM.NOTE] 원격 데스크톱을 사용하여 HBase 클러스터에 연결하고(헤드 노드에 연결됨) 명령 프롬프트에서 **ipconfig**를 실행하여 DNS 접미사를 가져올 수도 있습니다. RDP를 사용하도록 설정하고 RDP를 통해 클러스터에 연결하는 방법에 대한 지침은 [Azure 관리 포털을 사용하여 HDInsight에서 Hadoop 클러스터 관리][Azure 관리 포털을 사용하여 HDInsight에서 Hadoop 클러스터 관리]를 참조하세요.
    >
    > ![hdinsight.hbase.dns.surffix][hdinsight.hbase.dns.surffix]

<!--  3.  Change the Primary DNS Suffix configuration of the virtual machine. This enables virtual machine to automatically resolve the host name of the HBase cluster without explicit specification of the suffix. For example, the *workernode0* host name will be correctly resolved to the workernode0 of the HBase cluster.       To make the configuration change:      1. RDP into the virtual machine.      2. Open **Local Group Policy Editor**. The executable is gpedit.msc.     3. Expand **Computer Configuration**, expand **Administrative Templates**, expand **Network**, and then click **DNS Client**.      - Set **Primary DNS Suffix** to the value obtained in the step 2:           ![hdinsight.hbase.primary.dns.suffix][img-primary-dns-suffix]     4. Click **OK**.      5. Reboot the virtual machine. -->

가상 컴퓨터가 HBase 클러스터와 통신할 수 있는지 확인하려면 가상 컴퓨터에서 `ping headnode0.<dns suffix>` 명령을 사용합니다. 예를 들면 ping headnode0.mycluster.b1.cloudapp.net을 사용합니다.

Java 응용 프로그램에서 이 정보를 사용하려는 경우 [Maven을 통해 HDInsight(Hadoop)와 함께 HBase를 사용하는 Java 응용 프로그램 작성][Maven을 통해 HDInsight(Hadoop)와 함께 HBase를 사용하는 Java 응용 프로그램 작성]의 단계에 따라 응용 프로그램을 만들 수 있습니다. 응용 프로그램이 원격 HBase 서버에 연결하도록 하려면 이 예제의 **hbase-site.xml** 파일이 ZooKeeper의 FQDN을 사용하도록 수정합니다. 예를 들면 다음과 같습니다.

    <property>
        <name>hbase.zookeeper.quorum</name>
        <value>zookeeper0.<dns suffix>,zookeeper1.<dns suffix>,zookeeper2.<dns suffix></value>
    </property>

> [WACOM.NOTE] 자체 DNS 서버를 사용하는 방법을 포함한 Azure 가상 네트워크의 이름 확인에 대한 자세한 내용은 [이름 확인(DNS)][이름 확인(DNS)]을 참조하세요.

## <span id="powershell"></span></a>Azure PowerShell을 사용하여 HBase 클러스터 프로비전\*\*

1.  PowerShell ISE를 엽니다.
2.  다음 명령을 복사하여 스크립트 창에 붙여 넣습니다.

        $hbaseClusterName = "<HBaseClusterName>"
        $hadoopUserName = "<HBaseClusterUsername>"
        $hadoopUserPassword = "<HBaseClusterUserPassword>"
        $location = "<HBaseClusterLocation>"  #i.e. "West US"
        $clusterSize = <HBaseClusterSize>  
        $vnetID = "<AzureVirtualNetworkID>"
        $subNetName = "<AzureVirtualNetworkSubNetName>"
        $storageAccountName = "<AzureStorageAccountName>" # Do not use the full name here
        $storageAccountKey = "<AzureStorageAccountKey>"
        $storageContainerName = "<AzureBlobStorageContainer>"

        $password = ConvertTo-SecureString $hadoopUserPassword -AsPlainText -Force
        $creds = New-Object System.Management.Automation.PSCredential ($hadoopUserName, $password) 

        New-AzureHDInsightCluster -Name $hbaseClusterName `
                                  -ClusterType HBase `
                                  -Version 3.1 `
                                  -Location $location `
                                  -ClusterSizeInNodes $clusterSize `
                                  -Credential $creds `
                                  -VirtualNetworkId $vnetID `
                                  -SubnetName $subNetName `
                                  -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" `
                                  -DefaultStorageAccountKey $storageAccountKey `
                                  -DefaultStorageContainerName $storageContainerName

3.  **스크립트 실행**을 클릭하거나 **F5** 키를 누릅니다.
4.  클러스터의 유효성을 검사하기 위해 관리 포털에서 클러스터를 확인하거나 맨 아래 창에서 다음 PowerShell cmdlet을 실행할 수 있습니다.

    Get-AzureHDInsightCluster

## <span id="nextsteps"></span></a>다음 단계

이 자습서에서는 HBase 클러스터를 프로비전하는 방법을 배웠습니다. 자세한 내용은 다음을 참조하세요.

-   [HDInsight 시작][HDInsight 시작]
-   [HDInsight에서 Hadoop 클러스터 프로비전][HDInsight에서 Hadoop 클러스터 프로비전]
-   [HDInsight에서 Hadoop과 함께 HBase를 사용하여 시작][HDInsight에서 Hadoop을 통해 HBase 사용 시작]
-   [HDInsight에서 HBase를 사용하여 Twitter 데이터 분석][HDInsight에서 HBase를 사용하여 Twitter 데이터 분석]
-   [가상 네트워크 개요][가상 네트워크 개요]

  [필수 조건]: #prerequisites
  [가상 네트워크에 HBase 클러스터 프로비전]: #hbaseprovision
  [HBase Java RPC API를 사용하여 가상 네트워크에 프로비전된 HBase 클러스터에 연결]: #connect
  [PowerShell을 사용하여 HBase 클러스터 프로비전]: #powershell
  [다음 단계]: #nextsteps
  [구매 옵션]: http://azure.microsoft.com/ko-kr/pricing/purchase-options/
  [구성원 제공 항목]: http://azure.microsoft.com/ko-kr/pricing/member-offers/
  [무료 평가판]: http://azure.microsoft.com/ko-kr/pricing/free-trial/
  [Azure PowerShell 설치 및 구성]: ../install-configure-powershell
  [Windows PowerShell 스크립트 실행]: http://technet.microsoft.com/ko-kr/library/ee176949.aspx
  [Azure 관리 포털]: https://manage.windowsazure.com
  [HDInsight에서 Hadoop와 함께 Azure Blob 저장소 사용]: ../hdinsight-use-blob-storage/
  [클러스터 이름 및 버전 필드]: ./media/hdinsight-hbase-provision-vnet/hbasewizard1.png
  [노드 수 및 하위 지역 필드]: ./media/hdinsight-hbase-provision-vnet/hbasewizard2.png
  [관리자 이름 및 암호 필드]: ./media/hdinsight-hbase-provision-vnet/hbasewizard3.png
  [저장소 계정 선택]: ./media/hdinsight-hbase-provision-vnet/hbasewizard4.png
  [HDInsight에서 Hadoop을 통해 HBase 사용 시작]: ../hdinsight-hbase-get-started/
  [Windows Server를 실행하는 가상 컴퓨터 만들기]: ../virtual-machines-windows-tutorial/
  [Azure 관리 포털을 사용하여 HDInsight에서 Hadoop 클러스터 관리]: ../hdinsight-administer-use-management-portal/#rdp
  [hdinsight.hbase.dns.surffix]: ./media/hdinsight-hbase-provision-vnet/DNSSuffix.png
  [Maven을 통해 HDInsight(Hadoop)와 함께 HBase를 사용하는 Java 응용 프로그램 작성]: azure.microsoft.com/ko-kr/documentation/articles/hdinsight-hbase-build-java-maven/
  [이름 확인(DNS)]: http://msdn.microsoft.com/ko-kr/library/azure/jj156088.aspx
  [HDInsight 시작]: ../hdinsight-get-started/
  [HDInsight에서 Hadoop 클러스터 프로비전]: ../hdinsight-provision-clusters/
  [HDInsight에서 HBase를 사용하여 Twitter 데이터 분석]: ../hdinsight-hbase-twitter-sentiment/
  [가상 네트워크 개요]: http://msdn.microsoft.com/library/azure/jj156007.aspx
