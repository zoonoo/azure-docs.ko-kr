---
title: StorSimple Snapshot Manager MMC 메뉴 작업 | Microsoft Docs
description: StorSimple 스냅샷 관리자에서 표준 Microsoft Management Console(MMC) 메뉴 작업을 사용하는 방법에 대해 설명합니다.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 78ef81af-0d3a-4802-be54-ad192f9ac8a6
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: alkohli
ms.openlocfilehash: 01064c3668b673baea7aedd9a65c92b03c48dc10
ms.sourcegitcommit: 814778c54b59169c5899199aeaa59158ab67cf44
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2020
ms.locfileid: "90056006"
---
# <a name="use-the-mmc-menu-actions-in-storsimple-snapshot-manager"></a>StorSimple 스냅샷 관리자에서 MMC 메뉴 작업 사용

## <a name="overview"></a>개요
StorSimple 스냅샷 관리자에서 모든 작업 메뉴에 나열된 다음과 같은 작업 및 **작업** 창의 모든 변형을 확인할 수 있습니다.

* View
* 여기에서 창 새로 만들기 
* 새로 고침 
* 목록 내보내기 
* 도움말 

이러한 작업은 Microsoft Management Console(MMC)의 일부로, StorSimple 스냅샷 관리자 특정 메뉴가 아닙니다. 이 자습서에서는 이러한 작업 및 StorSimple 스냅샷 관리자에서 각 작업을 사용하는 방법에 대해 설명합니다.

## <a name="view"></a>View
**뷰** 옵션을 사용하여 **결과** 창 뷰와 콘솔 창 뷰를 변경할 수 있습니다. 

#### <a name="to-change-the-results-pane-view"></a>결과 창 보기를 변경하려면
1. 바탕 화면 아이콘을 클릭하여 StorSimple 스냅샷 관리자를 시작합니다.
2. **범위** 창에서 원하는 노드를 마우스 오른쪽 단추로 클릭하거나 노드를 확장하고 **결과** 창에서 항목을 마우스 오른쪽 단추로 클릭한 다음 **뷰** 옵션을 클릭합니다. 
3. **결과** 창에 나타나는 열을 추가하거나 제거하려면 **열 추가/제거**를 클릭합니다. **열 추가/제거** 대화 상자가 표시됩니다.
   
    ![결과 창에 열 추가 또는 제거](./media/storsimple-snapshot-manager-mmc-menu/HCS_SSM_Add_remove_columns.png) 
4. 다음과 같이 양식을 완료합니다.
   
   * **사용 가능** 열 목록에서 항목을 선택하고 **추가**를 클릭하여 **표시된 열** 목록에 추가합니다. 
   * **표시된 열** 목록에서 항목을 클릭하고 **제거**를 클릭해 목록에서 제거합니다. 
   * **표시된** 열 목록에서 항목을 선택하고 **위로 이동** 또는 **아래로 이동**을 클릭하여 항목을 목록에서 위 또는 아래로 이동합니다. 
   * **기본값 복원**을 클릭하여 기본 **결과** 창 구성으로 되돌아갑니다. 
5. 선택을 완료했으면 **확인**을 클릭합니다. 

#### <a name="to-change-the-console-window-view"></a>콘솔 창 보기를 변경하려면
1. 바탕 화면 아이콘을 클릭하여 StorSimple 스냅샷 관리자를 시작합니다.
2. **범위** 창에서 원하는 노드를 마우스 오른쪽 단추로 클릭하고 **뷰**를 클릭한 다음 **사용자 지정**을 클릭합니다. **사용자 지정** 대화 상자가 나타납니다.
   
    ![콘솔 창 사용자 지정](./media/storsimple-snapshot-manager-mmc-menu/HCS_SSM_Customize.png) 
3. 확인란을 선택하거나 선택 취소하여 콘솔 창에 항목을 표시하거나 숨깁니다. 선택을 완료했으면 **확인**을 클릭합니다.

## <a name="new-window-from-here"></a>여기에서 창 새로 만들기
**여기에서 창 새로 만들기** 옵션을 사용하여 새 콘솔 창을 열 수 있습니다.

