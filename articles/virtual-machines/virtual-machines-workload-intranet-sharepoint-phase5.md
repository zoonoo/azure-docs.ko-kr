<properties
	pageTitle="SharePoint Server 2013 팜 5단계 | Microsoft Azure"
	description="Azure의 SharePoint Server 2013 팜 5단계에서 가용성 그룹을 만들고 여기에 SharePoint 데이터베이스를 추가합니다."
	documentationCenter=""
	services="virtual-machines"
	authors="JoeDavies-MSFT"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/21/2015"
	ms.author="josephd"/>

# SharePoint 인트라넷 팜 작업 5단계: 가용성 그룹을 만들고 SharePoint 데이터베이스 추가

Azure 인프라 서비스의 SQL Server AlwaysOn 가용성 그룹을 사용하여 인트라넷 전용 SharePoint 2013 팜을 배포하는 이 마지막 단계에서는 새 AlwaysOn 가용성 그룹을 만들고 SharePoint 팜의 데이터베이스를 추가합니다.

전체 단계를 보려면 [Azure에서 SQL Server AlwaysOn 가용성 그룹을 사용하여 SharePoint 배포](virtual-machines-workload-intranet-sharepoint-overview.md)를 참조하세요.

## 가용성 그룹을 만들고 데이터베이스 추가

SharePoint는 초기 구성의 일부로 여러 데이터베이스를 만듭니다. 이 단계에서는 해당 데이터베이스를 준비해야 합니다.

1.	주 컴퓨터에서 데이터베이스의 전체 백업 및 트랜잭션 로그 백업을 수행합니다.
2.	백업 컴퓨터에서 전체 및 로그 백업을 복원합니다.

백업 및 복원한 데이터베이스는 가용성 그룹에 추가할 수 있습니다. SQL Server에서는 한 번 이상 백업했으며 다른 컴퓨터에서 복원한 데이터베이스만 그룹에 포함하도록 허용합니다.

### 백업 폴더 공유

백업 및 복원을 사용하려면 백업 파일(.bak)을 보조 SQL Server VM에서 액세스할 수 있어야 합니다. 이렇게 하려면 다음 절차를 수행합니다.

1.	**[도메인]\\sp\_farm\_db**로 주 SQL Server 호스트에 로그온합니다.
2.	F:\\ 디스크로 이동합니다.
3.	**Backup** 폴더를 마우스 오른쪽 단추로 클릭하고 **공유 대상**을 클릭한 후에 **특정 사용자**를 클릭합니다.
4.	**파일 공유** 대화 상자에서 **[도메인]\\sqlservice**를 입력하고 **추가**를 클릭합니다.
5.	**sqlservice** 계정 이름의 **권한 수준** 열을 클릭하고 **읽기/쓰기**를 클릭합니다.
6.	**공유**, **완료**를 차례로 클릭합니다.

보조 SQL Server 호스트에서 위 절차를 수행합니다. 단, 이번에는 5단계에서 F:\\Backup 폴더에 대해 sqlservice 계정에 **읽기** 권한을 부여합니다.

### 데이터베이스 백업 및 복원

