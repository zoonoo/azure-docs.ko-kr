---
title: VMware 평가 및 마이그레이션을 위한 azure 마이그레이션 지원 매트릭스
description: 설정 지원 및 Azure Migrate 서비스를 사용 하 여 Azure에 VMware Vm의 마이그레이션하고 평가 대 한 제한 사항 요약 되어 있습니다.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 06/24/2019
ms.author: raynew
ms.openlocfilehash: 567a6582e193208a7ff4aa37bafefe1dec4f4e8f
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811584"
---
# <a name="support-matrix-for-vmware-assessment-and-migration"></a>VMware 평가 및 마이그레이션을 위한 지원 매트릭스

사용할 수는 [Azure Migrate 서비스](migrate-overview.md) 평가 하 고 Microsoft Azure 클라우드로 컴퓨터를 마이그레이션하려 합니다. 이 문서는 지원 설정 및 평가 하 고 온-프레미스 VMware Vm을 마이그레이션에 대 한 제한 사항 요약 되어 있습니다.


## <a name="vmware-scenarios"></a>VMware 시나리오

VMware Vm에 대 한 지원 되는 시나리오를 요약 하는 테이블입니다.

**배포** | **세부 정보** 
--- | --- 
**온-프레미스 VMware Vm 평가** | [설정](tutorial-prepare-vmware.md) 첫 번째 평가 합니다.<br/><br/> [실행](scale-vmware-assessment.md) 대규모 평가 합니다.
**VMware Vm 마이그레이션** | 에이전트 없는 마이그레이션을 사용 하 여 몇 가지 제한 사항이 마이그레이션할 수도 있고 한 에이전트 기반 마이그레이션을 사용 하 여 수 있습니다. [자세히 알아보기](server-migrate-overview.md)


    


## <a name="azure-migrate-projects"></a>Azure Migrate 프로젝트

**지원** | **세부 정보**
--- | ---
Azure 사용 권한 | Azure Migrate 프로젝트를 만드는 구독의 참가자 또는 소유자 권한이 필요 합니다.
VMware 제한 사항  | 단일 프로젝트에서 최대 35,000 VMware Vm을 평가 합니다.

프로젝트 평가 한도까지 VMware Vm 및 Hyper-v Vm을 모두 포함할 수 있습니다.

## <a name="assessment-vmware-server-requirements"></a>평가 VMware 서버 요구 사항

이 테이블에는 평가 지원 및 VMware 가상화 서버에 대 한 제한 사항 요약 되어 있습니다.

**지원** | **세부 정보**
--- | ---
**vCenter 서버** | VMware Vm은 평가 하려는 5.5, 6.0, 6.5, 또는 6.7을 실행 하는 하나 이상의 vCenter 서버에서 관리 되어야 합니다.

## <a name="assessment-vcenter-server-permissions"></a>평가 vCenter 서버 사용 권한

평가 위해 vCenter Server에 대 한 읽기 전용 계정이 필요 합니다.

## <a name="assessment-appliance-requirements"></a>평가 어플라이언스 요구 사항

**지원** | **세부 정보**
--- | ---
**ESXi** | 어플라이언스 VM은 버전 5.5 이상을 실행 하는 ESXi 호스트에 배포 되어야 합니다.
**Azure Migrate 프로젝트** | 단일 프로젝트를 사용 하 여 어플라이언스에 연결할 수 있습니다.
**vCenter Server** | 어플라이언스는 vCenter Server에서 최대 10,000 개의 VMware Vm을 검색할 수 있습니다.<br/> 어플라이언스는 하나의 vCenter Server에 연결할 수 있습니다.


## <a name="assessment-url-access-requirements"></a>평가 URL 액세스 요구 사항

Azure Migrate 기기에는 인터넷에 인터넷 연결이 필요합니다.

- 어플라이언스를 배포할 때 Azure Migrate는 연결 검사를 아래 표에 요약 된 url입니다.
- URL 기반 firewall.proxy를 사용 하는 경우 이러한 Url 프록시 Url을 조회 하는 동안 받은 모든 CNAME 레코드는 해결 되었는지에 대 한 액세스를 허용 합니다.

