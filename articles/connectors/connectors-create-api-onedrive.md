---
title: Microsoft OneDrive에서 파일 액세스 및 관리
description: Azure Logic Apps에서 자동화 된 워크플로를 만들어 OneDrive의 파일을 업로드 하 고 관리 합니다.
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 10/18/2016
tags: connectors
ms.openlocfilehash: 8dd54fec963b8f4775a8ade6277b071d62ca3850
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86524212"
---
# <a name="access-and-manage-files-in-onedrive-connector-by-using-azure-logic-apps"></a>Azure Logic Apps를 사용 하 여 OneDrive 커넥터에서 파일 액세스 및 관리

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) 및 [OneDrive 커넥터](/connectors/onedriveconnector/)를 사용 하 여 파일 업로드, 가져오기, 삭제 등을 포함 하 여 파일을 관리 하는 자동화 된 작업 및 워크플로를 만들 수 있습니다. OneDrive를 사용 하 여 다음 작업을 수행할 수 있습니다.

* OneDrive에서 파일을 저장하여 워크플로를 작성하거나 OneDrive의 기존 파일을 업데이트합니다. 
* 트리거를 사용하여 OneDrive 내에서 파일이 만들어지거나 업데이트될 때 워크플로를 시작합니다.
* 파일 만들기, 파일 삭제 등의 작업을 사용합니다. 예를 들어 새 Office 365 전자 메일이 첨부 파일과 함께 수신되면(트리거) OneDrive에 새 파일을 만듭니다(작업).

이 아티클에서는 논리 앱에서 OneDrive 커넥터를 사용하는 방법을 보여주고, 트리거 및 작업을 나열합니다.

Logic Apps에 대해 자세히 알아보려면 [논리 앱이란 무엇인가요?](../logic-apps/logic-apps-overview.md) 및 [논리 앱 만들기](../logic-apps/quickstart-create-first-logic-app-workflow.md)를 참조하세요.

## <a name="connect-to-onedrive"></a>OneDrive에 연결

논리 앱에서 서비스에 액세스하려면 먼저 서비스에 대한 *연결*을 만들어야 합니다. 연결은 논리 앱과 다른 서비스 간의 연결을 제공합니다. 예를 들어 OneDrive에 연결하려면 먼저 OneDrive *연결*이 필요합니다. 연결을 만들려면 연결하려는 서비스에 액세스할 때 일반적으로 사용하는 자격 증명을 입력합니다. 따라서 OneDrive를 사용하는 경우 OneDrive 계정에 대한 자격 증명을 입력하여 연결을 만듭니다.

### <a name="create-the-connection"></a>연결 만들기

[!INCLUDE [Steps to create a connection to OneDrive](../../includes/connectors-create-api-onedrive.md)]

## <a name="use-a-trigger"></a>트리거 사용

