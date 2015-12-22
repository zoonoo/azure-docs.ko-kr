<properties
	pageTitle="Microsoft Azure 포털 개요"
	description="Microsoft Azure 포털을 사용하는 방법에 대해 알아봅니다."
	services=""
	documentationCenter=""
	authors="davidwrede"
	manager="dwrede"
	editor="jimbe"/>

<tags
	ms.service="na"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na" 
	ms.topic="hero-article"
	ms.date="12/16/2015"
	ms.author="dwrede"/>

# Microsoft Azure 포털 개요

Microsoft Azure 포털은 Azure 리소스를 프로비전 및 관리할 수 있는 중앙 위치입니다. 이 자습서에서는 포털에 대해 알아보고 다음 주요 기능 중 일부의 사용 방법을 보여 줍니다. - Microsoft 및 기타 공급업체에서 제공하는 수천 개의 항목을 찾아보고 구매 및/또는 프로비전할 수 있는 **포괄적인 마켓플레이스** - 관심 있는 리소스를 쉽게 찾아서 여러 가지 관리 작업을 수행할 수 있는 **확장 가능한 통합 찾아보기 환경** - 설정, 작업, 청구 정보, 상태 모니터링, 사용 데이터 등을 일관된 방법으로 노출하여 Azure의 광범위한 서비스를 관리할 수 있는 **일관성 있는 관리 페이지**(또는 블레이드) - 로그인할 때마다 보려는 정보를 표시하는 사용자 지정 시작 화면을 만들 수 있는 **개인 환경**. 또한 타일이 포함된 관리 블레이드를 사용자 지정할 수 있습니다.

 ![Azure 포털 UI 개요][UIOrientation]

## 시작하기 전에