**URL** | **세부 정보**  
--- | --- |
*.portal.azure.com  | Azure portal에서 Azure Migrate로 이동 합니다.
*.windows.net | Azure 구독에 로그인합니다.
*.microsoftonline.com | Azure Migrate 서비스와 통신 하는 어플라이언스에 대 한 Active Directory 앱을 만듭니다.
management.azure.com | Azure Migrate 서비스와 통신 하는 어플라이언스에 대 한 Active Directory 앱을 만듭니다.
dc.services.visualstudio.com | 내부 모니터링에 사용 되는 앱 로그를 업로드 합니다.
*.vault.azure.net | Azure Key Vault에서 비밀을 관리 합니다.
\*.servicebus.windows.net | 어플라이언스 및 Azure Migrate 서비스 간의 통신 합니다.
*.discoverysrv.windowsazure.com <br/> *.migration.windowsazure.com <br/> \*.hypervrecoverymanager.windowsazure.com | Azure Migrate 서비스 Url에 연결 합니다.
\*.blob.core.windows.net | 저장소 계정에 데이터를 업로드 합니다.


## <a name="assessment-port-requirements"></a>평가 포트 요구 사항

**장치** | **연결**
--- | --- 
어플라이언스 | 어플라이언스로 원격 데스크톱 연결을 허용 하도록 3389 TCP 포트에서 인바운드 연결 합니다.<br/> 인바운드 URL을 사용 하 여 어플라이언스 관리 앱을 원격으로 액세스 하려면 44368 포트로 연결: https://<appliance-ip-or-name>:44368 <br/>Azure Migrate를 검색 및 성능 메타 데이터를 전송 하려면 포트 443에서 아웃 바운드 연결 합니다.
vCenter Server | 인바운드 어플라이언스 평가 대 한 구성 및 성능 메타 데이터를 수집할 수 있도록 TCP 포트 443에 연결 합니다. <br/> 어플라이언스는 기본적으로 포트 443에서 vCenter에 연결합니다. VCenter server는 다른 포트에서 수신 대기를 하는 경우에 검색을 설정 하는 경우 포트를 수정할 수 있습니다.


## <a name="agentless-migration-vmware-server-requirements"></a>에이전트 없는 마이그레이션 VMware 서버 요구 사항

이 테이블에는 평가 지원 및 VMware 가상화 서버에 대 한 제한 사항 요약 되어 있습니다.

**지원** | **세부 정보**
--- | ---
**vCenter 서버** | VMware Vm 에이전트는 마이그레이션을 사용 하 여 마이그레이션한 5.5, 6.0, 6.5, 또는 6.7을 실행 하는 하나 이상의 vCenter 서버에서 관리 되어야 합니다.

## <a name="agentless-migration-vcenter-server-permissions"></a>에이전트 없는 마이그레이션 vCenter 서버 사용 권한

**사용 권한** | **세부 정보**
--- | --- 
Datastore.Browse | VM 스냅숏 생성 및 삭제 문제를 해결 하려면 로그 검색을 허용 합니다.
Datastore.LowLevelFileOperations | 스냅숏 생성 및 삭제를 해결 하는 데이터 저장소 브라우저에서 읽기/쓰기/삭제/이름 바꾸기 작업을 허용 합니다.
VirtualMachine.Configuration.DiskChangeTracking | 사용을 허용 하거나 데이터 스냅숏 간의 변경 된 블록을 가져오려고 VM 디스크의 변경 내용 추적을 사용 하지 않도록 설정
VirtualMachine.Configuration.DiskLease | Vm의 경우 VMware vSphere 가상 디스크 개발 키트 (VDDK)를 사용 하 여 디스크를 읽도록 디스크 임대 작업을 허용 합니다.
VirtualMachine.Provisioning.AllowReadOnlyDiskAccess | VDDK를 사용 하 여 디스크를 읽도록 VM에서 디스크를 열 수 있습니다.
VirtualMachine.Provisioning.AllowVirtualMachineDownload  | 로그를 다운로드 하 여 오류가 발생 하는 경우 문제를 해결 하는 VM과 연결 된 파일에 읽기 작업이 허용 됩니다. 
VirtualMachine.SnapshotManagement.* | VM 스냅숏 복제의 생성 및 관리를 허용 합니다. 
가상 Machine.Interaction.Power 해제 | VM을 Azure로 마이그레이션하는 동안 전원 꺼짐 수 있습니다.


