<properties 
	pageTitle="Azure 포털을 사용하여 Azure 리소스 관리 | Microsoft Azure" 
	description="여러 리소스를 포함된 리소스에 대한 수명 주기 경계 역할을 하는 논리 그룹으로 그룹화합니다." 
	services="azure-resource-manager,azure-portal" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="azure-resource-manager" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/02/2015" 
	ms.author="tomfitz"/>


# Azure 포털을 사용하여 Azure 리소스 관리

## 소개

일반적으로 Microsoft Azure에서 리소스(예: 데이터베이스 서버, 데이터베이스, 웹 사이트)를 관리하려면 한 번에 한 리소스에 대해 작업을 수행해야 했습니다. 여러 리소스로 구성된 복잡한 응용 프로그램이 있을 경우 응용 프로그램 인프라의 변경 내용을 수동으로 조정해야 했습니다. Azure 포털에서는 Azure 리소스 관리자를 활용하여 리소스 그룹을 만들어 응용 프로그램의 모든 리소스를 함께 배포 및 관리할 수 있습니다.

일반적으로 리소스 그룹은 특정 응용 프로그램과 관련된 리소스를 포함합니다. 예를 들어 그룹은 공용 웹 사이트를 호스트하는 웹 응용 프로그램, 사이트에서 사용되는 관계형 데이터를 저장하는 SQL 데이터베이스, 비관계형 자산을 저장하는 저장소 계정을 포함할 수 있습니다. 리소스 그룹의 모든 리소스는 동일한 수명 주기를 공유해야 합니다. 리소스 관리자에 대한 자세한 내용은 [리소스 관리자 개요](../resource-group-overview.md)를 참조하세요.

