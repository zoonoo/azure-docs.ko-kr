---
title: Azure Policy에서 가상 머신용 Automanage 사용
description: Azure Portal에서 기본 제공 Azure Policy를 통해 VM에 대해 Azure Automanage를 사용하도록 설정하는 방법에 대해 알아봅니다.
author: ju-shim
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: how-to
ms.date: 09/04/2020
ms.author: jushiman
ms.openlocfilehash: 8f679626b69bd855e86b94cdde51955edd068e8f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91714895"
---
# <a name="enable-automanage-for-virtual-machines-through-azure-policy"></a>Azure Policy에서 가상 머신용 Automanage 사용

많은 VM에 대해 Automanage를 사용하도록 설정하려면 기본 제공 [Azure Policy](..\governance\azure-management.md)를 사용하여 수행할 수 있습니다. 이 문서에서는 Azure Portal에서 Automanage를 사용하도록 설정하기 위해 올바른 정책을 찾고 이를 할당하는 방법을 안내합니다.


## <a name="prerequisites"></a>필수 구성 요소

Azure 구독이 없는 경우 시작하기 전에 [계정을 만드세요](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/).

> [!NOTE]
> 평가판 계정은 이 자습서에서 사용되는 가상 머신에 액세스할 수 없습니다. 종량제 구독으로 업그레이드하세요.

> [!IMPORTANT]
> Automanage를 사용하도록 설정하려면 **소유자** 역할 또는 **기여자** 및 **사용자 액세스 관리자** 역할과 같은 Azure RBAC 권한이 필요합니다.


## <a name="sign-in-to-azure"></a>Azure에 로그인

[Azure Portal](https://portal.azure.com/)에 로그인합니다.


## <a name="locate-and-assign-the-policy"></a>정책 찾기 및 할당

1. Azure Portal에서 **정책** 으로 이동합니다.
1. **정의** 창으로 이동합니다.
1. **범주** 드롭다운을 클릭하여 사용 가능한 옵션을 확인합니다.
1. **자동 관리 - Azure 가상 머신 모범 사례** 옵션을 선택합니다.
1. 이제 목록이 업데이트되어 *자동 관리 사용* 으로 시작하는 이름의 기본 제공 정책이 표시됩니다.
1. *Automanage 사용 - Azure 가상 머신 모범 사례* 기본 제공 정책 이름을 클릭합니다.
1. 정책을 클릭하면 이제 **정의** 탭을 볼 수 있습니다.

    > [!NOTE]
    > Azure Policy 정의는 구성 프로필 또는 계정과 같은 Automanage 매개 변수를 설정하는 데 사용됩니다. 또한 정책이 올바른 VM에만 적용되도록 하는 필터를 설정합니다.

1. **할당 단추** 를 클릭하여 할당을 만듭니다.
1. **기본 사항** 탭에서 *구독* 및 *리소스 그룹* 을 설정하여 **범위** 를 채웁니다.

    > [!NOTE]
    > 범위에서 이 정책이 적용되는 VM을 정의할 수 있습니다. 구독 수준 또는 리소스 그룹 수준에서 애플리케이션을 설정할 수 있습니다. 리소스 그룹을 설정하면 현재 해당 리소스 그룹에 있는 모든 VM 또는 여기에 추가되는 향후 VM에 Automanage가 자동으로 활성화됩니다. 

1. **매개 변수** 탭을 클릭하고 **Automanage 계정** 및 원하는 **구성 프로필** 을 설정합니다. 
1. **검토 + 만들기** 탭 아래에서 설정을 검토합니다.
1. **만들기** 를 클릭하여 할당을 적용합니다.
1. **정의** 옆에 있는 **할당** 탭에서 할당을 확인합니다.

> [!NOTE]
> 해당 정책이 현재 리소스 그룹 또는 구독에 있는 VM에 적용되기 시작하는 데 약간의 시간이 걸립니다.


## <a name="next-steps"></a>다음 단계 

Azure Portal을 통해 가상 머신에 대해 Azure Automanage를 사용하도록 설정하는 또 다른 방법을 알아봅니다. 

> [!div class="nextstepaction"]
> [Azure Portal에서 가상 머신용 Automanage 사용](quick-create-virtual-machines-portal.md)