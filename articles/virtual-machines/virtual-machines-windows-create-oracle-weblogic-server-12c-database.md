<properties
	pageTitle="Oracle WebLogic Server 및 데이터베이스 VM | Microsoft Azure"
	description="리소스 관리자 배포 모델을 사용하여 Windows Server 2012에서 실행 중인 Oracle WebLogic Server 12c 및 Oracle Database 12c Azure 이미지를 만듭니다."
	services="virtual-machines-windows"
	authors="bbenz"
	documentationCenter=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows"
	ms.workload="infrastructure-services"
	ms.date="06/22/2015"
	ms.author="bbenz" />

#Azure에서 Oracle WebLogic Server 12c 및 Oracle Database 12c 가상 컴퓨터 만들기

이 문서에서는 Azure에서 Windows Server 2012에서 실행되는 Microsoft에서 제공한 Oracle WebLogic Server 12c 및 Oracle Database 12c 이미지에 따라 가상 컴퓨터를 만드는 방법을 보여 줍니다.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]클래식 배포 모델.


##Azure에서 Oracle WebLogic Server 12c 및 Oracle Database 12c 가상 컴퓨터 만들기

1. [Azure 포털](https://ms.portal.azure.com/)에 로그인합니다.

2.	**Marketplace**를 클릭하고 **계산**을 클릭한 다음 검색 상자에 **Oracle**을 입력합니다.

3.	**Oracle Database 12c and WebLogic Server 12c Standard Edition on Windows Server 2012** 또는 **Oracle Database 12c and WebLogic Server 12c Enterprise Edition on Windows Server 2012** 이미지를 선택합니다. 이 이미지에 관한 정보(예: 최소 권장된 크기)를 검토한 후 **다음**을 클릭합니다.

4.	가상 컴퓨터의 **호스트 이름**을 지정합니다.

5.	가상 컴퓨터의 **사용자 이름**을 지정합니다. 이 사용자 이름은 가상 컴퓨터에 원격으로 로그인하는 데 사용됩니다. Oracle 데이터베이스 사용자 이름이 아닙니다.

6.	가상 컴퓨터의 암호를 지정하고 확인하거나 SSH(보안 셸) 공용 키를 제공합니다.

7.	**가격 책정 계층**을 선택합니다. 권장 가격 책정 계층이 기본적으로 표시됩니다. 모든 구성 옵션을 보려면 오른쪽 위에서 **모두 보기**를 클릭합니다.

8. 필요에 따라 선택적 구성을 설정합니다. 다음 고려 사항을 따르세요.

	a. 가상 컴퓨터 이름으로 새 저장소 계정을 만들려면 **저장소 계정**을 그대로 둡니다.

	b. **가용성 집합**을 **구성되지 않음**으로 둡니다.

	c. 이때 끝점을 추가하지 마세요.

9.	리소스 그룹을 선택하거나 만듭니다. 자세한 내용은 [Azure 포털을 사용하여 Azure 리소스 관리](../resource-group-portal.md)를 참조하세요.

10. **구독**을 선택합니다.

11. **위치**를 선택합니다.


##이 가상 컴퓨터에서 호스트되는 데이터베이스 만들기

**Azure에서 Oracle Database 12c 가상 컴퓨터를 사용하여 데이터베이스를 만들려면** 섹션으로 시작하는 [Azure에서 Oracle Database 12c 가상 컴퓨터 만들기](virtual-machines-windows-classic-create-oracle-database.md)의 지침을 따릅니다.

##이 가상 컴퓨터에서 호스트되는 Oracle WebLogic Server 12c를 구성하려면
**Azure에서 Oracle WebLogic Server 12c 가상 컴퓨터를 구성하려면** 섹션으로 시작하는 [Azure에서 Oracle WebLogic Server 12c 가상 컴퓨터 만들기](virtual-machines-windows-create-oracle-weblogic-server-12c.md)의 지침을 따릅니다.

##추가 리소스
[Oracle 가상 컴퓨터 이미지에 대한 기타 고려 사항](virtual-machines-windows-classic-oracle-considerations.md)

[Oracle 가상 컴퓨터 이미지 목록](virtual-machines-linux-classic-oracle-images.md)

[Java 응용 프로그램에서 Oracle 데이터베이스에 연결](http://docs.oracle.com/cd/E11882_01/appdev.112/e12137/getconn.htm#TDPJD136)

[Microsoft Azure에서 Linux를 사용하는 Oracle WebLogic Server 12c](http://www.oracle.com/technetwork/middleware/weblogic/learnmore/oracle-weblogic-on-azure-wp-2020930.pdf)

[Oracle 데이터베이스 2일 DBA 12c 릴리스 1](http://docs.oracle.com/cd/E16655_01/server.121/e17643/toc.htm)

<!---HONumber=AcomDC_0323_2016-->