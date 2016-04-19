<properties 
	pageTitle="Azure 포털을 사용하여 Azure 리소스 관리 | Microsoft Azure" 
	description="Azure 포털 및 Azure Resource Manager를 사용하여 리소스를 배포 및 관리합니다. 리소스에 태그를 지정하고 감사 로그를 보는 방법을 보여 줍니다." 
	services="azure-resource-manager,azure-portal" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="timlt" 
	editor="tysonn"/>

<tags 
	ms.service="azure-resource-manager" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="get-started-article" 
	ms.date="04/08/2016" 
	ms.author="tomfitz"/>


# Azure 포털을 사용하여 Azure 리소스 배포 및 관리

## 소개

이 토픽에서는 Azure 리소스를 배포 및 관리하는 데 [Azure Resource Manager](../resource-group-overview.md)와 함께 [Azure 포털](https://portal.azure.com)을 사용하는 방법을 보여 줍니다.

현재 일부 서비스에서만 포털이나 리소스 관리자를 지원합니다. 이러한 서비스의 경우 [클래식 포털](https://manage.windowsazure.com)을 사용해야 합니다. 각 서비스의 상태는 [Azure 포털 가용성 차트](https://azure.microsoft.com/features/azure-portal/availability/)를 참조하세요.

Azure PowerShell 및 Azure CLI를 통해 리소스를 관리할 수도 있습니다. 이러한 인터페이스를 사용하는 방법에 대한 자세한 내용은 [Azure Resource Manager로 Azure PowerShell 사용](../powershell-azure-resource-manager.md) 및 [Azure Resource Manager에서 Mac, Linux 및 Windows용 Azure CLI 사용](../xplat-cli-azure-resource-manager.md)을 참조하세요. Visual Studio를 통해 솔루션을 배포하는 방법에 대한 자세한 내용은 [Visual Studio를 통해 Azure 리소스 그룹 만들기 및 배포](../vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)를 참조하세요.

## 리소스 그룹 만들기

빈 리소스 그룹을 만들려면 **새로 만들기**, **관리** 및 **리소스 그룹**을 선택합니다.

![빈 소스 그룹 만들기](./media/resource-group-portal/create-empty-group.png)

이름과 위치를 지정하고 필요한 경우 구독을 선택합니다.

![그룹 값 설정](./media/resource-group-portal/set-group-properties.png)

## 리소스 배포

리소스 그룹을 만든 후에는 리소스 그룹에 리소스를 배포할 수 있습니다. 배포를 시작하려면 **새로 만들기**와 배포할 리소스 유형을 선택하기만 하면 됩니다.

![리소스 배포](./media/resource-group-portal/deploy-resource.png)

배포하려는 리소스 유형이 표시되지 않으면 마켓플레이스를 검색할 수 있습니다.

![마켓플레이스 검색](./media/resource-group-portal/search-resource.png)

선택한 리소스 유형에 따라 배포 전 설정할 관련 속성 모음이 있습니다. 리소스 유형에 따라 달라지므로 해당 옵션은 여기에 표시되지 않습니다. 모든 유형에 대해 대상 리소스 그룹을 선택해야 합니다. 다음 이미지는 새 웹 앱을 만들고 이를 방금 만든 리소스 그룹에 배포하는 방법을 보여 줍니다.

![리소스 그룹 만들기](./media/resource-group-portal/select-existing-group.png)

또는 리소스를 배포할 때 새 리소스 그룹을 만들도록 결정할 수 있습니다. 구독에서 기존 리소스 그룹 중 하나를 선택하지 않고 **새로 만들기**를 선택하고 리소스 그룹에 이름을 지정합니다.

![새 리소스 그룹 만들기](./media/resource-group-portal/select-new-group.png)

배포가 시작됩니다. 몇 분 정도 걸릴 수 있습니다. 배포가 완료되면 알림이 표시됩니다.

![알림 보기](./media/resource-group-portal/view-notification.png)

리소스를 배포한 후에는 그룹에 더 많은 리소스를 추가할 것인지 결정할 수 있습니다. 리소스 그룹 블레이드에서 **추가** 명령을 사용하여 리소스 그룹에 리소스를 추가할 수 있습니다.

![리소스 추가](./media/resource-group-portal/add-resource.png)

## 템플릿 내보내기

리소스 그룹을 설정한 후에는 리소스 그룹에 대한 Resource Manager 템플릿을 살펴볼 수 있습니다. 템플릿을 내보내면 다음과 같은 두 가지 이점이 있습니다.

1. 모든 인프라가 템플릿에 정의되어 있기 때문에 향후 솔루션 배포를 간단하게 자동화할 수 있습니다.

2. 솔루션을 나타내는 JSON(JavaScript Object Notation)을 살펴보면서 템플릿 구문에 익숙해질 수 있습니다.

포털을 통해 리소스 그룹의 현재 상태를 나타내는 템플릿을 생성하거나 특정 배포에 사용된 템플릿을 검색할 수 있습니다. 두 옵션은 이 토픽에 나와 있습니다.

리소스 그룹을 변경했고 현재 상태의 JSON 표현을 검색해야 하는 경우에는 리소스 그룹에 대한 템플릿을 내보내는 것이 좋습니다. 그러나 생성된 템플릿에는 최소한의 매개 변수만 포함되고 변수는 포함되지 않습니다. 템플릿의 값은 대부분 하드 코드됩니다. 생성된 템플릿을 배포하기 전에, 다양한 환경에 맞게 배포를 사용자 지정할 수 있도록 더 많은 값을 매개 변수로 변환할 수 있습니다.

리소스를 배포하는 데 사용된 실제 템플릿을 살펴보아야 하는 경우에는 특정 배포에 대한 템플릿을 내보내는 것이 좋습니다. 이 템플릿에는 원래 배포에 대해 정의된 모든 매개 변수와 변수가 포함됩니다. 그러나 조직 내 다른 사람이 템플릿에 정의된 범위를 넘어서 리소스 그룹을 변경할 경우 이 템플릿은 리소스 그룹의 현재 상태를 나타내지 않습니다.

> [AZURE.NOTE] 템플릿 내보내기 기능은 미리 보기 버전이며, 템플릿 내보내기를 지원하지 않는 리소스 유형도 있습니다. 템플릿 내보내기를 시도할 때 일부 리소스를 내보내지 못했다는 오류가 표시될 수 있습니다. 필요한 경우 템플릿을 다운로드한 후 템플릿에서 이러한 리소스를 수동으로 정의할 수 있습니다.

### 리소스 그룹에 대한 템플릿 내보내기

리소스 그룹 블레이드에서 리소스 그룹의 현재 상태를 나타내는 템플릿을 내보낼 수 있습니다.

리소스 그룹에 대한 템플릿을 보려면 **템플릿 내보내기**를 선택합니다.

![리소스 그룹 내보내기](./media/resource-group-portal/export-resource-group.png)

Resource Manager가 다음 4개 파일을 자동으로 생성합니다.

1. 솔루션의 인프라를 정의하는 템플릿

2. 배포하는 동안 값을 전달하는 데 사용할 수 있는 매개 변수 파일

3. 실행하여 템플릿을 배포할 수 있는 Azure PowerShell 스크립트 파일

4. 실행하여 템플릿을 배포할 수 있는 Azure CLI 스크립트 파일

먼저, 현재 리소스 그룹을 나타내는 템플릿을 살펴봅니다.

![템플릿 표시](./media/resource-group-portal/show-rg-template.png)

**리소스** 섹션 내에 배포할 리소스에 대한 정의가 표시됩니다.

매개 변수 파일에서 배포하는 동안 전달할 매개 변수 값을 저장할 수 있습니다.

![매개 변수 표시](./media/resource-group-portal/show-parameters.png)

Azure PowerShell을 통해 템플릿을 배포하기 위한 스크립트 파일이 있습니다.

![Azure PowerShell 표시](./media/resource-group-portal/show-powershell.png)

그리고 Azure CLI를 통해 템플릿을 배포하기 위한 스크립트 파일이 있습니다.

![Azure CLI 표시](./media/resource-group-portal/show-cli.png)

포털에서는 이 템플릿을 작업하기 위한 세 가지 옵션을 제공합니다. 지금 바로 템플릿을 다시 배포하려면 **배포**를 선택합니다. 모든 파일을 로컬로 다운로드하려면 **다운로드**를 선택합니다. 나중에 포털을 통해 사용할 수 있도록 Azure 계정에 파일을 저장하려면 **템플릿 저장**을 선택합니다.

### 배포에서 템플릿 다운로드

리소스 그룹 블레이드 내에서 이 리소스 그룹에 대한 마지막 배포의 날짜 및 상태를 볼 수 있습니다. 링크를 선택하면 그룹에 대한 배포의 기록이 표시됩니다.

![마지막 배포](./media/resource-group-portal/last-deployment.png)

기록에서 모든 배포를 선택하면 해당 배포에 대한 세부 정보를 보여 줍니다. 리소스를 배포할 때마다 Resource Manager는 사용된 템플릿을 보존합니다. **템플릿 보기**를 선택하여 배포에 사용된 실제 템플릿을 검색할 수 있습니다.

![템플릿 내보내기](./media/resource-group-portal/export-template.png)

이 배포에 사용되는 템플릿이 표시됩니다. 템플릿에는 사용자가 정의한 모든 매개 변수 및 변수가 포함되어 있습니다.

![템플릿 표시](./media/resource-group-portal/show-template.png)

앞에서도 언급했지만, 리소스 그룹이 완전하게 표현되지 않을 수도 있습니다. 이 배포 범위를 벗어나는 리소스를 추가 또는 삭제하는 경우 해당 작업이 템플릿에 반영되지 않습니다. 이전 섹션에서 보여드린 것처럼 템플릿, 매개 변수 파일 및 스크립트 파일을 볼 수 있습니다. 또한 이전 섹션에서 보여드린 것처럼 템플릿을 다시 배포하고, 다운로드하고, 저장할 수도 있습니다.

## 리소스 그룹 관리

**리소스 그룹**을 클릭하여 모든 리소스 그룹을 찾아볼 수 있습니다.

![리소스 그룹 찾아보기](./media/resource-group-portal/browse-groups.png)

특정 리소스 그룹을 선택하면 그룹의 모든 리소스 목록을 포함하여 해당 리소스 그룹에 대한 정보를 제공하는 리소스 그룹 블레이드가 표시됩니다.

![리소스 그룹 요약](./media/resource-group-portal/group-summary.png)

요약 아래 **섹션 추가**를 선택하여 리소스 그룹 블레이드에 그래프 및 테이블을 더 추가할 수 있습니다.

![섹션 추가](./media/resource-group-portal/add-section.png)

블레이드에 포함하려는 정보를 선택하기 위한 타일 갤러리가 나타납니다. 표시되는 타일 유형은 리소스 유형으로 필터링됩니다. 선택하는 리소스가 달라지면 사용할 수 있는 타일이 변경됩니다.

![섹션 추가](./media/resource-group-portal/tile-gallery.png)

필요한 타일을 사용 가능한 공간으로 끕니다.

![타일 끌기](./media/resource-group-portal/drag-tile.png)

포털 맨 위에서 **완료**를 선택하면 새 보기가 블레이드에 포함됩니다.

![타일 표시](./media/resource-group-portal/show-lens.png)

리소스 그룹에 대한 빠른 액세스의 경우 대시보드에 블레이드를 고정할 수 있습니다.

![리소스 그룹 고정](./media/resource-group-portal/pin-group.png)

또는 섹션 위에 줄임표(...)를 선택하여 대시보드에 블레이드의 한 섹션을 고정할 수 있습니다. 블레이드의 섹션 크기를 사용자 지정하거나 완전히 제거할 수도 있습니다. 다음 이미지는 CPU 및 메모리 섹션을 고정, 사용자 지정 또는 제거하는 방법을 보여 줍니다.

![선택 고정](./media/resource-group-portal/pin-cpu-section.png)

대시보드에 섹션을 고정하면 대시보드에 요약이 표시됩니다.

![대시보드 보기](./media/resource-group-portal/view-startboard.png)

그리고 즉시 선택하면 데이터에 대한 세부 정보로 이동합니다.

리소스 그룹을 사용하면 포함된 모든 리소스의 수명 주기를 관리할 수 있으므로 리소스 그룹을 삭제하면 그룹 내에 포함된 모든 리소스가 삭제됩니다. 리소스 그룹 내부의 개별 리소스를 삭제할 수도 있습니다. 삭제하려는 리소스 그룹에 다른 리소스 그룹의 리소스가 연결되었을 수 있으므로 리소스 그룹을 삭제할 때는 주의해야 합니다. 연결된 리소스는 삭제될 수는 없지만 예상대로 작동하지 않을 수 있습니다.

![그룹 삭제](./media/resource-group-portal/delete-group.png)

## 리소스 태그 지정

리소스 그룹 및 리소스에 태그를 적용하여 논리적으로 자산을 구성할 수 있습니다. 포털을 통한 태그 사용에 대한 자세한 내용은 [태그를 사용하여 Azure 리소스 구성](../resource-group-using-tags.md)을 참조하세요.

## 저장된 템플릿 배포

계정에 템플릿을 저장한 경우 나중에 **찾아보기**, **템플릿**을 순서대로 선택하여 저장된 템플릿을 볼 수 있습니다.

![템플릿 찾아보기](./media/resource-group-portal/browse-templates.png)

사용자 고유의 템플릿 컬렉션이 표시됩니다.

![템플릿 컬렉션 표시](./media/resource-group-portal/show-template-collection.png)


## 사용자 지정 템플릿 배포

배포를 실행하려고 하지만 Marketplace에서 템플릿 중 하나를 사용하지 않으려는 경우 솔루션에 대한 인프라를 정의하는 사용자 지정된 템플릿을 만들 수 있습니다. 템플릿에 대한 자세한 내용은 [Azure 리소스 관리자 템플릿 작성](../resource-group-authoring-templates.md)을 참조하세요.

포털을 통해 사용자 지정된 템플릿을 배포하려면 **새로 만들기**를 선택하고 옵션 중에서 선택할 수 있을 때까지 **템플릿 배포**를 검색합니다.

![템플릿 배포 찾기](./media/resource-group-portal/search-template.png)

사용 가능한 리소스에서 **템플릿 배포**를 선택합니다.

![템플릿 배포 선택](./media/resource-group-portal/select-template.png)

템플릿 배포를 시작한 후 사용자 지정 템플릿을 만들고 배포에 대한 값을 설정할 수 있습니다.

![템플릿 만들기](./media/resource-group-portal/show-custom-template.png)

또는 [Azure 빠른 시작 템플릿](https://azure.microsoft.com/documentation/templates/)에서 기존 템플릿을 선택할 수 있습니다. 이러한 템플릿은 커뮤니티에서 제공합니다. 커뮤니티에서는 여러 일반적인 시나리오를 다루며, 여러분이 배포하려는 것과 비슷한 템플릿을 누군가가 이미 추가했을 수도 있습니다. 템플릿을 검색하여 현재 시나리오와 일치하는 항목을 찾을 수 있습니다.

![빠른 시작 템플릿 선택](./media/resource-group-portal/select-quickstart-template.png)

템플릿을 선택하면 템플릿이 편집기에 로드됩니다.

## 구독 및 비용 보기

모든 리소스에 대한 롤업 비용 및 구독에 대한 정보를 볼 수 있습니다. **구독** 및 표시할 구독을 선택합니다. 선택할 구독이 하나만 있을 수 있습니다.

![(구독당)](./media/resource-group-portal/select-subscription.png)

구독 블레이드 내에 진행 속도가 표시됩니다.

![진행 속도](./media/resource-group-portal/burn-rate.png)

그리고 리소스 유형별 비용 분석이 표시됩니다.

![리소스 비용](./media/resource-group-portal/cost-by-resource.png)

## Azure 대시보드에 대한 액세스 제어

포털에서 대부분 타일에 표시되는 정보에 대한 액세스는 Azure [역할 기반 액세스 제어](../active-directory/role-based-access-control-configure.md)로 제어됩니다. 대시보드를 환경에 원활하게 통합하기 위해 게시된 모든 대시보드가 Azure 리소스로 구현됩니다. 액세스 제어 관점에서 대시보드는 가상 컴퓨터 또는 저장소 계정과 차이가 없습니다.

다음은 예제입니다. Azure 구독을 보유하고 구독의 **소유자**, **참여자** 또는 **읽기 권한자** 역할에 할당된 팀의 다양한 멤버가 있다고 가정해 보겠습니다. 소유자 또는 참여자인 사용자는 구독 내에서 대시보드를 나열, 보기, 만들기, 수정 또는 삭제할 수 있습니다. 읽기 권한자인 사용자는 대시보드를 나열 및 볼 수 있지만 수정 또는 삭제할 수 없습니다. 읽기 권한자 액세스를 보유한 사용자는 게시된 대시보드에 대한 로컬 편집을 만들 수 있지만(예: 문제 해결 시) 해당 변경 내용을 서버로 다시 게시하는 옵션은 없습니다. 대시보드의 개인 복사본을 직접 만들 수 있는 옵션을 갖게 됩니다.

대시보드의 개별 타일은 데이터를 보여주는 리소스에 따라 고유한 액세스 제어 요구 사항을 적용합니다. 즉, 개별 타일에 있는 데이터를 보호하면서 보다 광범위하게 공유할 수 있는 대시보드를 디자인할 수 있습니다.

## 다음 단계

- 감사 로그를 보려면 [Resource Manager로 작업 감사](../resource-group-audit.md)를 참조하세요.
- 배포 오류를 해결하려면 [Azure 포털을 사용하여 리소스 그룹 배포 문제 해결](../resource-manager-troubleshoot-deployments-portal.md)을 참조하세요.

<!---HONumber=AcomDC_0413_2016-->