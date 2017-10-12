---
title: "Azure AD 액세스 검토를 사용하여 사용자 액세스 관리 | Microsoft Docs"
description: "Azure Active Directory 액세스 검토를 사용하여 응용 프로그램에 대한 그룹 또는 할당의 멤버 자격으로 사용자 액세스를 관리하는 방법에 대해 알아보기"
services: active-directory
documentationcenter: 
author: markwahl-msft
manager: femila
editor: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/19/2017
ms.author: billmath
ms.openlocfilehash: 0459eb5cc71939202c8491f6b2714e28bd8e202d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="manage-user-access-with-azure-ad-access-reviews"></a>Azure AD 액세스 검토를 사용하여 사용자 액세스 관리

Azure Active Directory를 사용하여 사용자 각자 또는 의사 결정자가 액세스 검토에 참여하고 사용자의 액세스에 다시 인증(또는 "증명")하도록 요청하여 손쉽게 사용자가 적절한 액세스를 갖는지 확인할 수 있습니다.  검토자는 Azure AD에서 제안 사항에 따라 지속적인 액세스에 대한 각 사용자의 요구에 입력을 제공할 수 있습니다. 액세스 검토가 완료되면 더 이상 필요하지 않은 사용자에서 액세스를 변경 및 제거할 수 있습니다.

> [!NOTE]
> 게스트 사용자의 액세스만 검토하고 모든 종류의 사용자의 액세스를 검토하지 않으려는 경우 [액세스 검토로 게스트 사용자 액세스 관리](active-directory-azure-ad-controls-manage-guest-access-with-access-reviews.md)를 참조하세요.  전역 관리자와 같은 관리 역할에서 사용자의 멤버 자격을 검토하려는 경우 [Azure AD PIM에서 액세스 검토를 시작하는 방법](active-directory-privileged-identity-management-how-to-start-security-review.md)을 참조하세요. 
>
>

## <a name="prerequisites"></a>필수 조건 

액세스 검토는 EMS E5에 포함된 Azure Active Directory의 Premium P2 버전에서 사용할 수 있습니다. 자세한 내용은 [Azure Active Directory 버전](active-directory-editions.md)을 참조하세요.  검토 만들기, 액세스 검토 또는 리뷰 적용을 위해 이 기능과 상호 작용하는 각 사용자는 라이선스가 필요합니다.


## <a name="creating-and-performing-an-access-review"></a>액세스 검토 만들기 및 수행

액세스 검토 중인 검토자로 한 명 이상의 사용자를 포함할 수 있습니다.  

1. 하나 이상의 구성원이 있는 Azure Active Directory에서 그룹을 선택하거나 하나 이상의 할당된 사용자가 있는 Azure Active Directory에 연결된 응용 프로그램을 선택합니다. 
2. 각 사용자 검토가 고유의 액세스를 갖거나 하나 이상의 사용자 검토가 모든 사용자의 액세스를 가질지 여부를 결정합니다.
3. 검토자의 액세스 패널에 나타나도록 액세스 검토를 활성화합니다.  전역 관리자로서 [액세스 검토 페이지](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/)로 이동합니다. 
4. 액세스 검토를 시작합니다. [액세스 검토를 만드는 방법](active-directory-azure-ad-controls-create-access-review.md)에서 자세히 알아봅니다.
5. 입력을 제공하도록 검토자에게 요청합니다. 기본적으로 각 사용자는 액세스 패널에 대한 링크와 함께 Azure AD에서 전자 메일을 받으며 여기에서 [자신의 액세스 검토를 수행](active-directory-azure-ad-controls-perform-access-review.md)합니다.
6. 검토자가 입력을 하지 않으면 Azure AD에 미리 알림을 보내 달라고 요청할 수 있습니다.  기본적으로 Azure AD는 아직 응답하지 않은 검토자에게 종료일 중간에 미리 알림을 자동으로 보냅니다.
7. 검토자가 입력을 제공하면 액세스 검토를 중지하고 변경 내용을 적용합니다. [액세스 검토를 완료하는 방법](active-directory-azure-ad-controls-complete-access-review.md)에서 자세히 알아봅니다.


## <a name="next-steps"></a>다음 단계

- [그룹의 멤버 또는 응용 프로그램에 액세스에 대한 액세스 검토 만들기](active-directory-azure-ad-controls-create-access-review.md)




