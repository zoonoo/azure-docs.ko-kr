---
title: Azure Portal에서 대시보드 만들기 및 공유
description: 이 문서에서는 Azure Portal에서 대시보드를 만들고, 사용자 지정하고, 게시하고, 공유하는 방법을 설명합니다.
services: azure-portal
documentationcenter: ''
author: sewatson
manager: mtillman
ms.assetid: ff422f36-47d2-409b-8a19-02e24b03ffe7
ms.service: azure-portal
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 03/23/2020
ms.author: mblythe
ms.openlocfilehash: e6eda3b522a5b46cd82ef29ae493891a624d3272
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81459290"
---
# <a name="create-and-share-dashboards-in-the-azure-portal"></a>Azure Portal에서 대시보드 만들기 및 공유

대시보드는 Azure 포털에서 클라우드 리소스에 대한 집중적이고 체계적인 보기입니다. 일상 작업을 신속하게 시작하고 리소스를 모니터링할 수 있는 작업 영역으로 대시보드를 사용합니다. 예를 들어 프로젝트, 작업 또는 사용자 역할을 기반으로 사용자 지정 대시보드를 빌드합니다.

Azure 포털은 기본 대시보드를 시작점으로 제공합니다. 기본 대시보드를 편집할 수 있습니다. 추가 대시보드를 만들고 사용자 지정하고 대시보드를 게시하고 공유하여 다른 사용자가 사용할 수 있도록 합니다. 이 문서에서는 새 대시보드를 만들고, 인터페이스를 사용자 지정하고, 대시보드를 게시하고 공유하는 방법을 설명합니다.

## <a name="create-a-new-dashboard"></a>새 대시보드 만들기

이 예제에서는 새 개인 대시보드를 만들고 이름을 할당합니다. 시작하려면 다음 단계를 수행하세요.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. Azure 포털 메뉴에서 **대시보드**를 선택합니다. 기본 보기가 이미 대시보드로 설정되어 있을 수 있습니다.

    ![대시보드 열기](./media/azure-portal-dashboards/portal-menu-dashboard.png)

1. **새 대시보드를**선택합니다.

    ![새 대시보드의 스크린샷](./media/azure-portal-dashboards/create-new-dashboard.png)

    이 작업은 **타일을**선택하는 타일 갤러리와 타일을 정렬할 빈 그리드를 엽니다.

    ![타일 갤러리 및 빈 그리드의 스크린 샷](./media/azure-portal-dashboards/dashboard-name.png)

1. 대시보드 레이블에서 내 대시보드 텍스트를 선택하고 사용자 지정 **대시보드를** 쉽게 식별하는 데 도움이 되는 이름을 입력합니다.

1. 편집 모드를 종료하려면 페이지 헤더에서 **사용자 지정 완료를** 선택합니다.

이제 대시보드 보기에 새 대시보드가 표시됩니다. 대시보드 이름 옆에 있는 화살표를 선택하여 사용 가능한 대시보드를 확인합니다. 목록에는 다른 사용자가 만들고 공유한 대시보드가 포함될 수 있습니다.

## <a name="edit-a-dashboard"></a>대시보드 편집

이제 Azure 리소스를 나타내는 타일을 추가, 크기 조정 및 정렬할 대시보드를 편집해 보겠습니다.

### <a name="add-tiles-from-the-dashboard"></a>대시보드에서 타일 추가

대시보드에 타일을 추가하려면 다음 단계를 따르십시오.

1. 페이지 ![헤더에서](./media/azure-portal-dashboards/dashboard-edit-icon.png) 편집 아이콘 **편집을** 선택합니다.

    ![편집을 강조 하는 대시보드의 스크린샷](./media/azure-portal-dashboards/dashboard-edit.png)

1. 타일 **갤러리를** 탐색하거나 검색 필드를 사용하여 원하는 타일을 찾습니다.

1. 기본 크기와 위치가 있는 대시보드에 타일을 추가하려면 **추가를** 선택합니다. 또는 타일을 그리드로 드래그하여 원하는 위치에 배치합니다.

> [!TIP]
> 두 개 이상의 조직에서 작업하는 경우 **대시보드에 조직 ID** 타일을 추가하여 리소스가 속한 조직을 명확하게 표시합니다.

