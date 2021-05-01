---
title: Azure Portal에서 대시보드 만들기
description: 이 문서에서는 Azure Portal에서 대시보드를 만들고 사용자 지정하는 방법을 설명합니다.
ms.assetid: ff422f36-47d2-409b-8a19-02e24b03ffe7
ms.topic: how-to
ms.date: 03/16/2021
ms.openlocfilehash: fa7f1813d86571b568d23d64cab5705f8a117faa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104774661"
---
# <a name="create-a-dashboard-in-the-azure-portal"></a>Azure Portal에서 대시보드 만들기

대시보드는 Azure Portal의 클라우드 리소스에 집중적으로 구성된 보기입니다. 대시보드를 작업 영역으로 사용하여 일상적인 작업 목적으로 리소스를 모니터링하고 신속하게 작업을 시작할 수 있습니다. 예를 들어 프로젝트, 작업 또는 사용자 역할을 기반으로 사용자 지정 대시보드를 빌드합니다.

Azure Portal은 기본 대시보드를 시작점으로 제공합니다. 기본 대시보드를 편집하고 추가 대시보드를 만들어 사용자 지정할 수 있습니다. 이 문서에서는 새 대시보드를 만들고 사용자 지정하는 방법을 설명합니다. 대시보드 공유에 대한 자세한 내용은 [Azure 역할 기반 액세스 제어를 사용하여 Azure 대시보드 공유](azure-portal-dashboard-share-access.md)를 참조하세요.

## <a name="create-a-new-dashboard"></a>새 대시보드 만들기

이 예제에서는 새 프라이빗 대시보드를 만들고 이름을 할당합니다. 시작하려면 다음 단계를 수행하세요.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. Azure Portal 메뉴에서 **대시보드** 를 선택합니다. 기본 보기는 이미 대시보드로 설정되어 있을 수 있습니다.

    ![대시보드를 엽니다](./media/azure-portal-dashboards/portal-menu-dashboard.png)

1. **새 대시보드**, **빈 대시보드** 를 차례로 선택합니다.

    ![새 대시보드의 스크린샷](./media/azure-portal-dashboards/create-new-dashboard.png)

    이 작업을 수행하면 **타일 갤러리** 가 열리며, 여기서 타일을 선택하고 타일을 정렬할 빈 그리드를 선택합니다.

1. 대시보드 레이블에서 **내 대시보드** 텍스트를 선택하고 사용자 지정 대시보드를 쉽게 식별할 수 있는 이름을 입력합니다.

    ![타일 갤러리 및 빈 그리드 스크린샷](./media/azure-portal-dashboards/dashboard-name.png)

1. 페이지 머리글의 **사용자 지정 완료** 를 선택하여 편집 모드를 종료하고 **저장** 을 선택합니다.

    :::image type="content" source="media/azure-portal-dashboards/dashboard-save.png" alt-text="대시보드 저장 프로세스의 스크린샷":::

이제 대시보드 보기에 새 대시보드가 표시됩니다. 대시보드 이름 옆의 화살표를 선택하면 사용할 수 있는 대시보드를 표시합니다. 이 목록에는 다른 사용자가 만들고 공유한 대시보드가 포함되어 있을 수 있습니다.

## <a name="edit-a-dashboard"></a>대시보드 편집

이제 대시보드를 편집하여 사용자의 Azure 리소스를 대표하는 타일을 추가, 크기 조정 및 정렬해 봅니다.

### <a name="add-tiles-from-the-tile-gallery"></a>타일 갤러리에서 타일 추가

대시보드에 타일을 추가하려면 다음 단계를 수행합니다.

1. 페이지 머리글에서 ![편집 아이콘](./media/azure-portal-dashboards/dashboard-edit-icon.png) **편집** 을 선택합니다.

    ![대시보드 강조 표시 편집의 스크린샷](./media/azure-portal-dashboards/dashboard-edit.png)

1. **타일 갤러리** 를 찾아보거나 검색 필드를 사용해 원하는 타일을 찾습니다.

1. **추가** 를 선택해 기본 사이즈와 기본 위치로 타일을 대시보드에 추가합니다. 또는 타일을 그리드로 끌어 원하는 위치에 놓습니다. 원하는 타일을 추가합니다. 다음과 같은 몇 가지 아이디어가 있습니다.

    - **모든 리소스** 를 추가하여 이미 만든 리소스를 확인합니다.

    - 둘 이상의 조직에서 작업하는 경우 **조직 ID** 타일을 대시보드에 추가해 리소스가 속한 조직을 명확하게 표시합니다.

