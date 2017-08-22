---
title: "Visual Studio에서 Logic Apps 만들기, 빌드 및 배포 - Azure Logic Apps | Microsoft Docs"
description: "Azure Logic Apps를 디자인, 빌드 및 배포할 수 있도록 Visual Studio 프로젝트를 만듭니다."
author: jeffhollan
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: e484e5ce-77e9-4fa9-bcbe-f851b4eb42a6
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: H1Hack27Feb2017
ms.date: 2/14/2017
ms.author: LADocs; jehollan
ms.translationtype: HT
ms.sourcegitcommit: 79bebd10784ec74b4800e19576cbec253acf1be7
ms.openlocfilehash: e7f5cf483d22e4c60dedbe5176ceb0bc8b2b6e66
ms.contentlocale: ko-kr
ms.lasthandoff: 08/03/2017

---

# <a name="design-build-and-deploy-azure-logic-apps-in-visual-studio"></a>Visual Studio에서 Azure Logic Apps 디자인, 빌드 및 배포

[Azure Portal](https://portal.azure.com/)에서 Azure Logic Apps를 만들고 관리할 수 있는 좋은 방법을 제공하지만 Logic Apps를 디자인, 빌드 및 배포하기 위해 Visual Studio를 사용할 수도 있습니다. Visual Studio는 Logic Apps Designer와 같이 논리 앱을 빌드하는 풍부한 도구를 제공하고, 배포 및 자동화 템플릿을 구성하고, 모든 환경에 배포합니다. 

Azure Logic Apps을 시작하려면 [Azure Portal에서 첫 번째 논리 앱을 만드는 방법](logic-apps-create-a-logic-app.md)을 알아보세요.

## <a name="installation-steps"></a>설치 단계

Azure Logic Apps용 Visual Studio 도구를 설치하고 구성하려면 다음 단계를 따르세요.

### <a name="prerequisites"></a>필수 조건

* [Visual Studio 2017](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx) 또는 Visual Studio 2015
* [최신 Azure SDK](https://azure.microsoft.com/downloads/) (2.9.1 이상)
* [Azure PowerShell](https://github.com/Azure/azure-powershell#installation)
* 포함된 디자이너를 사용하는 경우 웹에 대한 액세스

### <a name="install-visual-studio-tools-for-azure-logic-apps"></a>Azure Logic Apps용 Visual Studio 도구 설치

필수 구성 요소를 설치한 후에:

1. Visual Studio를 엽니다. **도구** 메뉴에서 **확장 및 업데이트**를 선택합니다.
2. 온라인을 검색할 수 있도록 **온라인** 범주를 확장합니다.
3. **Visual Studio용 Azure Logic Apps 도구**를 찾을 때까지 **Logic Apps**을 찾아보거나 검색합니다.
4. 확장을 다운로드하고 설치하려면 **다운로드**를 클릭합니다.
5. 설치 후 Visual Studio를 다시 시작합니다.

> [!NOTE]
> Visual Studio Marketplace에서 직접 [Visual Studio 2017용 Azure Logic Apps 도구](https://marketplace.visualstudio.com/items?itemName=VinaySinghMSFT.AzureLogicAppsToolsforVisualStudio-18551) 및 [Visual Studio 2015용 Azure Logic Apps 도구](https://marketplace.visualstudio.com/items?itemName=VinaySinghMSFT.AzureLogicAppsToolsforVisualStudio)를 다운로드할 수도 있습니다.

설치를 완료한 후에 Logic App Designer에서 Azure 리소스 그룹을 사용할 수 있습니다.

## <a name="create-your-project"></a>프로젝트 만들기

1. **파일** 메뉴에서 **새로 만들기**로 이동하고 **프로젝트**를 선택합니다. 또는 기존 솔루션에 프로젝트를 추가하려면 **추가**로 이동하고 **새 프로젝트**를 선택합니다.

    ![파일 메뉴](./media/logic-apps-deploy-from-vs/filemenu.png)

2. **새 프로젝트** 창에서 **클라우드**를 찾고 **Azure 리소스 그룹**을 선택합니다. 프로젝트 이름을 지정하고 **확인**을 클릭합니다.

    ![새 프로젝트 추가](./media/logic-apps-deploy-from-vs/addnewproject.png)

3. **Logic App** 템플릿을 선택하면 사용자가 사용할 빈 논리 앱 배포 템플릿을 만듭니다. 템플릿을 선택한 후에 **확인**을 클릭합니다.

    ![Logic App 템플릿 선택](./media/logic-apps-deploy-from-vs/selectazuretemplate1.png)

    이제 논리 앱 프로젝트가 솔루션에 추가되었습니다. 
    솔루션 탐색기에서 배포 파일이 표시되어야 합니다.

    ![배포 파일](./media/logic-apps-deploy-from-vs/deployment.png)

## <a name="create-your-logic-app-with-logic-app-designer"></a>Logic App Designer에서 논리 앱 만들기

Logic App이 포함된 Azure 리소스 그룹 프로젝트가 있는 경우 Visual Studio에서 Logic App Designer를 열어 워크플로를 만들 수 있습니다. 

> [!NOTE]
> 사용 가능한 속성 및 데이터에 대한 커넥터를 쿼리하기 위해 디자이너가 인터넷에 연결되어야 합니다. 예를 들어, Dynamics CRM Online 커넥터를 사용하는 경우 디자이너는 사용 가능한 사용자 지정 및 기본 속성을 표시하기 위해 CRM 인스턴스를 쿼리합니다.

1. `<template>.json` 파일을 마우스 오른쪽 단추로 클릭하고 **Logic App Designer로 열기**를 선택합니다. (`Ctrl+L`)

2. Azure 구독, 리소스 그룹 및 배포 템플릿의 위치를 선택합니다.

    > [!NOTE]
    > 논리 앱을 디자인하면 디자인하는 동안 속성을 쿼리하는 API 연결 리소스가 만들어집니다. Visual Studio에서는 디자인하는 동안 선택한 리소스 그룹을 사용하여 해당 연결을 만듭니다. API 연결을 보거나 변경하려면 Azure Portal로 이동하고 **API 연결**을 찾아봅니다.

    ![구독 선택기](./media/logic-apps-deploy-from-vs/designer_picker.png)

    디자이너는 렌더링할 `<template>.json` 파일에서 정의를 사용합니다.

4. 논리 앱을 만들고 디자인합니다. 배포 템플릿에는 변경 내용이 업데이트됩니다.

    ![Visual Studio의 Logic App Designer](./media/logic-apps-deploy-from-vs/designer_in_vs.png)

Visual Studio는 논리 앱이 작동하는 데 필요한 연결을 설정하기 위해 리소스 파일에 `Microsoft.Web/connections` 리소스를 추가합니다. 이러한 연결 속성은 배포할 때 설정할 수 있으며 배포한 후 Azure Portal의 **API 연결** 에서 관리할 수 있습니다.

### <a name="switch-to-json-code-view"></a>JSON 코드 보기로 전환

논리 앱의 JSON 표현으로 표시하려면 디자이너 아래에 있는 **코드 보기** 탭을 선택합니다.

전체 리소스 JSON으로 다시 전환하려면 `<template>.json` 파일을 마우스 오른쪽 단추로 클릭하고 **열기**를 선택합니다.

### <a name="add-references-for-dependent-resources-to-visual-studio-deployment-templates"></a>Visual Studio 배포 템플릿에 종속 리소스에 대한 참조 추가

논리 앱에서 종속 리소스를 참조하려는 경우 논리 앱 배포 템플릿에서 [Azure Resource Manager 템플릿 함수](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-functions)를 사용할 수 있습니다. 예를 들어, 논리 앱에서 논리 앱과 함께 배포하려는 Azure Function 또는 통합 계정을 참조하려고 합니다. Logic App Designer가 올바르게 렌더링하도록 배포 템플릿에서 매개 변수를 사용하는 방법에 대한 다음 지침을 따르세요. 

이러한 종류의 트리거 및 작업에서 논리 앱 매개 변수를 사용할 수 있습니다.

*   하위 워크플로
*   함수 앱
*   APIM 호출
*   API 연결 런타임 URL
*   API 연결 경로

parameters, variables, resourceId, concat 등과 같은 템플릿 함수를 사용할 수 있습니다. 예를 들어, Azure Function 리소스 ID를 대체하는 방법은 다음과 같습니다.

```
"parameters":{
    "functionName": {
        "type":"string",
        "minLength":1,
        "defaultValue":"<FunctionName>"
    }
},
```

그리고 매개 변수를 사용하는 위치는 다음과 같습니다.

```
"MyFunction": {
    "type": "Function",
    "inputs": {
        "body":{},
        "function":{
            "id":"[resourceid('Microsoft.Web/sites/functions','functionApp',parameters('functionName'))]"
        }
    },
    "runAfter":{}
}
```
또 다른 예로, Service Bus 메시지 전송 작업을 매개 변수화할 수 있습니다.

```
"Send_message": {
    "type": "ApiConnection",
        "inputs": {
            "host": {
                "connection": {
                    "name": "@parameters('$connections')['servicebus']['connectionId']"
                }
            },
            "method": "post",
            "path": "[concat('/@{encodeURIComponent(''', parameters('queueuname'), ''')}/messages')]",
            "body": {
                "ContentData": "@{base64(triggerBody())}"
            },
            "queries": {
                "systemProperties": "None"
            }
        },
        "runAfter": {}
    }
```
> [!NOTE] 
> host.runtimeUrl은 선택 사항이며 있는 경우 템플릿에서 제거할 수 있습니다.
> 


> [!NOTE] 
> 매개 변수를 사용할 때 Logic App Designer를 작동시키려면 다음과 같은 기본값을 제공해야 합니다.
> 
> ```
> "parameters": {
>     "IntegrationAccount": {
>     "type":"string",
>     "minLength":1,
>     "defaultValue":"/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Logic/integrationAccounts/<integrationAccountName>"
>     }
> },
> ```

### <a name="save-your-logic-app"></a>논리 앱 저장

언제든지 논리 앱을 저장하려면 **파일** > **저장**으로 이동합니다. (`Ctrl+S`) 

앱을 저장할 때 논리 앱에 오류가 발생하면 Visual Studio **출력** 창에 표시됩니다.

## <a name="deploy-your-logic-app-from-visual-studio"></a>Visual Studio에서 논리 앱 배포

앱을 구성한 후에 몇 단계 만에 Visual Studio에서 직접 배포할 수 있습니다. 

1. 솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **배포** > **새로운 배포...**로 이동합니다.

    ![새 배포](./media/logic-apps-deploy-from-vs/newdeployment.png)

2. 메시지가 표시되면 Azure 구독에 로그인합니다. 

3. 이제 논리 앱을 배포하려는 리소스 그룹에 대한 세부 정보를 선택해야 합니다. 완료되면 **배포**를 선택합니다.

    > [!NOTE]
    > 리소스 그룹에 대한 올바른 템플릿 및 매개 변수 파일을 선택했는지 확인합니다. 예를 들어, 프로덕션 환경에 배포하려는 경우 프로덕션 매개 변수 파일을 선택합니다.

    ![리소스 그룹 배포](./media/logic-apps-deploy-from-vs/deploytoresourcegroup.png)

    배포 상태는 **출력** 창에 표시됩니다. 
    **출력 보기 선택** 목록에서 **Azure 프로비전**을 선택해야 합니다.

    ![배포 상태 출력](./media/logic-apps-deploy-from-vs/output.png)

나중에 원본 제어에서 논리 앱을 편집하고 Visual Studio를 사용하여 새 버전을 배포할 수 있습니다.

> [!NOTE]
> Azure Portal에서 정의를 직접 변경하게 되면 해당 변경 내용은 다음에 Visual Studio에서 배포할 경우 덮어쓰여집니다. 

## <a name="add-your-logic-app-to-an-existing-resource-group-project"></a>논리 앱을 기존 리소스 그룹 프로젝트에 추가

기존 리소스 그룹 프로젝트가 있는 경우 JSON 개요 창에서 해당 프로젝트에 논리 앱을 추가할 수 있습니다. 이전에 만든 앱과 함께 다른 논리 앱을 추가할 수도 있습니다.

1. `<template>.json` 파일을 엽니다.

2. JSON 개요 창을 열려면 **보기** > **다른 창** > **JSON 개요**로 이동합니다.

3. 템플릿 파일에 리소스를 추가하려면 JSON 개요 창의 위쪽에서 **리소스 추가**를 클릭합니다. 또는 JSON 개요 창에서 **리소스**를 마우스 오른쪽 단추로 클릭하고 **새 리소스 추가**를 선택합니다.

    ![JSON 개요 창](./media/logic-apps-deploy-from-vs/jsonoutline.png)
    
4. **리소스 추가** 대화 상자에서 **Logic App**을 찾고 선택합니다. 논리 앱의 이름을 지정하고 **추가**를 선택합니다.

    ![리소스 추가](./media/logic-apps-deploy-from-vs/addresource.png)

## <a name="next-steps"></a>다음 단계

* [Visual Studio 클라우드 탐색기를 사용하여 Logic Apps 관리](logic-apps-manage-from-vs.md)
* [일반적인 예제 및 시나리오 보기](logic-apps-examples-and-scenarios.md)
* [Azure Logic Apps을 사용하여 비즈니스 프로세스를 자동화하는 방법 알아보기](http://channel9.msdn.com/Events/Build/2016/T694)
* [Azure Logic Apps과 시스템을 통합하는 방법 알아보기](http://channel9.msdn.com/Events/Build/2016/P462)