#### <a name="to-open-a-new-console-window"></a>새 콘솔 창을 열려면
1. 바탕 화면 아이콘을 클릭하여 StorSimple 스냅샷 관리자를 시작합니다.
2. I**범위** 창에서 노드를 마우스 오른쪽 단추로 클릭하고 **여기에서 창 새로 만들기**를 클릭합니다. 
   
    선택한 범위만 표시된 새 창이 나타납니다. 예를 들어, **백업 정책** 노드를 마우스 오른쪽 단추로 클릭하면 **범위** 창의 **백업 정책** 노드와 **결과** 창에서 정의된 백업 정책 목록만 표시됩니다. 다음 예제를 참조하세요.
   
    ![여기에서 창 새로 만들기](./media/storsimple-snapshot-manager-mmc-menu/HCS_SSM_NewWindow.png) 

## <a name="refresh"></a>새로 고침
**새로 고침** 작업을 사용해 콘솔 창을 업데이트할 수 있습니다.

#### <a name="to-update-the-console-window"></a>콘솔 창을 업데이트하려면
1. 바탕 화면 아이콘을 클릭하여 StorSimple 스냅샷 관리자를 시작합니다.
2. **범위** 창에서 원하는 노드를 마우스 오른쪽 단추로 클릭하거나 노드를 확장하고 **결과** 창에서 항목을 마우스 오른쪽 단추로 클릭한 다음 **새로 고침**을 클릭합니다. 

## <a name="export-list"></a>목록 내보내기
**목록 내보내기** 작업을 사용하여 목록을 CSV(쉼표로 구분된 값) 파일로 저장할 수 있습니다. 예를 들어 백업 정책 또는 백업 카탈로그 목록을 내보낼 수 있습니다. 그런 다음 분석을 위해 CSV 파일을 스프레드시트 애플리케이션으로 가져올 수 있습니다.

#### <a name="to-save-a-list-in-a-comma-separated-value-csv-file"></a>쉼표로 구분한 값(CSV) 파일로 목록을 저장하려면
1. 바탕 화면 아이콘을 클릭하여 StorSimple 스냅샷 관리자를 시작합니다. 
2. **범위** 창에서 원하는 노드를 마우스 오른쪽 단추로 클릭하거나 노드를 확장하고 **결과** 창에서 항목을 마우스 오른쪽 단추로 클릭한 다음 **목록 내보내기**를 클릭합니다. 
3. **목록 내보내기** 대화 상자가 나타납니다. 다음과 같이 양식을 완료합니다. 
   
   1. **파일 이름** 상자에 CSV 파일 이름을 입력하거나 화살표를 눌러 드롭다운 목록에서 선택합니다.
   2. **파일 형식** 상자에서 화살표를 클릭하고 드롭다운 목록에서 파일 형식을 선택합니다.
   3. 선택한 항목만 저장하려면 행을 선택하고 **선택한 행만 저장** 확인란을 클릭합니다. 내보낸 목록을 모두 저장하려면 **선택한 행만 저장** 확인란의 선택을 취소합니다.
   4. **저장**을 클릭합니다.
      
      ![쉼표로 구분된 값 파일로 목록 내보내기](./media/storsimple-snapshot-manager-mmc-menu/HCS_SSM_Export_List.png) 

## <a name="help"></a>도움말
**도움말** 메뉴를 사용하여 StorSimple 스냅샷 관리자 및 MMC에 사용 가능한 온라인 도움말을 볼 수 있습니다.

#### <a name="to-view-available-online-help"></a>사용 가능한 온라인 도움말을 보려면
1. 바탕 화면 아이콘을 클릭하여 StorSimple 스냅샷 관리자를 시작합니다.
2. **범위** 창에서 원하는 노드를 마우스 오른쪽 단추로 클릭하거나 노드를 확장하고 **결과** 창의 항목을 마우스 오른쪽 단추로 클릭한 다음 **도움말**을 클릭합니다. 

## <a name="next-steps"></a>다음 단계
* [StorSimple 스냅샷 관리자 사용자 인터페이스](storsimple-use-snapshot-manager.md)에 대해 자세히 알아봅니다.
* [StorSimple 스냅샷 관리자를 사용하여 StorSimple 솔루션을 관리](storsimple-snapshot-manager-admin.md)하는 방법을 자세히 알아봅니다.

