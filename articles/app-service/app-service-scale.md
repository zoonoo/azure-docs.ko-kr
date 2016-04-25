<properties
	pageTitle="Azure 앱 서비스에서 가격 책정 계층 크기 조정"
	description="자동 크기 조정을 포함하여 Azure 앱 서비스에서 웹앱, 모바일 앱, API 앱 및 논리 앱의 크기를 조정하는 방법을 알아봅니다."
	services="app-service"
	documentationCenter=""
	authors="stepsic-microsoft-com"
	manager="wpickett"
	editor="mollybos"/>

<tags
	ms.service="app-service"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/09/2016"
	ms.author="stepsic"/>

# Azure 앱 서비스에서 가격 책정 계층 크기 조정

Microsoft Azure에서 앱의 성능 및 처리량 향상을 위해 [Azure 포털](https://portal.azure.com/)을 사용하여 앱 서비스 계획을 **무료**에서 **공유**, **기본**, **표준** 또는 **프리미엄**으로 크기 조정할 수 있습니다.

앱 서비스 계획의 서비스 수준은 [계획의 *가격 책정 계층*](/pricing/details/app-service/)을 기반으로 합니다. **표준** 및 **프리미엄**과 같은 높은 가격 책정 계층은 Azure의 리소스 사용 방법을 결정하는 데 보다 뛰어난 유연성과 견고성을 제공합니다. 가격 책정 계층을 변경하면 서비스의 코어 수와 메모리 양에 영향을 주며, 이를 *강화*(또는 *규모 축소*)이라고 합니다.

가격 책정 계층의 규모를 증가하는 것 외에도 서비스의 인스턴스 수를 늘릴 수 있습니다. 이를 *규모 확장* 또는 *규모 감축*이라고 합니다. *규모 확장* 및 *규모 감축*에 대한 자세한 내용은 [수동 또는 자동으로 인스턴스 개수 크기 조정](../azure-portal/insights-how-to-scale.md) 문서를 참조하세요.

앱 서비스 계획에 대한 자세한 내용은 [Azure 앱 서비스 계획의 포괄 개요](azure-web-sites-web-hosting-plans-in-depth-overview.md)를 참조하세요. 개별 앱 서비스 계획의 가격 및 기능에 대한 자세한 내용은 [앱 서비스 가격 정보](/pricing/details/app-service/)를 참조하세요.

마지막으로, 전용 [앱 서비스 환경](app-service-app-service-environments-readme.md)에서 사용하려는 경우는 크기 조정 작업이 다르게 작동합니다. 자세한 내용은 [앱 서비스 환경에서 웹앱 확장](../app-service-web/app-service-web-scale-a-web-app-in-an-app-service-environment.md)을 참조하세요.

> [AZURE.NOTE] **무료** 모드에서 **기본**, **표준** 또는 **프리미엄** 모드로 전환하기 전에 Azure 앱 서비스 구독에 대한 지출 한도를 먼저 제거해야 합니다. Microsoft Azure 앱 서비스 구독 옵션을 보거나 변경하려면 [Microsoft Azure 구독][azuresubscriptions]을 참조하세요.

<a name="scalingsharedorbasic"></a> <a name="scalingstandard"></a>

## 가격 책정 계층 크기 조정

1. 브라우저에서 [Azure 포털][portal]을 열고 크기를 조정할 앱을 찾습니다.

2. 앱의 **필수 항목**에서 **앱 서비스 계획/가격 책정 계층** 링크를 클릭합니다.

3. **가격 책정 계층**을 클릭하면 계획에 대한 가능한 서비스 수준 목록이 표시됩니다. 계층의 평균 비용을 파악할 수 있도록 각 계층과 함께 예상 가격이 표시됩니다.

	![계획 선택](./media/app-service-scale/ChoosePricingTier.png)

4. 계층을 선택했으면 **선택**을 클릭합니다.

	작업이 완료되면 **알림** 탭에 **성공**이 녹색으로 깜박입니다.

또한 [여기서](http://go.microsoft.com/fwlink/?LinkId=309169) Azure의 다양한 계산 계층에 대해 알아볼 수 있습니다.

<a name="ScalingSQLServer"></a>
##관련 리소스 크기 조정
앱이 SQL 또는 저장소와 같은 다른 서비스에 종속된 경우 요구에 따라 이러한 서비스의 크기를 조정할 수도 있습니다.

1. **필수 항목**에서 **리소스 그룹** 링크를 클릭합니다.

2. 그런 다음 리소스 그룹 블레이드의 **요약** 파트에서 데이터베이스(또는 크기를 조정하려는 다른 리소스) 중 하나를 클릭합니다.

	![연결된 데이터베이스](./media/app-service-scale/ResourceGroup.png)

3. 연결된 리소스 블레이드에서 **가격 책정 계층** 파트를 클릭하고 성능 요구 사항에 따라 계층 중 하나를 선택한 다음 **선택**을 클릭합니다.

	![SQL 데이터베이스 크기 조정](./media/app-service-scale/ScaleDatabase.png)

4. 앱이 저장소를 사용하는 경우 지역에서 복제를 지원하는 가격 책정 계층을 선택하면 해당 기능이 자동으로 설정됩니다. 반면, SQL의 경우 SQL 데이터베이스의 고가용성 및 재해 복구 기능을 향상시키기 위해 지역에서 복제를 수동으로 구성해야 합니다. 이렇게 하려면 **지역에서 복제** 파트를 클릭합니다.

	![SQL 데이터베이스에 대한 지역에서 복제 설정](./media/app-service-scale/GeoReplication.png)

<a name="devfeatures"></a>
## 개발자 기능
가격 책정 계층에 따라 다음과 같은 개발자 지향 기능을 사용할 수 있습니다.

### 비트 수 ###

- **기본**, **표준** 및 **프리미엄** 계층은 64비트 및 32비트 응용 프로그램을 지원합니다.
- **무료** 및 **표준** 계획 계층은 32비트 응용 프로그램만 지원합니다.

### 디버거 지원 ###

- 디버거 지원은 앱 서비스 계획당 1건의 동시 연결에서 **무료**, **공유** 및 **기본** 모드에 대해 사용할 수 있습니다.
- 디버거 지원은 앱 서비스 계획당 5건의 동시 연결에서 **표준** 및 **프리미엄** 모드에 대해 사용할 수 있습니다.

<a name="OtherFeatures"></a>
## 기타 기능

- 개발자를 비롯하여 모든 사용자에게 유용한 가격 및 기능을 포함하여 앱 서비스 계획의 나머지 모든 기능에 대한 자세한 내용은 [앱 서비스 가격 정보](/pricing/details/web-sites/)를 참조하세요.

>[AZURE.NOTE] Azure 계정을 등록하기 전에 Azure 앱 서비스를 시작하려면 [앱 서비스 평가](http://go.microsoft.com/fwlink/?LinkId=523751)로 이동합니다. 앱 서비스에서 단기 시작 웹앱을 즉시 만들 수 있습니다. 신용 카드는 필요하지 않으며 약정도 필요하지 않습니다.

<a name="Next Steps"></a>
## 다음 단계

- Azure에 등록하려면 [Microsoft Azure 무료 평가판](/pricing/free-trial/)을 참조하세요.
- 가격, 지원 및 SLA에 대한 자세한 내용은 다음 링크를 방문하세요.

	[데이터 전송 가격 정보](/pricing/details/data-transfers/)

	[Microsoft Azure 지원 계획](/support/plans/)

	[서비스 수준 계약](/support/legal/sla/)

	[SQL 데이터베이스 가격 정보](/pricing/details/sql-database/)

	[Microsoft Azure를 위한 가상 컴퓨터 및 클라우드 서비스 크기][vmsizes]

	[앱 서비스 가격 정보](/pricing/details/app-service/)

	[앱 서비스 가격 정보 - SSL 연결](/pricing/details/web-sites/#ssl-connections)

- 확장 가능하며 복원력이 뛰어난 아키텍처 빌드를 포함하여 Azure 앱 서비스 모범 사례에 대한 자세한 내용은 [모범 사례: Azure 앱 서비스 웹앱](http://blogs.msdn.com/b/windowsazure/archive/2014/02/10/best-practices-windows-azure-websites-waws.aspx)을 참조하세요.

- 웹 앱 크기 조정에 대한 비디오:

	- [Azure 웹 사이트 크기를 조정하는 방법 - 스테판 스차코우(Stefan Schackow)](/documentation/videos/azure-web-sites-free-vs-standard-scaling/)
	- [Azure 웹 사이트, CPU 또는 예약 리소스 크기 자동 조정 - 스테판 스차코우(Stefan Schackow)](/documentation/videos/auto-scaling-azure-web-sites/)
	- [Azure 웹 사이트 크기 조정 방법 - 스테판 스차코우(Stefan Schackow)](/documentation/videos/how-azure-web-sites-scale/)

## 변경된 내용
* 웹 사이트에서 앱 서비스로의 변경에 대한 지침은 [Azure 앱 서비스와 이 서비스가 기존 Azure 서비스에 미치는 영향](http://go.microsoft.com/fwlink/?LinkId=529714)을 참조하세요.

<!-- LINKS -->
[vmsizes]: http://go.microsoft.com/fwlink/?LinkId=309169
[SQLaccountsbilling]: http://go.microsoft.com/fwlink/?LinkId=234930
[azuresubscriptions]: http://go.microsoft.com/fwlink/?LinkID=235288
[portal]: https://portal.azure.com/

<!-- IMAGES -->
[ResourceGroup]: ./media/web-sites-scale/scale10ResourceGroup.png
[ScaleDatabase]: ./media/web-sites-scale/scale11SQLScale.png
[GeoReplication]: ./media/web-sites-scale/scale12SQLGeoReplication.png

<!---HONumber=AcomDC_0413_2016-->