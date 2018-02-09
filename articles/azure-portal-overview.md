---
title: "Azure Portal 개요 | Microsoft Docs"
description: "Microsoft Azure 포털을 사용하는 방법에 대해 알아봅니다."
services: 
documentationcenter: 
author: davidwrede
manager: erikre
editor: jimbe
ms.assetid: 53cb9df1-c96a-4f4e-b022-18336cd3d697
ms.service: azure-portal
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 12/16/2015
ms.author: dwrede
ms.openlocfilehash: 45decfdec01b7086d1f9d18b31cf01cec1adb34d
ms.sourcegitcommit: e19742f674fcce0fd1b732e70679e444c7dfa729
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/03/2018
---
# <a name="microsoft-azure-portal-overview"></a>Microsoft Azure 포털 개요
Microsoft Azure 포털은 Azure 리소스를 프로비전 및 관리할 수 있는 중앙 위치입니다.  이 자습서는 포털에 익숙해지도록 이러한 주요 기능 중 일부를 사용하는 방법을 보여줍니다.

* **포괄적인 마켓플레이스** 를 사용하여 구입하거나 프로비전할 수 있는 Microsoft 및 다른 공급 업체의 수천 개의 항목을 탐색할 수 있습니다.
* **통합되고 확장 가능한 브라우저 환경** 을 사용하여 관심 있는 리소스를 쉽게 찾고 다양한 관리 작업을 수행할 수 있습니다.
* **일관적인 관리 페이지** (또는 블레이드)를 사용하면 노출 설정, 작업, 대금 청구 정보, 상태 모니터링 및 사용 현황 데이터 등 일관된 방식으로 Azure의 다양한 서비스를 관리할 수 있습니다.
* **개인 환경** 을 사용하면 로그인할 때마다 확인하려는 정보를 표시하는 사용자 지정된 시작 화면을 만들 수 있습니다.  또한 타일이 포함된 관리 블레이드를 사용자 지정할 수 있습니다.
  
  ![Azure Portal UI 개요][UIOrientation]

