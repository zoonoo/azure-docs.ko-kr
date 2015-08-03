<properties
   pageTitle="클라우드 크루저 및 Microsoft Azure 청구 API 통합"
   description="경험으로 해당 제품에 Azure 청구 API를 통합하여 Microsoft Azure 청구 파트너 Cloud Cruiser에서 고유한 관점을 제공합니다. Microsoft Azure 팩용 Cloud Cruiser를 사용/시도하는 데 관심을 두는 Azure 및 Cloud Cruiser 고객에게 특히 유용합니다."
   services="billing"
   documentationCenter=""
   authors="BryanLa"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="billing"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="billing"
   ms.date="06/17/2015"
   ms.author="mobandyo;sirishap;bryanla"/>

# 클라우드 크루저 및 Microsoft Azure 청구 API 통합 

이 문서는 새로운 Microsoft Azure 청구 API로부터 수집한 정보를 Cloud Cruiser에서 워크플로 비용 시뮬레이션 및 분석에 사용할 수 있는 방법을 설명합니다.

## Azure RateCard API
RateCard API는 Azure에서 환율 정보를 제공합니다. 적절한 자격 증명을 인증한 후에 제품 ID를 제공하면 연결 속도와 함께 Azure에서 사용할 수 있는 서비스에 대 한 메타데이터를 수집하는 API를 쿼리할 수 있습니다.

다음은 A0(Windows) 인스턴스에 대한 가격을 표시하는 API의 샘플 응답입니다.

    {       
		"MeterId": "0e59ad56-03e5-4c3d-90d4-6670874d7e29",       
		"MeterName": "Compute Hours",       
		"MeterCategory": "Virtual Machines",       
		"MeterSubCategory": "A0 VM (Windows)",       
		"Unit": "Hours",       
		"MeterRates": 
		{         
			"0": 0.029       
		},       
		"EffectiveDate": "2014-08-01T00:00:00Z",       
		"IncludedQuantity": 0.0     
	}, 

## Azure RateCard API에 대한 클라우드 크루저의 인터페이스
다른 방법으로 클라우드 크루저가 RateCard API 정보를 활용할 수 있습니다. 이 문서에서 어떻게 IaaS를 워크로드 비용 시뮬레이션 및 분석을 만드는데 사용할 수 있는지 알아보겠습니다

이 사용 사례를 설명하기 위해 Microsoft Azure 팩(WAP)을 실행하는 여러 인스턴스의 워크로드를 가정합니다. Azure에서 이 같은 워크로드를 시뮬레이션하고 이러한 마이그레이션을 수행하는 비용을 추정하는 것이 목표입니다. 이 시뮬레이션을 만들기 위해 두 주요 작업을 수행합니다.

1. **RateCard API에서 수집한 서비스 정보 가져오기 및 프로세스** -이 작업은 또한 RateCard API에서 새 요금제로 변환되고 게시된 워크북에서 수행됩니다. 이 새 요금제는 Azure 가격을 추정하는 시뮬레이션에 사용됩니다.

2. **IaaS에 대한 WAP 서비스 및 Azure 서비스 표준화** - Azure 서비스가 인스턴스 크기에 기반한 반면(A0, A1, A2 등) WAP 서비스는 기본적으로 개별 리소스에 기반합니다.(CPU, 메모리 크기, 디스크 크기 등) 이 첫번째 작업은 Cloud Cruiser ETL 엔진이라는 호출된 워크북에 의해 수행되고 이러한 리소스를 Azure 인스턴스 서비스와 비슷한 인스턴스 크기로 묶을 수 있습니다.

## RateCard API에서 데이터 가져오기

클라우드 크루저 워크북은 RateCard API에서 정보를 수집하고 처리하는 자동화된 방법을 제공합니다. ETL(추출-변환-로드) 워크북을 사용하면 클라우드 크루저 데이터베이스에서 데이터를 수집, 변환 및 게시하도록 구성할 수 있습니다.

각 워크북은 하나 또는 여러 컬렉션을 가질 수 있습니다. 이를 사용하여 사용 데이터를 보완하거나 보강할 다른 소스에서 정보를 상호 연결할 수 있습니다. 다음 두 개의 스크린샷은, 기존 워크북에서 새 *컬렉션*을 만들고 RateCard API에서 *컬렉션*에 대한 정보를 가져오는 것을 보여줍니다.

![그림 1-새 컬렉션 만들기][1]

![그림 2-새 컬렉션에서 데이터 가져오기][2]

워크북에 데이터를 가져온 후 데이터를 수정하고 모델화하여 여러 단계 및 변환 프로세스를 만들 수 있습니다. 이 예제에서 infrastructure-as-a-Service (IaaS,)에 관심이 있으므로 IaaS 아닌 다른 서비스와 관련된 불필요한 행 또는 레코드를 제거하는 변환 단계를 사용합니다.

아래 스크린샷은 RateCard API에서 수집된 데이터를 처리하는 변환 단계를 보여줍니다.

![그림 3-RateCard API에서 수집된 데이터를 처리하는 변환 단계][3]

## 새로운 서비스 및 요금제 정의

Cloud Cruiser에서 서비스를 정의하는 여러 가지 방법이 있습니다. 옵션 중 하나는 사용 데이터에서 서비스를 가져오는 것입니다. 이 메서드는 공급자가 서비스를 이미 정의한 경우에 공용 클라우드로 작업할 때 일반적으로 사용됩니다.

요금제는 유효 날짜 또는 다른 옵션과 함께 고객의 그룹에 따라 다양한 서비스에 적용할 수 있는 가격 또는 요금의 집합입니다. 요금제는 Cloud Cruiser에서 시뮬레이션 또는 "가상 분석" 시나리오를 만드는 데 사용하여 서비스의 변경이 워크로드의 총비용에 주는 영향을 이해할 수 있습니다.

