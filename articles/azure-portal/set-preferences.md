---
title: Azure 포털 기본 설정 | 마이크로 소프트 문서
description: Azure 포털 기본 설정을 사용자 고유의 기본 설정에 맞게 변경할 수 있습니다. 설정에는 비활성 세션 시간 시간, 기본 보기, 메뉴 모드, 대비, 테마, 알림, 언어 및 지역 형식이 포함됩니다.
services: azure-portal
keywords: 설정, 시간 시간, 언어, 지역
author: mgblythe
ms.author: mblythe
ms.date: 12/19/2019
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: 7bcfdeec832b14eb53c0dab6cb2f53970d85c804
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76310682"
---
# <a name="set-your-azure-portal-preferences"></a>Azure Portal 기본 설정 지정

Azure 포털의 기본 설정을 변경하여 사용자 고유의 기본 설정에 맞게 변경할 수 있습니다. 아래에 나열된 각 설정은 변경할 수 있습니다.

* [비활성 세션 시간 시간](#change-the-idle-duration-for-inactive-sign-out)
* [기본 보기](#choose-your-default-view)
* [포털 메뉴 모드](#choose-a-portal-menu-mode)
* [색상 및 고대비 테마](#choose-a-theme)
* [팝업 알림](#enable-or-disable-pop-up-notifications)
* [언어 및 지역 형식](#change-language-and-regional-settings)

## <a name="change-general-portal-settings"></a>일반 포털 설정 변경

1. [Azure 포털에](https://portal.azure.com)로그인합니다.
2. 전역 페이지 헤더에서 **설정을** 선택합니다.

    ![설정이 강조 표시된 전역 페이지 헤더 아이콘을 보여주는 스크린샷](./media/set-preferences/header-settings.png)

### <a name="change-the-idle-duration-for-inactive-sign-out"></a>비활성 로그아웃의 유휴 기간 변경

비활성 시간 초과 설정은 워크스테이션 을 보호하는 것을 잊어버린 경우 무단 액세스로부터 리소스를 보호하는 데 도움이 됩니다. 잠시 동안 유휴 상태가 되면 Azure 포털 세션에서 자동으로 로그아웃됩니다.

**비활성 상태일 때 서명 에서**드롭다운을 선택합니다. 유휴 상태인 경우 Azure 포털 세션이 로그아웃된 기간을 선택합니다.

   ![비활성 시간 설정이 강조 표시된 포털 설정을 보여주는 스크린샷](./media/set-preferences/inactive-signout-user.png)

변경 사항은 자동으로 저장됩니다. 유휴 상태인 경우 설정한 기간 이후에 Azure 포털 세션이 로그아웃됩니다.

디렉터리 수준의 관리자가 이 설정을 사용하여 최대 유휴 시간을 적용할 수도 있습니다. 관리자가 디렉터리 수준 시간 지정 설정을 설정한 경우에도 비활성 로그아웃 기간을 설정할 수 있습니다. 디렉터리 수준에서 설정된 시간 설정보다 작은 시간 설정을 선택합니다.

관리자가 비활성 시간 초과 정책을 사용하도록 설정한 경우 **디렉터리 비활성 시간 초과 정책 확인란을 재정의합니다.** 정책 설정보다 작은 시간 간격을 설정합니다.

   ![디렉토리 비활성 시간 초과 정책 설정을 재정의하여 포털 설정을 보여주는 스크린샷이 강조 표시되었습니다.](./media/set-preferences/inactive-signout-override.png)


> [!NOTE]
> 관리자이고 Azure 포털의 모든 사용자에 대해 비활성 시간 초과 설정을 적용하려는 경우 [Azure 포털 사용자에 대한 디렉터리 수준 비활성 시간 설정 설정을](admin-timeout.md) 참조하세요.
>

### <a name="choose-your-default-view"></a>기본 보기 선택 

Azure 포털에 로그인할 때 기본적으로 열리는 페이지를 변경할 수 있습니다.

   ![기본 보기가 강조 표시된 Azure 포털 설정을 보여주는 스크린샷](./media/set-preferences/default-view.png)

기본 보기 설정은 로그인할 때 표시되는 Azure 포털 보기를 제어합니다. 기본적으로 Azure Home을 열거나 대시보드 보기를 열도록 선택할 수 있습니다.

* **홈은** 사용자 정의 할 수 없습니다.  인기 있는 Azure 서비스에 대한 바로 가기를 표시하고 가장 최근에 사용한 리소스를 나열합니다. 또한 Microsoft 학습 및 Azure 로드맵과 같은 리소스에 대한 유용한 링크도 제공합니다.
* 대시보드를 사용자 지정하여 사용자 정의할 수 있습니다. 예를 들어 프로젝트, 작업 또는 역할에 중점을 둔 대시보드를 빌드할 수 있습니다. **대시보드를**선택하면 기본 보기가 가장 최근에 사용한 대시보드로 이동합니다.

### <a name="choose-a-portal-menu-mode"></a>포털 메뉴 모드 선택

포털 메뉴의 기본 모드는 포털 메뉴가 페이지에서 차지하는 공간을 제어합니다.

* 포털 메뉴가 **플라이아웃** 모드에 있으면 필요할 때까지 숨김이 됩니다. 메뉴를 열거나 닫을 메뉴 아이콘을 선택합니다.
* 포털 메뉴에 대해 **도킹** 모드를 선택하면 항상 표시됩니다. 메뉴를 축소하여 더 많은 작업 공간을 제공할 수 있습니다. 

### <a name="choose-a-theme"></a>테마 선택

선택한 테마는 Azure 포털에 나타나는 배경 및 글꼴 색상에 영향을 줍니다. 미리 설정된 네 가지 색상 테마 중 하나를 선택할 수 있습니다. 각 미리보기 이미지를 선택하여 자신에게 가장 적합한 테마를 찾습니다.

   ![테마가 강조 표시된 Azure 포털 설정을 보여주는 스크린샷](./media/set-preferences/theme.png)

대신 고대비 테마 중 하나를 선택할 수 있습니다. 대비가 높은 설정을 사용하면 Azure 포털에서 시각 장애가 있는 사용자를 쉽게 읽고 다른 모든 테마 선택 항목을 재정의할 수 있습니다. 자세한 내용은 [고대비 켜기 또는 테마 변경](azure-portal-change-theme-high-contrast.md)을 참조하십시오.

### <a name="enable-or-disable-pop-up-notifications"></a>팝업 알림 사용 또는 사용 안 함

알림은 현재 세션과 관련된 시스템 메시지입니다. 방금 만든 리소스가 사용 가능해지는 경우, 현재 크레딧 잔액과 같은 정보를 제공하거나 마지막 작업을 확인합니다. 팝업 알림이 켜져 있으면 메시지가 화면 상단 모서리에 간략하게 표시됩니다. 

팝업 알림을 사용 하거나 사용 하지 않도록 설정 하려면 **팝업 알림 활성화** 확인란을 선택 하거나 선택 해제 합니다.

   ![팝업 알림이 강조 표시된 Azure 포털 설정을 보여주는 스크린샷](./media/set-preferences/popup-notifications.png)

현재 세션 중에 수신된 모든 알림을 읽으려면 전역 헤더에서 **알림을** 선택합니다.

   ![알림이 강조 표시된 Azure 포털 전역 헤더를 보여주는 스크린샷](./media/set-preferences/read-notifications.png)

이전 세션의 알림을 읽으려면 활동 로그에서 이벤트를 찾습니다. 자세한 내용은 [Azure 활동 로그 이벤트 보기를](/azure/azure-monitor/platform/activity-log-view)읽고 검색합니다.

### <a name="settings-under-useful-links"></a>유용한 링크 아래설정

Azure 포털 설정을 변경하고 삭제하려면 **기본 설정 복원을**선택합니다. 포털 설정에 변경한 내용은 모두 손실됩니다. 이 옵션은 대시보드 사용자 지정에 영향을 주지 않습니다.

모든 설정 **내보내기** 또는 **모든 설정 및 개인 대시보드 삭제에**대한 자세한 내용은 [사용자 설정 내보내기 또는 삭제를](azure-portal-export-delete-settings.md)참조하십시오.

## <a name="change-language-and-regional-settings"></a>언어 및 지역 설정 변경

Azure 포털의 텍스트가 표시되는 방식을 제어하는 두 가지 설정이 있습니다. **언어** 설정은 Azure 포털의 텍스트에 대해 표시되는 언어를 제어합니다. **지역 형식은** 날짜, 시간, 숫자 및 통화가 표시되는 방식을 제어합니다.

Azure 포털에서 사용되는 언어를 변경하려면 드롭다운을 사용하여 사용 가능한 언어 목록에서 선택합니다.

선택한 언어에 대한 지역 옵션을 표시하도록 지역 형식 선택이 변경됩니다. 자동 선택을 변경하려면 드롭다운을 사용하여 원하는 지역 형식을 선택합니다.

예를 들어 영어를 해당 언어로 선택한 다음 미국을 지역 형식으로 선택하면 통화가 미국 달러로 표시됩니다. 영어를 언어로 선택한 다음 유럽을 지역 형식으로 선택하면 통화가 유로로 표시됩니다.

언어 및 지역 별 형식 설정을 업데이트하려면 **적용을** 선택합니다.

   ![언어 및 지역 별 형식 설정을 보여주는 스크린샷](./media/set-preferences/language.png)

>[!NOTE]
>이러한 언어 및 지역 설정은 Azure 포털에만 영향을 미칩니다. 새 탭 이나 창에서 열리는 문서 링크 표시 할 언어를 결정 하기 위해 브라우저의 언어 설정을 사용 합니다.
>

## <a name="next-steps"></a>다음 단계

* [사용자 지정 대시보드 만들기 및 공유](azure-portal-dashboards.md)
* [Azure Portal 방법 비디오 시리즈](azure-portal-video-series.md)
