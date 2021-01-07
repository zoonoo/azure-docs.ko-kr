---
title: Logic Apps와 통합
titleSuffix: Azure Digital Twins
description: 사용자 지정 커넥터를 사용 하 여 Azure Digital Twins에 Logic Apps를 연결 하는 방법을 참조 하세요.
author: baanders
ms.author: baanders
ms.date: 9/11/2020
ms.topic: how-to
ms.service: digital-twins
ms.reviewer: baanders
ms.openlocfilehash: 3fbd9016bcbfa83574d894af7ca728b863f54344
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93129323"
---
# <a name="integrate-with-logic-apps-using-a-custom-connector"></a>사용자 지정 커넥터를 사용 하 여 Logic Apps와 통합

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) 는 앱 및 서비스에서 워크플로를 자동화 하는 데 도움이 되는 클라우드 서비스입니다. Azure Digital Twins Api에 Logic Apps를 연결 하 여 Azure 디지털 쌍 및 해당 데이터를 중심으로 이러한 자동화 된 흐름을 만들 수 있습니다.

Azure Digital Twins는 현재 Logic Apps에 대해 인증 된 (미리 작성 된) 커넥터를가지고 있지 않습니다. 대신, Azure Digital Twins와 Logic Apps를 사용 하는 현재 프로세스는 Logic Apps 작업 하도록 수정 된 [사용자 지정 Azure 디지털 쌍 Swagger](/samples/azure-samples/digital-twins-custom-swaggers/azure-digital-twins-custom-swaggers/) 를 사용 하 여 [**사용자 지정 Logic Apps 커넥터**](../logic-apps/custom-connector-overview.md)를 만드는 것입니다.

> [!NOTE]
> 위에 연결 된 사용자 지정 Swagger 샘플에는 여러 버전의 Swagger가 포함 되어 있습니다. 최신 버전은 가장 최근 날짜의 하위 폴더에 있지만이 샘플에 포함 된 이전 버전도 계속 지원 됩니다.

