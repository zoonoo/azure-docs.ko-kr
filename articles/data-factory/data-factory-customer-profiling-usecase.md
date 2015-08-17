<properties 
	pageTitle="사용 사례 - 고객 프로파일링" 
	description="Azure 데이터 팩터리로 데이터 기반 워크플로를(파이프라인) 만들어 게임 고객을 프로파일링하는 방법을 알아봅니다." 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/26/2015" 
	ms.author="spelluru"/>

# 사용 사례 - 고객 프로파일링

Azure Data Factory는 솔루션 가속기의 Cortana Analytics Suite를 구현하는 데 사용되는 다양한 서비스 중 하나입니다. Cortana Analytics에 대한 자세한 내용은 [Cortana Analytics Suite](http://www.microsoft.com/cortanaanalytics)를 방문하십시오. 이 문서에서는 Azure 데이터 팩터리가 어떻게 일반적인 분석 문제를 해결할 수 있는지를 이해하기 시작하는 데 도움이 되는 간단한 사용 사례를 설명합니다.

이 간단한 사용 사례에 엑세스하고 사용하려면 [Azure를 구독](https://azure.microsoft.com/pricing/free-trial/)하기만 하면 됩니다. [샘플](data-factory-samples.md) 문서에 설명된 단계를 수행하여 이 사용 사례를 구현하는 샘플을 배포할 수 있습니다.

## 시나리오

Contoso는 게임 콘솔, 핸드헬드 장치, PC(개인용 컴퓨터) 등 다양한 플랫폼용 게임을 만드는 게임 회사입니다. 사용자가 이러한 게임을 플레이하면 사용 패턴, 게임 스타일 및 사용자 기본 설정을 추적하는 많은 양의 로그 데이터가 생성됩니다. 인구 통계, 지역 및 상품 데이터가 결합되는 경우 Contoso는 분석을 수행하여 각 사용자의 환경을 향상하고 업그레이드 및 게임 내 구매를 특정하는 방법을 가이드합니다.

Contoso의 목표는 사용자의 게임 기록 프로필을 기반으로 상향 판매 및 교차 판매 기회를 식별하고 새롭게 매력적인 기능을 개발하여 비즈니스를 성장시키고 고객에게 더 나은 환경을 제공하는 것입니다. 이 사용 사례에서는 자신의 사용자 행동을 최적화하려는 게임 회사를 비즈니스 예로 설명합니다. 하지만 이러한 원칙은 상품 및 서비스에 고객을 참여시키고 고객의 환경을 개선하고자 하는 모든 비즈니스에 적용됩니다.

## 과제

게임 회사는 이러한 형식의 사용 사례를 구현하려고 할 때 다양한 과제를 해결해야 합니다. 먼저, 여러 데이터 원본에서 다양한 크기 및 모양의 데이터를 온-프레미스 및 클라우드 형태로 수집하고 제품 데이터, 고객 동작 기록 데이터, 사용자가 다양한 플랫폼에서 게임을 플레이한 사용자 데이터를 캡처해야 합니다. 둘째, 게임 사용 패턴을 합리적이고 정확하게 계산해야 합니다. 셋째, 게임 회사는 전체적인 상향 판매 및 교차 판매의 게임 내 구매 프로필 성공을 추적하여 이러한 접근법의 효율성을 측정하고 향후 마켓팅 캠페인을 조정해야 합니다.

## 솔루션 개요

이 간단한 사용 사례는 Azure 데이터 팩터리를 사용하여 데이터를 수집, 준비, 변환, 분석 및 게시하는 방법의 예로 사용할 수 있습니다.

![종단 간 워크플로](./media/data-factory-customer-profiling-usecase/EndToEndWorkflow.png) 위의 그림은 배포된 후 Azure 포털 UI에서 데이터 파이프라인을 표시하는 방법을 보여줍니다.

1.	**PartitionGameLogsPipeline**은 Blob 저장소에서 원시 게임 이벤트를 읽고 연도, 월 및 일을 기준으로 파티션을 만듭니다.
2.	**EnrichGameLogsPipeline**은 분할된 게임 이벤트를 지역 코드 참조 데이터와 조인하고 IP 주소를 해당하는 지리적 위치에 매핑하여 데이터를 강화합니다.
3.	**AnalyzeMarketingCampaignPipeline** 파이프라인은 강화된 데이터를 활용하고 이 데이터를 광고 데이터와 함께 처리하여 마케팅 캠페인 효과가 포함된 최종 출력을 만듭니다.

이 사용 사례에서 Azure 데이터 팩터리는 HDInsight 작업(Hive 및 Pig 변환)으로 입력 데이터의 복사, 변환 및 처리를 조정하고 Azure SQL 데이터베이스로 최종 데이터를 출력하는 데 사용됩니다. 또한 데이터 파이프라인의 네트워크를 시각화하고 관리하며 UI에서 상태를 모니터링할 수 있습니다.

## 이점

사용자 프로필 분석을 최적화하고 비즈니스 목표에 맞추어 게임 회사는 신속하게 사용 패턴을 수집하고 모든 다른 게임 제품에 대한 마케팅 캠페인의 효과를 분석할 수 있습니다.

<!---HONumber=August15_HO6-->