### <a name="add-tiles-from-a-resource-page"></a>리소스 페이지에서 타일 추가

대시보드에 타일을 추가하는 다른 방법이 있습니다. 많은 리소스 페이지에는 명령 모음에 푸시핀 아이콘이 포함되어 있습니다. 아이콘을 선택하면 원본 페이지를 나타내는 타일이 현재 활성 상태인 대시보드에 고정됩니다. 

![핀 아이콘이 있는 페이지 명령 모음의 스크린샷](./media/azure-portal-dashboards/dashboard-pin-blade.png)

### <a name="resize-or-rearrange-tiles"></a>타일 크기 조정 또는 재배열

타일 크기를 변경하거나 대시보드에서 타일을 다시 정렬하려면 다음 단계를 따르십시오.

1. 페이지 ![헤더에서](./media/azure-portal-dashboards/dashboard-edit-icon.png) 편집 아이콘 **편집을** 선택합니다.

1. 타일의 오른쪽 상단 모서리에서 컨텍스트 메뉴를 선택합니다. 그런 다음 타일 크기를 선택합니다. 모든 크기를 지원하는 타일에는 오른쪽 아래 모서리에 "핸들"이 포함되어 있어 타일을 원하는 크기로 드래그할 수 있습니다.

    ![타일 크기 메뉴가 열려 있는 대시보드의 스크린샷](./media/azure-portal-dashboards/dashboard-tile-resize.png)

1. 타일을 선택하고 그리드의 새 위치로 드래그하여 대시보드를 정렬합니다.

### <a name="additional-tile-configuration"></a>추가 타일 구성

일부 타일은 원하는 정보를 표시하기 위해 더 많은 구성이 필요할 수 있습니다. 예를 들어 **메트릭 차트** 타일은 **Azure Monitor**에서 메트릭을 표시하도록 설정해야 합니다. 타일 데이터를 사용자 지정하여 대시보드의 기본 시간 설정을 재정의할 수도 있습니다.

설정해야 하는 타일은 **타일을** 사용자 지정할 때까지 구성 타일 배너를 표시합니다. 타일을 사용자 지정하려면 다음을 수행합니다.

1. 편집 모드를 종료하려면 페이지 헤더에서 **사용자 지정 완료를** 선택합니다.

1. 배너를 선택한 다음 필요한 설정을 수행합니다.

    ![구성이 필요한 타일의 스크린샷](./media/azure-portal-dashboards/dashboard-configure-tile.png)

> [!NOTE]
> 마크다운 타일을 사용하면 대시보드에 사용자 지정 정적 콘텐츠를 표시할 수 있습니다. 기본 지침, 이미지, 하이퍼링크 집합 또는 연락처 정보일 수 있습니다. 마크다운 타일 사용에 대한 자세한 내용은 [Azure 대시보드에서 마크다운 타일 사용을 사용하여 사용자 지정 콘텐츠를 표시합니다.](azure-portal-markdown-tile.md)

### <a name="customize-tile-data"></a>타일 데이터 사용자 지정

대시보드의 데이터는 지난 24시간 동안의 활동을 자동으로 표시합니다. 이 타일에 대해 다른 시간 범위를 표시하려면 다음 단계를 따르십시오.

1. 컨텍스트 메뉴에서 타일 데이터 사용자 ![지정또는](./media/azure-portal-dashboards/dashboard-filter.png) 타일의 왼쪽 위 모서리에서 필터 아이콘 필터를 **선택합니다.**

    ![타일 컨텍스트 메뉴의 스크린샷](./media/azure-portal-dashboards/dashboard-customize-tile-data.png)

1. **타일 수준에서 대시보드 시간 설정을 재정의하려면 확인란을**선택합니다.

    ![타일 시간 설정을 구성하는 대화 상자의 스크린샷](./media/azure-portal-dashboards/dashboard-override-time-settings.png)

1. 이 타일에 대해 표시할 시간 범위를 선택합니다. 지난 30분에서 지난 30일까지 선택하거나 사용자 지정 범위를 정의할 수 있습니다.

1. 표시할 시간 세분성을 선택합니다. 1분 분 단위에서 1개월까지 어디서나 표시할 수 있습니다.

