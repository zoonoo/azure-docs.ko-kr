가용성 그룹 수신기는 SQL Server 가용성 그룹에서 수신하는 IP 주소 및 네트워크 이름입니다. 가용성 그룹 수신기를 만들려면 다음 단계를 수행합니다.

1. [클러스터 네트워크 리소스의 이름을 가져옵니다](#getnet).

1. [클라이언트 액세스 지점을 추가합니다](#addcap).

1. [가용성 그룹에 대한 IP 리소스를 구성합니다](#congroup).

1. [SQL Server 가용성 그룹 리소스가 클라이언트 액세스 지점에 종속되게 합니다](#dependencyGroup).

1. [클라이언트 액세스 지점 리소스가 IP 주소에 종속되게 합니다](#listname).

1. [PowerShell에서 클러스터 매개 변수를 설정합니다](#setparam).

다음 섹션은 이러한 각 단계에 대한 자세한 지침을 제공합니다. 

#### <a name="a-namegetnetaget-the-name-of-the-cluster-network-resource"></a><a name="getnet"></a>클러스터 네트워크 리소스의 이름 가져오기

1. RDP를 사용하여 주 복제본을 호스트하는 Azure 가상 컴퓨터에 연결합니다. 

1. 장애 조치(failover) 클러스터 관리자를 엽니다.

1. **네트워크** 노드를 선택하고 클러스터 네트워크 이름을 확인합니다. 이 이름을 PowerShell 스크립트에서 `$ClusterNetworkName` 변수에 사용합니다.

   다음 그림에서 클러스터 네트워크 이름은 **클러스터 네트워크 1**입니다.

   ![클러스터 네트워크 이름](./media/virtual-machines-ag-listener-configure/90-clusternetworkname.png)

#### <a name="a-nameaddcapaadd-the-client-access-point"></a><a name="addcap"></a>클라이언트 액세스 지점 추가

클라이언트 액세스 지점은 응용 프로그램이 가용성 그룹의 데이터베이스에 연결하는 데 사용하는 네트워크 이름입니다. 장애 조치(Failover) 클러스터 관리자에서 클라이언트 액세스 지점을 만듭니다. 

1. 클러스터 이름을 확장한 다음 **역할**을 클릭합니다.

1. **역할** 창에서 가용성 그룹 이름을 마우스 오른쪽 단추로 클릭한 다음 **리소스 추가** > **클라이언트 액세스 지점**을 선택합니다.

   ![클라이언트 액세스 지점](./media/virtual-machines-ag-listener-configure/92-addclientaccesspoint.png)

1. **이름** 상자에서 이 새 수신기의 이름을 만듭니다. 

   새 수신기 이름은 응용 프로그램에서 SQL Server 가용성 그룹의 데이터베이스에 연결하는 데 사용하는 네트워크 이름입니다.
   
   수신기 만들기를 완료하려면 **다음**을 두 번 클릭한 다음 **마침**을 클릭합니다. 현재 온라인 상태에서 수신기 또는 리소스를 가져오지 마세요.
   
#### <a name="a-namecongroupaconfigure-the-ip-resource-for-the-availability-group"></a><a name="congroup"></a>가용성 그룹에 대한 IP 리소스 구성

1. **리소스** 탭을 클릭한 다음 만든 클라이언트 액세스 지점을 확장합니다. 클라이언트 액세스 지점은 오프라인입니다.

   ![클라이언트 액세스 지점](./media/virtual-machines-ag-listener-configure/94-newclientaccesspoint.png) 

1. IP 리소스를 마우스 오른쪽 단추로 클릭하고 속성을 클릭합니다. IP 주소의 이름을 적어둡니다. 이 이름을 PowerShell 스크립트에서 `$IPResourceName` 변수에 사용합니다.

1. **IP 주소**에서 **고정 IP 주소**를 클릭합니다. Azure Portal에서 부하 분산 장치 주소를 설정할 때 사용한 주소와 동일한 주소로 IP 주소를 설정합니다.

   ![IP 리소스](./media/virtual-machines-ag-listener-configure/96-ipresource.png) 

<!-----------------------I don't see this option on server 2016
1. Disable NetBIOS for this address and click **OK**. Repeat this step for each IP resource if your solution spans multiple Azure VNets. 
------------------------->

#### <a name="a-name--dependencygroupamake-the-sql-server-availability-group-resource-dependent-on-the-client-access-point"></a><a name = "dependencyGroup"></a>SQL Server 가용성 그룹 리소스가 클라이언트 액세스 지점에 종속되게 하기

1. 장애 조치(Failover) 클러스터 관리자에서 **역할**을 클릭하고 가용성 그룹을 클릭합니다.

1. **리소스** 탭에서 **서버 이름** 아래의 가용성 리소스 그룹을 마우스 오른쪽 단추로 클릭하고 **속성**을 클릭합니다. 

1. 종속성 탭에서 이름 리소스를 추가합니다. 이 리소스는 클라이언트 액세스 지점입니다. 

   ![IP 리소스](./media/virtual-machines-ag-listener-configure/97-propertiesdependencies.png) 

1. **확인**을 클릭합니다.

#### <a name="a-namelistnameamake-the-client-access-point-resource-dependent-on-the-ip-address"></a><a name="listname"></a>클라이언트 액세스 지점 리소스가 IP 주소에 종속되게 하기

1. 장애 조치(Failover) 클러스터 관리자에서 **역할**을 클릭하고 가용성 그룹을 클릭합니다. 

1. **리소스** 탭에서 **서버 이름** 아래의 클라이언트 액세스 지점 리소스를 마우스 오른쪽 단추로 클릭하고 **속성**을 클릭합니다. 

   ![IP 리소스](./media/virtual-machines-ag-listener-configure/98-dependencies.png) 

1. **종속성** 탭을 클릭합니다. 수신기 리소스 이름에 대한 종속성을 설정합니다. 여러 리소스가 나열되어 있으면 IP 주소에 AND가 아닌 OR 종속성이 있는지 확인합니다. **확인**을 클릭합니다. 

   ![IP 리소스](./media/virtual-machines-ag-listener-configure/98-propertiesdependencies.png) 

1. 수신기 이름을 마우스 오른쪽 단추로 클릭하고 **온라인 상태로 전환**을 클릭합니다. 

#### <a name="a-namesetparamaset-the-cluster-parameters-in-powershell"></a><a name="setparam"></a>PowerShell에서 클러스터 매개 변수 설정

1. 다음 PowerShell 스크립트를 SQL Server 중 하나에 복사합니다. 사용자 환경에 맞게 변수를 업데이트합니다.     
   ```PowerShell
   $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
   $IPResourceName = "<IPResourceName>" # the IP Address resource name
   $ILBIP = “<n.n.n.n>” # the IP Address of the Internal Load Balancer (ILB). This is the static IP address for the load balancer you configured in the Azure portal.
   [int]$ProbePort = <nnnnn>

   Import-Module FailoverClusters

   Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"=$ProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
   ```

2. 클러스터 노드 중 하나에서 PowerShell 스크립트를 실행하여 클러스터 매개 변수를 설정합니다.  

> [!NOTE]
> SQL Server가 별도 지역에 있는 경우 PowerShell 스크립트를 두 번 실행해야 합니다. 처음으로 첫 번째 지역에서 `$ILBIP` 및 `$ProbePort`를 사용합니다. 두 번째로 두 번째 지역에서 `$ILBIP` 및 `$ProbePort`를 사용합니다. 클러스터 네트워크 이름 및 클러스터 IP 리소스 이름이 동일합니다. 




<!--HONumber=Jan17_HO2-->


