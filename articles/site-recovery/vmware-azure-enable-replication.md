---
title: Azure Site Recovery를 사용 하 여 재해 복구를 위해 VMware Vm 사용
description: 이 문서에서는 Azure Site Recovery 서비스를 사용 하 여 재해 복구에 VMware VM 복제를 사용 하도록 설정 하는 방법을 설명 합니다.
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.date: 04/01/2020
ms.topic: conceptual
ms.author: ramamill
ms.openlocfilehash: 74870d10348421bf726b9bdc58504a74cf4105a9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "86129934"
---
# <a name="enable-replication-to-azure-for-vmware-vms"></a>VMware VM에 대해 Azure로의 복제를 사용하도록 설정

이 문서에서는 온-프레미스 VMware VM (가상 머신)을 Azure로 복제 하도록 설정 하는 방법을 설명 합니다.

## <a name="prerequisites"></a>사전 요구 사항

이 문서에서는 시스템이 다음 조건을 충족 한다고 가정 합니다.

- [온-프레미스 원본 환경을 설정](vmware-azure-set-up-source.md)합니다.
- [Azure에서 대상 환경을 설정](vmware-azure-set-up-target.md)합니다.
- 시작 하기 전에 [요구 사항 및 필수 구성 요소를 확인](vmware-physical-azure-support-matrix.md) 합니다. 유의 해야 할 중요 한 사항은 다음과 같습니다.
  - 복제 된 컴퓨터에 대해 [지원 되는 운영 체제](vmware-physical-azure-support-matrix.md#replicated-machines) 입니다.
  - [저장소/디스크](vmware-physical-azure-support-matrix.md#storage) 지원.
  - 온-프레미스 컴퓨터에서 준수 해야 하는 [Azure 요구 사항](vmware-physical-azure-support-matrix.md#azure-vm-requirements)

### <a name="resolve-common-issues"></a>일반적인 문제 해결

- 각 디스크는 4 TB 보다 작아야 합니다.
- 운영 체제 디스크는 동적 디스크가 아닌 기본 디스크 여야 합니다.
- 2 세대 UEFI 지원 가상 컴퓨터의 경우 운영 체제 제품군이 Windows이 고 부팅 디스크는 300 보다 작아야 합니다.

## <a name="before-you-start"></a>시작하기 전에

VMware 가상 컴퓨터를 복제 하는 경우 다음 정보를 염두에 두어야 합니다.

- Azure 사용자 계정에 특정 [사용 권한](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines)이 있어야 Azure에 새 가상 머신을 복제할 수 있습니다.
- VMware VM은 15분마다 검색됩니다. Vm이 검색 후 Azure Portal에 표시 되는 데 15 분 이상 걸릴 수 있습니다. 새 vCenter 서버 또는 vSphere 호스트를 추가 하는 경우 검색은 15 분 이상 걸릴 수 있습니다.
- 포털에서 가상 머신의 환경 변경 내용이 업데이트 되는 데 15 분 이상 걸릴 수 있습니다. 예를 들어 VMware 도구를 설치 합니다.
- VMware Vm에 대해 마지막으로 검색 한 시간을 확인할 수 있습니다. vCenter 서버/v s p 호스트에 대 한 **구성 서버** 페이지의 **마지막 연락처** 필드를 참조 하세요.
- 예약 된 검색을 기다리지 않고 복제를 위해 가상 컴퓨터를 추가 하려면 구성 서버를 강조 표시 하 고 (클릭 하지 않음) **새로 고침**을 선택 합니다.
- 복제를 사용 하도록 설정 하는 경우 가상 컴퓨터가 준비 되 면 프로세스 서버가 VM에 Azure Site Recovery 모바일 서비스를 자동으로 설치 합니다.

## <a name="enable-replication"></a>복제 사용

이 섹션의 단계를 수행 하기 전에 다음 정보를 검토 하십시오.

- 이제 Azure Site Recovery은 모든 새 복제에 대해 관리 디스크에 직접 복제 됩니다. 프로세스 서버는 대상 지역의 캐시 저장소 계정에 복제 로그를 씁니다. 이러한 로그는의 명명 규칙을 사용 하는 복제본 관리 디스크에서 복구 위치를 만드는 데 사용 됩니다 `asrseeddisk` .
- Managed disks로의 복제에 대 한 PowerShell 지원은 [Az services module version 2.0.0](https://www.powershellgallery.com/packages/Az.RecoveryServices/2.0.0-preview) 에서 사용할 수 있습니다.
- 장애 조치 (failover) 시 선택 하는 복구 지점은 대상 관리 디스크를 만드는 데 사용 됩니다.
- 이전에 대상 저장소 계정에 복제 하도록 구성 된 Vm은 영향을 받지 않습니다.
- 새 가상 컴퓨터에 대 한 저장소 계정으로 복제는 REST (Representational State Transfer) API 및 PowerShell을 통해서만 사용할 수 있습니다. 저장소 계정에 대 한 복제에 Azure REST API 버전 2016-08-10 또는 2018-01-10을 사용 합니다.

복제를 사용 하도록 설정 하려면 다음 단계를 수행 합니다.

1. **2 단계: 응용 프로그램**  >  **원본**복제로 이동 합니다. 처음으로 복제를 사용 하도록 설정한 후에는 자격 증명 모음에서 **+ 복제** 를 선택 하 여 추가 가상 컴퓨터에 대해 복제를 사용 하도록 설정 합니다.
1. **원본** 페이지 > **원본**에서 구성 서버를 선택합니다.
1. **컴퓨터 유형에**대해 **Virtual Machines** 또는 **물리적 컴퓨터**를 선택 합니다.
1. **vCenter/vSphere 하이퍼바이저**에서 vSphere 호스트를 관리하는 vCenter Server를 선택하거나 해당 호스트를 선택합니다. 물리적 컴퓨터를 복제 하는 경우에는이 설정이 적합 하지 않습니다.
1. 프로세스 서버를 선택합니다. 추가 프로세스 서버를 만들지 않은 경우 구성 서버의 기본 제공 프로세스 서버가 드롭다운 메뉴에 제공 됩니다. 각 프로세스 서버의 상태는 권장 제한 및 기타 매개 변수로 표시 됩니다. 정상 프로세스 서버를 선택합니다. [중요 한](vmware-physical-azure-monitor-process-server.md#process-server-alerts) 프로세스 서버를 선택할 수 없습니다. 오류를 [해결](vmware-physical-azure-troubleshoot-process-server.md)**하거나** [스케일 아웃 프로세스 서버](vmware-azure-set-up-process-server-scale.md)를 설정할 수 있습니다.

   :::image type="content" source="./media/vmware-azure-enable-replication/ps-selection.png" alt-text="복제 원본 창 사용":::

   > [!NOTE]
   > [버전 9.24](site-recovery-whats-new.md)부터 프로세스 서버의 상태 경고를 향상 시키기 위해 추가 경고가 도입 되었습니다. 모든 경고가 생성 될 수 있도록 Site Recovery 구성 요소를 버전 9.24 이상으로 업그레이드 합니다.

1. **대상**에 대해 장애 조치 (failover) 된 가상 머신을 만들려는 구독 및 리소스 그룹을 선택 합니다. 장애 조치 (failover) 된 Vm에 대해 Azure에서 사용 하려는 배포 모델을 선택 합니다.
1. 장애 조치 (failover) 후 Azure Vm이 연결 될 Azure 네트워크 및 서브넷을 선택 합니다. 네트워크는 Site Recovery 서비스 자격 증명 모음과 동일한 지역에 있어야 합니다.

   **선택한 컴퓨터에 대해 지금 구성** 을 선택 하 여 보호를 위해 선택한 모든 가상 컴퓨터에 네트워크 설정을 적용 합니다. **나중에 구성** 을 선택 하 여 가상 머신 당 Azure 네트워크를 선택 합니다. 네트워크가 없는 경우 만들어야 합니다. Azure Resource Manager를 사용 하 여 네트워크를 만들려면 **새로 만들기**를 선택 합니다. 해당 하는 경우 서브넷을 선택 하 고 **확인**을 선택 합니다.

   :::image type="content" source="./media/vmware-azure-enable-replication/enable-rep3.png" alt-text="복제 원본 창 사용":::

1. **Virtual machines**  >  에서**가상**컴퓨터를 선택 하 고 복제 하려는 각 가상 컴퓨터를 선택 합니다. 복제를 사용 하도록 설정할 수 있는 가상 컴퓨터만 선택할 수 있습니다. 그런 다음, **확인**을 선택합니다. 특정 가상 컴퓨터를 보거나 선택할 수 없는 경우 문제 해결을 위해 [원본 컴퓨터가 Azure Portal에 나열 되지 않음](vmware-azure-troubleshoot-replication.md#step-3-troubleshoot-source-machines-that-arent-available-for-replication) 을 참조 하세요.

   :::image type="content" source="./media/vmware-azure-enable-replication/enable-replication5.png" alt-text="복제 원본 창 사용":::

1. 속성 **Properties**  >  **속성 구성**에서 프로세스 서버가 VM에 Site Recovery 모바일 서비스를 자동으로 설치 하는 데 사용 하는 계정을 선택 합니다. 또한 데이터 변동 패턴에 따라 복제에 사용할 대상 관리 디스크의 형식을 선택 합니다.
1. 기본적으로 원본 VM의 모든 디스크가 복제 됩니다. 복제에서 디스크를 제외 하려면 복제 하지 않을 디스크에 대 한 **포함** 확인란의 선택을 취소 합니다. 그런 다음, **확인**을 선택합니다. 나중에 추가 속성을 설정할 수 있습니다. 디스크 제외에 대해 [자세히 알아보세요](vmware-azure-exclude-disk.md).

   :::image type="content" source="./media/vmware-azure-enable-replication/enable-replication6.png" alt-text="복제 원본 창 사용":::

1. 복제 **설정**  >  **복제 설정 구성**에서 올바른 복제 정책이 선택 되어 있는지 확인 합니다. **설정**  >  **복제**정책  >  _정책 이름_  >  **설정 편집**에서 복제 정책 설정을 수정할 수 있습니다. 정책에 적용 되는 변경 내용은 복제 및 새 가상 컴퓨터에도 적용 됩니다.
1. 가상 컴퓨터를 복제 그룹으로 수집 하려는 경우 **다중 VM 일관성**을 사용 하도록 설정 합니다. 그룹의 이름을 지정 하 고 **확인**을 선택 합니다.

   > [!NOTE]
   > - 복제 그룹의 가상 컴퓨터는 함께 복제 되 고 장애 조치 (failover) 시 공유 크래시 일관성 및 앱 일치 복구 지점이 있습니다.
   > - 워크로드를 미러링하도록 VM 및 물리적 서버를 함께 수집합니다. 다중 VM 일관성을 사용하면 워크로드 성능에 영향을 줄 수 있습니다. 가상 컴퓨터에서 동일한 작업을 실행 하 고 일관성이 필요한 경우에만이 작업을 수행 합니다.

   :::image type="content" source="./media/vmware-azure-enable-replication/enable-replication7.png" alt-text="복제 원본 창 사용":::

1. **복제 사용**을 선택합니다. **Enable Protection** **설정**  >  **작업**  >  **Site Recovery 작업**에서 보호 사용 작업의 진행률을 추적할 수 있습니다. **보호 완료** 작업이 실행 된 후 가상 머신은 장애 조치 (failover)를 수행할 준비가 된 것입니다.

## <a name="view-and-manage-vm-properties"></a>VM 속성 보기 및 관리

그런 다음 원본 가상 머신의 속성을 확인 합니다. Azure VM 이름이 [Azure 가상 머신 요구 사항](vmware-physical-azure-support-matrix.md#replicated-machines)을 준수해야 합니다.

1. **설정**  >  **복제 된 항목**으로 이동한 다음 가상 컴퓨터를 선택 합니다. **Essentials** 페이지에는 VM의 설정 및 상태에 대 한 정보가 표시 됩니다.
1. **속성**에서 해당 VM에 대한 복제 및 장애 조치(failover) 정보를 볼 수 있습니다.
1. **Compute 및 네트워크**  >  **계산 속성**에서 여러 VM 속성을 변경할 수 있습니다.

   :::image type="content" source="./media/vmware-azure-enable-replication/vmproperties.png" alt-text="복제 원본 창 사용":::

   - **AZURE VM 이름**: 필요한 경우 azure 요구 사항에 맞게 이름을 수정 합니다.
   - **대상 vm 크기 또는 vm 유형**: 기본 vm 크기는 대상 Azure 지역에서 디스크 수, NIC 수, CPU 코어 수, 메모리 및 사용 가능한 VM 역할 크기를 포함 하는 매개 변수를 기반으로 선택 됩니다. Azure Site Recovery는 모든 조건을 충족 하는 사용 가능한 첫 번째 VM 크기를 선택 합니다. 장애 조치 (failover) 전에 언제 든 지 요구 사항에 따라 다른 VM 크기를 선택할 수 있습니다. VM 디스크 크기는 원본 디스크 크기에 따라 달라 집니다. 또한 장애 조치 (failover) 후에만 변경할 수 있습니다. [Windows에서 VM 디스크의 확장성 및 성능 목표](../virtual-machines/windows/disk-scalability-targets.md)에 대 한 디스크 크기 및 IOPS 속도에 대해 자세히 알아보세요.
   - **리소스 그룹**: 가상 머신이 사후 장애 조치 (failover)의 일부가 되는 [리소스 그룹](../azure-resource-manager/management/overview.md#resource-groups)을 선택할 수 있습니다. 장애 조치 (failover) 전에 언제 든 지이 설정을 변경할 수 있습니다. 장애 조치 (failover) 후 가상 컴퓨터를 다른 리소스 그룹으로 마이그레이션하면 해당 가상 컴퓨터에 대 한 보호 설정이 끊어집니다.
   - **가용성 집합**: 가상 머신이 사후 장애 조치 (failover)의 일부로 사용 되어야 하는 경우 [가용성 집합](../virtual-machines/windows/tutorial-availability-sets.md) 을 선택할 수 있습니다. 가용성 집합을 선택할 때는 다음 정보를 염두에 두어야 합니다.
     - 지정 된 리소스 그룹에 속하는 가용성 집합만 나열 됩니다.
     - 서로 다른 가상 네트워크에 있는 Vm은 동일한 가용성 집합에 속할 수 없습니다.
     - 동일한 크기의 가상 머신만 가용성 집합의 일부가 될 수 있습니다.

1. Azure VM에 할당 된 대상 네트워크, 서브넷 및 IP 주소에 대 한 정보를 추가할 수도 있습니다.
1. **디스크**에서 복제될 VM의 운영 체제 및 데이터 디스크를 볼 수 있습니다.

### <a name="configure-networks-and-ip-addresses"></a>네트워크 및 IP 주소 구성

대상 IP 주소를 설정할 수 있습니다.

- 주소를 입력 하지 않으면 장애 조치 (failover) 된 VM은 DHCP를 사용 합니다.
- 장애 조치(failover) 시 사용할 수 없는 주소를 설정하면 장애 조치(failover)가 작동하지 않습니다.
- 테스트 장애 조치 (failover) 네트워크에서 해당 주소를 사용할 수 있는 경우 테스트 장애 조치 (failover)에 동일한 대상 IP 주소를 사용할 수 있습니다.

네트워크 어댑터 수는 다음과 같이 대상 가상 머신에 대해 지정 하는 크기에 따라 결정 됩니다.

- 원본 가상 컴퓨터의 네트워크 어댑터 수가 대상 VM의 크기에 허용 되는 어댑터 수보다 작거나 같은 경우 대상의 어댑터 수는 원본과 동일 합니다.
- 원본 가상 컴퓨터의 어댑터 수가 대상 VM의 크기에 허용 된 수를 초과 하면 대상 크기의 최대값이 사용 됩니다. 예를 들어 원본 가상 컴퓨터에 두 개의 네트워크 어댑터가 있고 대상 VM의 크기가 4를 지 원하는 경우 대상 가상 컴퓨터에는 두 개의 어댑터가 있습니다. 원본 VM에 두 개의 어댑터가 있지만 대상 크기가 하나만 지 원하는 경우 대상 VM에는 하나의 어댑터만 있습니다.
- 가상 머신에 네트워크 어댑터가 여러 개 있으면 모두 동일한 네트워크에 연결됩니다. 또한 목록에 표시 되는 첫 번째 어댑터가 Azure 가상 머신에서 기본 네트워크 어댑터가 됩니다.

### <a name="azure-hybrid-benefit"></a>Azure 하이브리드 혜택

Microsoft 소프트웨어 보증 고객은 Azure 하이브리드 혜택를 사용 하 여 Azure로 마이그레이션되는 Windows Server 컴퓨터에 대 한 라이선스 비용을 절감할 수 있습니다. 이 혜택은 Azure 재해 복구에도 적용 됩니다. 자격이 있는 경우 장애 조치 (failover)가 있는 경우 Site Recovery 만드는 가상 머신에 혜택을 할당할 수 있습니다.

1. 복제 된 가상 컴퓨터의 **컴퓨터 및 네트워크 속성** 으로 이동 합니다.
1. Azure 하이브리드 혜택 수 있도록 하는 Windows Server 라이선스가 있는지 묻는 메시지가 표시 되 면 대답 합니다.
1. 장애 조치 (failover) 시 생성 되는 VM에 혜택을 적용 하는 데 사용할 수 있는 적격 한 Windows Server 라이선스 (소프트웨어 보증 포함)가 있는지 확인 합니다.
1. 복제 된 가상 컴퓨터에 대 한 설정을 저장 합니다.

Azure 하이브리드 혜택에 [대해 자세히 알아보세요](https://azure.microsoft.com/pricing/hybrid-benefit/) .

## <a name="next-steps"></a>다음 단계

가상 컴퓨터가 보호 된 상태에 도달한 후 [장애 조치 (failover)](site-recovery-failover.md) 를 시도 하 여 응용 프로그램이 Azure에 표시 되는지 확인 합니다.

- 복제를 사용 하지 않도록 설정 하는 등록 및 보호 설정을 정리 하는 방법에 [대해 자세히 알아보세요](site-recovery-manage-registration-and-protection.md) .
- PowerShell을 사용 하 여 가상 컴퓨터에 대 한 복제를 자동화 하는 방법에 대해 [자세히 알아보세요](vmware-azure-disaster-recovery-powershell.md) .
