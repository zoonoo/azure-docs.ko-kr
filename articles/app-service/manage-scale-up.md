---
title: 기능 및 용량 확장
description: Azure 앱 서비스에서 앱을 확장하는 방법을 알아봅니다. 더 많은 CPU, 메모리, 디스크 공간 및 추가 기능을 가져옵니다.
ms.assetid: f7091b25-b2b6-48da-8d4a-dcf9b7baccab
ms.topic: article
ms.date: 08/19/2019
ms.custom: seodec18
ms.openlocfilehash: dfd9297e80836978b8a185df3fc4659676383802
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75659902"
---
# <a name="scale-up-an-app-in-azure-app-service"></a>Azure 앱 서비스에서 앱 확장

이 문서에서는 Azure App Service에서 앱의 크기를 조정하는 방법에 대해 설명합니다. 크기를 조정하는 두 개의 워크플로(강화, 규모 확장)가 있으며 이 문서에서는 강화 워크플로를 다룹니다.

* [강화](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling): 더 많은 CPU, 메모리, 디스크 공간 및 추가 기능(전용 가상 머신(VM), 사용자 지정 도메인 및 인증서, 스테이징 슬롯, 자동 크기 조정 등)을 사용할 수 있습니다. 앱이 속한 App Service 계획의 가격 책정 계층을 변경하여 강화합니다.
* [규모 확장](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling): 앱을 실행하는 VM 인스턴스 수가 증가합니다.
  가격 책정 계층에 따라 최대 30개의 인스턴스로 확장할 수 있습니다. [고립](environment/intro.md) 계층에서 **App Service 환경**은 규모 확장 수를 100개 인스턴스까지 증대합니다. 규모 확장에 대한 자세한 내용은 [수동 또는 자동으로 인스턴스 개수 조정](../monitoring-and-diagnostics/insights-how-to-scale.md)을 참조하세요. 자동 크기 조정을 사용하는 방법을 찾아볼 수 있으며 이는 미리 정의된 규칙 및 일정에 따라 자동으로 인스턴스 개수를 조정합니다.

크기 조정 설정을 적용하는 데 몇 초밖에 걸리지 않으며 [App Service 계획](../app-service/overview-hosting-plans.md)의 모든 앱에 영향을 줍니다.
코드를 변경하거나 애플리케이션을 다시 배포할 필요가 없습니다.

개별 App Service 계획의 가격 책정 및 기능에 대한 자세한 내용은 [App Service 가격 정보](https://azure.microsoft.com/pricing/details/web-sites/)를 참조하세요.  

> [!NOTE]
> **무료** 계층에서 App Service 계획을 전환하기 전에 먼저 Azure 구독에 대한 [지출 한도](https://azure.microsoft.com/pricing/spending-limits/) 를 제거해야 합니다. Microsoft Azure App Service 구독 옵션을 보거나 변경하려면 [Microsoft Azure 구독][azuresubscriptions]을 참조하세요.
> 
> 

<a name="scalingsharedorbasic"></a>
<a name="scalingstandard"></a>

## <a name="scale-up-your-pricing-tier"></a>가격 책정 계층 강화

> [!NOTE]
> **PremiumV2** 계층으로 확장하려면 [App Service에 대한 PremiumV2 계층 구성](app-service-configure-premium-tier.md)을 참조하세요.
>

1. 브라우저에서 [Azure 포털을][portal]엽니다.

1. 앱 서비스 앱 페이지에서 왼쪽 메뉴에서 **확장(앱 서비스 요금제)을**선택합니다.
   
3. 티어를 선택한 다음 **적용을**선택합니다. 다양한 범주(예: **프로덕션)를**선택하고 더 많은 계층을 표시할 **추가 옵션을 참조하십시오.**
   
    ![Azure 앱을 강화하기 위해 이동합니다.][ChooseWHP]

    작업이 완료되면 녹색 성공 확인 표시가 있는 알림 팝업이 표시됩니다.

<a name="ScalingSQLServer"></a>

## <a name="scale-related-resources"></a>규모 관련 리소스
앱이 Azure SQL Database, Azure Storage 등의 다른 서비스에 종속된 경우 요구에 따라 해당 리소스를 개별적으로 강화할 수도 있습니다. 이러한 리소스는 App Service 계획에서 관리되지 않습니다.

1. 앱의 **개요** 페이지에서 **리소스 그룹** 링크를 선택합니다.
   
    ![Azure 앱의 관련 리소스를 강화합니다.](./media/web-sites-scale/RGEssentialsLink.png)

2. **리소스 그룹** 페이지의 **요약** 부분에서 확장할 리소스를 선택합니다. 다음 스크린샷은 SQL Database 리소스를 보여 주며 있습니다.
   
    ![리소스 그룹 페이지로 이동하여 Azure 앱을 강화합니다.](./media/web-sites-scale/ResourceGroup.png)

    관련 리소스를 확장하려면 특정 리소스 유형에 대한 설명서를 참조하십시오. 예를 들어 단일 SQL 데이터베이스를 확장하려면 Azure SQL Database 에서 [단일 데이터베이스 리소스 확장](../sql-database/sql-database-single-database-scale.md)을 참조하세요. MySQL 리소스에 대한 Azure 데이터베이스를 확장하려면 [MySQL 리소스 확장을](../mysql/concepts-pricing-tiers.md#scale-resources)참조하십시오.

<a name="OtherFeatures"></a>
<a name="devfeatures"></a>

## <a name="compare-pricing-tiers"></a>가격 책정 계층 비교

각 가격 책정 계층의 VM 크기 등, 상세 정보는 [App Service 가격 책정 정보](https://azure.microsoft.com/pricing/details/app-service)를 참조하세요.

각 계층의 서비스 한도, 할당량, 제약 조건과 지원 기능은 [App Service 제한](../azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits)을 참조하세요.

<a name="Next Steps"></a>

## <a name="more-resources"></a>추가 리소스

[수동 또는 자동으로 인스턴스 개수 조정](../monitoring-and-diagnostics/insights-how-to-scale.md)  
[앱 서비스에 대한 PremiumV2 계층 구성](app-service-configure-premium-tier.md)

<!-- LINKS -->
[vmsizes]:https://azure.microsoft.com/pricing/details/app-service/
[SQLaccountsbilling]:https://go.microsoft.com/fwlink/?LinkId=234930
[azuresubscriptions]:https://account.windowsazure.com/subscriptions
[portal]: https://portal.azure.com/

<!-- IMAGES -->
[ChooseWHP]: ./media/web-sites-scale/scale1ChooseWHP.png
[ResourceGroup]: ./media/web-sites-scale/scale10ResourceGroup.png
[ScaleDatabase]: ./media/web-sites-scale/scale11SQLScale.png
[GeoReplication]: ./media/web-sites-scale/scale12SQLGeoReplication.png
