---
title: Azure AD 자격 관리에서 요청 프로세스 및 전자 메일 알림-Azure Active Directory
description: 액세스 패키지에 대 한 요청 프로세스와 Azure Active Directory 자격 관리에서 전자 메일 알림이 전송 되는 시기에 대해 알아봅니다.
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
ms.date: 10/30/2019
ms.author: ajburnle
ms.reviewer: mamkumar
ms.collection: M365-identity-device-management
ms.openlocfilehash: e34d2c69cc808552a3b0c604804f3cd2597b379b
ms.sourcegitcommit: fa5ce8924930f56bcac17f6c2a359c1a5b9660c9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/31/2019
ms.locfileid: "73199973"
---
# <a name="request-process-and-email-notifications-in-azure-ad-entitlement-management"></a>Azure AD 자격 관리에서 요청 프로세스 및 전자 메일 알림

사용자가 액세스 패키지에 요청을 제출 하면 프로세스가 해당 액세스 요청을 전달 하기 시작 합니다. Azure AD 자격 관리는 프로세스 중에 키 이벤트가 발생 하는 경우 승인자 및 요청자에 게 전자 메일 알림을 보냅니다. 이 문서에서는 요청 프로세스 및 전송 되는 전자 메일 알림을 설명 합니다.

## <a name="request-process"></a>요청 프로세스

액세스 패키지에 액세스 해야 하는 사용자가 액세스 요청을 제출할 수 있습니다. 정책의 구성에 따라 요청에 승인이 필요할 수 있습니다. 요청이 승인 되 면 프로세스에서 액세스 패키지의 각 리소스에 대 한 사용자 액세스 권한을 할당 하기 시작 합니다. 다음 다이어그램에서는 프로세스의 개요와 다양 한 상태를 보여 줍니다.

![승인 프로세스 다이어그램](./media/entitlement-management-process/request-process.png)

| 상태 | 설명 |
| --- | --- |
| 보내는 | 사용자가 요청을 제출 합니다. |
| 승인 보류 중 | 액세스 패키지에 대 한 정책에 승인이 필요한 경우 요청이 승인 보류 중으로 이동 합니다. |
| 만료됨 | 승인 요청 시간 제한 내에서 요청을 승인 하는 승인자가 없으면 요청이 만료 됩니다. 다시 시도 하려면 사용자가 자신의 요청을 다시 제출 해야 합니다. |
| 거부 | 승인자가 요청을 거부 합니다. |
| 승인됨 | 승인자가 요청을 승인 합니다. |
| 배달 | 액세스 패키지의 모든 리소스에 대 한 액세스 권한이 사용자에 게 할당 **되지** 않았습니다. 외부 사용자 인 경우에는 사용자가 아직 리소스 디렉터리에 액세스 하 여 동의 확인 프롬프트를 수락 하지 않았을 수 있습니다. |
| 배달됨 | 액세스 패키지의 모든 리소스에 대 한 액세스 권한이 사용자에 게 할당 되었습니다. |
| 액세스 확장 | 정책에서 확장이 허용 되 면 사용자가 할당을 확장 한 것입니다. |
| 액세스가 만료 됨 | 액세스 패키지에 대 한 사용자의 액세스가 만료 되었습니다. 액세스를 다시 받으려면 사용자가 요청을 제출 해야 합니다. |

## <a name="email-notifications"></a>전자 메일 알림

승인자 인 경우 액세스 요청을 승인 해야 하 고 액세스 요청이 완료 되 면 전자 메일 알림을 보냅니다. 요청자는 요청 상태를 나타내는 전자 메일 알림을 받게 됩니다.

