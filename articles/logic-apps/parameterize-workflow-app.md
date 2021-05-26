---
title: 단일 테넌트 Azure Logic Apps의 워크플로에 대한 매개 변수 만들기
description: 단일 테넌트 Azure Logic Apps 배포 환경의 워크플로에서 달라지는 값의 매개 변수를 정의합니다.
services: logic-apps
ms.suite: integration
ms.reviewer: azla
ms.topic: how-to
ms.date: 05/25/2021
ms.openlocfilehash: e9e29a091608be54c806a98323b9e485bc7a49a8
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110388197"
---
# <a name="create-parameters-for-values-that-change-in-workflows-across-environments-for-single-tenant-azure-logic-apps"></a>단일 테넌트 Azure Logic Apps에 대한 환경의 워크플로에서 변하는 값의 매개 변수 만들기

Azure Logic Apps에서 매개 변수를 사용하여 환경에 따라 변할 수 있는 값을 추출할 수 있습니다. 워크플로에서 사용할 매개 변수를 정의하면 먼저 워크플로 설계에 집중하고 나중에 환경별 변수를 삽입할 수 있습니다.

*다중 테넌트* Azure Logic Apps에서는 워크플로 디자이너에서 매개 변수를 만들고 참조한 다음, ARM(Azure Resource Manager) 템플릿 및 매개 변수 파일에서 변수를 설정할 수 있습니다. 매개 변수는 배포 시 정의하고 설정합니다. 따라서 변수 하나만 변경해야 하는 경우에도 논리 앱의 ARM 템플릿을 다시 배포해야 합니다.

*단일 테넌트* Azure Logic Apps에서는 매개 변수 및 앱 설정을 사용하여 런타임과 배포 시에 환경 변수를 작업할 수 있습니다. 이 문서에서는 새로운 단일 테넌트 매개 변수 환경을 사용하여 환경 변수를 편집, 호출 및 참조하는 방법을 보여줍니다.

## <a name="prerequisites"></a>사전 요구 사항

- Azure 계정 및 구독 구독이 없는 경우 [Azure 체험 계정에 등록](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)합니다.

- [단일 테넌트 Azure Logic Apps에 호스트되는 논리 앱 워크플로](single-tenant-overview-compare.md).

  논리 앱이 없는 경우 Azure Portal 또는 [Visual Studio Code](create-single-tenant-workflows-visual-studio-code.md)에서 [논리 앱(표준)을 만듭니다](create-single-tenant-workflows-azure-portal.md).

## <a name="parameters-versus-app-settings"></a>매개 변수와 앱 설정 비교

환경 변수를 저장할 위치를 결정하기 전에 다음 정보를 검토하세요.

이미 Azure Functions 또는 Azure Web Apps를 사용하는 경우 앱 설정에 익숙할 수도 있습니다. Azure Logic Apps에서 앱 설정은 Azure Key Vault와 통합됩니다. 연결 문자열 및 키와 같은 [보안 문자열을 직접 참조](../app-service/app-service-key-vault-references.md)할 수 있습니다. 배포 시 환경 변수를 정의할 수 있는 ARM 템플릿과 마찬가지로 [논리 앱 워크플로 정의](/azure/templates/microsoft.logic/workflows) 내에서 앱 설정을 정의할 수 있습니다. 그런 다음, 연결 엔드포인트, 스토리지 문자열 등 동적으로 생성된 인프라 값을 캡처할 수 있습니다. 단, 앱 설정에는 크기 제한이 있으며 Azure Logic Apps의 특정 영역에서는 참조가 불가능합니다.

다중 테넌트 Azure Logic Apps의 워크플로에 익숙한 분들 중에는 매개 변수에 익숙한 분들도 있을 것입니다. 복합 개체 및 큰 크기의 값을 지원하는 등 앱 설정보다 광범위한 사용 사례에서 매개 변수를 사용할 수 있습니다. Visual Studio Code를 로컬 개발 도구로 사용하면 논리 앱 프로젝트의 **workflow.json** 및 **connections.json** 파일에서 매개 변수를 참조할 수도 있습니다. 솔루션에서 두 옵션을 모두 사용하려면 매개 변수를 사용하여 앱 설정도 참조하면 됩니다.