이 예제에서 Cloud Cruiser에서 새 서비스를 정의 하는 RateCard API의 서비스 정보를 사용합니다. 같은 방법으로 Cloud Cruiser에서 새 요금제를 만들어서 서비스와 연결된 요금을 사용할 수 있습니다.

변환 프로세스가 끝날 때 새 단계를 만들고 새 서비스 및 요금으로 RateCard API에서 데이터를 게시할 수 있습니다.

![그림 4-새 서비스 및 요금 RateCard API에 데이터 게시][4]

## Azure 서비스 및 요금 확인

서비스 및 요금을 게시한 후 Cloud Cruiser의 *서비스* 탭에서 가져온 서비스의 목록을 확인할 수 있습니다.

![그림 5-새로운 서비스 확인][5]

*요금제* 탭에서 RateCard API에서 가져온 새 요금으로 "AzureSimulation"이라 불리는 새 요금제를 확인할 수 있습니다.

![그림 6-새 요금제와 연결된 요금 확인][6]

## WAP 및 Azure 서비스 표준화

기본적으로 WAP은 계산, 메모리 및 네트워크 리소스의 사용에 따라 사용 정보를 제공합니다. Cloud Cruiser에서 할당 또는 이러한 리소스의 요금제 사용에 직접 기반하여 서비스를 정의할 수 있습니다 예를 들어 CPU 사용의 각 시간 당 기본 요금을 설정하거나 인스턴스에 할당된 메모리의 GB에 비용을 청구할 수 있습니다.

이 예제에서 WAP와 Azure 간의 비용을 비교하기 위해 WAP의 리소스 사용을 번들로 집계한 다음 Azure 서비스에 매핑할 수 있습니다. 이 변환은 워크북에서 쉽게 구현할 수 있습니다.

![그림 7-서비스를 정규화하는 WAP 데이터 변환][7]

워크북에서 마지막 단계는 Cloud Cruiser 데이터베이스로 데이터를 게시하는 것입니다. 이 단계 동안 사용 데이터는 이제 서비스에 번들되고(Azure 서비스에 매핑) 요금을 만들어 기본 요금을 연결됩니다.

워크북을 마친 후 스케줄러에서 새 작업을 추가 하고 실행할 워크북에 대한 시간 및 빈도를 지정하여 데이터의 처리를 자동화할 수 있습니다.

![그림 8- 워크북 예약][8]

## 워크로드 비용 시뮬레이션 분석에 대한 보고서 만들기

사용은 수집되고 요금은 Cloud Cruiser 데이터베이스에 로드되기 때문에 원하는 워크로드 비용 시뮬레이션을 만들기 위해 고급 분석 보고 도구인 Cloud Cruiser Insights 모듈을 활용할 수 있습니다.

이 시나리오를 설명하기 위해 다음 보고서를 만들었습니다.

![비용 비교][9]

위쪽 그래프는 서비스 별로 끊어진 비용 비교를 보여주며 WAP(진한 파랑) 및 Azure(옅은 파랑) 간의 각 특정 서비스용 워크로드를 실행하는 가격을 비교합니다.

아래쪽 그래프는 동일하지만 부서 별로 끊어진 데이터를 표시하며 각 부서에서 WAP 및 Azure에서 워크로드를 실행하는 비용과 이러한 두 비용 절감(녹색) 간의 차이와 함께 보여줍니다.

## 다음 단계

+ Cloud Cruiser 워크북 및 보고서를 만드는 방법에 대한 자세한 지침은 Cloud Cruiser 온라인 [설명서](http://docs.cloudcruiser.com/) (유효한 로그인 필요)를 참조하십시오. Cloud Cruiser에 대한 자세한 내용은 [info@cloudcruiser.com](mailto:info@cloudcruiser.com)에 문의하십시오.
+ Azure 리소스 사용 및 RateCard API에 대한 개요는 [Microsoft Azure 리소스 소비에 대한 통찰력 얻기](billing-usage-rate-card-overview.md)를 참조하십시오. 
+ 두 API에 대한 정보는 [Azure 청구 REST API 참조](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c)를 확인하십시오. 이는 Azure 리소스 관리자에서 제공하는 API 집합의 일부입니다.
+ 샘플 코드를 곧바로 시작려면 [Github의 Microsoft Azure 청구 API 코드 샘플](https://github.com/Azure/BillingCodeSamples)을 확인하십시오.

## 자세한 정보
+ Azure 리소스 관리자에 대한 자세한 내용은 [Azure 리소스 관리자 개요](resource-group-overview.md)를 참조하십시오.

<!--Image references-->
[1]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Create-New-Workbook-Collection.png "그림 1-새 컬렉션 만들기"
[2]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Import-Data-From-RateCard.png "그림 2-새 컬렉션에서 데이터 가져오기"
[3]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Transformation-Steps-Process-RateCard-Data.png "그림 3-RateCard API에서 수집된 데이터를 처리하는 변환 단계"
[4]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Publish-RateCard-Data-New-Services-Rates.png "그림 4-새 서비스 및 요금 RateCard API에 데이터 게시"
[5]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Verify-Azure-Services-And-Pricing1.png "그림 5-새로운 서비스 확인"
[6]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Verify-Azure-Services-And-Pricing2.png "그림 6-새 요금제와 연결된 요금 확인"
[7]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Transforming-WAP-Normalize-Services.png "그림 7-서비스를 정규화하는 WAP 데이터 변환"
[8]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Workbook-Scheduling.png "그림 8- 워크북 예약"
[9]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Workload-Cost-Simulation-Report.png "그림 9- 워크로드 비용 비교 시나리오에 대한 예제 보고서"

<!---HONumber=July15_HO4-->