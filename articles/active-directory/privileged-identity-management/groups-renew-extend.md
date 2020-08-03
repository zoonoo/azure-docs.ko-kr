---
title: Privileged Identity Management에서 구성원 할당의 만료 된 그룹 소유자 갱신 | Azure AD | Microsoft Docs
description: Azure AD Privileged Identity Management (PIM)에서 역할 할당 가능 그룹 할당을 확장 하거나 갱신 하는 방법에 대해 알아봅니다.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: pim
ms.date: 06/30/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0cfbb0be22dee4550050d6af10314f3a3bb1f583
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/31/2020
ms.locfileid: "87505992"
---
# <a name="extend-or-renew-privileged-access-group-assignments-preview-in-privileged-identity-management"></a>Privileged Identity Management에서 권한 있는 액세스 그룹 할당 (미리 보기) 확장 또는 갱신

Azure Active Directory (Azure AD) Privileged Identity Management (PIM)는 권한 있는 액세스 그룹의 액세스 및 할당 수명 주기를 관리 하는 컨트롤을 제공 합니다. 관리자는 시작 및 종료 날짜-시간 속성을 사용 하 여 역할을 할당할 수 있습니다. 할당 끝에 도달 하면 Privileged Identity Management는 영향을 받는 사용자 또는 그룹에 전자 메일 알림을 보냅니다. 또한 리소스 관리자에게도 적절한 액세스가 유지되도록 메일 알림을 보냅니다. 액세스가 연장되지 않더라도 할당이 갱신되어 만료된 상태에서 최대 30일 동안 표시됩니다.

## <a name="who-can-extend-and-renew"></a>확장 및 갱신할 수 있는 사람

리소스의 관리자만 권한 있는 액세스 그룹 할당을 확장 하거나 갱신할 수 있습니다. 영향을 받는 사용자 또는 그룹은 만료 될 할당을 연장 하도록 요청 하 고 이미 만료 된 할당을 갱신 하도록 요청할 수 있습니다.

## <a name="when-notifications-are-sent"></a>알림이 전송 되는 경우

Privileged Identity Management는 관리자 및 만료 되는 권한 있는 액세스 그룹 할당의 영향을 받는 사용자에 게 전자 메일 알림을 보냅니다.

- 만료 전 14 일 이내
- 만료 전 1 일
- 할당이 만료 되는 경우

관리자는 사용자 또는 그룹이 만료 또는 만료 된 할당을 확장 또는 갱신 하도록 요청할 때 알림을 받습니다. 관리자가 요청을 확인 하면 모든 관리자와 요청 하는 사용자에 게 승인 또는 거부 알림이 표시 됩니다.

## <a name="extend-group-assignments"></a>그룹 할당 확장

다음 단계는 그룹 할당의 확장 또는 갱신을 요청, 확인, 관리 하는 프로세스를 간략하게 설명 합니다.

### <a name="self-extend-expiring-assignments"></a>자동 연장 할당 만료

권한 있는 액세스 그룹에 할당 된 사용자는 그룹에 대 한 **할당** 페이지의 **적격** 또는 **활성** 탭에서 만료 된 그룹 할당을 직접 확장할 수 있습니다. 사용자 또는 그룹은 다음 14 일 후에 만료 되는 적격 및 활성 할당을 연장 하도록 요청할 수 있습니다.

![작업 열을 사용 하 여 적합 한 자격을 나열 하는 내 역할 페이지](media/groups-renew-extend/self-extend-group-assignment.png)

할당 종료 날짜-시간이 14 일 이내 이면 **확장** 명령을 사용할 수 있습니다. 그룹 할당의 확장을 요청 하려면 **확장** 을 선택 하 여 요청 양식을 엽니다.

![이유 상자 및 세부 정보를 사용 하 여 그룹 할당 창 확장](media/groups-renew-extend/extend-request-details-group-assignment.png)

>[!NOTE]
>연장이 필요한 이유와 부여되어야 하는 연장 기간(이 정보가 있는 경우)에 대한 세부 정보를 포함시키는 것이 좋습니다.

잠시 후 관리자는 확장 요청을 검토 하도록 요청 하는 전자 메일 알림을 받습니다. 확장 요청이 이미 제출 된 경우 Azure 알림이 포털에 표시 됩니다.

요청 상태를 보거나 취소 하려면 그룹 할당에 대 한 **보류 중인 요청** 페이지를 엽니다.

![권한 있는 액세스 그룹 할당-취소 링크를 표시 하는 보류 중인 요청 페이지](media/groups-renew-extend/group-assignment-extend-cancel-request.png)

### <a name="admin-approved-extension"></a>관리 승인 된 확장

사용자 또는 그룹이 그룹 할당을 확장 하는 요청을 제출 하는 경우 관리자는 원래 할당의 세부 정보 및 요청에 대 한 이유를 포함 하는 전자 메일 알림을 받습니다. 알림에는 관리자가 요청을 승인하거나 거부할 수 있는 직접 링크가 포함됩니다.