## <a name="agentless-migration-vmware-vm-requirements"></a>에이전트 없는 마이그레이션-VMware VM 요구 사항

**지원** | **세부 정보**
--- | ---
**지원되는 운영 체제** | [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) 하 고 [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) 에이전트 없이 마이그레이션을 사용 하 여 Azure에서 지원 되는 운영 체제를 마이그레이션할 수 있습니다.
**Azure에 대해 필요한 변경** | 일부 Vm을 Azure에서 실행할 수 있도록 변경 해야 합니다. Azure Migrate에는 다음 운영 체제에 대 한 자동으로 이러한 변경 작업:<br/> - Red Hat Enterprise Linux 6.5+, 7.0+<br/> - CentOS 6.5+, 7.0+</br> - SUSE Linux Enterprise Server 12 SP1+<br/> - Ubuntu 14.04LTS, 16.04LTS, 18.04LTS<br/> -Debian 7, 8<br/><br/> 다른 운영 체제 마이그레이션 시작 하기 전에 수동으로 조정 해야 합니다. 관련 문서를이 작업을 수행 하는 방법에 대 한 지침을 포함 합니다.
**Linux boot** | 전용 파티션에 /boot 인 경우 OS 디스크에 있어야 하 고 여러 디스크에 걸쳐 분산 하지 않습니다.<br/> /Boot 루트 (/) 파티션의 일부 이면 다음 '/' 파티션 OS 디스크에 있고 다른 디스크에 걸쳐 있지 않고 합니다.
**UEFI 부팅** | UEFI 부팅을 사용 하 여 Vm 마이그레이션에 대 한 지원 되지 않습니다.
**암호화 된 디스크/볼륨** | 암호화 된 디스크/볼륨을 사용 하 여 Vm 마이그레이션에 대 한 지원 되지 않습니다.
**RDM/통과 디스크** | Vm이 RDM 또는 통과 디스크에 있는 경우 이러한 디스크는 Azure에 복제할 수 없습니다.
**NFS** | Vm의 볼륨으로 탑재 하는 NFS 볼륨을 복제할 수 없습니다.
**대상 디스크** | Vm에서 Azure managed disks (HHD 표준, 프리미엄 SSD)로 마이그레이션할 수 있습니다.


## <a name="agentless-migration-appliance-requirements"></a>에이전트 없는 마이그레이션 어플라이언스 요구 사항


**지원** | **세부 정보**
--- | ---
**ESXi** | 어플라이언스 VM은 버전 5.5 이상을 실행 하는 ESXi 호스트에 배포 되어야 합니다.
**Azure Migrate 프로젝트** | 단일 프로젝트를 사용 하 여 어플라이언스에 연결할 수 있습니다.
**vCenter Server** | 어플라이언스는 vCenter Server에서 최대 10,000 개의 VMware Vm을 검색할 수 있습니다.<br/> 어플라이언스는 하나의 vCenter Server에 연결할 수 있습니다.
**VDDK** | Azure 마이그레이션 서버 마이그레이션을 사용 하 여 에이전트 없는 마이그레이션 인를 실행 하는 경우 VMware vSphere 가상 디스크 개발 키트 (VDDK) 어플라이언스 VM에 설치 되어야 합니다.

## <a name="agentless-migration-url-access-requirements"></a>에이전트 없는 마이그레이션 URL 액세스 요구 사항

Azure Migrate 기기에는 인터넷에 인터넷 연결이 필요합니다.

- 어플라이언스를 배포할 때 Azure Migrate는 연결 검사를 아래 표에 요약 된 url입니다.
- URL 기반 firewall.proxy를 사용 하는 경우 이러한 Url 프록시 Url을 조회 하는 동안 받은 모든 CNAME 레코드는 해결 되었는지에 대 한 액세스를 허용 합니다.

