---
title: 빠른 시작 - Azure Portal에서 VM용 Azure Automanage 사용
description: Azure Portal에서 신규 또는 기존 VM에 가상 머신용 Automanage를 신속하게 사용하도록 설정하는 방법을 알아봅니다.
author: ju-shim
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: quickstart
ms.date: 09/04/2020
ms.author: jushiman
ms.openlocfilehash: 43e0805a828a2ed32d23984b6ffef95a7e710953
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90945405"
---
# <a name="quickstart-enable-azure-automanage-for-virtual-machines-in-the-azure-portal"></a>빠른 시작: Azure Portal에서 가상 머신용 Azure Automanage 사용

Azure Portal을 사용하여 신규 또는 기존 가상 머신에 Automanage를 사용하도록 설정하여 가상 머신용 Azure Automanage를 시작하세요.


## <a name="prerequisites"></a>필수 구성 요소

Azure 구독이 없는 경우 시작하기 전에 [계정을 만드세요](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/).

> [!NOTE]
> 평가판 계정은 이 자습서에서 사용되는 가상 머신에 액세스할 수 없습니다. 종량제 구독으로 업그레이드하세요.

> [!IMPORTANT]
> Automanage를 사용하도록 설정하려면 다음 RBAC 권한이 필요합니다. **소유자** 역할 또는 **기여자**와 **사용자 액세스 관리자** 역할


## <a name="sign-in-to-azure"></a>Azure에 로그인

[Azure Portal](https://portal.azure.com/)에 로그인합니다.


## <a name="enable-automanage-for-vms-on-an-existing-vm"></a>기존 VM에서 VM용 Automanage 사용

1. 검색 창에서 **Automanage – Azure 가상 머신 모범 사례**를 검색하고 선택합니다.

2. **기존 VM에서 사용**을 선택합니다.

    :::image type="content" source="media\quick-create-virtual-machine-portal\zero-vm-list-view.png" alt-text="기존 VM에서 사용":::

3. **머신 선택** 블레이드에서 다음을 수행합니다.
    1. **구독** 및 **리소스 그룹**을 기준으로 VM 목록을 필터링합니다.
    1. 온보딩하려는 각 가상 머신의 확인란을 선택합니다.
    1. **선택** 단추를 클릭합니다.

    :::image type="content" source="media\quick-create-virtual-machine-portal\existing-vm-select-machine.png" alt-text="사용 가능한 VM 목록에서 기존 VM 선택":::

4. **구성 프로필**에서 **프로필 및 기본 설정을 찾아 변경**을 클릭합니다.

    :::image type="content" source="media\quick-create-virtual-machine-portal\existing-vm-quick-create.png" alt-text="프로필 및 기본 설정을 찾아 변경":::

5. **구성 프로필 + 기본 설정 선택** 블레이드에서 다음을 수행합니다.
    1. 왼쪽에서 프로필을 선택합니다. 테스트의 경우 *개발/테스트*를, 프로덕션의 경우 *프로덕션*을 선택합니다.
    1. **선택** 단추를 클릭합니다.

    :::image type="content" source="media\quick-create-virtual-machine-portal\browse-production-profile.png" alt-text="프로덕션 구성 프로필 찾아보기":::

6. **사용** 단추를 클릭합니다.


## <a name="enable-automanage-for-vms-on-a-new-vm"></a>신규 VM에서 VM용 Automanage 사용

1. [빠른 시작 - Azure Portal에서 Windows VM 만들기](..\virtual-machines\windows\quick-create-portal.md)의 생성 단계를 수행합니다.

2. VM이 배포되면 배포 상태 페이지가 표시되고 하단에 권장되는 **다음 단계**가 표시됩니다.

    :::image type="content" source="media\quick-create-virtual-machine-portal\create-next-steps.png" alt-text="배포 페이지 하단에 있는 다음 단계 섹션":::

3. **다음 단계**에서 **가상 머신 자동 관리 사용 모범 사례**를 선택합니다.

4. **Automanage – Azure 가상 머신 모범 사례** 페이지에서 **머신**은 새로 만든 VM으로 자동 입력됩니다.

    :::image type="content" source="media\quick-create-virtual-machine-portal\create-new-enable-overview.png" alt-text="새로 만든 VM이 선택한 머신으로 표시됩니다.":::

5. **구성 프로필**에서 **프로필 및 기본 설정을 찾아 변경**을 클릭합니다.

6. **구성 프로필 + 기본 설정 선택** 블레이드에서 다음을 수행합니다.
    1. 왼쪽에서 프로필을 선택합니다. 테스트의 경우 *개발/테스트*를, 프로덕션의 경우 *프로덕션*을 선택합니다.
    1. **선택** 단추를 클릭합니다.

    :::image type="content" source="media\quick-create-virtual-machine-portal\browse-production-profile.png" alt-text="프로덕션 구성 프로필 찾아보기":::

7. **사용** 단추를 클릭합니다.


## <a name="disable-automanage-for-vms"></a>VM용 Automanage 사용 안 함

자동 관리를 사용하지 않도록 설정하여 가상 머신용 Azure Automanage 사용을 신속하게 중지합니다.

:::image type="content" source="media\automanage-virtual-machines\disable-step-1.png" alt-text="가상 머신에서 Automanage 사용 안 함":::

1. 모든 자동 관리형 VM이 나열되는 **Automanage – Azure 가상 머신 모범 사례** 페이지로 이동합니다.
1. 사용하지 않도록 설정할 가상 머신 옆의 확인란을 선택합니다.
1. **자동 관리 사용 안 함** 버튼을 클릭합니다.
1. **사용 안 함**에 동의하기 전에 결과 팝업의 메시지를 자세히 읽어 보세요.


## <a name="clean-up-resources"></a>리소스 정리

가상 머신용 Azure Automanage를 사용해 보기 위해 새 리소스 그룹을 만들었는데 더 이상 필요하지 않다면 리소스 그룹을 삭제할 수 있습니다. 그룹을 삭제하면 리소스 그룹에 포함된 모든 리소스와 VM도 삭제됩니다.

Azure Automanage는 리소스를 저장할 기본 리소스 그룹을 만듭니다. "DefaultResourceGroupRegionName" 및 "AzureBackupRGRegionName"이라는 명명 규칙을 가진 리소스 그룹을 확인하여 모든 리소스를 정리합니다.

1. **리소스 그룹**을 선택합니다.
1. 리소스 그룹 페이지에서 **삭제**를 선택합니다.
1. 메시지가 표시되면 리소스 그룹의 이름을 확인한 다음, **삭제**를 선택합니다.


## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 VM용 Azure Automanage를 사용하도록 설정했습니다. 

가상 머신에 Automanage를 사용하도록 설정할 때 사용자 지정된 기본 설정을 만들고 적용하는 방법을 알아보세요. 

> [!div class="nextstepaction"]
> [VMS용 Azure Automanage - 사용자 지정 구성 프로필](virtual-machines-custom-preferences.md)
