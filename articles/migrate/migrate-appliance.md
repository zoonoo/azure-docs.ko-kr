---
title: Azure Migrate 어플라이언스
description: 서버 평가 및 마이그레이션에 사용되는 Azure 마이그레이션 어플라이언스에 대한 개요를 제공합니다.
ms.topic: conceptual
ms.date: 03/23/2020
ms.openlocfilehash: bccf4738d46b65f2d149eafc8e69591141d7d073
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437584"
---
# <a name="azure-migrate-appliance"></a>Azure Migrate 어플라이언스

이 문서에서는 Azure 마이그레이션 어플라이언스에 대한 필수 구성 프로그램 및 지원 요구 사항을 요약합니다. 

## <a name="deployment-scenarios"></a>배포 시나리오

Azure 마이그레이션 어플라이언스는 다음 시나리오에서 사용됩니다.

**시나리오** | **도구** | **사용 대상** 
--- | --- | ---
**VM웨어 VM 평가** | Azure 마이그레이션:서버 평가 | VM웨어 VM 자세히 알아보기<br/><br/> 기계 앱 및 종속성 검색<br/><br/> 평가를 위해 컴퓨터 메타데이터 및 성능 메타데이터를 수집합니다.
**VMware VM 에이전트 없는 마이그레이션** | Azure 마이그레이션:서버 마이그레이션 | VM웨어 VM 자세히 알아보기 <br/><br/> 에이전트 없는 마이그레이션을 통해 VMware VM을 복제합니다.
**하이퍼 V VM 평가** | Azure 마이그레이션:서버 평가 | 하이퍼 VM 살펴보기<br/><br/> 평가를 위해 컴퓨터 메타데이터 및 성능 메타데이터를 수집합니다.
**물리적 기계 평가** |  Azure 마이그레이션:서버 평가 |  물리적 서버(또는 물리적 서버로 취급하는 VM)를 검색합니다.<br/><br/> 평가를 위해 컴퓨터 메타데이터 및 성능 메타데이터를 수집합니다.

## <a name="appliance---vmware"></a>어플라이언스 - VMware 

다음 표에는 VMware에 대한 Azure 마이그레이션 어플라이언스 요구 사항이 요약됩니다.

