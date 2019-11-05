---
title: VMware 평가 및 마이그레이션에 대 한 Azure Migrate 지원 매트릭스
description: Azure Migrate 서비스를 사용 하 여 VMware Vm을 Azure로 평가 하 고 마이그레이션하기 위한 지원 설정 및 제한 사항을 요약 합니다.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 09/17/2019
ms.author: raynew
ms.openlocfilehash: 2a8a19dfd2cdc7a64a5ea90b96808963b19f73bb
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73498647"
---
# <a name="support-matrix-for-vmware-assessment-and-migration"></a>VMware 평가 및 마이그레이션 지원 매트릭스

[Azure Migrate](migrate-overview.md) 를 사용 하 여 컴퓨터를 평가 하 고 Microsoft Azure 클라우드로 마이그레이션할 수 있습니다. 이 문서에서는 온-프레미스 VMware Vm을 평가 하 고 마이그레이션하기 위한 지원 설정 및 제한 사항을 요약 합니다.


## <a name="vmware-scenarios"></a>VMware 시나리오

이 표에는 VMware Vm에 대해 지원 되는 시나리오가 요약 되어 있습니다.

**배포웹사이트를** | **세부 정보**
--- | ---
**온-프레미스 VMware Vm 평가** | 첫 번째 평가를 [설정](tutorial-prepare-vmware.md) 합니다.<br/><br/> 대규모 평가를 [실행](scale-vmware-assessment.md) 합니다.
**VMware VM 마이그레이션** | 에이전트 없는 마이그레이션을 사용 하 여 마이그레이션하거나 에이전트 기반 마이그레이션을 사용할 수 있습니다. [자세한 정보](server-migrate-overview.md)


## <a name="azure-migrate-projects"></a>Azure Migrate 프로젝트

