<properties
   pageTitle="Microsoft Azure 리소스 사용에 대한 통찰력 얻기 | Microsoft Azure"
   description="Azure 청구 사용량 및 RateCard API에 대한 개념 정보를 제공하며 이는 Azure 리소스 소비 및 추세에 대한 통찰력을 제공하는데 사용합니다."
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
   ms.date="11/02/2015"
   ms.author="mobandyo;bryanla"/>

# Microsoft Azure 리소스 소비에 대한 통찰력 얻기 

고객 및 파트너는 Azure 비용을 정확하게 예측하고 관리하는 기능이 필요합니다. Capex에서 Opex 모델로 이동하는 동안 쇼백 및 요금 환불을 수행하는 기능이 필요할 뿐만 아니라 특히 큰 클라우드 배포를 위한 모드 정확도 및 청구를 제공합니다.

이 문서에서 논의된 Azure 리소스 사용량 및 속도 카드 API는 Azure 리소스의 소비에 대한 새로운 통찰력을 가능하게 하여 이러한 요구를 설명합니다.

## Azure 리소스 사용량 및 RateCard API 소개 

Azure 리소스 사용량 및 RateCard API는 Azure 리소스 관리자가 노출한 API의 제품군의 일부로서 리소스 제공자로 구현됩니다.

### Azure 리소스 사용량 API(미리 보기)
고객 및 파트너는 Azure 리소스 사용량 API를 사용하여 예상된 Azure 사용량 데이터를 가져올 수 있습니다. 기능은 다음과 같습니다.
	
