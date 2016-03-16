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

<!---HONumber=AcomDC_0107_2016-->