**지원** | **세부 정보**
--- | ---
**Azure 사용 권한** | Azure Migrate 프로젝트를 만들려면 구독에 대 한 참가자 또는 소유자 권한이 있어야 합니다.
**VMware 제한 사항**  | 단일 프로젝트에서 최대 35000 VMware Vm을 평가 합니다. Azure 구독에서 여러 프로젝트를 만들 수 있습니다. 프로젝트에는 VMware Vm과 Hyper-v Vm이 모두 포함 될 수 있습니다 (평가 제한까지).
**지리** | 지원 되는 지역을 [검토](migrate-support-matrix.md#supported-geographies) 합니다.

**지리** | **메타데이터 스토리지 위치**
--- | ---
Azure Government | 미국 정부 버지니아
아시아 태평양 | 동아시아 또는 동남 아시아
오스트레일리아 | 오스트레일리아 동부 또는 오스트레일리아 남동쪽
브라질 | 브라질 남부
캐나다 | 캐나다 중부 또는 캐나다 동부
유럽 | 북유럽 또는 유럽 서부
프랑스 | 프랑스 중부
인도 | 인도 중부 또는 인도 남부
일본 |  일본 동부 또는 일본 서 부
한국 | 대한민국 중부 또는 한국 남부
영국 | 영국 남부 또는 영국 서부
미국 | 미국 중부 또는 미국 서 부 2


 > [!NOTE]
 > Azure Government에 대 한 지원은 현재 [이전 버전](https://docs.microsoft.com/azure/migrate/migrate-services-overview#azure-migrate-versions) 의 Azure Migrate 에서만 사용할 수 있습니다.


## <a name="application-discovery"></a>응용 프로그램 검색

Azure Migrate: 서버 평가에서 앱, 역할 및 기능을 검색할 수 있습니다. 앱 인벤토리를 검색 하면 온-프레미스 워크 로드에 맞게 조정 된 마이그레이션 경로를 식별 하 고 계획할 수 있습니다. Azure Migrate: 서버 평가는 컴퓨터 게스트 자격 증명을 사용 하 여 WMI 및 SSH 호출을 통해 컴퓨터에 원격으로 액세스 하는 에이전트 없는 검색을 제공 합니다

**지원** | **세부 정보**
--- | ---
지원 되는 컴퓨터 | 온-프레미스 VMware VM
컴퓨터 운영 체제 | 모든 Windows 및 Linux 버전
자격 증명 | 현재에서는 모든 Windows server에 대해 하나의 자격 증명을 사용할 수 있으며 모든 Linux 서버에 대해 하나의 자격 증명을 사용할 수 있습니다. Windows Vm에 대 한 게스트 사용자 계정과 모든 Linux Vm에 대 한 일반/일반 사용자 계정 (비 sudo 액세스)을 만듭니다.
앱에 대 한 컴퓨터 제한-검색 | 어플라이언스 당 1만. 35000 프로젝트당

## <a name="assessment-vcenter-server-requirements"></a>평가-vCenter Server 요구 사항

이 표에는 VMware 가상화 서버에 대 한 평가 지원 및 제한이 요약 되어 있습니다.

**지원** | **세부 정보**
--- | ---
**vCenter 서버** | 평가 하려는 VMware Vm은 5.5, 6.0, 6.5 또는 6.7을 실행 하는 하나 이상의 vCenter 서버에서 관리 되어야 합니다.

## <a name="assessment-vcenter-server-permissions"></a>평가-vCenter Server 권한

평가의 경우 vCenter Server에 대 한 읽기 전용 계정이 필요 합니다.

## <a name="assessment-appliance-requirements"></a>평가-어플라이언스 요구 사항

Azure Migrate는 경량 어플라이언스를 실행 하 여 VMware Vm을 검색 하 고 Azure Migrate에 VM 메타 데이터 및 성능 데이터를 보냅니다. VMware 용 어플라이언스는 vCenter Server로 가져온 OVA 템플릿을 사용 하 여 배포 됩니다. 다음 표에는 어플라이언스 요구 사항이 요약 되어 있습니다.

**지원** | **세부 정보**
--- | ---
**어플라이언스 배포** | 어플라이언스를 VMware VM으로 배포 합니다. 32 g b RAM, 8 개 vCPUs, 80 GB의 디스크 저장소 및 외부 가상 스위치를 사용 하 여 VM을 할당 하려면 vCenter Server에 충분 한 리소스가 필요 합니다.<br/><br/> 어플라이언스는 직접 또는 프록시를 통해 인터넷에 액세스 해야 합니다.<br/> 어플라이언스 VM은 버전 5.5 이상을 실행 하는 ESXi 호스트에 배포 해야 합니다.
**Azure Migrate 프로젝트** | 어플라이언스는 단일 프로젝트에 연결할 수 있습니다. <br/> 모든 수의 어플라이언스를 단일 프로젝트에 연결할 수 있습니다.<br/> 프로젝트에서 최대 35000 개의 Vm을 평가할 수 있습니다.
**조사** | 어플라이언스는 vCenter Server에서 VMware Vm을 1만 개까지 검색할 수 있습니다.<br/> 어플라이언스는 단일 vCenter Server에 연결할 수 있습니다.
**평가 그룹** | 단일 그룹에 최대 35000 대의 컴퓨터를 추가할 수 있습니다.
**평가** | 단일 평가에서 최대 35000 Vm을 평가할 수 있습니다.


## <a name="assessment-url-access-requirements"></a>평가-URL 액세스 요구 사항

Azure Migrate 어플라이언스를 인터넷에 연결 해야 합니다.

- 어플라이언스를 배포 하는 경우 Azure Migrate 아래 표에 요약 된 Url에 대 한 연결 확인을 수행 합니다.
- URL 기반 프록시를 사용 하 여 인터넷에 연결 하는 경우 이러한 Url에 대 한 액세스를 허용 하 여 프록시가 Url을 조회 하는 동안 수신 된 CNAME 레코드를 확인 하 게 합니다.

**URL** | **세부 정보**  
--- | --- |
*.portal.azure.com  | Azure Portal Azure Migrate으로 이동 합니다.
*.windows.net <br/> *.msftauth.net <br/> *.msauth.net <br/> *. microsoft.com <br/> *. live.com | Azure 구독에 로그인합니다.
*.microsoftonline.com <br/> *.microsoftonline-p.com | 어플라이언스에 대 한 Active Directory 앱을 만들어 Azure Migrate 서비스와 통신 합니다.
management.azure.com | 어플라이언스에 대 한 Active Directory 앱을 만들어 Azure Migrate 서비스와 통신 합니다.
dc.services.visualstudio.com | 내부 모니터링에 사용 되는 앱 로그를 업로드 합니다.
*.vault.azure.net | Azure Key Vault에서 비밀을 관리 합니다.
*.servicebus.windows.net | 어플라이언스와 Azure Migrate 서비스 간의 통신.
*.discoverysrv.windowsazure.com <br/> *.migration.windowsazure.com <br/> *.hypervrecoverymanager.windowsazure.com | Azure Migrate 서비스 Url에 연결 합니다.
*.blob.core.windows.net | 저장소 계정에 데이터를 업로드 합니다.
http://aka.ms/latestapplianceservices<br/><br/> https://download.microsoft.com/download | Azure Migrate 어플라이언스 업데이트에 사용 됩니다.

## <a name="assessment-port-requirements"></a>평가-포트 요구 사항

**디바이스** | **연결**
--- | ---
기기가 | 어플라이언스에 대 한 원격 데스크톱 연결을 허용 하기 위해 TCP 포트 3389에서 인바운드 연결<br/><br/> URL을 사용 하 여 어플라이언스 관리 앱에 원격으로 액세스 하기 위한 포트 44368의 인바운드 연결: ```https://<appliance-ip-or-name>:44368``` <br/><br/>Azure Migrate에 검색 및 성능 메타 데이터를 보내기 위한 포트 443, 5671 및 5672의 아웃 바운드 연결
vCenter Server | 어플라이언스에서 평가를 위한 구성 및 성능 메타 데이터를 수집할 수 있도록 TCP 포트 443에서 인바운드 연결 <br/><br/> 어플라이언스는 기본적으로 포트 443의 vCenter에 연결 됩니다. VCenter 서버가 다른 포트에서 수신 대기 하는 경우 검색을 설정할 때 포트를 수정할 수 있습니다.

## <a name="assessment-dependency-visualization"></a>평가-종속성 시각화

종속성 시각화를 사용 하면 평가 하 고 마이그레이션하려는 컴퓨터 간에 종속성을 시각화할 수 있습니다. 신뢰 수준이 높은 컴퓨터를 평가 하려는 경우 일반적으로 종속성 매핑을 사용 합니다. VMware Vm의 경우 종속성 시각화는 다음과 같이 지원 됩니다.

- **에이전트 없는 종속성 시각화**:이 옵션은 현재 미리 보기 상태입니다. 컴퓨터에 에이전트를 설치 하지 않아도 됩니다.
    - 사용 하도록 설정 된 컴퓨터에서 TCP 연결 데이터를 캡처하여 작동 합니다. 종속성 검색을 시작한 후에는 기기가 5 분의 폴링 간격으로 컴퓨터에서 데이터를 수집 합니다.
    - 수집 되는 데이터는 다음과 같습니다.
        - TCP 연결
        - 활성 연결이 있는 프로세스의 이름
        - 위의 프로세스를 실행 하는 설치 된 응용 프로그램의 이름
        - 아니요. 모든 폴링 간격에서 감지 된 연결
- **에이전트 기반 종속성 시각화**: 에이전트 기반 종속성 시각화를 사용 하려면 분석 하려는 각 온-프레미스 컴퓨터에 다음 에이전트를 다운로드 하 여 설치 해야 합니다.
    - 각 컴퓨터에 MMA (Microsoft Monitoring agent)를 설치 해야 합니다. MMA 에이전트를 설치 하는 방법에 [대해 자세히 알아보세요](how-to-create-group-machine-dependencies.md#install-the-mma) .
    - 각 컴퓨터에 종속성 에이전트를 설치 해야 합니다. 종속성 에이전트를 설치 하는 방법에 [대해 자세히 알아보세요](how-to-create-group-machine-dependencies.md#install-the-dependency-agent) .
    - 또한 인터넷에 연결되지 않은 머신이 있으면 해당 머신에 Log Analytics 게이트웨이를 다운로드하여 설치해야 합니다.

## <a name="migration---limitations"></a>마이그레이션-제한 사항
복제를 위해 한 번에 최대 10 개의 Vm을 선택할 수 있습니다. 더 많은 컴퓨터를 마이그레이션하려는 경우 10 그룹으로 복제 합니다. VMware 에이전트 없는 마이그레이션의 경우 최대 100 복제를 동시에 실행할 수 있습니다.

## <a name="agentless-migration-vmware-server-requirements"></a>에이전트 없는 마이그레이션-VMware 서버 요구 사항

이 표에는 VMware 가상화 서버에 대 한 평가 지원 및 제한이 요약 되어 있습니다.

**지원** | **세부 정보**
--- | ---
vCenter Server | 버전 5.5, 6.0, 6.5 또는 6.7입니다.
VMware vSphere | 버전 5.5, 6.0, 6.5 또는 6.7

## <a name="agentless-migration-vcenter-server-permissions"></a>에이전트 없는 마이그레이션-vCenter Server 권한

**권한** | **세부 정보**
--- | ---
Datastore.Browse | VM 로그 파일을 검색 하 여 스냅숏 만들기 및 삭제 문제를 해결할 수 있습니다.
Datastore.LowLevelFileOperations | 데이터 저장소 브라우저에서 읽기/쓰기/삭제/이름 바꾸기 작업을 허용 하 여 스냅숏 생성 및 삭제 문제를 해결 합니다.
VirtualMachine.Configuration.DiskChangeTracking | VM 디스크의 변경 내용 추적 사용 또는 사용 안 함을 허용 하 여 스냅숏 간에 변경 된 데이터 블록을 가져옵니다.
VirtualMachine.Configuration.DiskLease | VM에 대 한 디스크 임대 작업을 허용 하 VMware vSphere VDDK (가상 디스크 개발 키트)를 사용 하 여 디스크를 읽습니다.
VirtualMachine.Provisioning.AllowReadOnlyDiskAccess | VDDK를 사용 하 여 디스크를 읽을 수 있도록 VM에서 디스크를 엽니다.
VirtualMachine.Provisioning.AllowVirtualMachineDownload  | VM과 연결 된 파일에 대 한 읽기 작업을 허용 하 여 로그를 다운로드 하 고 오류가 발생 하는 경우 문제를 해결 합니다.
VirtualMachine.SnapshotManagement.* | 복제에 대 한 VM 스냅숏 만들기 및 관리를 허용 합니다.
가상 컴퓨터. 상호 작용. 전원 끄기 | Azure로 마이그레이션하는 동안 VM의 전원이 꺼질 수 있도록 허용 합니다.


## <a name="agentless-migration-vmware-vm-requirements"></a>에이전트 없는 마이그레이션-VMware VM 요구 사항

**지원** | **세부 정보**
--- | ---
**지원되는 운영 체제** | Azure에서 지원 되는 [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) 및 [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) 운영 체제는 에이전트 없는 마이그레이션을 사용 하 여 마이그레이션할 수 있습니다.
**Azure에 대 한 필수 변경 내용** | 일부 Vm은 Azure에서 실행할 수 있도록 변경 해야 할 수 있습니다. Azure Migrate는 다음 운영 체제에 대해 이러한 변경을 자동으로 수행 합니다.<br/> -Red Hat Enterprise Linux 6.5 +, 7.0 이상<br/> -CentOS 6.5 이상, 7.0 이상</br> -SUSE Linux Enterprise Server 12 SP1 이상<br/> -Ubuntu 14.04 LTS, 16.04 LTS, 18.04 LTS<br/> -Debian 7, 8<br/><br/> 다른 운영 체제의 경우 마이그레이션하기 전에 수동으로 조정 해야 합니다. 관련 문서에는이 작업을 수행 하는 방법에 대 한 지침이 포함 되어 있습니다.
**Linux 부팅** | /Boot는 전용 파티션에 있는 경우 OS 디스크에 상주해 야 하며 여러 디스크에 분산 되 면 안 됩니다.<br/> /Boot가 루트 (/) 파티션의 일부인 경우 '/' 파티션은 OS 디스크에 있어야 하며 다른 디스크에 걸쳐 있지 않아야 합니다.
**UEFI 부팅** | UEFI 부팅이 포함 된 Vm은 마이그레이션을 지원 하지 않습니다.
**디스크 크기** | 2TB OS 디스크 데이터 디스크의 경우 4tb
**디스크 제한** |  VM 당 최대 60 디스크
**암호화 된 디스크/볼륨** | 암호화 된 디스크/볼륨이 있는 Vm은 마이그레이션을 지원 하지 않습니다.
**공유된 디스크 클러스터** | 지원되지 않습니다.
**독립 디스크** | 지원되지 않습니다.
**RDM/통과 디스크** | Vm에 RDM 또는 통과 디스크가 있는 경우 이러한 디스크는 Azure에 복제 되지 않습니다.
**용** | Vm에 볼륨으로 탑재 된 NFS 볼륨이 복제 되지 않습니다.
**iSCSI 대상** | ISCSI 대상을 사용 하는 Vm은 에이전트 없는 마이그레이션에 대해 지원 되지 않습니다.
**다중 경로 IO** | 지원되지 않습니다.
**저장소 vMotion** | 지원되지 않습니다. VM에서 저장소 vMotion를 사용 하는 경우 복제가 작동 하지 않습니다.
**팀 Nic** | 지원되지 않습니다.
**Ipv6)** | 지원되지 않습니다.
**대상 디스크** | Vm은 Azure에서 관리 되는 디스크 (표준 HDD, 프리미엄 SSD)로만 마이그레이션할 수 있습니다.
**동시 복제** | vCenter Server 당 Vm 100 더 많은 경우 100의 일괄 처리로 마이그레이션합니다.


## <a name="agentless-migration-appliance-requirements"></a>에이전트 없는 마이그레이션-어플라이언스 요구 사항


**지원** | **세부 정보**
--- | ---
**ESXi** | 어플라이언스 VM은 버전 5.5 이상을 실행 하는 ESXi 호스트에 배포 해야 합니다.
**Azure Migrate 프로젝트** | 어플라이언스는 단일 프로젝트에 연결할 수 있습니다.
**vCenter Server** | 어플라이언스는 vCenter Server에서 VMware Vm을 1만 개까지 검색할 수 있습니다.<br/> 어플라이언스는 한 vCenter Server에 연결할 수 있습니다.
**VDDK** | Azure Migrate Server Migration을 사용 하 여 에이전트 없는 마이그레이션을 실행 하는 경우 VMware vSphere VDDK을 어플라이언스 VM에 설치 해야 합니다.

## <a name="agentless-migration-url-access-requirements"></a>에이전트 없는 마이그레이션-URL 액세스 요구 사항

Azure Migrate 어플라이언스는 인터넷에 인터넷으로 연결 되어야 합니다.

- 어플라이언스를 배포 하는 경우 Azure Migrate 아래 표에 요약 된 Url에 대 한 연결 확인을 수행 합니다.
- URL 기반 프록시를 사용 하는 경우 이러한 Url에 대 한 액세스를 허용 하 여 프록시가 Url을 조회 하는 동안 수신 된 CNAME 레코드를 확인 하 게 합니다.

**URL** | **세부 정보**  
--- | ---
*.portal.azure.com | Azure Portal Azure Migrate으로 이동 합니다.
*.windows.net <br/> *.msftauth.net <br/> *.msauth.net <br/> *. microsoft.com <br/> *. live.com  | Azure 구독에 로그인합니다.
*.microsoftonline.com <br/> *.microsoftonline-p.com | 어플라이언스에 대 한 Active Directory 앱을 만들어 Azure Migrate 서비스와 통신 합니다.
management.azure.com | 어플라이언스에 대 한 Active Directory 앱을 만들어 Azure Migrate 서비스와 통신 합니다.
dc.services.visualstudio.com | 내부 모니터링에 사용 되는 앱 로그를 업로드 합니다.
*.vault.azure.net | Azure Key Vault에서 비밀을 관리 합니다.
*.servicebus.windows.net | 어플라이언스와 Azure Migrate 서비스 간의 통신.
*.discoverysrv.windowsazure.com <br/> *.migration.windowsazure.com <br/> *.hypervrecoverymanager.windowsazure.com | Azure Migrate 서비스 Url에 연결 합니다.
*.blob.core.windows.net | 저장소 계정에 데이터를 업로드 합니다.
http://aka.ms/latestapplianceservices<br/><br/> https://download.microsoft.com/download | Azure Migrate 어플라이언스 업데이트에 사용 됩니다.


## <a name="agentless-migration-port-requirements"></a>에이전트 없는 마이그레이션-포트 요구 사항

**디바이스** | **연결**
--- | ---
기기가 | 포트 443의 아웃 바운드 연결을 사용 하 여 복제 된 데이터를 Azure에 업로드 하 고, 복제 및 오케스트레이션 서비스를 사용 하 여 Azure Migrate 서비스와 통신 합니다.
vCenter Server | 어플라이언스에서 복제를 오케스트레이션 할 수 있도록 허용 하는 포트 443의 인바운드 연결-스냅숏 만들기, 데이터 복사, 릴리스 스냅숏
vSphere/EXSI 호스트 | 어플라이언스의 TCP 포트 902에 대 한 인바운드는 스냅숏에서 데이터를 복제 합니다.


## <a name="agent-based-migration-vmware-server-requirements"></a>에이전트 기반 마이그레이션-VMware 서버 요구 사항

이 표에는 VMware 가상화 서버에 대 한 평가 지원 및 제한이 요약 되어 있습니다.

**지원** | **세부 정보**
--- | ---
vCenter Server | 버전 5.5, 6.0, 6.5 또는 6.7입니다.
VMware vSphere | 버전 5.5, 6.0, 6.5 또는 6.7.

### <a name="agent-based-migration-vcenter-server-permissions"></a>에이전트 기반 마이그레이션-vCenter Server 권한

VCenter Server에 대 한 읽기 전용 계정입니다.

## <a name="agent-based-migration-replication-appliance-requirements"></a>에이전트 기반 마이그레이션-복제 어플라이언스 요구 사항

Azure Migrate 서버 마이그레이션과 함께 VMware Vm 및 물리적 서버에 대 한 에이전트 기반 마이그레이션에 사용 되는 [복제 어플라이언스](migrate-replication-appliance.md) 에 대 한 요구 사항은 표에 요약 되어 있습니다.

> [!NOTE]
> Azure Migrate 허브에 제공 된 OVA 템플릿을 사용 하 여 복제 어플라이언스를 설정 하는 경우 어플라이언스는 Windows Server 2016를 실행 하 고 지원 요구 사항을 준수 합니다. 물리적 서버에서 복제 어플라이언스를 수동으로 설정 하는 경우 요구 사항에 부합 하는지 확인 합니다.



**구성 요소** | **요구 사항**
--- | ---
 | **Vmware 설정** (vmware VM 어플라이언스)
PowerCLI | VMware VM에서 복제 어플라이언스를 실행 하는 경우에는 [Powercli 버전 6.0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1) 을 설치 해야 합니다.
NIC 유형 | VMXNET3 (기기가 VMware VM 인 경우)
 | **하드웨어 설정**
CPU 코어 | 8
RAM | 16GB
디스크 수 | 3: OS 디스크, 프로세스 서버 캐시 디스크, 보존 드라이브입니다.
사용 가능한 디스크 공간 (캐시) | 600GB
사용 가능한 디스크 공간(보존 디스크) | 600GB
**소프트웨어 설정** |
운영 체제 | Windows Server 2016 또는 Windows Server 2012 R2
운영 체제 로케일 | 미국 영어(en-us)
TLS | TLS 1.2를 사용 하도록 설정 해야 합니다.
.NET Framework | .NET Framework 4.6 이상이 컴퓨터에 설치 되어 있어야 합니다 (강력한 암호화를 사용 하도록 설정 됨).
MySQL | MySQL을 어플라이언스에 설치 해야 합니다.<br/> MySQL을 설치해야 합니다. 수동으로 설치 하거나 어플라이언스를 배포 하는 동안 설치할 수 Site Recovery.
다른 앱 | 복제 어플라이언스에서 다른 앱을 실행 하지 마세요.
Windows Server 역할 | 다음 역할을 사용하지 않도록 설정함: <br> - Active Directory Domain Services <br>- 인터넷 정보 서비스 <br> - Hyper-V
그룹 정책 | 다음 그룹 정책을 사용하지 않도록 설정함: <br> - 명령 프롬프트에 대한 액세스 방지 <br> - 레지스트리 편집 도구에 대한 액세스 방지 <br> - 파일 첨부를 위한 트러스트 논리 <br> - 스크립트 실행 켜기 <br> [자세한 정보](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)
IIS | - 기존의 기본 웹 사이트 없음 <br> - 포트 443에서 수신 대기하는 기존의 웹 사이트/애플리케이션 없음 <br>- [익명 인증](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) 사용 <br> - [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx) 설정 사용
**네트워크 설정** |
IP 주소 유형 | 정적
포트 | 443(컨트롤 채널 오케스트레이션)<br>9443(데이터 전송)
NIC 유형 | VMXNET3

### <a name="replication-appliance-url-access"></a>복제 어플라이언스 URL 액세스

복제 어플라이언스는 이러한 Url에 대 한 액세스 권한이 필요 합니다.

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
https:\//login.microsoftonline.com <br/> https:\//secure.aadcdn.microsoftonline-p.com <br/> https:\//login.live.com <br/> https:\//graph.windows.net <br/> https:\//login.windows.net <br/> https:\//www.live.com <br/> https:\//www.microsoft.com  | 설치 프로그램은 이러한 Url에 액세스할 수 있어야 합니다. Azure Active Directory에서 액세스 제어 및 ID 관리에 사용됩니다.
https:\//dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi | MySQL 다운로드를 완료하려면


#### <a name="mysql-installation-options"></a>MySQL 설치 옵션

이러한 방법 중 하나를 사용 하 여 복제 어플라이언스에 MySQL을 설치할 수 있습니다.

**메서드** | **세부 정보**
--- | ---
수동으로 다운로드 및 설치 | MySQL 응용 프로그램을 다운로드 하 & C:\Temp\ASRSetup 폴더에 배치한 다음 수동으로 설치 합니다.<br/> 기기를 설정 하는 경우 MySQL은 이미 설치 된 것으로 표시 됩니다.
온라인 다운로드 하지 않음 | C:\Temp\ASRSetup. 폴더에 MySQL 설치 관리자 응용 프로그램을 추가 합니다. 어플라이언스를 설치 하 고를 클릭 하 여 MySQL을 다운로드 하 고 설치 하면 추가 된 설치 관리자가 사용 됩니다.
Azure Migrate에서 다운로드 및 설치 | 어플라이언스를 설치할 때 MySQL을 입력 하 라는 메시지가 표시 되 면 **다운로드 및 설치**를 선택 합니다.



## <a name="agent-based-migration-vmware-vm-requirements"></a>에이전트 기반 마이그레이션-VMware VM 요구 사항

**지원** | **세부 정보**
--- | ---
**컴퓨터 작업** | Azure Migrate 지원 되는 컴퓨터에서 실행 되는 모든 작업 (예를 들어 Active Directory, SQL server 등)의 마이그레이션을 지원 합니다.
**운영 체제** | 최신 정보는 Site Recovery에 대 한 [운영 체제 지원](../site-recovery/vmware-physical-azure-support-matrix.md#replicated-machines) 을 참조 하세요. Azure Migrate는 동일한 VM 운영 체제를 지원 합니다.
**Linux 파일 시스템/게스트 저장소** | 최신 정보는 Site Recovery에 대 한 [Linux 파일 시스템 지원](../site-recovery/vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage) 을 참조 하세요. Azure Migrate는 동일한 Linux 파일 시스템을 지원 합니다.
**네트워크/저장소** | 최신 정보는 Site Recovery에 대 한 [네트워크](../site-recovery/vmware-physical-azure-support-matrix.md#network) 및 [저장소](../site-recovery/vmware-physical-azure-support-matrix.md#storage) 필수 구성 요소를 검토 하세요. Azure Migrate는 동일한 네트워크/저장소 요구 사항을 제공 합니다.
**Azure 요구 사항** | 최신 정보는 Site Recovery에 대 한 [Azure 네트워크](../site-recovery/vmware-physical-azure-support-matrix.md#azure-vm-network-after-failover), [저장소](../site-recovery/vmware-physical-azure-support-matrix.md#azure-storage)및 [계산](../site-recovery/vmware-physical-azure-support-matrix.md#azure-compute) 요구 사항을 검토 하세요. Azure Migrate는 VMware 마이그레이션에 대 한 요구 사항이 동일 합니다.
**모바일 서비스** | 마이그레이션하려는 각 VM에 모바일 서비스 에이전트를 설치 해야 합니다.
**UEFI 부팅** | Azure에서 마이그레이션된 VM은 자동으로 BIOS 부팅 VM으로 변환 됩니다.<br/><br/> OS 디스크에는 최대 4 개의 파티션이 있어야 하 고 볼륨은 NTFS로 포맷 되어야 합니다.
**대상 디스크** | Vm은 Azure에서 관리 되는 디스크 (표준 HDD, 프리미엄 SSD)로만 마이그레이션할 수 있습니다.
**디스크 크기** | 2TB OS 디스크 데이터 디스크의 경우 8TB입니다.
**디스크 제한** |  VM 당 최대 63 디스크
**암호화 된 디스크/볼륨** | 암호화 된 디스크/볼륨이 있는 Vm은 마이그레이션을 지원 하지 않습니다.
**공유된 디스크 클러스터** | 지원되지 않습니다.
**독립 디스크** | 지원됩니다.
**통과 디스크** | 지원됩니다.
**용** | Vm에 볼륨으로 탑재 된 NFS 볼륨이 복제 되지 않습니다.
iSCSI 대상 | ISCSI 대상을 사용 하는 Vm은 에이전트 없는 마이그레이션에 대해 지원 되지 않습니다.
**다중 경로 IO** | 지원되지 않습니다.
**저장소 vMotion** | 지원됨
**팀 Nic** | 지원되지 않습니다.
**Ipv6)** | 지원되지 않습니다.




## <a name="agent-based-migration-url-access-requirements"></a>에이전트 기반 마이그레이션-URL 액세스 요구 사항

VMware Vm에서 실행 되는 모바일 서비스는 인터넷에 연결 되어 있어야 합니다.

모바일 서비스를 배포 하는 경우 아래 표에 요약 된 Url에 대 한 연결 확인을 수행 합니다.


**URL** | **세부 정보**  
--- | ---
*.portal.azure.com | Azure Portal Azure Migrate으로 이동 합니다.
*.windows.net | Azure 구독에 로그인합니다.
*.microsoftonline.com | 어플라이언스에 대 한 Active Directory 앱을 만들어 Azure Migrate 서비스와 통신 합니다.
management.azure.com | 어플라이언스에 대 한 Active Directory 앱을 만들어 Azure Migrate 서비스와 통신 합니다.
dc.services.visualstudio.com | 내부 모니터링에 사용 되는 앱 로그를 업로드 합니다.
*.vault.azure.net | Azure Key Vault에서 비밀을 관리 합니다.
*.servicebus.windows.net | 어플라이언스와 Azure Migrate 서비스 간의 통신.
*.discoverysrv.windowsazure.com <br/> *.migration.windowsazure.com <br/> *.hypervrecoverymanager.windowsazure.com | Azure Migrate 서비스 Url에 연결 합니다.
*.blob.core.windows.net | 저장소 계정에 데이터를 업로드 합니다.

## <a name="agent-based-migration-port-requirements"></a>에이전트 기반 마이그레이션-포트 요구 사항

**디바이스** | **연결**
--- | ---
VM | Vm에서 실행 되는 모바일 서비스는 복제 관리를 위해 HTTPS 443 인바운드 포트의 온-프레미스 복제 어플라이언스 (구성 서버)와 통신 합니다.<br/><br/> VM은 HTTPS 9443 인바운드 포트의 프로세스 서버(구성 서버 컴퓨터에서 실행)로 복제 데이터를 전송합니다. 이 포트는 수정할 수 있습니다.
복제 어플라이언스 | 복제 어플라이언스는 HTTPS 443 아웃 바운드 포트를 통해 Azure를 사용 하 여 복제를 오케스트레이션 합니다.
프로세스 서버 | 프로세스 서버는 복제 데이터를 수신 하 고, 최적화 하 고, 암호화 하 고 포트 443 아웃 바운드를 통해 Azure storage로 보냅니다.<br/> 기본적으로 프로세스 서버는 복제 어플라이언스에서 실행 됩니다.

## <a name="azure-vm-requirements"></a>Azure VM 요구 사항

Azure로 복제 된 모든 온-프레미스 Vm은이 표에 요약 된 Azure VM 요구 사항을 충족 해야 합니다. Site Recovery에서 복제에 대 한 필수 구성 요소 확인을 실행 하면 일부 요구 사항이 충족 되지 않은 경우 검사가 실패 합니다.

**구성 요소** | **요구 사항** | **세부 정보**
--- | --- | ---
게스트 운영 체제 | 에이전트 없는 [복제를 사용 하는 Vmware vm](#agentless-migration-vmware-vm-requirements)및 [에이전트 기반 복제를 사용 하는 vmware vm](#agent-based-migration-vmware-vm-requirements)에 대해 지원 되는 운영 체제를 확인 합니다.<br/> 지원 되는 운영 체제에서 실행 되는 모든 워크 로드를 마이그레이션할 수 있습니다. | 지원되지 않는 경우 확인이 실패합니다.
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
마이그레이션 후 연결-Windows | 마이그레이션 후 Windows를 실행 하는 Azure Vm에 연결 하려면 다음을 수행 합니다.<br/> -마이그레이션하기 전에 온-프레미스 VM에서 RDP를 사용 하도록 설정 합니다. **공용** 프로필에 대한 TCP 및 UDP 규칙이 추가되었는지와 해당 RDP가 **Windows 방화벽** > **허용되는 앱**에서 모든 프로필에 대해 허용되는지 확인합니다.<br/> 사이트 간 VPN 액세스의 경우 RDP를 사용 하도록 설정 하 고 **Windows 방화벽** 에서 rdp를 허용 하 고, **도메인 및 개인** 네트워크에 대해 **허용 되는 앱 및 기능** -> 합니다. 또한 운영 체제의 SAN 정책이 **OnlineAll**으로 설정 되어 있는지 확인 합니다. [자세히 알아봅니다](prepare-for-migration.md). |
마이그레이션 후 연결-Linux | SSH를 사용 하 여 마이그레이션한 후 Azure Vm에 연결 하려면:<br/> 마이그레이션 전에 온-프레미스 컴퓨터에서 Secure Shell 서비스가 시작으로 설정 되어 있고 방화벽 규칙에서 SSH 연결을 허용 하는지 확인 합니다.<br/> 장애 조치 (failover) 후에 Azure VM에서 장애 조치 (failover) 된 VM의 네트워크 보안 그룹 규칙에 대 한 SSH 포트 및 연결 된 Azure 서브넷에 대 한 들어오는 연결을 허용 합니다. 또한 VM에 대 한 공용 IP 주소를 추가 합니다. |  


## <a name="next-steps"></a>다음 단계

[VMware](tutorial-prepare-vmware.md) 평가 및 마이그레이션을 준비 합니다.
