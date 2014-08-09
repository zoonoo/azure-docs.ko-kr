<properties linkid="web-sites-staged-publishing" urlDisplayName="How to stage sites on Microsoft Azure" pageTitle="Staged Deployment on Microsoft Azure Web Sites" metaKeywords="Microsoft Azure Web Sites, Staged Deployment, Site Slots" description="Learn how to use staged publishing on Microsoft Azure Web Sites." metaCanonical="" services="web-sites" documentationCenter="" title="Staged Deployment on Microsoft Azure Web Sites" authors="timamm" solutions="" writer="timamm" manager="paulettm" editor="mollybos" />

Microsoft Azure 웹 사이트의 준비된 배포
=======================================

목차
----

-   [개요](#Overview)
-   [웹 사이트에 배포 슬롯 추가](#Add)
-   [배포 슬롯의 구성 정보](#AboutConfiguration)
-   [배포 슬롯 교환](#Swap)
-   [프로덕션 사이트를 준비 사이트로 롤백](#Rollback)
-   [사이트 슬롯 삭제](#Delete)
-   [사이트 슬롯용 Azure PowerShell cmdlet](#PowerShell)
-   [사이트 슬롯용 Azure 플랫폼 간 명령줄 인터페이스(xplat-cli) 명령](#CLI)

## 개요 ##
Microsoft Azure 웹 사이트에서 실행되는 표준 모드 사이트에 대해 사이트 슬롯을 만드는 옵션을 사용하면 준비된 배포 워크플로가 사용하도록 설정됩니다. 각 기본 프로덕션 사이트에 대해 개발 또는 준비 사이트 슬롯을 만들고(그러면 프로덕션 슬롯이 됨) 이들 슬롯을 중단 시간 없이 교환할 수 있습니다. 준비된 배포는 다음과 같은 시나리오에서 가장 유용합니다.

-   **배포 전 유효성 검사** - 준비 사이트 슬롯에 콘텐츠 또는 구성을 배포한 후 변경한 경우 이들 변경을 프로덕션으로 교환하기 전에 유효성을 검사할 수 있습니다.

-   **사이트 콘텐츠 작성 및 통합** - 준비 배포 슬롯에 콘텐츠 업데이트를 점진적으로 추가한 후 업데이트가 완료될 때 배포 슬롯을 프로덕션으로 교환할 수 있습니다.

-   **프로덕션 사이트 롤백** - 프로덕션과 교환한 변경 내용이 예상과 다른 경우 원래 콘텐츠를 프로덕션과 바로 교환할 수 있습니다.

Microsoft Azure는 소스 사이트 슬롯의 모든 인스턴스를 프로덕션으로 교환하기 전에 가동 준비하므로 콘텐츠 배포 시 콜드 부팅이 발생하지 않습니다. 트래픽 리디렉션은 중단 없이 원활하게 수행되며 교환 작업으로 인해 삭제되는 요청은 없습니다. 현재, 표준 웹 사이트별로 오직 하나의 배포 슬롯과 기본 프로덕션 슬롯만 지원됩니다.

## 웹 사이트에 배포 슬롯 추가 ##

사이트 슬롯을 만들 수 있으려면 사이트가 표준 모드로 실행 중이어야 합니다.

1.  빠른 시작 페이지나 웹 사이트 대시보드 페이지의 간략 상태 섹션에서 **Add a new deployment slot**을 클릭합니다.

    ![새 배포 슬롯 추가](./media/web-sites-staged-publishing/QGAddNewDeploymentSlot.png)

    > [WACOM.NOTE] 웹 사이트가 아직 표준 모드가 아닌 경우 **You must be in the standard mode to enable staged publishing** 메시지가 수신됩니다. 이때 **업그레이드**를 선택할 수 있는 옵션이 제공되고 계속하기 전에 웹 사이트의 **크기 조정** 탭으로 이동합니다.

2.  **Add New Deployment Slot** 대화 상자가 나타납니다.

    ![Add New Deployment Slot 대화 상자](./media/web-sites-staged-publishing/AddNewDeploymentSlotDialog.png)

    배포 슬롯의 이름을 지정합니다. 이름은 영숫자로 60자를 초과해서는 안 됩니다. 특수 문자나 공백은 허용되지 않습니다.

3.  웹 사이트 목록에서 배포 슬롯에 표시할 웹 사이트 이름의 왼쪽에 있는 표시를 확장합니다. 지정한 배포 슬롯 이름이 프로덕션 사이트 이름 다음에 괄호로 묶여 나타납니다.

    ![배포 슬롯이 있는 사이트 목록](./media/web-sites-staged-publishing/SiteListWithStagedSite.png)

4.  배포 사이트 슬롯의 이름을 선택하면 다른 웹 사이트와 마찬가지로 일련의 탭이 있는 페이지가 열립니다. ***웹-사이트-이름*(*배포-슬롯-이름*)**이 포털 페이지의 맨 위에 나타나서 현재 배포 사이트 슬롯을 보는 중임을 알립니다.

    ![배포 슬롯 제목](./media/web-sites-staged-publishing/StagingTitle.png)

이제 배포 사이트 슬롯의 콘텐츠와 구성을 업데이트할 수 있습니다. 게시 프로필을 사용하거나 콘텐츠 업데이트를 위해 배포 사이트 슬롯에 연결된 배포 자격 증명을 사용합니다.

## 배포 슬롯의 구성 정보 ##
배포 슬롯이 만들어지면 배포 슬롯의 구성은 기본적으로 프로덕션 사이트 슬롯에서 복제됩니다. 모든 사이트 슬롯의 구성은 편집 가능합니다.

**슬롯 교환 시 변경될 구성**:

-   일반 설정
-   연결 문자열
-   처리기 매핑
-   모니터링 및 진단 설정

**슬롯 교환 시 변경되지 않을 구성**:

-   게시 끝점
-   사용자 지정 도메인 이름
-   SSL 인증서 및 바인딩
-   크기 조정 설정

**참고**:

-   배포 슬롯은 표준 모드의 사이트에서만 사용할 수 있습니다.

-   무료, 공유 또는 기본 모드로 사이트를 변경하면 해당 사이트를 더 이상 교환할 수 없습니다.

-   프로덕션으로 교환하려는 배포 슬롯은 프로덕션에 배치되었을 때를 염두에 두고 그와 동일하게 구성해야 합니다.

-   기본적으로 배포 슬롯은 프로덕션 사이트와 동일한 데이터베이스를 가리킵니다. 하지만 배포 슬롯의 데이터베이스 연결 문자열을 변경하여 다른 데이터베이스를 가리키도록 배포 슬롯을 구성할 수 있습니다. 그런 다음 배포 슬롯의 원래 데이터베이스 연결 문자열을 프로덕션으로 교환하기 직전에 복원할 수 있습니다.

## 배포 슬롯 교환 ##

1.  배포 슬롯을 교환하려면 웹 사이트 목록에서 배포 슬롯을 선택하고 명령 모음에서 **교환** 단추를 클릭합니다.

    ![교환 단추](./media/web-sites-staged-publishing/SwapButtonBar.png)

2.  Swap Deployments 대화 상자가 나타납니다. 대화 상자에서 소스가 되어야 할 사이트 슬롯 및 대상이 되어야 할 사이트를 선택할 수 있습니다.

    ![Swap Deployments 대화 상자](./media/web-sites-staged-publishing/SwapDeploymentsDialog.png)

3.  확인 표시를 클릭하여 작업을 완료합니다. 작업을 마치면 사이트 슬롯이 교환됩니다.

## 프로덕션 사이트를 준비 사이트로 롤백 ##
프로덕션으로 교환된 콘텐츠 또는 구성에 대해 오류가 식별되면 간단히 배포 슬롯(이전의 프로덕션 사이트)을 다시 프로덕션으로 교환하면 됩니다. 그런 다음 준비 모드에 있는 동안 새 버전의 사이트를 추가로 더 수정합니다.

> [WACOM.NOTE] 롤백이 성공하면 배포 사이트 슬롯에는 변경되지 않은 이전 프로덕션 사이트의 콘텐츠 및 구성이 포함되어야 합니다.

## 사이트 슬롯 삭제 ##

Azure 웹 사이트 포털 페이지 맨 아래에 있는 명령 모음에서 **삭제**를 클릭합니다. 웹 사이트와 모든 배포 슬롯을 삭제할지, 아니면 배포 슬롯만 삭제할지에 대한 옵션이 제공됩니다.

![사이트 슬롯 삭제](./media/web-sites-staged-publishing/DeleteStagingSiteButton.png)

확인 메시지에 대해 **예**로 답변하면 선택한 옵션에 따라 하나의 슬롯 또는 모든 슬롯이 삭제됩니다.

**참고**:

-   프로덕션이 아닌 사이트 슬롯에 대해 크기 조정을 사용할 수 없습니다. 크기 조정은 프로덕션 사이트 슬롯에 대해서만 사용할 수 있습니다.

-   연결된 리소스 관리는 프로덕션이 아닌 사이트 슬롯에 대해 지원되지 않습니다.

-   원하는 경우 프로덕션 사이트 슬롯에 바로 게시할 수 있습니다.

-   현재, 배포 슬롯(사이트)은 프로덕션 슬롯(사이트)과 동일한 리소스를 공유하고 동일한 VM에서 실행됩니다. 준비 슬롯에 스트레스 테스트를 실행하는 경우 프로덕션 환경에 비교 가능한 스트레스 로드가 발생합니다.

## 사이트 슬롯용 Azure PowerShell cmdlet

Azure PowerShell은 Windows PowerShell을 통해 Azure를 관리하기 위한 cmdlet을 제공하는 모듈로, Azure 웹 사이트 배포 슬롯을 관리하는 기능도 지원합니다.

-   Azure PowerShell을 설치 및 구성하는 방법과 Azure 구독에 Azure PowerShell을 인증하는 방법에 대한 자세한 내용은 [Azure PowerShell 설치 및 구성 방법](http://www.windowsazure.com/ko-kr/documentation/articles/install-configure-powershell)(영문)을 참조하십시오.

-   PowerShell에서 Azure 웹 사이트에 사용할 수 있는 cmdlet을 나열하려면 `help AzureWebsite`를 호출합니다.

* * * * *

### Get-AzureWebsite

다음 예에서와 같이 **Get-AzureWebsite** cmdlet은 현재 구독의 Azure 웹 사이트 관련 정보를 제공합니다.

`Get-AzureWebsite siteslotstest`

* * * * *

### New-AzureWebsite

**New-AzureWebsite** cmdlet을 사용하여 사이트 및 슬롯 모두의 이름을 지정하면 표준 모드에서 웹 사이트의 사이트 슬롯을 만들 수 있습니다. 다음 예에서와 같이 배포 슬롯을 만드는 사이트와 동일한 영역을 나타낼 수도 있습니다.

`New-AzureWebsite siteslotstest –Slot staging –Location “West US”`

* * * * *

### Publish-AzureWebsiteProject

다음 예에서와 같이 콘텐츠 배포에 **Publish-AzureWebsiteProject** cmdlet을 사용할 수 있습니다.

`Publish-AzureWebsiteProject -Name siteslotstest -Slot staging -Package [path].zip`

* * * * *

### Show-AzureWebsite

콘텐츠 및 구성 업데이트를 새 슬롯에 적용한 후 다음 예에서와 같이 **Show-AzureWebsite** cmdlet을 사용하여 슬롯으로 이동한 후 업데이트의 유효성을 검사할 수 있습니다.

`Show-AzureWebsite -Name siteslotstest -Slot staging`

* * * * *

### Switch-AzureWebsiteSlot

다음 예에서와 같이 **Switch-AzureWebsiteSlot** cmdlet은 교환 작업을 수행하여 업데이트된 배포 슬롯을 프로덕션 사이트로 만들 수 있습니다. 이때 프로덕션 사이트에는 중단 시간이나 콜드 부팅이 발생하지 않습니다.

`Switch-AzureWebsiteSlot -Name siteslotstest`

* * * * *

### Remove-AzureWebsite

배포 슬롯이 더 이상 필요하지 않은 경우 다음 예에서와 같이 **Remove-AzureWebsite** cmdlet을 사용하여 삭제할 수 있습니다.

`Remove-AzureWebsite -Name siteslotstest -Slot staging`

* * * * *

## 사이트 슬롯용 Azure 플랫폼 간 명령줄 인터페이스(xplat-cli) 명령

Azure 플랫폼 간 명령줄 인터페이스(xplat-cli)는 Azure 플랫폼 작업을 위한 플랫폼 간 오픈 소스 명령 집합을 제공하며, Azure 웹 사이트의 배포 슬롯을 관리하는 기능을 지원합니다.

-   xplat-cli 설치 및 구성 지침과 xplat-cli를 Azure 구독에 연결하는 방법에 대한 자세한 내용은 [Azure 플랫폼 간 명령줄 인터페이스 설치 및 구성](http://www.windowsazure.com/ko-kr/documentation/articles/xplat-cli)(영문)을 참조하십시오.

-   xplat-cli에서 Azure 웹 사이트에 사용할 수 있는 명령을 나열하려면 `azure site -h`를 호출합니다.

* * * * *

## azure site list
현재 구독의 Azure 웹 사이트 관련 정보를 확인하려면 다음 예에서와 같이 **azure site list**를 호출합니다.

`azure site list siteslotstest`

* * * * *

## azure site create
표준 모드에서 웹 사이트의 사이트 슬롯을 만들려면 다음 예에서와 같이 **azure site create**를 호출하고 기존 사이트의 이름과 만들 슬롯의 이름을 지정합니다.

`azure site create siteslotstest --slot staging`

새 슬롯의 소스 제어를 사용하도록 설정하려면 다음 예에서와 같이 **--git** 옵션을 사용합니다.

`azure site create –-git siteslotstest --slot staging`

* * * * *

## azure site swap
업데이트된 배포 슬롯을 프로덕션 사이트로 만들려면 다음 예에서와 같이 **azure site swap** 명령을 사용하여 교환 작업을 수행합니다. 이때 프로덕션 사이트에는 중단 시간이나 콜드 부팅이 발생하지 않습니다.

`azure site swap siteslotstest`

* * * * *

## azure site delete
더 이상 필요하지 않은 배포 슬롯을 삭제하려면 다음 예에서와 같이 **azure site delete** 명령을 사용합니다.

`azure site delete siteslotstest --slot staging`

* * * * *
