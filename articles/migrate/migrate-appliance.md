---
title: Azure Migrate 어플라이언스
description: 서버 평가 및 마이그레이션에 사용 되는 Azure Migrate 어플라이언스에 대 한 개요를 제공 합니다.
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: 439f6d9c80a0b93f071d30d580facc4604cabbac
ms.sourcegitcommit: 31236e3de7f1933be246d1bfeb9a517644eacd61
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82780337"
---
# <a name="azure-migrate-appliance"></a>Azure Migrate 어플라이언스

이 문서에서는 Azure Migrate 어플라이언스에 대 한 필수 구성 요소 및 지원 요구 사항을 요약 합니다. 

## <a name="deployment-scenarios"></a>배포 시나리오

Azure Migrate 어플라이언스는 다음과 같은 시나리오에서 사용 됩니다.

**시나리오** | **도구** | **사용 대상** 
--- | --- | ---
**VMware VM 평가** | Azure Migrate: 서버 평가 | VMware Vm 검색<br/><br/> 컴퓨터 앱 및 종속성 검색<br/><br/> 평가에 대 한 컴퓨터 메타 데이터 및 성능 메타 데이터를 수집 합니다.
**VMware VM 에이전트 없는 마이그레이션** | Azure Migrate: 서버 마이그레이션 | VMware Vm 검색 <br/><br/> 에이전트 없는 마이그레이션을 사용 하 여 VMware Vm을 복제 합니다.
**Hyper-v VM 평가** | Azure Migrate: 서버 평가 | Hyper-v Vm 검색<br/><br/> 평가에 대 한 컴퓨터 메타 데이터 및 성능 메타 데이터를 수집 합니다.
**물리적 컴퓨터 평가** |  Azure Migrate: 서버 평가 |  물리적 서버 (또는 물리적 서버로 처리 하는 Vm)를 검색 합니다.<br/><br/> 평가에 대 한 컴퓨터 메타 데이터 및 성능 메타 데이터를 수집 합니다.

## <a name="deployment-methods"></a>배포 방법

다음 두 가지 방법을 사용 하 여 어플라이언스를 배포할 수 있습니다.

- VMware Vm 및 Hyper-v Vm (VMware 용 OVA 템플릿 또는 Hyper-v 용 VHD)에 대 한 템플릿을 사용 하 여 어플라이언스를 배포할 수 있습니다.
- 템플릿을 사용 하지 않으려는 경우 PowerShell 스크립트를 사용 하 여 VMware 또는 Hyper-v에 대 한 어플라이언스를 배포할 수 있습니다.
- Azure Government에서 스크립트를 사용 하 여 어플라이언스를 배포 해야 합니다.
- 물리적 서버의 경우 항상 스크립트를 사용 하 여 어플라이언스를 배포 합니다.
- 다운로드 링크는 아래 표에 나와 있습니다.


## <a name="appliance---vmware"></a>어플라이언스-VMware 

다음 표에는 VMware에 대 한 Azure Migrate 어플라이언스 요구 사항이 요약 되어 있습니다.

