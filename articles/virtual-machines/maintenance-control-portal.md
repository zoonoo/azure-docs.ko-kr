---
title: Azure Portal를 사용 하 여 Azure 가상 머신에 대 한 유지 관리 제어
description: 유지 관리 제어 및 Azure Portal를 사용 하 여 Azure Vm에 유지 관리를 적용 하는 시기를 제어 하는 방법을 알아봅니다.
author: cynthn
ms.service: virtual-machines
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 04/22/2020
ms.author: cynthn
ms.openlocfilehash: b174e2631131e6bf26d7b1cb62442c8a99102e2e
ms.sourcegitcommit: 4313e0d13714559d67d51770b2b9b92e4b0cc629
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/27/2020
ms.locfileid: "91397286"
---
# <a name="control-updates-with-maintenance-control-and-the-azure-portal"></a>유지 관리 제어 및 Azure Portal를 사용 하 여 업데이트 제어

유지 관리 제어를 통해 격리 된 Vm 및 Azure 전용 호스트에 업데이트를 적용 하는 시기를 결정할 수 있습니다. 이 항목에서는 유지 관리 제어를 위한 Azure Portal 옵션을 설명 합니다. 유지 관리 제어, 제한 사항 및 기타 관리 옵션을 사용 하는 이점에 대 한 자세한 내용은 [유지 관리 제어를 사용 하 여 플랫폼 업데이트 관리](maintenance-control.md)를 참조 하세요.

## <a name="create-a-maintenance-configuration"></a>유지 관리 구성 만들기

1. Azure Portal에 로그인합니다.

1. **유지 관리 구성을**검색 합니다.

   ![유지 관리 구성을 여는 방법을 보여 주는 스크린샷](media/virtual-machines-maintenance-control-portal/maintenance-configurations-search.png)

1. **추가**를 클릭합니다.

   ![유지 관리 구성을 추가 하는 방법을 보여 주는 스크린샷](media/virtual-machines-maintenance-control-portal/maintenance-configurations-add.png)

1. 구독 및 리소스 그룹을 선택 하 고, 구성에 대 한 이름을 제공 하 고, 지역을 선택 합니다. **다음**을 클릭합니다.

   ![유지 관리 구성 기본 사항을 보여 주는 스크린샷](media/virtual-machines-maintenance-control-portal/maintenance-configurations-basics.png)

1. 태그 및 값을 추가 합니다. **다음**을 클릭합니다.

   ![유지 관리 구성에 태그를 추가 하는 방법을 보여 주는 스크린샷](media/virtual-machines-maintenance-control-portal/maintenance-configurations-tags.png)

1. 요약을 검토합니다. **만들기**를 클릭합니다.

   ![유지 관리 구성을 만드는 방법을 보여 주는 스크린샷](media/virtual-machines-maintenance-control-portal/maintenance-configurations-create.png)

1. 배포가 완료 되 면 **리소스로 이동**을 클릭 합니다.

   ![유지 관리 구성 배포 완료를 보여 주는 스크린샷](media/virtual-machines-maintenance-control-portal/maintenance-configurations-deployment-complete.png)

## <a name="assign-the-configuration"></a>구성 할당

유지 관리 구성의 세부 정보 페이지에서 할당을 클릭 한 다음 **리소스 할당**을 클릭 합니다. 

![리소스를 할당 하는 방법을 보여 주는 스크린샷](media/virtual-machines-maintenance-control-portal/maintenance-configurations-add-assignment.png)

유지 관리 구성에 할당 하려는 리소스를 선택 하 고 **확인**을 클릭 합니다. **유형** 열은 리소스가 격리 된 VM 또는 Azure 전용 호스트 인지 여부를 표시 합니다. 구성을 할당 하려면 VM이 실행 되 고 있어야 합니다. 중지 된 VM에 구성을 할당 하려고 하면 오류가 발생 합니다. 

<!---Shantanu to add details about the error case--->

![리소스를 선택 하는 방법을 보여 주는 스크린샷](media/virtual-machines-maintenance-control-portal/maintenance-configurations-select-resource.png)

## <a name="check-configuration"></a>구성 확인

구성이 올바르게 적용 되었는지 확인 하거나 **유지 관리 구성을**사용 하 여 현재 할당 된 유지 관리 구성을 확인할 수 있습니다. **유형** 열은 구성이 격리 된 VM 또는 Azure 전용 호스트에 할당 되는지 여부를 표시 합니다. 

![유지 관리 구성을 확인 하는 방법을 보여 주는 스크린샷](media/virtual-machines-maintenance-control-portal/maintenance-configurations-host-type.png)

해당 속성 페이지에서 특정 가상 컴퓨터에 대 한 구성을 확인할 수도 있습니다. **유지 관리** 를 클릭 하 여 해당 가상 컴퓨터에 할당 된 구성을 확인 합니다.

![호스트의 유지 관리를 확인 하는 방법을 보여 주는 스크린샷](media/virtual-machines-maintenance-control-portal/maintenance-configurations-check-config.png)

## <a name="check-for-pending-updates"></a>보류 중인 업데이트 확인

또한 유지 관리 구성에 대 한 업데이트가 보류 중인지 확인 하는 두 가지 방법이 있습니다. **유지 관리 구성**의 구성에 대 한 세부 정보에서 **할당** 을 클릭 하 고 **유지 관리 상태**를 확인 합니다.

![보류 중인 업데이트를 확인 하는 방법을 보여 주는 스크린샷](media/virtual-machines-maintenance-control-portal/maintenance-configurations-pending.png)

전용 호스트의 **Virtual Machines** 또는 속성을 사용 하 여 특정 호스트를 확인할 수도 있습니다. 

![강조 표시 된 유지 관리 상태를 보여 주는 스크린샷](media/virtual-machines-maintenance-control-portal/maintenance-configurations-pending-vm.png)

## <a name="apply-updates"></a>업데이트 적용

**Virtual Machines**를 사용 하 여 요청 시 보류 중인 업데이트를 적용할 수 있습니다. VM 세부 정보에서 **유지 관리** 를 클릭 하 고 **지금 유지 관리 적용**을 클릭 합니다.

![보류 중인 업데이트를 적용 하는 방법을 보여 주는 스크린샷](media/virtual-machines-maintenance-control-portal/maintenance-configurations-apply-updates-now.png)

## <a name="check-the-status-of-applying-updates"></a>업데이트 적용 상태 확인 

**유지 관리 구성** 또는 **Virtual Machines**를 사용 하 여 구성에 대 한 업데이트의 진행률을 확인할 수 있습니다. VM 세부 정보에서 **유지 관리**를 클릭 합니다. 다음 예에서는 **유지 관리 상태** 에 업데이트 **보류 중**이 표시 됩니다.

![보류 중인 업데이트의 상태를 확인 하는 방법을 보여 주는 스크린샷](media/virtual-machines-maintenance-control-portal/maintenance-configurations-status.png)

## <a name="delete-a-maintenance-configuration"></a>유지 관리 구성 삭제

구성을 삭제 하려면 구성 세부 정보를 열고 **삭제**를 클릭 합니다.

![구성을 삭제 하는 방법을 보여 주는 스크린샷](media/virtual-machines-maintenance-control-portal/maintenance-configurations-delete.png)


## <a name="next-steps"></a>다음 단계

자세히 알아보려면 [유지 관리 및 업데이트](maintenance-and-updates.md)를 참조 하세요.
