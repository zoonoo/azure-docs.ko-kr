<properties 
	pageTitle="스트림 분석 입력 만들기 | Microsoft Azure" 
	description="스트림 분석 솔루션에 대한 입력 소스에 연결하고 구성하는 방법을 알아봅니다."
	documentationCenter=""
	services="stream-analytics"
	authors="jeffstokes72" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="stream-analytics" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-services" 
	ms.date="08/05/2015" 
	ms.author="jeffstok"/>

# 스트림 분석 입력 만들기

## 스트림 분석 입력 이해
---
스트림 분석 입력은 데이터 원본에 대한 연결로 정의됩니다. 데이터가 해당 데이터 원본에 전송되면 스트림 분석 작업에서 사용되고 실시간으로 처리됩니다. 입력은 데이터 스트림 입력과 참조 데이터 입력의 두 가지 고유한 그룹으로 나뉩니다. 스트림 분석은 작업 구독 내부 및 외부의 입력 소스 이벤트 허브 및 Blob 저장소와 높은 수준으로 통합됩니다. 지원되는 데이터 형식은 Avro, CSV 및 JSON입니다.

## 데이터 스트림 입력
---
기본 수준에서 스트림 분석 작업 정의는 작업에서 사용되고 변환될 하나 이상의 데이터 스트림 입력 소스를 포함해야 합니다. Azure Blob 저장소 및 Azure 이벤트 허브는 데이터 스트림 입력 소스로 지원됩니다. Azure 이벤트 허브 입력 소스는 여러 장치와 서비스에서 이벤트 스트림을 수집하는 데 사용됩니다. 데이터 스트림의 예로 소셜 미디어 활동 피드, 주식 거래 정보 또는 센서 데이터 등이 포함될 수 있습니다.

또는 Azure Blob 저장소를 대량 데이터 수집을 위한 입력 소스로 사용할 수 있습니다. Azure Blob을 사용하는 경우 데이터가 휴지 상태이므로 스트림 분석에서 Blob에 포함된 모든 데이터의 타임스탬프를 Blob 자체의 생성 타임스탬프로 해석하는 것에 주의하세요. 즉, Blob의 레코드에 타임스탬프가 포함되고 TIMESTAMP BY 키워드가 사용되는 경우가 아니면 이렇게 해석됩니다.

## 참조 데이터 입력
---
스트림 분석은 참조 데이터라는 두 번째 유형의 입력 소스도 지원합니다. 이 데이터는 대체로 상관관계를 지정하고 조회를 수행하는 데 사용되는 보조 데이터로, 일반적으로 정적이며 자주 변경되지 않습니다. Azure Blob 저장소는 현재 유일하게 지원되는 참조 데이터용 입력 소스입니다. 참조 데이터 소스 Blob은 크기가 50MB로 제한됩니다.

입력 구성의 경로 패턴에 {date} 및 {time} 토큰을 사용하도록 지정하여 참조 데이터 새로 고침 지원을 사용하도록 설정할 수 있습니다. 작업은 UTC 표준 시간대를 사용하여 Blob 이름으로 인코딩된 날짜 및 시간에 기반한 해당 Blob을 로드합니다. 인코드된 날짜 및 시간을 포함하는 이 참조 데이터 Blob 시퀀스는 결과의 일관성을 보장하는 데 필요합니다. 예를 들어 처리 지연이 있고 Blob 파일을 다시 로드해야 하는 경우 파일이 수정되지 않고 동일한 위치에 있어야 합니다. 그렇지 않으면 다른 출력이 표시될 수 있습니다. 지원되는 유일한 시나리오는 미래 날짜 및 시간이 경로 패턴에 인코드된 새 Blob을 추가하는 것입니다.

예를 들어 작업에 날짜 형식이 "YYYY-MM-DD"이고 시간 형식이 "HH:mm"인 경로 패턴(예: /sample/{date}/{time}/products.csv)을 사용하여 포털에서 구성된 참조 입력이 있는 경우, 작업은 UTC 표준 시간대로 2015년 4월 16일 오후 5시 30분에 /sample/2015-04-16/17:30/products.csv라는 이름의 파일을 선택합니다.


