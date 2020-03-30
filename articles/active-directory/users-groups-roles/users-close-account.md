---
title: 관리되지 않는 Azure AD 디렉터리에서 직장 또는 학교 계정 닫기
description: 관리되지 않는 Azure Active Directory에서 직장 또는 학교 계정을 닫는 방법
services: active-directory
author: rolyon
manager: mtillman
ms.service: active-directory
ms.subservice: users-groups-roles
ms.topic: article
ms.workload: identity
ms.date: 05/20/2019
ms.author: rolyon
ms.reviewer: ''
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3c101c0ef7932151e675c5c514ac558e6e0f94b2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73815728"
---
# <a name="close-your-work-or-school-account-in-an-unmanaged-directory"></a>관리되지 않는 디렉터리에서 직장 또는 학교 계정 닫기

관리되지 않는 Azure Active Directory(Azure AD) 조직의 사용자이고 해당 조직의 앱을 더 이상 사용하거나 연결을 유지할 필요가 없는 경우 언제든지 계정을 닫을 수 있습니다. 관리되지 않는 디렉터리에는 전역 관리자가 없습니다. 관리되지 않는 디렉터리에 있는 사용자는 관리자에게 문의하지 않고도 자신의 계정을 직접 닫을 수 있습니다.

관리되지 않는 디렉터리에서 사용자는 셀프 서비스 등록 중에 생성되는 경우가 많습니다. 예를 들어 무료 서비스에 가입하는 조직의 정보 워커가 있을 수 있습니다. 셀프 서비스 등록에 대한 자세한 내용은 [Azure Active Directory에 대한 셀프 서비스 등록이란 무엇입니까?](directory-self-service-signup.md)

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="before-you-begin"></a>시작하기 전에

계정을 해지하려면 전에 다음 항목을 확인해야 합니다.

* 관리되지 않는 Azure AD 디렉터리 사용자인지 확인합니다. 관리되는 디렉터리에 속한 경우 계정을 닫을 수 없습니다. 관리되는 디렉터리에 속해 있고 계정을 닫으려면 관리자에게 문의해야 합니다. 관리되지 않는 디렉터리에 속해 있는지 여부를 확인하는 방법에 대한 자세한 내용은 [관리되지 않는 테넌트에서 사용자 삭제를](https://docs.microsoft.com/flow/gdpr-dsr-delete#delete-the-user-from-unmanaged-tenant)참조하십시오.

* 보관하려는 데이터를 저장합니다. 내보내기 요청을 제출하는 방법에 대한 자세한 내용은 [관리되지 않는 테넌에 대한 시스템 생성 로그 액세스 및 내보내기를](https://docs.microsoft.com/power-platform/admin/powerapps-gdpr-dsr-guide-systemlogs#accessing-and-exporting-system-generated-logs-for-unmanaged-tenants)참조하십시오.

> [!WARNING]
> 계정을 폐쇄하는 것은 되돌릴 수 없습니다. 계정을 해지하면 모든 개인 데이터가 삭제됩니다. 더 이상 계정 및 계정과 연결된 데이터에 액세스할 수 없습니다.

## <a name="close-your-account"></a>계정 닫기

관리되지 않는 직장 또는 학교 계정을 닫려면 다음 단계를 따르세요.

1. 닫으려는 계정을 사용하여 [계정을 닫으려면](https://go.microsoft.com/fwlink/?linkid=873123)로그인합니다.

1. **데이터 요청에서** **계정 닫기**를 선택합니다.

    ![내 데이터 요청 - 계정 닫기](./media/users-close-account/close-account.png)

1. 확인 메시지를 검토한 다음 **예**.

    ![내 데이터 요청 - 닫기 확인](./media/users-close-account/confirm-close.png)

## <a name="next-steps"></a>다음 단계

- [Azure Active Directory에 대한 셀프 서비스 등록이란 무엇입니까?](directory-self-service-signup.md)
- [관리되지 않는 테넌트에서 사용자 삭제](https://docs.microsoft.com/flow/gdpr-dsr-delete#delete-the-user-from-unmanaged-tenant)
- [관리되지 않는 테넌트에 대한 시스템 생성 로그 액세스 및 내보내기](https://docs.microsoft.com/power-platform/admin/powerapps-gdpr-dsr-guide-systemlogs#accessing-and-exporting-system-generated-logs-for-unmanaged-tenants)