트리거는 논리 앱에 정의된 워크플로를 시작하는 데 사용할 수 있는 이벤트입니다. 원하는 간격 및 빈도로 서비스의 "폴링"을 트리거합니다. [트리거에 대해 자세히 알아보세요.](../logic-apps/logic-apps-overview.md#logic-app-concepts)

1. 논리 앱 디자이너에서를 입력 `onedrive` 하 여 트리거 목록을 가져옵니다.  

   !["Microsoft 관리 되는 Api 표시" 라는 대화 상자에 "onedrive"가 포함 된 상자가 있습니다. 다음은 4 개 트리거의 목록입니다. 그 중 첫 번째는 "OneDrive-파일이 생성 될 때"입니다. 두 번째 "OneDrive-파일이 수정 되는 경우"가 선택 되었습니다.](./media/connectors-create-api-onedrive/onedrive-1.png)

2. **파일을 수정할 때**를 선택합니다. 연결이 이미 있는 경우 선택 표시 단추를 선택하여 폴더를 선택합니다.

   !["파일을 수정 하는 경우" 라는 대화 상자에는 연결 된 찾아보기 단추가 있는 "폴더" 라는 상자가 있습니다.](./media/connectors-create-api-onedrive/sample-folder.png)

   로그인하라는 메시지가 표시되면 로그인 세부 정보를 입력하여 연결을 만듭니다. 이 아티클의 [연결 만들기](connectors-create-api-onedrive.md#create-the-connection)에는 단계가 나열됩니다.

   이 예제에서는 선택한 폴더의 파일이 업데이트되면 논리 앱이 실행됩니다. 이 트리거의 결과를 보려면 전자 메일을 보내는 다른 작업을 추가합니다. 예를 들어 파일이 업데이트될 때 전자 메일을 보내는 Office 365 Outlook *전자 메일 보내기* 작업을 추가합니다.

3. **편집** 단추를 선택하고 **빈도** 및 **간격** 값을 설정합니다. 예를 들어 15분마다 폴링을 트리거하려면 **빈도**를 **분**으로, **간격**을 **15**로 설정합니다. 

   !["파일을 수정 하는 경우" 라는 대화 상자에는 "폴더", "빈도", "간격", "표준 시간대" 및 "시작 시간" 이라는 레이블이 지정 된 5 개의 상자가 표시 됩니다. "빈도" 및 "표준 시간대" 필드에 대 한 드롭다운 목록이 있습니다.](./media/connectors-create-api-onedrive/trigger-properties.png)

4. 변경 내용을 **저장**합니다(도구 모음 왼쪽 위 모서리). 논리 앱이 저장되며 이 논리 앱이 사용 상태로 자동 설정될 수 있습니다.

## <a name="use-an-action"></a>작업 사용

작업은 논리 앱에 정의된 워크플로에 의해 수행되는 작업입니다. [작업에](../logic-apps/logic-apps-overview.md#logic-app-concepts)대해 자세히 알아보세요.

1. 더하기 기호를 선택합니다. **작업 추가**, **조건 추가** 또는 **자세히** 옵션 중 하나 등 몇 가지가 표시됩니다.

   ![스크린샷에는 "+ 새 단계", "작업 추가", "조건 추가" 및 "..."와 같은 네 가지 단추가 표시 됩니다. 추가 정보 ".](./media/connectors-create-api-onedrive/add-action.png)

2. **작업 추가**를 선택합니다.

3. 검색 상자에를 입력 `onedrive` 하 여 사용 가능한 모든 작업 목록을 가져옵니다.

   !["Microsoft 관리 되는 Api 표시" 라는 대화 상자에 "onedrive"가 포함 된 상자가 있습니다. 다음은 8 개의 작업 목록입니다. 첫 번째는 "OneDrive-파일 만들기" 이며 선택 됩니다.](./media/connectors-create-api-onedrive/onedrive-actions.png) 

4. 이 예제에서는 **OneDrive - 파일 만들기**를 선택합니다. 연결이 이미 존재하는 경우 **폴더 경로**를 선택하여 파일을 입력하고 **파일 이름**을 입력한 후 원하는 **파일 콘텐츠**를 선택합니다.  

   !["파일 만들기" 라는 대화 상자에는 "폴더 경로", "파일 이름" 및 "폴더 콘텐츠" 라는 레이블이 붙은 세 개의 상자가 표시 됩니다. "폴더 경로" 상자 옆에 디렉터리 찾아보기 단추가 있습니다.](./media/connectors-create-api-onedrive/sample-action.png)

   연결 정보를 입력 하 라는 메시지가 표시 되 면이 항목에 [설명 된 대로 연결을 만드는](#create-the-connection) 데 필요한 세부 정보를 입력 합니다.

   이 예제에서는 OneDrive 폴더에 새 파일을 만듭니다. 다른 트리거의 출력을 사용하여 OneDrive 파일을 만들 수 있습니다. 예를 들어 Office 365 Outlook *새 전자 메일이 도착했을 때* 트리거를 추가합니다. 그런 후 ForEach 내에서 Attachments 및 Content-Type 필드를 사용하는 OneDrive *파일 만들기* 작업을 추가하여 OneDrive에서 새 파일을 만듭니다.

   !["For each" 라는 대화 상자에는 "첨부 파일"을 포함 하는 "이전 단계에서 출력 선택" 이라는 상자가 있습니다. "폴더 경로", "파일 이름" 및 "파일 콘텐츠" 상자를 사용 하 여 "For each" 상자의 나머지 부분을 다루는 "파일 만들기" 대화 상자가 있습니다. ](./media/connectors-create-api-onedrive/foreach-action.png)

5. 변경 내용을 **저장**합니다(도구 모음 왼쪽 위 모서리). 논리 앱이 저장되며 이 논리 앱이 사용 상태로 자동 설정될 수 있습니다.

## <a name="connector-specific-details"></a>커넥터 관련 세부 정보

[커넥터 세부 정보](/connectors/onedriveconnector/)에서 swagger에 정의된 모든 트리거 및 작업과 제한 사항도 확인할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [Azure Logic Apps용 커넥터](apis-list.md)