이 항목에서는 Azure 포털 내에서 리소스 그룹을 사용하는 방법에 대한 간단한 개요를 제공합니다. 현재 일부 서비스에서만 포털이나 리소스 관리자를 지원합니다. 이러한 서비스의 경우 [클래식 포털](https://manage.windowsazure.com)을 사용해야 합니다. 각 서비스의 상태는 [서비스, 지역 및 API 버전에 대한 리소스 관리자 지원](resource-manager-supported-services.md)을 참조하세요.

## 리소스 그룹 및 블레이드 만들기

빈 리소스 그룹을 만들어야 하는 경우 **새로 만들기**, **관리** 및 **리소스 그룹**을 선택합니다.

![빈 소스 그룹 만들기](./media/resource-group-portal/create-empty-group.png)

이름과 위치를 지정하고 필요한 경우 구독을 선택합니다.

![그룹 값 설정](./media/resource-group-portal/set-group-properties.png)

그러나 명시적으로 빈 리소스 그룹을 만들 필요가 없습니다. 새 리소스 그룹을 만들 때 새 리소스 그룹을 만들거나 기존 리소스 그룹을 사용하도록 선택할 수 있습니다. 다음 이미지는 기존 리소스 그룹을 선택하거나 새로 만드는 옵션으로 새 웹 응용 프로그램을 만드는 방법을 보여 줍니다.

![리소스 그룹 만들기](./media/resource-group-portal/select-existing-group.png)

## 리소스 그룹 찾아보기

**모두 찾아보기** 및 **리소스 그룹**을 클릭하여 모든 리소스 그룹을 찾아볼 수 있습니다.

![리소스 그룹 찾아보기](./media/resource-group-portal/browse-groups.png)

특정 리소스 그룹을 선택하면 그룹의 모든 리소스 목록을 포함하여 해당 리소스 그룹에 대한 정보를 제공하는 리소스 그룹 블레이드가 표시됩니다.

![리소스 그룹 요약](./media/resource-group-portal/group-summary.png)

리소스 그룹 블레이드에서 리소스 그룹의 모든 리소스에 대한 청구 및 모니터링 정보를 통합적으로 볼 수도 있습니다.

![모니터링 및 대금 청구](./media/resource-group-portal/monitoring-billing.png)

## 인터페이스 사용자 지정

리소스 그룹 요약에 대한 빠른 액세스의 경우 시작 보드에 블레이드를 고정할 수 있습니다.

![리소스 그룹 고정](./media/resource-group-portal/pin-group.png)

또는 구역 위에 줄임표(...)를 선택하여 시작 보드에 블레이드의 한 섹션을 고정할 수 있습니다. 블레이드의 섹션 크기를 사용자 지정하거나 완전히 제거할 수도 있습니다. 다음 이미지는 고정, 사용자 지정 또는 이벤트 섹션을 제거하는 방법을 보여 줍니다.

![선택 고정](./media/resource-group-portal/pin-section.png)

시작 보드에 이벤트 섹션을 고정한 후 시작 보드에 이벤트 요약이 표시됩니다.

![이벤트 시작 보드](./media/resource-group-portal/events-startboard.png)

그리고 즉시 선택하면 이벤트에 대한 세부 정보로 이동합니다.

## 과거의 배포 보기

리소스 그룹 블레이드 내에서 이 리소스 그룹에 대한 마지막 배포의 날짜 및 상태를 볼 수 있습니다. 링크를 선택하면 그룹에 대한 배포의 기록이 표시됩니다.

![마지막 배포](./media/resource-group-portal/last-deployment.png)

기록에서 모든 배포를 선택하면 해당 배포에 대한 세부 정보를 보여 줍니다.

![배포 요약](./media/resource-group-portal/deployment-summary.png)

배포하는 동안 실행된 개별 작업을 볼 수 있습니다. 다음 이미지는 하나의 성공한 작업 및 실패한 작업을 보여 줍니다.

![작업 세부 정보](./media/resource-group-portal/operation-details.png)

작업 중 하나를 선택하면 작업에 대한 자세한 정보를 보여 줍니다. 아래와 같이 작업이 실패한 경우 특히 유용할 수 있습니다. 배포에 실패한 이유를 해결할 수 있도록 합니다. 다음 이미지에서는 이름이 고유하지 않아서 웹 사이트가 배포되지 않은 것을 확인할 수 있습니다.

![작업 메시지](./media/resource-group-portal/operation-message.png)

## 감사 로그 보기

감사 로그는 배포 작업 뿐만 아니라 구독의 리소스에서 수행된 모든 관리 작업을 포함합니다. 예를 들어 조직에서 누군가 응용 프로그램을 중지하는 경우 감사 로그에서 볼 수 있습니다. 감사 로그를 보려면 **모두 찾아보기** 및 **감사 로그**를 선택합니다.

![감사 로그 찾아보기](./media/resource-group-portal/browse-audit-logs.png)

작업 섹션에서 구독에서 수행된 개별 작업을 볼 수 있습니다.

![감사 로그 보기](./media/resource-group-portal/view-audit-log.png)

작업 중 하나를 선택하여 작업을 실행한 사용자를 포함하여 더 많은 정보를 볼 수 있습니다.

**필터**를 선택하여 감사 로그에 표시된 항목을 필터링할 수 있습니다.

![로그 필터](./media/resource-group-portal/filter-logs.png)

특정 호출자 또는 작업의 수준에 의해 시작되는 지정된 시간 범위 내에서 리소스 그룹 또는 리소스에 속하는 것과 같은 확인할 작업의 유형을 선택할 수 있습니다.

![필터 옵션](./media/resource-group-portal/filter-options.png)

## 리소스 그룹에 리소스 추가

리소스 그룹 블레이드에서 **추가** 명령을 사용하여 리소스 그룹에 리소스를 추가할 수 있습니다.

![리소스 추가](./media/resource-group-portal/add-resource.png)

사용 가능한 목록에서 원하는 리소스를 선택할 수 있습니다.

## 리소스 그룹 삭제

리소스 그룹을 사용하면 포함된 모든 리소스의 수명 주기를 관리할 수 있으므로 리소스 그룹을 삭제하면 그룹 내에 포함된 모든 리소스가 삭제됩니다. 리소스 그룹 내부의 개별 리소스를 삭제할 수도 있습니다. 연결된 다른 리소스가 있을 수 있으므로 리소스 그룹을 삭제할 때는 주의해야 합니다. 리소스 그룹을 삭제할 때 연결된 리소스를 리소스 맵에서 보고 의도하지 않은 결과가 발생하지 않도록 필요한 조치를 취할 수 있습니다. 연결된 리소스는 삭제될 수는 없지만 예상대로 작동하지 않을 수 있습니다.

![그룹 삭제](./media/resource-group-portal/delete-group.png)

## 리소스 태그 지정

리소스 그룹 및 리소스에 태그를 적용하여 논리적으로 자산을 구성할 수 있습니다. 포털을 통한 태그 사용에 대한 자세한 내용은 [태그를 사용하여 Azure 리소스 구성](../resource-group-using-tags.md)을 참조하세요.

## 사용자 지정 템플릿 배포

배포를 실행하려고 하지만 Marketplace에서 템플릿 중 하나를 사용하지 않으려는 경우 솔루션에 대한 인프라를 정의하는 사용자 지정된 템플릿을 만들 수 있습니다. 템플릿에 대한 자세한 내용은 [Azure 리소스 관리자 템플릿 작성](../resource-group-authoring-templates.md)을 참조하세요.

포털을 통해 사용자 지정된 템플릿을 배포하려면 **새로 만들기**, **마켓플레이스** 및 **모두**를 선택합니다.

![템플릿 배포 찾기](./media/resource-group-portal/launch-template.png)

**템플릿 배포**를 검색하고 반환된 목록에서 선택합니다.

![템플릿 배포 찾기](./media/resource-group-portal/search-template.png)

템플릿 배포를 시작한 후 사용자 지정 템플릿을 만들고 배포에 대한 값을 설정할 수 있습니다.

![템플릿 만들기](./media/resource-group-portal/show-custom-template.png)

## 다음 단계
시작하기

- 리소스 관리자의 개념에 대한 소개는 [Azure 리소스 관리자 개요](../resource-group-overview.md)를 참조하세요.  
- 리소스 배포 시 Azure PowerShell 사용에 대한 소개는 [Azure 리소스 관리자에서 Azure PowerShell 사용](../powershell-azure-resource-manager.md)을 참조하세요.
- 리소스 배포 시 Azure CLI 사용에 대한 소개는 [Azure 리소스 관리에서 Mac, Linux 및 Windows용 Azure CLI 사용](../xplat-cli-azure-resource-manager.md)을 참조하세요. 
  


 

<!---HONumber=AcomDC_1203_2015-->