**URL** | **세부 정보**  
--- | --- 
*.portal.azure.com | Azure portal에서 Azure Migrate로 이동 합니다.
*.windows.net | Azure 구독에 로그인합니다.
*.microsoftonline.com | Azure Migrate 서비스와 통신 하는 어플라이언스에 대 한 Active Directory 앱을 만듭니다.
management.azure.com | Azure Migrate 서비스와 통신 하는 어플라이언스에 대 한 Active Directory 앱을 만듭니다.
dc.services.visualstudio.com | 내부 모니터링에 사용 되는 앱 로그를 업로드 합니다.
*.vault.azure.net | Azure Key Vault에서 비밀을 관리 합니다.
\*.servicebus.windows.net | 어플라이언스 및 Azure Migrate 서비스 간의 통신 합니다.
*.discoverysrv.windowsazure.com<br/> *.migration.windowsazure.com<br/> \*.hypervrecoverymanager.windowsazure.com | Azure Migrate 서비스 Url에 연결 합니다.
\*.blob.core.windows.net | 저장소 계정에 데이터를 업로드 합니다.


## <a name="agentless-migration-port-requirements"></a>에이전트 없는 마이그레이션 포트 요구 사항

**장치** | **연결**
--- | --- 
어플라이언스 | 아웃 바운드 TCP 포트 3389 Azure에 복제 된 데이터를 업로드 하는 데 복제 및 마이그레이션에 대 한 Azure Migrate와 통신 합니다.
vCenter Server | 복제를 오케스트레이션 하기-데이터 복사, 스냅숏을 만든 어플라이언스를 허용 하는 443 TCP 포트에서 인바운드 연결 해제 스냅숏
vSphere/EXSI 호스트 | 인바운드 TCP 포트 902 스냅숏의 데이터를 복제 하는 어플라이언스에 대 한를 합니다. 


## <a name="agent-based-migration-vmware-server-requirements"></a>에이전트 기반 마이그레이션-VMware 서버 요구 사항

이 테이블에는 평가 지원 및 VMware 가상화 서버에 대 한 제한 사항 요약 되어 있습니다.

**지원** | **세부 정보**
--- | ---
**vCenter server/ESXI** | VMware Vm은 마이그레이션한 5.5, 6.0, 6.5, 또는 6.7, 실행 또는 vSphere 버전 5.5, 6.0, 6.5 또는 6.7을 사용 하 여 ESXI 호스트에서 실행 중인 하나 이상의 vCenter 서버에서 관리 되어야 합니다.

### <a name="agent-based-migration-vcenter-server-permissions"></a>에이전트 기반 마이그레이션-vCenter 서버 사용 권한

**사용 권한** | **세부 정보**
--- | --- 
Datastore.AllocateSpace | VM에 대 한 데이터 저장소에 할당 된 공간을 허용 스냅숏, 복제 또는 가상 디스크입니다. 
Datastore.Browse | VM 스냅숏 생성 및 삭제 문제를 해결 하려면 로그 검색을 허용 합니다.
Datastore.LowLevelFileOperations | 읽기 허용, 작성, 삭제 및 이름 바꾸기 스냅숏 만들기/삭제 문제를 해결 하려면 데이터 저장소 브라우저에서 작업 합니다.
Datastore.UpdateVirtualMachineFiles | 데이터 저장소 resignatured 후 데이터 저장소에서 VM 파일에 경로 업데이트를 허용 합니다.
Network.AssignNetwork | VM 리소스를 네트워크를 할당할 수 있습니다.
AssignVirtualMachineToResourcePool | 리소스 풀에 VM의 할당을 허용 합니다.
Resource.MigratePoweredOffVirtualMachine | 다른 리소스 풀 또는 호스트에 전원이 꺼진 VM 마이그레이션을 허용 합니다.
Resource.MigratePoweredOnVirtualMachine | 다른 리소스 풀 또는 호스트에 전원이 켜진 VM의 vMotion을 사용 하 여 마이그레이션을 허용 합니다.
Tasks.CreateTask | 확장 사용자 정의 작업을 만들 수 있습니다.
Tasks.UpdateTask | 사용자 정의 작업을 업데이트 하려면 확장을 허용 합니다.
VirtualMachine.Configuration. | VM 옵션 및 장치를 구성할 수 있습니다.
가상 Machine.Interaction.AnswerQuestion | VM 상태 전환 또는 런타임 오류 문제 해결을 허용 합니다.
Virtual Machine.Interaction.DeviceConnection | VM의 disconnectable 가상 장치의 연결된 상태를 변경할 수 있습니다.
가상 Machine.Interaction.ConfigureCDMedia | 가상 DVD 또는 CD-ROM 장치를 구성할을 수 있습니다.
가상 Machine.Interaction.ConfigureFloppyMedia | 가상 플로피 장치를 구성할을 수 있습니다.
Virtual Machine.Interaction.PowerOff | Vm이 Azure로 마이그레이션하는 동안 전원 꺼짐 수 있습니다.
Virtual Machine.Interaction.PowerOn | 전원이 꺼진 VM 켜기 및 일시 중지 된 VM을 다시 시작을 허용 합니다.
Virtual Machine.Interaction.VMwareToolsInstall | 탑재 또는 게스트 운영 체제에 대 한 CD-ROM으로 VMware 도구 CD 설치 관리자를 분리할 수 있습니다.
VirtualMachine.Inventory.CreateNew | VM 만들기 및 필요한 리소스의 할당을 허용 합니다.
VirtualMachine.Inventory.Register | VCenter Server에 기존 VM 또는 호스트 인벤토리 추가 허용 합니다.
VirtualMachine.Inventory.Unregister | VCenter Server에서에서 VMe 또는 호스트 인벤토리 등록을 취소할 수 있습니다.
VirtualMachine.Provisioning.AllowVirtualMachineFilesUpload | Vmx, 디스크, 로그 및 nvram를 포함 하 여 VM에 연결 된 파일에 쓰기 작업을 허용 합니다.
VirtualMachine.Provisioning.AllowVirtualMachineDownload | 문제 해결에 대 한 로그를 다운로드 하는 VM과 연결 된 파일에서 읽기 작업을 허용 합니다.
VirtualMachine.SnapshotManagement.RemoveSnapshot | 스냅숏 기록에서 스냅숏 제거를 허용 합니다.

