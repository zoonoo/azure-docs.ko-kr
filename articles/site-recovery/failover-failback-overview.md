---
title: Azure Site Recovery 장애 조치 (failover) 및 장애 복구 정보
description: Azure Site Recovery 장애 조치 (failover) 및 장애 조치 (failover)에 대해 알아봅니다.
ms.topic: conceptual
ms.date: 12/24/2019
ms.openlocfilehash: 3617683200aa3ffba08061b70993613fd0cc7241
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2020
ms.locfileid: "92369882"
---
# <a name="about-on-premises-disaster-recovery-failoverfailback"></a>온-프레미스 재해 복구 장애 조치 (failover)/장애 복구 정보

이 문서에서는 [Azure Site Recovery](site-recovery-overview.md)를 사용 하 여 Azure에 온-프레미스 컴퓨터를 재해 복구 하는 동안 장애 조치 및 장애 복구 (failback)에 대 한 개요

## <a name="recovery-stages"></a>복구 단계

Site Recovery 장애 조치 (Failover) 및 장애 복구 (failback)는 4 단계로 이루어집니다.

- **1 단계: 온-프레미스에서 장애 조치 (failover)**: 온-프레미스 컴퓨터에 대해 azure로의 복제를 설정한 후 온-프레미스 사이트가 다운 되 면 해당 컴퓨터를 azure로 장애 조치 (failover) 합니다. 장애 조치(Failover) 후 복제된 데이터에서 Azure VM이 만들어집니다.
- **2 단계: Azure vm**다시 보호: Azure에서 azure vm을 다시 보호 하 여 온-프레미스 사이트에 다시 복제를 시작 합니다. 데이터 일관성을 유지 하기 위해 온-프레미스 VM (사용 가능한 경우)이 다시 보호 중에 꺼져 있습니다.
- **3 단계: azure에서 장애**조치 (failover): 온-프레미스 사이트가 다시 정상적으로 실행 되는 경우 다른 장애 조치 (failover)를 실행 하 여 azure vm을 온-프레미스 사이트로 장애 복구 (failback) 합니다. 장애 조치 (failover) 된 원본 위치 또는 대체 위치로 장애 복구 (failback) 할 수 있습니다.
- **4 단계: 온-프레미스 컴퓨터**다시 보호: 장애 복구 (failback) 후에 Azure에 대 한 온-프레미스 컴퓨터의 복제를 다시 사용 하도록 설정 합니다.

## <a name="failover"></a>장애 조치

BCDR (비즈니스 연속성 및 재해 복구) 전략의 일부로 장애 조치 (failover)를 수행 합니다.

- BCDR 전략의 첫 번째 단계로, 지속적으로 온-프레미스 컴퓨터를 Azure에 복제 합니다. 사용자는 온-프레미스 원본 컴퓨터에서 실행 되는 워크 로드 및 응용 프로그램에 액세스 합니다.
- 예를 들어 온-프레미스에서 가동 중단이 발생 하는 경우에는 Azure로 컴퓨터를 복제 하는 데 실패 합니다. Azure Vm은 복제 된 데이터를 사용 하 여 생성 됩니다.
- 비즈니스 연속성을 위해 사용자는 Azure Vm의 앱에 계속 액세스할 수 있습니다.

장애 조치 (Failover)는 2 단계 작업입니다.

- **장애 조치**(failover): 선택한 복구 지점을 사용 하 여 Azure VM을 만들고 가져오는 장애 조치 (failover)입니다.
- **커밋**: 장애 조치 (failover) 후 AZURE에서 VM을 확인 합니다.
    - 그런 다음 선택한 복구 지점으로 장애 조치 (failover)를 커밋하거나 커밋에 대해 다른 지점을 선택할 수 있습니다.
    - 장애 조치 (failover)를 커밋한 후에는 복구 지점을 변경할 수 없습니다.


## <a name="connect-to-azure-after-failover"></a>장애 조치 (failover) 후 Azure에 연결

RDP/SSH를 사용 하 여 장애 조치 (failover) 후 생성 된 Azure Vm에 연결 하려면 여러 가지 요구 사항이 있습니다.