관리자는 전자 메일의 링크를 사용 하는 것 외에도 Privileged Identity Management 관리 포털로 이동 하 고 왼쪽 창에서 **요청 승인** 을 선택 하 여 요청을 승인 하거나 거부할 수 있습니다.

![권한 있는 액세스 그룹 할당-승인 또는 거부할 요청 및 링크를 나열 하는 요청 승인 페이지](media/groups-renew-extend/group-assignment-extend-admin-approve.png)

관리자가 **승인** 또는 **거부**를 선택 하면 요청의 세부 정보가 감사 로그에 대 한 비즈니스 근거를 제공 하는 필드와 함께 표시 됩니다.

![요청자 이유, 할당 유형, 시작 시간, 종료 시간 및 이유를 사용 하 여 그룹 할당 요청 승인](media/groups-renew-extend/group-assignment-extend-admin-approve-reason.png)

그룹 할당 확장 요청을 승인할 때 리소스 관리자는 새 시작 날짜, 종료 날짜 및 할당 유형을 선택할 수 있습니다. 관리자가 특정 작업을 완료하도록 제한된 액세스를 제공하려는 경우 할당 유형 변경이 필요할 수 있습니다(예: 하루). 이 예제에서는 관리자가 **적격**에서 **활성**으로 할당을 변경할 수 있습니다. 즉, 활성화를 요구하지 않고도 요청자에게 액세스 권한을 제공할 수 있습니다.

### <a name="admin-initiated-extension"></a>관리자가 시작한 확장

그룹에 할당 된 사용자가 그룹 할당에 대 한 확장을 요청 하지 않는 경우 관리자는 사용자를 대신 하 여 할당을 확장할 수 있습니다. 그룹 할당의 관리 확장에는 승인이 필요 하지 않지만 할당이 확장 된 후에는 다른 모든 관리자에 게 알림이 전송 됩니다.

그룹 할당을 확장 하려면 Privileged Identity Management의 할당 뷰로 이동 합니다. 확장이 필요한 할당을 찾습니다. 그런 다음 작업 열에서 **연장**을 선택합니다.

![확장할 링크를 사용 하 여 적격 그룹 할당을 나열 하는 할당 페이지](media/groups-renew-extend/group-assignment-extend-admin-approve.png)

## <a name="renew-group-assignments"></a>그룹 할당 갱신

확장 요청 프로세스와 개념적으로 유사 하지만 만료 된 그룹 할당을 갱신 하는 프로세스는 다릅니다. 다음 단계를 사용 하 여 할당 및 관리자는 필요한 경우 만료 된 할당에 대 한 액세스를 갱신할 수 있습니다.

### <a name="self-renew"></a>자동 갱신

더 이상 리소스에 액세스할 수 없는 사용자는 최대 30 일의 만료 된 할당 기록을 액세스할 수 있습니다. 이렇게 하려면 왼쪽 창에서 **내 역할** 로 이동한 다음 **만료 된 할당** 탭을 선택 합니다.

![내 역할 페이지-만료 된 할당 탭](media/groups-renew-extend/groups-renew-from-my-roles.png)

표시 되는 할당 목록은 기본적으로 **적격 할당**으로 설정 됩니다. 드롭다운 메뉴를 사용 하 여 적격 및 활성 할당 간을 전환할 수 있습니다.

목록에서 그룹 할당에 대 한 갱신을 요청 하려면 **갱신** 작업을 선택 합니다. 그런 다음 요청 이유를 제공합니다. 리소스 관리자가 승인 또는 거부를 결정 하는 데 도움이 될 수 있는 추가 컨텍스트나 비즈니스 근거 외에도 기간을 제공 하는 것이 유용 합니다.

![이유 상자를 보여 주는 그룹 할당 갱신 창](media/groups-renew-extend/groups-renew-request-form.png)

요청이 제출 된 후에는 리소스 관리자에 게 그룹 할당을 갱신 하는 보류 중인 요청에 대 한 알림이 표시 됩니다.

### <a name="admin-approves"></a>관리자 승인

리소스 관리자는 전자 메일 알림의 링크를 사용 하거나 Azure Portal에서 Privileged Identity Management에 액세스 하 고 왼쪽 창에서 **요청 승인** 을 선택 하 여 갱신 요청에 액세스할 수 있습니다.

관리자가 **승인** 또는 **거부**를 선택 하면 요청의 세부 정보가 감사 로그에 대 한 비즈니스 근거를 제공 하는 필드와 함께 표시 됩니다.

그룹 할당을 갱신 하는 요청을 승인할 때 리소스 관리자는 새 시작 날짜, 종료 날짜 및 할당 형식을 입력 해야 합니다.

## <a name="next-steps"></a>다음 단계

- [Privileged Identity Management에서 권한 있는 액세스 그룹 할당에 대 한 요청 승인 또는 거부](groups-approval-workflow.md)
- [Privileged Identity Management에서 권한 있는 액세스 그룹 설정 구성](groups-role-settings.md)