> [!NOTE]
> 개발의 경우 **connections.json** 파일을 매개 변수화하면 디자이너 환경이 로컬과 Azure Portal 모두에서 제한됩니다. 개발에 디자이너를 사용해야 하는 경우 매개 변수화되지 않은 **connections.json** 파일을 사용합니다. 그런 다음, 배포 파이프라인에서 매개 변수화된 파일로 바꿉니다. 런타임은 여전히 매개 변수화와 함께 작동합니다. 디자이너 개선 사항은 개발 중입니다.

매개 변수화의 기본 메커니즘으로 매개 변수를 사용하는 것이 좋습니다. 그러면 보안 키 또는 문자열을 저장해야 할 때 권장 사항에 따라 매개 변수의 앱 설정을 참조할 수 있습니다.

## <a name="what-is-parameterization"></a>매개 변수화란?

Visual Studio Code를 사용하는 경우 논리 앱 프로젝트의 **parameters.json** 파일에서 매개 변수를 정의할 수 있습니다. 논리 앱의 모든 워크플로 또는 연결 개체에서 **parameters.json** 파일의 모든 매개 변수를 참조할 수 있습니다. 단일 테넌트 Azure Logic Apps의 워크플로 입력 매개 변수화는 다중 테넌트 Azure Logic Apps와 비슷한 방식으로 작동합니다.

트리거 또는 작업 입력에서 매개 변수를 참조하려면 `@parameters('<parameter-name>')` 식을 사용합니다.

> [!IMPORTANT]
> **parameters.json** 파일에서 참조하는 매개 변수도 포함해야 합니다.

*단일 테넌트* Azure Logic Apps에서 **connections.json** 파일의 여러 부분을 매개 변수화할 수 있습니다. 그런 다음, **connections.json** 파일을 소스 제어에 체크 인하고 **parameters.json** 파일을 통해 모든 연결을 관리할 수 있습니다. **connections.json** 파일을 매개 변수화하려면 `ConnectionRuntimeUrl` 같은 리터럴 값을 단일 `parameters()` 식(예: `@parameters('api-runtimeUrl')`)으로 바꿉니다.

`authentication` JSON 개체와 같은 복합 개체를 매개 변수화할 수도 있습니다. 예를 들어 `authentication` 개체 값을 `@parameters('api-auth')`와 같은 단일 매개 변수 식을 보유하는 문자열로 바꿉니다. 

> [!NOTE]
> **connections.json** 파일에서 유일하게 유효한 식 형식은 `@parameters` 및 `@appsetting`입니다.

## <a name="define-parameters"></a>매개 변수 정의

단일 테넌트 기반 워크플로에서는 모든 매개 변수 값을 **parameters.json** 이라는 루트 수준 JSON 파일에 배치해야 합니다. 이 파일에는 키-값 쌍을 포함하는 개체가 포함되어 있습니다. 키는 각 매개 변수의 이름이고, 값은 각 매개 변수의 구조입니다. 각 구조에는 `type` 및 `value` 선언이 모두 포함되어야 합니다.

> [!NOTE]
> **parameters.json** 파일에서 유일하게 유효한 식 형식은 `@appsetting`입니다.

다음 예제에서는 샘플 매개 변수 파일을 보여줍니다.

```json
{ 
    "responseString": { 
        "type": "string", 
        "value": "hello" 
    }, 
    "functionAuth": { 
        "type": "object", 
        "value": { 
            "type": "QueryString", 
            "name": "Code", 
            "value": "@appsetting('<AzureFunctionsOperation-FunctionAppKey')" 
        } 
    } 
} 
```

일반적으로 매개 변수 파일의 여러 버전을 관리해야 합니다. 개발, 테스트, 프로덕션 등의 여러 배포 환경을 대상으로 하는 값이 있을 수 있습니다. 이러한 매개 변수 파일을 관리하는 방법은 종종 ARM 템플릿 매개 변수 파일을 관리하는 방법과 비슷합니다. 특정 환경에 배포하는 경우 일반적으로 DevOps에 대한 파이프라인을 통해 해당 매개 변수 파일을 승격합니다.

Azure CLI를 사용하여 매개 변수 파일을 동적으로 바꾸려면 다음 명령을 실행합니다.

```azurecli
az functionapp deploy --resource-group MyResourceGroup --name MyLogicApp --src-path C:\parameters.json --type static --target-path parameters.json
```

> [!NOTE]
> 현재 매개 변수 파일을 동적으로 바꾸는 기능은 Azure Portal 또는 워크플로 디자이너에서 아직 사용할 수 없습니다.

