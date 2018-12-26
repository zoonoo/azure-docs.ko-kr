---
title: PIM에서 Azure 리소스 역할에 대한 액세스 검토 시작 | Microsoft Docs
description: Azure AD PIM(Privileged Identity Management)에서 Azure 리소스 역할에 대한 액세스 검토를 시작하는 방법을 알아봅니다.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.component: pim
ms.date: 04/02/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 9a35d32d89931a03b33f232ba4f79226fc3f57e5
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/29/2018
ms.locfileid: "43189177"
---
# <a name="start-an-access-review-for-azure-resource-roles-in-pim"></a>PIM에서 Azure 리소스 역할에 대한 액세스 검토 시작
사용자가 더 이상 필요 없는 권한 있는 액세스를 가진 경우 "오래된" 역할 할당이 됩니다. 오래된 역할 할당과 관련된 위험을 줄이기 위해 권한 있는 역할 관리자는 주기적으로 역할을 검토해야 합니다. 이 문서에서는 Azure 리소스에 대한 PIM(Privileged Identity Management)의 액세스 검토를 시작하는 방법을 다룹니다.

PIM 응용 프로그램 기본 페이지에서 다음 위치로 이동합니다.

* **액세스 검토** > **추가**

![액세스 검토 추가](media/azure-pim-resource-rbac/rbac-access-review-home.png)

**추가** 단추를 선택하면 **액세스 검토 만들기** 블레이드가 나타납니다. 이 블레이드에서 이름 및 시간 제한을 사용해 검토를 구성하고, 검토할 역할을 선택하고, 검토를 수행할 사람을 결정합니다.

![액세스 검토 만들기](media/azure-pim-resource-rbac/rbac-create-access-review.png)

### <a name="configure-the-review"></a>검토 구성
액세스 검토를 만들려면 먼저 이름을 지정한 다음, 시작 및 종료 날짜를 설정합니다.

![검토 구성 - 스크린 샷](media/azure-pim-resource-rbac/rbac-access-review-setting-1.png)

사용자가 완료할 수 있도록 검토 기간이 충분히 긴지 확인해야 합니다. 검토를 종료 날짜 전에 마친다면 언제든지 검토를 일찍 중지할 수 있습니다.

### <a name="choose-a-role-to-review"></a>검토할 역할 선택
각 검토는 오직 하나의 역할에 집중합니다. 특정 역할 블레이드에서 액세스 검토를 시작하지 않는 경우 지금 역할을 선택해야 합니다.

1. **역할 멤버 자격 검토**로 이동
   
    ![역할 멤버 자격 검토 - 스크린샷](media/azure-pim-resource-rbac/rbac-access-review-setting-2.png)
2. 목록에서 역할 하나를 선택 합니다.

### <a name="decide-who-will-perform-the-review"></a>검토를 수행할 사용자를 결정합니다.
검토를 수행하는 데 세 가지 옵션이 있습니다. 검토를 다른 사람에게 완료하도록 할당할 수도 있고, 관리자가 수행할 수도 있고, 각 사용자가 자신의 액세스를 직접 검토할 수도 있습니다.

1. 옵션 중 하나를 선택하십시오.
   
   * **선택한 사용자**: 액세스가 필요한 사용자를 알 수 없는 경우 이 옵션을 사용합니다. 이 옵션을 사용하여 리소스 소유자 또는 관리자 그룹에게 검토를 완료하도록 할당할 수 있습니다.
   * **할당됨(자체)**: 사용자에게 자신의 역할 할당을 검토하게 하려면 이 옵션을 사용합니다.
   
2. **검토자 선택**으로 이동합니다.
   
    ![검토자 선택 - 스크린 샷](media/azure-pim-resource-rbac/rbac-access-review-setting-3.png)

### <a name="start-the-review"></a>검토를 시작합니다.
마지막으로, 사용자에게 액세스를 승인한 이유를 입력하라고 요구할 수 있습니다. 필요한 경우 검토에 대한 설명을 추가합니다. 그리고 **시작**을 선택합니다.

사용자에게 대기 중인 액세스 검토가 있다는 사실을 알려주고, [액세스 검토를 수행하는 방법](pim-resource-roles-perform-access-review.md)을 보여줍니다.

## <a name="manage-the-access-review"></a>액세스 검토 관리
PIM Azure 리소스 대시보드에서 검토자가 검토를 얼마나 완료했는지 진행률을 추적할 수 있습니다. [검토가 완료될 때까지](pim-resource-roles-complete-access-review.md) 디렉터리에서 어떤 액세스 권한도 변경되지 않습니다.

검토 기간이 끝날 때까지는 사용자에게 검토를 완료하거나, 또는 액세스 검토 섹션에서 검토를 일찍 중지하도록 알릴 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [PIM에서 Azure 리소스 역할에 대한 액세스 검토 완료](pim-resource-roles-complete-access-review.md)
- [PIM에서 Azure 리소스 역할에 대한 액세스 검토 수행](pim-resource-roles-perform-access-review.md)
- [PIM에서 Azure AD 디렉터리 역할에 대한 액세스 검토 시작](pim-how-to-start-security-review.md)
