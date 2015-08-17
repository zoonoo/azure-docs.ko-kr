<properties title="Creating an Oracle WebLogic Server 12c and Oracle Database 12c Virtual Machine in Azure" pageTitle="Azure에서 Oracle WebLogic Server 12c 및 Oracle Database 12c 가상 컴퓨터 만들기" description="Oracle WebLogic Server 12c 및 Microsoft Azure에서 Windows Server 2012에서 실행 중인 Oracle Database 12c 이미지를 만드는 예제를 단계별로 설명합니다." services="virtual-machines" authors="bbenz" documentationCenter=""/>
<tags ms.service="virtual-machines" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="infrastructure-services" ms.date="06/22/2015" ms.author="bbenz" />
#Azure에서 Oracle WebLogic Server 11g 가상 컴퓨터 만들기
다음 예제에서는 Azure에서 Windows Server 2008 R2에서 실행되는 Microsoft에서 제공한 Oracle WebLogic Server 11g 이미지에 따라 가상 컴퓨터를 만드는 방법을 보여줍니다.

##Azure에서 Oracle WebLogic Server 11g 가상 컴퓨터 만들기

1. [Azure 포털](https://ms.portal.azure.com/)에 로그인합니다.

2. **Marketplace**를 클릭하고 계산을 클릭한 다음 검색 상자에 **Oracle**을 입력합니다.

3. 이 이미지에 관한 정보(예: 최소 권장된 크기)를 선택하거나 검토한 다음 클릭합니다.

4. VM의 **호스트 이름**을 지정합니다.

5. VM의 **사용자 이름**을 지정합니다. 이 사용자는 VM에 원격으로 로그인하기 위함입니다. 이는 Oracle 데이터베이스 사용자 이름이 아닙니다.

6. VM의 암호를 지정하고 확인하거나 SSH 공용 키를 제공합니다.

7. 권장 가격 책정 계층이 기본으로 표시됩니다. 모든 구성 옵션을 보려면 맨 위 오른쪽에서 **모두 보기**를 클릭합니다.

8. 필요한 경우 다음의 고려 사항으로 [선택적 구성](https://msdn.microsoft.com/library/azure/dn763935.aspx)을 설정합니다.

	1. VM 이름으로 새 저장소 계정을 만들려면 **저장소 계정**을 그대로 둡니다.

	2. **가용성 집합**을 "구성 되지 않음"으로 둡니다.

	3. 이때 모든 **끝점**을 추가하지 마십시오.

9. [리소스 그룹](resource-group-portal.md)을 선택하거나 만듭니다.

10. **구독**을 선택합니다.

11. **위치**를 선택합니다.

##Azure에서 Oracle WebLogic Server 11g 가상 컴퓨터 구성하기

1. [Azure 포털](https://ms.portal.azure.com/)에 로그인합니다.

2. **가상 컴퓨터**를 클릭합니다.

3. 로그인할 가상 컴퓨터의 이름을 클릭합니다.

4. **연결**을 클릭합니다.

5. 가상 컴퓨터에 연결하려면 필요에 따라 메시지에 응답합니다. 관리자 이름 및 암호를 묻는 메시지가 표시되면 가상 컴퓨터를 만들 때 제공한 값을 사용하십시오.

6. **Windows 시작**을 클릭하고 **모든 프로그램**, **Oracle WebLogic**, **WebLogic Server 11g R1**, **도구**, **구성 마법사**를 차례로 클릭합니다.

7. **시작** 대화 상자에서 **새 WebLogic 도메인 만들기**를 선택한 후 **다음**을 클릭합니다.

	![](media/virtual-machines-creating-oracle-webLogic-server-11g-virtual-machine/image30.png)

8. **도메인 소스 선택** 대화 상자에서 기본값을 적용한 후 **다음**을 클릭합니다.

	![](media/virtual-machines-creating-oracle-webLogic-server-11g-virtual-machine/image31.png)

9. **도메인 이름 및 위치 지정** 대화 상자에서 기본값을 적용한 후 **다음**을 클릭합니다.

	![](media/virtual-machines-creating-oracle-webLogic-server-11g-virtual-machine/image32.png)

10. **관리자의 사용자 이름 및 암호 구성** 대화 상자에서

	1. [옵션] **weblogic**에서 선택한 값으로 사용자 이름을 변경합니다.

	2. WebLogic 서버 관리자의 암호를 지정하고 확인합니다.

	3. **다음**을 클릭합니다.

	![](media/virtual-machines-creating-oracle-webLogic-server-11g-virtual-machine/image33.png)

11. **서버 시작 모드 및 JDK 구성** 대화 상자에서 **프로덕션 모드**를 선택하고 사용할 수 있는 JDK(또는 원하는 경우 JDK 브라우저)를 선택하고 **다음**을 클릭합니다.

	![](media/virtual-machines-creating-oracle-webLogic-server-11g-virtual-machine/image34.png)

12.	**선택적 구성 선택** 대화 상자에서 모든 옵션을 선택하지 말고 **다음**을 클릭합니다.

	![](media/virtual-machines-creating-oracle-webLogic-server-11g-virtual-machine/image35.png)

13.	**구성 요약** 대화 상자에서 **생성**을 클릭합니다.
	
	![](media/virtual-machines-creating-oracle-webLogic-server-11g-virtual-machine/image35.png)

14.	**도메인 만들기** 대화 상자에서 **관리 서버 시작**을 선택한 다음 **완료**를 클릭합니다.

	![](media/virtual-machines-creating-oracle-webLogic-server-11g-virtual-machine/image37.png)

15.	**startWebLogic.cmd**의 명령 프롬프트가 시작됩니다. 메시지가 표시되면 WebLogic 사용자 이름과 암호를 제공합니다.

## Azure에서 Oracle WebLogic Server 11g 가상 컴퓨터에 응용 프로그램 설치하기

1. 가상 컴퓨터에 로그인된 상태에서 [http://www.oracle.com/webfolder/technetwork/tutorials/obe/fmw/wls/12c/12-ManageSessions--4478/files/shoppingcart.war](http://www.oracle.com/webfolder/technetwork/tutorials/obe/fmw/wls/12c/12-ManageSessions--4478/files/shoppingcart.war)에서 사용할 수 있는 shoppingcart.war 예제를 로컬로 복사합니다. 예를 들어 **c:\\mywar**라는 폴더를 만들고 [http://www.oracle.com/webfolder/technetwork/tutorials/obe/fmw/wls/12c/12-ManageSessions--4478/files/shoppingcart.war](http://www.oracle.com/webfolder/technetwork/tutorials/obe/fmw/wls/12c/12-ManageSessions--4478/files/shoppingcart.war)에서 WAR를 **c:\\mywar**로 저장합니다.

2. **WebLogic Server 관리 콘솔**인 [http://localhost:7001/console](http://localhost:7001/console)을 엽니다. 메시지가 표시되면 WebLogic 사용자 이름과 암호를 제공합니다.

3. **WebLogic Server 관리 콘솔**에서 **잠금 및 편집**을 클릭하고 **배포**를 클릭한 다음 **설치**를 클릭합니다.

4. **경로**는 `c:\mywar\shoppingcart.war`을 입력합니다.

	![](media/virtual-machines-creating-oracle-webLogic-server-11g-virtual-machine/image38.png)

	**다음**을 클릭합니다.

5. **이 배포를 응용 프로그램으로 설치**를 선택한 후 **다음**을 클릭합니다.

6. **마침**을 클릭합니다.

7. **WebLogic Server 관리 콘솔** 내에서 **변경 내용 활성화**를 클릭합니다.

8. **배포**를 클릭하고 **shoppingcart**를 선택하고 **시작**을 클릭한 다음 **모든 요청 서비스**를 클릭합니다. 확인하라는 메시지가 나타나면 **예**를 클릭합니다.

9. 로컬로 실행 중인 쇼핑 카트 응용 프로그램을 보려면 [http://localhost:7001/shoppingcart](http://localhost:7001/shoppingcart)에 대해 브라우저를 엽니다.

10.  7001 포트에 대한 방화벽을 통해 인바운드 연결을 허용합니다.

	1. **가상 컴퓨터**에 로그인되어 있는 동안 **Windows 시작**을 클릭하고 **제어판**, **시스템 및 보안**, **Windows 방화벽**, **고급 설정**을 차례로 클릭합니다. 이렇게 하면 **고급 보안이 포함된 Windows 방화벽** 관리 콘솔이 열립니다.

	2. 방화벽 관리 콘솔의 왼쪽 창에서 **인바운드 규칙**을 클릭한 다음(**인바운드 규칙**이 보이지 않는 경우 왼쪽 창의 맨 위에 있는 노드를 확장함) 오른쪽 창에서 **새 규칙**을 클릭합니다.

	3. **규칙 유형**은 **포트**를 선택하고 **다음**을 클릭합니다.

	4. **프로토콜 및 포트**는 **TCP**를 선택하고 **특정 로컬 포트**를 선택하여 포트에 대해 **7001**을 입력하고 **다음**을 클릭합니다.

	5. **연결 허용**을 선택하고 **다음**을 클릭합니다.

	6. 규칙이 적용되는 프로필에 대해 기본값을 적용하고 **다음**을 클릭합니다.

	7. 규칙 이름 및 설명(선택 항목)을 지정한 다음 **마침**을 클릭합니다.

11. 가상 컴퓨터의 끝점 만들기:

	1. [Azure 포털](https://ms.portal.azure.com/)에 로그인합니다.

    2. **찾아보기**를 클릭합니다.

    3. **가상 컴퓨터**를 클릭합니다.

    4. 가상 컴퓨터를 선택합니다.

	5. **설정**을 클릭합니다.

    6. **Endpoints**를 클릭합니다.

    7. **추가**를 클릭합니다.

    8. 끝점의 이름을 지정합니다.

		1. 프로토콜에 **TCP**를 사용합니다.

        2. 공용 포트에 **80**을 사용합니다.

        3. 개인 포트에 **7001**을 사용합니다.

    9. 나머지 옵션은 그대로 둡니다.

	10. **확인**을 클릭합니다.

12. 인터넷에서 실행 중인 쇼핑 카트 응용 프로그램을 보려면 `http://<<unique_domain_name>>/shoppingcart`의 형태로 URL에 대해 브라우저를 엽니다. (**가상 컴퓨터**를 클릭한 다음 Oracle WebLogic Server를 실행하기 위해 사용 중인 가상 컴퓨터를 선택하여 [Azure 포털](https://ms.portal.azure.com/) 내에서 `<<unique_domain_name>>`에 대한 값을 확인할 수 있습니다.)

## 추가 리소스

지금까지 Oracle WebLogic Server를 실행 중인 가상 컴퓨터를 설정했습니다. 추가 정보는 다음 항목을 참조하세요.

- [Oracle 가상 컴퓨터 이미지 - 기타 고려 사항](virtual-machines-miscellaneous-considerations-oracle-virtual-machine-images.md)

- [Oracle WebLogic Server 제품 설명서](http://www.oracle.com/technetwork/middleware/weblogic/documentation/index.html)

- [Azure용 Oracle 가상 컴퓨터 이미지](virtual-machines-oracle-list-oracle-virtual-machine-images.md)

<!---HONumber=August15_HO6-->