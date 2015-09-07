다음 단계는 SQL Server Management Studio (SSMS)를 사용하여 인터넷을 통해 SQL Server 인스턴스에 연결하는 방법을 보여줍니다. 그러나 동일한 단계는 온 프레미스 및 Azure에서 실행중인 응용 프로그램에 대해 SQL Server 가상 컴퓨터를 액세스할 수 있게 만들도록 적용합니다.

인터넷 또는 다른 VM에서 SQL Server의 인스턴스에 연결하기 전에 먼저 아래의 섹션에 설명된 대로 다음 작업을 완료해야 합니다.

- [가상 컴퓨터에 대한 TCP 끝점 만들기](#create-a-tcp-endpoint-for-the-virtual-machine)
- [Windows 방화벽에서 TCP 포트 열기](#open-tcp-ports-in-the-windows-firewall-for-the-default-instance-of-the-database-engine)
- [TCP 프로토콜에서 수신하도록 SQL Server 구성](#configure-sql-server-to-listen-on-the-tcp-protocol)
- [혼합된 모드 인증에 대한 SQL Server 구성](#configure-sql-server-for-mixed-mode-authentication)
- [SQL Server 인증 로그인 만들기](#create-sql-server-authentication-logins)
- [가상 컴퓨터의 DNS 이름 확인](#determine-the-dns-name-of-the-virtual-machine)
- [다른 컴퓨터에서 데이터베이스 엔진에 연결](#connect-to-the-database-engine-from-another-computer)

연결 경로는 다음 다이어그램에 요약되어 있습니다.

![SQL Server 가상 컴퓨터에 연결](./media/virtual-machines-sql-server-connection-steps/SQLServerinVMConnectionMap.png)

### 가상 컴퓨터용 TCP 끝점 만들기

인터넷에서 SQL 서버에 연결하려면, 가상 컴퓨터에 들어오는 TCP 통신을 수신하는 끝점이 있어야 합니다. 이 Azure 구성 단계에서는 들어오는 TCP 포트 트래픽을 가상 컴퓨터에 액세스 가능한 TCP 포트로 보냅니다.

>[AZURE.NOTE]동일한 클라우드 서버 또는 가상 네트워크 내에서 연결하는 경우, 공개적으로 엑세스 할 수 있는 끝점을 만들 필요가 없습니다. 이 경우, 다음 단계를 계속 할 수 있습니다. 자세한 내용은 [연결 시나리오](../articles/virtual-machines/virtual-machines-sql-server-connectivity.md#connection-scenarios)를 참조하세요.

1. Azure 관리 포털에서 **가상 컴퓨터**를 클릭합니다.
	
2. 새로 만든 가상 컴퓨터를 클릭합니다. 가상 컴퓨터에 대한 정보가 표시됩니다.
	
3. 페이지 맨 위 근처에서 **끝점** 페이지를 선택한 후 페이지 맨 아래에서 **추가**를 클릭합니다.
	
4. **가상 컴퓨터에 끝점 추가** 페이지에서 **독립 실행형 끝점 추가**를 클릭한 후 다음 화살표를 클릭하여 계속합니다.
	
5. **끝점의 세부 정보를 지정하십시오.** 페이지에서 다음 정보를 제공합니다.

	- **이름** 상자에 끝점의 이름을 입력합니다.
	- **프로토콜** 상자에서 **TCP**를 선택합니다. **공용 포트** 상자에 **57500**을 입력할 수 있습니다. 마찬가지로, SQL Server의 기본 수신 대기 포트 **1433**를 **개인 포트** 상자에 입력할 수 있습니다. 많은 조직이 악의적인 보안 공격을 방지하기 위해 다른 포트 번호를 선택합니다. 

6. 확인 표시를 클릭하여 계속합니다. 끝점이 만들어집니다.

### Windows 방화벽에서 데이터베이스 엔진의 기본 인스턴스용 TCP 포트 열기

1. Windows 원격 데스크톱을 통해 가상 컴퓨터에 연결합니다. 시작 화면에서 로그인한 후 **WF.msc**를 입력하고 ENTER를 누릅니다. 

	![방화벽 프로그램 시작](./media/virtual-machines-sql-server-connection-steps/12Open-WF.png)
2. **고급 보안이 포함된 Windows 방화벽**의 왼쪽 창에 있는 작업 창에서 **인바운드 규칙**을 마우스 오른쪽 단추로 클릭한 후 **새 규칙**을 클릭합니다.

	![새 규칙](./media/virtual-machines-sql-server-connection-steps/13New-FW-Rule.png)

3. **새 Inbount 규칙 마법사** 대화 상자의 **규칙 유형**에서 **포트**를 선택하고 **다음**을 클릭합니다.

4. **프로토콜 및 포트** 대화 상자에서 기본 **TCP**를 사용합니다. **특정 로컬 포트** 상자에 데이터베이스 엔진의 인스턴스 포트 번호(기본 인스턴스의 경우 **1433** 또는 끝점 단계에서 개인 포트에 대해 선택한 포트)를 입력합니다.

	![TCP 포트 1433](./media/virtual-machines-sql-server-connection-steps/14Port-1433.png)

5. **다음**을 클릭합니다.

6. **작업** 대화 상자에서 **연결 허용**을 선택한 후 **다음**을 클릭합니다.

	**보안 정보:** **안전한 경우 연결 허용**을 선택하면 추가 보안을 제공할 수 있습니다. 사용자 환경에서 추가 보안을 구성하려는 경우 이 옵션을 선택하세요.

	![연결 허용](./media/virtual-machines-sql-server-connection-steps/15Allow-Connection.png)

7. **프로필** 대화 상자에서 **공용**, **개인** 및 **도메인**을 선택합니다. 그런 후 **Next**를 클릭합니다.

    **보안 정보:** **공개**를 선택하면 인터넷을 통한 액세스가 허용됩니다. 가능하면 더 제한적인 프로필을 선택하세요.

	![공개 프로필](./media/virtual-machines-sql-server-connection-steps/16Public-Private-Domain-Profile.png)

8. **이름** 대화 상자에서 이 규칙의 이름 및 설명을 입력한 후 **마침**을 클릭합니다.

	![규칙 이름](./media/virtual-machines-sql-server-connection-steps/17Rule-Name.png)

필요한 경우 다른 구성 요소의 추가 포트를 엽니다. 자세한 내용은 [SQL Server 액세스를 허용하도록 Windows 방화벽 구성](http://msdn.microsoft.com/library/cc646023.aspx)을 참조하십시오.


### TCP 프로토콜을 수신 대기하도록 SQL Server 구성

1. 가상 컴퓨터에 연결되어 있는 동안 시작 페이지에서 **SQL Server 구성 관리자**를 입력하고 ENTER 키를 누릅니다.
	
	![SSCM 열기](./media/virtual-machines-sql-server-connection-steps/9Click-SSCM.png)

2. SQL Server 구성 관리자의 콘솔 창에서 **SQL Server 네트워크 구성**을 확장합니다.

3. 콘솔 창에서 **MSSQLSERVER용 프로토콜**(기본 인스턴스 이름)을 클릭합니다. 세부 정보 창에서 TCP를 마우스 오른쪽 단추로 클릭하면 기본적으로 갤러리 이미지에 대해 사용으로 설정되어 있어야 합니다. 사용자 지정 이미지에 대해 **사용**(상태가 사용 안 함인 경우)을 클릭합니다.

	![TCP 사용](./media/virtual-machines-sql-server-connection-steps/10Enable-TCP.png)

5. 콘솔 창에서 **SQL Server 서비스**를 클릭합니다. 세부 정보 창에서 **SQL Server(_인스턴스 이름_)**(기본 인스턴스는 **SQL Server (MSSQLSERVER)**)를 마우스 오른쪽 단추로 클릭한 후 **다시 시작**을 클릭하여 SQL Server의 인스턴스를 중지했다가 다시 시작합니다.

	![데이터베이스 엔진 다시 시작](./media/virtual-machines-sql-server-connection-steps/11Restart.png)

7. SQL Server 구성 관리자를 닫습니다.

SQL Server 데이터베이스 엔진용 프로토콜 사용 설정에 대한 자세한 내용은 [서버 네트워크 프로토콜 설정 또는 해제](http://msdn.microsoft.com/library/ms191294.aspx)를 참조하십시오.

### 혼합 모드 인증을 위해 SQL Server 구성

SQL Server 데이터베이스 엔진은 도메인 환경에서만 Windows 인증을 사용할 수 있습니다. 다른 컴퓨터에서 데이터베이스 엔진에 연결하려면 혼합 모드 인증을 위해 SQL Server를 구성하십시오. 혼합 모드 인증은 SQL Server 인증과 Windows 인증을 모두 허용합니다.

>[AZURE.NOTE]Azure 가상 네트워크를, 구성된 도메인 환경으로 구성한 경우, 혼합 모드 인증은 구성할 필요가 없습니다.

1. 가상 컴퓨터에 연결되어 있는 동안 시작 페이지에서 **SQL Server 2014 Management Studio**를 입력하고 선택한 아이콘을 클릭합니다.

	![SSMS 시작](./media/virtual-machines-sql-server-connection-steps/18Start-SSMS.png)

	처음으로 Management Studio를 열 때 사용자 Management Studio 환경이 만들어져야 합니다. 어느 정도 시간이 걸릴 수 있습니다.

2. Management Studio에서 **서버에 연결** 대화 상자가 표시됩니다. **서버 이름** 상자에, 개체 탐색기를 사용하여 데이터베이스 엔진에 연결할 가상 컴퓨터의 이름을 입력합니다. 가상 컴퓨터 이름 대신 **(로컬)** 또는 단일 기간을 **서버 이름**으로 사용할 수도 있습니다. **Windows 인증**을 선택하고, **사용자 이름** 상자의 _**VM\_이름**\\로컬\_관리자_를 그대로 둡니다. **Connect**를 클릭합니다.

	![서버에 연결](./media/virtual-machines-sql-server-connection-steps/19Connect-to-Server.png)

3. SQL Server Management Studio 개체 탐색기에서 SQL Server 인스턴스의 이름(가상 컴퓨터 이름)을 마우스 오른쪽 단추로 클릭한 후 **속성**을 클릭합니다.

	![서버 속성](./media/virtual-machines-sql-server-connection-steps/20Server-Properties.png)

4. **보안** 페이지의 **서버 인증**에서 **SQL Server 및 Windows 인증 모드**를 선택한 후 **확인**을 클릭합니다.

	![인증 모드 선택](./media/virtual-machines-sql-server-connection-steps/21Mixed-Mode.png)

5. SQL Server Management Studio 대화 상자에서 **확인**을 클릭하여 SQL Server를 다시 시작해야 하는 요구 사항을 확인합니다.

6. 개체 탐색기에서 서버를 마우스 오른쪽 단추로 클릭한 후 **다시 시작**을 클릭합니다. SQL Server 에이전트가 실행 중인 경우 에이전트도 다시 시작해야 합니다.

	![다시 시작](./media/virtual-machines-sql-server-connection-steps/22Restart2.png)

7. SQL Server Management Studio 대화 상자에서 **예**를 클릭하여 SQL Server를 다시 시작한다는 데 동의합니다.

### SQL Server 인증 로그인 만들기

다른 컴퓨터에서 데이터베이스 엔진에 연결하려면 SQL Server 인증 로그인을 하나 이상 만들어야 합니다.

1. SQL Server Management Studio 개체 탐색기에서 새 로그인을 만들 서버 인스턴스의 폴더를 확장합니다.

2. **보안** 폴더를 마우스 오른쪽 단추로 클릭하고, **새로 만들기**를 가리키고 **로그인...**을 선택합니다.

	![새 로그인](./media/virtual-machines-sql-server-connection-steps/23New-Login.png)

3. **로그인 - 신규** 대화 상자의 **일반** 페이지에 있는 **로그인 이름** 상자에 새 사용자의 이름을 입력합니다.

4. **SQL Server 인증**을 선택합니다.

5. **암호** 상자에 새 사용자의 암호를 입력합니다. **암호 확인** 상자에 암호를 다시 입력합니다.

6. 복잡성 및 강제성에 대한 암호 정책 옵션을 적용하려면 **암호 정책 강제 적용**(권장)을 선택합니다. SQL Server 인증을 선택할 경우 이 항목은 기본 옵션입니다.

7. 만료에 대한 암호 정책 옵션을 적용하려면 **암호 만료 강제 적용**(권장)을 선택합니다. 이 확인란을 사용하려면 먼저 암호 정책 강제 적용을 선택해야 합니다. SQL Server 인증을 선택할 경우 이 항목은 기본 옵션입니다.

8. 처음으로 로그인을 사용한 후 사용자가 새 암호를 만들도록 강제하려면 **다음 로그인할 때 반드시 암호 변경**을 선택합니다. 다른 사용자가 이 로그인을 사용할 경우에 이 옵션을 선택하는 것이 좋습니다. 사용자만 이 로그인을 사용하는 경우에는 이 옵션을 선택하지 마십시오. 이 확인란을 사용하려면 먼저 암호 만료 강제 적용을 선택해야 합니다. SQL Server 인증을 선택할 경우 이 항목은 기본 옵션입니다.

9. **기본 데이터베이스** 목록에서 로그인의 기본 데이터베이스를 선택합니다. **master**가 이 옵션의 기본값입니다. 사용자 데이터베이스를 아직 만들지 않은 경우 **master**로 설정된 상태로 둡니다.

10. **기본 언어** 목록에서 **기본값**을 선택 값으로 둡니다.
    
	![로그인 속성](./media/virtual-machines-sql-server-connection-steps/24Test-Login.png)

11. 처음 로그인을 만드는 경우 이 로그인을 SQL Server 관리자로 지정할 수 있습니다. 그렇게 하는 경우 **서버 역할** 페이지에서 **sysadmin**을 선택합니다.

	**보안 정보:** sysadmin 고정 서버 역할의 구성원은 데이터베이스 엔진을 완전히 제어할 수 있습니다. 따라서 이 역할의 구성원은 신중하게 제한해야 합니다.

	![sysadmin](./media/virtual-machines-sql-server-connection-steps/25sysadmin.png)

12. 확인을 클릭합니다.

SQL Server 로그인에 대한 자세한 내용은 [로그인 만들기](http://msdn.microsoft.com/library/aa337562.aspx)를 참조하십시오.

### 가상 컴퓨터의 DNS 이름 확인

다른 컴퓨터에서 SQL Server 데이터베이스 엔진에 연결하려면 가상 컴퓨터의 DNS(Domain Name System) 이름을 알아야 합니다. 이 이름은 인터넷에서 가상 컴퓨터를 식별하는 이름입니다. IP 주소를 사용할 수 있지만 Azure가 중복 또는 유지 관리를 위해 리소스를 이동할 경우 IP 주소가 변경될 수 있습니다. DNS 이름은 새 IP 주소로 리디렉션할 수 있으므로 안정적입니다.

1. Azure 관리 포털(또는 이전 단계)에서 **가상 컴퓨터**를 선택합니다. 

2. **가상 컴퓨터 인스턴스** 페이지의 **빠른 보기** 열에서 http://로 시작하는 가상 컴퓨터의 DNS 이름을 찾아서 복사합니다.

	![DNS 이름](./media/virtual-machines-sql-server-connection-steps/sql-vm-dns-name.png)
	

### 다른 컴퓨터에서 데이터베이스 엔진에 연결
 
1. 인터넷에 연결된 컴퓨터에서 SQL Server Management Studio를 엽니다.
2. **서버에 연결** 또는 **데이터베이스 엔진에 연결** 대화 상자의 **서버 이름** 상자에 가상 컴퓨터의 DNS 이름(이전 작업에서 확인된 이름) 및 공개 끝점 포트 번호를 *DNS이름,포트 번호* 형식(예: **tutorialtestVM.cloudapp.net,57500**)으로 입력합니다. 포트 번호를 가져오려면 Azure 관리 포털에 로그인하여 가상 컴퓨터를 찾습니다. 대시보드에서 **끝점**을 클릭하고 **MSSQL**에 할당된 **공용 포트**를 사용합니다. ![공용 포트](./media/virtual-machines-sql-server-connection-steps/sql-vm-port-number.png)
3. **인증** 상자에 **SQL Server 인증**을 선택합니다.
5. **로그인** 상자에, 이전 작업에서 만든 로그인 이름을 입력합니다.
6. **암호** 상자에, 이전 작업에서 만든 로그인의 암호를 입력합니다.
7. **Connect**를 클릭합니다.

	![SSMS를 사용하여 연결](./media/virtual-machines-sql-server-connection-steps/33Connect-SSMS.png)

<!---HONumber=August15_HO9-->