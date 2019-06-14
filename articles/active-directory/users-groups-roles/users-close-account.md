---
title: 관리 되지 않는 디렉터리-Azure Active Directory에에서 회사 또는 학교 계정 사용 중지 | Microsoft Docs
description: 관리 되지 않는 Azure Active Directory에서 회사 또는 학교 계정을 사용 중지 하는 방법입니다.
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
ms.openlocfilehash: 39b359ef7feeaec541ba17e98a5d1e9b74c6403a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65958002"
---
# <a name="close-your-work-or-school-account-in-an-unmanaged-directory"></a>관리 되지 않는 디렉터리에 회사 또는 학교 계정 닫기

더 이상 관리 되지 않는 Azure Active Directory (Azure AD) 조직에 사용자 인 경우 해당 조직에서 앱을 사용 하거나 언제 든 계정을 종료할 수 없습니다를 사용 하 여 모든 연결은 유지 관리 해야 합니다. 관리 되지 않는 디렉터리 전역 관리자가 없습니다. 관리 되지 않는 디렉터리에서 사용자는 관리자에 게 문의 하지 않고도 해당 계정에는 자체를 닫아도 됩니다.

관리 되지 않는 디렉터리에서 사용자는 등록 종종 셀프 서비스 하는 동안 만들어집니다. 예로 정보 근로자는 조직에서 무료 서비스에 등록할 수 있습니다. 셀프 서비스 등록에 대 한 자세한 내용은 참조 하세요. [란, 셀프 서비스 Azure Active Directory에 대 한 등록?](directory-self-service-signup.md)합니다.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="before-you-begin"></a>시작하기 전에

계정을 종료할 수 있습니다, 전에 다음 항목을 확인 해야 합니다.

* 관리 되지 않는 Azure의 사용자 인지 확인 AD 디렉터리입니다. 관리 되는 디렉터리에 속한 사용자 계정의 닫을 수 없습니다. 관리 되는 디렉터리에 속해야 하 고 계정 사용 중지 하려면 프로그램 관리자에 게 문의 해야 합니다. 관리 되지 않는 디렉터리에 속해 있는지 확인 하는 방법에 대 한 정보를 참조 하세요 [관리 되지 않는 테 넌 트에서 사용자를 삭제](https://docs.microsoft.com/flow/gdpr-dsr-delete#delete-the-user-from-unmanaged-tenant)합니다.

* 유지 하려는 모든 데이터를 저장 합니다. 내보내기 요청을 제출 하는 방법에 대 한 자세한 내용은 [액세스 및 관리 되지 않는 테 넌 트에 대 한 시스템 생성 로그를 내보내는](https://docs.microsoft.com/power-platform/admin/powerapps-gdpr-dsr-guide-systemlogs#accessing-and-exporting-system-generated-logs-for-unmanaged-tenants)합니다.

> [!WARNING]
> 계정을 닫는 되돌릴 수 없습니다. 사용자 계정의 닫으면 모든 개인 데이터 제거 됩니다. 계정 및 계정과 연결 된 데이터에 대 한 액세스를 더 이상.

## <a name="close-your-account"></a>계정 닫기

관리 되지 않는 회사 또는 학교 계정을 종결 하려면 다음이 단계를 수행 합니다.

1. 에 로그인 [계정 닫기](https://go.microsoft.com/fwlink/?linkid=873123), 종결 하려는 된 계정을 사용 합니다.

1. 온 **내 데이터 요청**를 선택 **계정 닫기**합니다.

    ![내 데이터 요청 수-계정 닫기](./media/users-close-account/close-account.png)

1. 확인 메시지를 검토 하 고 선택한 **예**합니다.

    ![내 데이터 요청 수-닫기 확인](./media/users-close-account/confirm-close.png)

## <a name="next-steps"></a>다음 단계

- [셀프 서비스를 새로운 Azure Active Directory에 대 한 등록?](directory-self-service-signup.md)
- [관리 되지 않는 테 넌 트에서 사용자를 삭제 합니다.](https://docs.microsoft.com/flow/gdpr-dsr-delete#delete-the-user-from-unmanaged-tenant)
- [액세스 및 관리 되지 않는 테 넌 트에 대 한 시스템 생성 로그 내보내기](https://docs.microsoft.com/power-platform/admin/powerapps-gdpr-dsr-guide-systemlogs#accessing-and-exporting-system-generated-logs-for-unmanaged-tenants)
