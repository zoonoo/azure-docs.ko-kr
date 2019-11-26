---
title: Azure Migrate 어플라이언스 아키텍처
description: 서버 평가 및 마이그레이션에 사용 되는 Azure Migrate 어플라이언스에 대 한 개요를 제공 합니다.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: raynew
ms.openlocfilehash: 49545ca6c43c272c3fd84f8bee59b8617aae136d
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74232562"
---
# <a name="azure-migrate-appliance"></a>Azure Migrate 어플라이언스

이 문서에서는 Azure Migrate 어플라이언스를 설명 합니다. Azure Migrate 평가 및 마이그레이션 도구를 사용 하 여 앱, 인프라 및 워크 로드를 검색, 평가 및 마이그레이션할 때 어플라이언스를 배포 하는 경우 Microsoft Azure 합니다. 

[Azure Migrate](migrate-services-overview.md) 는 Azure에 대 한 온-프레미스 앱 및 워크 로드 및 사설/공용 클라우드 vm의 검색, 평가 및 마이그레이션을 추적 하는 중앙 허브를 제공 합니다. 이 허브는 평가 및 마이그레이션에 사용되는 Azure Migrate 도구뿐만 아니라 타사 ISV(독립 소프트웨어 공급업체) 제품도 제공합니다.



## <a name="appliance-overview"></a>어플라이언스 개요

Azure Migrate 어플라이언스 유형 및 사용법은 다음과 같습니다.

**배포 된** | **용도** | **세부 정보**
--- | --- |  ---
VMware VM | Azure Migrate 평가 도구를 사용 하 여 VMware VM 평가<br/><br/> Azure Migrate 서버 마이그레이션 도구를 사용 하 여 VMware VM 에이전트 없이 마이그레이션 | OVA 템플릿을 다운로드 하 고 vCenter Server로 가져와 어플라이언스 VM을 만듭니다.
Hyper-V VM | Azure Migrate 평가 도구를 사용 하 여 hyper-v VM 평가 | 압축 된 VHD를 다운로드 하 고 Hyper-v로 가져와서 어플라이언스 VM을 만듭니다.

## <a name="appliance-access"></a>어플라이언스 액세스

어플라이언스를 구성한 후에는 TCP 포트 3389을 통해 어플라이언스 VM에 원격으로 액세스할 수 있습니다. URL: `https://<appliance-ip-or-name>:44368`을 사용 하 여 포트 44368에서 어플라이언스에 대 한 웹 관리 앱에 원격으로 액세스할 수도 있습니다.

## <a name="appliance-license"></a>어플라이언스 라이선스
어플라이언스는 180 일 동안 유효한 Windows Server 2016 evaluation 라이선스와 함께 제공 됩니다. 평가 기간이 만료에 근접 한 경우 새 어플라이언스를 다운로드 하 여 배포 하거나 어플라이언스 VM의 운영 체제 라이선스를 활성화 하는 것이 좋습니다.

## <a name="appliance-agents"></a>어플라이언스 에이전트
어플라이언스에 이러한 에이전트가 설치 되어 있습니다.

**에이전트** | **세부 정보**
--- | ---
검색 에이전트 | 온-프레미스 가상 컴퓨터의 구성 데이터 수집
평가 에이전트 | VM 성능 데이터를 수집 하기 위해 온-프레미스 환경을 프로 파일링 합니다.
마이그레이션 어댑터 | VM 복제를 오케스트레이션 하 고 Vm과 Azure 간의 통신을 조정 합니다.
마이그레이션 게이트웨이 | 복제 된 VM 데이터를 Azure로 보냅니다.


## <a name="appliance-deployment-requirements"></a>어플라이언스 배포 요구 사항

