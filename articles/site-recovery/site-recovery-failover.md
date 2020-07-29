---
title: Azure Site Recovery를 사용 하 여 재해 복구 중 장애 조치 (failover) 실행
description: Azure Site Recovery로 Vm/물리적 서버를 Azure로 장애 조치 (failover) 하는 방법입니다.
ms.service: site-recovery
ms.topic: article
ms.date: 12/10/2019
ms.openlocfilehash: bebc4cd56f248d09579dcde2fc234f63dd65a09f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84309971"
---
# <a name="run-a-failover-from-on-premises-to-azure"></a>온-프레미스에서 Azure로 장애 조치(failover) 실행

이 문서에서는에서 온-프레미스 컴퓨터를 Azure로 장애 조치 (failover) 하는 방법을 설명 [Azure Site Recovery](site-recovery-overview.md)

## <a name="before-you-start"></a>시작하기 전에

- 재해 복구의 장애 조치 (failover) 프로세스에 [대해 알아봅니다](failover-failback-overview.md) .
- 여러 컴퓨터를 장애 조치 (failover) 하려는 경우 복구 계획에서 컴퓨터를 함께 수집 하는 방법에 [대해 알아봅니다](recovery-plan-overview.md) .
- 전체 장애 조치 (failover)를 수행 하기 전에 [재해 복구 훈련](site-recovery-test-failover-to-azure.md) 을 실행 하 여 모든 것이 예상 대로 작동 하는지 확인 합니다.

## <a name="prepare-to-connect-after-failover"></a>장애 조치 (failover) 후 연결 준비

장애 조치 (failover) 후 생성 된 Azure Vm에 연결할 수 있는지 확인 하기 위해 장애 조치 (failover) 전에 온-프레미스에서 수행 해야 하는 여러 가지 작업이 있습니다.


### <a name="prepare-on-premises-to-connect-after-failover"></a>장애 조치 (failover) 후 연결 하기 위해 온-프레미스 준비

장애 조치 (failover) 후 RDP/SSH를 사용 하 여 Azure Vm에 연결 하려는 경우 장애 조치 (failover) 전에 온-프레미스에서 수행 해야 하는 여러 가지 작업이 있습니다.

