<properties 
	pageTitle="Microsoft Azure 웹 사이트의 준비된 배포" 
	description="Microsoft Azure 웹 사이트에서 준비된 게시를 사용하는 방법에 대해 알아봅니다." 
	services="web-sites" 
	documentationCenter="" 
	authors="cephalin" 
	writer="cephalin" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="9/9/2014" 
	ms.author="cephalin"/>

<a name="Overview"></a>
#Microsoft Azure 웹 사이트의 준비된 배포
Azure 웹 사이트에 응용 프로그램을 배포할 때 기본 프로덕션 슬롯 대신 별도의 배포 슬롯을 배포할 수 있습니다. 이것은 실제로는 자체적인 호스트 이름을 가진 라이브 사이트입니다. 이 옵션은 **표준** 웹 호스팅 계획에서 사용할 수 있습니다. 또한 프로덕션 슬롯을 포함하여 두 배포 슬롯 간에 사이트 및 사이트 구성을 교환할 수 있습니다. 응용 프로그램을 배포 슬롯에 배포하면 다음과 같은 이점이 있습니다.

- 프로덕션 슬롯과 교환하기 전에 준비 배포 슬롯에서 웹 사이트 변경 사항의 유효성을 검사할 수 있습니다.

- 교환 후에는 이전의 준비된 사이트가 들어 있던 슬롯 안에 이전의 프로덕션 사이트가 들어갑니다. 프로덕션 슬롯과 교환한 변경 내용이 예상과 다른 경우 같은 교환 작업을 즉시 수행하여 "마지막 양호 상태"로 돌아갈 수 있습니다. 
 
- 슬롯을 먼저 배포하고 프로덕션으로 교환하기 때문에 프로덕션으로 교환되기 전에 슬롯에 있는 모든 인스턴가 준비되어 있는 상태입니다. 따라서 사이트를 배포할 때 가동 중지가 발생하지 않습니다. 트래픽 리디렉션은 중단 없이 원활하게 수행되며 교환 작업으로 인해 삭제되는 요청은 없습니다. 

**표준** 계획에서는 각 웹 사이트에 대해 프로덕션 슬롯 외 4개의 배포 슬롯이 지원됩니다. 