다음 다이어그램에서는 이러한 전자 메일 알림이 승인자 또는 요청자에 게 전송 되는 시기를 보여 줍니다. [전자 메일 알림 표](entitlement-management-process.md#email-notifications-table) 를 참조 하 여 다이어그램에 표시 된 전자 메일 알림에 해당 하는 숫자를 찾습니다.

### <a name="primary-approvers-and-alternate-approvers"></a>기본 승인자 및 대체 승인자
다음 다이어그램에서는 기본 승인자 및 대체 승인자의 경험과 요청 프로세스 중에 수신 하는 전자 메일 알림을 보여 줍니다.

![기본 및 대체 승인자 프로세스 흐름](./media/entitlement-management-process/primary-approvers-and-alternate-with-escalation-flow.png)

### <a name="requestors"></a>요청자
다음 다이어그램에서는 요청 과정에서 받은 요청자 및 전자 메일 알림의 환경을 보여 줍니다.

![요청자 프로세스 흐름](./media/entitlement-management-process/requestor-approval-and-expiration-request-flow.png)

### <a name="email-notifications-table"></a>전자 메일 알림 테이블
다음 표에서는 이러한 각 전자 메일 알림에 대 한 자세한 정보를 제공 합니다. 이러한 전자 메일을 관리 하기 위해 규칙을 사용할 수 있습니다. 예를 들어 Outlook에서 제목에이 테이블의 단어가 포함 된 경우 메일을 폴더로 이동 하는 규칙을 만들 수 있습니다.

| # | 전자 메일 제목 | 전송 될 때 | 보낸 사람 |
| --- | --- | --- | --- |
| 1 | 작업 필요: *[date]* 로 전달 된 요청을 승인 하거나 거부 합니다. | 이 전자 메일은 작업을 수행할 수 있도록 요청이 에스컬레이션 된 후 1 단계 대체 승인자에 게 전송 됩니다. | 1 단계 대체 승인자 |
| 2 | 작업 필요: *[date]* 의 요청을 승인 또는 거부 합니다. | 에스컬레이션을 사용 하지 않도록 설정한 경우 작업을 수행 하려면이 전자 메일을 1 단계 기본 승인자에 게 보냅니다. | 1 단계 기본 승인자 |
| 3 | 미리 알림: [ *요청자]* 에 대해 *[date]* 요청을 승인 하거나 거부 합니다. | 이 미리 알림 전자 메일은 단계 1 기본 승인자에 게 전송 됩니다. 에스컬레이션을 사용 하지 않도록 설정 하는 경우 작업을 수행 하려면 아직 작업을 수행 하지 않은 경우에만 수행 합니다. | 1 단계 기본 승인자 |
| 4 | [ *Date]* 의 *[time]* 에 의해 요청을 승인 하거나 거부 합니다. | 이 전자 메일은 작업을 수행 하기 위해 1 단계 기본 승인자 (에스컬레이션을 사용 하도록 설정 된 경우)로 전송 됩니다. | 1 단계 기본 승인자 |
| 5 | 작업 필요 미리 알림: *[요청자]* 에 대해 *[date]* 요청을 승인 하거나 거부 합니다. | 이 미리 알림 메일은 단계 1 기본 승인자에 게 전송 됩니다. 에스컬레이션을 사용 하도록 설정한 경우 작업을 수행 하려면 아직 작업을 수행 하지 않은 경우에만 작업을 수행 합니다. | 1 단계 기본 승인자 |
| 6 | *[Access_package]* 에 대 한 요청이 만료 되었습니다. | 이 전자 메일은 요청이 만료 된 후 단일 단계 또는 여러 단계 요청에 대 한 단계 1 기본 승인자 및/또는 1 단계 대체 승인자에 게 전송 됩니다. | 1 단계 기본 승인자, 1 단계 대체 승인자 |
| 7 | [ *요청자]* 에 대해 승인 된 요청 *[access_package]* | 요청이 완료 되 면이 메일은 1 단계 기본 승인자 및/또는 1 단계 대체 승인자에 게 전송 됩니다. | 1 단계 기본 승인자, 1 단계 대체 승인자 |
| 8 | [ *요청자]* 에 대해 승인 된 요청 *[access_package]* | 이 전자 메일은 단계 1이 승인 된 경우에만 2 단계 요청에 대 한 1 단계 기본 승인자 및/또는 1 단계 대체 승인자에 게 전송 됩니다. | 1 단계 기본 승인자, 1 단계 대체 승인자 |
| 9 | 요청 거부 *[access_package]* | 요청이 거부 된 경우에만이 전자 메일을 요청자에 게 보냅니다. | 요청 |
| 10 | *[Access_package]* 에 대 한 요청이 만료 되었습니다. | 이 전자 메일은 요청이 만료 된 후 1 단계 또는 여러 단계 요청에 대 한 단계 1의 끝에서 요청자에 게 전송 됩니다. | 요청 |
| 18 | 이제 *[access_package]* 에 액세스할 수 있습니다. | 이 전자 메일은 최종 사용자에 게 액세스 권한을 사용 하 여 시작 하기 위해 전송 됩니다. | 요청 |
| 19 | [ *Date]* 로 *[access_package]* 에 대 한 액세스 확장 | 이 전자 메일은 액세스 권한이 만료 되기 전에 최종 사용자에 게 전송 됩니다. | 요청 |
| 20 | *[Access_package]* 에 대 한 액세스가 종료 되었습니다. | 이 전자 메일은 액세스 권한이 만료 된 후 최종 사용자에 게 전송 됩니다. | 요청 |

### <a name="access-request-emails"></a>액세스 요청 전자 메일

요청 자가 승인을 요구 하도록 구성 된 액세스 패키지에 대 한 액세스 요청을 제출 하는 경우 정책에 추가 된 모든 승인자는 요청에 대 한 세부 정보와 함께 전자 메일 알림을 받게 됩니다. 세부 정보에는 요청자의 이름, 조직, 액세스 시작 및 종료 날짜 (제공 된 경우), 비즈니스 근거, 요청이 제출 된 시간 및 요청이 만료 되는 시간 등이 포함 됩니다.

전자 메일에는 클릭 하 여 Myaccess로 이동 하 여 액세스 요청을 승인 또는 거부 하는 링크 승인자가 포함 됩니다. 요청자에 게 액세스 요청을 제출 하면 승인자에 게 전송 되는 샘플 메일 알림은 다음과 같습니다.

![패키지 전자 메일에 액세스 하는 요청 승인](./media/entitlement-management-shared/approver-request-email.png)

또한 기본 승인자는 작업을 수행 하 고 요청에 대 한 결정을 내리는 미리 알림이 포함 된 전자 메일 알림을 보냅니다. 다음은 작업을 수행 하도록 알리기 위해 기본 승인자가 받는 알림의 샘플 전자 메일입니다.

![미리 알림 액세스 요청 전자 메일](./media/entitlement-management-process/approver-access-request-reminder-email.png)

### <a name="alternate-approver-request-emails"></a>대체 승인자 요청 전자 메일

대체 승인자에 대 한 전달이 사용 하도록 설정 되어 있으면 전달 정책에 따라 요청이 아직 보류 중인 경우 요청이 전달 됩니다. 대체 승인자가 요청을 승인 하거나 거부 하는 알림 전자 메일을 받게 됩니다. 다음은 대체 승인자가 받는 알림의 샘플 전자 메일입니다.

![대체 승인자 요청 전자 메일](./media/entitlement-management-process/alternate-approver-email-fwd-request.png)

두 가지 모두 기본 승인자와 대체 승인자가 요청을 승인 하거나 거부할 수 있습니다.

### <a name="approved-or-denied-emails"></a>승인 또는 거부 된 전자 메일

액세스 요청이 승인 되 고 액세스할 수 있는 경우 또는 액세스 요청이 거부 된 경우 요청자에 게 알립니다. 승인자가 요청자에 의해 전송 된 액세스 요청을 받으면 액세스 요청을 승인 하거나 거부할 수 있습니다. 승인자는 의사 결정을 위해 비즈니스 근거를 추가 해야 합니다. 다음은 요청이 승인 된 후 기본 또는 대체 승인자에 게 전송 되는 샘플 전자 메일입니다.

![액세스 요청 전자 메일 검토](./media/entitlement-management-process/approver-request-email-approved.png)

액세스 요청이 승인 되 고 해당 액세스 권한이 프로 비전 되 면 요청자에 게 액세스 패키지에 대 한 액세스 권한이 있는 전자 메일 알림이 전송 됩니다. 액세스 패키지에 대 한 액세스 권한이 부여 된 경우 요청자에 게 전송 되는 샘플 메일 알림은 다음과 같습니다.

![만료 된 액세스 요청 전자 메일](./media/entitlement-management-process/requestor-email-approved.png)

액세스 요청이 거부 되 면 요청자에 게 전자 메일 알림이 전송 됩니다. 액세스 요청이 거부 된 경우 요청자에 게 전송 되는 샘플 메일 알림은 다음과 같습니다.

![요청자 요청 거부 된 전자 메일](./media/entitlement-management-process/requestor-email-denied.png)

### <a name="expired-access-request-emails"></a>만료 된 액세스 요청 전자 메일

승인자가 요청을 승인 하거나 거부 하지 않은 경우 액세스 요청이 만료 될 수 있습니다. 

요청이 구성 된 만료 날짜에 도달 하 여 만료 되 면 승인자가 더 이상 승인 하거나 거부할 수 없습니다. 다음은 모든 기본 승인자와 대체 승인자에 게 전송 된 알림의 샘플 전자 메일입니다.

 ![승인자 만료 된 액세스 요청 전자 메일](./media/entitlement-management-process/approver-request-email-expired.png)

 또한 전자 메일 알림은 요청자에 게 전송 되어 액세스 요청이 만료 되었음을 알리고 액세스 요청을 다시 제출 해야 한다는 것을 알립니다. 액세스 요청이 만료 된 경우 요청자에 게 전송 되는 샘플 메일 알림은 다음과 같습니다.

![요청자 만료 된 액세스 요청 전자 메일](./media/entitlement-management-process/requestor-email-request-expired.png)

## <a name="next-steps"></a>다음 단계

- [액세스 패키지에 대 한 액세스 요청](entitlement-management-request-access.md)
- [액세스 요청 승인 또는 거부](entitlement-management-request-approve.md)
