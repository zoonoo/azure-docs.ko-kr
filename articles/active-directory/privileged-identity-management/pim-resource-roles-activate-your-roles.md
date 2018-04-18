---
title: Azure 리소스에 대한 Privileged Identity Management - 역할 활성화 | Microsoft Docs
description: PIM의 역할을 활성화하는 방법을 설명합니다.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/02/2018
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: 3e5456e7a632639cb82d7ba2b2e073938b1798ef
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2018
---
# <a name="privileged-identity-management---resource-roles---activate"></a>Privileged Identity Management - 리소스 역할 - 활성화
Azure Resources에 대한 역할 활성화에서는 적격 역할 구성원이 미래의 날짜/시간으로 활성화를 예약하고 관리자가 구성한 최대 기간 내에서 특정 활성화를 선택할 수 있는 새로운 환경을 소개하고 있습니다. [여기서 Azure AD 역할 활성화](../active-directory-privileged-identity-management-how-to-activate-role.md)에 대해 자세히 알아봅니다.

## <a name="activate-roles"></a>역할 활성화
왼쪽 탐색 모음에서 내 역할 섹션으로 이동합니다. 활성화하려는 역할에 대해 "활성화"를 클릭합니다.
![](media/azure-pim-resource-rbac/rbac-roles.png)

[활성화] 메뉴에서 원하는 시작 날짜와 시간을 입력하여 해당 역할을 활성화합니다. 선택적으로 활성화 기간(역할이 활성 상태로 유지되는 기간)을 줄이고 필요한 경우 근거를 입력합니다. 그런 다음 [활성화]를 클릭합니다.

시작 날짜와 시간이 수정되지 않는다면 해당 역할이 몇 초 이내에 활성화됩니다. [내 역할] 페이지에서 활성화 배너 메시지에 대한 큐에서 대기 중인 역할이 표시됩니다. 이 메시지를 지우려면 [새로 고침] 단추를 클릭합니다.

![](media/azure-pim-resource-rbac/rbac-activate-notification.png)

활성화가 미래의 날짜와 시간으로 예약되면 보류 중인 요청이 왼쪽 탐색 메뉴의 [보류 중인 요청] 탭에 표시됩니다. 역할 활성화가 더 이상 필요하지 않은 경우 사용자는 페이지의 오른쪽에 있는 [취소] 단추를 클릭하여 해당 요청을 취소할 수 있습니다.

![](media/azure-pim-resource-rbac/rbac-activate-pending.png)


## <a name="just-enough-administration"></a>Just Enough Administration

Azure 리소스용 PIM을 사용하면 리소스 역할 할당에 대한 JEA(Just Enough Administration) 모범 사례를 쉽게 적용할 수 있습니다. Azure 구독 또는 리소스 그룹에 할당된 사용자 및 그룹 구성원은 축소된 범위에서 기존 역할 할당을 활성화할 수 있습니다. 

검색 페이지에서 관리해야 하는 종속 리소스를 찾습니다.

![](media/azure-pim-resource-rbac/azure-resources-02.png)

왼쪽 탐색 메뉴에서 [내 역할]을 선택하고 활성화할 적절한 역할을 선택합니다. 아래와 같이 리소스 그룹이 아닌 구독에 역할이 할당되었으므로 할당 유형이 [상속됨]입니다.

![](media/azure-pim-resource-rbac/my-roles-02.png)
