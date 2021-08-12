---
title: Azure Portal에서 논리 앱 관리
description: Azure Portal을 사용하여 논리 앱을 편집, 사용, 사용 안 함 또는 삭제합니다.
services: logic-apps
ms.suite: integration
author: lauradolan
ms.author: ladolan
ms.reviewer: estfan, jonfan, logicappspm
ms.topic: conceptual
ms.custom: mvc
ms.date: 04/23/2021
ms.openlocfilehash: 4c4a3b1e5d165681e921d2fadeadc5dea9633d41
ms.sourcegitcommit: 62e800ec1306c45e2d8310c40da5873f7945c657
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108162908"
---
# <a name="manage-logic-apps-in-the-azure-portal"></a>Azure Portal에서 논리 앱 관리

[Azure Portal](https://portal.azure.com) 또는 [Visual Studio](manage-logic-apps-with-visual-studio.md)를 사용하여 논리 앱을 관리할 수 있습니다. 이 문서에서는 Azure Portal에서 논리 앱을 편집, 사용 안 함, 사용 또는 삭제하는 방법을 보여 줍니다. Azure Logic Apps를 처음 접하는 경우 [Azure Logic Apps란?](logic-apps-overview.md)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독 Azure 구독이 없는 경우 [체험 Azure 계정에 등록](https://azure.microsoft.com/free/)합니다.

* 기존의 논리 앱. Azure Portal에서 논리 앱을 만드는 방법을 알아보려면 [빠른 시작: Azure Logic Apps을 사용하여 첫 번째 워크플로 만들기 - Azure Portal](quickstart-create-first-logic-app-workflow.md)을 참조하세요.

<a name="find-logic-app"></a>

## <a name="find-and-open-a-logic-app"></a>논리 앱 찾기 및 열기

1. Azure 계정을 사용하여 [Azure Portal](https://portal.azure.com) 에 로그인합니다.

1. 포털 검색 상자에 `logic apps`를 입력하고, **논리 앱** 을 선택합니다.

1. 논리 앱 목록에서 목록을 찾아보거나 필터링하여 논리 앱을 찾습니다.

1. 논리 앱을 열려면 관리하려는 앱을 선택합니다.

## <a name="view-logic-app-properties"></a>논리 앱 속성 보기

1. Azure Portal에서 [논리 앱을 찾아서 엽니다](#find-logic-app).

1. 논리 앱의 메뉴에 있는 **설정** 에서 **속성** 을 선택합니다.

1. **속성** 창에서 논리 앱에 대한 다음 정보를 보고 복사할 수 있습니다.

   * **이름**
   * **리소스 ID**
   * **리소스 그룹**
   * **위치**
   * **유형** 
   * **구독 이름**
   * **구독 ID**
   * **액세스 엔드포인트**
   * **런타임 발신 IP 주소**
   * **액세스 엔드포인트 IP 주소**
   * **커넥터 발신 IP 주소**

<a name="disable-enable-logic-apps"></a>

## <a name="disable-or-enable-logic-apps"></a>논리 앱 사용 또는 사용하지 않도록 설정

다음번에 트리거 조건이 충족되더라도 트리거가 발생하지 않게 하려면 논리 앱을 사용하지 않도록 설정합니다. Azure Portal에서 [단일 논리 앱](#disable-enable-single-logic-app) 또는 [동시에 여러 논리 앱](#disable-or-enable-multiple-logic-apps)을 사용하거나 사용하지 않도록 설정할 수 있습니다. 논리 앱을 사용하지 않도록 설정하면 다음과 같은 방식으로 워크플로 인스턴스에 영향을 줍니다.

* Logic Apps 서비스는 완료될 때까지 진행 중인 실행 및 보류 중인 모든 실행을 계속합니다. 볼륨 또는 백로그에 따라 이 프로세스를 완료하는 데 시간이 걸릴 수 있습니다.

* Logic Apps 서비스는 새 워크플로 인스턴스를 만들거나 실행하지 않습니다.

* 트리거는 다음에 조건이 충족될 때 발생하지 않습니다. 하지만 트리거 상태는 논리 앱이 중지된 지점을 기억합니다. 따라서 논리 앱을 다시 활성화하면 마지막 실행 이후 처리되지 않은 모든 항목에 대해 트리거가 발생합니다.

  트리거가 마지막 실행 이후 처리되지 않은 항목에서 트리거되지 않도록 하려면 논리 앱을 다시 활성화하기 전에 트리거의 상태를 지웁니다.

  1. 논리 앱에서 워크플로의 트리거 일부를 편집합니다.
  1. 변경 내용을 저장합니다. 이 단계는 트리거의 현재 상태를 다시 설정합니다.
  1. [논리 앱을 다시 활성화](#disable-enable-single-logic-app)합니다.

<a name="disable-enable-single-logic-app"></a>

### <a name="disable-or-enable-a-single-logic-app"></a>단일 논리 앱을 사용 또는 사용하지 않도록 설정

1. Azure Portal에서 [논리 앱을 찾아서 엽니다](#find-logic-app).

1. 논리 앱 메뉴에서 **개요** 를 선택합니다.

   * 논리 앱을 사용하지 않도록 설정하려면 개요 창 도구 모음에서 **사용 안 함** 을 선택합니다.
   * 논리 앱을 사용하도록 설정하려면 개요 창 도구 모음에서 **사용함** 을 선택합니다.

     > [!NOTE]
     > 논리 앱이 이미 사용하지 않도록 설정되어 있는 경우 **사용** 옵션만 표시됩니다.
     > 논리 앱이 이미 사용하도록 설정되어 있는 경우 **사용 안 함** 옵션만 표시됩니다.

1. 작업의 성공 여부를 확인하려면 주 Azure 도구 모음에서 **알림** 목록(종 모양 아이콘)을 엽니다.

<a name="disable-or-enable-multiple-logic-apps"></a>

### <a name="disable-or-enable-multiple-logic-apps"></a>여러 논리 앱을 사용 또는 사용하지 않도록 설정

1. Azure Portal의 검색 상자에 `logic apps`를 입력하고, **논리 앱** 을 선택합니다.

1. **논리 앱** 페이지에서 논리 앱의 **상태** 열을 검토합니다.

1. 확인란 열에서 중지 또는 시작할 논리 앱을 선택합니다.

   * 선택한 실행 중 논리 앱을 중지하려면 개요 창 도구 모음에서 **사용 안 함/중지** 를 선택합니다. 선택 사항을 확인합니다.
   * 선택한 중지된 논리 앱을 시작하려면 개요 창 도구 모음에서 **사용/시작** 을 선택합니다.

1. 작업의 성공 여부를 확인하려면 주 Azure 도구 모음에서 **알림** 목록(종 모양 아이콘)을 엽니다.

   > [!TIP]
   > 올바른 상태가 표시되지 않는 경우 페이지를 새로 고칩니다.

<a name="delete-logic-apps"></a>

## <a name="delete-logic-apps"></a>논리 앱 삭제

단일 논리 앱 또는 여러 논리 앱을 동시에 삭제할 수 있습니다. 논리 앱을 삭제하면 다음과 같은 방식으로 워크플로 인스턴스에 영향을 줍니다.

* Logic Apps 서비스가 진행 중이거나 보류 중인 실행을 취소하기 위해 전력을 다합니다.

  볼륨이나 백로그가 큰 경우에도 대부분의 실행은 완료 또는 시작 전에 취소됩니다. 그러나 취소 프로세스를 완료하는 데 시간이 걸릴 수 있습니다. 서비스가 취소 프로세스를 진행하는 동안 일부 실행을 선택하여 진행할 수 있습니다.

* Logic Apps 서비스는 새 워크플로 인스턴스를 만들거나 실행하지 않습니다.

* 워크플로를 삭제한 다음, 동일한 워크플로를 다시 만들면 다시 생성된 워크플로에는 삭제된 워크플로와 동일한 메타데이터가 포함되지 않습니다. 삭제된 워크플로를 호출한 모든 워크플로를 다시 저장해야 합니다. 이렇게 하면 호출자가 다시 생성된 워크플로에 대한 올바른 정보를 가져옵니다. 그렇지 않으면 다시 생성된 워크플로에 대한 호출이 `Unauthorized` 오류와 함께 실패합니다. 이 동작은 Azure 함수를 호출하는 워크플로 및 통합 계정에서 아티팩트를 사용하는 워크플로에도 적용됩니다.

1. Azure Portal의 검색 상자에 `logic apps`를 입력하고, **논리 앱** 을 선택합니다.

1. **논리 앱** 목록의 확인란 열에서 삭제할 논리 앱을 하나 또는 여러 개 선택합니다. 도구 모음에서 **삭제** 를 선택합니다.

1. 확인 상자가 표시되면 `yes`를 입력하고 **삭제** 를 선택합니다.

1. 작업의 성공 여부를 확인하려면 주 Azure 도구 모음에서 **알림** 목록(종 모양 아이콘)을 엽니다.

<a name="manage-logic-app-versions"></a>

## <a name="manage-logic-app-versions"></a>논리 앱 버전 관리

논리 앱의 버전 제어를 위해 Azure Portal을 사용할 수 있습니다. 논리 앱의 버전 기록을 확인하고 이전 버전을 승격할 수 있습니다.

<a name="find-version-history"></a>

### <a name="find-and-view-previous-versions"></a>이전 버전 찾기 및 보기

1. Azure Portal에서 [논리 앱을 찾아서 엽니다](#find-logic-app).

1. 논리 앱 메뉴의 **개발 도구** 에서 **버전** 을 선택합니다.

   ![개발 도구에서 버전 선택 페이지를 표시하는 Azure Portal의 논리 앱 스크린샷.](./media/manage-logic-apps-with-azure-portal/logic-apps-menu-versions.png)

1. 목록에서 관리할 논리 앱의 **버전** 을 선택합니다. 검색 창에 **버전** 식별자를 입력하여 목록을 필터링할 수 있습니다.

1. **기록 버전** 페이지에는 읽기 전용 모드에서 이전 버전의 세부 정보가 표시됩니다. Logic Apps **디자이너** 와 **코드 보기** 모드 사이에서 선택할 수 있습니다.

   ![코드 보기 및 디자이너 보기 옵션을 보여주는 Logic Apps 기록 버전 페이지의 스크린샷.](./media/manage-logic-apps-with-azure-portal/history-version.png)

<a name="promote-previous-versions"></a>

### <a name="promote-previous-versions"></a>이전 버전 승격

1. 논리 앱의 버전 기록에서 [승격할 버전을 찾아 선택](#find-version-history)합니다.

1. **기록 버전** 페이지에서 **승격** 을 선택합니다.

   ![이전 버전을 승격하는 단추를 보여주는 논리 앱 버전 기록의 스크린샷.](./media/manage-logic-apps-with-azure-portal/promote-button.png)

1. 열리는 **Logic Apps 디자이너** 페이지에서 필요에 따라 승격하는 버전을 편집합니다. **디자이너** 와 **코드 보기** 모드 사이에서 전환할 수 있습니다. **매개 변수**, **템플릿** 및 **커넥터** 를 업데이트할 수도 있습니다.

   ![논리 앱의 이전 버전을 승격하는 단추를 보여주는 Logic Apps 디자이너의 스크린샷.](./media/manage-logic-apps-with-azure-portal/promote-page.png)

1. 업데이트를 저장하고 이전 버전 승격을 완료하려면 **저장** 을 선택합니다. (또는 변경 내용을 취소하려면 **취소** 를 선택합니다.) 

   다시 [논리 앱의 버전 기록을 보면](#find-version-history) 승격된 버전이 목록 상단에 표시되며 새 식별자가 생깁니다.

## <a name="next-steps"></a>다음 단계

* [논리 앱 모니터링](monitor-logic-apps.md)
