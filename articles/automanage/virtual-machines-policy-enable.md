---
title: Azure Policy을 통해 가상 컴퓨터에 Automanage 사용
description: Azure Portal의 기본 제공 Azure Policy를 통해 Vm에 대해 Azure Automanage를 사용 하도록 설정 하는 방법에 대해 알아봅니다.
author: ju-shim
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: how-to
ms.date: 09/04/2020
ms.author: jushiman
ms.openlocfilehash: 8f679626b69bd855e86b94cdde51955edd068e8f
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2020
ms.locfileid: "91714895"
---
# <a name="enable-automanage-for-virtual-machines-through-azure-policy"></a>Azure Policy을 통해 가상 컴퓨터에 Automanage 사용

많은 Vm에 대해 Automanage를 사용 하도록 설정 하려면 기본 제공 [Azure Policy](..\governance\azure-management.md)를 사용 하면 됩니다. 이 문서에서는 올바른 정책을 찾고 Azure Portal에서 Automanage를 사용 하도록 설정 하는 방법을 안내 합니다.


## <a name="prerequisites"></a>필수 구성 요소

Azure 구독이 없는 경우 시작하기 전에 [계정을 만드세요](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/).

> [!NOTE]
> 평가판 계정은 이 자습서에서 사용되는 가상 머신에 액세스할 수 없습니다. 종량제 구독으로 업그레이드하세요.

> [!IMPORTANT]
> 자동 관리를 사용 하도록 설정 하려면 **소유자** 역할 또는 **참가자** 와 **사용자 액세스 관리자** 역할을 함께 사용 하도록 설정 하려면 다음과 같은 Azure RBAC 권한이 필요 합니다.


## <a name="sign-in-to-azure"></a>Azure에 로그인

[Azure Portal](https://portal.azure.com/)에 로그인합니다.


## <a name="locate-and-assign-the-policy"></a>정책 찾기 및 할당

1. Azure Portal **정책** 으로 이동 합니다.
1. **정의** 창으로 이동
1. **범주** 드롭다운을 클릭 하 여 사용 가능한 옵션을 확인 합니다.
1. **Automanage 사용-Azure 가상 머신 모범 사례** 옵션을 선택 합니다.
1. 이제 목록이 *자동 관리 사용* 으로 시작 하는 이름을 가진 기본 제공 정책을 표시 하도록 업데이트 됩니다.
1. *Automanage 사용-Azure 가상 머신 모범 사례* 기본 제공 정책 이름을 클릭 합니다.
1. 정책을 클릭 하면 이제 **정의** 탭을 볼 수 있습니다.

    > [!NOTE]
    > Azure Policy 정의는 구성 프로필 또는 계정과 같은 Automanage 매개 변수를 설정 하는 데 사용 됩니다. 또한 정책이 올바른 Vm에만 적용 되도록 하는 필터를 설정 합니다.

1. **할당 단추를** 클릭 하 여 할당을 만듭니다.
1. **기본 사항** 탭에서 *구독* 및 *리소스 그룹* 을 설정 하 여 **범위** 를 채웁니다.

    > [!NOTE]
    > 범위에서이 정책이 적용 되는 Vm을 정의할 수 있습니다. 구독 수준 또는 리소스 그룹 수준에서 응용 프로그램을 설정할 수 있습니다. 리소스 그룹을 설정 하는 경우 현재 해당 리소스 그룹 또는 해당 리소스 그룹에 추가 하는 이후 Vm에서 자동 관리가 자동으로 사용 하도록 설정 됩니다. 

1. **매개 변수** 탭을 클릭 하 고 **automanage 계정** 및 원하는 **구성 프로필** 을 설정 합니다. 
1. **검토 + 만들기** 탭에서 설정을 검토 합니다.
1. **만들기** 를 클릭 하 여 할당을 적용 합니다.
1. **정의** 옆의 **할당** 탭에서 할당 보기

> [!NOTE]
> 해당 정책이 리소스 그룹 또는 구독에 현재 있는 Vm에 적용 되기 시작 하는 데 다소 시간이 걸립니다.


## <a name="next-steps"></a>다음 단계 

Azure Portal을 통해 가상 컴퓨터에 대해 Azure Automanage를 사용 하도록 설정 하는 다른 방법을 알아봅니다. 

> [!div class="nextstepaction"]
> [Azure Portal에서 가상 컴퓨터에 대해 Automanage를 사용 하도록 설정](quick-create-virtual-machines-portal.md)