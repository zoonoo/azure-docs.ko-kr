---
title: Azure 포털에서 논리 앱 관리
description: Azure 포털을 사용하여 논리 앱을 편집, 활성화, 비활성화 또는 삭제합니다.
services: logic-apps
ms.suite: integration
author: lauradolan
ms.author: ladolan
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.custom: mvc
ms.date: 04/13/2020
ms.openlocfilehash: f726ca90c215c4aff3734bd8022bbc1ad4dc5f87
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415949"
---
# <a name="manage-logic-apps-in-the-azure-portal"></a>Azure 포털에서 논리 앱 관리

[Azure 포털](https://portal.azure.com) 또는 Visual Studio를 사용하여 논리 앱을 관리할 수 [있습니다.](manage-logic-apps-with-visual-studio.md) 이 문서에서는 Azure Portal에서 논리 앱을 편집, 비활성화, 활성화 또는 삭제하는 방법을 보여 주며 있습니다. Azure 논리 앱을 새로 접하는 경우 [Azure 논리 앱이란 무엇입니까?](logic-apps-overview.md)

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독 Azure 구독이 없는 경우 [체험 Azure 계정에 등록](https://azure.microsoft.com/free/)합니다.

* 기존 논리 앱입니다. Azure 포털에서 논리 앱을 만드는 방법을 알아보려면 [빠른 시작: Azure Logic Apps - Azure Portal을 사용하여 첫 번째 워크플로 만들기를](quickstart-create-first-logic-app-workflow.md)참조하십시오.

<a name="find-logic-app"></a>

## <a name="find-your-logic-apps"></a>논리 앱 찾기

논리 앱을 찾고 열려면 다음 단계를 따르십시오.

1. Azure 계정을 사용하여 [Azure Portal](https://portal.azure.com) 에 로그인합니다.

1. Azure 검색 창에서 `logic apps`을 입력하고 **논리 앱을 선택합니다.**

   !["Logic Apps" 찾기 및 선택](./media/manage-logic-apps-with-azure-portal/find-select-logic-apps.png)

1. 논리 **앱** 페이지에서 관리하려는 논리 앱을 찾아 선택합니다.

   논리 앱의 **개요** 창이 열리면 다음과 같은 방법으로 논리 **앱** 페이지에 나타나는 목록을 필터링할 수 있습니다.

   * 이름으로 논리 앱 검색
   * 구독, 리소스 그룹, 위치 및 태그별로 논리 앱 필터링
   * 리소스 그룹, 유형, 구독 및 위치별로 논리 앱 그룹화

## <a name="view-logic-app-properties"></a>논리 앱 속성 보기

1. Azure 포털에서 [논리 앱을 찾아 엽니다.](#find-logic-app)

1. 논리 앱의 메뉴에서 **설정**에서 **속성을**선택합니다.

1. **속성** 창에서 논리 앱에 대한 다음 정보를 보고 복사할 수 있습니다.

   * **이름**
   * **리소스 ID**
   * **리소스 그룹**
   * **위치**
   * **형식** 
   * **구독 이름**
   * **구독 ID**
   * **액세스 끝점**
   * **런타임 나가는 IP 주소**
   * **엔드포인트 IP 주소 액세스**
   * **커넥터 나가는 IP 주소**

## <a name="disable-or-enable-logic-apps"></a>논리 앱을 사용하지 않도록 설정하거나 사용 설정

Azure [포털에서 단일](#disable-or-enable-multiple-logic-apps) 논리 [앱](#disable-enable-single-logic-app) 또는 여러 논리 앱을 동시에 활성화하거나 사용하지 않도록 설정할 수 있습니다. [Visual Studio에서 논리 앱을 사용하거나 사용하지 않도록 설정할](manage-logic-apps-with-visual-studio.md#disable-or-enable-logic-app)수도 있습니다.

논리 앱을 사용하지 않도록 설정하면 워크플로 인스턴스에 영향을 미치고 다음과 같은 방식으로 실행됩니다.

* 진행 중인 모든 실행 및 보류 중인 실행이 완료될 때까지 계속됩니다. 이러한 실행 횟수에 따라 이 프로세스에 다소 시간이 걸릴 수 있습니다.

* Logic Apps 엔진은 새 워크플로 인스턴스를 만들거나 실행하지 않습니다.

* 트리거는 다음에 해당 조건이 충족될 때 발사되지 않습니다.

* 트리거 상태는 논리 앱이 중지된 지점을 기억합니다. 따라서 논리 앱을 다시 활성화하면 마지막 실행 이후 처리되지 않은 모든 항목에 대해 트리거가 실행됩니다.

  논리 앱이 마지막 실행 이후 처리되지 않은 항목에 대해 실행되지 않도록 하려면 논리 앱을 다시 활성화하기 전에 트리거의 상태를 지우십시오.

  1. Azure 포털에서 [논리 앱을 찾아 엽니다.](#find-logic-app)

  1. 논리 앱 트리거의 모든 부분을 편집합니다.

  1. 변경 내용을 저장합니다. 이 단계는 트리거의 현재 상태를 재설정합니다.

  1. [논리 앱을 다시 활성화합니다.](#disable-enable-single-logic-app)

<a name="disable-enable-single-logic-app"></a>

### <a name="disable-or-enable-single-logic-app"></a>단일 논리 앱을 사용 안 함 또는 사용 설정

1. Azure 포털에서 [논리 앱을 찾아 엽니다.](#find-logic-app)

1. 논리 앱의 메뉴에서 **개요를**선택합니다. 다음 옵션 중에서 선택합니다.

   * 도구 모음에서 **사용 안 함**을 선택합니다.

     ![Azure 포털에서 단일 논리 앱 사용 안 함](./media/manage-logic-apps-with-azure-portal/disable-single-logic-app.png)

     논리 앱이 이미 비활성화된 경우 **사용** 옵션만 표시됩니다.

   * 도구 모음에서 **을 사용하도록**선택합니다.

     ![Azure 포털에서 단일 논리 앱 사용](./media/manage-logic-apps-with-azure-portal/enable-single-logic-app.png)

     논리 앱이 이미 활성화되어 있는 경우 **사용 안 함** 옵션만 표시됩니다. 

   Azure 포털에는 작업의 성공 또는 실패 여부를 확인하는 기본 Azure 도구 모음에 알림이 표시됩니다.

   ![작동 상태 확인 알림](./media/manage-logic-apps-with-azure-portal/operation-confirmation-notification.png)

<a name="disable-or-enable-multiple-logic-apps"></a>

### <a name="disable-or-enable-multiple-logic-apps"></a>여러 논리 앱을 사용하지 않도록 설정하거나 사용하도록 설정

1. Azure 포털에서 사용하지 않도록 설정하거나 활성화하려는 [논리 앱을 찾습니다.](#find-logic-app)

1. 논리 앱이 현재 사용 중인지 또는 사용 안 사용 중지되었는지 확인하려면 **Logic Apps** 페이지에서 해당 논리 앱의 **상태** 열을 검토합니다. 

   ![논리 앱 상태 열](./media/manage-logic-apps-with-azure-portal/view-logic-app-status.png)

   **상태** 열이 표시되지 않으면 논리 **앱** 도구 모음에서 **미리 보기 시도를**선택합니다.

   ![미리 보기 켜기](./media/manage-logic-apps-with-azure-portal/select-try-preview.png)

1. 확인란 열에서 비활성화하거나 활성화할 논리 앱을 선택합니다. 도구 모음에서 **사용 안 함** 또는 사용 **옵션을**선택합니다.

   ![Azure 포털에서 여러 논리 앱 사용 또는 비활성화](./media/manage-logic-apps-with-azure-portal/enable-disable-multiple-logic-apps.png)

1. 확인 상자가 나타나면 **예를** 선택하여 계속합니다.

   Azure 포털에는 작업의 성공 또는 실패 여부를 확인하는 기본 Azure 도구 모음에 알림이 표시됩니다.

## <a name="delete-logic-apps"></a>논리 앱 삭제

Azure 포털에서 [단일 논리 앱을 삭제하거나](#delete-single-logic-app) [여러 논리 앱을 동시에 삭제할](#delete-multiple-logic-apps) 수 있습니다. [Visual Studio에서 논리 앱을 삭제할](manage-logic-apps-with-visual-studio.md#delete-your-logic-app)수도 있습니다.

논리 앱을 삭제하면 다음과 같은 방식으로 워크플로 인스턴스에 영향을 줍니다.

* 진행 중인 모든 실행 및 보류 중인 실행이 완료될 때까지 계속됩니다. 이러한 실행 횟수에 따라 이 프로세스에 다소 시간이 걸릴 수 있습니다.

* Logic Apps 엔진은 새 워크플로 인스턴스를 만들거나 실행하지 않습니다.

<a name="delete-single-logic-app"></a>

### <a name="delete-single-logic-app"></a>단일 논리 앱 삭제

1. Azure 포털에서 [논리 앱을 찾아 엽니다.](#find-logic-app)

1. 논리 앱의 메뉴에서 **개요를**선택합니다. 논리 앱의 도구 모음에서 **삭제를**선택합니다.

   ![논리 앱 도구 모음에서 "삭제"를 선택합니다.](./media/manage-logic-apps-with-azure-portal/delete-single-logic-app.png)

1. 확인 상자가 나타나면 논리 앱이름을 입력하고 **삭제를**선택합니다.

   ![논리 앱 삭제 확인](./media/manage-logic-apps-with-azure-portal/delete-confirmation-single-logic-app.png)

   Azure 포털에는 작업의 성공 또는 실패 여부를 확인하는 기본 Azure 도구 모음에 알림이 표시됩니다.

<a name="delete-multiple-logic-apps"></a>

### <a name="delete-multiple-logic-apps"></a>여러 논리 앱 삭제

1. Azure 포털에서 [삭제하려는 논리 앱을 찾습니다.](#find-logic-app)

1. 확인란 열에서 삭제할 논리 앱을 선택합니다. 도구 모음에서 **삭제**를 선택합니다.

   ![여러 논리 앱 삭제](./media/manage-logic-apps-with-azure-portal/delete-multiple-logic-apps.png)

1. 확인 상자가 나타나면 `yes`을 입력하고 **삭제를**선택합니다.

   ![로직 앱 삭제 확인](./media/manage-logic-apps-with-azure-portal/delete-confirmation-multiple-logic-apps.png)

   Azure 포털에는 작업의 성공 또는 실패 여부를 확인하는 기본 Azure 도구 모음에 알림이 표시됩니다.

<a name="manage-logic-app-versions"></a>

## <a name="manage-logic-app-versions"></a>논리 앱 버전 관리

논리 앱의 버전 제어에 Azure 포털을 사용할 수 있습니다. 논리 앱의 버전 기록을 찾고 이전 버전을 승격할 수 있습니다.

<a name="find-version-history"></a>

### <a name="find-and-view-previous-versions"></a>이전 버전 찾기 및 보기

1. Azure 포털에서 [관리하려는 논리 앱을 찾습니다.](#find-logic-app)

1. 논리 앱의 메뉴에서 **개발 도구에서** **버전을 선택합니다.**

   ![논리 앱 의 메뉴에서 "개발 도구"에서 "버전"을 선택합니다.](./media/manage-logic-apps-with-azure-portal/logic-apps-menu-versions.png)

1. 목록에서 관리할 논리 앱의 **버전을** 선택합니다. 검색 막대에 **버전** 식별자를 입력하여 목록을 필터링할 수 있습니다.

1. **히스토리 버전** 페이지에서 이전 버전의 세부 정보를 읽기 전용 모드로 볼 수 있습니다. 로직 앱 **디자이너와** **코드 보기** 모드 중에서 선택할 수 있습니다.

   ![코드 보기 및 논리 앱 디자이너 보기가 있는 논리 앱의 기록 버전 페이지](./media/manage-logic-apps-with-azure-portal/history-version.png)

<a name="promote-previous-versions"></a>

### <a name="promote-previous-versions"></a>이전 버전 홍보

1. 논리 앱의 버전 기록에서 [승격할 버전을 찾아 선택합니다.](#find-version-history)

1. 기록 **버전** 페이지에서 **승격을**선택합니다.

   ![논리 앱의 버전 기록에서 단추 승격](./media/manage-logic-apps-with-azure-portal/promote-button.png)

1. 열리는 **로직 앱 디자이너** 페이지에서 필요에 따라 홍보할 버전을 편집합니다. **디자이너** 보기 모드와 **코드 보기** 모드 간에 전환할 수 있습니다. **매개 변수,** **템플릿**및 **커넥터를 업데이트할**수도 있습니다.

   ![이전 버전을 홍보하기 위한 논리 앱 디자이너 페이지](./media/manage-logic-apps-with-azure-portal/promote-page.png)

1. 업데이트를 저장하고 이전 버전 승격을 완료하려면 **저장을**선택합니다. (또는 변경 내용을 취소하려면 **삭제를**선택합니다.) 

   논리 [앱의 버전 기록을](#find-version-history) 다시 보면 승격된 버전이 목록의 맨 위에 나타나고 새 식별자가 있습니다.

## <a name="next-steps"></a>다음 단계

* [논리 앱 모니터링](monitor-logic-apps.md)
