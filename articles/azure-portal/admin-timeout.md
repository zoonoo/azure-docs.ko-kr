---
title: Azure 포털 사용자에 대한 디렉터리 수준의 비활성 시간 초과 설정 | 마이크로 소프트 문서
description: 관리자는 세션이 로그아웃되기 전에 최대 유휴 시간을 적용할 수 있습니다. 비활성 시간 초과 정책은 디렉터리 수준에서 설정됩니다.
services: azure-portal
keywords: 설정, 시간 설정
author: mgblythe
ms.author: mblythe
ms.date: 02/10/2020
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: e27135d09da7060f2a948e37f6026fe66fbef5b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79096618"
---
# <a name="set-directory-level-inactivity-timeout"></a>디렉터리 수준 비활성 시간 초과 설정

비활성 시간 초과 설정은 사용자가 워크스테이션을 보호하는 것을 잊어버린 경우 무단 액세스로부터 리소스를 보호하는 데 도움이 됩니다. 사용자가 잠시 동안 유휴 상태이면 Azure 포털 세션이 자동으로 로그아웃됩니다. 전역 관리자 [역할의](../active-directory/users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator) 관리자는 세션이 로그아웃되기 전에 최대 유휴 시간을 적용할 수 있습니다. 비활성 시간 제한 설정은 디렉터리 수준에서 적용됩니다. 디렉터리에 대한 자세한 내용은 [활성 디렉터리 도메인 서비스 개요를](/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview)참조하십시오.

## <a name="configure-the-inactive-timeout-setting"></a>비활성 시간 시간 설정 구성

전역 관리자이고 Azure 포털의 모든 사용자에 대해 유휴 시간 지정 설정을 적용하려는 경우 다음 단계를 따르십시오.

1. [Azure 포털에](https://portal.azure.com)로그인합니다.
2. 전역 페이지 헤더에서 **설정을** 선택합니다.
3. 링크 텍스트 를 선택 **디렉터리 수준 시간 정을 구성**합니다.

    ![링크 텍스트가 강조 표시된 포털 설정을 보여주는 스크린샷](./media/admin-timeout/settings.png)

4. 새 페이지가 열립니다. **디렉터리 수준 비활성 시간 초과 구성** 페이지에서 Azure Portal이 설정을 켜려면 **디렉터리 수준 유휴 시간 설정 을** 선택합니다.
5. 그런 다음 세션이 자동으로 로그아웃되기 전에 사용자가 유휴 상태일 수 있는 최대 시간 동안 **시간** 및 **분을** 입력합니다.
6. **적용**을 선택합니다.

    ![디렉터리 수준 비활성 시간 초과를 설정하는 페이지를 보여주는 스크린샷](./media/admin-timeout/configure.png)

비활성 시간 초과 정책이 올바르게 설정되어 있는지 확인하려면 전역 페이지 헤더에서 **알림을** 선택합니다. 성공 알림이 나열되어 있는지 확인합니다.

  ![디렉터리 수준 비활성 시간 초과에 대한 성공적인 알림 메시지를 보여주는 스크린샷](./media/admin-timeout/confirmation.png)

이 설정은 새 세션에 적용됩니다. 이미 로그인한 사용자에게는 즉시 적용되지 않습니다.

> [!NOTE]
> 전역 관리자가 디렉터리 수준의 시간 초과 설정을 구성한 경우 사용자는 정책을 재정의하고 자체 비활성 로그아웃 기간을 설정할 수 있습니다. 그러나 사용자는 전역 관리자가 디렉터리 수준에서 설정한 시간 간격보다 적은 시간 간격을 선택해야 합니다.
>

## <a name="next-steps"></a>다음 단계

* [Azure Portal 기본 설정 지정](set-preferences.md)
* [사용자 설정 내보내기 또는 삭제](azure-portal-export-delete-settings.md)
* [고대비 켜기 및 테마 변경](azure-portal-change-theme-high-contrast.md)
