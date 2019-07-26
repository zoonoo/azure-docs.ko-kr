---
title: Azure AD 자격 관리 (미리 보기)에서 요청 프로세스 및 전자 메일 알림-Azure Active Directory
description: 액세스 패키지에 대 한 요청 프로세스와 Azure Active Directory 자격 관리 (미리 보기)에서 전자 메일 알림이 전송 되는 시기에 대해 알아봅니다.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: mamtakumar
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 05/30/2019
ms.author: ajburnle
ms.reviewer: mamkumar
ms.collection: M365-identity-device-management
ms.openlocfilehash: 25ad17f4c4d338e1d0b8118c1a172e0aaad7118e
ms.sourcegitcommit: bafb70af41ad1326adf3b7f8db50493e20a64926
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/25/2019
ms.locfileid: "68489094"
---
# <a name="request-process-and-email-notifications-in-azure-ad-entitlement-management-preview"></a>Azure AD 자격 관리 (미리 보기)의 요청 프로세스 및 전자 메일 알림

> [!IMPORTANT]
> Azure AD(Azure Active Directory) 권한 관리는 현재 공개 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

사용자가 액세스 패키지에 요청을 제출 하면 해당 요청을 배달 하는 프로세스가 시작 됩니다. 또한 Azure AD 자격 관리는 프로세스 중에 키 이벤트가 발생 하는 경우 승인자 및 요청자에 게 전자 메일 알림을 보냅니다.

이 문서에서는 요청 프로세스 및 전송 되는 전자 메일 알림을 설명 합니다.

## <a name="request-process"></a>요청 프로세스

액세스 패키지에 액세스 해야 하는 사용자가 액세스 요청을 제출할 수 있습니다. 정책의 구성에 따라 요청에 승인이 필요할 수 있습니다. 요청이 승인 되 면 프로세스에서 액세스 패키지의 각 리소스에 대 한 사용자 액세스 권한을 할당 하기 시작 합니다. 다음 다이어그램에서는 프로세스의 개요와 다양 한 상태를 보여 줍니다.

![승인 프로세스 다이어그램](./media/entitlement-management-process/request-process.png)

| State | Description |
| --- | --- |
| 제출됨 | 사용자가 요청을 제출 합니다. |
| 승인 보류 중 | 액세스 패키지에 대 한 정책에 승인이 필요한 경우 요청이 승인 보류 중으로 이동 합니다. |
| 만료됨 | 승인 요청 시간 제한 내에서 요청을 승인 하는 승인자가 없으면 요청이 만료 됩니다. 다시 시도 하려면 사용자가 자신의 요청을 다시 제출 해야 합니다. |
| 거부됨 | 승인자가 요청을 거부 합니다. |
| 승인 | 승인자가 요청을 승인 합니다. |
| 배달 중 | 액세스 패키지의 모든 리소스에 대 한 액세스 권한이 사용자에 게 할당 **되지** 않았습니다. 외부 사용자 인 경우 사용자는 아직 리소스 디렉터리에 액세스 하지 않고 사용 권한 프롬프트를 수락 했습니다. |
| Delivered | 액세스 패키지의 모든 리소스에 대 한 액세스 권한이 사용자에 게 할당 되었습니다. |
| 액세스 확장 | 정책에서 확장이 허용 되 면 사용자가 할당을 확장 한 것입니다. |
| 액세스가 만료 됨 | 액세스 패키지에 대 한 사용자의 액세스가 만료 되었습니다. 액세스를 다시 받으려면 사용자가 요청을 제출 해야 합니다. |

## <a name="email-notifications"></a>메일 알림

승인자 인 경우 액세스 요청을 승인 해야 하 고 액세스 요청이 완료 되 면 전자 메일 알림을 보냅니다. 요청자는 요청 상태를 나타내는 전자 메일 알림을 받게 됩니다. 다음 다이어그램에서는 이러한 전자 메일 알림이 전송 되는 시기를 보여 줍니다.

![자격 관리 메일 프로세스](./media/entitlement-management-process/email-notifications.png)

다음 표에서는 이러한 각 전자 메일 알림에 대 한 자세한 정보를 제공 합니다.