## <a name="before-you-get-started"></a>시작하기 전에
이 자습서를 진행하려면 유효한 Azure 구독이 필요합니다.  없는 경우 지금 [무료 평가판에 등록](https://azure.microsoft.com/pricing/free-trial/) 하세요.  구독한 후에는 <https://portal.azure.com>에서 포털에 액세스할 수 있습니다.

## <a name="how-to-create-a-resource"></a>리소스를 만드는 방법
Azure에는 수천 개의 항목을 한 곳에서 만들 수 있는 마켓플레이스가 있습니다.  새 Windows Server 2012 VM을 만들려는 경우를 가정해 보겠습니다.  +새로 만들기 허브는 마켓플레이스의 큐레이트된 주요 범주 집합에 대한 진입점입니다.  각 범주에는 모든 범주 및 검색을 표시하는 전체 마켓플레이스의 링크와 함께 소규모의 주요 항목 집합이 있습니다. 새 Windows Server 2012 VM을 만들려면 다음 작업을 수행합니다.  

1. Windows Server 2012는 주요 항목이므로 Compute 범주에서 선택할 수 있습니다.  
2. 양식에서 몇 가지 기본 입력 항목을 작성합니다.
3. '만들기'를 클릭하면 VM이 즉시 프로비전되기 시작합니다.

리소스가 만들어지면 알림 허브에서 알림이 제공되며 관리 블레이드(나중에 언제든지 리소스를 찾아볼 수 있음)가 열립니다.

![포털 범주][PortalCategories]

## <a name="how-to-find-your-resources"></a>리소스를 찾는 방법
자주 액세스하는 리소스는 항상 시작 보드에 고정할 수 있지만 자주 액세스하지 않는 항목은 검색해야 할 수 있습니다.  아래에 표시된 찾아보기 허브를 통해 모든 리소스를 확인할 수 있습니다.  구독별로 필터링하고, 열을 선택/크기 조정하고, 개별 항목을 클릭하여 관리 블레이드로 이동할 수 있습니다.

![찾아보기 허브][BrowseHub]

## <a name="how-to-manage-and-delegate-access-to-a-resource"></a>리소스에 대한 액세스 권한을 관리 및 위임하는 방법
이 블레이드에서는 원격 데스크톱을 사용하여 가상 컴퓨터에 연결하고, 핵심 성과 지표를 모니터링하고, RBAC(역할 기반 액세스)를 사용하여 이 VM에 대한 액세스를 제어하고, VM을 구성하고, 기타 중요한 관리 작업을 수행할 수 있습니다.  역할 기반의 액세스 위임은 규모별로 관리하는 데 중요합니다.  자세히 알아보려면 [여기](active-directory/role-based-access-control-configure.md)를 클릭하세요. 리소스에 대한 액세스 권한을 위임하려면 다음 작업을 수행합니다.

1. 리소스를 찾습니다.
2. 필수 패키지 섹션에서 ‘모든 설정’을 클릭합니다.
3. 설정 목록에서 '사용자'를 클릭합니다.
4. 명령 모음에서 ‘추가’를 클릭합니다.
5. 사용자 및 역할을 선택합니다.

![리소스 관리][ManageResource]

## <a name="how-to-get-help"></a>도움말을 보는 방법
문제가 발생한 경우 걱정하지 않아도 됩니다.  포털에는 올바른 지침을 제공할 수 있는 도움말 및 지원 페이지가 있습니다.  [지원 계획](https://azure.microsoft.com/support/plans/)에 따라 포털에서 직접 지원 티켓을 만들 수도 있습니다.  지원 티켓을 만든 후에는 포털 내에서 티켓의 수명 주기를 관리할 수 있습니다. 도움말 및 지원 페이지를 보려면 찾아보기 -> 도움말 + 지원으로 이동합니다.  

![도움말 및 지원][HelpSupport]

## <a name="summary"></a>요약
이 자습서에서 배운 내용을 검토하겠습니다.

* 등록하고 구독을 가져오며 포털로 이동하는 방법을 알아보았습니다.
* 포털 UI를 중심으로 리소스를 만들고 찾아보는 방법을 알아보았습니다.
* 리소스를 만들고 찾아보는 방법을 알아보았습니다.
* 구조 또는 관리 블레이드 및 여러 유형의 리소스를 지속적으로 관리하는 방법에 대해 알아보았습니다.
* 포털을 사용자 지정하여 가장 관심이 있는 정보를 가져오는 방법을 알아보았습니다.
* RBAC(역할 기반 액세스)를 사용하여 리소스에 대한 액세스를 제어하는 방법을 알아보았습니다.
* 도움말 및 지원을 얻는 방법을 알아보았습니다.

Microsoft Azure 포털은 클라우드에서 응용 프로그램을 빌드 및 관리하는 작업을 크게 간소화합니다.  Microsoft는 지속적으로 [사용자 의견을 듣고](https://feedback.azure.com/forums/223579-azure-preview-portal/) 개선하므로 항상 [관리 블로그](https://azure.microsoft.com/blog/topics/management/)에서 최신 정보를 확인하시기 바랍니다.  [ScottGu의 블로그](http://weblogs.asp.net/scottgu) 는 모든 Azure 업데이트를 확인하는 데 유용합니다.

[UIOrientation]: ./media/azure-portal-how-to-use/azure_portal_1.png
[PortalCategories]: ./media/azure-portal-how-to-use/azure_portal_2.png
[BrowseHub]: ./media/azure-portal-how-to-use/azure_portal_3.png
[ManageResource]: ./media/azure-portal-how-to-use/azure_portal_4.png
[CustomizeBlades]: ./media/azure-portal-how-to-use/azure_portal_5.png
[HelpSupport]: ./media/azure-portal-how-to-use/azure_portal_6.png