> [AZURE.NOTE]현재 스트림 분석 작업은 시간이 Blob 이름에 인코드된 시간과 일치하는 경우에만 참조 Blob 새로 고침 데이터를 찾습니다. 예를 들어 작업은 UTC 표준 시간대로 2015년 4월 16일 오후 5시 30분과 오후 5:30:59.999999999 사이에 /sample/2015-04-16/17:30/products.csv를 찾습니다. 시계가 오후 5시 31분을 가리키면 /sample/2015-04-16/17:30/products.csv 찾기를 중지하고 /sample/2015-04-16/17:31/products.csv 찾기를 시작합니다.

작업이 시작되는 경우에만 이전 참조 데이터 Blob이 고려됩니다. 이때 작업은 값이 작업 시작 시간보다 이전이고 이름에 최신 날짜/시간이 인코드되어 있는 Blob(작업 시작 시간 전의 최신 참조 데이터 Blob)을 찾습니다. 이렇게 하면 작업을 시작할 때 비어 있지 않은 참조 데이터가 설정됩니다. 찾을 수 없는 경우 작업이 실패하고 사용자에 게 진단 알림이 표시됩니다.

    “Initializing input without a valid reference data blob for UTC time <job start time>.”

 
## 데이터 스트림 입력 만들기
---
데이터 스트림 입력을 만들려면 스트림 분석 작업의 **입력** 탭으로 이동한 다음 페이지 맨 아래에 있는 **입력 추가**를 클릭합니다.

![image1](./media/stream-analytics-connect-data-event-inputs/01-stream-analytics-create-inputs.png)

 데이터 스트림 입력을 만드는 경우 [**이벤트 허브**](Creating-an-Event-hub-input-data-stream) 또는 [**Blob 저장소**](Creating-a Blob-storage-input-data-stream)의 두 가지 선택 항목이 사용자에게 표시됩니다. 처리할 스트림 형식에 적합한 항목을 선택합니다.

![image2](./media/stream-analytics-connect-data-event-inputs/02-stream-analytics-create-inputs.png)