이 문서에서는 [Azure Portal](https://portal.azure.com) 를 사용 하 여 Azure Digital twins 인스턴스에 Logic Apps를 연결 하는 데 사용할 수 있는 **사용자 지정 커넥터를 만듭니다** . 그런 다음이 연결을 사용 하는 **논리 앱을 만듭니다** . 예를 들어 타이머에서 트리거되는 이벤트는 Azure Digital twins 인스턴스의 쌍을 자동으로 업데이트 합니다. 

## <a name="prerequisites"></a>필수 구성 요소

Azure 구독이 아직 없는 경우 시작하기 전에 **[체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)** 을 만듭니다.
이 계정으로 [Azure Portal](https://portal.azure.com) 에 로그인 합니다. 

또한 필수 구성 요소를 설치 하는 과정에서 다음 항목을 완료 해야 합니다. 이 섹션의 나머지 부분에서는 다음 단계를 안내 합니다.
- Azure Digital Twins 인스턴스 설정
- 디지털 쌍 추가

### <a name="set-up-azure-digital-twins-instance"></a>Azure Digital Twins 인스턴스 설정

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]

### <a name="add-a-digital-twin"></a>디지털 쌍 추가

이 문서에서는 Logic Apps를 사용 하 여 Azure Digital Twins 인스턴스의 쌍을 업데이트 합니다. 계속 하려면 인스턴스에 하나 이상의 쌍을 추가 해야 합니다. 

[DigitalTwins api](/rest/api/digital-twins/dataplane/twins), [.net (c #) SDK](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet-preview&preserve-view=true)또는 [Azure Digital 쌍 CLI](how-to-use-cli.md)를 사용 하 여 쌍를 추가할 수 있습니다. 이러한 방법을 사용 하 여 쌍를 만드는 방법에 대 한 자세한 단계 [*는 방법: 디지털 쌍 관리*](how-to-manage-twin.md)를 참조 하세요.

사용자가 만든 인스턴스에 쌍의 쌍 **_ID_** 가 필요 합니다.

## <a name="set-up-app-registration"></a>앱 등록 설정

[!INCLUDE [digital-twins-prereq-registration.md](../../includes/digital-twins-prereq-registration.md)]

### <a name="get-app-registration-client-secret"></a>앱 등록 클라이언트 암호 가져오기

또한 Azure AD 앱 등록에 대 한 **_클라이언트 암호_** 를 만들어야 합니다. 이렇게 하려면 Azure Portal에서 [앱 등록](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) 페이지로 이동 합니다 .이 링크를 사용 하거나 포털 검색 표시줄에서 찾을 수 있습니다. 세부 정보를 열기 위해 목록에서 이전 섹션에서 만든 등록을 선택 합니다. 

등록 메뉴에서 *인증서 및 비밀* 을 적중 하 고 *+ 새 클라이언트 암호* 를 선택 합니다.

:::image type="content" source="media/how-to-integrate-logic-apps/client-secret.png" alt-text="Azure AD 앱 등록의 포털 뷰입니다. 리소스 메뉴에서 ' 인증서 및 암호 '를 강조 표시 하 고 ' 새 클라이언트 암호 ' 주위의 페이지에서 강조 표시 합니다.":::

*설명* 및 *만료* 에 사용할 값을 입력 하 고 *추가* 를 누릅니다.

:::image type="content" source="media/how-to-integrate-logic-apps/add-client-secret.png" alt-text="Azure AD 앱 등록의 포털 뷰입니다. 리소스 메뉴에서 ' 인증서 및 암호 '를 강조 표시 하 고 ' 새 클라이언트 암호 ' 주위의 페이지에서 강조 표시 합니다.":::

이제 클라이언트 암호가 _만료_ 됨 및 _값_ 필드가 있는 _인증서 & 암호_ 페이지에 표시 되는지 확인 합니다. 나중에 사용할 _값_ 을 기록해 둡니다 (복사 아이콘을 사용 하 여 클립보드로 복사할 수도 있음).

:::image type="content" source="media/how-to-integrate-logic-apps/client-secret-value.png" alt-text="Azure AD 앱 등록의 포털 뷰입니다. 리소스 메뉴에서 ' 인증서 및 암호 '를 강조 표시 하 고 ' 새 클라이언트 암호 ' 주위의 페이지에서 강조 표시 합니다.":::

## <a name="create-custom-logic-apps-connector"></a>사용자 지정 Logic Apps 커넥터 만들기

이제 Azure Digital Twins Api에 대 한 [사용자 지정 Logic Apps 커넥터](../logic-apps/custom-connector-overview.md) 를 만들 준비가 되었습니다. 이 작업을 수행한 후에는 다음 섹션에서 논리 앱을 만들 때 Azure Digital Twins에 연결할 수 있습니다.

Azure Portal에서 [Logic Apps 사용자 지정 커넥터](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Web%2FcustomApis) 페이지로 이동 합니다 .이 링크를 사용 하거나 포털 검색 표시줄에서 검색할 수 있습니다. 적중 *+ 추가*

:::image type="content" source="media/how-to-integrate-logic-apps/logic-apps-custom-connector.png" alt-text="Azure AD 앱 등록의 포털 뷰입니다. 리소스 메뉴에서 ' 인증서 및 암호 '를 강조 표시 하 고 ' 새 클라이언트 암호 ' 주위의 페이지에서 강조 표시 합니다.":::

다음에 나오는 *Logic Apps 사용자 지정 커넥터 만들기* 페이지에서 구독 및 리소스 그룹을 선택 하 고 새 커넥터에 대 한 이름 및 배포 위치를 선택 합니다. *검토 + 만들기* 를 누릅니다. 

:::image type="content" source="media/how-to-integrate-logic-apps/create-logic-apps-custom-connector.png" alt-text="Azure AD 앱 등록의 포털 뷰입니다. 리소스 메뉴에서 ' 인증서 및 암호 '를 강조 표시 하 고 ' 새 클라이언트 암호 ' 주위의 페이지에서 강조 표시 합니다.":::

그러면 아래에서 *만들기* 를 눌러 리소스를 만들 수 있는 *검토 + 만들기* 탭으로 이동 합니다.

:::image type="content" source="media/how-to-integrate-logic-apps/review-logic-apps-custom-connector.png" alt-text="Azure AD 앱 등록의 포털 뷰입니다. 리소스 메뉴에서 ' 인증서 및 암호 '를 강조 표시 하 고 ' 새 클라이언트 암호 ' 주위의 페이지에서 강조 표시 합니다.":::

커넥터에 대 한 배포 페이지로 이동 합니다. 배포가 완료 되 면 *리소스로 이동* 단추를 눌러 포털에서 커넥터의 세부 정보를 확인 합니다.

### <a name="configure-connector-for-azure-digital-twins"></a>Azure Digital Twins 용 커넥터 구성

다음으로, Azure Digital Twins에 연결 하기 위해 만든 커넥터를 구성 합니다.

먼저 Logic Apps 작업 하도록 수정 된 사용자 지정 Azure Digital Twins Swagger를 다운로드 합니다. *ZIP 다운로드* 단추를 방문 하 여 [**이 링크**](/samples/azure-samples/digital-twins-custom-swaggers/azure-digital-twins-custom-swaggers/) 에서 **Azure Digital twins custom swagger (Logic Apps 커넥터)** 샘플을 다운로드 합니다. 다운로드 한 *Azure_Digital_Twins_custom_Swaggers__Logic_Apps_connector_.zip* 폴더로 이동 하 여 압축을 풉니다. 

이 자습서에 대 한 사용자 지정 Swagger는 _* * Azure_Digital_Twins_custom_Swaggers__Logic_Apps_connector_ \LogicApps **_ 폴더에 있습니다. 이 폴더에는 *안정적* 이 고 *미리 보기가* 포함 된 하위 폴더가 포함 되어 있습니다. 둘 다 날짜별로 구성 된 Swagger의 서로 다른 버전을 보유 합니다. 가장 최근 날짜의 폴더에는 Swagger의 최신 복사본이 포함 됩니다. 선택한 버전 중에서 Swagger 파일의 이름은 _** digitaltwins.js* * _입니다.

> [!NOTE]
> 미리 보기 기능을 사용 하 여 작업 하지 않는 한 일반적으로 가장 *안정적인* Swagger 버전을 사용 하는 것이 좋습니다. 그러나 Swagger의 이전 버전 및 미리 보기 버전도 계속 지원 됩니다. 

그런 다음 [Azure Portal](https://portal.azure.com) 의 커넥터 개요 페이지로 이동 하 여 *편집* 을 누릅니다.

:::image type="content" source="media/how-to-integrate-logic-apps/edit-connector.png" alt-text="Azure AD 앱 등록의 포털 뷰입니다. 리소스 메뉴에서 ' 인증서 및 암호 '를 강조 표시 하 고 ' 새 클라이언트 암호 ' 주위의 페이지에서 강조 표시 합니다.":::

뒤에 나오는 *Logic Apps 사용자 지정 커넥터 편집* 페이지에서 다음 정보를 구성 합니다.
* **사용자 지정 커넥터**
    - API 끝점: REST (기본값 유지)
    - 가져오기 모드: OpenAPI 파일 (기본값 유지)
    - 파일: 앞에서 다운로드 한 사용자 지정 Swagger 파일입니다. *가져오기* 를 누르고 컴퓨터 ( *Azure_Digital_Twins_custom_Swaggers__Logic_Apps_connector_ \LogicApps \...\digitaltwins.js* )에서 파일을 찾은 다음 *열기* 를 누릅니다.
* **일반 정보**
    - 아이콘: 원하는 아이콘을 업로드 합니다.
    - 아이콘 배경색: 16 진수 코드를 색의 ' #xxxxxx ' 형식으로 입력 합니다.
    - 설명: 원하는 모든 값을 입력 합니다.
    - 구성표: HTTPS (기본값 유지)
    - Host: Azure Digital Twins 인스턴스의 *호스트 이름* 입니다.
    - 기준 URL:/(기본값 유지)

그런 다음 창 맨 아래에 있는 *보안* 단추를 눌러 다음 구성 단계로 이동 합니다.

:::image type="content" source="media/how-to-integrate-logic-apps/configure-next.png" alt-text="Azure AD 앱 등록의 포털 뷰입니다. 리소스 메뉴에서 ' 인증서 및 암호 '를 강조 표시 하 고 ' 새 클라이언트 암호 ' 주위의 페이지에서 강조 표시 합니다.":::

보안 단계에서 다음 정보를 *편집* 하 고 구성 합니다.
* **인증 유형** : OAuth 2.0
* **OAuth 2.0** :
    - Id 공급자: Azure Active Directory
    - 클라이언트 ID: Azure AD 앱 등록에 대 한 *응용 프로그램 (클라이언트) id*
    - 클라이언트 암호: Azure AD 앱 등록을 위한 [*필수 구성 요소*](#prerequisites) 에서 만든 *클라이언트 암호* 입니다.
    - 로그인 URL: https://login.windows.net (기본값 유지)
    - 테 넌 트 ID: Azure AD 앱 등록에 대 한 *디렉터리 (테 넌 트) id*
    - 리소스 URL: 0b07f429-9f4b-4714-9392-cc5e8e80c8b0
    - 범위: 디렉터리. AccessAsUser. 모두
    - 리디렉션 URL: (지금은 기본값 유지)

리디렉션 URL 필드에는 *리디렉션 url을 생성 하는 사용자 지정 커넥터 저장* 이 표시 됩니다. 이 작업을 수행 하려면 창 위쪽에 있는 *업데이트 커넥터* 를 방문 하 여 커넥터 설정을 확인 합니다.

:::image type="content" source="media/how-to-integrate-logic-apps/update-connector.png" alt-text="Azure AD 앱 등록의 포털 뷰입니다. 리소스 메뉴에서 ' 인증서 및 암호 '를 강조 표시 하 고 ' 새 클라이언트 암호 ' 주위의 페이지에서 강조 표시 합니다.":::

<!-- Success message? didn't see one -->

리디렉션 URL 필드로 돌아가서 생성 된 값을 복사 합니다. 이 버전 번호는 다음 단계에서 사용합니다.

:::image type="content" source="media/how-to-integrate-logic-apps/copy-redirect-url.png" alt-text="Azure AD 앱 등록의 포털 뷰입니다. 리소스 메뉴에서 ' 인증서 및 암호 '를 강조 표시 하 고 ' 새 클라이언트 암호 ' 주위의 페이지에서 강조 표시 합니다.":::

커넥터를 만드는 데 필요한 모든 정보입니다 (정의 단계에 대 한 이전 보안을 계속할 필요 없음). *편집 Logic Apps 사용자 지정 커넥터* 창을 닫을 수 있습니다.

>[!NOTE]
>처음에 *편집* 을 누르고 있는 커넥터의 개요 페이지로 돌아가 *편집* 을 다시 방문 하면 구성 선택 항목을 입력 하는 전체 프로세스가 다시 시작 됩니다. 마지막으로 수행한 시간에서 값을 채우지 않으므로 변경 된 값을 사용 하 여 업데이트 된 구성을 저장 하려는 경우에는 다른 모든 값을 다시 입력 하 여 기본값으로 덮어쓰는 것을 방지 해야 합니다.

### <a name="grant-connector-permissions-in-the-azure-ad-app"></a>Azure AD 앱에서 커넥터 권한 부여

다음으로, 마지막 단계에서 복사한 사용자 지정 커넥터의 *리디렉션 URL* 값을 사용 하 여 Azure AD 앱 등록에서 커넥터 권한을 부여 합니다.

Azure Portal에서 [앱 등록](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) 페이지로 이동 하 여 목록에서 등록을 선택 합니다. 

등록 메뉴의 *인증* 에서 URI를 추가 합니다.

:::image type="content" source="media/how-to-integrate-logic-apps/add-uri.png" alt-text="Azure AD 앱 등록의 포털 뷰입니다. 리소스 메뉴에서 ' 인증서 및 암호 '를 강조 표시 하 고 ' 새 클라이언트 암호 ' 주위의 페이지에서 강조 표시 합니다."::: 

사용자 지정 커넥터의 *리디렉션 URL* 을 새 필드에 입력 하 고 *저장* 아이콘을 누릅니다.

:::image type="content" source="media/how-to-integrate-logic-apps/save-uri.png" alt-text="Azure AD 앱 등록의 포털 뷰입니다. 리소스 메뉴에서 ' 인증서 및 암호 '를 강조 표시 하 고 ' 새 클라이언트 암호 ' 주위의 페이지에서 강조 표시 합니다.":::

이제 Azure Digital Twins Api에 액세스할 수 있는 사용자 지정 커넥터 설정이 완료 되었습니다. 

## <a name="create-logic-app"></a>논리 앱 만들기

다음으로, 새 커넥터를 사용 하 여 Azure Digital Twins 업데이트를 자동화 하는 논리 앱을 만듭니다.

[Azure Portal](https://portal.azure.com)포털 검색 창에서 *논리 앱* 을 검색 합니다. 선택 하면 *논리 앱* 페이지로 이동 합니다. *논리 앱 만들기* 단추를 눌러 새 논리 앱을 만듭니다.

:::image type="content" source="media/how-to-integrate-logic-apps/create-logic-app.png" alt-text="Azure AD 앱 등록의 포털 뷰입니다. 리소스 메뉴에서 ' 인증서 및 암호 '를 강조 표시 하 고 ' 새 클라이언트 암호 ' 주위의 페이지에서 강조 표시 합니다.":::

다음 *논리 앱* 페이지에서 구독 및 리소스 그룹을 입력 합니다. 또한 논리 앱의 이름을 선택 하 고 배포 위치를 선택 합니다.

_검토 + 만들기_ 단추를 누릅니다.

그러면 정보를 검토 하 고 맨 아래에 있는 *만들기* 를 눌러 리소스를 만들 수 있는 *검토 + 만들기* 탭으로 이동 됩니다.

논리 앱에 대 한 배포 페이지로 이동 합니다. 배포가 완료 되 면 *리소스로 이동* 단추를 클릭 하 여 워크플로의 논리를 채울 *Logic Apps 디자이너로* 이동 합니다.

### <a name="design-workflow"></a>디자인 워크플로

*Logic Apps 디자이너* 의 *공통 트리거로 시작* 에서 _**되풀이**_ 를 선택 합니다.

:::image type="content" source="media/how-to-integrate-logic-apps/logic-apps-designer-recurrence.png" alt-text="Azure AD 앱 등록의 포털 뷰입니다. 리소스 메뉴에서 ' 인증서 및 암호 '를 강조 표시 하 고 ' 새 클라이언트 암호 ' 주위의 페이지에서 강조 표시 합니다.":::

다음에 나오는 *Logic Apps 디자이너* 페이지에서 **되풀이** 빈도를 *Second* 로 변경 하 여 이벤트가 3 초 마다 트리거됩니다. 이렇게 하면 나중에 대기 하지 않고도 결과를 쉽게 확인할 수 있습니다.

적중 *+ 새 단계*

이렇게 하면 *작업 선택* 상자가 열립니다. *사용자 지정* 탭으로 전환 합니다. 맨 위에 있는 상자에 사용자 지정 커넥터가 표시 됩니다.

:::image type="content" source="media/how-to-integrate-logic-apps/custom-action.png" alt-text="Azure AD 앱 등록의 포털 뷰입니다. 리소스 메뉴에서 ' 인증서 및 암호 '를 강조 표시 하 고 ' 새 클라이언트 암호 ' 주위의 페이지에서 강조 표시 합니다.":::

해당 커넥터에 포함 된 Api 목록을 표시 하려면이를 선택 합니다. 검색 표시줄을 사용 하거나 목록을 스크롤하여 **DigitalTwins_Add** 를 선택 합니다. 이 API는이 문서에서 사용 되는 API 이지만 Logic Apps 연결에 대 한 유효한 선택으로 다른 API를 선택할 수도 있습니다.

커넥터에 연결 하려면 Azure 자격 증명을 사용 하 여 로그인 하 라는 메시지가 표시 될 수 있습니다. *요청 된 사용 권한* 대화 상자가 표시 되 면 메시지에 따라 앱에 대 한 동의를 부여 하 고 수락 합니다.

새 *DigitalTwinsAdd* 상자에서 다음과 같이 필드를 채웁니다.
* _id_ : 논리 앱을 업데이트 하려는 인스턴스에서 디지털 쌍의 쌍 *id* 를 채웁니다.
* 쌍:이 필드는 선택한 API 요청에 필요한 본문을 입력 _합니다._ *DigitalTwinsUpdate* 의 경우이 본문은 JSON 패치 코드 형식입니다. 쌍을 업데이트 하는 JSON 패치를 구조화 하는 방법에 대 한 자세한 내용은 *방법: 디지털 쌍 관리* 의 디지털 쌍 [업데이트](how-to-manage-twin.md#update-a-digital-twin) 섹션을 참조 하십시오.
* _api-version_ : 최신 api 버전입니다. 현재이 값은 *2020-10-31* 입니다.

Logic Apps 디자이너에서 *저장* 을 누릅니다.

같은 창에서 _+ 새 단계_ 를 선택 하 여 다른 작업을 선택할 수 있습니다.

:::image type="content" source="media/how-to-integrate-logic-apps/save-logic-app.png" alt-text="Azure AD 앱 등록의 포털 뷰입니다. 리소스 메뉴에서 ' 인증서 및 암호 '를 강조 표시 하 고 ' 새 클라이언트 암호 ' 주위의 페이지에서 강조 표시 합니다.":::

## <a name="query-twin-to-see-the-update"></a>업데이트를 확인 하는 쌍 쿼리

이제 논리 앱을 만들었으므로, Logic Apps 디자이너에서 정의한 쌍 업데이트 이벤트가 3 초 마다 되풀이에서 발생 해야 합니다. 즉, 3 초 후에 쌍을 쿼리하고 새 패치 된 값이 반영 된 것을 확인할 수 있습니다.

사용자 지정 메서드 (예: [사용자 지정 클라이언트 앱](tutorial-command-line-app.md), [Azure 디지털 쌍 탐색기 샘플 앱](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/), [sdk 및 api](how-to-use-apis-sdks.md)또는 [CLI](how-to-use-cli.md))를 통해 쌍을 쿼리할 수 있습니다. 

Azure Digital Twins 인스턴스를 쿼리 하는 방법에 대 한 자세한 내용은 [*방법: 쌍 그래프 쿼리*](how-to-query-graph.md)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

이 문서에서는 사용자가 제공한 패치를 사용 하 여 Azure Digital Twins 인스턴스의 쌍을 정기적으로 업데이트 하는 논리 앱을 만들었습니다. 사용자 지정 커넥터에서 다른 Api를 선택 하 여 인스턴스에 대 한 다양 한 작업에 대 한 Logic Apps를 만들 수 있습니다.

사용 가능한 Api 작업 및 요구 사항에 대 한 자세한 내용을 보려면 [*방법: Azure Digital Twins api 및 Sdk 사용*](how-to-use-apis-sdks.md)을 참조 하세요.