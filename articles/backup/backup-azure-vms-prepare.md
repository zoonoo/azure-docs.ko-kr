<properties
	pageTitle="Azure 가상 컴퓨터를 백업하기 위한 환경 준비 | Microsoft Azure"
	description="환경이 Azure 가상 컴퓨터를 백업할 준비가 되었는지 확인"
	services="backup"
	documentationCenter=""
	authors="Jim-Parker"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/02/2015"
	ms.author="trinadhk; aashishr; jimpark; markgal"/>

# Azure 가상 컴퓨터를 백업하기 위한 환경 준비
Azure 가상 컴퓨터를 백업하기 전에 환경을 준비하려면 이러한 필수 구성을 완료해야 합니다. 이 작업을 이미 완료한 경우 [VM 백업](backup-azure-vms.md)을 시작할 수 있습니다. 그렇지 않은 경우 환경이 준비되었는지 확인하는 아래 단계를 계속 진행합니다.


## 1\. 백업 자격 증명 모음

![백업 자격 증명 모음](./media/backup-azure-vms-prepare/step1.png)

Azure 가상 컴퓨터를 백업하려면 먼저 백업 자격 증명 모음을 만들어야 합니다. 자격 증명 모음은 모든 백업과 시간에 따라 생성된 복구 지점을 저장하는 엔터티입니다. 자격 증명 모음에는 백업 중인 가상 컴퓨터에 적용할 백업 정책도 포함됩니다.

이 그림은 여러 Azure 백업 엔터티 간의 관계를 보여 줍니다. ![Azure 백업 엔터티 및 관계](./media/backup-azure-vms-prepare/vault-policy-vm.png)

백업 자격 증명 모음을 만들려면:

