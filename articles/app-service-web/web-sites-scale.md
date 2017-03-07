---
title: "Azure에서 앱 강화 | Microsoft Docs"
description: "Azure 앱 서비스에서 앱을 강화하여 용량 및 기능을 추가하는 방법을 알아봅니다."
services: app-service
documentationcenter: 
author: cephalin
manager: erikre
editor: mollybos
ms.assetid: f7091b25-b2b6-48da-8d4a-dcf9b7baccab
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2016
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: bc0fe3341bc5b698032c56dec836b942ce7fa8be
ms.openlocfilehash: 3ca6156369bf4ae530a91520e86166592181511a
ms.lasthandoff: 03/02/2017


---
# <a name="scale-up-an-app-in-azure"></a>Azure에서 앱 강화
이 문서에서는 Azure 앱 서비스에서 앱의 크기를 조정하는 방법에 대해 설명합니다. 크기를 조정하는 두 개의 워크플로(강화, 규모 확장)가 있으며 이 문서에서는 강화 워크플로를 다룹니다.

* [강화](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling): 더 많은 CPU, 메모리, 디스크 공간 및 추가 기능(전용 가상 컴퓨터(VM), 사용자 지정 도메인 및 인증서, 스테이징 슬롯, 자동 크기 조정 등)을 사용할 수 있습니다. 앱이 속한 앱 서비스 계획의 가격 책정 계층을 변경하여 강화합니다.
* [규모 확장](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling): 앱을 실행하는 VM 인스턴스 수가 증가합니다.
  가격 책정 계층에 따라 최대 20개의 인스턴스로 확장할 수 있습니다. [프리미엄](../app-service/app-service-app-service-environments-readme.md) 계층에서 **앱 서비스 환경** 은 규모 확장 개수가 인스턴스 50개로 증가합니다. 규모 확장에 대한 자세한 내용은 [수동 또는 자동으로 인스턴스 개수 조정](../monitoring-and-diagnostics/insights-how-to-scale.md)을 참조하세요. 자동 크기 조정을 사용하는 방법을 찾아볼 수 있으며 이는 미리 정의된 규칙 및 일정에 따라 자동으로 인스턴스 개수를 조정합니다.

크기 조정 설정을 적용하는 데 몇 초밖에 걸리지 않으며 [앱 서비스 계획](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)의 모든 앱에 영향을 줍니다.
코드를 변경하거나 응용 프로그램을 다시 배포할 필요가 없습니다.

