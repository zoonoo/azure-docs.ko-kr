<properties 
    pageTitle="일반적인 DocumentDB 사용 사례 | Microsoft Azure" 
    description="DocumentDB의 5가지 상위 사용 사례인 사용자 생성 콘텐츠, 이벤트 로깅, 카탈로그 데이터, 사용자 기본 설정 데이터 및 IoT(사물 인터넷)에 대해 알아봅니다." 
    services="documentdb" 
    authors="h0n" 
    manager="jhubbard" 
    editor="monicar" 
    documentationCenter=""/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="07/10/2015" 
    ms.author="hawong"/>

# 일반적인 DocumentDB 사용 사례
이 문서에서는 DocumentDB의 몇 가지 일반적인 사용 사례를 간략하게 설명합니다. 이 문서의 권장 사항은 DocumentDB로 응용 프로그램을 개발할 때 시작점 역할을 합니다.

이 문서를 읽은 다음에는 다음과 같은 질문에 답할 수 있습니다.
 
- DocumentDB의 일반적인 사용 사례는 무엇인가요?
- DocumentDB를 사용자 생성 콘텐츠 저장소로 사용할 경우 어떤 이점이 있나요?
- DocumentDB를 카탈로그 데이터 저장소로 사용할 경우 어떤 이점이 있나요?
- DocumentDB를 이벤트 로그 저장소로 사용할 경우 어떤 이점이 있나요?
- DocumentDB를 사용자 기본 설정 데이터 저장소로 사용할 경우 어떤 이점이 있나요?
- DocumentDB를 IoT(사물 인터넷) 시스템의 데이터 저장소로 사용할 경우 어떤 이점이 있나요?

    
## 사용자 생성 콘텐츠
DocumentDB의 일반적인 사용 사례는 웹 및 모바일 응용 프로그램, 특히 소셜 미디어 응용 프로그램에 대한 UGC(사용자 생성 콘텐츠)를 저장 및 쿼리하는 것입니다. UGC의 몇 가지 예로 채팅 세션, 트윗, 블로그 게시물, 평가, 의견 등이 있습니다. 대개 소셜 미디어 응용 프로그램의 UGC는 고정된 구조로 제한되지 않는 자유 형식 텍스트, 속성, 태그 및 관계의 혼합입니다.

변환이나 복잡한 개체-관계형 매핑 계층을 요구하지 않고 채팅, 의견, 게시물 등의 콘텐츠를 DocumentDB에 저장할 수 있습니다. 개발자가 응용 프로그램 코드를 반복할 때 요구 사항에 맞게 데이터 속성을 쉽게 추가하거나 수정할 수 있으므로 신속한 개발이 가능합니다.

다양한 소셜 네트워크와 통합되는 응용 프로그램은 이러한 네트워크의 스키마 변화에 응답해야 합니다. DocumentDB에서는 기본적으로 데이터가 자동으로 인덱싱되므로 언제든지 데이터를 쿼리할 수 있습니다. 따라서 이러한 응용 프로그램이 해당 요구에 따라 유연하게 프로젝션을 검색할 수 있습니다.

