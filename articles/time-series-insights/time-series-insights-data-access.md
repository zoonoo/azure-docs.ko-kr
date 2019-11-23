---
title: Configure security to grant data access - Azure Time Series Insights Preview | Microsoft Docs
description: Learn how to configure security, permissions, and manage data access policies in your Azure Time Series Insights Preview environment.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 11/20/2019
ms.custom: seodec18
ms.openlocfilehash: b79ca1d93baf1941d5de8db0c314f9cd21e51056
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74328262"
---
# <a name="grant-data-access-to-an-environment"></a>환경에 대한 데이터 액세스 권한 부여

이 문서에서는 두 가지 유형의 Azure Time Series Insights 미리 보기 액세스 정책에 대해 설명합니다.

> [!TIP]
> Read [Authentication and Authorization](time-series-insights-authentication-and-authorization.md) for Azure Active Directory app registration steps.

## <a name="sign-in-to-time-series-insights"></a>Sign in to Time Series Insights

1. [Azure portal](https://portal.azure.com/)에 로그인합니다.
1. Time Series Insights 환경을 찾습니다. **검색** 상자에 `Time Series`를 입력합니다. 검색 결과에서 **시계열 환경**을 선택합니다.
1. 목록에서 Time Series Insights 환경을 선택합니다.

## <a name="grant-data-access"></a>데이터 액세스 권한 부여

다음 단계에 따라 사용자 계정에 대해 데이터 액세스 권한을 부여합니다.

1. **데이터 액세스 정책**을 선택한 다음, **+ 추가**를 선택합니다.

    [![Select and add a Data Access Policy](media/data-access/data-access-select-add-button.png)](media/data-access/data-access-select-add-button.png#lightbox)

1. **사용자 선택**을 선택합니다. 사용자 이름 또는 메일 주소를 검색하여 추가할 사용자를 찾습니다. Select **Select** to confirm the selection.

    [![Select a user to add](media/data-access/data-access-select-user-to-confirm.png)](media/data-access/data-access-select-user-to-confirm.png#lightbox)

1. **역할 선택**을 선택합니다. 사용자에 대한 적절한 액세스 역할을 선택합니다.

    * 사용자가 참조 데이터를 변경하고 저장된 쿼리 및 큐브 뷰를 환경의 다른 사용자와 공유할 수 있도록 하려면 **참가자**를 선택합니다.

    * 또는 사용자가 환경의 데이터를 쿼리하고 공유되지 않는 개인 쿼리를 환경에 저장할 수 있게 허용하려면 **읽기 권한자**를 선택합니다.

   **확인**을 선택하여 역할 선택을 확인합니다.

    [![Confirm the selected role](media/data-access/data-access-select-a-role.png)](media/data-access/data-access-select-a-role.png#lightbox)

1. **사용자 역할 선택** 페이지에서 **확인**을 선택합니다.

    [![Select OK on the Select User Role page](media/data-access/data-access-confirm-user-and-role.png)](media/data-access/data-access-confirm-user-and-role.png#lightbox)

1. **데이터 액세스 정책** 페이지에 사용자 및 각 사용자의 역할이 표시되는지 확인합니다.

    [![Verify the correct users and roles](media/data-access/data-access-verify-and-confirm-assignments.png)](media/data-access/data-access-verify-and-confirm-assignments.png#lightbox)

## <a name="provide-guest-access-from-another-azure-ad-tenant"></a>Provide guest access from another Azure AD tenant

The `Guest` role isn’t a management role. 특정 테넌트에서 다른 테넌트로 초대된 계정에 사용되는 용어입니다. 게스트 계정을 테넌트의 디렉터리에 초대한 후에는 다른 계정과 동일한 액세스 제어가 적용될 수 있습니다. 액세스 제어(IAM) 블레이드를 사용하여 Time Series Insights 환경에 대한 관리 액세스 권한을 부여할 수 있습니다. 또는 데이터 액세스 정책 블레이드를 통해 환경의 데이터에 대한 액세스 권한을 부여할 수 있습니다. Azure AD(Azure Active Directory) 테넌트 게스트 액세스에 대한 자세한 내용은 [Azure Portal에서 Azure Active Directory B2B 협업 사용자 추가](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator)를 참조하세요.

다른 테넌트의 Azure AD 사용자에게 Time Series Insights 환경에 대한 게스트 액세스 권한을 부여하려면 다음 단계를 수행합니다.

1. **데이터 액세스 정책**을 선택한 다음, **+ 초대**를 선택합니다.

    [![Select Data Access Polices, then + Invite](media/data-access/data-access-invite-another-aad-tenant.png)](media/data-access/data-access-invite-another-aad-tenant.png#lightbox)

1. 초대할 사용자의 메일 주소를 입력합니다. 이 메일 주소는 Azure AD와 연결되어야 합니다. 필요한 경우 초대에 개인 메시지를 포함할 수 있습니다.

    [![Enter the email address to find the selected user](media/data-access/data-access-invite-guest-by-email.png)](media/data-access/data-access-invite-guest-by-email.png#lightbox)

1. 화면에 표시되는 확인 거품을 찾습니다.

    [![Look for the confirmation bubble to appear](media/data-access/data-access-confirmation-bubble.png)](media/data-access/data-access-confirmation-bubble.png#lightbox)

1. **사용자 선택**을 선택합니다. 초대한 게스트 사용자의 메일 주소를 검색하여 추가할 사용자를 찾습니다. Then, **Select** to confirm the selection.

    [![Select the user and confirm the selection](media/data-access/data-access-select-invited-person-confirmation.png)](media/data-access/data-access-select-invited-person-confirmation.png#lightbox)

1. **역할 선택**을 선택합니다. 게스트 사용자의 적절한 액세스 역할을 선택합니다.

    * 사용자가 참조 데이터를 변경하고 저장된 쿼리 및 큐브 뷰를 환경의 다른 사용자와 공유할 수 있도록 하려면 **참가자**를 선택합니다.

    * 또는 사용자가 환경의 데이터를 쿼리하고 공유되지 않는 개인 쿼리를 환경에 저장할 수 있게 허용하려면 **읽기 권한자**를 선택합니다.

   **확인**을 선택하여 역할 선택을 확인합니다.

    [![Confirm the role choice](media/data-access/data-access-select-ok-and-confirm.png)](media/data-access/data-access-select-ok-and-confirm.png#lightbox)

1. **사용자 역할 선택** 페이지에서 **확인**을 선택합니다.

1. **데이터 액세스 정책** 페이지에 게스트 사용자 및 각 게스트 사용자의 역할이 표시되는지 확인합니다.

    [![Verify that users and roles are correctly assigned](media/data-access/data-access-confirm-invited-users-and-roles.png)](media/data-access/data-access-confirm-invited-users-and-roles.png#lightbox)

1. Now, the guest user will receive an invitation email at the email address specified above. The guest user will select **Get Started** to confirm their acceptance and connect to Azure Cloud.

    [![Guest selects Get Started to accept](media/data-access/data-access-email-invitation.png)](media/data-access/data-access-email-invitation.png#lightbox)

1. After selecting **Get Started** the guest user will be presented with a permissions box associated with the administrator's organization. Upon granting permission by selecting **Accept**, they will be signed in.

    [![Guest reviews permissions and accepts](media/data-access/data-access-grant-permission-sign-in.png)](media/data-access/data-access-grant-permission-sign-in.png#lightbox)

1. The administrator [shares the environment URL](time-series-insights-parameterized-urls.md) with their guest.

1. After the guest user is signed in to the email address you used to invite them, and they accept the invitation, they will be directed to Azure portal. 

1. The guest can now access the shared environment using the environment URL provided by the administrator. They can enter that URL into their web browser for immediate access.

1. The guest user will see the administrator's tenant by selecting their profile icon in the upper-right corner of the Time Series explorer.

    [![Avatar selection on insights.azure.com](media/data-access/data-access-select-tenant-and-instance.png)](media/data-access/data-access-select-tenant-and-instance.png#lightbox)


    After the guest user selects the administrator's tenant, they will have the ability to select the shared Time Series Insights environment. 
    
    They now have all the capabilities associated with the role that you provided them with in **step 5**.

    [![Guest user selects your Azure tenant from drop-down](media/data-access/data-access-all-capabilities.png)](media/data-access/data-access-all-capabilities.png#lightbox)

## <a name="next-steps"></a>다음 단계

* Time Series Insights 환경에 [Azure Event Hubs 이벤트 원본을 추가하는 방법](./time-series-insights-how-to-add-an-event-source-eventhub.md)을 알아봅니다.

* [이벤트 원본으로 이벤트를 전송](./time-series-insights-send-events.md)합니다.

* [Time Series Insights 미리 보기 탐색기에서 사용자 환경을 봅니다](./time-series-insights-update-explorer.md).
