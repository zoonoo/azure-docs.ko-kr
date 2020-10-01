---
title: Visual Studio Code에서 자동화 워크플로 (미리 보기) 만들기
description: Visual Studio Code에서 Azure Logic Apps (미리 보기) 확장을 사용 하 여 응용 프로그램, 데이터, 클라우드 서비스 및 온-프레미스 시스템을 통합 하는 상태 비저장 또는 상태 저장 자동화 워크플로를 만듭니다.
services: logic-apps
ms.suite: integration
ms.reviewer: deli, rohitha, vikanand, hongzili, sopai, absaafan, logicappspm
ms.topic: conceptual
ms.date: 09/26/2020
ms.openlocfilehash: cc52358af203bafc87c5f9ac3ae1f237c0c7ae6c
ms.sourcegitcommit: ffa7a269177ea3c9dcefd1dea18ccb6a87c03b70
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91597783"
---
# <a name="create-stateful-or-stateless-workflows-in-visual-studio-code-with-the-azure-logic-apps-preview-extension"></a>Azure Logic Apps (미리 보기) 확장을 사용 하 여 Visual Studio Code에 상태 저장 또는 상태 비저장 워크플로 만들기

> [!IMPORTANT]
> 이 기능은 공개 미리 보기로 제공 되며 서비스 수준 계약 없이 제공 되며 프로덕션 워크 로드에는 권장 되지 않습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

