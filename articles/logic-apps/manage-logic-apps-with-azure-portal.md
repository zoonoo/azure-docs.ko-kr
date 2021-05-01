---
title: Azure Portal에서 논리 앱 관리
description: Azure Portal을 사용하여 논리 앱을 편집, 사용, 사용 안 함 또는 삭제합니다.
services: logic-apps
ms.suite: integration
author: lauradolan
ms.author: ladolan
ms.reviewer: estfan, jonfan, logicappspm
ms.topic: article
ms.custom: mvc
ms.date: 07/20/2020
ms.openlocfilehash: d50f577a7170982be004cc8957114f79675fbc6e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96006101"
---
# <a name="manage-logic-apps-in-the-azure-portal"></a>Azure Portal에서 논리 앱 관리

[Azure Portal](https://portal.azure.com) 또는 [Visual Studio](manage-logic-apps-with-visual-studio.md)를 사용하여 논리 앱을 관리할 수 있습니다. 이 문서에서는 Azure Portal에서 논리 앱을 편집, 사용 안 함, 사용 또는 삭제하는 방법을 보여 줍니다. Azure Logic Apps를 처음 접하는 경우 [Azure Logic Apps란?](logic-apps-overview.md)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독 Azure 구독이 없는 경우 [체험 Azure 계정에 등록](https://azure.microsoft.com/free/)합니다.

* 기존의 논리 앱. Azure Portal에서 논리 앱을 만드는 방법을 알아보려면 [빠른 시작: Azure Logic Apps을 사용하여 첫 번째 워크플로 만들기 - Azure Portal](quickstart-create-first-logic-app-workflow.md)을 참조하세요.

<a name="find-logic-app"></a>

## <a name="find-your-logic-apps"></a>논리 앱 찾기

논리 앱을 찾아서 열려면 다음 단계를 수행합니다.

1. Azure 계정을 사용하여 [Azure Portal](https://portal.azure.com) 에 로그인합니다.

1. Azure Portal 검색창에 `logic apps`를 입력하고 **Logic Apps** 를 선택합니다.

   ![검색창에 Logic Apps를 검색하여 선택된 상태의 Azure Portal 메뉴 스크린샷.](./media/manage-logic-apps-with-azure-portal/find-select-logic-apps.png)

1. **Logic Apps** 페이지에서 관리하려는 논리 앱을 찾아 선택합니다.

   논리 앱의 **개요** 창이 열리면 **Logic Apps** 페이지에 표시되는 목록을 다음과 같은 방식으로 필터링할 수 있습니다.

   * 이름으로 논리 앱 검색
   * 구독, 리소스 그룹, 위치 및 태그로 논리 앱 필터링
   * 리소스 그룹, 종류, 구독 및 위치별 논리 앱 그룹화

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

## <a name="disable-or-enable-logic-apps"></a>논리 앱 사용 또는 사용하지 않도록 설정

Azure Portal에서 [단일 논리 앱](#disable-enable-single-logic-app) 또는 [동시에 여러 논리 앱](#disable-or-enable-multiple-logic-apps)을 사용하거나 사용하지 않도록 설정할 수 있습니다. [Visual Studio에서 논리 앱을 사용하거나 사용하지 않도록 설정](manage-logic-apps-with-visual-studio.md#disable-or-enable-logic-app)할 수도 있습니다.

논리 앱을 사용하지 않도록 설정하면 워크플로 인스턴스에 영향을 주고 다음과 같은 방식으로 실행됩니다.

* 진행 중이거나 보류 중인 모든 실행은 완료될 때까지 계속됩니다. 이러한 실행의 수에 따라 이 프로세스에는 시간이 다소 걸릴 수 있습니다.

* Logic Apps 엔진은 새 워크플로 인스턴스를 만들거나 실행하지 않습니다.

* 트리거는 다음에 조건이 충족될 때 발생하지 않습니다.

* 트리거 상태는 논리 앱이 중지된 지점을 기억합니다. 따라서 논리 앱을 다시 사용하도록 설정하면 마지막 실행 이후 처리되지 않은 모든 항목에 대해 트리거가 발생합니다.

  논리 앱이 마지막 실행 이후 처리되지 않은 항목에서 발생하지 않도록 하려면 논리 앱을 다시 사용하도록 설정하기 전에 트리거의 상태를 지우십시오.

  1. Azure Portal에서 [논리 앱을 찾아서 엽니다](#find-logic-app).

  1. 논리 앱 트리거의 모든 부분을 편집합니다.

  1. 변경 내용을 저장합니다. 이 단계는 트리거의 현재 상태를 다시 설정합니다.

  1. [논리 앱을 다시 사용하도록 설정합니다](#disable-enable-single-logic-app).

<a name="disable-enable-single-logic-app"></a>

### <a name="disable-or-enable-single-logic-app"></a>단일 논리 앱을 사용 또는 사용하지 않도록 설정

1. Azure Portal에서 [논리 앱을 찾아서 엽니다](#find-logic-app).

1. 논리 앱 메뉴에서 **개요** 를 선택합니다. 다음 옵션 중에서 선택합니다.

   * 도구 모음에서 **사용 안 함** 을 선택합니다.

     ![사용 안 함 단추가 선택되어 있는 논리 앱 도구 모음의 스크린샷.](./media/manage-logic-apps-with-azure-portal/disable-single-logic-app.png)

     논리 앱이 이미 사용하지 않도록 설정되어 있는 경우 **사용** 옵션만 표시됩니다.

   * 도구 모음에서 **사용** 을 선택합니다.

     ![사용 단추가 선택되어 있는 논리 앱 도구 모음의 스크린샷.](./media/manage-logic-apps-with-azure-portal/enable-single-logic-app.png)

     논리 앱이 이미 사용하도록 설정되어 있는 경우 **사용 안 함** 옵션만 표시됩니다. 

   Azure Portal은 작업의 성공 또는 실패 여부를 확인하는 기본 Azure 도구 모음에 대한 알림을 표시합니다.

   ![작업 상태를 확인하는 알림을 보여주는 Azure Portal의 스크린샷](./media/manage-logic-apps-with-azure-portal/operation-confirmation-notification.png)

<a name="disable-or-enable-multiple-logic-apps"></a>

### <a name="disable-or-enable-multiple-logic-apps"></a>여러 논리 앱을 사용 또는 사용하지 않도록 설정

1. Azure Portal에서 사용하지 않도록 설정하거나 사용하도록 설정하려는 [논리 앱을 찾습니다](#find-logic-app).

1. 논리 앱을 현재 사용할 수 있는지 여부를 확인하려면 **Logic Apps** 페이지에서 해당 논리 앱의 **상태** 열을 검토합니다. 

   ![상태 열을 기준으로 정렬된 논리 앱 목록을 보여 주는 Azure Portal Logic Apps 페이지의 스크린샷.](./media/manage-logic-apps-with-azure-portal/view-logic-app-status.png)

   **상태** 열이 표시되지 않는 경우 **Logic Apps** 도구 모음에서 **미리 보기 사용** 을 선택합니다.

   ![미리 보기 사용 단추가 선택되어 있는 Azure Portal Logic Apps 페이지 스크린샷.](./media/manage-logic-apps-with-azure-portal/select-try-preview.png)

1. 확인란 열에서 사용 또는 사용하지 않도록 설정하려는 논리 앱을 선택합니다. 도구 모음에서 **사용 안 함** 또는 **사용** 을 선택합니다.

   ![여러 논리 앱에 대한 사용 및 사용 안 함 단추를 표시하는 Azure Portal Logic Apps 페이지의 스크린샷.](./media/manage-logic-apps-with-azure-portal/enable-disable-multiple-logic-apps.png)

1. 확인 상자가 표시되면 **예** 를 선택하여 계속합니다.

   Azure Portal은 작업의 성공 또는 실패 여부를 확인하는 기본 Azure 도구 모음에 대한 알림을 표시합니다.

## <a name="delete-logic-apps"></a>논리 앱 삭제

Azure Portal에서 [단일 논리 앱을 삭제](#delete-single-logic-app)하거나 [동시에 여러 논리 앱을 삭제](#delete-multiple-logic-apps)할 수 있습니다. [Visual Studio에서 논리 앱을 삭제](manage-logic-apps-with-visual-studio.md#delete-your-logic-app)할 수도 있습니다.

논리 앱을 삭제하면 다음과 같은 방식으로 워크플로 인스턴스에 영향을 줍니다.

* 진행 중이거나 보류 중인 모든 실행은 완료될 때까지 계속됩니다. 이러한 실행의 수에 따라 이 프로세스에는 시간이 다소 걸릴 수 있습니다.

* Logic Apps 엔진은 새 워크플로 인스턴스를 만들거나 실행하지 않습니다.

> [!NOTE]
> 자식 논리 앱을 삭제하고 다시 만드는 경우 부모 논리 앱을 다시 저장해야 합니다. 다시 만들어진 자식 앱은 다른 메타데이터를 포함합니다.
> 자식을 다시 만든 후 부모 논리 앱을 다시 저장하지 않는 경우 "권한 없음" 오류와 함께 자식 논리 앱에 대한 호출이 실패합니다. 이 동작은 통합 계정에서 아티팩트를 사용하거나 Azure Functions를 호출하는 것과 같은 부모-자식 논리 앱에 적용됩니다.

<a name="delete-single-logic-app"></a>

### <a name="delete-single-logic-app"></a>단일 논리 앱 삭제

1. Azure Portal에서 [논리 앱을 찾아서 엽니다](#find-logic-app).

1. 논리 앱 메뉴에서 **개요** 를 선택합니다. 논리 앱 도구 모음에서 **삭제** 를 선택합니다.

   ![삭제 단추가 선택되어 있는 논리 앱 도구 모음의 스크린샷.](./media/manage-logic-apps-with-azure-portal/delete-single-logic-app.png)

1. 확인 상자가 표시되면 논리 앱의 이름을 입력하고 **삭제** 를 선택합니다.

   ![단일 논리 앱 삭제를 확인하는 메시지를 표시하는 Logic Apps 스크린샷.](./media/manage-logic-apps-with-azure-portal/delete-confirmation-single-logic-app.png)

   Azure Portal은 작업의 성공 또는 실패 여부를 확인하는 기본 Azure 도구 모음에 대한 알림을 표시합니다.

<a name="delete-multiple-logic-apps"></a>

### <a name="delete-multiple-logic-apps"></a>여러 논리 앱 삭제

1. Azure Portal에서 [삭제하려는 논리 앱을 찾습니다](#find-logic-app).

1. 확인란 열에서 삭제하려는 논리 앱을 선택합니다. 도구 모음에서 **삭제** 를 선택합니다.

   ![삭제하도록 선택한 목록에서 여러 논리 앱을 보여주는 Logic Apps 페이지의 스크린샷.](./media/manage-logic-apps-with-azure-portal/delete-multiple-logic-apps.png)

1. 확인 상자가 표시되면 `yes`를 입력하고 **삭제** 를 선택합니다.

   ![여러 논리 앱 삭제를 확인하는 메시지를 표시하는 Logic Apps 스크린샷.](./media/manage-logic-apps-with-azure-portal/delete-confirmation-multiple-logic-apps.png)

   Azure Portal은 작업의 성공 또는 실패 여부를 확인하는 기본 Azure 도구 모음에 대한 알림을 표시합니다.

<a name="manage-logic-app-versions"></a>

## <a name="manage-logic-app-versions"></a>논리 앱 버전 관리

논리 앱의 버전 제어를 위해 Azure Portal을 사용할 수 있습니다. 논리 앱의 버전 기록을 확인하고 이전 버전을 승격할 수 있습니다.

<a name="find-version-history"></a>

### <a name="find-and-view-previous-versions"></a>이전 버전 찾기 및 보기

1. Azure Portal에서 [관리하려는 논리 앱을 찾습니다](#find-logic-app).

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
