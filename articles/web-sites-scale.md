<properties 
	pageTitle="Azure 앱 서비스에서 웹 앱 크기 조정" 
	description="자동 크기 조정을 포함하여 Azure 앱 서비스에서 웹 앱의 크기를 확장하고 축소하는 방법에 대해 알아봅니다." 
	services="app-service\web" 
	documentationCenter="" 
	authors="cephalin" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/24/2015" 
	ms.author="cephalin"/>

# Azure 앱 서비스에서 웹 앱 크기 조정 #

Microsoft Azure에서 웹 앱에 대한 향상된 성능 및 처리량은 [Azure 포털](http://go.microsoft.com/fwlink/?LinkId=529715)을 사용하여 [앱 서비스](http://go.microsoft.com/fwlink/?LinkId=529714) 계획을 **무료** 모드에서 **공유**, **기본**, **표준** 또는 **프리미엄** 모드로 크기를 조정할 수 있습니다. 

Azure 웹 앱 크기를 확장하려면 두 가지 관련 작업을 수행해야 합니다. 먼저 앱 서비스 계획 모드를 더 높은 수준의 서비스로 변경하고, 더 높은 수준의 서비스로 전환한 후에는 특정 설정을 구성합니다. 이 문서에서는 두 가지 작업을 모두 다룹니다. **표준** 및 **프리미엄** 모드와 같이 더 높은 서비스 계층은 Azure의 리소스 사용 방법을 결정하는 데 보다 뛰어난 유연성과 견고성을 제공합니다.

관리 포털의 크기 조정 탭에서 모드를 쉽게 변경하고 구성할 수 있습니다. 필요에 따라 크기를 확장하거나 축소할 수 있습니다. 이러한 변경은 적용하는 데 몇 초밖에 걸리지 않으며 앱 서비스 계획의 모든 웹 앱에 영향을 줍니다. 또한 변경을 적용하기 위해 코드를 변경하거나 응용 프로그램을 다시 배포할 필요도 없습니다.

앱 서비스 계획에 대한 자세한 내용은 [앱 서비스 계획의 정의](web-sites-web-hosting-plan-overview.md) and [Azure 앱 서비스 계획의 보다 자세한 개요](azure-web-sites-web-hosting-plans-in-depth-overview.md).를 참조하세요. 개별 앱 서비스 계획의 가격 및 기능에 대한 내용은 [앱 서비스 가격 세부 정보](/pricing/details/web-sites/).  를 참조하세요.

> [AZURE.NOTE] 웹 앱을 **무료** 모드에서 **기본**, **표준** 또는 **프리미엄** 모드로 전환하기 전에 Azure 앱 서비스 구독에 대한 지출 한도를 제거해야 합니다. Microsoft Azure 앱 서비스 구독에 대한 옵션을 보거나 변경하려면 [Microsoft Azure 구독][azuresubscriptions]을 참조하세요.

<a name="scalingsharedorbasic"></a>
<!-- ===================================== -->
## 공유 또는 기본 모드로 크기 조정
<!-- ===================================== -->

1. 브라우저에서 [Azure 포털][portal]을 엽니다.
	
2. 웹 앱의 블레이드에서 **모든 설정**, **크기 조정**을 차례로 클릭한 다음 **인스턴스를 추가하고 더 나은 성능을 얻으려면 무료 계획 업그레이드**를 클릭합니다.
	
	![계획 선택][ChooseWHP]
	
4. **가격 책정 계층 선택** 블레이드에서 **공유** 또는 **기본** 모드를 선택한 다음 **선택**을 클릭합니다.
	
	**알림** 탭은 작업이 완료되면 녹색 **성공**으로 깜박입니다. 
	
5. 인스턴스 수를 늘리려면 왼쪽에서 오른쪽으로 **인스턴스** 막대를 슬라이드하고 명령 모음에서 **저장**을 클릭합니다. **공유** 모드에서는 인스턴스 크기 옵션을 사용할 수 없습니다. 인스턴스 크기에 대한 자세한 내용은 [Microsoft Azure를 위한 가상 컴퓨터 및 클라우드 서비스 크기][vmsizes]를 참조하세요.
	
	![기본 모드에 대한 인스턴스 크기][ChooseBasicInstances]
	
	**알림** 탭은 작업이 완료되면 녹색 **성공**으로 깜박입니다. 
	
<a name="scalingstandard"></a>
<!-- ================================= -->
## 표준 또는 프리미엄 모드로 크기 조정
<!-- ================================= -->

> [AZURE.NOTE] 웹 호스팅 계획을 **표준** 또는 **프리미엄** 모드로 전환하기 전에 Microsoft Azure 앱 서비스 구독에 대한 지출 한도를 제거해야 합니다. 그렇지 않으면 대금 청구 기간이 끝나기 전에 한도에 도달할 경우 웹 앱을 사용할 수 없게 됩니다. Microsoft Azure 앱 서비스 구독에 대한 옵션을 보거나 변경하려면 [Microsoft Azure 구독][azuresubscriptions]을 참조하세요.

1. **표준** 또는 **프리미엄** 모드로 크기를 조정하려면 **공유** 또는 **기본**으로 크기를 조정할 때와 동일한 초기 단계를 따라 **가격 책정 계층 선택**에서 **표준** 또는 **프리미엄** 모드를 선택한 다음 **선택**을 클릭합니다. 
	
	**알림** 탭은 작업이 완료되면 녹색 **성공**으로 깜박이며 **자동 크기 조정 모드**가 활성화됩니다.
	
	![표준 또는 프리미엄 모드로 크기 조정][ScaleStandard]
	
	**인스턴스** 막대를 슬라이드하여 위에서 설명한 **기본** 모드에서와 같이 더 많은 인스턴스로 크기를 수동으로 조정할 수 있습니다. 그러나 여기에서는 **자동 크기 조정 모드** 사용 방법을 배웁니다. 
	
2. **자동 크기 조정 모드**에서 **성능**을 선택하면 성능 메트릭스에 기반하여 크기를 자동으로 조정합니다.
	
	![자동 크기 조정 모드를 성능으로 설정][Autoscale]
	
3. **인스턴스 범위**에서 두 슬라이드를 이동하여 앱 서비스 계획에 대해 자동으로 크기를 조정할 인스턴스의 최소 및 최대 수를 정의합니다. 이 자습서에서는 최대 슬라이더를 **6** 인스턴스로 이동합니다.
	
4. 명령 모음에서 **저장**을 클릭합니다.
	
4. **대상 메트릭**에서 **>**를 클릭하여 기본 메트릭에 대한 자동 크기 조정 규칙을 구성합니다.  
	
	![대상 메트릭 설정][SetTargetMetrics]
	
	CPU, 메모리, 디스크 큐, HTTP 큐 및 데이터 흐름을 포함하여 다른 성능 메트릭에 대한 자동 크기 조정 규칙을 구성할 수 있습니다. 여기에서는 다음을 수행하는 CPU 비율에 대한 자동 크기 조정을 구성합니다.
	
	- 마지막 10분 후에 CPU가 70% 이상인 경우 1인스턴스씩 크기를 확장합니다.
	- 마지막 5분 후에 CPU가 90% 이상인 경우 3인스턴스씩 크기를 확장합니다.
	- 마지막 30분 후에 CPU가 50% 미만인 경우 1인스턴스씩 크기를 축소합니다. 
	
	
4. **메트릭** 드롭다운을 **CPU 비율**로 둡니다.
	
5. **크기 확장 규칙**에서 **조건**을 **보다 큼**으로, **임계값**을 **70**(%)로, **경과 시간**을 **10**(분)으로, **확장 단위**를 **1**(인스턴스)로, **휴지 기간**을 **10**(분)으로 설정하여 첫 번째 규칙을 구성합니다. 
	
	![첫 번째 자동 크기 조정 규칙 설정][SetFirstRule]
	
	>[AZURE.NOTE] **휴지 기간** 설정은 이전 크기 조정 작업 이후에 이 규칙이 다시 크기 조정하도록 대기해야 하는 시간을 지정합니다.
	
6. **크기 확장 규칙 추가**를 클릭한 다음 **조건**을 **보다 큼**으로, **임계값**을 **90**(%)로, **경과 시간**을 **1**(분)으로, **확장 단위**를 **3**(인스턴스)로, **휴지 기간**을 **1**(분)으로 설정하여 두 번째 규칙을 구성합니다.
	
	![두 번째 자동 크기 조정 규칙 설정][SetSecondRule]
	
5. **크기 축소 규칙**에서 **조건**을 **적음**으로, **임계값**을 **50**(%)로, **경과 시간**을 **30**(분)으로, **축소 단위**를 **1**(인스턴스)로, **휴지 기간**을 **60**(분)으로 설정하여 세 번째 규칙을 구성합니다. 
	
	![세 번째 자동 크기 조정 규칙 설정][SetThirdRule]
	
7. 명령 모음에서 **저장**을 클릭합니다. 이제 자동 크기 조정 규칙은 **크기 조정** 블레이드에 반영되어야 합니다. 
	
	![자동 크기 조정 규칙 결과 설정][SetRulesFinal]

<a name="ScalingSQLServer"></a>
##웹 앱에 연결된 SQL Server 데이터베이스 크기 조정
웹 앱에 연결된 하나 이상의 SQL 서버 데이터베이스가 있는 경우(앱 서비스 계획 모드와 무관) 필요에 따라 빠르게 크기를 조정할 수 있습니다.

1. 연결된 데이터베이스 중 하나의 크기를 조정하려면 [Azure 포털][portal]에서 웹 앱의 블레이드를 엽니다. 축소 가능한 **필수 항목** 드롭다운에서 **리소스 그룹** 링크를 클릭합니다. 그런 다음 리소스 그룹 블레이드의 **요약** 부분에서 연결된 데이터베이스 중 하나를 클릭했습니다.

	![연결된 데이터베이스][ResourceGroup]
	
2. 연결된 SQL 데이터베이스 블레이드에서**가격 책정 계층** 부분을 클릭하고 성능 요구 사항에 따른 계층 중 하나를 선택한 다음 **선택**을 클릭합니다. 
	
	![SQL 데이터베이스 크기 조정][ScaleDatabase]
	
3. SQL 데이터베이스의 고가용성 및 재해 복구 기능을 높이기 위해 지리적 복제를 설정할 수도 있습니다. 이를 위해서는 **지리적 복제** 부분을 클릭합니다.
	
	![SQL 데이터베이스에 대한 지리적 복제 설정][GeoReplication]

<a name="devfeatures"></a>
## 개발자 기능
웹 앱 모드에 따라 다음과 같은 개발자 지향 기능을 사용할 수 있습니다.

### 비트 수 ###

- **기본**, **표준** 및 **프리미엄** 모드는 64비트 및 32비트 응용 프로그램을 지원합니다.
- **무료** 및 **공유** 계획 모드는 32비트 응용 프로그램만 지원합니다.

### 디버거 지원 ###

- 디버거 지원은 앱 서비스 계획당 1건의 동시 연결에서 **무료**, **공유** 및 **기본** 모드에 대해 사용할 수 있습니다.
- 디버거 지원은 앱 서비스 계획당 5건의 동시 연결에서 **표준** 및 **프리미엄** 모드에 대해 사용할 수 있습니다.

<a name="OtherFeatures"></a>
## 기타 기능

### 웹 끝점 모니터링 ###

- 웹 끝점 모니터링은 **기본**, **표준** 및 **프리미엄** 모드에서 사용할 수 있습니다. 웹 끝점 모니터링에 대한 자세한 내용은 [웹 앱을 모니터링하는 방법](web-sites-monitor.md).을 참조하십시오.

- 개발자를 비롯하여 모든 사용자에게 유용한 가격 및 기능을 포함하여 앱 서비스 계획의 나머지 모든 기능에 대한 자세한 내용은 [앱 서비스 가격 세부 정보](/pricing/details/web-sites/).를 참조하세요.

>[AZURE.NOTE] Azure 계정을 등록하기 전에 Azure 앱 서비스를 시작하려면 [앱 서비스 사용](http://go.microsoft.com/fwlink/?LinkId=523751)으로 이동합니다. 앱 서비스에서는 단기 시작 웹 앱을 즉시 만들 수 있습니다. 신용 카드는 필요하지 않으며 약정도 필요하지 않습니다.

<a name="Next Steps"></a>	
## 다음 단계

- Azure를 시작하려면 [Microsoft Azure 무료 평가판](/pricing/free-trial/).을 참조하세요.
- 가격, 지원 및 SLA에 대한 자세한 내용은 다음 링크를 방문하세요.
	
	[데이터 전송 가격 정보](/pricing/details/data-transfers/)
	
	[Microsoft Azure 지원 계획](/support/plans/)
	
	[서비스 수준 계약](/support/legal/sla/)
	
	[SQL 데이터베이스 가격 정보](/pricing/details/sql-database/)
	
	[Microsoft Azure를 위한 가상 컴퓨터 및 클라우드 서비스 크기][vmsizes]
	
	[앱 서비스 가격 정보](/pricing/details/web-sites/)
	
	[앱 서비스 가격 정보 - SSL 연결](/pricing/details/web-sites/#ssl-connections)

- 확장 가능하며 복원력이 뛰어난 아키텍처 빌드를 포함하여 Azure 앱 서비스 모범 사례에 대한 자세한 내용은 [모범 사례: Azure 앱 서비스 웹 앱](http://blogs.msdn.com/b/windowsazure/archive/2014/02/10/best-practices-windows-azure-websites-waws.aspx)을 참조하세요.

- 웹 앱 크기 조정에 대한 비디오:
	
	- [Azure 웹 사이트 크기를 조정하는 방법 - 스테판 스차코우(Stefan Schackow)](/documentation/videos/azure-web-sites-free-vs-standard-scaling/)
	- [Azure 웹 사이트, CPU 또는 예약 리소스 크기 자동 조정 - 스테판 스차코우(Stefan Schackow)](/documentation/videos/auto-scaling-azure-web-sites/)
	- [Azure 웹 사이트 크기 조정 방법 - 스테판 스차코우(Stefan Schackow)](/documentation/videos/how-azure-web-sites-scale/)

## 변경 내용
* 웹 사이트에서 앱 서비스로의 변경에 대한 가이드는 다음을 참조: [Azure 앱 서비스 및 기존 Azure 서비스에 대한 영향](http://go.microsoft.com/fwlink/?LinkId=529714)
* 이전 포털에서 새 포털로의 변경에 대한 가이드는 다음을 참조: [미리 보기 포털 탐색에 대한 참조](http://go.microsoft.com/fwlink/?LinkId=529715)

<!-- LINKS -->
[vmsizes]:http://go.microsoft.com/fwlink/?LinkId=309169
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

<!--HONumber=49-->