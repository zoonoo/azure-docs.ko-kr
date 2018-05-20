---
title: Azure Virtual Machines를 백업하기 위한 환경 준비 | Microsoft Docs
description: 환경이 Azure의 가상 머신을 백업할 준비가 되었는지 확인합니다.
services: backup
documentationcenter: ''
author: markgalioto
manager: carmonn
editor: ''
keywords: 백업; 백업;
ms.assetid: 238ab93b-8acc-4262-81b7-ce930f76a662
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 9/10/2017
ms.author: cwatson
ms.openlocfilehash: 03bb4c3e68643ded686c20b7ba0d5d079a9d4057
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2018
---
# <a name="prepare-your-environment-to-back-up-azure-virtual-machines"></a>Azure 가상 머신을 백업하기 위한 환경 준비
> [!div class="op_single_selector"]
> * [Resource Manager 모델](backup-azure-arm-vms-prepare.md)
> * [클래식 모델](backup-azure-vms-prepare.md)
>
>

Azure VM(가상 머신)을 백업하려면 세 가지 조건을 충족해야 합니다.

* *VM과 동일한 지역*에서 백업 자격 증명 모음을 만들거나 기존 백업 자격 증명 모음을 식별해야 합니다.
* Azure 공용 인터넷 주소와 Azure 저장소 끝점 간의 네트워크 연결을 설정합니다.
* VM에 VM 에이전트를 설치합니다.

사용자 환경이 이러한 조건을 이미 갖춘 경우 [VM 문서 백업](backup-azure-vms.md)을 진행합니다. 그렇지 않으면 이 문서에 따라 Azure VM을 백업하도록 환경을 준비하는 단계를 수행합니다.

