---
title: "Site Recovery를 사용하여 복제된 Hyper-V VM을 Azure로 장애 조치(failover) 및 장애 복구(failback) | Microsoft Docs"
description: "Azure Site Recovery를 사용하여 VMware VM을 Azure로 장애 조치(failover)하고, 온-프레미스 사이트로 장애 복구(failback)하는 방법을 알아봅니다."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 72065d01-ed0f-430e-b117-a5885cecd1db
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 09/15/2017
ms.author: raynew
ms.openlocfilehash: e1cc21661450a983c25b24fe2a6228e26ceecec6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="fail-over-and-fail-back-hyper-v-vms-replicated-to-azure"></a>복제된 Hyper-V VM을 Azure로 장애 조치(failover) 및 장애 복구(failback)

[Azure Site Recovery](site-recovery-overview.md) 서비스는 온-프레미스 컴퓨터 및 Azure VM(Virtual Machines)의 복제, 장애 조치(failover) 및 장애 복구(failback)를 관리하고 오케스트레이션합니다.

이 자습서에서는 Hyper-V VM을 Azure로 장애 조치(failover)하는 방법을 설명합니다. 장애 조치(failover)한 후에 온-프레미스 사이트가 사용 가능해지면 장애 복구(failback)합니다. 이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 온-프레미스에서 Azure로 Hyper-V VM 장애 조치(failover)
> * Azure에서 온-프레미스로 장애 복구(failback) 및 다시 Azure에 복제 시작

## <a name="overview"></a>개요
장애 조치(failover) 및 장애 복구(failback)는 다음 2단계로 진행됩니다.

1. **Azure로 장애 조치(failover)**: 온-프레미스 사이트에서 Azure로 컴퓨터를 장애 조치합니다.
2. **Azure에서 온-프레미스로 장애 복구(failback)**: Azure에서 온-프레미스로 계획된 장애 조치(failover)를 실행합니다. 계획된 장애 조치(failover) 후에는 역방향 복제를 사용하여 온-프레미스에서 Azure로 복제를 시작할 수 있습니다. 


## <a name="fail-over-to-azure"></a>Azure로 장애 조치(failover)

### <a name="failover-prerequisites"></a>장애 조치 필수 구성 요소

장애 조치(failover)를 완료하려면 다음이 필요합니다.

- [재해 복구 드릴](tutorial-dr-drill-azure.md)을 완료하여 모든 항목이 예상대로 작동하는지 확인합니다.
- 필요에 따라 장애 조치(failover) 전에 Azure VM에 연결하도록 준비합니다.
- 장애 조치(failover)를 실행하기 전에 VM 속성을 확인합니다.

#### <a name="prepare-to-connect-to-azure-vms-after-failover"></a>장애 조치(Failover) 후 Azure VM에 연결할 준비

장애 조치(failover) 후 RDP를 사용하여 Azure VM에 연결하려면 다음을 수행합니다.

##### <a name="azure-vms-running-windows"></a>Windows가 실행되는 Azure VM

