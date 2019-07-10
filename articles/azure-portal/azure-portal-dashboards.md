---
title: Azure Portal 대시보드 만들기 및 공유 | Microsoft Docs
description: 이 문서에는 만들기, 사용자 지정, 게시 및 Azure portal에서 대시보드를 공유 하는 방법을 설명 합니다.
services: azure-portal
documentationcenter: ''
author: sewatson
manager: mtillman
editor: tysonn
ms.assetid: ff422f36-47d2-409b-8a19-02e24b03ffe7
ms.service: azure-portal
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 07/01/2019
ms.author: kfollis
ms.openlocfilehash: 8dd1349ca9ab62484eb6693291e3b869ff079dc1
ms.sourcegitcommit: 084630bb22ae4cf037794923a1ef602d84831c57
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/03/2019
ms.locfileid: "67537357"
---
# <a name="create-and-share-dashboards-in-the-azure-portal"></a>Azure Portal에서 대시보드 만들기 및 공유

대시보드는 클라우드 리소스의 Azure portal에서 포커스가 있는, 구성 된 보기를 만들 수 있는 방법을 제공 합니다. 대시보드를 사용 하 여 신속 하 게 일상 작업에 대 한 작업을 시작 하 고 수 있는 리소스를 모니터링 작업 영역으로 합니다.  예를 들어 프로젝트, 작업 또는 사용자 역할에 따라 사용자 지정 대시보드를 빌드하십시오.  Azure portal 시작 점으로 기본 대시보드를 제공합니다. 수 기본 대시보드를 편집, 추가 대시보드를 사용자 지정 및 게시 만들고 다른 사용자에 게 사용할 수 있게 하려면 대시보드를 공유 합니다. 이 문서에서는 새 대시보드를 만들고, 인터페이스를 사용자 지정, 게시 및 대시보드를 공유 하는 방법을 설명 합니다.

## <a name="create-a-new-dashboard"></a>새 대시보드 만들기

이 예제에서는 새 개인 대시보드를 만들고 이름을 할당 합니다. 시작 하려면 다음이 단계를 수행 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 선택 **대시보드** 왼쪽된 세로 막대의 위 섹션에서. 기본 보기는 이미 대시보드로 설정할 수 있습니다.
1. 선택 **+ 새 대시보드**합니다.

    ![기본 대시보드의 스크린 샷](./media/azure-portal-dashboards/dashboard-new.png)

4. 열립니다는 **타일 갤러리**, 타일 및 타일을 정렬할 수 있습니다 빈 그리드가 선택할 수 있는 합니다.

    ![타일 갤러리 및 빈 그리드의 스크린 샷](./media/azure-portal-dashboards/dashboard-name.png)

5. 선택 된 **내 대시보드** 대시보드에서 텍스트 레이블을 지정 하 고 사용자 지정 대시보드를 쉽게 식별 하는 데 도움이 되는 이름을 입력 합니다.
1. 선택 **사용자 지정 완료** 종료 하려면 페이지 머리글에 편집 모드입니다.

대시보드 보기에는 이제 빈 대시보드에 표시 됩니다. 사용 가능한 대시보드를 보려면 대시보드 이름 옆의 드롭다운 목록 선택 – 목록에서 다른 사용자가 만들고 공유한 대시보드를 포함할 수 있습니다.

## <a name="edit-a-dashboard"></a>대시보드 편집

이제 편집 하십시오. 대시보드를 추가, 크기 조정 및 Azure 리소스를 나타내는 타일을 정렬 합니다.

### <a name="add-tiles"></a>타일 추가

타일에 대시보드를 추가 하려면 다음이 단계를 수행 합니다.
1. 선택 ![편집 아이콘](./media/azure-portal-dashboards/dashboard-edit-icon.png) **편집** 페이지 머리글에서 합니다.

    ![편집을 강조 표시 하는 대시보드의 스크린 샷](./media/azure-portal-dashboards/dashboard-edit.png)

