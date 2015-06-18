<properties 
	pageTitle="리소스 관리자에서 Microsoft Azure Platform 간 명령줄 인터페이스 사용" 
	description="리소스 관리자에서 Microsoft Azure 플랫폼 간 명령줄 인터페이스 사용" 
	editor="tysonn" 
	manager="timlt" 
	documentationCenter="" 
	authors="squillace" 
	services=""/>

<tags 
	ms.service="multiple" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="command-line-interface" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/05/2015" 
	ms.author="rasquill"/>

#리소스 관리자와 Azure 플랫폼간 명령줄 인터페이스를 사용 하 여

<div class="dev-center-tutorial-selector sublanding"><a href="/documentation/articles/powershell-azure-resource-manager.md" title="Windows PowerShell">Windows PowerShell</a><a href="/documentation/articles/xplat-cli-azure-resource-manager.md" title="Cross-Platform CLI" class="current">플랫폼간 CLI</a></div>

이 기사에서는 리소스 관리자와 작동 하도록 Azure 플랫폼간 명령줄 인터페이스 (xplat-cli)를 사용 하는 방법을 배웁니다. 

>[AZURE.NOTE] 리소스 관리자는 현재 미리 보기로, 및 Azure 서비스 관리와 같은 수준의 관리 기능을 제공 하지 않습니다. 
>
> 설치 하지 이미 있고 xplat-cli를 구성 하는 경우 참조 [Microsoft Azure 플랫폼간 명령줄 인터페이스 설치 및 구성][xplatsetup] 에 대 한 자세한 단계 설치 하는 방법, 구성 및 xplat-cli를 사용 합니다.


##리소스 관리자

리소스 관리자를 통해 _리소스_(데이터베이스 서버, 데이터베이스 또는 웹 사이트와 같은 사용자 관리 엔터티)의 그룹을 단일 논리 단위 또는 _리소스 그룹_으로 관리할 수 있습니다. 예를들어, 리소스 그룹 리소스로 웹사이트 및 SQL 데이터베이스 포함 될 수 있습니다.

리소스 관리자 사용을 지원 하기 위해 리소스 그룹 내의 리소스에 대 한 변경 내용을 설명 하는 더 선언적 방법 *templates*는 JSON 문서인 합니다. 템플릿 언어는 명령을 실행할 때 인라인으로 채워진 되거나 별도 JSON 파일에 저장 될 수 있는 매개 변수를 설명할 수도 있습니다. 이 옵션을 사용 하면 같은 서식 파일을 사용 하 여 간단히 다른 매개 변수를 제공 하 여 새 리소스를 쉽게 만들 수 있습니다. 예를 들어 웹 사이트를 만드는 템플릿에 사이트 이름 및 웹 사이트가 위치할 지역에 대한 매개 변수, 기타 일반 매개 변수 등을 포함합니다.

>[AZURE.NOTE] 지금은 템플릿 언어의 세부 사항은 문서화 되어있지 않습니다. 설명서를 사용할 수 있게 되면 이 항목을 업데이트하여 참조 설명서에 대한 링크를 제공할 계획입니다.
>
> 사용할 수 있습니다는 `azure group template download` 를 다운로드 하 여 템플릿 갤러리에서 Microsoft 및 파트너에서 제공 하는 템플릿을 수정 하는 명령입니다.

그룹을 수정 또는 생성하는 데 템플릿을 사용한 경우 _배포_가 만들어져서 그룹에 적용됩니다.

##인증

현재 작업 중인 xplat-cli를 통해 리소스 관리자와 회사 또는 학교 계정을 사용 하 여 Microsoft Azure에 인증 해야 합니다. Microsoft 계정 또는 .publishsettings 파일을 통해 설치한 인증서를 사용하여 인증할 수는 없습니다.

조직 계정을 사용 하 여 인증에 대 한 자세한 내용은 참조 [Microsoft Azure 플랫폼간 명령줄 인터페이스 설치 및 구성][xplatsetup].

##찾기 및 리소스 그룹 템플릿 구성

1. 기본적으로 리소스 관리자 모드를 사용할 수 없으므로 xplat-cli 리소스 관리자 명령은 사용할 수 있도록 다음 명령을 사용 해야 합니다.

		azure config mode arm

	>[AZURE.NOTE] 리소스 관리자 모드와 Azure 서비스 관리 모드는 함께 사용할 수 없습니다. 즉, 한 모드에서 만든 리소스는 다른 모드에서 관리할 수 없습니다.