1. [관리 포털](http://manage.windowsazure.com/)에 로그인합니다.

2. **새로 만들기** > **데이터 서비스** > **복구 서비스** > **백업 자격 증명 모음** > **빠른 생성**을 클릭합니다. 조직 계정과 연결된 구독이 여러 개인 경우 백업 자격 증명 모음과 연결할 올바른 계정을 선택합니다. 각 Azure 구독에서 여러 개의 백업 자격 증명 모음을 사용하여 보호 중인 가상 컴퓨터를 구성할 수 있습니다.

3. **이름**에 자격 증명 모음을 식별하기 위한 이름을 입력합니다. 이는 각 구독에 대해 고유해야 합니다.

4. **지역**에서 자격 증명 모음에 대한 지리적 지역을 선택합니다. 자격 증명 모음은 보호하려는 가상 컴퓨터와 동일한 지역에 있어야 합니다. 가상 컴퓨터가 여러 지역에 있는 경우 각 지역에 자격 증명 모음을 만듭니다. 백업 데이터를 저장하기 위해 저장소 계정을 지정할 필요는 없습니다. 백업 자격 증명 모음 및 Azure 백업 서비스가 자동으로 처리합니다.

    ![백업 자격 증명 모음 만들기](./media/backup-azure-vms-prepare/backup_vaultcreate.png)

5. **자격 증명 모음 만들기**를 클릭합니다. 백업 자격 증명 모음을 만드는 데 시간이 걸릴 수 있습니다. 포털의 맨 아래에서 상태 알림을 모니터링합니다.

    ![자격 증명 모음 알림 메시지 만들기](./media/backup-azure-vms-prepare/creating-vault.png)

6. 자격 증명 모음이 성공적으로 만들어졌으며 복구 서비스 페이지에 활성 상태로 나열됨을 확인하는 메시지가 나타납니다. 자격 증명 모음이 생성된 후 즉시 적절한 저장소 중복 옵션을 선택해야 합니다. [백업 자격 증명 모음에서 저장소 중복 옵션 설정](backup-configure-vault.md#azure-backup---storage-redundancy-options)에 대해 자세히 알아보세요.

    ![백업 자격 증명 모음 목록](./media/backup-azure-vms-prepare/backup_vaultslist.png)

7. 백업 자격 증명 모음을 클릭하면 **빠른 시작** 페이지로 이동하며, Azure 가상 컴퓨터의 백업 지침이 표시됩니다.

    ![대시보드 페이지의 가상 컴퓨터 백업 지침](./media/backup-azure-vms-prepare/vmbackup-instructions.png)



## 2\. 네트워크 연결

![네트워크 연결](./media/backup-azure-vms-prepare/step2.png)

백업 확장은 VM의 스냅샷을 관리하도록 Azure 저장소 끝점(HTTP URL)에 명령을 보내기 때문에 제대로 작동하려면 Azure 공용 IP에 연결되어야 합니다. 제대로 인터넷에 연결하지 않은 경우 VM의 이러한 HTTP 요청 시간이 초과되고 백업 작업이 실패합니다.

### NSG의 네트워크 제한 사항

배포가 (예를 들어 네트워크 보안 그룹을 통해) 액세스 제한을 준비한다면 Azure 백업 자격 증명에 백업 트래픽이 영향을 받지 않고 유지되도록 추가 단계를 수행해야 합니다.

백업 트래픽에 대한 경로를 제공하는 두 가지 방법이 있습니다.

1. [Azure 데이터 센터 IP 범위](http://www.microsoft.com/ko-KR/download/details.aspx?id=41653)를 허용 목록에 추가합니다.
2. HTTP 프록시를 배포하여 트래픽을 라우팅합니다.

관리 효율성, 세부적인 제어 및 비용 간의 절충입니다.

|옵션|장점|단점|
|------|----------|-------------|
|옵션 1: 허용 목록 IP 범위| 추가 비용 없음<br><br>NSG에서 액세스를 여는 경우 <i>Set-AzureNetworkSecurityRule</i> commandlet를 사용합니다. | 시간이 지남에 따라 영향을 받는 IP 범위가 변경되기 때문에 관리하기 복잡합니다.<br>저장소 뿐만 아니라 Azure 전체에 대한 액세스를 제공합니다.|
|옵션 2: HTTP 프록시| 허용되는 저장소 URL에 걸친 프록시에서 세부적인 제어<br>VM에 대한 인터넷 액세스의 단일 지점<br>Azure IP 주소 변경이 적용되지 않음| 프록시 소프트웨어를 사용하여 VM을 실행하기 위한 추가 비용입니다.|

### VM 백업에 HTTP 프록시 사용
VM을 백업할 때 스냅숏 관리 명령은 HTTPS API를 사용하여 백업 확장에서 Azure 저장소에 보내집니다. 프록시가 공용 인터넷에 액세스할 수 있도록 구성되기 때문에 이 트래픽은 프록시를 통해 확장에서 라우팅되어야 합니다.

>[AZURE.NOTE]사용해야 할 프록시 소프트웨어에 대한 권장 사항은 없습니다. 아래에서 설명한 구성과 호환되는 프록시를 선택하도록 합니다.

아래 예제에서 앱 VM은 공용 인터넷에 대한 모든 HTTP 트래픽에 프록시 VM을 사용하도록 구성해야 합니다. 프록시 VM은 VNET의 VM에서 들어오는 트래픽을 허용하도록 구성해야 합니다. 마지막으로 NSG(*NSG 잠금*이라고 명명됨)는 프록시 VM에서 아웃바운드 인터넷 트래픽을 허용하는 새 보안 규칙이 필요합니다.

![HTTP 프록시 배포 다이어그램을 사용하는 NSG](./media/backup-azure-vms-prepare/nsg-with-http-proxy.png)

**A) 나가는 네트워크 연결 허용.**

1. Windows 컴퓨터의 경우 승격된 명령 프롬프트에서 다음 명령을 실행합니다.

	```
	netsh winhttp set proxy http://<proxy IP>:<proxy port>
	```

	시스템 수준의 프록시 구성을 설정하고 나가는 HTTP/HTTPS 트래픽에 사용됩니다.

2. Linux 컴퓨터의 경우 ```/etc/environment``` 파일에 다음 줄을 추가합니다.

 	```
 	http_proxy=http://<proxy IP>:<proxy port>
 	```

	```/etc/waagent.conf``` 파일에 다음 줄을 추가합니다.

	```
HttpProxy.Host=<proxy IP>
HttpProxy.Port=<proxy port>
```

**B) 프록시 서버에서 들어오는 연결 허용**

1. 프록시 서버의 Windows 방화벽을 열고 마우스 오른쪽 단추로 *인바운드 규칙*을 클릭한다음 **새 규칙...**을 클릭합니다.

	![방화벽 열기](./media/backup-azure-vms-prepare/firewall-01.png)

	![새 규칙 만들기](./media/backup-azure-vms-prepare/firewall-02.png)
2. *새 인바운드 규칙 마법사*에서 *규칙 형식*에 **사용자 지정** 옵션을 선택하고 다음을 클릭합니다. *프로그램*을 선택하려는 화면에서 **모든 프로그램**을 선택하고 다음을 클릭합니다.

3. *프로토콜 및 포트* 화면에서 아래 테이블의 입력을 사용하고 다음을 클릭합니다.

	![새 규칙 만들기](./media/backup-azure-vms-prepare/firewall-03.png)

| 입력 필드 | 값 |
| --- | --- |
| 프로토콜 형식 | TCP |
| 로컬 포트 | 드롭다운에서 *특정 포트*를 선택하고 텍스트 상자에 구성된 ```<Proxy Port>```를 입력합니다. |
| 원격 포트 | 드롭다운에서 *모든 포트*를 선택합니다. |

마법사의 나머지 부분의 경우 끝까지 클릭하고 이 규칙에 이름을 지정합니다.

**C) NSG에 예외 규칙 추가**

