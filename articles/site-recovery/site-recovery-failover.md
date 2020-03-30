---
title: Azure 사이트 복구를 사용 하 여 재해 복구 하는 동안 장애 조치 실행
description: Azure 사이트 복구를 사용하여 VM/물리적 서버를 Azure로 장애 조치하는 방법
ms.service: site-recovery
ms.topic: article
ms.date: 12/10/2019
ms.openlocfilehash: 99a197e8f5ebac8a3b0be1b567ee41b43a2c4476
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79471271"
---
# <a name="run-a-failover-from-on-premises-to-azure"></a>온-프레미스에서 Azure로 장애 조치(failover) 실행

이 문서에서는 [Azure 사이트 복구에서](site-recovery-overview.md) 온-프레미스 컴퓨터를 Azure에 장애 조치하는 방법을 설명합니다.

## <a name="before-you-start"></a>시작하기 전에

- 재해 복구의 장애 조치 프로세스에 대해 [알아봅니다.](failover-failback-overview.md)
- 여러 컴퓨터에서 장애 조치(failover)하려면 복구 계획에서 컴퓨터를 함께 수집하는 방법을 [알아봅니다.](recovery-plan-overview.md)
- 전체 장애 조치(failover)를 수행하기 전에 [재해 복구 드릴을](site-recovery-test-failover-to-azure.md) 실행하여 모든 것이 예상대로 작동하는지 확인합니다.

## <a name="prepare-to-connect-after-failover"></a>장애 조치 후 연결 준비

장애 조치 후 생성된 Azure VM에 연결할 수 있도록 장애 조치 전에 온-프레미스에서 수행해야 하는 여러 가지 작업을 수행합니다.


### <a name="prepare-on-premises-to-connect-after-failover"></a>장애 조치 후 연결하기 위해 온-프레미스 준비

장애 조치 후 RDP/SSH를 사용하여 Azure VM에 연결하려는 경우 장애 조치 전에 온-프레미스에서 수행해야 하는 여러 가지 사항이 있습니다.