2. 템플릿 작업을 할 경우 고유한 템플릿을 만들거나 템플릿 갤러리의 템플릿을 사용할 수 있습니다. 이 경우 보겠습니다 사용 하 여 템플릿 갤러리에서 하나. 갤러리의 사용 가능한 템플릿을 나열하려면 다음 명령을 사용합니다. (사용 가능한 수천 개의 템플릿 있기 때문에 반드시 결과 페이지를 매겨야 하거나 사용 하 여 **grep** 또는 **findstr**[Windows에서] 또는 즐겨 찾는 문자열 검색 명령에 흥미로운 템플릿을 찾습니다. 사용할 수 있습니다는 **--json** 옵션을 보다 쉽게 검색 하는 것에 대 한 JSON 형식의 전체 목록을 다운로드 합니다. 아래 예제에서는 호출 템플릿을 사용 하 여 **Microsoft.WebSiteSQLDatabase.0.2.6 미리 보기**.)	

		azure group template list

	응답 게시자 및 템플릿 이름을 나열 하 고 (훨씬 더 많은 수는) 있더라도 다음과 비슷한 나타납니다.

		data:    Publisher               Name
		data:    ----------------------------------------------------------------------------
		data:    Microsoft               Microsoft.WebSite.0.1.0-preview1
		data:    Microsoft               Microsoft.PHPStarterKit.0.1.0-preview1
		data:    Microsoft               Microsoft.HTML5EmptySite.0.1.0-preview1
		data:    Microsoft               Microsoft.ASPNETEmptySite.0.1.0-preview1
		data:    Microsoft               Microsoft.WebSiteMySQLDatabase.0.1.0-preview1

3. Azure 웹사이트에 만든 서식 파일의 세부 정보를 보려면 다음 명령을 사용 합니다.

		azure group template show Microsoft.WebSiteSQLDatabase.0.2.6-미리 보기

	이 서식 파일에 대 한 설명 정보를 반환 합니다. 

4. 서식 파일을 선택 했으면 (**Microsoft.WebSiteSQLDatabase.0.2.6 미리 보기를 표시 하는 azure 그룹 템플릿**), 다음 명령을 사용 하 여 다운로드할 수 있습니다.

		azure group template download Microsoft.WebSiteSQLDatabase.0.2.6-preview

	템플릿을 다운로드하면 요구 사항에 맞게 해당 템플릿을 사용자 지정할 수 있습니다. 예를 들어 다른 리소스를 템플릿에 추가할 수 있습니다.

	>[AZURE.NOTE] 사용 하 여 서식 파일을 수정할 경우는 `azure group template validate` 서식 파일을 만들거나 기존 리소스 그룹을 수정 하려면 사용 하기 전에 유효성을 검사 하는 명령입니다.