DevOps 배포를 위한 논리 앱 설정에 대한 자세한 내용은 다음 설명서를 검토하세요.

- [단일 테넌트 기반 논리 앱에 대한 DevOps 배포 개요](devops-deployment-single-tenant-azure-logic-apps.md)
- [단일 테넌트 기반 논리 앱에 대한 DevOps 배포 설정](set-up-devops-deployment-single-tenant-azure-logic-apps.md)

## <a name="manage-app-settings"></a>모바일 앱 설정

단일 테넌트 Azure Logic Apps의 앱 설정에는 동일한 논리 앱의 *모든 워크플로* 에 대한 전역 구성 옵션이 포함됩니다. Visual Studio Code에서 로컬로 워크플로를 실행하는 경우 이러한 설정은 파일의 **local.settings.json** 파일에서 로컬 환경 변수로 액세스할 수 있습니다. 그런 다음, 매개 변수에서 이러한 앱 설정을 참조할 수 있습니다.

앱 설정을 추가, 업데이트 또는 삭제하려면 Visual Studio Code, Azure Portal, Azure CLI 또는 ARM(Bicep) 템플릿에 대한 다음 섹션을 선택하고 검토합니다.

### <a name="azure-portal"></a>[Azure Portal](#tab/azure-portal)

Azure Portal에서 논리 앱의 앱 설정을 검토하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/) 검색 상자에서 단일 테넌트 기반 논리 앱을 찾아서 엽니다.
1. 논리 앱 메뉴의 **설정** 에서 **구성** 을 선택합니다.
1. **구성** 페이지의 **애플리케이션 설정** 탭에서 논리 앱의 앱 설정을 검토합니다.
1. 모든 값을 보려면 **값 표시** 를 선택합니다. 또는 단일 값을 보려면 해당 값을 선택합니다.

새 설정을 추가하려면 다음 단계를 수행합니다.

1. **애플리케이션 설정** 탭의 **애플리케이션 설정** 에서 **새 애플리케이션 설정** 을 선택합니다.
1. **이름** 에 새 설정의 *키* 또는 이름을 입력합니다.
1. **값** 에 새 설정의 값을 입력합니다.
1. 새 *키-값* 쌍을 만들 준비가 되면 **확인** 을 선택합니다.

:::image type="content" source="./media/parameterize-workflow-app/portal-app-settings-values.png" alt-text="단일 테넌트 기반 논리 앱의 앱 설정 및 값이 표시된 Azure Portal 구성 창을 보여주는 스크린샷" lightbox="./media/parameterize-workflow-app/portal-app-settings-values.png":::

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI를 사용하여 현재 앱 설정을 검토하려면 `az logicapp config appsettings list` 명령을 실행합니다. 명령에 `--name -n` 및 `--resource-group -g` 매개 변수가 포함되어 있는지 확인합니다. 예를 들면 다음과 같습니다.

```azurecli
az logicapp config appsettings list --name MyLogicApp --resource-group MyResourceGroup
```

Azure CLI를 사용하여 앱 설정을 추가하거나 업데이트하려면 `az logicapp config appsettings set` 명령을 실행합니다. 명령에 `--name n` 및 `--resource-group -g` 매개 변수가 포함되어 있는지 확인합니다. 예를 들어 다음 명령은 값이 `12345`인 `CUSTOM_LOGIC_APP_SETTING`이라는 키로 설정을 만듭니다.

```azurecli
az logicapp config appsettings set --name MyLogicApp --resource-group MyResourceGroup --settings CUSTOM_LOGIC_APP_SETTING=12345 
```

### <a name="resource-manager-or-bicep-template"></a>[Resource Manager 또는 Bicep 템플릿](#tab/azure-resource-manager)

ARM 템플릿 또는 Bicep 템플릿에서 앱 설정을 검토하고 정의하려면 논리 앱의 리소스 정의를 찾고 `appSettings` JSON 개체를 업데이트합니다. 전체 리소스 정의는 [ARM 템플릿 참조](/azure/templates/microsoft.web/sites)를 확인하세요.

이 예제에서는 ARM 템플릿 또는 Bicep 템플릿에 대한 파일 설정을 보여줍니다.

```json
"appSettings": [
    {
        "name": "string",
        "value": "string"
    },
    {
        "name": "string",
        "value": "string"
    },
    <...>
], 
```

---

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure Logic Apps의 단일 테넌트 및 다중 테넌트와 통합 서비스 환경](single-tenant-overview-compare.md)