Azure PowerShell 명령 프롬프트에서 다음 명령을 입력합니다.

```
Get-AzureNetworkSecurityGroup -Name "NSG-lockdown" |
Set-AzureNetworkSecurityRule -Name "allow-proxy " -Action Allow -Protocol TCP -Type Outbound -Priority 200 -SourceAddressPrefix "10.0.0.5/32" -SourcePortRange "*" -DestinationAddressPrefix Internet -DestinationPortRange "80-443"
```

이 명령은 NSG에 예외를 추가하여 10.0.0.5의 포트에서 포트 80(HTTP) 또는 443(HTTPS)의 인터넷 주소에 TCP 트래픽을 허용합니다. 공용 인터넷에서 특정 포트를 적중해야 할 경우에 또한 해당 포트를 ```-DestinationPortRange```에 추가해야 합니다.

*배포에 적절한 세부 정보를 사용하여 예제에서 이름을 대체하도록 합니다.*

## 3\. VM 에이전트

![VM 에이전트](./media/backup-azure-vms-prepare/step3.png)

Azure 가상 컴퓨터 백업을 시작하기 전에 Azure VM 에이전트가 가상 컴퓨터에 올바르게 설치되었는지 확인합니다. VM 에이전트는 가상 컴퓨터를 만들 때의 선택적 구성 요소이므로 가상 컴퓨터를 프로비전하기 전에 VM 에이전트에 대한 확인란이 선택되었는지 확인해야 합니다.

### 수동 설치 및 업데이트

Azure 갤러리에서 만든 VM에는 VM 에이전트가 이미 있습니다. 그러나 온-프레미스 데이터 센터에서 마이그레이션한 가상 컴퓨터에는 VM 에이전트가 설치되어 있지 않습니다. 이러한 VM의 경우 VM 에이전트를 명시적으로 설치해야 합니다. [기존 VM에 VM 에이전트 설치](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx)에 대해 자세히 알아보세요.

