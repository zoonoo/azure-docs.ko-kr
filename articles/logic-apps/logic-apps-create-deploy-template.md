---
title: "Azure Logic Apps용 배포 템플릿 만들기 | Microsoft Docs"
description: "논리 앱 배포 및 릴리스 관리용 Azure Resource Manager 템플릿 만들기"
services: logic-apps
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: anneta
editor: 
ms.assetid: 85928ec6-d7cb-488e-926e-2e5db89508ee
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.custom: H1Hack27Feb2017
ms.date: 10/18/2016
ms.author: LADocs; jehollan
ms.openlocfilehash: 9cfbb294010d48deaf4b4c78c6a6bcd59a387d87
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="create-templates-for-logic-apps-deployment-and-release-management"></a>논리 앱 배포 및 릴리스 관리용 템플릿 만들기

논리 앱을 만든 후 Azure Resource Manager 템플릿으로 만들 수 있습니다.
이 방식을 사용하면 필요에 따라 어떤 환경이나 리소스 그룹에도 논리 앱을 손쉽게 배포할 수 있습니다.
Resource Manager 템플릿에 대한 자세한 내용은 [Azure Resource Manager 작성](../azure-resource-manager/resource-group-authoring-templates.md) 및 [Azure Resource Manager 템플릿을 사용하여 리소스 배포](../azure-resource-manager/resource-group-template-deploy.md)를 확인하세요.

## <a name="logic-app-deployment-template"></a>논리 앱 배포 템플릿

논리 앱은 세 가지 기본 구성 요소가 있습니다.

* **논리 앱 리소스**: 가격 책정 정책, 위치, 워크플로 정의 등의 정보가 포함됩니다.
* **워크플로 정의**: 논리 앱 워크플로 단계 및 Logic Apps 엔진이 워크플로를 실행하는 방법을 설명합니다.
논리 앱의 **코드 보기** 창에서 이 정의를 볼 수 있습니다.
논리 앱 리소스의 `definition` 속성에서 이 정의를 찾을 수 있습니다.
* **연결**: 연결 문자열 및 액세스 토큰과 같은 커넥터 연결과 관련된 메타데이터를 안전하게 보관하는 별도의 리소스를 참조합니다.
논리 앱 리소스에서 논리 앱은 `parameters` 섹션의 이러한 리소스를 참조합니다.

