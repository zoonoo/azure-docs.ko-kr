---
title: Azure Migrate 어플라이언스
description: 서버 평가 및 마이그레이션에 사용 되는 Azure Migrate 어플라이언스에 대 한 개요를 제공 합니다.
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: 1b1e35c3b7a9d98e57ec4261f6f913c370bbb365
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/25/2020
ms.locfileid: "77597544"
---
# <a name="azure-migrate-appliance"></a>Azure Migrate 어플라이언스

이 문서에서는 Azure Migrate 어플라이언스를 설명 합니다. [Azure Migrate: 서버 평가](migrate-services-overview.md#azure-migrate-server-assessment-tool) 도구를 사용 하 여 Microsoft Azure 마이그레이션하기 위한 앱, 인프라 및 워크 로드를 검색 하 고 평가 하는 경우 어플라이언스를 배포 합니다. 어플라이언스는 VMware Vm을 [에이전트 없이](server-migrate-overview.md)마이그레이션할 때 [서버 평가를 Azure Migrate](migrate-services-overview.md#azure-migrate-server-migration-tool) 사용 하 여 Azure로 마이그레이션할 때에도 사용 됩니다.

## <a name="appliance-overview"></a>어플라이언스 개요

Azure Migrate 어플라이언스는 다음과 같은 시나리오에서 사용 됩니다.

**시나리오** | **도구** | **용도** 
--- | --- | ---
VMware VM | Azure Migrate: 서버 평가<br/><br/> Azure Migrate: 서버 마이그레이션 | VMware Vm 검색<br/><br/> 컴퓨터 앱 및 종속성 검색<br/><br/> 평가에 대 한 컴퓨터 메타 데이터 및 성능 메타 데이터를 수집 합니다.<br/><br/> 에이전트 없는 마이그레이션을 사용 하 여 VMware Vm을 복제 합니다.
Hyper-V VM | Azure Migrate: 서버 평가 | Hyper-v Vm 검색<br/><br/> 평가에 대 한 컴퓨터 메타 데이터 및 성능 메타 데이터를 수집 합니다.
물리적 머신 |  Azure Migrate: 서버 평가 |  물리적 서버 검색<br/><br/> 평가에 대 한 컴퓨터 메타 데이터 및 성능 메타 데이터를 수집 합니다.

## <a name="appliance---vmware"></a>어플라이언스-VMware 

**요구 사항** | **VMware** 
--- | ---
**다운로드 형식** | . OVA 
**다운로드 링크** | https://aka.ms/migrate/appliance/vmware 
**다운로드 크기** | 11.2 GB
**라이선스** | 다운로드 한 어플라이언스 템플릿에는 180 일 동안 유효한 Windows Server 2016 평가판 라이선스가 제공 됩니다. 평가 기간이 만료에 근접 한 경우 새 어플라이언스를 다운로드 하 여 배포 하거나 어플라이언스 VM의 운영 체제 라이선스를 활성화 하는 것이 좋습니다.
**배포** | 어플라이언스를 VMware VM으로 배포 합니다. 32 GB RAM, 8 개 vCPUs, 80 GB의 디스크 저장소 및 외부 가상 스위치를 사용 하 여 VM을 할당 하려면 vCenter Server에 충분 한 리소스가 필요 합니다.<br/> 어플라이언스는 직접 또는 프록시를 통해 인터넷에 액세스 해야 합니다.<br/> 어플라이언스는 단일 vCenter Server에 연결할 수 있습니다.
**하드웨어** | VCenter의 리소스를 사용 하 여 32 GB RAM 8 vCPUs, 80 GB의 디스크 저장소, 외부 가상 스위치를 사용 하 여 VM을 할당 합니다. 
**해시 값** | [여기](https://docs.microsoft.com/azure/migrate/tutorial-assess-vmware#verify-security) 를 확인 하세요.
**vCenter 서버/호스트** | 어플라이언스 VM은 버전 5.5 이상을 실행 하는 ESXi 호스트에 배포 해야 합니다.<br/><br/> 5\.5, 6.0, 6.5 또는 6.7을 실행 하는 vCenter Server.
**Azure Migrate 프로젝트** | 어플라이언스는 단일 프로젝트에 연결할 수 있습니다. <br/> 모든 수의 어플라이언스를 단일 프로젝트에 연결할 수 있습니다.<br/> 
**조사** | 어플라이언스는 vCenter Server에서 VMware Vm을 1만 개까지 검색할 수 있습니다.<br/> 어플라이언스는 단일 vCenter Server에 연결할 수 있습니다.
**어플라이언스 구성 요소** | 관리 앱: 배포 중 사용자 입력에 대 한 어플라이언스의 웹 앱입니다.<br/> 검색 에이전트: 컴퓨터 구성 데이터를 수집 합니다.<br/> 평가 에이전트: 성능 데이터를 수집 합니다.<br/> DRA: VM 복제를 오케스트레이션 하 고 컴퓨터/Azure 간의 통신을 조정 합니다.<br/> 게이트웨이: 복제 된 데이터를 Azure로 보냅니다.<br/> 자동 업데이트 서비스: 구성 요소를 업데이트 합니다 (24 시간 마다 실행).
**VDDK (에이전트 없는 마이그레이션)** | Azure Migrate Server Migration을 사용 하 여 에이전트 없는 마이그레이션을 실행 하는 경우 VMware vSphere VDDK을 어플라이언스 VM에 설치 해야 합니다.


## <a name="appliance---hyper-v"></a>어플라이언스-Hyper-v

**요구 사항** | **Hyper-V** 
--- | ---
**다운로드 형식** | Zip 폴더 (VHD 포함)
**다운로드 링크** | https://aka.ms/migrate/appliance/hyperv 
**다운로드 크기** | 10 GB
**라이선스** | 다운로드 한 어플라이언스 템플릿에는 180 일 동안 유효한 Windows Server 2016 평가판 라이선스가 제공 됩니다. 평가 기간이 만료에 근접 한 경우 새 어플라이언스를 다운로드 하 여 배포 하거나 어플라이언스 VM의 운영 체제 라이선스를 활성화 하는 것이 좋습니다.
**어플라이언스 배포**   |  어플라이언스를 Hyper-v VM으로 배포 합니다.<br/> Azure Migrate에서 제공 하는 어플라이언스 VM은 Hyper-v VM 버전 5.0입니다.<br/> Hyper-v 호스트는 Windows Server 2012 R2 이상을 실행 해야 합니다.<br/> 호스트는 16gb RAM, 8 개 vCPUs, 80 GB의 저장소 공간 및 어플라이언스 VM에 대 한 외부 스위치를 할당 하는 데 충분 한 공간이 필요 합니다.<br/> 어플라이언스에는 정적 또는 동적 IP 주소와 인터넷 액세스가 필요 합니다.
**하드웨어** | Hyper-v 호스트의 리소스-16gb RAM, 8 개 vCPUs, 80 GB의 저장소 공간 및 어플라이언스 VM에 대 한 외부 스위치를 할당 합니다.
**해시 값** | [여기](https://docs.microsoft.com/azure/migrate/tutorial-assess-hyper-v#verify-security) 를 확인 하세요.
**Hyper-V 호스트** | Windows Server 2012 R2 이상 버전을 실행 하 고 있습니다.
**Azure Migrate 프로젝트** | 어플라이언스는 단일 프로젝트에 연결할 수 있습니다. <br/> 모든 수의 어플라이언스를 단일 프로젝트에 연결할 수 있습니다.<br/> 
**조사** | 어플라이언스는 Hyper-v Vm을 최대 5000 개까지 검색할 수 있습니다.<br/> 어플라이언스는 최대 300 Hyper-v 호스트에 연결할 수 있습니다.
**어플라이언스 구성 요소** | 관리 앱: 배포 중 사용자 입력에 대 한 어플라이언스의 웹 앱입니다.<br/> 검색 에이전트: 컴퓨터 구성 데이터를 수집 합니다.<br/> 평가 에이전트: 성능 데이터를 수집 합니다.<br/>  자동 업데이트 서비스: 구성 요소를 업데이트 합니다 (24 시간 마다 실행).


## <a name="appliance---physical"></a>어플라이언스-물리적

**요구 사항** | **실제** 
--- | ---
**다운로드 형식** | 압축 폴더 (PowerShell 기반 설치 관리자 스크립트 사용)
**다운로드 링크** | [다운로드 링크](https://go.microsoft.com/fwlink/?linkid=2105112)
**다운로드 크기** | 59.7 M B
**하드웨어** | 전용 물리적 컴퓨터 또는 가상 컴퓨터를 사용 합니다. 어플라이언스를 실행 하는 컴퓨터는 16gb RAM, 8 개 vCPUs, 80 GB의 저장소 공간 및 외부 스위치를 요구 합니다.<br/> 어플라이언스에는 정적 또는 동적 IP 주소와 인터넷 액세스가 필요 합니다.
**해시 값** | [여기](https://docs.microsoft.com/azure/migrate/tutorial-assess-physical#verify-security) 를 확인 하세요.
**운영 체제** | 어플라이언스 컴퓨터에서 Windows Server 2016를 실행 해야 합니다. 
**어플라이언스 배포**   |  어플라이언스 설치 관리자 스크립트는 포털 (zip 폴더)에서 다운로드 됩니다. <br/> 폴더의 압축을 풀고 PowerShell 스크립트 (AzureMigrateInstaller)를 실행 합니다.
**조사** | 어플라이언스는 최대 250 대의 물리적 서버를 검색할 수 있습니다.
**어플라이언스 구성 요소** | 관리 앱: 배포 중 사용자 입력에 대 한 어플라이언스의 웹 앱입니다.<br/> 검색 에이전트: 컴퓨터 구성 데이터를 수집 합니다.<br/> 평가 에이전트: 성능 데이터를 수집 합니다.<br/>  자동 업데이트 서비스: 구성 요소를 업데이트 합니다 (24 시간 마다 실행).


## <a name="url-access"></a>URL 액세스

Azure Migrate 어플라이언스를 인터넷에 연결 해야 합니다.

- 어플라이언스를 배포 하는 경우 Azure Migrate 아래 표에 요약 된 Url에 대 한 연결 확인을 수행 합니다.
- URL 기반 프록시를 사용 하 여 인터넷에 연결 하는 경우 이러한 Url에 대 한 액세스를 허용 하 여 프록시가 Url을 조회 하는 동안 수신 된 CNAME 레코드를 확인 하 게 합니다.

**URL** | **세부 정보**  
--- | --- |
*.portal.azure.com  | Azure Portal로 이동합니다.
*.windows.net <br/> *.msftauth.net <br/> *.msauth.net <br/> *.microsoft.com <br/> *.live.com | Azure 구독에 로그인합니다.
*.microsoftonline.com <br/> *.microsoftonline-p.com | 기기가 Azure Migrate와 통신할 수 있도록 Active Directory 앱을 만듭니다.
management.azure.com | 어플라이언스에 대 한 Active Directory 앱을 만들어 Azure Migrate 서비스와 통신 합니다.
dc.services.visualstudio.com | 내부 모니터링에 사용 되는 앱 로그를 업로드 합니다.
*.vault.azure.net | Azure Key Vault에서 비밀을 관리 합니다.
aka.ms/* | 즉, 링크에 대 한 액세스를 허용 합니다. Azure Migrate 어플라이언스 업데이트에 사용 됩니다.
download.microsoft.com/download | Microsoft 다운로드에서 다운로드할 수 있습니다.
\*.servicebus.windows.net | 어플라이언스와 Azure Migrate 서비스 간의 통신.
*.discoverysrv.windowsazure.com <br/> *.migration.windowsazure.com | Azure Migrate 서비스 Url에 연결 합니다.
\*.hypervrecoverymanager.windowsazure.com | **VMware 에이전트 없는 마이그레이션에 사용 됩니다.**<br/><br/> Azure Migrate 서비스 Url에 연결 합니다.
\*.blob.core.windows.net |  **VMware 에이전트 없는 마이그레이션에 사용 됩니다.**<br/><br/>마이그레이션을 위해 저장소에 데이터를 업로드 합니다.




## <a name="collected-data---vmware"></a>수집 된 데이터-VMware

### <a name="collected-performance-data-vmware"></a>수집 된 성능 데이터-VMware

다음은 기기가 수집 하 여 Azure에 전송 하는 VMware VM 성능 데이터입니다.

**Data** | **카운터** | **평가 영향**
--- | --- | ---
CPU 사용률 | cpu.usage.average | 권장 되는 VM 크기/비용
메모리 사용률 | mem.usage.average | 권장 되는 VM 크기/비용
디스크 읽기 처리량 (초당 MB) | virtualDisk.read.average | 디스크 크기, 저장소 비용, VM 크기에 대 한 계산
디스크 쓰기 처리량 (초당 MB) | virtualDisk.write.average | 디스크 크기, 저장소 비용, VM 크기에 대 한 계산
초당 디스크 읽기 작업 수 | virtualDisk.numberReadAveraged.average | 디스크 크기, 저장소 비용, VM 크기에 대 한 계산
초당 디스크 쓰기 작업 수 | virtualDisk.numberWriteAveraged.average  | 디스크 크기, 저장소 비용, VM 크기에 대 한 계산
NIC 읽기 처리량 (초당 MB) | net.received.average | VM 크기에 대 한 계산
NIC 쓰기 처리량 (초당 MB) | net.transmitted.average  |VM 크기에 대 한 계산


### <a name="collected-metadata-vmware"></a>수집 된 메타 데이터-VMware

> [!NOTE]
> Azure Migrate 어플라이언스에서 검색 된 메타 데이터는 Azure로 마이그레이션할 때 응용 프로그램을 적절 하 게 크기를 조정 하 고, Azure 적합성 분석, 응용 프로그램 종속성 분석 및 비용 계획을 수행 하는 데 사용 됩니다. Microsoft에서는 모든 라이센스 규정 준수 감사와 관련하여 이 데이터를 사용하지 않습니다.

다음은 기기가 수집 하 여 Azure에 전송 하는 VMware VM 메타 데이터의 전체 목록입니다.

**Data** | **카운터**
--- | --- 
**컴퓨터 세부 정보** | 
VM ID | vm.Config.InstanceUuid 
VM 이름 | vm.Config.Name
vCenter Server ID | VMwareClient.Instance.Uuid
VM 설명 | vm.Summary.Config.Annotation
라이선스 제품 이름 | vm.Client.ServiceContent.About.LicenseProductName
운영 체제 유형 | vm.SummaryConfig.GuestFullName
부팅 유형 | vm.Config.Firmware
코어 수 | vm.Config.Hardware.NumCPU
메모리(MB) | vm.Config.Hardware.MemoryMB
디스크 수 | vm. 구성 합니다. FindAll (x = >는 VirtualDisk). count
디스크 크기 목록 | vm. 구성 합니다. FindAll (x = >는 VirtualDisk)
네트워크 어댑터 목록 | vm. 구성 합니다. FindAll (x = >는 VirtualEthernet). count
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
속성 | container.GetType().Name
자식 개체 유형 | container.ChildType
참조 세부 정보 | container.MoRef
부모 세부 정보 | Container.Parent
VM 당 폴더 세부 정보 | ((Folder)container).ChildEntity.Type
VM 당 데이터 센터 정보 | ((Datacenter)container).VmFolder
호스트 폴더당 데이터 센터 정보 | ((Datacenter)container).HostFolder
호스트 당 클러스터 세부 정보 | ((ClusterComputeResource)container).Host
VM 당 호스트 세부 정보 | (HostSystem) 컨테이너). VM

## <a name="collected-data---hyper-v"></a>수집 된 데이터-Hyper-v

### <a name="collected-performance-data-hyper-v"></a>수집 된 성능 데이터-Hyper-v

> [!NOTE]
> Azure Migrate 어플라이언스에서 검색 된 메타 데이터는 Azure로 마이그레이션할 때 응용 프로그램을 적절 하 게 크기를 조정 하 고, Azure 적합성 분석, 응용 프로그램 종속성 분석 및 비용 계획을 수행 하는 데 사용 됩니다. Microsoft에서는 모든 라이센스 규정 준수 감사와 관련하여 이 데이터를 사용하지 않습니다.

다음은 기기가 수집 하 여 Azure에 전송 하는 하이퍼 VM 성능 데이터입니다.

**성능 카운터 클래스** | **카운터** | **평가 영향**
--- | --- | ---
Hyper-v 하이퍼바이저 가상 프로세서 | % 게스트 실행 시간 | 권장 되는 VM 크기/비용
Hyper-v 동적 메모리 VM | 현재 압력 (%)<br/> 게스트 표시 실제 메모리 (MB) | 권장 되는 VM 크기/비용
Hyper-v 가상 저장 장치 | 읽은 바이트/초 | 디스크 크기, 저장소 비용, VM 크기에 대 한 계산
Hyper-v 가상 저장 장치 | 쓰기 바이트/초 | 디스크 크기, 저장소 비용, VM 크기에 대 한 계산
Hyper-V 가상 네트워크 어댑터 | 받은 바이트 수/초 | VM 크기에 대 한 계산
Hyper-V 가상 네트워크 어댑터 | 보낸 바이트/초 | VM 크기에 대 한 계산

- CPU 사용률은 VM에 연결 된 모든 가상 프로세서에 대 한 모든 사용량의 합계입니다.
- 메모리 사용률은 (현재 압력 * 게스트 표시 실제 메모리)/100입니다.
- 디스크 및 네트워크 사용률 값은 나열 된 Hyper-v 성능 카운터에서 수집 됩니다.

### <a name="collected-metadata-hyper-v"></a>수집 된 메타 데이터-Hyper-v

다음은 기기가 수집 하 여 Azure에 전송 하는 Hyper-v VM 메타 데이터의 전체 목록입니다.

**Data** | **WMI 클래스** | **WMI 클래스 속성**
--- | --- | ---
**컴퓨터 세부 정보** | 
BIOS의 일련 번호 _ Msvm_BIOSElement | BIOSSerialNumber
VM 유형 (Gen 1 또는 2) | Msvm_VirtualSystemSettingData | VirtualSystemSubType
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




## <a name="discovery-and-collection-process"></a>검색 및 수집 프로세스

어플라이언스는 다음 프로세스를 사용 하 여 vCenter 서버 및 Hyper-v 호스트/클러스터와 통신 합니다.

1. **검색 시작**:
    - Hyper-v 어플라이언스에서 검색을 시작 하면 WinRM 포트 5985 (HTTP) 및 5986 (HTTPS)에서 Hyper-v 호스트와 통신 합니다.
    - VMware 어플라이언스에서 검색을 시작 하면 기본적으로 TCP 포트 443의 vCenter server와 통신 합니다. VCenter server가 다른 포트에서 수신 대기 하는 경우 어플라이언스 웹 앱에서 구성할 수 있습니다.
2. **메타 데이터 및 성능 데이터를 수집**합니다.
    - 어플라이언스는 CIM(Common Information Model) (CIM) 세션을 사용 하 여 hyper-v 호스트에서 5985 및 5986 포트의 hyper-v VM 데이터를 수집 합니다.
    - 어플라이언스는 기본적으로 포트 443와 통신 하 여 vCenter Server에서 VMware VM 데이터를 수집 합니다.
3. **데이터 보내기**: 어플라이언스는 Azure Migrate 서버 평가를 위해 수집 된 데이터를 보내고 SSL 포트 443을 통해 서버 마이그레이션을 Azure Migrate 합니다. 어플라이언스는 인터넷을 통해 Azure에 연결 하거나 공용/Microsoft 피어 링과 함께 Express 경로를 사용할 수 있습니다.
    - 성능 데이터의 경우 어플라이언스는 실시간 사용률 데이터를 수집 합니다.
        - 성능 데이터는 각 성능 메트릭에 대해 VMware에 대해 20 초 마다 수집 되 고 Hyper-v의 경우 30 초 마다 수집 됩니다.
        - 수집 된 데이터는 10 분 동안 단일 데이터 요소를 만들기 위해 롤업 됩니다.
        - 최고 사용률 값은 모든 20/30 초 데이터 요소에서 선택 되 고 평가 계산을 위해 Azure로 전송 됩니다.
        - 평가 속성에 지정 된 백분위 수 값 (50 번째/90 번째/95 번째/99 초)에 따라 10 분 지점은 오름차순으로 정렬 되 고 적절 한 백분위 수 값은 평가를 계산 하는 데 사용 됩니다.
    - 서버 마이그레이션의 경우 어플라이언스는 VM 데이터 수집을 시작 하 여 Azure에 복제 합니다.
4. **평가 및 마이그레이션**: 이제 Azure Migrate Server 평가를 사용 하 여 어플라이언스에서 수집한 메타 데이터에서 평가를 만들 수 있습니다. 또한 Azure Migrate 서버 마이그레이션을 사용 하 여 VMware Vm 마이그레이션을 시작 하 여 에이전트 없는 VM 복제를 오케스트레이션 할 수도 있습니다.


![Architecture](./media/migrate-appliance/architecture.png)


## <a name="appliance-upgrades"></a>어플라이언스 업그레이드

어플라이언스를 실행 하는 Azure Migrate 에이전트가 업데이트 되 면 기기가 업그레이드 됩니다.

- 이는 기본적으로 어플라이언스에서 자동 업데이트를 사용 하기 때문에 자동으로 발생 합니다.
- 이 기본 설정을 변경 하 여 에이전트를 수동으로 업데이트할 수 있습니다.
- 자동 업데이트를 사용 하지 않도록 설정 하려면 레지스트리 편집기 > HKEY_LOCAL_MACHINE \SOFTWARE\Microsoft\AzureAppliance로 이동 하 여 레지스트리 키 ("자동 업데이트"를 0 (DWORD)로 설정 합니다.
 
### <a name="set-agent-updates-to-manual"></a>에이전트 업데이트를 수동으로 설정

수동 업데이트의 경우 어플라이언스의 각 오래 된 에이전트에 대 한 **업데이트** 단추를 사용 하 여 어플라이언스의 모든 에이전트를 동시에 업데이트 해야 합니다. 언제 든 지 업데이트 설정을 다시 자동 업데이트로 전환할 수 있습니다.

## <a name="next-steps"></a>다음 단계

VMware에 대 한 어플라이언스를 설정 하 [는 방법을 알아봅니다](tutorial-assess-vmware.md#set-up-the-appliance-vm) .
Hyper-v에 대 한 어플라이언스를 설정 하 [는 방법을 알아봅니다](tutorial-assess-hyper-v.md#set-up-the-appliance-vm) .