가용성 그룹에 추가해야 하는 모든 데이터베이스에 대해 다음 절차를 반복해야 합니다. 일부 SharePoint 2013 데이터베이스는 SQL Server AlwaysOn 가용성 그룹을 지원하지 않습니다. 자세한 내용은 [SharePoint 데이터베이스에 대해 지원되는 고가용성 및 재해 복구 옵션](http://technet.microsoft.com/library/jj841106.aspx)을 참조하세요.

데이터베이스를 백업하려면 다음 단계를 수행합니다.

1.	주 SQL Server 컴퓨터의 시작 화면에서 **SQL Studio**를 입력하고 **SQL Server Management Studio**를 클릭합니다.
2.	**Connect**를 클릭합니다.
3.	왼쪽 창에서 **데이터베이스** 노드를 확장합니다.
4.	백업할 데이터베이스를 마우스 오른쪽 단추로 클릭하고 **작업**을 가리킨 다음 **백업**을 클릭합니다.
5.	**대상** 섹션에서 **제거**를 클릭하여 백업 파일의 기본 파일 경로를 제거합니다.
6.	**추가**를 클릭합니다. **파일 이름**에 **\[machineName]\\backup[databaseName].bak**를 입력합니다. 여기서 machineName은 주 SQL Server 컴퓨터의 이름이고 databaseName은 데이터베이스의 이름입니다. **확인**을 클릭한 다음 백업 성공 메시지가 표시되면 **확인**을 다시 클릭합니다.
7.	왼쪽 창에서 **[databaseName]**을 마우스 오른쪽 단추로 클릭하고 **작업**을 가리킨 다음 **백업**을 클릭합니다.
8.	**백업 유형**에서 **트랜잭션 로그**를 선택하고 **확인**을 두 번 클릭합니다.
9.	이 원격 데스크톱 세션을 열어 둡니다.

데이터베이스를 복원하려면 다음 단계를 수행합니다.

1.	보조 SQL Server 컴퓨터에 **[domainName]\\sp\_farm\_db**로 로그온합니다.
2.	시작 화면에서 **SQL Studio**를 입력하고 **SQL Server Management Studio**를 클릭합니다.
3.	**Connect**를 클릭합니다.
4.	왼쪽 창에서 **데이터베이스**를 마우스 오른쪽 단추로 클릭하고 **데이터베이스 복원**을 클릭합니다.
5.	**원본** 섹션에서 **장치**를 선택하고 줄임표(...) 단추를 클릭합니다.
6.	**백업 장치 선택**에서 **추가**를 클릭합니다.
7.	**백업 파일 위치**에 **\[machineName]\\backup**을 입력하고 Enter를 누른 다음, **[databaseName].bak**를 선택하고**확인**을 두 번 클릭합니다. 그러면 **복원할 백업 세트** 섹션에 전체 백업 및 로그 백업이 표시됩니다.
8.	**페이지 선택**에서 **옵션**을 클릭합니다. **복원 옵션** 섹션의 **복구 상태**에서 **RESTORE WITH NORECOVERY**를 선택하고 **확인**을 클릭합니다.
9.	메시지가 표시되면 **확인**을 클릭합니다.

### 가용성 그룹 만들기

백업 및 복원 방법을 사용하여 데이터베이스를 하나 이상 준비한 후에 가용성 그룹을 만듭니다.

1.	주 SQL Server 컴퓨터의 원격 데스크톱 세션으로 돌아옵니다.
2.	**SQL Server Management Studio**의 왼쪽 창에서 **AlwaysOn 고가용성**을 마우스 오른쪽 단추로 클릭하고 **새 가용성 그룹 마법사**를 클릭합니다.
3.	**소개** 페이지에서 **다음**을 클릭합니다.
4.	**가용성 그룹 이름 지정** 페이지에서 가용성 그룹의 이름을 **가용성 그룹 이름**에 AG1과 같이 입력한 후에 **다음**을 클릭합니다.
5.	**데이터베이스 선택** 페이지에서 백업한 SharePoint 팜용 데이터베이스를 선택하고 **다음**을 클릭합니다. 원하는 주 복제본에서 전체 백업을 하나 이상 생성했으므로, 이러한 데이터베이스는 가용성 그룹의 필수 구성 요소를 충족합니다.
6.	**복제본 지정** 페이지에서 **복제본 추가**를 클릭합니다.
7.	**서버에 연결**에 보조 SQL Server 컴퓨터의 이름을 입력한 다음 **연결**을 클릭합니다.
8.	**복제본 지정** 페이지에서 보조 SQL Server 호스트가 **가용성 복제본**에 표시됩니다. 두 인스턴스 모두에 대해 다음 옵션 값을 설정합니다.

초기 역할 | 옵션 | 값
--- | --- | ---
보조 | 자동 장애 조치(Failover)(최대 2개) | 선택
주 | 자동 장애 조치(Failover)(최대 2개) | 선택
보조 | 동기 커밋(최대 3개) | 선택
주 | 동기 커밋(최대 3개) | 선택
보조 | 읽을 수 있는 보조 | 예
주 | 읽을 수 있는 보조 | 예

9.	**다음**을 클릭합니다.  
10.	**초기 데이터 동기화 선택** 페이지에서 **조인만**을 클릭하고 **다음**을 클릭합니다. 데이터 동기화는 주 서버에서 전체 백업과 트랜잭션 백업을 가져온 다음 백업에서 복원하는 방식을 통해 수동으로 실행합니다. 새 가용성 그룹 마법사가 데이터 동기화를 자동으로 수행하도록 **전체**를 선택할 수 있습니다. 그러나 일부 대기업에서 사용하는 대형 데이터베이스의 경우에는 동기화를 수행하지 않는 것이 좋습니다.  
11.	**유효성 검사** 페이지에서 **다음**을 클릭합니다. 가용성 그룹 수신기가 구성되어 있지 않으므로 수신기 구성 누락에 대한 경고가 표시됩니다.
12.	**요약** 페이지에서 **마침**을 클릭합니다. 마법사가 완료되면 **결과** 페이지를 검토하여 가용성 그룹이 올바르게 만들어졌는지 확인합니다. 가용성 그룹이 올바르게 만들어졌으면 **닫기**를 클릭하여 마법사를 종료합니다.
13.	시작 화면에서 **장애 조치**를 입력하고 **장애 조치(Failover) 클러스터 관리자**를 클릭합니다. 왼쪽 창에서 클러스터의 이름을 열고 **역할**을 클릭합니다. 가용성 그룹 이름이 지정된 새 역할이 표시됩니다.  

SharePoint 데이터베이스에 대해 SQL Server AlwaysOn 가용성 그룹이 구성되었습니다.

## AlwaysOn 가용성 그룹에 대한 수신기 구성

필요에 따라 AlwaysOn 가용성 그룹에 대한 수신기 구성을 작성할 수 있습니다. 해당 단계는 [자습서: AlwaysOn 가용성 그룹의 수신기 구성](https://msdn.microsoft.com/library/dn425027.aspx)을 참조하세요. 수신기는 하나만 만들고 내부 부하 분산 인스턴스의 가상 IP 주소를 사용하도록 구성해야 합니다.

수신기를 만든 후 모든 SharePoint 가상 컴퓨터가 클러스터의 첫 번째 SQL Server 컴퓨터 이름이 아닌 수신기를 사용하도록 구성해야 합니다. 이름을 사용하는 대신 SQL 별칭을 사용하도록 SharePoint 가상 컴퓨터를 구성합니다. 자세한 내용과 단계는 [SharePoint의 SQL 별칭](http://blogs.msdn.com/b/priyo/archive/2013/09/13/sql-alias-for-sharepoint.aspx)을 참조하세요.

SQL Server AlwaysOn 가용성 그룹을 사용하는 SharePoint에 대한 자세한 내용은 [SQL Server 2013에 대해 SQL Server 2012 AlwaysOn 가용성 그룹 구성](https://technet.microsoft.com/library/jj715261.aspx)을 참조하세요.


## 추가 리소스

[Azure에서 SQL Server AlwaysOn 가용성 그룹을 사용하여 SharePoint 배포](virtual-machines-workload-intranet-sharepoint-overview.md)

[Azure 인프라 서비스에서 호스트되는 SharePoint 팜](virtual-machines-sharepoint-infrastructure-services.md)

[SQL Server AlwaysOn이 포함된 SharePoint 인포그래픽](http://go.microsoft.com/fwlink/?LinkId=394788)

[SharePoint 2013용 Microsoft Azure 아키텍처](https://technet.microsoft.com/library/dn635309.aspx)

[Azure 인프라 서비스 구현 지침](virtual-machines-infrastructure-services-implementation-guidelines.md)

[Azure 인프라 서비스 작업: 고가용성 LOB(기간 업무) 응용 프로그램](virtual-machines-workload-high-availability-lob-application.md)

<!---HONumber=August15_HO9-->