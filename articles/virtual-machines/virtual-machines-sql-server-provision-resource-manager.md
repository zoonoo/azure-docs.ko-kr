<properties
	pageTitle="Azure 리소스 관리자(GUI)에서 SQL Server 가상 컴퓨터 프로비전 | Microsoft Azure"
	description="Azure 리소스 관리자 모드에서 SQL Server 가상 컴퓨터 만들기 이 자습서에서는 스크립트보다는 사용자 인터페이스 및 도구를 주로 사용합니다."
	services="virtual-machines"
	documentationCenter="na"
	authors="MikeRayMSFT"
    editor=""
	manager="jeffreyg"
	tags="azure-service-management" />


<tags
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="12/22/2015"
	ms.author="mikeray" />

# Azure 리소스 관리자에서 SQL Server 가상 컴퓨터 프로비전

> [AZURE.SELECTOR]
- [Classic portal](virtual-machines-provision-sql-server.md)
- [PowerShell](virtual-machines-sql-server-create-vm-with-powershell.md)
- [Azure Resource Manager portal](virtual-machines-sql-server-provision-resource-manager.md)

<br/>

>[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]클래식 배포 모델.

이 종단 간 자습서에서는 Azure 리소스 관리자 모델을 사용하여 포털에서 Azure 가상 컴퓨터를 프로비전하고 Azure 갤러리의 템플릿에서 SQL Server를 구성하는 방법을 보여줍니다.

Azure 가상 컴퓨터(VM) 갤러리에는 Microsoft SQL Server가 포함된 몇 개의 이미지가 있습니다. 갤러리에서 VM 이미지 중 하나를 선택하고 몇 번의 클릭으로 Azure 환경에 VM을 프로비전할 수 있습니다.

이 자습서에서는 다음을 수행합니다.