1. 인터넷을 통해 Azure VM에 액세스하려면 장애 조치(failover) 전에 온-프레미스 VM에서 RDP를 사용합니다. TCP 및 UDP 규칙이 **공용** 프로필에 추가되었는지 그리고 **Windows 방화벽** > **허용되는 앱**에서 모든 프로필에 대해 RDP가 허용되는지 확인합니다.
2. 사이트 간 VPN을 통해 액세스하려면 온-프레미스 컴퓨터에서 RDP를 활성화합니다. RDP가 **Windows 방화벽** -> **허용되는 앱 및 기능**에서 **도메인 또는 사설** 네트워크에 대해 허용되어야 합니다. 운영 체제의 SAN 정책이 **OnlineAll**로 설정되어 있는지 확인합니다. [자세히 알아봅니다](https://support.microsoft.com/kb/3031135). 장애 조치를 트리거할 때 VM에 보류 중인 Windows 업데이트가 없어야 합니다. 있는 경우 업데이트가 완료될 때까지 가상 컴퓨터에 로그인할 수 없습니다. 
3. 장애 조치 후 Microsoft Azure VM에서 **부트 진단**을 확인하여 VM의 스크린샷을 검토합니다. 연결할 수 없는 경우 VM이 실행 중인지 확인한 다음 해당 [문제 해결 팁](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx)(영문)을 검토합니다.


##### <a name="azure-vms-running-linux"></a>Linux가 실행되는 Azure VM

1. 장애 조치(failover) 전에 온-프레미스 컴퓨터에서 Secure Shell 서비스가 시스템 부팅 시 자동으로 시작되도록 설정되어 있는지 확인합니다. 방화벽 규칙에서 SSH 연결을 허용하는지 확인합니다.
2. 장애 조치 후 Azure VM에서 장애 조치된 VM의 네트워크 보안 그룹 규칙 및 연결되어 있는 Azure 서브넷에 대한 SSH 포트로 들어오는 연결을 허용합니다.  VM에 대한 [공용 IP 주소를 추가](site-recovery-monitoring-and-troubleshooting.md#adding-a-public-ip-on-a-resource-manager-virtual-machine)합니다. **부트 진단**을 확인하여 VM의 스크린샷을 볼 수 있습니다.


#### <a name="verify-vm-properties"></a>VM 속성 확인

VM 속성을 확인하고 VM이 [Azure 요구 사항](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements)을 준수하는지 확인합니다.

1. **보호된 항목**에서 **복제된 항목** > VM을 클릭합니다.
2. **복제된 항목** 창에 VM 정보, 상태 및 최신 사용 가능한 복구 지점의 요약이 제공됩니다. 자세한 내용을 보려면 **속성**을 클릭합니다.
3. **계산 및 네트워크**에서 Azure 이름, 리소스 그룹, 대상 크기, [가용성 집합](../virtual-machines/windows/tutorial-availability-sets.md) 및 [관리되는 디스크 설정](#managed-disk-considerations)을 수정할 수 있습니다.
4. 장애 조치(failover) 후 Azure VM이 배치될 네트워크/서브넷 및 할당되는 IP 주소를 포함한 네트워크 설정을 보고 수정할 수 있습니다.
5. **디스크**에서 VM의 운영 체제 및 데이터 디스크에 대한 정보를 볼 수 있습니다.


### <a name="run-a-failover-from-on-premises-to-azure"></a>온-프레미스에서 Azure로 장애 조치(failover) 실행

Hyper-V VM에 대해 주기적이거나 계획된 장애 조치(failover)를 실행할 수 있습니다.

- 예기치 않은 중단에 대비해서 주기적인 장애 조치(failover)를 사용합니다. 이 장애 조치(failover)를 실행하면 Site Recovery에서 Azure VM을 만들고 작동시킵니다. 특정 복구 지점에 대해 장애 조치(failover)를 실행합니다. 사용하는 복구 지점에 따라 데이터 손실이 발생할 수 있습니다.
- 유지 관리 또는 예정된 중단에 대해서는 계획된 장애 조치(failover)를 사용할 수 있습니다. 이 옵션은 데이터 손실을 발생하지 않습니다. 계획된 장애 조치(failover)가 트리거되면 원본 VM이 종료됩니다. 동기화되지 않은 데이터가 동기화되고 장애 조치(failover)가 트리거됩니다.

이 절차에서는 주기적 장애 조치(failover)를 실행하는 방법을 설명합니다.


1. **설정** > **복제된 항목**에서 VM > **장애 조치(Failover)**를 클릭합니다.
2. **장애 조치(Failover)**에서 장애 조치할 **복구 지점**을 선택합니다. 다음 옵션 중 하나를 사용할 수 있습니다.
    - **최신**(기본값): 이 옵션은 먼저 Site Recovery로 전송된 모든 데이터를 처리합니다. 이 옵션은 장애 조치(failover) 후에 생성된 Azure VM은 장애 조치(failover)가 트리거되었을 때 Site Recovery로 복제된 모든 데이터를 보유하므로 가장 낮은 RPO(복구 지점 목표)를 제공합니다.
    - **가장 최근에 처리됨**: 이 옵션은 VM을 Site Recovery에서 처리된 최신 복구 지점으로 장애 조치합니다. 이 옵션은 처리되지 않은 데이터를 처리하는 데 시간이 투입되지 않으므로 낮은 RTO(복구 시간 목표)를 제공합니다.
    - **최신 앱 일치**: 이 옵션은 VM을 Site Recovery에서 처리된 최신 앱 일치 복구 지점으로 장애 조치합니다. 
    - **사용자 지정**: 복구 지점을 지정합니다.
3. System Center VMM 클라우드의 Hyper-V VM을 Azure로 장애 조치(failover)하고 클라우드에 대해 데이터 암호화를 사용하는 경우 **암호화 키**에서 VMM 서버에서 공급자 설정 중에 데이터 암호화를 활성화할 때 발행된 인증서를 선택합니다.
4. 장애 조치를 트리거하기 전에 Site Recovery에서 원본 가상 컴퓨터를 종료하려고 시도하는 경우 **장애 조치(failover)를 시작하기 전에 컴퓨터를 종료합니다**를 선택합니다. 또한 Site Recovery는 장애 조치(failover)를 트리거하기 전에 Azure로 아직 전송되지 않은 온-프레미스 데이터를 동기화하려고 합니다. 종료가 실패하더라도 장애 조치(failover)는 계속됩니다. **작업** 페이지에서 장애 조치 진행 상황 확인을 수행할 수 있습니다.
5. Azure VM에 연결할 준비가 된 경우 연결하고, 장애 조치(failover) 후에 유효성을 검사합니다.
6. 확인한 후 장애 조치(failover)를 **커밋**합니다. 그러면 사용 가능한 복구 지점이 모두 삭제됩니다.

> [!WARNING]
> **진행 중인 장애 조치(failover) 취소 안 함**: 장애 조치(failover)를 시작하기 전에 VM 복제가 중지됩니다. 진행 중인 장애 조치(failover)를 취소하면 장애 조치(failover)가 중지되지만 VM은 다시 복제되지 않습니다.  


## <a name="fail-back-from-azure-to-on-premises"></a>Azure에서 온-프레미스로 장애 복구(failback)

### <a name="prerequisites-for-failback"></a>장애 복구(failback)에 대한 필수 조건

장애 복구(failback)를 완료하려면 기본 사이트 VMM 서버/Hyper-V 서버가 Site Recovery에 연결되어 있는지 확인합니다.


### <a name="run-a-failback-and-reprotect-on-premises-vms"></a>장애 복구(failback) 실행 및 온-프레미스 VM 다시 보호

Azure에서 온-프레미스 사이트로 장애 조치(failover)한 후 온-프레미스 사이트에서 Azure로 VM을 복제하기 시작합니다.

1. **설정** > **복제된 항목**에서 VM > **계획된 장애 조치(Failover)**를 클릭합니다.
2. **계획된 장애 조치(Failover) 확인**에서 장애 조치(failover) 방향(Azure에서)을 확인하고 원본 위치와 대상 위치를 선택합니다. 
3. **데이터 동기화**에서 동기화 방법을 지정합니다.
    - **장애 조치(failover) 전 데이터 동기화(델타 변경 내용만 동기화)** - 이 옵션에서는 VM을 종료하지 않고 동기화하기 때문에 VM 가동 중지 시간이 최소로 유지됩니다. 다음과 같은 작업이 수행됩니다.
        1. Azure VM의 스냅숏을 생성한 후 온-프레미스 Hyper-V 호스트로 복사합니다. VM이 계속 Azure에서 실행됩니다.
        2. Azure VM이 종료되어, 여기서 새로운 변경이 발생하지 않습니다. 최종 델타 변경 집합이 온-프레미스 서버로 전송되고, 온-프레미스 VM이 시작합니다.
    - **장애 조치(failover) 중에만 데이터 동기화(전체 다운로드)** - 오랜 시간 동안 Azure에서 실행한 경우 이 옵션을 사용합니다. 이 옵션에서는 여러 디스크가 변경되고 체크섬 계산에 시간이 소비되지 않으므로 더 빠릅니다. 이 옵션은 디스크 다운로드를 수행합니다. 온-프레미스 VM이 삭제된 경우에도 유용합니다.
4. System Center VMM 클라우드의 Hyper-V VM을 Azure로 장애 조치(failover)하고 클라우드에 대해 데이터 암호화를 사용하는 경우 **암호화 키**에서 VMM 서버에서 공급자 설정 중에 데이터 암호화를 활성화할 때 발행된 인증서를 선택합니다.
5. 장애 조치를 시작합니다. **작업** 탭에서 장애 조치 진행 상황을 따를 수 있습니다.
6. 장애 조치(failover) 전에 데이터를 동기화하도록 선택한 경우, 초기 데이터 동기화가 완료되고 Azure VM을 종료할 준비가 되면, **작업** > 계획된 장애 조치(failover) 작업 이름 > **장애 조치(Failover) 완료**를 차례로 클릭합니다. 그러면 Azure VM이 종료되고, 온-프레미스로 최신 변경 내용이 전송되며, 온-프레미스 VM이 시작됩니다.
7. 온-프레미스 VM에 로그온하여 예상대로 사용 가능한지 확인합니다.
8. 온-프레미스 VM은 이제 커밋 보류 중 상태가 됩니다. **커밋**을 클릭하여 장애 조치(failover)를 커밋합니다. 그러면 Azure VM 및 해당 디스크가 삭제되고, 온-프레미스 VM이 역방향 복제를 위해 준비됩니다.
9. 온-프레미스 VM을 Azure에 복제하기 시작하려면 **역방향 복제**를 사용합니다.


> [!NOTE]
> 역방향 복제는 Azure VM이 해제된 후에 발생한 변경 내용만 복제하며 델타 변경 내용만 전송됩니다.

