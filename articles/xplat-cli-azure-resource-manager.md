<properties linkid="script-xplat-intro" urlDisplayName="Microsoft Azure Cross-Platform Command-Line Interface" pageTitle="Using Microsoft Azure Cross-Platform Command-Line Interface with the Resource Manager" title="Using Microsoft Azure Cross-Platform Command-Line Interface with the Resource Manager" metaKeywords="windows azure cross-platform command-line interface Resource Manager, windows azure command-line resource manager, azure command-line resource manager, azure cli resource manager" description="Use the Microsoft Azure Cross-Platform Command-Line Interface with the Resource Manager" metaCanonical="http://www.windowsazure.com/ko-KR/script/xplat-cli-intro" umbracoNaviHide="0" disqusComments="1" editor="mollybos" manager="paulettm" documentationCenter="" solutions="" authors="larryfr" services="" />

<tags ms.service="multiple" ms.workload="multiple" ms.tgt_pltfrm="command-line-interface" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr"></tags>

# 리소스 관리자에서 Azure 플랫폼 간 명령줄 인터페이스 사용

<div class="dev-center-tutorial-selector sublanding"><a href="/ko-KR/documentation/articles/powershell-azure-resource-manager.md" title="Windows PowerShell">Windows PowerShell</a><a href="/ko-KR/documentation/articles/xplat-cli-azure-resource-manager.md" title="크로스 플랫폼 CLI" class="current">크로스 플랫폼 CLI</a></div>

최근에 Microsoft Azure를 관리하는 새로운 방법인 리소스 관리자의 Preview가 도입되었습니다. 이 문서에서는 Azure 플랫폼 간 명령줄 인터페이스(xplat-cli)를 사용하여 리소스 관리자 작업을 수행하는 방법에 대해 알아봅니다.

> [WACOM.NOTE] 리소스 관리자는 현재 Preview 상태이며, Azure 서비스 관리와 동일한 수준의 관리 기능을 제공하지 않습니다.

> [WACOM.NOTE] xplat-cli를 아직 설치 및 구성하지 않은 경우 xplat-cli를 설치, 구성 및 사용하는 방법에 대한 자세한 단계는 [Microsoft Azure 플랫폼 간 명령줄 인터페이스 설치 및 구성][]을 참조하세요.

## 리소스 관리자

리소스 관리자를 통해 *리소스*(데이터베이스 서버, 데이터베이스 또는 웹 사이트와 같은 사용자 관리 엔터티)의 그룹을 단일 논리 단위 또는 *리소스 그룹*으로 관리할 수 있습니다. 예를 들어 리소스 그룹에는 웹 사이트 및 SQL 데이터베이스 리소스가 포함될 수 있습니다.

리소스 그룹 내의 리소스에 대한 변경 내용을 더욱 선언적으로 설명하는 방법을 지원하기 위해 리소스 관리자는 JSON 문서인 *템플릿*을 사용합니다. 또한 템플릿 언어를 통해 명령을 실행할 때 인라인으로 채워지거나 별도의 JSON 파일에 저장될 수 있는 매개 변수를 설명할 수도 있습니다. 이를 통해 간단히 다른 매개 변수를 지정하여 동일한 템플릿을 사용하는 새 리소스를 쉽게 만들 수 있습니다. 예를 들어 웹 사이트를 만드는 템플릿에 사이트 이름 및 웹 사이트가 위치할 지역에 대한 매개 변수, 기타 일반 매개 변수 등을 포함합니다.

> [WACOM.NOTE] 지금은 템플릿 언어에 대한 설명이 문서화되지 않았습니다. 설명서를 사용할 수 있게 되면 이 항목을 업데이트하여 참조 설명서에 대한 링크를 제공할 계획입니다.
>
> 그러나 `azure group template download` 명령을 사용하여 템플릿 갤러리에서 Microsoft 및 파트너에서 제공한 템플릿을 다운로드하여 수정할 수 있습니다.

그룹을 수정 또는 생성하는 데 템플릿을 사용한 경우 *배포*가 만들어져서 그룹에 적용됩니다.

## 인증

현재 xplat-cli를 통해 리소스 관리자에 대한 작업을 하려면 조직 계정을 사용하여 Microsoft Azure에 인증해야 합니다. Microsoft 계정 또는 .publishsettings 파일을 통해 설치한 인증서를 사용하여 인증할 수는 없습니다.

조직 계정을 사용하여 인증하는 방법에 대한 자세한 내용은 [Microsoft Azure 플랫폼 간 명령줄 인터페이스 설치 및 구성][]을 참조하십시오.

## 그룹 및 템플릿 작업

1.  리소스 관리자는 현재 Preview 상태이므로 리소스 관리자에서 작동하는 xplat-cli 명령은 기본적으로 사용하지 않게 설정되어 있습니다. 다음 명령을 사용하여 해당 명령을 사용하도록 설정합니다.

        azure config mode arm

    > [WACOM.NOTE] 리소스 관리자 모드와 Azure 서비스 관리 모드는 함께 사용할 수 없습니다. 즉, 한 모드에서 만든 리소스는 다른 모드에서 관리할 수 없습니다.