## <a name="agent-based-migration-replication-appliance-requirements"></a>마이그레이션 복제 에이전트 기반 어플라이언스 요구 사항

에 대 한 요구 사항을 합니다 [복제 어플라이언스](migrate-replication-appliance.md) VMware Vm 및 물리적 에이전트 기반 마이그레이션에 사용 되는 Azure 마이그레이션 서버 마이그레이션을 사용 하 여 서버를 테이블에 요약 되어 있습니다.

> [!NOTE]
> Azure Migrate 허브에서 제공 하는 OVA 템플릿을 사용 하 여 복제 어플라이언스를 설정 하는 경우 어플라이언스는 Windows Server 2016을 실행 하며 지원 요구 사항을 준수 합니다. 복제 어플라이언스는 물리적 서버에서 수동으로 설정 하 고 있는지 확인 하는 경우 요구 사항을 준수 합니다.



**구성 요소** | **요구 사항** 
--- | ---
 | **VMware 설정** (VMware VM 어플라이언스)
**PowerCLI** | [PowerCLI 버전 6.0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1) 복제 어플라이언스 VMware VM에서 실행 중인 경우 설치 해야 합니다.
**NIC 유형** | VMXNET3 (어플라이언스 VMware VM 경우)
 | **하드웨어 설정** 