## 목차 ##
- [웹 사이트에 배포 슬롯 추가](#Add)
- [배포 슬롯의 구성 정보](#AboutConfiguration)
- [배포 슬롯 교환](#Swap)
- [프로덕션 사이트를 준비 사이트로 롤백](#Rollback)
- [사이트 슬롯 삭제](#Delete)
- [사이트 슬롯용 Azure PowerShell cmdlet](#PowerShell)
- [사이트 슬롯용 Azure 플랫폼 간 명령줄 인터페이스(xplat-cli) 명령](#CLI)

<a name="Add"></a>
##웹 사이트에 배포 슬롯 추가##

다중 배포 슬롯을 사용하려면 웹 사이트가 **표준** 호스팅 계획으로 실행 중이어야 합니다. 

1. 빠른 시작 페이지나 웹 사이트 대시보드 페이지의 빠른 보기 섹션에서 **새 배포 슬롯 추가**를 클릭합니다. 
	
	![Add a new deployment slot][QGAddNewDeploymentSlot]
	
	> [AZURE.NOTE]
	> 웹 사이트가 아직 **표준** 모드가 아닌 경우 **준비된 게시를 사용하려면 표준 모드여야 합니다** 메시지가 수신됩니다. 이때 **업그레이드**를 선택할 수 있는 옵션이 제공되고 계속하기 전에 웹 사이트의 **크기 조정** 탭으로 이동합니다.
	
2. **새 배포 슬롯 추가** 대화 상자에서 슬롯에 이름을 지정하고, 다른 기존 배포 슬롯으로부터 웹 사이트 구성을 복제할 것인지 여부를 선택합니다. 확인 표시를 클릭하여 계속합니다. 
	
	![Configuration Source][ConfigurationSource1]
	
	슬롯을 처음 만들 때는 두 가지 선택만 가능합니다. 프로덕션의 기본 슬롯으로부터 구성을 복제하거나, 구성을 아예 복제하지 않는 것입니다. 
	
	여러 개의 슬롯을 만든 후에는 프로덕션이 아닌 슬롯으로부터 구성을 복제할 수 있습니다.
	
	![Configuration sources][MultipleConfigurationSources]

5. 웹 사이트 목록에서 배포 슬롯에 표시할 웹 사이트 이름의 왼쪽에 있는 표시를 확장합니다. 웹 사이트 이름 뒤에 배포 슬롯 이름이 표시됩니다. 
	
	![Site List with Deployment Slot][SiteListWithStagedSite]
	
4. 배포 사이트 슬롯의 이름을 클릭하면 다른 웹 사이트와 마찬가지로 일련의 탭이 있는 페이지가 열립니다. <strong><i>웹-사이트-이름</i>(<i>배포-슬롯-이름</i>)</strong>이 포털 페이지의 맨 위에 나타나서 현재 배포 사이트 슬롯을 보는 중임을 알립니다.
	
	![Deployment Slot Title][StagingTitle]
	
5. 대시보드 뷰에서 사이트 URL을 클릭합니다. 배포 슬롯은 고유의 호스트 이름을 가지고 있고 라이브 사이트이기도 합니다. 배포 슬롯에 대한 공용 액세스를 제한하려면 [Azure 웹 사이트 - 비 프로덕션 배포 슬롯에 대한 웹 액세스 차단](http://ruslany.net/2014/04/azure-web-sites-block-web-access-to-non-production-deployment-slots/)을 참조하세요.

	-	 

콘텐츠가 없습니다. 다른 리포지토리 분기 또는 아예 다른 리포지토리로부터 슬롯에 배포할 수 있습니다. 슬롯의 구성을 변경할 수도 있습니다. 게시 프로필을 사용하거나 콘텐츠 업데이트를 위해 배포 슬롯에 연결된 배포 자격 증명을 사용합니다.  예를 들어 [git를 사용하여 이 슬롯에 게시](http://azure.microsoft.com/documentation/articles/web-sites-publish-source-control/)할 수 있습니다.

<a name="AboutConfiguration"></a>
##배포 슬롯의 구성 정보##
다른 배포 슬롯으로부터 구성을 복제할 때 복제된 구성을 편집할 수 있습니다. 다음 목록은 슬롯을 교환할 때 변경되는 구성을 보여 줍니다.

**슬롯 교환 시 변경될 구성**:

- 일반 설정
- 연결 문자열
- 처리기 매핑
- 모니터링 및 진단 설정

**슬롯 교환 시 변경되지 않을 구성**:

- 게시 끝점
- 사용자 지정 도메인 이름
- SSL 인증서 및 바인딩
- 크기 조정 설정

**참고**:

- 다중 배포 슬롯은 **표준** 웹 호스팅 계획을 사용하는 사이트에서만 사용할 수 있습니다.

- 사이트에 여러 개의 슬롯이 있을 때는 호스팅 계획을 변경할 수 없습니다.

- 프로덕션으로 교환하려는 슬롯은 프로덕션에 배치되었을 때를 염두에 두고 그와 동일하게 구성해야 합니다.

- 기본적으로 배포 슬롯은 프로덕션 사이트와 동일한 데이터베이스를 가리킵니다. 하지만 배포 슬롯의 데이터베이스 연결 문자열을 변경하여 다른 데이터베이스를 가리키도록 배포 슬롯을 구성할 수 있습니다. 그런 다음 배포 슬롯의 원래 데이터베이스 연결 문자열을 프로덕션으로 교환하기 직전에 복원할 수 있습니다.


<a name="Swap"></a>
##배포 슬롯 교환##

1. 배포 슬롯을 교환하려면 교환하려는 웹 사이트 목록에서 배포 슬롯을 선택하고 명령 모음에서 **교환** 단추를 클릭합니다. 
	
	![Swap Button][SwapButtonBar]
	
2. Swap Deployments 대화 상자가 나타납니다. 대화 상자에서 소스가 되어야 할 사이트 슬롯 및 대상이 되어야 할 사이트를 선택할 수 있습니다.
	
	![Swap Deployments Dialog][SwapDeploymentsDialog]
	
3. 확인 표시를 클릭하여 작업을 완료합니다. 작업을 마치면 사이트 슬롯이 교환됩니다.


<a name="Rollback"></a>
##프로덕션 사이트를 준비 사이트로 롤백##
슬롯 교환 후 프로덕션에서 오류가 발견되면 같은 두 슬롯을 즉시 교환하여 슬롯을 교환 전 상태로 롤백합니다. 

<a name="Delete"></a>
##사이트 슬롯 삭제##

Azure 웹 사이트 포털 페이지 맨 아래에 있는 명령 모음에서 **삭제**를 클릭합니다. 웹 사이트와 모든 배포 슬롯을 삭제할지, 아니면 배포 슬롯만 삭제할지에 대한 옵션이 제공됩니다. 

![Delete a Site Slot][DeleteStagingSiteButton]

**참고**:

- 프로덕션이 아닌 슬롯에 대해 크기 조정을 사용할 수 없습니다. 크기 조정은 프로덕션 슬롯에 대해서만 사용할 수 있습니다.

- 연결된 리소스 관리는 프로덕션이 아닌 슬롯에 대해 지원되지 않습니다. 

- 원하는 경우 프로덕션 슬롯에 바로 게시할 수 있습니다.

- 기본적으로 배포 슬롯(사이트)은 프로덕션 슬롯(사이트)과 동일한 리소스를 공유하고 동일한 VM에서 실행됩니다. 준비 슬롯에 스트레스 테스트를 실행하는 경우 프로덕션 환경에 비교 가능한 스트레스 로드가 발생합니다. 
	
	> [AZURE.NOTE] 특별히 [Azure Preview 포털](https://portal.azure.com)에서는 프로덕션이 아닌 슬롯을 다른 웹 호스팅 계획으로 일시적으로 전환함으로써 프로덕션 슬롯에 미칠 수 있는 영향을 방지할 수 있습니다. 테스트 슬롯을 프로덕션으로 교환하기 전에 테스트 및 프로덕션 슬롯이 다시 한 번 같은 웹 호스팅 계획을 공유해야 합니다.

<!-- ======== AZURE POWERSHELL CMDLETS =========== -->

<a name="PowerShell"></a>
##사이트 슬롯용 Azure PowerShell cmdlet 

Azure PowerShell은 Windows PowerShell을 통해 Azure를 관리하기 위한 cmdlet을 제공하는 모듈로, Azure 웹 사이트 배포 슬롯을 관리하는 기능도 지원합니다. 

- Azure PowerShell을 설치 및 구성하는 방법과 Microsoft Azure 구독에 Azure PowerShell을 인증하는 방법에 대한 자세한 내용은 [Microsoft Azure PowerShell 설치 및 구성 방법](http://azure.microsoft.com/documentation/articles/install-configure-powershell)을 참조하세요.  

- PowerShell에서 Azure 웹 사이트에 사용할 수 있는 cmdlet을 나열하려면  `help AzureWebsite`를 호출합니다. 

----------

###Get-AzureWebsite
다음 예에서와 같이 **Get-AzureWebsite** cmdlet은 현재 구독의 Azure 웹 사이트 관련 정보를 제공합니다. 

`Get-AzureWebsite siteslotstest`

----------

###New-AzureWebsite
**New-AzureWebsite** cmdlet을 사용하여 사이트 및 슬롯 모두의 이름을 지정하면 표준 모드에서 웹 사이트의 사이트 슬롯을 만들 수 있습니다. 다음 예에서와 같이 배포 슬롯을 만드는 사이트와 동일한 영역을 나타낼 수도 있습니다. 

`New-AzureWebsite siteslotstest -Slot staging -Location "West US"`

----------

###Publish-AzureWebsiteProject
다음 예에서와 같이 콘텐츠 배포에 **Publish-AzureWebsiteProject** cmdlet을 사용할 수 있습니다. 

`Publish-AzureWebsiteProject -Name siteslotstest -Slot staging -Package [path].zip`

----------

###Show-AzureWebsite
콘텐츠 및 구성 업데이트를 새 슬롯에 적용한 후 다음 예에서와 같이 **Show-AzureWebsite** cmdlet을 사용하여 슬롯으로 이동한 후 업데이트의 유효성을 검사할 수 있습니다.

`Show-AzureWebsite -Name siteslotstest -Slot staging`

----------

###Switch-AzureWebsiteSlot
다음 예에서와 같이 **Switch-AzureWebsiteSlot** cmdlet은 교환 작업을 수행하여 업데이트된 배포 슬롯을 프로덕션 사이트로 만들 수 있습니다. 이때 프로덕션 사이트에는 중단 시간이나 콜드 부팅이 발생하지 않습니다. 

`Switch-AzureWebsiteSlot -Name siteslotstest`

----------

###Remove-AzureWebsite
배포 슬롯이 더 이상 필요하지 않은 경우 다음 예에서와 같이 **Remove-AzureWebsite** cmdlet을 사용하여 삭제할 수 있습니다.

`Remove-AzureWebsite -Name siteslotstest -Slot staging` 

----------

<!-- ======== XPLAT-CLI =========== -->

<a name="CLI"></a>
##사이트 슬롯용 Azure 플랫폼 간 명령줄 인터페이스(xplat-cli) 명령

Azure 플랫폼 간 명령줄 인터페이스(xplat-cli)는 Azure 플랫폼 작업을 위한 플랫폼 간 오픈 소스 명령 집합을 제공하며, Azure 웹 사이트의 배포 슬롯을 관리하는 기능을 지원합니다. 

- xplat-cli 설치 및 구성 지침과 xplat-cli를 Azure 구독에 연결하는 방법에 대한 자세한 내용은 [Azure 플랫폼 간 명령줄 인터페이스 설치 및 구성](http://azure.microsoft.com/documentation/articles/xplat-cli)을 참조하세요. 

-  xplat-cli에서 Azure 웹 사이트에 사용할 수 있는 명령을 나열하려면  `azure site -h`를 호출합니다. 

----------
###azure site list
현재 구독의 Azure 웹 사이트 관련 정보를 확인하려면 다음 예에서와 같이 **azure site list**를 호출합니다.
 
`azure site list siteslotstest`

----------
###azure site create
표준 모드에서 웹 사이트의 사이트 슬롯을 만들려면 다음 예에서와 같이 **azure site create**를 호출하고 기존 사이트의 이름과 만들 슬롯의 이름을 지정합니다.

`azure site create siteslotstest --slot staging`

새 슬롯의 소스 제어를 사용하도록 설정하려면 다음 예에서와 같이 **--git** 옵션을 사용합니다.
 
`azure site create --git siteslotstest --slot staging`

----------
###azure site swap
업데이트된 배포 슬롯을 프로덕션 사이트로 만들려면 다음 예에서와 같이 **azure site swap** 명령을 사용하여 교환 작업을 수행합니다. 이때 프로덕션 사이트에는 중단 시간이나 콜드 부팅이 발생하지 않습니다. 

`azure site swap siteslotstest`

----------
###azure site delete
더 이상 필요하지 않은 배포 슬롯을 삭제하려면 다음 예에서와 같이 **azure site delete** 명령을 사용합니다.

`azure site delete siteslotstest --slot staging`

----------
## 다음 단계 ##
[Azure 웹 사이트 - 비 프로덕션 배포 슬롯에 대한 웹 액세스 차단](http://ruslany.net/2014/04/azure-web-sites-block-web-access-to-non-production-deployment-slots/)

[Microsoft Azure 무료 평가판](http://azure.microsoft.com/pricing/free-trial/)


<!-- IMAGES -->
[QGAddNewDeploymentSlot]:  ./media/web-sites-staged-publishing/QGAddNewDeploymentSlot.png
[AddNewDeploymentSlotDialog]: ./media/web-sites-staged-publishing/AddNewDeploymentSlotDialog.png
[ConfigurationSource1]: ./media/web-sites-staged-publishing/ConfigurationSource1.png
[MultipleConfigurationSources]: ./media/web-sites-staged-publishing/MultipleConfigurationSources.png
[SiteListWithStagedSite]: ./media/web-sites-staged-publishing/SiteListWithStagedSite.png
[StagingTitle]: ./media/web-sites-staged-publishing/StagingTitle.png
[SwapButtonBar]: ./media/web-sites-staged-publishing/SwapButtonBar.png
[SwapConfirmationDialog]:  ./media/web-sites-staged-publishing/SwapConfirmationDialog.png
[DeleteStagingSiteButton]: ./media/web-sites-staged-publishing/DeleteStagingSiteButton.png
[SwapDeploymentsDialog]: ./media/web-sites-staged-publishing/SwapDeploymentsDialog.png


<!--HONumber=42-->
