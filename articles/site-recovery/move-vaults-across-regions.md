---
title: Azure Site Recovery 구성을 다른 Azure 지역으로 이동 | Microsoft Docs
description: Site Recovery 구성을 다른 Azure 지역으로 이동하기 위한 지침입니다.
services: site-recovery
author: rajani-janaki-ram
ms.service: site-recovery
ms.topic: tutorial
ms.date: 07/31/2019
ms.author: rajanaki
ms.custom: MVC
ms.openlocfilehash: 2cf06a0c4e35d22cbad260201183516db2f07436
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2019
ms.locfileid: "70013470"
---
# <a name="move-a-recovery-services-vault-and-azure-site-recovery-configuration-to-another-azure-region"></a>Recovery Services 자격 증명 모음 및 Azure Site Recovery 구성을 다른 Azure 지역으로 이동

기존 Azure 리소스를 한 지역에서 다른 지역으로 이동하려는 다양한 상황이 있습니다. 예를 들면 관리 용이성, 거버넌스 사유 또는 회사 인수 합병 등이 있습니다. Azure VM을 이동할 때 이동할 수 있는 관련 리소스 중 하나는 재해 복구 구성입니다. 

기존 재해 복구 구성을 한 지역에서 다른 지역으로 이동할 수 있는 최상의 방법은 없습니다. 원본 VM 지역에 따라 대상 지역을 구성했기 때문입니다. 원본 지역을 변경하기로 결정한 경우 대상 지역의 이전 기존 구성은 다시 사용할 수 없으며 다시 설정해야 합니다. 이 문서에서는 재해 복구 설정을 다시 구성하고 다른 지역으로 이동하는 단계별 프로세스를 정의합니다.

이 문서에서는 다음을 수행합니다.

> [!div class="checklist"]
> * 이동을 위한 필수 조건을 확인합니다.
> * Azure Site Recovery에서 사용한 리소스를 식별합니다.
> * 복제를 사용하지 않도록 설정합니다.
> * 리소스를 삭제합니다.
> * VM의 새 원본 지역에 따라 Site Recovery를 설정합니다.

> [!IMPORTANT]
> 현재는 Recovery Services 자격 증명 모음과 재해 복구 구성을 있는 그대로 다른 지역으로 이동하는 최상의 방법은 없습니다. 이 문서에서는 복제를 사용하지 않도록 설정하고 새 지역에서 설정하는 과정을 안내합니다.

## <a name="prerequisites"></a>필수 조건

- Azure VM을 다른 지역으로 이동하기 전에 재해 복구 구성을 제거하고 삭제해야 합니다. 

  > [!NOTE]
  > Azure VM의 새 대상 지역이 재해 복구 대상 지역과 동일한 경우 기존 복제 구성을 사용하고 이동할 수 있습니다. [다른 Azure 지역에 Azure IaaS VM 이동](azure-to-azure-tutorial-migrate.md)의 단계를 따릅니다.

- 의사 결정을 내리고 이해 관계자에게 알려야 합니다. VM의 이동이 완료될 때까지 VM은 재해로부터 보호되지 않습니다.

## <a name="identify-the-resources-that-were-used-by-azure-site-recovery"></a>Azure Site Recovery에서 사용한 리소스 식별
다음 단계를 진행하기 전에 이 단계를 수행하는 것이 좋습니다. VM을 복제하는 동안 관련 리소스를 식별하는 것이 더 쉽습니다.

복제되는 각 Azure VM에 대해 **보호된 항목** > **복제된 항목** > **속성**으로 차례로 이동하여 다음 리소스를 식별합니다.

- 대상 리소스 그룹
- 캐시 스토리지 계정
- 대상 스토리지 계정(비관리형 디스크 기반 Azure VM의 경우) 
- 대상 네트워크


## <a name="disable-the-existing-disaster-recovery-configuration"></a>기존 재해 복구 구성 사용 안 함

1. Recovery Services 자격 증명 모음으로 이동합니다.
2. **보호된 항목** > **복제된 항목**에서 머신을 마우스 오른쪽 단추로 클릭한 후 **복제 사용 안 함**을 선택합니다.
3. 이동하려는 모든 VM에 대해 이 단계를 반복합니다.

> [!NOTE]
> 보호된 서버에서는 모바일 서비스가 제거되지 않습니다. 수동으로 제거해야 합니다. 서버를 다시 보호하려는 경우 모바일 서비스 제거를 건너뛸 수 있습니다.

## <a name="delete-the-resources"></a>리소스 삭제

1. Recovery Services 자격 증명 모음으로 이동합니다.
2. **삭제**를 선택합니다.
3. [이전에 식별한](#identify-the-resources-that-were-used-by-azure-site-recovery) 다른 리소스를 모두 삭제합니다.
 
## <a name="move-azure-vms-to-the-new-target-region"></a>새 대상 지역으로 Azure VM 이동

Azure VM을 대상 지역으로 이동하기 위한 요구 사항에 따라 이러한 문서에 나온 단계를 수행합니다.

- [다른 지역으로 Azure VM 이동](azure-to-azure-tutorial-migrate.md)
- [가용성 영역으로 Azure VM 이동](move-azure-VMs-AVset-Azone.md)

## <a name="set-up-site-recovery-based-on-the-new-source-region-for-the-vms"></a>VM의 새 원본 지역에 따라 Site Recovery 설정

[Azure VM에 대한 재해 복구 설정](azure-to-azure-tutorial-enable-replication.md)의 단계에 따라 새 지역으로 이동한 Azure VM에 대한 재해 복구를 구성합니다.
