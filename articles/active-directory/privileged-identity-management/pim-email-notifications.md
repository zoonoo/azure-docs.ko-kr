---
title: Azure AD PIM에서 이메일 알림 | Microsoft Docs
description: Azure AD PIM(Privileged Identity Management)에서 이메일 알림을 설명합니다.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.component: protection
ms.date: 07/14/2018
ms.author: rolyon
ms.reviewer: hanki
ms.custom: pim
ms.openlocfilehash: 6c329554b5854f113fb216f874fa5a918110f9c5
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/14/2018
ms.locfileid: "39058095"
---
# <a name="email-notifications-in-azure-ad-pim"></a>Azure AD PIM에서 이메일 알림

Azure AD PIM(Privileged Identity Management)에서 주요 이벤트가 발생할 때 이메일 알림이 관련 관리자 또는 사용자에게 전송됩니다. 예를 들어 PIM은 다음과 같은 이벤트에 대한 이메일을 보냅니다.

- 권한 있는 역할 활성화가 승인 보류 중인 경우
- 권한 있는 역할 활성화 요청이 승인된 경우
- 권한 있는 역할이 활성화된 경우
- 권한 있는 역할이 할당된 경우
- Azure AD PIM이 사용되도록 설정된 경우

2018년 7월 말부터는 PIM을 통해 보낸 이메일 알림에는 새로운 보낸 사람 이메일 주소 및 새로운 시각적 디자인이 포함됩니다. 이 업데이트는 Azure AD용 PIM 및 Azure 리소스용 PIM 모두에 영향을 줍니다. 이전에 이메일 알림을 트리거한 모든 이벤트는 계속 이메일을 보냅니다. 이 업데이트는 전적으로 기능을 변경하지 않는 시각적 개체 변경 내용입니다.

## <a name="sender-email-address"></a>보낸 사람 이메일 주소

2018년 7월 말부터 이메일 알림에는 다음 주소가 포함됩니다.

- 이메일 주소: **azure-noreply@microsoft.com**
- 표시 이름: Microsoft Azure

이전에 이메일 알림에는 다음 주소가 있었습니다.

- 이메일 주소: **azureadnotifications@microsoft.com**
- 표시 이름: Microsoft Azure AD 알림 서비스

## <a name="pim-emails-for-azure-ad-roles"></a>Azure AD 역할에 대한 PIM 이메일

2018년 7월 말부터 Azure AD 역할에 대한 PIM 이메일 알림에는 업데이트된 디자인이 포함됩니다. 다음에서는 사용자가 가상의 Contoso 조직에 대한 권한 있는 역할을 활성화할 때 전송되는 예제 이메일을 보여줍니다.

![Azure AD 역할에 대한 새로운 PIM 이메일](./media/pim-email-notifications/email-directory-new.png)

이전에 사용자가 권한 있는 역할을 활성화하면 다음과 같은 이메일이 표시되었습니다.

![Azure AD 역할에 대한 이전 PIM 이메일](./media/pim-email-notifications/email-directory-old.png)

## <a name="pim-emails-for-azure-resource-roles"></a>Azure 리소스 역할에 대한 PIM 이메일

2018년 7월 말부터 Azure 리소스 역할에 대한 PIM 이메일 알림에는 업데이트된 디자인이 포함됩니다. 다음에서는 사용자가 가상의 Contoso 조직에 대한 권한 있는 역할을 할당받을 때 전송되는 예제 이메일을 보여줍니다.

![Azure 리소스 역할에 대한 새로운 PIM 이메일](./media/pim-email-notifications/email-resources-new.png)

이전에 사용자가 권한 있는 역할을 할당받으면 다음과 같은 이메일이 표시되었습니다.

![Azure 리소스 역할에 대한 이전 PIM 이메일](./media/pim-email-notifications/email-resources-old.png)

## <a name="next-steps"></a>다음 단계

- [Azure AD PIM에서 역할 활성화 설정을 관리하는 방법](pim-how-to-change-default-settings.md)
- [Azure AD PIM의 승인](azure-ad-pim-approval-workflow.md)
