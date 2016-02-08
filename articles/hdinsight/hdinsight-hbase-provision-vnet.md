<properties
	pageTitle="가상 네트워크에 HBase 클러스터 프로비전 | Microsoft Azure"
	description="Azure HDInsight에서 HBase 사용 시작 Azure 가상 네트워크에 HDInsight HBase 클러스터를 만드는 방법을 알아봅니다."
	keywords=""
	services="hdinsight,virtual-network"
	documentationCenter=""
	authors="mumian"
	manager="paulettm"
	editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="11/18/2015"
   ms.author="jgao"/>

# Azure 가상 네트워크에 HBase 클러스터 프로비전 

[Azure 가상 네트워크][1]에서 Azure HDInsight HBase 클러스터를 만드는 방법에 대해 알아봅니다.

[AZURE.INCLUDE [hdinsight-azure-preview-portal](../../includes/hdinsight-azure-preview-portal.md)]

* [Azure 가상 네트워크에 HBase 클러스터 프로비전](hdinsight-hbase-provision-vnet-v1.md)

가상 네트워크 통합을 사용하면 응용 프로그램이 HBase와 직접 통신할 수 있도록 응용 프로그램과 동일한 가상 네트워크에 HBase 클러스터를 배포할 수 있습니다. 이점은 다음과 같습니다.

- 웹 응용 프로그램을 HBase 클러스터의 노드에 직접 연결하므로 HBase Java RPC(원격 프로시저 호출) API를 통해 통신할 수 있습니다.
- 트래픽이 여러 게이트웨이 및 부하 분산 장치를 통과하지 않도록 하여 성능을 향상시킵니다.
- 공용 끝점을 노출하지 않고 중요한 정보를 보다 안전한 방식으로 처리할 수 있습니다.

##필수 조건
이 자습서를 시작하기 전에 다음이 있어야 합니다.

- **Azure 구독**. [Azure 무료 평가판](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)을 참조하세요.

