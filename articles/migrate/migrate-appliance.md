---
title: Azure Migrate 어플라이언스 아키텍처 | Microsoft Docs
description: Azure Migrate 어플라이언스에 대 한 개요를 제공합니다.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/04/2019
ms.author: raynew
ms.openlocfilehash: c2c9ca3082aa9c2067a63f8d6304e8a229dac14a
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811454"
---
# <a name="azure-migrate-appliance"></a>Azure Migrate 어플라이언스

이 문서에서는 Azure Migrate 어플라이언스를 설명 합니다. Azure Migrate 평가 및 마이그레이션 도구를 사용 하 여 검색, 평가 및 Microsoft Azure에 앱, 인프라 및 워크 로드를 마이그레이션할 때 어플라이언스를 배포 합니다. 

[Azure Migrate](migrate-services-overview.md) 검색, 평가 및 온-프레미스 앱 및 워크 로드 및 사설/공용 클라우드 Vm에서 Azure로 마이그레이션을 추적 하는 중앙 허브를 제공 합니다. 허브는 평가 및 마이그레이션 뿐만 아니라 타사 독립 소프트웨어 공급 업체 (ISV) 제품에 대 한 Azure Migrate 도구를 제공합니다.



## <a name="appliance-overview"></a>어플라이언스 개요

Azure Migrate 어플라이언스 형식 및 사용은 다음과 같습니다.

**로 배포** | **사용 대상** | **세부 정보**
--- | --- |  ---
VMware VM | Azure Migrate 평가 도구를 사용 하 여 VMware VM 평가 합니다.<br/><br/> Azure 마이그레이션 Server 마이그레이션 도구를 사용 하 여 VMware VM 에이전트 없는 마이그레이션 | OVA 템플릿을 다운로드 하 고 vCenter VM 어플라이언스를 만들려면 서버에 가져옵니다.
Hyper-V VM | Azure Migrate 평가 도구를 사용 하 여 Hyper-v VM 평가 합니다. | 압축 된 VHD를 다운로드 하 고 VM 어플라이언스를 만들려면 Hyper-v로 가져옵니다.

## <a name="appliance-access"></a>어플라이언스에 대 한 액세스

어플라이언스를 구성한 후에 TCP 포트 3389 통해 어플라이언스 VM을 원격으로 액세스할 수 있습니다. URL 사용 하 여 44368 포트로 어플라이언스에 대 한 웹 관리 앱을 원격으로 액세스할 수 있습니다: ``` https://<appliance-ip-or-name>:44368 ```합니다.

## <a name="appliance-license"></a>어플라이언스 라이선스
어플라이언스 180 일 동안 유효 하는 Windows Server 2016 평가판 라이선스를 함께 제공 됩니다. 평가 기간이 만료에 가까운 경우에 어플라이언스 VM의 운영 체제 라이선스를 활성화 하는 하거나에 다운로드 하 고 새 어플라이언스를 배포 하는 것이 좋습니다.

## <a name="appliance-agents"></a>어플라이언스 에이전트
기기에 이러한 에이전트를 설치 합니다.

**에이전트** | **세부 정보**
--- | ---
검색 에이전트 | 온-프레미스 Vm에서 구성 데이터를 수집합니다.
평가 에이전트 | VM 성능 데이터를 수집 하도록 온-프레미스 환경을 프로필을 만듭니다.
마이그레이션 어댑터 | VM 복제를 오케스트레이션 하 고 Vm 및 Azure 간의 통신을 조정 합니다.
마이그레이션 게이트웨이 | Azure에 복제 된 VM 데이터를 보냅니다.


## <a name="appliance-deployment-requirements"></a>어플라이언스 배포 요구 사항