2. 찾아보기 합니다 **타일 갤러리** 또는 검색 필드를 사용 하 여 타일을 찾습니다.
1. 선택 **추가** 기본 크기 및 위치를 사용 하 여 대시보드를 자동으로 타일을 추가 합니다. 또는 타일을 그리드로 끌어서 원하는 위치에 놓습니다.

명령 모음에서 압정 아이콘을 포함 하는 여러 리소스 페이지 (라고도 "블레이드에서"). 아이콘을 선택 하면 현재 활성 상태인 대시보드 원본 페이지를 나타내는 타일을 고정 됩니다. 이 타일을 대시보드에 추가 하는 대체 방법입니다.

![고정 아이콘을 사용 하 여 명령 모음 페이지의 스크린샷](./media/azure-portal-dashboards/dashboard-pin-blade.png)

> [!TIP]
> 둘 이상의 조직이 사용 하 여 작업 하는 경우 추가 합니다 **조직 id** 명확 하 게는 조직 리소스를 표시 하도록 대시보드 타일에 속해야 합니다.
>
>
### <a name="resize-or-rearrange-tiles"></a>크기를 조정 하거나 타일을 다시 정렬
타일의 크기를 변경 하거나 대시보드에서 타일을 다시 정렬 하려면 다음이 단계를 수행 합니다.

1. 선택 ![편집 아이콘](./media/azure-portal-dashboards/dashboard-edit-icon.png) **편집** 페이지 머리글에서 합니다.
1. 타일의 오른쪽 위 모서리에서 상황에 맞는 메뉴를 선택 합니다. 그런 다음 타일 크기를 선택 합니다. 또한 타일이 모든 크기를 지 원하는 타일을 원하는 크기로 끕니다 수 있는 오른쪽 아래 모퉁이에 "핸들"를 포함 합니다.

   ![타일 크기 메뉴가 열려 있는 대시보드의 스크린 샷](./media/azure-portal-dashboards/dashboard-tile-resize.png)

3. 타일을 선택 하 고 대시보드를 정렬 하려면 그리드에서 새 위치로 끕니다.

### <a name="additional-tile-configuration"></a>추가 타일 구성

일부 타일에 표시할 정보를 표시 하려면 추가 구성을 해야 합니다. 예를 들어 합니다 **메트릭 차트** 타일에서 메트릭을 표시 하도록 해야 **Azure Monitor**합니다. 또한 대시보드의 기본 시간 설정을 재정의할 수 타일 데이터를 사용자 지정할 수 있습니다.

표시 설정 해야 하는 타일을 **구성 타일** 타일을 사용자 지정할 때까지 배너. 해당 배너를 선택한 다음 필요한 설치 프로세스를 수행 합니다.

![구성 해야 하는 타일의 스크린샷](./media/azure-portal-dashboards/dashboard-configure-tile.png)

> [!NOTE]
> Markdown 타일을 대시보드에 사용자 지정, 정적 콘텐츠를 표시할 수 있습니다. 이 기본 지침, 이미지, 하이퍼링크, 집합이 정도 연락처 정보. Markdown 타일을 사용 하는 방법에 대 한 자세한 내용은 참조 하세요. [사용자 지정 markdown 타일을 사용 하 여](azure-portal-markdown-tile.md)입니다.
>
>
### <a name="customize-tile-data"></a>타일 데이터를 사용자 지정

대시보드의 데이터는 자동으로 지난 24 시간에 대 한 작업을 표시합니다. 방금이 타일에 대 한 다른 시간 범위를 표시 하려면 다음이 단계를 수행 합니다.

1. 선택 된 ![필터 아이콘](./media/azure-portal-dashboards/dashboard-filter.png) 선택 타일의 왼쪽된 위 모퉁이에서 필터 아이콘 **사용자 지정 타일 데이터** 상황에 맞는 메뉴에서.

   ![타일 상황에 맞는 메뉴의 스크린샷](./media/azure-portal-dashboards/dashboard-customize-tile-data.png)