- [리소스 관리자 배포 모델을 사용하여 Azure 포털에 연결 및 갤러리에서 SQL VM 이미지 프로비전](#Provision)

- [가상 컴퓨터 및 SQL Server 설정 구성](#ConfigureVM)

- [원격 데스크톱을 사용하여 가상 컴퓨터 열기](#Open)

- [다른 컴퓨터에서 SQL Server Management Studio를 사용하여 SQL Server 인스턴스에 연결](#Connect)

- [다음 단계](#Next)

이 자습서에서는 사용자에게 Azure 계정이 이미 있다고 가정합니다. Azure 계정이 없는 경우 [Azure 무료 평가판](http://azure.microsoft.com/pricing/free-trial/)을 방문하십시오.

## <a id="Provision">리소스 관리자 배포 모델을 사용하여 갤러리에서 SQL VM 이미지 프로비전

1. 사용자 계정을 사용하여 [Azure 포털](http://portal.azure.com)에 로그인합니다.
1. Azure 포털에서 **+새로 만들기**를 클릭합니다. 포털에서 **새** 블레이드가 열립니다. SQL Server VM 템플릿은 마켓플레이스의 **계산** 그룹에 있습니다.

1. **새** 블레이드에서 **계산**을 클릭합니다.
1. **계산** 블레이드에 있는 모든 리소스 유형을 보려면 **모두 보기**를 클릭합니다. <br/> ![Azure 계산 블레이드](./media/virtual-machines-sql-server-provision-resource-manager/azure-compute-blade.png) <br/>
1. **데이터베이스 서버**에서 SQL Server에 대해 사용 가능한 모든 템플릿을 보려면 **SQL Server**를 클릭합니다. **데이터베이스 서버**를 찾기 위해 아래로 스크롤해야 할 수도 있습니다.
1. 	각 템플릿은 SQL Server 버전 및 운영 체제를 식별합니다. 목록에서 이러한 이미지 중 하나를 선택하여 세부 정보가 포함된 블레이드를 표시합니다.
1.	세부 정보 블레이드는 이 가상 컴퓨터 이미지에 대한 설명을 제공하고 여기에서 배포 모델을 선택할 수 있습니다. **배포 모델 선택** 아래에서 **리소스 관리자**를 선택하고 **만들기**를 클릭합니다. <br/> ![Azure 계산 블레이드](./media/virtual-machines-sql-server-provision-resource-manager/azure-compute-sql-deployment-model.png) <br/>

## <a id="ConfigureVM"> VM 구성
Azure 포털에는 SQL Server 가상 컴퓨터를 구성하기 위한 5개의 블레이드가 있습니다.

1.	기본 설정 구성
1.	가상 컴퓨터 크기 선택
1.	가상 컴퓨터 설정 구성
1.	SQL Server 구성
1.	요약 검토

## 1\. 기본 설정 구성
**가상 컴퓨터 만들기** 블레이드의 **기본 사항** 아래에서 다음 정보를 제공합니다.

* 고유한 가상 컴퓨터 **이름**.
* **사용자 이름** 상자에 컴퓨터 로컬 관리자 계정의 고유한 사용자 이름을 입력합니다. 이 계정은 SQL Server sysadmin 고정 서버 역할의 멤버이기도 합니다.
* **암호** 상자에 강력한 암호를 입력합니다.
* 구독이 여러 개인 경우 구독이 작성하려고 하는 VM에 대해 올바른지 확인합니다.
* **리소스 그룹** 상자에 리소스 그룹의 이름을 입력합니다. 또는 기존 리소스 그룹을 사용하려면 **기존 선택**을 클릭합니다. 리소스 그룹은 Azure에서 관련된 서비스의 컬렉션입니다. 리소스 그룹에 대한 자세한 내용은 [Azure 리소스 관리자 개요](../resource-group-overview.md)를 참조하세요. **위치**가 요구 사항에 적합한지 확인합니다.
* **확인**을 클릭하여 설정을 저장합니다. <br/>

>![SQL ARM 기본 사항](./media/virtual-machines-sql-server-provision-resource-manager/azure-sql-arm-basic.png) <br/>

## 2\. 가상 컴퓨터 크기 선택
**가상 컴퓨터 만들기** 블레이드의 **크기** 아래에서 가상 컴퓨터 크기를 선택합니다. Azure 포털에 권장되는 크기가 표시됩니다. 가상 컴퓨터 크기에 대한 자세한 내용은 [가상 컴퓨터 크기](virtual-machines-size-specs.md)를 참조하세요. 크기는 선택한 템플릿을 기반으로 합니다. 크기는 VM을 실행하는 월간 비용을 예측합니다. 서버에 대한 VM 크기를 선택합니다. SQL Server VM 크기에 대한 고려 사항은 [Azure 가상 컴퓨터의 SQL Server에 대한 성능 모범 사례](virtual-machines-sql-server-performance-best-practices.md)를 참조하세요.

## 3\. 가상 컴퓨터 설정 구성
**가상 컴퓨터 만들기** 블레이드의 **설정** 아래에서 Azure 저장소, 가상 컴퓨터의 네트워킹 및 모니터링을 구성합니다.

- **저장소** 아래에서 디스크 유형을 지정합니다. 프리미엄 저장소는 프로덕션 워크로드용으로 권장됩니다.

>[AZURE.NOTE]프리미엄 저장소는 기본적으로 사용됩니다. 프리미엄 저장소를 지원하는 크기로 컴퓨터의 크기가 자동으로 조정됩니다. 프리미엄 저장소를 비활성화하면 이전 컴퓨터 크기 선택이 사용됩니다.

- **저장소 계정** 아래에서 자동으로 프로비전된 저장소 계정 이름을 적용 하거나, **저장소 계정**을 클릭하여 기존 계정을 선택하고 저장소 계정 유형을 구성할 수 있습니다. 기본적으로 Azure에서는 로컬 중복 저장소로 새 저장소 계정을 만듭니다.

- **네트워크** 아래에서 기능에 대해 자동으로 채워진 값을 적용하거나 각 기능을 클릭하여 **가상 네트워크**, **서브넷**, **공용 IP 주소** 및 **네트워크 보안 그룹**을 구성할 수 있습니다. 기본적으로 Azure에서는 이러한 값을 자동으로 구성합니다.

- Azure에서는 VM에 지정된 것과 동일한 저장소 계정을 사용하여 기본적으로 **모니터링**을 활성화합니다. 여기에서 이러한 설정을 변경할 수 있습니다.

- **가용성 집합** 아래에서 가용성 집합을 지정합니다. 이 자습서에서는 **없음**을 선택할 수 있습니다. SQL AlwaysOn 가용성 그룹을 설정하려는 경우 가상 컴퓨터를 다시 만들지 않도록 가용성을 구성합니다. 자세한 내용은 [가상 컴퓨터의 가용성 관리](virtual-machines-manage-availability.md)를 참조하세요.

## 4\. SQL Server 구성
**가상 컴퓨터 만들기** 블레이드의 **SQL Server 구성** 아래에서 SQL Server에 대한 특정 설정 및 최적화를 구성합니다. SQL Server에 대해 구성할 수 있는 설정은 다음과 같습니다.

- 연결
- 인증
- 저장소 최적화
- 패치
- 백업
- 주요 자격 증명 모음 통합

### 연결
**SQL 연결** 아래에서 **공용(인터넷)**을 지정하여 컴퓨터 또는 인터넷 상의 서비스에서 SQL Server로의 연결을 허용합니다. 이 옵션을 선택하면 Azure에서는 포트 1433에서 트래픽을 허용하도록 방화벽 및 네트워크 보안 그룹을 자동으로 구성합니다. <br/>![SQL ARM 연결](./media/virtual-machines-sql-server-provision-resource-manager/azure-sql-arm-connectivity-alt.png) <br/>

인터넷을 통해 SQL Server에 연결하려면 먼저 SQL Server 인증을 사용하도록 설정해야 합니다.

>[AZURE.NOTE]보안을 위해 네트워크 보안 그룹을 사용하여 원본 포트를 제한합니다. 자세한 내용은 [NSG(네트워크 보안 그룹)란?](virtual-networks-nsg.md)을 참조하세요.

인터넷을 통해 데이터베이스 엔진에 대한 연결을 사용하지 않으려는 경우 다음 옵션 중 하나가 자동으로 선택됩니다.- **로컬(VM 내부만)**은 VM 내부에서 SQL Server만으로 연결을 허용합니다. - **사설(가상 네트워크 내부)**은 동일한 가상 네트워크의 컴퓨터 또는 서비스에서 SQL Server로의 연결을 허용합니다.


**포트** 기본값은 1433입니다. 다른 포트 번호를 지정할 수 있습니다. 자세한 내용은 [SQL Server 가상 컴퓨터에 연결(리소스 관리자) | Microsoft Azure](virtual-machines-sql-server-connectivity-resource-manager.md)를 참조하세요.



### 인증
SQL Server 인증을 요구하는 경우 **SQL 인증** 아래 **사용**을 클릭합니다.

<br/>![SQL ARM 인증](./media/virtual-machines-sql-server-provision-resource-manager/azure-sql-arm-authentication.png) <br/>


SQL Server 인증을 사용하도록 설정하는 경우 **로그인 이름** 및 **암호**를 지정합니다. 이 사용자 이름은 SQL Server 인증 로그인 및 sysadmin 고정된 서버 역할의 멤버가 됩니다. 인증 모드에 대한 자세한 내용은 [인증 모드 선택](http://msdn.microsoft.com/library/ms144284.aspx)을 참조하세요. 기본적으로 SQL Server는 SQL Server 인증을 사용하지 않습니다. 이 시나리오에서 가상 컴퓨터의 로컬 관리자는 SQL Server 인스턴스에 연결할 수 있습니다.

>[AZURE.NOTE]인터넷(즉, 공용 연결 옵션)을 통해 SQL Server에 액세스하려는 경우 여기에서 SQL 인증을 사용해야 합니다. SQL Server에 대한 공용 액세스를 위해서는 SQL 인증을 사용해야 합니다.

### 저장소 최적화
저장소 요구 사항을 지정하려면 **저장소 구성**을 클릭합니다. 초당 입/출력 작업(IOPs), 처리량(MB/s) 및 총 저장소 크기로 요구 사항을 지정할 수 있습니다. 슬라이딩 규모를 사용하여 이를 구성합니다. 포털에는 이러한 요구 사항에 따라 디스크 수를 자동으로 계산합니다.

기본적으로 Azure에서는 5000 IOPs, 200MBs 및 1TB의 저장소 공간에 대해 저장소를 최적화합니다. 워크로드에 따라 이러한 설정 저장소를 변경할 수 있습니다. **다음에 대해 저장소 최적화**에서 다음 중 하나를 선택합니다.

- **일반**은 기본 설정이며 대부분의 워크로드를 지원합니다.다.
- **트랜잭션** 처리는 기존의 데이터베이스 OLTP 워크로드에 대해 저장소를 최적화합니다.
- **데이터 웨어하우징**은 분석 및 보고 워크로드에 대해 저장소를 최적화합니다.

다음 이미지는 저장소 구성 블레이드를 보여줍니다. <br/>![SQL ARM 저장소](./media/virtual-machines-sql-server-provision-resource-manager/azure-sql-arm-storage.png) <br/>

>[AZURE.NOTE]저장소 구성 제한은 가상 컴퓨터 크기에 따라 달라집니다. 자세한 내용은 [가상 컴퓨터의 크기](virtual-machines-size-specs.md)를 참조하세요.

### 패치
기본적으로 **SQL 자동화된 패치**가 사용됩니다. Azure에서는 자동화된 패치를 통해 SQL Server와 운영 체제를 자동으로 패치합니다. 요일, 시간 및 유지 관리 기간에 대한 날짜를 지정합니다. Azure에서 유지 관리 기간에 패치를 수행합니다. 유지 관리 기간 일정에서는 VM 로캘 시간을 사용합니다. Azure에서 SQL Server와 운영 체제를 자동으로 패치하도록 하지 않으려면 **사용 안 함**을 클릭합니다.

<br/>![SQL ARM 패치](./media/virtual-machines-sql-server-provision-resource-manager/azure-sql-arm-patching.png) <br/>

자세한 내용은 [Azure 가상 컴퓨터에서 SQL Server에 대해 자동화된 패치](virtual-machines-sql-server-automated-patching.md)를 참조하세요.

### 백업
**SQL 자동화된 백업** 아래에서 모든 데이터베이스에 대해 자동 데이터베이스 백업을 사용합니다. SQL 자동화된 백업을 사용하면 다음을 구성할 수 있습니다.

- 백업 보존 기간(일)
- 백업에 사용할 저장소 계정
- 백업을 암호화할지 여부를 나타냅니다. 백업을 암호화하려면 **사용**을 클릭합니다. 자동화된 백업을 암호화하는 경우 암호를 지정합니다. Azure에서는 백업을 암호화할 인증서를 만들고 지정된 암호를 사용하여 인증서를 보호합니다.

<br/>![SQL ARM 백업](./media/virtual-machines-sql-server-provision-resource-manager/azure-sql-arm-autobackup.png) <br/>

 자세한 내용은 [Azure 가상 컴퓨터에서 SQL Server에 대한 자동화된 백업](virtual-machines-sql-server-automated-backup.md)을 참조하세요.

### 주요 자격 증명 모음 통합
Azure에서 암호화를 위한 보안 비밀 정보를 저장하려면 **Azure 주요 자격 증명 모음 통합**을 클릭하고 **사용**을 클릭합니다.

<br/>![SQL ARM Azure 주요 자격 증명 모음 통합](./media/virtual-machines-sql-server-provision-resource-manager/azure-sql-arm-akv.png) <br/>

다음 표에서는 Azure 주요 자격 증명 모음 통합을 구성하는 데 필요한 매개 변수를 나열합니다.

|매개 변수|설명|예제|
|----------|----------|-------|
|**주요 자격 증명 모음 URL** | 주요 자격 증명 모음의 위치입니다.|https://contosokeyvault.vault.azure.net/ |
|**AKV 주체 이름** |Azure Active Directory 서비스 주체 이름. 이를 클라이언트 ID라고도 합니다. |fde2b411-33d5-4e11-af04eb07b669ccf2|
| **AKV 주체 암호**|AKV 통합은 VM이 주요 자격 증명 모음에 액세스할 수 있도록 SQL Server 내에 자격 증명을 만듭니다. 이 자격 증명의 이름을 선택하세요. | 9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM=|
|**자격 증명 이름**|이 자격 증명을 식별할 이름을 선택합니다.| mycred1|

자세한 내용은 [Azure VM에서 SQL Server에 대한 Azure 주요 자격 증명 모음 통합 구성](virtual-machines-sql-server-azure-key-vault-integration.md)을 참조하세요.

## 5\. 요약 검토
요약을 검토하고 **확인**을 클릭하여 SQL Server, 리소스 그룹 및 이 VM에 대해 지정된 리소스를 만듭니다. Azure 포털에서 배포를 모니터링할 수 있습니다. 화면 맨 위에 있는 **알림** 단추는 배포의 기본 상태를 표시합니다.

##<a id="Open"> 원격 데스크톱을 사용하여 가상 컴퓨터 열기 및 설치 완료
다음 단계에 따라 원격 데스크톱을 사용하여 가상 컴퓨터를 엽니다.

1.	Azure VM을 작성하면 VM에 대한 아이콘이 Azure 대시보드에 표시됩니다. 이 아이콘을 클릭하면 VM에 대한 정보가 표시됩니다.
1.	VM 블레이드 맨 위에서 **연결**을 클릭합니다. 브라우저가 VM에 대한 .rdp 파일을 다운로드합니다. .rdp 파일을 엽니다.
1.	원격 데스크톱 연결에서 이 원격 연결의 게시자를 식별할 수 없으며 그래도 연결할지 여부를 묻는 메시지가 표시됩니다. **Connect**를 클릭합니다.
1.	**Windows 보안** 대화 상자에서 **다른 계정 사용**을 클릭합니다. **사용자 이름**에서 VM을 구성할 때 지정한 <machine name><사용자 이름>을 입력합니다.

SQL Server 가상 컴퓨터에 연결되었으면 SQL Server Management Studio를 시작하고 로컬 관리자 자격 증명을 사용하여 Windows 인증으로 연결할 수 있습니다. 그러면 방화벽 설정이나 필요한 경우 프로비전 후 SQL Server 구성 설정을 변경할 수 있습니다.

##<a id="Connect"> 인터넷을 통해 SQL Server에 연결

인터넷에서 SQL Server 데이터베이스 엔진에 연결하려는 경우 방화벽 구성, SQL Server 인증 사용, 네트워크 보안 그룹 구성 등의 여러 단계가 있습니다. 포트 1433에 TCP 트래픽을 허용하도록 네트워크 보안 그룹 규칙이 있어야 합니다.

포털을 사용하여 리소스 관리자를 통해 SQL Server 가상 컴퓨터 이미지를 프로비전하는 경우 SQL 연결 옵션 및 사용된 SQL Server 인증에 **공용**을 선택하면 다음 단계가 수행됩니다. 그러나 인터넷을 통한 SQL Server 인스턴스 액세스를 완료하는 데 몇 가지 남은 단계가 있습니다.

>[AZURE.NOTE]프로비전 중에 공용을 선택하지 않은 경우 인터넷을 통한 SQL Server 인스턴스 액세스에 추가 단계가 필요합니다. 자세한 내용은 [SQL Server 가상 컴퓨터에 연결(리소스 관리자) | Microsoft Azure](virtual-machines-sql-server-connectivity-resource-manager.md)를 참조하세요.

가상 컴퓨터에 로컬로 액세스하거나 동일한 가상 네트워크 내에서 액세스해야 하는 경우 다음 단계가 필요하지 않습니다.

> [AZURE.INCLUDE [VM 리소스 관리자에서 SQL Server에 연결](../../includes/virtual-machines-sql-server-connection-steps-resource-manager.md)]

##<a id="Next">다음 단계
Azure에서 SQL Server를 사용하는 방법에 대한 기타 정보는 [Azure 가상 컴퓨터의 SQL Server](../articles/virtual-machines/virtual-machines-sql-server-infrastructure-services.md)를 참조하세요.

<!---HONumber=AcomDC_0107_2016-->