---
title: "Azure 트래픽 관리자를 사용하여 Azure 웹 앱 트래픽 제어"
description: "이 문서에서는 Azure 웹앱과 관련된 Azure Traffic Manager의 요약 정보를 제공합니다."
services: app-service\web
documentationcenter: 
author: cephalin
writer: cephalin
manager: erikre
editor: mollybos
ms.assetid: dabda633-e72f-4dd4-bf1c-6e945da456fd
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/25/2016
ms.author: cephalin
ms.openlocfilehash: 93645aa5765d533b45fe2266f061ad61c0bf45d7
ms.sourcegitcommit: a7c01dbb03870adcb04ca34745ef256414dfc0b3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/17/2017
---
# <a name="controlling-azure-web-app-traffic-with-azure-traffic-manager"></a>Azure 트래픽 관리자를 사용하여 Azure 웹 앱 트래픽 제어
> [!NOTE]
> 이 문서에서는 Azure Web Apps와 관련된 Microsoft Azure Traffic Manager의 요약 정보를 제공합니다. Microsoft Azure 트래픽 관리자 자체에 대한 추가 정보는 이 문서의 끝 부분에 있는 링크를 방문하면 찾아볼 수 있습니다.
> 
> 

## <a name="introduction"></a>소개
Azure 트래픽 관리자를 사용하면 웹 클라이언트의 요청을 Azure 앱 서비스의 웹 앱에 분산하는 방법을 제어할 수 있습니다. 웹앱 끝점을 Azure Traffic Manager 프로필에 추가하는 경우 Azure Traffic Manager가 웹앱의 상태(실행 중, 중지됨 또는 삭제됨)를 계속 추적하므로 트래픽을 수신할 끝점을 결정할 수 있습니다.

## <a name="routing-methods"></a>라우팅 방법
Azure Traffic Manager는 세 가지의 다른 라우팅 방법을 사용합니다. 이러한 방법은 Azure Web Apps와 관련된 다음 목록에서 설명합니다.

* **[우선 순위](#priority):** 모든 트래픽에 대해 기본 웹앱을 사용하고 기본 또는 백업 웹앱을 사용할 수 없는 경우 백업을 제공합니다.
* **[가중](#weighted):** 웹앱 집합에 균일하게 또는 정의한 가중치에 따라 트래픽을 분산합니다.
* **[성능](#performance):** 웹앱이 여러 지리적 위치에 있을 때 가장 낮은 네트워크 지연 시간을 기준으로 "가장 가까운" 웹앱을 사용합니다.
* **[지리적](#geographic):** DNS 쿼리가 시작된 지리적 위치에 따라 특정 웹앱으로 사용자를 전송합니다. 

자세한 내용은 [Traffic Manager 라우팅 방법](../traffic-manager/traffic-manager-routing-methods.md)을 참조하세요.

## <a name="web-apps-and-traffic-manager-profiles"></a>웹 앱 및 트래픽 관리자 프로필
웹 앱 트래픽을 제어하도록 구성하려면 앞서 설명한 세 가지 부하 분산 방법 중 하나를 사용하는 프로필을 Azure 트래픽 관리자에서 만든 후 트래픽을 제어하려는 끝점(이 경우에는 웹 앱)을 프로필에 추가합니다. 웹앱 상태(실행 중, 중지됨 또는 삭제됨)가 정기적으로 프로필에 전달되므로 Azure Traffic Manager가 상태에 따라 트래픽을 보낼 수 있습니다.

Azure에서 Azure 트래픽 관리자를 사용할 경우 다음 사항에 유의하십시오.

* 동일한 지역 내에서 웹 앱 전용 배포의 경우 웹 앱은 웹 앱 모드와 상관없이 장애 조치(failover) 및 라운드 로빈 기능을 이미 제공합니다.
* 동일한 지역에서 다른 Azure 클라우드 서비스와 함께 웹 앱을 사용하는 배포의 경우 끝점의 두 유형을 결합하여 하이브리드 시나리오를 사용할 수 있습니다.
* 프로필에서 지역당 하나의 웹 앱 끝점만 지정할 수 있습니다. 한 지역의 끝점으로 하나의 웹 앱을 선택한 경우 해당 지역의 나머지 웹 앱은 해당 프로필에 대해 선택할 수 없게 됩니다.
* Azure Traffic Manager 프로필에서 지정한 웹앱 끝점은 프로필에서 웹앱에 대한 구성 페이지의 **도메인 이름** 섹션에 표시되지만 이 섹션에서 구성할 수 없습니다.
* 프로필에 웹앱을 추가한 후 사이트를 설정한 경우 웹앱의 포털 페이지의 대시보드에 있는 **사이트 URL**은 해당 웹앱의 사용자 지정 도메인 URL을 표시합니다. 그렇지 않으면 Traffic Manager 프로필 URL(예: `contoso.trafficmgr.com`)을 표시합니다. 웹앱의 직접적인 도메인 이름과 Traffic Manager URL은 모두 웹앱 구성 페이지의 **도메인 이름** 섹션에 표시됩니다.
* 사용자 지정 도메인 이름이 예상대로 작동하지만 웹앱에 해당 이름을 추가하는 작업 외에도 DNS 맵이 Traffic Manager URL을 가리키도록 구성해야 합니다. Azure 웹앱에 대해 사용자 지정 도메인을 설정하는 방법에 대한 자세한 내용은 [Azure 웹 사이트에 대한 사용자 지정 도메인 이름 구성](app-service-web-tutorial-custom-domain.md)을 참조하세요.
* Standard 또는 Premium 모드의 웹앱만 Azure Traffic Manager 프로필에 추가할 수 있습니다.

## <a name="next-steps"></a>다음 단계
Azure 트래픽 관리자의 개념 및 기술 개요에 대해서는 [트래픽 관리자 개요](../traffic-manager/traffic-manager-overview.md)를 참조하십시오.

Web Apps에서 Traffic Manager를 사용하는 방법에 대한 자세한 내용은 블로그 게시물 [Azure 웹 사이트에서 Azure Traffic Manager 사용](http://blogs.msdn.com/b/waws/archive/2014/03/18/using-windows-azure-traffic-manager-with-waws.aspx) 및 [이제 Azure Traffic Manager를 Azure 웹 사이트와 통합할 수 있음](https://azure.microsoft.com/blog/2014/03/27/azure-traffic-manager-can-now-integrate-with-azure-web-sites/)을 참조하세요.