2.  템플릿 작업을 할 경우 고유한 템플릿을 만들거나 템플릿 갤러리의 템플릿을 사용할 수 있습니다. 갤러리의 사용 가능한 템플릿을 나열하려면 다음 명령을 사용합니다.

        azure group template list

    그러면 게시자 및 템플릿 이름이 다음과 유사하게 표시됩니다.

        data:    Publisher               Name
        data:    ----------------------------------------------------------------------------
        data:    Microsoft               Microsoft.WebSite.0.1.0-preview1
        data:    Microsoft               Microsoft.PHPStarterKit.0.1.0-preview1
        data:    Microsoft               Microsoft.HTML5EmptySite.0.1.0-preview1
        data:    Microsoft               Microsoft.ASPNETEmptySite.0.1.0-preview1
        data:    Microsoft               Microsoft.WebSiteMySQLDatabase.0.1.0-preview1

3.  Azure 웹 사이트를 만드는 템플릿의 세부 정보를 보려면 다음 명령을 사용합니다.

        azure group template show Microsoft.WebSiteSQLDatabase.0.1.0-preview1

    그러면 템플릿에 대한 설명 정보가 반환됩니다.

4.  템플릿을 선택한 후 다음 명령을 사용하여 해당 템플릿을 다운로드할 수 있습니다.

        azure group template download Microsoft.WebSiteSQLDatabase.0.1.0-preview1

    템플릿을 다운로드하면 요구 사항에 맞게 해당 템플릿을 사용자 지정할 수 있습니다. 예를 들어 다른 리소스를 템플릿에 추가할 수 있습니다.

    > [WACOM.NOTE] 템플릿을 수정한 경우 해당 템플릿을 사용하여 기존 리소스 그룹을 만들거나 수정하기 전에 `azure group template validate` 명령을 사용하여 템플릿의 유효성을 검사합니다.

5.  텍스트 편집기에서 템플릿 파일을 엽니다. 위쪽에 있는 **parameters** 컬렉션을 확인하십시오. 여기에는 템플릿에 기술된 리소스를 만드는 데 필요한 매개 변수 목록이 포함되어 있습니다. **sku**와 같은 일부 매개 변수에는 기본값이 있지만 **siteName**과 같은 다른 매개 변수의 경우에는 간단히 값의 형식을 지정해야 합니다. 템플릿을 사용할 경우 명령줄 매개 변수의 일부로 매개 변수를 지정하거나 매개 변수 값이 포함된 파일을 지정하여 매개 변수를 지정할 수 있습니다. 어떤 방법으로든 매개 변수는 JSON 형식이어야 합니다.

    Microsoft.WebSiteSQLDatabase.0.1.0-preview1 템플릿의 매개 변수가 포함된 파일을 만들려면 다음 데이터를 사용하여 이름이 **params.json**인 파일을 만듭니다. **MyWebSite**와 같이 **My**로 시작하는 값을 고유한 값으로 바꿉니다. **siteLocation**은 **North Europe** 또는 **South Central US**와 같이 근처 Azure 지역을 지정합니다.

        {
          "siteName": {
            "value": "MyWebSite"
          },
          "hostingPlanName": {
            "value": "MyHostingPlan"
          },
          "siteLocation": {
            "value": "North Europe"
          },
          "serverName": {
            "value": "MySQLServer"
          },
          "serverLocation": {
            "value": "North Europe"
          },
          "administratorLogin": {
            "value": "MySQLAdmin"
          },
          "administratorLoginPassword": {
            "value": "MySQLAdminPassword"
          },
          "databaseName": {
            "value": "MySQLDB"
          }
        }

6.  **params.json** 파일을 저장한 후 다음 명령을 사용하여 템플릿에 따라 새 리소스 그룹을 만듭니다. `-e` 매개 변수에서는 이전 단계에서 만든 **params.json** 파일을 지정합니다.

        azure group create MyGroupName "MyDataCenter" -y Microsoft.WebSiteSQLDatabase.0.1.0-preview1 -d MyDeployment -e params.json

    **MyGroupName**을 사용하려는 그룹 이름으로 바꾸고, **MyDataCenter**를 템플릿에서 지정한 **siteLocation** 값으로 바꿉니다.

    > [WACOM.NOTE] 이 명령을 실행하는 경우 배포가 업로드되면 OK가 반환되지만 배포가 아직 그룹의 리소스에 적용되기 전입니다. 배포 상태를 확인하려면 다음 명령을 사용합니다.
    >
    > `azure group deployment show MyGroupName MyDeployment`
    >
    > **ProvisioningState**에서 배포 상태를 보여 줍니다.
    >
    > 구성이 올바르지 않아서 장기 실행 배포를 중지해야 하는 경우 다음 명령을 사용합니다.
    >
    > `azure group deployment stop MyGroupName MyDeployment`
    >
    > 배포 이름을 지정하지 않은 경우 이름이 템플릿 파일의 이름을 기반으로 하여 자동으로 만들어집니다. 그리고 `azure group create` 명령의 출력 일부로 반환됩니다.

