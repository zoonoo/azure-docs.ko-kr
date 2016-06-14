<properties
   pageTitle="논리 앱 배포 템플릿 만들기 | Microsoft Azure"
   description="논리 앱 배포 템플릿을 만드는 방법과 릴리스 관리에 어떻게 사용할 수 있는지에 대해 알아봅니다."
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="jeffhollan"
   manager="erikre"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="05/25/2016"
   ms.author="jehollan"/>
   
# 논리 앱 배포 템플릿 만들기

논리 앱을 만든 후에, 논리 앱을 필요한 모든 환경 또는 리소스 그룹에 쉽게 배포할 수 있도록 Azure 리소스 템플릿으로 만들고 싶을 수 있습니다. Resource Manager 템플릿에 대한 소개는 [Azure Resource Manager 작성](../resource-group-authoring-templates.md) 및 [Azure Resource Manager 템플릿을 사용하여 리소스 배포](../resource-group-template-deploy.md)를 확인해야 합니다.

## 논리 앱 배포 템플릿 개요

논리 앱은 3가지 기본 구성 요소로 이루어집니다.

* **논리 앱 리소스** - 가격 계획, 위치 및 워크플로 정의 등에 대한 정보를 포함하는 리소스입니다.
* **워크플로 정의** - **코드 보기**를 선택하면 표시되는 내용으로 흐름의 단계와 엔진이 실행되는 방식에 대한 정의입니다. 논리 앱 리소스의 `definition` 속성입니다.
* **연결** - 연결 문자열 및 액세스 토큰과 같은 커넥터 연결과 관련된 메타데이터를 안전하게 보관하는 별도의 리소스입니다. 논리 앱 리소스의 `parameters`에 있는 논리 앱에서 참조됩니다.