2. 확인란을 선택 **타일 수준에서 대시보드 시간 설정 재정의**합니다.

   ![타일 시간 설정을 구성 하려면 대화 상자의 스크린샷](./media/azure-portal-dashboards/dashboard-override-time-settings.png)

3. 이 타일에 대 한 표시 하는 시간 범위를 선택 합니다. 지난 30 분에서 지난 30 일 동안 하도록 선택할 수도 있고 사용자 지정 범위를 정의할 수 있습니다.
1. 표시할 시간 단위를 선택 합니다. 표시할 수 있습니다 아무 곳 이나 1 분 단위로에서 1 개월입니다.
1. **적용**을 선택합니다.

## <a name="delete-a-tile"></a>타일 삭제

대시보드에서 타일을 제거 하려면 다음이 단계를 수행 합니다.

* 타일의 오른쪽 위 모서리에서 상황에 맞는 메뉴를 선택한 다음 선택 **대시보드에서 제거**합니다. 또는
* 선택 ![편집 아이콘](./media/azure-portal-dashboards/dashboard-edit-icon.png) **편집** 사용자 지정 모드로 전환 합니다. 타일의 오른쪽 위 모퉁이 포인터로 가리킨 다음를 선택 합니다 ![삭제 아이콘](./media/azure-portal-dashboards/dashboard-delete-icon.png) 대시보드에서 타일을 제거 하는 아이콘을 삭제 합니다.

   ![대시보드에서 타일을 제거 하는 방법을 보여 주는 스크린샷](./media/azure-portal-dashboards/dashboard-delete-tile.png)

## <a name="clone-a-dashboard"></a>대시보드 복제

기존 대시보드 템플릿으로 새 대시보드를 사용 하려면 다음이 단계를 수행 합니다.

1. 대시보드 보기에 대시보드를 복사 하려는 보여주는 있는지 확인 합니다.
1. 페이지 머리글에서 선택 ![복제 아이콘](./media/azure-portal-dashboards/dashboard-clone.png) **복제**합니다.
1. 명명 된 대시보드의 복사본 "복제 *에 대시보드 이름을*" 편집 모드에서 열립니다. 이 문서의 이전 단계를 사용 하 여 이름을 바꾸고 대시보드를 사용자 지정 합니다.

## <a name="publish-and-share-a-dashboard"></a>게시 및 대시보드를 공유 합니다.

대시보드를 만든 경우에 것을 볼 수 있는 유일한 경우는 기본적으로 전용. 대시보드를 다른 사람에 게 사용할 수 있도록, 하려면 다른 사용자와 공유할 수 있습니다. 먼저 Azure 리소스로 대시보드를 게시 해야 합니다. 를 게시 및 사용자 지정 대시보드를 공유 하려면 다음이 단계를 수행 합니다.

1. 선택 ![공유 아이콘](./media/azure-portal-dashboards/dashboard-share-icon.png) **공유** 페이지 머리글에서 합니다. 합니다 **공유 + 액세스 제어** 폼이 나타납니다.
1. 올바른 대시보드 이름이 표시 되는지 확인 합니다.
1. 선택 된 **구독 이름**합니다. 구독에 대 한 액세스를 사용 하 여 사용자는 공유 대시보드를 사용할 수 있습니다. 개별 타일을 나타내는 리소스에 대 한 액세스는 Azure 역할 기반 access control에서 결정 됩니다.
1. 선택한 구독에 대 한 '대시보드' 리소스 그룹에이 대시보드를 게시 하려면이 확인란을 선택 합니다. 또는 확인란의 선택을 취소 하 고 대신 기존 리소스 그룹에 게시 하려면 선택 합니다.
1. 대시보드 리소스에 대 한 위치를 선택 합니다. 다른 리소스를 사용 하 여 대시보드를 배치 하는 것이 좋습니다. 참고: 기존 리소스 그룹에서을 선택 하면 대시보드는 해당 리소스 그룹을 사용 하 여 자동으로 배치 합니다.
1. **게시**를 선택합니다.

   ![대시보드 게시 대화 상자 스크린샷](./media/azure-portal-dashboards/dashboard-publish.png)