| # | 전자 메일 제목 | 전송 될 때 | 보낸 사람 |
| --- | --- | --- | --- |
| 1 | 작업 필요: [ *Date]* 로 [ *요청자]* 에서 *[access package]* 로의 액세스 요청을 검토 합니다. | 요청 자가 액세스 패키지에 대 한 요청을 제출 하는 경우 | 모든 승인자 |
| 2 | 작업 필요: [ *Date]* 로 [ *요청자]* 에서 *[access package]* 로의 액세스 요청을 검토 합니다. | 승인 요청 시간 초과 전 X 일 | 모든 승인자 |
| 3 | 상태 알림: [ *요청자]* 의 액세스 요청 *[액세스 패키지]* 가 만료 되었습니다. | 승인자가 요청 기간 내에 액세스 요청을 승인 하거나 거부 하지 않는 경우 | 요청자 |
| 4 | 상태 알림: [ *요청자]* *[액세스 패키지]* 에 대 한 액세스 요청이 완료 되었습니다. | 첫 번째 승인자가 액세스 요청을 승인 하거나 거부 하는 경우 | 모든 승인자 |
| 5 | *[Access package]* 에 대 한 액세스가 거부 되었습니다. | 요청자에 게 액세스 패키지에 대 한 액세스 권한이 거부 된 경우 | 요청자 |
| 6 | 이제 *[access package]* 에 액세스할 수 있습니다.  | 요청자에 게 액세스 패키지의 모든 리소스에 대 한 액세스 권한이 부여 된 경우 | 요청자 |
| 7 | *[Access package]* 에 대 한 액세스가 X 일 후에 만료 됩니다. | 액세스 패키지에 대 한 요청자의 액세스 권한이 만료 되기까지 X 일 전 | 요청자 |
| 8 | *[Access package]* 에 대 한 액세스 권한이 만료 되었습니다. | 액세스 패키지에 대 한 요청자의 액세스 권한이 만료 되는 경우 | 요청자 |

### <a name="access-request-emails"></a>액세스 요청 전자 메일

요청 자가 승인을 요구 하도록 구성 된 액세스 패키지에 대 한 액세스 요청을 제출 하는 경우 정책에 구성 된 모든 승인자는 요청에 대 한 세부 정보와 함께 전자 메일 알림을 받습니다. 세부 정보에는 요청자의 이름, 조직, 액세스 시작 및 종료 날짜, 제공 되는 경우 비즈니스 근거, 요청이 제출 된 시간, 요청이 만료 되는 시간 등이 포함 됩니다. 전자 메일에는 승인자가 액세스 요청을 승인 하거나 거부할 수 있는 링크가 포함 되어 있습니다. 다음은 요청 자가 액세스 요청을 제출할 때 승인자에 게 전송 되는 샘플 전자 메일 알림입니다.

![액세스 요청 전자 메일 검토](./media/entitlement-management-shared/email-approve-request.png)

### <a name="approved-or-denied-emails"></a>승인 또는 거부 된 전자 메일

액세스 요청이 승인 되 고 액세스할 수 있는 경우 또는 액세스 요청이 거부 된 경우 요청자에 게 알립니다. 승인자가 요청자에 의해 전송 된 액세스 요청을 받으면 액세스 요청을 승인 하거나 거부할 수 있습니다. 승인자는 의사 결정을 위해 비즈니스 근거를 추가 해야 합니다.

액세스 요청이 승인 되 면 권한 부여 관리에서 요청자에 게 액세스 패키지의 각 리소스에 대 한 액세스 권한을 부여 하는 프로세스를 시작 합니다. 요청자에 게 액세스 패키지의 모든 리소스에 대 한 액세스 권한이 부여 된 후에는 해당 액세스 요청이 승인 되었고 이제 액세스 패키지에 액세스할 수 있음을 요청자에 게 전자 메일 알림이 전송 됩니다. 액세스 패키지에 대 한 액세스 권한이 부여 된 경우 요청자에 게 전송 되는 샘플 메일 알림은 다음과 같습니다.

액세스 요청이 거부 되 면 요청자에 게 전자 메일 알림이 전송 됩니다. 액세스 요청이 거부 될 때 요청자에 게 전송 되는 샘플 메일 알림은 다음과 같습니다.

### <a name="expired-access-request-emails"></a>만료 된 액세스 요청 전자 메일

액세스 요청이 만료 되 면 요청자에 게 알립니다. 요청 자가 액세스 요청을 제출 하면 요청 기간이 만료 된 후 요청 기간이 발생 합니다. 승인/거부 결정을 제출 하는 승인자가 없는 경우 요청은 계속 보류 중인 승인 상태로 유지 됩니다. 요청이 구성 된 만료 기간에 도달 하면 요청이 만료 되며 승인자가 더 이상 승인 하거나 거부할 수 없습니다. 이 경우 요청이 만료 된 상태가 됩니다. 만료 된 요청은 더 이상 승인 또는 거부할 수 없습니다. 액세스 요청이 만료 되었으며 액세스 요청을 다시 전송 해야 한다는 것을 요청자에 게 전자 메일 알림이 전송 됩니다. 액세스 요청이 만료 된 경우 요청자에 게 전송 되는 샘플 메일 알림은 다음과 같습니다.

![만료 된 액세스 요청 전자 메일](./media/entitlement-management-process/email-expired-access-request.png)

## <a name="next-steps"></a>다음 단계

- [액세스 패키지에 대 한 액세스 요청](entitlement-management-request-access.md)
- [액세스 요청 승인 또는 거부](entitlement-management-request-approve.md)