이 자습서를 진행하려면 유효한 Azure 구독이 필요합니다. 없는 경우 지금 [무료 평가판에 등록](http://azure.microsoft.com/pricing/free-trial/)하세요. 구독한 후에는 [https://portal.azure.com]에서 포털에 액세스할 수 있습니다.

## 리소스를 만드는 방법

Azure에는 수천 개의 항목을 한 곳에서 만들 수 있는 마켓플레이스가 있습니다. 새 Windows Server 2012 VM을 만들려는 경우를 가정해 보겠습니다. +새로 만들기 허브는 마켓플레이스의 큐레이트된 주요 범주 집합에 대한 진입점입니다. 각 범주에는 모든 범주 및 검색을 표시하는 전체 마켓플레이스의 링크와 함께 소규모의 주요 항목 집합이 있습니다. 새 Windows Server 2012 VM을 만들려면 다음 작업을 수행합니다.

1.	Windows Server 2012는 주요 항목이므로 계산 범주에서 선택할 수 있습니다.  
2.	양식에서 몇 가지 기본 입력 항목을 작성합니다.
3.	'만들기'를 클릭하면 VM이 즉시 프로비전되기 시작합니다.

리소스가 만들어지면 알림 허브에서 알림이 제공되며 관리 블레이드(나중에 언제든지 리소스를 찾아볼 수 있음)가 열립니다.

![포털 범주][PortalCategories]


## 리소스를 찾는 방법

자주 액세스하는 리소스는 항상 시작 보드에 고정할 수 있지만 자주 액세스하지 않는 항목은 검색해야 할 수 있습니다. 아래에 표시된 찾아보기 허브를 통해 모든 리소스를 확인할 수 있습니다. 구독별로 필터링하고, 열을 선택/크기 조정하고, 개별 항목을 클릭하여 관리 블레이드로 이동할 수 있습니다.

![찾아보기 허브][BrowseHub]

## 리소스에 대한 액세스 권한을 관리 및 위임하는 방법

이 블레이드에서는 원격 데스크톱을 사용하여 가상 컴퓨터에 연결하고, 핵심 성과 지표를 모니터링하고, RBAC(역할 기반 액세스)를 사용하여 이 VM에 대한 액세스를 제어하고, VM을 구성하고, 기타 중요한 관리 작업을 수행할 수 있습니다. 역할 기반의 액세스 위임은 규모별로 관리하는 데 중요합니다. 자세히 알아보려면 [여기](role-based-access-control-configure.md)를 클릭하세요. 리소스에 대한 액세스 권한을 위임하려면 다음 작업을 수행합니다.

1.	리소스를 찾습니다.
2.	필수 패키지 섹션에서 ‘모든 설정’을 클릭합니다.
3.	설정 목록에서 '사용자'를 클릭합니다.
4.	명령 모음에서 ‘추가’를 클릭합니다.
5.	사용자 및 역할을 선택합니다.

![리소스 관리][ManageResource]

## 리소스 블레이드를 사용자 지정하는 방법

Azure에서는 리소스의 블레이드를 미리 구성하지만 이러한 블레이드의 타일은 사용자가 제어합니다. 사용자 지정 모드로 이동하여 타일을 손쉽게 추가, 제거, 크기 조정 또는 다시 정렬할 수 있습니다. 블레이드를 사용자 지정하려면 다음 작업을 수행합니다.

1.	리소스를 찾습니다.
2.	사용자 지정할 블레이드의 맨 위에 있는 '...'를 클릭합니다.
3.	‘파트 추가’를 클릭합니다.
4.	파트를 끌어서 놓기 시작합니다.  

![블레이드 사용자 지정][CustomizeBlades]

## 도움말을 보는 방법

문제가 발생한 경우 걱정하지 않아도 됩니다. 포털에는 올바른 지침을 제공할 수 있는 도움말 및 지원 페이지가 있습니다. [지원 계획](http://azure.microsoft.com/support/plans/)에 따라 포털에서 직접 지원 티켓을 만들 수도 있습니다. 지원 티켓을 만든 후에는 포털 내에서 티켓의 수명 주기를 관리할 수 있습니다. 도움말 및 지원 페이지를 보려면 찾아보기 -> 도움말 + 지원으로 이동합니다.

![도움말 및 지원][HelpSupport]

## 요약

이 자습서에서 배운 내용을 검토해 보겠습니다. - 등록하고, 구독을 얻고, 포털을 찾아보는 방법을 살펴보았습니다. - 포털 UI를 중심으로 리소스를 만들고 찾는 방법을 살펴보았습니다. - 리소스를 만들고 찾는 방법을 살펴보았습니다. - 구조 또는 관리 블레이드와 다양한 유형의 리소스를 일관적으로 관리할 수 있는 방법을 살펴보았습니다. - 가장 관심 있는 정보를 가져오도록 포털을 사용자 지정하는 방법을 살펴보았습니다. - RBAC(역할 기반 액세스)를 사용하여 리소스에 대한 액세스를 제어하는 방법을 살펴보았습니다. - 도움말 및 지원 페이지를 보는 방법을 살펴보았습니다.

Microsoft Azure 포털은 클라우드에서 응용 프로그램을 빌드 및 관리하는 작업을 크게 간소화합니다. Microsoft는 지속적으로 [사용자 의견을 듣고](http://feedback.azure.com/forums/223579-azure-preview-portal) 개선하므로 항상 [관리 블로그](http://azure.microsoft.com/blog/topics/management/)에서 최신 정보를 확인하시기 바랍니다. 또한 [ScottGu의 블로그](http://weblogs.asp.net/scottgu)는 모든 Azure 업데이트를 확인하는 데 유용합니다.

[UIOrientation]: ./media/azure-portal-how-to-use/azure_portal_1.png
[PortalCategories]: ./media/azure-portal-how-to-use/azure_portal_2.png
[BrowseHub]: ./media/azure-portal-how-to-use/azure_portal_3.png
[ManageResource]: ./media/azure-portal-how-to-use/azure_portal_4.png
[CustomizeBlades]: ./media/azure-portal-how-to-use/azure_portal_5.png
[HelpSupport]: ./media/azure-portal-how-to-use/azure_portal_6.png

<!---HONumber=AcomDC_1217_2015-->