1. **적용**을 선택합니다.

## <a name="delete-a-tile"></a>타일 삭제

대시보드에서 타일을 제거하려면 다음 단계를 따르십시오.

* 타일의 오른쪽 상단 모서리에서 컨텍스트 메뉴를 선택한 다음 **대시보드에서 제거를**선택합니다. 또는

* 편집 ![아이콘](./media/azure-portal-dashboards/dashboard-edit-icon.png) **편집을** 선택하여 사용자 지정 모드로 들어갑니다. 타일의 오른쪽 상단 모서리에 마우스를 ![가져온](./media/azure-portal-dashboards/dashboard-delete-icon.png) 다음 삭제 아이콘 삭제 아이콘을 선택하여 대시보드에서 타일을 제거합니다.

   ![대시 보드에서 타일을 제거하는 방법을 보여주는 스크린 샷](./media/azure-portal-dashboards/dashboard-delete-tile.png)

## <a name="clone-a-dashboard"></a>대시보드 복제

기존 대시보드를 새 대시보드의 템플릿으로 사용하려면 다음 단계를 따르세요.

1. 대시보드 보기에 복사할 대시보드가 표시되는지 확인합니다.

1. 페이지 헤더에서 복제 ![아이콘](./media/azure-portal-dashboards/dashboard-clone.png) **복제를**선택합니다.

1. 대시보드 *이름의* 복제라는 대시보드 **복사본이** 편집 모드에서 열립니다. 이 문서의 이전 단계를 사용하여 대시보드의 이름을 변경하고 사용자 지정합니다.

## <a name="publish-and-share-a-dashboard"></a>대시보드 게시 및 공유

대시보드를 만들 때 기본적으로 비공개이기 때문에 대시보드를 볼 수 있는 유일한 사람이됩니다. 다른 사용자가 대시보드를 사용할 수 있도록 하려면 대시보드를 게시하고 공유할 수 있습니다. 자세한 내용은 [역할 기반 액세스 제어 를 사용하여 Azure 대시보드 공유를](azure-portal-dashboard-share-access.md)참조하세요.

### <a name="open-a-shared-dashboard"></a>공유 대시보드 열기

공유 대시보드를 찾고 열려면 다음 단계를 따르세요.

1. 대시보드 이름 옆에 있는 화살표를 선택합니다.

1. 표시된 대시보드 목록에서 선택합니다. 열려는 대시보드가 나열되지 않은 경우:

    1. **모든 대시보드 찾아보기를 선택합니다.**

        ![대시보드 선택 메뉴의 스크린샷](./media/azure-portal-dashboards/dashboard-browse.png)

    1. **유형** 필드에서 공유 **대시보드를 선택합니다.**

        ![모든 대시보드 선택 메뉴의 스크린샷](./media/azure-portal-dashboards/dashboard-browse-all.png)

    1. 하나 이상의 구독을 선택합니다. 텍스트를 입력하여 대시보드를 이름으로 필터링할 수도 있습니다.

    1. 공유 대시보드 목록에서 대시보드를 선택합니다.

## <a name="delete-a-dashboard"></a>대시보드 삭제

비공개 또는 공유 대시보드를 영구적으로 삭제하려면 다음 단계를 따르세요.

1. 대시보드 이름 옆에 있는 목록에서 삭제할 대시보드를 선택합니다.

1. 페이지 ![헤더에서 삭제](./media/azure-portal-dashboards/dashboard-delete-icon.png) **아이콘을** 선택합니다.

1. 개인 대시보드의 경우 확인 대화 상자에서 **확인을** 선택하여 대시보드를 제거합니다. 공유 대시보드의 경우 확인 대화 상자에서 확인란을 선택하여 게시된 대시보드가 더 이상 다른 사용자가 볼 수 없는지 확인합니다. 그런 다음 **확인을**선택합니다.

    ![삭제 확인 스크린샷](./media/azure-portal-dashboards/dashboard-delete-dash.png)

## <a name="next-steps"></a>다음 단계

* [역할 기반 Access Control을 사용하여 Azure 대시보드 공유](azure-portal-dashboard-share-access.md)
* [프로그래밍 방식으로 Azure 대시보드 만들기](azure-portal-dashboards-create-programmatically.md)