**요구 사항** | **Vm 웨어** 
--- | ---
**어플라이언스 부품** | 어플라이언스에는 다음과 같은 구성 요소가 있습니다.<br/><br/> - **관리 앱**: 어플라이언스 배포 중에 사용자 입력을 위한 웹 앱입니다. Azure로 마이그레이션할 컴퓨터를 평가할 때 사용됩니다.<br/> - **검색 에이전트**: 에이전트는 컴퓨터 구성 데이터를 수집합니다. Azure로 마이그레이션할 컴퓨터를 평가할 때 사용됩니다.<br/>- **평가 에이전트**: 에이전트가 성능 데이터를 수집합니다. Azure로 마이그레이션할 컴퓨터를 평가할 때 사용됩니다.<br/>- **자동 업데이트 서비스**: 어플라이언스 구성 요소를 업데이트합니다(24시간마다 실행).<br/>- **DRA 에이전트**: VM 복제를 오케스트레이션하고 복제된 컴퓨터와 Azure 간의 통신을 조정합니다. 에이전트 없는 마이그레이션을 사용하여 VMware VM을 Azure로 복제할 때만 사용됩니다.<br/>- **게이트웨이**: 복제된 데이터를 Azure로 보냅니다. 에이전트 없는 마이그레이션을 사용하여 VMware VM을 Azure로 복제할 때만 사용됩니다.
**지원되는 배포** | OVA 템플릿을 사용하여 VM웨어 VM으로 배포합니다.<br/><br/> PowerShell 설치 스크립트를 사용하여 VMware VM 또는 물리적 컴퓨터로 배포합니다.
**프로젝트 지원** |  어플라이언스는 단일 프로젝트와 연결할 수 있습니다. <br/> 어플라이언스 의 수는 단일 프로젝트와 연결할 수 있습니다.<br/> 
**검색 제한** | 어플라이언스는 vCenter 서버에서 최대 10,000개의 VM웨어 VM을 검색할 수 있습니다.<br/> 어플라이언스는 단일 vCenter 서버에 연결할 수 있습니다.
**OVA 템플릿** | 포털 또는 에서 https://aka.ms/migrate/appliance/vmware다운로드합니다.<br/><br/> 다운로드 크기는 11.2 GB입니다.<br/><br/> 다운로드한 어플라이언스 템플릿에는 180일 동안 유효한 Windows Server 2016 평가 라이센스가 함께 제공됩니다. 평가 기간이 만료에 가까워지면 새 어플라이언스를 다운로드하여 배포하거나 어플라이언스 VM의 운영 체제 라이센스를 활성화하는 것이 좋습니다.
**파워쉘 스크립트** | 스크립트 [다운로드](https://go.microsoft.com/fwlink/?linkid=2105112).<br/><br/> 
**소프트웨어/하드웨어** |  어플라이언스는 Windows Server 2016, 32GB RAM, 8vCPU, 약 80GB의 디스크 저장소 및 외부 가상 스위치가 있는 컴퓨터에서 실행되어야 합니다.<br/> 어플라이언스는 직접 또는 프록시를 통해 인터넷에 액세스해야 합니다.<br/><br/> VMware VM에서 어플라이언스를 실행하는 경우 요구 사항을 충족하는 VM을 할당하려면 vCenter Server에 충분한 리소스가 필요합니다.<br/><br/> 실제 컴퓨터에서 어플라이언스를 실행하는 경우 Windows Server 2016을 실행하고 하드웨어 요구 사항을 충족하는지 확인합니다. 
**VMware 요구 사항** | 어플라이언스를 VM웨어 VM으로 배포하는 경우 5.5 이상 버전을 실행하는 ESXi 호스트에 배포해야 합니다.<br/><br/> v센터 서버가 5.5, 6.0, 6.5 또는 6.7을 실행합니다.
**VDDK(에이전트 없는 마이그레이션)** | 어플라이언스를 VM웨어 VM으로 배포하고 에이전트 없는 마이그레이션을 실행하는 경우 VMware vSphere VDDK를 어플라이언스 VM에 설치해야 합니다.
**해시 값 OVA** | OVA 템플릿 해시 값을 [확인합니다.](tutorial-assess-vmware.md#verify-security)
**해시 값-PowerShell 스크립트** | PowerShell 스크립트 해시 값을 [확인합니다.](deploy-appliance-script.md#verify-file-security)




## <a name="appliance---hyper-v"></a>어플라이언스 - 하이퍼-V

**요구 사항** | **Hyper-V** 
--- | ---
**어플라이언스 부품** | 어플라이언스에는 다음과 같은 구성 요소가 있습니다.<br/><br/>- **관리 앱**: 어플라이언스 배포 중에 사용자 입력을 위한 웹 앱입니다. Azure로 마이그레이션할 컴퓨터를 평가할 때 사용됩니다.<br/> - **검색 에이전트**: 에이전트는 컴퓨터 구성 데이터를 수집합니다. Azure로 마이그레이션할 컴퓨터를 평가할 때 사용됩니다.<br/>- **평가 에이전트**: 에이전트가 성능 데이터를 수집합니다. Azure로 마이그레이션할 컴퓨터를 평가할 때 사용됩니다.<br/>- **자동 업데이트 서비스**: 어플라이언스 구성 요소를 업데이트합니다(24시간마다 실행).
**지원되는 배포** | VHD 템플릿을 사용하여 하이퍼 VVM으로 배포합니다.<br/><br/> PowerShell 설치 스크립트를 사용하여 Hyper-V VM 또는 물리적 컴퓨터로 배포합니다.
**프로젝트 지원** |  어플라이언스는 단일 프로젝트와 연결할 수 있습니다. <br/> 어플라이언스 의 수는 단일 프로젝트와 연결할 수 있습니다.<br/> 
**검색 제한** | 어플라이언스는 최대 5,000개의 Hyper-V VM을 검색할 수 있습니다.<br/> 어플라이언스는 최대 300개의 Hyper-V 호스트에 연결할 수 있습니다.
**VHD 템플릿** | VHD를 포함한 압축 폴더. 포털 또는 에서 https://aka.ms/migrate/appliance/hyperv다운로드합니다.<br/><br/> 다운로드 크기는 10GB입니다.<br/><br/> 다운로드한 어플라이언스 템플릿에는 180일 동안 유효한 Windows Server 2016 평가 라이센스가 함께 제공됩니다. 평가 기간이 만료에 가까워지면 새 어플라이언스를 다운로드하여 배포하거나 어플라이언스 VM의 운영 체제 라이센스를 활성화하는 것이 좋습니다.
**파워쉘 스크립트** | 스크립트 [다운로드](https://go.microsoft.com/fwlink/?linkid=2105112).<br/><br/> 
**소프트웨어/하드웨어***   |  어플라이언스는 Windows Server 2016, 32GB RAM, 8vCPU, 약 80GB의 디스크 저장소 및 외부 가상 스위치가 있는 컴퓨터에서 실행되어야 합니다.<br/> 어플라이언스는 정적 또는 동적 IP 주소가 필요하며 직접 또는 프록시를 통해 인터넷에 액세스해야 합니다.<br/><br/> 어플라이언스를 Hyper-V VM으로 실행하는 경우 하이퍼 V 호스트에 16GB RAM, 8vCPU, 약 80GB의 저장 공간 및 어플라이언스 VM에 대한 외부 스위치를 할당하는 데 충분한 리소스가 필요합니다.<br/><br/> 실제 컴퓨터에서 어플라이언스를 실행하는 경우 Windows Server 2016을 실행하고 하드웨어 요구 사항을 충족하는지 확인합니다. 
**하이퍼 V 요구 사항** | VHD 템플릿을 함께 어플라이언스를 배포하는 경우 Azure 마이그레이션에서 제공하는 어플라이언스 VM은 Hyper-V VM 버전 5.0입니다.<br/><br/> 하이퍼-V 호스트는 Windows Server 2012 R2 이상에서 실행 중이어야 합니다. 
**해시 값 VHD** | VHD 템플릿 해시 값을 [확인합니다.](tutorial-assess-hyper-v.md#verify-security)
**해시 값-PowerShell 스크립트** | PowerShell 스크립트 해시 값을 [확인합니다.](deploy-appliance-script.md#verify-file-security)


## <a name="appliance---physical"></a>어플라이언스 - 물리적

**요구 사항** | **물리적** 
--- | ---
**어플라이언스 부품** | 어플라이언스에는 다음과 같은 구성 요소가 있습니다. <br/><br/> - **관리 앱**: 어플라이언스 배포 중에 사용자 입력을 위한 웹 앱입니다. Azure로 마이그레이션할 컴퓨터를 평가할 때 사용됩니다.<br/> - **검색 에이전트**: 에이전트는 컴퓨터 구성 데이터를 수집합니다. Azure로 마이그레이션할 컴퓨터를 평가할 때 사용됩니다.<br/>- **평가 에이전트**: 에이전트가 성능 데이터를 수집합니다. Azure로 마이그레이션할 컴퓨터를 평가할 때 사용됩니다.<br/>- **자동 업데이트 서비스**: 어플라이언스 구성 요소를 업데이트합니다(24시간마다 실행).
**지원되는 배포** | PowerShell 설치 스크립트를 사용하여 전용 물리적 컴퓨터 또는 VM으로 배포합니다.
**프로젝트 지원** |  어플라이언스는 단일 프로젝트와 연결할 수 있습니다. <br/> 어플라이언스 의 수는 단일 프로젝트와 연결할 수 있습니다.<br/> 
**검색 제한** | 어플라이언스는 최대 250개의 물리적 서버를 검색할 수 있습니다.
**파워쉘 스크립트** | 포털의 압축된 폴더에서 스크립트(AzureMigrateInstaller.ps1)를 다운로드합니다. [자세히 알아보기](tutorial-assess-physical.md#set-up-the-appliance). 또는 [직접 다운로드합니다.](https://go.microsoft.com/fwlink/?linkid=2105112)<br/><br/> 다운로드 크기는 59.7 MB입니다.
**소프트웨어/하드웨어** |  어플라이언스는 Windows Server 2016, 32GB RAM, 8vCPU, 약 80GB의 디스크 저장소 및 외부 가상 스위치가 있는 컴퓨터에서 실행되어야 합니다.<br/> 어플라이언스는 정적 또는 동적 IP 주소가 필요하며 직접 또는 프록시를 통해 인터넷에 액세스해야 합니다.<br/><br/> 실제 컴퓨터에서 어플라이언스를 실행하는 경우 Windows Server 2016을 실행하고 하드웨어 요구 사항을 충족하는지 확인합니다. 
**해시 값** | PowerShell 스크립트 해시 값을 [확인합니다.](deploy-appliance-script.md#verify-file-security)

## <a name="url-access"></a>URL 액세스

Azure 마이그레이션 어플라이언스는 인터넷에 연결해야 합니다.

- 어플라이언스를 배포할 때 Azure Migrate는 아래 표에 요약된 URL에 대한 연결 검사를 수행합니다.
- URL 기반 프록시를 사용하여 인터넷에 연결하는 경우 이러한 URL에 대한 액세스를 허용해야 하며, URL을 조회하는 동안 받은 CNAME 레코드를 프록시에서 확인할 수 있어야 합니다.

**URL** | **세부 정보**  
--- | --- |
*.portal.azure.com  | Azure Portal로 이동합니다.
*.windows.net <br/> *.msftauth.net <br/> *.msauth.net <br/> *.microsoft.com <br/> *.live.com | Azure 구독에 로그인합니다.
*.microsoftonline.com <br/> *.microsoftonline-p.com | 어플라이언스가 Azure 마이그레이션과 통신할 수 있도록 Azure Active Directory(AD) 앱을 만듭니다.
management.azure.com | 어플라이언스에 대한 Azure AD 앱을 만들어 Azure 마이그레이션 서비스와 통신합니다.
dc.services.visualstudio.com | 내부 모니터링에 사용되는 앱 로그를 업로드합니다.
*.vault.azure.net | Azure 키 자격 증명 모음에서 비밀을 관리합니다.
aka.ms/* | 일명 링크에 대한 액세스를 허용합니다. Azure 마이그레이션 어플라이언스 업데이트에 사용됩니다.
download.microsoft.com/download | Microsoft 다운로드에서 다운로드를 허용합니다.
\*.servicebus.windows.net | 어플라이언스와 Azure 마이그레이션 서비스 간의 통신입니다.
*.discoverysrv.windowsazure.com <br/> *.migration.windowsazure.com | Azure 마이그레이션 서비스 URL에 연결합니다.
\*.hypervrecoverymanager.windowsazure.com | **VMware 에이전트 없는 마이그레이션에 사용**<br/><br/> Azure 마이그레이션 서비스 URL에 연결합니다.
\*.blob.core.windows.net |  **VMware 에이전트 없는 마이그레이션에 사용**<br/><br/>마이그레이션을 위해 저장소에 데이터를 업로드합니다.




## <a name="collected-data---vmware"></a>수집된 데이터 - VMware

어플라이언스는 메타데이터, 성능 데이터 및 종속성 분석 데이터를 수집합니다(에이전트 없는 [종속성 분석이](concepts-dependency-visualization.md) 사용되는 경우).

### <a name="metadata"></a>메타데이터

Azure Migrate 어플라이언스에서 검색한 메타데이터를 사용하면 컴퓨터와 앱이 Azure로 마이그레이션할 준비가 되었는지, 적절한 크기의 컴퓨터 및 앱, 계획 비용 및 응용 프로그램 종속성을 분석할 준비가 되었는지 파악하는 데 도움이 됩니다. Microsoft는 라이선스 규정 준수 감사에서 이 데이터를 사용하지 않습니다.

어플라이언스가 수집하고 Azure로 보내는 VMware VM 메타데이터의 전체 목록은 다음과 같습니다.

**데이터** | **카운터**
--- | --- 
**기계 세부 정보** | 
VM ID | vm.Config.InstanceUuid 
VM 이름 | vm.Config.Name
vCenter Server ID | VMwareClient.인스턴스.Uuid
VM 설명 | vm.Summary.Config.Annotation
라이선스 제품 이름 | vm.Client.ServiceContent.About.LicenseProductName
운영 체제 유형 | Vm. 요약Config.게스트전체이름
부팅 유형 | vm.Config.Firmware
코어 수 | vm.Config.Hardware.NumCPU
메모리(MB) | vm.Config.Hardware.MemoryMB
디스크 수 | Vm. Config.Hardware.Device.ToList(). FindAll(x => 가상 디스크).카운트
디스크 크기 목록 | Vm. Config.Hardware.Device.ToList(). FindAll(x => 가상 디스크)
네트워크 어댑터 목록 | Vm. Config.Hardware.Device.ToList(). FindAll(x => 버추얼넷).카운트
CPU 사용률 | cpu.usage.average
메모리 사용률 |mem.usage.average
**디스크 세부 정보당** | 
디스크 키 값 | disk.Key
디쿠미트 번호 | disk.UnitNumber
디스크 컨트롤러 키 값 | disk.ControllerKey.Value
프로비전된 기가바이트 | virtualDisk.DeviceInfo.Summary
디스크 이름 | 디스크를 사용하여 생성된 값입니다. 단위 번호, 디스크. 키, 디스크. 컨트롤러키.발루
초당 읽기 작업 | virtualDisk.numberReadAveraged.average
초당 쓰기 작업 | virtualDisk.numberWriteAveraged.average
읽기 처리량(초당 MB) | virtualDisk.read.average
쓰기 처리량(초당 MB) | virtualDisk.write.average
**NIC 세부 정보당** | 
네트워크 어댑터 이름 | nic.Key
MAC 주소 | ((VirtualEthernetCard)nic).MacAddress
IPv4 주소 | vm.Guest.Net
IPv6 주소 | vm.Guest.Net
읽기 처리량(초당 MB) | net.received.average
쓰기 처리량(초당 MB) | net.transmitted.average
**인벤토리 경로 세부 정보** | 
속성 | container.GetType().Name
자식 개체 유형 | container.ChildType
참조 세부 정보 | container.MoRef
부모 세부 정보 | Container.Parent
VM당 폴더 세부 정보 | ((Folder)container).ChildEntity.Type
VM당 데이터 센터 세부 정보 | ((Datacenter)container).VmFolder
호스트 폴더당 데이터 센터 세부 정보 | ((Datacenter)container).HostFolder
호스트당 클러스터 세부 정보 | ((클러스터컴퓨팅 리소스)컨테이너를 사용합니다. 호스트
VM당 호스트 세부 정보 | ((호스트 시스템)컨테이너를 참조하십시오. Vm

### <a name="performance-data"></a>성능 데이터


어플라이언스가 수집하고 Azure로 보내는 VMware VM 성능 데이터는 다음과 같습니다.

**데이터** | **카운터** | **영향 평가**
--- | --- | ---
CPU 사용률 | cpu.usage.average | 권장 VM 크기/비용
메모리 사용률 | mem.usage.average | 권장 VM 크기/비용
디스크 읽기 처리량(초당 MB) | virtualDisk.read.average | 디스크 크기, 스토리지 비용, VM 크기 계산
디스크 쓰기 처리량(초당 MB) | virtualDisk.write.average | 디스크 크기, 스토리지 비용, VM 크기 계산
초당 디스크 읽기 작업 | virtualDisk.numberReadAveraged.average | 디스크 크기, 스토리지 비용, VM 크기 계산
디스크는 초당 작업을 기록합니다. | virtualDisk.numberWriteAveraged.average  | 디스크 크기, 스토리지 비용, VM 크기 계산
NIC 읽기 처리량(초당 MB) | net.received.average | VM 크기에 대한 계산
NIC는 처리량(초당 MB)을 씁니다. | net.transmitted.average  |VM 크기에 대한 계산

### <a name="app-dependencies-metadata"></a>앱 종속성 메타데이터

에이전트 없는 종속성 분석은 연결 및 프로세스 데이터를 수집합니다.

#### <a name="connection-data"></a>연결 데이터

에이전트 없는 종속성 분석을 위해 사용하도록 설정된 각 VM에서 어플라이언스가 수집하는 연결 데이터는 다음과 같습니다. 이 데이터는 Azure로 전송됩니다.

**데이터** | **사용된 명령** 
--- | --- 
로컬 포트 | netstat
로컬 IP 주소 | netstat
원격 포트 | netstat
원격 IP 주소 | netstat
TCP 연결 상태 | netstat
프로세스 ID | netstat
아니요. 활성 연결의 | netstat

#### <a name="process-data"></a>데이터 처리
에이전트 없는 종속성 분석을 위해 사용하도록 설정된 각 VM에서 어플라이언스가 수집하는 프로세스 데이터는 다음과 같습니다. 이 데이터는 Azure로 전송됩니다.

**데이터** | **WMI 클래스(WMI class)** | **WMI 클래스 속성**
--- | --- | ---
프로세스 이름 | Win32_Process | 실행 경로
인수 처리 | Win32_Process | 명령줄
애플리케이션 이름 | Win32_Process | 버전정보.제품 이름 실행 가능한 Path 속성의 매개 변수

#### <a name="linux-vm-data"></a>리눅스 VM 데이터

에이전트 없는 종속성 분석을 위해 사용 가능한 각 Linux VM에서 어플라이언스가 수집하는 연결 및 프로세스 데이터는 다음과 같습니다. 이 데이터는 Azure로 전송됩니다.

**데이터** | **사용된 명령** 
--- | ---
로컬 포트 | netstat 
로컬 IP 주소 | netstat 
원격 포트 | netstat 
원격 IP 주소 | netstat 
TCP 연결 상태 | netstat 
아니요. 활성 연결의 | netstat
프로세스 ID  | netstat 
프로세스 이름 | ps
인수 처리 | ps
애플리케이션 이름 | dpkg 또는 rpm



## <a name="collected-data---hyper-v"></a>수집된 데이터 - 하이퍼-V

어플라이언스는 메타데이터, 성능 데이터 및 종속성 분석 데이터를 수집합니다(에이전트 없는 [종속성 분석이](concepts-dependency-visualization.md) 사용되는 경우).

### <a name="metadata"></a>메타데이터
Azure Migrate 어플라이언스에서 검색한 메타데이터를 사용하면 컴퓨터와 앱이 Azure로 마이그레이션할 준비가 되었는지, 적절한 크기의 컴퓨터 및 앱, 계획 비용 및 응용 프로그램 종속성을 분석할 준비가 되었는지 파악하는 데 도움이 됩니다. Microsoft는 라이선스 규정 준수 감사에서 이 데이터를 사용하지 않습니다.

어플라이언스가 수집하고 Azure로 보내는 Hyper-V VM 메타데이터의 전체 목록은 다음과 같습니다.

**데이터* | **WMI 클래스** | **WMI 클래스 속성**
--- | --- | ---
**기계 세부 정보** | 
BIOS _ Msvm_BIOSElement 일련 번호 | 바이오스시리얼 넘버
VM 유형(1세대 또는 2세대) | Msvm_VirtualSystemSettingData | 가상 시스템 하위 유형
VM 표시 이름 | Msvm_VirtualSystemSettingData | ElementName
VM 버전 | Msvm_ProcessorSettingData | 가상 수량
메모리(바이트) | Msvm_MemorySettingData | 가상 수량
VM에서 사용할 수 있는 최대 메모리 | Msvm_MemorySettingData | 제한
동적 메모리 사용 | Msvm_MemorySettingData | DynamicMemoryEnabled
운영 체제 이름/버전/FQDN | Msvm_KvpExchangeComponent | 게스트 인트린시익스아이템 이름 데이터
VM 전원 상태 | Msvm_ComputerSystem | EnabledState
**디스크 세부 정보당** | 
디스크 식별자 | Msvm_VirtualHardDiskSettingData | 버추얼 디스크 ID
가상 하드 디스크 유형 | Msvm_VirtualHardDiskSettingData | Type
가상 하드 디스크 크기 | Msvm_VirtualHardDiskSettingData | 최대 내부 크기
가상 하드 디스크 상위 | Msvm_VirtualHardDiskSettingData | ParentPath
**NIC 세부 정보당** | 
IP 주소(합성 NIC) | Msvm_GuestNetworkAdapterConfiguration | IP 주소
DHCP 지원(합성 NIC) | Msvm_GuestNetworkAdapterConfiguration | DHCPEnabled
NIC ID(합성 NIC) | Msvm_SyntheticEthernetPortSettingData | InstanceID
NIC MAC 주소(합성 NIC) | Msvm_SyntheticEthernetPortSettingData | 주소
NIC ID(레거시 NIC) | MsvmEmulatedEthernet포트세팅 데이터 | InstanceID
닉 맥 ID (레거시 NIC) | MsvmEmulatedEthernet포트세팅 데이터 | 주소

### <a name="performance-data"></a>성능 데이터

어플라이언스가 수집하고 Azure로 보내는 Hyper VM 성능 데이터는 다음과 같습니다.

**성능 카운터 클래스** | **카운터** | **영향 평가**
--- | --- | ---
하이퍼 V 하이퍼바이저 가상 프로세서 | % 게스트 실행 시간 | 권장 VM 크기/비용
하이퍼-V 다이나믹 메모리 VM | 전류 압력(%)<br/> 게스트 가시 물리적 메모리 (MB) | 권장 VM 크기/비용
하이퍼 V 가상 스토리지 장치 | 바이트/초 읽기 | 디스크 크기, 스토리지 비용, VM 크기 계산
하이퍼 V 가상 스토리지 장치 | 바이트/초 쓰기 | 디스크 크기, 스토리지 비용, VM 크기 계산
Hyper-V Virtual Network 어댑터 | 바이트 수신/초 | VM 크기에 대한 계산
Hyper-V Virtual Network 어댑터 | 전송된 바이트/초 | VM 크기에 대한 계산

- CPU 사용률은 VM에 연결된 모든 가상 프로세서에 대한 모든 사용량의 합계입니다.
- 메모리 사용률은 (전류 압력 * 게스트 가시 실제 메모리) / 100입니다.
- 디스크 및 네트워크 사용률 값은 나열된 Hyper-V 성능 카운터에서 수집됩니다.

## <a name="appliance-upgrades"></a>어플라이언스 업그레이드

어플라이언스에서 실행 중인 Azure 마이그레이션 에이전트가 업데이트되면 어플라이언스가 업그레이드됩니다. 기본적으로 어플라이언스에서 자동 업데이트가 활성화되어 있기 때문에 이 작업은 자동으로 수행됩니다. 이 기본 설정을 변경하여 에이전트를 수동으로 업데이트할 수 있습니다.

- **자동 업데이트 끄기**: HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureAppliance "자동 업데이트" 키를 0(DWORD)으로 설정하여 레지스트리에서 자동 업데이트를 끕니다. 수동 업데이트를 사용하려는 경우 어플라이언스의 오래된 각 에이전트에 대한 업데이트 단추를 사용하여 어플라이언스의 모든 에이전트를 동시에 **업데이트하는** 것이 중요합니다.
- **수동 업데이트**: 수동 업데이트의 경우 어플라이언스의 오래된 각 에이전트에 대한 **업데이트** 단추를 사용하여 어플라이언스의 모든 에이전트를 업데이트해야 합니다. 언제든지 업데이트 설정을 자동 업데이트로 다시 전환할 수 있습니다.

![어플라이언스 자동 업데이트](./media/migrate-appliance/autoupdate.png)

## <a name="next-steps"></a>다음 단계

- VMware용 어플라이언스를 설정하는 [방법에 대해 알아봅니다.](how-to-set-up-appliance-vmware.md)
- 하이퍼-V에 대한 어플라이언스를 설정하는 [방법에 대해 알아봅니다.](how-to-set-up-appliance-hyper-v.md)
- 물리적 서버에 대한 어플라이언스를 설정하는 [방법에 대해 알아봅니다.](how-to-set-up-appliance-physical.md)

