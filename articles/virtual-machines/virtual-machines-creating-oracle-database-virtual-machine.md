<properties 
	pageTitle="Azure 포털을 사용하여 Oracle 데이터베이스 VM 만들기 | Microsoft Azure" 
	description="클래식 배포 모델 및 Azure 미리 보기 포털을 통해 Oracle 데이터베이스를 사용하여 가상 컴퓨터를 만드는 방법에 알아봅니다." 
	services="virtual-machines" 
	authors="bbenz" 
	documentationCenter=""
	tags="azure-service-management"/>
	
<tags 
	ms.service="virtual-machines" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="Windows" 
	ms.workload="infrastructure-services" 
	ms.date="06/22/2015" 
	ms.author="bbenz" />
	
#Azure에서 Oracle 데이터베이스 가상 컴퓨터를 만들기

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]리소스 관리자 모델.


다음 예제에서는 Azure에서 Windows Server 2012에서 실행되는 Microsoft에서 제공한 Oracle 데이터베이스 이미지에 따라 가상 컴퓨터(VM)를 만드는 방법을 보여줍니다. 두 단계가 있습니다. 먼저 VM을 만들고 VM 내에서 Oracle 데이터베이스를 만듭니다. 표시된 예제는 Oracle 데이터베이스 버전 12c지만 해당 단계는 버전 11g와 거의 동일합니다.

##Azure에서 Oracle 데이터베이스 VM 만들기