5. 사용에 대 한 리소스 그룹 템플릿을 구성 하려면 텍스트 편집기에서 템플릿 파일을 엽니다. 참고는 **매개 변수** 위쪽 JSON 컬렉션입니다. 이이 템플릿에 템플릿에 기술 된 리소스를 만드는데 필요한 매개 변수 목록을 포함 합니다. 일부 매개 변수 같은 **sku** 동안 다른 사용자 지정 하면는 값의 형식 같은 기본값을 가진 **siteName**. 
	
	서식 파일을 사용할 경우 일부로 명령줄 매개 변수 또는 매개 변수 값을 포함 하는 파일을 지정 하 여 매개 변수를 제공할 수 있습니다. 어떤 방법을 사용 하는 매개 변수 JSON 형식에서 이어야 하며 기본값이 없는 해당 키에 대 한 고유한 값을 제공 해야 합니다.

	에 대 한 매개 변수가 포함 된 파일을 만드는 예는 **Microsoft.WebSiteSQLDatabase.0.2.6 미리 보기** 서식 파일에서 사용 하 여 파일을 만들려면 다음 데이터 라는 **params.json**. 아래로 시작 하는 값으로 바꾸십시오 **_My_** 와 같은 **_MyWebSite_** 고유한 값을 사용 합니다. **siteLocation** 와 같은, 근처 Azure 지역을 지정 해야 **북유럽** 또는 **미국 중남부**. (이 예에서는 **미국 서 부**)

		{
		  "siteName": {
		    "value": "MyWebSite"
		  },
		  "hostingPlanName": {
		    "value": "MyHostingPlan"
		  },
		  "siteLocation": {
		    "value": "West US"
		  },
		  "serverName": {
		    "value": "MySQLServer"
		  },
		  "serverLocation": {
		    "value": "West US"
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


1. 저장 후의 **params.json** 파일에서 다음 명령을 사용 하 여 템플릿을 기반으로 새 리소스 그룹을 만듭니다. '-e' 매개 변수를 지정 된 **params.json** 이전 단계에서 만든 파일입니다. 대체는 **MyGroupName** 와, 사용 하려는 그룹 이름 및 **MyDataCenter** 와 **siteLocation** 에 지정 된 값 프로그램 **params.json** 템플릿 매개 변수 파일입니다.

		azure group create MyGroupName "West US" -f Microsoft.WebSiteSQLDatabase.0.2.6-preview.json -d MyDeployment -e params.json

	>[AZURE.NOTE] 이 명령은 배포를 업로드 하 고 나면 있지만 그룹의 리소스에 적용 된 배포 하기 전에 확인을 반환 합니다. 배포 상태를 확인하려면 다음 명령을 사용합니다.
	>
	> `azure group deployment show MyGroupName MyDeployment`
	> 
	> **ProvisioningState** 배포의 상태를 표시 합니다.
	> 
	> 구성이 올바르지 않아서 장기 실행 배포를 중지해야 하는 경우 다음 명령을 사용합니다.
	> 
	> `azure group deployment stop MyGroupName MyDeployment`
	> 
	> 배포 이름을 지정하지 않은 경우 이름이 템플릿 파일의 이름을 기반으로 하여 자동으로 만들어집니다. 출력의 일부로 반환 됩니다는 `azure group create` 명령 합니다.

3. 그룹을 보려면 다음 명령을 사용 합니다.

		azure group show MyGroupName

	이 명령을 사용하면 그룹의 리소스에 대한 정보가 반환됩니다. 여러 그룹이 있는 경우 사용할 수 있습니다는 `azure group list` 그룹 이름의 목록을 검색 한 다음 사용 하는 명령 `azure group show` 특정 그룹의 세부 정보를 볼 수 있습니다.

##리소스 작업

템플릿을 통해 구성에서 그룹 전체에 대한 변경을 선언할 수 있지만 특정 리소스에 대해서만 작업해야 하는 경우도 있습니다. 이렇게 하려면 사용 하는 `azure resource` 명령 합니다.

> [AZURE.NOTE] 사용 하는 경우는 `azure resource` 명령이 아닌는 `list` 명령을 사용 하 여 사용 하는 리소스의 API 버전을 지정 해야는 '-매개 변수 o '. API 버전을 사용 하는 방법에 대 한 알 수 없는 경우 서식 파일을 확인 하 고 찾기는 **apiVersion** 리소스에 대 한 필드입니다.

1. 그룹의 모든 리소스를 나열 하려면 다음 명령을 사용 합니다.

		azure resource list MyGroupName

1. 그룹 내 웹사이트와 같은 개별 리소스를 보려면 다음 명령을 사용 합니다.

		azure resource show MyGroupName MyWebSiteName Microsoft.Web/sites -o "2014-04-01"

	공지는 **Microsoft.Web/sites** 매개 변수입니다. 이 정보를 요청 하는 리소스의 유형을 나타냅니다. 이전에 다운로드한 템플릿 파일을 살펴보면 템플릿에서 설명한 웹 사이트 리소스의 유형을 정의하는 데 동일한 값을 사용한 것을 알 수 있습니다.

	이 명령을 실행하면 웹 사이트 관련 정보가 반환됩니다. 예는 **호스트 이름을** 필드는 웹사이트에 대 한 URL을 포함 해야 합니다. 브라우저에서 이 URL을 사용하여 웹 사이트가 실행 중인지 확인할 수 있습니다.

2. 리소스에 세부 정보를 볼 때 유용 자주 사용 하는 '-json'으로이 매개 변수를 사용 하면 출력 일부 값은 중첩 된 구조 또는 컬렉션으로 읽을 수 있습니다. 다음은 show 명령의 결과를 JSON 문서로 반환하는 명령의 예입니다.

		azure resource show MyGroupName MyWebSite Microsoft.Web/sites -o "2014-04-01" --json

	>[AZURE.NOTE] 사용 하 여 JSON 데이터 파일에 저장할 수는 &gt; 문자를 파일에 출력을 파이프 합니다. 예를 들면 다음과 같습니다.
	>
	> `azure resource show MyGroupName MyWebSite Micrsoft.Web/sites --json > myfile.json`

3. 기존 리소스를 삭제 하려면 다음 명령을 사용 합니다.

		azure resource delete MyGroupName MyWebSite Microsoft.Web/sites -o "2014-04-01"

##로깅

그룹에 수행한 작업에 기록 된 정보를 보려면 사용 하는 `azure group log show` 명령 합니다. 기본적으로 그룹에 수행한 마지막 작업을 목록이 표시 됩니다. 모든 작업을 보려면 `--all` 매개 변수 옵션을 사용합니다. 마지막 배포의 경우 `--last-deployment`를 사용합니다. 특정 배포의 경우 `--deployment`를 사용하고 배포 이름을 지정합니다. 다음 예제에서는 그룹에 대해 수행한 모든 작업의 로그가 반환 'MyGroup'.

	azure group log show mygroup --all

##다음 단계

* Azure 플랫폼간 명령줄 인터페이스 사용에 대 한 자세한 내용은 참조 [Microsoft Azure 플랫폼간 명령줄 인터페이스 설치 및 구성][xplatsetup].
* Windows Azure PowerShell을 사용 하 여 리소스 관리자와 작업에 대 한 자세한 내용은 [리소스 관리자와 Windows PowerShell을 사용 하 여 시작][psrm]

[signuporg]: http://www.windowsazure.com/documentation/articles/sign-up-organization/
[adtenant]: http://technet.microsoft.com/library/jj573650#createAzureTenant
[portal]: https://manage.windowsazure.com/
[xplatsetup]: /documentation/articles/xplat-cli/
[psrm]: http://go.microsoft.com/fwlink/?LinkId=394760

<!--HONumber=47-->
 