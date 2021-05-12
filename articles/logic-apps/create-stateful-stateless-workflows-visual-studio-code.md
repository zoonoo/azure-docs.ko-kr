---
title: Visual Studio Code에서 Logic Apps 미리 보기 워크플로 만들기
description: Azure Logic Apps(미리 보기) 확장을 사용하여 Visual Studio Code에서 자동화 및 통합 시나리오를 위한 워크플로를 빌드하고 실행합니다.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, az-logic-apps-dev
ms.topic: conceptual
ms.date: 03/30/2021
ms.openlocfilehash: 491d5f14cc8f456d228a5bc6efaa6686575979c1
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106078743"
---
# <a name="create-stateful-and-stateless-workflows-in-visual-studio-code-with-the-azure-logic-apps-preview-extension"></a>Azure Logic Apps(미리 보기) 확장을 사용하여 Visual Studio Code에서 상태 저장 또는 상태 비저장 워크플로 만들기

> [!IMPORTANT]
> 이 기능은 공개 미리 보기 상태이고 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

[Azure Logic Apps 미리 보기](logic-apps-overview-preview.md)를 사용하면 Azure Logic Apps(미리 보기) 확장을 사용하여 Visual Studio Code에서 [ *상태 저장* 및 *상태 비저장* 워크플로](logic-apps-overview-preview.md#stateful-stateless)를 포함하는 논리 앱을 만들고 실행하여 앱, 데이터, 클라우드 서비스 및 시스템에 자동화 및 통합 솔루션을 빌드할 수 있습니다. 이 새 논리 앱 유형을 사용하면 Azure뿐 아니라 Docker 컨테이너를 포함한 다양한 호스팅 환경에서 이식성, 향상된 성능 및 유연성을 제공하는 재설계된 Azure Logic Apps 미리 보기 런타임으로 구동되는 여러 워크플로를 빌드할 수 있습니다. 새 논리 앱 유형에 대한 자세한 내용은 [Azure Logic Apps 미리 보기 개요](logic-apps-overview-preview.md)를 참조하세요.

![Visual Studio Code, 논리 앱 프로젝트 및 워크플로를 보여주는 스크린샷입니다.](./media/create-stateful-stateless-workflows-visual-studio-code/visual-studio-code-logic-apps-overview.png)

Visual Studio Code에서는 Azure Logic Apps(미리보기) 확장을 사용하여 개발 환경에서 논리 앱의 워크플로를 *로컬로* 빌드 및 실행할 수 있는 프로젝트를 만드는 것으로 시작할 수 있습니다. Azure Portal에서 [새 **Logic App(미리보기)** 리소스를](create-stateful-stateless-workflows-azure-portal.md) 만들어 시작할 수도 있지만 두 접근법 모두 동일한 종류의 호스팅 환경에서 논리 앱을 배포 및 실행할 수 있는 기능을 제공합니다.

예전처럼 원래 논리 앱 유형을 만들 수도 있습니다. Visual Studio Code의 개발 환경은 원래 논리 앱과 새 논리 앱 유형 간에 서로 다르지만 Azure 구독에는 두 유형을 모두 포함할 수 있습니다. Azure 구독에서 배포된 모든 논리 앱을 보고 액세스할 수 있지만 앱은 자체 범주 및 섹션으로 구성됩니다.

이 문서에서는 Azure Logic Apps(미리 보기) 확장을 사용하고 다음과 같은 상위 수준 작업을 수행하여 Visual Studio Code에서 논리 앱 및 워크플로를 만드는 방법을 안내합니다:

* 논리 앱 및 워크플로에 대한 프로젝트를 만듭니다.

* 트리거 및 작업을 추가합니다.

* 로컬에서 실행 기록을 실행, 테스트, 디버그 및 검토합니다.

* 방화벽 액세스를 위한 도메인 이름 세부 정보를 찾습니다.

* 필요에 따라 Application Insights를 사용하도록 설정하는 Azure에 배포합니다.

* Visual Studio Code 및 Azure Portal에서 배포된 논리 앱을 관리합니다.

* 상태 비저장 워크플로에 실행 기록을 사용하도록 설정합니다.

* 배포 후 Application Insights를 사용하도록 설정하거나 엽니다.

* 어디서나 실행할 수 있는 Docker 컨테이너에 배포합니다.

> [!NOTE]
> 현재 알려진 문제에 대한 자세한 내용은 [GitHub의 Logic Apps 공개 미리 보기 알려진 문제 페이지](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md)를 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소

### <a name="access-and-connectivity"></a>액세스 및 연결

* 요구 사항을 다운로드하고, Visual Studio Code에서 Azure 계정으로 연결하고, Visual Studio Code에서 Azure, Docker 컨테이너 또는 다른 환경으로 게시할 수 있도록 인터넷에 액세스합니다.

* Azure 계정 및 구독 구독이 없는 경우 [Azure 체험 계정에 등록](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)합니다.

* 이 문서와 동일한 예제 논리 앱을 빌드하려면 Microsoft 회사 또는 학교 계정으로 로그인하는 Office 365 Outlook 이메일 계정이 필요합니다.

  Outlook.com 또는 [Gmail](../connectors/connectors-google-data-security-privacy-policy.md)처럼 [Azure Logic Apps에서 지원하는 다른 이메일 커넥터](/connectors/)를 사용하기로 선택하는 경우에도 예제를 계속 진행할 수 있고 일반적인 단계는 대체적으로 동일하지만 사용자 인터페이스 및 옵션은 다를 수 있습니다. 예를 들어 Outlook.com 커넥터를 사용하는 경우 개인 Microsoft 계정을 대신 사용하여 로그인합니다.

<a name="storage-requirements"></a>

### <a name="storage-requirements"></a>저장소 요구 사항

#### <a name="windows"></a>Windows

Windows를 사용할 때 Visual Studio Code에서 논리 앱 프로젝트를 로컬로 빌드 및 실행하려면 다음 단계에 따라 Azure Storage Emulator를 설정합니다:

1. [Azure Storage Emulator 5.10](https://go.microsoft.com/fwlink/p/?linkid=717179)를 다운로드 및 설치합니다.

1. 아직 없는 경우 에뮬레이터를 실행할 수 있도록 무료 [SQL Server 2019 Express Edition](https://go.microsoft.com/fwlink/p/?linkid=866658)과 같은 로컬 SQL DB 설치되어 있어야 합니다.

   자세한 내용은 [개발 및 테스트에 Azure Storage Emulator 사용](../storage/common/storage-use-emulator.md)을 참조하세요.

1. 프로젝트를 실행하려면 먼저 에뮬레이터를 시작해야 합니다.

   ![실행 중인 Azure Storage Emulator를 보여주는 스크린샷입니다.](./media/create-stateful-stateless-workflows-visual-studio-code/start-storage-emulator.png)

#### <a name="macos-and-linux"></a>macOS 및 Linux

macOS 또는 Linux를 사용할 때 Visual Studio Code에서 논리 앱 프로젝트를 로컬로 빌드 및 실행하려면 다음 단계에 따라 Azure Storage 계정을 만들고 설정합니다.

> [!NOTE]
> 현재 Visual Studio Code의 디자이너는 Linux OS에서 작동하지 않지만, Logic Apps 미리 보기 런타임을 사용하는 논리 앱을 Linux 기반 가상 머신에 계속 빌드, 실행 및 배포할 수 있습니다. 지금은 Windows 또는 macOS의 Visual Studio Code에서 논리 앱을 빌드한 다음 Linux 기반 가상 머신에 배포할 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인하고 [Azure Functions의 사전 요구 사항](../azure-functions/storage-considerations.md)인 [Azure Storage 계정을 만듭니다](../storage/common/storage-account-create.md?tabs=azure-portal).

1. 스토리지 계정 메뉴 **설정** 아래의 **액세스 키** 를 선택합니다.

1. **액세스 키** 창에서 스토리지 계정의 연결 문자열을 찾아 복사합니다. 다음의 예제와 유사합니다:

   `DefaultEndpointsProtocol=https;AccountName=fabrikamstorageacct;AccountKey=<access-key>;EndpointSuffix=core.windows.net`

   ![스토리지 계정 액세스 키와 연결 문자열이 복사된 Azure Portal을 보여주는 스크린샷입니다.](./media/create-stateful-stateless-workflows-visual-studio-code/find-storage-account-connection-string.png)

   자세한 내용은 [스토리지 계정 키 관리](../storage/common/storage-account-keys-manage.md?tabs=azure-portal#view-account-access-keys)를 검토하세요.

1. 안전한 장소에 연결 문자열을 저장합니다. Visual Studio Code에서 논리 앱 프로젝트를 만든 후에는 프로젝트의 루트 수준 폴더에 있는 **local.settings.json** 파일에 문자열을 추가해야 합니다.

   > [!IMPORTANT]
   > Docker 컨테이너에 배포하려는 경우 배포에 사용하는 Docker 파일에도 이 연결 문자열을 사용해야 합니다. 프로덕션 시나리오의 경우 키 자격 증명 모음을 사용하는 등 이러한 암호와 중요한 정보를 보호하고 안전하게 지켜야 합니다.
  
### <a name="tools"></a>도구

* [Visual Studio Code 1.30.1(2019년 1월) 이상 ](https://code.visualstudio.com/)(무료) 또한, Visual Studio Code용 도구가 아직 없는 경우 다운로드하여 설치합니다:

  * [Azure 계정 확장](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)-Visual Studio Code의 다른 모든 Azure 확장에 대해 단일 공통 Azure 로그인 및 구독 필터링 환경을 제공합니다.

  * [Visual Studio Code 확장용 C#](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)-F5 기능을 사용하여 논리 앱을 실행할 수 있습니다.

  * MSI(Microsoft Installer) 버전 (`func-cli-3.0.3245-x*.msi`)을 사용하여 [Azure Functions Core Tools 3.0.3245 이상](https://github.com/Azure/azure-functions-core-tools/releases/tag/3.0.3245).

    이러한 도구에는 Visual Studio Code의 미리 보기 확장에서 사용하는 Azure Functions 런타임을 구동하는 동일한 런타임 버전이 포함됩니다.

    > [!IMPORTANT]
    > 해당 버전보다 이전 버전을 설치한 경우 먼저 이전 버전을 제거하거나 PATH 환경 변수가 다운로드 및 설치한 버전을 가리키는지 확인합니다.

  * [Visual Studio Code용 Azure Logic Apps(미리 보기) 확장](https://go.microsoft.com/fwlink/p/?linkid=2143167)입니다. 이 확장은 Visual Studio Code에서 로컬로 실행되는 상태 저장 및 상태 비저장 워크플로를 빌드하고 Azure 또는 Docker 컨테이너에 직접 배포하는 논리 앱을 만들 수 있는 기능을 제공합니다.

    현재 Visual Studio Code에 기존의 Azure Logic Apps 확장과 공개 미리 보기 확장을 모두 설치할 수 있습니다. 개발 환경은 확장 간에 서로 다르지만 Azure 구독에는 확장을 사용해서 만드는 두 유형을 모두 포함할 수 있습니다. Visual Studio Code는 Azure 구독에 배포된 모든 논리 앱을 표시하지만, 확장 이름, **Logic Apps** 및 **Azure Logic Apps(미리 보기)** 별로 서로 다른 섹션으로 구성합니다.

    > [!IMPORTANT]
    > 이전의 프라이빗 미리 보기 확장을 사용하여 논리 앱 프로젝트를 만든 경우 해당 프로젝트는 공개 미리 보기 확장에서 작동하지 않습니다. 그러나 프라이빗 미리 보기 확장을 제거하고, 연결된 파일을 삭제하고, 공개 미리 보기 확장을 설치하면 해당 프로젝트를 마이그레이션할 수 있습니다. 그런 다음 Visual Studio Code에서 새 프로젝트를 만들고 이전에 만든 논리 앱의 **워크플로. 정의** 파일을 새 프로젝트에 복사합니다. 자세한 내용은 [프라이빗 미리 보기 확장에서 마이그레이션](#migrate-private-preview)을 참조하세요.
    > 
    > 이전의 공개 미리 보기 확장을 사용하여 논리 앱 프로젝트를 만든 경우 마이그레이션 단계 없이 해당 프로젝트를 계속 사용할 수 있습니다.

    ****Azure Logic Apps(미리 보기)** 확장을 설치하려면 다음 단계를 수행합니다:**

    1. Visual Studio Code의 왼쪽 도구 모음에서 **확장** 을 선택합니다.

    1. 확장 검색 상자에 `azure logic apps preview`을 입력합니다. 결과 목록에서 **Azure Logic Apps(미리 보기)** **>** **설치** 를 선택합니다.

       설치가 완료되면 미리 보기 확장이 **확장: 설치됨** 목록에 표시됩니다.

       !["Azure Logic Apps(미리 보기)" 확장에 밑줄이 그어진 Visual Studio Code의 설치된 확장 목록을 보여주는 스크린샷입니다.](./media/create-stateful-stateless-workflows-visual-studio-code/azure-logic-apps-extension-installed.png)

       > [!TIP]
       > 확장이 설치된 목록에 표시되지 않으면 Visual Studio Code를 다시 시작하십시오.

* JavaScript를 실행하는 [인라인 코드 작업 동작](../logic-apps/logic-apps-add-run-inline-code.md)을 사용하려면 [Node.js 버전 10.x.x, 11.x.x, 또는 12.x.x.](https://nodejs.org/en/download/releases/)를 설치합니다.

  > [!TIP] 
  > Windows의 경우 MSI 버전을 다운로드합니다. ZIP 버전을 대신 사용하는 경우 운영 체제의 PATH 환경 변수를 사용하여 Node.js를 사용하도록 수동으로 설정해야 합니다.

* 로컬에서 [기본 제공 HTTP 웹후크 트리거](../connectors/connectors-native-webhook.md)와 같은 웹후크 기반 트리거와 작업을 실행하려면 Visual Studio Code에서 [콜백 URL의 착신 전환을 설정](#webhook-setup)해야 합니다.

* 이 문서에서 만드는 예제 논리 앱을 테스트하려면 예제 논리 앱의 첫 단계인 요청 트리거에 호출을 보낼 수 있는 도구가 필요합니다. 해당 도구가 없는 경우 [Postman](https://www.postman.com/downloads/)을 다운로드하여 설치하고 사용할 수 있습니다.

* [Application Insights](../azure-monitor/app/app-insights-overview.md) 사용을 지원하는 설정으로 논리 앱을 만들고 배포하는 경우 필요에 따라 논리 앱에 진단 로깅 및 추적을 사용하도록 설정할 수 있습니다. Visual Studio Code에서 논리 앱을 배포할 때 또는 배포 후에 이러한 작업을 수행할 수 있습니다. Application Insights 인스턴스가 필요하지만, 논리 앱을 배포할 때 [미리](../azure-monitor/app/create-workspace-resource.md) 또는 배포 후에 이 리소스를 만들 수 있습니다.

<a name="migrate-private-preview"></a>

## <a name="migrate-from-private-preview-extension"></a>프라이빗 미리 보기 확장에서 마이그레이션

**Azure Logic Apps(프라이빗 미리 보기)**  확장으로 만든 모든 논리 앱 프로젝트는 공개 미리 보기 확장에서 작동하지 않습니다. 그러나 다음 단계를 수행하여 이러한 프로젝트를 새 프로젝트로 마이그레이션 할 수 있습니다:

1. 프라이빗 미리 보기 확장을 제거합니다.

1. 다음 위치에서 연결된 확장 번들 및 NuGet 패키지 폴더를 삭제합니다:

   * 이전 확장 번들을 포함하고 다음 경로를 따라 배치된 **Microsoft.Azure.Functions.ExtensionBundle.Workflows** 폴더:

     * `C:\Users\{userName}\AppData\Local\Temp\Functions\ExtensionBundles`

     * `C:\Users\{userName}\.azure-functions-core-tools\Functions\ExtensionBundles`

   * 프라이빗 미리 보기 확장에 대한 [NuGet](/nuget/what-is-nuget) 캐시이자, 이 경로를 따라 배치된 **microsoft.azure.workflows.webjobs.extension** 폴더:

     `C:\Users\{userName}\.nuget\packages`

1. **Azure Logic Apps(미리 보기)** 확장을 설치합니다.

1. Visual Studio Code에서 새 프로젝트를 만듭니다.

1. 이전에 만든 논리 앱의 **워크플로 정의** 파일을 새 프로젝트에 복사합니다.

<a name="set-up"></a>

## <a name="set-up-visual-studio-code"></a>Visual Studio Code 설정

1. 모든 확장이 올바르게 설치되었는지 확인하려면 Visual Studio Code를 다시 로드하거나 다시 시작합니다.

1. 미리 보기 확장이 최신 업데이트를 얻을 수 있도록 Visual Studio Code가 확장 업데이트를 자동으로 찾고 설치하는지 확인합니다. 그렇지 않으면 오래된 버전을 수동으로 제거하고 최신 버전을 설치해야 합니다.

   1. **파일** 메뉴에서 **기본 설정** **>** **설정** 으로 이동합니다.

   1. **사용자** 탭에서 **기능** **>** **확장** 으로 이동합니다.

   1. **업데이트 자동 확인** 및 **자동 업데이트** 가 선택되어 있는지 확인합니다.

또한 기본적으로 Logic Apps 미리 보기 확장에 대해 다음 설정이 활성화되고 설정됩니다:

* **Azure Logic Apps V2: 프로젝트 런타임**, 버전 **~3** 으로 설정됨

  > [!NOTE]
  > 이 버전은 [인라인 코드 작업](../logic-apps/logic-apps-add-run-inline-code.md)을 사용하는 데 필요합니다.

* **Azure Logic Apps V2: 실험적 보기 관리자**, Visual Studio Code에서 최신 디자이너를 사용하도록 설정합니다. 항목 끌어서 놓기와 같이 디자이너에 문제가 발생하는 경우 이 설정을 해제합니다.

해당 설정을 찾아서 확인하려면 다음 단계를 수행합니다:

1. **파일** 메뉴에서 **기본 설정** **>** **설정** 으로 이동합니다.

1. **사용자** 탭에서 **>** **확장** **>** **Azure Logic Apps(미리 보기)** 로 이동합니다.

   예를 들어 여기에서 **Azure Logic Apps V2 : 프로젝트 런타임** 설정을 찾거나 검색 상자를 사용하여 다른 설정을 찾을 수 있습니다:

   !["Azure Logic Apps(미리 보기)" 확장에 대한 Visual Studio Code 설정을 보여주는 스크린샷입니다.](./media/create-stateful-stateless-workflows-visual-studio-code/azure-logic-apps-preview-settings.png)

<a name="connect-azure-account"></a>

## <a name="connect-to-your-azure-account"></a>Azure 계정에 연결

1. Visual Studio Code 작업 막대에서 Azure 아이콘을 선택합니다.

   ![활동 표시줄과 선택한 Azure 아이콘 Visual Studio Code 보여주는 스크린샷입니다.](./media/create-stateful-stateless-workflows-visual-studio-code/visual-studio-code-azure-icon.png)

1. Azure 창에서 **Azure: Logic Apps(미리 보기)** 아래의 **Azure에 로그인** 을 선택합니다. Visual Studio Code 인증 페이지가 표시되면 Azure 계정으로 로그인합니다.

   ![Azure 창과 Azure 로그인을 위해 선택한 링크를 보여주는 스크린샷입니다.](./media/create-stateful-stateless-workflows-visual-studio-code/sign-in-azure-subscription.png)

   로그인하면 Azure 창에는 Azure 계정의 구독이 표시됩니다. 또한 공개적으로 릴리스된 확장이 있는 경우 **Logic Apps(미리 보기)** 섹션이 아닌 **Logic Apps** 섹션에서 해당 확장으로 만든 논리 앱을 찾을 수 있습니다.
   
   예상되는 구독이 표시되지 않거나 창에 특정 구독만 표시하려면 다음 단계를 수행합니다:

   1. 구독 목록에서 **구독 선택** 단추(필터 아이콘)가 나타날 때까지 첫 번째 구독 옆으로 포인터를 이동합니다. 필터 아이콘을 선택합니다.

      ![Azure 창 및 선택한 필터 아이콘을 보여주는 스크린샷입니다.](./media/create-stateful-stateless-workflows-visual-studio-code/filter-subscription-list.png)

      또는 Visual Studio Code 상태 표시줄에서 Azure 계정을 선택합니다. 

   1. 다른 구독 목록이 표시되면 원하는 구독을 선택한 다음 **확인** 을 선택했는지 확인합니다.

<a name="create-project"></a>

## <a name="create-a-local-project"></a>로컬 프로젝트 만들기

논리 앱을 만들기 전에 Visual Studio Code에서 논리 앱을 관리, 실행 및 배포할 수 있도록 로컬 프로젝트를 만듭니다. 기본 프로젝트는 함수 앱 프로젝트라고도 하는 Azure Functions 프로젝트와 비슷합니다. 그러나 해당 프로젝트 유형은 서로 별개이므로 논리 앱과 함수 앱을 동일한 프로젝트에서 사용할 수 없습니다.

1. 컴퓨터에서 나중에 Visual Studio Code에서 만들 프로젝트에 사용할 *빈* 로컬 폴더를 만듭니다.

1. Visual Studio Code에서 열려 있는 모든 폴더를 닫습니다.

1. Azure 창에서 **Azure: Logic Apps(미리 보기)** 옆의 **새 프로젝트 만들기**(폴더 및 번개 모양 아이콘)를 선택합니다.

   !["새 프로젝트 만들기"가 선택된 Azure 창 도구 모음을 보여주는 스크린샷입니다.](./media/create-stateful-stateless-workflows-visual-studio-code/create-new-project-folder.png)

1. Windows Defender 방화벽에서 `Code.exe`(Visual Studio Code) 및 `func.exe`(Azure Functions Core Tools)에 대한 네트워크 액세스 권한을 부여하라는 프롬프트가 표시되면 **홈 네트워크, 회사 네트워크 등의 개인 네트워크** **>** **액세스 허용** 을 선택합니다.

1. 프로젝트 폴더를 만든 위치로 이동하여 해당 폴더를 선택한 다음 계속합니다.

   ![새로 만든 프로젝트 폴더와 "선택" 단추가 선택된 "폴더 선택" 대화 상자를 보여주는 스크린샷입니다.](./media/create-stateful-stateless-workflows-visual-studio-code/select-project-folder.png)

1. 표시되는 템플릿 목록에서 **상태 저장 워크플로** 또는 **상태 비저장 워크플로** 를 선택합니다. 이 예제에서는 **상태 저장 워크플로** 를 선택합니다.

   !["상태 저장 워크플로"가 선택된 워크플로 템플릿 목록을 보여주는 스크린샷입니다.](./media/create-stateful-stateless-workflows-visual-studio-code/select-stateful-stateless-workflow.png)

1. 워크플로의 이름을 입력하고 Enter 키를 누릅니다. 이 예제에서는 `Fabrikam-Stateful-Workflow`을(를) 이름으로 사용합니다.

   !["새 상태 저장 워크플로 만들기(3/4)" 상자와 "Fabrikam-상태 저장-워크플로"를 워크플로 이름으로 보여주는 스크린샷입니다.](./media/create-stateful-stateless-workflows-visual-studio-code/name-your-workflow.png)

   Visual Studio Code에서 프로젝트 만들기를 완료하고 코드 편집기에서 워크플로에 대한 **workflow.json** 파일을 엽니다.

   > [!NOTE]
   > 프로젝트를 여는 방법을 선택하라는 메시지가 표시되면 현재 Visual Studio Code 창에서 프로젝트를 열려는 경우 **현재 창에서 열기** 를 선택합니다. Visual Studio Code에서 새 인스턴스를 열려면 **새 창에서 열기** 를 선택합니다.

1. Visual Studio 도구 모음에서 탐색기 창을 엽니다(아직 열려 있지 않은 경우).

   이제 자동으로 생성된 프로젝트 파일이 포함된 프로젝트가 탐색기 창에 표시됩니다. 예를 들어 프로젝트에 워크플로 이름을 표시하는 폴더가 있습니다. 이 폴더에서 **workflow.json** 파일에는 워크플로의 기본 JSON 정의가 포함되어 있습니다.

   ![프로젝트 폴더, 워크플로 폴더 및 "workflow.json" 파일이 표시된 탐색기 창을 보여주는 스크린샷입니다.](./media/create-stateful-stateless-workflows-visual-studio-code/local-project-created.png)

1. macOS 또는 Linux를 사용하는 경우 프로젝트를 로컬에서 실행하는 데 필요한 다음 단계를 수행하여 스토리지 계정에 대한 액세스를 설정합니다:

   1. 프로젝트의 루트 폴더에서 **local.settings.json** 파일을 엽니다.

      ![탐색기 창과 프로젝트의 'local.settings.json'파일을 보여주는 스크린샷입니다.](./media/create-stateful-stateless-workflows-visual-studio-code/local-settings-json-files.png)

   1. `AzureWebJobsStorage` 속성 값을 앞에서 저장한 스토리지 계정의 연결 문자열로 바꿉니다. 예를 들면 다음과 같습니다:

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

      > [!IMPORTANT]
      > 프로덕션 시나리오의 경우 키 자격 증명 모음을 사용하는 등 이러한 암호와 중요한 정보를 보호하고 안전하게 지켜야 합니다.

   1. 완료되면 변경 내용을 저장해야 합니다.

<a name="enable-built-in-connector-authoring"></a>

## <a name="enable-built-in-connector-authoring"></a>기본 제공 커넥터 제작 사용

[미리 보기 릴리스의 확장성 프레임워크](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-built-in-connector/ba-p/1921272)를 사용하여 필요한 모든 서비스에 대한 기본 제공 커넥터를 만들 수 있습니다. Azure Service Bus 및 SQL Server와 같은 기본 제공 커넥터와 마찬가지로, 이러한 커넥터는 더 높은 처리량, 짧은 대기 시간, 로컬 연결을 제공하고 기본적으로 미리 보기 런타임과 동일한 프로세스에서 실행됩니다.

제작 기능은 현재 Visual Studio Code에서만 사용할 수 있지만, 기본적으로 사용하도록 설정되어 있지 않습니다. 이러한 커넥터를 만들려면 먼저 확장 번들 기반(Node.js)에서 NuGet 패키지 기반(.NET)으로 프로젝트를 변환해야 합니다.

> [!IMPORTANT]
> 이 작업은 단방향 작업으로 실행 취소할 수 없습니다.

1. 탐색기 창의 프로젝트 루트에서 다른 모든 파일 및 폴더 아래의 빈 영역 위로 마우스 포인터를 이동하고, 바로 가기 메뉴를 연 다음 **Nuget 기반 논리 앱 프로젝트로 변환** 을 선택합니다.

   ![프로젝트 창의 빈 영역에서 프로젝트 바로가기 메뉴를 연 상태의 탐색기 창을 보여주는 스크린샷입니다.](./media/create-stateful-stateless-workflows-visual-studio-code/convert-logic-app-project.png)

1. 프롬프트가 표시되면 프로젝트 변환을 확인합니다.

1. 계속하려면 [Azure Logic Apps Running Anywhere - 기본 제공 커넥터 확장성](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-built-in-connector/ba-p/1921272) 문서의 단계를 검토하고 수행합니다.

<a name="open-workflow-definition-designer"></a>

## <a name="open-the-workflow-definition-file-in-the-designer"></a>디자이너에서 워크플로 정의 파일 열기

1. 다음 명령을 실행하여 컴퓨터에 설치된 버전을 확인합니다:

   `..\Users\{yourUserName}\dotnet --list-sdks`

   .NET Core SDK 5.x를 사용하는 경우 이 버전을 사용하면 디자이너에서 논리 앱의 기본 워크플로 정의를 열 수 없을 수도 있습니다. 이 버전을 제거하는 대신 프로젝트의 루트 폴더에서 3.1.201 이후의 .NET Core 런타임 3.x 버전을 참조하는 **global.json** 파일을 만듭니다. 예를 들면 다음과 같습니다:

   ```json
   {
      "sdk": {
         "version": "3.1.8",
         "rollForward": "disable"
      }
   }
   ```

   > [!IMPORTANT]
   > Visual Studio Code에서 프로젝트의 루트 폴더에 **global.json** 파일을 명시적으로 추가해야 합니다. 그렇지 않으면 디자이너가 열리지 않습니다.

1. 워크플로에 대한 프로젝트 폴더를 확장합니다. 파일의 바로 가기 메뉴에서 **workflow.json** 을 열고 **디자이너에서 열기** 를 선택합니다.

   !["디자이너에서 열기"가 선택된 workflow.json 파일의 탐색기 창과 바로 가기 창을 보여주는 스크린샷입니다.](./media/create-stateful-stateless-workflows-visual-studio-code/open-definition-file-in-designer.png)

1. **Azure에서 커넥터 사용** 목록에서 Azure 서비스용 커넥터뿐 아니라 Azure에서 사용할 수 있고 배포된 모든 관리 커넥터에 적용되는 **Azure 커넥터 사용** 을 선택합니다.

   !["Azure에서 커넥터 사용"목록이 열려있고 "Azure에서 커넥터 사용"이 선택된 탐색기 창을 보여주는 스크린샷입니다.](./media/create-stateful-stateless-workflows-visual-studio-code/use-connectors-from-azure.png)

   > [!NOTE]
   > 상태 비 저장 워크플로는 현재 트리거가 아닌 Azure에 배포되는 [관리 커넥터](../connectors/apis-list.md#managed-api-connectors)에 대한 *작업* 만 지원합니다. 상태 비저장 워크플로에 대해 Azure에서 커넥터를 사용하도록 설정하는 옵션이 있지만 사용자가 선택할 수 있는 관리되는 커넥터 트리거가 디자이너에 표시되지 않습니다.

1. **구독 선택** 목록에서 논리 앱 프로젝트에 사용할 Azure 구독을 선택합니다.

   !["구독 선택" 상자와 선택한 구독이 있는 탐색기 창을 보여주는 스크린샷입니다.](./media/create-stateful-stateless-workflows-visual-studio-code/select-azure-subscription.png)

1. 리소스 그룹 목록에서 **새 리소스 그룹 만들기** 를 선택합니다.

   ![리소스 그룹 목록과 "새 리소스 그룹 만들기"가 선택된 탐색기 창을 보여주는 스크린샷입니다.](./media/create-stateful-stateless-workflows-visual-studio-code/create-select-resource-group.png)

1. Azure 리소스 그룹의 이름을 입력하고 Enter 키를 누릅니다. 이 예에서는 `Fabrikam-Workflows-RG`를 사용합니다.

   ![탐색기 창 및 리소스 그룹 이름 상자를 보여주는 스크린샷입니다.](./media/create-stateful-stateless-workflows-visual-studio-code/enter-name-for-resource-group.png)

1. 위치 목록에서 리소스 그룹 및 리소스를 만들 때 사용할 Azure 지역을 찾아 선택합니다. 이 예제에서는 **미국 중서부** 를 사용합니다.

   ![위치 목록 및 "미국 중서부"가 선택된 탐색기 창을 보여주는 스크린샷입니다.](./media/create-stateful-stateless-workflows-visual-studio-code/select-azure-region.png)

   이 단계를 수행하면 Visual Studio Code가 워크플로 디자이너를 엽니다.

   > [!NOTE]
   > Visual Studio Code가 워크플로 디자인 타임 API를 시작할 때, 시작하는 데 몇 초 정도 걸릴 수 있다는 메시지가 표시될 수 있습니다. 이 메시지를 무시하거나 **확인을** 선택할 수 있습니다.
   >
   > 디자이너가 열리지 않는 경우 문제 해결 섹션의 [디자이너가 열리지 않습니다](#designer-fails-to-open)를 검토합니다.

   디자이너가 표시되면 **작업 선택** 프롬프트가 디자이너에 표시되고 기본적으로 선택되어 **작업 추가** 창이 표시됩니다.

   ![워크플로 디자이너를 보여주는 스크린샷입니다.](./media/create-stateful-stateless-workflows-visual-studio-code/workflow-app-designer.png)

1. 다음으로, 워크플로에 [트리거 및 작업 추가](#add-trigger-actions)를 진행합니다.

<a name="add-trigger-actions"></a>

## <a name="add-a-trigger-and-actions"></a>트리거 및 작업 추가

디자이너를 열면 **작업 선택** 프롬프트가 디자이너에 표시되고 기본적으로 선택됩니다. 이제 트리거 및 작업 추가를 진행하여 워크플로 만들기를 시작할 수 있습니다.

이 예제의 워크플로는 이 트리거와 다음 작업을 사용합니다:

* 기본 제공 [요청 트리거](../connectors/connectors-native-reqres.md) - **HTTP 요청** 이 수신될 때 인바운드 호출 또는 요청을 수신하고 다른 서비스 또는 논리 앱이 호출할 수 있는 엔드포인트를 만듭니다.

* [Office 365 Outlook 작업](../connectors/connectors-create-api-office365-outlook.md), **이메일 보내기**.

* 기본 제공 [응답 작업](../connectors/connectors-native-reqres.md) - 회신을 보내고 호출자에게 데이터를 반환하는 데 사용됩니다.

### <a name="add-the-request-trigger"></a>요청 트리거 추가

1. 디자이너 옆의 **트리거 추가** 창의 **작업 선택** 검색 상자에서 **기본 제공** 이 선택되어 있는지 확인하고 기본적으로 실행되는 트리거를 선택할 수 있습니다.

1. **작업 선택** 검색 상자에 `when a http request`을(를) 입력하고 **HTTP 요청을 수신하는 경우** 라는 이름의 기본 제공 요청 트리거를 선택합니다.

   ![워크플로 디자이너와 "HTTP 요청을 수신하는 경우" 트리거가 선택된 **트리거 추가** 창을 보여주는 스크린샷입니다.](./media/create-stateful-stateless-workflows-visual-studio-code/add-request-trigger.png)

   트리거가 디자이너에 표시되면 트리거의 세부 정보 창이 열리고 트리거의 속성, 설정 및 기타 작업이 표시됩니다.

   !["HTTP 요청을 수신하는 경우" 트리거가 선택되고 트리거 세부 정보 창이 열려 있는 워크플로 디자이너를 보여주는 스크린샷입니다.](./media/create-stateful-stateless-workflows-visual-studio-code/request-trigger-added-to-designer.png)

   > [!TIP]
   > 세부 정보 창이 표시되지 않으면 디자이너에서 트리거를 선택했는지 확인합니다.

1. 디자이너에서 항목을 삭제해야 하는 경우 [디자이너에서 항목을 삭제하는 다음 단계를 수행](#delete-from-designer)합니다.

### <a name="add-the-office-365-outlook-action"></a>Office 365 Outlook 작업 추가

1. 디자이너에서, 추가한 트리거 아래의 **새 단계** 를 선택합니다.

   **작업 선택** 프롬프트가 디자이너에 표시되고 다음 작업을 선택할 수 있도록 **작업 추가** 창이 다시 열립니다.

1. **작업 추가** 창에서 **작업 선택** 검색 상자 아래의 **Azure** 를 선택하여 Azure에서 배포된 관리 커넥터에 대한 작업을 찾아 선택할 수 있습니다.

   이 예제에서는 **이메일 보내기(V2)** 라는 Office 365 Outlook 작업을 선택 및 사용합니다.

   ![Office 365 Outlook "이메일 보내기" 작업이 선택된 워크플로 디자이너 및 ** 작업 추가 ** 창을 보여주는 스크린샷입니다.](./media/create-stateful-stateless-workflows-visual-studio-code/add-send-email-action.png)

1. 작업의 세부 정보 창에서 **로그인** 을 선택하여 이메일 계정에 연결합니다.

   !["로그인"이 선택된 상태에서 워크플로 디자이너 및 ** 이메일 보내기 (V2) ** 창을 보여주는 스크린샷입니다.](./media/create-stateful-stateless-workflows-visual-studio-code/send-email-action-sign-in.png)

1. Visual Studio Code에 이메일 계정에 대한 액세스에 동의를 요청하는 프롬프트가 표시되면 **열기** 를 선택합니다.

   ![액세스를 허용하는 Visual Studio Code 프롬프트를 보여주는 스크린샷입니다.](./media/create-stateful-stateless-workflows-visual-studio-code/visual-studio-code-open-external-website.png)

   > [!TIP]
   > 이후 프롬프트가 표시되지 않도록 하려면 인증 페이지를 트러스트된 도메인으로 추가할 수 있도록 **트러스트된 도메인 구성** 을 선택합니다.

1. 후속 프롬프트에 따라 로그인하여 액세스를 허용하고 Visual Studio Code로 돌아가기를 허용합니다.

   > [!NOTE]
   > 프롬프트를 완료하기 전에 너무 많은 시간이 경과하면 인증 프로세스 시간이 초과되고 실패합니다. 이 경우 디자이너로 돌아가서 로그인을 다시 시도하여 연결을 만듭니다.

1. Azure Logic Apps(미리 보기) 확장에서 이메일 계정에 대한 액세스에 동의를 요청하는 프롬프트가 표시되면 **열기** 를 선택합니다. 후속 프롬프트에 따라 액세스를 허용합니다.

   ![액세스를 허용하는 미리 보기 확장 프롬프트를 보여주는 스크린샷입니다.](./media/create-stateful-stateless-workflows-visual-studio-code/allow-preview-extension-open-uri.png)

   > [!TIP]
   > 이후 프롬프트가 표시되지 않도록 하려면 **이 확장에 대해 다시 묻지 않음** 을 선택합니다.

   Visual Studio Code에서 연결을 만든 후 일부 커넥터는 `The connection will be valid for {n} days only` 메시지를 표시합니다. 이 시간제한은 Visual Studio Code에서 논리 앱을 작성하는 동안에만 적용됩니다. 배포 후에는 논리 앱이 자동으로 사용 설정된 [시스템이 할당한 관리 ID](../logic-apps/create-managed-service-identity.md)를 사용하여 런타임에 인증할 수 있으므로 이 제한이 더 이상 적용되지 않습니다. 이 관리 ID는 연결을 만들 때 사용하는 인증 자격 증명이나 연결 문자열과는 다릅니다. 이 시스템이 할당한 관리 ID를 사용하지 않도록 설정하면 런타임에 연결이 작동하지 않습니다.

1. 디자이너에서 **이메일 보내기** 작업이 선택되어 있지 않으면 해당 작업을 선택합니다.

1. 작업 세부 정보 창의 **매개 변수** 탭에서 작업에 필요한 정보를 입력합니다. 예를 들면 다음과 같습니다:

   ![Office 365 Outlook "이메일 보내기" 작업에 대한 세부 정보가 포함된 워크플로 디자이너를 보여주는 스크린샷입니다.](./media/create-stateful-stateless-workflows-visual-studio-code/send-email-action-details.png)

   | 속성 | 필수 | 값 | Description |
   |----------|----------|-------|-------------|
   | **수행할 작업** | 예 | <*your-email-address*> | 이메일을 받는 사람. 테스트 목적의 이메일 주소를 사용할 수 있습니다. 이 예제에서는 가상의 이메일 주소 `sophiaowen@fabrikam.com`을 사용합니다. |
   | **Subject** | 예 | `An email from your example workflow` | 이메일 제목 |
   | **본문** | 예 | `Hello from your example workflow!` | 이메일 본문 내용 |
   ||||

   > [!NOTE]
   > **설정**, **정적 결과** 또는 **다음 시간 이후에 실행** 탭의 세부 정보 창에서 변경 작업을 원하는 경우 탭을 전환하거나 포커스를 디자이너로 이동하기 전에 **완료** 를 선택하여 변경 내용을 커밋해야 합니다. 그렇지 않으면 Visual Studio Code에서 변경 내용을 유지하지 않습니다. 자세한 내용은 [GitHub에서 Logic Apps 공개 미리 보기의 알려진 문제 페이지](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md)를 검토하세요.

1. 디자이너에서 **저장** 을 선택합니다.

> [!IMPORTANT]
> 로컬로 [기본 제공 HTTP 웹후크 트리거 또는 작업](../connectors/connectors-native-webhook.md)과 같이 웹후크 기반 트리거 및 작업을 사용하여 워크플로를 실행하려면 [웹후크 콜백 URL의 착신 전환 설정](#webhook-setup)을 통해 이 기능을 사용하도록 설정해야 합니다.

<a name="webhook-setup"></a>

## <a name="enable-locally-running-webhooks"></a>로컬로 실행하는 웹후크 사용

Azure에서 실행되는 논리 앱과 함께 **HTTP 웹후크** 와 같이 웹후크 기반 트리거 또는 작업을 사용하는 경우 Logic Apps 런타임은 해당 엔드포인트로 콜백 URL을 생성 및 등록하여 서비스 엔드포인트를 구독합니다. 그런 다음 트리거 또는 작업은 서비스 엔드포인트가 URL을 호출할 때까지 기다립니다. 그러나 Visual Studio Code에서 작업할 때 생성된 콜백 URL은 `http://localhost:7071/...`로 시작합니다. 이 URL은 프라이빗 localhost 서버용으로 서비스 엔드포인트가 이 URL을 호출할 수 없습니다.

Visual Studio Code에서 웹후크 기반 트리거 및 작업을 로컬로 실행하려면 localhost 서버를 노출하고 서비스 엔드포인트의 호출을 웹후크 콜백 URL로 안전하게 전달하는 공용 URL을 설정해야 합니다. [**ngrok**](https://ngrok.com/)과 같은 전달 서비스 및 도구를 사용하여 localhost로 HTTP 터널을 열거나 자체 도구를 사용할 수 있습니다.

#### <a name="set-up-call-forwarding-using-ngrok"></a>**ngrok** 을 사용하여 호출 전달 설정

1. [계정이 없는 경우 **ngrok** 계정](https://dashboard.ngrok.com/signup)에 가입합니다. 그렇지 않으면 [계정에 로그인](https://dashboard.ngrok.com/login)합니다.

1. **ngrok** 클라이언트가 연결 및 액세스를 인증하는 데 필요한 개인 인증 토큰을 가져옵니다.

   1. [인증 토큰 페이지](https://dashboard.ngrok.com/auth/your-authtoken)를 찾으려면 계정 대시보드 메뉴에서 **인증** 을 확장하고 **Authtoken** 을 선택합니다.

   1. **Authtoken** 상자에서 토큰을 안전한 위치에 복사합니다.

1. [**ngrok** 다운로드 페이지](https://ngrok.com/download) 또는 [계정 대시보드](https://dashboard.ngrok.com/get-started/setup)에서 원하는 **ngrok** 버전을 다운로드하고 .zip 파일을 추출합니다. 자세한 내용은 [1단계: 설치를 위해 압축 해제](https://ngrok.com/download)를 참조하세요.

1. 컴퓨터에서 명령 프롬프트 도구를 엽니다. **ngrok.exe** 파일이 있는 위치로 이동합니다.

1. 다음 명령을 실행하여 **ngrok** 클라이언트를 **ngrok** 계정에 연결합니다. 자세한 내용은 [2 단계: 계정 연결](https://ngrok.com/download)을 참조하세요.

   `ngrok authtoken <your_auth_token>`

1. 다음 명령을 실행하여 localhost 포트 7071에 대한 HTTP 터널을 엽니다. 자세한 내용은 [3 단계: 실행](https://ngrok.com/download)을 참조하세요.

   `ngrok http 7071`

1. 출력에서 다음 줄을 찾습니다:

   `http://<domain>.ngrok.io -> http://localhost:7071`

1. 다음 형식의 URL을 복사하여 저장합니다: `http://<domain>.ngrok.io`

#### <a name="set-up-the-forwarding-url-in-your-app-settings"></a>앱 설정에서 전달 URL 설정

1. Visual Studio Code의 디자이너에서 **HTTP + 웹후크** 트리거 또는 작업을 추가합니다.

1. 호스트 엔드포인트에 대한 프롬프트가 표시되면 이전에 만든 전달 URL(리디렉션)을 입력합니다.

   > [!NOTE]
   > 프롬프트를 무시하면 전달 URL을 입력해야 한다는 경고가 표시되므로 **구성** 을 선택하고 URL을 입력합니다. 이 단계를 완료하면 추가될 수 있는 후속 웹후크 트리거 또는 작업에 대해 프롬프트가 다시 표시되지 않습니다.
   >
   > 프롬프트를 다시 표시하려면 프로젝트 루트 수준에서 **local.settings.json** 파일의 바로 가기 메뉴를 열고 **웹후크 리디렉션 엔드포인트 구성** 을 선택합니다. 이제 전달 URL을 입력할 수 있는 프롬프트가 표시됩니다.

   Visual Studio Code는 프로젝트 루트 폴더에 있는 **local.settings.json** 파일에 전달 URL을 추가합니다. 이제 `Values`개체 속성`Workflows.WebhookRedirectHostUri`에 표시되고 전달 URL로 설정됩니다. 예를 들어 다음과 같습니다:
   
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

로컬 디버깅 세션을 처음 시작하거나 디버깅하지 않고 워크플로를 실행하면 Logic Apps 런타임이 워크플로를 서비스 엔드포인트에 등록하고 웹후크 작업을 알리기 위해 해당 엔드포인트를 구독합니다. 다음에 워크플로를 실행하면 구독 등록이 이미 로컬 스토리지에 있기 때문에 런타임이 등록되거나 재구독되지 않습니다.

로컬에서 실행되는 웹후크 기반 트리거 또는 작업을 사용하는 워크플로에 대한 디버깅 세션을 중지하면 기존 구독 등록이 삭제되지 않습니다. 등록을 취소하려면 구독 등록을 수동으로 제거하거나 삭제해야 합니다.

> [!NOTE]
> 워크플로가 실행되기 시작하면 터미널 창에 다음 예제와 같은 오류가 표시될 수 있습니다:
>
> `message='Http request failed with unhandled exception of type 'InvalidOperationException' and message: 'System.InvalidOperationException: Synchronous operations are disallowed. Call ReadAsync or set AllowSynchronousIO to true instead.`
>
> 이 경우 프로젝트 루트 폴더에서 **local.settings.json** 파일을 열고 속성이 `true`으로 설정되었는지 확인합니다:
>
> `"FUNCTIONS_V2_COMPATIBILITY_MODE": "true"`

<a name="manage-breakpoints"></a>

## <a name="manage-breakpoints-for-debugging"></a>디버깅을 위한 중단점 관리

디버깅 세션을 시작하여 논리 앱 워크플로를 실행하고 테스트하기 전 각 워크플로의 **workflow.json** 파일에 [중단점](https://code.visualstudio.com/docs/editor/debugging#_breakpoints)을 설정할 수 있습니다. 다른 설정은 필요하지 않습니다. 

현재 중단점은 트리거가 아닌 작업에 대해서만 지원됩니다. 각 작업 정의에는 다음과 같은 중단점 위치가 있습니다:

* 작업 이름을 표시하는 줄에 시작 중단점을 설정합니다. 디버깅 세션 중에 이 중단점이 적중하면 작업 입력을 평가하기 전에 검토할 수 있습니다.

* 작업의 닫는 중괄호 ( **}** )를 표시하는 줄에 종료 중단점을 설정합니다. 디버깅 세션 중에 이 중단점이 적중하면 작업 실행이 완료되기 전에 작업 결과를 검토할 수 있습니다.

중단점을 추가하려면 다음 단계를 수행하세요:

1. 디버그할 워크플로의 **workflow.json** 파일을 엽니다.

1. 중단점을 설정하려는 줄의 왼쪽 열에서 해당 열 내부를 선택합니다. 중단점을 제거하려면 해당 중단점을 선택합니다.

   디버깅 세션을 시작하면 실행 보기가 코드 창의 왼쪽에 표시되고 디버그 도구 모음은 위쪽 근처에 표시됩니다.

   > [!NOTE]
   > 실행 보기가 자동으로 표시되지 않으면 Ctrl+Shift+D를 누릅니다.

1. 중단점이 적중할 때 사용 가능한 정보를 검토하려면 실행 보기에서 **변수** 창을 검사합니다.

1. 워크플로 실행을 계속하려면 디버그 도구 모음에서 **계속**(재생 단추)을 선택합니다.

워크플로를 실행하는 동안 언제든지 중단점을 추가하고 제거할 수 있습니다. 그러나 실행 시작 후 **workflow.json** 파일을 업데이트하면 중단점이 자동으로 업데이트되지 않습니다. 중단점을 업데이트하려면 논리 앱을 다시 시작합니다.

일반 정보는 [중단점 - Visual Studio Code](https://code.visualstudio.com/docs/editor/debugging#_breakpoints)를 참조하세요.

<a name="run-test-debug-locally"></a>

## <a name="run-test-and-debug-locally"></a>로컬에서 실행, 테스트 및 디버그

논리 앱을 테스트하려면 다음 단계를 수행하여 디버깅 세션을 시작하고 요청 트리거에 의해 만들어진 엔드포인트의 URL을 찾습니다. 나중에 해당 엔드포인트에 요청을 보낼 수 있도록 이 URL이 필요합니다.

1. 상태 비저장 워크플로를 더 쉽게 디버그하기 위해 [해당 워크플로의 실행 기록을 사용하도록 설정](#enable-run-history-stateless)할 수 있습니다.

1. Visual Studio Code 작업 막대에서 **실행** 메뉴를 열고 **디버깅 시작**(F5)을 선택합니다.

   디버깅 세션을 검토할 수 있는 **터미널** 창이 열립니다.

   > [!NOTE]
   > **"preLaunchTask 'generateDebugSymbols' 실행 후 오류가 발생했습니다"** 오류가 발생하면 문제 해결 섹션의 [디버깅 세션을 시작하지 못했습니다](#debugging-fails-to-start)를 참조하세요.

1. 이제 요청 트리거에서 엔드포인트에 대한 콜백 URL을 찾습니다.

   1. 프로젝트를 볼 수 있도록 탐색기 창을 다시 엽니다.

   1. **workflow.json** 파일의 바로 가기 메뉴에서 **개요** 를 선택합니다.

      !["개요"가 선택된 workflow.json 파일의 탐색기 창과 바로 가기 창을 보여주는 스크린샷입니다.](./media/create-stateful-stateless-workflows-visual-studio-code/open-workflow-overview.png)

   1. 예제 요청 트리거에 대한 다음 URL과 유사한 **콜백 URL** 값을 찾습니다:

      `http://localhost:7071/api/<workflow-name>/triggers/manual/invoke?api-version=2020-05-01-preview&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=<shared-access-signature>`

      ![콜백 URL이 있는 워크플로의 개요 페이지를 보여주는 스크린샷](./media/create-stateful-stateless-workflows-visual-studio-code/find-callback-url.png)

1. 논리 앱 워크플로를 트리거하여 콜백 URL을 테스트하려면 요청을 만들고 전송하기 위한 [Postman](https://www.postman.com/downloads/) 또는 선호하는 도구를 엽니다.

   이 예제에서는 Postman을 사용합니다. 자세한 내용은 [Postman 시작](https://learning.postman.com/docs/getting-started/introduction/)을 참조하세요.

   1. Postman 도구 모음에서 **새로 만들기** 를 선택합니다.

      ![Postman에서 새로 만들기 단추를 선택한 것을 보여주는 스크린샷](./media/create-stateful-stateless-workflows-visual-studio-code/postman-create-request.png)

   1. **새로 만들기** 창의 **구성 요소** 에서 **요청** 을 선택합니다.

   1. **요청 저장** 창의 **요청 이름** 에서 요청의 이름을 입력합니다(예: `Test workflow trigger`).

   1. **저장할 컬렉션 또는 폴더 선택** 에서 **컬렉션 만들기** 를 선택합니다.

   1. **모든 컬렉션** 에서 요청을 구성하기 위해 만들 컬렉션의 이름을 입력하고 Enter 키를 누른 다음, **<*컬렉션 이름*>에 저장** 을 선택합니다. 이 예제에서는 컬렉션 이름으로 `Logic Apps requests`을(를) 사용합니다.

      요청 트리거의 콜백 URL에 요청을 보낼 수 있도록 Postman의 요청 창이 열립니다.

      ![요청 창이 열린 Postman을 보여주는 스크린샷](./media/create-stateful-stateless-workflows-visual-studio-code/postman-request-pane.png)

   1. Visual Studio Code로 돌아갑니다. 워크플로 개요 페이지에서 **콜백 URL** 속성 값을 복사합니다.

   1. Postman으로 돌아갑니다. 메서드 목록 옆의 요청 창을 보면 현재 기본 요청 메서드가 **가져오기** 로 표시됩니다. 이전에 복사한 콜백 URL을 주소 상자에 붙여 넣고 **보내기** 를 선택합니다.

      ![Postman 및 주소 상자 콜백 URL에서 [보내기] 단추를 선택한 것을 보여주는 스크린샷](./media/create-stateful-stateless-workflows-visual-studio-code/postman-test-call-back-url.png)

      예제 논리 앱 워크플로는 이 예제와 비슷한 이메일을 보냅니다:

      ![예제에서 설명한 대로 Outlook 이메일을 보여주는 스크린샷](./media/create-stateful-stateless-workflows-visual-studio-code/workflow-app-result-email.png)

1. Visual Studio Code에서 워크플로의 개요 페이지로 돌아갑니다.

   상태 저장 워크플로를 만든 경우 보낸 요청이 워크플로를 트리거한 후 개요 페이지에 워크플로의 실행 상태 및 기록이 표시됩니다.

   > [!TIP]
   > 실행 상태가 표시되지 않으면 **새로 고침** 을 선택하여 개요 페이지를 새로 고칩니다. 조건이 충족되지 않거나 데이터를 찾을 수 없어 트리거를 건너뛴 경우에는 실행이 발생하지 않습니다.

   ![실행 상태 및 기록이 포함된 워크플로의 개요 페이지를 보여주는 스크린샷](./media/create-stateful-stateless-workflows-visual-studio-code/post-trigger-call.png)

   | 실행 상태 | 설명 |
   |------------|-------------|
   | **중단됨** | 시스템이 중단되거나 Azure 구독이 만료되는 등의 외부 문제로 인해 실행이 중지되었거나 완료되지 않았습니다. |
   | **취소** | 실행이 트리거되고 시작되었지만 취소 요청이 수신되었습니다. |
   | **실패** | 실행에서 하나 이상의 작업이 실패했습니다. 실패를 처리하는 워크플로의 후속 작업이 설정되지 않았습니다. |
   | **실행 중** | 실행이 트리거되어 현재 진행 중이지만, [작업 제한](logic-apps-limits-and-config.md) 또는 [현재 가격 책정 계획](https://azure.microsoft.com/pricing/details/logic-apps/)으로 인해 제한된 실행에도 이 상태가 표시될 수 있습니다. <p><p>**팁**: [진단 로깅](monitor-logic-apps-log-analytics.md)을 설정한 경우 발생하는 모든 제한된 이벤트에 대한 정보를 얻을 수 있습니다. |
   | **성공함** | 실행이 성공했습니다. 작업이 실패하면 워크플로의 후속 작업에서 실패를 처리합니다. |
   | **시간 초과됨** | 현재 기간이 [**실행 기록 보존 기간(일)** 설정](logic-apps-limits-and-config.md#run-duration-retention-limits)을 통해 제어되는 실행 기간 제한을 초과하여 실행 시간이 초과되었습니다. 실행의 기간은 실행의 시작 시간과 해당 시작 시간의 실행 기간 제한을 사용하여 계산됩니다. <p><p>**참고**: 실행 기간이 [**실행 기록 보존 기간(일)** 설정](logic-apps-limits-and-config.md#run-duration-retention-limits)을 통해 제어되는 현재 *실행 기록 보존 제한* 을 초과하는 경우에도 일간 정리 작업을 통해 실행 기록에서 실행이 지워집니다. 실행 시간이 초과되든 아니면 실행이 완료되든, 보존 기간은 항상 실행의 시작 시간과 *현재* 보존 제한을 사용하여 계산됩니다. 따라서 진행 중인 실행의 기간 제한을 줄이면 실행 시간이 초과됩니다. 그러나 실행이 유지되는지 아니면 기록에서 실행이 지워지는지 여부는 실행 기간이 보존 한도를 초과했는지 여부에 따라 결정됩니다. |
   | **대기 중** | 예를 들어 아직 실행 중인 이전 워크플로 인스턴스 때문에 실행이 시작되지 않았거나 일시 중지되었습니다. |
   |||

1. 특정 실행의 각 단계에 대한 상태와 단계의 입력 및 출력을 검토하려면 해당 실행에 대한 줄임표( **...** ) 단추를 선택하고 **실행 표시** 를 선택합니다.

   ![줄임표 단추와 "실행 표시"가 선택된 워크플로의 실행 기록 행을 보여주는 스크린샷](./media/create-stateful-stateless-workflows-visual-studio-code/show-run-history.png)

   Visual Studio Code는 모니터링 보기를 열고 실행의 각 단계에 대한 상태를 표시합니다.

   ![워크플로 실행의 각 단계와 상태를 보여주는 스크린샷](./media/create-stateful-stateless-workflows-visual-studio-code/run-history-action-status.png)

   > [!NOTE]
   > 실행이 실패하고 모니터링 보기 단계에 `400 Bad Request` 오류가 표시되는 경우 이 문제는 긴 트리거 이름 또는 작업 이름으로 인해 기본 URI(Uniform Resource Identifier)가 기본 문자 제한을 초과하여 발생할 수 있습니다. 자세한 내용은 ["400 잘못된 요청"](#400-bad-request)을 참조하세요.

   워크플로의 각 단계에서 가능한 상태는 다음과 같습니다:

   | 작업 상태 | 아이콘 | 설명 |
   |---------------|------|-------------|
   | **중단됨** | !["중단됨" 작업 상태 아이콘][aborted-icon] | 시스템이 중단되거나 Azure 구독이 만료되는 등의 외부 문제로 인해 작업이 중지되었거나 완료되지 않았습니다. |
   | **취소** | !["취소됨" 작업 상태 아이콘][cancelled-icon] | 작업이 실행 중이지만 취소 요청이 수신되었습니다. |
   | **실패** | !["실패" 작업 상태 아이콘][failed-icon] | 작업이 실패했습니다. |
   | **실행 중** | !["실행 중" 작업 상태 아이콘][running-icon] | 작업이 현재 실행 중입니다. |
   | **생략** | !["건너뜀" 작업 상태 아이콘][skipped-icon] | 바로 이전 작업이 실패하여 작업을 건너뛰었습니다. 작업에는 이전 작업이 성공적으로 완료되어야 현재 작업을 실행할 수 있는 `runAfter` 조건이 있습니다. |
   | **성공함** | !["성공함" 작업 상태 아이콘][succeeded-icon] | 작업이 성공했습니다. |
   | **다시 시도하여 성공함** | !["다시 시도하여 성공함" 작업 상태 아이콘][succeeded-with-retries-icon] | 한 번 이상 다시 시도한 후 작업이 성공했습니다. 다시 시도 기록을 검토하려면 실행 기록 세부 정보 보기에서 해당 작업을 선택합니다. 그러면 입력 및 출력을 볼 수 있습니다. |
   | **시간 초과됨** | !["시간 초과됨" 작업 상태 아이콘][timed-out-icon] | 해당 작업의 설정에 지정된 제한 시간으로 인해 작업이 중지되었습니다. |
   | **대기 중** | !["대기 중" 작업 상태 아이콘][waiting-icon] | 호출자의 인바운드 요청을 기다리는 웹후크 작업에 적용됩니다. |
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

1. 각 단계에 대한 입력 및 출력을 검토하려면 검사하려는 단계를 선택합니다.

   ![워크플로의 각 단계에 대한 상태와 확장된 "이메일 보내기" 작업의 입력 및 출력을 보여주는 스크린샷](./media/create-stateful-stateless-workflows-visual-studio-code/run-history-details.png)

1. 해당 단계에 대한 원시 입력 및 출력을 추가로 검토하려면 **원시 입력 표시** 또는 **원시 출력 표시** 를 선택합니다.

1. 디버깅 세션을 중지하려면 **실행** 메뉴에서 **디버깅 중지**(Shift + F5)를 선택합니다.

<a name="return-response"></a>

## <a name="return-a-response"></a>응답 반환

논리 앱에 요청을 보낸 호출자에게 응답을 반환하려면 요청 트리거로 시작하는 워크플로에 대해 기본 제공 [응답 작업](../connectors/connectors-native-reqres.md)을 사용할 수 있습니다.

1. 워크플로 디자이너의 **이메일 보내기** 작업에서 **새 단계** 를 선택합니다.

   **작업 선택** 프롬프트가 디자이너에 표시되고, 다음 작업을 선택할 수 있도록 **작업 추가 창** 이 다시 열립니다.

1. **작업 추가** 창의 **작업 선택** 검색 상자에서 **기본 제공** 이 선택되어 있는지 확인합니다. 검색 상자에 `response`을 입력하고 **응답** 작업을 선택합니다.

   ![응답 작업이 선택된 워크플로 디자이너를 보여주는 스크린샷입니다.](./media/create-stateful-stateless-workflows-visual-studio-code/add-response-action.png)

   디자이너에 **응답** 작업이 표시되면 작업의 세부 정보 창이 자동으로 열립니다.

   !["응답" 작업의 세부 정보 창이 열려 있고 "본문" 속성이 "이메일 보내기" 작업의 "본문" 속성 값으로 설정된 워크플로 디자이너를 보여주는 스크린샷입니다.](./media/create-stateful-stateless-workflows-visual-studio-code/response-action-details.png)

1. **매개 변수** 탭에서 호출하려는 함수에 필요한 정보를 입력합니다.

   이 예제에서는 **이메일 보내기** 작업에서 출력되는 **본문** 속성 값을 반환합니다.

   1. **본문** 속성 상자 내부를 클릭하면 동적 콘텐츠 목록이 표시되고 이전 트리거 및 작업에서 사용 가능한 출력 값이 표시됩니다.

      ![동적 콘텐츠 목록이 표시되도록 "본문" 속성의 마우스 포인터가 있는 "응답" 작업의 세부 정보 창을 보여주는 스크린샷입니다.](./media/create-stateful-stateless-workflows-visual-studio-code/open-dynamic-content-list.png)

   1. 동적 콘텐츠 목록의 **이메일 보내기** 에서 **본문** 을 선택합니다.

      ![열려 있는 동적 콘텐츠 목록을 보여주는 스크린샷입니다. 목록에서 "이메일 보내기" 헤더에 "본문" 출력 값이 선택됩니다.](./media/create-stateful-stateless-workflows-visual-studio-code/select-send-email-action-body-output-value.png)

      완료되면 응답 작업의 **본문** 속성이 이제 **이메일 보내기** 작업의 **본문** 출력 값으로 설정됩니다.

      ![워크플로의 각 단계에 대한 상태와 확장된 "응답" 작업의 입력 및 출력을 보여주는 스크린샷입니다.](./media/create-stateful-stateless-workflows-visual-studio-code/response-action-details-body-property.png)

1. 디자이너에서 **저장** 을 선택합니다.

<a name="retest-workflow"></a>

## <a name="retest-your-logic-app"></a>논리 앱 다시 테스트하기

논리 앱을 업데이트한 후 Visual Studio의 디버거를 재실행하고 [로컬에서 실행, 테스트 및 디버그 단계](#run-test-debug-locally)와 유사하게 업데이트된 논리 앱을 트리거하는 다른 요청을 보내 다른 테스트를 실행할 수 있습니다.

1. Visual Studio Code 작업 막대에서 **실행** 메뉴를 열고 **디버깅 시작**(F5)을 선택합니다.

1. Postman 또는 요청 만들기 및 보내기 도구에서 워크플로를 트리거하기 위한 다른 요청을 보냅니다.

1. 상태 저장 워크플로를 만든 경우 워크플로의 개요 페이지에서 가장 최근 실행에 대한 상태를 확인합니다. 해당 실행의 각 단계에 대한 상태, 입력 및 출력을 보려면 해당 실행에 대한 줄임표 ( **...** ) 단추를 선택하고 **실행 표시** 를 선택합니다.

   예를 들어 예제 워크플로가 응답 작업으로 업데이트된 후 실행에 대한 단계별 상태는 다음과 같습니다.

   ![업데이트된 워크플로의 각 단계에 대한 상태와 확장된 "응답" 작업의 입력 및 출력을 보여주는 스크린샷입니다.](./media/create-stateful-stateless-workflows-visual-studio-code/run-history-details-rerun.png)

1. 디버깅 세션을 중지하려면 **실행** 메뉴에서 **디버깅 중지**(Shift + F5)를 선택합니다.

<a name="firewall-setup"></a>

##  <a name="find-domain-names-for-firewall-access"></a>방화벽 액세스를 위한 도메인 이름 찾기

Azure Portal에서 논리 앱 워크플로를 배포 및 실행하기 전에 환경에 트래픽을 제한하는 엄격한 네트워크 요구 사항이나 방화벽이 있는 경우 워크플로에 있는 모든 트리거 또는 작업 연결에 대한 권한을 설정해야 합니다.

해당 연결에 대한 FQDNs(정규화된 도메인 이름)을 찾으려면 다음 단계를 수행합니다:

1. 논리 앱 프로젝트에서 워크플로에 첫 번째 연결 기반 트리거 또는 작업을 추가한 후에 생성되는 **connections.json** 파일을 열고 `managedApiConnections` 개체를 찾습니다.

1. 생성한 각 연결에 대해 이 정보로 방화벽을 설정할 수 있도록 안전한 위치에 `connectionRuntimeUrl` 속성 값을 찾아 복사하고 저장합니다.

   이 예제 **connections.json** 파일에는 AS2 연결과 다음 `connectionRuntimeUrl` 값을 사용하는 Office 365 연결이 포함되어 있습니다:

   * AS2: `"connectionRuntimeUrl": https://9d51d1ffc9f77572.00.common.logic-{Azure-region}.azure-apihub.net/apim/as2/11d3fec26c87435a80737460c85f42ba`

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

Visual Studio Code에서 새 **Logic App(미리 보기)** 리소스 유형을 사용하여 논리 앱을 배포하는 Azure에 프로젝트를 직접 게시할 수 있습니다. Azure Functions의 함수 앱 리소스와 마찬가지로 이 새 리소스 유형을 배포하려면 배포 중에 설정할 수 있는 [호스팅 요금제 및 가격 책정 계층 ](../app-service/overview-hosting-plans.md)을 선택해야 합니다. 호스팅 요금제 및 가격 책정에 대한 자세한 내용은 다음 항목을 검토합니다:

* [Azure App Service에서 강화](../app-service/manage-scale-up.md)
* [Azure Functions 비율 크기 조정 및 호스팅](../azure-functions/functions-scale.md)

논리 앱을 새 리소스로 게시할 수 있습니다. 그러면 [함수 앱 요구 사항과 유사한 Azure 스토리지 계정](../azure-functions/storage-considerations.md)과 같이 필요한 리소스를 자동으로 만듭니다. 또는 이전에 배포된 **Logic App(미리 보기)** 리소스에 논리 앱을 게시하여 해당 논리 앱을 덮어쓸 수 있습니다.

### <a name="publish-to-a-new-logic-app-preview-resource"></a>새 Logic App(미리 보기) 리소스에 게시

1. Visual Studio Code 작업 막대에서 Azure 아이콘을 선택합니다.

1. **Azure: Logic Apps(미리 보기)** 창 도구 모음에서 **논리 앱에 배포** 를 선택합니다.

   !["Azure: Logic Apps(미리 보기)" 창과 "논리 앱에 배포"가 선택된 창의 도구 모음을 보여주는 스크린샷입니다.](./media/create-stateful-stateless-workflows-visual-studio-code/deploy-to-logic-app.png)

1. 프롬프트가 표시되면 논리 앱 배포에 사용할 Azure 구독을 선택합니다.

1. Visual Studio Code가 열리는 목록에서 다음 옵션 중에서 선택합니다:

   * **Azure에서 Logic App(미리 보기) 만들기** (빠른 버전)
   * **Azure Advanced에서 새 Logic App(미리 보기) 만들기**
   * 이전에 배포된 **Logic App(미리 보기)** 리소스(있는 경우)

   이 예제에서는 **Azure Advanced에서 새 Logic App(미리 보기) 만들기** 를 계속 진행합니다.

   !["Azure에서 새 Logic App(미리 보기)"가 선택된 목록이 포함된 "Azure: Logic Apps(미리 보기)" 창을 보여주는 스크린샷입니다.](./media/create-stateful-stateless-workflows-visual-studio-code/select-create-logic-app-options.png)

1. 새 **Logic App(미리 보기)** 리소스를 만들려면 다음 단계를 수행합니다:

   1. **Logic App(미리 보기)** 리소스에 사용할 전역적으로 고유한 이름을 새 논리 앱에 입력합니다. 이 예에서는 `Fabrikam-Workflows-App`를 사용합니다.

      !["Azure: Logic Apps(미리 보기)" 창과 새로 만들 논리 앱의 이름을 입력하라는 프롬프트를 보여주는 스크린샷입니다.](./media/create-stateful-stateless-workflows-visual-studio-code/enter-logic-app-name.png)

   1. [**App Service 요금제** (전용)](../azure-functions/dedicated-plan.md) 또는 [**프리미엄**](../azure-functions/functions-premium-plan.md) 중에서 새 논리 앱에 대한 [호스팅 요금제](../app-service/overview-hosting-plans.md)를 선택합니다.

      > [!IMPORTANT]
      > 사용량 요금제는 이 리소스 유형에는 지원되지 않아 사용할 수 없습니다. 선택한 요금제는 나중에 사용할 수 있는 기능 및 가격 책정 계층에 영향을 줍니다. 자세한 내용은 다음 항목을 검토하세요: 
      >
      > * [Azure Functions 비율 크기 조정 및 호스팅](../azure-functions/functions-scale.md)
      > * [App Service 가격 세부 정보](https://azure.microsoft.com/pricing/details/app-service/)
      >
      > 예를 들어 프리미엄 요금제는 Azure Functions에서 논리 앱을 만들고 배포할 때와 마찬가지로 Azure 가상 네트워크와 비공개로 연결하고 통합하는 기능과 같은 네트워킹 기능에 대한 액세스를 제공합니다. 
      > 자세한 내용은 다음 항목을 검토하세요:
      > 
      > * [Azure Functions 네트워킹 옵션](../azure-functions/functions-networking-options.md)
      > * [어디서나 실행되는 Azure Logic Apps - Azure Logic Apps 미리 보기의 네트워킹 기능](https://techcommunity.microsoft.com/t5/integrations-on-azure/logic-apps-anywhere-networking-possibilities-with-logic-app/ba-p/2105047)

      이 예제에서는 **App Service 요금제** 를 사용합니다.

      !["Azure: Logic Apps(미리 보기)" 창과 "App Service 요금제" 또는 "프리미엄"을 선택하라는 프롬프트를 보여주는 스크린샷입니다.](./media/create-stateful-stateless-workflows-visual-studio-code/select-hosting-plan.png)

   1. 기존 App Service 요금제를 선택하거나 새로 만듭니다. 이 예제에서는 **새 App Service 요금제 만들기** 를 선택합니다.

      !["Azure: Logic Apps(미리 보기)" 창과 "새 App Service 요금제 만들기" 또는 기존 App Service 요금제를 선택하라는 프롬프트를 보여주는 스크린샷입니다.](./media/create-stateful-stateless-workflows-visual-studio-code/create-app-service-plan.png)

   1. App Service 요금제 이름을 입력한 다음 요금제에 대한 [가격 책정 계층](../app-service/overview-hosting-plans.md)을 선택합니다. 이 예제에서는 **F1 무료** 요금제를 선택합니다.

      !["Azure: Logic Apps(미리 보기)" 창과 가격 책정 계층을 선택하라는 프롬프트를 보여주는 스크린샷입니다.](./media/create-stateful-stateless-workflows-visual-studio-code/select-pricing-tier.png)

   1. 최적의 성능을 위해 배포할 프로젝트와 동일한 리소스 그룹을 찾아 선택합니다.

      > [!NOTE]
      > 다른 리소스 그룹을 만들거나 사용할 수 있지만 그러면 성능에 영향을 줄 수 있습니다. 다른 리소스 그룹을 만들거나 선택하고 확인 프롬프트가 표시된 후 취소하면 배포도 취소됩니다.

   1. 상태 저장 워크플로의 경우 **새 스토리지 계정 만들기** 또는 기존 스토리지 계정을 선택합니다.

      !["Azure: Logic Apps(미리 보기)" 창과 새 스토리지 계정을 만들거나 기존 스토리지 계정을 선택하라는 프롬프트를 보여주는 스크린샷입니다.](./media/create-stateful-stateless-workflows-visual-studio-code/create-storage-account.png)

   1. 논리 앱의 만들기 및 배포 설정에서 [Application Insights](../azure-monitor/app/app-insights-overview.md) 사용을 지원하는 경우 필요에 따라 논리 앱에 진단 로깅 및 추적을 사용하도록 설정할 수 있습니다. Visual Studio Code에서 논리 앱을 배포할 때 또는 배포 후에 이러한 작업을 수행할 수 있습니다. Application Insights 인스턴스가 필요하지만, 논리 앱을 배포할 때 [미리](../azure-monitor/app/create-workspace-resource.md) 또는 배포 후에 이 리소스를 만들 수 있습니다.

      지금 로깅 및 추적을 사용하도록 설정하려면 다음 단계를 수행합니다:

      1. 기존 Application Insights 리소스를 선택하거나 **새 Application Insights 리소스를 만듭니다**.

      1. [Azure Portal](https://portal.azure.com)에서, Application Insights 리소스로 이동합니다.

      1. 리소스 메뉴에서 **개요** 를 선택합니다. **계측 키** 값을 찾아 복사합니다.

      1. Visual Studio Code의 프로젝트 루트 폴더에서 **local.settings.json** 파일을 엽니다.

      1. `Values`개체에서`APPINSIGHTS_INSTRUMENTATIONKEY` 속성을 추가하고 계측 키에 값을 설정합니다. 예를 들면 다음과 같습니다:

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
         > 트리거 및 동작 이름이 Application Insights 인스턴스에 올바르게 표시되는지 확인할 수 있습니다.
         >
         > 1. Azure Portal에서, Application Insights 리소스로 이동합니다.
         >
         > 2. 리소스 메뉴의 **조사** 에서 **애플리케이션 맵** 을 선택합니다.
         >
         > 3. 맵에 표시되는 작업 이름을 검토합니다.
         >
         > 기본 제공 트리거의 인바운드 요청 일부는 애플리케이션 맵에서 중복으로 나타날 수 있습니다. 
         > 이러한 중복 항목은 `WorkflowName.ActionName` 형식을 사용하는 대신 워크플로 이름을 작업 이름으로 사용하며 Azure Functions 호스트에서 시작됩니다.

      1. 다음으로 논리 앱이 수집하고 Application Insights 인스턴스로 보내는 추적 데이터의 심각도 수준을 선택적으로 조정할 수 있습니다.

         워크플로가 트리거되고 작업이 실행될 때 같이 워크플로 관련 이벤트가 발생할 때마다 런타임은 다양한 추적을 내보냅니다. 해당 추적은 워크플로의 수명을 다루고 다음 이벤트 유형을 포함하지만 이에 국한되지는 않습니다:

         * 시작, 중지 및 오류와 같은 서비스 활동입니다.
         * 작업 및 디스패처 활동입니다.
         * 트리거, 동작 및 실행 등의 워크플로 활동입니다.
         * 성공 또는 실패와 같은 스토리지 요청 활동입니다.
         * 인바운드, 아웃 바운드, 성공 및 실패 등의 HTTP 요청 작업입니다.
         * 디버그 메시지와 같은 모든 개발 추적입니다.

         각 이벤트 유형은 심각도 수준에 할당됩니다. 예를 들어 `Trace` 수준은 가장 자세한 메시지를 캡처하는 반면 `Information` 수준은 논리 앱, 워크플로, 트리거 및 작업이 시작 및 중지되는 시기와 같은 워크플로의 일반 활동을 캡처합니다. 다음 표에서는 심각도 수준 및 해당 추적 유형을 설명합니다:

         | 심각도 수준 | 추적 형식 |
         |----------------|------------|
         | 위험 | 논리 앱에서 복구할 수 없는 오류를 설명하는 로그입니다. |
         | 디버그 | 개발 중에 조사용으로 사용할 수 있는 로그입니다(예: 인바운드 및 아웃바운드 HTTP 호출). |
         | Error | 워크플로 실행 실패를 나타내는 로그이지만, 논리 앱의 일반적인 실패는 아닌 로그입니다. |
         | 정보 | 논리 앱 또는 워크플로의 일반 활동을 추적하는 로그입니다. 예를 들면 다음과 같습니다: <p><p>- 트리거, 동작 또는 실행이 시작되고 끝나는 경우. <br>- 논리 앱이 시작되거나 종료되는 경우. |
         | 추적 | 스토리지 요청 또는 디스패처 활동과 같이 가장 자세한 메시지와 워크플로 실행 활동과 관련된 모든 메시지가 포함된 로그입니다. |
         | 경고 | 논리 앱에서 비정상적인 상태를 강조하지만 실행을 방해하지는 않는 로그입니다. |
         |||

         심각도 수준을 설정하려면 프로젝트 루트 수준에서 **host.json** 파일을 열고 `logging` 개체를 찾습니다. 이 개체는 논리 앱의 모든 워크플로에 대한 로그 필터링을 제어하고 [로그 유형 필터링을 위한 ASP.NET Core 레이아웃](/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1&preserve-view=true#log-filtering)을 수행합니다.

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

         `logging` 개체에 `Host.Triggers.Workflow` 속성을 포함하는 `logLevel` 개체가 포함되지 않은 경우 해당 항목을 추가합니다. 원하는 추적 유형의 심각도 수준으로 속성을 설정합니다. 예를 들면 다음과 같습니다:

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

   배포 단계를 완료하면 Visual Studio Code가 논리 앱을 게시하는 데 필요한 리소스를 만들고 배포를 시작합니다.

1. 배포 프로세스를 검토하고 모니터링하려면 **보기** 메뉴에서 **출력** 을 선택합니다. 출력 창 도구 모음 목록에서 **Azure Logic Apps** 을 선택합니다.

   ![배포 진행률 및 상태와 함께 도구 모음 목록에서 "Azure Logic Apps"가 선택된 출력 창을 보여주는 스크린샷입니다.](./media/create-stateful-stateless-workflows-visual-studio-code/logic-app-deployment-output-window.png)

   Visual Studio Code에서 Azure에 논리 앱을 배포하는 작업이 완료되면 다음과 같은 메시지가 표시됩니다:

   ![Azure에 배포가 성공적으로 완료되었다는 메시지를 보여주는 스크린샷입니다.](./media/create-stateful-stateless-workflows-visual-studio-code/deployment-to-azure-completed.png)

   축하합니다, 이제 Azure에 논리 앱이 배치되었으며 기본적으로 사용하도록 설정되었습니다.

다음으로 이러한 작업을 수행하는 방법을 배울 수 있습니다:

* [프로젝트에 빈 워크플로를 추가합니다](#add-workflow-existing-project).

* [Visual Studio Code](#manage-deployed-apps-vs-code) 또는 [Azure Portal](#manage-deployed-apps-portal)을 사용하여 배포된 논리 앱을 관리합니다.

* [상태 비저장 워크플로에서 실행 기록을 사용하도록 설정합니다](#enable-run-history-stateless).

* [배포된 논리 앱에 대한 Azure Portal 모니터링 보기를 사용하도록 설정합니다](#enable-monitoring).

<a name="add-workflow-existing-project"></a>

## <a name="add-blank-workflow-to-project"></a>프로젝트에 빈 워크플로 추가

논리 앱 프로젝트에 여러 워크플로를 포함할 수 있습니다. 프로젝트에 빈 워크플로를 추가하려면 다음 단계를 수행합니다:

1. Visual Studio Code 작업 막대에서 Azure 아이콘을 선택합니다.

1. Azure 창에서 **Azure: Logic Apps(미리 보기)** 옆에 있는 **워크플로 만들기**(Azure Logic Apps 아이콘)를 선택합니다.

1. 추가하려는 워크플로 유형 선택: **상태 저장** 또는 **상태 비저장**

1. 워크플로 이름을 입력합니다.

완료되면 워크플로 정의를 위한 **workflow.json** 파일과 함께 새 워크플로 폴더가 프로젝트에 표시됩니다.

<a name="manage-deployed-apps-vs-code"></a>

## <a name="manage-deployed-logic-apps-in-visual-studio-code"></a>Visual Studio Code에서 배포된 논리 앱 관리

Visual Studio Code에서는 원래 **Logic Apps** 인지 아니면 **Logic App(미리 보기)** 리소스 유형인지에 관계없이 Azure 구독에서 배포된 모든 논리 앱을 볼 수 있으며 해당 논리 앱을 관리하는 데 도움이 되는 작업을 선택할 수 있습니다. 그러나 두 리소스 유형 모두에 액세스하려면 Visual Studio Code용 **Azure Logic Apps** 및 **Azure Logic Apps(미리 보기)** 확장이 모두 필요합니다.

1. 왼쪽 도구 모음에서 Azure 아이콘을 선택합니다. **Azure: Logic Apps(미리 보기)** 창에서 구독을 확장하면 해당 구독에 대해 배포된 모든 논리 앱이 표시됩니다.

1. 관리하려는 논리 앱을 엽니다. 논리 앱의 바로 가기 메뉴에서 수행하려는 작업을 선택합니다.

   예를 들어 배포된 논리 앱 중지, 시작, 재시작 또는 삭제와 같은 작업을 선택할 수 있습니다.

   ![열린 "Azure Logic Apps(미리 보기)" 확장 창 및 배포된 워크플로가 있는 Visual Studio Code를 보여주는 스크린샷입니다.](./media/create-stateful-stateless-workflows-visual-studio-code/find-deployed-workflow-visual-studio-code.png)

1. 논리 앱의 모든 워크플로를 보려면 논리 앱을 확장한 다음 **워크플로** 노드를 확장합니다.

1. 특정 워크플로를 보려면 워크플로의 바로 가기 메뉴를 열고 **디자이너에서 열기** 를 선택하여 읽기 전용 모드로 엽니다.

   다음 옵션을 사용하여 워크플로를 편집할 수 있습니다:

   * Visual Studio Code의 워크플로 디자이너에서 프로젝트의 **workflow.json** 파일을 열고, 편집한 다음 논리 앱을 Azure에 다시 배포합니다.

   * Azure Portal에서 [논리 앱을 찾아서 엽니다](#manage-deployed-apps-portal). 워크플로를 찾고, 편집하고, 저장합니다.

1. Azure Portal에서 배포된 논리 앱을 열려면 논리 앱의 바로 가기 메뉴를 열고 **Portal에서 열기** 를 선택합니다.

   Azure Portal이 브라우저에서 열리고 Visual Studio Code에 로그인한 경우 포털에 자동으로 로그인되며 논리 앱이 표시됩니다.

   ![Visual Studio Code에서 논리 앱에 대한 Azure Portal 페이지를 보여주는 스크린샷입니다.](./media/create-stateful-stateless-workflows-visual-studio-code/deployed-workflow-azure-portal.png)

   또한 Azure Portal에 별도로 로그인하고 포털 검색 상자를 사용하여 논리 앱을 찾은 다음 결과 목록에서 논리 앱을 선택할 수도 있습니다.

   ![Azure Portal 및 선택된 것으로 표시되는 배포된 논리 앱에 대한 검색 결과가 포함된 검색 창을 보여주는 스크린샷입니다.](./media/create-stateful-stateless-workflows-visual-studio-code/find-deployed-workflow-azure-portal.png)

<a name="manage-deployed-apps-portal"></a>

## <a name="manage-deployed-logic-apps-in-the-portal"></a>포털에서 배포된 논리 앱 관리

Azure Portal에서 원래 **Logic Apps** 리소스 유형이든 **Logic App(미리 보기)** 리소스 유형이든 관계없이 Azure 구독에 있는 배포된 모든 논리 앱을 볼 수 있습니다. 현재 각 리소스 유형은 Azure에서 별도의 범주로 구성되고 관리됩니다. **Logic App(미리 보기)** 리소스 유형을 포함하는 논리 앱을 찾으려면 다음 단계를 수행합니다:

1. Azure Portal의 검색창에 `logic app preview`을 입력합니다. 결과 목록이 표시되면 **서비스** 에서 **Logic App(미리 보기)** 을 선택합니다.

   !["논리 앱 미리보기" 검색 텍스트가 있는 Azure Portal 검색 상자를 보여주는 스크린샷입니다.](./media/create-stateful-stateless-workflows-visual-studio-code/portal-find-logic-app-preview-resource.png)

1. **Logic App(미리 보기)** 창에서 Visual Studio Code에서 배포한 논리 앱을 찾아 선택합니다.

   ![Azure에 배포된 Azure portal 및 Logic App(미리 보기) 리소스를 보여주는 스크린샷입니다.](./media/create-stateful-stateless-workflows-visual-studio-code/logic-app-preview-resources-pane.png)

   Azure Portal에서 선택한 논리 앱에 대한 개별 리소스 페이지를 엽니다.

   ![Azure Portal에서 논리 앱 워크플로의 리소스 페이지를 보여주는 스크린샷입니다.](./media/create-stateful-stateless-workflows-visual-studio-code/deployed-workflow-azure-portal.png)

1. 이 논리 앱에서 워크플로를 보려면 논리 앱의 메뉴에서 **워크플로** 를 선택합니다.

   **워크플로** 창에는 현재 논리 앱의 모든 워크플로가 표시됩니다. 이 예제에서는 Visual Studio Code에서 만든 워크플로를 보여줍니다.

   !["워크플로"창이 열려 있고 배포된 워크플로가 있는 "Logic App(미리 보기)" 리소스 페이지를 보여주는 스크린 샷](./media/create-stateful-stateless-workflows-visual-studio-code/deployed-logic-app-workflows-pane.png)

1. 워크플로를 보려면 **워크플로** 창에서 해당 워크플로를 선택합니다.

   워크플로 창이 열리고 해당 워크플로에서 수행할 수 있는 추가 정보 및 작업이 표시됩니다.

   예를 들어 워크플로의 단계를 보려면 **디자이너** 를 선택합니다.

   ![선택한 워크플로 "개요" 창과 워크플로 메뉴에 선택한 "디자이너" 명령을 보여주는 스크린샷입니다.](./media/create-stateful-stateless-workflows-visual-studio-code/workflow-overview-pane-select-designer.png)

   워크플로 디자이너가 열리고 Visual Studio Code에서 빌드된 워크플로가 표시됩니다. 이제 Azure Portal에서 이 워크플로를 변경할 수 있습니다.

   ![Visual Studio Code에서 배포된 워크플로 디자이너 및 워크플로를 보여주는 스크린샷입니다.](./media/create-stateful-stateless-workflows-visual-studio-code/opened-workflow-designer.png)

<a name="add-workflow-portal"></a>

## <a name="add-another-workflow-in-the-portal"></a>포털에서 다른 워크플로 추가

Azure Portal을 통해 Visual Studio Code에서 배포한 **Logic App(미리 보기)** 리소스에 빈 워크플로를 추가하고 Azure Portal에서 해당 워크플로를 빌드할 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에서 배포된 **Logic App(미리 보기)** 리소스를 찾아 선택합니다.

1. 논리 앱 메뉴에서 **개요** 를 선택합니다. **워크플로** 창에서 **추가** 를 선택합니다.

   ![선택한 논리 앱의 "워크플로" 창과 "추가" 명령이 선택된 도구 모음을 보여주는 스크린샷입니다.](./media/create-stateful-stateless-workflows-visual-studio-code/add-new-workflow.png)

1. **새 워크플로** 창에서 워크플로의 이름을 입력합니다. **상태 저장** 또는 **상태 비저장** **>** **만들기** 를 선택합니다.

   Azure가 **워크플로** 창에 표시되는 새 워크플로를 배포한 후 디자이너 또는 코드 보기 열기와 같은 다른 작업을 관리하고 수행할 수 있도록 해당 워크플로를 선택합니다.

   ![관리 및 검토 옵션과 함께 선택한 워크플로를 보여주는 스크린샷입니다.](./media/create-stateful-stateless-workflows-visual-studio-code/view-new-workflow.png)

   예를 들어 새 워크플로를 위해 디자이너를 열면 빈 캔버스가 표시됩니다. 이제 Azure Portal에서 이 워크플로를 빌드할 수 있습니다.

   ![워크플로 디자이너와 빈 워크플로를 보여주는 스크린샷입니다.](./media/create-stateful-stateless-workflows-visual-studio-code/opened-blank-workflow-designer.png)

<a name="enable-run-history-stateless"></a>

## <a name="enable-run-history-for-stateless-workflows"></a>상태 비저장 워크플로에 대한 실행 기록 사용 설정

상태 비저장 워크플로를 보다 쉽게 디버그하려면 해당 워크플로에 실행 기록을 사용하도록 설정한 다음, 완료되면 실행 기록을 사용하지 않도록 설정하면 됩니다. Visual Studio Code의 경우 다음 단계를 수행하거나, Azure Portal에서 작업하는 경우 [Azure Portal에서 상태 저장 및 상태 비저장 워크플로 만들기](create-stateful-stateless-workflows-azure-portal.md#enable-run-history-stateless)를 참조하세요.

1. Visual Studio Code 프로젝트에서 **workflow-designtime** 폴더를 확장하고 **local.settings.json** 파일을 엽니다.

1. `Workflows.{yourWorkflowName}.operationOptions` 속성을 추가하고 값을 `WithStatelessRunHistory`로 설정합니다. 다음은 그 예입니다:

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

1. 작업을 마친 후 실행 기록을 사용하지 않도록 설정하려면 `Workflows.{yourWorkflowName}.OperationOptions` 속성을 `None`로 설정하거나 이 속성과 해당 값을 삭제합니다.

<a name="enable-monitoring"></a>

## <a name="enable-monitoring-view-in-the-azure-portal"></a>Azure Portal 모니터링 보기 사용 설정

Visual Studio Code에서 Azure로 **Logic App(미리 보기)** 리소스를 배포한 후 Azure Portal 및 해당 워크플로에 대한 **모니터링** 환경을 사용하여 해당 리소스의 워크플로에 대해 사용 가능한 실행 기록 및 세부 정보를 검토할 수 있습니다. 그러나 먼저 해당 논리 앱 리소스에서 **모니터링** 보기 기능을 사용하도록 설정해야 합니다.

1. [Azure Portal](https://portal.azure.com)에서 배포된 **Logic App(미리 보기)** 리소스를 찾아 선택합니다.

1. 해당 리소스의 메뉴의 **API** 에서 **CORS** 를 선택합니다.

1. **CORS** 창의 **허용된 원본** 에서 와일드카드 문자(*)를 추가합니다.

1. 완료되면 **CORS** 도구 모음에서 **저장** 을 선택합니다.

   ![배포된 Logic App(미리 보기) 리소스가 있는 Azure Portal을 보여주는 스크린샷입니다. 리소스 메뉴에서 "허용된 원본"의 새 항목이 와일드카드 "*" 문자로 설정된 상태로 "CORS"가 선택됩니다.](./media/create-stateful-stateless-workflows-visual-studio-code/enable-run-history-deployed-logic-app.png)

<a name="enable-open-application-insights"></a>

## <a name="enable-or-open-application-insights-after-deployment"></a>배포 후 Application Insights를 사용하도록 설정하거나 열기

워크플로가 실행되는 동안 논리 앱은 다른 이벤트와 함께 원격 분석 데이터를 내보냅니다. 이 원격 분석 데이터를 사용하여 워크플로가 얼마나 잘 실행되는지 그리고 Logic Apps 런타임이 얼마나 다양한 방식으로 작동하는지 보다 정확하게 파악할 수 있습니다. 거의 실시간 원격 분석 데이터(라이브 메트릭)를 제공하는 [Application Insights](../azure-monitor/app/app-insights-overview.md)를 사용하여 워크플로를 모니터링할 수 있습니다. 이 데이터를 사용하여 문제를 진단하고 경고를 설정하고 차트를 작성할 때 이 기능을 통해 오류 및 성능 문제를 보다 쉽게 조사할 수 있습니다.

논리 앱의 만들기 및 배포 설정에서 [Application Insights](../azure-monitor/app/app-insights-overview.md) 사용을 지원하는 경우 필요에 따라 논리 앱에 진단 로깅 및 추적을 사용하도록 설정할 수 있습니다. Visual Studio Code에서 논리 앱을 배포할 때 또는 배포 후에 이러한 작업을 수행할 수 있습니다. Application Insights 인스턴스가 필요하지만, 논리 앱을 배포할 때 [미리](../azure-monitor/app/create-workspace-resource.md) 또는 배포 후에 이 리소스를 만들 수 있습니다.

배포한 논리 앱에서 Application Insights를 사용하도록 설정하거나 이미 사용하도록 설정한 Application Insights 데이터를 검토하려면 다음 단계를 수행합니다:

1. Azure Portal에서 배포된 논리 앱을 찾습니다.

1. 논리 앱 메뉴의 **설정** 에서 **Application Insights** 를 선택합니다.

1. Application Insights가 사용하도록 설정되지 않은 경우 **Application Insights** 창에서 **Application Insights 켜기** 를 선택합니다. 창이 업데이트된 후 아래쪽에서 **적용** 을 선택합니다.

   Application Insights가 사용하도록 설정된 경우 **Application Insights** 창에서 **Application Insights 데이터 보기** 를 선택합니다.

Application Insights가 열리면 논리 앱에 대한 다양한 메트릭을 검토할 수 있습니다. 자세한 내용은 다음 항목을 검토하세요:

* [어디서나 실행되는 Azure Logic Apps - Application Insights로 모니터링 - 1부](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-monitor-with-application/ba-p/1877849)
* [어디서나 실행되는 Azure Logic Apps - Application Insights로 모니터링 - 2부](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-monitor-with-application/ba-p/2003332)

<a name="deploy-docker"></a>

## <a name="deploy-to-docker"></a>Docker에 배포

[.NET CLI](/dotnet/core/tools/)를 사용하여 호스팅 환경으로 [Docker 컨테이너](/visualstudio/docker/tutorials/docker-tutorial#what-is-a-container)에 논리 앱을 배포할 수 있습니다. 해당 명령을 사용하여 논리 앱의 프로젝트를 빌드하고 게시할 수 있습니다. 그런 다음 논리 앱을 배포하기 위한 대상으로 Docker 컨테이너를 빌드하고 실행할 수 있습니다.

Docker에 익숙하지 않은 경우, 다음 항목을 검토하세요:

* [Docker란?](/dotnet/architecture/microservices/container-docker-introduction/docker-defined)
* [컨테이너 및 Docker 소개](/dotnet/architecture/microservices/container-docker-introduction/)
* [.NET 및 Docker 소개](/dotnet/core/docker/introduction)
* [Docker 컨테이너, 이미지 및 레지스트리](/dotnet/architecture/microservices/container-docker-introduction/docker-containers-images-registries)
* [자습서: Docker 시작(Visual Studio Code)](/visualstudio/docker/tutorials/docker-tutorial)

### <a name="requirements"></a>요구 사항

* 논리 앱이 배포에 사용하는 Azure Storage 계정

* Docker 컨테이너를 빌드할 때 사용하는 워크플로용 Docker 파일

  예를 들어 이 샘플 Docker 파일은 논리 앱을 배포하고 Azure Portal에 논리 앱을 게시하는 데 사용된 Azure Storage 계정의 액세스 키가 포함된 연결 문자열을 지정합니다. 이 문자열을 찾으려면 [스토리지 계정 연결 문자열 가져오기](#find-storage-account-connection-string)를 참조하세요. 자세한 내용은 [Docker 파일 작성에 대한 모범 사례](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/)를 검토하세요.
  
  > [!IMPORTANT]
  > 프로덕션 시나리오의 경우 키 자격 증명 모음을 사용하는 등 이러한 암호와 중요한 정보를 보호하고 안전하게 지켜야 합니다. 특히 Docker 파일의 경우 [BuildKit으로 이미지 빌드](https://docs.docker.com/develop/develop-images/build_enhancements/) 및 [Docker Secrets로 중요한 데이터 관리](https://docs.docker.com/engine/swarm/secrets/)를 검토하세요.

   ```text
   FROM mcr.microsoft.com/azure-functions/node:3.0

   ENV AzureWebJobsStorage <storage-account-connection-string>
   ENV AzureWebJobsScriptRoot=/home/site/wwwroot \
       AzureFunctionsJobHost__Logging__Console__IsEnabled=true \
       FUNCTIONS_V2_COMPATIBILITY_MODE=true

   COPY . /home/site/wwwroot

   RUN cd /home/site/wwwroot
   ```

<a name="find-storage-account-connection-string"></a>

### <a name="get-storage-account-connection-string"></a>스토리지 계정 연결 문자열 가져오기

Docker 컨테이너 이미지를 빌드하고 실행하려면 먼저 스토리지 계정 액세스 키가 포함된 연결 문자열을 가져와야 합니다. 이전에는 macOS 또는 Linux에서 확장을 사용하거나 논리 앱을 Azure Portal에 배포할 때 이 스토리지 계정을 만들었습니다.

이 연결 문자열을 찾아서 복사하려면 다음 단계를 수행합니다:

1. Azure Portal에서 스토리지 계정 메뉴 **설정** 에서 **액세스 키** 를 선택합니다. 

1. **액세스 키** 창에서 스토리지 계정의 연결 문자열을 찾아 복사합니다. 다음의 예제와 유사합니다:

   `DefaultEndpointsProtocol=https;AccountName=fabrikamstorageacct;AccountKey=<access-key>;EndpointSuffix=core.windows.net`

   ![스토리지 계정 액세스 키와 연결 문자열이 복사된 Azure Portal을 보여주는 스크린샷입니다.](./media/create-stateful-stateless-workflows-visual-studio-code/find-storage-account-connection-string.png)

   자세한 내용은 [스토리지 계정 키 관리](../storage/common/storage-account-keys-manage.md?tabs=azure-portal#view-account-access-keys)를 검토하세요.

1. 배포에 사용하는 Docker 파일에 이 문자열을 추가할 수 있도록 연결 문자열을 안전한 위치에 저장합니다. 

<a name="find-storage-account-master-key"></a>

### <a name="find-master-key-for-storage-account"></a>스토리지 계정용 마스터 키 찾기

워크플로에 요청 트리거가 포함된 경우 Docker 컨테이너 이미지를 빌드하고 실행한 후 [트리거의 콜백 URL을 가져와야](#get-callback-url-request-trigger) 합니다. 이 작업의 경우 배포에 사용하는 스토리지 계정에 대한 마스터 키 값도 지정해야 합니다.

1. 이 마스터 키를 찾으려면 프로젝트에서 **azure-webjobs-secrets/{deployment-name}/host.json** 파일을 엽니다.

1. `AzureWebJobsStorage` 속성을 찾고 이 섹션에서 키 값을 복사합니다:

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

1. 나중에 사용할 수 있도록 안전한 위치에 키 값을 저장합니다.

<a name="build-run-docker-container-image"></a>

### <a name="build-and-run-your-docker-container-image"></a>Docker 컨테이너 이미지 빌드 및 실행

1. Docker 파일을 사용하고 다음 명령을 실행하여 Docker 컨테이너 이미지를 빌드합니다:

   `docker build --tag local/workflowcontainer .`

   자세한 내용은 [Docker 빌드](https://docs.docker.com/engine/reference/commandline/build/)를 참조하세요.

1. 다음 명령을 사용하여 컨테이너를 로컬에서 실행합니다:

   `docker run -e WEBSITE_HOSTNAME=localhost -p 8080:80 local/workflowcontainer`

   자세한 내용은 [Docker 실행](https://docs.docker.com/engine/reference/commandline/run/)을 참조하세요.

<a name="get-callback-url-request-trigger"></a>

### <a name="get-callback-url-for-request-trigger"></a>요청 트리거에 대한 콜백 URL을 가져오기

요청 트리거를 사용하는 워크플로의 경우 다음 요청을 전송하여 트리거의 콜백 URL을 가져옵니다:

`POST /runtime/webhooks/workflow/api/management/workflows/{workflow-name}/triggers/{trigger-name}/listCallbackUrl?api-version=2020-05-01-preview&code={master-key}`

`{trigger-name}` 값은 워크플로 JSON 정의에 표시되는 요청 트리거의 이름입니다. `{master-key}` 값은 **azure-webjobs-secrets/{deployment-name}/host.json** 파일에서 `AzureWebJobsStorage` 속성에 대해 설정한 Azure Storage 계정에 정의됩니다. 자세한 내용은 [스토리지 계정 마스터 키 찾기](#find-storage-account-master-key)를 참조하세요.

<a name="delete-from-designer"></a>

## <a name="delete-items-from-the-designer"></a>디자이너에서 항목 삭제

디자이너에서 워크플로의 항목을 삭제하려면 다음 단계 중 하나를 수행합니다:

* 항목을 선택하고 항목의 바로 가기 메뉴(Shift+F10)를 열고 **삭제** 를 선택합니다. 확인하려면 **확인** 을 선택합니다.

* 항목을 선택하고 삭제 키를 누릅니다. 확인하려면 **확인** 을 선택합니다.

* 항목을 선택하면 해당 항목에 대한 세부 정보 창이 열립니다. 이 창의 오른쪽 위 모서리에서 줄임표( **...** ) 메뉴를 열고 **삭제** 를 선택합니다. 확인하려면 **확인** 을 선택합니다.

  ![디자이너에서 항목을 선택하여 세부 정보 창이 열려 있고 줄임표 단추를 선택하여 "삭제" 명령이 표시된 것을 보여주는 스크린샷입니다.](./media/create-stateful-stateless-workflows-visual-studio-code/delete-item-from-designer.png)

  > [!TIP]
  > 줄임표 메뉴가 표시되지 않는 경우 세부 정보 창의 오른쪽 위 모서리에 줄임표( **...** ) 단추가 표시되도록 Visual Studio Code 창을 충분히 넓게 확장합니다.

<a name="troubleshooting"></a>

## <a name="troubleshoot-errors-and-problems"></a>오류 및 문제 해결

<a name="designer-fails-to-open"></a>

### <a name="designer-fails-to-open"></a>디자이너 열기 실패

디자이너를 열려고 하면 **"워크플로 디자인 타임을 시작할 수 없습니다"** 라는 오류 메시지가 표시됩니다. 이전에 디자이너를 열려고 시도한 뒤 프로젝트를 중단하거나 삭제한 경우 확장 번들이 올바르게 다운로드되지 않을 수 있습니다. 이 원인이 문제인지 확인하려면 다음 단계를 수행합니다:

  1. Visual Studio Code에서 출력 창을 엽니다. **보기** 메뉴에서 **출력** 을 선택합니다.

  1. 출력 창의 제목 표시줄에 있는 목록에서 **Azure Logic Apps(미리 보기)** 를 선택하여 확장의 출력을 검토할 수 있습니다. 예를 들면 다음과 같습니다:

     !["Azure Logic Apps"이 선택된 출력 창을 보여주는 스크린샷입니다.](./media/create-stateful-stateless-workflows-visual-studio-code/check-outout-window-azure-logic-apps.png)

  1. 출력을 검토하고 다음 오류 메시지가 표시되는지 확인합니다:

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

   이 오류를 해결하려면 **...\Users\{your-username}\AppData\Local\Temp\Functions\ExtensionBundles** 위치에서 **ExtensionBundles** 폴더를 삭제하고 다시 디자이너에서 **workflow.json** 파일을 다시 열어보십시오.

<a name="missing-triggers-actions"></a>

### <a name="new-triggers-and-actions-are-missing-from-the-designer-picker-for-previously-created-workflows"></a>이전에 만든 워크플로의 디자이너 선택기에 새 트리거 및 작업이 없음

Azure Logic Apps 미리 보기는 Azure Function 작업, Liquid 작업 및 XML 작업(예: **XML 유효성 검사** 및 **XML 변환**)을 위한 기본 제공 작업을 지원합니다. 그러나 이전에 만든 논리 앱의 경우 Visual Studio Code에서 오래된 버전의 확장 번들 `Microsoft.Azure.Functions.ExtensionBundle.Workflows`을(를) 사용하기 때문에 이러한 작업이 디자이너 선택기에 표시되지 않을 수 있습니다.

또한 논리 앱을 만들 때 **Azure 커넥터 사용** 을 사용하도록 설정하거나 선택하지 않으면 **Azure Function 작업** 커넥터 및 작업이 디자이너 선택기에 표시되지 않습니다. 앱을 만들 때 Azure 배포 커넥터를 사용하도록 설정하지 않은 경우 Visual Studio Code의 프로젝트에서 사용하도록 설정할 수 있습니다. 바로 가기 메뉴에서 **workflow.json** 을 열고 **Azure 커넥터 사용** 을 선택합니다.

오래된 번들을 수정하려면, 다음 단계를 수행하여 오래된 번들을 삭제하면 Visual Studio Code에서 확장 번들을 최신 버전으로 자동 업데이트합니다.

> [!NOTE]
> 이 솔루션은 Azure Portal을 사용하여 만든 논리 앱이 아닌 Azure Logic Apps(미리 보기) 확장과 함께 Visual Studio Code를 사용하여 만들고 배포하는 논리 앱에만 적용됩니다. [지원되는 트리거 및 작업이 Azure portal 디자이너에 없음](create-stateful-stateless-workflows-azure-portal.md#missing-triggers-actions)을 참조하세요.

1. 손실하지 않으려는 작업을 모두 저장하고 Visual Studio를 닫습니다.

1. 컴퓨터에서 기존 번들의 버전 폴더가 들어 있는 다음 폴더로 이동합니다:

   `...\Users\{your-username}\.azure-functions-core-tools\Functions\ExtensionBundles\Microsoft.Azure.Functions.ExtensionBundle.Workflows`

1. 이전 번들 버전 폴더를 삭제합니다(예: 1.1.3 버전 폴더가 있는 경우 해당 폴더 삭제).

1. 이제 필수 NuGet 패키지의 버전 폴더가 포함된 다음 폴더로 이동합니다:

   `...\Users\{your-username}\.nuget\packages\microsoft.azure.workflows.webjobs.extension`

1. 이전 패키지 버전 폴더를 삭제합니다(예: 1.0.0.8 버전 미리 보기 폴더가 있는 경우 해당 폴더 삭제).

1. 디자이너에서 Visual Studio Code, 프로젝트 및 **workflow.json** 파일을 다시 엽니다.

누락된 트리거와 작업이 이제 디자이너에 표시됩니다.

<a name="400-bad-request"></a>

### <a name="400-bad-request-appears-on-a-trigger-or-action"></a>트리거 또는 작업에 "400 잘못된 요청"이 표시됨

실행이 실패하고 모니터링 보기에서 실행을 검사하면 이 오류가 긴 이름을 가진 트리거 또는 작업에 표시될 수 있으며, 이로 인해 기본 URI(Uniform Resource Identifier)가 기본 문자 제한을 초과하게 됩니다.

이 문제를 해결하고 긴 URI를 조정하려면 아래 단계를 수행하여 컴퓨터에서 `UrlSegmentMaxCount` 및 `UrlSegmentMaxLength` 레지스트리 키를 편집합니다. 해당 키 기본값은 [Windows용 Http.sys 레지스트리 설정](/troubleshoot/iis/httpsys-registry-windows) 항목을 참조하세요.

> [!IMPORTANT]
> 시작하기 전에 작업 내용을 저장했는지 확인합니다. 이 솔루션을 사용하려면 변경 사항을 적용하기 위해 작업을 마친 후 컴퓨터를 다시 시작해야 합니다.

1. 컴퓨터에서 **실행** 창을 열고 레지스트리 편집기를 여는 `regedit` 명령을 실행합니다.

1. **사용자 계정 컨트롤** 상자에서 **예** 를 선택하여 컴퓨터에 대한 변경 사항을 허용합니다.

1. 왼쪽 창의 **컴퓨터** 에서 **HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\HTTP\Parameters** 경로를 따라 노드를 확장하고 **매개 변수** 를 선택합니다.

1. 오른쪽 창에서 `UrlSegmentMaxCount` 및 `UrlSegmentMaxLength` 레지스트리 키를 찾습니다.

1. URI가 사용하려는 이름을 수용할 수 있도록 해당 키 값을 충분히 늘립니다. 해당 키가 없으면 다음 단계를 수행하여 **매개 변수** 폴더에 추가합니다:

   1. **매개 변수** 바로 가기 메뉴에서 **새** > **DWORD (32-비트) 값** 을 선택합니다.

   1. 표시되는 편집 상자에 새 키 이름으로 `UrlSegmentMaxCount`을 입력합니다.

   1. 새 키의 바로 가기 메뉴를 열고 **수정** 을 선택합니다.

   1. 표시되는 **문자열 편집** 상자에 원하는 **값 데이터** 키 값을 16진수 또는 10진수 형식으로 입력합니다. 예를 들어 16진수의 `400`은 10진수의 `1024`와 동일합니다.

   1. `UrlSegmentMaxLength` 키 값을 추가하려면 다음 단계를 반복합니다.

   해당 키 값을 늘리거나 추가하면 레지스트리 편집기가 다음과 같이 표시됩니다:

   ![레지스트리 편집기를 보여주는 스크린샷입니다.](media/create-stateful-stateless-workflows-visual-studio-code/edit-registry-settings-uri-length.png)

1. 준비가 되면 변경 사항을 적용하도록 컴퓨터를 다시 시작합니다.

<a name="debugging-fails-to-start"></a>

### <a name="debugging-session-fails-to-start"></a>디버깅 세션이 시작되지 않음

디버깅 세션을 시작하려 할 때 **"preLaunchTask 'generateDebugSymbols'실행 후 오류가 발생했습니다"** 오류가 발생합니다. 이 문제를 해결하려면 프로젝트에서 **tasks.json** 파일을 편집하여 기호 생성을 건너뜁니다.

1. 프로젝트에서 **.vscode** 폴더를 확장하고 **tasks.json** 파일을 엽니다.

1. 다음 작업에서 앞 줄을 종료하는 쉼표와 함께 `"dependsOn: "generateDebugSymbols"` 줄을 삭제합니다. 예를 들면 다음과 같습니다:

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

Azure Logic Apps(미리 보기) 확장에 대한 여러분의 의견을 듣고 싶습니다!

* 버그 또는 문제가 발견되는 경우 [GitHub에서 문제를 알려주세요](https://github.com/Azure/logicapps/issues).
* [이 사용자 의견 양식을 사용](https://aka.ms/lafeedback)하여 질문, 요청, 의견 및 기타 피드백을 보내주세요.
