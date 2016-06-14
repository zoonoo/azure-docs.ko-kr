<properties
	pageTitle="Oracle WebLogic Server 및 데이터베이스 VM | Microsoft Azure"
	description="리소스 관리자 배포 모델을 사용하여 Windows Server 2012에서 실행 중인 Oracle WebLogic Server 12c 및 Oracle Database 12c Azure 이미지를 만듭니다."
	services="virtual-machines-windows"
	authors="rickstercdn"
	manager="timlt"
	documentationCenter=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows"
	ms.workload="infrastructure-services"
	ms.date="05/17/2016"
	ms.author="rickstercdn" />

#Azure에서 Oracle WebLogic Server 12c 및 Oracle Database 12c 가상 컴퓨터 만들기

[AZURE.INCLUDE [virtual-machines-common-oracle-support](../../includes/virtual-machines-common-oracle-support.md)]

이 문서에서는 Azure에서 Windows Server 2012에 Oracle 소프트웨어가 설치된 상태로 이전에 만든 이미지에서 Oracle WebLogic Server 12c 및 Oracle 데이터베이스 12c를 만드는 방법을 보여 줍니다.

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

<!---HONumber=AcomDC_0601_2016-->