- **Azure 역할 기반 액세스 제어** -고객 및 파트너는 [Azure Preview 포털](https://portal.azure.com) 또는 [Azure PowerShell cmdlet](powershell-install-configure.md)을 통해 액세스 정책을 구성하여 사용자 또는 응용 프로그램이 구독의 사용 현황 데이터에 액세스를 가져오도록 지정할 수 있습니다. 호출자가 인증에 대한 표준 Azure Active Directory 토큰을 사용해야 합니다. 호출자가 특정 Azure 구독에 대한 사용 데이터에 액세스하려면 판독기, 소유자 또는 참가자 역할에 추가되어야 합니다.

- **매시간 또는 매일 집계** -호출자는 Azure 사용 데이터를 매시간 버킷 또는 매일 버킷으로 할지 지정할 수 있습니다. 기본값은 매일입니다.

- **제공된 인스턴스 메타데이터 (리소스 태그 포함)** – 정규화된 리소스 uri와 같은 인스턴스 수준 정보가 (/subscriptions/{subscription-id}/..), 리소스 그룹 정보 및 리소스 태그와 함께 응답으로 제공됩니다. 이는 고객을 명확하게 지원하고 태그가 청구 구간 같은 사용 사례 별로 사용을 프로그래밍 방식으로 할당합니다.

- **제공된 리소스 메타데이터** -측정기 이름, 측정기 범주, 측정기 하위 범주, 단위 및 하위 지역과 같은 리소스 세부 정보도 응답에 전달되어 호출자가 사용된 것을 더 잘 이해합니다. Azure 포털, Azure 사용 CSV, EA 청구 CSV 및 다른 공용 경험 을 통해 리소스 메타데이터 용어를 정렬하기 위해 노력하여 경험을 통해 고객이 데이터를 연결할 수 있도록 합니다.

- **모든 사용 유형에 대한 사용** – 종량제, MSDN, 약정 금액, 화폐성 크레딧 및 EA를 포함하는 모든 형식에 대한 사용 데이터에 액세스할 수 있습니다.

### Azure 리소스 RateCard API(미리 보기)
고객 및 파트너는 Azure 리소스 RateCard API를 사용하여 각 예상 가격 정보와 함께 사용 가능한 Azure 리소스의 목록을 가져옵니다. 기능은 다음과 같습니다.

- **Azure 역할 기반 액세스 제어** -고객 및 파트너는 [Azure Preview 포털](https://portal.azure.com) 또는 [Azure PowerShell cmdlet](powershell-install-configure.md)를 통해 액세스 정책을 구성하여 사용자 또는 응용 프로그램이 RateCard 데이터에 액세스를 얻도록 지정할 수 있습니다. 호출자가 인증에 대한 표준 Azure Active Directory 토큰을 사용해야 합니다. 호출자가 특정 Azure 구독에 대한 사용 데이터에 액세스하려면 판독기, 소유자 또는 참가자 역할에 추가되어야 합니다.
	
- **종량제, MSDN, 금액 약정 및 금액 크레딧 제공(EA은 지원되지 않음)** - 이 API는 Azure 제공 수준 환율 정보 및 구독 수준을 제공합니다. API의 호출자는 제안 정보를 전달하여 리소스 세부 정보 및 속도를 가져와야 합니다. EA는 등록 당 사용자 지정된 속도를 제공하기 때문에 이번에는 EA 속도를 제공할 수 없습니다.

## 시나리오

다음은 사용 및 RateCard Api의 조합을 사용하여 가능하게 된 일부 시나리오입니다.

- **해당월 Azure 소비** -고객은 사용 및 RateCard를 조합하여 사용 및 요금 예측량의 매시간 및 매일 버킷을 분석함으로써 해당월 동안 클라우드 소비에 대한 높은 통찰력을 얻을 수 있습니다. 

- **경고 설정** – 고객 및 파트너는 사용과 RateCard API를 사용하여 예상되는 사용량 및 요금 예측량을 가져옴으로써 클라우드 소비량에 대한 리소스 기반 또는 통화 기반 경고를 설정합니다.

- **예측 청구** – 고객 및 파트너는 예상된 소비량 및 클라우드 소비량을 얻을 수 있으며 청구 주기가 끝날 때 청구를 예측할 기계 학습 알고리즘을 적용합니다.

- **사전 소비 비용 분석** – 고객은 RateCard API 사용하여 원하는 사용 수치를 제공함으로써 워크로드를 Azure에 이동하면 얼마가 청구되는지 예측할 수 있습니다. 고객이 다른 클라우드 또는 사설 클라우드에 기존 워크로드를 가진 경우 Azure 속도로 사용을 매핑하여 예상된 Azure 소비를 더 잘 예측할 수 있습니다. 청구 파트너가 제품을 피벗하고 약정 금액 및 금액 크레딧을 포함하여 종량제를 넘어선 다른 제품 유형 간에 비교/대조하는 기능을 제공하는 한편 이것은 [Azure 가격 계산기](http://azure.microsoft.com/pricing/calculator/)를 통해 확인할 정보 보기를 향상시킵니다. 또한 API는 지역별로 비용 측정 변경을 수행하는 기능을 제공합니다. 이는 전세계에 서로 다른 도시에서 리소스를 배포하면 총비용에 직접적인 영향을 줄 수 있으므로 배포를 결정하는 데 필요한 가상 분석 유형을 사용합니다.

- **가상 분석** -

	- 고객 및 파트너는 해당 워크로드를 Azure 리소스의 다른 하위 지역에서 실행하는 것이 비용 효율적인지 또는 다른 구성에서 실행하는 것이 비용 효율적인지 여부를 확인할 수 있습니다. Azure 리소스 비용은 실행 중인 Azure 하위 지역을 기준으로 다를 수 있으며 이를 통해 고객 및 파트너가 비용을 최적화할 수 있습니다.

	- 고객 및 파트너는 다른 Azure 제안 형식이 Azure 리소스에 더 나은 속도를 제공하는지 결정할 수도 있습니다.

## 파트너 솔루션

[고객에게 ITFM를 제공하는 Microsoft Azure 사용량 및 RateCard APIs 사용 가능한 Cloudyn](billing-usage-rate-card-partner-solution-cloudyn.md)는 Azure 청구 API 파트너 [Cloudyn](https://www.cloudyn.com/microsoft-azure/)에서 제공하는 통합 환경을 설명합니다. 이 문서에서는 Azure 고객에게 Azure 청구 API용 Cloudyn 사용 방법을 보여주어 Azure 소비 데이터에서 통찰력을 얻을 수 있는 짧은 비디오를 포함하여 경험을 자세하게 제공합니다.

[Cloud Cruiser 및 Microsoft Azure 청구 API 통합](billing-usage-rate-card-partner-solution-cloudcruiser.md)은 고객이 단일 사용자 인터페이스에서 Microsoft Azure 개인 또는 호스팅된 공용 클라우드의 운영 및 재무 측면을 완벽하게 관리할 수 있도록 [Azure Pack용 Cloud Cruiser의 Express](http://www.cloudcruiser.com/partners/microsoft/)가 WAP 포털에서 어떻게 직접적으로 작동하는지를 설명합니다.

## 다음 단계
+ 두 API에 대한 자세한 내용은 [Azure 청구 REST API 참조](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c)를 확인하십시오. 이는 Azure 리소스 관리자에서 제공하는 API 집합입니다.
+ 샘플 코드를 곧바로 시작하려면 [Azure 코드 샘플](https://azure.microsoft.com/documentation/samples/?term=billing)의 Microsoft Azure 청구 API 코드 샘플을 확인하세요.

## 자세한 정보
+ Azure 리소스 관리자에 대한 자세한 내용은 [Azure 리소스 관리자 개요](resource-group-overview.md)를 참조하십시오.
+ 클라우드 지출을 이해하는 데 필요한 도구 모음에 대한 추가 정보는 Gartner 문서 [IT 재무 관리(ITFM) 도구에 대한 시장 가이드](http://www.gartner.com/technology/reprints.do?id=1-212F7AL&ct=140909&st=sb)를 참조하십시오.

<!---HONumber=Nov15_HO2-->