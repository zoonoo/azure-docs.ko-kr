---
title: 사용 사례 - 고객 프로파일링
description: Azure 데이터 팩터리로 데이터 기반 워크플로를(파이프라인) 만들어 게임 고객을 프로파일링하는 방법을 알아봅니다.
services: data-factory
documentationcenter: ''
author: spelluru
manager: jhubbard
editor: monicar

ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/06/2016
ms.author: spelluru

---
# 사용 사례 - 고객 프로파일링
Azure Data Factory는 솔루션 가속기의 Cortana Intelligence Suite를 구현하는 데 사용되는 다양한 서비스 중 하나입니다. Cortana Intelligence에 대한 자세한 내용은 [Cortana Intelligence Suite](http://www.microsoft.com/cortanaanalytics)를 참조하세요. 이 문서에서는 Azure 데이터 팩터리가 어떻게 일반적인 분석 문제를 해결할 수 있는지를 이해하기 시작하는 데 도움이 되는 간단한 사용 사례를 설명합니다.

이 간단한 사용 사례를 액세스하여 사용하려면 [Azure를 구독](https://azure.microsoft.com/pricing/free-trial/)하기만 하면 됩니다. [샘플](data-factory-samples.md) 문서에 설명된 단계를 수행하여 이 사용 사례를 구현하는 샘플을 배포할 수 있습니다.

## 시나리오
Contoso는 게임 콘솔, 핸드헬드 장치, PC(개인용 컴퓨터) 등 다양한 플랫폼용 게임을 만드는 게임 회사입니다. 플레이어가 이러한 게임을 플레이하면 사용 패턴, 게임 스타일 및 사용자 기본 설정을 추적하는 많은 양의 로그 데이터가 생성됩니다. 인구 통계, 지역 및 제품 데이터가 결합되는 경우 Contoso는 분석을 수행하여 플레이어의 환경을 개선하고 업그레이드 및 게임 내 구매를 특정화하는 방법을 안내합니다.

Contoso의 목표는 플레이어의 게임 기록을 기반으로 상향 판매/교차 판매 기회를 식별하고 매력적인 기능을 추가하여 비즈니스를 성장시키고 고객에게 더 나은 환경을 제공하는 것입니다. 이 사용 사례의 경우 비즈니스의 예로 게임 회사를 사용합니다. 이 회사에서는 플레이어의 동작에 따라 게임을 최적화하려고 합니다. 이러한 원칙은 상품 및 서비스에 고객을 참여시키고 고객의 환경을 개선하고자 하는 모든 비즈니스에 적용됩니다.

## 과제
## 솔루션 개요
이 간단한 사용 사례는 Azure 데이터 팩터리를 사용하여 데이터를 수집, 준비, 변환, 분석 및 게시하는 방법의 예로 사용할 수 있습니다.

![종단 간 워크플로](./media/data-factory-customer-profiling-usecase/EndToEndWorkflow.png)

이 그림은 배포된 후 Azure 포털에서 데이터 파이프라인을 표시하는 방법을 보여줍니다.

1. **PartitionGameLogsPipeline**은 Blob 저장소에서 원시 게임 이벤트를 읽고 연도, 월 및 일을 기준으로 파티션을 만듭니다.
2. **EnrichGameLogsPipeline**은 분할된 게임 이벤트를 지역 코드 참조 데이터와 조인하고 IP 주소를 해당하는 지리적 위치에 매핑하여 데이터를 강화합니다.
3. **AnalyzeMarketingCampaignPipeline** 파이프라인은 강화된 데이터를 사용하고 이 데이터를 광고 데이터와 함께 처리하여 마케팅 캠페인 효과가 포함된 최종 출력을 만듭니다.

이 예에서 Data Factory는 입력 데이터의 복사, 변환 및 처리를 조정하고 Azure SQL Database로 최종 데이터를 출력하는 데 사용됩니다. 또한 데이터 파이프라인의 네트워크를 시각화하고 관리하며 UI에서 상태를 모니터링할 수 있습니다.

## 이점
사용자 프로필 분석을 최적화하고 비즈니스 목표에 맞추어 게임 회사는 신속하게 사용 패턴을 수집하고 마케팅 캠페인의 효과를 분석할 수 있습니다.

<!---HONumber=AcomDC_0907_2016-->