| **작업** | **Windows** | **Linux** |
| --- | --- | --- |
| VM 에이전트 설치 | <li>[에이전트 MSI](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)를 다운로드하여 설치합니다. 설치를 완료하려면 관리자 권한이 있어야 합니다. <li>[VM 속성을 업데이트](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx)하여 에이전트가 설치되었다고 표시합니다. | <li> github에서 최신 [Linux 에이전트](https://github.com/Azure/WALinuxAgent)를 설치합니다. 설치를 완료하려면 관리자 권한이 있어야 합니다. <li> [VM 속성을 업데이트](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx)하여 에이전트가 설치되었다고 표시합니다. |
| VM 에이전트 업데이트 | VM 에이전트 업데이트는 [VM 에이전트 이진](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)을 다시 설치하면 되는 간단한 작업입니다. <br><br>VM 에이전트를 업데이트하는 동안 실행 중인 백업 작업이 없도록 합니다. | [Linux VM 에이전트 업데이트](../virtual-machines-linux-update-agent.md)의 지침을 따르세요. <br><br>VM 에이전트를 업데이트하는 동안 실행 중인 백업 작업이 없도록 합니다. |
| VM 에이전트 설치의 유효성을 검사 | <li>탐색 하는 *C:\\WindowsAzure\\Packages* Azure VM의 폴더입니다. <li>WaAppAgent.exe 파일을 찾습니다.<li> 파일을 마우스 오른쪽 단추로 클릭하고 **속성**으로 이동한 다음 **세부 정보** 탭을 선택합니다. 제품 버전 필드가 2.6.1198.718 이상이어야 합니다. | - |


[VM 에이전트](https://go.microsoft.com/fwLink/?LinkID=390493&clcid=0x409) 및 [설치 방법](http://azure.microsoft.com/blog/2014/04/15/vm-agent-and-extensions-part-2/)에 대해 알아보세요.

### 백업 확장

가상 컴퓨터를 백업하기 위해 Azure 백업 서비스는 VM 에이전트 확장을 설치합니다. Azure 백업 서비스는 추가 사용자 개입 없이 원활하게 백업 확장을 업그레이드 및 패치합니다.

백업 확장은 VM을 실행하는 경우 설치됩니다. 또한 실행 중인 VM은 응용 프로그램 일치 복구 지점을 확보하는 가장 큰 기회를 제공합니다. 그러나 Azure 백업 서비스는 VM이 꺼져 확장을 설치할 수 없더라도(즉, 오프라인 VM) VM을 계속 백업합니다. 이 경우 복구 지점은 위에서 설명한 대로 *크래시 일관성 상태*가 됩니다.


## 제한 사항

- Azure 리소스 관리자 기반(즉, IaaS V2) 가상 컴퓨터의 백업은 지원되지 않습니다.
- 16개 이상의 데이터 디스크가 있는 가상 컴퓨터의 백업은 지원되지 않습니다.
- 프리미엄 저장소를 사용하는 가상 컴퓨터의 백업은 지원되지 않습니다.
- 여러 예약된 IP가 있는 가상 컴퓨터의 백업은 지원되지 않습니다.
- 예약된 IP가 있고 정의된 끝점이 없는 가상 컴퓨터의 백업은 지원되지 않습니다.
- 여러 NIC를 사용하는 가상 컴퓨터의 백업은 지원되지 않습니다.
- 부하 분산 구성(내부 및 인터넷 연결)에 포함된 가상 컴퓨터의 백업은 지원되지 않습니다.
- 복원하는 동안 기존 가상 컴퓨터의 교체는 지원되지 않습니다. 먼저 기존 가상 컴퓨터와 관련 디스크를 모두 삭제한 다음 백업에서 데이터를 복원합니다.
- 지역 간 백업 및 복원은 지원되지 않습니다.
- Azure 백업 서비스를 사용한 가상 컴퓨터 백업은 Azure의 모든 공용 지역에서 지원됩니다. 다음은 지원되는 지역의 [검사 목록](http://azure.microsoft.com/regions/#services)입니다. 찾는 지역이 현재 지원되지 않는 경우, 자격 증명 모음을 만드는 동안 드롭다운 목록에 표시되지 않습니다.
- Azure 백업 서비스를 사용하는 가상 컴퓨터 백업은 선택한 운영 체제 버전에 대해서만 지원됩니다.
  - **Linux**: Azure 인증 배포 목록은 [여기](../virtual-machines-linux-endorsed-distributions.md)서 확인할 수 있습니다. 가상 컴퓨터에서 VM 에이전트를 사용할 수 있는 한 기타 Bring-Your-Own-Linux 배포도 작동합니다.
  - **Windows Server**: Windows Server 2008 R2 이전 버전은 지원되지 않습니다.
- 다중 DC 구성의 일부인 도메인 컨트롤러 VM 복원은 PowerShell을 통해서만 지원됩니다. [다중 DC 도메인 컨트롤러 복원](backup-azure-restore-vms.md#restoring-domain-controller-vms)에 대해 자세히 알아보세요.

## 질문이 있으십니까?
질문이 있거나 포함되었으면 하는 기능이 있는 경우 [의견을 보내 주세요](http://aka.ms/azurebackup_feedback).

## 다음 단계

- [VM 백업 인프라 계획](backup-azure-vms-introduction.md)
- [가상 컴퓨터 설정](backup-azure-vms.md)
- [가상 컴퓨터 백업 관리](backup-azure-manage-vms.md)

<!---HONumber=Nov15_HO3-->