- [검토](migrate-support-matrix-vmware.md#assessment-appliance-requirements) VMware 기기와 기기에 액세스 해야 하는 Url에 대 한 배포 요구 사항입니다.
- [검토](migrate-support-matrix-hyper-v.md#assessment-appliance-requirements) Hyper-v 기기와 기기에 액세스 해야 하는 Url에 대 한 배포 요구 사항입니다.


## <a name="collected-performance-data-vmware"></a>수집 된 성능 데이터-VMware

어플라이언스 수집 하 고 Azure에 전송 하는 VMware VM 성능 데이터는 다음과 같습니다.

**데이터** | **카운터** | **영향 평가**
--- | --- | ---
CPU 사용량 | cpu.usage.average | 권장 VM 크기/비용
메모리 사용률 | mem.usage.average | 권장 VM 크기/비용
디스크 읽기 처리량 (초당 MB) | virtualDisk.read.average | 디스크 크기, 저장소 비용, VM 크기에 대 한 계산
디스크 쓰기 처리량 (초당 MB) | virtualDisk.write.average | 디스크 크기, 저장소 비용, VM 크기에 대 한 계산
디스크 읽기 작업 / 초 | virtualDisk.numberReadAveraged.average | 디스크 크기, 저장소 비용, VM 크기에 대 한 계산
디스크 쓰기 작업 / 초 | virtualDisk.numberWriteAveraged.average  | 디스크 크기, 저장소 비용, VM 크기에 대 한 계산
NIC 읽기 처리량 (초당 MB) | net.received.average | VM 크기에 대 한 계산
NIC 쓰기 처리량 (초당 MB) | net.transmitted.average  |VM 크기에 대 한 계산


## <a name="collected-metadata-vmware"></a>수집 된 메타 데이터-VMware

어플라이언스 수집 하 고 Azure에 전송 하는 VMware VM 메타 데이터의 전체 목록은 다음과 같습니다.

**데이터** | **카운터**
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
메모리 (MB) | vm.Config.Hardware.MemoryMB
디스크 수 | vm입니다. Config.Hardware.Device.ToList() 합니다. FindAll(x => is VirtualDisk).count
디스크 크기 목록 | vm입니다. Config.Hardware.Device.ToList() 합니다. FindAll (x = > VirtualDisk 됩니다)
네트워크 어댑터 목록 | vm입니다. Config.Hardware.Device.ToList() 합니다. FindAll(x => is VirtualEthernet).count
CPU 사용량 | cpu.usage.average
메모리 사용률 |mem.usage.average
**디스크 세부 정보 당** | 
디스크 키 값 | disk.Key
Dikunit 수 | disk.UnitNumber
디스크 컨트롤러 키 값 | disk.ControllerKey.Value
프로비전된 기가바이트 | virtualDisk.DeviceInfo.Summary
디스크 이름 | 디스크를 사용 하 여 생성 하는 값입니다. UnitNumber, 디스크입니다. 키를 디스크입니다. ControllerKey.VAlue
초당 읽기 작업 | virtualDisk.numberReadAveraged.average
초당 쓰기 작업 | virtualDisk.numberWriteAveraged.average
읽기 처리량 (초당 MB) | virtualDisk.read.average
쓰기 처리량 (초당 MB) | virtualDisk.write.average
**NIC 세부 정보 당** | 
네트워크 어댑터 이름 | nic.Key
MAC 주소 | ((VirtualEthernetCard)nic).MacAddress
IPv4 주소 | vm.Guest.Net
IPv6 주소 | vm.Guest.Net
읽기 처리량 (초당 MB) | net.received.average
쓰기 처리량 (초당 MB) | net.transmitted.average
**인벤토리 경로 세부 정보** | 
이름 | container.GetType().Name
자식 개체 유형 | container.ChildType
참조 세부 정보 | container.MoRef
부모 세부 정보 | Container.Parent
VM 당 폴더 세부 정보 | ((Folder)container).ChildEntity.Type
VM 당 데이터 센터 세부 정보 | ((Datacenter)container).VmFolder
데이터 센터 호스트 폴더 세부 정보 | ((Datacenter)container).HostFolder
호스트 클러스터 세부 정보 | ((ClusterComputeResource)container).Host
VM 당 호스트 세부 정보 | ((HostSystem)container).VM



## <a name="collected-performance-data-hyper-v"></a>수집 된 성능 데이터 Hyper-v

어플라이언스 수집 하 고 Azure에 전송 하는 VMware VM 성능 데이터는 다음과 같습니다.

**성능 카운터 클래스** | **카운터** | **영향 평가**
--- | --- | ---
Hyper-v 하이퍼바이저 가상 프로세서 | % 게스트에 대 한 런타임 정보 | 권장 VM 크기/비용
Hyper-v 동적 메모리 VM | Current Pressure (%)<br/> Guest Visible Physical Memory (MB) | 권장 VM 크기/비용
Hyper-v 가상 저장소 장치 | 읽기 바이트/초 | 디스크 크기, 저장소 비용, VM 크기에 대 한 계산
Hyper-v 가상 저장소 장치 | 쓰기 바이트/초 | 디스크 크기, 저장소 비용, VM 크기에 대 한 계산
Hyper-v 가상 네트워크 어댑터 | 초당 받은 바이트 | VM 크기에 대 한 계산
Hyper-v 가상 네트워크 어댑터 | Bytes Sent/Second | VM 크기에 대 한 계산

- CPU 사용률에는 VM에 연결 하는 모든 가상 프로세서에 대 한 모든 사용량의 합계입니다.
- 메모리 사용률이 (Current Pressure * Guest Visible Physical Memory) / 100입니다.
- 디스크 및 네트워크 사용률 값은 나열 된 Hyper-v 성능 카운터에서 수집 됩니다.

## <a name="collected-metadata-hyper-v"></a>수집 된 메타 데이터 Hyper-v

어플라이언스 수집 하 고 Azure에 전송 하는 Hyper-v VM 메타 데이터의 전체 목록은 다음과 같습니다.

**데이터** | **WMI 클래스** | **WMI 클래스 속성**
--- | --- | ---
**컴퓨터 세부 정보** | 
BIOS _ Msvm_BIOSElement 일련 번호 | BIOSSerialNumber
VM 유형 (Gen 1 또는 2) | Msvm_VirtualSystemSettingData | VirtualSystemSubType
VM 표시 이름 | Msvm_VirtualSystemSettingData | ElementName
VM 버전 | Msvm_ProcessorSettingData | VirtualQuantity
메모리 (바이트) | Msvm_MemorySettingData | VirtualQuantity
VM에서 사용할 수 있는 최대 메모리 | Msvm_MemorySettingData | 제한
동적 메모리 지원 | Msvm_MemorySettingData | DynamicMemoryEnabled
운영 체제 이름/버전/FQDN | Msvm_KvpExchangeComponent | GuestIntrinsciExchangeItems 이름 데이터
VM 전원 상태 | Msvm_ComputerSystem | EnabledState
**디스크 세부 정보 당** | 
디스크 식별자 | Msvm_VirtualHardDiskSettingData | VirtualDiskId
가상 하드 디스크 형식 | Msvm_VirtualHardDiskSettingData | 형식
가상 하드 디스크 크기 | Msvm_VirtualHardDiskSettingData | MaxInternalSize
가상 하드 디스크 부모 | Msvm_VirtualHardDiskSettingData | ParentPath
**NIC 세부 정보 당** | 
IP 주소 (가상 Nic) | Msvm_GuestNetworkAdapterConfiguration | IPAddresses
DHCP (가상 Nic)를 사용 하도록 설정 | Msvm_GuestNetworkAdapterConfiguration | DHCPEnabled
NIC ID (가상 Nic) | Msvm_SyntheticEthernetPortSettingData | InstanceID
NIC의 MAC 주소 (가상 Nic) | Msvm_SyntheticEthernetPortSettingData | 주소
NIC ID (레거시 Nic) | MsvmEmulatedEthernetPortSetting 데이터 | InstanceID
NIC MAC ID (레거시 Nic) | MsvmEmulatedEthernetPortSetting 데이터 | 주소




## <a name="discovery-and-collection-process"></a>검색 및 수집 프로세스

어플라이언스는 vCenter Server와 Hyper-v 호스트/클러스터 마다 다음 프로세스를 사용 하 여 통신 합니다.


1. **검색 시작**:
    - Hyper-v 기기에서 검색을 시작 하면 WinRM 포트 5985 (HTTP) 및 5986(https (HTTPS)에서 Hyper-v 호스트와 통신 합니다.
    - VMware 기기에서 검색을 시작 하면 기본적으로 TCP 포트 443에서 vCenter 서버와 통신 합니다. VCenter server는 다른 포트에서 수신 대기를 하는 경우에 어플라이언스 웹 앱에서 구성할 수 있습니다.
2. **메타 데이터 및 성능 데이터 수집**:
    - 어플라이언스 포트 5985 및 5986에서 Hyper-v 호스트에서 Hyper-v VM으로 데이터를 수집 하도록 모델 CIM (Common Information) 세션을 사용 합니다.
    - 어플라이언스는 vCenter Server에서에서 VMware VM 데이터를 수집 하도록 기본적으로 포트 443 사용 하 여 통신 합니다.
3. **데이터 전송**: 어플라이언스는 SSL 포트 443 통해 Azure Migrate Server 평가 및 Azure 마이그레이션 서버 마이그레이션에 수집 된 데이터를 보냅니다.
    - 어플라이언스 성능 데이터에 대 한 실시간 사용률 데이터를 수집합니다.
        - 성능 데이터는 VMware에 대 한 20 초 마다 및 각 성능 메트릭에 대 한 Hyper-v에 대 한 30 초 마다 수집 됩니다.
        - 수집 된 데이터는 10 분 동안 단일 데이터 지점을 만들려면 롤업 됩니다.
        - 모든 20에서 최고 사용률 값을 선택/30 데이터 요소를 두 번째 및 평가 계산에 대 한 Azure로 전송 합니다.
        - 10 분 지점 오름차순으로 정렬 되 고 적절 한 백분위 수 값이 평가 계산에 사용 됩니다 (50/90th/95th/99) 평가 속성에 지정 된 백분위 수 값에 따라
    - 서버 마이그레이션에 대 한 어플라이언스 VM 데이터 수집을 시작 하 고 Azure에 복제 합니다.
4. **마이그레이션하고 평가할**: 이제 Azure Migrate Server 평가 사용 하 여 어플라이언스에 의해 수집 된 메타 데이터에서 평가 만들 수 있습니다. 또한 Azure 마이그레이션 서버 마이그레이션을 사용 하 여 에이전트 없는 VM 복제를 오케스트레이션 하는 VMware Vm을 마이그레이션하고 시작할 수 있습니다.


![아키텍처](./media/migrate-appliance/architecture.png)


## <a name="appliance-upgrades"></a>어플라이언스 업그레이드

어플라이언스는 Azure Migrate 에이전트가 어플라이언스에서 실행 되는 업데이트 업그레이드 됩니다.

- 왜냐하면 자동으로 자동 업데이트를이 어플라이언스에 기본적으로 사용 됩니다.
- 에이전트를 수동으로 업데이트 하려면이 기본 설정을 변경할 수 있습니다.
- 자동 업데이트를 사용 하지 않으려면 HKLM\SOFTWAREMicrosoft\Azure에서 레지스트리 키 어플라이언스 자동 업데이트를 설정 합니다.

### <a name="set-agent-updates-to-manual"></a>수동으로 에이전트를 업데이트 설정

수동 업데이트 해야 같은 시간에 어플라이언스에서 모든 에이전트를 업데이트 하는 사용 하 여는 **업데이트** 어플라이언스에서 각 오래 된 에이전트에 대 한 단추입니다. 언제 든 지 자동 업데이트를 다시 설정 업데이트를 전환할 수 있습니다.

## <a name="next-steps"></a>다음 단계

[에 대해 알아봅니다 어떻게](tutorial-assess-vmware.md#set-up-the-appliance-vm) VMware에 대 한 어플라이언스를 설정 합니다.
[에 대해 알아봅니다 어떻게](tutorial-assess-hyper-v.md#set-up-the-appliance-vm) 어플라이언스에 Hyper-v을 설정 합니다.

