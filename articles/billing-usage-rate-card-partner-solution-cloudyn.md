<properties
   pageTitle="Microsoft Azure 사용량 및 RateCard API로 Cloudyn을 사용하여 고객에 ITFM 제공 | Microsoft Azure"
   description="경험으로 해당 제품에 Azure 청구 API를 통합하여 Microsoft Azure 청구 파트너 Cloudyn에서 고유한 관점을 제공합니다. Azure 서비스용 Cloudyn를 사용/시도하는 데 관심을 두는 Azure 및 Cloudyn 고객에게 특히 유용합니다."
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
   ms.date="02/19/2016"
   ms.author="mobandyo;bryanla"/>

# 고객용 ITFM를 제공하여 Microsoft Azure 사용 및 RateCard API 사용 Cloudyn 

Cloudyn, Microsoft 개발 파트너 및 클라우드 관리 기능의 선두 주자는 새로운 Microsoft Azure 리소스 사용 및 RateCard API의 비공개 미리 보기 버전으로 선택되었습니다. 사용 API는 구독에 대한 예상된 Azure 소비 데이터에 액세스를 제공합니다. RateCard API는 비-엔터프라이즈 규약(EA) 고객에 대한 모든 Azure 서비스의 전체 가격 정보를 제공합니다. 함께 통합되어 이러한 API는 Cloudyn에서 제공하는 것과 같이 IT 재무 관리(ITFM) 도구에 입력하기 위한 전체 정보 기초를 제공합니다.

## 소개 

RateCard API의 데이터와 사용 API에서 데이터의 소위 "multiplication"(사용 [단위] [$unit] 가격 = 자세한 사용 현황 및 비용)는 오늘날 Azure에서 사용 가능한 세분화되고 정확하며 신뢰할 수 있는 청구 정보를 만듭니다.

![ITFM 개요][1]

이런 API를 소비하면 고객의 사용 및 비용에 대한 주요 정보를 제공합니다. 또한 Cloudyn이 간단한 프로그래밍 방식으로 고객 계정을 분석하고 고객에 대해 다양한 ITFM 작업을 수행할 수 있도록 합니다.

