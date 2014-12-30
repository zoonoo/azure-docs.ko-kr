<properties urlDisplayName="How to monitor" pageTitle="클라우드 서비스를 모니터링하는 방법 - Azure" metaKeywords="Azure monitoring cloud services, Azure Management Portal cloud services" description="Learn how to monitor cloud services by using the Azure Management Portal." metaCanonical="" services="cloud-services" documentationCenter="" title="How to Monitor Cloud Services" authors="raynew" solutions="" manager="johndaw" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/23/2014" ms.author="raynew" />


#클라우드 서비스를 모니터링하는 방법

[WACOM.INCLUDE [고지 사항](../includes/disclaimer.md)]

Azure 관리 포털에서 클라우드 서비스의 주요 성능 메트릭을 모니터링할 수 있습니다. 각 서비스 역할에 대해 모니터링 수준을 최소 및 자세히로 설정하고 모니터링 표시를 사용자 지정할 수 있습니다. 자세한 모니터링 데이터는 저장소 계정에 저장되며, 포털 외부에서 액세스할 수 있습니다. 

관리 포털의 모니터링 표시는 매우 다양하게 구성할 수 있습니다. **모니터** 페이지의 메트릭 목록에서 모니터링할 메트릭을 선택하고 **모니터** 페이지 및 대시보드에서 메트릭 차트에 넣을 메트릭을 선택할 수 있습니다. 

