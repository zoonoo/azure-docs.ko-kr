---
title: Azure 사이트 복구를 사용하여 재해 복구를 위해 VMware VM을 사용하도록 설정합니다.
description: 이 문서에서는 Azure 사이트 복구 서비스를 사용하여 재해 복구를 위해 VMware VM 복제를 사용하도록 설정하는 방법에 대해 설명합니다.
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.date: 04/01/2020
ms.topic: conceptual
ms.author: ramamill
ms.openlocfilehash: 6547bcf2061213cd01550367171d432900693ea5
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80584138"
---
# <a name="enable-replication-to-azure-for-vmware-vms"></a>VMware VM에 대해 Azure로의 복제를 사용하도록 설정

이 문서에서는 VM웨어 가상 시스템(VM)을 Azure에 복제하는 방법을 설명합니다.

## <a name="prerequisites"></a>사전 요구 사항

이 문서에서는 시스템이 다음 기준을 충족한다고 가정합니다.

- [온-프레미스 소스 환경을 설정합니다.](vmware-azure-set-up-source.md)
- [Azure 에서 대상 환경을 설정합니다.](vmware-azure-set-up-target.md)
- 시작하기 전에 [요구 사항 및 필수 구성 조건을 확인합니다.](vmware-physical-azure-support-matrix.md) 주의해야 할 중요한 사항은 다음과 같습니다.
  - 복제된 컴퓨터에 대한 [지원되는 운영 체제입니다.](vmware-physical-azure-support-matrix.md#replicated-machines)
  - [스토리지/디스크](vmware-physical-azure-support-matrix.md#storage) 지원.
  - 온-프레미스 컴퓨터가 준수해야 하는 [Azure 요구 사항입니다.](vmware-physical-azure-support-matrix.md#azure-vm-requirements)

### <a name="resolve-common-issues"></a>일반적인 문제 해결

- 각 디스크는 4TB보다 작아야 합니다.
- 운영 체제 디스크는 동적 디스크가 아닌 기본 디스크여야 합니다.
- 2세대 UEFI 지원 가상 시스템의 경우 운영 체제 패밀리는 Windows여야 하며 부팅 디스크는 300GB보다 작아야 합니다.

## <a name="before-you-start"></a>시작하기 전에

VMware 가상 컴퓨터를 복제할 때 다음 정보를 염두에 두십시오.

- Azure 사용자 계정에 특정 [사용 권한](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines)이 있어야 Azure에 새 가상 머신을 복제할 수 있습니다.
- VMware VM은 15분마다 검색됩니다. VM이 검색 후 Azure 포털에 표시되는 데 15분 이상 걸릴 수 있습니다. 새 vCenter 서버 또는 vSphere 호스트를 추가하면 검색에 15분 이상이 걸릴 수 있습니다.
- 포털에서 가상 컴퓨터의 환경 변경 내용을 업데이트하는 데 15분 이상 걸릴 수 있습니다. 예를 들어 VMware 도구 설치를 예로 들 수 있습니다.
- VMware VM에 대한 마지막으로 검색된 시간: vCenter 서버/vSphere 호스트에 대한 **구성 서버** 페이지의 **마지막 연락처** 필드 참조를 확인할 수 있습니다.
- 예약된 검색을 기다리지 않고 복제를 위해 가상 컴퓨터를 추가하려면 구성 서버를 강조 표시하고(클릭하지 는 않지만) **새로 고침을**선택합니다.
- 복제를 사용하도록 설정하면 가상 시스템이 준비되면 프로세스 서버가 VM에 Azure 사이트 복구 이동성 서비스를 자동으로 설치합니다.

## <a name="enable-replication"></a>복제 사용

이 섹션의 단계를 수행하기 전에 다음 정보를 검토하십시오.

- 이제 Azure 사이트 복구는 모든 새 복제에 대해 관리되는 디스크에 직접 복제합니다. 프로세스 서버는 대상 지역의 캐시 저장소 계정에 복제 로그를 씁니다. 이러한 로그는 `asrseeddisk`의 명명 규칙이 있는 복제본 관리 디스크에서 복구 지점을 만드는 데 사용됩니다.
- 관리 디스크에 대한 복제에 대한 PowerShell 지원은 [Az.RecoveryServices 모듈 버전 2.0.0부터](https://www.powershellgallery.com/packages/Az.RecoveryServices/2.0.0-preview) 사용할 수 있습니다.
- 장애 조치(failover) 시점에 선택한 복구 지점이 대상 관리 디스크를 만드는 데 사용됩니다.
- 대상 저장소 계정에 복제하도록 이전에 구성된 VM은 영향을 받지 않습니다.
- 새 가상 시스템에 대한 저장소 계정에 대한 복제는 표현 상태 전송(REST) API 및 PowerShell을 통해서만 사용할 수 있습니다. Azure REST API 버전 2016-08-10 또는 2018-01-10을 사용하여 저장소 계정에 복제합니다.

복제를 사용하려면 다음 단계를 따르십시오.

1. **2단계로 이동: 응용 프로그램** > **소스**복제 . 처음으로 복제를 사용하도록 설정한 후 볼트에서 **+복제를** 선택하여 추가 가상 시스템에 대한 복제를 사용하도록 설정합니다.
1. **원본** 페이지 > **원본**에서 구성 서버를 선택합니다.
1. **컴퓨터 유형의**경우 **가상 컴퓨터** 또는 물리적 **컴퓨터를**선택합니다.
1. **vCenter/vSphere 하이퍼바이저**에서 vSphere 호스트를 관리하는 vCenter Server를 선택하거나 해당 호스트를 선택합니다. 실제 컴퓨터를 복제하는 경우 이 설정은 관련이 없습니다.
1. 프로세스 서버를 선택합니다. 추가 프로세스 서버가 생성되지 않으면 구성 서버의 붙만들어진 프로세스 서버를 드롭다운 메뉴에서 사용할 수 있습니다. 각 프로세스 서버의 상태 상태는 권장 제한 및 기타 매개 변수에 따라 표시됩니다. 정상 프로세스 서버를 선택합니다. [중요한](vmware-physical-azure-monitor-process-server.md#process-server-alerts) 프로세스 서버를 선택할 수 없습니다. 오류를 [해결](vmware-physical-azure-troubleshoot-process-server.md)**하거나 **[스케일 아웃 프로세스 서버](vmware-azure-set-up-process-server-scale.md)를 설정할 수 있습니다.

   :::image type="content" source="./media/vmware-azure-enable-replication/ps-selection.png" alt-text="복제 원본 창 사용":::

   > [!NOTE]
   > 버전 [9.24부터](site-recovery-whats-new.md)프로세스 서버의 상태 경고를 향상시키기 위해 추가 경고가 도입됩니다. 모든 경고가 생성될 수 있도록 사이트 복구 구성 요소를 버전 9.24 이상으로 업그레이드합니다.

1. **Target의**경우 가상 컴퓨터를 통해 실패한 을 만들 구독 및 리소스 그룹을 선택합니다. 실패한 VM에 대해 Azure에서 사용할 배포 모델을 선택합니다.
1. 장애 조치 후 Azure VM이 연결할 Azure 네트워크 및 서브넷을 선택합니다. 네트워크는 사이트 복구 서비스 자격 증명 모음과 동일한 지역에 있어야 합니다.

   보호를 위해 선택한 모든 가상 컴퓨터에 네트워크 설정을 적용하려면 **선택한 컴퓨터에 대해 지금 구성을** 선택합니다. **나중에 구성을** 선택하여 가상 시스템당 Azure 네트워크를 선택합니다. 네트워크가 없는 경우 만들어야 합니다. Azure 리소스 관리자를 사용하여 네트워크를 만들려면 **새 을 만듭니다.** 해당하는 경우 서브넷을 선택한 다음 **확인을**선택합니다.

   :::image type="content" source="./media/vmware-azure-enable-replication/enable-rep3.png" alt-text="복제 대상 창 사용":::

1. **가상 시스템의** > 경우**가상 컴퓨터를 선택하고**복제할 각 가상 컴퓨터를 선택합니다. 복제를 활성화할 수 있는 가상 컴퓨터만 선택할 수 있습니다. 그런 다음, **확인**을 선택합니다. 특정 가상 컴퓨터를 보거나 선택할 수 없는 경우 문제를 해결하기 위해 [Azure 포털에 원본 컴퓨터가 나열되지 않은](vmware-azure-troubleshoot-replication.md#step-3-troubleshoot-source-machines-that-arent-available-for-replication) 것을 참조하세요.

   :::image type="content" source="./media/vmware-azure-enable-replication/enable-replication5.png" alt-text="복제 활성화 가상 컴퓨터 창 선택":::

1. **속성** > **구성 속성의**경우 프로세스 서버가 VM에 사이트 복구 이동성 서비스를 자동으로 설치하는 데 사용하는 계정을 선택합니다. 또한 데이터 변동 패턴에 따라 복제에 사용할 대상 관리 디스크 유형을 선택합니다.
1. 기본적으로 원본 VM의 모든 디스크가 복제됩니다. 복제에서 디스크를 제외하려면 복제하지 않으려는 디스크에 대한 **포함** 확인란을 선택 취소합니다. 그런 다음, **확인**을 선택합니다. 나중에 추가 속성을 설정할 수 있습니다. 디스크 제외에 대해 [자세히 알아보세요](vmware-azure-exclude-disk.md).

   :::image type="content" source="./media/vmware-azure-enable-replication/enable-replication6.png" alt-text="복제 구성 속성 창 사용":::

1. **복제 설정에서** > **복제 설정 구성,** 올바른 복제 정책이 선택되었는지 확인합니다. **설정** > **복제 정책** > _이름_ > 편집**설정에서**복제 정책 설정을 수정할 수 있습니다. 정책에 적용된 변경 사항은 복제 및 새 가상 시스템에도 적용됩니다.
1. 가상 컴퓨터를 복제 그룹으로 수집하려면 **다중 VM 일관성을**사용하도록 설정합니다. 그룹에 대한 이름을 지정한 다음 **확인을**선택합니다.

   > [!NOTE]
   > - 복제 그룹의 가상 시스템은 함께 복제되며 장애 조치될 때 충돌 일관성 및 앱 일관된 복구 지점을 공유합니다.
   > - 워크로드를 미러링하도록 VM 및 물리적 서버를 함께 수집합니다. 다중 VM 일관성을 사용하면 워크로드 성능에 영향을 줄 수 있습니다. 가상 시스템이 동일한 워크로드를 실행하고 일관성이 필요한 경우에만 이 작업을 수행합니다.

   :::image type="content" source="./media/vmware-azure-enable-replication/enable-replication7.png" alt-text="복제 창 사용":::

1. **복제 활성화를**선택합니다.  >  **설정** > **작업****사이트 복구 작업에서**보호 **활성화** 작업의 진행 상황을 추적할 수 있습니다. 보호 **종료** 작업이 실행되면 가상 시스템이 장애 조치(failover)에 사용할 준비가 된 것입니다.

## <a name="view-and-manage-vm-properties"></a>VM 속성 보기 및 관리

다음으로 원본 가상 시스템의 속성을 확인합니다. Azure VM 이름이 [Azure 가상 머신 요구 사항](vmware-physical-azure-support-matrix.md#replicated-machines)을 준수해야 합니다.

1. **복제된 설정으로** > **이동한**다음 가상 컴퓨터를 선택합니다. **필수** 페이지에는 VM의 설정 및 상태에 대한 정보가 표시됩니다.
1. **속성**에서 해당 VM에 대한 복제 및 장애 조치(failover) 정보를 볼 수 있습니다.
1. **계산 및 네트워크** > **계산 속성에서**여러 VM 속성을 변경할 수 있습니다.

   :::image type="content" source="./media/vmware-azure-enable-replication/vmproperties.png" alt-text="계산 및 네트워크 속성 창":::

   - **Azure VM 이름**: 필요한 경우 Azure 요구 사항을 충족하도록 이름을 수정합니다.
   - **대상 VM 크기 또는 VM 유형:** 기본 VM 크기는 대상 Azure 영역에서 디스크 수, NIC 수, CPU 코어 수, 메모리 및 사용 가능한 VM 역할 크기를 포함하는 매개 변수를 기반으로 선택됩니다. Azure 사이트 복구는 모든 조건을 충족시키는 사용 가능한 첫 번째 VM 크기를 선택합니다. 장애 조치(failover) 전에 언제든지 필요에 따라 다른 VM 크기를 선택할 수 있습니다. VM 디스크 크기는 원본 디스크 크기도 기반으로 하며 장애 조치 후에만 변경할 수 있습니다. Windows의 VM 디스크에 대한 [확장성 및 성능 목표에 대한](/azure/virtual-machines/windows/disk-scalability-targets)디스크 크기 및 IOPS 속도에 대해 자세히 알아보십시오.
   - **리소스 그룹:** 가상 시스템이 장애 조치(failover)의 일부가 되는 [리소스 그룹을](/azure/azure-resource-manager/management/overview#resource-groups)선택할 수 있습니다. 장애 조치(failover) 전에 언제든지 이 설정을 변경할 수 있습니다. 장애 조치 후 가상 컴퓨터를 다른 리소스 그룹으로 마이그레이션하는 경우 해당 가상 컴퓨터 중단에 대한 보호 설정이 중단됩니다.
   - **가용성 집합**: 가상 시스템이 장애 조치(failover)의 일부가 되어야 하는 경우 [가용성 집합을](/azure/virtual-machines/windows/tutorial-availability-sets) 선택할 수 있습니다. 가용성 집합을 선택할 때 다음 정보를 염두에 두십시오.
     - 지정된 리소스 그룹에 속한 가용성 집합만 나열됩니다.
     - 서로 다른 가상 네트워크에 있는 VM은 동일한 가용성 집합의 일부가 될 수 없습니다.
     - 동일한 크기의 가상 머신만 가용성 집합의 일부가 될 수 있습니다.

1. Azure VM에 할당된 대상 네트워크, 서브넷 및 IP 주소에 대한 정보를 추가할 수도 있습니다.
1. **디스크**에서 복제될 VM의 운영 체제 및 데이터 디스크를 볼 수 있습니다.

### <a name="configure-networks-and-ip-addresses"></a>네트워크 및 IP 주소 구성

대상 IP 주소를 설정할 수 있습니다.

- 주소를 제공하지 않으면 VM을 통해 실패한 경우 DHCP를 사용합니다.
- 장애 조치(failover) 시 사용할 수 없는 주소를 설정하면 장애 조치(failover)가 작동하지 않습니다.
- 테스트 장애 조치 네트워크에서 주소를 사용할 수 있는 경우 테스트 장애 조치(failover)에 동일한 대상 IP 주소를 사용할 수 있습니다.

네트워크 어댑터 수는 다음과 같이 대상 가상 시스템에 대해 지정한 크기에 따라 결정됩니다.

- 원본 가상 시스템의 네트워크 어댑터 수가 대상 VM 크기에 허용되는 어댑터 수보다 작거나 같으면 대상은 원본과 동일한 어댑터 수를 가수입니다.
- 원본 가상 시스템의 어댑터 수가 대상 VM 크기에 허용되는 수를 초과하면 대상 크기 최대가 사용됩니다. 예를 들어 원본 가상 시스템에 두 개의 네트워크 어댑터가 있고 대상 VM의 크기가 4개를 지원하는 경우 대상 가상 시스템에는 두 개의 어댑터가 있습니다. 원본 VM에 두 개의 어댑터가 있지만 대상 크기가 하나만 지원하는 경우 대상 VM에는 하나의 어댑터만 있습니다.
- 가상 머신에 네트워크 어댑터가 여러 개 있으면 모두 동일한 네트워크에 연결됩니다. 또한 목록에 표시되는 첫 번째 어댑터는 Azure 가상 컴퓨터의 기본 네트워크 어댑터가 됩니다.

### <a name="azure-hybrid-benefit"></a>Azure 하이브리드 혜택

Microsoft 소프트웨어 보증 고객은 Azure 하이브리드 혜택을 사용하여 Azure로 마이그레이션되는 Windows Server 컴퓨터에 대한 라이선스 비용을 절감할 수 있습니다. 이 혜택은 Azure 재해 복구에도 적용됩니다. 자격이 있는 경우 장애 조치(failover)가 있는 경우 사이트 복구가 만드는 가상 시스템에 이점을 할당할 수 있습니다.

1. 복제된 가상 시스템의 **컴퓨터 및 네트워크 속성으로** 이동합니다.
1. Azure 하이브리드 혜택을 받을 수 있는 Windows 서버 라이선스가 있는지 묻는 메시지가 표시됩니다.
1. 장애 조치(failover)에서 생성될 VM에 혜택을 적용하는 데 사용할 수 있는 소프트웨어 보증을 통해 적격 한 Windows Server 라이선스가 있는지 확인합니다.
1. 복제된 가상 시스템에 대한 설정을 저장합니다.

Azure 하이브리드 혜택에 대해 [자세히 알아보세요.](https://azure.microsoft.com/pricing/hybrid-benefit/)

## <a name="next-steps"></a>다음 단계

가상 시스템이 보호된 상태에 도달하면 [장애 조치(failover)를](site-recovery-failover.md) 시도하여 응용 프로그램이 Azure에 나타나는지 확인합니다.

- 복제를 사용하지 않도록 설정하려면 등록 및 보호 설정을 정리하는 방법에 대해 [자세히 알아봅니다.](site-recovery-manage-registration-and-protection.md)
- PowerShell을 사용하여 가상 시스템에 대한 복제를 자동화하는 방법에 대해 [자세히 알아봅니다.](vmware-azure-disaster-recovery-powershell.md)
