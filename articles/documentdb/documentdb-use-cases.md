---
title: "Azure DocumentDB의 일반적인 사용 사례 및 NoSQL 시나리오 | Microsoft Docs"
description: "DocumentDB의 5가지 상위 사용 사례인 사용자 생성 콘텐츠, 이벤트 로깅, 카탈로그 데이터, 사용자 기본 설정 데이터 및 IoT(사물 인터넷)에 대해 알아봅니다."
services: documentdb
author: h0n
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: eca68a58-1a8c-4851-8cf8-6e4d2b889905
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/23/2017
ms.author: hawong
translationtype: Human Translation
ms.sourcegitcommit: 0bec803e4b49f3ae53f2cc3be6b9cb2d256fe5ea
ms.openlocfilehash: 9a0ba106b4c2aded8aaac673dfba3610bd4ca0fb
ms.lasthandoff: 03/24/2017


---

# <a name="common-documentdb-use-cases"></a>일반적인 DocumentDB 사용 사례
이 문서에서는 DocumentDB의 몇 가지 일반적인 사용 사례를 간략하게 설명합니다.  이 문서의 권장 사항은 DocumentDB로 응용 프로그램을 개발할 때 시작점 역할을 합니다.   

이 문서를 읽은 다음에는 다음과 같은 질문에 답할 수 있습니다. 

* DocumentDB의 일반적인 사용 사례는 무엇인가요?
* DocumentDB를 소매 응용 프로그램으로 사용할 경우 어떤 이점이 있나요?
* DocumentDB를 IoT(사물 인터넷) 시스템의 데이터 저장소로 사용할 경우 어떤 이점이 있나요?
* DocumentDB를 웹 및 모바일 응용 프로그램으로 사용할 경우 어떤 이점이 있나요?

## <a name="common-use-cases-for-documentdb"></a>DocumentDB의 일반적인 사용 사례
Azure DocumentDB는 광범위한 응용 프로그램 및 사용 사례에 사용되는 범용 NoSQL 데이터베이스입니다. 낮은 응답 시간(밀리초)이 필요하고 신속하게 크기를 조정해야 하는 응용 프로그램에 적합합니다. 고성능 응용 프로그램에 적합한 DocumentDB의 몇 가지 특성은 다음과 같습니다.

* DocumentDB는 기본적으로 고가용성 및 확장성을 위해 데이터를 분할합니다.
* DocumentDB에는 낮은 대기 시간의 응답 시간(밀리초)을 지원하는 SSD 지원 저장소가 있습니다.
* DocumentDB는 Eventual, Session 및 Bounded-Staleness와 같은 일관성 수준을 지원하므로 비용 대비 성능 비율이 낮습니다. 
* DocumentDB에는 저장소 및 처리량을 독립적으로 측정하는 유연한 데이터 친화적인 가격 책정 모델이 있습니다.
* DocumentDB의 예약된 처리량 모델을 사용하면 기본 하드웨어의 CPU/메모리/IOP 대신 읽기/쓰기 수로 예측할 수 있습니다.
* DocumentDB는 일별 요청 수가 수십억 개에 이르는 대량 요청 볼륨으로 확장되도록 디자인되었습니다.

이러한 특성은 낮은 응답 시간이 필요하고 방대한 양의 읽기 및 쓰기를 처리해야 하는 웹, 모바일, 게임 및 IoT 응용 프로그램에 유용합니다. 

## <a name="iot-and-telematics"></a>IoT 및 텔레매틱스
IoT 사용 사례는 일반적으로 데이터를 수집, 처리 및 저장하는 방법에서 일부 패턴을 공유합니다.  먼저 이러한 시스템이 다양한 로컬의 장치 센서에서 데이터 버스트를 수집해야 합니다. 다음으로 이러한 시스템이 스트리밍 데이터를 처리 및 분석하여 실시간 통찰력을 끌어냅니다. 그런 다음 배치 분석에 사용할 수 있도록 데이터를 콜드 저장소에 보관합니다. Microsoft Azure 제품은 Azure Event Hubs, Azure DocumentDB, Azure Stream Analytics, Azure Notification Hub, Azure Machine Learning, Azure HDInsight 및 PowerBI를 포함한 IoT 사용 사례에 적용할 수 있는 다양한 서비스를 제공합니다. 

![Azure DocumentDB IoT 참조 아키텍처](./media/documentdb-use-cases/documentdb-iot.png)