[Azure 리소스 탐색기](http://resources.azure.com)와 같은 도구를 사용하여 기존 논리 앱에 대한 이러한 모든 사항을 볼 수 있습니다.

리소스 그룹 배포에 사용할 수 있는 논리 앱에 대한 템플릿을 만들려면 리소스를 정의하고 필요에 따라 매개 변수화해야 합니다. 예를 들어, 개발, 테스트 및 프로덕션 환경에 배포하는 경우 각 환경에서 SQL 데이터베이스에 대해 다른 연결 문자열을 사용하거나 다른 구독 또는 리소스 그룹 내에 배포하려고 할 수 있습니다.

## 논리 앱 배포 템플릿 만들기

논리 앱 배포 템플릿을 만드는 데 도움이 되는 몇 가지 도구가 있습니다. 위의 리소스를 사용하거나 필요에 따라 매개 변수를 만들어 직접 작성할 수 있습니다. [논리 앱 템플릿 작성기](https://github.com/jeffhollan/LogicAppTemplateCreator) PowerShell 모듈을 활용할 수도 있습니다. 오픈 소스 모듈은 논리 앱과 논리 앱에서 사용하는 모든 연결을 평가하고 배포에 필요한 매개 변수와 함께 템플릿 리소스를 생성하는 방법으로 작동합니다. 예를 들어, 서비스 버스 큐에서 메시지를 수신하고 SQL Azure 데이터베이스에 데이터를 추가하는 논리 앱이 있는 경우 이 도구는 모든 오케스트레이션 논리를 보존하고 SQL 및 서비스 버스 연결 문자열을 매개 변수화하여 배포 시 설정할 수 있도록 합니다.

>[AZURE.NOTE] 연결은 논리 앱과 동일한 리소스 그룹 내에 있어야 합니다.

### 논리 앱 템플릿 PowerShell 모듈 설치

가장 쉬운 설치 방법은 `Install-Module -Name LogicAppTemplate` 명령과 함께 [PowerShell 갤러리](https://www.powershellgallery.com/packages/LogicAppTemplate/0.1)를 통하는 방법입니다.

또한 수동으로 설치할 수도 있습니다.

1. [논리 앱 템플릿 작성기](https://github.com/jeffhollan/LogicAppTemplateCreator/releases)의 최신 릴리스를 다운로드합니다.  
1. PowerShell 모듈 폴더에 폴더를 추출합니다(일반적으로 `%UserProfile%\Documents\WindowsPowerShell\Modules`).

모듈에서 모든 테넌트 및 구독 액세스 토큰에 대한 작업을 하려면 [armclient](https://github.com/projectkudu/ARMClient) 명령줄 도구로 사용하는 것이 좋습니다. `armclient`에 대한 자세한 내용은 [여기](http://blog.davidebbo.com/2015/01/azure-resource-manager-client.html)에서 확인할 수 있습니다.

### PowerShell을 통해 논리 앱 템플릿 생성

설치한 후에는 다음 명령을 사용하여 템플릿을 생성할 수 있습니다.

`armclient token $SubscriptionId | Get-LogicAppTemplate -LogicApp MyApp -ResourceGroup MyRG -SubscriptionId $SubscriptionId -Verbose | Out-File C:\template.json`

여기서 `$SubscriptionId`는 Azure 구독 ID입니다. 이 줄은 `armclient`를 통해 액세스 토큰을 처음으로 가져와 PowerShell 스크립트를 통해 파이핑한 후 템플릿을 `.json` 파일로 출력합니다.

## 논리 앱 템플릿에 매개 변수 추가

논리 앱 템플릿을 만든 후에는 필요에 따라 매개 변수를 계속 추가하거나 수정할 수 있습니다. 예를 들어 정의에 단일 배포로 배포할 계획인 중첩된 워크플로 또는 Azure Function에 대한 리소스 ID가 포함된 경우 템플릿에 추가 리소스를 추가하고 필요에 따라 ID를 매개 변수화할 수 있습니다. 각 리소스 그룹과 함께 배포할 예정인 swagger 끝점 또는 사용자 지정 API에 대한 참조도 마찬가지입니다.

## 논리 앱 템플릿 배포

템플릿을 만든 후에는 PowerShell, REST API, Visual Studio, Release Management, Azure 포털 템플릿 배포와 같은 여러 도구를 사용하여 배포할 수 있습니다. 배포에 대한 자세한 내용은 [여기](../resource-group-template-deploy.md)에서 확인할 수 있습니다. 매개 변수에 대한 값을 저장할 [매개 변수 파일](../resource-group-template-deploy.md#parameter-file)을 만드는 것이 좋습니다.

### OAuth 연결 권한 부여

배포 후 논리 앱은 유효한 매개 변수를 통해 종단 간 작동하지만 OAuth 연결에는 유효한 액세스 토큰을 생성하도록 권한을 부여해야 합니다. 디자이너에서 논리 앱을 열고 연결에 권한을 부여하여 이 작업을 수행할 수 있으며 자동화하려는 경우 각 OAuth 연결에 동의하는 스크립트를 사용할 수도 있습니다. 예제 스크립트는 GitHub의 [Connection Auth](https://github.com/logicappsio/LogicAppConnectionAuth) 프로젝트 아래에서 확인할 수 있습니다.

## Visual Studio Release Management 사용

환경을 배포 및 관리하는 일반적인 시나리오는 논리 앱 배포 템플릿과 함께 Visual Studio Release Management와 같은 도구를 사용하는 것입니다. VSTS에는 빌드 또는 릴리스 파이프라인에 추가할 수 있는 [Azure 리소스 그룹 배포](https://github.com/Microsoft/vsts-tasks/tree/master/Tasks/DeployAzureResourceGroup) 작업이 포함됩니다. 배포 권한 부여를 위해서는 [서비스 주체](https://blogs.msdn.microsoft.com/visualstudioalm/2015/10/04/automating-azure-resource-group-deployment-using-a-service-principal-in-visual-studio-online-buildrelease-management/)가 필요하며 그 다음 릴리스 정의를 생성할 수 있습니다.

1. **릴리스** 아래에서 **빈** 정의로 **새 정의**를 시작하도록 선택합니다.

    ![][1]

1. 여기에 필요한 모든 아티팩트를 선택합니다. 수동으로 또는 빌드 프로세스의 일부로 생성된 논리 앱 템플릿일 수 있습니다.
1. **Azure 리소스 그룹 배포** 작업을 추가합니다.
1. [서비스 주체](https://blogs.msdn.microsoft.com/visualstudioalm/2015/10/04/automating-azure-resource-group-deployment-using-a-service-principal-in-visual-studio-online-buildrelease-management/)로 구성하고 **템플릿** 및 **템플릿 매개 변수** 파일을 참조합니다.
1. 릴리스 프로세스에서 다른 환경, 자동화된 테스트 또는 승인자에 대한 단계를 필요에 따라 계속 작성합니다.
    
<!-- Image References -->
[1]: ./media/app-service-logic-create-deploy-template/emptyReleaseDefinition.PNG

<!---HONumber=AcomDC_0601_2016-->