1. 페이지 머리글에서 **저장** 을 선택합니다.

### <a name="add-tiles-from-a-resource-page"></a>리소스 페이지에서 타일 추가

다른 방법으로 타일을 대시보드에 추가할 수 있습니다. 대부분의 리소스 페이지에는 명령 모음에 압정 아이콘이 있습니다. 이 아이콘을 선택하면 원본 페이지를 나타내는 타일이 현재 활성 상태인 대시보드에 고정됩니다. 

![고정 아이콘이 있는 페이지 명령 모음의 스크린샷](./media/azure-portal-dashboards/dashboard-pin-blade.png)

### <a name="resize-or-rearrange-tiles"></a>타일 크기 조정 또는 재정렬

타일의 크기를 변경하거나 대시보드의 타일을 재정렬하려면 다음 단계를 수행합니다.

1. 페이지 머리글에서 ![편집 아이콘](./media/azure-portal-dashboards/dashboard-edit-icon.png) **편집** 을 선택합니다.

1. 타일의 오른쪽 위 모퉁이에 있는 바로 가기 메뉴를 선택합니다. 그 다음 타일 크기를 선택합니다. 모든 크기를 지원하는 타일의 오른쪽 아래 모서리에는 타일을 원하는 크기로 끌어서 조절할 수 있는 “핸들”이 포함됩니다.

    ![타일 크기 메뉴가 열린 대시보드의 스크린샷](./media/azure-portal-dashboards/dashboard-tile-resize.png)

1. 타일을 선택하고 그리드의 새 위치로 끌어 대시보드를 정렬합니다.

### <a name="additional-tile-configuration"></a>추가 타일 구성

일부 타일에는 원하는 정보를 표시하는 추가 구성이 필요할 수 있습니다. 예를 들어 **메트릭 차트** 타일은 Azure Monitor의 메트릭을 표시하도록 설정되어야 합니다. 또한 타일 데이터를 사용자 지정하여 대시보드의 기본 시간 설정을 재정의할 수 있습니다.

설정해야 하는 타일은 타일을 사용자 지정할 때까지 배너가 표시됩니다. **메트릭 차트** 의 경우 배너가 **메트릭에서 편집** 됩니다. 타일을 사용자 지정하려면 다음 절차를 수행합니다.

1. 페이지 머리글에서 **저장** 을 선택하여 편집 모드를 종료합니다.

1. 배너를 선택하고 필요한 설정을 수행합니다.

    ![구성이 필요한 타일의 스크린샷](./media/azure-portal-dashboards/dashboard-configure-tile.png)

> [!NOTE]
> markdown 타일을 사용하면 대시보드에 사용자 지정 고정 콘텐츠를 표시할 수 있습니다. 이는 기본 지침, 이미지, 하이퍼링크 집합 또는 연락처 정보를 표시할 수도 있습니다. markdown 타일을 사용하는 방법에 대한 자세한 내용은 [Azure 대시보드의 markdown 타일을 사용하여 사용자 지정 콘텐츠 표시](azure-portal-markdown-tile.md)를 참조하세요.

### <a name="customize-tile-data"></a>타일 데이터의 사용자 지정

대시보드의 데이터는 지난 24시간 동안의 작업을 자동으로 표시합니다. 이 타일에 다른 시간 범위를 표시하려면 다음 단계를 수행합니다.

1. 바로 가기 메뉴나 타일의 왼쪽 위 모서리에 있는 ![필터 아이콘](./media/azure-portal-dashboards/dashboard-filter.png) 필터에서 **타일 데이터 사용자 지정** 을 선택합니다.

    ![타일 바로 가기 메뉴 스크린샷](./media/azure-portal-dashboards/dashboard-customize-tile-data.png)

1. **타일 수준에서 대시보드 시간 설정 재정의** 로 확인란을 선택합니다.

    ![타일 시간 설정을 구성하는 대화 상자 스크린샷](./media/azure-portal-dashboards/dashboard-override-time-settings.png)

1. 이 타일에 표시할 시간 범위를 정합니다. 지난 30분에서 지난 30일 사이에서 선택하거나 사용자 지정 범위를 정의할 수 있습니다.

1. 표시할 시간 세분성을 선택합니다. 1분에서 1개월까지 어디든 표시할 수 있습니다.

1. **적용** 을 선택합니다.

## <a name="delete-a-tile"></a>타일 삭제

대시보드에서 타일을 제거하려면 다음 단계를 수행합니다.