7.  그룹을 보려면 다음 명령을 사용합니다.

        azure group show MyGroupName

    이 명령을 사용하면 그룹의 리소스에 대한 정보가 반환됩니다. 여러 그룹이 있는 경우 `azure group list` 명령을 사용하여 그룹 이름 목록을 검색한 후 `azure group show` 명령을 사용하여 특정 그룹의 세부 정보를 확인할 수 있습니다.

## 리소스 작업

템플릿을 통해 구성에서 그룹 전체에 대한 변경을 선언할 수 있지만 특정 리소스에 대해서만 작업해야 하는 경우도 있습니다. 이러한 경우에는 `azure resource` 명령을 사용하여 작업을 수행할 수 있습니다.

> [WACOM.NOTE] `list` 명령이 아닌 `azure resource` 명령을 사용할 경우 `-o` 매개 변수를 사용하여 작업 중인 리소스의 API 버전을 지정해야 합니다. 어떤 API 버전을 사용해야 하는지 확실하지 않은 경우 템플릿 파일을 참조하여 리소스의 **apiVersion** 필드를 찾아봅니다.

1.  그룹의 모든 리소스를 나열하려면 다음 명령을 사용합니다.

        azure resource list MyGroupName

2.  웹 사이트와 같이 그룹 내의 개별 리소스를 보려면 다음 명령을 사용합니다.

        azure resource show MyGroupName MyWebSiteName Microsoft.Web/sites -o "2014-04-01"

    **Microsoft.Web/sites** 매개 변수를 확인합니다. 이 매개 변수는 정보를 요청하는 대상 리소스의 유형을 나타냅니다. 이전에 다운로드한 템플릿 파일을 살펴보면 템플릿에서 설명한 웹 사이트 리소스의 유형을 정의하는 데 동일한 값을 사용한 것을 알 수 있습니다.

    이 명령을 실행하면 웹 사이트 관련 정보가 반환됩니다. 예를 들어 **hostNames** 필드에는 웹 사이트의 URL이 포함되어 있습니다. 브라우저에서 이 URL을 사용하여 웹 사이트가 실행 중인지 확인할 수 있습니다.

3.  리소스에 대한 세부 정보를 확인하는 경우 `--json` 매개 변수를 사용하면 일부 값이 중첩 구조 또는 컬렉션으로 표시되어 더 읽기 쉽게 출력되므로 상당히 유용합니다. 다음은 show 명령의 결과를 JSON 문서로 반환하는 명령의 예입니다.

        azure resource show MyGroupName MyWebSite Microsoft.Web/sites -o "2014-04-01" --json

    > [WACOM.NOTE] \> 문자를 사용하여 출력을 파일로 연결함으로써 JSON 데이터를 파일로 저장할 수 있습니다. 예를 들면 다음과 같습니다.
    >
    > `azure resource show MyGroupName MyWebSite Micrsoft.Web/sites --json > myfile.json`

4.  기존 리소스를 삭제하려면 다음 명령을 사용합니다.

        azure resource delete MyGroupName MyWebSite Microsoft.Web/sites -o "2014-04-01"

## 로깅

그룹에 수행한 작업에 대해 로깅된 정보를 보려면 `azure group log show` 명령을 사용합니다. 기본적으로 이 명령은 그룹에 대해 수행한 마지막 작업을 나열합니다. 모든 작업을 보려면 `--all` 매개 변수 옵션을 사용합니다. 마지막 배포의 경우 `--last-deployment`를 사용합니다. 특정 배포의 경우 `--deployment`를 사용하고 배포 이름을 지정합니다. 다음 예제를 실행하면 그룹 'MyGroup'에 대해 수행한 모든 작업의 로그가 반환됩니다.

    azure group log show mygroup --all

## 다음 단계

-   Azure 플랫폼 간 명령줄 인터페이스를 사용하는 방법에 대한 자세한 내용은 [Microsoft Azure 플랫폼 간 명령줄 인터페이스 설치 및 구성][]을 참조하십시오.
-   Azure PowerShell을 사용하는 리소스 관리자 작업에 대한 자세한 내용은 [리소스 관리자와 함께 Windows PowerShell 사용 시작][](영문)을 참조하십시오.

  [Windows PowerShell]: /ko-KR/documentation/articles/powershell-azure-resource-manager.md "Windows PowerShell"
  [크로스 플랫폼 CLI]: /ko-KR/documentation/articles/xplat-cli-azure-resource-manager.md "크로스 플랫폼 CLI"
  [Microsoft Azure 플랫폼 간 명령줄 인터페이스 설치 및 구성]: /ko-KR/documentation/articles/xplat-cli/
  [리소스 관리자와 함께 Windows PowerShell 사용 시작]: http://go.microsoft.com/fwlink/?LinkId=394760