**장애 조치(failover) 후** | **위치** | **작업**
--- | --- | ---
**Windows를 실행하는 Azure VM** | 장애 조치(Failover) 전에 온-프레미스 컴퓨터에서 | 인터넷을 통해 Azure VM에 액세스하려면 RDP를 활성화하고, TCP 및 UDP 규칙이 **공용**에 추가되었는지 그리고 **Windows 방화벽** > **허용되는 앱**에서 모든 프로필에 대해 RDP가 허용되는지 확인합니다.<br/><br/> 사이트 간 연결을 통해 Azure VM에 액세스하려면 컴퓨터에서 RDP를 사용하도록 설정하고 **도메인 및 개인** 네트워크에 대해 Windows **방화벽** -> **허용 앱 및 기능에서**RDP가 허용되는지 확인합니다.<br/><br/> <br/><br/> 정적 영구 경로 및 WinHTTP 프록시를 제거합니다. 운영 체제의 SAN 정책이 **OnlineAll**로 설정되어 있는지 확인합니다. [자세히 알아봅니다](https://support.microsoft.com/kb/3031135).<br/><br/> 장애 조치를 트리거할 때 VM에 보류 중인 Windows 업데이트가 없는지 확인합니다. 장애 조치 시 Windows 업데이트가 시작될 수 있으며, 업데이트를 완료할 때까지 VM에 로그인할 수 없습니다.
**Linux를 실행하는 Azure VM** | 장애 조치(Failover) 전에 온-프레미스 컴퓨터에서 | VM의 보안 셸 서비스가 시스템 부팅 시 자동으로 시작되도록 설정되어 있는지 확인합니다.<br/><br/> 방화벽 규칙이 SSH 연결을 허용하는지 확인합니다.


## <a name="run-a-failover"></a>장애 조치(Failover) 실행

이 절차에서는 [복구 계획](site-recovery-create-recovery-plans.md)에 대한 장애 조치를 실행하는 방법을 설명합니다. 단일 VM에 대해 장애 조치(failover)를 실행하려면 [VMware VM,](vmware-azure-tutorial-failover-failback.md) [물리적 서버](physical-to-azure-failover-failback.md)또는 [Hyper-V VM에](hyper-v-azure-failover-failback-tutorial.md)대한 지침을 따릅니다.


다음과 같이 복구 계획 장애 조치(failover)를 실행합니다.

1. 사이트 복구 자격 증명 모음에서 **복구 계획** > *recoveryplan_name*선택합니다.
2. **장애 조치**를 클릭합니다.

    ![장애 조치](./media/site-recovery-failover/Failover.png)

3. **장애 조치(Failover)** > **방향에서**Azure로 복제하는 경우 기본값을 그대로 둡니다.
4. **장애 조치(Failover)에서**장애 조치(failover)를 초과할 **복구 지점을** 선택합니다.

    - **최신**: 최신 포인트를 사용합니다. 이렇게 하면 사이트 복구 서비스로 전송된 모든 데이터가 처리되고 각 컴퓨터에 대한 복구 지점이 만들어집니다. 이 옵션은 장애 조치 후 생성된 VM에 장애 조치가 트리거될 때 사이트 복구에 복제된 모든 데이터가 있기 때문에 가장 낮은 RPO(복구 지점 목표)를 제공합니다.
   - **최신 처리 :** 이 옵션을 사용하여 VM을 사이트 복구에서 이미 처리한 최신 복구 지점으로 장애 조치합니다. VM 최신 복구 지점에서 최신 처리된 복구 지점을 볼 수 **있습니다.** 이 옵션은 처리되지 않은 데이터를 처리하는 데 소요되는 시간이 없기 때문에 낮은 RTO를 제공합니다.
   - **최신 앱 일관성**: 이 옵션을 사용하여 사이트 복구에서 처리된 최신 응용 프로그램 일관된 복구 지점으로 VM을 장애 조치합니다.
   - **처리된 최신 다중 VM**: 복제 그룹의 일부인 이 옵션 VM을 사용하여 최신 공통 다중 VM 일관된 복구 지점으로 장애 조치(failover)됩니다. 다른 가상 시스템은 최신 처리된 복구 지점으로 장애 조치됩니다. 이 옵션은 다중 VM 일관성이 활성화된 하나 이상의 VM이 있는 복구 계획에만 해당됩니다.
   - **최신 다중 VM 앱 일관성**: 복제 그룹의 일부인 이 옵션 VM을 사용하면 최신 공통 다중 VM 응용 프로그램 일관된 복구 지점으로 장애 조치됩니다. 다른 가상 머신은 최근의 애플리케이션 일치 복구 지점으로 장애 조치(failover)됩니다. 다중 VM 일관성이 활성화된 VM이 하나 이상 있는 복구 계획에만 해당합니다.
   - **사용자 지정**: 복구 계획에 사용할 수 없습니다. 이 옵션은 개별 VM의 장애 조치(failover)에만 해당됩니다.

5. **장애 조치(failover)를 시작하기 전에** 사이트 복구가 소스 VM을 종료하려는 경우 장애 복구를 시작하기 전에 컴퓨터를 종료합니다. 종료가 실패하더라도 장애 조치는 계속됩니다.  

    > [!NOTE]
    > Hyper-V VM을 장애 조치(failover)를 실행하지 않으면 장애 조치(failover)를 트리거하기 전에 아직 서비스로 전송되지 않은 온-프레미스 데이터를 동기화하고 복제하려고 합니다. 

6. **작업** 페이지에서 장애 조치 진행률을 따릅니다. 오류가 발생하더라도 복구 계획이 완료될 때까지 실행됩니다.
7. 장애 조치(failover)가 끝나면 VM에 로그인하여 유효성을 검사합니다. 
8. 장애 조치(failover)에 사용할 다른 복구 지점으로 전환하려면 **복구 지점 변경 을**사용합니다.
9. 준비가 되면 장애 조치(failover)를 커밋할 수 있습니다. **커밋** 작업은 서비스에서 사용할 수 있는 모든 복구 지점을 삭제합니다. **복구 지점 변경** 옵션을 더 이상 사용할 수 없습니다.

## <a name="run-a-planned-failover-hyper-v"></a>계획된 장애 조치 실행(하이퍼-V)

하이퍼 VM에 대해 계획된 장애 조치(failover)를 실행할 수 있습니다.

- 계획된 장애 조치(failover)는 데이터 손실 장애 조치 제로 옵션입니다.
- 계획된 장애 조치(failover)가 트리거되면 먼저 원본 가상 머신이 종료되고, 최신 데이터가 동기화된 다음, 장애 조치(failover)가 트리거됩니다.
- 계획된 장애 조치(failover) 옵션을 사용하여 **계획된 장애 조치(failover)를** 실행합니다. 일반 장애 조치와 유사한 방식으로 실행됩니다.
 
## <a name="track-failovers"></a>장애 조치 추적

장애 조치와 관련된 여러 작업이 있습니다.

![장애 조치](./media/site-recovery-failover/FailoverJob.png)

- **필수 구성 조건 검사**: 장애 조치(failover)에 필요한 모든 조건이 충족되었는지 확인합니다.
- **장애 조치**: Azure VM을 만들 수 있도록 데이터를 처리합니다. **최신** 복구 지점을 선택한 경우 서비스로 전송된 데이터에서 복구 지점이 만들어집니다.
- **시작**: 이전 단계에서 처리된 데이터를 사용하여 Azure VM을 만듭니다.

> [!WARNING]
> **진행 중인 장애 조치 취소:** 장애 중지가 시작되기 전에 VM에 대한 복제가 중지됩니다. 진행 중인 작업을 취소하면 장애 조치(failover)가 중지되지만 VM은 복제를 시작하지 않습니다. 복제를 다시 시작할 수 없습니다.


### <a name="extra-failover-time"></a>추가 장애 조치 시간

경우에 따라 VM 장애 조치(VM Failover)를 완료하는 데 보통 8~10분 정도 걸리는 중간 단계가 필요합니다. 다음은 이 추가 단계/시간의 영향을 받는 컴퓨터입니다.

* 9.8보다 오래된 모빌리티 서비스 버전을 실행하는 VMware 가상 시스템.
* 물리적 서버 및 Hyper-V VM은 물리적 서버로 보호됩니다.
* VMware Linux VM
* 이러한 드라이버가 부팅 드라이버로 존재하지 않는 VMware VM:
    * storvsc
    * vmbus
    * storflt
    * intelide
    * atapi
* DHCP를 사용하지 않는 VMware VM은 DHCP 또는 정적 IP 주소를 사용중인지 여부에 관계없이 사용할 수 있습니다.


## <a name="automate-actions-during-failover"></a>장애 조치 중 작업 자동화

장애 조치 중에 작업을 자동화할 수 있습니다. 이렇게 하려면 복구 계획에서 스크립트 또는 Azure 자동화 Runbook을 사용할 수 있습니다.

- 스크립트 추가를 포함하여 복구 계획을 만들고 사용자 지정하는 방법에 대해 [알아봅니다.](site-recovery-create-recovery-plans.md)
- 복구 계획에 Azure 자동화 실행책을 추가하는 방법에 대해 [알아봅니다.](site-recovery-runbook-automation.md)


## <a name="configure-settings-after-failover"></a>장애 조치 후 설정 구성

### <a name="retain-drive-letters-after-failover"></a>장애 조치 후 드라이브 문자 유지

사이트 복구는 드라이브 문자의 보존을 처리합니다. VM 복제 중에 디스크를 제외하는 경우 이 작동 방식의 [예를 검토합니다.](exclude-disks-replication.md#example-1-exclude-the-sql-server-tempdb-disk)

### <a name="prepare-in-azure-to-connect-after-failover"></a>장애 조치 후 연결할 Azure에서 준비

RDP 또는 SSH를 사용하여 장애 조치 후 생성된 Azure VM에 연결하려면 표에 요약된 요구 사항을 따릅니다.

**장애 조치** | **위치** | **작업**
--- | --- | ---
**Windows를 실행하는 Azure VM** | 장애 조치(failover) 후 Azure VM |  VM에 대한 [공용 IP 주소를 추가](https://aka.ms/addpublicip)합니다.<br/><br/> 장애 조치된 VM 및 해당 VM이 연결된 Azure 서브넷에 대한 네트워크 보안 그룹 규칙에서 RDP 포트로 들어오는 연결을 허용해야 합니다.<br/><br/> **부트 진단**을 확인하여 VM에 대한 스크린샷을 검토합니다.<br/><br/> 연결할 수 없는 경우 VM이 실행 중인지 확인하고 이러한 [문제 해결 팁을 검토합니다.](https://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx)
**Linux를 실행하는 Azure VM** | 장애 조치(failover) 후 Azure VM | 장애 조치(Failover)된 VM 그리고 해당 VM이 연결된 Azure 서브넷의 네트워크 보안 그룹 규칙이 SSH 포트로 들어오는 연결을 허용해야 합니다.<br/><br/> VM에 대한 [공용 IP 주소를 추가](https://aka.ms/addpublicip)합니다.<br/><br/> **부트 진단**에서 VM에 대한 스크린샷을 검토합니다.<br/><br/>

[여기](site-recovery-failover-to-azure-troubleshoot.md)에 설명된 단계에 따라 장애 조치(failover) 후 연결 문제를 해결합니다.

## <a name="set-up-ip-addressing"></a>IP 주소 지정 설정

- **내부 IP 주소**: 장애 조치 후 Azure VM의 내부 IP 주소를 설정하려면 몇 가지 옵션이 있습니다.
    - 동일한 IP 주소 유지: Azure VM에서 온-프레미스 컴퓨터에 할당된 것과 동일한 IP 주소를 사용할 수 있습니다.
    - 다른 IP 주소 사용: Azure VM에 대해 다른 IP 주소를 사용할 수 있습니다.
    - 내부 IP 주소 설정에 대해 [자세히 알아보세요.](concepts-on-premises-to-azure-networking.md#assign-an-internal-address)
- **외부 IP 주소**: 장애 조치(failover)에서 공용 IP 주소를 유지할 수 있습니다. 장애 조치 프로세스의 일부로 만든 Azure VM은 Azure 지역에서 사용할 수 있는 Azure 공용 IP 주소를 할당받아야 합니다. 수동으로 또는 복구 계획을 통해 프로세스를 자동화하여 공용 IP 주소를 할당할 수 있습니다. [자세히 알아봅니다](concepts-public-ip-address-with-site-recovery.md).


## <a name="next-steps"></a>다음 단계

실패한 후에는 Azure VM을 온-프레미스 사이트로 다시 복제하기 위해 다시 보호해야 합니다. 복제가 실행되고 나면 준비가 되면 온-프레미스에서 장애 조치(failtion)할 수 있습니다.

- 다시 보호 및 장애 조치(failback)에 대해 [자세히 알아보세요.](failover-failback-overview.md#reprotectionfailback)
- VMware 재보호 및 장애 조치(failback)를 [준비합니다.](vmware-azure-reprotect.md)
- [장애 조치](hyper-v-azure-failback.md) 하이퍼 VM.
- 실제 서버의 장애 조치 및 장애 조치(failback) 프로세스에 [대해 알아봅니다.](physical-to-azure-failover-failback.md)