## 이벤트 허브 데이터 입력 스트림 만들기
---
### 이벤트 허브 개요
이벤트 허브는 확장성이 높은 이벤트 ingestor이며 일반적으로 스트림 분석 데이터 유입에 대한 가장 일반적인 방법입니다. 다양한 장치 및 서비스에서 이벤트 스트림을 수집하도록 설계됩니다. 실시간 분석을 위해 이벤트 허브 및 스트림 분석이 함께 종단 간 솔루션을 고객에게 제공합니다. 이벤트 허브를 사용하면 고객이 Azure에 이벤트를 실시간으로 공급할 수 있으며 스트림 분석 작업에서 실시간으로 처리할 수 있습니다. 예를 들어 고객은 웹 클릭, 센서 판독값, 온라인 로그 이벤트를 이벤트 허브에 보내고 스트림 분석 작업을 만들어 실시간 필터링, 집계 및 조인을 위해 이벤트 허브를 입력 데이터 스트림으로 사용할 수 있습니다. 이벤트 허브는 데이터 송신에도 사용할 수 있습니다. 스트림 분석 작업의 출력이 다른 스트리밍 작업의 입력이 되는 경우 이벤트 허브가 출력으로 사용될 수 있습니다. 이벤트 허브에 대한 자세한 내용은 [이벤트 허브](https://azure.microsoft.com/services/event-hubs/ "이벤트 허브(영문)") 설명서를 참조하세요.

### 소비자 그룹
각 스트림 분석 작업 입력마다 고유한 이벤트 허브 소비자 그룹이 있도록 구성해야 합니다. 작업에 자체 조인 또는 여러 입력이 포함된 경우 일부 입력이 둘 이상의 판독기 다운스트림에서 읽혀 단일 소비자 그룹의 총 판독기 수가 이벤트 허브의 소비자 그룹당 판독기 수 제한인 5개를 초과할 수 있습니다. 이 경우 추가 이벤트 허브를 통해 라우팅되는 여러 쿼리 및 중간 결과로 쿼리를 분석해야 합니다. 또한 이벤트 허브당 20개의 소비자 그룹으로 제한됩니다. 자세한 내용은 [이벤트 허브 프로그래밍 가이드](https://msdn.microsoft.com/library/azure/dn789972.aspx "이벤트 허브 프로그래밍 가이드")를 참조하세요.

## Azure 포털에서 이벤트 허브 입력을 만드는 방법의 예
---
다음은 이벤트 허브를 입력으로 구성하는 연습입니다. 이벤트 허브 입력을 사용하려면 사용자에게 이벤트 허브에 대해 수집된 다음 정보가 있어야 합니다.

1. 입력 별칭 - 작업 쿼리에서 참조되는 입력 별칭입니다.
2. 서비스 버스 네임스페이스 이름 
3. 이벤트 허브 이름
3. 이벤트 허브 정책 이름
4. 선택 사항: 이벤트 허브 소비자 그룹 이름
	- 이벤트 허브에서 데이터를 수집하는 소비자 그룹입니다. 지정되지 않은 경우, 스트림 분석 작업은 기본 소비자 그룹을 사용하여 이벤트 허브에서 데이터를 수집합니다. 각 스트림 분석 작업마다 고유한 소비자 그룹을 사용하는 것이 좋습니다.
5. 데이터 스트림에는 직렬화 형식(Avro, CSV, JSON)이 사용됩니다.

먼저 스트림 분석 작업의 입력 페이지에서 **ADD INPUT**을 선택합니다.

![image1](./media/stream-analytics-connect-data-event-inputs/01-stream-analytics-create-inputs.png)

그런 다음 이벤트 허브를 입력으로 선택합니다.

![image6](./media/stream-analytics-connect-data-event-inputs/06-stream-analytics-create-inputs.png)

아래와 같이 이벤트 허브에 대한 필드에 정보를 입력합니다.

![image7](./media/stream-analytics-connect-data-event-inputs/07-stream-analytics-create-inputs.png)

그런 다음 이벤트 직렬화 형식이 데이터 스트림에 올바른지 확인합니다.

![image8](./media/stream-analytics-connect-data-event-inputs/08-stream-analytics-create-inputs.png)

**완료**를 선택하면 이제 이벤트 허브 데이터 스트림 입력이 생성되었습니다.

## Blob 저장소 입력 데이터 스트림 만들기
---
클라우드에 저장할 구조화되지 않은 대량 데이터가 있는 시나리오에서 Blob 저장소는 비용 효과적이고 확장성 있는 솔루션을 제공합니다. 이 데이터는 일반적으로 '휴지 상태'의 데이터로 간주됩니다. Blob이 데이터 스트림 입력에 유용할 수 있는 한 가지 시나리오는 시스템에서 로그가 이미 캡처되었으며 구문 분석하여 의미 있는 데이터를 추출해야 하는 로그 분석입니다. 또 다른 예로 미사용 데이터 웨어하우징 데이터 분석을 들 수 있습니다. Blob 저장소에 대한 자세한 내용은 [Blob 저장소](http://azure.microsoft.com/services/storage/blobs/) 설명서를 참조하세요.

다음은 Blob 저장소를 입력으로 구성하는 연습입니다. Azure Blob 저장소 입력을 사용하려면 다음 정보가 필요합니다.

1. 입력 별칭 - 작업 쿼리에서 참조되는 입력 별칭입니다.
2. 저장소 계정이 스트리밍 작업과 다른 구독에 있는 경우 저장소 계정 이름과 저장소 계정 키가 필요합니다.
3. 컨테이너 이름
4. 파일 이름 접두사
5. 데이터에 사용되는 직렬화 형식(Avro, CSV, JSON)

스트림 분석 작업의 입력 탭에서 **ADD INPUT**을 클릭하고 기본 옵션인 **데이터 스트림**을 선택합니다. ![image1](./media/stream-analytics-connect-data-event-inputs/01-stream-analytics-create-inputs.png)

**Blob 저장소**를 선택합니다.

![image2](./media/stream-analytics-connect-data-event-inputs/02-stream-analytics-create-inputs.png)

아래와 같이 저장소 계정에 대한 필드에 정보를 입력합니다.

![image3](./media/stream-analytics-connect-data-event-inputs/03-stream-analytics-create-inputs.png)

> [AZURE.NOTE]고급 설정 구성 상자를 클릭하면 사용자 지정 경로에 판독값 Blob에 대한 경로 접두사 패턴을 지정할 수 있습니다. 이 필드를 지정하지 않으면 스트림 분석은 컨테이너의 모든 Blob을 읽습니다.

![image4](./media/stream-analytics-connect-data-event-inputs/04-stream-analytics-create-inputs.png)

데이터에 대한 올바른 직렬화 설정을 선택합니다. 옵션은 JSON, CSV 및 Avro입니다.

![image5](./media/stream-analytics-connect-data-event-inputs/05-stream-analytics-create-inputs.png)

**완료**를 선택하면 이제 Blob 저장소 데이터 스트림 입력이 생성되었습니다.

## Blob 저장소 참조 데이터 입력 만들기
---
참조 데이터 기능을 활용하기 위해 Blob 저장소를 활용할 수 있습니다.

다음은 Blob 저장소를 참조 데이터 입력으로 구성하는 연습입니다. 시작하려면 다음 정보가 필요합니다.

1. 입력 별칭 - 작업 쿼리에서 참조되는 입력 별칭입니다.
2. 저장소 계정이 스트리밍 작업과 다른 구독에 있는 경우 저장소 계정 이름과 저장소 계정 키가 필요합니다.
3. 컨테이너 이름
4. 파일 이름 접두사
5. 데이터에 사용되는 직렬화 형식(CSV, JSON)
6. 경로 패턴 지정된 컨테이너 내에서 Blob을 찾는 데 사용되는 파일 경로. 경로 내에서 {date} 및 {time}의 두 변수 인스턴스 중 하나 또는 둘 다를 지정할 수 있습니다.


스트림 분석 작업의 입력 탭에서 **ADD INPUT**을 클릭하고 기본 옵션인 **참조 데이터**를 선택합니다.

![image9](./media/stream-analytics-connect-data-event-inputs/09-stream-analytics-create-inputs.png)

아래와 같이 Blob 저장소 및 저장소 계정에 대한 필드에 정보를 입력합니다.

![image10](./media/stream-analytics-connect-data-event-inputs/10-stream-analytics-create-inputs.png)

아래로 스크롤하여 Blob이 포함된 경로 계층 구조에 대한 접두사 패턴과 날짜 및 시간 필드의 형식을 지정해야 합니다.

![image12](./media/stream-analytics-connect-data-event-inputs/12-stream-analytics-create-inputs.png)

데이터에 대한 올바른 직렬화 설정을 선택합니다. 옵션은 JSON, CSV 및 Avro입니다.

![image11](./media/stream-analytics-connect-data-event-inputs/11-stream-analytics-create-inputs.png)

**완료**를 선택하면 이제 Blob 저장소 참조 데이터 입력이 생성되었습니다.


## 도움말 보기
추가 지원이 필요할 경우 [Azure 스트림 분석 포럼](https://social.msdn.microsoft.com/Forums/ko-kr/home?forum=AzureStreamAnalytics)을 참조하세요.

## 다음 단계

- [Azure 스트림 분석 소개](stream-analytics-introduction.md)
- [Azure 스트림 분석 사용 시작](stream-analytics-get-started.md)
- [Azure 스트림 분석 작업 규모 지정](stream-analytics-scale-jobs.md)
- [Azure 스트림 분석 쿼리 언어 참조](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Azure 스트림 분석 관리 REST API 참조](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!---HONumber=August15_HO6-->