### <a name="set-access-control-on-a-shared-dashboard"></a>공유 대시보드 액세스 제어 설정

대시보드를 게시 한 후 다음이 단계를 수행 하 여 대시보드에 대 한 액세스 권한이 있는 사용자 관리:

1. 에 **공유 + 액세스 제어** 창 **사용자 관리**합니다.

   ![대화를 제어 하는 대시보드 공유 + 액세스 스크린샷](./media/azure-portal-dashboards/dashboard-share-access-control.png)

2. 합니다 **Access Control** 페이지가 열립니다. 이 페이지에서는 사용자에 대 한 액세스 수준을 검토 하거나 새 역할 할당을 추가 합니다. 역할 할당을 여기에 추가 하면 대시보드에 대 한 사용 권한을 부여 하는 합니다.

> [!NOTE]
> 타일은 조직에서 리소스의 대표 보기입니다. 리소스에 대 한 액세스는 역할 기반 액세스 제어 할당을 통해 관리 및 사용 권한은 리소스까지 구독에서 상속 됩니다. 대시보드에 액세스할 수 있도록 대시보드에 표시 되는 리소스에 사용 권한을 자동으로 할당 하지 않습니다. 공유 된 대시보드 및 리소스에 대 한 역할 기반 access control 사용 권한에 대 한 자세한 내용은 참조 하세요. [역할 기반 액세스 제어를 사용 하 여 대시보드를 공유](azure-portal-dashboard-share-access.md)합니다.

### <a name="open-a-shared-dashboard"></a>공유 대시보드 열기

찾고 공유 대시보드를 열고 다음을 수행 합니다.

1. 대시보드 이름 옆에 있는 드롭다운을 선택 합니다.
1. 대시보드 표시 된 목록에서 선택 하거나 **모든 대시보드 찾아보기** 열려는 대시보드 목록에 없는 경우.

   ![대시보드 선택 메뉴 스크린샷](./media/azure-portal-dashboards/dashboard-browse.png)

3. 에 **형식** 필드를 선택한 **대시보드를 공유**합니다.
1. 하나 이상의 구독을 선택 합니다. 또한 대시보드를 이름별으로 필터링 할 텍스트를 입력할 수 있습니다.
1. 공유 대시보드 목록에서 대시보드를 선택 합니다.

## <a name="delete-a-dashboard"></a>대시보드 삭제

개인 또는 공유 대시보드를 영구적으로 삭제 하려면 다음이 단계를 수행 합니다.

1. 대시보드 이름 옆의 드롭다운 목록에서 삭제 하려는 대시보드를 선택 합니다.
1. 선택 ![삭제 아이콘](./media/azure-portal-dashboards/dashboard-delete-icon.png) **삭제** 페이지 머리글에서 합니다.
1. 개인 대시보드를 선택 **확인** 대시보드 제거 확인 대화 상자에서. 확인 대화 상자에서 공유 대시보드는 게시 된 대시보드에 더 이상 다른 사용자가 볼 수 있는 확인 확인란을 선택 합니다. 그런 다음 **확인**을 선택합니다.

   ![삭제 확인의 스크린 샷](./media/azure-portal-dashboards/dashboard-delete-dash.png)

## <a name="next-steps"></a>다음 단계

* [역할 기반 액세스 제어를 사용 하 여 대시보드를 공유 합니다.](azure-portal-dashboard-share-access.md)
* [Azure 대시보드를 프로그래밍 방식으로 만들기](azure-portal-dashboards-create-programmatically.md)