**장애 조치(Failover)** | **위치** | **actions**
--- | --- | ---
**Azure VM (Windows (** | 장애 조치 (failover) 전 온-프레미스 컴퓨터 | **인터넷을 통한 액세스**: RDP를 사용 하도록 설정 합니다. TCP 및 UDP 규칙이 **공용**에 추가 되었는지 그리고 **Windows 방화벽**허용 되는 앱의 모든 프로필에 대해 RDP가 허용 되는지 확인  >  **Allowed Apps**합니다.<br/><br/> **사이트 간 VPN을 통해 액세스**: 컴퓨터에서 RDP를 사용 하도록 설정 합니다. **Windows Firewall**  ->  **도메인 및 개인** 네트워크의 Windows 방화벽 허용 되는**앱 및 기능**에서 RDP가 허용 되는지 확인 합니다.<br/><br/>  운영 체제의 SAN 정책이 **OnlineAll**로 설정되어 있는지 확인합니다. [자세히 알아보기](https://support.microsoft.com/kb/3031135).<br/><br/> 장애 조치를 트리거할 때 VM에 보류 중인 Windows 업데이트가 없는지 확인합니다. 장애 조치 (failover) 시 Windows 업데이트 시작 될 수 있으며 업데이트가 완료 될 때까지 VM에 로그온 할 수 없습니다.
**Windows를 실행하는 Azure VM** | 장애 조치 (failover) 후 Azure VM에서 |  VM에 대한 [공용 IP 주소를 추가](/archive/blogs/srinathv/how-to-add-a-public-ip-address-to-azure-vm-for-vm-failed-over-using-asr)합니다.<br/><br/> 장애 조치 (failover) 된 VM 및 연결 된 Azure 서브넷의 네트워크 보안 그룹 규칙은 RDP 포트에 대 한 들어오는 연결을 허용 해야 합니다.<br/><br/> **부트 진단**을 확인하여 VM에 대한 스크린샷을 검토합니다. 연결할 수 없는 경우 VM이 실행 중인지 확인 하 고 [문제 해결 팁](https://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx)을 검토 합니다.
**Linux를 실행하는 Azure VM** | 장애 조치 (failover) 전 온-프레미스 컴퓨터 | VM의 보안 셸 서비스가 시스템 부팅 시 자동으로 시작되도록 설정되어 있는지 확인합니다.<br/><br/> 방화벽 규칙이 SSH 연결을 허용하는지 확인합니다.
**Linux를 실행하는 Azure VM** | 장애 조치 (failover) 후 Azure VM에서 | 장애 조치(Failover)된 VM 그리고 해당 VM이 연결된 Azure 서브넷의 네트워크 보안 그룹 규칙이 SSH 포트로 들어오는 연결을 허용해야 합니다.<br/><br/> VM에 대한 [공용 IP 주소를 추가](/archive/blogs/srinathv/how-to-add-a-public-ip-address-to-azure-vm-for-vm-failed-over-using-asr)합니다.<br/><br/> **부트 진단**에서 VM에 대한 스크린샷을 검토합니다.<br/><br/>

## <a name="types-of-failover"></a>장애 조치(Failover) 유형

Site Recovery는 다른 장애 조치 (failover) 옵션을 제공 합니다.

**장애 조치(Failover)** | **세부 정보** | **복구** | **Workflow**
--- | --- | --- | ---
**테스트 장애 조치** | 데이터 손실이 나 가동 중지 시간 없이 BCDR 전략의 유효성을 검사 하는 드릴을 실행 하는 데 사용 됩니다.| 진행 중인 복제 또는 프로덕션 환경에 영향을 주지 않고 Azure에서 VM의 복사본을 만듭니다. | 1. 단일 VM 또는 복구 계획의 여러 Vm에서 테스트 장애 조치 (failover)를 실행 합니다.<br/><br/> 2. 테스트 장애 조치 (failover)에 사용할 복구 지점을 선택 합니다.<br/><br/> 3. 장애 조치 (failover) 후 Azure VM이 생성 될 때 배치 될 Azure 네트워크를 선택 합니다. 네트워크는 테스트 장애 조치 (failover)에만 사용 됩니다.<br/><br/> 4. 정상적으로 작동 하는지 확인 합니다. Site Recovery는 드릴 하는 동안 Azure에서 만든 Vm을 자동으로 정리 합니다.
**계획 된 장애 조치 (failover)-Hyper-v**  | 일반적으로 계획 된 가동 중지 시간에 사용 됩니다.<br/><br/> 원본 Vm이 종료 됩니다. 최신 데이터는 장애 조치 (failover)를 시작 하기 전에 동기화 됩니다. | 계획 된 워크플로의 데이터 손실이 0입니다. | 1. 가동 중지 시간 유지 관리 기간을 계획 하 고 사용자에 게 알립니다.<br/><br/> 2. 사용자 연결 앱을 오프 라인으로 전환 합니다.<br/><br/> 3. 최신 복구 지점으로 계획 된 장애 조치 (failover)를 시작 합니다. 컴퓨터가 종료 되지 않거나 오류가 발생 한 경우에는 장애 조치 (failover)가 실행 되지 않습니다.<br/><br/> 4. 장애 조치 (failover) 후 azure VM이 Azure에서 활성 상태 인지 확인 합니다.<br/><br/> 5. 장애 조치 (failover)를 커밋하여 완료 합니다. 커밋 작업은 모든 복구 지점이 삭제 됩니다.
**장애 조치 (Failover)-Hyper-v** | 일반적으로 계획 되지 않은 중단이 발생 하거나 기본 사이트를 사용할 수 없는 경우에 실행 됩니다.<br/><br/> 필요에 따라 VM을 종료 하 고 장애 조치 (failover)를 시작 하기 전에 최종 변경 내용을 동기화 합니다.  | 앱에 대 한 최소 데이터 손실. | 1. BCDR 계획을 시작 합니다. <br/><br/> 2. 장애 조치 (failover)를 시작 합니다. 장애 조치 (failover)를 트리거하기 전에 Site Recovery VM을 종료 하 고 최신 변경 내용을 동기화/복제 해야 하는지 여부를 지정 합니다.<br/><br/> 3. 아래 표에 요약 된 여러 복구 지점 옵션에 대 한 장애 조치 (failover)를 수행할 수 있습니다.<br/><br/> VM을 종료 하는 옵션을 사용 하지 않거나 Site Recovery 종료할 수 없는 경우 최신 복구 지점이 사용 됩니다.<br/>장애 조치 (failover)는 컴퓨터를 종료할 수 없는 경우에도 실행 됩니다.<br/><br/> 4. 장애 조치 (failover) 후 azure VM이 Azure에서 활성 상태 인지 확인 합니다.<br/> 필요한 경우 24 시간 동안 보존 기간에서 다른 복구 지점을 선택할 수 있습니다.<br/><br/> 5. 장애 조치 (failover)를 커밋하여 완료 합니다. 커밋 작업은 사용 가능한 모든 복구 지점이 삭제 됩니다.
**장애 조치 (Failover)-VMware** | 일반적으로 계획 되지 않은 중단이 발생 하거나 기본 사이트를 사용할 수 없는 경우에 실행 됩니다.<br/><br/> 필요에 따라 VM의 종료를 트리거하고 장애 조치 (failover)를 시작 하기 전에 최종 변경 내용을 동기화 하 고 복제 하도록 Site Recovery를 지정 합니다.  | 앱에 대 한 최소 데이터 손실. | 1. BCDR 계획을 시작 합니다. <br/><br/> 2. Site Recovery에서 장애 조치 (failover)를 시작 합니다. 장애 조치 (failover)를 실행 하기 전에 Site Recovery VM 종료 및 동기화를 시도해 야 하는지 여부를 지정 합니다.<br/> 장애 조치 (failover)는 컴퓨터를 종료할 수 없는 경우에도 실행 됩니다.<br/><br/> 3. 장애 조치 (failover) 후 azure VM이 Azure에서 활성 상태 인지 확인 합니다. <br/>필요한 경우 보존 기간 72 시간에서 다른 복구 지점을 선택할 수 있습니다.<br/><br/> 5. 장애 조치 (failover)를 커밋하여 완료 합니다. 커밋 작업은 모든 복구 지점이 삭제 됩니다.<br/> Windows VM의 경우 Site Recovery는 장애 조치(failover) 중에 VMware 도구를 사용하지 않도록 설정합니다.

## <a name="failover-processing"></a>장애 조치 (Failover) 처리

일부 시나리오에서는 장애 조치(failover)를 위해서는 추가 처리가 필요하며 이러한 작업을 완료하는 데는 약 8~10분이 소요됩니다. 다음에 대한 테스트 장애 조치(failover) 시간이 길어질 수 있습니다.

* 9.8 이전의 모바일 서비스 버전을 실행하는 VMware VM
* 실제 서버
* VMware Linux VM
* 물리적 서버로 보호되는 Hyper-V VM
* DHCP 서비스를 사용하도록 설정되지 않은 VMware VM
* 다음 부팅 드라이버가 없는 VMware VM: storvsc, vmbus, storflt, intelide, atapi.

## <a name="recovery-point-options"></a>복구 지점 옵션

장애 조치 (failover) 중에 다양 한 복구 지점 옵션을 선택할 수 있습니다.

**옵션** | **세부 정보**
--- | ---
**최신 (가장 낮은 RPO)** | 이 옵션은 가장 낮은 RPO(복구 지점 목표)를 제공합니다. 먼저 Site Recovery 서비스로 보낸 모든 데이터를 처리 하 여 장애 조치 (failover) 전에 각 VM에 대 한 복구 지점을 만듭니다. 이 복구 지점에는 장애 조치 (failover)가 트리거될 때 Site Recovery에 복제 된 모든 데이터가 포함 됩니다.
**가장 최근에 처리 됨**  | 이 옵션은 Vm을 Site Recovery에서 처리 된 최신 복구 지점으로 장애 조치 (failover) 합니다. 특정 VM에 대한 최신 복구 지점을 보려면 VM 설정에서 **최신 복구 지점**을 선택하세요. 이 옵션은 처리되지 않은 데이터를 처리하는 데 시간이 투입되지 않으므로 낮은 RTO(복구 시간 목표)를 제공합니다.
**최신 앱 일치** |  이 옵션은 앱 일치 복구 지점을 사용 하도록 설정 된 경우 Vm을 Site Recovery에서 처리 된 최신 응용 프로그램 일치 복구 지점으로 장애 조치 (failover) 합니다. VM 설정에서 최신 복구 지점을 확인 합니다.
**최신 다중 VM 처리 됨** | 이 옵션은 다중 VM 일치가 사용하도록 설정된 하나 이상의 VM이 포함된 복구 계획에 사용할 수 있습니다. 이 설정이 사용하도록 설정된 VM은 최신 일반 다중 VM 일치 복구 지점으로 장애 조치(Failover)됩니다. 계획의 다른 Vm은 가장 최근에 처리 된 복구 지점으로 장애 조치 (failover) 됩니다.
**최신 다중 VM 앱 일치** |  이 옵션은 다중 VM 일치가 사용하도록 설정된 하나 이상의 VM이 포함된 복구 계획에 사용할 수 있습니다. 복제 그룹에 속하는 VM은 공통된 최신 다중 VM 애플리케이션 일관성 복구 지점으로 장애 조치(failover)됩니다. 다른 VM은 최신 애플리케이션 일치 복구 지점으로 장애 조치(Failover)됩니다.
**Custom** | 특정 VM을 특정 복구 시점으로 장애 조치 (failover) 하려면이 옵션을 사용 합니다. 이 옵션은 복구 계획에 사용할 수 없습니다.

> [!NOTE]
> 복구 지점은 다른 Recovery Services 자격 증명 모음으로 마이그레이션할 수 없습니다.

## <a name="reprotectionfailback"></a>다시 보호/장애 복구

Azure로 장애 조치 (failover) 후 복제 된 Azure Vm은 보호 되지 않은 상태에 있습니다.

- 온-프레미스 사이트로 장애 복구 (failback) 하는 첫 번째 단계로 온-프레미스로 복제 되는 Azure Vm을 시작 해야 합니다. 다시 보호 프로세스는 장애 조치 (failover) 된 컴퓨터의 유형에 따라 달라 집니다.
- 컴퓨터를 Azure에서 온-프레미스로 복제 한 후 Azure에서 온-프레미스 사이트로 장애 조치 (failover)를 실행할 수 있습니다.
- 컴퓨터를 온-프레미스에서 다시 실행 한 후에는 재해 복구를 위해 Azure에 복제 하도록 복제를 사용 하도록 설정할 수 있습니다.

장애 복구는 다음과 같이 작동 합니다.

- 장애 복구 (failback)를 수행 하려면 VM이 장애 복구 (failback)를 위해 하나 이상의 복구 지점을 필요로 합니다. 복구 계획에서 계획의 모든 Vm에는 하나 이상의 복구 지점이 필요 합니다.
- **최신** 복구 지점을 사용 하 여 장애 복구 하는 것이 좋습니다 (충돌 일치 지점).
    - 앱 일치 복구 지점 옵션이 있습니다. 이 경우 단일 VM은 사용 가능한 최신 앱 일치 복구 지점으로 복구 됩니다. 복제 그룹이 있는 복구 계획의 경우 각 복제 그룹은 사용 가능한 일반 복구 지점으로 복구 됩니다.
    - 앱 일치 복구 지점은 시간이 뒤에 있을 수 있으며 데이터가 손실 될 수 있습니다.
- Azure에서 온-프레미스 사이트로 장애 조치 (failover) 하는 동안 Site Recovery는 Azure Vm을 종료 합니다. 장애 조치 (failover)를 커밋하면 Azure에서 장애 복구 된 Azure Vm을 제거 Site Recovery.


## <a name="vmwarephysical-reprotectionfailback"></a>VMware/물리적 다시 보호/장애 복구

VMware 컴퓨터와 물리적 서버를 Azure에서 온-프레미스로 다시 보호 하 고 장애 복구 (failback) 하려면 장애 복구 (failback) 인프라가 필요 하며 몇 가지 요구 사항이 있습니다.

- **Azure의 임시 프로세스 서버**: Azure에서 장애 복구하려면 Azure에서 복제를 처리하는 프로세스 서버로 작동하도록 Azure VM을 설정합니다. 장애 복구를 완료한 후 이 VM을 삭제할 수 있습니다.
- **VPN 연결**: 장애 복구하려면 Azure 네트워크에서 온-프레미스 사이트로의 VPN 연결(또는 ExpressRoute)이 필요합니다.
- **별도의 마스터 대상 서버**: 기본적으로 구성 서버와 함께 온-프레미스 VMware VM에 설치된 마스터 대상 서버에서 장애 복구를 처리합니다. 대량 트래픽을 장애 복구해야 하는 경우 별도의 온-프레미스 마스터 대상 서버를 이 용도로 설정합니다.
- **장애 복구 정책**: 온-프레미스 사이트에 다시 복제하려면 장애 복구 정책이 필요합니다. 이 정책은 온-프레미스에서 Azure로의 복제 정책을 만들 때 자동으로 만들어졌습니다.
    - 이 정책은 구성 서버와 자동으로 연결 됩니다.
    - 이 정책은 편집할 수 없습니다.
    - 정책 값: RPO 임계값-15 분 복구 지점 보존-24 시간 앱 일치 스냅숏 빈도-60 분

VMware/물리적 다시 보호 및 장애 복구 (failback)에 대해 자세히 알아보세요.
- 다시 보호 및 장애 복구 (failback)에 대 한 추가 요구 사항을 [검토](vmware-azure-reprotect.md#before-you-begin) 합니다.
- Azure에서 프로세스 서버를 [배포](vmware-azure-prepare-failback.md#deploy-a-process-server-in-azure) 합니다.
- 별도의 마스터 대상 서버를 [배포](vmware-azure-prepare-failback.md#deploy-a-separate-master-target-server) 합니다.

Azure Vm을 온-프레미스로 다시 보호 하는 경우 원래 위치 또는 대체 위치로 장애 복구 (failback) 하도록 지정할 수 있습니다.

- **원래 위치 복구**: Azure에서 동일한 원본 온-프레미스 컴퓨터로 장애 복구 (있는 경우) 합니다. 이 시나리오에서는 변경 내용만 온-프레미스로 다시 복제 됩니다.
- **대체 위치 복구**: 온-프레미스 컴퓨터가 존재 하지 않는 경우 Azure에서 대체 위치로 장애 복구 (failback) 할 수 있습니다. Azure VM을 온-프레미스로 다시 보호 하는 경우 온-프레미스 컴퓨터가 만들어집니다. 전체 데이터 복제는 Azure에서 온-프레미스로 발생 합니다. --위치 장애 복구 (failback)에 대 한 요구 사항 및 제한 사항을 [검토](concepts-types-of-failback.md) 합니다.



## <a name="hyper-v-reprotectionfailback"></a>Hyper-v 다시 보호/장애 복구

Hyper-v Vm을 Azure에서 온-프레미스로 다시 보호 및 장애 복구 (failback) 하려면 다음을 수행 합니다.

- 저장소 계정을 사용 하 여 복제 하는 Hyper-v Vm만 장애 복구 (failback) 할 수 있습니다. 관리 디스크를 사용 하 여 복제 하는 Hyper-v Vm의 장애 복구 (Failback)는 지원 되지 않습니다.
- 온-프레미스 Hyper-v 호스트 (또는 사용 되는 경우 System Center VMM)를 Azure에 연결 해야 합니다.
- Azure에서 온-프레미스로 계획 된 장애 복구 (failback)를 실행 합니다.
- Hyper-v VM 장애 복구 (failback)에 대해 설정 해야 하는 특정 구성 요소가 없습니다.
- 계획 된 장애 조치 (failover) 중에 장애 복구 전에 데이터를 동기화 하는 옵션을 선택할 수 있습니다.
    - **장애 조치 (failover) 전 데이터 동기화**:이 옵션은 컴퓨터를 종료 하지 않고도 동기화 하는 가상 컴퓨터의 가동 중지 시간을 최소화 합니다.
        - 1 단계: Azure VM의 스냅숏을 만들어 온-프레미스 Hyper-v 호스트에 복사 합니다. 컴퓨터가 Azure에서 계속 실행됩니다.
        - 2 단계: Azure VM을 종료 하 여 새로운 변경이 발생 하지 않도록 합니다. 최종 델타 변경 집합은 온-프레미스 서버에 전송 되 고 온-프레미스 VM이 시작 됩니다.
    - **장애 조치 (failover) 중에만 데이터 동기화**:이 옵션은 대부분의 디스크가 변경 되었으므로 체크섬 계산을 수행 하지 않기 때문에 더 빠릅니다. 디스크의 다운로드를 수행합니다. VM이 while (한 달 이상)에 대해 Azure에서 실행 되 고 있거나 온-프레미스 VM이 삭제 된 경우이 옵션을 사용 하는 것이 좋습니다.

Hyper-v 다시 보호 및 장애 복구에 [대해 자세히 알아보세요](hyper-v-azure-failback.md) .

Azure Vm을 온-프레미스로 다시 보호 하는 경우 원래 위치 또는 대체 위치로 장애 복구 (failback) 하도록 지정할 수 있습니다.

- **원래 위치 복구**: Azure에서 동일한 원본 온-프레미스 컴퓨터로 장애 복구 (있는 경우) 합니다. 이 시나리오에서는 이전 절차에서 설명한 동기화 옵션 중 하나를 선택 합니다.
- **대체 위치 복구**: 온-프레미스 컴퓨터가 존재 하지 않는 경우 Azure에서 대체 위치로 장애 복구 (failback) 할 수 있습니다. Azure VM을 온-프레미스로 다시 보호 하는 경우 온-프레미스 컴퓨터가 만들어집니다. 이 옵션을 사용 하면 장애 조치 (failover) 전에 데이터를 동기화 하는 옵션을 선택 하는 것이 좋습니다.
- 위치 장애 복구 (failback)에 대 한 요구 사항 및 제한 사항을 [검토](hyper-v-azure-failback.md) 합니다.


온-프레미스 사이트로 장애 복구 (failback) 한 후 **역방향 복제** 를 사용 하 여 VM을 Azure로 복제 하 고, 주기를 완료 합니다.




## <a name="next-steps"></a>다음 단계
- [특정 VMware vm](vmware-azure-tutorial-failover-failback.md) 장애 조치 (failover)
- [특정 Hyper-v vm](hyper-v-azure-failover-failback-tutorial.md)을 장애 조치 (failover) 합니다.
- 복구 계획을 [만듭니다](site-recovery-create-recovery-plans.md) .
- [복구 계획에서 vm](site-recovery-failover.md)을 장애 조치 (failover) 합니다.
- [준비](vmware-azure-failback.md) VMware 다시 보호 및 장애 복구 (failback)
- [Hyper-v vm](hyper-v-azure-failback.md)을 장애 복구 (failback) 합니다.