- VMware 어플라이언스에 대 한 배포 요구 사항 및 어플라이언스에서 액세스 해야 하는 Url을 [검토](migrate-support-matrix-vmware.md#assessment-appliance-requirements) 합니다.
- Hyper-v 어플라이언스에 대 한 배포 요구 사항 및 어플라이언스에서 액세스 해야 하는 Url을 [검토](migrate-support-matrix-hyper-v.md#assessment-appliance-requirements) 합니다.


## <a name="collected-performance-data-vmware"></a>수집 된 성능 데이터-VMware

다음은 기기가 수집 하 여 Azure에 전송 하는 VMware VM 성능 데이터입니다.

**데이터** | **카운터** | **평가 영향**
--- | --- | ---
CPU 사용률 | cpu.usage.average | 권장 되는 VM 크기/비용
메모리 사용률 | mem.usage.average | 권장 되는 VM 크기/비용
디스크 읽기 처리량 (초당 MB) | virtualDisk.read.average | 디스크 크기, 저장소 비용, VM 크기에 대 한 계산
디스크 쓰기 처리량 (초당 MB) | virtualDisk.write.average | 디스크 크기, 저장소 비용, VM 크기에 대 한 계산
초당 디스크 읽기 작업 수 | virtualDisk.numberReadAveraged.average | 디스크 크기, 저장소 비용, VM 크기에 대 한 계산
초당 디스크 쓰기 작업 수 | virtualDisk.numberWriteAveraged.average  | 디스크 크기, 저장소 비용, VM 크기에 대 한 계산
NIC 읽기 처리량 (초당 MB) | net.received.average | VM 크기에 대 한 계산
NIC 쓰기 처리량 (초당 MB) | net.transmitted.average  |VM 크기에 대 한 계산


## <a name="collected-metadata-vmware"></a>수집 된 메타 데이터-VMware

> [!NOTE]
> Azure Migrate 어플라이언스에서 검색 된 메타 데이터는 Azure로 마이그레이션할 때 응용 프로그램을 적절 하 게 크기를 조정 하 고, Azure 적합성 분석, 응용 프로그램 종속성 분석 및 비용 계획을 수행 하는 데 사용 됩니다. Microsoft에서는 모든 라이센스 규정 준수 감사와 관련하여 이 데이터를 사용하지 않습니다.

다음은 기기가 수집 하 여 Azure에 전송 하는 VMware VM 메타 데이터의 전체 목록입니다.

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
이름 | container.GetType().Name
자식 개체 유형 | container.ChildType
참조 세부 정보 | container.MoRef
부모 세부 정보 | Container.Parent
VM 당 폴더 세부 정보 | ((Folder)container).ChildEntity.Type
VM 당 데이터 센터 정보 | ((Datacenter)container).VmFolder
호스트 폴더당 데이터 센터 정보 | ((Datacenter)container).HostFolder
호스트 당 클러스터 세부 정보 | ((ClusterComputeResource)container).Host
VM 당 호스트 세부 정보 | (HostSystem) 컨테이너). VM



## <a name="collected-performance-data-hyper-v"></a>수집 된 성능 데이터-Hyper-v

> [!NOTE]
> Azure Migrate 어플라이언스에서 검색 된 메타 데이터는 Azure로 마이그레이션할 때 응용 프로그램을 적절 하 게 크기를 조정 하 고, Azure 적합성 분석, 응용 프로그램 종속성 분석 및 비용 계획을 수행 하는 데 사용 됩니다. Microsoft에서는 모든 라이센스 규정 준수 감사와 관련하여 이 데이터를 사용하지 않습니다.

다음은 기기가 수집 하 여 Azure에 전송 하는 하이퍼 VM 성능 데이터입니다.

**성능 카운터 클래스** | **카운터** | **평가 영향**
--- | --- | ---
Hyper-v 하이퍼바이저 가상 프로세서 | % 게스트 실행 시간 | 권장 되는 VM 크기/비용
Hyper-v 동적 메모리 VM | 현재 압력 (%)<br/> 게스트 표시 실제 메모리 (MB) | 권장 되는 VM 크기/비용
Hyper-v 가상 저장 장치 | 읽은 바이트/초 | 디스크 크기, 저장소 비용, VM 크기에 대 한 계산
Hyper-v 가상 저장 장치 | 쓰기 바이트/초 | 디스크 크기, 저장소 비용, VM 크기에 대 한 계산
Hyper-v Virtual Network 어댑터 | 받은 바이트 수/초 | VM 크기에 대 한 계산
Hyper-v Virtual Network 어댑터 | 보낸 바이트/초 | VM 크기에 대 한 계산

- CPU 사용률은 VM에 연결 된 모든 가상 프로세서에 대 한 모든 사용량의 합계입니다.
- 메모리 사용률은 (현재 압력 * 게스트 표시 실제 메모리)/100입니다.
- 디스크 및 네트워크 사용률 값은 나열 된 Hyper-v 성능 카운터에서 수집 됩니다.

## <a name="collected-metadata-hyper-v"></a>수집 된 메타 데이터-Hyper-v

다음은 기기가 수집 하 여 Azure에 전송 하는 Hyper-v VM 메타 데이터의 전체 목록입니다.

**데이터** | **WMI 클래스** | **WMI 클래스 속성**
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
가상 하드 디스크 유형 | Msvm_VirtualHardDiskSettingData | 에
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
3. **데이터 보내기**: 어플라이언스는 Azure Migrate 서버 평가를 위해 수집 된 데이터를 보내고 SSL 포트 443을 통해 서버 마이그레이션을 Azure Migrate 합니다.
    - 성능 데이터의 경우 어플라이언스는 실시간 사용률 데이터를 수집 합니다.
        - 성능 데이터는 각 성능 메트릭에 대해 VMware에 대해 20 초 마다 수집 되 고 Hyper-v의 경우 30 초 마다 수집 됩니다.
        - 수집 된 데이터는 10 분 동안 단일 데이터 요소를 만들기 위해 롤업 됩니다.
        - 최고 사용률 값은 모든 20/30 번째 데이터 요소에서 선택 되 고 평가 계산을 위해 Azure로 전송 됩니다.
        - 평가 속성에 지정 된 백분위 수 값 (50 번째/90 번째/95 번째/99 초)에 따라 10 분 지점은 오름차순으로 정렬 되 고 적절 한 백분위 수 값은 평가를 계산 하는 데 사용 됩니다.
    - 서버 마이그레이션의 경우 어플라이언스는 VM 데이터 수집을 시작 하 여 Azure에 복제 합니다.
4. **평가 및 마이그레이션**: 이제 Azure Migrate Server 평가를 사용 하 여 어플라이언스에서 수집한 메타 데이터에서 평가를 만들 수 있습니다. 또한 Azure Migrate 서버 마이그레이션을 사용 하 여 VMware Vm 마이그레이션을 시작 하 여 에이전트 없는 VM 복제를 오케스트레이션 할 수도 있습니다.


![아키텍처](./media/migrate-appliance/architecture.png)


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

