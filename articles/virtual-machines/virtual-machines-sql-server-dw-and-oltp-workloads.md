<properties 
	pageTitle="Azure 가상 컴퓨터의 SQL Server 데이터 웨어하우징 및 트랜잭션 작업"
	description="데이터 웨어하우징 및 OLTP 워크로드를 위해 미리 구성 및 최적화된 Azure의 SQL Server 가상 컴퓨터 이미지를 설명합니다."
	services="virtual-machines"
	documentationCenter="na"
	authors="rothja"
	manager="jeffreyg"
	editor="monicar"/>
<tags 
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="08/19/2015"
	ms.author="jroth"/>

# Azure 가상 컴퓨터의 SQL Server 데이터 웨어하우징 및 트랜잭션 작업

Azure 가상 컴퓨터에서 데이터 웨어하우징 또는 트랜잭션 워크로드에 SQL Server를 사용하려면 Azure 가상 컴퓨터 갤러리에서 미리 구성한 가상 컴퓨터 이미지 중 하나를 사용하는 것이 좋습니다. 이러한 이미지는 [Azure 가상 컴퓨터의 SQL Server에 대한 성능 모범 사례](virtual-machines-sql-server-performance-best-practices.md)의 권장 사항을 기준으로 최적화되어 있습니다.

이 문서에서는 Azure 가상 컴퓨터에서 이러한 워크로드를 실행하는 데 초점을 맞춥니다(IaaS, 서비스 형태의 인프라라고도 함). Azure에서 데이터 웨어하우징과 트랜잭션 워크로드를 서비스 형태로 실행할 수도 있습니다. 자세한 내용은 [Azure SQL 데이터 웨어하우스에 미리 보기](http://azure.microsoft.com/documentation/services/sql-data-warehouse/) 및 [Azure SQL 데이터베이스](http://azure.microsoft.com/documentation/services/sql-database/)를 참조하세요.

## 사용 가능한 미리 구성된 VM 이미지에는 어떤 것이 있습니까?

다음의 미리 구성된 VM 이미지를 Azure VM 갤러리에서 사용할 수 있습니다.

- [Windows Server 2012 R2의 트랜잭션 워크로드에 맞게 최적화된 SQL Server 2014 Enterprise](http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2014fortransactionalworkloadswindowsserver2012r2/)

- [Windows Server 2012 R2의 데이터 웨어하우징 워크로드에 맞게 최적화된 SQL Server 2014 Enterprise](http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2014datawarehousingwindowsserver2012r2/)

- [Windows Server 2012 R2의 트랜잭션 워크로드에 맞게 최적화된 SQL Server 2012 SP2 Enterprise](http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2012sp2fortransactionalworkloadswindowsserver2012r2)

- [Windows Server 2012 R2의 데이터 웨어하우징 워크로드에 맞게 최적화된 SQL Server 2012 SP2 Enterprise](http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2012sp2datawarehousingworkloadswindowsserver2012r2)

- [Windows Server 2012의 트랜잭션 워크로드에 맞게 최적화된 SQL Server 2012 SP2 Enterprise](http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2012sp2fortransactionalworkloadswindowsserver2012/)

- [Windows Server 2012의 데이터 웨어하우징에 맞게 최적화된 SQL Server 2012 SP2 Enterprise](http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2012sp2datawarehousingworkloadswindowsserver2012/)

현재는 최고 처리량(또는 집계 대역폭)을 제공하기 위해 최대 16개의 데이터 디스크 연결을 허용하는 VM 인스턴스에서 이러한 이미지를 지원하고 있습니다. 이 인스턴스는 표준 계층 A4, A7, A8, A9, D4, D13, D14, F3, G4 및 G5와 기본 계층 A4입니다. 크기 및 옵션에 대한 자세한 내용은 [Azure에서의 가상 컴퓨터 크기](virtual-machines-size-specs.md)를 참조하세요.

>[AZURE.NOTE]2014년 9월 이전에는 구 트랜잭션 및 DW 갤러리 이미지를 사용할 수 있었습니다. 그러나 이러한 이미지를 사용하려면 데이터 디스크를 연결해야 했습니다. 프로비전 시점에 사용할 수 있게 준비된 최신 이미지를 사용하는 것이 좋습니다.

## 트랜잭션/DW 이미지를 사용하여 갤러리에서 SQL VM 프로비저닝

1. [Azure 관리 포털](http://manage.windowsazure.com/)에 로그인합니다.

1. 왼쪽 창의 Azure 메뉴 항목에서 **가상 컴퓨터**를 클릭합니다.

1. 하단 왼쪽 모서리에서 **새로 만들기**를 클릭한 다음 **컴퓨터**, **가상 컴퓨터**, **갤러리에서**를 차례로 클릭합니다.

1. 가상 컴퓨터 이미지 선택 페이지에서 트랜잭션 또는 데이터 웨어하우징 이미지에 대한 SQL Server 중 하나를 선택합니다.

	![Azure VM 갤러리](./media/virtual-machines-sql-server-dw-and-oltp-workloads/IC814362.png)

1. **가상 컴퓨터 구성** 페이지의 **크기** 옵션에서 지원되는 크기를 선택합니다.

	![Azure VM 갤러리 구성](./media/virtual-machines-sql-server-dw-and-oltp-workloads/IC814363.png)

	>[AZURE.NOTE]현재는 표준 계층 A4, A7, A8, A9, D4, D13, D14, G3, G4 및 G5와 기본 계층 A4가 지원됩니다. 지원되지 않는 VM 크기의 프로비전 시도는 실패합니다.

1. 프로비전이 완료될 때까지 기다립니다. 기다리는 동안 가상 컴퓨터 페이지(아래 그림처럼)에서 프로비전 상태를 확인할 수 있습니다. 프로비전이 완료되면 상태가 **실행 중**이 되며 확인 표시가 나타납니다.

	![Azure VM 갤러리 상태](./media/virtual-machines-sql-server-dw-and-oltp-workloads/IC814364.png)

## PowerShell을 사용하여 트랜잭션/DW 이미지 만들기

PowerShell Commandlet **New-AzureQuickVM**을 사용하여 VM을 만들 수도 있습니다. 클라우드 서비스 이름, VM 이름, 이미지 이름, 관리자 사용자 이름 및 암호, 유사 정보를 매개 변수 형태로 전달해야 합니다. **Get-AzureVMImage**를 사용하여 사용 가능한 모든 VM 이미지을 나열하면 이미지 이름을 간단하게 가져올 수 있습니다.

예를 들어, 다음 PowerShell 명령은 위섹션의 목록에서 이미지 레이블이 **Windows Server 2012 R2의 데이터 웨어하우징 워크로드에 맞게 최적화된 SQL Server 2012 SP2 Enterprise**인 최신 이미지를 반환합니다.

	(Get-AzureVMImage | where {$_.Label -like "SQL Server 2012 SP2 Enterprise Optimized for DataWarehousing Workloads on Windows Server 2012 R2"} | sort PublishedDate -Descending)[0].ImageName

PowerShell을 사용하여 VM을 만드는 것과 관련한 자세한 내용은 [Azure PowerShell을 사용하여 Windows 기반 가상 컴퓨터 만들기 및 미리 구성하기](virtual-machines-ps-create-preconfigure-windows-vms.md)를 참조하세요.

## 트랜잭션/DW 이미지에 포함된 특정 구성

이 이미지에 포함된 최적화는 [Azure 가상 컴퓨터의 SQL Server에 대한 성능 모범 사례](virtual-machines-sql-server-performance-best-practices.md)를 기준으로 합니다. 특히 이러한 이미지에 대한 구성은 다음과 같은 최적화를 포함합니다.

>[AZURE.NOTE]사용자 고유의 라이선스가 있고 데이터 웨어하우징 또는 트랜잭션 가상 컴퓨터를 처음부터 새로 만들려면 아래의 미리 구성된 갤러리 이미지에서 제공하는 최적화 예제와 성능 문서를 기준으로 최적화를 수행할 수 있습니다.

### 디스크 구성

|구성|설정|
|---|---|
|연결된 데이터 디스크 수|15|
|저장소 공간|2개의 저장소 풀:<br/>--1개의 데이터 풀(12개 데이터 디스크, 12TB 고정 크기); Column = 12<br/>--1개의 로그 풀(3개 데이터 디스크, 3TB 고정 크기); Column = 3<br/><br/>데이터 디스크 하나는 사용자가 연결하여 사용을 결정할 수 있게 남아 있음<br/><br/>**DW**: 스트라이프 크기 = 256KB<br/>**트랜잭션**: 스트라이프 크기 = 64KB|
|디스크 크기|각각 1TB|
|구성|HostCache=None|
|할당 크기|NTFS 할당 단위 크기 = 64KB|

### SQL Server 구성

|구성|설정|
|---|---|
|시작 매개 변수|-T1117: 데이터베이스가 자동 증가해야 할 경우 데이터 파일을 동일한 크기로 유지<br/><br/>-T1118: tempdb 확장성 지원(자세한 내용은 [SQL Server(2005 및 2008) 추적 플래그 1118(-T1118) 사용](http://blogs.msdn.com/b/psssql/archive/2008/12/17/sql-server-2005-and-2008-trace-flag-1118-t1118-usage.aspx?WT.mc_id=Blog_SQL_Announce_Announce) 참조)|
|복구 모델|**DW**: ALTER DATABASE를 사용하여 model 데이터베이스에 대해 SIMPLE로 설정<br/>**트랜잭션**: 변경 없음|
|설정 기본 위치|SQL Server 오류 로그와 추적 파일 디렉터리를 데이터 디스크로 이동|
|데이터베이스 기본 위치|시스템 데이터베이스가 데이터 디스크로 옮겨졌습니다.<br/><br/>사용자 데이터베이스 만들기를 위한 위치가 데이터 디스크로 변경되었습니다.|
|즉시 파일 초기화|사용|
|메모리 내 페이지 잠금|사용(자세한 내용은 [메모리 내 페이지 잠금 옵션 사용(Windows) 참조)](https://msdn.microsoft.com/library/ms190730.aspx)|

## FAQ

- 최적화된 이미지와 최적화되지 않은 이미지 간에 가격 차이가 있습니까?

	아니요. 최적화된 이미지도 같은 가격 책정 모델을 따르며 추가 비용이 없습니다(자세한 내용은 [여기](http://azure.microsoft.com/pricing/details/virtual-machines/) 참조). 비용이 더 높아지면 VM 인스턴스 크기가 더 커집니다.

- 고려해야 하는 성능 픽스가 있습니까?

	다음과 같은 SQL Server 성능 픽스를 적용하는 것이 좋습니다.

	- [픽스: SQL Server 2012에서 임시 테이블 작업에 대한 Select를 실행할 때 I/O 성능 저하](https://support.microsoft.com/kb/2958012)

	- [픽스: SQL Server 2014에서 SQL Server 리소스를 이동할 때 "SQL Server 성능 카운터가 비활성화됨"](http://support.microsoft.com/kb/2973444)

- 저장소 공간에 대한 자세한 내용을 찾으려면 어떻게 해야 합니까?

	저장소 공간에 자세한 내용은 [저장소 공간 FAQ(질문과 대답)](http://social.technet.microsoft.com/wiki/contents/articles/11382.storage-spaces-frequently-asked-questions-faq.aspx)를 참조하세요.

- 새 DW 이미지와 기존 이미지 간의 차이는 무엇입니까?

	기존 DW 이미지의 경우 VM을 만든 후 데이터 디스크를 연결하는 등의 추가적인 작업을 고객이 수행해야 했습니다. 반면 새 DW 이미지는 만든 즉시 사용할 수 있기 때문에 더 신속하게 제공되며 오류 가능성이 더 낮습니다.

- 이전 DW 이미지를 사용해야 할 경우 어떻게 합니까? 액세스할 수 있는 방법이 있습니까?

	이전 VM 이미지는 계속 사용할 수 있습니다. 갤러리에서 직접 액세스하짐 못할 뿐입니다. Powershell commandlet을 통해 사용을 계속할 수 있습니다. 예를 들어 **Get-AzureVMImage**를 사용하여 모든 이미지를 나열할 수 있고 설명과 게시일을 근거로 이전 DW 이미지를 찾으면 **New-AzureVM**을 사용하여 프로비전할 수 있습니다.

## 다음 단계

SQL Server와 함께 모든 가상 컴퓨터를 설치한 후 다음 작업이 필요할 수 있습니다.

- [데이터 마이그레이션](virtual-machines-migrate-onpremises-database.md)
- [연결 설정](virtual-machines-sql-server-connectivity.md).

Azure VM에서 SQL Server 실행에 관련된 다른 항목은 [Azure 가상 컴퓨터의 SQL Server](virtual-machines-sql-server-infrastructure-services.md)를 참조하세요.

<!---HONumber=August15_HO9-->