많은 소셜 응용 프로그램은 글로벌 규모로 실행되며 예측할 수 없는 사용 패턴을 나타낼 수 있습니다. 사용량 수요에 따라 응용 프로그램 계층의 크기가 조정되므로 데이터 저장소의 크기를 조정하는 유연성이 필요합니다. DocumentDB 계정으로 데이터 파티션을 더 추가하여 규모를 확장할 수 있습니다. 또한 여러 지역에 추가 DocumentDB 계정을 만들 수도 있습니다. DocumentDB 서비스 지역 가용성은 [Azure 지역](http://azure.microsoft.com/regions/#services)을 참조하세요.

## 카탈로그 데이터
카탈로그 데이터 사용 시나리오에는 사용자, 장소 및 제품과 같은 엔터티의 특성 집합 저장 및 쿼리가 포함됩니다. 카탈로그 데이터의 몇 가지 예로 사용자 계정, 제품 카탈로그, IoT에 대한 장치 레지스트리, BOM 시스템 등이 있습니다. 이 데이터의 특성은 다양하며, 시간에 따라 응용 프로그램 요구 사항에 맞게 변경될 수 있습니다.

제품 카탈로그의 예로 자동차 부품 공급업체를 고려해 보세요. 각 부품에는 모든 부품이 공유하는 공통 특성 외에 고유한 특성이 있을 수 있습니다. 또한 특정 부품의 특성이 다음 연도에 새 모델이 출시될 때는 변경될 수 있습니다. JSON 문서 저장소로서, DocumentDB는 유연한 스키마를 지원하며 중첩 속성으로 데이터를 나타낼 수 있게 하므로 제품 카탈로그 데이터를 저장하는 데 적합합니다.

## 로그 데이터
응용 프로그램 로깅은 큰 볼륨으로 내보내지는 경우가 많으며, 배포된 응용 프로그램 버전 또는 구성 요소 로깅 이벤트를 기준으로 다른 특성을 가질 수 있습니다. 로그 데이터는 복잡한 관계나 고정된 구조로 제한되지 않습니다. JSON은 간단하고 쉽게 읽을 수 있기 때문에 로그 데이터가 JSON 형식으로 유지되는 경우가 점점 늘어나고 있습니다.
   
일반적으로 이벤트 로그 데이터와 관련된 두 가지 주요 사용 사례가 있습니다. 첫 번째 사용 사례는 문제 해결을 위해 데이터 하위 집합에 대한 임시 쿼리를 수행하는 것입니다. 문제 해결 중에 일반적으로 시계열로 데이터 하위 집합이 로그에서 먼저 검색됩니다. 그런 다음 오류 수준이나 오류 메시지가 포함된 데이터 집합을 필터링하여 드릴다운을 수행합니다. DocumentDB에 이벤트 로그를 저장할 경우의 이점은 바로 이 부분입니다. DocumentDB에 저장된 로그 데이터는 기본적으로 자동으로 인덱싱되므로 언제든지 쿼리할 수 있습니다. 또한 여러 데이터 파티션에 로그 데이터를 시계열로 유지할 수 있습니다. 오래된 로그는 보존 정책에 따라 콜드 저장소로 전달할 수 있습니다.

두 번째 사용 사례는 큰 볼륨의 로그 데이터에 대해 오프라인으로 수행되는 장기 실행 데이터 분석 작업과 관련이 있습니다. 이 사용 사례의 예로 서버 가용성 분석, 응용 프로그램 오류 분석, 클릭 동향 데이터 분석 등이 있습니다. 일반적으로 Hadoop을 사용하여 이러한 유형의 분석을 수행합니다. DocumentDB용 Hadoop 커넥터를 통해 DocumentDB 데이터베이스는 Pig, Hive 및 Map/Reduce 작업에 대한 데이터 원본 및 싱크로 작동합니다. DocumentDB용 Hadoop 커넥터에 대한 자세한 내용은 [DocumentDB 및 HDInsight를 사용하여 Hadoop 작업 실행](documentdb-run-hadoop-with-hdinsight/)을 참조하세요.

## 사용자 기본 설정 데이터
오늘날 대부분의 최신 웹 및 모바일 응용 프로그램은 복잡한 뷰와 환경으로 제공됩니다. 이러한 뷰와 환경은 대개 동적이며, 사용자 기본 설정이나 기분 및 브랜딩 요구에 맞게 조정됩니다. 따라서 응용 프로그램이 UI 요소와 환경을 신속하게 렌더링하려면 개인 설정을 효과적으로 검색할 수 있어야 합니다.

JSON은 간단할 뿐 아니라 JavaScript에서 쉽게 해석될 수 있으므로 UI 레이아웃 데이터를 나타내는 효과적인 형식입니다. DocumentDB는 빠른 읽기 및 대기 시간이 짧은 쓰기를 허용하는 조정 가능한 일관성 수준을 제공합니다. 따라서 개인 설정을 비롯한 UI 레이아웃 데이터를 DocumentDB에 JSON 문서로 저장하면 네트워크를 통해 이 데이터를 효과적으로 전달할 수 있습니다.

## 장치 센서 데이터
IoT 사용 사례는 일반적으로 데이터를 수집, 처리 및 저장하는 방법에서 일부 패턴을 공유합니다. 먼저, 이러한 시스템은 다양한 로캘의 장치 센서에서 데이터 버스트를 수집할 수 있는 데이터 유입을 허용합니다. 또한 이러한 시스템은 스트리밍 데이터를 처리 및 분석하여 실시간 통찰력을 갖게 합니다. 끝으로, 모두는 아니지만 대부분의 데이터가 임시 쿼리 및 오프라인 분석을 위해 결국 데이터 저장소에 저장됩니다.

Microsoft Azure는 IoT 사용 사례에 활용할 수 있는 다양한 서비스를 제공합니다. Azure IoT 서비스는 Azure 이벤트 허브, Azure DocumentDB, Azure 스트림 분석, Azure 알림 허브, Azure 기계 학습, Azure HDInsight 및 PowerBI를 포함하는 서비스 집합입니다.

Azure 이벤트 허브는 짧은 대기 시간으로 높은 처리량 데이터 수집을 제공하기 때문에 데이터 버스트를 수집할 수 있습니다. 실시간으로 처리해야 하는 수집된 데이터는 분석을 위해 Azure 스트림 분석으로 전달할 수 있습니다. 임시 쿼리를 위해 데이터를 DocumentDB에 로드할 수 있습니다. 데이터가 DocumentDB에 로드되고 나면 데이터를 쿼리할 수 있습니다. 실시간 분석의 일부로 DocumentDB의 데이터를 참조 데이터로 사용할 수 있습니다. 또한 Pig, Hive 또는 Map/Reduce 작업을 위해 DocumentDB 데이터를 HDInsight에 연결하여 데이터를 추가로 구체화하고 처리할 수 있습니다. 그런 다음 보고를 위해 구체화된 데이터가 DocumentDB에 다시 로드됩니다.

DocumentDB, EventHub 및 Storm을 사용한 샘플 IoT 솔루션은 [GitHub의 hdinsight-storm-examples 리포지토리](https://github.com/hdinsight/hdinsight-storm-examples/)를 참조하세요.

IoT용 Azure 제품에 대한 자세한 내용은 [사물 인터넷 만들기](http://www.microsoft.com/ko-KR/server-cloud/internet-of-things.aspx)를 참조하세요.

## 다음 단계
 
DocumentDB를 시작하기 위해 [계정](http://azure.microsoft.com/pricing/free-trial/)을 만든 다음 [학습 경로](https://azure.microsoft.com/documentation/learning-paths/documentdb/)에 따라 DocumentDB에 대해 알아보고 필요한 정보를 찾을 수 있습니다.

또는 DocumentDB를 사용하는 고객에 대한 추가 정보를 원하는 경우 다음 고객 사례를 확인할 수 있습니다.

- [Breeze](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18602). 선도적인 통합자가 유연한 클라우드 기술을 사용하여 수분 내에 다국적 기업에 글로벌 통찰력을 제공합니다.
- [News Republic](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18639). 뉴스에 인텔리전스를 추가하여 시민 참여를 위한 정보를 제공합니다. 
- [SGS International](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18653). 전 세계에서 일관된 색을 유지하기 위해 주요 브랜드가 SGS에 의존합니다. 그리고 SGS는 Azure에 의존합니다.
- [Telenor](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18608). 글로벌 리더인 Telenor는 클라우드를 사용하여 시작 속도로 이동합니다. 
- [XOMNI](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18667). 미래 저장소가 빠른 검색 및 쉬운 데이터 흐름으로 실행됩니다.
 

<!---HONumber=Oct15_HO3-->