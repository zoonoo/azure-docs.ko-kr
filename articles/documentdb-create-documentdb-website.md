<properties 
	pageTitle="Azure 리소스 관리자 템플릿을 사용하여 DocumentDB 및 Azure 웹 사이트 배포 | Azure" 
	description="Azure 리소스 관리자 템플릿을 사용하여 DocumentDB 계정, Azure 웹 사이트 및 샘플 웹 응용 프로그램을 배포하는 방법을 알아봅니다." 
	services="documentdb, websites" 
	authors="stephbaron" 
	manager="johnmac" 
	editor="monicar" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/23/2015" 
	ms.author="stbaro"/>

# Azure 리소스 관리자 템플릿을 사용하여 DocumentDB 및 Azure 웹 사이트 배포 #

이 자습서에서는 Azure 리소스 관리자 템플릿을 사용하여 [Microsoft Azure DocumentDB](http://azure.microsoft.com/services/documentdb/), [Azure 웹 사이트](http://azure.microsoft.com/services/websites/) 및 샘플 웹 응용 프로그램을 배포 및 통합하는 방법을 설명합니다.

이 자습서를 완료하고 나면 다음을 알게 됩니다.  

-	Azure 리소스 관리자 템플릿을 사용하여 DocumentDB 계정 및 Azure 웹 사이트를 배포 및 통합하는 방법
-	Azure 리소스 관리자 템플릿을 사용하여 DocumentDB 계정, Azure 웹 사이트 및 Webdeploy 응용 프로그램을 배포 및 통합하는 방법

##<a id="Prerequisites"></a>필수 조건 ##
> [AZURE.TIP] 이 자습서에서는 Azure 리소스 관리자 템플릿, JSON 또는 Azure PowerShell을 이전에 사용해 본 경험이 있다고 가정하지는 않지만, 참조된 템플릿 또는 배포 옵션을 수정하려면 이러한 각 영역에 대한 지식이 필요합니다.

이 자습서의 지침을 따르기 전에 다음이 있는지 확인하세요.

- Azure 구독. Azure는 구독 기반 플랫폼입니다.  구독 가져오기에 대한 자세한 내용은 [구매 옵션](http://azure.microsoft.com/pricing/purchase-options/), [구성원 제공 항목](http://azure.microsoft.com/pricing/member-offers/) 또는 [무료 평가판](http://azure.microsoft.com/pricing/free-trial/)을 참조하세요.
- Azure 저장소 계정. 자세한 내용은 [Azure 저장소 계정 정보](storage-whatis-account.md)를 참조하세요.
- Azure PowerShell이 포함된 워크스테이션. 자세한 내용은 [Azure PowerShell 설치 및 구성](install-configure-powershell.md)을 참조하세요.

##<a id="CreateDB"></a>1단계: 샘플 파일 다운로드 및 압축 풀기 ##
먼저 이 자습서에서 사용할 샘플 파일을 다운로드합니다.

1. [DocumentDB 계정, 웹 사이트 만들기 및 데모 응용 프로그램 배포 샘플](https://portalcontent.blob.core.windows.net/samples/CreateDocDBWebsiteTodo.zip)을 로컬 폴더(예: C:\DocumentDBTemplates)에 다운로드하고 파일 압축을 풉니다.  이 샘플은 DocumentDB 계정, Azure 웹 사이트 및 웹 응용 프로그램을 배포합니다.  또한 DocumentDB 계정에 연결되도록 웹 응용 프로그램을 자동으로 구성합니다.

2. [DocumentDB 계정 및 웹 사이트 만들기 샘플](https://portalcontent.blob.core.windows.net/samples/CreateDocDBWebSite.zip)을 로컬 폴더(예: C:\DocumentDBTemplates)에 다운로드하고 파일 압축을 풉니다.  이 샘플은 DocumentDB 계정 및 Azure 웹 사이트를 배포하고 쉽게 DocumentDB 연결 정보를 노출하도록 웹 사이트의 구성을 수정하지만 웹 응용 프로그램을 포함하지는 않습니다.  

> [AZURE.TIP] 컴퓨터의 보안 설정에 따라, 압축을 푼 파일을 마우스 오른쪽 단추로 클릭하고 **속성**을 클릭한 후 **차단 해제**를 클릭하여 해당 파일을 차단 해제해야 할 수 있습니다.

![Screenshot of the Properties window with the Unblock button highlighted](./media/documentdb-create-documentdb-website/image1.png)


##<a id="Build"></a>2단계: Document 계정, 웹 사이트 및 데모 응용 프로그램 샘플 배포 ##

이제 첫 번째 템플릿을 배포합니다.

> [AZURE.TIP] 템플릿은 아래에 입력된 웹 사이트 이름과 DocumentDB 계정 이름이 a) 유효한지, b) 사용 가능한지를 확인하지 않습니다.  PowerShell 배포 스크립트를 실행하기 전에 지정하려는 이름의 가용성을 확인하는 것이 좋습니다.

1. Microsoft Azure PowerShell을 열고 [DocumentDB 계정, 웹 사이트 만들기 및 데모 응용 프로그램 배포 샘플](https://portalcontent.blob.core.windows.net/samples/CreateDocDBWebsiteTodo.zip)을 다운로드하고 압축을 푼 폴더(예: C:\DocumentDBTemplates\CreateDocDBWebsiteTodo)로 이동합니다.


2. CreateDocDBWebsiteTodo.ps1 PowerShell 스크립트를 실행할 것입니다.  이 스크립트는 다음과 같은 필수 매개 변수를 사용합니다.
	- WebsiteName: 웹 사이트 이름을 지정하고 웹 사이트에 액세스하는 데 사용할 URL을 만드는 데 사용됩니다(예: "mydemodocdbwebsite"를 지정할 경우 웹 사이트에 액세스하는 데 사용되는 URL은 mydemodocdbwebsite.azurewebsites.net이 됨).

	- ResourceGroupName: 배포할 Azure 리소스 그룹의 이름을 지정합니다. 지정된 리소스 그룹이 없으면 만들어집니다.

	- docDBAccountName: 만들 DocumentDB 계정의 이름을 지정합니다.

	- location: DocumentDB 및 웹 사이트 리소스를 만들 Azure 위치를 지정합니다.  유효한 값은 East Asia, Southeast Asia, East US, West US, North Europe, West Europe입니다(입력된 location 값은 대/소문자를 구분함).


3. 다음은 스크립트를 실행하기 위한 예제 명령입니다.

    	PS C:\DocumentDBTemplates\CreateDocDBWebsiteTodo> .\CreateDocDBWebsiteTodo.ps1 -WebSiteName "mydemodocdbwebsite" -ResourceGroupName "myDemoResourceGroup" -docDBAccountName "mydemodocdbaccount" -location "West US"

	> [AZURE.TIP] 스크립트 실행의 일부로 Azure 계정 사용자 이름 및 암호를 입력하라는 메시지가 표시됩니다. 전체 배포를 완료하는 데 10~15분이 걸립니다.  	

4. 다음은 결과 출력의 예입니다. 

		VERBOSE: 1:06:00 PM - Created resource group 'myDemoResourceGroup' in location westus'
		VERBOSE: 1:06:01 PM - Template is valid.
		VERBOSE: 1:06:01 PM - Create template deployment 'Microsoft.DocumentDBWebSiteTodo'.
		VERBOSE: 1:06:08 PM - Resource Microsoft.DocumentDb/databaseAccounts 'mydemodocdbaccount' provisioning status is running
		VERBOSE: 1:06:10 PM - Resource Microsoft.Web/serverFarms 'mydemodocdbwebsite' provisioning status is succeeded
		VERBOSE: 1:06:14 PM - Resource microsoft.insights/alertrules 'CPUHigh mydemodocdbwebsite' provisioning status is succeeded
		VERBOSE: 1:06:16 PM - Resource microsoft.insights/autoscalesettings 'mydemodocdbwebsite-myDemoResourceGroup' provisioning status is succeeded
		VERBOSE: 1:06:16 PM - Resource microsoft.insights/alertrules 'LongHttpQueue mydemodocdbwebsite' provisioning status is succeeded
		VERBOSE: 1:06:21 PM - Resource Microsoft.Web/Sites 'mydemodocdbwebsite' provisioning status is succeeded
		VERBOSE: 1:06:23 PM - Resource microsoft.insights/alertrules 'ForbiddenRequests mydemodocdbwebsite' provisioning status is succeeded
		VERBOSE: 1:06:25 PM - Resource microsoft.insights/alertrules 'ServerErrors mydemodocdbwebsite' provisioning status is succeeded
		VERBOSE: 1:06:25 PM - Resource microsoft.insights/components 'mydemodocdbwebsite' provisioning status is succeeded
		VERBOSE: 1:16:22 PM - Resource Microsoft.DocumentDb/databaseAccounts 'mydemodocdbaccount' provisioning status is succeeded
		VERBOSE: 1:16:22 PM - Resource Microsoft.DocumentDb/databaseAccounts 'mydemodocdbaccount' provisioning status is succeeded
		VERBOSE: 1:16:24 PM - Resource Microsoft.Web/Sites/config 'mydemodocdbwebsite/web' provisioning status is succeeded
		VERBOSE: 1:16:27 PM - Resource Microsoft.Web/Sites/Extensions 'mydemodocdbwebsite/MSDeploy' provisioning status is running
		VERBOSE: 1:16:35 PM - Resource Microsoft.Web/Sites/Extensions 'mydemodocdbwebsite/MSDeploy' provisioning status is succeeded

		ResourceGroupName : myDemoResourceGroup
		Location          : westus
		Resources         : {mydemodocdbaccount, CPUHigh mydemodocdbwebsite, ForbiddenRequests mydemodocdbwebsite, LongHttpQueue mydemodocdbwebsite...}
		ResourcesTable    :
                    Name                                    Type                                   Location
                    ======================================  =====================================  =========
                    mydemodocdbaccount                      Microsoft.DocumentDb/databaseAccounts  westus
                    CPUHigh mydemodocdbwebsite              microsoft.insights/alertrules          eastus
                    ForbiddenRequests mydemodocdbwebsite    microsoft.insights/alertrules          eastus
                    LongHttpQueue mydemodocdbwebsite        microsoft.insights/alertrules          eastus
                    ServerErrors mydemodocdbwebsite         microsoft.insights/alertrules          eastus
                    mydemodocdbwebsite-myDemoResourceGroup  microsoft.insights/autoscalesettings   eastus
                    mydemodocdbwebsite                      microsoft.insights/components          centralus
                    mydemodocdbwebsite                      Microsoft.Web/serverFarms              westus
                    mydemodocdbwebsite                      Microsoft.Web/sites                    westus

		ProvisioningState : Succeeded


5. 샘플 응용 프로그램을 살펴보기 전에 이 템플릿 배포에서 수행한 내용을 살펴보겠습니다.

	- Azure 웹 사이트를 만들었습니다.

	- Azure DocumentDB 계정을 만들었습니다.

	- 웹 배포 패키지가 Azure 웹 사이트에 배포되었습니다.

	- Azure DocumentDB 끝점 및 주 마스터 키가 응용 프로그램 설정으로 노출되도록 Azure 웹 사이트 구성이 수정되었습니다.

	- 일련의 기본 모니터링 규칙을 만들었습니다.

	
6. 응용 프로그램을 사용하려면 웹 사이트 URL로 이동하기만 하면 됩니다(위의 예제에서 URL은 http://mydemodocdbwebsite.azurewebsites.net이 됨).  다음과 같은 웹 응용 프로그램이 표시됩니다.

	![Sample Todo application](./media/documentdb-create-documentdb-website/image2.png)

7. 계속 진행하여 2개의 작업을 만든 후 [Azure Preview 포털](https://portal.azure.com)을 엽니다.

8. 리소스 그룹 찾아보기를 선택하고 배포 중에 만든 리소스 그룹(위의 샘플에서는 myDemoResourceGroup임)을 선택합니다.

	![Screenshot of the Azure Preview portal with the myDemoResourceGroup highlighted](./media/documentdb-create-documentdb-website/image3.png)
9.  요약 렌즈에서 리소스 맵이 어떻게 모든 관련 리소스(DocumentDB 계정, 웹 사이트, 모니터링)를 보여 주는지 살펴봅니다.

	![Screenshot of the Summary lens](./media/documentdb-create-documentdb-website/image4.png)
10.  DocumentDB 계정을 클릭하고 쿼리 탐색기(계정 블레이드 아래쪽 근처에 있음)를 시작합니다.

	![Screenshot of the Resource Group and Account blades with the Query Explorer tile highlighted](./media/documentdb-create-documentdb-website/image8.png)

11. 기본 쿼리 "SELECT * FROM c"를 실행하고 결과를 검사합니다.  쿼리가 위의 7단계에서 만든 todo 항목의 JSON 표현을 검색했음을 알 수 있습니다.  자유롭게 쿼리를 실험합니다. 예를 들어 SELECT * FROM c WHERE c.isComplete = true를 실행하면 완료로 표시된 모든 todo 항목이 반환됩니다.


	![Screenshot of the Query Explorer and Results blades showing the query results](./media/documentdb-create-documentdb-website/image5.png)
12. 자유롭게 DocumentDB 포털 환경을 탐색하거나 샘플 Todo 응용 프로그램을 수정합니다.  준비가 되면 다른 템플릿을 배포합니다.
	 
##<a id="Build"></a>3단계: 문서 계정 및 웹 사이트 샘플 배포 ##

이제 두 번째 템플릿을 배포합니다.

> [AZURE.TIP] 템플릿은 아래에 입력된 웹 사이트 이름과 DocumentDB 계정 이름이 a) 유효한지, b) 사용 가능한지를 확인하지 않습니다.  PowerShell 배포 스크립트를 실행하기 전에 지정하려는 이름의 가용성을 확인하는 것이 좋습니다.

1. Microsoft Azure PowerShell을 열고 [DocumentDB 계정 및 웹 사이트 만들기 샘플](https://portalcontent.blob.core.windows.net/samples/CreateDocDBWebSite.zip)을 다운로드하고 압축을 푼 폴더(예: C:\DocumentDBTemplates\CreateDocDBWebsite)로 이동합니다.


2. CreateDocDBWebsite.ps1 PowerShell 스크립트를 실행할 것입니다.  이 스크립트는 배포한 첫 번째 템플릿과 동일한 매개 변수를 사용합니다. 즉, 다음과 같습니다.
	- WebsiteName: 웹 사이트 이름을 지정하고 웹 사이트에 액세스하는 데 사용할 URL을 만드는 데 사용됩니다(예: "myotherdocumentdbwebsite"를 지정할 경우 웹 사이트에 액세스하는 데 사용되는 URL은 myotherdocumentdbwebsite.azurewebsites.net이 됨).

	- ResourceGroupName: 배포할 Azure 리소스 그룹의 이름을 지정합니다.  지정된 리소스 그룹이 없으면 만들어집니다.

	- docDBAccountName: 만들 DocumentDB 계정의 이름을 지정합니다.

	- 	location: DocumentDB 및 웹 사이트 리소스를 만들 Azure 위치를 지정합니다.  유효한 값은 East Asia, Southeast Asia, East US, West US, North Europe, West Europe입니다(입력된 location 값은 대/소문자를 구분함).

3. 다음은 스크립트를 실행하기 위한 예제 명령입니다.

    	PS C:\DocumentDBTemplates\CreateDocDBWebSite> .\CreateDocDBWebSite.ps1 -WebSiteName "myotherdocumentdbwebsite" -ResourceGroupName "myOtherDemoResourceGroup" -docDBAccountName "myotherdocumentdbdemoaccount" -location "East US"

	> [AZURE.TIP] 스크립트 실행의 일부로 Azure 계정 사용자 이름 및 암호를 입력하라는 메시지가 표시됩니다.  전체 배포를 완료하는 데 10~15분이 걸립니다.  	

4. 배포 출력은 첫 번째 템플릿 예제와 매우 유사합니다. 
5. Azure 포털을 열기 전에 이 템플릿 배포에서 수행한 내용을 살펴보겠습니다.

	- Azure 웹 사이트를 만들었습니다.

	- Azure DocumentDB 계정을 만들었습니다.

	- 	Azure DocumentDB 끝점, 주 마스터 키 및 보조 마스터 키가 응용 프로그램 설정으로 노출되도록 Azure 웹 사이트 구성이 수정되었습니다.

	- 	일련의 기본 모니터링 규칙을 만들었습니다.

6. [Azure Preview 포털](https://portal.azure.com)을 열고 리소스 그룹 찾아보기를 선택한 후 배포 중에 만든 리소스 그룹(위의 샘플에서는 myOtherDemoResourceGroup임)을 선택합니다.
7. 요약 렌즈에서 방금 배포된 Azure 웹 사이트를 클릭합니다.

	![Screenshot of the Summary lens with the myotherdocumentdwebsite website highlighted](./media/documentdb-create-documentdb-website/image6.png)
8. 웹 사이트 블레이드에서 **모든 설정**을 클릭한 후 **응용 프로그램 설정**을 클릭하고 응용 프로그램 설정이 DocumentDB 끝점 및 각 DocumentDB 마스터 키에 대해 어떻게 제시되어 있는지 확인합니다.

	![Screenshot of the Website, Settings, and Site settings blades](./media/documentdb-create-documentdb-website/image7.png)
9. 자유롭게 Azure 포털을 계속 탐색하거나 DocumentDB [샘플](http://go.microsoft.com/fwlink/?LinkID=402386)(영문) 중 하나에 따라 고유한 DocumentDB 응용 프로그램을 만듭니다.

	
	

##<a name="NextSteps"></a>다음 단계

축하합니다. Azure 리소스 관리자 템플릿을 사용하여 DocumentDB, Azure 웹 사이트 및 샘플 웹 응용 프로그램을 배포했습니다.

- DocumentDB에 대해 자세히 알아보려면 [여기](http://azure.com/docdb)를 클릭하세요.
- Azure 웹 사이트에 대해 자세히 알아보려면 [여기](http://go.microsoft.com/fwlink/?LinkId=325362)를 클릭하세요.
- Azure 리소스 관리자 템플릿에 대해 자세히 알아보려면 [여기](https://msdn.microsoft.com/library/azure/dn790549.aspx)를 클릭하세요.

<!--HONumber=49-->