- **Azure PowerShell이 포함된 워크스테이션**. [Azure PowerShell 설치 및 사용](https://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/)을 참조하세요. 자세한 내용은 [Azure PowerShell 설치 및 구성](../install-configure-powershell.md)을 참조하세요. Azure PowerShell 스크립트를 실행하려면 관리자로 Azure PowerShell을 실행하고 실행 정책을 *RemoteSigned*로 설정해야 합니다. [Set-executionpolicy cmdlet 사용][2]을 참조하세요.

	Azure PowerShell 스크립트를 실행하기 전에 cmdlet을 사용하여 Azure 구독에 연결되어 있는지 확인합니다.

		Add-AzureAccount

	여러 Azure 구독이 있는 경우 다음 cmdlet을 사용하여 현재 구독을 설정합니다.

		Select-AzureSubscription <AzureSubscriptionName>


## 가상 네트워크에 HBase 클러스터 프로비전

응용 프로그램은 일반적으로 많은 구성 요소로 구성됩니다. 이 자습서에서는 다음을 사용합니다.

- Azure 가상 네트워크
- Azure 저장소 계정
- Azure HDInsight HBase 클러스터
- (옵션) DNS 서버 역할을 하는 Azure 가상 컴퓨터

Azure 리소스 관리자를 사용하면 그룹으로 응용 프로그램에서 리소스와 함께 사용할 수 있습니다. 응용 프로그램에 대한 모든 리소스의 배포, 업데이트 또는 삭제를 조정된 단일 작업으로 수행할 수 있습니다. 배포용 템플릿을 사용하고 이 템플릿을 테스트, 스테이징 및 프로덕션와 같은 여러 환경에서 사용할 수 있습니다. 전체 그룹에 대한 롤업 비용을 확인하여 조직에 요금 청구를 명확히 할 수 있습니다.

**리소스 그룹을 만들려면**

1. [Azure 포털](https://portal.azure.com)에 로그인합니다.
2. **새로 만들기**, **관리** 및 **리소스 그룹**을 차례로 클릭합니다.
3. 다음 값을 입력하거나 선택합니다.

	- **리소스 그룹 이름**: 리소스 그룹의 이름을 입력합니다.
	- **구독**: 이 리소스 그룹에 사용되는 Azure 구독을 선택합니다.
	- **리소스 그룹 위치**: Azure 데이터 센터를 선택합니다. 이 위치는 HDInsight 클러스터 위치와 일치하지 않아도 됩니다.

4. **만들기**를 클릭합니다.

HBase 클러스터를 프로비전하기 전에 Azure 가상 네트워크가 있어야 합니다.

**Azure 포털을 사용하여 가상 네트워크를 만들려면**

1. [포털](https://portal.azure.com)에 로그인합니다.
2. **새로 만들기**, **네트워킹** 및 **가상 네트워크**를 차례로 클릭합니다.
3. **배포 모델 선택**에서 Windows 기반 HDInsight 클러스터를 사용하려면 **클래식**을 선택하고 Linux 기반 HDInsight 클러스터를 사용하려면 **리소스 관리자**를 선택합니다. 마지막으로 **만들기**를 클릭합니다.

    > [AZURE.NOTE] Windows 기반 클러스터에는 v1(클래식) 가상 네트워크가 필요하고, Linux 기반 클러스터에는 v2(Azure 리소스 관리자) 가상 네트워크가 필요합니다. 올바른 유형의 네트워크가 없으면, 클러스터를 만들어도 사용할 수 없습니다.
    >
    > 만들려는 클러스터에서 사용할 수 없는 가상 네트워크에 리소스가 있는 경우에는, 클러스터에서 사용할 수 있는 새로운 가상 네트워크를 만들어서 호환되지 않는 가상 네트워크에 연결할 수 있습니다. 그 후 필요한 네트워크 버전에서 클러스터를 만들면 두 네트워크가 연결되어 있기 때문에 다른 네트워크에 있는 리소스에 액세스할 수 있습니다. 클래식 가상 네트워크와 새 가상 네트워크 연결에 대한 내용은 [새 VNet에 클래식 VNet 연결](../virtual-network/virtual-networks-arm-asm-s2s.md)을 참조하세요.
    
4. 다음 값을 입력하거나 선택합니다.

	- **이름**: 가상 네트워크의 이름입니다.
	- **주소 공간**: 클러스터의 모든 노드에 대한 주소를 제공하기에 충분히 큰 가상 네트워크의 주소 공간을 선택합니다. 그렇지 않으면 프로비전에 실패합니다. 이 자습서를 연습할 때는 기본값을 사용할 수 있습니다. **확인**을 클릭하여 변경 내용을 저장합니다.
    
        > [AZURE.NOTE] 가상 네트워크를 여러 HDInsight 클러스터와 사용하려는 경우에는 각 클러스터에 대해 단일 서브넷을 지정하는 것이 좋습니다.
         
	- **리소스 그룹**: 자습서의 앞부분에서 만든 리소스 그룹을 선택합니다.
	- **구독**: 이 가상 네트워크에 사용할 Azure 구독을 선택합니다.
	- **위치** - 위치는 만들 HBase 클러스터와 동일해야 합니다.
    
        > [AZURE.NOTE] > Azure HDInsight는 위치 기반 가상 네트워크만 지원하며 선호도 그룹을 기반으로 하는 가상 네트워크와는 현재 연동되지 않습니다.
        
    가상 네트워크에 대한 특정 구성 요구 사항을 포함하여 가상 네트워크로 HDInsight를 사용하는 방법에 대한 자세한 내용은 [Azure 가상 네트워크를 사용하여 HDInsight 기능 확장](hdinsight-extend-hadoop-virtual-network.md)을 참조하세요.

5. **만들기**를 클릭합니다.

기본적으로 가상 네트워크에서는 Azure에서 제공하는 내부 DNS(Domain Name System) 서버를 사용합니다. 사용자 지정 DNS 서버가 포함된 고급 네트워킹 구성도 지원됩니다. 자세한 지침이 필요하면 [이름 확인(DNS)](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)을 참조하세요.

**(옵션) 가상 네트워크에 DNS 서버 가상 컴퓨터를 추가하려면**

DNS 서버는 선택 사항이지만 반드시 필요한 경우도 있습니다. 절차는 [두 Azure 가상 네트워크 간의 DNS 구성][hdinsight-hbase-replication-dns]에 나와 있습니다. 기본적으로 다음 단계를 수행해야 합니다.

1. 가상 네트워크에 Azure 가상 컴퓨터 추가
2. 가상 컴퓨터의 고정 IP 주소 설정
3. 가상 컴퓨터에 DNS 서버 역할 추가
4. 가상 네트워크에 DNS 서버 할당

**Azure 포털을 사용하여 HBase 클러스터를 프로비전하려면**

> [AZURE.NOTE] Azure PowerShell을 사용하여 새 HBase 클러스터를 프로비전하는 방법에 대한 자세한 내용은 [Azure PowerShell을 사용하여 HBase 클러스터 프로비전](#powershell)을 참조하세요.


**HDInsight 클러스터를 만들려면**

1. [Azure 포털](https://portal.azure.com)에 로그인합니다.
2. **새로 만들기**, **데이터 분석** 및 **HDInsight**를 차례로 클릭합니다.

    ![Azure 포털에서 새 클러스터 만들기](./media/hdinsight-provision-clusters/HDI.CreateCluster.1.png "Azure 포털에서 새 클러스터 만들기")

3. 다음 값을 입력하거나 선택합니다.

  - **클러스터 이름**: 클러스터의 이름을 입력합니다. 이름을 사용할 수 있는 경우 클러스터 이름 옆에 녹색 확인 표시가 나타납니다.
  - **클러스터 유형**: **HBase**를 선택합니다.
  - **클러스터 운영 체제**: **Windows Server 2012 R2 Datacenter**를 선택합니다.
  - **구독**: 이 클러스터를 프로비전하는 데 사용할 Azure 구독을 선택합니다.
  - **리소스 그룹**: 자습서의 앞부분에서 만든 리소스 그룹을 선택합니다.
  - **자격 증명**: Hadoop 사용자(HTTP 사용자)의 사용자 이름 및 암호를 구성합니다. 클러스터에 원격 데스크톱을 사용하도록 설정하는 경우 원격 데스크톱 사용자의 사용자 이름 및 암호와 계정 만료 날짜를 구성해야 합니다. 아래쪽의 **선택**을 클릭하여 변경 내용을 저장합니다.
  - **데이터 원본**: 클러스터의 기본 파일 시스템으로 사용할 기존 Azure 저장소 계정을 선택하거나 새 Azure 저장소 계정을 만듭니다. 기본 컨테이너의 기본 이름은 클러스터 이름입니다. 또한 저장소 계정의 위치는 클러스터의 위치를 결정합니다.
  - **노드 가격 책정 계층**: 학습 또는 평가 목적을 위해서 지역 노드 1개를 선택하여 비용을 최소화합니다.

  	- **선택 방법**: 모든 구독에서 저장소 계정을 찾을 수 있도록 하려면 이 항목을 **모든 구독에서**로 설정합니다. 기존 저장소 계정의 **저장소 이름** 및 **액세스 키**를 입력하려면 이 항목을 **액세스 키**로 설정합니다.
  	- **저장소 계정 선택/새로 만들기**: 클러스터와 연결할 기존 저장소 계정을 찾아 선택하려면 **저장소 계정 선택**을 클릭합니다. 또는 새 저장소 계정을 만들려면 **새로 만들기**를 클릭합니다. 저장소 계정의 이름을 입력할 때 나타나는 필드를 사용합니다. 이름을 사용할 수 있는 경우 녹색 확인 표시가 나타납니다.
    - **기본 컨테이너 선택**: 클러스터에 사용할 기본 컨테이너의 이름을 입력하려면 이 항목을 사용합니다. 여기에 아무 이름이나 입력할 수 있지만, 컨테이너가 이 특정 클러스터에 사용됨을 쉽게 인식할 수 있도록 클러스터와 같은 이름을 사용하는 것이 좋습니다.
  	- **위치**: 저장소 계정이 있거나 저장소 계정을 만들 지리적인 지역입니다. 이 위치는 클러스터 위치를 결정합니다. 클러스터와 해당 기본 저장소 계정은 같은 Azure 데이터 센터에 공동 배치되어야 합니다.

  - **노드 가격 책정 계층**: 클러스터에 필요한 작업자 노드 수를 설정합니다. 클러스터의 예상 비용이 블레이드 내에 표시됩니다.
	- **옵션 구성**: 이 자습서에서는 **가상 네트워크**만 구성하면 됩니다. 자습서의 앞부분에서 만든 가상 네트워크를 선택합니다. 서브넷도 선택해야 합니다.

4. **만들기**를 클릭합니다.


새 HBase 클러스터 사용을 시작하려는 경우 [HDInsight에서 Hadoop을 통해 HBase 사용 시작](../hdinsight-hbase-get-started.md)의 절차를 사용할 수 있습니다.

##HBase Java RPC API를 사용하여 가상 네트워크에 프로비전된 HBase 클러스터에 연결

1.	동일한 Azure 가상 네트워크와 동일한 서브넷에 IaaS(infrastructure as a service) 가상 컴퓨터를 프로비전합니다. 따라서 가상 컴퓨터와 HBase 클러스터가 둘 다 동일한 내부 DNS 서버를 사용하여 호스트 이름을 확인합니다. 이렇게 하려면 **갤러리에서** 옵션을 선택한 다음 데이터 센터 대신 가상 네트워크를 선택해야 합니다. 지침에 대해서는 [Windows Server를 실행하는 가상 컴퓨터 만들기](../virtual-machines-windows-tutorial.md)를 참조하세요. 작은 VM 크기의 표준 Windows Server 2012 이미지면 충분합니다.

2.	Java 응용 프로그램을 사용하여 HBase에 원격으로 연결할 때는 FQDN(정규화된 도메인 이름)을 사용해야 합니다. FQDN을 확인하려면 HBase 클러스터의 연결별 DNS 접미사를 가져와야 합니다. 이렇게 하려면 Curl을 사용하여 Ambari를 쿼리하거나 원격 데스크톱을 사용하여 클러스터에 연결합니다.

	* **Curl** - 다음 명령을 사용합니다.

			curl -u <username>:<password> -k https://<clustername>.azurehdinsight.net/ambari/api/v1/clusters/<clustername>.azurehdinsight.net/services/hbase/components/hbrest

		반환되는 JSON(JavaScript Object Notation) 데이터에서 "host\_name" 항목을 찾습니다. 이 항목에는 클러스터의 노드에 대한 FQDN이 포함되어 있습니다. 예:

			...
			"host_name": "wordkernode0.<clustername>.b1.cloudapp.net
			...

		클러스터 이름으로 시작하는 도메인 이름 부분이 DNS 접미사입니다. 예를 들면 mycluster.b1.cloudapp.net과 같습니다.

	* **Azure PowerShell** - 다음 Azure PowerShell 스크립트를 사용하여 DNS 접미사를 반환하는 데 사용할 수 있는 **Get-ClusterDetail** 함수를 등록합니다.

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
			     Displays information to facilitate an HDInsight cluster-to-cluster scenario within the same virtual network.
				.Description
				 This command shows the following 4 properties of an HDInsight cluster:
				 1. ZookeeperQuorum (supports only HBase type cluster)
					Shows the value of HBase property "hbase.zookeeper.quorum".
				 2. ZookeeperClientPort (supports only HBase type cluster)
					Shows the value of HBase property "hbase.zookeeper.property.clientPort".
				 3. HBaseRestServers (supports only HBase type cluster)
					Shows a list of host FQDNs that run the HBase REST server.
				 4. FQDNSuffix (supports all cluster types)
					Shows the FQDN suffix of hosts in the cluster.
			    .EXAMPLE
			     Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName ZookeeperQuorum
			     This command shows the value of HBase property "hbase.zookeeper.quorum".
			    .EXAMPLE
			     Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName ZookeeperClientPort
			     This command shows the value of HBase property "hbase.zookeeper.property.clientPort".
			    .EXAMPLE
			     Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName HBaseRestServers
			     This command shows a list of host FQDNs that run the HBase REST server.
			    .EXAMPLE
			     Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName FQDNSuffix
			     This command shows the FQDN suffix of hosts in the cluster.
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

		Azure PowerShell 스크립트를 실행한 후 다음 명령을 사용하여 **Get-ClusterDetail** 함수를 통해 DNS 접미사를 반환합니다. 이 명령을 사용할 때는 HDInsight HBase 클러스터 이름, 관리자 이름 및 관리자 암호를 지정합니다.

			Get-ClusterDetail -ClusterDnsName <yourclustername> -PropertyName FQDNSuffix -Username <clusteradmin> -Password <clusteradminpassword>

		그러면 DNS 접미사가 반환됩니다. 예를 들면 **yourclustername.b4.internal.cloudapp.net**과 같습니다.

	> [AZURE.NOTE] 원격 데스크톱을 사용하여 HBase 클러스터에 연결하고(헤드 노드에 연결됨) 명령 프롬프트에서 **ipconfig**를 실행하여 DNS 접미사를 가져올 수도 있습니다. RDP를 사용하도록 설정하고 RDP(원격 데스크톱 프로토콜)를 통해 클러스터에 연결하는 방법에 대한 지침은 [Azure 포털을 사용하여 HDInsight에서 Hadoop 클러스터 관리][hdinsight-admin-portal]를 참조하세요.
	>
	> ![hdinsight.hbase.dns.surffix][img-dns-surffix]


<!--
3.	Change the primary DNS suffix configuration of the virtual machine. This enables the virtual machine to automatically resolve the host name of the HBase cluster without explicit specification of the suffix. For example, the *workernode0* host name will be correctly resolved to workernode0 of the HBase cluster.

	To make the configuration change:

	1. RDP into the virtual machine.
	2. Open **Local Group Policy Editor**. The executable is gpedit.msc.
	3. Expand **Computer Configuration**, expand **Administrative Templates**, expand **Network**, and then click **DNS Client**.
	- Set **Primary DNS Suffix** to the value obtained in step 2:

		![hdinsight.hbase.primary.dns.suffix][img-primary-dns-suffix]
	4. Click **OK**.
	5. Reboot the virtual machine.
-->

가상 컴퓨터가 HBase 클러스터와 통신할 수 있는지 확인하려면 가상 컴퓨터에서 `ping headnode0.<dns suffix>` 명령을 사용합니다. 예를 들면 ping headnode0.mycluster.b1.cloudapp.net을 사용합니다.

Java 응용 프로그램에서 이 정보를 사용하려는 경우 [Maven을 통해 HDInsight(Hadoop)와 함께 HBase를 사용하는 Java 응용 프로그램 작성](hdinsight-hbase-build-java-maven.md)의 단계에 따라 응용 프로그램을 만들 수 있습니다. 응용 프로그램이 원격 HBase 서버에 연결하도록 하려면 이 예제의 **hbase-site.xml** 파일이 ZooKeeper의 FQDN을 사용하도록 수정합니다. 예:

	<property>
    	<name>hbase.zookeeper.quorum</name>
    	<value>zookeeper0.<dns suffix>,zookeeper1.<dns suffix>,zookeeper2.<dns suffix></value>
	</property>

> [AZURE.NOTE] 자체 DNS 서버를 사용하는 방법을 포함한 Azure 가상 네트워크의 이름 확인에 대한 자세한 내용은 [이름 확인(DNS)](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)을 참조하세요.

##Azure PowerShell을 사용하여 HBase 클러스터 프로비전

**Azure PowerShell을 사용하여 HBase 클러스터를 프로비전하려면**

1. Azure PowerShell ISE(통합 스크립팅 환경)를 엽니다.
2. 다음을 복사하여 스크립트 창에 붙여 넣습니다.

		$hbaseClusterName = "<HBaseClusterName>"
		$hadoopUserName = "<HBaseClusterUsername>"
		$hadoopUserPassword = "<HBaseClusterUserPassword>"
		$location = "<HBaseClusterLocation>"  #i.e. "West US"
		$clusterSize = <HBaseClusterSize>  
		$resourceGroup = "<AzureResourceGroupName>"
		$vnetID = "<AzureVirtualNetworkID>"
		$subNetName = "<AzureVirtualNetworkSubNetName>"
		$storageAccountName = "<AzureStorageAccountName>" # Do not use the full name here
		$storageAccountKey = "<AzureStorageAccountKey>"
		$storageContainerName = "<AzureBlobStorageContainer>"

		$password = ConvertTo-SecureString $hadoopUserPassword -AsPlainText -Force
		$creds = New-Object System.Management.Automation.PSCredential ($hadoopUserName, $password)

		New-AzureHDInsightCluster -ResourceGroupName $resourceGroup `
		                          -ClusterName $hbaseClusterName `
				                    	-ClusterType HBase `
				                    	-Location $location `
				                    	-ClusterSizeInNodes $clusterSize `
		                          -HttpCredential $creds `
				                    	-VirtualNetworkId $vnetID `
				                    	-SubnetName $subNetName `
				                    	-DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" `
				                    	-DefaultStorageAccountKey $storageAccountKey `
		                          -DefaultStorageContainer $storageContainerName


3. **스크립트 실행**을 클릭하거나 **F5** 키를 누릅니다.
4. 클러스터의 유효성을 검사하기 위해 Azure 포털에서 클러스터를 확인하거나 맨 아래 창에서 다음 Azure PowerShell cmdlet을 실행할 수 있습니다.

	Get-AzureHDInsightCluster

##다음 단계

이 자습서에서는 HBase 클러스터를 프로비전하는 방법을 배웠습니다. 자세한 내용은 다음을 참조하세요.

- [HDInsight 시작](../hdinsight-get-started.md)
- [HDInsight에서 HBase 복제 구성](hdinsight-hbase-geo-replication.md)
- [HDInsight에서 Hadoop 클러스터 프로비전](hdinsight-provision-clusters.md)
- [HDInsight의 Hadoop에서 HBase 사용 시작](../hdinsight-hbase-get-started.md)
- [HDInsight에서 HBase를 사용하여 Twitter 데이터 분석](../hdinsight-hbase-twitter-sentiment.md)
- [가상 네트워크 개요][vnet-overview]


[1]: http://azure.microsoft.com/services/virtual-network/
[2]: http://technet.microsoft.com/library/ee176961.aspx
[3]: http://technet.microsoft.com/library/hh847889.aspx

[hbase-get-started]: ../hdinsight-hbase-get-started.md
[hbase-twitter-sentiment]: ../hdinsight-hbase-twitter-sentiment.md
[vnet-overview]: ../virtual-network/virtual-networks-overview.md
[vm-create]: ../virtual-machines-windows-tutorial.md

[azure-portal]: https://portal.azure.com
[azure-create-storageaccount]: ../storage-create-storage-account.md
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md#rdp

[hdinsight-powershell-reference]: https://msdn.microsoft.com/library/dn858087.aspx


[twitter-streaming-api]: https://dev.twitter.com/docs/streaming-apis
[twitter-statuses-filter]: https://dev.twitter.com/docs/api/1.1/post/statuses/filter


[powershell-install]: ../install-configure-powershell.md


[hdinsight-customize-cluster]: hdinsight-hadoop-customize-cluster.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-get-started]: ../hdinsight-get-started.md
[hdinsight-storage-powershell]: ../hdinsight-use-blob-storage.md#powershell
[hdinsight-analyze-flight-delay-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-storage]: ../hdinsight-use-blob-storage.md
[hdinsight-use-sqoop]: hdinsight-use-sqoop.md
[hdinsight-power-query]: hdinsight-connect-excel-power-query.md
[hdinsight-hive-odbc]: hdinsight-connect-excel-hive-ODBC-driver.md
[hdinsight-hbase-replication-dns]: hdinsight-hbase-geo-replication-configure-DNS.md

[img-dns-surffix]: ./media/hdinsight-hbase-provision-vnet/DNSSuffix.png
[img-primary-dns-suffix]: ./media/hdinsight-hbase-provision-vnet/PrimaryDNSSuffix.png
[img-provision-cluster-page1]: ./media/hdinsight-hbase-provision-vnet/hbasewizard1.png "새 HBase 클러스터에 대한 프로비전 정보"
[img-provision-cluster-page5]: ./media/hdinsight-hbase-provision-vnet/hbasewizard5.png "스크립트 작업을 사용하여 HBase 클러스터 사용자 지정"

[azure-preview-portal]: https://portal.azure.com

<!---HONumber=AcomDC_0128_2016-->