---
title: Azure Automanage for Vm에서 사용자 지정 기본 설정 만들기
description: Azure Automanage for Vm에서 구성 프로필을 조정 하 고 사용자 고유의 기본 설정을 지정 하는 방법에 대해 알아봅니다.
author: ju-shim
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: how-to
ms.date: 09/04/2020
ms.author: jushiman
ms.openlocfilehash: cf8e7340ce3554bf27364335457a5b7eadcdc049
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90937642"
---
# <a name="create-a-custom-preference-in-azure-automanage-for-vms"></a>Azure Automanage for Vm에서 사용자 지정 기본 설정 만들기

Azure Automanage for virtual machines 모범 사례에는 필요한 경우 조정할 수 있는 기본 구성 프로필이 있습니다. 이 문서에서는 신규 또는 기존 VM에서 automanagement를 사용 하도록 설정 하는 경우 사용자 고유의 구성 프로필 기본 설정을 지정 하는 방법을 설명 합니다.

현재 [Azure Backup](..\backup\backup-azure-arm-vms-prepare.md#create-a-custom-policy) 및 [Microsoft 맬웨어 방지 프로그램](../security/fundamentals/antimalware.md#default-and-custom-antimalware-configuration)에 대 한 사용자 지정을 지원 합니다.


> [!NOTE]
> Automanage를 사용 하는 동안에는 VM의 구성 프로필 또는 기본 설정을 변경할 수 없습니다. 해당 VM에 대해 Automanage를 사용 하지 않도록 설정한 다음 원하는 구성 프로필 및 기본 설정으로 Automanage를 다시 사용 하도록 설정 해야 합니다.


## <a name="prerequisites"></a>필수 구성 요소

Azure 구독이 없는 경우 시작하기 전에 [계정을 만드세요](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/).

> [!NOTE]
> 평가판 계정은 이 자습서에서 사용되는 가상 머신에 액세스할 수 없습니다. 종량제 구독으로 업그레이드하세요.

> [!IMPORTANT]
> 자동 관리를 사용 하도록 설정 하려면 **소유자** 역할 또는 **참가자** 와 **사용자 액세스 관리자** 역할을 사용 하도록 설정 하려면 다음 RBAC 권한이 필요 합니다.


## <a name="sign-in-to-azure"></a>Azure에 로그인

[Azure Portal](https://portal.azure.com/)에 로그인합니다.


## <a name="enable-automanage-for-vms-on-an-existing-vm"></a>기존 VM에서 Vm에 대해 Automanage 사용

1. 검색 창에서 **Automanage – Azure virtual machines 모범 사례**를 검색 하 고 선택 합니다.

2. **기존 VM에서 사용**을 선택 합니다.

3. **컴퓨터 선택** 블레이드에서 다음을 수행 합니다.
    1. **구독** 및 **리소스 그룹**을 기준으로 vm 목록을 필터링 합니다.
    1. 온보드 할 각 가상 머신의 확인란을 선택 합니다.
    1. **선택** 단추를 클릭합니다.

    :::image type="content" source="media\virtual-machine-custom-preferences\existing-vm-select-machine.png" alt-text="사용 가능한 Vm 목록에서 기존 VM을 선택 합니다.":::

4. **구성 프로필**에서 찾아보기를 클릭 **하 고 프로필 및 기본 설정을 변경**합니다.

    :::image type="content" source="media\virtual-machine-custom-preferences\existing-vm-quick-create.png" alt-text="프로필 및 기본 설정을 찾아보고 변경 합니다.":::

5. **구성 프로필 + 기본 설정 선택** 블레이드에서 왼쪽에 있는 프로필을 선택 합니다. 테스트에 대 한 *개발/테스트* , 프로덕션에 대해 *Prod* 입니다.

    :::image type="content" source="media\virtual-machine-custom-preferences\browse-production-profile.png" alt-text="프로덕션 구성 프로필을 찾아봅니다.":::

6. 선택한 프로필의 **구성 기본 설정** 에 특정 서비스에 대해 조정할 수 있는 드롭다운이 있습니다.
    1. **새 기본 설정 만들기**를 클릭 합니다.
    1. **구성 기본 설정 만들기** 블레이드에서 기본 탭을 작성 합니다.
        1. Subscription
        1. Resource group
        1. 기본 설정 이름
        1. 지역

    :::image type="content" source="media\virtual-machine-custom-preferences\create-preference.png" alt-text="구성 기본 설정을 입력 합니다.":::

7. 기본 설정 탭으로 이동 하 여 원하는 구성 기본 설정을 조정 합니다.
        
    > [!NOTE]
    > 모범 사례에 부합 하는 조정만 프로필 구성을 변경할 때 허용 됩니다.

8. 구성 프로필을 검토 합니다.
9. **만들기** 단추를 클릭합니다.

10. **사용** 단추를 클릭합니다.


## <a name="disable-automanage-for-vms"></a>Vm에 자동 관리 사용 안 함

Automanage를 사용 하지 않도록 설정 하 여 가상 머신에 대해 Azure Automanage 사용을 신속 하 게 중지 합니다.

:::image type="content" source="media\virtual-machine-custom-preferences\disable-step-1.png" alt-text="가상 머신에서 Automanage를 사용 하지 않도록 설정":::

1. 자동 관리 되는 모든 Vm을 나열 하는 **Automanage – Azure virtual machines 모범 사례** 페이지로 이동 합니다.
1. 사용 하지 않도록 설정할 가상 컴퓨터 옆의 확인란을 선택 합니다.
1. **Automanagent 사용 안 함** 단추를 클릭 합니다.
1. **비활성화**에 동의 하기 전에 결과 팝업의 메시지를 자세히 읽습니다.


## <a name="clean-up-resources"></a>리소스 정리

새 리소스 그룹을 만들어 가상 컴퓨터에 대 한 Azure Automanage를 시도 하 고 더 이상 필요 하지 않은 경우 리소스 그룹을 삭제할 수 있습니다. 또한 그룹을 삭제 하면 VM 및 리소스 그룹의 모든 리소스가 삭제 됩니다.

Azure Automanage는 리소스를 저장할 기본 리소스 그룹을 만듭니다. "Defaultresourcegroupregid Onname" 및 "AzureBackupRGRegionName" 라는 명명 규칙을 가진 리소스 그룹을 확인 하 여 모든 리소스를 정리 합니다.

1. **리소스 그룹**을 선택합니다.
1. 리소스 그룹 페이지에서 **삭제**를 선택합니다.
1. 메시지가 표시 되 면 리소스 그룹의 이름을 확인 한 다음 **삭제**를 선택 합니다.


## <a name="next-steps"></a>다음 단계 

FAQ에서 질문과 대답을 확인 하세요. 

> [!div class="nextstepaction"]
> [질문과 대답](faq.md)