[Azure Resource Explorer](http://resources.azure.com)와 같은 도구를 사용하여 기존 Logic Apps에 대한 이런 모든 내용을 볼 수 있습니다.

리소스 그룹 배포에 사용할 수 있는 논리 앱 템플릿을 만들려면 리소스를 정의하고 필요에 따라 매개 변수화해야 합니다.
예를 들어, 개발, 테스트 및 프로덕션 환경에 배포하는 경우 각 환경에서 SQL Database에 대해 다른 연결 문자열을 사용할 수 있습니다.
또는, 다른 구독이나 리소스 그룹 내에 배포할 수 있습니다.  

## <a name="create-a-logic-app-deployment-template"></a>논리 앱 배포 템플릿 만들기

유효한 논리 앱 배포 템플릿을 생성하는 가장 쉬운 방법은 [Logic Apps용 Visual Studio Tools](logic-apps-deploy-from-vs.md)를 사용하는 것입니다.
Visual Studio Tools는 구독 또는 위치에서 사용할 수 있는 올바른 배포 템플릿을 생성합니다.

논리 앱 배포 템플릿을 만들 때 도움을 받을 수 있는 다른 몇 가지 도구가 있습니다.
템플릿은 손으로 작성할 수 있습니다. 즉, 여기서 논의했던 리소스를 사용하여 필요에 따라 매개 변수를 만듭니다.
[논리 앱 템플릿 작성기](https://github.com/jeffhollan/LogicAppTemplateCreator) PowerShell 모듈을 활용하는 옵션도 있습니다. 이 오픈 소스 모듈은 논리 앱과 논리 앱에서 사용하는 모든 연결을 평가하고, 배포에 필요한 매개 변수와 함께 템플릿 리소스를 생성합니다.
예를 들어, Azure Service Bus 큐에서 메시지를 수신하고 Azure SQL Database에 데이터를 추가하는 논리 앱이 있는 경우 이 도구는 모든 오케스트레이션 논리를 보존하고 SQL 및 Service Bus 연결 문자열을 매개 변수화하여 배포 시 설정할 수 있도록 합니다.

> [!NOTE]
> 연결은 논리 앱과 동일한 리소스 그룹 내에 있어야 합니다.
>
>

### <a name="install-the-logic-app-template-powershell-module"></a>논리 앱 템플릿 PowerShell 모듈 설치
가장 쉬운 설치 방법은 `Install-Module -Name LogicAppTemplate` 명령을 사용하여 [PowerShell 갤러리](https://www.powershellgallery.com/packages/LogicAppTemplate/0.1)를 통하는 것입니다.  

또한, PowerShell 모듈을 수동으로 설치할 수 있습니다.

1. [논리 앱 템플릿 작성기](https://github.com/jeffhollan/LogicAppTemplateCreator/releases)의 최신 릴리스를 다운로드합니다.  
2. PowerShell 모듈 폴더에 폴더를 추출합니다(일반적으로 `%UserProfile%\Documents\WindowsPowerShell\Modules`).

모듈에서 모든 테넌트 및 구독 액세스 토큰에 대한 작업을 하려면 [ARMClient](https://github.com/projectkudu/ARMClient) 명령줄 도구로 사용하는 것이 좋습니다.  이 [블로그 게시물](http://blog.davidebbo.com/2015/01/azure-resource-manager-client.html)은 ARMClient에 대해 자세히 설명 합니다.

### <a name="generate-a-logic-app-template-by-using-powershell"></a>PowerShell을 사용하여 논리 앱 템플릿 생성
PowerShell을 설치한 후 다음 명령을 사용하여 템플릿을 생성합니다.

`armclient token $SubscriptionId | Get-LogicAppTemplate -LogicApp MyApp -ResourceGroup MyRG -SubscriptionId $SubscriptionId -Verbose | Out-File C:\template.json`

`$SubscriptionId` 은(는) Azure 구독 ID입니다. 이 줄은 ARMClient를 통해 액세스 토큰을 가져온 다음 PowerShell 스크립트로 실행시키고, JSON 파일로 템플릿을 만듭니다.

## <a name="add-parameters-to-a-logic-app-template"></a>논리 앱 템플릿에 매개 변수 추가
논리 앱 템플릿을 만든 다음 필요한 매개 변수를 추가하거나 수정할 수 있습니다. 예를 들어 정의에 단일 배포로 배포할 계획인 중첩된 워크플로 또는 Azure Function에 대한 리소스 ID가 포함된 경우, 템플릿에 추가 리소스를 추가하고 필요에 따라 ID를 매개 변수화할 수 있습니다. 각 리소스 그룹과 함께 배포할 예정인 Swagger 끝점 또는 사용자 지정 API에 대한 참조도 마찬가지입니다.

## <a name="deploy-a-logic-app-template"></a>논리 앱 템플릿 배포

템플릿을 만든 후에는 PowerShell, REST API, [Visual Studio Team Services Release Management](#team-services), Azure Portal을 통한 템플릿 배포와 같은 여러 도구를 사용하여 배포할 수 있습니다.
또한, 매개 변수 값을 저장할 [매개 변수 파일](../azure-resource-manager/resource-group-template-deploy.md#parameter-files)을 만드는 것이 좋습니다.
[Azure Resource Manager 템플릿 및 PowerShell로 리소스 배포](../azure-resource-manager/resource-group-template-deploy.md) 또는 [Azure Resource Manager 템플릿 및 Azure Portal로 리소스 배포](../azure-resource-manager/resource-group-template-deploy-portal.md) 방법을 알아봅니다.

### <a name="authorize-oauth-connections"></a>OAuth 연결 권한 부여

배포 후 논리 앱은 유효한 매개 변수와 함께 종단 간에 적용됩니다.
그러나 OAuth 연결이 유효한 액세스 토큰을 생성하도록 권한을 부여해야 합니다.
OAuth 연결에 권한을 부여하려면 Logic Apps Designer에서 논리 앱을 열고 이러한 연결에 권한을 부여합니다. 또는, 자동화 배포의 경우 스크립트를 사용하여 각 OAuth 연결을 승인할 수 있습니다.
GitHub에 [LogicAppConnectionAuth](https://github.com/logicappsio/LogicAppConnectionAuth) 프로젝트라는 예시가 있습니다.

<a name="team-services"></a>
## <a name="visual-studio-team-services-release-management"></a>Visual Studio Team Services 릴리스 관리

환경을 배포 및 관리하는 일반적인 시나리오는 논리 앱 배포 템플릿과 함께 Visual Studio Team Services의 릴리스 관리 등의 도구를 사용하는 것입니다. Visual Studio Team Services에는 모든 빌드 또는 릴리스 파이프라인에 추가할 수 있는 [Azure 리소스 그룹 배포](https://github.com/Microsoft/vsts-tasks/tree/master/Tasks/DeployAzureResourceGroup) 작업이 포함됩니다. 배포 권한 부여를 위해서는 [서비스 주체](https://blogs.msdn.microsoft.com/visualstudioalm/2015/10/04/automating-azure-resource-group-deployment-using-a-service-principal-in-visual-studio-online-buildrelease-management/) 가 필요하며 그 다음 릴리스 정의를 생성할 수 있습니다.

1. 릴리스 관리에서 빈 정의를 생성할 수 있도록 **비어 있음**을 선택합니다.

    ![빈 정의 만들기][1]

2. 수동으로 또는 빌드 프로세스의 일부로 생성된 논리 앱 템플릿을 포함할 가능성이 높은 필요한 리소스를 선택합니다.
3. **Azure 리소스 그룹 배포** 작업을 추가합니다.
4. [서비스 주체](https://blogs.msdn.microsoft.com/visualstudioalm/2015/10/04/automating-azure-resource-group-deployment-using-a-service-principal-in-visual-studio-online-buildrelease-management/)로 구성하고 템플릿 및 템플릿 매개 변수 파일을 참조합니다.
5. 릴리스 프로세스에서 다른 환경, 자동화된 테스트 또는 승인자에 대한 단계를 필요에 따라 계속 작성합니다.

<!-- Image References -->
[1]: ./media/logic-apps-create-deploy-template/emptyreleasedefinition.png