Azure 이벤트 허브는 짧은 대기 시간으로 높은 처리량 데이터 수집을 제공하기 때문에 데이터 버스트를 수집할 수 있습니다. 실시간으로 처리해야 하는 수집된 데이터는 분석을 위해 Azure Stream Analytics로 전달할 수 있습니다. 임시 쿼리를 위해 데이터를 DocumentDB에 로드할 수 있습니다. 데이터가 DocumentDB에 로드되고 나면 데이터를 쿼리할 수 있습니다.  실시간 분석의 일부로 DocumentDB의 데이터를 참조 데이터로 사용할 수 있습니다. 또한 Pig, Hive 또는 Map/Reduce 작업을 위해 DocumentDB 데이터를 HDInsight에 연결하여 데이터를 추가로 구체화하고 처리할 수 있습니다.  그런 다음 보고를 위해 구체화된 데이터가 DocumentDB에 다시 로드됩니다.   

DocumentDB, EventHub 및 Storm을 사용한 샘플 IoT 솔루션은 [GitHub의 hdinsight-storm-examples 리포지토리](https://github.com/hdinsight/hdinsight-storm-examples/)를 참조하세요.

IoT용 Azure 제품에 대한 자세한 내용은 [사물 인터넷 만들기](http://www.microsoft.com/en-us/server-cloud/internet-of-things.aspx)를 참조하세요. 

## <a name="retail-and-marketing"></a>소매 및 마케팅
DocumentDB는 카탈로그 데이터를 저장하기 위해 소매 업계에서 널리 사용됩니다. 카탈로그 데이터 사용 시나리오에는 사용자, 장소 및 제품과 같은 엔터티의 특성 집합 저장 및 쿼리가 포함됩니다.  카탈로그 데이터의 몇 가지 예로 사용자 계정, 제품 카탈로그, IoT에 대한 장치 레지스트리, BOM 시스템 등이 있습니다.  이 데이터의 특성은 다양하며, 시간에 따라 응용 프로그램 요구 사항에 맞게 변경될 수 있습니다.  

제품 카탈로그의 예로 자동차 부품 공급업체를 고려해 보세요. 각 부품에는 모든 부품이 공유하는 공통 특성 외에 고유한 특성이 있을 수 있습니다.  또한 특정 부품의 특성이 다음 연도에 새 모델이 출시될 때는 변경될 수 있습니다.  JSON 문서 저장소로써 DocumentDB는 유연한 스키마와 계층형 데이터를 지원하므로 제품 카탈로그 데이터를 저장하는 데 적합합니다.

![Azure DocumentDB 소매 카탈로그 참조 아키텍처](./media/documentdb-use-cases/documentdb-retail-catalog.png)

 뿐만 아니라 DocumentDB에 저장된 데이터는 Pig, Hive 또는 Map/Reduce 작업을 통해 빅 데이터 분석에 사용되는 HDInsight와 통합할 수 있습니다. DocumentDB용 Hadoop 커넥터에 대한 자세한 내용은 [DocumentDB 및 HDInsight를 사용하여 Hadoop 작업 실행](documentdb-run-hadoop-with-hdinsight.md)을 참조하세요.

 ![Azure DocumentDB 소매 주문 참조 아키텍처](./media/documentdb-use-cases/documentdb-retail-orders.png)
## <a name="gaming"></a>게임
데이터베이스 계층은 게임 응용 프로그램의 중요한 구성 요소입니다. 오늘날의 게임은 모바일/콘솔 클라이언트에서 그래픽 처리를 수행하지만 게임 내 통계, 소셜 미디어 통합 및 고득점 순위표와 같은 사용자 지정되고 개인 설정된 콘텐츠를 제공하기 위해 클라우드에 의존합니다. 게임은 몰입도 높은 게임 환경을 제공하기 위해 1밀리초의 읽기 및 쓰기 대기 시간을 요구하는 경우가 종종 있습니다. 게임 데이터베이스는 속도가 빨라야 하며 신규 게임 출시 및 기능 업데이트 동안 요청 속도의 대량 스파이크를 처리할 수 있어야 합니다.

DocumentDB는 [The Walking Dead: No Man's Land](https://azure.microsoft.com//blog/the-walking-dead-no-mans-land-game-soars-to-1-with-azure-documentdb/)([Next Games](http://www.nextgames.com/)) 및 [Halo 5: Guardians](https://azure.microsoft.com/blog/how-halo-5-guardians-implemented-social-gameplay-using-azure-documentdb/)와 같은 게임에서 사용됩니다. DocumentDB는 게임 개발자에게 다음과 같은 이점을 제공합니다.

* DocumentDB는 성능을 탄력적으로 확장하거나 축소할 수 있습니다. 따라서 게임에서 단일 API 호출을 통해 수십 명부터 수백만 명에 이르는 동시 게이머의 프로필 및 통계를 처리할 수 있습니다.
* DocumentDB는 게임 플레이 중 지연을 방지하기 위해 밀리초 단위의 읽기 및 쓰기를 지원합니다.
* DocumentDB의 자동 인덱싱은 여러 속성에 대한 실시간 필터링을 지원합니다. 예를 들어 내부 플레이어 ID로 플레이어를 찾거나 GameCenter, Facebook, Google ID 또는 플레이드의 길드 멤버 자격에 따른 쿼리로 플레이어를 찾을 수 있습니다. 이는 복잡한 인덱싱을 작성하거나 인프라를 분할하지 않고도 가능합니다.
* 게임 내 채팅 메시지, 플레이어 길드 멤버 자격, 완료된 챌린지, 고득점 순위표, 소셜 그래프 등의 소셜 기능을 유연한 스키마로 보다 쉽게 구현할 수 있습니다.
* DocumentDB는 최소한의 설치 및 관리 작업만 필요한 관리되는 PaaS(Platform-as-a-Service)이므로 신속한 반복이 가능하며 시장 출시 시간을 단축해 줍니다.

![Azure DocumentDB 게임 참조 아키텍처](./media/documentdb-use-cases/documentdb-gaming-architecture.png)

## <a name="web-and-mobile-applications"></a>웹 및 모바일 응용 프로그램
DocumentDB는 일반적으로 웹 및 모바일 응용 프로그램 내에서 사용되며 소셜 상호 작용을 모델링하는 데 특히 적합하며 타사 서비스와 통합하여 풍부한 개인 설정 환경을 구축합니다. DocumentDB SDK를 사용하면 인기 있는 [Xamarin 프레임워크](documentdb-mobile-apps-with-xamarin.md)를 사용하여 풍부한 iOS 및 Android 응용 프로그램을 빌드할 수 있습니다.  

### <a name="social-applications"></a>소셜 응용 프로그램
DocumentDB의 일반적인 사용 사례는 웹 및 모바일 응용 프로그램, 특히 소셜 미디어 응용 프로그램에 대한 UGC(사용자 생성 콘텐츠)를 저장 및 쿼리하는 것입니다. UGC의 몇 가지 예로 채팅 세션, 트윗, 블로그 게시물, 평가, 의견 등이 있습니다. 대개 소셜 미디어 응용 프로그램의 UGC는 고정된 구조로 제한되지 않는 자유 형식 텍스트, 속성, 태그 및 관계의 혼합입니다. 변환이나 복잡한 개체-관계형 매핑 계층을 요구하지 않고 채팅, 의견, 게시물 등의 콘텐츠를 DocumentDB에 저장할 수 있습니다.  개발자가 응용 프로그램 코드를 반복할 때 요구 사항에 맞게 데이터 속성을 쉽게 추가하거나 수정할 수 있으므로 신속한 개발이 가능합니다.  

타사 소셜 네트워크와 통합되는 응용 프로그램은 이러한 네트워크의 스키마 변화에 응답해야 합니다. DocumentDB에서는 기본적으로 데이터가 자동으로 인덱싱되므로 언제든지 데이터를 쿼리할 수 있습니다. 따라서 이러한 응용 프로그램이 해당 요구에 따라 유연하게 프로젝션을 검색할 수 있습니다.

많은 소셜 응용 프로그램은 글로벌 규모로 실행되며 예측할 수 없는 사용 패턴을 나타낼 수 있습니다. 사용량 수요에 따라 응용 프로그램 계층의 크기가 조정되므로 데이터 저장소의 크기를 조정하는 유연성이 필요합니다.  DocumentDB 계정으로 데이터 파티션을 더 추가하여 규모를 확장할 수 있습니다.  또한 여러 지역에 추가 DocumentDB 계정을 만들 수도 있습니다. DocumentDB 서비스 지역 가용성은 [Azure 지역](https://azure.microsoft.com/regions/#services)을 참조하세요.

![Azure DocumentDB 웹앱 참조 아키텍처](./media/documentdb-use-cases/documentdb-web.png)

### <a name="personalization"></a>개인 설정
오늘날 최신 응용 프로그램은 복잡한 뷰와 환경으로 제공됩니다. 이는 일반적으로 동적이며, 사용자 기본 설정이나 기분 및 브랜딩 요구에 맞게 조정됩니다. 따라서 응용 프로그램이 UI 요소와 환경을 신속하게 렌더링하려면 개인 설정을 효과적으로 검색할 수 있어야 합니다. 

JSON은 간단할 뿐 아니라 JavaScript에서 쉽게 해석될 수 있으므로 UI 레이아웃 데이터를 나타내는 효과적인 형식입니다. DocumentDB는 빠른 읽기 및 대기 시간이 짧은 쓰기를 허용하는 조정 가능한 일관성 수준을 제공합니다. 따라서 개인 설정을 비롯한 UI 레이아웃 데이터를 DocumentDB에 JSON 문서로 저장하면 네트워크를 통해 이 데이터를 효과적으로 전달할 수 있습니다.

## <a name="next-steps"></a>다음 단계
DocumentDB를 시작하기 위해 [계정](https://azure.microsoft.com/pricing/free-trial/)을 만든 다음 [학습 경로](https://azure.microsoft.com/documentation/learning-paths/documentdb/)에 따라 DocumentDB에 대해 알아보고 필요한 정보를 찾을 수 있습니다. 

또는 DocumentDB를 사용하는 고객에 대한 추가 정보를 원하는 경우 다음 고객 사례를 확인할 수 있습니다.

* [Sulekha.com](https://customers.microsoft.com/en-US/story/sulekha-uses-azure-documentdb-to-connect-customers-and-businesses-across-india). Sulekha는 Azure DocumentDB를 사용하여 인도의 고객과 기업을 연결합니다.
* [NewOrbit](https://customers.microsoft.com/en-US/story/neworbit-takes-flight-with-azure-documentdb). NewOrbit는 Azure DocumentDB를 사용하여 플라이트를 가져옵니다.
* [Affinio](https://customers.microsoft.com/en-US/doclink/affinio-switches-from-aws-to-azure-documentdb-to-harness-social-data-at-scale). Affinio는 대규모의 소셜 데이터를 사용하기 위해 AWS에서 Azure DocumentDB로 전환합니다.
* [Next Games](https://azure.microsoft.com//blog/the-walking-dead-no-mans-land-game-soars-to-1-with-azure-documentdb/). The Walking Dead: No Man's Land - Azure DocumentDB에서 지원하는 1위 게임
* [Halo](https://azure.microsoft.com/blog/how-halo-5-guardians-implemented-social-gameplay-using-azure-documentdb/). Halo 5에서 Azure DocumentDB를 사용하여 소셜 게임을 구현한 방식
* [Cortana 분석 갤러리](https://azure.microsoft.com/blog/cortana-analytics-gallery-a-scalable-community-site-built-on-azure-documentdb/). Cortana 분석 갤러리 - Azure DocumentDB를 기반으로 하는 확장 가능한 커뮤니티 사이트
* [Breeze](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18602). 선도적인 통합자가 유연한 클라우드 기술을 사용하여 수분 내에 다국적 기업에 글로벌 통찰력을 제공합니다.
* [News Republic](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18639). 뉴스에 인텔리전스를 추가하여 시민 참여를 위한 정보를 제공합니다. 
* [SGS International](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18653). 전 세계에서 일관된 색을 유지하기 위해 주요 브랜드가 SGS에 의존합니다. 그리고 SGS는 Azure에 의존합니다.
* [Telenor](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18608). 글로벌 리더인 Telenor는 클라우드를 사용하여 시작 속도로 이동합니다. 
* [XOMNI](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18667). 미래 저장소가 빠른 검색 및 쉬운 데이터 흐름으로 실행됩니다.
* [Nucleo](https://customers.microsoft.com/story/azure-based-software-platform-breaks-down-barriers-bet). Azure 기반 소프트웨어 플랫폼은 비즈니스와 고객 간의 장벽을 없앱니다.
* [Weka](https://customers.microsoft.com/story/weka-smart-fridge-improves-vaccine-management-so-more-people-can-be-protected-against-diseases). Weka Smart Fridge는 보다 많은 사람들을 질병으로부터 보호할 수 있도록 개선된 백신 관리 기능을 제공합니다.
* [Orange Tribes](https://customers.microsoft.com/en-US/story/theres-more-to-that-food-app-than-meets-the-eye-or-the-mouth). 눈과 입을 충족하는 훌륭한 음식 앱입니다.
* [Real Madrid](https://customers.microsoft.com/en-US/story/real-madrid-brings-the-stadium-closer-to-450-million-f). Real Madrid는 Microsoft 클라우드를 통해 전 세계 4억5천만 명에 이르는 팬들을 경기장에 끌어모읍니다.
* [Tuku](https://customers.microsoft.com/en-US/story/tuku-makes-car-buying-fun-with-help-from-azure-services). TUKU는 Azure 서비스의 도움으로 차를 구입하는 재미를 줍니다. 