**요구 사항** | **VMware** 
--- | ---
**권한** | 어플라이언스 웹 앱에 로컬로 또는 원격으로 액세스 하려면, 도메인 관리자 또는 어플라이언스 컴퓨터의 로컬 관리자 여야 합니다.
**어플라이언스 구성 요소** | 어플라이언스의 구성 요소는 다음과 같습니다.<br/><br/> - **관리 앱**: 어플라이언스를 배포 하는 동안 사용자가 입력 하는 웹 앱입니다. Azure로 마이그레이션하기 위해 컴퓨터를 평가 하는 데 사용 됩니다.<br/> - **검색 에이전트**: 에이전트에서 컴퓨터 구성 데이터를 수집 합니다. Azure로 마이그레이션하기 위해 컴퓨터를 평가 하는 데 사용 됩니다.<br/>- **평가 에이전트**: 에이전트는 성능 데이터를 수집 합니다. Azure로 마이그레이션하기 위해 컴퓨터를 평가 하는 데 사용 됩니다.<br/>- **자동 업데이트 서비스**: 어플라이언스 구성 요소를 업데이트 합니다 (24 시간 마다 실행).<br/>- **DRA 에이전트**: VM 복제를 오케스트레이션 하 고 복제 된 컴퓨터와 Azure 간의 통신을 조정 합니다. 에이전트 없는 마이그레이션을 사용 하 여 Azure에 VMware Vm을 복제 하는 경우에만 사용 됩니다.<br/>- **게이트웨이**: 복제 된 데이터를 Azure로 보냅니다. 에이전트 없는 마이그레이션을 사용 하 여 Azure에 VMware Vm을 복제 하는 경우에만 사용 됩니다.
**지원 되는 배포** | OVA 템플릿을 사용 하 여 VMware VM으로 배포 합니다.<br/><br/> PowerShell 설치 스크립트를 사용 하 여 VMware VM 또는 물리적 컴퓨터로 배포 합니다.
**프로젝트 지원** |  어플라이언스는 단일 프로젝트에 연결할 수 있습니다. <br/> 모든 수의 어플라이언스를 단일 프로젝트에 연결할 수 있습니다.<br/> 
**검색 제한** | 어플라이언스는 vCenter Server에서 VMware Vm을 1만 개까지 검색할 수 있습니다.<br/> 어플라이언스는 단일 vCenter Server에 연결할 수 있습니다.
**OVA 템플릿** | 포털 또는에서 https://aka.ms/migrate/appliance/vmware다운로드 합니다.<br/><br/> 다운로드 크기는 11.2 GB입니다.<br/><br/> 다운로드 한 어플라이언스 템플릿에는 180 일 동안 유효한 Windows Server 2016 평가판 라이선스가 제공 됩니다. 평가 기간이 만료에 근접 한 경우 새 어플라이언스를 다운로드 하 여 배포 하거나 어플라이언스 VM의 운영 체제 라이선스를 활성화 하는 것이 좋습니다.
**PowerShell 스크립트** | 스크립트 [다운로드](https://go.microsoft.com/fwlink/?linkid=2105112).<br/><br/> 
**소프트웨어/하드웨어** |  어플라이언스는 Windows Server 2016, 32 GB RAM, 8 개 vCPUs, 80 g b의 디스크 저장소 및 외부 가상 스위치를 사용 하 여 컴퓨터에서 실행 해야 합니다.<br/> 어플라이언스는 직접 또는 프록시를 통해 인터넷에 액세스 해야 합니다.<br/><br/> VMware VM에서 어플라이언스를 실행 하는 경우 요구 사항에 맞는 VM을 할당 하는 데 충분 한 리소스가 vCenter Server에 있어야 합니다.<br/><br/> 물리적 컴퓨터에서 어플라이언스를 실행 하는 경우 Windows Server 2016를 실행 하 고 하드웨어 요구 사항을 충족 하는지 확인 합니다. 
**VMware 요구 사항** | 어플라이언스를 VMware VM으로 배포 하는 경우 버전 5.5 이상을 실행 하는 ESXi 호스트에 배포 해야 합니다.<br/><br/> 5.5, 6.0, 6.5 또는 6.7을 실행 하는 vCenter Server.
**VDDK (에이전트 없는 마이그레이션)** | 어플라이언스를 VMware VM으로 배포 하 고 에이전트 없는 마이그레이션을 실행 하는 경우 VMware vSphere VDDK을 어플라이언스 VM에 설치 해야 합니다.
**해시 값-OVA** | OVA 템플릿 해시 값을 [확인](tutorial-assess-vmware.md#verify-security) 합니다.
**해시 값-PowerShell 스크립트** | PowerShell 스크립트 해시 값을 [확인](deploy-appliance-script.md#verify-file-security) 합니다.




## <a name="appliance---hyper-v"></a>어플라이언스-Hyper-v

**요구 사항** | **Hyper-V** 
--- | ---
**권한** | 어플라이언스 웹 앱에 로컬로 또는 원격으로 액세스 하려면, 도메인 관리자 또는 어플라이언스 컴퓨터의 로컬 관리자 여야 합니다.
**어플라이언스 구성 요소** | 어플라이언스의 구성 요소는 다음과 같습니다.<br/><br/>- **관리 앱**: 어플라이언스를 배포 하는 동안 사용자가 입력 하는 웹 앱입니다. Azure로 마이그레이션하기 위해 컴퓨터를 평가 하는 데 사용 됩니다.<br/> - **검색 에이전트**: 에이전트에서 컴퓨터 구성 데이터를 수집 합니다. Azure로 마이그레이션하기 위해 컴퓨터를 평가 하는 데 사용 됩니다.<br/>- **평가 에이전트**: 에이전트는 성능 데이터를 수집 합니다. Azure로 마이그레이션하기 위해 컴퓨터를 평가 하는 데 사용 됩니다.<br/>- **자동 업데이트 서비스**: 어플라이언스 구성 요소를 업데이트 합니다 (24 시간 마다 실행).
**지원 되는 배포** | VHD 템플릿을 사용 하 여 Hyper-v VM으로 배포<br/><br/> PowerShell 설치 스크립트를 사용 하 여 Hyper-v VM 또는 물리적 컴퓨터로 배포 합니다.
**프로젝트 지원** |  어플라이언스는 단일 프로젝트에 연결할 수 있습니다. <br/> 모든 수의 어플라이언스를 단일 프로젝트에 연결할 수 있습니다.<br/> 
**검색 제한** | 어플라이언스는 Hyper-v Vm을 최대 5000 개까지 검색할 수 있습니다.<br/> 어플라이언스는 최대 300 Hyper-v 호스트에 연결할 수 있습니다.
**VHD 템플릿** | VHD를 포함 하는 압축 폴더. 포털 또는에서 https://aka.ms/migrate/appliance/hyperv다운로드 합니다.<br/><br/> 다운로드 크기는 10gb입니다.<br/><br/> 다운로드 한 어플라이언스 템플릿에는 180 일 동안 유효한 Windows Server 2016 평가판 라이선스가 제공 됩니다. 평가 기간이 만료에 근접 한 경우 새 어플라이언스를 다운로드 하 여 배포 하거나 어플라이언스 VM의 운영 체제 라이선스를 활성화 하는 것이 좋습니다.
**PowerShell 스크립트** | 스크립트 [다운로드](https://go.microsoft.com/fwlink/?linkid=2105112).<br/><br/> 
**소프트웨어/하드웨어***   |  어플라이언스는 Windows Server 2016, 32 GB RAM, 8 개 vCPUs, 80 g b의 디스크 저장소 및 외부 가상 스위치를 사용 하 여 컴퓨터에서 실행 해야 합니다.<br/> 어플라이언스는 고정 또는 동적 IP 주소가 필요 하며 직접 또는 프록시를 통해 인터넷에 액세스 해야 합니다.<br/><br/> 어플라이언스를 Hyper-v VM으로 실행 하는 경우 Hyper-v 호스트에 16gb RAM, 8 개 vCPUs, 80 GB의 저장소 공간 및 어플라이언스 VM에 대 한 외부 스위치를 할당 하는 데 충분 한 리소스가 필요 합니다.<br/><br/> 물리적 컴퓨터에서 어플라이언스를 실행 하는 경우 Windows Server 2016를 실행 하 고 하드웨어 요구 사항을 충족 하는지 확인 합니다. 
**Hyper-v 요구 사항** | VHD 템플릿으로 어플라이언스를 배포 하는 경우 Azure Migrate에서 제공 하는 어플라이언스 VM은 Hyper-v VM 버전 5.0입니다.<br/><br/> Hyper-v 호스트는 Windows Server 2012 R2 이상을 실행 해야 합니다. 
**해시 값-VHD** | VHD 템플릿 해시 값을 [확인](tutorial-assess-hyper-v.md#verify-security) 합니다.
**해시 값-PowerShell 스크립트** | PowerShell 스크립트 해시 값을 [확인](deploy-appliance-script.md#verify-file-security) 합니다.


## <a name="appliance---physical"></a>어플라이언스-물리적

**요구 사항** | **실제** 
--- | ---
**권한** | 어플라이언스 웹 앱에 로컬로 또는 원격으로 액세스 하려면, 도메인 관리자 또는 어플라이언스 컴퓨터의 로컬 관리자 여야 합니다.
**어플라이언스 구성 요소** | 어플라이언스의 구성 요소는 다음과 같습니다. <br/><br/> - **관리 앱**: 어플라이언스를 배포 하는 동안 사용자가 입력 하는 웹 앱입니다. Azure로 마이그레이션하기 위해 컴퓨터를 평가 하는 데 사용 됩니다.<br/> - **검색 에이전트**: 에이전트에서 컴퓨터 구성 데이터를 수집 합니다. Azure로 마이그레이션하기 위해 컴퓨터를 평가 하는 데 사용 됩니다.<br/>- **평가 에이전트**: 에이전트는 성능 데이터를 수집 합니다. Azure로 마이그레이션하기 위해 컴퓨터를 평가 하는 데 사용 됩니다.<br/>- **자동 업데이트 서비스**: 어플라이언스 구성 요소를 업데이트 합니다 (24 시간 마다 실행).
**지원 되는 배포** | PowerShell 설치 스크립트를 사용 하 여 전용 물리적 컴퓨터 또는 VM으로 배포 합니다. 스크립트는 포털에서 다운로드할 수 있습니다.
**프로젝트 지원** |  어플라이언스는 단일 프로젝트에 연결할 수 있습니다. <br/> 모든 수의 어플라이언스를 단일 프로젝트에 연결할 수 있습니다.<br/> 
**검색 제한** | 어플라이언스는 최대 250 대의 물리적 서버를 검색할 수 있습니다.
**PowerShell 스크립트** | 포털에서 zip 폴더에 스크립트 (AzureMigrateInstaller)를 다운로드 합니다. [자세한 정보를 알아보세요](tutorial-assess-physical.md#set-up-the-appliance). 또는 [직접 다운로드](https://go.microsoft.com/fwlink/?linkid=2105112)합니다.<br/><br/> 다운로드 크기는 59.7 MB입니다.
**소프트웨어/하드웨어** |  어플라이언스는 Windows Server 2016, 32 GB RAM, 8 개 vCPUs, 80 g b의 디스크 저장소 및 외부 가상 스위치를 사용 하 여 컴퓨터에서 실행 해야 합니다.<br/> 어플라이언스는 고정 또는 동적 IP 주소가 필요 하며 직접 또는 프록시를 통해 인터넷에 액세스 해야 합니다.<br/><br/> 물리적 컴퓨터에서 어플라이언스를 실행 하는 경우 Windows Server 2016를 실행 하 고 하드웨어 요구 사항을 충족 하는지 확인 합니다.<br/> Windows Server 2019를 사용 하는 컴퓨터에서 어플라이언스를 실행 하는 것은 지원 되지 않습니다.
**해시 값** | PowerShell 스크립트 해시 값을 [확인](deploy-appliance-script.md#verify-file-security) 합니다.

## <a name="url-access"></a>URL 액세스

Azure Migrate 어플라이언스를 인터넷에 연결 해야 합니다.

- 어플라이언스를 배포 하는 경우 필요한 Url에 대 한 연결 확인을 Azure Migrate 합니다.
- 목록의 모든 Url에 대 한 액세스를 허용 해야 합니다. 평가만 수행 하는 경우에는 VMware 에이전트 없는 마이그레이션에만 필요 하다 고 표시 된 Url을 건너뛸 수 있습니다.
-  URL 기반 프록시를 사용 하 여 인터넷에 연결 하는 경우 프록시가 Url을 조회 하는 동안 수신 된 CNAME 레코드를 확인 하는지 확인 합니다.

### <a name="public-cloud-urls"></a>공용 클라우드 Url

**URL** | **세부 정보**  
--- | --- |
*.portal.azure.com  | Azure Portal로 이동합니다.
*.windows.net <br/> *.msftauth.net <br/> *.msauth.net <br/> *.microsoft.com <br/> *.live.com | Azure 구독에 로그인합니다.
*.microsoftonline.com <br/> *.microsoftonline-p.com | 기기가 Azure Migrate와 통신할 수 있도록 AD (Azure Active Directory) 앱을 만듭니다.
management.azure.com | 어플라이언스에 대 한 Azure AD 앱을 만들어 Azure Migrate 서비스와 통신 합니다.
*.services.visualstudio.com | 내부 모니터링에 사용 되는 앱 로그를 업로드 합니다.
*.vault.azure.net | Azure Key Vault에서 비밀을 관리 합니다.
aka.ms/* | 즉, 링크에 대 한 액세스를 허용 합니다. Azure Migrate 어플라이언스 업데이트에 사용 됩니다.
download.microsoft.com/download | Microsoft 다운로드에서 다운로드할 수 있습니다.
\*.servicebus.windows.net | 어플라이언스와 Azure Migrate 서비스 간의 통신.
*.discoverysrv.windowsazure.com <br/> *.migration.windowsazure.com | Azure Migrate 서비스 Url에 연결 합니다.
\*.hypervrecoverymanager.windowsazure.com | **VMware 에이전트 없는 마이그레이션에 사용 됩니다.**<br/><br/> Azure Migrate 서비스 Url에 연결 합니다.
\*.blob.core.windows.net |  **VMware 에이전트 없는 마이그레이션에 사용 됩니다.**<br/><br/>마이그레이션을 위해 저장소에 데이터를 업로드 합니다.

### <a name="government-cloud-urls"></a>정부 클라우드 Url

**URL** | **세부 정보**  
--- | --- |
*.portal.azure.us  | Azure Portal로 이동합니다.
graph.windows.net | Azure 구독에 로그인합니다.
login.microsoftonline.us  | 기기가 Azure Migrate와 통신할 수 있도록 AD (Azure Active Directory) 앱을 만듭니다.
management.usgovcloudapi.net | 어플라이언스에 대 한 Azure AD 앱을 만들어 Azure Migrate 서비스와 통신 합니다.
dc.services.visualstudio.com | 내부 모니터링에 사용 되는 앱 로그를 업로드 합니다.
*. vault.usgovcloudapi.net | Azure Key Vault에서 비밀을 관리 합니다.
aka.ms/* | 즉, 링크에 대 한 액세스를 허용 합니다. Azure Migrate 어플라이언스 업데이트에 사용 됩니다.
download.microsoft.com/download | Microsoft 다운로드에서 다운로드할 수 있습니다.
*. servicebus.usgovcloudapi.net  | 어플라이언스와 Azure Migrate 서비스 간의 통신.
*. discoverysrv.windowsazure.us <br/> *. migration.windowsazure.us | Azure Migrate 서비스 Url에 연결 합니다.
*. hypervrecoverymanager.windowsazure.us | **VMware 에이전트 없는 마이그레이션에 사용 됩니다.**<br/><br/> Azure Migrate 서비스 Url에 연결 합니다.
*.blob.core.usgovcloudapi.net  |  **VMware 에이전트 없는 마이그레이션에 사용 됩니다.**<br/><br/>마이그레이션을 위해 저장소에 데이터를 업로드 합니다.
*. applicationinsights.us | 내부 모니터링에 사용 되는 앱 로그를 업로드 합니다.





## <a name="collected-data---vmware"></a>수집 된 데이터-VMware

어플라이언스는 메타 데이터, 성능 데이터 및 종속성 분석 데이터를 수집 합니다 (에이전트 없는 [종속성 분석이](concepts-dependency-visualization.md) 사용 되는 경우).

### <a name="metadata"></a>메타데이터

Azure Migrate 어플라이언스에서 검색 된 메타 데이터를 사용 하면 컴퓨터와 앱이 Azure로 마이그레이션할 준비가 되었는지 여부를 파악 하 고, 올바른 크기의 컴퓨터와 앱을 계획 하며, 비용을 계획 하 고, 응용 프로그램 종속성을 분석할 수 있습니다. Microsoft는 라이선스 규정 준수 감사에서이 데이터를 사용 하지 않습니다.

다음은 기기가 수집 하 여 Azure에 전송 하는 VMware VM 메타 데이터의 전체 목록입니다.

**데이터로** | **COUNTER**
--- | --- 
**컴퓨터 세부 정보** | 
VM ID | vm.Config.InstanceUuid 
VM 이름 | vm.Config.Name
vCenter Server ID | VMwareClient
VM 설명 | vm.Summary.Config.Annotation
라이선스 제품 이름 | vm.Client.ServiceContent.About.LicenseProductName
운영 체제 유형 | vm. 요약 GuestFullName
부팅 유형 | vm.Config.Firmware
코어 수 | vm.Config.Hardware.NumCPU
메모리(MB) | vm.Config.Hardware.MemoryMB
디스크 수 | vm. 구성 합니다.. FindAll (x =>는 VirtualDisk). count
디스크 크기 목록 | vm. 구성 합니다.. FindAll (x =>는 VirtualDisk)
네트워크 어댑터 목록 | vm. 구성 합니다.. FindAll (x =>는 VirtualEthernet). count
CPU 사용률 | cpu.usage.average
메모리 사용률 |mem.usage.average
**디스크당 정보** | 
디스크 키 값 | disk.Key
Dikunit 번호 | disk.UnitNumber
디스크 컨트롤러 키 값 | disk.ControllerKey.Value
프로비전된 기가바이트 | virtualDisk.DeviceInfo.Summary
디스크 이름 | 디스크를 사용 하 여 생성 된 값입니다. 단위 번호, 디스크. 키, 디스크. ControllerKey. 값
초당 읽기 작업 | virtualDisk.numberReadAveraged.average
초당 쓰기 작업 | virtualDisk.numberWriteAveraged.average
읽기 처리량 (초당 MB) | virtualDisk.read.average
쓰기 처리량 (초당 MB) | virtualDisk.write.average
**NIC 세부 정보** | 
네트워크 어댑터 이름 | nic.Key
MAC 주소 | ((VirtualEthernetCard)nic).MacAddress
IPv4 주소 | vm.Guest.Net
IPv6 주소 | vm.Guest.Net
읽기 처리량 (초당 MB) | net.received.average
쓰기 처리량 (초당 MB) | net.transmitted.average
**인벤토리 경로 세부 정보** | 
Name | container.GetType().Name
자식 개체 유형 | container.ChildType
참조 세부 정보 | container.MoRef
부모 세부 정보 | Container.Parent
VM 당 폴더 세부 정보 | ((Folder)container).ChildEntity.Type
VM 당 데이터 센터 정보 | ((Datacenter)container).VmFolder
호스트 폴더당 데이터 센터 정보 | ((Datacenter)container).HostFolder
호스트 당 클러스터 세부 정보 | ((ClusterComputeResource 컨테이너). 호스팅하기
VM 당 호스트 세부 정보 | (HostSystem) 컨테이너). VM

### <a name="performance-data"></a>성능 데이터


다음은 기기가 수집 하 여 Azure에 전송 하는 VMware VM 성능 데이터입니다.

**Data** | **카운터** | **영향 평가**
--- | --- | ---
CPU 사용률 | cpu.usage.average | 권장 되는 VM 크기/비용
메모리 사용률 | mem.usage.average | 권장 되는 VM 크기/비용
디스크 읽기 처리량 (초당 MB) | virtualDisk.read.average | 디스크 크기, 저장소 비용, VM 크기에 대 한 계산
디스크 쓰기 처리량 (초당 MB) | virtualDisk.write.average | 디스크 크기, 저장소 비용, VM 크기에 대 한 계산
초당 디스크 읽기 작업 수 | virtualDisk.numberReadAveraged.average | 디스크 크기, 저장소 비용, VM 크기에 대 한 계산
초당 디스크 쓰기 작업 수 | virtualDisk.numberWriteAveraged.average  | 디스크 크기, 저장소 비용, VM 크기에 대 한 계산
NIC 읽기 처리량 (초당 MB) | net.received.average | VM 크기에 대 한 계산
NIC 쓰기 처리량 (초당 MB) | net.transmitted.average  |VM 크기에 대 한 계산

### <a name="app-dependencies-metadata"></a>앱 종속성 메타 데이터

에이전트 없는 종속성 분석은 연결을 수집 하 고 데이터를 처리 합니다.

#### <a name="connection-data"></a>연결 데이터

다음은 에이전트 없는 종속성 분석에 사용 하도록 설정 된 각 VM에서 기기가 수집 하는 연결 데이터입니다. 이 데이터는 Azure에 전송 됩니다.

**Data** | **사용 되는 명령** 
--- | --- 
로컬 포트 | netstat
로컬 IP 주소 | netstat
원격 포트 | netstat
원격 IP 주소 | netstat
TCP 연결 상태 | netstat
프로세스 ID | netstat
아니요. 활성 연결 | netstat

#### <a name="process-data"></a>데이터 처리
다음은 에이전트 없는 종속성 분석에 사용 하도록 설정 된 각 VM에서 기기가 수집 하는 프로세스 데이터입니다. 이 데이터는 Azure에 전송 됩니다.

**Data** | **WMI 클래스(WMI class)** | **WMI 클래스 속성**
--- | --- | ---
프로세스 이름 | Win32_Process | ExecutablePath
프로세스 인수 | Win32_Process | 명령줄
애플리케이션 이름 | Win32_Process | ExecutablePath 속성의 VersionInfo 매개 변수

#### <a name="linux-vm-data"></a>Linux VM 데이터

다음은 에이전트 없는 종속성 분석에 사용 하도록 설정 된 각 Linux VM에서 기기가 수집 하는 연결 및 프로세스 데이터입니다. 이 데이터는 Azure에 전송 됩니다.

**Data** | **사용 되는 명령** 
--- | ---
로컬 포트 | netstat 
로컬 IP 주소 | netstat 
원격 포트 | netstat 
원격 IP 주소 | netstat 
TCP 연결 상태 | netstat 
아니요. 활성 연결 | netstat
프로세스 ID  | netstat 
프로세스 이름 | ps
프로세스 인수 | ps
애플리케이션 이름 | dpkg 또는 rpm



## <a name="collected-data---hyper-v"></a>수집 된 데이터-Hyper-v

어플라이언스는 메타 데이터, 성능 데이터 및 종속성 분석 데이터를 수집 합니다 (에이전트 없는 [종속성 분석이](concepts-dependency-visualization.md) 사용 되는 경우).

### <a name="metadata"></a>메타데이터
Azure Migrate 어플라이언스에서 검색 된 메타 데이터를 사용 하면 컴퓨터와 앱이 Azure로 마이그레이션할 준비가 되었는지 여부를 파악 하 고, 올바른 크기의 컴퓨터와 앱을 계획 하며, 비용을 계획 하 고, 응용 프로그램 종속성을 분석할 수 있습니다. Microsoft는 라이선스 규정 준수 감사에서이 데이터를 사용 하지 않습니다.

다음은 기기가 수집 하 여 Azure에 전송 하는 Hyper-v VM 메타 데이터의 전체 목록입니다.

**데이터로** | **WMI 클래스** | **WMI 클래스 속성**
--- | --- | ---
**컴퓨터 세부 정보** | 
BIOS의 일련 번호 _ Msvm_BIOSElement | BIOSSerialNumber
VM 유형 (Gen 1 또는 2) | Msvm_VirtualSystemSettingData | VirtualSystemSubType 형식
VM 표시 이름 | Msvm_VirtualSystemSettingData | ElementName
VM 버전 | Msvm_ProcessorSettingData | VirtualQuantity
메모리 (바이트) | Msvm_MemorySettingData | VirtualQuantity
VM에서 사용할 수 있는 최대 메모리 | Msvm_MemorySettingData | 제한
동적 메모리 사용 | Msvm_MemorySettingData | DynamicMemoryEnabled
운영 체제 이름/버전/FQDN | Msvm_KvpExchangeComponent | Guestintrinsicexchangeitems 확인 이름 데이터
VM 전원 상태 | Msvm_ComputerSystem | EnabledState
**디스크당 정보** | 
디스크 식별자 | Msvm_VirtualHardDiskSettingData | VirtualDiskId
가상 하드 디스크 유형 | Msvm_VirtualHardDiskSettingData | Type
가상 하드 디스크 크기 | Msvm_VirtualHardDiskSettingData | MaxInternalSize
가상 하드 디스크 부모 | Msvm_VirtualHardDiskSettingData | ParentPath
**NIC 세부 정보** | 
IP 주소 (가상 Nic) | Msvm_GuestNetworkAdapterConfiguration | IPAddresses
DHCP 사용 (가상 Nic) | Msvm_GuestNetworkAdapterConfiguration | DHCPEnabled
NIC ID (가상 Nic) | Msvm_SyntheticEthernetPortSettingData | InstanceID
NIC MAC 주소 (가상 Nic) | Msvm_SyntheticEthernetPortSettingData | 주소
NIC ID (레거시 Nic) | MsvmEmulatedEthernetPortSetting 데이터 | InstanceID
NIC MAC ID (레거시 Nic) | MsvmEmulatedEthernetPortSetting 데이터 | 주소

### <a name="performance-data"></a>성능 데이터

다음은 기기가 수집 하 여 Azure에 전송 하는 하이퍼 VM 성능 데이터입니다.

**성능 카운터 클래스** | **카운터** | **영향 평가**
--- | --- | ---
Hyper-v 하이퍼바이저 가상 프로세서 | % 게스트 실행 시간 | 권장 되는 VM 크기/비용
Hyper-v 동적 메모리 VM | 현재 압력 (%)<br/> 게스트 표시 실제 메모리 (MB) | 권장 되는 VM 크기/비용
Hyper-v 가상 저장 장치 | 읽은 바이트/초 | 디스크 크기, 저장소 비용, VM 크기에 대 한 계산
Hyper-v 가상 저장 장치 | 쓰기 바이트/초 | 디스크 크기, 저장소 비용, VM 크기에 대 한 계산
Hyper-V Virtual Network 어댑터 | 받은 바이트 수/초 | VM 크기에 대 한 계산
Hyper-V Virtual Network 어댑터 | 보낸 바이트/초 | VM 크기에 대 한 계산

- CPU 사용률은 VM에 연결 된 모든 가상 프로세서에 대 한 모든 사용량의 합계입니다.
- 메모리 사용률은 (현재 압력 * 게스트 표시 실제 메모리)/100입니다.
- 디스크 및 네트워크 사용률 값은 나열 된 Hyper-v 성능 카운터에서 수집 됩니다.


## <a name="collected-data---physical"></a>수집 된 데이터-실제

어플라이언스는 메타 데이터, 성능 데이터 및 종속성 분석 데이터를 수집 합니다 (에이전트 없는 [종속성 분석이](concepts-dependency-visualization.md) 사용 되는 경우).

### <a name="windows-metadata"></a>Windows 메타 데이터

Azure Migrate 어플라이언스에서 검색 된 메타 데이터를 사용 하면 컴퓨터와 앱이 Azure로 마이그레이션할 준비가 되었는지 여부를 파악 하 고, 올바른 크기의 컴퓨터와 앱을 계획 하며, 비용을 계획 하 고, 응용 프로그램 종속성을 분석할 수 있습니다. Microsoft는 라이선스 규정 준수 감사에서이 데이터를 사용 하지 않습니다.

다음은 기기가 수집 하 여 Azure에 전송 하는 Windows server 메타 데이터의 전체 목록입니다.

**데이터로** | **WMI 클래스** | **WMI 클래스 속성**
--- | --- | ---
FQDN | Win32_ComputerSystem | 도메인, 이름, PartOfDomain
프로세서 코어 수 | Win32_PRocessor | NumberOfCores
할당 된 메모리 | Win32_ComputerSystem | Total이상 메모리
BIOS 일련 번호 | Win32_ComputerSystemProduct | IdentifyingNumber
BIOS GUID | Win32_ComputerSystemProduct | UUID
부팅 유형 | Win32_DiskPartition | Type = **GPT: System** for EFI/BIOS를 사용 하는 파티션 확인
OS 이름 | Win32_OperatingSystem | 캡션
OS 버전 |Win32_OperatingSystem | 버전
OS 아키텍처 | Win32_OperatingSystem | OSArchitecture
디스크 수 | Win32_DiskDrive | 모델, 크기, DeviceID, 미디어 유형, 이름
디스크 크기 | Win32_DiskDrive | Size
NIC 목록 | Win32_NetworkAdapterConfiguration | 설명, 인덱스
NIC IP 주소 | Win32_NetworkAdapterConfiguration | IPAddress
NIC MAC 주소 | Win32_NetworkAdapterConfiguration | MACAddress

### <a name="linux-metadata"></a>Linux 메타 데이터

다음은 기기가 수집 하 여 Azure에 전송 하는 Linux 서버 메타 데이터의 전체 목록입니다.

**데이터로** | **용** 
--- | --- 
FQDN | cat/proc/sys/kernel/hostname, hostname-f
프로세서 코어 수 |  /proc/cpuinfo \| awk '/^ processor/{print $3} ' \| wc.exe-l
할당 된 메모리 | cat/proc/meminfo \| Grep memtotal \| awk ' {printf "% 0f에서", $2/1024} '
BIOS 일련 번호 | lshw \| grep "serial:" \| head-n1 \| awk ' {print $2} ' <br/> /usr/sbin/dmidecode-t 1 \| Grep ' 직렬 ' \| awk ' {$1 = ""; $2 = ""; print} '
BIOS GUID | cat/sys/class/dmi/id/product_uuid
부팅 유형 | [-d/sys/firmware/efi]  && echo efi \| \| echo BIOS
OS 이름/버전 | OS 버전 및 이름에 대 한 다음 파일에 액세스 합니다.<br/><br/> /etc/os-release<br/> /usr/lib/os-release <br/> /etc/enterprise-release <br/> /etc/redhat-release<br/> /etc/oracle-release<br/>  /etc/SuSE-release<br/>  /etc/lsb-release  <br/> /etc/debian_version
OS 아키텍처 | Uname-m
디스크 수 | fdisk-l \| Egrep ' Disk. * bytes ' \| awk ' {print $2} ' \| cut-f1-d ': '
부팅 디스크 | df/boot \| sed-n 2p \| awk ' {print $1} '
디스크 크기 | fdisk-l \| Egrep ' Disk. * bytes ' \| egrep $disk: \| awk ' {print $5} '
NIC 목록 | ip-o-4 addr show \| awk ' {print $2} '
NIC IP 주소 | ip 주소 show $nic \| grep inet \| awk ' {print $2} ' \| cut-f1-d "/" 
NIC MAC 주소 | ip 주소 show $nic \| grep 에테르 스코프 \| awk ' {print $2} '

### <a name="windows-performance-data"></a>Windows 성능 데이터

다음은 기기가 수집 하 여 Azure에 전송 하는 Windows server 성능 데이터입니다.

**Data** | **WMI 클래스(WMI class)** | **WMI 클래스 속성**
--- | --- | ---
CPU 사용량 | Win32_PerfFormattedData_PerfOS_Processor | PercentIdleTime
메모리 사용량 | Win32_PerfFormattedData_PerfOS_Memory | AvailableMBytes
NIC 수 | Win32_PerfFormattedData_Tcpip_NetworkInterface | 네트워크 장치 수를 가져옵니다.
NIC 당 받은 데이터 | Win32_PerfFormattedData_Tcpip_NetworkInterface  | BytesReceivedPerSec
NIC 당 전송 된 데이터 | BWin32_PerfFormattedData_Tcpip_NetworkInterface | BytesSentPersec
디스크 수 | BWin32_PerfFormattedData_PerfDisk_PhysicalDisk | 디스크 수
디스크 세부 정보 | Win32_PerfFormattedData_PerfDisk_PhysicalDisk | DiskWritesPerSec, Diskwritespersec, DiskReadsPerSec, Diskwritespersec

### <a name="linux-performance-data"></a>Linux 성능 데이터

다음은 기기가 수집 하 여 Azure에 전송 하는 Linux server 성능 데이터입니다.

**Data** | **Linux** 
--- | --- 
CPU 사용량 | cat/proc/st/| grep ' cpu '/proc/stat
메모리 사용량 | 무료 \| grep 메모리 \| awk ' {print $3/$ 2 * 100.0} '
NIC 수 | lshw-클래스 network \| grep eth [0-60] \| wc.exe-l
NIC 당 받은 데이터 | cat/sys/class/net/eth $ nic/statistics/rx_bytes
NIC 당 전송 된 데이터 | cat/sys/class/net/eth $ nic/statistics/tx_bytes
디스크 수 | fdisk-l \| Egrep ' Disk. * bytes ' \| awk ' {print $2} ' \| cut-f1-d ': '
디스크 세부 정보 | cat/proc/diskstats


## <a name="appliance-upgrades"></a>어플라이언스 업그레이드

어플라이언스를 실행 하는 Azure Migrate 에이전트가 업데이트 되 면 기기가 업그레이드 됩니다. 자동 업데이트는 기본적으로 어플라이언스에서 사용 되기 때문에 자동으로 발생 합니다. 이 기본 설정을 변경 하 여 어플라이언스 서비스를 수동으로 업데이트할 수 있습니다.

### <a name="turn-off-auto-update"></a>자동 업데이트 해제

1. 어플라이언스를 실행 하는 컴퓨터에서 레지스트리 편집기를 엽니다.
2. **HKEY_LOCAL_MACHINE \software\microsoft\azureappliance**로 이동 합니다.
3. 자동 업데이트를 해제 하려면 DWORD 값이 0 인 레지스트리 **키 자동 업데이트 키를** 만듭니다.

    ![레지스트리 키 설정](./media/migrate-appliance/registry-key.png)


### <a name="turn-on-auto-update"></a>자동 업데이트 설정

다음 방법 중 하나를 사용 하 여 자동 업데이트를 켤 수 있습니다.

- HKEY_LOCAL_MACHINE \SOFTWARE\Microsoft\AzureAppliance.에서 자동 업데이트 레지스트리 키를 삭제 하 여
- 검색이 완료 되 면 어플라이언스 Configuration Manager 합니다.

레지스트리 키를 삭제 하려면:

1. 어플라이언스를 실행 하는 컴퓨터에서 레지스트리 편집기를 엽니다.
2. **HKEY_LOCAL_MACHINE \software\microsoft\azureappliance**로 이동 합니다.
3. 자동 업데이트를 해제 하기 위해 이전에 만든 **레지스트리 키 자동**업데이트를 삭제 합니다.

어플라이언스 Configuration Manager에서 설정 하려면 검색이 완료 된 후 다음을 수행 합니다.

1. 어플라이언스 컴퓨터에서 어플라이언스 Configuration Manager를 엽니다.
2. **어플라이언스 서비스** > **Azure Migrate 구성 요소의 자동 업데이트가 꺼져**있습니다 .를 클릭 하 여 자동 업데이트를 설정 합니다.

    ![자동 업데이트 설정](./media/migrate-appliance/turn-on.png)

### <a name="check-the-appliance-services-version"></a>어플라이언스 서비스 버전 확인

다음 방법 중 하나를 사용 하 여 어플라이언스 서비스 버전을 확인할 수 있습니다.

- 어플라이언스 Configuration Manager에서 검색이 완료 된 후
- 어플라이언스 컴퓨터의 **제어판** > **프로그램 및 기능**에서

어플라이언스 Configuration Manager를 체크 인하려면:

1. 검색이 완료 되 면 어플라이언스 Configuration Manager (어플라이언스 웹 앱에서)를 엽니다.
2. **어플라이언스 서비스**에서 어플라이언스 서비스 버전을 확인 합니다.

    ![버전 확인](./media/migrate-appliance/version.png)

제어판을 체크 인하려면:

1. 어플라이언스에서**제어판** > 의**프로그램 및 기능** **시작** > 을 클릭 합니다.
2. 목록에서 어플라이언스 서비스 버전을 확인 합니다.

    ![제어판의 버전 확인](./media/migrate-appliance/programs-features.png)

### <a name="manually-update-an-older-version"></a>이전 버전 수동 업데이트

구성 요소에 대해 이전 버전을 실행 하는 경우 서비스를 제거 하 고 최신 버전으로 수동으로 업데이트 해야 합니다.

1. 최신 어플라이언스 서비스 버전을 확인 하려면 LatestComponents 파일을 [다운로드](https://aka.ms/latestapplianceservices) 합니다.
2.  다운로드 한 후 LatestComponents 파일을 메모장에서 엽니다.
3. 파일에서 최신 서비스 버전을 확인 하 고 다운로드 링크를 다운로드 합니다. 예를 들면 다음과 같습니다.

    "Name": "ASRMigrationWebApp", "DownloadLink": "https://download.microsoft.com/download/f/3/4/f34b2eb9-cc8d-4978-9ffb-17321ad9b7ed/MicrosoftAzureApplianceConfigurationManager.msi", "Version": "6.0.211.2", "Md5Hash": "e00a742acc35e78a64a6a81e75469b84"

4.  파일의 다운로드 링크를 사용 하 여 오래 된 서비스의 최신 버전을 다운로드 합니다.
5. 다운로드 한 후 관리자 명령 창에서 다음 명령을 실행 하 여 다운로드 한 MSI의 무결성을 확인 합니다.

    ``` C:\>Get-FileHash -Path <file_location> -Algorithm [Hashing Algorithm] ```예: C:\>CertUtil-hashfile C:\Users\public\downloads\MicrosoftAzureApplianceConfigurationManager.MSI MD5

5. 명령 출력이 파일의 서비스에 대 한 해시 값 항목과 일치 하는지 확인 합니다 (예: 위의 MD5 해시 값).
6. 이제 MSI를 실행 하 여 서비스를 설치 합니다. 자동 설치 이며 설치가 완료 되 면 설치 창이 닫힙니다.
7. 설치가 완료 되 면 **제어판** > **프로그램 및 기능**에서 서비스의 버전을 확인 합니다. 이제 서비스 버전을 json 파일에 표시 된 최신 버전으로 업그레이드 해야 합니다.



## <a name="next-steps"></a>다음 단계

- VMware에 대 한 어플라이언스를 설정 하 [는 방법을 알아봅니다](how-to-set-up-appliance-vmware.md) .
- Hyper-v에 대 한 어플라이언스를 설정 하 [는 방법을 알아봅니다](how-to-set-up-appliance-hyper-v.md) .
- 물리적 서버용 어플라이언스를 설정 하 [는 방법에 대해 알아봅니다](how-to-set-up-appliance-physical.md) .

