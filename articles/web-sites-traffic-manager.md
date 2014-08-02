<properties linkid="web-sites-traffic-manager" urlDisplayName="Controlling Windows Azure Web Sites Traffic with Azure Traffic Manager" pageTitle="Controlling Azure Web Sites Traffic with Azure Traffic Manager" metaKeywords="Azure Web Sites, Traffic Manager, request routing, round robin, failover, performance" description="This article provides summary information for  Azure Traffic Manager as it relates to Azure Web Sites." metaCanonical="" services="web-sites" documentationCenter="" title="Controlling Azure Web Sites Traffic with Azure Traffic Manager" authors="timamm" solutions="" writer="timamm" manager="paulettm" editor="mollybos" />

Azure 트래픽 관리자를 사용하여 Azure 웹 사이트 트래픽 제어
==========================================================

> [WACOM.NOTE] 이 문서에서는 Azure 웹 사이트와 관련된 Microsoft Azure 트래픽 관리자의 요약 정보를 제공합니다. Microsoft Azure 트래픽 관리자 자체에 대한 추가 정보는 이 문서의 끝 부분에 있는 링크를 방문하면 찾아볼 수 있습니다.

소개
----

Azure 트래픽 관리자를 사용하면 웹 클라이언트의 요청을 Azure 웹 사이트에 분산하는 방법을 제어할 수 있습니다. Azure 웹 사이트 끝점을 Azure 트래픽 관리자 프로필에 추가하는 경우 Azure 트래픽 관리자가 웹 사이트의 상태(실행 중, 중지됨 또는 삭제됨)를 계속 추적하므로 트래픽을 수신할 끝점을 결정할 수 있습니다.

부하 분산 방법
--------------

Azure 트래픽 관리자는 세 가지의 다른 부하 분산 방법을 사용합니다. 각 방법은 Azure 웹 사이트와 관련된 다음 목록에서 설명합니다.

-   **장애 조치(Failover)**: 다른 지역에 웹 사이트 클론이 있는 경우 이 방법을 사용하여 모든 웹 클라이언트 트래픽을 처리하도록 웹 사이트를 하나 구성하고, 첫 번째 웹 사이트를 사용할 수 없게 되는 경우 해당 트래픽을 처리하도록 다른 지역에 추가 웹 사이트를 구성할 수 있습니다.

-   **라운드 로빈**: 다른 지역에 웹 사이트 클론이 있는 경우 이 방법을 사용하여 여러 지역의 웹 사이트에 균등하게 트래픽을 분산할 수 있습니다.

-   **성능**: 성능 방법은 클라이언트에 대한 가장 짧은 왕복 시간에 따라 트래픽을 분산합니다. 성능 방법은 동일한 지역 내에 있거나 다른 지역에 있는 웹 사이트에 사용할 수 있습니다.

Azure 트래픽 관리자의 부하 분산에 대한 자세한 내용은 [트래픽 관리자 부하 분산 방법 정보](http://msdn.microsoft.com/en-us/library/windowsazure/dn339010.aspx)를 참조하십시오.

Azure 웹 사이트 및 트래픽 관리자 프로필
---------------------------------------

웹 사이트 트래픽을 제어하도록 구성하려면 앞서 설명한 세 가지 부하 분산 방법 중 하나를 사용하는 프로필을 Azure 트래픽 관리자에서 만든 후 트래픽을 제어하려는 끝점(이 경우에는 웹 사이트)을 프로필에 추가합니다. 웹 사이트 상태(실행 중, 중지됨 또는 삭제됨)가 정기적으로 프로필에 전달되므로 Azure 트래픽 관리자가 상태에 따라 트래픽을 보낼 수 있습니다.

Azure에서 Azure 트래픽 관리자를 사용할 경우 다음 사항에 유의하십시오.

-   동일한 지역 내에서 웹 사이트 전용 배포의 경우 Azure 웹 사이트는 웹 사이트 모드와 상관없이 장애 조치(Failover) 및 라운드 로빈 기능을 이미 제공합니다.

-   동일한 지역에서 다른 Azure 클라우드 서비스와 함께 Azure 웹 사이트를 사용하는 배포의 경우 끝점의 두 유형을 결합하여 하이브리드 시나리오를 사용할 수 있습니다.

-   프로필에서 지역당 하나의 웹 사이트 끝점만 지정할 수 있습니다. 한 지역의 끝점으로 하나의 웹 사이트를 선택한 경우 해당 지역의 나머지 웹 사이트는 해당 프로필에 대해 선택할 수 없게 됩니다.

-   Azure 트래픽 관리자 프로필에서 지정한 웹 사이트 끝점은 프로필에서 웹 사이트에 대한 구성 페이지의 **도메인 이름** 섹션에 표시되지만 이 섹션에서 구성할 수 없습니다.

-   프로필에 웹 사이트를 추가한 후 사이트를 설정한 경우 웹 사이트 포털 페이지의 대시보드에 있는 **사이트 URL**은 해당 웹 사이트의 사용자 지정 도메인 URL을 표시합니다. 그렇지 않으면 트래픽 관리자 프로필 URL(예: `contoso.trafficmgr.com`)을 표시합니다. 웹 사이트의 직접적인 도메인 이름과 트래픽 관리자 URL은 모두 웹 사이트 구성 페이지의 **도메인 이름** 섹션에 표시됩니다.

-   사용자 지정 도메인 이름이 예상대로 작동하지만 웹 사이트에 해당 이름을 추가하는 작업 외에도 DNS 맵이 트래픽 관리자 URL을 가리키도록 구성해야 합니다. Azure 웹 사이트에 대해 사용자 지정 도메인을 설정하는 방법에 대한 자세한 내용은 [Azure 웹 사이트에 대한 사용자 지정 도메인 이름 구성](https://www.windowsazure.com/en-us/documentation/articles/web-sites-custom-domain-name/)(영문)을 참조하십시오.

-   표준 모드에 있는 웹 사이트만 Azure 트래픽 관리자 프로필에 추가할 수 있습니다.

다음 단계
---------

Azure 트래픽 관리자의 개념 및 기술 개요에 대해서는 [트래픽 관리자 개요](http://msdn.microsoft.com/en-us/library/windowsazure/hh744833.aspx)를 참조하십시오.

Azure 웹 사이트 사용을 비롯하여 Azure 트래픽 관리자를 구성하는 방법에 대한 자세한 내용은 [트래픽 관리자 구성 작업](http://msdn.microsoft.com/en-us/library/windowsazure/hh744830.aspx)을 참조하십시오.

Azure 트래픽 관리자의 부하 분산에 대한 자세한 내용은 [트래픽 관리자 부하 분산 방법 정보](http://msdn.microsoft.com/en-us/library/windowsazure/dn339010.aspx)를 참조하십시오.

