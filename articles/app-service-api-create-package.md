<properties 
	pageTitle="API 앱 패키지 만들기" 
	description="API 앱 패키지를 만드는 방법을 알아봅니다." 
	services="app-service\api" 
	documentationCenter=".net"
	authors="guangyang"
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="04/20/2015" 
	ms.author="guayan"/>

# API 앱 패키지 만들기

## 개요

이 문서에서는 [Azure 마켓플레이스](http://azure.microsoft.com/marketplace/api-apps/)에 게시할 수 있도록 API 앱 패키지를 만드는 방법을 보여 줍니다.

- API 앱을 만드는 방법을 알아보려면 [Visual Studio를 사용하여 API 앱 만들기](app-service-dotnet-create-api-app.md)를 참조하세요.
- Azure 마켓플레이스에 API 앱 패키지를 게시하는 방법을 알아보려면 [Azure 마켓플레이스에 API 앱 패키지 게시](app-service-api-publish-package)를 참조하세요.

## 폴더 구조

API 앱의 Nuget 패키지(*.nupkg* 파일)에는 *Content* 폴더 아래에 다음 파일 및 폴더가 있습니다.

    apiapp.json
    Metadata
        apiDefinition.swagger.json
        icons
            <icon files>
        screenshots
            <screenshot files>
        deploymentTemplates
            <template files>
        UIDefinition.json
    <other artifacts like source code, binary, etc.>

*.nupkg* 파일은 다음 그림과 같이 프로젝트 폴더에 *apiapp.json* 및 *Metadata*가 있는 Visual Studio 프로젝트를 패키지할 때 이 폴더 구조로 만들어집니다.

![솔루션 탐색기의 apiapp.json 및 Metadata](./media/app-service-api-create-package/metadatainse.png)

다음 섹션에서는 프로젝트를 패키지 및 배포하는 방법을 보여 주기 전에 API 앱 폴더 구조의 각 파일과 폴더에 대해 설명합니다.

## apiapp.json

API 앱의 매니페스트 파일입니다.

|이름(굵게=필수)|형식|형식|설명|
|:---------------------|:-----|:-------|:------------|
|**id**|string|[a-zA-Z0-9_.]|이 패키지의 ID입니다. 네임스페이스 내에서 고유해야 하며 영숫자 문자, "_" 및 "."만 포함할 수 있습니다. 영숫자 문자로 시작해야 합니다.|
|**namespace**|string|도메인 이름|**id** 속성과 함께 API 앱을 고유하게 식별하는 네임스페이스입니다. 게시자 AAD 테넌트의 도메인 이름 중 하나여야 합니다.|
|**version**|string|[semver](http://docs.nuget.org/Create/Versioning)|이 패키지의 버전입니다. 사용자가 이 패키지에 대해 자동 업그레이드를 사용하도록 설정한 경우 동일한 주 버전 내의 새 버전에만 적용됩니다.|
|**gateway**|string|2015-01-14|이 패키지에서 사용하고 있는 게이트웨이 API 버전입니다. 게이트웨이는 리소스 그룹의 API 앱에 대한 모든 요청이 라우팅되는 특수 웹앱입니다. 주요 기능 중 하나는 인증을 처리하는 것입니다. 현재 유일한 게이트웨이 버전은 2015-01-14입니다. 나중에 새 게이트웨이 버전이 출시되면 이 속성을 통해 단절을 초래하는 변경을 방지하고 이전 게이트웨이 API를 계속 사용할 수 있습니다.| 
|**title**|string||API 앱의 표시 이름입니다.|
|**summary**|string|최대 100자|API 앱에 대한 간단한 요약입니다.
|description|string|최대 1500자|API 앱의 전체 설명입니다. HTML을 포함할 수 있습니다. 허용되는 요소 및 특성은 "h1", "h2", "h3", "h4", "h5", "p", "ol", "ul", "li", "a[target|href]", "br", "strong", "em", "b", "i"입니다.|
|**author**|string|최대 256자|API 앱의 만든 이입니다.|
|homepage|string|URL|API 앱의 홈페이지입니다.|
|endpoints|object||API 앱 플랫폼에서 API 앱의 메서드 및 상태에 대한 정보를 쿼리할 수 있는 끝점 목록입니다.|
|endpoints.apiDefinition|string|URL 경로|API 앱에 의해 노출되는 API의 상대 URL로서, GET 요청에 대한 Swagger 2.0 API 정의(예: "/swagger/docs/v1")를 반환합니다. 이 값을 설정하면 패키지의 정적 apiDefinition.swagger.json 파일(있는 경우) 대신 이 값이 사용됩니다.|
|endpoints.status|string|URL 경로|API 앱에 의해 노출되는 API의 상대 URL로서, GET 요청 시 API 앱에 대한 상태 정보를 반환합니다.|
|categories|string[]|community, social, enterprise, integration, protocol, app-datasvc, other|이 API 앱 패키지가 표시되는 Azure 마켓플레이스 범주입니다. 기본적으로 API 앱은 항상 커뮤니티 범주 아래에 표시됩니다. API 앱이 승인되면 지정된 범주에 표시됩니다.|
|라이선스|object||API 앱의 라이선스입니다.|
|**license.type**|string||SPDX 라이선스 ID입니다(예: MIT).|
|license.url|string|URL|전체 라이선스 텍스트를 가리키는 절대 URL입니다.|
|license.requireAcceptance|bool|true, false|설치 전에 라이선스를 수락해야 하는지 여부입니다. 기본값: false입니다.|
|links|object[]||마켓플레이스 페이지에 추가할 링크의 배열입니다.|
|**links.text**|string||링크의 텍스트입니다.|
|**links.url**|string|URL|링크의 절대 URL입니다.|
|authentication|object[]||나가는 API 호출을 만들기 위해 이 API 앱에 필요한 인증의 종류를 나타내는 배열입니다. 현재는 API 앱 패키지당 하나의 인증만 지원됩니다.|
|**authentication.type**|string|Box, DropBox, Facebook, Google, Office365, OneDrive, Quickbooks, Salesforce, SharePointOnline, Twitter, Yammer|API 앱에 필요한 인증입니다. 현재 11가지 인증 유형이 지원됩니다. 나중에 더 추가될 예정입니다.| 
|authentication.scopes|string[]||인증 유형과 관련된 범위의 배열입니다.|
|copyright|string|API 앱의 저작권 표시입니다.
|brandColor|string|모든 CSS 호환 형식|UI 환경을 구동할 선택적 브랜드 색입니다. 예를 들어 논리 앱 디자이너는 이 속성을 사용하여 카드 머리글의 배경색을 그립니다.|

## metadata/apiDefinition.swagger.json

필요에 따라 여기에서 정적 Swagger 2.0 JSON 파일을 제공하여 API 앱의 API 정의를 노출할 수 있습니다. 먼저 플랫폼에서 **endpoints.apiDefinition** 속성이 **apiapp.json**에 구성되어 있는지 확인합니다. 구성되어 있으면 이 속성에 지정된 URL에서 API 정의를 가져옵니다. 그렇지 않으면 이 파일을 찾으려고 시도합니다.

- Swagger 2.0 표준에 대한 자세한 내용은 [http://swagger.io/](http://swagger.io/)를 참조하세요. <!--todo URL 제공
- API 정의를 논리 앱에 최적화되도록 사용자 지정하는 방법에 대한 자세한 내용은 [문서 제목]()을(를) 참조하세요.
- 동적 API 정의를 노출하는 방법에 대한 자세한 내용은 [문서 제목]()을(를) 참조하세요. -->

## metadata/icons

필요에 따라 API 앱 패키지에 대한 사용자 고유의 아이콘을 제공할 수 있습니다. 필요한 아이콘 파일을 제공하지 않으면 아래와 같은 기본 아이콘이 사용됩니다.

![API 앱 기본 큰 아이콘](./media/app-service-api-create-package/api-app-default-large-icon.png)

|파일 |너비|높이|설명|
|:--------------------|:----|:-----|:----------|
|metadata/icons/small.png|40px|40px|필수(사용자 고유의 아이콘을 사용하려는 경우)|
|metadata/icons/medium.png|90px|90px|필수(사용자 고유의 아이콘을 사용하려는 경우)|
|metadata/icons/large.png|115px|115px|필수(사용자 고유의 아이콘을 사용하려는 경우)|
|metadata/icons/wide.png|255px|115px|필수(사용자 고유의 아이콘을 사용하려는 경우)|
|metadata/icons/hero.png|815px|290px|선택(사용자 고유의 아이콘을 사용하려는 경우)|

## metadata/screenshots

필요에 따라 API 앱 패키지에 대한 스크린샷을 최대 5개 제공할 수 있습니다.

|파일|너비|높이|설명|
|:--------------------|:----|:-----|:----------|
|metadata/screenshots/*.png|533px|324px|API 앱 패키지의 스크린샷|

## metadata/deploymentTemplates

배포하는 동안 API 앱 패키지에 일부 사용자 지정 구성이 필요한 경우가 있습니다. 예를 들어 [Azure 저장소 Blob 컨테이너](http://azure.microsoft.com/marketplace/partners/microsoft_com/azurestorageblobconnector/)에는 저장소 Blob 컨테이너 URI가 필요합니다. 또한 필요에 따라 액세스 키를 구성할 수 있습니다.

이 시나리오를 지원하려면 이 폴더의 ARM(Azure 리소스 관리자) 템플릿 JSON 파일 목록을 추가하여 API 앱 배포를 사용자 지정하면 됩니다. API 앱 플랫폼에서 사용자 지정 ARM 템플릿을 Microsoft 시스템 템플릿과 병합하여 배포를 위한 최종 템플릿을 생성합니다. 또한 API 앱 사용자가 값을 입력할 수 있도록 Azure Preview 포털 **만들기** 블레이드에 사용자 지정 ARM 템플릿에 정의된 모든 매개 변수(**$system**에 필요)를 묻는 메시지가 자동으로 표시됩니다.

다음은 API 앱을 배포하는 동안 Blob 컨테이너 URI 및 액세스 키를 요청하는 방법을 보여 주는 샘플 ARM 템플릿입니다.

    {
      "$schema": "http://schemas.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "$system": {
          "type": "object"
        },
        "BlobConnector_ContainerUrl": {
          "type": "string"
        },
        "BlobConnector_AccessKey": {
          "type": "securestring"
        }
      },
      "resources": [
        {
          "apiVersion": "2015-04-01",
          "type": "Microsoft.Web/sites",
          "name": "[parameters('$system').siteName]",
          "location": "[parameters('$system').location]",
          "properties": {
            "siteConfig": {
              "appSettings": [
                {
                  "name": "BlobConnector_ContainerUrl",
                  "value": "[parameters('BlobConnector_ContainerUrl')]"
                },
                {
                  "name": "BlobConnector_AccessKey",
                  "value": "[parameters('BlobConnector_AccessKey')]"
                }
              ]
            }
          }
        }
      ]
    }

해당 Azure Preview 포털 만들기 블레이드는 아래 스크린샷에 표시되어 있습니다. 이 스크린샷에서는 만들기 블레이드를 개선하기 위해 UIDefinition.json을 사용하는 API 앱 패키지를 보여 줍니다. 자세한 내용은 [metadata/UIDefinition.json](#metadata-uidefinition-json)을 참조하세요.

![사용자 지정 ARM 템플릿 만들기 블레이드 예](./media/app-service-api-create-package/custom-arm-template-create-blade-example.png)

자세한 내용은 [Azure 리소스 관리자 템플릿 언어](https://msdn.microsoft.com/library/dn835138)를 참조하세요.

## metadata/UIDefinition.json

사용자 지정 ARM 템플릿을 사용하여 API 앱 배포에 필요한 사용자 지정 구성을 지정해야 하는 경우 API 앱 사용자가 값을 입력할 수 있도록 Azure Preview 포털 **만들기** 블레이드에서 ARM 템플릿 매개 변수를 묻는 메시지를 자동으로 표시합니다. *UIDefinition.json*을 사용해 기본값, 도구 설명, 유효성 검사 등을 제공하여 **만들기** 블레이드 UI를 개선할 수 있습니다.

*UIDefinition.json 파일*을 제공하려면 다음 기본 코드로 시작한 다음 아래 표에 따라 각 매개 변수를 구성합니다.


    {
      "parameters": {
        "<your API app package id>": {
            "<name of the ARM template parameter>": { },
            "<name of the ARM template parameter>": { }
        }
      }
    }

|이름 |형식|설명|
|:---------------------|:-------|:------------|
|defaultValue|string|Azure Preview 포털 만들기 블레이드의 입력 컨트롤에 대한 기본값입니다.|
|displayName|string|Azure Preview 포털 만들기 블레이드의 입력 컨트롤에 대한 레이블입니다. 이 매개 변수는 짧아야 합니다.|
|description|string|입력된 컨트롤에 대한 설명입니다.|
|tooltip|string|Azure Preview 포털 만들기 블레이드의 입력 컨트롤에 대한 도구 설명입니다. 사용자가 레이블 오른쪽에 있는 정보 버블을 클릭하면 표시됩니다. 이 매개 변수는 길고 포괄적일 수 있습니다.|
|constraints|Object|매개 변수에 대해 확인할 제약 조건입니다.|
|constraints.required|bool|매개 변수가 필요한지 여부입니다. 기본값은 false입니다.|

예를 들어 다음은 [Azure 저장소 Blob 커넥터](http://azure.microsoft.com/marketplace/partners/microsoft_com/azurestorageblobconnector/)용 *UIDefinition.json* 파일입니다.

    {
      "parameters": {
        "AzureStorageBlobConnector": {
          "BlobConnector_ContainerUrl": {
            "defaultValue": "",
            "displayName": "Container/SAS URI",
            "description": "Specify the URI of the Blob Container. The URI may also include the SAS token. Example http://storageaccountname.blob.core.windows.net/containername or http://storageaccountname.blob.core.windows.net/containername?sr=c&si=mypolicy&sig=signatureblah",
            "tooltip": "Specify the URI of the Blob Container. The URI may also include the SAS token. Example http://storageaccountname.blob.core.windows.net/containername or http://storageaccountname.blob.core.windows.net/containername?sr=c&si=mypolicy&sig=signatureblah",
            "constraints": {
              "required": "true"
            }
          },
          "BlobConnector_AccessKey": {
            "defaultValue": "",
            "displayName": "Access Key",
            "description": "Specify a valid primary/secondary storage account access key. Leave this field empty if you are using SAS token for authentication.",
            "tooltip": "Specify a valid primary/secondary storage account access key. Leave this field empty if you are using SAS token for authentication.",
            "constraints": {
              "required": "false"
            }
          }
        }
      }
    }

이 파일은 다음 **패키지 설정** 블레이드를 표시하도록 Azure Preview 포털 **만들기** 블레이드를 구성합니다.

![UIDefinition 만들기 블레이드 예](./media/app-service-api-create-package/uidefinition-create-blade-example.png)

<!--todo add when ready to document status URI
A URI to a web service Get method that returns a value that indicates the API app's current status. If you provide this URI, the portal will show the API app's current operational status along with other information about the API app, for example:  running, nearing quota, SSL certificate expiring, etc. The format of the JSON the portal expects to receive is shown below, following the end of this table. If you don't provide an endpoints.status URI, the portal shows the Azure platform status as the API app's status.
Here is an example that shows the expected format of the JSON response from the Get method that `endpoints.status` points to:
		{
		  "status":[{ 
		    "name":"Nearing Quota", 
		    "message":"One or more quotas is nearing their limit", 
		    "level":"Warning" 
		  }, { 
		    "name":"Expiring Certificate", 
		    "message":"The SSL Certificate associated with this website is about to expire", 
		    "level":"Error"
		  }, {
		    "name":"Running", 
		    "message":"The website is handling traffic", 
		    "level":"Info"
		  }]
		}
The `name` property is a short description of the status, `message` is a longer description, and `level` can be "Error", "Warning", or "Info" for normal status.
-->

## API 앱 패키지 만들기

API 앱 패키지를 만들려면

1. [Azure 플랫폼 간 명령줄 인터페이스를 다운로드하여 설치합니다](xplat-cli).
2. 다음 명령을 실행하여 Azure 리소스 관리자 모드로 전환합니다.

        azure config mode arm

3. 다음 명령을 실행하여 API 앱의 nuget 패키지를 만듭니다.

        azure apiapp package create -p <package folder> -o <destination folder>

    예:

        azure apiapp package create -p c:\ContactList\ContactList -o c:\ContactListPackage

이 명령은 패키지 콘텐츠의 유효성을 검사하여 다음 사항을 확인합니다.

- 위에 설명된 형식을 따르는지 여부
- Metadata\\apiDefinition.swagger.json(제공된 경우)에 올바른 Swagger 2.0 API 정의가 포함되어 있는지 여부

문제가 있는 경우 이를 해결하고 올바른 API 앱 패키지를 만들 수 있도록 세부 사항을 표시합니다.

## 다음 단계

API 앱 패키지를 Azure 마켓플레이스에 게시할 준비가 되었습니다. 방법을 알아보려면 [Azure 마켓플레이스에 API 앱 패키지 게시](app-service-api-publish-package) 자습서를 따르세요.

<!--HONumber=52-->