개별 앱 서비스 계획의 가격 책정 및 기능에 대한 자세한 내용은 [앱 서비스 가격 정보](https://azure.microsoft.com/pricing/details/web-sites/)를 참조하세요.  

> [!NOTE]
> **무료** 계층에서 앱 서비스 계획을 전환하기 전에 먼저 Azure 구독에 대한 [지출 한도](https://azure.microsoft.com/pricing/spending-limits/) 를 제거해야 합니다. Microsoft Azure App Service 구독 옵션을 보거나 변경하려면 [Microsoft Azure 구독][azuresubscriptions]을 참조하세요.
> 
> 

<a name="scalingsharedorbasic"></a>
<a name="scalingstandard"></a>

## <a name="scale-up-your-pricing-tier"></a>가격 책정 계층 강화
1. 브라우저에서 [Azure Portal][portal]을 엽니다.
2. 앱 블레이드에서 **모든 설정**을 클릭한 다음 **강화**를 클릭합니다.
   
    ![Azure 앱을 강화하기 위해 이동합니다.][ChooseWHP]
3. 사용자 계층을 선택한 다음 **선택**을 클릭합니다.
   
    작업이 완료되면 **알림** 탭에 **성공**이 녹색으로 깜박입니다.

<a name="ScalingSQLServer"></a>

## <a name="scale-related-resources"></a>규모 관련 리소스
앱이 Azure SQL 데이터베이스 또는 Azure 저장소와 같은 다른 서비스에 종속된 경우 요구에 따라 해당 리소스를 강화할 수도 있습니다. 이러한 리소스는 앱 서비스 계획을 사용하여 크기가 조정되지 않고 개별적으로 조정해야 합니다.

1. **필수 항목**에서 **리소스 그룹** 링크를 클릭합니다.
   
    ![Azure 앱의 관련 리소스를 강화합니다.](./media/web-sites-scale/RGEssentialsLink.png)
2. **리소스 그룹** 블레이드의 **요약** 파트에서 크기를 조정하려는 리소스를 클릭합니다. 다음 스크린샷은 SQL 데이터베이스 리소스 및 Azure Storage 리소스를 보여 줍니다.
   
    ![리소스 그룹 블레이드로 이동하여 Azure 앱을 강화합니다.](./media/web-sites-scale/ResourceGroup.png)
3. SQL Database 리소스의 경우 **설정** > **가격 책정 계층**을 클릭하여 가격 책정 계층을 조정합니다.
   
    ![Azure 앱에 대한 SQL 데이터베이스 백 엔드를 강화합니다.](./media/web-sites-scale/ScaleDatabase.png)
   
    또한 SQL 데이터베이스 인스턴스에 [지역에서 복제](../sql-database/sql-database-geo-replication-overview.md) 를 설정합니다.
   
    Azure Storage 리소스의 경우 **설정** > **구성**을 클릭하여 저장소 옵션을 강화합니다.
   
    ![Azure 앱에서 사용하는 Azure 저장소 계정을 강화합니다](./media/web-sites-scale/ScaleStorage.png)

<a name="devfeatures"></a>

## <a name="learn-about-developer-features"></a>개발자 기능에 대해 알아보기
가격 책정 계층에 따라 다음과 같은 개발자 지향 기능을 사용할 수 있습니다.

### <a name="bitness"></a>비트 수
* **기본**, **표준** 및 **프리미엄** 계층은 64비트 및 32비트 응용 프로그램을 지원합니다.
* **무료** 및 **공유** 계획 계층은 32비트 응용 프로그램만 지원합니다.

### <a name="debugger-support"></a>디버거 지원
* 디버거 지원은 App Service 계획당&1;건의 연결에서 **무료**, **공유** 및 **기본** 모드에 대해 사용할 수 있습니다.
* 디버거 지원은 App Service 계획당&5;건의 동시 연결에서 **표준** 및 **프리미엄** 모드에 대해 사용할 수 있습니다.

<a name="OtherFeatures"></a>

## <a name="learn-about-other-features"></a>다른 기능에 대해 알아보기
* 개발자를 비롯하여 모든 사용자에게 유용한 가격 및 기능을 포함하여 앱 서비스 계획의 나머지 모든 기능에 대한 자세한 내용은 [앱 서비스 가격 정보](https://azure.microsoft.com/pricing/details/web-sites/)를 참조하세요.

> [!NOTE]
> Azure 계정을 등록하기 전에 Azure App Service를 시작하려면 [App Service 평가](https://azure.microsoft.com/try/app-service/)로 이동합니다. App Service에서 단기 스타터 웹앱을 즉시 만들 수 있습니다. 신용 카드는 필요하지 않으며 약정은 없습니다.
> 
> 

<a name="Next Steps"></a>

## <a name="next-steps"></a>다음 단계
* Azure에 등록하려면 [Microsoft Azure 무료 평가판](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.
* 가격 책정, 지원 및 SLA에 대한 자세한 내용은 다음 링크를 방문하세요.
  
    [데이터 전송 가격 정보](https://azure.microsoft.com/pricing/details/data-transfers/)
  
    [Microsoft Azure 지원 계획](https://azure.microsoft.com/support/plans/)
  
    [서비스 수준 계약](https://azure.microsoft.com/support/legal/sla/)
  
    [SQL 데이터베이스 가격 정보](https://azure.microsoft.com/pricing/details/sql-database/)
  
    [Microsoft Azure를 위한 가상 컴퓨터 및 클라우드 서비스 크기][vmsizes]
  
    [앱 서비스 가격 정보](https://azure.microsoft.com/pricing/details/app-service/)
  
    [앱 서비스 가격 정보 - SSL 연결](https://azure.microsoft.com/pricing/details/web-sites/#ssl-connections)
* 확장성 있고 복원력이 뛰어난 아키텍처 빌드를 포함하여 Azure 앱 서비스 모범 사례에 대한 자세한 내용은 [모범 사례: Azure 앱 서비스 웹앱](http://blogs.msdn.com/b/windowsazure/archive/2014/02/10/best-practices-windows-azure-websites-waws.aspx)을 참조하세요.
* 앱 서비스 앱 크기 조정에 대한 비디오는 다음 리소스를 참조하세요.
  
  * [Azure 웹 사이트 크기를 조정하는 방법 - 스테판 스차코우(Stefan Schackow)](https://azure.microsoft.com/en-us/resources/videos/azure-web-sites-free-vs-standard-scaling/)
  * [Azure 웹 사이트, CPU 또는 예약 리소스 크기 자동 조정 - 스테판 스차코우(Stefan Schackow)](https://azure.microsoft.com/en-us/resources/videos/auto-scaling-azure-web-sites/)
  * [Azure 웹 사이트 크기 조정 방법 - 스테판 스차코우(Stefan Schackow)](https://azure.microsoft.com/en-us/resources/videos/how-azure-web-sites-scale/)

<!-- LINKS -->
[vmsizes]:/pricing/details/app-service/
[SQLaccountsbilling]:http://go.microsoft.com/fwlink/?LinkId=234930
[azuresubscriptions]:http://go.microsoft.com/fwlink/?LinkID=235288
[portal]: https://portal.azure.com/

<!-- IMAGES -->
[ChooseWHP]: ./media/web-sites-scale/scale1ChooseWHP.png
[ChooseBasicInstances]: ./media/web-sites-scale/scale2InstancesBasic.png
[SaveButton]: ./media/web-sites-scale/05SaveButton.png
[BasicComplete]: ./media/web-sites-scale/06BasicComplete.png
[ScaleStandard]: ./media/web-sites-scale/scale3InstancesStandard.png
[Autoscale]: ./media/web-sites-scale/scale4AutoScale.png
[SetTargetMetrics]: ./media/web-sites-scale/scale5AutoScaleTargetMetrics.png
[SetFirstRule]: ./media/web-sites-scale/scale6AutoScaleFirstRule.png
[SetSecondRule]: ./media/web-sites-scale/scale7AutoScaleSecondRule.png
[SetThirdRule]: ./media/web-sites-scale/scale8AutoScaleThirdRule.png
[SetRulesFinal]: ./media/web-sites-scale/scale9AutoScaleFinal.png
[ResourceGroup]: ./media/web-sites-scale/scale10ResourceGroup.png
[ScaleDatabase]: ./media/web-sites-scale/scale11SQLScale.png
[GeoReplication]: ./media/web-sites-scale/scale12SQLGeoReplication.png

