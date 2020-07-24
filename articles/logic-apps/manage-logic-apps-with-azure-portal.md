---
title: Azure Portal에서 논리 앱 관리
description: Azure Portal를 사용 하 여 논리 앱을 편집, 사용, 사용 안 함 또는 삭제 합니다.
services: logic-apps
ms.suite: integration
author: lauradolan
ms.author: ladolan
ms.reviewer: estfan, jonfan, logicappspm
ms.topic: article
ms.custom: mvc
ms.date: 07/20/2020
ms.openlocfilehash: d50f577a7170982be004cc8957114f79675fbc6e
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87078614"
---
# <a name="manage-logic-apps-in-the-azure-portal"></a>Azure Portal에서 논리 앱 관리

[Azure Portal](https://portal.azure.com) 또는 [Visual Studio](manage-logic-apps-with-visual-studio.md)를 사용 하 여 논리 앱을 관리할 수 있습니다. 이 문서에서는 Azure Portal에서 논리 앱을 편집, 사용 안 함, 사용 또는 삭제 하는 방법을 보여 줍니다. Azure Logic Apps를 처음 접하는 경우 [Azure Logic Apps](logic-apps-overview.md)을 참조 하세요.

## <a name="prerequisites"></a>필수 구성 요소

* Azure 구독 Azure 구독이 없는 경우 [체험 Azure 계정에 등록](https://azure.microsoft.com/free/)합니다.

* 기존 논리 앱입니다. Azure Portal에서 논리 앱을 만드는 방법을 알아보려면 [빠른 시작: Azure Logic Apps를 사용 하 여 첫 번째 워크플로 만들기-Azure Portal](quickstart-create-first-logic-app-workflow.md)를 참조 하세요.

<a name="find-logic-app"></a>

## <a name="find-your-logic-apps"></a>논리 앱 찾기

논리 앱을 찾아서 열려면 다음 단계를 수행 합니다.

1. Azure 계정을 사용하여 [Azure Portal](https://portal.azure.com) 에 로그인합니다.

1. Azure search 표시줄에서 `logic apps` 를 입력 하 고 **Logic Apps**를 선택 합니다.

   ![검색 창에서 검색 하 고 선택한 Logic Apps 있는 Azure Portal 메뉴의 스크린샷](./media/manage-logic-apps-with-azure-portal/find-select-logic-apps.png)

1. **Logic Apps** 페이지에서 관리 하려는 논리 앱을 찾아 선택 합니다.

   논리 앱의 **개요** 창이 열리면 **Logic Apps** 페이지에 표시 되는 목록을 다음과 같은 방식으로 필터링 할 수 있습니다.

   * 이름으로 논리 앱 검색
   * 구독, 리소스 그룹, 위치 및 태그로 논리 앱 필터링
   * 리소스 그룹, 유형, 구독 및 위치별 논리 앱 그룹화

## <a name="view-logic-app-properties"></a>논리 앱 속성 보기

1. Azure Portal에서 [논리 앱을 찾아서 엽니다](#find-logic-app).

1. 논리 앱의 메뉴에서 **설정**아래에 있는 **속성**을 선택 합니다.

1. **속성** 창에서 논리 앱에 대 한 다음 정보를 보고 복사할 수 있습니다.

   * **이름**
   * **리소스 ID**
   * **리소스 그룹**
   * **위치**
   * **형식** 
   * **Subscription Name**
   * **구독 ID**
   * **액세스 끝점**
   * **런타임 나가는 IP 주소**
   * **끝점 IP 주소 액세스**
   * **커넥터 나가는 IP 주소**

## <a name="disable-or-enable-logic-apps"></a>논리 앱 사용 또는 사용 안 함

Azure Portal에서 한 번에 [하나의 논리 앱](#disable-enable-single-logic-app) 또는 [여러 논리](#disable-or-enable-multiple-logic-apps) 앱을 사용 하거나 사용 하지 않도록 설정할 수 있습니다. [Visual Studio에서 논리 앱을 사용 하거나 사용 하지 않도록](manage-logic-apps-with-visual-studio.md#disable-or-enable-logic-app)설정할 수도 있습니다.

논리 앱을 사용 하지 않도록 설정 하면 워크플로 인스턴스에 영향을 주고 다음과 같은 방식으로 실행 됩니다.

* 진행 중인 모든 실행 및 보류 중인 실행은 완료 될 때까지 계속 됩니다. 이러한 실행의 수에 따라이 프로세스에 다소 시간이 걸릴 수 있습니다.

* Logic Apps 엔진은 새 워크플로 인스턴스를 만들거나 실행 하지 않습니다.

* 트리거는 다음에 조건이 충족 될 때 발생 하지 않습니다.

* 트리거 상태는 논리 앱이 중지 된 지점을 기억 합니다. 따라서 논리 앱을 다시 사용 하도록 설정 하면 마지막 실행 이후 처리 되지 않은 모든 항목에 대해 트리거가 발생 합니다.

  논리 앱이 마지막 실행 이후 처리 되지 않은 항목에서 발생 하지 않도록 하려면 논리 앱을 다시 사용 하도록 설정 하기 전에 트리거의 상태를 지우십시오.

  1. Azure Portal에서 [논리 앱을 찾아서 엽니다](#find-logic-app).

  1. 논리 앱 트리거의 모든 부분을 편집 합니다.

  1. 변경 내용을 저장합니다. 이 단계는 트리거의 현재 상태를 다시 설정 합니다.

  1. [논리 앱을 다시 사용 하도록 설정](#disable-enable-single-logic-app)합니다.

<a name="disable-enable-single-logic-app"></a>

### <a name="disable-or-enable-single-logic-app"></a>단일 논리 앱 사용 또는 사용 안 함

1. Azure Portal에서 [논리 앱을 찾아서 엽니다](#find-logic-app).

1. 논리 앱의 메뉴에서 **개요**를 선택 합니다. 다음 옵션 중에서 선택 합니다.

   * 도구 모음에서 **사용 안 함**을 선택합니다.

     ![사용 안 함 단추를 선택 하 여 논리 앱의 도구 모음 스크린샷](./media/manage-logic-apps-with-azure-portal/disable-single-logic-app.png)

     논리 앱이 이미 사용 하지 않도록 설정 된 경우에는 **사용** 옵션만 표시 됩니다.

   * 도구 모음에서 **사용**을 선택 합니다.

     ![활성화 단추가 선택 된 것을 보여 주는 논리 앱의 도구 모음 스크린샷](./media/manage-logic-apps-with-azure-portal/enable-single-logic-app.png)

     논리 앱을 이미 사용 하도록 설정한 경우에는 **사용 안 함** 옵션만 표시 됩니다. 

   Azure Portal는 작업의 성공 또는 실패 여부를 확인 하는 기본 Azure 도구 모음에 대 한 알림을 표시 합니다.

   ![작업 상태를 확인 하는 알림을 보여 주는 Azure Portal의 스크린샷](./media/manage-logic-apps-with-azure-portal/operation-confirmation-notification.png)

<a name="disable-or-enable-multiple-logic-apps"></a>

### <a name="disable-or-enable-multiple-logic-apps"></a>여러 논리 앱 사용 또는 사용 안 함

1. Azure Portal에서 사용 하지 않도록 설정 하거나 사용 하도록 설정 하려는 [논리 앱을 찾습니다](#find-logic-app) .

1. 논리 앱을 현재 사용할 수 있는지 여부를 확인 하려면 **Logic Apps** 페이지에서 해당 논리 앱의 **상태** 열을 검토 합니다. 

   ![상태 열을 기준으로 정렬 된 논리 앱 목록을 보여 주는 Azure Portal Logic Apps 페이지의 스크린샷](./media/manage-logic-apps-with-azure-portal/view-logic-app-status.png)

   **상태** 열이 표시 되지 않는 경우 **Logic Apps** 도구 모음에서 **미리 보기 시도**를 선택 합니다.

   ![미리 보기 시도 단추를 선택 하 여 Azure Portal Logic Apps 페이지의 스크린샷](./media/manage-logic-apps-with-azure-portal/select-try-preview.png)

1. 확인란 열에서 사용 하거나 사용 하지 않을 논리 앱을 선택 합니다. 도구 모음에서 **사용 안 함** 또는 **사용**을 선택 합니다.

   ![여러 논리 앱에 대 한 사용 및 사용 안 함 단추를 표시 하는 Azure Portal Logic Apps 페이지의 스크린샷](./media/manage-logic-apps-with-azure-portal/enable-disable-multiple-logic-apps.png)

1. 확인 상자가 표시 되 면 **예** 를 선택 하 여 계속 합니다.

   Azure Portal는 작업의 성공 또는 실패 여부를 확인 하는 기본 Azure 도구 모음에 대 한 알림을 표시 합니다.

## <a name="delete-logic-apps"></a>논리 앱 삭제

Azure Portal에서 [단일 논리 앱을 삭제](#delete-single-logic-app) 하거나 [여러 논리](#delete-multiple-logic-apps) 앱을 동시에 삭제할 수 있습니다. [Visual Studio에서 논리 앱을 삭제할](manage-logic-apps-with-visual-studio.md#delete-your-logic-app)수도 있습니다.

논리 앱을 삭제 하면 다음과 같은 방식으로 워크플로 인스턴스에 영향을 줍니다.

* 진행 중인 모든 실행 및 보류 중인 실행은 완료 될 때까지 계속 됩니다. 이러한 실행의 수에 따라이 프로세스에 다소 시간이 걸릴 수 있습니다.

* Logic Apps 엔진은 새 워크플로 인스턴스를 만들거나 실행 하지 않습니다.

> [!NOTE]
> 자식 논리 앱을 삭제 하 고 다시 만드는 경우 부모 논리 앱을 다시 저장 해야 합니다. 다시 만들어진 자식 앱은 다른 메타 데이터를 포함 합니다.
> 자식을 다시 만든 후 부모 논리 앱을 다시 저장 하지 않으면 자식 논리 앱에 대 한 호출이 실패 하 고 "권한 없음" 오류가 표시 됩니다. 이 동작은 통합 계정에서 아티팩트를 사용 하거나 Azure 함수를 호출 하는 것과 같은 부모-자식 논리 앱에 적용 됩니다.

<a name="delete-single-logic-app"></a>

### <a name="delete-single-logic-app"></a>단일 논리 앱 삭제

1. Azure Portal에서 [논리 앱을 찾아서 엽니다](#find-logic-app).

1. 논리 앱의 메뉴에서 **개요**를 선택 합니다. 논리 앱의 도구 모음에서 **삭제**를 선택 합니다.

   ![선택 된 삭제 단추를 표시 하는 논리 앱의 도구 모음 스크린샷](./media/manage-logic-apps-with-azure-portal/delete-single-logic-app.png)

1. 확인 상자가 표시 되 면 논리 앱의 이름을 입력 하 고 **삭제**를 선택 합니다.

   ![단일 논리 앱 삭제를 확인 하는 Logic Apps 프롬프트 스크린샷](./media/manage-logic-apps-with-azure-portal/delete-confirmation-single-logic-app.png)

   Azure Portal는 작업의 성공 또는 실패 여부를 확인 하는 기본 Azure 도구 모음에 대 한 알림을 표시 합니다.

<a name="delete-multiple-logic-apps"></a>

### <a name="delete-multiple-logic-apps"></a>여러 논리 앱 삭제

1. Azure Portal에서 [삭제 하려는 논리 앱을 찾습니다](#find-logic-app).

1. 확인란 열에서 삭제 하려는 논리 앱을 선택 합니다. 도구 모음에서 **삭제**를 선택합니다.

   ![삭제 하도록 선택한 목록에서 여러 논리 앱을 보여 주는 Logic Apps 페이지의 스크린샷](./media/manage-logic-apps-with-azure-portal/delete-multiple-logic-apps.png)

1. 확인 상자가 표시 되 면를 입력 하 `yes` 고 **삭제**를 선택 합니다.

   ![여러 논리 앱의 삭제를 확인 하는 Logic Apps 프롬프트의 스크린샷](./media/manage-logic-apps-with-azure-portal/delete-confirmation-multiple-logic-apps.png)

   Azure Portal는 작업의 성공 또는 실패 여부를 확인 하는 기본 Azure 도구 모음에 대 한 알림을 표시 합니다.

<a name="manage-logic-app-versions"></a>

## <a name="manage-logic-app-versions"></a>논리 앱 버전 관리

논리 앱의 버전 제어를 위해 Azure Portal를 사용할 수 있습니다. 논리 앱의 버전 기록을 확인 하 고 이전 버전을 승격할 수 있습니다.

<a name="find-version-history"></a>

### <a name="find-and-view-previous-versions"></a>이전 버전 찾기 및 보기

1. Azure Portal에서 관리 하려는 [논리 앱을 찾습니다](#find-logic-app).

1. 논리 앱 메뉴의 **개발 도구**아래에서 **버전**을 선택 합니다.

   ![개발 도구에서 선택한 버전 페이지를 표시 하는 Azure Portal에서 논리 앱의 스크린샷](./media/manage-logic-apps-with-azure-portal/logic-apps-menu-versions.png)

1. 목록에서 관리할 논리 앱의 **버전** 을 선택 합니다. 검색 표시줄에 **버전** 식별자를 입력 하 여 목록을 필터링 할 수 있습니다.

1. **기록 버전** 페이지에는 읽기 전용 모드에서 이전 버전의 세부 정보가 표시 됩니다. Logic Apps **디자이너** 와 **코드 보기** 모드 사이에서 선택할 수 있습니다.

   ![코드 보기 및 디자이너 뷰 옵션을 보여 주는 Logic Apps 기록 버전 페이지의 스크린샷](./media/manage-logic-apps-with-azure-portal/history-version.png)

<a name="promote-previous-versions"></a>

### <a name="promote-previous-versions"></a>이전 버전 수준 올리기

1. 논리 앱의 버전 기록에서 [승격할 버전을 찾아 선택](#find-version-history)합니다.

1. **기록 버전** 페이지에서 **수준 올리기**를 선택 합니다.

   ![이전 버전을 승격 하는 단추를 보여 주는 논리 앱 버전 기록의 스크린샷](./media/manage-logic-apps-with-azure-portal/promote-button.png)

1. 열려 있는 **Logic Apps Designer** 페이지에서 필요에 따라 승격 하는 버전을 편집 합니다. **디자이너** 와 **코드 보기** 모드 간을 전환할 수 있습니다. **매개 변수**, **템플릿**및 **커넥터**를 업데이트할 수도 있습니다.

   ![이전 버전의 논리 앱을 승격 하는 단추를 보여 주는 Logic Apps 디자이너의 스크린샷](./media/manage-logic-apps-with-azure-portal/promote-page.png)

1. 업데이트를 저장 하 고 이전 버전의 수준을 올리기를 완료 하려면 **저장**을 선택 합니다. 또는 변경을 취소 하려면 **취소**를 선택 합니다. 

   [논리 앱의 버전 기록을 다시 보면](#find-version-history) 승격 된 버전이 목록의 맨 위에 표시 되 고 새 식별자가 포함 됩니다.

## <a name="next-steps"></a>다음 단계

* [논리 앱 모니터링](monitor-logic-apps.md)
