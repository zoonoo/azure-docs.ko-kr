---
title: Visual Studio Code에서 Logic Apps Preview 워크플로 만들기
description: Azure Logic Apps (미리 보기) 확장을 사용 하 여 Visual Studio Code의 자동화 및 통합 시나리오에 대 한 워크플로를 작성 하 고 실행 합니다.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, az-logic-apps-dev
ms.topic: conceptual
ms.date: 03/08/2021
ms.openlocfilehash: f7f8082cc9120345336610d5cb49741140d3b606
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102557015"
---
# <a name="create-stateful-and-stateless-workflows-in-visual-studio-code-with-the-azure-logic-apps-preview-extension"></a>Azure Logic Apps (미리 보기) 확장을 사용 하 여 Visual Studio Code에 상태 저장 및 상태 비저장 워크플로 만들기

> [!IMPORTANT]
> 이 기능은 공개 미리 보기 상태이고 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

[Azure Logic Apps 미리 보기](logic-apps-overview-preview.md)를 사용 하면 Azure Logic Apps (미리 보기) 확장을 사용 하 여 Visual Studio Code에서 [ *상태 저장* 및 *상태 비저장* 워크플로](logic-apps-overview-preview.md#stateful-stateless) 를 포함 하는 논리 앱을 만들고 실행 하 여 앱, 데이터, 클라우드 서비스 및 시스템 간에 자동화 및 통합 솔루션을 빌드할 수 있습니다. 이 새 논리 앱 유형을 사용 하 여 Azure 뿐만 아니라 Docker 컨테이너를 비롯 하 여 다양 한 호스팅 환경에서 이식성, 향상 된 성능 및 유연성을 제공 하는 다시 디자인 된 Azure Logic Apps 미리 보기 런타임으로 구동 되는 여러 워크플로를 빌드할 수 있습니다. 새 논리 앱 유형에 대 한 자세한 내용은 [Azure Logic Apps 미리 보기 개요](logic-apps-overview-preview.md)를 참조 하세요.

![Visual Studio Code, 논리 앱 프로젝트 및 워크플로를 보여 주는 스크린샷](./media/create-stateful-stateless-workflows-visual-studio-code/visual-studio-code-logic-apps-overview.png)

Visual Studio Code에서 Azure Logic Apps (미리 보기) 확장을 사용 하 여 개발 환경에서 논리 앱의 워크플로를 *로컬로* 빌드 및 실행할 수 있는 프로젝트를 만들어 시작할 수 있습니다. [Azure Portal에서 새 **논리 앱 (미리 보기)** 리소스를 만들](create-stateful-stateless-workflows-azure-portal.md)수도 있지만 두 가지 방법 모두 동일한 종류의 호스팅 환경에서 논리 앱을 배포 하 고 실행할 수 있는 기능을 제공 합니다.

한편으로는 여전히 원래 논리 앱 유형을 만들 수 있습니다. Visual Studio Code의 개발 환경은 원본 및 새 논리 앱 유형에 따라 다르지만 Azure 구독은 두 유형을 모두 포함할 수 있습니다. Azure 구독에서 배포 된 모든 논리 앱을 보고 액세스할 수 있지만 앱은 자신의 범주 및 섹션으로 구성 됩니다.

이 문서에서는 Azure Logic Apps (미리 보기) 확장을 사용 하 고 이러한 고급 작업을 수행 하 여 Visual Studio Code에서 논리 앱 및 워크플로를 만드는 방법을 보여 줍니다.

* 논리 앱 및 워크플로에 대 한 프로젝트를 만듭니다.

* 트리거와 작업을 추가 합니다.

* 실행 기록을 로컬로 실행, 테스트, 디버그 및 검토 합니다.

* 방화벽 액세스를 위한 도메인 이름 세부 정보를 찾습니다.

* 필요에 따라 Application Insights를 사용 하도록 설정 하는 Azure에 배포 합니다.

* Visual Studio Code 및 Azure Portal에서 배포 된 논리 앱을 관리 합니다.

* 상태 비저장 워크플로에 대 한 실행 기록을 사용 하도록 설정 합니다.

* 배포 후 Application Insights를 사용 하거나 엽니다.

* 어디에서 나 실행할 수 있는 Docker 컨테이너에 배포 합니다.

> [!NOTE]
> 현재 알려진 문제에 대 한 자세한 내용은 [GitHub에서 공개 미리 보기의 알려진 문제 Logic Apps 페이지](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md)를 참조 하세요.

## <a name="prerequisites"></a>전제 조건

### <a name="access-and-connectivity"></a>액세스 및 연결

* 인터넷에 액세스 하 여 요구 사항을 다운로드 하 고, Visual Studio Code에서 Azure 계정으로 연결 하 고, Visual Studio Code에서 Azure, Docker 컨테이너 또는 다른 환경으로 게시할 수 있습니다.

* Azure 계정 및 구독 구독이 없는 경우 [Azure 체험 계정에 등록](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)합니다.

* 이 문서에서 동일한 예제 논리 앱을 빌드하려면 Microsoft 회사 또는 학교 계정을 사용 하 여 로그인 하는 Office 365 Outlook 전자 메일 계정이 필요 합니다.

  Outlook.com 또는 [Gmail](../connectors/connectors-google-data-security-privacy-policy.md)과 같은 [Azure Logic Apps에서 지 원하는 다른 전자 메일 커넥터](/connectors/)를 사용 하도록 선택 하는 경우에도 예제를 계속 진행할 수 있지만 일반적인 전체 단계는 동일 하지만 사용자 인터페이스와 옵션은 일부 면에서 다를 수 있습니다. 예를 들어 Outlook.com 커넥터를 사용 하는 경우 대신 개인 Microsoft 계정를 사용 하 여 로그인 합니다.

<a name="storage-requirements"></a>

### <a name="storage-requirements"></a>저장소 요구 사항

#### <a name="windows"></a>Windows

Windows를 사용할 때 Visual Studio Code에서 논리 앱 프로젝트를 로컬로 빌드 및 실행 하려면 다음 단계에 따라 Azure Storage 에뮬레이터를 설정 합니다.

1. [Azure Storage Emulator 5.10](https://go.microsoft.com/fwlink/p/?linkid=717179)를 다운로드 하 여 설치 합니다.

1. 아직 없는 경우 에뮬레이터를 실행할 수 있도록 무료 [SQL Server 2019 Express Edition](https://go.microsoft.com/fwlink/p/?linkid=866658)과 같은 로컬 SQL DB가 설치 되어 있어야 합니다.

   자세한 내용은 [Azure Storage 에뮬레이터를 사용 하 여 개발 및 테스트](../storage/common/storage-use-emulator.md)를 참조 하세요.

1. 프로젝트를 실행 하려면 먼저 에뮬레이터를 시작 해야 합니다.

   ![실행 중인 Azure Storage 에뮬레이터를 보여 주는 스크린샷](./media/create-stateful-stateless-workflows-visual-studio-code/start-storage-emulator.png)

#### <a name="macos-and-linux"></a>macOS 및 Linux

MacOS 또는 Linux를 사용할 때 Visual Studio Code에서 논리 앱 프로젝트를 로컬로 빌드 및 실행 하려면 다음 단계를 수행 하 여 Azure Storage 계정을 만들고 설정 합니다.

> [!NOTE]
> 현재 Visual Studio Code의 디자이너는 Linux OS에서 작동 하지 않지만, Logic Apps Preview runtime을 사용 하는 논리 앱의 빌드, 실행 및 배포를 계속 실행 하 여 Linux 기반 가상 컴퓨터에 배포할 수 있습니다. 지금은 Windows 또는 macOS에서 Visual Studio Code 하 여 논리 앱을 빌드한 다음 Linux 기반 가상 머신에 배포할 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인 하 고 [Azure Functions에 대 한 필수 구성](../azure-functions/storage-considerations.md)요소인 [Azure Storage 계정을 만듭니다](../storage/common/storage-account-create.md?tabs=azure-portal).

1. 저장소 계정 메뉴의 **설정** 에서 **액세스 키** 를 선택 합니다.

1. **액세스 키** 창에서 저장소 계정의 연결 문자열을 찾아 복사 합니다 .이 예는 다음과 같습니다.

   `DefaultEndpointsProtocol=https;AccountName=fabrikamstorageacct;AccountKey=<access-key>;EndpointSuffix=core.windows.net`

   ![저장소 계정 액세스 키와 연결 문자열이 복사 된 Azure Portal를 보여 주는 스크린샷](./media/create-stateful-stateless-workflows-visual-studio-code/find-storage-account-connection-string.png)

   자세한 내용은 [저장소 계정 키 관리](../storage/common/storage-account-keys-manage.md?tabs=azure-portal#view-account-access-keys)를 참조 하세요.

1. 안전한 곳에 연결 문자열을 저장 합니다. Visual Studio Code에서 논리 앱 프로젝트를 만든 후에는 프로젝트의 루트 수준 폴더에 있는 파일 **의local.settings.js** 에 문자열을 추가 해야 합니다.

   > [!IMPORTANT]
   > Docker 컨테이너에 배포 하려는 경우 배포에 사용 하는 Docker 파일에이 연결 문자열을 추가 해야 합니다.

### <a name="tools"></a>도구

* [Visual Studio Code 1.30.1 (1 월 2019) 이상](https://code.visualstudio.com/)이며 무료입니다. 또한 이러한 도구를 아직 설치 하지 않은 경우 다운로드 하 여 설치 Visual Studio Code 합니다.

  * [Azure 계정 확장](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)-Visual Studio Code의 다른 모든 azure 확장에 대해 단일 공통 azure 로그인 및 구독 필터링 환경을 제공 합니다.

  * [C # for Visual Studio Code 확장](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)-F5 기능을 사용 하 여 논리 앱을 실행할 수 있습니다.

  * Microsoft Installer (MSI) 버전 ()을 사용 하 여 [3.0.3245 이상을 Azure Functions Core Tools](https://github.com/Azure/azure-functions-core-tools/releases/tag/3.0.3245) `func-cli-3.0.3245-x*.msi` 합니다.

    이러한 도구에는 미리 보기 확장이 Visual Studio Code에서 사용 하는 Azure Functions 런타임을 구동 하는 동일한 런타임의 버전이 포함 되어 있습니다.

    > [!IMPORTANT]
    > 이러한 버전 보다 이전 버전인 경우 먼저 해당 버전을 제거 하거나 PATH 환경 변수가 다운로드 및 설치 버전을 가리키는지 확인 합니다.

  * [Visual Studio Code에 대 한 Azure Logic Apps (미리 보기) 확장](https://go.microsoft.com/fwlink/p/?linkid=2143167)입니다. 이 확장은 Visual Studio Code에서 로컬로 실행 되는 상태 저장 및 상태 비저장 워크플로를 빌드하고 Azure 또는 Docker 컨테이너에 직접 배포 하는 논리 앱을 만들 수 있는 기능을 제공 합니다.

    현재는 원래 Azure Logic Apps 확장 및 공용 미리 보기 확장이 Visual Studio Code에 설치 되어 있을 수 있습니다. 확장 간에 개발 환경에 차이가 있기는 하지만 Azure 구독에는 확장을 사용 하 여 만든 논리 앱 유형이 모두 포함 될 수 있습니다. Visual Studio Code는 Azure 구독에 배포 된 모든 논리 앱을 표시 하지만, 확장 이름, **Logic Apps** 및 **Azure Logic Apps (미리 보기)** 를 통해 서로 다른 섹션으로 구성 합니다.

    > [!IMPORTANT]
    > 이전 비공개 미리 보기 확장을 사용 하 여 논리 앱 프로젝트를 만든 경우 이러한 프로젝트는 공개 미리 보기 확장에서 작동 하지 않습니다. 그러나 비공개 미리 보기 확장을 제거 하 고, 연결 된 파일을 삭제 하 고, 공개 미리 보기 확장을 설치 하면 이러한 프로젝트를 마이그레이션할 수 있습니다. 그런 다음 Visual Studio Code에서 새 프로젝트를 만들고 이전에 만든 논리 앱의 **워크플로. 정의** 파일을 새 프로젝트에 복사 합니다. 자세한 내용은 [비공개 미리 보기 확장에서 마이그레이션](#migrate-private-preview)을 참조 하세요.
    > 
    > 이전 공개 미리 보기 확장을 사용 하 여 논리 앱 프로젝트를 만든 경우 마이그레이션 단계 없이 해당 프로젝트를 계속 사용할 수 있습니다.

    ****Azure Logic Apps (미리 보기)** 확장을 설치 하려면 다음 단계를 수행 합니다.**

    1. Visual Studio Code의 왼쪽 도구 모음에서 **확장** 을 선택 합니다.

    1. 확장 검색 상자에을 입력 `azure logic apps preview` 합니다. 결과 목록에서 **Azure Logic Apps (미리 보기)** **>** **설치** 를 선택 합니다.

       설치가 완료 되 면 미리 보기 확장이 **확장: 설치** 목록에 표시 됩니다.

       !["Azure Logic Apps (미리 보기)" 확장명이 밑줄로 표시 된 Visual Studio Code의 설치 된 확장 목록을 보여 주는 스크린샷](./media/create-stateful-stateless-workflows-visual-studio-code/azure-logic-apps-extension-installed.png)

       > [!TIP]
       > 확장이 설치 된 목록에 표시 되지 않으면 Visual Studio Code를 다시 시작 하십시오.

* JavaScript를 실행 하는 [인라인 코드 작업 동작](../logic-apps/logic-apps-add-run-inline-code.md) 을 사용 하려면 [Node.js 버전 2.x, 4.x 또는 12. x.](https://nodejs.org/en/download/releases/)x를 설치 합니다.

  > [!TIP] 
  > Windows의 경우 MSI 버전을 다운로드 합니다. ZIP 버전을 대신 사용 하는 경우 운영 체제에 대 한 PATH 환경 변수를 사용 하 여 Node.js 수동으로 사용 하도록 설정 해야 합니다.

* Visual Studio Code에서 [기본 제공 HTTP](../connectors/connectors-native-webhook.md)웹 후크 트리거와 같은 webhook 기반 트리거 및 작업을 로컬로 실행 하려면 [콜백 URL에 대 한 전달을 설정](#webhook-setup)해야 합니다.

* 이 문서에서 만든 예제 논리 앱을 테스트 하려면 예제 논리 앱의 첫 단계인 요청 트리거에 대 한 호출을 보낼 수 있는 도구가 필요 합니다. 이러한 도구가 없는 경우 [Postman](https://www.postman.com/downloads/)을 다운로드 하 여 설치 하 고 사용할 수 있습니다.

* 논리 앱을 만들고 [Application Insights](../azure-monitor/app/app-insights-overview.md)를 지 원하는 설정을 사용 하 여 배포 하는 경우 필요에 따라 논리 앱에 대 한 진단 로깅 및 추적을 사용 하도록 설정할 수 있습니다. Visual Studio Code 또는 배포 후에 논리 앱을 배포할 때 이러한 작업을 수행할 수 있습니다. Application Insights 인스턴스가 필요 하지만, 논리 앱을 배포할 때 또는 배포 후에이 리소스를 [미리](../azure-monitor/app/create-workspace-resource.md)만들 수 있습니다.

<a name="migrate-private-preview"></a>

## <a name="migrate-from-private-preview-extension"></a>비공개 미리 보기 확장에서 마이그레이션

**Azure Logic Apps (비공개 미리 보기)** 확장을 사용 하 여 만든 논리 앱 프로젝트는 공개 미리 보기 확장에서 작동 하지 않습니다. 그러나 다음 단계를 수행 하 여 이러한 프로젝트를 새 프로젝트로 마이그레이션할 수 있습니다.

1. 비공개 미리 보기 확장을 제거 합니다.

1. 다음 위치에서 연결 된 확장 번들 및 NuGet 패키지 폴더를 삭제 합니다.

   * 이전 확장 번들을 포함 하 고 다음 경로를 따라 배치 된 **Microsoft. Node.js 번들. 워크플로** 폴더.

     * `C:\Users\{userName}\AppData\Local\Temp\Functions\ExtensionBundles`

     * `C:\Users\{userName}\.azure-functions-core-tools\Functions\ExtensionBundles`

   * 비공개 미리 보기 확장에 대 한 [NuGet](/nuget/what-is-nuget) 캐시이 고이 경로를 따라 배치 되는 **webjobs** 폴더입니다.

     `C:\Users\{userName}\.nuget\packages`

1. **Azure Logic Apps (미리 보기)** 확장을 설치 합니다.

1. Visual Studio Code에서 새 프로젝트를 만듭니다.

1. 이전에 만든 논리 앱의 **워크플로 정의** 파일을 새 프로젝트에 복사 합니다.

<a name="set-up"></a>

## <a name="set-up-visual-studio-code"></a>Visual Studio Code 설정

1. 모든 확장이 제대로 설치 되었는지 확인 하려면 다시 로드 하거나 Visual Studio Code를 다시 시작 합니다.

1. Preview 확장에서 최신 업데이트를 가져오는 Visual Studio Code 자동으로 확장 업데이트를 찾아서 설치 하는지 확인 합니다. 그렇지 않으면 오래 된 버전을 수동으로 제거 하 고 최신 버전을 설치 해야 합니다.

   1. **파일** 메뉴에서 **기본** **>** **설정 설정** 으로 이동 합니다.

   1. **사용자** 탭에서 **기능** **>** **확장** 으로 이동 합니다.

   1. **업데이트 자동 확인** 및 **자동 업데이트** 가 선택 되어 있는지 확인 합니다.

또한 기본적으로 Logic Apps preview 확장에 대해 다음 설정을 사용 하도록 설정 하 고 설정 합니다.

* **Azure Logic Apps V2:** 버전 **~ 3** 으로 설정 된 Project Runtime

  > [!NOTE]
  > 이 버전은 [인라인 코드 작업](../logic-apps/logic-apps-add-run-inline-code.md)을 사용 하는 데 필요 합니다.

* **Azure Logic Apps V2:** Visual Studio Code에서 최신 디자이너를 사용 하도록 설정 하는 실험적 뷰 관리자입니다. 항목 끌어서 놓기와 같이 디자이너에서 문제가 발생 하는 경우이 설정을 해제 합니다.

이러한 설정을 찾아서 확인 하려면 다음 단계를 수행 합니다.

1. **파일** 메뉴에서 **기본** **>** **설정 설정** 으로 이동 합니다.

1. **사용자** 탭에서 **>** **확장** **>** **Azure Logic Apps (미리 보기)** 로 이동 합니다.

   예를 들어, 여기에서 **V2: Project Runtime 설정 Azure Logic Apps** 찾거나 검색 상자를 사용 하 여 다른 설정을 찾을 수 있습니다.

   !["Azure Logic Apps (미리 보기)" 확장에 대 한 Visual Studio Code 설정을 보여 주는 스크린샷](./media/create-stateful-stateless-workflows-visual-studio-code/azure-logic-apps-preview-settings.png)

<a name="connect-azure-account"></a>

## <a name="connect-to-your-azure-account"></a>Azure 계정에 연결

1. Visual Studio Code 활동 표시줄에서 Azure 아이콘을 선택 합니다.

   ![활동 표시줄과 선택한 Azure 아이콘 Visual Studio Code 보여 주는 스크린샷](./media/create-stateful-stateless-workflows-visual-studio-code/visual-studio-code-azure-icon.png)

1. Azure 창의 **azure: Logic Apps (미리 보기)** 에서 **azure에 로그인** 을 선택 합니다. Visual Studio Code 인증 페이지가 표시 되 면 Azure 계정으로 로그인 합니다.

   ![Azure 로그인에 대 한 Azure 창 및 선택한 링크를 보여 주는 스크린샷](./media/create-stateful-stateless-workflows-visual-studio-code/sign-in-azure-subscription.png)

   로그인 한 후 Azure 창에는 Azure 계정에 구독이 표시 됩니다. 또한 공개적으로 릴리스된 확장이 있는 경우 **Logic Apps (미리 보기)** 섹션이 아닌 **Logic Apps** 섹션에서 해당 확장을 사용 하 여 만든 논리 앱을 찾을 수 있습니다.
   
   예상 되는 구독이 표시 되지 않거나이 창에 특정 구독만 표시 하려면 다음 단계를 수행 합니다.

   1. 구독 목록에서 **구독 선택** 단추 (필터 아이콘)가 나타날 때까지 첫 번째 구독 옆으로 포인터를 이동 합니다. 필터 아이콘을 선택합니다.

      ![Azure 창 및 선택한 필터 아이콘을 보여 주는 스크린샷](./media/create-stateful-stateless-workflows-visual-studio-code/filter-subscription-list.png)

      또는 Visual Studio Code 상태 표시줄에서 Azure 계정을 선택 합니다. 

   1. 다른 구독 목록이 표시 되 면 원하는 구독을 선택한 다음 **확인** 을 선택 했는지 확인 합니다.

<a name="create-project"></a>

## <a name="create-a-local-project"></a>로컬 프로젝트 만들기

논리 앱을 만들기 전에 Visual Studio Code에서 논리 앱을 관리, 실행 및 배포할 수 있도록 로컬 프로젝트를 만듭니다. 기본 프로젝트는 함수 앱 프로젝트 라고도 하는 Azure Functions 프로젝트와 비슷합니다. 그러나 이러한 프로젝트 형식은 서로 별개 이므로 동일한 프로젝트에 논리 앱과 함수 앱을 함께 사용할 수 없습니다.

1. 컴퓨터에서 나중에 Visual Studio Code에서 만들 프로젝트에 사용할 *빈* 로컬 폴더를 만듭니다.

1. Visual Studio Code에서 열려 있는 모든 폴더를 닫습니다.

1. Azure 창에서 **azure: Logic Apps (미리 보기)** 옆에 있는 **새 프로젝트 만들기** (폴더 및 라이트닝 볼트를 표시 하는 아이콘)를 선택 합니다.

   !["새 프로젝트 만들기"가 선택 된 Azure 창 도구 모음을 보여 주는 스크린샷](./media/create-stateful-stateless-workflows-visual-studio-code/create-new-project-folder.png)

1. Windows Defender 방화벽에서 Azure Functions Core Tools Visual Studio Code 된에 대 한 네트워크 액세스 권한을 부여 하 라는 메시지가 표시 되 면 `Code.exe` `func.exe` **홈 또는 회사 네트워크에서 액세스 허용과 같은 개인 네트워크를** 선택 **>** 합니다.

1. 프로젝트 폴더를 만든 위치로 이동 하 여 해당 폴더를 선택 하 고 계속 합니다.

   ![새로 만든 프로젝트 폴더와 "선택" 단추가 선택 된 "폴더 선택" 대화 상자를 보여 주는 스크린샷](./media/create-stateful-stateless-workflows-visual-studio-code/select-project-folder.png)

1. 표시 되는 템플릿 목록에서 **상태 저장 워크플로** 또는 **상태 비저장 워크플로** 를 선택 합니다. 이 예제에서는 **상태 저장 워크플로** 를 선택 합니다.

   !["상태 저장 워크플로"가 선택 된 워크플로 템플릿 목록을 보여 주는 스크린샷](./media/create-stateful-stateless-workflows-visual-studio-code/select-stateful-stateless-workflow.png)

1. 워크플로의 이름을 제공 하 고 Enter 키를 누릅니다. 이 예에서는 `Fabrikam-Stateful-Workflow` 를 이름으로 사용 합니다.

   !["새 상태 저장 워크플로 만들기 (3/4)" 상자와 "Fabrikam-상태 저장 워크플로"를 워크플로 이름으로 보여 주는 스크린샷](./media/create-stateful-stateless-workflows-visual-studio-code/name-your-workflow.png)

   Visual Studio Code 프로젝트 만들기를 완료 하 고 코드 편집기에서 워크플로에 대 한 **workflow.js** 파일을 엽니다.

   > [!NOTE]
   > 프로젝트를 여는 방법을 선택 하 라는 메시지가 표시 되 면 현재 Visual Studio Code 창에서 프로젝트를 열려는 경우 **현재 창에서 열기** 를 선택 합니다. Visual Studio Code에 대 한 새 인스턴스를 열려면 **새 창에서 열기** 를 선택 합니다.

1. Visual Studio 도구 모음에서 탐색기 창을 엽니다 (아직 열려 있지 않은 경우).

   탐색기 창에는 프로젝트가 자동으로 생성 된 프로젝트 파일을 포함 하 여 표시 됩니다. 예를 들어 프로젝트에 워크플로 이름을 표시 하는 폴더가 있습니다. 이 폴더 내에서 파일 **의workflow.js** 에는 워크플로의 기본 JSON 정의가 포함 되어 있습니다.

   ![프로젝트 폴더, 워크플로 폴더 및 "workflow.json" 파일을 사용 하 여 탐색기 창을 보여 주는 스크린샷](./media/create-stateful-stateless-workflows-visual-studio-code/local-project-created.png)

1. MacOS 또는 Linux를 사용 하는 경우 프로젝트를 로컬로 실행 하는 데 필요한 다음 단계를 수행 하 여 저장소 계정에 대 한 액세스를 설정 합니다.

   1. 프로젝트의 루트 폴더에서 파일 **에local.settings.js** 를 엽니다.

      ![탐색기 창 및 프로젝트의 ' local.settings.json ' 파일을 보여 주는 스크린샷](./media/create-stateful-stateless-workflows-visual-studio-code/local-settings-json-files.png)

   1. `AzureWebJobsStorage`속성 값을 앞에서 저장 한 저장소 계정의 연결 문자열로 바꿉니다. 예를 들면 다음과 같습니다.

      이전:

      ```json
      {
         "IsEncrypted": false,
         "Values": {
            "AzureWebJobsStorage": "UseDevelopmentStorage=true",
            "FUNCTIONS_WORKER_RUNTIME": "dotnet"
          }
      }
      ```

      이후:

      ```json
      {
         "IsEncrypted": false,
         "Values": {
            "AzureWebJobsStorage": "DefaultEndpointsProtocol=https;AccountName=fabrikamstorageacct;AccountKey=<access-key>;EndpointSuffix=core.windows.net",
           "FUNCTIONS_WORKER_RUNTIME": "dotnet"
         }
      }
      ```

   1. 완료 되 면 변경 내용을 저장 해야 합니다.

<a name="enable-built-in-connector-authoring"></a>

## <a name="enable-built-in-connector-authoring"></a>기본 제공 커넥터 제작 사용

[미리 보기 릴리스의 확장성 프레임 워크](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-built-in-connector/ba-p/1921272)를 사용 하 여 필요한 모든 서비스에 대 한 기본 제공 커넥터를 만들 수 있습니다. Azure Service Bus 및 SQL Server와 같은 기본 제공 커넥터와 마찬가지로, 이러한 커넥터는 높은 처리량, 짧은 대기 시간, 로컬 연결을 제공 하 고 미리 보기 런타임과 동일한 프로세스에서 기본적으로 실행 됩니다.

제작 기능은 현재 Visual Studio Code 에서만 사용할 수 있지만 기본적으로 사용 하도록 설정 되어 있지 않습니다. 이러한 커넥터를 만들려면 먼저 확장 번들 기반 (Node.js)에서 NuGet 패키지 기반 (.NET)으로 프로젝트를 변환 해야 합니다.

> [!IMPORTANT]
> 이 작업은 실행 취소할 수 없는 단방향 작업입니다.

1. 탐색기 창의 프로젝트 루트에서 다른 모든 파일 및 폴더 아래에 있는 빈 영역 위로 마우스 포인터를 이동 하 고 바로 가기 메뉴를 연 다음 **Nuget 기반 논리 앱 프로젝트로 변환** 을 선택 합니다.

   ![프로젝트 창의 빈 영역에서 프로젝트의 바로 가기 메뉴를 연 상태에서 탐색기 창이 표시 된 것을 보여 주는 스크린샷](./media/create-stateful-stateless-workflows-visual-studio-code/convert-logic-app-project.png)

1. 프롬프트가 표시 되 면 프로젝트 변환을 확인 합니다.

1. 계속 하려면 [원격-기본 제공 커넥터 확장성을 실행](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-built-in-connector/ba-p/1921272)하는 Azure Logic Apps 문서의 단계를 검토 하 고 수행 합니다.

<a name="open-workflow-definition-designer"></a>

## <a name="open-the-workflow-definition-file-in-the-designer"></a>디자이너에서 워크플로 정의 파일을 엽니다.

1. 다음 명령을 실행 하 여 컴퓨터에 설치 된 버전을 확인 합니다.

   `..\Users\{yourUserName}\dotnet --list-sdks`

   .NET Core SDK 5.x를 사용 하는 경우이 버전을 사용 하면 디자이너에서 논리 앱의 기본 워크플로 정의를 열 수 없습니다. 이 버전을 제거 하는 대신 프로젝트의 루트 폴더에서 3.1.201 보다 이후 버전의 .NET Core 런타임 2.x 버전을 참조 하는 파일 **에global.js** 를 만듭니다. 예를 들면 다음과 같습니다.

   ```json
   {
      "sdk": {
         "version": "3.1.8",
         "rollForward": "disable"
      }
   }
   ```

   > [!IMPORTANT]
   > Visual Studio Code 내부에서 프로젝트의 루트 폴더에 있는 파일 **에global.js** 를 명시적으로 추가 해야 합니다. 그렇지 않으면 디자이너가 열리지 않습니다.

1. 워크플로에 대 한 프로젝트 폴더를 확장 합니다. 파일의 바로 가기 메뉴에서 **workflow.js** 을 열고 **디자이너에서 열기** 를 선택 합니다.

   !["디자이너에서 열기"가 선택 된 파일에 대 한 workflow.js의 탐색기 창 및 바로 가기 창을 보여 주는 스크린샷](./media/create-stateful-stateless-workflows-visual-studio-code/open-definition-file-in-designer.png)

1. Azure에서 **커넥터** 사용 목록에서 azure 서비스에 대 한 커넥터 뿐만 아니라 azure에서 사용 가능 하 고 배포 되는 모든 관리 되는 커넥터에 적용 되는 **Azure의 커넥터 사용** 을 선택 합니다.

   !["Azure에서 커넥터 사용" 목록과 "Azure에서 커넥터 사용"이 선택 된 탐색기 창을 보여 주는 스크린샷](./media/create-stateful-stateless-workflows-visual-studio-code/use-connectors-from-azure.png)

   > [!NOTE]
   > 상태 비저장 워크플로는 현재 Azure에 배포 되 고 트리거가 아닌 [관리 되는 커넥터](../connectors/apis-list.md#managed-api-connectors)에 대 한 *동작만* 지원 합니다. 상태 비저장 워크플로에 대해 Azure에서 커넥터를 사용 하도록 설정 하는 옵션이 있지만 사용자가 선택할 수 있는 관리 되는 커넥터 트리거가 디자이너에 표시 되지 않습니다.

1. **구독 선택** 목록에서 논리 앱 프로젝트에 사용할 Azure 구독을 선택 합니다.

   !["구독 선택" 상자와 구독이 선택 된 상태에서 탐색기 창을 보여 주는 스크린샷](./media/create-stateful-stateless-workflows-visual-studio-code/select-azure-subscription.png)

1. 리소스 그룹 목록에서 **새 리소스 그룹 만들기** 를 선택 합니다.

   ![리소스 그룹 목록과 "새 리소스 그룹 만들기"가 선택 된 탐색기 창을 보여 주는 스크린샷](./media/create-stateful-stateless-workflows-visual-studio-code/create-select-resource-group.png)

1. 리소스 그룹의 이름을 입력 하 고 enter 키를 누릅니다. 이 예에서는 `Fabrikam-Workflows-RG`를 사용합니다.

   ![탐색기 창 및 리소스 그룹 이름 상자를 표시 하는 스크린샷](./media/create-stateful-stateless-workflows-visual-studio-code/enter-name-for-resource-group.png)

1. 위치 목록에서 리소스 그룹 및 리소스를 만들 때 사용할 Azure 지역을 찾아 선택 합니다. 이 예제에서는 **미국 서 부** 를 사용 합니다.

   ![위치 목록 및 "미국 서 부 중부"가 선택 된 탐색기 창을 표시 하는 스크린샷](./media/create-stateful-stateless-workflows-visual-studio-code/select-azure-region.png)

   이 단계를 수행한 후 Visual Studio Code workflow designer를 엽니다.

   > [!NOTE]
   > Visual Studio Code 워크플로 디자인 타임 API를 시작 하면 시작 하는 데 몇 초 정도 걸릴 수 있습니다. 이 메시지를 무시 하거나 **확인을** 선택할 수 있습니다.
   >
   > 디자이너가 열리지 않는 경우 문제 해결 섹션을 검토 합니다. [디자이너가 열리지](#designer-fails-to-open)않습니다.

   디자이너가 표시 되 면 **작업 선택** 프롬프트가 디자이너에 표시 되며 기본적으로 선택 됩니다. **그러면 작업 추가** 창이 표시 됩니다.

   ![Workflow designer를 보여 주는 스크린샷](./media/create-stateful-stateless-workflows-visual-studio-code/workflow-app-designer.png)

1. 다음으로, 워크플로에 [트리거와 작업을 추가](#add-trigger-actions) 합니다.

<a name="add-trigger-actions"></a>

## <a name="add-a-trigger-and-actions"></a>트리거 및 작업 추가

디자이너를 열면 **작업 선택** 프롬프트가 디자이너에 표시 되며 기본적으로 선택 됩니다. 이제 트리거 및 작업을 추가 하 여 워크플로 만들기를 시작할 수 있습니다.

이 예제의 워크플로는이 트리거와 다음 작업을 사용 합니다.

* **HTTP 요청** 을 받으면 인바운드 호출 또는 요청을 수신 하 고 다른 서비스 또는 논리 앱이 호출할 수 있는 끝점을 만드는 기본 제공 [요청 트리거입니다](../connectors/connectors-native-reqres.md).

* [Office 365 Outlook 작업](../connectors/connectors-create-api-office365-outlook.md)에서 **전자 메일을 보냅니다**.

* 회신을 보내고 호출자에 게 데이터를 반환 하는 데 사용 하는 기본 제공 [응답 동작](../connectors/connectors-native-reqres.md)입니다.

### <a name="add-the-request-trigger"></a>요청 트리거 추가

1. 디자이너 옆의 **트리거 추가** 창에 있는 **작업 선택** 검색 상자에서 **기본 제공** 이 선택 되어 있는지 확인 하 여 기본적으로 실행 되는 트리거를 선택할 수 있습니다.

1. **작업 선택** 검색 상자에를 입력 하 `when a http request` 고 **HTTP 요청을 받을 때** 이름이 지정 된 기본 제공 요청 트리거를 선택 합니다.

   !["HTTP 요청을 받았을 때" 트리거가 선택 된 상태에서 워크플로 디자이너 및 * * 트리거 추가 * * 창을 보여 주는 스크린샷](./media/create-stateful-stateless-workflows-visual-studio-code/add-request-trigger.png)

   트리거가 디자이너에 나타나면 트리거의 세부 정보 창이 열리며 트리거의 속성, 설정 및 기타 작업을 표시 합니다.

   !["HTTP 요청을 받았을 때" 트리거를 선택 하 고 트리거 세부 정보 창이 열려 있는 workflow designer를 보여 주는 스크린샷](./media/create-stateful-stateless-workflows-visual-studio-code/request-trigger-added-to-designer.png)

   > [!TIP]
   > 세부 정보 창이 표시 되지 않으면 디자이너에서 트리거를 선택 해야 합니다.

1. 디자이너에서 항목을 삭제 해야 하는 경우 [디자이너에서 항목](#delete-from-designer)을 삭제 하려면 다음 단계를 수행 합니다.

### <a name="add-the-office-365-outlook-action"></a>Office 365 Outlook 작업 추가

1. 디자이너에서 추가한 트리거에서 **새 단계** 를 선택 합니다.

   **작업 선택** 프롬프트가 디자이너에 표시 되며 다음 작업을 선택할 수 있도록 **작업 추가** 창이 다시 열립니다.

1. 작업 **추가** 창의 **작업 선택** 검색 상자 아래 **에서 Azure를 선택 하** 여 azure에 배포 된 관리 되는 커넥터에 대 한 작업을 찾아 선택할 수 있습니다.

   이 예제에서는 Office 365 Outlook 작업, **전자 메일 보내기 (V2)** 를 선택 하 고 사용 합니다.

   ![Office 365 Outlook "전자 메일 보내기" 작업을 선택 하 여 workflow designer 및 * * 작업 추가 * * 창을 보여 주는 스크린샷](./media/create-stateful-stateless-workflows-visual-studio-code/add-send-email-action.png)

1. 작업의 세부 정보 창에서 **로그인** 을 선택 하 여 전자 메일 계정에 대 한 연결을 만들 수 있습니다.

   !["로그인"이 선택 된 상태에서 워크플로 디자이너 및 * * 전자 메일 보내기 (V2) * * 창을 보여 주는 스크린샷](./media/create-stateful-stateless-workflows-visual-studio-code/send-email-action-sign-in.png)

1. Visual Studio Code 전자 메일 계정에 대 한 액세스에 대 한 동의를 묻는 메시지가 표시 되 면 **열기** 를 선택 합니다.

   ![액세스를 허용 하는 Visual Studio Code 프롬프트를 보여 주는 스크린샷](./media/create-stateful-stateless-workflows-visual-studio-code/visual-studio-code-open-external-website.png)

   > [!TIP]
   > 이후 프롬프트를 방지 하려면 트러스트 된 도메인 **구성** 을 선택 하 여 인증 페이지를 트러스트 된 도메인으로 추가할 수 있습니다.

1. 다음 프롬프트에 따라 로그인 하 여 액세스를 허용 하 고 Visual Studio Code으로 돌아갈 수 있습니다.

   > [!NOTE]
   > 메시지를 완료 하기 전에 너무 많은 시간이 경과 하면 인증 프로세스 시간이 초과 되 고 실패 합니다. 이 경우 디자이너로 돌아가서 로그인을 다시 시도 하 여 연결을 만듭니다.

1. Azure Logic Apps (미리 보기) 확장에서 전자 메일 계정에 액세스 하는 데 동의 하는지 묻는 메시지가 표시 되 면 **열기** 를 선택 합니다. 다음 프롬프트에 따라 액세스를 허용 합니다.

   ![액세스를 허용 하는 미리 보기 확장 메시지를 보여 주는 스크린샷](./media/create-stateful-stateless-workflows-visual-studio-code/allow-preview-extension-open-uri.png)

   > [!TIP]
   > 이후 메시지가 표시 되지 않도록 하려면 **이 확장에 대해 다시 묻지 않음** 을 선택 합니다.

   Visual Studio Code에서 연결을 만든 후 일부 커넥터는 메시지를 표시 `The connection will be valid for {n} days only` 합니다. 이 시간 제한은 Visual Studio Code에서 논리 앱을 작성 하는 동안에만 적용 됩니다. 배포 후에는 논리 앱이 자동으로 사용 하도록 설정 [된 시스템 할당 관리 id](../logic-apps/create-managed-service-identity.md)를 사용 하 여 런타임에 인증할 수 있으므로이 제한이 더 이상 적용 되지 않습니다. 이 관리 id는 연결을 만들 때 사용 하는 인증 자격 증명이 나 연결 문자열과 다릅니다. 이 시스템 할당 관리 id를 사용 하지 않도록 설정 하면 런타임에 연결이 작동 하지 않습니다.

1. 디자이너에서 **전자 메일 보내기** 작업이 선택 되어 있지 않으면 해당 작업을 선택 합니다.

1. 작업의 세부 정보 창에 있는 **매개 변수** 탭에서 작업에 필요한 정보를 제공 합니다. 예를 들면 다음과 같습니다.

   ![Office 365 Outlook "전자 메일 보내기" 작업에 대 한 세부 정보를 포함 하는 workflow designer를 보여 주는 스크린샷](./media/create-stateful-stateless-workflows-visual-studio-code/send-email-action-details.png)

   | 속성 | 필수 | 값 | Description |
   |----------|----------|-------|-------------|
   | **수행할 작업** | 예 | <*your-email-address*> | 전자 메일 받는 사람으로, 테스트 목적으로 전자 메일 주소를 사용할 수 있습니다. 이 예에서는 가상의 전자 메일를 사용 `sophiaowen@fabrikam.com` 합니다. |
   | **Subject** | 예 | `An email from your example workflow` | 이메일 제목 |
   | **본문** | 예 | `Hello from your example workflow!` | 전자 메일 본문 내용 |
   ||||

   > [!NOTE]
   > **설정**, **정적 결과** 또는 **다음 이후 실행** 탭의 세부 정보 창에서 변경 하려는 경우 탭을 전환 하거나 디자이너로 포커스를 변경 하기 전에 **완료** 를 선택 하 여 이러한 변경 내용을 커밋해야 합니다. 그렇지 않으면 Visual Studio Code 변경 내용을 유지 하지 않습니다. 자세한 내용은 [GitHub에서 공개 미리 보기의 알려진 문제 Logic Apps 페이지](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md)를 참조 하세요.

1. 디자이너에서 **저장** 을 선택 합니다.

> [!IMPORTANT]
> 웹 후크 기반 트리거 또는 작업 (예: [기본 제공 HTTP 웹 후크 트리거 또는 작업](../connectors/connectors-native-webhook.md))을 사용 하는 워크플로를 로컬로 실행 하려면 [WEBHOOK의 콜백 URL에 대해 전달을 설정](#webhook-setup)하 여이 기능을 사용 하도록 설정 해야 합니다.

<a name="webhook-setup"></a>

## <a name="enable-locally-running-webhooks"></a>로컬로 실행 되는 웹 후크 사용

Azure에서 실행 되는 논리 앱을 사용 하 여 **HTTP webhook** 와 같은 webhook 기반 트리거 또는 작업을 사용 하는 경우 Logic Apps 런타임은 해당 끝점을 사용 하 여 콜백 URL을 생성 하 고 등록 하 여 서비스 끝점을 구독 합니다. 그런 다음 트리거 또는 작업은 서비스 끝점이 URL을 호출할 때까지 기다립니다. 그러나 Visual Studio Code에서 작업 하는 경우 생성 된 콜백 URL은로 시작 `http://localhost:7071/...` 합니다. 이 URL은 localhost 서버에 대 한 것으로, 서비스 끝점에서이 URL을 호출할 수 없도록 전용입니다.

Visual Studio Code에서 webhook 기반 트리거와 작업을 로컬로 실행 하려면 localhost 서버를 노출 하는 공용 URL을 설정 하 고 서비스 끝점에서 웹 후크 콜백 URL로 호출을 안전 하 게 전달 해야 합니다. [**Ngrok**](https://ngrok.com/)와 같은 전달 서비스 및 도구를 사용 하 여 로컬 호스트 포트로 HTTP 터널을 열거나 사용자 고유의 도구를 사용할 수 있습니다.

#### <a name="set-up-call-forwarding-using-ngrok"></a>**Ngrok** 를 사용 하 여 호출 전달 설정

1. [ **Ngrok** 계정이 없으면 등록](https://dashboard.ngrok.com/signup) 합니다. 그렇지 않으면 [계정에 로그인](https://dashboard.ngrok.com/login)합니다.

1. 사용자의 계정에 대 한 액세스를 **ngrok** 클라이언트에서 연결 및 인증 하는 데 필요한 개인 인증 토큰을 가져옵니다.

   1. [인증 토큰 페이지](https://dashboard.ngrok.com/auth/your-authtoken)를 찾으려면 계정 대시보드 메뉴에서 **인증** 을 확장 하 고 **authtoken** 을 선택 합니다.

   1. **Authtoken** 상자에서 토큰을 안전한 위치에 복사 합니다.

1. [ **Ngrok** 다운로드 페이지](https://ngrok.com/download) 또는 [계정 대시보드에서](https://dashboard.ngrok.com/get-started/setup)원하는 **ngrok** 버전을 다운로드 하 고 .zip 파일을 추출 합니다. 자세한 내용은 [Step 1: Install 압축 해제를](https://ngrok.com/download)참조 하세요.

1. 컴퓨터에서 명령 프롬프트 도구를 엽니다. **ngrok.exe** 파일이 있는 위치로 이동 합니다.

1. 다음 명령을 실행 하 여 **ngrok** 클라이언트를 **ngrok** 계정에 연결 합니다. 자세한 내용은 [2 단계: 계정 연결](https://ngrok.com/download)을 참조 하세요.

   `ngrok authtoken <your_auth_token>`

1. 다음 명령을 실행 하 여 HTTP 터널에서 localhost 포트 7071를 엽니다. 자세한 내용은 [3 단계: 실행](https://ngrok.com/download)을 참조 하세요.

   `ngrok http 7071`

1. 출력에서 다음 줄을 찾습니다.

   `http://<domain>.ngrok.io -> http://localhost:7071`

1. 다음 형식의 URL을 복사 하 여 저장 합니다. `http://<domain>.ngrok.io`

#### <a name="set-up-the-forwarding-url-in-your-app-settings"></a>앱 설정에서 전달 URL 설정

1. Visual Studio Code의 디자이너에서 **HTTP + Webhook** 트리거 또는 작업을 추가 합니다.

1. 호스트 끝점 위치에 대 한 프롬프트가 표시 되 면 이전에 만든 전달 (리디렉션) URL을 입력 합니다.

   > [!NOTE]
   > 프롬프트를 무시 하면 전달 URL을 제공 해야 한다는 경고가 표시 됩니다. 따라서 **구성** 을 선택 하 고 url을 입력 합니다. 이 단계를 완료 한 후에는 추가 될 수 있는 후속 webhook 트리거나 작업에 대해 프롬프트가 다시 표시 되지 않습니다.
   >
   > 프롬프트를 다시 표시 하려면 프로젝트의 루트 수준에서 파일의 바로 가기 메뉴 **에서local.settings.js** 를 열고 **Webhook 리디렉션 끝점 구성** 을 선택 합니다. 이제 전달 URL을 제공할 수 있는 프롬프트가 표시 됩니다.

   Visual Studio Code 전달 URL을 프로젝트의 루트 폴더에 있는 파일 **의local.settings.js** 에 추가 합니다. 개체에서 `Values` 이제 라는 속성이 `Workflows.WebhookRedirectHostUri` 나타나고 전달 URL로 설정 됩니다. 예를 들면 다음과 같습니다.
   
   ```json
   {
      "IsEncrypted": false,
      "Values": {
         "AzureWebJobsStorage": "UseDevelopmentStorage=true",
         "FUNCTIONS_WORKER_RUNTIME": "node",
         "FUNCTIONS_V2_COMPATIBILITY_MODE": "true",
         <...>
         "Workflows.WebhookRedirectHostUri": "http://xxxXXXXxxxXXX.ngrok.io",
         <...>
      }
   }
   ```

로컬 디버깅 세션을 시작 하거나 디버깅 하지 않고 워크플로를 실행 하는 경우 Logic Apps 런타임은 워크플로를 서비스 끝점에 등록 하 고 웹 후크 작업을 알리기 위해 해당 끝점을 구독 합니다. 다음 번에 워크플로를 실행 하면 구독 등록이 로컬 저장소에 이미 있기 때문에 런타임이 등록 되거나 재구독 되지 않습니다.

로컬에서 webhook 기반 트리거 또는 작업을 실행 하는 워크플로 실행에 대 한 디버깅 세션을 중지 하면 기존 구독 등록이 삭제 되지 않습니다. 등록을 취소 하려면 구독 등록을 수동으로 제거 하거나 삭제 해야 합니다.

> [!NOTE]
> 워크플로가 실행 되기 시작 하면 터미널 창에 다음 예제와 같은 오류가 표시 될 수 있습니다.
>
> `message='Http request failed with unhandled exception of type 'InvalidOperationException' and message: 'System.InvalidOperationException: Synchronous operations are disallowed. Call ReadAsync or set AllowSynchronousIO to true instead.`
>
> 이 경우 프로젝트의 루트 폴더에 있는 파일 **에local.settings.js** 을 열고 속성이로 설정 되었는지 확인 합니다 `true` .
>
> `"FUNCTIONS_V2_COMPATIBILITY_MODE": "true"`

<a name="manage-breakpoints"></a>

## <a name="manage-breakpoints-for-debugging"></a>디버깅을 위한 중단점 관리

디버깅 세션을 시작 하 여 논리 앱 워크플로를 실행 하 고 테스트 하기 전에 각 워크플로의 **workflow.js파일에** [중단점](https://code.visualstudio.com/docs/editor/debugging#_breakpoints) 을 설정할 수 있습니다. 다른 설정은 필요 하지 않습니다. 

현재 중단점은 트리거가 아니라 작업에 대해서만 지원 됩니다. 각 작업 정의에는 다음과 같은 중단점 위치가 있습니다.

* 작업의 이름을 표시 하는 줄에서 시작 중단점을 설정 합니다. 디버깅 세션 중에이 중단점이 적중 되 면 계산 되기 전에 작업의 입력을 검토할 수 있습니다.

* 작업의 닫는 중괄호 (**}**)를 표시 하는 줄에서 종료 중단점을 설정 합니다. 디버깅 세션 중에이 중단점이 적중 될 때 작업 실행이 완료 되기 전에 작업의 결과를 검토할 수 있습니다.

중단점을 추가 하려면 다음 단계를 수행 합니다.

1. 디버그할 워크플로의 **workflow.js** 파일을 엽니다.

1. 중단점을 설정 하려는 줄의 왼쪽 열에서 해당 열 내부를 선택 합니다. 중단점을 제거 하려면 해당 중단점을 선택 합니다.

   디버깅 세션을 시작 하면 실행 뷰가 코드 창의 왼쪽에 표시 되 고 디버그 도구 모음은 위쪽 근처에 표시 됩니다.

   > [!NOTE]
   > 실행 보기가 자동으로 표시 되지 않으면 Ctrl + Shift + D를 누릅니다.

1. 중단점이 적중 될 때 사용 가능한 정보를 검토 하려면 실행 뷰에서 **변수** 창을 검사 합니다.

1. 워크플로 실행을 계속 하려면 디버그 도구 모음에서 **계속** (재생 단추)을 선택 합니다.

워크플로를 실행 하는 동안 언제 든 지 중단점을 추가 하 고 제거할 수 있습니다. 그러나 실행이 시작 된 후 파일 **의workflow.js** 를 업데이트 하는 경우 중단점은 자동으로 업데이트 되지 않습니다. 중단점을 업데이트 하려면 논리 앱을 다시 시작 합니다.

일반 정보는 [중단점-Visual Studio Code](https://code.visualstudio.com/docs/editor/debugging#_breakpoints)를 참조 하세요.

<a name="run-test-debug-locally"></a>

## <a name="run-test-and-debug-locally"></a>로컬에서 실행, 테스트 및 디버그

논리 앱을 테스트 하려면 다음 단계를 수행 하 여 디버깅 세션을 시작 하 고 요청 트리거에 의해 만들어진 끝점의 URL을 찾습니다. 나중에 해당 끝점에 요청을 보낼 수 있도록이 URL이 필요 합니다.

1. 상태 비저장 워크플로를 더 쉽게 디버깅 하기 위해 [해당 워크플로에 대 한 실행 기록을 사용 하도록 설정할](#enable-run-history-stateless)수 있습니다.

1. Visual Studio Code 활동 표시줄에서 **실행** 메뉴를 열고 **디버깅 시작** (F5)을 선택 합니다.

   디버깅 세션을 검토할 수 있도록 **터미널** 창이 열립니다.

   > [!NOTE]
   > **PreLaunchTask ' generateDebugSymbols ' "을 실행 한 후** 오류가 발생 하는 경우 문제 해결 섹션을 참조 하세요. [디버깅 세션이 시작 되지 않습니다](#debugging-fails-to-start).

1. 이제 요청 트리거에서 끝점에 대 한 콜백 URL을 찾습니다.

   1. 프로젝트를 볼 수 있도록 탐색기 창을 다시 엽니다.

   1. **workflow.js** 파일의 바로 가기 메뉴에서 **개요** 를 선택 합니다.

      !["개요"가 선택 된 파일의 workflow.js에 대 한 탐색기 창 및 바로 가기 창을 보여 주는 스크린샷](./media/create-stateful-stateless-workflows-visual-studio-code/open-workflow-overview.png)

   1. **콜백 url** 값을 찾습니다 .이 값은 예제 요청 트리거에 대 한 다음 url과 유사 합니다.

      `http://localhost:7071/api/<workflow-name>/triggers/manual/invoke?api-version=2020-05-01-preview&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=<shared-access-signature>`

      ![콜백 URL을 사용 하 여 워크플로의 개요 페이지를 보여 주는 스크린샷](./media/create-stateful-stateless-workflows-visual-studio-code/find-callback-url.png)

1. 논리 앱 워크플로를 트리거하여 콜백 URL을 테스트 하려면 요청을 만들고 보내기 위한 [Postman](https://www.postman.com/downloads/) 또는 선호 하는 도구를 엽니다.

   이 예는 Postman을 사용 하 여 계속 합니다. 자세한 내용은 [Postman 시작](https://learning.postman.com/docs/getting-started/introduction/)을 참조 하세요.

   1. Postman 도구 모음에서 **새로 만들기** 를 선택 합니다.

      ![새 단추가 선택 된 Postman을 보여 주는 스크린샷](./media/create-stateful-stateless-workflows-visual-studio-code/postman-create-request.png)

   1. **새로 만들기** 창의 **구성 요소** 아래에서 **요청** 을 선택 합니다.

   1. 요청 **저장** 창의 **요청 이름** 에서 요청 이름을 제공 합니다 (예:) `Test workflow trigger` .

   1. **저장할 컬렉션 또는 폴더 선택** 에서 **컬렉션 만들기** 를 선택 합니다.

   1. **모든 컬렉션** 아래에서 요청을 구성 하기 위해 만들 컬렉션의 이름을 입력 하 고 enter 키를 누른 다음 ***컬렉션 이름* > <저장을** 선택 합니다. 이 예제에서는 `Logic Apps requests` 를 컬렉션 이름으로 사용 합니다.

      요청 트리거에 대 한 콜백 URL에 요청을 보낼 수 있도록 postman의 요청 창이 열립니다.

      ![열린 요청 창에서 Postman을 보여 주는 스크린샷](./media/create-stateful-stateless-workflows-visual-studio-code/postman-request-pane.png)

   1. Visual Studio Code로 돌아갑니다. 워크플로의 개요 페이지에서 **콜백 URL** 속성 값을 복사 합니다.

   1. Postman으로 돌아갑니다. 요청 창에서 현재 기본 요청 메서드로 **가져오기를** 표시 하는 메서드 목록에서 이전에 주소 상자에 복사한 콜백 URL을 붙여넣은 다음 **보내기** 를 선택 합니다.

      ![보내기 단추가 선택 된 주소 상자에 Postman 및 callback URL을 표시 하는 스크린샷](./media/create-stateful-stateless-workflows-visual-studio-code/postman-test-call-back-url.png)

      예제 논리 앱 워크플로는이 예제와 비슷한 전자 메일을 보냅니다.

      ![예제에 설명 된 대로 Outlook 전자 메일을 보여 주는 스크린샷](./media/create-stateful-stateless-workflows-visual-studio-code/workflow-app-result-email.png)

1. Visual Studio Code에서 워크플로의 개요 페이지로 돌아갑니다.

   상태 저장 워크플로를 만든 경우 전송한 요청이 워크플로를 트리거한 후 개요 페이지에 워크플로의 실행 상태 및 기록이 표시 됩니다.

   > [!TIP]
   > 실행 상태가 표시 되지 않으면 **새로 고침** 을 선택 하 여 개요 페이지를 새로 고쳐 봅니다. 충족 되지 않은 조건으로 인해 건너뛴 트리거와 데이터를 찾을 수 없는 경우에는 실행 되지 않습니다.

   ![실행 상태 및 기록이 포함 된 워크플로의 개요 페이지를 보여 주는 스크린샷](./media/create-stateful-stateless-workflows-visual-studio-code/post-trigger-call.png)

   | 실행 상태 | 설명 |
   |------------|-------------|
   | **중단됨** | 시스템이 중단 되거나 Azure 구독이 중단 된 등의 외부 문제로 인해 실행이 중지 되었거나 완료 되지 않았습니다. |
   | **취소** | 실행이 트리거되고 시작 되었지만 취소 요청이 수신 되었습니다. |
   | **실패** | 실행에 하나 이상의 작업이 실패 했습니다. 워크플로의 후속 작업이 실패를 처리 하도록 설정 되지 않았습니다. |
   | **실행 중** | 실행이 트리거 되었지만 진행 중이지만, [작업 제한](logic-apps-limits-and-config.md) 또는 [현재 요금제](https://azure.microsoft.com/pricing/details/logic-apps/)로 인해 제한 된 실행에 대해서도이 상태가 표시 될 수 있습니다. <p><p>**팁**: [진단 로깅을](monitor-logic-apps-log-analytics.md)설정 하는 경우 발생 하는 스로틀 이벤트에 대 한 정보를 얻을 수 있습니다. |
   | **성공함** | 실행이 성공 했습니다. 작업이 실패 하면 워크플로의 후속 작업에서 해당 실패를 처리 합니다. |
   | **시간 초과됨** | 현재 기간이 실행 [ **기록 보존 기간 (일)** 설정](logic-apps-limits-and-config.md#run-duration-retention-limits)에 의해 제어 되는 실행 기간 제한을 초과 하 여 실행 시간이 초과 되었습니다. 실행의 기간은 시작 시간에서 실행의 시작 시간 및 실행 기간 제한을 사용 하 여 계산 됩니다. <p><p>**참고**: 실행 기간이 현재 *실행 기록 보존* 한도를 초과 하는 경우에도 실행 기록 보존 기간 ( [ **일)** 설정](logic-apps-limits-and-config.md#run-duration-retention-limits)에 의해 제어 되는 경우 매일 정리 작업을 통해 실행 기록에서 실행이 지워집니다. 실행 제한 시간이 초과 되거나 완료 되는지 여부는 항상 실행의 시작 시간 및 *현재* 보존 제한을 사용 하 여 계산 됩니다. 따라서 진행 중인 실행에 대 한 기간 제한을 줄이면 실행 제한 시간이 초과 됩니다. 그러나 실행 기간이 보존 한도를 초과 했는지 여부에 따라 실행 기록에서 실행이 유지 되거나 지워집니다. |
   | **대기 중** | 예를 들어 아직 실행 중인 이전 워크플로 인스턴스로 인해 실행이 시작 되지 않았거나 일시 중지 되었습니다. |
   |||

1. 특정 실행의 각 단계에 대 한 상태와 단계의 입력 및 출력을 검토 하려면 해당 실행에 대 한 줄임표 (**...**) 단추를 선택 하 고 **실행 표시** 를 선택 합니다.

   ![줄임표 단추와 "실행 표시"가 선택 된 워크플로의 실행 기록 행을 보여 주는 스크린샷](./media/create-stateful-stateless-workflows-visual-studio-code/show-run-history.png)

   Visual Studio Code 모니터링 보기를 열고 실행의 각 단계에 대 한 상태를 표시 합니다.

   ![워크플로 실행의 각 단계와 해당 상태를 보여 주는 스크린샷](./media/create-stateful-stateless-workflows-visual-studio-code/run-history-action-status.png)

   > [!NOTE]
   > 실행이 실패 하 고 모니터링 보기의 단계에서 오류가 표시 되는 경우 `400 Bad Request` 이 문제는 기본 URI (Uniform Resource Identifier)가 기본 문자 제한을 초과 하는 긴 트리거 이름 또는 작업 이름으로 인해 발생할 수 있습니다. 자세한 내용은 ["400 잘못 된 요청"](#400-bad-request)을 참조 하세요.

   워크플로의 각 단계에 사용할 수 있는 상태는 다음과 같습니다.

   | 작업 상태 | 아이콘 | 설명 |
   |---------------|------|-------------|
   | **중단됨** | !["중단 됨" 작업 상태 아이콘][aborted-icon] | 시스템이 중단 되었거나 Azure 구독이 중단 된 등의 외부 문제로 인해 작업이 중지 되었거나 완료 되지 않았습니다. |
   | **취소** | !["취소 됨" 작업 상태 아이콘][cancelled-icon] | 작업이 실행 중이지만 취소 요청이 수신 되었습니다. |
   | **실패** | !["실패" 작업 상태 아이콘][failed-icon] | 작업이 실패 했습니다. |
   | **실행 중** | !["실행 중" 작업 상태 아이콘][running-icon] | 작업이 현재 실행 되 고 있습니다. |
   | **생략** | !["건너뜀" 작업 상태 아이콘][skipped-icon] | 바로 이전 작업이 실패 했으므로 작업을 건너뛰었습니다. 작업에는 `runAfter` 이전 작업이 성공적으로 완료 되어야 현재 작업을 실행할 수 있도록 하는 조건이 있습니다. |
   | **성공함** | !["성공" 작업 상태 아이콘][succeeded-icon] | 작업이 성공 했습니다. |
   | **다시 시도 성공** | !["재시도 성공" 작업 상태 아이콘][succeeded-with-retries-icon] | 하나 이상의 재시도 후에만 작업이 성공 했습니다. 다시 시도 기록을 검토 하려면 실행 기록 세부 정보 보기에서 해당 작업을 선택 하 여 입력 및 출력을 볼 수 있습니다. |
   | **시간 초과됨** | !["시간이 초과 되었습니다." 작업 상태 아이콘][timed-out-icon] | 해당 작업의 설정에 지정 된 제한 시간 제한으로 인해 작업이 중지 되었습니다. |
   | **대기 중** | !["대기 중" 작업 상태 아이콘][waiting-icon] | 호출자의 인바운드 요청을 기다리는 webhook 작업에 적용 됩니다. |
   ||||

   [aborted-icon]: ./media/create-stateful-stateless-workflows-visual-studio-code/aborted.png
   [cancelled-icon]: ./media/create-stateful-stateless-workflows-visual-studio-code/cancelled.png
   [failed-icon]: ./media/create-stateful-stateless-workflows-visual-studio-code/failed.png
   [running-icon]: ./media/create-stateful-stateless-workflows-visual-studio-code/running.png
   [skipped-icon]: ./media/create-stateful-stateless-workflows-visual-studio-code/skipped.png
   [succeeded-icon]: ./media/create-stateful-stateless-workflows-visual-studio-code/succeeded.png
   [succeeded-with-retries-icon]: ./media/create-stateful-stateless-workflows-visual-studio-code/succeeded-with-retries.png
   [timed-out-icon]: ./media/create-stateful-stateless-workflows-visual-studio-code/timed-out.png
   [waiting-icon]: ./media/create-stateful-stateless-workflows-visual-studio-code/waiting.png

1. 각 단계에 대 한 입력 및 출력을 검토 하려면 검사 하려는 단계를 선택 합니다.

   ![워크플로의 각 단계에 대 한 상태와 확장 된 "전자 메일 보내기" 작업의 입력 및 출력을 보여 주는 스크린샷](./media/create-stateful-stateless-workflows-visual-studio-code/run-history-details.png)

1. 해당 단계에 대 한 원시 입력 및 출력을 추가로 검토 하려면 **원시 입력 표시** 또는 **원시 출력 표시** 를 선택 합니다.

1. 디버깅 세션을 중지 하려면 **실행** 메뉴에서 **디버깅 중지** (Shift + F5)를 선택 합니다.

<a name="return-response"></a>

## <a name="return-a-response"></a>응답 반환

요청을 논리 앱에 보낸 호출자에 게 응답을 반환 하려면 요청 트리거로 시작 하는 워크플로에 대 한 기본 제공 [응답 작업](../connectors/connectors-native-reqres.md) 을 사용할 수 있습니다.

1. Workflow designer의 **이메일 보내기** 작업에서 **새 단계** 를 선택 합니다.

   **작업 선택** 프롬프트가 디자이너에 표시 되며 다음 작업을 선택할 수 있도록 **작업 추가 창이** 다시 열립니다.

1. 작업 **추가** 창의 **작업 선택** 검색 상자 아래에서 **기본 제공** 이 선택 되어 있는지 확인 합니다. 검색 상자에를 입력 하 `response` 고 **응답** 작업을 선택 합니다.

   ![응답 동작이 선택 된 워크플로 디자이너를 보여 주는 스크린샷](./media/create-stateful-stateless-workflows-visual-studio-code/add-response-action.png)

   디자이너에 **응답** 동작이 표시 되 면 작업의 세부 정보 창이 자동으로 열립니다.

   !["응답" 작업의 세부 정보 창이 열리고 "본문" 속성이 "전자 메일 보내기" 작업의 "본문" 속성 값으로 설정 된 워크플로 디자이너를 보여 주는 스크린샷](./media/create-stateful-stateless-workflows-visual-studio-code/response-action-details.png)

1. **매개 변수** 탭에서 호출 하려는 함수에 필요한 정보를 제공 합니다.

   이 예에서는 **전자 메일 보내기** 작업에서 출력 되는 **Body** 속성 값을 반환 합니다.

   1. 동적 콘텐츠 목록이 표시 되도록 **본문** 속성 상자 내부를 클릭 하 고 워크플로의 이전 트리거와 작업에서 사용 가능한 출력 값을 표시 합니다.

      ![동적 콘텐츠 목록이 표시 되도록 "Body" 속성 안에 마우스 포인터가 있는 "응답" 작업의 세부 정보 창을 보여 주는 스크린샷](./media/create-stateful-stateless-workflows-visual-studio-code/open-dynamic-content-list.png)

   1. 동적 콘텐츠 목록의 **전자 메일 보내기** 아래에서 **본문** 을 선택 합니다.

      ![동적 콘텐츠 열기 목록을 보여 주는 스크린샷 목록에서 "전자 메일 보내기" 헤더에 "Body" 출력 값이 선택 됩니다.](./media/create-stateful-stateless-workflows-visual-studio-code/select-send-email-action-body-output-value.png)

      완료 되 면 응답 작업의 **본문** 속성이 **전자 메일 보내기** 작업의 **본문** 출력 값으로 설정 됩니다.

      ![워크플로의 각 단계에 대 한 상태와 확장 된 "응답" 작업의 입력 및 출력을 보여 주는 스크린샷](./media/create-stateful-stateless-workflows-visual-studio-code/response-action-details-body-property.png)

1. 디자이너에서 **저장** 을 선택 합니다.

<a name="retest-workflow"></a>

## <a name="retest-your-logic-app"></a>논리 앱 다시 테스트

논리 앱을 업데이트 한 후에는 Visual Studio에서 디버거를 다시 실행 하 고 [로컬에서 실행, 테스트 및 디버그](#run-test-debug-locally)의 단계와 비슷하게 업데이트 된 논리 앱을 트리거하는 다른 요청을 보내 다른 테스트를 실행할 수 있습니다.

1. Visual Studio Code 활동 표시줄에서 **실행** 메뉴를 열고 **디버깅 시작** (F5)을 선택 합니다.

1. 요청을 만들고 전송 하는 Postman 또는 도구에서 워크플로를 트리거하기 위한 다른 요청을 보냅니다.

1. 상태 저장 워크플로를 만든 경우 워크플로의 개요 페이지에서 가장 최근 실행에 대 한 상태를 확인 합니다. 해당 실행의 각 단계에 대 한 상태, 입력 및 출력을 보려면 해당 실행에 대 한 줄임표 (**...**) 단추를 선택 하 고 **실행 표시** 를 선택 합니다.

   예를 들어 예제 워크플로가 응답 작업으로 업데이트 된 후 실행에 대 한 단계별 상태는 다음과 같습니다.

   ![업데이트 된 워크플로의 각 단계에 대 한 상태와 확장 된 "응답" 작업의 입력 및 출력을 보여 주는 스크린샷](./media/create-stateful-stateless-workflows-visual-studio-code/run-history-details-rerun.png)

1. 디버깅 세션을 중지 하려면 **실행** 메뉴에서 **디버깅 중지** (Shift + F5)를 선택 합니다.

<a name="firewall-setup"></a>

##  <a name="find-domain-names-for-firewall-access"></a>방화벽 액세스를 위한 도메인 이름 찾기

Azure Portal에서 논리 앱 워크플로를 배포 및 실행 하기 전에 환경에 엄격한 네트워크 요구 사항이 나 트래픽을 제한 하는 방화벽이 있는 경우 워크플로에 있는 트리거 또는 작업 연결에 대 한 사용 권한을 설정 해야 합니다.

이러한 연결에 대 한 Fqdn (정규화 된 도메인 이름)을 찾으려면 다음 단계를 수행 합니다.

1. 논리 앱 프로젝트에서 워크플로에 첫 번째 연결 기반 트리거 또는 작업을 추가한 후에 생성 되는 **connections.js** 파일을 열고 개체를 찾습니다 `managedApiConnections` .

1. 만든 각 연결에 대해 `connectionRuntimeUrl` 이 정보를 사용 하 여 방화벽을 설정할 수 있도록 안전한 위치에 속성 값을 찾아서 복사 하 고 저장 합니다.

   이 파일 **connections.js** 예제에는 두 개의 연결, 즉 AS2 연결과 Office 365 연결이 포함 되어 있습니다 `connectionRuntimeUrl` .

   * AS2 `"connectionRuntimeUrl": https://9d51d1ffc9f77572.00.common.logic-{Azure-region}.azure-apihub.net/apim/as2/11d3fec26c87435a80737460c85f42ba`

   * Office 365: `"connectionRuntimeUrl": https://9d51d1ffc9f77572.00.common.logic-{Azure-region}.azure-apihub.net/apim/office365/668073340efe481192096ac27e7d467f`

   ```json
   {
      "managedApiConnections": {
         "as2": {
            "api": {
               "id": "/subscriptions/{Azure-subscription-ID}/providers/Microsoft.Web/locations/{Azure-region}/managedApis/as2"
            },
            "connection": {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Web/connections/{connection-resource-name}"
            },
            "connectionRuntimeUrl": https://9d51d1ffc9f77572.00.common.logic-{Azure-region}.azure-apihub.net/apim/as2/11d3fec26c87435a80737460c85f42ba,
            "authentication": {
               "type":"ManagedServiceIdentity"
            }
         },
         "office365": {
            "api": {
               "id": "/subscriptions/{Azure-subscription-ID}/providers/Microsoft.Web/locations/{Azure-region}/managedApis/office365"
            },
            "connection": {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Web/connections/{connection-resource-name}"
            },
            "connectionRuntimeUrl": https://9d51d1ffc9f77572.00.common.logic-{Azure-region}.azure-apihub.net/apim/office365/668073340efe481192096ac27e7d467f,
            "authentication": {
               "type":"ManagedServiceIdentity"
            }
         }
      }
   }
   ```

<a name="deploy-azure"></a>

## <a name="deploy-to-azure"></a>Azure에 배포

Visual Studio Code에서 새 **논리 앱 (미리 보기)** 리소스 형식을 사용 하 여 논리 앱을 배포 하는 Azure에 프로젝트를 직접 게시할 수 있습니다. Azure Functions의 함수 앱 리소스와 마찬가지로,이 새 리소스 종류를 배포 하려면 배포 중에 설정할 수 있는 [호스팅 계획 및 가격 책정 계층](../app-service/overview-hosting-plans.md)을 선택 해야 합니다. 호스팅 계획 및 가격 책정에 대 한 자세한 내용은 다음 항목을 검토 하십시오.

* [Azure App Service에서 확장](../app-service/manage-scale-up.md)
* [Azure Functions 비율 크기 조정 및 호스팅](../azure-functions/functions-scale.md)

논리 앱을 새 리소스로 게시할 수 있습니다. 그러면 [함수 앱 요구 사항과 유사한 Azure Storage 계정과](../azure-functions/storage-considerations.md)같은 필요한 리소스를 자동으로 만듭니다. 또는 논리 앱을 해당 논리 앱을 덮어쓰는 이전에 배포 된 **논리 앱 (미리 보기)** 리소스에 게시할 수 있습니다.

### <a name="publish-to-a-new-logic-app-preview-resource"></a>새 논리 앱 (미리 보기) 리소스에 게시

1. Visual Studio Code 활동 표시줄에서 Azure 아이콘을 선택 합니다.

1. **Azure: Logic Apps (미리 보기)** 창 도구 모음에서 **논리 앱에 배포** 를 선택 합니다.

   !["논리 앱에 배포"가 선택 된 "Azure: Logic Apps (미리 보기)" 창과 창의 도구 모음을 보여 주는 스크린샷](./media/create-stateful-stateless-workflows-visual-studio-code/deploy-to-logic-app.png)

1. 메시지가 표시 되 면 논리 앱 배포에 사용할 Azure 구독을 선택 합니다.

1. Visual Studio Code 열리는 목록에서 다음 옵션 중에서 선택 합니다.

   * **Azure에서 새 논리 앱 (미리 보기) 만들기** (빠른)
   * **Azure Advanced에서 새 논리 앱 (미리 보기) 만들기**
   * 이전에 배포 된 **논리 앱 (미리 보기) 리소스 (** 있는 경우)

   이 예에서는 **Azure Advanced에서 새 논리 앱 (미리 보기) 만들기** 를 계속 진행 합니다.

   !["Azure에서 새 논리 앱 만들기 (미리 보기)"가 선택 된 목록이 포함 된 "Azure: Logic Apps (미리 보기)" 창을 보여 주는 스크린샷](./media/create-stateful-stateless-workflows-visual-studio-code/select-create-logic-app-options.png)

1. 새 **논리 앱 (미리 보기)** 리소스를 만들려면 다음 단계를 수행 합니다.

   1. **논리 앱 (미리 보기)** 리소스에 사용할 이름인 새 논리 앱에 대 한 전역적으로 고유한 이름을 제공 합니다. 이 예에서는 `Fabrikam-Workflows-App`를 사용합니다.

      !["Azure: Logic Apps (미리 보기)" 창을 표시 하는 스크린샷 및 만들 새 논리 앱의 이름을 입력 하 라는 메시지가 표시 됩니다.](./media/create-stateful-stateless-workflows-visual-studio-code/enter-logic-app-name.png)

   1. [ **App Service 계획** (전용)](../azure-functions/dedicated-plan.md) 또는 [**프리미엄**](../azure-functions/functions-premium-plan.md)중에서 새 논리 앱에 대 한 [호스팅 계획](../app-service/overview-hosting-plans.md) 을 선택 합니다.

      > [!IMPORTANT]
      > 소비 계획은이 리소스 종류에 대해 지원 되지 않으며 사용할 수 없습니다. 선택한 요금제는 나중에 사용할 수 있는 기능 및 가격 책정 계층에 영향을 줍니다. 자세한 내용은 다음 항목을 검토 하십시오. 
      >
      > * [Azure Functions 비율 크기 조정 및 호스팅](../azure-functions/functions-scale.md)
      > * [App Service 가격 정보](https://azure.microsoft.com/pricing/details/app-service/)
      >
      > 예를 들어 Premium 요금제는 논리 앱을 만들고 배포 하는 경우와 유사 Azure Functions 하 게 Azure 가상 네트워크와의 연결 및 개인 연결과 같은 네트워킹 기능에 대 한 액세스를 제공 합니다. 
      > 자세한 내용은 다음 항목을 검토 하십시오.
      > 
      > * [Azure Functions 네트워킹 옵션](../azure-functions/functions-networking-options.md)
      > * [Azure Logic Apps 미리 보기를 사용 하 여 어디서 나 Azure Logic Apps 실행-네트워킹 가능성](https://techcommunity.microsoft.com/t5/integrations-on-azure/logic-apps-anywhere-networking-possibilities-with-logic-app/ba-p/2105047)

      이 예에서는 **App Service 계획** 을 사용 합니다.

      !["Azure: Logic Apps (미리 보기)" 창을 표시 하 고 "App Service Plan" 또는 "Premium"을 선택 하 라는 메시지를 표시 하는 스크린샷](./media/create-stateful-stateless-workflows-visual-studio-code/select-hosting-plan.png)

   1. 새 App Service 계획을 만들거나 기존 계획을 선택 합니다. 이 예에서는 **새 App Service 계획 만들기** 를 선택 합니다.

      !["Azure: Logic Apps (미리 보기)" 창 및 "새 App Service 계획 만들기"에 대 한 프롬프트를 보여 주는 스크린샷 또는 기존 App Service 계획을 선택 합니다.](./media/create-stateful-stateless-workflows-visual-studio-code/create-app-service-plan.png)

   1. App Service 계획의 이름을 입력 한 다음 계획에 대 한 [가격 책정 계층](../app-service/overview-hosting-plans.md) 을 선택 합니다. 이 예에서는 **F1 무료** 요금제를 선택 합니다.

      !["Azure: Logic Apps (미리 보기)" 창을 표시 하는 스크린샷 및 가격 책정 계층을 선택 하 라는 메시지가 표시 됩니다.](./media/create-stateful-stateless-workflows-visual-studio-code/select-pricing-tier.png)

   1. 성능을 최적화 하려면 배포에 대 한 프로젝트와 동일한 리소스 그룹을 찾아 선택 합니다.

      > [!NOTE]
      > 다른 리소스 그룹을 만들거나 사용할 수 있지만 이렇게 하면 성능에 영향을 줄 수 있습니다. 다른 리소스 그룹을 만들거나 선택 하 고 확인 메시지가 표시 되 면 취소를 선택 하면 배포도 취소 됩니다.

   1. 상태 저장 워크플로의 경우 **새 저장소 계정 만들기** 또는 기존 저장소 계정을 선택 합니다.

      !["Azure: Logic Apps (미리 보기)" 창이 표시 되 고 저장소 계정을 만들거나 선택 하 라는 메시지가 표시 되는 스크린샷](./media/create-stateful-stateless-workflows-visual-studio-code/create-storage-account.png)

   1. 논리 앱의 만들기 및 배포 설정에서 [Application Insights](../azure-monitor/app/app-insights-overview.md)사용을 지 원하는 경우 필요에 따라 논리 앱에 대 한 진단 로깅 및 추적을 사용 하도록 설정할 수 있습니다. Visual Studio Code 또는 배포 후에 논리 앱을 배포할 때 이러한 작업을 수행할 수 있습니다. Application Insights 인스턴스가 필요 하지만, 논리 앱을 배포할 때 또는 배포 후에이 리소스를 [미리](../azure-monitor/app/create-workspace-resource.md)만들 수 있습니다.

      지금 로깅 및 추적을 사용 하도록 설정 하려면 다음 단계를 수행 합니다.

      1. 기존 Application Insights 리소스를 선택 하거나 **새 Application Insights 리소스를 만듭니다**.

      1. [Azure Portal](https://portal.azure.com)에서 Application Insights 리소스로 이동 합니다.

      1. 리소스 메뉴에서 **개요** 를 선택 합니다. **계측 키** 값을 찾아 복사 합니다.

      1. Visual Studio Code의 프로젝트 루트 폴더에서 파일 **의local.settings.js** 를 엽니다.

      1. 개체에서 `Values` 속성을 추가 하 `APPINSIGHTS_INSTRUMENTATIONKEY` 고 값을 계측 키로 설정 합니다. 예를 들면 다음과 같습니다.

         ```json
         {
            "IsEncrypted": false,
            "Values": {
               "AzureWebJobsStorage": "UseDevelopmentStorage=true",
               "FUNCTIONS_WORKER_RUNTIME": "dotnet",
               "APPINSIGHTS_INSTRUMENTATIONKEY": <instrumentation-key>
            }
         }
         ```

         > [!TIP]
         > 트리거와 동작 이름이 Application Insights 인스턴스에 올바르게 표시 되는지 여부를 확인할 수 있습니다.
         >
         > 1. Azure Portal에서 Application Insights 리소스로 이동 합니다.
         >
         > 2. 리소스 리소스 메뉴의 **조사** 에서 **응용 프로그램 맵** 을 선택 합니다.
         >
         > 3. 맵에 표시 되는 작업 이름을 검토 합니다.
         >
         > 기본 제공 트리거의 일부 인바운드 요청이 응용 프로그램 맵에 중복으로 나타날 수 있습니다. 
         > 이러한 복제는 형식을 사용 하는 대신 `WorkflowName.ActionName` 워크플로 이름을 작업 이름으로 사용 하 고 Azure Functions 호스트에서 시작 됩니다.

      1. 그런 다음 필요에 따라 논리 앱에서 수집 하 여 Application Insights 인스턴스에 전송 하는 추적 데이터에 대 한 심각도 수준을 조정할 수 있습니다.

         워크플로가 트리거되고 작업이 실행 될 때와 같이 워크플로 관련 이벤트가 발생할 때마다 런타임은 다양 한 추적을 내보냅니다. 이러한 추적은 워크플로의 수명을 다루며 다음 이벤트 유형을 포함 하지만이에 국한 되지 않습니다.

         * 시작, 중지 및 오류와 같은 서비스 활동입니다.
         * 작업 및 디스패처 활동.
         * 트리거, 동작 및 실행 등의 워크플로 활동입니다.
         * 저장소 요청 활동 (예: 성공 또는 실패)입니다.
         * 인바운드, 아웃 바운드, 성공, 실패 등의 HTTP 요청 작업
         * 디버그 메시지와 같은 모든 개발 추적

         각 이벤트 유형은 심각도 수준에 할당 됩니다. 예를 들어 `Trace` 수준에서 가장 자세한 메시지를 캡처하고, 수준에서는 `Information` 논리 앱, 워크플로, 트리거 및 작업이 시작 및 중지 되는 경우와 같이 워크플로의 일반 작업을 캡처합니다. 다음 표에서는 심각도 수준 및 해당 추적 유형을 설명 합니다.

         | 심각도 수준 | 추적 유형 |
         |----------------|------------|
         | 위험 | 논리 앱에서 복구할 수 없는 오류를 설명 하는 로그입니다. |
         | 디버그 | 인바운드 및 아웃 바운드 HTTP 호출과 같은 개발 중에 조사에 사용할 수 있는 로그입니다. |
         | 오류 | 워크플로 실행 오류를 나타내지만 논리 앱에서 일반적인 실패가 아닌 로그입니다. |
         | 정보 산업 | 논리 앱 또는 워크플로의 일반 활동을 추적 하는 로그입니다. 예를 들면 다음과 같습니다. <p><p>-트리거, 동작 또는 실행이 시작 되 고 끝나는 경우 <br>-논리 앱이 시작 되거나 종료 되는 경우 |
         | 추적 | 저장소 요청 또는 디스패처 활동과 같은 가장 자세한 메시지와 워크플로 실행 작업과 관련 된 모든 메시지를 포함 하는 로그입니다. |
         | 경고 | 논리 앱에서 비정상 상태를 강조 표시 하지만 실행을 방해 하지 않는 로그입니다. |
         |||

         심각도 수준을 설정 하려면 프로젝트의 루트 수준에서 파일 **에host.js** 을 열고 개체를 찾습니다 `logging` . 이 개체는 논리 앱의 모든 워크플로에 대 한 로그 필터링을 제어 하 고 [로그 유형 필터링을 위한 ASP.NET Core 레이아웃](/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1&preserve-view=true#log-filtering)을 따릅니다.

         ```json
         {
            "version": "2.0",
            "logging": {
               "applicationInsights": {
                  "samplingExcludedTypes": "Request",
                  "samplingSettings": {
                     "isEnabled": true
                  }
               }
            }
         }
         ```

         `logging`개체가 속성을 포함 하는 개체를 포함 하지 않는 경우 `logLevel` `Host.Triggers.Workflow` 해당 항목을 추가 합니다. 속성을 원하는 추적 형식에 대 한 심각도 수준으로 설정 합니다. 예를 들면 다음과 같습니다.

         ```json
         {
            "version": "2.0",
            "logging": {
               "applicationInsights": {
                  "samplingExcludedTypes": "Request",
                  "samplingSettings": {
                     "isEnabled": true
                  }
               },
               "logLevel": {
                  "Host.Triggers.Workflow": "Information"
               }
            }
         }
         ```

   배포 단계를 완료 하 고 나면 Visual Studio Code는 논리 앱을 게시 하는 데 필요한 리소스를 만들고 배포 하기 시작 합니다.

1. 배포 프로세스를 검토 하 고 모니터링 하려면 **보기** 메뉴에서 **출력** 을 선택 합니다. 출력 창 도구 모음 목록에서 **Azure Logic Apps** 을 선택 합니다.

   ![배포 진행률 및 상태와 함께 도구 모음 목록에서 "Azure Logic Apps"가 선택 된 상태에서 출력 창을 보여 주는 스크린샷](./media/create-stateful-stateless-workflows-visual-studio-code/logic-app-deployment-output-window.png)

   Visual Studio Code에서 논리 앱을 Azure에 배포 하는 작업이 완료 되 면 다음과 같은 메시지가 표시 됩니다.

   ![Azure에 배포가 성공적으로 완료 되었다는 메시지를 보여 주는 스크린샷](./media/create-stateful-stateless-workflows-visual-studio-code/deployment-to-azure-completed.png)

   축 하 합니다. 이제 논리 앱은 Azure에서 라이브 되며 기본적으로 사용 하도록 설정 됩니다.

다음으로 이러한 작업을 수행 하는 방법을 배울 수 있습니다.

* [프로젝트에 빈 워크플로를 추가](#add-workflow-existing-project)합니다.

* Visual Studio Code 또는 [Azure Portal](#manage-deployed-apps-portal)를 사용 하 여 [배포 된 논리 앱을 관리](#manage-deployed-apps-vs-code) 합니다.

* [상태 비저장 워크플로에서 실행 기록을 사용 하도록 설정](#enable-run-history-stateless)합니다.

* [배포 된 논리 앱에 대 한 Azure Portal 모니터링 보기를 사용 하도록 설정](#enable-monitoring)합니다.

<a name="add-workflow-existing-project"></a>

## <a name="add-blank-workflow-to-project"></a>프로젝트에 빈 워크플로 추가

논리 앱 프로젝트에 여러 워크플로를 포함할 수 있습니다. 프로젝트에 빈 워크플로를 추가 하려면 다음 단계를 수행 합니다.

1. Visual Studio Code 활동 표시줄에서 Azure 아이콘을 선택 합니다.

1. Azure 창에서 **azure: Logic Apps (미리 보기)** 옆에 있는 **워크플로 만들기** (Azure Logic Apps 아이콘)를 선택 합니다.

1. 추가 하려는 워크플로 유형 선택: **상태 저장** 또는 **상태 비저장**

1. 워크플로의 이름을 제공 합니다.

완료 되 면 워크플로 정의에 대 한 파일 **에workflow.js** 와 함께 새 워크플로 폴더가 프로젝트에 표시 됩니다.

<a name="manage-deployed-apps-vs-code"></a>

## <a name="manage-deployed-logic-apps-in-visual-studio-code"></a>Visual Studio Code에서 배포 된 논리 앱 관리

Visual Studio Code은 Azure 구독에서 배포 된 모든 논리 앱 (원래 **Logic Apps** 인지 아니면 **논리 앱 (미리 보기)** 리소스 종류)을 볼 수 있으며 해당 논리 앱을 관리 하는 데 도움이 되는 작업을 선택할 수 있습니다. 그러나 두 리소스 유형 모두에 액세스 하려면 Visual Studio Code에 대 한 **Azure Logic Apps** 및 **Azure Logic Apps (미리 보기)** 확장이 모두 필요 합니다.

1. 왼쪽 도구 모음에서 Azure 아이콘을 선택 합니다. **Azure: Logic Apps (미리 보기)** 창에서 구독을 확장 합니다. 그러면 해당 구독에 대해 배포 된 모든 논리 앱이 표시 됩니다.

1. 관리 하려는 논리 앱을 엽니다. 논리 앱의 바로 가기 메뉴에서 수행 하려는 작업을 선택 합니다.

   예를 들어 배포 된 논리 앱 중지, 시작, 다시 시작 또는 삭제와 같은 작업을 선택할 수 있습니다.

   ![열린 "Azure Logic Apps (미리 보기)" 확장 창 및 배포 된 워크플로를 사용 하 여 Visual Studio Code를 보여 주는 스크린샷](./media/create-stateful-stateless-workflows-visual-studio-code/find-deployed-workflow-visual-studio-code.png)

1. 논리 앱의 모든 워크플로를 보려면 논리 앱을 확장 한 다음 **워크플로** 노드를 확장 합니다.

1. 특정 워크플로를 보려면 워크플로의 바로 가기 메뉴를 열고 **디자이너에서 열기** 를 선택 하 여 읽기 전용 모드로 워크플로를 엽니다.

   워크플로를 편집 하려면 다음 옵션을 사용할 수 있습니다.

   * Visual Studio Code의 workflow designer에서 프로젝트의 **workflow.js** 파일을 열고, 편집 하 고, 논리 앱을 Azure에 다시 배포 합니다.

   * Azure Portal에서 [논리 앱을 찾아서 엽니다](#manage-deployed-apps-portal). 워크플로를 찾고, 편집 하 고, 저장 합니다.

1. Azure Portal에서 배포 된 논리 앱을 열려면 논리 앱의 바로 가기 메뉴를 열고 **포털에서 열기** 를 선택 합니다.

   Azure Portal 브라우저에서 열리고 Visual Studio Code에 로그인 하 고 논리 앱을 표시 하는 경우 자동으로 포털에 로그인 합니다.

   ![Visual Studio Code에서 논리 앱에 대 한 Azure Portal 페이지를 보여 주는 스크린샷](./media/create-stateful-stateless-workflows-visual-studio-code/deployed-workflow-azure-portal.png)

   Azure Portal에 별도로 로그인 하 고 포털 검색 상자를 사용 하 여 논리 앱을 찾은 다음, 결과 목록에서 논리 앱을 선택할 수도 있습니다.

   ![선택 된 것으로 표시 되는 배포 된 논리 앱에 대 한 검색 결과가 포함 된 검색 표시줄과 Azure Portal를 보여 주는 스크린샷](./media/create-stateful-stateless-workflows-visual-studio-code/find-deployed-workflow-azure-portal.png)

<a name="manage-deployed-apps-portal"></a>

## <a name="manage-deployed-logic-apps-in-the-portal"></a>포털에서 배포 된 논리 앱 관리

Azure Portal에서 Azure 구독에 있는 모든 배포 된 논리 앱 (원래 **Logic Apps** 리소스 유형 인지 또는 **논리 앱 (미리 보기)** 리소스 유형)을 볼 수 있습니다. 현재 각 리소스 종류는 Azure에서 별도의 범주로 구성 되 고 관리 됩니다. **논리 앱 (미리 보기)** 리소스 종류를 포함 하는 논리 앱을 찾으려면 다음 단계를 수행 합니다.

1. Azure Portal 검색 상자에을 입력 `logic app preview` 합니다. 결과 목록이 표시 되 면 **서비스** 에서 **논리 앱 (미리 보기)** 을 선택 합니다.

   !["논리 앱 미리 보기" 검색 텍스트를 사용 하 여 Azure Portal 검색 상자를 보여 주는 스크린샷](./media/create-stateful-stateless-workflows-visual-studio-code/portal-find-logic-app-preview-resource.png)

1. **논리 앱 (미리 보기)** 창에서 Visual Studio Code에서 배포한 논리 앱을 찾아 선택 합니다.

   ![Azure Portal 및 Azure에 배포 된 논리 앱 (미리 보기) 리소스를 보여 주는 스크린샷](./media/create-stateful-stateless-workflows-visual-studio-code/logic-app-preview-resources-pane.png)

   Azure Portal 선택한 논리 앱에 대 한 개별 리소스 페이지를 엽니다.

   ![Azure Portal에서 논리 앱 워크플로의 리소스 페이지를 보여 주는 스크린샷](./media/create-stateful-stateless-workflows-visual-studio-code/deployed-workflow-azure-portal.png)

1. 이 논리 앱에서 워크플로를 보려면 논리 앱의 메뉴에서 **워크플로** 를 선택 합니다.

   **워크플로** 창에는 현재 논리 앱의 모든 워크플로가 표시 됩니다. 이 예제에서는 Visual Studio Code에서 만든 워크플로를 보여 줍니다.

   !["워크플로" 창이 열리고 배포 된 워크플로를 사용 하는 "논리 앱 (미리 보기)" 리소스 페이지를 보여 주는 스크린샷](./media/create-stateful-stateless-workflows-visual-studio-code/deployed-logic-app-workflows-pane.png)

1. **워크플로를 보려면 워크플로 창에서** 해당 워크플로를 선택 합니다.

   워크플로 창이 열리고 해당 워크플로에서 수행할 수 있는 추가 정보 및 작업이 표시 됩니다.

   예를 들어 워크플로의 단계를 보려면 **디자이너** 를 선택 합니다.

   ![선택한 워크플로의 "개요" 창을 보여 주는 스크린샷, 워크플로 메뉴에는 선택한 "Designer" 명령이 표시 됩니다.](./media/create-stateful-stateless-workflows-visual-studio-code/workflow-overview-pane-select-designer.png)

   Workflow designer가 열리고 Visual Studio Code에서 작성 한 워크플로가 표시 됩니다. 이제 Azure Portal에서이 워크플로를 변경할 수 있습니다.

   ![Visual Studio Code에서 배포 된 워크플로 디자이너와 워크플로를 보여 주는 스크린샷](./media/create-stateful-stateless-workflows-visual-studio-code/opened-workflow-designer.png)

<a name="add-workflow-portal"></a>

## <a name="add-another-workflow-in-the-portal"></a>포털에서 다른 워크플로 추가

Azure Portal를 통해 Visual Studio Code에서 배포한 **논리 앱 (미리 보기)** 리소스에 빈 워크플로를 추가 하 고 Azure Portal에서 해당 워크플로를 빌드할 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에서 배포 된 **논리 앱 (미리 보기)** 리소스를 찾아 선택 합니다.

1. 논리 앱 메뉴에서 **워크플로** 를 선택 합니다. **워크플로** 창에서 **추가** 를 선택 합니다.

   ![선택한 논리 앱의 "워크플로" 창 및 "추가" 명령이 선택 된 도구 모음을 보여 주는 스크린샷](./media/create-stateful-stateless-workflows-visual-studio-code/add-new-workflow.png)

1. **새 워크플로** 창에서 워크플로의 이름을 입력 합니다. **상태 저장** 또는 **상태 비저장** **>** **만들기** 를 선택 합니다.

   Azure가 **워크플로 창에 표시 되는 새** 워크플로를 배포한 후에는 디자이너 또는 코드 보기 열기와 같은 다른 작업을 관리 하 고 수행할 수 있도록 해당 워크플로를 선택 합니다.

   ![관리 및 검토 옵션으로 선택한 워크플로를 보여 주는 스크린샷](./media/create-stateful-stateless-workflows-visual-studio-code/view-new-workflow.png)

   예를 들어 새 워크플로에 대 한 디자이너를 열면 빈 캔버스가 표시 됩니다. 이제 Azure Portal에서이 워크플로를 빌드할 수 있습니다.

   ![워크플로 디자이너와 빈 워크플로를 보여 주는 스크린샷](./media/create-stateful-stateless-workflows-visual-studio-code/opened-blank-workflow-designer.png)

<a name="enable-run-history-stateless"></a>

## <a name="enable-run-history-for-stateless-workflows"></a>상태 비저장 워크플로에 대 한 실행 기록 사용

상태 비저장 워크플로를 더 쉽게 디버깅 하려면 해당 워크플로에 대 한 실행 기록을 사용 하도록 설정한 다음 완료 되 면 실행 기록을 사용 하지 않도록 설정 하면 됩니다. Visual Studio Code에 대해 다음 단계를 수행 하거나 Azure Portal 작업 하는 경우 [Azure Portal에서 상태 저장 및 상태 비저장 워크플로 만들기](create-stateful-stateless-workflows-azure-portal.md#enable-run-history-stateless)를 참조 하세요.

1. Visual Studio Code 프로젝트에서 **워크플로-designtime** 폴더를 확장 하 고 파일 **에서local.settings.js** 를 엽니다.

1. 속성을 추가 하 `Workflows.{yourWorkflowName}.operationOptions` 고 값을로 설정 합니다 `WithStatelessRunHistory` . 예를 들면 다음과 같습니다.

   **Windows**

   ```json
   {
      "IsEncrypted": false,
      "Values": {
         "AzureWebJobsStorage": "UseDevelopmentStorage=true",
         "FUNCTIONS_WORKER_RUNTIME": "dotnet",
         "Workflows.{yourWorkflowName}.OperationOptions": "WithStatelessRunHistory"
      }
   }
   ```

   **macOS 또는 Linux**

   ```json
   {
      "IsEncrypted": false,
      "Values": {
         "AzureWebJobsStorage": "DefaultEndpointsProtocol=https;AccountName=fabrikamstorageacct; \
             AccountKey=<access-key>;EndpointSuffix=core.windows.net",
         "FUNCTIONS_WORKER_RUNTIME": "dotnet",
         "Workflows.{yourWorkflowName}.OperationOptions": "WithStatelessRunHistory"
      }
   }
   ```

1. 완료 되 면 실행 기록을 비활성화 하려면 `Workflows.{yourWorkflowName}.OperationOptions` 속성을로 설정 `None` 하거나 속성 및 해당 값을 삭제 합니다.

<a name="enable-monitoring"></a>

## <a name="enable-monitoring-view-in-the-azure-portal"></a>Azure Portal 모니터링 보기 사용

Visual Studio Code에서 Azure로 **논리 앱 (미리 보기)** 리소스를 배포한 후에는 해당 워크플로에 대 한 Azure Portal 및 **모니터링** 환경을 사용 하 여 해당 리소스에서 워크플로의 사용 가능한 실행 기록과 세부 정보를 검토할 수 있습니다. 그러나 먼저 해당 논리 앱 리소스에 대해 **모니터** 보기 기능을 사용 하도록 설정 해야 합니다.

1. [Azure Portal](https://portal.azure.com)에서 배포 된 **논리 앱 (미리 보기)** 리소스를 찾아 선택 합니다.

1. 해당 리소스의 메뉴에 있는 **API** 에서 **CORS** 를 선택 합니다.

1. **CORS** 창의 **허용 된 원본** 아래에서 와일드 카드 문자 (*)를 추가 합니다.

1. 완료 되 면 **CORS** 도구 모음에서 **저장** 을 선택 합니다.

   ![배포 된 논리 앱 (미리 보기) 리소스를 사용 하 여 Azure Portal를 보여 주는 스크린샷 [리소스] 메뉴에서 "CORS"는 와일드 카드 "*" 문자로 설정 된 "허용 된 원본"의 새 항목과 함께 선택 됩니다.](./media/create-stateful-stateless-workflows-visual-studio-code/enable-run-history-deployed-logic-app.png)

<a name="enable-open-application-insights"></a>

## <a name="enable-or-open-application-insights-after-deployment"></a>배포 후 Application Insights 사용 또는 열기

워크플로를 실행 하는 동안 논리 앱은 다른 이벤트와 함께 원격 분석을 내보냅니다. 이 원격 분석을 사용 하 여 워크플로가 얼마나 잘 실행 되는지와 Logic Apps 런타임이 다양 한 방식으로 작동 하는 방식을 보다 잘 파악할 수 있습니다. 거의 실시간으로 원격 분석 (라이브 메트릭)을 제공 하는 [Application Insights](../azure-monitor/app/app-insights-overview.md)을 사용 하 여 워크플로를 모니터링할 수 있습니다. 이 기능을 통해이 데이터를 사용 하 여 문제를 진단 하 고, 경고를 설정 하 고, 차트를 작성 하는 경우 오류 및 성능 문제를 보다 쉽게 조사할 수 있습니다.

논리 앱의 만들기 및 배포 설정에서 [Application Insights](../azure-monitor/app/app-insights-overview.md)사용을 지 원하는 경우 필요에 따라 논리 앱에 대 한 진단 로깅 및 추적을 사용 하도록 설정할 수 있습니다. Visual Studio Code 또는 배포 후에 논리 앱을 배포할 때 이러한 작업을 수행할 수 있습니다. Application Insights 인스턴스가 필요 하지만, 논리 앱을 배포할 때 또는 배포 후에이 리소스를 [미리](../azure-monitor/app/create-workspace-resource.md)만들 수 있습니다.

배포 된 논리 앱에 대 한 Application Insights를 사용 하도록 설정 하거나 이미 사용 하도록 설정 된 경우 Application Insights 데이터를 검토 하려면 다음 단계를 수행 합니다.

1. Azure Portal에서 배포 된 논리 앱을 찾습니다.

1. 논리 앱 메뉴의 **설정** 에서 **Application Insights** 를 선택 합니다.

1. Application Insights 사용 하도록 설정 되지 않은 경우 **Application Insights** 창에서 **Application Insights 켜기** 를 선택 합니다. 창 업데이트 후 아래쪽에서 **적용** 을 선택 합니다.

   Application Insights 사용 하도록 설정 된 경우 **Application Insights** 창에서 **Application Insights 데이터 보기** 를 선택 합니다.

Application Insights 열리면 논리 앱에 대 한 다양 한 메트릭을 검토할 수 있습니다. 자세한 내용은 다음 항목을 검토 하십시오.

* [거의 모든 위치에서 실행 하는 Azure Logic Apps-Application Insights를 사용 하 여 모니터링-1 부](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-monitor-with-application/ba-p/1877849)
* [어디에서 나 실행 Azure Logic Apps Application Insights를 사용 하 여 모니터링-2 부](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-monitor-with-application/ba-p/2003332)

<a name="deploy-docker"></a>

## <a name="deploy-to-docker"></a>Docker에 배포

[.NET CLI](/dotnet/core/tools/)를 사용 하 여 호스팅 환경으로 [Docker 컨테이너](/visualstudio/docker/tutorials/docker-tutorial#what-is-a-container) 에 논리 앱을 배포할 수 있습니다. 이러한 명령을 사용 하 여 논리 앱의 프로젝트를 빌드하고 게시할 수 있습니다. 그런 다음 논리 앱을 배포 하기 위한 대상으로 Docker 컨테이너를 빌드하고 실행할 수 있습니다.

Docker에 익숙하지 않은 경우 다음 항목을 검토 하세요.

* [Docker란?](/dotnet/architecture/microservices/container-docker-introduction/docker-defined)
* [컨테이너 및 Docker 소개](/dotnet/architecture/microservices/container-docker-introduction/)
* [.NET 및 Docker 소개](/dotnet/core/docker/introduction)
* [Docker 컨테이너, 이미지 및 레지스트리](/dotnet/architecture/microservices/container-docker-introduction/docker-containers-images-registries)
* [자습서: Docker 시작 (Visual Studio Code)](/visualstudio/docker/tutorials/docker-tutorial)

### <a name="requirements"></a>요구 사항

* 논리 앱이 배포에 사용 하는 Azure Storage 계정

* Docker 컨테이너를 빌드할 때 사용 하는 워크플로에 대 한 Docker 파일

  예를 들어이 샘플 Docker 파일은 논리 앱을 배포 합니다. 는 Azure Portal에 논리 앱을 게시 하는 데 사용 된 Azure Storage 계정의 액세스 키를 포함 하는 연결 문자열을 지정 합니다. 이 문자열을 찾으려면 [저장소 계정 연결 문자열 가져오기](#find-storage-account-connection-string)를 참조 하세요.

   ```text
   FROM mcr.microsoft.com/azure-functions/node:3.0

   ENV AzureWebJobsStorage <storage-account-connection-string>
   ENV AzureWebJobsScriptRoot=/home/site/wwwroot \
       AzureFunctionsJobHost__Logging__Console__IsEnabled=true \
       FUNCTIONS_V2_COMPATIBILITY_MODE=true

   COPY . /home/site/wwwroot

   RUN cd /home/site/wwwroot
   ```

   자세한 내용은 [Docker 파일 작성에 대 한 모범 사례](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/) 를 참조 하세요.

<a name="find-storage-account-connection-string"></a>

### <a name="get-storage-account-connection-string"></a>저장소 계정 연결 문자열 가져오기

Docker 컨테이너 이미지를 빌드하고 실행 하려면 먼저 저장소 계정에 대 한 액세스 키가 포함 된 연결 문자열을 가져와야 합니다. 이전에는 macOS 또는 Linux에서 확장을 사용 하거나 논리 앱을 Azure Portal에 배포할 때이 저장소 계정을 만들었습니다.

이 연결 문자열을 찾아서 복사 하려면 다음 단계를 수행 합니다.

1. Azure Portal 저장소 계정 메뉴의 **설정** 에서 **액세스 키** 를 선택 합니다. 

1. **액세스 키** 창에서 저장소 계정의 연결 문자열을 찾아 복사 합니다 .이 예는 다음과 같습니다.

   `DefaultEndpointsProtocol=https;AccountName=fabrikamstorageacct;AccountKey=<access-key>;EndpointSuffix=core.windows.net`

   ![저장소 계정 액세스 키와 연결 문자열이 복사 된 Azure Portal를 보여 주는 스크린샷](./media/create-stateful-stateless-workflows-visual-studio-code/find-storage-account-connection-string.png)

   자세한 내용은 [저장소 계정 키 관리](../storage/common/storage-account-keys-manage.md?tabs=azure-portal#view-account-access-keys)를 참조 하세요.

1. 배포에 사용 하는 Docker 파일에이 문자열을 추가할 수 있도록 안전한 위치에 연결 문자열을 저장 합니다. 

<a name="find-storage-account-master-key"></a>

### <a name="find-master-key-for-storage-account"></a>저장소 계정에 대 한 마스터 키 찾기

워크플로에 요청 트리거가 포함 된 경우 Docker 컨테이너 이미지를 빌드하고 실행 한 후 [트리거의 콜백 URL을 가져와야](#get-callback-url-request-trigger) 합니다. 이 작업의 경우 배포에 사용 하는 저장소 계정에 대 한 마스터 키 값도 지정 해야 합니다.

1. 이 마스터 키를 찾으려면 프로젝트에서 **webjobs/{deployment-name}/host.js** 파일을 엽니다.

1. `AzureWebJobsStorage`속성을 찾고이 섹션에서 키 값을 복사 합니다.

   ```json
   {
      <...>
      "masterKey": {
         "name": "master",
         "value": "<master-key>",
         "encrypted": false
      },
      <...>
   }
   ```

1. 나중에 사용할 수 있도록 안전한 위치에이 키 값을 저장 합니다.

<a name="build-run-docker-container-image"></a>

### <a name="build-and-run-your-docker-container-image"></a>Docker 컨테이너 이미지 빌드 및 실행

1. Docker 파일을 사용 하 고 다음 명령을 실행 하 여 Docker 컨테이너 이미지를 빌드합니다.

   `docker build --tag local/workflowcontainer .`

   자세한 내용은 [docker 빌드](https://docs.docker.com/engine/reference/commandline/build/)를 참조 하세요.

1. 다음 명령을 사용 하 여 컨테이너를 로컬로 실행 합니다.

   `docker run -e WEBSITE_HOSTNAME=localhost -p 8080:80 local/workflowcontainer`

   자세한 내용은 [docker run](https://docs.docker.com/engine/reference/commandline/run/)항목을 참조 하세요.

<a name="get-callback-url-request-trigger"></a>

### <a name="get-callback-url-for-request-trigger"></a>요청 트리거에 대 한 콜백 URL을 가져옵니다.

요청 트리거를 사용 하는 워크플로의 경우이 요청을 전송 하 여 트리거의 콜백 URL을 가져옵니다.

`POST /runtime/webhooks/workflow/api/management/workflows/{workflow-name}/triggers/{trigger-name}/listCallbackUrl?api-version=2020-05-01-preview&code={master-key}`

`{trigger-name}`값은 워크플로의 JSON 정의에 표시 되는 요청 트리거의 이름입니다. `{master-key}`값은 `AzureWebJobsStorage` **webjobs/{deployment-name}/host.js** 파일 내에서 속성에 대해 설정한 Azure Storage 계정에 정의 됩니다. 자세한 내용은 [저장소 계정 마스터 키 찾기](#find-storage-account-master-key)를 참조 하세요.

<a name="delete-from-designer"></a>

## <a name="delete-items-from-the-designer"></a>디자이너에서 항목 삭제

디자이너에서 워크플로의 항목을 삭제 하려면 다음 단계 중 하나를 수행 합니다.

* 항목을 선택 하 고, 항목의 바로 가기 메뉴 (Shift + F10)를 열고, **삭제** 를 선택 합니다. 확인하려면 **확인** 을 선택합니다.

* 항목을 선택 하 고 delete 키를 누릅니다. 확인하려면 **확인** 을 선택합니다.

* 항목을 선택 하면 해당 항목에 대 한 세부 정보 창이 열립니다. 창의 오른쪽 위 모서리에서 줄임표 (**...**) 메뉴를 열고 **삭제** 를 선택 합니다. 확인하려면 **확인** 을 선택합니다.

  ![디자이너에서 열려 있는 세부 정보 창에 선택한 줄임표 단추와 "Delete" 명령이 있는 선택한 항목을 보여 주는 스크린샷](./media/create-stateful-stateless-workflows-visual-studio-code/delete-item-from-designer.png)

  > [!TIP]
  > 줄임표 메뉴가 표시 되지 않는 경우 세부 정보 창에 오른쪽 위 모퉁이에 있는 줄임표 (**...**) 단추가 표시 되도록 충분 한 창의 너비 Visual Studio Code 확장 합니다.

<a name="troubleshooting"></a>

## <a name="troubleshoot-errors-and-problems"></a>오류 및 문제 해결

<a name="designer-fails-to-open"></a>

### <a name="designer-fails-to-open"></a>디자이너 열기 실패

디자이너를 열려고 하면 **"워크플로 디자인 타임을 시작할 수 없습니다." 라는** 오류 메시지가 표시 됩니다. 이전에 디자이너를 열려고 시도 하 고 프로젝트를 중단 또는 삭제 한 경우 확장 번들이 제대로 다운로드 되지 않을 수 있습니다. 이 원인이 문제 인지 확인 하려면 다음 단계를 수행 합니다.

  1. Visual Studio Code에서 출력 창을 엽니다. **보기** 메뉴에서 **출력** 을 선택 합니다.

  1. 출력 창의 제목 표시줄에 있는 목록에서 **Azure Logic Apps (미리 보기)** 를 선택 하 여 확장의 출력을 검토할 수 있습니다. 예를 들면 다음과 같습니다.

     !["Azure Logic Apps"가 선택 된 상태에서 출력 창을 보여 주는 스크린샷](./media/create-stateful-stateless-workflows-visual-studio-code/check-outout-window-azure-logic-apps.png)

  1. 출력을 검토 하 고이 오류 메시지가 표시 되는지 확인 합니다.

     ```text
     A host error has occurred during startup operation '{operationID}'.
     System.Private.CoreLib: The file 'C:\Users\{userName}\AppData\Local\Temp\Functions\
     ExtensionBundles\Microsoft.Azure.Functions.ExtensionBundle.Workflows\1.1.7\bin\
     DurableTask.AzureStorage.dll' already exists.
     Value cannot be null. (Parameter 'provider')
     Application is shutting down...
     Initialization cancellation requested by runtime.
     Stopping host...
     Host shutdown completed.
     ```

   이 오류를 해결 하려면이 위치에서 **Extensionbundles** 폴더를 삭제 **합니다. ..\Users \{ } \AppData\Local\Temp\Functions\ExtensionBundles** 를 다시 시작 하 고 디자이너에서 파일 **에workflow.js** 를 다시 엽니다.

<a name="missing-triggers-actions"></a>

### <a name="new-triggers-and-actions-are-missing-from-the-designer-picker-for-previously-created-workflows"></a>이전에 만든 워크플로의 디자이너 선택기에서 새 트리거 및 작업이 누락 됨

Azure Logic Apps 미리 보기는 Azure 함수 작업, 액체 작업 및 xml 작업 (예: xml **유효성 검사** 및 **변환 xml**)에 대 한 기본 제공 작업을 지원 합니다. 그러나 이전에 만든 논리 앱의 경우 이러한 작업은 Visual Studio Code 오래 된 버전의 확장 번들을 사용 하는지 여부를 선택 하기 위해 디자이너 선택기에 표시 되지 않을 수 있습니다 `Microsoft.Azure.Functions.ExtensionBundle.Workflows` .

또한 논리 앱을 만들 때 **azure에서 커넥터 사용** 을 설정 하거나 선택 하지 않는 한 **azure Function 작업** 커넥터 및 작업은 디자이너 선택기에 표시 되지 않습니다. 앱을 만들 때 Azure에서 배포 된 커넥터를 사용 하도록 설정 하지 않은 경우 Visual Studio Code의 프로젝트에서 사용 하도록 설정할 수 있습니다. 바로 가기 메뉴 **에서workflow.js** 를 열고 **Azure에서 커넥터 사용** 을 선택 합니다.

오래 된 번들을 수정 하려면 다음 단계를 수행 하 여 오래 된 번들을 삭제 합니다. 그러면 Visual Studio Code는 확장 번들을 최신 버전으로 자동으로 업데이트 합니다.

> [!NOTE]
> 이 솔루션은 Azure Portal을 사용 하 여 만든 논리 앱이 아니라 Azure Logic Apps (미리 보기) 확장과 함께 Visual Studio Code를 사용 하 여 만들고 배포 하는 논리 앱에만 적용 됩니다. [Azure Portal의 디자이너에서 지원 되는 트리거 및 작업이 누락](create-stateful-stateless-workflows-azure-portal.md#missing-triggers-actions)되었습니다 .를 참조 하세요.

1. 손실 하지 않으려는 작업을 모두 저장 하 고 Visual Studio를 닫습니다.

1. 컴퓨터에서 기존 번들의 버전이 있는 폴더를 포함 하는 다음 폴더로 이동 합니다.

   `...\Users\{your-username}\.azure-functions-core-tools\Functions\ExtensionBundles\Microsoft.Azure.Functions.ExtensionBundle.Workflows`

1. 이전 번들에 대 한 버전 폴더를 삭제 합니다. 예를 들어 버전 1.1.3 폴더가 있는 경우 해당 폴더를 삭제 합니다.

1. 이제 필요한 NuGet 패키지에 대 한 버전이 있는 폴더를 포함 하는 다음 폴더로 이동 합니다.

   `...\Users\{your-username}\.nuget\packages\microsoft.azure.workflows.webjobs.extension`

1. 이전 패키지에 대 한 버전 폴더를 삭제 합니다. 예를 들어 버전 1.0.0.8-preview에 대 한 폴더가 있는 경우 해당 폴더를 삭제 합니다.

1. 디자이너에서 파일의 Visual Studio Code, 프로젝트 및 **workflow.js** 를 다시 엽니다.

이제 누락 된 트리거와 작업이 디자이너에 표시 됩니다.

<a name="400-bad-request"></a>

### <a name="400-bad-request-appears-on-a-trigger-or-action"></a>트리거 또는 작업에 "400 잘못 된 요청"이 나타납니다.

실행이 실패 하 고 모니터링 보기에서 실행을 검사 하는 경우이 오류는 이름이 긴 트리거 또는 작업에 나타날 수 있으며,이로 인해 기본 URI (Uniform Resource Identifier)가 기본 문자 제한을 초과 합니다.

이 문제를 해결 하 고 긴 URI를 조정 하려면 `UrlSegmentMaxCount` `UrlSegmentMaxLength` 아래 단계를 수행 하 여 컴퓨터에서 및 레지스트리 키를 편집 합니다. 이러한 키의 기본값에 대 한 자세한 내용은이 항목 [Http.sys Windows에 대 한 레지스트리 설정](/troubleshoot/iis/httpsys-registry-windows)을 참조 하세요.

> [!IMPORTANT]
> 시작 하기 전에 작업을 저장 했는지 확인 합니다. 이 솔루션을 사용 하려면 변경 내용이 적용 될 수 있도록 작업을 수행한 후 컴퓨터를 다시 시작 해야 합니다.

1. 컴퓨터에서 **실행** 창을 열고 레지스트리 편집기를 여는 명령을 실행 합니다 `regedit` .

1. **사용자 계정 컨트롤** 상자에서 **예** 를 선택 하 여 컴퓨터에 대 한 변경 내용을 허용 합니다.

1. 왼쪽 창의 **컴퓨터** 에서 경로 **HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\HTTP\Parameters** 따라 노드를 확장 하 고 **매개 변수** 를 선택 합니다.

1. 오른쪽 창에서 `UrlSegmentMaxCount` 및 레지스트리 키를 찾습니다 `UrlSegmentMaxLength` .

1. Uri가 사용 하려는 이름을 수용할 수 있도록 이러한 키 값을 충분히 늘립니다. 이러한 키가 없으면 다음 단계를 수행 하 여 **매개 변수** 폴더에 추가 합니다.

   1. **매개 변수** 바로 가기 메뉴에서 **새**  >  **DWORD (32 비트) 값** 을 선택 합니다.

   1. 표시 되는 입력란에를 `UrlSegmentMaxCount` 새 키 이름으로 입력 합니다.

   1. 새 키의 바로 가기 메뉴를 열고 **수정** 을 선택 합니다.

   1. 표시 되는 **문자열 편집** 상자에 원하는 **값 데이터** 키 값을 16 진수 또는 10 진수 형식으로 입력 합니다. 예를 들어 `400` 16 진수의는 `1024` decimal의와 같습니다.

   1. 키 값을 추가 하려면 `UrlSegmentMaxLength` 다음 단계를 반복 합니다.

   이러한 키 값을 늘리거나 추가한 후 레지스트리 편집기는 다음 예와 같습니다.

   ![레지스트리 편집기를 보여 주는 스크린샷](media/create-stateful-stateless-workflows-visual-studio-code/edit-registry-settings-uri-length.png)

1. 준비가 되 면 변경 내용을 적용할 수 있도록 컴퓨터를 다시 시작 합니다.

<a name="debugging-fails-to-start"></a>

### <a name="debugging-session-fails-to-start"></a>디버깅 세션이 시작 되지 않음

디버깅 세션을 시작 하려고 하면 **"preLaunchTask ' generateDebugSymbols '을 실행 한 후 오류가 있습니다." 라는** 오류 메시지가 표시 됩니다. 이 문제를 해결 하려면 프로젝트에서 파일 **의tasks.js** 을 편집 하 여 기호 생성을 건너뜁니다.

1. 프로젝트에서 **. vscode** 폴더를 확장 하 고 파일 **에 대 한tasks.js** 를 엽니다.

1. 다음 작업에서 줄을 삭제 합니다 `"dependsOn: "generateDebugSymbols"` . 예를 들어 앞 줄을 종료 하는 쉼표와 함께를 삭제 합니다.

   이전:

   ```json
    {
      "type": "func",
      "command": "host start",
      "problemMatcher": "$func-watch",
      "isBackground": true,
      "dependsOn": "generateDebugSymbols"
    }
   ```

   이후:

   ```json
    {
      "type": "func",
      "command": "host start",
      "problemMatcher": "$func-watch",
      "isBackground": true
    }
   ```

## <a name="next-steps"></a>다음 단계

Azure Logic Apps (미리 보기) 확장을 사용 하 여 사용자의 경험을 듣고 싶습니다!

* 버그 또는 문제의 경우 [GitHub에서 문제를 만듭니다](https://github.com/Azure/logicapps/issues).
* 질문, 요청, 의견 및 기타 피드백은 [이 사용자 의견 양식을 사용](https://aka.ms/lafeedback)합니다.
