---
title: PIM에서 Azure 리소스 역할 활성화 | Microsoft Docs
description: Azure AD PIM(Privileged Identity Management)에서 Azure 리소스 역할을 활성화하는 방법을 알아봅니다.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: pim
ms.date: 08/21/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 234c1d71f0ec17d15a4dd589e3db92fd9bf68df2
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/29/2018
ms.locfileid: "43189492"
---
# <a name="activate-my-azure-resource-roles-in-pim"></a>PIM에서 Azure 리소스 역할 활성화
PIM(Privileged Identity Management)은 Azure 리소스에 대한 역할을 활성화하는 새로운 환경을 소개합니다. 적격 역할 멤버는 미래의 날짜 및 시간에 대한 활성화를 예약할 수 있습니다. 또한 최대(관리자로 구성됨) 내에서 특정 활성화 기간을 선택할 수도 있습니다. 자세한 내용은 [Azure AD Privileged Identity Management에서 역할을 활성화하거나 비활성화하는 방법](pim-how-to-activate-role.md)을 참조하세요.

## <a name="activate-a-role"></a>역할 활성화
왼쪽 창에서 **내 역할** 섹션으로 이동합니다. 활성화하려는 역할에 대해 **활성화**를 선택합니다.

!["내 역할" 창에서 "적격 역할" 탭입니다.](media/azure-pim-resource-rbac/rbac-roles.png)

**활성화** 메뉴에서 시작 날짜와 시간을 입력하여 해당 역할을 활성화합니다. 선택적으로 활성화 기간(역할이 활성 상태로 유지되는 기간)을 줄이고 필요한 경우 근거를 입력합니다. 그런 다음, **활성화**를 선택합니다.

시작 날짜와 시간이 수정되지 않는 경우 해당 역할이 몇 초 이내에 활성화됩니다. **내 역할** 창에서 배너 메시지는 역할이 활성화를 위해 대기 중임을 표시합니다. 이 메시지를 지우려면 새로 고침 단추를 선택합니다.

![승인 보류 중에 대한 배너 메시지 및 알림이 있는 "내 역할" 창](media/azure-pim-resource-rbac/rbac-activate-notification.png)

활성화가 미래의 날짜와 시간으로 예약되면 보류 중인 요청이 왼쪽 창의 **보류 중인 요청** 탭에 표시됩니다. 역할 활성화가 더 이상 필요하지 않는 경우 **취소** 단추를 선택하여 요청을 취소할 수 있습니다.

!["취소" 단추가 있는 보류 중인 요청의 목록](media/azure-pim-resource-rbac/rbac-activate-pending.png)

## <a name="use-a-role-immediately-after-activation"></a>활성화 즉시 역할 사용

캐싱으로 인해 활성화는 Azure Portal에서 새로 고쳐야 즉시 발생합니다. 역할이 활성화된 후에 지연 가능성을 줄이기 위해 포털에서 **응용 프로그램 액세스** 페이지를 사용할 수 있습니다. 이 페이지에서 액세스되는 응용 프로그램은 새 역할 할당을 즉시 확인합니다.

1. Azure AD Privileged Identity Management를 엽니다.

1. **응용 프로그램 액세스** 페이지를 클릭합니다.

    ![PIM 응용 프로그램 액세스 - 스크린샷](./media/pim-resource-roles-activate-your-roles/pim-application-access.png)

1. **모든 리소스** 페이지에서 포털을 다시 열려면 **Azure 리소스**를 클릭합니다.

    이 링크를 클릭하면 강제로 새로 고쳐 새 Azure 리소스 역할 할당을 확인합니다.

## <a name="apply-just-enough-administration-practices"></a>Just Enough Administration 사례 적용

Azure 리소스용 PIM을 사용하면 리소스 역할 할당에 대한 JEA(Just Enough Administration) 모범 사례를 쉽게 적용할 수 있습니다. Azure 구독 또는 리소스 그룹에 할당된 사용자 및 그룹 구성원은 축소된 범위에서 기존 역할 할당을 활성화할 수 있습니다. 

검색 페이지에서 관리해야 하는 종속 리소스를 찾습니다.

![리소스 선택](media/azure-pim-resource-rbac/azure-resources-02.png)

왼쪽 창에서 **내 역할**을 선택하고 활성화할 적절한 역할을 선택합니다. 리소스 그룹이 아닌 구독에 역할이 할당되었으므로 할당 유형이 **상속됨**입니다.

![할당 형식이 강조 표시된 적격 역할 할당의 목록](media/azure-pim-resource-rbac/my-roles-02.png)

## <a name="next-steps"></a>다음 단계

- [PIM에서 Azure AD 디렉터리 역할 활성화](pim-how-to-activate-role.md)