앱, 데이터, 클라우드 서비스 및 시스템 간에 통합 되는 논리 앱 워크플로를 만들기 위해 Visual Studio Code 및 Azure Logic Apps (미리 보기) 확장을 사용 하 여 개발 환경에서 [ *상태 저장* 및 *상태 비저장* 논리 앱 워크플로](#stateful-stateless) 를 빌드하고 로컬로 실행할 수 있습니다.

![Visual Studio Code 및 논리 앱 워크플로를 보여 주는 스크린샷](./media/create-stateful-stateless-workflows-visual-studio-code/visual-studio-code-logic-apps-overview.png)

공개 미리 보기 확장을 사용 하 여 만든 논리 앱은 새 **논리 앱 (미리 보기)** 리소스 유형을 사용 하 고 로컬 환경의 [Azure Functions](../azure-functions/functions-overview.md) 런타임으로 구동 됩니다. 이 새 리소스 유형은 여러 워크플로를 포함할 수 있으며 여러 함수를 포함할 수 있는 **함수 앱** 리소스 유형과 비슷합니다.

그 동안에는 Visual Studio Code 및 Azure Portal에서 만들고 사용할 수 있는 원본 **Logic Apps** 리소스 종류가 여전히 존재 합니다. 그러나 원래 리소스 종류에 대 한 환경은 새 리소스 형식과는 다릅니다. 현재 **Logic Apps** 와 **논리 앱 (미리 보기)** 리소스 형식은 Visual Studio Code 및 Azure Portal에 동시에 존재할 수 있습니다. Azure 구독에서 배포 된 모든 논리 앱을 보고 액세스할 수 있지만, 이러한 앱은 표시 되 고 자체의 범주 및 섹션에서 별도로 유지 됩니다.

이 문서에서는 [이 공개 미리 보기에 대 한 개략적인 개요](#whats-new)를 제공 하 고, **논리 앱 (미리 보기)** 리소스 유형에 대 한 다양 한 측면을 설명 하 고, Visual Studio Code를 사용 하 여이 리소스를 만드는 방법을 설명 합니다.

* [상태 저장 및 상태 비저장](#stateful-stateless) 논리 앱이 서로 다른 방식입니다.

* [설정 요구 사항을](#prerequisites) 충족 하 고 공개 미리 보기 확장에 대 한 [Visual Studio Code를 설정](#set-up) 하는 방법입니다.

* [프로젝트를 만들고 워크플로 템플릿을 선택](#create-project)하 여 새 **논리 앱 (미리 보기)** 워크플로를 빌드하는 방법입니다.

* Visual Studio Code에서 새 논리 앱을 로컬로 실행 하 고 디버그 하는 방법입니다.

* 이러한 새 논리 앱을 Visual Studio Code에서 [Azure로](#publish-azure) 직접 게시 하거나 어디에서 나 실행할 수 있는 [Docker 컨테이너에](#deploy-docker) 게시 하는 방법입니다. Docker에 대 한 자세한 내용은 [docker](/dotnet/architecture/microservices/container-docker-introduction/docker-defined)란?을 참조 하세요.

<a name="whats-new"></a>

## <a name="whats-in-this-public-preview"></a>이 공개 미리 보기에는 무엇이 있나요?

Azure Logic Apps (미리 보기) 확장은 Visual Studio Code의 로컬 개발 환경에 대 한 많은 최신 및 추가 Logic Apps 기능을 제공 합니다. 예를 들면 다음과 같습니다.

* SaaS (Software as a Service) 및 PaaS (Platform as a service) 앱 및 서비스와 온-프레미스 시스템을 위한 커넥터를 위한 [390 + 커넥터](/connectors/connector-reference/connector-reference-logicapps-connectors) 에서 통합 및 자동화 워크플로에 대 한 논리 앱을 빌드 하세요.

  * Azure Service Bus, Azure Event Hubs 및 SQL Server와 같은 일부 관리 되는 커넥터는 기본 제공 네이티브 트리거와 HTTP 동작 등의 작업과 유사 하 게 실행 됩니다.

  * Azure Logic Apps 서비스가 클라우드 연결 런타임 끝점으로 요청을 보내는 데 사용할 수 있는 SAS (공유 액세스 서명) 연결 문자열을 생성 하기 때문에 어디서 나 실행할 수 있는 논리 앱을 만들고 배포 합니다. Logic Apps 서비스는 Azure에 배포할 때 이러한 값을 Azure Key Vault에 쉽게 저장할 수 있도록 이러한 연결 문자열을 다른 응용 프로그램 설정과 함께 저장 합니다.

    > [!NOTE]
    > 기본적으로 **논리 앱 (미리 보기)** 리소스에는 런타임에 연결을 인증 하기 위해 자동으로 사용 하도록 설정 된 [시스템 할당 관리 id](../logic-apps/create-managed-service-identity.md) 가 있습니다. 이 id는 연결을 만들 때 사용 하는 인증 자격 증명 또는 연결 문자열과 다릅니다. 이 id를 사용 하지 않도록 설정 하면 런타임에 연결이 작동 하지 않습니다.

* 메모리 에서만 실행 되는 상태 비저장 논리 앱을 만들어 더 빠르게 완료 하 고, 더 빠르게 응답 하 고, 처리량을 증가 하 고, 실행 기록 및 작업 간 데이터가 외부 저장소에 유지 되지 않기 때문에 실행 비용이 줄어듭니다. 필요에 따라 디버깅을 용이 하 게 하기 위해 실행 기록을 사용 하도록 설정할 수 있습니다. 자세한 내용은 [상태 저장 및 상태 비저장 논리 앱](#stateful-stateless)을 참조 하세요.

* Visual Studio Code 개발 환경에서 논리 앱을 로컬로 실행 하 고 디버깅 합니다.

* Visual Studio Code에서 [Azure App Service](../app-service/environment/intro.md) 및 [Docker 컨테이너](/dotnet/core/docker/introduction)와 같은 다양 한 호스팅 환경에 직접 논리 앱을 게시 하 고 배포 합니다.

> [!NOTE]
> 현재 알려진 문제에 대 한 자세한 내용은 미리 보기 확장의 [알려진 문제 GitHub 페이지](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md)를 참조 하세요.

<a name="stateful-stateless"></a>

## <a name="stateful-versus-stateless-logic-apps"></a>상태 저장 및 상태 비저장 논리 앱

* *상태 저장*

  이전 이벤트의 데이터를 유지, 검토 또는 참조 해야 할 때 상태 저장 논리 앱을 만듭니다. 이러한 논리 앱은 각 작업 및 해당 워크플로 상태에 대 한 입력 및 출력을 외부 저장소에 유지 합니다. 그러면 각 실행이 완료 된 후 가능한 실행 정보와 기록을 검토할 수 있습니다. 상태 저장 논리 앱은 정전 발생 시 또는 높은 복원 력을 제공 합니다. 서비스 및 시스템이 복원 된 후 중단 된 논리 앱 실행을 저장 된 상태에서 다시 작성 하 고 논리 앱을 다시 실행 하 여 완료할 수 있습니다. 상태 저장 워크플로는 최대 1 년 동안 계속 실행 될 수 있습니다.

* *상태 비저장*

  이후 검토를 위해 외부 저장소에서 이전 이벤트의 데이터를 저장, 검토 또는 참조할 필요가 없는 경우 상태 비저장 논리 앱을 만듭니다. 이러한 논리 앱은이 정보를 외부 저장소에 전송 하는 대신 각 작업 및 해당 워크플로 상태에 대 한 입력 및 출력을 메모리에만 유지 합니다. 그 결과, 상태 비저장 논리 앱은 일반적으로 5 분 보다 더 짧은 실행, 더 빠른 응답 시간으로 더 빠른 성능, 더 높은 처리량 및 실행 비용 절감, 실행 세부 정보 및 기록이 외부 저장소에 유지 되지 않기 때문입니다. 그러나 중단이 발생 하면 중단 된 실행이 자동으로 복원 되지 않으므로 호출자는 중단 된 실행을 수동으로 다시 전송 해야 합니다. 이러한 논리 앱은 동기적 으로만 실행할 수 있으며 디버깅을 용이 하 게 하기 위해 성능에 약간의 영향을 주는 [실행 기록을 사용 하도록 설정할](#run-history)수 있습니다.

  상태 비저장 워크플로는 현재 instead of [관리 커넥터](../connectors/apis-list.md#managed-api-connectors)에 대 한 동작만 지원 합니다. 워크플로를 시작 하려면 [기본 제공 요청, Event Hubs 또는 Service Bus 트리거](../connectors/apis-list.md#built-ins)를 선택 합니다. 지원 되지 않는 트리거, 동작 및 커넥터에 대 한 자세한 내용은 [지원 되지 않는 기능](#unsupported)을 참조 하세요.

상태 저장 및 상태 비저장 논리 앱 간에 중첩 된 논리 앱이 다르게 동작 하는 방법에 대 한 자세한 내용은 [상태 저장 및 상태 비저장 논리 앱 간의 중첩 된 동작 차이점](#nested-behavior)을 참조 하세요.

<a name="pricing-model"></a>

## <a name="pricing-model"></a>가격 책정 모델

새 **논리 앱 (미리 보기)** 리소스 유형을 배포 하는 경우 호스팅 계획을 선택 하 라는 메시지가 표시 됩니다. 특히 가격 책정 모델로 사용할 [App Service 계획 또는 프리미엄 계획](../azure-functions/functions-scale.md) 을 선택 하 라는 메시지가 표시 됩니다. App Service 계획을 선택 하는 경우 [가격 책정 계층](../app-service/overview-hosting-plans.md)을 선택 하 라는 메시지가 표시 됩니다. 공개 미리 보기 중에 App Service에서 논리 앱을 실행 하면 선택한 계획 위에 *추가* 요금이 발생 하지 않습니다.

상태 저장 논리 앱은 [외부 저장소](../azure-functions/functions-scale.md#storage-account-requirements)를 사용 하므로 Azure Storage 가격 책정 모델은 Azure Logic Apps 런타임이 수행 하는 저장소 트랜잭션에 적용 됩니다. 예를 들어 큐는 일정에 사용 되는 반면 테이블 및 blob은 워크플로 상태를 저장 하는 데 사용 됩니다.

이 새 리소스 유형에 적용 되는 가격 책정 모델에 대 한 자세한 내용은 다음 항목을 검토 하세요.

* [Azure Functions 비율 크기 조정 및 호스팅](../azure-functions/functions-scale.md)
* [Azure App Service에서 확장](../app-service/manage-scale-up.md)
* [Azure Functions 가격 정보](https://azure.microsoft.com/pricing/details/functions/)
* [App Service 가격 정보](https://azure.microsoft.com/pricing/details/app-service/windows/)
* [Azure Storage 가격 정보](https://azure.microsoft.com/pricing/details/storage/)

<a name="unsupported"></a>

## <a name="unavailable-or-unsupported-capabilities"></a>사용할 수 없거나 지원 되지 않는 기능

이 공개 미리 보기의 경우 이러한 기능을 사용할 수 없거나 지원 되지 않습니다.

* 새 **논리 앱 (미리 보기)** 리소스 만들기는 현재 macos에서 사용할 수 없습니다.

* 일부 Azure 지역은 아직 지원 되지 않습니다. 현재 사용 가능한 지역에 대해서는 [지역 목록을](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md#available-regions)확인 하세요.

* 워크플로를 시작 하려면 [Request, HTTP, Event Hubs 또는 Service Bus 트리거](../connectors/apis-list.md)를 사용 합니다. 현재, [엔터프라이즈 커넥터](../connectors/apis-list.md#enterprise-connectors), [온-프레미스 데이터 게이트웨이 트리거](../connectors/apis-list.md#on-premises-connectors), Webhook 기반 트리거, 슬라이딩 윈도우 트리거, [사용자 지정 커넥터](../connectors/apis-list.md#custom-apis-and-connectors), 통합 계정, 해당 아티팩트 및 [커넥터는](../connectors/apis-list.md#integration-account-connectors) 이 미리 보기에서 지원 되지 않습니다. "Azure 함수 호출" 기능을 사용할 수 없으므로 지금은 HTTP *작업* 을 사용 하 여 azure function에 대 한 요청 URL을 호출 합니다.

  상태 비저장 논리 앱 워크플로는 트리거가 아니라 [관리 되는 커넥터](../connectors/apis-list.md#managed-api-connectors)에 대해서만 작업을 사용할 수 있습니다. 이전에 지정한 트리거를 제외 하 고, 상태 저장 워크플로는 관리 되는 커넥터에 대해 트리거와 작업을 모두 사용할 수 있습니다.

* 새 **논리 앱 (미리 보기)** 리소스 유형은 [Azure의 프리미엄 또는 App Service 호스팅 계획](#publish-azure) 에만 배포 하거나 [ISEs (통합 서비스 환경)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)이 아닌 [Docker 컨테이너](#deploy-docker)에 배포할 수 있습니다. **소비** 호스팅 계획은이 리소스 종류를 배포 하는 데 지원 되지 않으며 사용할 수 없습니다.

* Azure Portal에서는 새 **논리 앱 (미리 보기)** 리소스 유형을 사용 하 여 새 논리 앱을 만들 수 없습니다. 이러한 논리 앱은 Visual Studio Code 에서만 만들 수 있습니다. 그러나 Visual Studio Code에서 Azure로이 리소스 유형을 사용 하 여 논리 앱을 배포한 후에는 [해당 논리 앱에 새 워크플로를 추가할](#add-workflows)수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

### <a name="access-and-connectivity"></a>액세스 및 연결

* 인터넷에 액세스 하 여 요구 사항을 다운로드 하 고, Visual Studio Code에서 Azure 계정으로 연결 하 고, Visual Studio Code에서 Azure, Docker 컨테이너 또는 다른 환경으로 게시할 수 있습니다.

* Azure 계정 및 구독 구독이 없는 경우 [Azure 체험 계정에 등록](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)합니다.

* 이 문서에서 동일한 예제 논리 앱을 빌드하려면 Microsoft 회사 또는 학교 계정을 사용 하 여 로그인 하는 Office 365 Outlook 전자 메일 계정이 필요 합니다.

  Outlook.com 또는 [Gmail](../connectors/connectors-google-data-security-privacy-policy.md)과 같은 [Azure Logic Apps에서 지 원하는 다른 전자 메일 커넥터](/connectors/)를 사용 하도록 선택 하는 경우에도 예제를 계속 진행할 수 있지만 일반적인 전체 단계는 동일 하지만 사용자 인터페이스와 옵션은 일부 면에서 다를 수 있습니다. 예를 들어 Outlook.com 커넥터를 사용 하는 경우 대신 개인 Microsoft 계정를 사용 하 여 로그인 합니다.

### <a name="storage-requirements"></a>저장소 요구 사항

1. [Azure Storage Emulator 5.10](https://go.microsoft.com/fwlink/p/?linkid=717179)를 다운로드 하 여 설치 합니다.

1. 에뮬레이터를 실행 하려면 무료 [SQL Server 2019 Express Edition](https://go.microsoft.com/fwlink/p/?linkid=866658)과 같은 로컬 SQL DB가 설치 되어 있어야 합니다. 자세한 내용은 [Azure Storage 에뮬레이터를 사용 하 여 개발 및 테스트](../storage/common/storage-use-emulator.md)를 참조 하세요.

   > [!IMPORTANT]
   > 논리 앱 디자이너를 열어 논리 앱 워크플로를 만들려면 먼저 에뮬레이터를 시작 해야 합니다. 그렇지 않으면 메시지를 받게 `Workflow design time could not be started` 됩니다.
   >
   > ![실행 중인 Azure Storage 에뮬레이터를 보여 주는 스크린샷](./media/create-stateful-stateless-workflows-visual-studio-code/start-storage-emulator.png)

### <a name="tools"></a>도구

* [Visual Studio Code 1.30.1 (1 월 2019) 이상](https://code.visualstudio.com/)이며 무료입니다. 또한 Visual Studio Code에 대 한 추가 도구를 다운로드 하 여 설치 합니다 (아직 없는 경우).

  * [Azure 계정 확장](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)-Visual Studio Code의 다른 모든 azure 확장에 대해 단일 공통 azure 로그인 및 구독 필터링 환경을 제공 합니다.

  * [C # for Visual Studio Code 확장](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)-F5 기능을 사용 하 여 논리 앱을 실행할 수 있습니다.

  * Microsoft Installer (MSI)를 통해 [3.0.2931](https://github.com/Azure/azure-functions-core-tools/releases/tag/3.0.2931) 또는 [2.7.2936](https://github.com/Azure/azure-functions-core-tools/releases/tag/2.7.2936)버전 중 하나를 [Azure Functions Core Tools](../azure-functions/functions-run-local.md)합니다. 이러한 도구에는 Visual Studio Code에서 실행 되는 Azure Functions 런타임을 구동 하는 동일한 런타임의 버전이 포함 되어 있습니다.

    > [!IMPORTANT]
    > 이러한 버전 보다 이전 버전인 경우 먼저 해당 버전을 제거 하거나 PATH 환경 변수가 다운로드 및 설치 버전을 가리키는지 확인 합니다.
    >
    > JavaScript 코드를 실행 하는 데 [ **인라인 코드** 작업](../logic-apps/logic-apps-add-run-inline-code.md) 을 사용 하려는 경우 작업에서 버전 2x를 지원 하지 않으므로 Azure Functions 런타임 버전 3을 사용 해야 합니다. 또한이 작업은 현재 Linux 운영 체제에서 지원 되지 않습니다.

  * [Visual Studio Code에 대 한 Azure Logic Apps (미리 보기) 확장](https://go.microsoft.com/fwlink/p/?linkid=2143167)입니다. 이 공개 미리 보기 확장은 상태 저장 및 상태 비저장 논리 앱을 만들고 Visual Studio Code에서 로컬로 실행할 수 있는 기능을 제공 합니다.

    현재 Visual Studio Code에서 원래 **Azure Logic Apps** 확장 및 새 **Azure Logic Apps (미리 보기)** 확장을 동시에 설치할 수 있습니다. Visual Studio Code 도구 모음에서 Azure 아이콘을 선택 하 여 Azure에 배포 된 모든 논리 앱을 볼 수 있지만 각 리소스 종류는 자체 확장 섹션, **Logic Apps** 및 **Azure Logic Apps (미리 보기)** 에 표시 됩니다.

    > [!IMPORTANT]
    > **Azure Logic Apps (비공개 미리 보기)** 확장을 사용 하 여 논리 앱을 만든 경우 이러한 논리 앱은 공개 미리 보기 확장에서 작동 하지 않습니다. 그러나 비공개 미리 보기 확장을 제거 하 고 필요한 정리를 수행 하 고 공개 미리 보기 확장을 설치 하 여 이러한 논리 앱을 마이그레이션할 수 있습니다. 그런 다음 Visual Studio Code에서 새 프로젝트를 만들고 이전에 만든 논리 앱의 **워크플로. 정의** 파일을 새 프로젝트에 복사할 수 있습니다.
    >
    > 따라서 공용 미리 보기 확장을 설치 하기 전에 이전 버전을 모두 제거 하 고 이러한 아티팩트를 삭제 해야 합니다.
    >
    > * 이전 확장 번들을 포함 하 고 다음 경로를 따라 배치 된 **Microsoft. Node.js 번들. 워크플로** 폴더.
    >
    >   * `C:\Users\{userName}\AppData\Local\Temp\Functions\ExtensionBundles`
    >
    >   * `C:\Users\{userName}.azure-functions-core-tools\Functions\ExtensionBundles`
    >
    > * 비공개 미리 보기 확장에 대 한 [NuGet](/nuget/what-is-nuget) 캐시이 고이 경로를 따라 배치 되는 **webjobs** 폴더입니다.
    >
    >   `C:\Users\{userName}\.nuget\packages`

    **Azure Logic Apps (미리 보기)** 확장을 설치 하려면 다음 단계를 수행 합니다.

    1. Visual Studio Code의 왼쪽 도구 모음에서 **확장**을 선택 합니다.

    1. 확장 검색 상자에을 입력 `azure logic apps preview` 합니다. 결과 목록에서 **Azure Logic Apps (미리 보기)** **>** **설치**를 선택 합니다.

       설치가 완료 되 면 공용 미리 보기 확장이 **확장: 설치 됨** 목록에 표시 됩니다.

       !["Azure Logic Apps (미리 보기)" 확장명이 밑줄로 표시 된 Visual Studio Code의 설치 된 확장 목록을 보여 주는 스크린샷](./media/create-stateful-stateless-workflows-visual-studio-code/azure-logic-apps-extension-installed.png)

* 이 문서에서 만든 예제 논리 앱을 테스트 하려면 예제 논리 앱의 첫 단계인 요청 트리거에 대 한 호출을 보낼 수 있는 도구가 필요 합니다. 이러한 도구가 없는 경우 [Postman](https://www.postman.com/downloads/)을 다운로드 하 여 설치 하 고 사용할 수 있습니다.

* 보다 쉬운 진단 로깅 및 추적 기능을 위해 [Application Insights](../azure-monitor/app/app-insights-overview.md) 리소스를 추가 하 고 사용할 수 있습니다. 논리 앱을 배포 하는 동안 또는 논리 앱을 배포한 후 Azure Portal에서이 리소스를 만들 수 있습니다.

<a name="set-up"></a>

## <a name="set-up-visual-studio-code"></a>Visual Studio Code 설정

1. 모든 확장이 제대로 설치 되었는지 확인 하려면 다시 로드 하거나 Visual Studio Code를 다시 시작 합니다.

1. 공용 미리 보기 확장에서 최신 업데이트를 사용할 수 있도록 자동으로 확장 업데이트를 찾아서 설치 Visual Studio Code 하는지 확인 합니다.

   이 설정을 확인하려면 다음 단계를 수행합니다.

   1. **파일** 메뉴에서 **기본** **>** **설정 설정**으로 이동 합니다.

   1. **사용자** 탭에서 **기능** **>** **확장**으로 이동 합니다.

   1. **업데이트 자동 확인** 및 **자동 업데이트** 가 선택 되어 있는지 확인 합니다.

1. 이러한 **Azure Logic Apps (미리 보기)** 확장 설정이 Visual Studio Code에 설정 되었는지 확인 합니다.

   * **Azure Logic Apps V2: Panel 모드**
   * **Azure Logic Apps V2: 프로젝트 런타임**

   1. **파일** 메뉴에서 **기본** **>** **설정 설정**으로 이동 합니다.

   1. **사용자** 탭에서 **>** **확장** **>** **Azure Logic Apps (미리 보기)** 로 이동 합니다.

   1. **Azure Logic Apps V2: Panel 모드**에서 **패널 모드 사용** 이 선택 되어 있는지 확인 합니다. **Azure Logic Apps V2: Project Runtime**에서 이전에 설치한 [Azure Functions Core Tools 버전](#prerequisites) 에 따라 버전을 **~ 3** 또는 **~ 2**로 설정 합니다.

      > [!IMPORTANT]
      > JavaScript 코드를 실행 하기 위해 [ **인라인 코드** 작업](../logic-apps/logic-apps-add-run-inline-code.md) 을 사용 하려면 작업에서 버전 2를 지원 하지 않으므로 Project Runtime 버전 3을 사용 해야 합니다. 또한이 작업은 현재 Linux 운영 체제에서 지원 되지 않습니다.

      !["Azure Logic Apps (미리 보기)" 확장에 대 한 Visual Studio Code 설정을 보여 주는 스크린샷](./media/create-stateful-stateless-workflows-visual-studio-code/azure-logic-apps-preview-settings.png)

<a name="connect-azure-account"></a>

## <a name="connect-to-your-azure-account"></a>Azure 계정에 연결

1. Visual Studio Code 도구 모음에서 Azure 아이콘을 선택합니다.

   ![Visual Studio Code 도구 모음 및 선택한 Azure 아이콘을 보여 주는 스크린샷](./media/create-stateful-stateless-workflows-visual-studio-code/visual-studio-code-azure-icon.png)

1. Azure 창의 **azure: Logic Apps (미리 보기)** 에서 **azure에 로그인**을 선택 합니다. Visual Studio Code 인증 페이지가 표시 되 면 Azure 계정으로 로그인 합니다.

   ![Azure 로그인에 대 한 Azure 창 및 선택한 링크를 보여 주는 스크린샷](./media/create-stateful-stateless-workflows-visual-studio-code/sign-in-azure-subscription.png)

   로그인 한 후 Azure 창에는 Azure 계정에 구독이 표시 됩니다. 공개적으로 릴리스된 Logic Apps 확장을 사용 하는 경우 미리 보기 확장의 **Logic Apps (preview** 섹션이 아닌 릴리스된 확장의 **Logic Apps** 섹션에서 원래 확장을 사용 하 여 만든 원본 Logic Apps 리소스를 찾을 수 있습니다.
   
   예상 되는 구독이 표시 되지 않거나이 창에 특정 구독만 표시 하려면 다음 단계를 수행 합니다.

   1. 구독 목록에서 **구독 선택** 단추 (필터 아이콘)가 나타날 때까지 첫 번째 구독 옆으로 포인터를 이동 합니다. 필터 아이콘을 선택 합니다.

      ![Azure 창 및 선택한 필터 아이콘을 보여 주는 스크린샷](./media/create-stateful-stateless-workflows-visual-studio-code/filter-subscription-list.png)

      또는 Visual Studio Code 상태 표시줄에서 Azure 계정을 선택 합니다. 

   1. 다른 구독 목록이 표시 되 면 원하는 구독을 선택한 다음 **확인**을 선택 했는지 확인 합니다.

<a name="create-project"></a>

## <a name="create-a-local-project"></a>로컬 프로젝트 만들기

논리 앱을 만들기 전에 Visual Studio Code에서 논리 앱을 관리 하 고 배포할 수 있도록 로컬 프로젝트를 만듭니다. 기본 프로젝트는 함수 앱 프로젝트 라고도 하는 Azure Functions 프로젝트와 비슷합니다. 그러나 이러한 프로젝트 형식은 서로 별개 이므로 동일한 프로젝트에 논리 앱 워크플로 및 함수를 사용할 수 없습니다.

1. 컴퓨터에서 나중에 Visual Studio Code에서 만들 프로젝트에 사용할 *빈* 로컬 폴더를 만듭니다.

1. Visual Studio Code에서 열려 있는 모든 폴더를 닫습니다.

1. Azure 창에서 **azure: Logic Apps (미리 보기)** 옆에 있는 **새 프로젝트 만들기** (폴더 및 라이트닝 볼트를 표시 하는 아이콘)를 선택 합니다.

   !["새 프로젝트 만들기"가 선택 된 Azure 창 도구 모음을 보여 주는 스크린샷](./media/create-stateful-stateless-workflows-visual-studio-code/create-new-project-folder.png)

1. Windows Defender 방화벽에서 Azure Functions Core Tools Visual Studio Code 된에 대 한 네트워크 액세스 권한을 부여 하 라는 메시지가 표시 되 면 `Code.exe` `func.exe` **홈 또는 회사 네트워크에서 액세스 허용과 같은 개인 네트워크를** 선택 **>** **Allow access**합니다.

1. 프로젝트 폴더를 만든 위치로 이동 하 여 해당 폴더를 선택 하 고 계속 합니다.

   ![새로 만든 프로젝트 폴더와 "선택" 단추가 선택 된 "폴더 선택" 대화 상자를 보여 주는 스크린샷](./media/create-stateful-stateless-workflows-visual-studio-code/select-project-folder.png)

1. 표시 되는 템플릿 목록에서 **상태 저장 워크플로** 또는 **상태 비저장 워크플로**를 선택 합니다. 이 예제에서는 **상태 저장 워크플로**를 선택 합니다.

   !["상태 저장 워크플로"가 선택 된 워크플로 템플릿 목록을 보여 주는 스크린샷](./media/create-stateful-stateless-workflows-visual-studio-code/select-stateful-stateless-workflow.png)

1. 논리 앱 워크플로의 이름을 제공 하 고 Enter 키를 누릅니다. 이 예에서는 `example-workflow` 를 이름으로 사용 합니다.

   !["새 상태 저장 워크플로 만들기 (3/4)" 상자와 "예제-워크플로"를 워크플로 이름으로 보여 주는 스크린샷](./media/create-stateful-stateless-workflows-visual-studio-code/name-your-workflow.png)

1. 표시 되는 다음 목록에서 **현재 창에서 열기**를 선택 합니다.

   !["현재 창에서 열기"가 선택 된 목록을 보여 주는 스크린샷](./media/create-stateful-stateless-workflows-visual-studio-code/select-project-location.png)

   Visual Studio Code 다시 로드 하면 탐색기 창이 열리고 프로젝트가 자동으로 생성 된 프로젝트 파일을 포함 하는 프로젝트가 표시 됩니다. 예를 들어 프로젝트에 논리 앱 워크플로 이름을 표시 하는 폴더가 있습니다. 이 폴더 내에서 파일 ** 의workflow.js** 에는 논리 앱 워크플로의 기본 JSON 정의가 포함 되어 있습니다.

   ![프로젝트 폴더, 워크플로 폴더 및 "workflow.json" 파일을 포함 하는 탐색기 창을 보여 주는 스크린샷](./media/create-stateful-stateless-workflows-visual-studio-code/local-project-created.png)

<a name="open-workflow-definition-designer"></a>

## <a name="open-the-workflow-definition-file-in-logic-app-designer"></a>논리 앱 디자이너에서 워크플로 정의 파일을 엽니다.

1. 다음 명령을 실행 하 여 컴퓨터에 설치 된 버전을 확인 합니다.

   `..\Users\{yourUserName}\dotnet --list-sdks`

   .NET Core SDK 5.x를 사용 하는 경우이 버전을 사용 하면 디자이너에서 논리 앱의 기본 워크플로 정의를 열 수 없습니다. 이 버전을 제거 하는 대신, 프로젝트의 루트 위치에서 3.1.201 보다 이후 버전의 .NET Core 런타임 2.x 버전을 참조 하는 파일 ** 에global.js** 를 만듭니다. 예를 들면 다음과 같습니다.

   ```json
   {
      "sdk": {
         "version": "3.1.8",
         "rollForward": "disable"
      }
   }
   ```

   파일 ** 의global.js** Visual Studio Code 내부에서 루트 위치의 프로젝트에 추가 해야 합니다. 그렇지 않으면 디자이너가 열리지 않습니다.

1. Visual Studio Code Windows 또는 Linux에서 실행 되는 경우 Azure Storage 에뮬레이터가 실행 중인지 확인 합니다. 자세한 내용은 [필수 구성 요소](#prerequisites)를 참조 하세요.

1. 워크플로에 대 한 프로젝트 폴더를 확장 합니다. 파일의 바로 가기 메뉴에서 **workflow.js** 을 열고 **디자이너에서 열기**를 선택 합니다.

   !["디자이너에서 열기"가 선택 된 파일에 대 한 workflow.js의 탐색기 창 및 바로 가기 창을 보여 주는 스크린샷](./media/create-stateful-stateless-workflows-visual-studio-code/open-definition-file-in-designer.png)

   라는 오류 메시지가 표시 되 면 `Workflow design time could not be started` Azure Storage 에뮬레이터가 실행 되 고 있는지 확인 합니다. 그렇지 않으면 다음과 같은 기타 문제 해결 방법을 시도해 보세요.

   Visual Studio Code에서 preview 확장의 출력을 확인 합니다.

   1. **보기** 메뉴에서 **출력**을 선택 합니다.

   1. **출력** 제목 표시줄의 목록에서 **Azure Logic Apps** 을 선택 하 여 미리 보기 확장에 대 한 출력을 볼 수 있습니다. 예를 들면 다음과 같습니다.

      !["Azure Logic Apps"가 선택 된 Visual Studio Code의 출력 창을 보여 주는 스크린샷](./media/create-stateful-stateless-workflows-visual-studio-code/check-outout-window-azure-logic-apps.png)

   1. 출력을 검토 하 고이 오류 메시지가 표시 되는지 확인 합니다.

      ```text
      A host error has occurred during startup operation '{operationID}'.
      System.Private.CoreLib: The file 'C:\Users\{userName}\AppData\Local\Temp\Functions\
      ExtensionBundles\Microsoft.Azure.Functions.ExtensionBundle.Workflows\1.1.1\bin\
      DurableTask.AzureStorage.dll' already exists.
      Value cannot be null. (Parameter 'provider')
      Application is shutting down...
      Initialization cancellation requested by runtime.
      Stopping host...
      Host shutdown completed.
      ```

      이 오류는 이전에 디자이너를 열려고 시도한 다음 프로젝트를 중단 하거나 삭제 한 경우에 발생할 수 있습니다. 이 오류를 해결 하려면이 위치에서 **Extensionbundles** 폴더를 삭제 **합니다. ..\Users \\ {\AppData\Local\Temp\Functions\ExtensionBundles}** 를 열고 디자이너에서 파일 ** 에workflow.js** 를 다시 엽니다.

1. Azure **에서 커넥터** 사용 목록에서 azure 서비스에 대 한 커넥터 뿐만 아니라 Azure Portal에서 사용할 수 있는 모든 관리 되는 커넥터에 적용 되는 **Azure의 커넥터 사용**을 선택 합니다.

   !["Azure에서 커넥터 사용" 목록과 "Azure에서 커넥터 사용"이 선택 된 탐색기 창을 보여 주는 스크린샷](./media/create-stateful-stateless-workflows-visual-studio-code/use-connectors-from-azure.png)

1. 리소스 그룹 목록에서 **새 리소스 그룹 만들기**를 선택 합니다.

   ![리소스 그룹 목록과 "새 리소스 그룹 만들기"가 선택 된 탐색기 창을 보여 주는 스크린샷](./media/create-stateful-stateless-workflows-visual-studio-code/create-select-resource-group.png)

1. 리소스 그룹의 이름을 입력 하 고 enter 키를 누릅니다. 이 예에서는 `example-logic-app-preview-rg`를 사용합니다.

   ![탐색기 창 및 리소스 그룹 이름 상자를 표시 하는 스크린샷](./media/create-stateful-stateless-workflows-visual-studio-code/enter-name-for-resource-group.png)

1. 위치 목록에서 리소스 그룹 및 리소스를 만드는 데 사용할 [지원 되는 Azure 지역을](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md#available-regions) 찾아 선택 합니다. 이 예제에서는 **미국 서 부**를 사용 합니다.

   > [!IMPORTANT]
   > 모든 지역이 현재 지원 되는 것은 아니지만 더 많은 지역을 추가 하기 위한 업데이트는 진행 중입니다. 지원 되지 않는 지역을 선택 하면 연결 만들기와 같은 문제가 발생할 수 있습니다. 현재 지원 되는 지역에 대 한 미리 보기 확장의 [알려진 문제 GitHub 페이지](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md#available-regions)를 검토 합니다.

   ![위치 목록 및 "미국 서 부 중부"가 선택 된 탐색기 창을 표시 하는 스크린샷](./media/create-stateful-stateless-workflows-visual-studio-code/select-azure-region.png)

   이 단계를 수행한 후 Visual Studio Code 논리 앱 디자이너를 엽니다.

   > [!NOTE]
   > Visual Studio Code 워크플로 디자인 타임 API를 시작 하면 시작 시 몇 초 정도 걸릴 수 있습니다. 이 메시지를 무시 하거나 **확인을**선택할 수 있습니다.

   논리 앱 디자이너가 표시 되 면 **작업 선택** 프롬프트가 디자이너에 표시 되며 기본적으로 선택 되어 **작업 추가** 창이 표시 됩니다.

   ![논리 앱 디자이너를 보여 주는 스크린샷](./media/create-stateful-stateless-workflows-visual-studio-code/workflow-app-designer.png)

1. 다음으로, 워크플로에 [트리거와 작업을 추가](#add-trigger-actions) 합니다.

<a name="add-trigger-actions"></a>

## <a name="add-a-trigger-and-actions"></a>트리거 및 작업 추가

**workflow.js** 파일의 바로 가기 메뉴에서 논리 앱 디자이너를 열면 **작업 선택** 프롬프트가 디자이너에 표시 되 고 기본적으로 선택 됩니다. 이제 트리거 및 작업을 추가 하 여 워크플로 만들기를 시작할 수 있습니다.

이 예제에서 논리 앱 워크플로는이 트리거와 다음 작업을 사용 합니다.

* **HTTP 요청**을 받으면 인바운드 호출 또는 요청을 수신 하 고 다른 서비스 또는 논리 앱이 호출할 수 있는 끝점을 만드는 기본 제공 [요청 트리거입니다](../connectors/connectors-native-reqres.md).

* [Office 365 Outlook 작업](../connectors/connectors-create-api-office365-outlook.md)에서 **전자 메일을 보냅니다**.

* 회신을 보내고 호출자에 게 데이터를 반환 하는 데 사용 하는 기본 제공 [응답 동작](../connectors/connectors-native-reqres.md)입니다.

### <a name="add-the-request-trigger"></a>요청 트리거 추가

1. 디자이너 옆의 **트리거 추가** 창에 있는 **작업 선택** 검색 상자에서 **기본 제공** 이 선택 되어 있는지 확인 하 여 기본적으로 실행 되는 트리거를 선택할 수 있습니다.

1. **작업 선택** 검색 상자에를 입력 하 `when a http request` 고 **HTTP 요청을 받을 때**이름이 지정 된 기본 제공 요청 트리거를 선택 합니다.

   ![논리 앱 디자이너를 보여 주는 스크린샷 및 "HTTP 요청을 받았을 때" 트리거가 선택 된 상태에서 * * 트리거 * * 창을 추가 합니다.](./media/create-stateful-stateless-workflows-visual-studio-code/add-request-trigger.png)

   트리거가 디자이너에 나타나면 트리거의 세부 정보 창이 열리며 트리거의 속성, 설정 및 기타 작업을 표시 합니다.

   !["HTTP 요청을 받았을 때" 트리거를 선택 하 고 트리거 세부 정보 창이 열려 있는 논리 앱 디자이너를 보여 주는 스크린샷](./media/create-stateful-stateless-workflows-visual-studio-code/request-trigger-added-to-designer.png)

   > [!TIP]
   > 세부 정보 창이 표시 되지 않으면 디자이너에서 트리거를 선택 해야 합니다.

1. 디자이너에서 항목을 삭제 해야 하는 경우 다음 단계를 수행 합니다.

   1. 디자이너에서 항목을 선택 하면 오른쪽의 세부 정보 창이 열립니다.

   1. 트리거 또는 작업 이름 옆의 오른쪽 위 모서리에 줄임표 (**...**) 단추가 표시 되도록 충분히 Visual Studio Code 창을 확장 합니다. 

   1. 줄임표 (**...**) 메뉴를 열고 **삭제**를 선택 합니다. 삭제를 확인 하려면 **확인**을 선택 합니다.

      ![디자이너에서 선택한 항목을 표시 하 고 선택 된 줄임표 단추와 "삭제" 옵션을 사용 하 여 선택한 항목을 표시 하는 스크린샷](./media/create-stateful-stateless-workflows-visual-studio-code/delete-item-from-designer.png)

### <a name="add-the-office-365-outlook-action"></a>Office 365 Outlook 작업 추가

1. 디자이너에서 추가한 트리거에서 **새 단계**를 선택 합니다.

   **작업 선택** 프롬프트가 디자이너에 표시 되며 다음 작업을 선택할 수 있도록 **작업 추가 창이** 다시 열립니다.

1. 작업 **추가** 창의 **작업 선택** 검색 상자 아래 **에서 Azure를 선택 하** 여 azure에 배포 된 관리 되는 커넥터에 대 한 작업을 찾아 선택할 수 있습니다.

   이 예제에서는 Office 365 Outlook 작업, **전자 메일 보내기 (V2)** 를 선택 하 고 사용 합니다.

   ![논리 앱 디자이너 및 * * Office 365 Outlook "전자 메일 보내기" 작업을 선택 하 여 * * 작업 추가 * * 창을 보여 주는 스크린샷](./media/create-stateful-stateless-workflows-visual-studio-code/add-send-email-action.png)

1. 작업의 세부 정보 창에서 **로그인** 을 선택 하 여 전자 메일 계정에 대 한 연결을 만들 수 있습니다.

   ![논리 앱 디자이너를 보여 주는 스크린샷 및 "로그인"이 선택 된 * * 전자 메일 보내기 (V2) * * 창](./media/create-stateful-stateless-workflows-visual-studio-code/send-email-action-sign-in.png)

   > [!NOTE]
   > 오류가 발생 하는 경우 `Failed to create connection...` 논리 앱에 대해 현재 지원 되지 않는 지역을 선택 했을 수 있습니다. 더 많은 지역을 추가 하기 위한 업데이트는 진행 중입니다. 현재 지원 되는 지역에 대해 미리 보기 확장의 [알려진 문제 GitHub 페이지](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md#available-regions)를 검토 합니다.

1. Visual Studio Code 전자 메일 계정에 대 한 액세스에 대 한 동의를 묻는 메시지가 표시 되 면 **열기**를 선택 합니다.

   ![액세스를 허용 하는 Visual Studio Code 프롬프트를 보여 주는 스크린샷](./media/create-stateful-stateless-workflows-visual-studio-code/visual-studio-code-open-external-website.png)

   > [!TIP]
   > 이후 프롬프트를 방지 하려면 트러스트 된 도메인 **구성** 을 선택 하 여 인증 페이지를 트러스트 된 도메인으로 추가할 수 있습니다.

1. 다음 프롬프트에 따라 로그인 하 여 액세스를 허용 하 고 Visual Studio Code으로 돌아갈 수 있습니다.

   > [!NOTE]
   > 메시지를 완료 하기 전에 너무 많은 시간이 경과 하면 인증 프로세스 시간이 초과 되 고 실패 합니다. 이 경우 디자이너로 돌아가서 로그인을 다시 시도 하 여 연결을 만듭니다.

1. Azure Logic Apps preview 확장에서 전자 메일 계정에 대 한 액세스에 대 한 동의를 묻는 메시지가 표시 되 면 **열기**를 선택 합니다. 다음 프롬프트에 따라 액세스를 허용 합니다.

   ![액세스를 허용 하는 미리 보기 확장 메시지를 보여 주는 스크린샷](./media/create-stateful-stateless-workflows-visual-studio-code/allow-preview-extension-open-uri.png)

   > [!TIP]
   > 이후 메시지가 표시 되지 않도록 하려면 **이 확장에 대해 다시 묻지 않음**을 선택 합니다.

   Visual Studio Code에서 연결을 만든 후 일부 커넥터는 `The connection will be valid for {n} days only.` Visual Studio Code에서 논리 앱을 작성 하는 동안에만이 시간 제한이 적용 되는 메시지를 표시 합니다. 배포 후에는 논리 앱이 자동으로 사용 하도록 설정 [된 시스템 할당 관리 id](../logic-apps/create-managed-service-identity.md)를 사용 하 여 런타임에 인증할 수 있으므로이 제한이 더 이상 적용 되지 않습니다. 이 관리 id는 연결을 만들 때 사용 하는 인증 자격 증명이 나 연결 문자열과 다릅니다. 이 시스템 할당 관리 id를 사용 하지 않도록 설정 하면 런타임에 연결이 작동 하지 않습니다.

1. 디자이너에서 **전자 메일 보내기** 작업이 선택 되어 있지 않으면 해당 작업을 선택 합니다.

1. 작업의 세부 정보 창에 있는 **매개 변수** 탭에서 작업에 필요한 정보를 제공 합니다. 예를 들면 다음과 같습니다.

   ![Office 365 Outlook "전자 메일 보내기" 작업에 대 한 세부 정보가 포함 된 논리 앱 디자이너를 보여 주는 스크린샷](./media/create-stateful-stateless-workflows-visual-studio-code/send-email-action-details.png)

   | 속성 | 필수 | 값 | Description |
   |----------|----------|-------|-------------|
   | **수행할 작업** | 예 | <*your-email-address*> | 전자 메일 받는 사람으로, 테스트 목적으로 전자 메일 주소를 사용할 수 있습니다. 이 예에서는 가상의 전자 메일를 사용 `sophiaowen@fabrikam.com` 합니다. |
   | **Subject** | 예 | `An email from your example workflow` | 이메일 제목 |
   | **본문** | 예 | `Hello from your example workflow!` | 전자 메일 본문 내용 |
   ||||

   > [!NOTE]
   > **설정**, **다음 이후에 실행**또는 **정적 결과** 탭의 세부 정보 창에서 변경 하려는 경우 탭을 전환 하거나 디자이너로 포커스를 변경 하기 전에 **완료** 를 선택 하 여 이러한 변경 내용을 커밋해야 합니다. 그렇지 않으면 Visual Studio Code 변경 내용을 유지 하지 않습니다. 자세한 내용은 preview 확장의 [알려진 문제 GitHub 페이지](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md)를 참조 하세요.

1. 디자이너에서 **저장**을 선택 합니다.

다음으로 Visual Studio Code에서 로컬로 워크플로를 실행 하 고 디버그 합니다.

<a name="debug-test-locally"></a>

## <a name="run-and-debug-locally"></a>로컬에서 실행 및 디버그

논리 앱을 테스트 하려면 다음 단계를 수행 하 여 디버깅 세션을 시작 하 고 요청 트리거에 의해 만들어진 끝점의 URL을 찾습니다. 나중에 해당 끝점에 요청을 보낼 수 있도록이 URL이 필요 합니다.

1. 상태 비저장 논리 앱 워크플로를 더 쉽게 디버깅할 수 있도록 [해당 워크플로에 대 한 실행 기록을 사용 하도록 설정할](#run-history)수 있습니다.

1. Visual Studio Code 도구 모음에서 **실행** 메뉴를 열고 **디버깅 시작** (F5)을 선택 합니다.

   디버깅 세션을 검토할 수 있도록 **터미널** 창이 열립니다.

1. 이제 요청 트리거에서 끝점에 대 한 콜백 URL을 찾습니다.

   1. 프로젝트를 볼 수 있도록 탐색기 창을 다시 엽니다.

   1. **workflow.js** 파일의 바로 가기 메뉴에서 **개요**를 선택 합니다.

      !["개요"가 선택 된 파일의 workflow.js에 대 한 탐색기 창 및 바로 가기 창을 보여 주는 스크린샷](./media/create-stateful-stateless-workflows-visual-studio-code/open-workflow-overview.png)

   1. **콜백 url** 값을 찾습니다 .이 값은 예제 요청 트리거에 대 한 다음 url과 유사 합니다.

      `http://localhost:7071/api/<workflow-name>/triggers/manual/invoke?api-version=2020-05-01-preview&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=<shared-access-signature>`

      ![콜백 URL을 사용 하 여 워크플로의 개요 페이지를 보여 주는 스크린샷](./media/create-stateful-stateless-workflows-visual-studio-code/find-callback-url.png)

1. 논리 앱 워크플로를 트리거하여 콜백 URL을 테스트 하려면 요청을 만들고 보내기 위한 [Postman](https://www.postman.com/downloads/) 또는 선호 하는 도구를 엽니다.

   이 예는 Postman을 사용 하 여 계속 합니다. 자세한 내용은 [Postman 시작](https://learning.postman.com/docs/getting-started/introduction/)을 참조 하세요.

   1. Postman 도구 모음에서 **새로 만들기**를 선택 합니다.

      ![새 단추가 선택 된 Postman을 보여 주는 스크린샷](./media/create-stateful-stateless-workflows-visual-studio-code/postman-create-request.png)

   1. **새로 만들기** 창의 **구성 요소**아래에서 **요청**을 선택 합니다.

   1. 요청 **저장** 창의 **요청 이름**에서 요청 이름을 제공 합니다 (예:) `Test workflow trigger` .

   1. **저장할 컬렉션 또는 폴더 선택**에서 **컬렉션 만들기**를 선택 합니다.

   1. **모든 컬렉션**아래에서 요청을 구성 하기 위해 만들 컬렉션의 이름을 입력 하 고 enter 키를 누른 다음 ** *컬렉션 이름* > <저장을**선택 합니다. 이 예제에서는 `Logic Apps requests` 를 컬렉션 이름으로 사용 합니다.

      요청 트리거에 대 한 콜백 URL에 요청을 보낼 수 있도록 postman의 요청 창이 열립니다.

      ![열린 요청 창에서 Postman을 보여 주는 스크린샷](./media/create-stateful-stateless-workflows-visual-studio-code/postman-request-pane.png)

   1. Visual Studio Code로 돌아갑니다. 워크플로의 개요 페이지에서 **콜백 URL** 속성 값을 복사 합니다.

   1. Postman으로 돌아갑니다. 요청 창에서 현재 기본 요청 메서드로 **가져오기를** 표시 하는 메서드 목록에서 이전에 주소 상자에 복사한 콜백 URL을 붙여넣은 다음 **보내기**를 선택 합니다.

      ![보내기 단추가 선택 된 주소 상자에 Postman 및 callback URL을 표시 하는 스크린샷](./media/create-stateful-stateless-workflows-visual-studio-code/postman-test-call-back-url.png)

      예제 논리 앱 워크플로는이 예제와 비슷한 전자 메일을 보냅니다.

      ![예제에 설명 된 대로 Outlook 전자 메일을 보여 주는 스크린샷](./media/create-stateful-stateless-workflows-visual-studio-code/workflow-app-result-email.png)

1. Visual Studio Code에서 워크플로의 개요 페이지로 돌아갑니다.

   상태 저장 워크플로를 만든 경우 전송한 요청이 워크플로를 트리거한 후 개요 페이지에 워크플로의 실행 상태 및 기록이 표시 됩니다. 실행 상태에 대 한 자세한 내용은 [실행 기록 검토](../logic-apps/monitor-logic-apps.md#review-runs-history)를 참조 하세요.

   ![실행 상태 및 기록이 포함 된 워크플로의 개요 페이지를 보여 주는 스크린샷](./media/create-stateful-stateless-workflows-visual-studio-code/post-trigger-call.png)

   > [!TIP]
   > 실행 상태가 표시 되지 않으면 **새로 고침**을 선택 하 여 개요 페이지를 새로 고쳐 봅니다.

1. 특정 실행의 각 단계에 대 한 상태와 단계의 입력 및 출력을 검토 하려면 해당 실행에 대 한 줄임표 (**...**) 단추를 선택 하 고 **실행 표시**를 선택 합니다.

   ![줄임표 단추와 "실행 표시"가 선택 된 워크플로의 실행 기록 행을 보여 주는 스크린샷](./media/create-stateful-stateless-workflows-visual-studio-code/show-run-history.png)

   Visual Studio Code 각 작업의 실행 상태를 표시 합니다.

1. 각 단계에 대 한 입력 및 출력을 검토 하려면 검사 하려는 단계를 확장 합니다. 해당 단계에 대 한 원시 입력 및 출력을 추가로 검토 하려면 **원시 입력 표시** 또는 **원시 출력 표시**를 선택 합니다.

   ![워크플로의 각 단계에 대 한 상태와 확장 된 "전자 메일 보내기" 작업의 입력 및 출력을 보여 주는 스크린샷](./media/create-stateful-stateless-workflows-visual-studio-code/run-history-details.png)

1. 디버깅 세션을 중지 하려면 **실행** 메뉴에서 **디버깅 중지** (Shift + F5)를 선택 합니다.

<a name="return-response"></a>

## <a name="return-a-response-to-the-caller"></a>호출자에 게 응답 반환

논리 앱에 요청을 보낸 호출자에 게 응답을 반환 하려면 요청 트리거로 시작 하는 워크플로에 대 한 기본 제공 [응답 작업](../connectors/connectors-native-reqres.md) 을 사용할 수 있습니다.

1. 논리 앱 디자이너의 **전자 메일 보내기** 작업에서 **새 단계**를 선택 합니다.

   **작업 선택** 프롬프트가 디자이너에 표시 되며 다음 작업을 선택할 수 있도록 **작업 추가 창이** 다시 열립니다.

1. 작업 **추가** 창의 **작업 선택** 검색 상자 아래에서 **기본 제공** 이 선택 되어 있는지 확인 합니다. 검색 상자에를 입력 하 `response` 고 **응답** 작업을 선택 합니다.

   ![응답 작업이 선택 된 논리 앱 디자이너를 보여 주는 스크린샷](./media/create-stateful-stateless-workflows-visual-studio-code/add-response-action.png)

   디자이너에 **응답** 동작이 표시 되 면 작업의 세부 정보 창이 자동으로 열립니다.

   !["응답" 작업의 세부 정보 창이 열리고 "본문" 속성이 "전자 메일 보내기" 작업의 "본문" 속성 값으로 설정 된 논리 앱 디자이너를 보여 주는 스크린샷](./media/create-stateful-stateless-workflows-visual-studio-code/response-action-details.png)

1. **매개 변수** 탭에서 호출 하려는 함수에 필요한 정보를 제공 합니다.

   이 예에서는 **전자 메일 보내기** 작업에서 출력 되는 **Body** 속성 값을 반환 합니다.

   1. 동적 콘텐츠 목록이 표시 되도록 **본문** 속성 상자 내부를 클릭 하 고 워크플로의 이전 트리거와 작업에서 사용 가능한 출력 값을 표시 합니다.

      ![동적 콘텐츠 목록이 표시 되도록 "Body" 속성 안에 마우스 포인터가 있는 "응답" 작업의 세부 정보 창을 보여 주는 스크린샷](./media/create-stateful-stateless-workflows-visual-studio-code/open-dynamic-content-list.png)

   1. 동적 콘텐츠 목록의 **전자 메일 보내기**아래에서 **본문**을 선택 합니다.

      ![동적 콘텐츠 열기 목록을 보여 주는 스크린샷 목록에서 "전자 메일 보내기" 헤더에 "Body" 출력 값이 선택 됩니다.](./media/create-stateful-stateless-workflows-visual-studio-code/select-send-email-action-body-output-value.png)

      완료 되 면 응답 작업의 **본문** 속성이 **전자 메일 보내기** 작업의 **본문** 출력 값으로 설정 됩니다.

      ![워크플로의 각 단계에 대 한 상태와 확장 된 "응답" 작업의 입력 및 출력을 보여 주는 스크린샷](./media/create-stateful-stateless-workflows-visual-studio-code/response-action-details-body-property.png)

1. 디자이너에서 **저장**을 선택 합니다.

<a name="retest-workflow"></a>

## <a name="retest-your-logic-app"></a>논리 앱 다시 테스트

논리 앱을 업데이트 한 후에는 [논리 앱을 디버그 및 테스트](#debug-test-locally)하는 단계와 마찬가지로 Visual Studio에서 디버거를 다시 실행 하 고 업데이트 된 논리 앱을 트리거하는 다른 요청을 보내 다른 테스트를 실행할 수 있습니다.

1. Visual Studio Code 도구 모음에서 **실행** 메뉴를 열고 **디버깅 시작** (F5)을 선택 합니다.

1. 요청을 만들고 전송 하는 Postman 또는 도구에서 워크플로를 트리거하기 위한 다른 요청을 보냅니다.

1. 상태 저장 워크플로를 만든 경우 워크플로의 개요 페이지에서 가장 최근 실행에 대 한 상태를 확인 합니다. 해당 실행의 각 단계에 대 한 상태, 입력 및 출력을 보려면 해당 실행에 대 한 줄임표 (**...**) 단추를 선택 하 고 **실행 표시**를 선택 합니다.

   예를 들어 예제 워크플로가 응답 작업으로 업데이트 된 후 실행에 대 한 단계별 상태는 다음과 같습니다.

   ![업데이트 된 워크플로의 각 단계에 대 한 상태와 확장 된 "응답" 작업의 입력 및 출력을 보여 주는 스크린샷](./media/create-stateful-stateless-workflows-visual-studio-code/run-history-details-rerun.png)

1. 디버깅 세션을 중지 하려면 **실행** 메뉴에서 **디버깅 중지** (Shift + F5)를 선택 합니다.

<a name="publish-azure"></a>

## <a name="publish-to-azure"></a>Azure에 게시

Visual Studio Code에서 Azure에 직접 프로젝트를 배포할 수 있습니다. 그러면 새 **논리 앱 (미리 보기)** 리소스 유형을 사용 하 여 논리 앱을 게시 합니다. Azure Functions의 함수 앱 리소스와 마찬가지로,이 새 리소스 종류를 배포 하려면 배포 중에 설정할 수 있는 [호스팅 계획 및 가격 책정 계층](../app-service/overview-hosting-plans.md)을 선택 해야 합니다. 호스팅 계획 및 가격 책정에 대 한 자세한 내용은 다음 항목을 검토 하십시오.

* [Azure App Service에서 확장](../app-service/manage-scale-up.md)
* [Azure Functions 비율 크기 조정 및 호스팅](../azure-functions/functions-scale.md)

논리 앱을 새 리소스로 게시할 수 있습니다. 그러면 [함수 앱 요구 사항과 유사한 Azure Storage 계정과](../azure-functions/storage-considerations.md)같은 추가 필요한 리소스가 자동으로 생성 됩니다. 또는 논리 앱을 해당 논리 앱을 덮어쓰는 이전에 배포 된 **논리 앱 (미리 보기)** 리소스에 게시할 수 있습니다.

### <a name="publish-as-a-new-logic-app-preview-resource"></a>새 논리 앱 (미리 보기) 리소스로 게시

1. Visual Studio Code 도구 모음에서 Azure 아이콘을 선택합니다.

1. **Azure: Logic Apps (미리 보기)** 창 도구 모음에서 **논리 앱에 배포**를 선택 합니다.

   !["논리 앱에 배포"가 선택 된 "Azure: Logic Apps (미리 보기)" 창과 창의 도구 모음을 보여 주는 스크린샷](./media/create-stateful-stateless-workflows-visual-studio-code/deploy-to-logic-app.png)

1. Visual Studio Code 열리는 목록에서 다음 옵션 중에서 선택 합니다.

   * **Azure에서 새 논리 앱 (미리 보기) 만들기** (빠른)
   * **Azure Advanced에서 새 논리 앱 (미리 보기) 만들기**
   * 이전에 배포 된 **논리 앱 (미리 보기) 리소스 (** 있는 경우)

   이 예에서는 **Azure Advanced에서 새 논리 앱 (미리 보기) 만들기**를 계속 진행 합니다.

   !["Azure에서 새 논리 앱 만들기 (미리 보기)"가 선택 된 목록이 포함 된 "Azure: Logic Apps (미리 보기)" 창을 보여 주는 스크린샷](./media/create-stateful-stateless-workflows-visual-studio-code/select-create-logic-app-options.png)

1. 새 **논리 앱 (미리 보기)** 리소스를 만들려면 다음 단계를 수행 합니다.

   1. **논리 앱 (미리 보기)** 리소스에 사용할 이름인 새 논리 앱에 대 한 전역적으로 고유한 이름을 제공 합니다. 이 예에서는 `example-logic-app-preview`를 사용합니다.

      !["Azure: Logic Apps (미리 보기)" 창을 표시 하는 스크린샷 및 만들 새 논리 앱의 이름을 입력 하 라는 메시지가 표시 됩니다.](./media/create-stateful-stateless-workflows-visual-studio-code/enter-logic-app-name.png)

   1. 새 논리 앱에 대 한 호스팅 계획을 선택 합니다 ( [**App Service 계획**](../azure-functions/functions-scale.md#app-service-plan) 또는 [**프리미엄**](../azure-functions/functions-scale.md#premium-plan)). 이 예에서는 **App Service 계획**을 선택 합니다.

      !["Azure: Logic Apps (미리 보기)" 창을 표시 하 고 "App Service Plan" 또는 "Premium"을 선택 하 라는 메시지를 표시 하는 스크린샷](./media/create-stateful-stateless-workflows-visual-studio-code/select-hosting-plan.png)

   1. 새 App Service 계획을 만들거나 기존 계획을 선택 합니다. 이 예에서는 **새 App Service 계획 만들기**를 선택 합니다.

      !["Azure: Logic Apps (미리 보기)" 창 및 "새 App Service 계획 만들기"에 대 한 프롬프트를 보여 주는 스크린샷 또는 기존 App Service 계획을 선택 합니다.](./media/create-stateful-stateless-workflows-visual-studio-code/create-app-service-plan.png)

   1. App Service 계획의 이름을 입력 한 다음 계획에 대 한 [가격 책정 계층](../app-service/overview-hosting-plans.md) 을 선택 합니다. 이 예에서는 **F1 무료** 요금제를 선택 합니다.

      !["Azure: Logic Apps (미리 보기)" 창을 표시 하는 스크린샷 및 가격 책정 계층을 선택 하 라는 메시지가 표시 됩니다.](./media/create-stateful-stateless-workflows-visual-studio-code/select-pricing-tier.png)

   1. 성능을 최적화 하려면 배포에 대 한 프로젝트와 동일한 리소스 그룹을 찾아 선택 합니다.

      > [!NOTE]
      > 다른 리소스 그룹을 만들거나 사용할 수 있지만 이렇게 하면 성능에 영향을 줄 수 있습니다. 다른 리소스 그룹을 만들거나 선택 하 고 확인 메시지가 표시 되 면 취소를 선택 하면 배포도 취소 됩니다.

   1. 상태 저장 워크플로의 경우 **새 저장소 계정 만들기** 또는 기존 저장소 계정을 선택 합니다.

      !["Azure: Logic Apps (미리 보기)" 창이 표시 되 고 저장소 계정을 만들거나 선택 하 라는 메시지가 표시 되는 스크린샷](./media/create-stateful-stateless-workflows-visual-studio-code/create-storage-account.png)

   1. 더 쉬운 진단 로깅 및 추적 기능을 위해 기존 Application Insights 리소스를 선택할 수 있습니다. 그렇지 않으면 **새 Application Insights 리소스 만들기**를 선택 하거나 앱을 배포한 후 Azure Portal에서 Application Insights를 설정할 수 있습니다.

      를 배포 하기 전에 `logLevel` `logging` 프로젝트의 루트 수준에 있는 파일의 **host.js** 개체에 개체를 추가 하 고를로 설정 해야 합니다 `Host.Triggers.Workflow` `Information` . 예를 들면 다음과 같습니다.

      ```json
      "logLevel": {
         "Host.Triggers.Workflow": "Information"
      },
      ```

      파일 ** 의host.js** 표시 되는 방법은 다음과 같습니다.

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

   작업이 완료 되 면 논리 앱 Visual Studio Code을 게시 하는 데 필요한 리소스를 만들고 배포 하기 시작 합니다.

1. 배포 프로세스를 검토 하 고 모니터링 하려면 **보기** 메뉴에서 **출력**을 선택 합니다. 출력 창 도구 모음 목록에서 **Azure Logic Apps**을 선택 합니다.

   ![배포 진행률 및 상태와 함께 도구 모음 목록에서 "Azure Logic Apps"가 선택 된 상태에서 출력 창을 보여 주는 스크린샷](./media/create-stateful-stateless-workflows-visual-studio-code/logic-app-deployment-output-window.png)

   Visual Studio Code 논리 앱 워크플로를 Azure에 배포 하는 작업이 완료 되 면 다음 메시지가 표시 됩니다.

   ![Azure에 배포가 성공적으로 완료 되었다는 메시지를 보여 주는 스크린샷](./media/create-stateful-stateless-workflows-visual-studio-code/deployment-to-azure-completed.png)

   축 하 합니다. 이제 논리 앱은 Azure에서 라이브 되며 기본적으로 사용 하도록 설정 됩니다.

다음으로 이러한 작업을 수행 하는 방법을 배울 수 있습니다.

* Azure Portal 또는 [Visual Studio Code](#find-manage-deployed-workflows-vs-code)에서 [배포 된 논리 앱을 찾습니다](#find-manage-deployed-workflows-portal) .

* [상태 비저장 논리 앱 워크플로에서 실행 기록을 사용 하도록 설정](#run-history)합니다.

* [배포 된 **논리 앱 (미리 보기)** 리소스에서 모니터링을 사용 하도록 설정](#enable-monitoring)합니다.

<a name="find-manage-deployed-workflows-vs-code"></a>

## <a name="find-and-manage-deployed-logic-apps-in-visual-studio-code"></a>Visual Studio Code에서 배포 된 논리 앱 찾기 및 관리

Visual Studio Code은 Azure 구독에서 배포 된 모든 논리 앱 (원래 **Logic Apps** 인지 아니면 **논리 앱 (미리 보기)** 리소스 종류)을 볼 수 있으며 해당 논리 앱을 관리 하는 데 도움이 되는 작업을 선택할 수 있습니다. 그러나 두 리소스 유형 모두에 액세스 하려면 Visual Studio Code에 대 한 **Azure Logic Apps** 및 **Azure Logic Apps (미리 보기)** 확장이 모두 필요 합니다.

1. 왼쪽 도구 모음에서 Azure 아이콘을 선택 합니다. **Azure: Logic Apps (미리 보기)** 창에서 구독을 확장 합니다. 그러면 해당 구독에 대해 배포 된 모든 논리 앱이 표시 됩니다.

1. 관리 하려는 논리 앱을 찾아 선택 합니다. 논리 앱의 바로 가기 메뉴를 열고 수행 하려는 작업을 선택 합니다.

   예를 들어 배포 된 논리 앱 중지, 시작, 다시 시작 또는 삭제와 같은 작업을 선택할 수 있습니다.

   ![열린 "Azure Logic Apps (미리 보기)" 확장 창 및 배포 된 워크플로를 사용 하 여 Visual Studio Code를 보여 주는 스크린샷](./media/create-stateful-stateless-workflows-visual-studio-code/find-deployed-workflow-visual-studio-code.png)

1. 논리 앱의 모든 워크플로를 보려면 논리 앱을 확장 한 다음 **워크플로** 노드를 확장 합니다.

1. 특정 워크플로를 보려면 워크플로의 바로 가기 메뉴를 열고 **디자이너에서 열기**를 선택 하 여 읽기 전용 모드로 워크플로를 엽니다.

   워크플로를 편집 하려면 다음 옵션을 사용할 수 있습니다.

   * Visual Studio Code에서 논리 앱 디자이너의 파일 ** 에 있는 프로젝트workflow.js** 를 열고, 편집 하 고, 논리 앱을 Azure에 다시 배포 합니다.

   * Azure Portal에서 [논리 앱을 찾아서 엽니다](#find-manage-deployed-workflows-portal). 워크플로를 찾고, 편집 하 고, 저장 합니다.

1. Azure Portal에서 배포 된 논리 앱을 열려면 논리 앱의 바로 가기 메뉴를 열고 **포털에서 열기**를 선택 합니다.

   Azure Portal 브라우저에서 열리고 Visual Studio Code에 로그인 하 고 논리 앱을 표시 하는 경우 자동으로 포털에 로그인 합니다.

   ![Visual Studio Code에서 논리 앱에 대 한 Azure Portal 페이지를 보여 주는 스크린샷](./media/create-stateful-stateless-workflows-visual-studio-code/deployed-workflow-azure-portal.png)

   Azure Portal에 별도로 로그인 하 고 포털 검색 상자를 사용 하 여 논리 앱을 찾은 다음, 결과 목록에서 논리 앱을 선택할 수도 있습니다.

   ![선택 된 것으로 표시 되는 배포 된 논리 앱에 대 한 검색 결과가 포함 된 검색 표시줄과 Azure Portal를 보여 주는 스크린샷](./media/create-stateful-stateless-workflows-visual-studio-code/find-deployed-workflow-azure-portal.png)

<a name="find-manage-deployed-workflows-portal"></a>

## <a name="find-and-manage-deployed-logic-apps-in-the-portal"></a>포털에서 배포 된 논리 앱 찾기 및 관리

Azure Portal에서 Azure 구독에 있는 모든 배포 된 논리 앱 (원래 **Logic Apps** 리소스 유형 인지 또는 **논리 앱 (미리 보기)** 리소스 유형)을 볼 수 있습니다. 현재 각 리소스 종류는 Azure에서 별도의 범주로 구성 되 고 관리 됩니다.

> [!NOTE]
> 공개 미리 보기의 경우에는 배포 된 **논리 앱 (미리 보기)** 리소스만 Azure Portal에서 볼 수 있으며 새 **논리 앱 (미리 보기)** 리소스를 만들 수는 없습니다. 이러한 논리 앱은 Visual Studio Code 에서만 만들 수 있습니다. 그러나이 리소스 종류를 사용 하 여 배포 된 논리 앱에 [워크플로를 추가할](#add-workflows) 수 있습니다.

**논리 앱 (미리 보기)** 리소스 종류를 포함 하는 논리 앱을 찾으려면 다음 단계를 수행 합니다.

1. Azure Portal 검색 상자에을 입력 `logic app preview` 합니다. 결과 목록이 표시 되 면 **서비스**에서 **논리 앱 (미리 보기)** 을 선택 합니다.

   !["논리 앱 미리 보기" 검색 텍스트를 사용 하 여 Azure Portal 검색 상자를 보여 주는 스크린샷](./media/create-stateful-stateless-workflows-visual-studio-code/portal-find-logic-app-preview-resource.png)

1. **논리 앱 (미리 보기)** 창에서 Visual Studio Code에서 배포한 논리 앱을 찾아 선택 합니다.

   ![Azure Portal 및 Azure에 배포 된 논리 앱 (미리 보기) 리소스를 보여 주는 스크린샷](./media/create-stateful-stateless-workflows-visual-studio-code/logic-app-preview-resources-pane.png)

   Azure Portal 선택한 논리 앱에 대 한 개별 리소스 페이지를 엽니다.

   ![Azure Portal에서 논리 앱 워크플로의 리소스 페이지를 보여 주는 스크린샷](./media/create-stateful-stateless-workflows-visual-studio-code/deployed-workflow-azure-portal.png)

1. 이 논리 앱에서 워크플로를 보려면 논리 앱의 메뉴에서 **워크플로**를 선택 합니다.

   **워크플로** 창에는 현재 논리 앱의 모든 워크플로가 표시 됩니다. 이 예제에서는 Visual Studio Code에서 만든 워크플로를 보여 줍니다.

   !["워크플로" 창이 열리고 배포 된 워크플로를 사용 하는 "논리 앱 (미리 보기)" 리소스 페이지를 보여 주는 스크린샷](./media/create-stateful-stateless-workflows-visual-studio-code/deployed-logic-app-workflows-pane.png)

1. **워크플로를 보려면 워크플로 창에서** 해당 워크플로를 선택 합니다.

   워크플로 창이 열리고 해당 워크플로에서 수행할 수 있는 추가 정보 및 작업이 표시 됩니다.

   예를 들어 워크플로의 단계를 보려면 **디자이너**를 선택 합니다.

   ![선택한 워크플로의 "개요" 창을 보여 주는 스크린샷, 워크플로 메뉴에는 선택한 "Designer" 명령이 표시 됩니다.](./media/create-stateful-stateless-workflows-visual-studio-code/workflow-overview-pane-select-designer.png)

   논리 앱 디자이너가 열리고 Visual Studio Code에서 작성 한 워크플로가 표시 됩니다. 이제 Azure Portal에서이 워크플로를 변경할 수 있습니다.

   ![Visual Studio Code에서 배포 된 논리 앱 디자이너 및 워크플로를 보여 주는 스크린샷](./media/create-stateful-stateless-workflows-visual-studio-code/opened-workflow-designer.png)

<a name="add-workflows"></a>

## <a name="add-a-workflow-to-deployed-logic-apps"></a>배포 된 논리 앱에 워크플로 추가

Azure Portal를 통해 Visual Studio Code에서 배포한 **논리 앱 (미리 보기)** 리소스에 빈 워크플로를 추가 하 고 Azure Portal에서 해당 워크플로를 빌드할 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에서 배포 된 **논리 앱 (미리 보기)** 리소스를 찾아 선택 합니다.

1. 논리 앱의 메뉴에서 **워크플로**를 선택 합니다. **워크플로** 창에서 **추가**를 선택 합니다.

   ![선택한 논리 앱의 "워크플로" 창 및 "추가" 명령이 선택 된 도구 모음을 보여 주는 스크린샷](./media/create-stateful-stateless-workflows-visual-studio-code/add-new-workflow.png)

1. **새 워크플로** 창에서 워크플로의 이름을 입력 합니다. **상태 저장** 또는 **상태 비저장** **>** **만들기**를 선택 합니다.

   Azure가 **워크플로 창에 표시 되는 새** 워크플로를 배포한 후 해당 워크플로를 선택 하 여 논리 앱 디자이너 또는 코드 보기 열기와 같은 관리 및 기타 작업을 수행 합니다.

   ![관리 및 검토 옵션으로 선택한 워크플로를 보여 주는 스크린샷](./media/create-stateful-stateless-workflows-visual-studio-code/view-new-workflow.png)

   예를 들어 새 워크플로에 대 한 디자이너를 열면 빈 캔버스가 표시 됩니다. 이제 Azure Portal에서이 워크플로를 빌드할 수 있습니다.

   ![논리 앱 디자이너와 빈 워크플로를 보여 주는 스크린샷](./media/create-stateful-stateless-workflows-visual-studio-code/opened-blank-workflow-designer.png)

<a name="run-history"></a>

## <a name="run-history-for-stateless-logic-app-workflows"></a>상태 비저장 논리 앱 워크플로에 대 한 실행 기록

상태 비저장 논리 앱 워크플로를 더 쉽게 디버깅 하기 위해 Visual Studio Code 또는 Azure Portal에서 해당 워크플로에 대 한 실행 기록을 사용 하도록 설정한 다음 완료 되 면 실행 기록을 사용 하지 않도록 설정할 수 있습니다.

### <a name="for-a-stateless-logic-app-workflow-in-visual-studio-code"></a>상태 비저장 논리 앱 워크플로 Visual Studio Code

Visual Studio Code에서 로컬로 상태 비저장 논리 앱 워크플로를 작업 하 고 실행 하는 경우 다음 단계를 수행 합니다.

1. 프로젝트에서 **워크플로 designtime** 폴더를 찾아 확장 합니다. 파일 ** 에서local.settings.js** 를 찾아 엽니다.

1. 속성을 추가 하 `Workflows.{yourWorkflowName}.operationOptions` 고 값을로 설정 합니다 `WithStatelessRunHistory` . 예를 들면 다음과 같습니다.

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

1. 완료 되 면 실행 기록을 사용 하지 않도록 설정 하려면 `Workflows.{yourWorkflowName}.OperationOptions` 속성 및 해당 값을 삭제 하거나 속성을로 설정 `None` 합니다.

### <a name="for-a-stateless-logic-app-workflow-in-the-azure-portal"></a>Azure Portal에서 상태 비저장 논리 앱 워크플로

프로젝트를 Azure Portal에 이미 배포한 경우에는 다음 단계를 수행 합니다.

1. [Azure Portal](https://portal.azure.com)에서 **논리 앱 (미리 보기)** 리소스를 찾아 엽니다.

1. 논리 앱 메뉴의 **설정**에서 **구성**을 선택 합니다.

1. **응용 프로그램 설정** 탭에서 **새 응용 프로그램 설정**을 선택 합니다.

1. **응용 프로그램 설정 추가/편집** 창의 **이름** 상자에 다음 작업 옵션 이름을 입력 합니다. 

   `Workflows.{yourWorkflowName}.OperationOptions`

1. **값** 상자에 다음 값을 입력 합니다.`WithStatelessRunHistory`

   예를 들면 다음과 같습니다.

   !["구성" > "새 응용 프로그램 설정" < "새 응용 프로그램 설정", "응용 프로그램 설정 추가/편집" 창이 열리고 "워크플로를 사용 하 여 Azure Portal 및 논리 앱 (미리 보기) 리소스를 보여 주는 스크린샷 {{Workflowname}. OperationOptions "옵션을" WithStatelessRunHistory "로 설정 합니다.](./media/create-stateful-stateless-workflows-visual-studio-code/stateless-operation-options-run-history.png)

1. 완료되면 **확인**을 선택합니다. **구성** 창에서 **저장**을 선택 합니다.

배포 된 논리 앱 (미리 보기) 리소스에 대 한 모니터링을 사용 하도록 설정 하려면 다음 섹션을 계속 진행 합니다.

<a name="enable-monitoring"></a>

## <a name="enable-monitoring-for-deployed-logic-app-preview-resources"></a>배포 된 논리 앱 (미리 보기) 리소스에 대 한 모니터링 사용

배포 된 **논리 앱 (미리 보기)** 리소스에 대 한 모니터링을 사용 하도록 설정 하려면 다음 단계를 수행 합니다.

1. [Azure Portal](https://portal.azure.com)에서 배포 된 **논리 앱 (미리 보기)** 리소스를 찾아 선택 합니다.

1. 해당 리소스의 메뉴에 있는 **API**에서 **CORS**를 선택 합니다.

1. **CORS** 창의 **허용 된 원본**아래에서 와일드 카드 문자 (*)를 추가 합니다.

1. 완료 되 면 **CORS** 도구 모음에서 **저장**을 선택 합니다.

   ![배포 된 Logic Apps (미리 보기) 리소스를 사용 하 여 Azure Portal를 보여 주는 스크린샷 [리소스] 메뉴에서 "CORS"는 와일드 카드 "*" 문자로 설정 된 "허용 된 원본"의 새 항목과 함께 선택 됩니다.](./media/create-stateful-stateless-workflows-visual-studio-code/enable-run-history-deployed-logic-app.png)

<a name="deploy-docker"></a>

## <a name="deploy-to-docker-container"></a>Docker 컨테이너에 배포

[.Net CORE CLI (명령줄 인터페이스) 도구](/dotnet/core/tools/)를 사용 하 여 프로젝트를 빌드한 다음 빌드를 게시할 수 있습니다. 그런 다음 논리 앱 워크플로를 배포 하기 위한 대상으로 [Docker 컨테이너](/visualstudio/docker/tutorials/docker-tutorial#what-is-a-container) 를 빌드하고 사용할 수 있습니다. 자세한 내용은 다음 항목을 검토 하십시오.

* [컨테이너 및 Docker 소개](/dotnet/architecture/microservices/container-docker-introduction/)
* [.NET 및 Docker 소개](/dotnet/core/docker/introduction)
* [Docker 용어](/dotnet/architecture/microservices/container-docker-introduction/docker-terminology)
* [자습서: Docker 시작](/visualstudio/docker/tutorials/docker-tutorial)

1. 프로젝트를 빌드하려면 명령줄 프롬프트를 열고 다음 명령을 실행 합니다.

   `dotnet build -c release`

   자세한 내용은 [dotnet 빌드](/dotnet/core/tools/dotnet-build/) 참조 페이지를 참조 하세요.

1. 다음 명령을 실행 하 여 빌드를 게시 합니다.

   `dotnet publish -c release`

   자세한 내용은 [dotnet publish](/dotnet/core/tools/dotnet-publish/) 참조 페이지를 참조 하세요.

1. .NET 워크플로에 대 한 Docker 파일을 사용 하 고 다음 명령을 실행 하 여 Docker 컨테이너를 빌드합니다.

   `docker build --tag local/workflowcontainer .`

   예를 들어 다음은 상태 저장 논리 앱을 배포 하 고 논리 앱을 Azure Portal에 게시 하는 데 사용 된 Azure Storage 계정에 대 한 연결 문자열을 지정 하는 샘플 Docker 파일입니다. Azure Portal의 저장소 계정 연결 문자열을 찾아서 복사 하려면 [저장소 계정 키 관리](../storage/common/storage-account-keys-manage.md?tabs=azure-portal#view-account-access-keys)를 검토 하세요.

   ![저장소 계정 액세스 키와 연결 문자열이 복사 된 Azure Portal를 보여 주는 스크린샷](./media/create-stateful-stateless-workflows-visual-studio-code/find-storage-account-connection-string.png)

   연결 문자열은 다음 샘플과 유사 합니다.

   `DefaultEndpointsProtocol=https;AccountName=fabrikamstorageaccount;AccountKey={access-key};EndpointSuffix=core.windows.net`

   Docker 파일의 형식은 다음과 같습니다.


   ```text
   FROM mcr.microsoft.com/azure-functions/dotnet:3.0.14492-appservice

   ENV AzureWebJobsStorage <storage-account-connection-string>
   ENV AZURE_FUNCTIONS_ENVIRONMENT Development
   ENV AzureWebJobsScriptRoot=/home/site/wwwroot
   ENV AzureFunctionsJobHost__Logging__Console__IsEnabled=true
   ENV FUNCTIONS_V2_COMPATIBILITY_MODE=true

   COPY ./bin/Release/netcoreapp3.1/publish/ /home/site/wwwroot
   ```

   자세한 내용은 [docker 빌드](https://docs.docker.com/engine/reference/commandline/build/)를 참조 하세요.

1. 나중에 Visual Studio Code에서 논리 앱을 만드는 데 사용 하는 프로젝트의 파일 ** 에local.settings.js** 문자열을 추가할 수 있도록 문자열을 안전한 위치에 저장 합니다.

1. 다음 명령을 사용 하 여 컨테이너를 로컬로 실행 합니다.

   `docker run -e WEBSITE_HOSTNAME=localhost -p 8080:80 local/workflowcontainer`

   자세한 내용은 [docker run](https://docs.docker.com/engine/reference/commandline/run/)항목을 참조 하세요.

1. 요청 트리거에 대 한 콜백 URL을 가져오려면 다음 요청을 보냅니다.

   `POST /runtime/webhooks/workflow/api/management/workflows/{workflow-name}/triggers/{trigger-name}/listCallbackUrl?api-version=2019-10-01-edge-preview&code={master-key}`

   <*마스터 키*> 값은 `AzureWebJobsStorage` 이 섹션의 값을 찾을 수 있는 **azure-webjobs/{deployment-name} host.js/on**파일에서에 대해 설정한 azure storage 계정에 정의 됩니다.

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

<a name="nested-behavior"></a>

## <a name="nested-behavior-differences-between-stateful-and-stateless-logic-apps"></a>상태 저장 논리 앱과 상태 비저장 논리 앱의 중첩 된 동작 차이점

[ApiConnectionWehook 형식을](../logic-apps/logic-apps-workflow-actions-triggers.md#apiconnectionwebhook-trigger) 사용 하 고 HTTPS 요청을 받을 수 있는 [요청](../connectors/connectors-native-reqres.md) 트리거, [HTTP Webhook](../connectors/connectors-native-webhook.md) 트리거 또는 관리 되는 커넥터 트리거를 사용 하 여 동일한 **논리 앱 (미리 보기)** 리소스에 있는 다른 논리 앱 워크플로에서 호출할 수 있는 [논리 앱 워크플로를 만들](../logic-apps/logic-apps-http-endpoint.md) 수 있습니다.

부모 워크플로를 통해 자식 워크플로를 호출한 후 중첩 된 논리 앱 워크플로에서 수행할 수 있는 동작 패턴은 다음과 같습니다.

* 비동기 폴링 패턴

  부모는 초기 호출에 대 한 응답을 기다리지 않고 자식의 실행이 완료 될 때까지 계속 해 서 자식의 실행 기록을 확인 합니다. 기본적으로 상태 저장 워크플로는이 패턴을 따릅니다 .이 패턴은 [요청 시간 제한 제한을](../logic-apps/logic-apps-limits-and-config.md)초과할 수 있는 장기 실행 자식 워크플로에 적합 합니다.

* 동기 패턴 ("발생 및 잊어버린")

  자식은 즉시 응답을 반환 하 여 호출을 승인 `202 ACCEPTED` 하 고 부모는 자식의 결과를 기다리지 않고 다음 작업을 계속 합니다. 대신, 부모는 자식의 실행이 완료 될 때 결과를 받습니다. 응답 동작을 포함 하지 않는 자식 상태 저장 워크플로는 항상 동기 패턴을 따릅니다. 자식 상태 저장 워크플로의 경우 실행 기록을 검토할 수 있습니다.

  이 동작을 사용 하도록 설정 하려면 워크플로의 JSON 정의에서 `operationOptions` 속성을로 설정 합니다 `DisableAsyncPattern` . 자세한 내용은 [트리거 및 작업 형식-작업 옵션](../logic-apps/logic-apps-workflow-actions-triggers.md#operation-options)을 참조 하세요.

* 트리거 및 대기

  자식 상태 비저장 워크플로의 경우 부모는 자식에서 결과를 반환 하는 응답을 기다립니다. 이 패턴은 기본 제공 [HTTP 트리거 또는 작업](../connectors/connectors-native-http.md) 을 사용 하 여 자식 워크플로를 호출 하는 것과 유사 하 게 작동 합니다. 응답 작업을 포함 하지 않는 자식 상태 비저장 워크플로는 즉시 `202 ACCEPTED` 응답을 반환 하지만, 부모는 자식이 완료 될 때까지 대기한 다음 작업을 계속 합니다. 이러한 동작은 자식 상태 비저장 워크플로에만 적용 됩니다.

이 테이블은 부모 및 자식이 상태 저장 인지, 상태 비저장 인지 또는 혼합 워크플로 유형 인지에 따라 자식 워크플로의 동작을 지정 합니다.

| 부모 워크플로 | 하위 워크플로 | 자식 동작 |
|-----------------|----------------|----------------|
| 상태 저장 | 상태 저장 | 설정으로 비동기 또는 동기 `"operationOptions": "DisableAsyncPattern"` |
| 상태 저장 | 상태 비저장 | 트리거 및 대기 |
| 상태 비저장 | 상태 저장 | 동기 |
| 상태 비저장 | 상태 비저장 | 트리거 및 대기 |
||||

## <a name="limits"></a>제한

[Azure Logic Apps에 대 한 많은 기존 제한이](../logic-apps/logic-apps-limits-and-config.md) 이 리소스 유형에 대해 동일 하지만,이 공개 미리 보기 확장의 차이점은 다음과 같습니다.

* 관리 커넥터: 연결당 분당 50 요청 수

* JavaScript 동작 [에 대 한 인라인 코드 작업](../logic-apps/logic-apps-add-run-inline-code.md) 의 경우 다음과 같은 제한 사항이 변경 되었습니다.

  * 코드 문자의 제한은 1024 자에서 10만 자로 늘어납니다.

  * 코드 실행 시간 제한이 5 초에서 15 초로 늘어납니다.

## <a name="next-steps"></a>다음 단계

이 공개 미리 보기 확장의 경험에 대 한 의견을 듣고 싶습니다.

* 버그 또는 문제의 경우 [GitHub에서 문제를 만듭니다](https://github.com/Azure/logicapps/issues).
* 질문, 요청, 의견 및 기타 피드백은 [이 사용자 의견 양식을 사용](https://aka.ms/lafeedback)합니다.