##<a name="supported-operating-system-for-backup"></a>지원되는 백업용 운영 체제
 * **Linux**: Azure Backup은 Core OS Linux를 제외한 [Azure 인증 배포 목록](../virtual-machines/linux/endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 을 지원합니다. _가상 머신에서 VM 에이전트를 사용할 수 있고 Python에 대한 지원이 지속하는 한 기타 Bring-Your-Own-Linux 배포도 작동합니다. 그러나 이러한 배포판을 백업에 대해서는 보증하지 않습니다._
 * **Windows Server**: Windows Server 2008 R2 이전 버전은 지원되지 않습니다.


## <a name="limitations-when-backing-up-and-restoring-a-vm"></a>VM 백업 및 복원 시의 제한 사항
> [!NOTE]
> Azure에는 리소스를 만들고 작업하기 위한 두 가지 배포 모델인 [리소스 관리자와 클래식](../azure-resource-manager/resource-manager-deployment-model.md)모델이 있습니다. 다음 목록에서는 클래식 모델에서 배포할 때의 제한 사항을 제공합니다.
>
>

* 16개 이상의 데이터 디스크가 있는 가상 머신의 백업은 지원되지 않습니다.
* 예약된 IP 주소가 있고 정의된 끝점이 없는 가상 머신의 백업은 지원되지 않습니다.
* Backup 데이터는 VM에 연결된 네트워크 탑재된 드라이브를 포함하지 않습니다.
* 복원하는 동안 기존 가상 머신의 교체는 지원되지 않습니다. 먼저 기존 가상 머신과 관련 디스크를 모두 삭제한 다음 백업에서 데이터를 복원합니다.
* 지역 간 백업 및 복원은 지원되지 않습니다.
* Azure Backup 서비스를 사용한 가상 머신 백업은 Azure의 모든 공용 지역에서 지원됩니다(지원되는 지역은 [검사 목록](https://azure.microsoft.com/regions/#services) 참조). 찾는 지역이 현재 지원되지 않는 경우, 자격 증명 모음을 만드는 동안 드롭다운 목록에 표시되지 않습니다.
* Azure Backup 서비스를 사용하는 가상 머신 백업은 선택한 운영 체제 버전에 대해서만 지원됩니다.
* 다중 DC 구성의 일부인 도메인 컨트롤러(DC) VM 복원은 PowerShell을 통해서만 지원됩니다. [다중 DC 도메인 컨트롤러 복원](backup-azure-restore-vms.md#restoring-domain-controller-vms)에 대해 자세히 알아보세요.
* 다음과 같은 특수 네트워크 구성을 포함하는 가상 머신 복원은 PowerShell 통해서만 지원됩니다. UI에서 복원 워크플로를 사용하여 만든 VM은 복원 작업이 완료된 후 이러한 네트워크 구성을 갖지 않습니다. 자세한 내용은 [특수 네트워크 구성을 가진 VM 복원](backup-azure-restore-vms.md#restoring-vms-with-special-network-configurations)을 참조하세요.
  * 부하 분산 장치 구성에서의 가상 머신(내부 및 외부)
  * 다중의 예약된 IP 주소가 있는 가상 머신
  * 다중 네트워크 어댑터가 있는 가상 머신

## <a name="create-a-backup-vault-for-a-vm"></a>VM에 대한 백업 자격 증명 모음 만들기
백업 자격 증명 모음은 모든 백업과 시간에 따라 생성된 복구 지점을 저장하는 엔터티입니다. 백업 자격 증명 모음에는 백업 중인 가상 머신에 적용할 백업 정책도 포함됩니다.

> [!IMPORTANT]
> 2017년 3월부터는 Backup 자격 증명 모음을 만드는 데 더 이상 클래식 포털을 사용할 수 없습니다. 기존 Backup 자격 증명 모음은 계속 지원되고 [Azure PowerShell을 사용하여 Backup 자격 증명 모음을 만들](./backup-client-automation-classic.md#create-a-backup-vault) 수 있습니다. 하지만 향후 향상되는 기능이 Recovery Services 자격 증명 모음에만 적용되므로 Microsoft에서는 모든 배포에 Recovery Services 자격 증명 모음을 만들도록 권장합니다.


이 그림은 여러 Azure Backup 엔터티 간의 관계를 보여 줍니다. ![Azure Backup 엔터티 및 관계](./media/backup-azure-vms-prepare/vault-policy-vm.png)



## <a name="network-connectivity"></a>네트워크 연결
VM 스냅숏을 관리하려면, 백업 확장에 Azure 공용 IP 주소에 대한 연결이 필요합니다. 올바른 인터넷 연결이 없으면, 가상 머신의 HTTP 요청 시간이 초과되고 백업 작업이 실패합니다. 배포에 액세스 제한이 있다면(예: 네트워크 보안 그룹(NSG)을 통해), 백업 트래픽에 대해 명확한 경로를 제공하기 위해 이 옵션 중 하나를 선택합니다.

* [Azure 데이터 센터 IP 범위 허용 목록](http://www.microsoft.com/en-us/download/details.aspx?id=41653) - IP 주소 허용 목록을 만드는 방법에 대한 지침은 이 문서를 참조하세요.
* 트래픽 라우팅을 위해 HTTP 프록시 서버를 배포합니다.

사용할 옵션을 결정할 때는, 관리 효율성, 세부적인 제어 및 비용 사이에 균형을 유지합니다.

| 옵션 | 장점 | 단점 |
| --- | --- | --- |
| 허용 목록 IP 범위 |추가 비용 없음<br><br>NSG에서 액세스를 여는 경우 <i>Set-AzureNetworkSecurityRule</i> cmdlet을 사용합니다. |시간이 지남에 따라 영향을 받는 IP 범위가 변경되기 때문에 관리하기가 복잡합니다.<br><br>저장소 뿐만 아니라 Azure 전체에 대한 액세스를 제공합니다. |
| HTTP 프록시 |허용되는 저장소 URL에 걸친 프록시에서 세부적인 제어 프록시에서 세분화된 제어를 설정하려면 https://\*.blob.core.windows.net/\* URL 패턴을 허용 목록에 추가해야 합니다. VM에서 사용하는 저장소 계정만 허용 목록에 추가하려면 https://\<storageAccount\>.blob.core.windows.net/\* URL 패턴을 허용 목록에 추가해야 합니다. <br>VM에 대한 인터넷 액세스의 단일 지점<br>Azure IP 주소 변경이 적용되지 않음 |프록시 소프트웨어를 사용하여 VM을 실행하기 위한 추가 비용입니다. |

### <a name="whitelist-the-azure-datacenter-ip-ranges"></a>Azure 데이터 센터 IP 범위 허용 목록
Azure 데이터 센터 IP 범위의 허용 목록을 만들려면, [Azure 웹 사이트](http://www.microsoft.com/en-us/download/details.aspx?id=41653)에서 IP 범위에 대한 자세한 내용과 지침을 참조하세요.

### <a name="using-an-http-proxy-for-vm-backups"></a>VM 백업에 HTTP 프록시 사용
VM을 백업할 때, VM의 백업 확장이 HTTPS API를 사용하여 Azure Storage에 스냅숏 관리 명령을 보냅니다. 공용 인터넷에 액세스하도록 구성된 유일한 구성 요소이므로, HTTP 프록시를 통해 백업 확장 트래픽을 라우팅합니다.

> [!NOTE]
> 사용해야 할 프록시 소프트웨어에 대한 권장 사항은 없습니다. 아웃바운드 연결이 있고 아래의 구성 단계와 호환되는 프록시를 선택하도록 합니다. 타사 소프트웨어는 프록시 설정을 수정하지 않습니다.
>
>

아래 예제 이미지는 HTTP 프록시를 사용하는 데 필요한 세 가지 구성 단계를 보여줍니다.

* 앱 VM은 프록시 VM을 통해 공용 인터넷으로 향하는 모든 HTTP 트래픽을 라우팅합니다.
* 프록시 VM은 가상 네트워크의 VM에서 들어오는 트래픽을 허용합니다.
* NSF-lockdown이라는 이름의 네트워크 보안 그룹(NSG)에 프록시 VM의 아웃바운드 인터넷 트래픽을 허용하는 보안 규칙이 필요합니다.

![HTTP 프록시 배포 다이어그램을 사용하는 NSG](./media/backup-azure-vms-prepare/nsg-with-http-proxy.png)

공용 인터넷 통신에 HTTP 프록시를 사용하려면, 다음 단계를 수행합니다.

#### <a name="step-1-configure-outgoing-network-connections"></a>1단계. 나가는 네트워크 연결 구성
###### <a name="for-windows-machines"></a>Windows 컴퓨터의 경우
로컬 시스템 계정에 대한 프록시 서버 구성을 설정합니다.

1. [PsExec](https://technet.microsoft.com/sysinternals/bb897553)
2. 관리자 권한 프롬프트에서 다음 명령을 실행합니다.

     ```
     psexec -i -s "c:\Program Files\Internet Explorer\iexplore.exe"
     ```
     Internet Explorer 창이 열립니다.
3. 도구 -> 인터넷 옵션 -> 연결 -> LAN 설정으로 이동합니다.
4. 시스템 계정에 대한 프록시 설정을 확인합니다. 프록시 IP 및 포트를 설정합니다.
5. Internet Explorer를 닫습니다.

시스템 수준의 프록시 구성을 설정하고 나가는 HTTP/HTTPS 트래픽에 사용됩니다.

현재 사용자 계정(로컬 시스템 계정이 아닌)으로 프록시 서버를 설정한 경우에는, 다음 스크립트를 사용하여 SYSTEMACCOUNT에 적용합니다.

```
   $obj = Get-ItemProperty -Path Registry::”HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections"
   Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections" -Name DefaultConnectionSettings -Value $obj.DefaultConnectionSettings
   Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections" -Name SavedLegacySettings -Value $obj.SavedLegacySettings
   $obj = Get-ItemProperty -Path Registry::”HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings"
   Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings" -Name ProxyEnable -Value $obj.ProxyEnable
   Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings" -Name Proxyserver -Value $obj.Proxyserver
```

> [!NOTE]
> 프록시 서버 로그에 "(407)프록시 인증 필요"가 발견되면, 인증이 제대로 설정되었는지 확인합니다.
>
>

###### <a name="for-linux-machines"></a>Linux 컴퓨터의 경우
다음 줄을 ```/etc/environment``` 파일에 추가합니다.

```
http_proxy=http://<proxy IP>:<proxy port>
```

```/etc/waagent.conf``` 파일에 다음 줄을 추가합니다.

```
HttpProxy.Host=<proxy IP>
HttpProxy.Port=<proxy port>
```

#### <a name="step-2-allow-incoming-connections-on-the-proxy-server"></a>2단계. 프록시 서버에서 들어오는 연결을 허용합니다.
1. 프록시 서버에서 Windows 방화벽을 엽니다. 방화벽에 액세스하는 가장 쉬운 방법은 고급 보안이 포함된 Windows 방화벽을 검색하는 것입니다.

    ![방화벽 열기](./media/backup-azure-vms-prepare/firewall-01.png)
2. Windows 방화벽 대화 상자에서 마우스 오른쪽 단추로 **인바운드 규칙**을 클릭한 다음 **새 규칙...** 을 클릭합니다.

    ![새 규칙 만들기](./media/backup-azure-vms-prepare/firewall-02.png)
3. **새 인바운드 규칙 마법사**에서 **규칙 형식**에 **사용자 지정** 옵션을 선택하고 **다음**을 클릭합니다.
4. **프로그램**을 선택하려는 페이지에서 **모든 프로그램**을 선택하고 **다음**을 클릭합니다.
5. **프로토콜 및 포트** 페이지에서 다음 정보를 입력하고 **다음**을 클릭합니다.

    ![새 규칙 만들기](./media/backup-azure-vms-prepare/firewall-03.png)

   * *프로토콜 유형*에서 *TCP*를 선택합니다.
   * *로컬 포트*에서 *특정 포트*를 선택하고 아래의 필드에서 구성되어 있는 ```<Proxy Port>```를 지정합니다.
   * *원격 포트*에서 *모든 포트*를 선택합니다.

     마법사의 나머지 부분의 경우 끝까지 클릭하고 이 규칙에 이름을 지정합니다.

#### <a name="step-3-add-an-exception-rule-to-the-nsg"></a>3단계. NSG에 예외 규칙 추가
Azure PowerShell 명령 프롬프트에서 다음 명령을 입력합니다.

다음 명령은 NSG에 예외를 추가합니다. 이 예외는 10.0.0.5의 모든 포트에서 오는 TCP 트래픽을 포트 80(HTTP) 또는 443(HTTPS)의 모든 인터넷 주소에 허용합니다. 공용 인터넷에 특정 포트가 필요하면, 해당 포트를 ```-DestinationPortRange``` 에도 추가해야 합니다.

```
Get-AzureNetworkSecurityGroup -Name "NSG-lockdown" |
Set-AzureNetworkSecurityRule -Name "allow-proxy " -Action Allow -Protocol TCP -Type Outbound -Priority 200 -SourceAddressPrefix "10.0.0.5/32" -SourcePortRange "*" -DestinationAddressPrefix Internet -DestinationPortRange "80-443"
```

*배포에 적절한 세부 정보를 사용하여 예제에서 이름을 대체하도록 합니다.*

## <a name="vm-agent"></a>VM 에이전트
Azure 가상 컴퓨터 백업을 시작하기 전에 Azure VM 에이전트가 가상 컴퓨터에 올바르게 설치되었는지 확인합니다. VM 에이전트는 가상 머신을 만들 때의 선택적 구성 요소이므로 가상 머신을 프로비전하기 전에 VM 에이전트에 대한 확인란이 선택되었는지 확인해야 합니다.

### <a name="manual-installation-and-update"></a>수동 설치 및 업데이트
Azure 갤러리에서 만든 VM에는 VM 에이전트가 이미 있습니다. 그러나 온-프레미스 데이터 센터에서 마이그레이션한 가상 머신에는 VM 에이전트가 설치되어 있지 않습니다. 이러한 VM의 경우 VM 에이전트를 명시적으로 설치해야 합니다. 

| **작업** | **Windows** | **Linux** |
| --- | --- | --- |
| VM 에이전트 설치 |[에이전트 MSI](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)를 다운로드하여 설치합니다. 설치를 완료하려면 관리자 권한이 있어야 합니다.<li>[VM 속성을 업데이트](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) 하여 에이전트가 설치되었다고 표시합니다. |<li> 최신 [Linux 에이전트](../virtual-machines/extensions/agent-linux.md)를 설치합니다. 설치를 완료하려면 관리자 권한이 있어야 합니다. 리포지토리에서 배포 에이전트를 설치하는 것이 좋습니다. GitHub에서 직접 Linux VM 에이전트를 설치하는 것은 **좋지 않습니다**.  |
| VM 에이전트 업데이트 |VM 에이전트 업데이트는 [VM 에이전트 이진](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)을 다시 설치하면 되는 간단한 작업입니다. <br>VM 에이전트를 업데이트하는 동안 실행 중인 백업 작업이 없도록 합니다. |[Linux VM 에이전트 업데이트](../virtual-machines/linux/update-agent.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)의 지침을 따르세요. 리포지토리에서 배포 에이전트를 업데이트하는 것이 좋습니다. GitHub에서 직접 Linux VM 에이전트를 업데이트하는 것은 **좋지 않습니다**.<br>VM 에이전트를 업데이트하는 동안 실행 중인 백업 작업이 없도록 합니다. |
| VM 에이전트 설치 유효성 검사 |<li>Azure VM에서 *C:\WindowsAzure\Packages* 폴더로 이동합니다. <li>WaAppAgent.exe 파일을 찾습니다.<li> 파일을 마우스 오른쪽 단추로 클릭하고 **속성**으로 이동한 다음 **세부 정보** 탭을 선택합니다. 제품 버전 필드가 2.6.1198.718 이상이어야 합니다. |해당 없음 |

[VM 에이전트](https://go.microsoft.com/fwLink/?LinkID=390493&clcid=0x409) 및 [설치 방법](https://azure.microsoft.com/blog/2014/04/15/vm-agent-and-extensions-part-2/)에 대해 알아보세요.

### <a name="backup-extension"></a>Backup 확장
가상 컴퓨터를 백업하기 위해 Azure Backup 서비스는 VM 에이전트 확장을 설치합니다. Azure Backup 서비스는 추가 사용자 개입 없이 원활하게 백업 확장을 업그레이드 및 패치합니다.

백업 확장은 VM을 실행하는 경우 설치됩니다. 또한 실행 중인 VM은 응용 프로그램 일치 복구 지점을 확보하는 가장 큰 기회를 제공합니다. 그러나 Azure Backup 서비스는 VM이 꺼져 확장을 설치할 수 없더라도(즉, 오프라인 VM) VM을 계속 백업합니다. 이 경우 복구 지점은 위에서 설명한 대로 *크래시 일관성 상태* 가 됩니다.

## <a name="questions"></a>질문이 있으십니까?
질문이 있거나 포함되었으면 하는 기능이 있는 경우 [의견을 보내 주세요](http://aka.ms/azurebackup_feedback).

## <a name="next-steps"></a>다음 단계
VM을 백업하기 위한 환경을 준비했으므로 이제 백업을 만들어야 합니다. 계획 문서에서는 VM 백업에 대한 보다 자세한 정보를 제공합니다.

* [가상 머신 설정](backup-azure-vms.md)
* [VM 백업 인프라 계획](backup-azure-vms-introduction.md)
* [가상 컴퓨터 백업 관리](backup-azure-manage-vms.md)