CPU 코어 | 8 
RAM | 16GB
디스크 수 | 3: OS 디스크, 프로세스 서버 캐시 디스크와 보존 드라이브입니다.
사용 가능한 디스크 공간 (캐시) | 600GB
사용 가능한 디스크 공간(보존 디스크) | 600GB
**소프트웨어 설정** | 
운영 체제 | Windows Server 2016 또는 Windows Server 2012 R2
운영 체제 로케일 | 미국 영어(en-us)
TLS | TLS 1.2는 사용 하도록 설정 해야 합니다.
.NET Framework | .NET framework 4.6 이상을 설치할지 머신에서 (강력한 암호화를 통해 사용 하도록 설정 합니다.
MySQL | MySQL 어플라이언스에 설치 되어야 합니다.<br/> MySQL을 설치해야 합니다. 수동으로 설치 하거나 Site Recovery가 어플라이언스 배포 중에 설치할 수 있습니다. 
다른 앱 | 복제 어플라이언스에서 다른 앱을 실행 하지 않아야 합니다.
Windows Server 역할 | 다음 역할을 사용하지 않도록 설정함: <br> - Active Directory Domain Services <br>- 인터넷 정보 서비스 <br> - Hyper-V 
그룹 정책 | 다음 그룹 정책을 사용하지 않도록 설정함: <br> - 명령 프롬프트에 대한 액세스 방지 <br> - 레지스트리 편집 도구에 대한 액세스 방지 <br> - 파일 첨부를 위한 트러스트 논리 <br> - 스크립트 실행 켜기 <br> [자세히 알아보기](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)
IIS | - 기존의 기본 웹 사이트 없음 <br> - 포트 443에서 수신 대기하는 기존의 웹 사이트/응용 프로그램 없음 <br>- [익명 인증](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) 사용 <br> - [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx) 설정 사용 
**네트워크 설정** | 
IP 주소 유형 | 정적 
포트 | 443(컨트롤 채널 오케스트레이션)<br>9443(데이터 전송) 
NIC 유형 | VMXNET3 

### <a name="replication-appliance-url-access"></a>복제 어플라이언스 URL 액세스

복제 어플라이언스에서 다음이 Url에 액세스를 해야합니다.

**URL** | **세부 정보**
--- | ---
\*.backup.windowsazure.com | 복제된 데이터 전송 및 조정에 사용됩니다.
\*.store.core.windows.net | 복제된 데이터 전송 및 조정에 사용됩니다.
\*.blob.core.windows.net | 복제된 데이터를 저장하는 스토리지 계정에 액세스하는 데 사용됩니다.
\*.hypervrecoverymanager.windowsazure.com | 복제 관리 작업 및 조정에 사용됩니다.
https:\//management.azure.com | 복제 관리 작업 및 조정에 사용됩니다. 
*.services.visualstudio.com | 원격 분석 용도로 사용(선택 사항)
time.nist.gov | 시스템 시간과 글로벌 시간 사이의 시간 동기화를 확인하는 데 사용됩니다.
time.windows.com | 시스템 시간과 글로벌 시간 사이의 시간 동기화를 확인하는 데 사용됩니다.
https:\//login.microsoftonline.com <br/> https:\//secure.aadcdn.microsoftonline-p.com <br/> https:\//login.live.com <br/> https:\//graph.windows.net <br/> https:\//login.windows.net <br/> https:\//www.live.com <br/> https:\//www.microsoft.com  | OVF 이러한 Url에 액세스를 해야합니다. Azure Active Directory에서 액세스 제어 및 ID 관리에 사용됩니다.
https:\//dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi | MySQL 다운로드를 완료하려면


#### <a name="mysql-installation-options"></a>MySQL 설치 옵션

다음이 방법 중 하나를 사용 하 여 복제 어플라이언스에서 MySQL은 설치할 수 있습니다.

**설치** | **세부 정보**
--- | ---
수동으로 다운로드 및 설치 | MySQL 응용 프로그램을 다운로드 및 C:\Temp\ASRSetup 폴더에 배치 다음 수동으로 설치 합니다.<br/> 이미 설치 된 MySQL 알아보겠습니다 어플라이언스를 설정 합니다. 
온라인 다운로드 하지 마십시오. | MySQL installer 응용 프로그램 C:\Temp\ASRSetup 폴더에 배치 합니다. 어플라이언스를 설치 하 고 다운로드 하 고 MySQL을 설치 하려면를 클릭 하는 경우 추가 설치 관리자를 사용 합니다. 
Azure에서 다운로드 마이그레이션 | 어플라이언스를 설치 하 고 MySQL에 대 한 메시지가 표시 되는 경우 선택 **다운로드 및 설치**합니다.



## <a name="agent-based-migration-vmware-vm-requirements"></a>에이전트 기반 마이그레이션-VMware VM 요구 사항

**지원** | **세부 정보**
--- | ---
**컴퓨터 작업** | Azure Migrate는 모든 워크 로드 (예: Active Directory, SQL server 등)의 마이그레이션을 지원 되는 컴퓨터에서 실행 합니다.
**운영 체제** | 최신 정보를 검토 합니다 [운영 체제 지원](../site-recovery/vmware-physical-azure-support-matrix.md#replicated-machines) Site Recovery에 대 한 합니다. Azure Migrate에는 동일한 VM 운영 체제 지원을 제공합니다.
**Linux 파일 시스템/게스트 저장소** | 최신 정보를 검토 합니다 [Linux 파일 시스템 지원](../site-recovery/vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage) Site Recovery에 대 한 합니다. Azure Migrate은 동일한 Linux 파일 시스템 지원 합니다.
**Network/Storage** | 최신 정보를 검토 합니다 [네트워크](../site-recovery/vmware-physical-azure-support-matrix.md#network) 하 고 [저장소](../site-recovery/vmware-physical-azure-support-matrix.md#storage) Site Recovery에 대 한 필수 구성 요소입니다. Azure Migrate는 동일한 네트워크/저장소 요구 사항을 제공합니다.
**Azure 요구 사항** | 최신 정보를 검토 합니다 [Azure 네트워크](../site-recovery/vmware-physical-azure-support-matrix.md#azure-vm-network-after-failover)를 [저장소](../site-recovery/vmware-physical-azure-support-matrix.md#azure-storage), 및 [계산](../site-recovery/vmware-physical-azure-support-matrix.md#azure-compute) Site Recovery에 대 한 요구 사항입니다. Azure Migrate에는 VMware 마이그레이션에 대 한 동일한 요구 사항이 있습니다.
**모바일 서비스** | 마이그레이션할 각 VM에 모바일 서비스 에이전트를 설치 해야 합니다.
**대상 디스크** | Vm에서 Azure managed disks (HHD 표준, 프리미엄 SSD)로 마이그레이션할 수 있습니다.

   

## <a name="agent-based-migration-url-access-requirements"></a>에이전트 기반 마이그레이션 URL 액세스 요구 사항

VMware Vm에서 실행 되는 모바일 서비스에는 인터넷에 인터넷 연결이 필요 합니다.

- 모바일 서비스를 배포 하면 아래 표에 요약 된 Url에 연결 확인을 수행 합니다.
- URL 기반 firewall.proxy를 사용 하는 경우 이러한 Url 프록시 Url을 조회 하는 동안 받은 모든 CNAME 레코드는 해결 되었는지에 대 한 액세스를 허용 합니다.

**URL** | **세부 정보**  
--- | --- 
*.portal.azure.com | Azure portal에서 Azure Migrate로 이동 합니다.
*.windows.net | Azure 구독에 로그인합니다.
*.microsoftonline.com | Azure Migrate 서비스와 통신 하는 어플라이언스에 대 한 Active Directory 앱을 만듭니다. 
management.azure.com | Azure Migrate 서비스와 통신 하는 어플라이언스에 대 한 Active Directory 앱을 만듭니다.
dc.services.visualstudio.com | 내부 모니터링에 사용 되는 앱 로그를 업로드 합니다.
*.vault.azure.net | Azure Key Vault에서 비밀을 관리 합니다.
\*.servicebus.windows.net | 어플라이언스 및 Azure Migrate 서비스 간의 통신 합니다.
*.discoverysrv.windowsazure.com <br/> *.migration.windowsazure.com <br/> \*.hypervrecoverymanager.windowsazure.com | Azure Migrate 서비스 Url에 연결 합니다.
\*.blob.core.windows.net | 저장소 계정에 데이터를 업로드 합니다.

## <a name="agent-based-migration-port-requirements"></a>에이전트 기반 마이그레이션 포트 요구 사항

**장치** | **연결**
--- | --- 
VM | Vm에서 실행 되는 모바일 서비스와 통신 온-프레미스 구성 서버 HTTPS 443 포트에서 인바운드 복제 관리에 대 한 합니다.<br/><br/> VM은 HTTPS 9443 인바운드 포트의 프로세스 서버(구성 서버 컴퓨터에서 실행)로 복제 데이터를 전송합니다. 이 포트는 수정할 수 있습니다.
복제 어플라이언스 | 복제 어플라이언스는 HTTPS 443 아웃 바운드 포트를 통해 Azure 사용 하 여 복제를 오케스트레이션합니다.
프로세스 서버 | 프로세스 서버는 복제 데이터를 수신하고, 이를 최적화 및 암호화하며, 443 아웃바운드 포트를 통해 Azure Storage로 보냅니다.<br/> 기본적으로 프로세스 서버는 복제 어플라이언스에서 실행 됩니다.

## <a name="azure-vm-requirements"></a>Azure VM 요구 사항

모든 온-프레미스에서 Azure로 복제 된 Vm이이 표에 요약 되어 있는 Azure VM 요구 사항에 맞아야 합니다. Site Recovery 복제에 대 한 필수 구성 요소 검사를 실행할 때 검사 요구 사항을 충족 되지 않으면 실패 합니다.

**구성 요소** | **요구 사항** | **세부 정보**
--- | --- | ---
게스트 운영 체제 | 지원 되는 운영 체제를 확인 [복제 에이전트를 사용 하 여 VMware Vm](#agentless-migration-vmware-vm-requirements), 및 [에이전트 기반 복제를 사용 하 여 VMware Vm](#agent-based-migration-vmware-vm-requirements)합니다.<br/> 지원 되는 운영 체제를 실행 하는 모든 워크 로드를 마이그레이션할 수 있습니다. | 지원되지 않는 경우 확인이 실패합니다.
게스트 운영 체제 아키텍처 | 64비트. | 지원되지 않는 경우 확인이 실패합니다.
운영 체제 디스크 크기 | 최대 2,048GB. | 지원되지 않는 경우 확인이 실패합니다.
운영 체제 디스크 수 | 1 | 지원되지 않는 경우 확인이 실패합니다.
데이터 디스크 수 | 64개 이하. | 지원되지 않는 경우 확인이 실패합니다.
데이터 디스크 크기 | 최대 4,095GB | 지원되지 않는 경우 확인이 실패합니다.
네트워크 어댑터 | 여러 어댑터가 지원됩니다. | 
공유 VHD | 지원되지 않습니다. | 지원되지 않는 경우 확인이 실패합니다.
FC 디스크 | 지원되지 않습니다. | 지원되지 않는 경우 확인이 실패합니다.
BitLocker | 지원되지 않습니다. | 컴퓨터의 복제를 사용하도록 설정하기 전에 Bitlocker를 사용하지 않도록 설정해야 합니다. 
VM 이름 | 1~63자 사이입니다.<br/> 문자, 숫자 및 하이픈으로 제한됩니다.<br/><br/> 컴퓨터 이름은 문자 또는 숫자로 시작하고 끝나야 합니다. |  Site Recovery에서 컴퓨터 속성의 값을 업데이트합니다.
Windows 마이그레이션 후 연결 | 마이그레이션 후 Windows를 실행 하는 Azure Vm에 연결 합니다.<br/> -마이그레이션을 시작 하기 전에 온-프레미스 VM에서 RDP를 사용 하도록 설정 합니다. **공용** 프로필에 대한 TCP 및 UDP 규칙이 추가되었는지와 해당 RDP가 **Windows 방화벽** > **허용되는 앱**에서 모든 프로필에 대해 허용되는지 확인합니다.<br/> 사이트-사이트 VPN 액세스를 위한 RDP를 사용 하도록 설정 하 고에서 RDP를 허용 **Windows 방화벽** -> **허용 된 앱 및 기능** 에 대 한 **도메인 및 개인** 네트워크입니다. 또한 운영 체제의 SAN 정책이 설정 되어 있는지를 확인 **OnlineAll**합니다. [자세히 알아보기](https://support.microsoft.com/kb/3031135). | 
Linux 마이그레이션 후 연결 | SSH를 사용 하 여 마이그레이션 후 Azure Vm에 연결 합니다.<br/> 온-프레미스 컴퓨터에서 마이그레이션을 시작 하기 전에 보안 셸 서비스가 시작, 설정 되어 있는지 및 방화벽 규칙이 SSH 연결을 허용 하는지 확인 합니다.<br/> 장애 조치 후 Azure VM에서 장애 조치 된 VM에 네트워크 보안 그룹 규칙을 연결 된 Azure 서브넷에 대 한 SSH 포트로 들어오는 연결을 허용 합니다. 또한 VM에 대 한 공용 IP 주소를 추가 합니다. |  


## <a name="next-steps"></a>다음 단계

[VMware에 대 한 준비](tutorial-prepare-vmware.md) 평가 및 마이그레이션.