1.	[Azure 포털](https://ms.portal.azure.com/)에 로그인합니다.

2.	**Marketplace**를 클릭하고 **계산**을 클릭한 다음 검색 상자에 **Oracle**을 입력합니다.

3.	**버전 11g, 버전 12c, Standard Edition, Enterprise Edition 또는 가장 많이 사용하는 옵션이나 고급 옵션 번들**을 포함한 사용할 수 있는 Oracle 데이터베이스 이미지 중 하나를 선택합니다. 선택하는 이미지에 관한 정보(예: 최소 권장 크기)를 검토하고 **다음**을 클릭합니다.

4.	VM의 **호스트 이름**을 지정합니다.

5.	VM의 **사용자 이름**을 지정합니다. 이 사용자는 VM에 원격으로 로그인하기 위한 것으로 Oracle 데이터베이스 사용자 이름이 아닙니다.

6.	VM의 암호를 지정하고 확인하거나 SSH 공용 키를 제공합니다.

7.	**가격 책정 계층**을 선택합니다. 권장 가격 책정 계층이 기본으로 표시됩니다. 모든 구성 옵션을 보려면 오른쪽 맨 위에서 **모두 보기**를 클릭합니다.

8.	다음 사항을 고려하여 필요에 따라 선택적 구성을 설정합니다.

	a. VM 이름으로 새 저장소 계정을 만들려면 **저장소 계정**을 그대로 둡니다.

	b. **가용성 집합**을 “구성되지 않음“으로 둡니다.

	c. 이때 모든 **끝점**을 추가하지 마십시오.

9.	리소스 그룹을 선택하거나 만듭니다.

10. **구독**을 선택합니다.

11. **위치**를 선택합니다.

12. **만들기**를 클릭하고 VM을 만드는 과정이 시작됩니다. VM이 **실행** 상태가 되면 다음 절차를 진행합니다.


##Azure에서 Oracle 데이터베이스 VM을 사용하여 데이터베이스를 만들려면

1.	[Azure 포털](https://ms.portal.azure.com/)에 로그인합니다.

2.	**가상 컴퓨터**를 클릭합니다.

3.	로그인할 VM의 이름을 클릭합니다.

4.	**Connect**를 클릭합니다.

5.	VM에 연결해야 한다는 메시지에 응답합니다. 관리자 이름 및 암호를 묻는 메시지가 표시되면 VM를 만들 때 제공한 값을 사용하십시오.

6.	VM의 컴퓨터 이름으로 설정된 해당 값으로 **ORACLE\_HOSTNAME**이라는 환경 변수를 만듭니다. 다음 단계를 이용하여 환경 변수를 만들 수 있습니다.

	a. Windows에서 **시작**을 클릭하고 **제어판**을 입력하여 **제어판** 아이콘, **시스템 및 보안**, **시스템**, **고급 시스템 설정**을 차례로 클릭합니다.

	b. **고급** 탭을 클릭한 다음 **환경 변수**를 클릭합니다.

	c. **시스템 변수** 섹션에서 **새로 만들기**를 클릭하여 변수를 만듭니다.

	d. **새 시스템 변수** 대화 상자에서 변수 이름에 **ORACLE\_HOSTNAME**을 입력하고 값으로 VM의 컴퓨터 이름을 입력합니다. 컴퓨터 이름을 확인하려면 명령 프롬프트를 열고 **SET COMPUTERNAME**을 실행합니다(해당 명령의 출력에는 컴퓨터 이름이 포함됨).

	e. **확인**을 클릭하여 새 환경 변수를 저장하고 **새 시스템 변수** 대화 상자를 닫습니다.

	f. 제어판에서 열려 있던 다른 대화 상자를 닫습니다.

7.	Windows에서 **시작**을 클릭한 다음 **데이터베이스 구성 도우미**를 입력합니다. **데이터베이스 구성 도우미** 아이콘을 클릭합니다.

8.	**데이터베이스 구성 도우미** 마법사에서 각 대화 상자 단계마다 필요한 값을 제공합니다.

	a. **1단계:** **데이터베이스 만들기**를 클릭하고 **다음**을 클릭합니다.

		![](media/virtual-machines-creating-oracle-database-virtual-machine/image5.png)

	b. **2단계:** **글로벌 데이터베이스 이름**에 대한 값을 입력합니다. **관리 암호**에 대한 값을 입력하고 확인합니다. 이 암호는 Oracle 데이터베이스 **SYSTEM** 사용자입니다. **컨테이너 데이터베이스로 만들기**를 비웁니다. **다음**을 클릭합니다.

		![](media/virtual-machines-creating-oracle-database-virtual-machine/image6.png)

	c. **3단계:** 필수 구성 요소 확인은 **4단계**에 앞서 자동으로 진행됩니다.

	d. **4단계:** **데이터베이스 만들기 - 요약** 옵션을 검토하고 **마침**을 클릭합니다.

		![](media/virtual-machines-creating-oracle-database-virtual-machine/image7.png)
	e. **5단계:** **진행률 페이지**는 데이터베이스 만들기 상태를 보고합니다.

		![](media/virtual-machines-creating-oracle-database-virtual-machine/image8.png)
	f. 데이터베이스가 만들어지면 **암호 관리** 대화 상자를 사용하는 옵션이 생깁니다. 요구 사항에 필요한 경우 암호 설정을 수정한 다음 대화 상자를 닫아 **데이터베이스 구성 도우미** 마법사를 종료합니다.

##설치된 데이터베이스를 확인하는 방법

1.	여전히 VM에 로그인한 상태로 SQL Plus 명령 프롬프트를 시작합니다. Windows에서 *시작**을 클릭한 다음 **SQL Plus**를 입력합니다. **SQL Plus** 아이콘을 클릭합니다.

2.	프롬프트가 나타나면 **SYSTEM**의 사용자 이름 및 Oracle 데이터베이스를 만들 때 지정한 암호를 사용하여 로그인합니다.

3.	SQL Plus 명령 프롬프트 창에서 다음 명령을 실행합니다.

		**select * from GLOBAL\_NAME;**

	결과는 사용자가 만든 데이터베이스의 전역 이름이어야 합니다.

	![](media/virtual-machines-creating-oracle-database-virtual-machine/image9.png)

##데이터베이스에 원격으로 연결할 수 있도록 허용
데이터베이스에 원격으로 연결할 수 있도록 허용하려면(예: Java 코드를 실행하는 클라이언트 컴퓨터에서) 데이터베이스 수신기를 시작하여 가상 컴퓨터의 방화벽에서 1521 포트를 열고 1521 포트에 대한 공용 끝점을 만들어야 합니다.

### 데이터베이스 수신기 시작
1.	VM에 로그인합니다.

2.	명령 프롬프트를 엽니다.

3.	명령 프롬프트에서 다음 명령을 실행합니다.

		**lsnrctl start**

> [AZURE.NOTE]**lsnrctl 상태**를 실행하여 수신기의 상태를 확인할 수 있습니다. 수신기를 종료하려는 경우 **lsnrctl 중지**를 실행합니다.

### 가상 컴퓨터의 방화벽에서 1521 포트 열기

1.	가상 컴퓨터에 로그인되어 있는 동안 Windows에서 **시작**을 클릭하고 **고급 보안이 포함된 Windows 방화벽**을 입력한 다음 **고급 보안이 포함된 Windows 방화벽** 아이콘을 클릭합니다. 이렇게 하면 **고급 보안이 포함된 Windows 방화벽** 관리 콘솔이 열립니다.

2.	방화벽 관리 콘솔의 왼쪽 창에서 **인바운드 규칙**을 클릭한 다음(**인바운드 규칙**이 보이지 않는 경우 왼쪽 창의 맨 위에 있는 노드를 확장함) 오른쪽 창에서 **새 규칙**을 클릭합니다.

3.	**규칙 유형**의 경우 **포트**를 선택한 후 **다음**을 클릭합니다.

4.	**프로토콜 및 포트**는 **TCP**를 선택하고 **특정 로컬 포트**를 선택하여 포트에 대해 **1521**을 입력하고 **다음**을 클릭합니다.

5.	**연결 허용**을 선택하고 **다음**을 클릭합니다.

6.	규칙이 적용되는 프로필에 대해 기본값을 적용하고 **다음**을 클릭합니다.

7.	규칙 이름 및 설명(선택 사항)을 지정한 다음 **마침**을 클릭합니다.

### 포트 1521에 대한 공용 끝점 만들기

1.	[Azure 포털](https://ms.portal.azure.com/)에 로그인합니다.

2.	**찾아보기**를 클릭합니다.

3.  **가상 컴퓨터**를 클릭합니다.

4.	가상 컴퓨터를 선택합니다.

5.	**설정**을 클릭합니다.

6.	**Endpoints**를 클릭합니다.

7.	**추가**를 클릭합니다.

8.	끝점의 이름을 지정합니다.

	a. 프로토콜에 **TCP**를 사용합니다.

	b. 공용 포트에 **1521**을 사용합니다.

	c. 개인 포트에 **1521**을 사용합니다.

9.	나머지 옵션은 그대로 둡니다.

10. **확인**을 클릭합니다.

##Oracle 데이터베이스 엔터프라이즈 관리자의 원격 액세스를 사용하도록 설정
Oracle 데이터베이스 엔터프라이즈 관리자에 대한 원격 액세스를 사용하도록 설정하려는 경우, 방화벽에서 5500 포트를 열고 Azure 포털에서 5500에 대한 가상 컴퓨터 끝점을 만듭니다(1521 포트를 열고 1521에 대한 끝점을 만들기 위해 이전의 단계 사용). 그런 다음 원격 컴퓨터에서 Oracle 엔터프라이즈 관리자를 실행하기 위해 `http://<<unique_domain_name>>:5500/em`의 양식에 URL로 브라우저를 엽니다.

> [AZURE.NOTE]**가상 컴퓨터**를 클릭한 다음 Oracle 데이터베이스를 실행하기 위해 사용 중인 가상 컴퓨터를 선택하여 [Azure 포털](https://ms.portal.azure.com/) 내에서 *<<unique\_domain\_name>>*에 대한 값을 확인할 수 있습니다.

##가장 많이 사용하는 옵션 및 고급 옵션 번들 구성
**인기있는 옵션이 탑재된 Oracle 데이터베이스** 또는 **고급 옵션이 탑재된 Oracle 데이터베이스**을 선택한 경우, 다음 단계는 Oracle 설치에서 추가 기능을 구성하는 것입니다. 각각의 개별 구성 요소의 필요에 따라 구성이 다양할 수 있기 때문에 Windows에서의 설정을 위한 지침은 Oracle 설명서를 참조합니다.

**인기있는 옵션이 탑재된 Oracle 데이터베이스**는 Oracle Database Enterprise Edition의 라이선스가 포함된 인스턴스 및 [분할](http://www.oracle.com/us/products/database/options/partitioning/overview/index.html), [활성 데이터 가드](http://www.oracle.com/us/products/database/options/active-data-guard/overview/index.html), [데이터베이스용 Oracle 튜닝 팩](http://docs.oracle.com/html/A86647_01/tun_ovw.htm), [데이터베이스용 Oracle 진단 팩](http://docs.oracle.com/cd/B28359_01/license.111/b28287/options.htm#CIHIHDDJ) 및 [데이터베이스용 Oracle 수명 주기 관리 팩](http://www.oracle.com/technetwork/oem/lifecycle-mgmt-495331.html)을 포함합니다.

**고급 옵션이 탑재된 Oracle 데이터베이스**은 가장 많이 사용하는 옵션 번들에 있는 모든 옵션의 라이선스가 포함된 인스턴스를 비롯하여 [고급 압축](http://www.oracle.com/us/products/database/options/advanced-compression/overview/index.html), [고급 보안](http://www.oracle.com/us/products/database/options/advanced-security/overview/index.html), [레이블 보안](http://www.oracle.com/us/products/database/options/label-security/overview/index.html), [데이터베이스 자격 증명 모음](http://www.oracle.com/us/products/database/options/database-vault/overview/index.html), [고급 분석](http://www.oracle.com/us/products/database/options/advanced-analytics/overview/index.html), [OLAP](http://docs.oracle.com/cd/E11882_01/license.112/e47877/options.htm#CIHGDEEF), [공간 및 그래프](http://docs.oracle.com/cd/E11882_01/license.112/e47877/options.htm#CIHGDEEF), [메모리 내 데이터베이스 캐시](http://www.oracle.com/technetwork/products/timesten/overview/timesten-imdb-cache-101293.html), [데이터 마스킹 팩](http://docs.oracle.com/cd/E11882_01/license.112/e47877/options.htm#CHDGEEBB), 및 Oracle 테스트 데이터 관리 팩(데이터 마스킹 팩의 일부)을 포함합니다.

##추가 리소스
지금까지 가상 컴퓨터를 설정하고 데이터베이스를 만들었습니다. 추가 정보는 다음 항목을 참조하세요.

-	[Oracle 가상 컴퓨터 이미지 - 기타 고려 사항](virtual-machines-miscellaneous-considerations-oracle-virtual-machine-images.md)

-	[Oracle 데이터베이스 12c 설명서 라이브러리](http://www.oracle.com/pls/db1211/homepage)

-	[Java 응용 프로그램에서 Oracle 데이터베이스에 연결](http://docs.oracle.com/cd/E11882_01/appdev.112/e12137/getconn.htm#TDPJD136)

-	[Azure용 Oracle 가상 컴퓨터 이미지 ](virtual-machines-oracle-list-oracle-virtual-machine-images.md)

-	[Oracle 데이터베이스 2일 DBA 12c 릴리스 1](http://docs.oracle.com/cd/E16655_01/server.121/e17643/toc.htm)

<!---HONumber=Oct15_HO3-->