* 타일의 오른쪽 위 모서리에 있는 바로 가기 메뉴를 선택한 다음 **대시보드에서 제거** 를 선택합니다. 또는,

* ![편집 아이콘 ](./media/azure-portal-dashboards/dashboard-edit-icon.png) **편집** 을 선택해 사용자 지정 모드로 전환합니다. 타일의 오른쪽 위 모서리를 마우스로 가리킨 다음 ![삭제 아이콘](./media/azure-portal-dashboards/dashboard-delete-icon.png)을 선택해 대시보드에서 타일을 제거합니다.

   ![대시보드에서 타일을 제거하는 방법을 보여 주는 스크린샷](./media/azure-portal-dashboards/dashboard-delete-tile.png)

## <a name="clone-a-dashboard"></a>대시보드 복제

기존 대시보드를 새 대시보드의 템플릿으로 사용하려면 다음 단계를 수행합니다.

1. 대시보드 보기에 복사하려는 대시보드가 표시되는지 확인합니다.

1. 페이지 머리글에서 ![복제 아이콘](./media/azure-portal-dashboards/dashboard-clone.png) **복제** 를 선택합니다.

1. 대시보드 복사본(*대시보드 이름의* **복제본**)이 편집 모드에서 열립니다. 이 문서의 이전 단계를 이용해 대시보드 이름을 바꾸고 사용자 지정합니다.

## <a name="publish-and-share-a-dashboard"></a>대시보드 게시 및 공유

대시보드를 만들 때는 기본적으로 비공개입니다. 즉, 사용자 자신만이 이를 볼 수 있는 유일한 사용자입니다. 다른 사용자가 대시보드를 사용할 수 있도록 하려면 해당 대시보드를 게시하고 공유할 수 있습니다. 자세한 내용은 [Azure 역할 기반 액세스 제어를 사용하여 Azure 대시보드 공유](azure-portal-dashboard-share-access.md)를 참조하세요.

### <a name="open-a-shared-dashboard"></a>공유 대시보드 열기

공유 대시보드를 찾아서 열려면 다음 단계를 수행합니다.

1. 대시보드 이름 옆의 화살표를 선택합니다.

1. 표시된 대시보드 목록에서 선택합니다. 열려는 대시보드가 목록에 없는 경우.

    1. **모든 대시보드 찾아보기** 를 선택합니다.

        ![대시보드 선택 메뉴 스크린샷](./media/azure-portal-dashboards/dashboard-browse.png)

    1. **유형** 필드에서 **공유 대시보드** 를 선택합니다.

        ![모든 대시보드 선택 메뉴의 스크린샷](./media/azure-portal-dashboards/dashboard-browse-all.png)

    1. 하나 이상의 구독을 선택합니다. 텍스트를 입력하여 이름별로 대시보드를 필터링할 수도 있습니다.

    1. 공유 대시보드 목록에서 대시보드를 선택합니다.

## <a name="delete-a-dashboard"></a>대시보드 삭제

프라이빗 또는 공유 대시보드를 영구적으로 삭제하려면 다음 단계를 수행합니다.

1. 대시보드 이름 옆의 목록에서 삭제하려는 대시보드를 선택합니다.

1. 페이지 머리글에서 ![삭제 아이콘](./media/azure-portal-dashboards/dashboard-delete-icon.png) **삭제** 를 선택합니다.

1. 개인 대시보드의 경우 확인 대화 상자에서 **확인** 을 선택해 대시보드를 제거합니다. 공유 대시보드의 경우 확인 대화 상자에서 확인란을 선택하여 게시된 대시보드가 더 이상 다른 사용자에게 표시되지 않음을 확인합니다. 그런 다음 **확인** 을 선택합니다.

    ![삭제 확인 스크린샷](./media/azure-portal-dashboards/dashboard-delete-dash.png)

## <a name="recover-a-deleted-dashboard"></a>삭제된 대시보드 복구

전체 Azure 클라우드에 있는 경우 Azure Portal에서 _게시된_ 대시보드를 삭제하면 삭제 후 14일 내에 해당 대시보드를 복구할 수 있습니다. 관련 정보는 [Azure Portal에서 삭제된 대시보드 복구](recover-shared-deleted-dashboard.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

* [Azure 역할 기반 액세스 제어를 사용하여 Azure 대시보드 공유](azure-portal-dashboard-share-access.md)
* [Azure 대시보드를 프로그래밍 방식으로 만들기](azure-portal-dashboards-create-programmatically.md)