**장애 조치(failover) 후** | **위치** | **actions**
--- | --- | ---
**Windows를 실행하는 Azure VM** | 장애 조치(Failover) 전에 온-프레미스 컴퓨터에서 | 인터넷을 통해 Azure VM에 액세스하려면 RDP를 활성화하고, TCP 및 UDP 규칙이 **공용**에 추가되었는지 그리고 **Windows 방화벽** > **허용되는 앱**에서 모든 프로필에 대해 RDP가 허용되는지 확인합니다.<br/><br/> 사이트 간 연결을 통해 Azure VM에 액세스 하려면 컴퓨터에서 rdp를 사용 하도록 설정 하 고, **Windows Firewall**  ->  **도메인 및 개인** 네트워크의 Windows 방화벽 허용 되는**앱 및 기능**에서 rdp를 허용 해야 합니다.<br/><br/> <br/><br/> 정적 영구 경로 및 WinHTTP 프록시를 제거 합니다. 운영 체제의 SAN 정책이 **OnlineAll**로 설정되어 있는지 확인합니다. [자세히 알아보기](https://support.microsoft.com/kb/3031135).<br/><br/> 장애 조치를 트리거할 때 VM에 보류 중인 Windows 업데이트가 없는지 확인합니다. 장애 조치 시 Windows 업데이트가 시작될 수 있으며, 업데이트를 완료할 때까지 VM에 로그인할 수 없습니다.
**Linux를 실행하는 Azure VM** | 장애 조치(Failover) 전에 온-프레미스 컴퓨터에서 | VM의 보안 셸 서비스가 시스템 부팅 시 자동으로 시작되도록 설정되어 있는지 확인합니다.<br/><br/> 방화벽 규칙이 SSH 연결을 허용하는지 확인합니다.


## <a name="run-a-failover"></a>장애 조치(Failover) 실행

이 절차에서는 [복구 계획](site-recovery-create-recovery-plans.md)에 대한 장애 조치를 실행하는 방법을 설명합니다. 단일 VM에 대 한 장애 조치 (failover)를 실행 하려면 [VMWARE vm](vmware-azure-tutorial-failover-failback.md), [물리적 서버](physical-to-azure-failover-failback.md)또는 [hyper-v vm](hyper-v-azure-failover-failback-tutorial.md)에 대 한 지침을 따르세요.


다음과 같이 복구 계획 장애 조치 (failover)를 실행 합니다.

1. Site Recovery 자격 증명 모음에서 **복구 계획**  >  *recoveryplan_name*을 선택 합니다.
2. **장애 조치**를 클릭합니다.

    ![장애 조치](./media/site-recovery-failover/Failover.png)

3. 장애 조치 ( **failover**  >  **) 방향**에서 Azure에 복제 하는 경우 기본값을 그대로 둡니다.
4. **장애 조치 (Failover)** 에서 장애 조치 (failover) 할 **복구 지점을** 선택 합니다.

    - **최신**: 최신 지점을 사용 합니다. Site Recovery 서비스에 전송 된 모든 데이터를 처리 하 고 각 컴퓨터에 대 한 복구 지점을 만듭니다. 장애 조치 (failover) 후 생성 된 VM에는 장애 조치 (failover)가 트리거될 때 Site Recovery로 복제 된 모든 데이터가 있으므로이 옵션은 가장 낮은 RPO (복구 지점 목표)를 제공 합니다.
    원본 지역이 다운 되 면 더 이상 로그를 처리할 수 없습니다. 따라서 처리 된 최신 복구 지점으로 장애 조치 (failover) 해야 합니다. 자세한 내용은 다음 단계를 참조 하세요.
   - **가장 최근에 처리**됨: Site Recovery에서 이미 처리 한 최신 복구 지점으로 vm을 장애 조치 (failover) 하려면이 옵션을 사용 합니다. VM **최신 복구 지점**에서 가장 최근에 처리 된 복구 지점을 볼 수 있습니다. 이 옵션은 처리 되지 않은 데이터를 처리 하는 데 시간을 소비 하지 않으므로 낮은 RTO를 제공 합니다.
   - **최신 앱 일치**: Site Recovery에 의해 처리 된 최신 응용 프로그램 일치 복구 지점으로 vm을 장애 조치 (failover) 하려면이 옵션을 사용 합니다.
   - **최신 다중 vm 처리**:이 옵션을 사용 하면 복제 그룹의 일부인 vm이 최신의 일반적인 다중 vm 일치 복구 지점으로 장애 조치 (failover) 됩니다. 다른 가상 머신은 가장 최근에 처리 된 복구 지점으로 장애 조치 (failover) 됩니다. 이 옵션은 다중 VM 일관성을 사용 하도록 설정 된 하나 이상의 VM이 있는 복구 계획에만 사용 됩니다.
   - **최신 다중 vm 앱 일치**:이 옵션을 사용 하면 복제 그룹의 일부인 vm이 가장 일반적인 다중 vm 응용 프로그램 일치 복구 지점으로 장애 조치 (failover) 됩니다. 다른 가상 머신은 최근의 애플리케이션 일치 복구 지점으로 장애 조치(failover)됩니다. 다중 VM 일관성을 사용 하도록 설정 된 하나 이상의 VM이 있는 복구 계획에만 해당 합니다.
   - **사용자 지정**: 복구 계획에 사용할 수 없습니다. 이 옵션은 개별 Vm의 장애 조치 (failover)에만 사용할 수 있습니다.

5. 장애 조치 (failover)를 시작 하기 전에 원본 Vm Site Recovery 종료 하려면 **장애 조치 (failover)를 시작 하기 전에 시스템 종료** 를 선택 합니다. 종료가 실패하더라도 장애 조치는 계속됩니다.  

    > [!NOTE]
    > Hyper-v Vm을 장애 조치 (failover) 하는 경우 종료는 장애 조치 (failover)를 트리거하기 전에 아직 서비스로 보내지 않은 온-프레미스 데이터를 동기화 하 고 복제 하려고 시도 합니다. 

6. **작업** 페이지에서 장애 조치 (failover) 진행률을 따릅니다. 오류가 발생 하더라도 복구 계획은 완료 될 때까지 실행 됩니다.
7. 장애 조치 (failover) 후 VM에 로그인 하 여 유효성을 검사 합니다. 
8. 장애 조치 (failover)에 사용할 다른 복구 지점으로 전환 하려면 **복구 지점 변경**을 사용 합니다.
9. 준비가 되 면 장애 조치 (failover)를 커밋할 수 있습니다. **커밋** 작업은 서비스에서 사용할 수 있는 모든 복구 지점이 삭제 됩니다. **복구 지점 변경** 옵션을 더 이상 사용할 수 없습니다.

## <a name="run-a-planned-failover-hyper-v"></a>계획 된 장애 조치 (failover) 실행 (Hyper-v)

Hyper-v Vm에 대해 계획 된 장애 조치 (failover)를 실행할 수 있습니다.

- 계획 된 장애 조치 (failover)는 데이터 손실 장애 조치 (failover) 옵션입니다.
- 계획된 장애 조치(failover)가 트리거되면 먼저 원본 가상 머신이 종료되고, 최신 데이터가 동기화된 다음, 장애 조치(failover)가 트리거됩니다.
- 계획 된 장애 조치 (failover) **옵션을** 사용 하 여 계획 된 장애 조치를 실행 합니다. 일반적인 장애 조치 (failover)와 비슷한 방식으로 실행 됩니다.
 
## <a name="track-failovers"></a>장애 조치 추적

장애 조치 (failover)와 관련 된 여러 작업이 있습니다.

![장애 조치](./media/site-recovery-failover/FailoverJob.png)

- **필수 조건 확인**: 장애 조치 (failover)에 필요한 모든 조건이 충족 되는지 확인 합니다.
- **장애 조치 (Failover)**: 데이터를 처리 하 여 Azure VM을 만들 수 있도록 합니다. **최신** 복구 지점을 선택한 경우 서비스로 전송 된 데이터에서 복구 지점이 생성 됩니다.
- **시작**: 이전 단계에서 처리 한 데이터를 사용 하 여 Azure VM을 만듭니다.

> [!WARNING]
> 장애 조치 (failover)를 **취소 하지 않음**: 장애 조치 (failover)를 시작 하기 전에 VM에 대 한 복제를 중지 합니다. 진행 중인 작업을 취소 하면 장애 조치 (failover)가 중지 되지만 VM은 복제를 시작 하지 않습니다. 복제를 다시 시작할 수 없습니다.


### <a name="extra-failover-time"></a>장애 조치 (failover) 시간 추가

경우에 따라 VM 장애 조치 (failover)에는 일반적으로 완료 하는 데 8 ~ 10 분 정도 소요 되는 중간 단계가 필요 합니다. 이러한 추가 단계/시간의 영향을 받는 컴퓨터는 다음과 같습니다.

* 9.8 이전의 모바일 서비스 버전을 실행 하는 VMware 가상 머신
* 물리적 서버로 보호 되는 Hyper-v Vm 및 물리적 서버
* VMware Linux VM
* 이러한 드라이버가 부팅 드라이버로 제공 되지 않는 VMware Vm:
    * storvsc
    * vmbus
    * storflt
    * intelide
    * atapi
* Dhcp를 사용 하 고 있는지 아니면 고정 IP 주소를 사용 하는지에 관계 없이 DHCP를 사용 하지 않는 VMware Vm.


## <a name="automate-actions-during-failover"></a>장애 조치 (failover) 중 작업 자동화

장애 조치 (failover) 중에 작업을 자동화할 수 있습니다. 이렇게 하려면 복구 계획에서 스크립트나 Azure automation runbook을 사용할 수 있습니다.

- 스크립트 추가를 포함 하 여 복구 계획을 만들고 사용자 지정 하는 방법에 [대해 알아봅니다](site-recovery-create-recovery-plans.md) .
- 복구 계획에 Azure Automation runbook을 추가 하는 방법에 [대해 알아봅니다](site-recovery-runbook-automation.md) .


## <a name="configure-settings-after-failover"></a>장애 조치 (failover) 후 설정 구성

### <a name="retain-drive-letters-after-failover"></a>장애 조치 (failover) 후 드라이브 문자 유지

Site Recovery는 드라이브 문자 보존을 처리 합니다. VM을 복제 하는 동안 디스크를 제외 하는 경우 작동 방법의 [예제를 검토](exclude-disks-replication.md#example-1-exclude-the-sql-server-tempdb-disk) 합니다.

### <a name="prepare-in-azure-to-connect-after-failover"></a>장애 조치 (failover) 후 연결 하기 위해 Azure에서 준비

RDP 또는 SSH를 사용 하 여 장애 조치 (failover) 후 생성 된 Azure Vm에 연결 하려면 표에 요약 된 요구 사항을 따르세요.

**장애 조치(Failover)** | **위치** | **actions**
--- | --- | ---
**Windows를 실행하는 Azure VM** | 장애 조치(failover) 후 Azure VM |  VM에 대한 [공용 IP 주소를 추가](https://aka.ms/addpublicip)합니다.<br/><br/> 장애 조치된 VM 및 해당 VM이 연결된 Azure 서브넷에 대한 네트워크 보안 그룹 규칙에서 RDP 포트로 들어오는 연결을 허용해야 합니다.<br/><br/> **부트 진단**을 확인하여 VM에 대한 스크린샷을 검토합니다.<br/><br/> 연결할 수 없는 경우 VM이 실행 중인지 확인 하 고 이러한 [문제 해결 팁](https://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx)을 검토 합니다.
**Linux를 실행하는 Azure VM** | 장애 조치(failover) 후 Azure VM | 장애 조치(Failover)된 VM 그리고 해당 VM이 연결된 Azure 서브넷의 네트워크 보안 그룹 규칙이 SSH 포트로 들어오는 연결을 허용해야 합니다.<br/><br/> VM에 대한 [공용 IP 주소를 추가](https://aka.ms/addpublicip)합니다.<br/><br/> **부트 진단**에서 VM에 대한 스크린샷을 검토합니다.<br/><br/>

[여기](site-recovery-failover-to-azure-troubleshoot.md)에 설명된 단계에 따라 장애 조치(failover) 후 연결 문제를 해결합니다.

## <a name="set-up-ip-addressing"></a>IP 주소 지정 설정

- **내부 ip 주소**: 장애 조치 (failover) 후 Azure VM의 내부 ip 주소를 설정 하려면 다음 두 가지 옵션을 사용할 수 있습니다.
    - 동일한 IP 주소 유지: Azure VM에서 온-프레미스 컴퓨터에 할당 된 것과 동일한 IP 주소를 사용할 수 있습니다.
    - 다른 IP 주소 사용: Azure VM에 다른 IP 주소를 사용할 수 있습니다.
    - 내부 IP 주소를 설정 하는 방법에 [대해 자세히 알아보세요](concepts-on-premises-to-azure-networking.md#assign-an-internal-address) .
- **외부 ip 주소**: 장애 조치 (failover) 시 공용 ip 주소를 유지할 수 있습니다. 장애 조치 (failover) 프로세스의 일부로 만든 azure Vm에는 Azure 지역에서 사용할 수 있는 Azure 공용 IP 주소가 할당 되어야 합니다. 수동으로 또는 복구 계획을 사용 하 여 프로세스를 자동화 하 여 공용 IP 주소를 할당할 수 있습니다. [자세히 알아보기](concepts-public-ip-address-with-site-recovery.md).


## <a name="next-steps"></a>다음 단계

장애 조치 (failover) 한 후 다시 보호 하 여 Azure Vm을 온-프레미스 사이트로 다시 복제 해야 합니다. 복제를 실행 하 고 나면 준비가 되 면 온-프레미스로 장애 복구 (failback) 할 수 있습니다.

- 다시 보호 및 장애 복구에 [대해 자세히 알아보세요](failover-failback-overview.md#reprotectionfailback) .
- VMware 다시 보호 및 장애 복구 (failback)를 [준비](vmware-azure-reprotect.md) 합니다.
- [장애 복구](hyper-v-azure-failback.md) Hyper-v Vm.
- 물리적 서버의 장애 조치 (failover) 및 장애 복구 (failback) 프로세스 [에 대해 알아봅니다](physical-to-azure-failover-failback.md) .