## RateCard 및 사용 API와 Cloudyn 통합
RateCard API는 지역 정보, 통화 및 로컬과 같은 여러 입력된 매개 변수가 필요하지만 가장 중요한 것은 OfferDurableID로 사용하는 고객에게 제공하는 Azure의 유형을 지정합니다.(종량제, 레거시 6 및 12개월 약정 계획, MSDN 제공, MPN 제공, 프로모션 제공 및 기타) OfferDurableID는 지정된 구독에 대한 "Offer ID" 아래 [Azure 사용 및 대금 청구 포털](https://account.windowsazure.com/Subscriptions)에서 찾을 수 있습니다.

[Azure용 Cloudyn](https://www.cloudyn.com/microsoft-azure/) 서비스에 등록하여 고객은 Cloudyn RateCard API를 통해 관련 가격 정보를 가져올 수 있는 OfferDurableID 코드를 추가할 수 있습니다. 다른 유형의 제품에 대한 정보는 [Microsoft Azure 제품 세부 정보](https://azure.microsoft.com/support/legal/offer-details/) 페이지에서 찾을 수 있습니다.

![Cloudyn ITFM 엔진 개요][2]

Azure 성능 API 외에도 Cloudyn은 사용 및 RateCard API를 모두 사용하여 시각화, 분석, 경고, 보고, 비용 관리 및 실행 가능한 권장 사항의 추가 계층을 만들고 Azure 고객이 신뢰할 수 있는 엔터프라이즈 클라우드 ITFM 도구를 제공합니다.

## 사용 및 RateCard API 통합이 사용하도록 설정한 Cloudyn ITFM 사용 사례 
사용 및 RateCard API가 사용하도록 설정한 일반적인 Cloudyn ITFM 사용 사례는 다음과 같습니다.

+ **비용 분석** -클라우드 비용이 모든 네이티브 식별 차원(공급자, 서비스, 계정, 하위 지역 등)으로 손상 될 수 있습니다. Azure 사용 및 RateCard API는 계정 당 사용 및 비용 데이터의 세분화된 손상을 제공하여 이를 쉬운 일로 만들며 이것을 Cloudyn이 그룹화 및 필터링하고 사용자에게 그래픽 또는 테이블 양식으로 나타냅니다.

![비용 분석 원형 차트][3]

+ **비용 할당 360** -재무 및 IT 관리자를 사용하여 클라우드 배포의 실제 비용 분석, 드라이버 및 추세를 발견할 수 있습니다. 추가로 클라우드 비용에 대한 전례 없는 통찰을 제공하고 엔터프라이즈 환불 및 쇼백을 촉진하여 관리자가 비즈니스 부서, 부서, 하위 지역 등과 배포 비용을 쉽게 연결할 수 있습니다. Azure 사용 및 RateCard API는 Cloudyn의 비용 할당 엔진에 입력으로 사용되어 태그되지 않거나 태그할 수 없도록 할당된 메서드 및 비즈니스 논리를 정의하여 API를 보완합니다.

![비용 할당 360 차트][4]

+ **비용 효율적인 크기 조정** - 활용률이 낮은 가상 컴퓨터에 최적화 크기 권장 사항을 제공하여 크기 제한을 초과하거나 과도하게 프로비전된 컴퓨터에서 고객의 비용을 줄입니다. 가상 컴퓨터 CPU 및 RAM 메트릭(성능 API을 통해), 실행 시간(사용 API을 통해) 및 비용(RateCard API를 통해)을 검사하여 수행합니다. 그런 다음 Cloudyn가 활용률이 낮은 CPU 또는 RAM 리소스(성능)에 따라 최적화 크기 권장 사항을 제공하고 활용률이 낮은 컴퓨터의 실제 시간-활용(사용)이 VM 간의 가격 델타(RateCard)를 곱하여 예상된 비용 절감을 계산합니다. 

![비용 효율적인 크기 조정][5]

+ **클라우드 포팅 권장 사항** -클라우드 포팅에서 재무 조언을 제공합니다. 주요 클라우드 공급 업체에 배포되는 클라우드 리소스의 사용자 현재 비용을 검사하고 Azure에서 해당하는 배포의 비용과 비교합니다. 그런 다음 Azure에 대해 세분화되고, 각 리소스, 재정에 기반한 포팅 권장 사항을 제공합니다. Azure에 필요한 해당 배포를 파악한 후 (성능 메트릭 및 사용자 기본 설정에 따라) Cloudyn이 RateCard API를 사용하여 Azure에서 동등한 배포의 비용을 평가합니다.

+ **성능 보고서** - Azure의 성능 API에서 사용하도록 설정된 이러한 보고서는 CPU 및 RAM 사용률에서 최적화 권장 사항으로 기능을 배열합니다. 아래는 평균 CPU 사용률로 인스턴스 분석 결과를 나타낸 인스턴스 사용률 보고서 예제입니다.

![성능 보고서][6]

+ **범주 관리자** -Cloudyn에서 주문을 구성되지 않은 클라우드 리소스로 가져오는 강력한 기능입니다. 비즈니스 사례와 연관된 효과적인 측정 및 보고를 위해 고유한 범주(태그)를 만들 자유를 사용자에게 제공합니다. 또한 사용자는 일치하지 않는 태그를 쉽게 규제및 분류하고(예: 입력 오류 및 기타 불일치) 정확한 비용 특성에 대한 태그되지 않은 리소스를 자동으로 검색합니다.

![범주 관리자][7]

## 비디오 

여기서는 짧은 비디오를 통해 Azure 고객에게 Azure 및 Azure 청구 Api용 Cloudyn 사용 방법을 보여주어 Azure 소비 데이터에서 통찰력을 얻을 수 있습니다.
 
> [AZURE.VIDEO cloudyn-provides-cloud-itfm-tools-via-microsoft-azure-apis]


## 다음 단계

+ 무료 [Azure용 Cloudyn](https://www.cloudyn.com/microsoft-azure/) 평가판을 시작하여 Microsoft Azure 클라우드 배포에서 사용자 지정 보고 및 분석과 함께 비용 투명성을 얻을 수 있는 방법을 참조합니다.
+ Azure 리소스 사용 및 RateCard API에 대한 개요는 [Microsoft Azure 리소스 소비에 대한 통찰력 얻기](billing-usage-rate-card-overview.md)를 참조하십시오. 
+ 두 API에 대한 정보는 [Azure 청구 REST API 참조](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c)를 확인하십시오. 이는 Azure 리소스 관리자에서 제공하는 API 집합의 일부입니다.
+ 샘플 코드를 곧바로 시작하려면 [Azure 코드 샘플](https://azure.microsoft.com/documentation/samples/?term=billing)의 Microsoft Azure 청구 API 코드 샘플을 확인하세요.

## 자세한 정보
+ Microsoft Azure 엔터프라이즈 규약(EA) 제공에 대한 자세한 내용을 보려면 [엔터프라이즈용 Azure 라이선스](https://azure.microsoft.com/pricing/enterprise-agreement/)를 방문하십시오
+ Azure 리소스 관리자에 대한 자세한 내용은 [Azure 리소스 관리자 개요](resource-group-overview.md)를 참조하십시오.
+ 클라우드 지출을 이해하는 데 필요한 도구 모음에 대한 추가 정보는 Gartner 문서 [IT 재무 관리(ITFM) 도구에 대한 시장 가이드](http://www.gartner.com/technology/reprints.do?id=1-212F7AL&ct=140909&st=sb)를 참조하십시오.

<!--Image references-->
[1]: ./media/billing-usage-rate-card-partner-solution-cloudyn/Cloudyn-ITFM-Overview.png
[2]: ./media/billing-usage-rate-card-partner-solution-cloudyn/Cloudyn-ITFM-Engine-Overview.png
[3]: ./media/billing-usage-rate-card-partner-solution-cloudyn/Cloudyn-Cost-Analysis-Pie-Chart.png
[4]: ./media/billing-usage-rate-card-partner-solution-cloudyn/Cloudyn-Cost-Allocation-360-Chart.png
[5]: ./media/billing-usage-rate-card-partner-solution-cloudyn/Cloudyn-Cost-Effective-Sizing.png
[6]: ./media/billing-usage-rate-card-partner-solution-cloudyn/Cloudyn-Performance-Reports.png
[7]: ./media/billing-usage-rate-card-partner-solution-cloudyn/Cloudyn-Category-Manager.png

<!---HONumber=AcomDC_0302_2016-->