##목차##
* [개념](#concepts)
* [방법: 클라우드 서비스에 대한 모니터링 구성](#verbose)
* [방법: 클라우드 서비스 메트릭에 대한 경고 받기](#receivealerts)
* [방법: 메트릭 테이블에 메트릭 추가](#addmetrics)
* [방법: 메트릭 차트 사용자 지정](#customizechart)
* [방법: 관리 포털 외부에서 자세한 모니터링 데이터 액세스](#accessverbose)

<h2><a id="concepts"></a>개념</h2>

기본적으로 최소 모니터링은 역할 인스턴스(가상 컴퓨터)에 대해 호스트 운영 체제에서 수집된 성능 카운터를 사용하여 새 클라우드 서비스에 제공됩니다. 최소 메트릭은 CPU 비율, 데이터 입력, 데이터 출력, 디스크 읽기 처리량 및 디스크 쓰기 처리량으로 제한됩니다. 자세한 모니터링을 구성하면 가상 컴퓨터(역할 인스턴스) 내의 성능 데이터를 기반으로 추가 메트릭을 받을 수 있습니다. 자세한 메트릭을 사용하면 응용 프로그램 작동 중 발생하는 문제를 보다 자세히 분석할 수 있습니다.

> [WACOM.NOTE]
> 자세한 모니터링을 사용하는 경우 역할 인스턴스 시작 시 진단 구성 파일을 사용하여 성능 카운터를 더 추가할 수 있습니다. 관리 포털에서 이러한 메트릭을 모니터링하려면 자세한 모니터링을 구성하기 전에 성능 카운터를 추가해야 합니다. 자세한 내용은 <a href="http://azure.microsoft.com/ko-kr/documentation/articles/cloud-services-dotnet-diagnostics/">Azure 클라우드 서비스 및 가상 컴퓨터에서 진단 사용</a>을 참조하세요.

기본적으로 역할 인스턴스의 성능 카운터 데이터는 역할 인스턴스에서 3분 간격으로 샘플링되고 전송됩니다. 자세한 모니터링을 사용하는 경우 원시 성능 카운터 데이터가 각 역할 인스턴스 및 각 역할의 전체 역할 인스턴스에 대해 5분, 1시간 및 12시간 간격으로 집계됩니다. 집계된 데이터는 10일 후에 삭제됩니다.

자세한 모니터링을 사용하면 집계된 모니터링 데이터가 저장소 계정에 테이블로 저장됩니다. 역할에 자세한 모니터링을 사용하려면 저장소 계정에 연결되는 진단 연결 문자열을 구성해야 합니다. 역할에 따라 다른 저장소 계정을 사용할 수 있습니다.

자세한 모니터링을 사용하면 데이터 저장소, 데이터 전송 및 저장소 트랜잭션과 관련된 저장소 비용이 증가합니다. 최소 모니터링에는 저장소 계정이 필요하지 않습니다. 최소 모니터링 수준에서 노출되는 메트릭에 대한 데이터는 모니터링 수준을 자세히로 설정하는 경우에도 저장소 계정에 저장되지 않습니다.


<h2><a id="verbose"></a>방법: 클라우드 서비스에 대한 모니터링 구성</h2>

다음 절차를 사용하여 관리 포털에서 자세한 모니터링 또는 최소 모니터링을 구성합니다. 자세한 모니터링은 Azure 진단을 사용하도록 설정하고 Azure 진단에서 자세한 모니터링 데이터를 저장하는 저장소 계정에 액세스할 수 있도록 진단 연결 문자열을 구성한 다음에야 켤 수 있습니다.

###시작하기 전에###

- 모니터링 데이터를 저장할 저장소 계정을 만듭니다. 역할에 따라 다른 저장소 계정을 사용할 수 있습니다. 자세한 내용은 **저장소 계정**에 대한 도움말을 참조하거나 [저장소 계정을 만드는 방법](/ko-kr/manage/services/storage/how-to-create-a-storage-account/)(영문)을 참조하세요.


- 클라우드 서비스 역할에 대해 Azure 진단을 사용하도록 설정합니다. <br /><br />자세한 내용은 [Azure 클라우드 서비스 및 가상 컴퓨터에서 진단 사용](/ko-kr/documentation/articles/cloud-services-dotnet-diagnostics/)을 참조하세요.

관리 포털에서 Azure 진단이 자세한 모니터링 데이터를 저장하는 저장소 계정에 액세스하는 데 사용하는 진단 연결 문자열을 추가하거나 수정하고 모니터링 수준을 자세히나 최소로 설정할 수 있습니다. 자세한 모니터링에서는 데이터를 저장소 계정에 저장하므로 모니터링 수준을 자세히로 설정하기 전에 진단 연결 문자열을 구성해야 합니다.

###자세한 모니터링을 위해 진단 연결 문자열을 구성하려면###

1. 자세한 모니터링 데이터를 저장하는 데 사용할 저장소 계정의 저장소 액세스 키를 복사합니다. [Azure 관리 포털](https://manage.windowsazure.com/)에서 **저장소 계정** 페이지의 **키 관리**를 사용할 수 있습니다. 자세한 내용은 [클라우드 서비스를 관리하는 방법](/ko-kr/documentation/articles/cloud-services-how-to-manage/)을 참조하거나 **저장소 계정**에 대한 도움말을 참조하세요. 

2. **클라우드 서비스**를 엽니다. 그런 다음 대시보드를 열려면 구성하려는 클라우드 서비스의 이름을 클릭합니다.

3. **프로덕션** 또는 **스테이징**을 클릭하여 구성할 배포를 표시합니다.

4. **구성**을 클릭합니다.

	아래와 같이 **구성** 페이지 맨 위에서 **모니터링** 설정을 편집합니다. 클라우드 서비스에 Azure 진단을 사용하도록 설정하지 않은 경우 **수준** 옵션을 사용할 수 없습니다. 데이터 보존 정책은 변경할 수 없습니다. 클라우드 서비스에 대한 자세한 모니터링 데이터는 10일 동안 저장됩니다.

	![Monitoring options](./media/cloud-services-how-to-monitor/CloudServices_MonitoringOptions.png)

5. **진단 연결 문자열**에서 자세한 모니터링을 수행할 각 역할에 대한 진단 연결 문자열을 입력합니다.
	
	연결 문자열의 형식은 다음과 같습니다. 샘플은 기본 끝점을 사용하는 클라우드 서비스에 대한 것입니다. 연결 문자열을 업데이트하려면 사용할 저장소 계정에 유효한 저장소 계정 이름과 저장소 액세스 키를 입력합니다.
         
 	DefaultEndpointsProtocol=https;AccountName=StorageAccountName;AccountKey=StorageAccountKey  

6. **저장**을 클릭합니다.

자세한 모니터링을 켜고 있는 경우 서비스 역할에 대한 진단 연결 문자열을 구성한 후 다음 절차를 수행합니다. 


###모니터링 수준을 자세히 또는 최소로 변경하려면###

1. [관리 포털](https://manage.windowsazure.com/)에서 클라우드 서비스 배포에 대한 **구성** 페이지를 엽니다.

2. **수준**에서 **세부 정보 표시** 또는 **최소**를 클릭합니다. 

3. **저장**을 클릭합니다.

자세한 모니터링을 켜면 해당 시간 내에 관리 포털에 모니터링 데이터가 표시되기 시작합니다.

원시 성능 카운터 데이터 및 집계된 모니터링 데이터는 역할에 대한 배포 ID로 한정되는 테이블로 저장소 계정에 저장됩니다. 

<h2><a id="receivealerts"></a>방법: 클라우드 서비스 메트릭에 대한 경고 받기</h2>

클라우드 서비스 모니터링 메트릭을 기반으로 경고를 받을 수 있습니다. Azure 관리 포털의 **관리 서비스** 페이지에서 선택한 메트릭이 지정한 값에 도달하는 경우 경고를 트리거하는 규칙을 만들 수 있습니다. 또한 경고가 트리거되면 메일을 보내도록 선택할 수 있습니다. 자세한 내용은 [방법: Azure에서 경고 알림 받기 및 경고 규칙 관리](http://go.microsoft.com/fwlink/?LinkId=309356)(영문)를 참조하세요.

<h2><a id="addmetrics"></a>방법: 메트릭 테이블에 메트릭 추가</h2>

1. [관리 포털](http://manage.windowsazure.com/)에서 클라우드 서비스에 대한 **모니터** 페이지를 엽니다.

	기본적으로 메트릭 테이블에는 사용 가능한 메트릭의 하위 집합이 표시됩니다. 다음 그림에서는 역할 수준에서 집계된 데이터를 사용하여 Memory\Available MBytes 성능 카운터로 제한되는 클라우드 서비스에 대한 기본적인 자세한 메트릭을 보여 줍니다. **메트릭 추가**를 사용하여 관리 포털에서 모니터링할 추가 집계 및 역할 수준 메트릭을 선택합니다.

	![Verbose display](./media/cloud-services-how-to-monitor/CloudServices_DefaultVerboseDisplay.png)
 
2. 메트릭 테이블에 메트릭을 추가하려면

	a. 아래와 같이 **메트릭 추가**를 클릭하여 **메트릭 선택**을 엽니다.
	사용 가능한 첫 번째 메트릭이 확장되어 사용 가능한 옵션을 보여 줍니다. 각 메트릭에 대한 맨 위 옵션에는 모든 역할에 대해 집계된 모니터링 데이터가 표시됩니다. 또한 데이터를 표시할 개별 역할을 선택할 수도 있습니다.

	![Add metrics](./media/cloud-services-how-to-monitor/CloudServices_AddMetrics.png)


	b. 표시할 메트릭을 선택하려면 다음을 수행합니다.

	- 메트릭 옆에 있는 아래쪽 화살표를 클릭하여 모니터링 옵션을 확장합니다.
	- 표시할 각 모니터링 옵션에 대한 확인란을 선택합니다.

	메트릭 테이블에 최대 50개의 메트릭을 표시할 수 있습니다.

	<div class="dev-callout"> 
	<b>힌트</b> 
	<p>자세한 모니터링에서 메트릭 목록에는 수십 개의 메트릭이 포함되어 있습니다. 스크롤 막대를 표시하려면 대화 상자의 오른쪽을 가리킵니다. 목록을 필터링하려면 아래와 같이 검색 아이콘을 클릭하고 검색 상자에 텍스트를 입력합니다.</p> 
</div>
 
	![Add metrics search](./media/cloud-services-how-to-monitor/CloudServices_AddMetrics_Search.png)

3. 메트릭 선택을 마치면 확인(확인 표시)을 클릭합니다.

	아래와 같이 선택한 메트릭이 메트릭 테이블에 추가됩니다.

	![monitor metrics](./media/cloud-services-how-to-monitor/CloudServices_Monitor_UpdatedMetrics.png)

 
4. 메트릭 테이블에서 메트릭을 삭제하려면 메트릭을 클릭하여 선택한 다음 **메트릭 삭제**를 클릭합니다. 메트릭을 선택한 경우에만 **메트릭 삭제**가 표시됩니다.


<h2><a id="customizechart"></a>방법: 메트릭 차트 사용자 지정</h2>

1. 메트릭 테이블에서 메트릭 차트에 넣을 메트릭을 최대 6개까지 선택합니다. 메트릭을 선택하려면 왼쪽에 있는 확인란을 클릭합니다. 메트릭 차트에서 메트릭을 제거하려면 메트릭 테이블에서 해당 확인란을 선택 취소합니다.

	메트릭 테이블에서 메트릭을 선택하면 메트릭이 메트릭 차트에 추가됩니다. 좁은 화면에서는 화면에 맞지 않는 메트릭 헤더가 **n more** 드롭다운 목록에 포함됩니다.

 
2. 상대 값(각 메트릭에 대한 최종 값만)과 절대 값(Y축에 표시됨) 표시를 전환하려면 차트 맨 위에서 상대 또는 절대를 선택합니다.

	![Relative or Absolute](./media/cloud-services-how-to-monitor/CloudServices_Monitor_RelativeAbsolute.png)

3. 메트릭 차트가 표시되는 시간 범위를 변경하려면 차트 맨 위에서 1시간, 24시간 또는 7일을 선택합니다.

	![Monitor display period](./media/cloud-services-how-to-monitor/CloudServices_Monitor_DisplayPeriod.png)

	대시보드 메트릭 차트에서 메트릭을 넣는 방법은 다양합니다. 표준 메트릭 집합을 사용할 수 있으며, 메트릭 헤더를 선택하여 메트릭을 추가하거나 제거합니다.

###대시보드에서 메트릭 차트를 사용자 지정하려면###

1. 클라우드 서비스에 대한 대시보드를 엽니다.

2. 차트에서 메트릭을 추가하거나 제거합니다.

	- 새 메트릭을 넣으려면 차트 헤더에서 메트릭에 대한 확인란을 선택합니다. 좁은 화면에서는 ***n*??metrics** 옆에 있는 아래쪽 화살표를 클릭하여 차트 헤더 영역에 표시할 수 없는 메트릭을 넣습니다.

	- 차트에 그려진 메트릭을 삭제하려면 헤더 옆에 있는 확인란을 선택 취소합니다.

3. **상대** 및 **절대** 표시를 전환합니다.

4. 1시간, 24시간 또는 7일의 데이터를 선택하여 표시합니다.

<h2><a id="accessverbose"></a>방법: 관리 포털 외부에서 자세한 모니터링 데이터 액세스</h2>

자세한 모니터링 데이터는 각 영역에 대해 지정한 저장소 계정에 테이블로 저장됩니다. 각 클라우드 서비스 배포에서 역할에 대해 6개의 테이블이 만들어집니다. 각각(5분, 1시간 및 12시간)에 대해 두 개의 테이블이 만들어집니다. 이러한 테이블 중 하나에는 역할 수준 집계가 저장되고, 다른 테이블에는 역할 인스턴스에 대한 집계가 저장됩니다. 

테이블 이름의 형식은 다음과 같습니다.

	WAD*deploymentID*PT*aggregation_interval*[R|RI]Table

설명:

- *deploymentID*는 클라우드 서비스 배포에 할당된 GUID입니다.

- *aggregation_interval* = 5M, 1H 또는 12H

- 역할 수준 집계 = R

- 역할 인스턴스에 대한 집계 = RI

예를 들어 다음 테이블에는 1시간 간격으로 집계된 자세한 모니터링 데이터가 저장됩니다.

	WAD8b7c4233802442b494d0cc9eb9d8dd9fPT1HRTable (hourly aggregations for the role)

	WAD8b7c4233802442b494d0cc9eb9d8dd9fPT1HRITable (hourly aggregations for role instances)


<!--HONumber=35_1-->
