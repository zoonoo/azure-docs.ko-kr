<properties 
	pageTitle="스트림 분석 입력 연결 | Microsoft Azure" 
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
	ms.date="11/23/2015" 
	ms.author="jeffstok"/>

# 스트림 분석 입력 소스 연결

## 스트림 분석 입력 이해
---
스트림 분석 입력은 데이터 원본에 대한 연결로 정의됩니다. 스트림 분석은 작업 구독 내부 및 외부의 Azure 소스 이벤트 허브 및 Blob 저장소와 높은 수준으로 통합됩니다. 데이터가 해당 데이터 원본에 전송되면 스트림 분석 작업에서 사용되고 실시간으로 처리됩니다. 입력은 데이터 스트림 입력과 참조 데이터 입력의 두 가지 고유한 형식으로 나뉩니다.

## 데이터 스트림 입력
---
스트림 분석 작업은 작업에서 사용되고 변환될 하나 이상의 데이터 스트림 입력을 포함해야 합니다. Azure Blob 저장소 및 Azure 이벤트 허브는 데이터 스트림 입력 소스로 지원됩니다. Azure 이벤트 허브는 소셜 미디어 활동 피드, 주식 거래 정보 또는 센서의 데이터와 같은 여러 장치 및 서비스에서 이벤트 스트림을 수집하는데 사용됩니다. 또는 Azure Blob 저장소를 대량 데이터 수집을 위한 입력 소스로 사용할 수 있습니다.

## 참조 데이터 입력
---
스트림 분석은 참조 데이터라는 두 번째 유형의 입력을 지원합니다. 이 데이터는 대체로 상관관계를 지정하고 조회를 수행하는 데 사용되는 보조 데이터로, 일반적으로 정적이며 자주 변경되지 않습니다. Azure Blob 저장소는 현재 유일하게 지원되는 참조 데이터용 입력 소스입니다. 참조 데이터 소스 Blob은 크기가 50MB로 제한됩니다.

## 이벤트 허브 데이터 입력 스트림 만들기
---
### 이벤트 허브 개요
이벤트 허브는 확장성이 높은 이벤트 ingestor이며 스트림 분석 작업에 대한 데이터 수집의 가장 일반적인 방법입니다. 실시간 분석을 위해 이벤트 허브 및 스트림 분석이 함께 종단 간 솔루션을 고객에게 제공합니다. 이벤트 허브를 사용하면 고객이 Azure에 이벤트를 실시간으로 공급할 수 있으며 스트림 분석 작업에서 실시간으로 처리할 수 있습니다. 예를 들어 고객은 웹 클릭, 센서 판독값, 온라인 로그 이벤트를 이벤트 허브에 보내고 스트림 분석 작업을 만들어 실시간 필터링, 집계 및 조인을 위해 이벤트 허브를 입력 데이터 스트림으로 사용할 수 있습니다. 이벤트 허브는 데이터 송신에도 사용할 수 있습니다. 이벤트 허브에 대한 자세한 내용은 [이벤트 허브](https://azure.microsoft.com/services/event-hubs/ "이벤트 허브(영문)") 설명서를 참조하세요.

### 소비자 그룹
각 스트림 분석 이벤트 허브 입력마다 고유한 소비자 그룹이 있도록 구성해야 합니다. 작업에 자체 조인 또는 여러 입력이 포함된 경우 일부 입력이 둘 이상의 판독기 다운스트림에서 읽혀 단일 소비자 그룹의 판독기 수에 영향을 줍니다 파티션당 소비자 그룹마다 5 판독기의 이벤트 허브 한도 초과를 방지하려면 각 스트림 분석 작업에 대한 소비자 그룹을 지정하는 것이 좋습니다. 또한 이벤트 허브당 20개의 소비자 그룹으로 제한됩니다. 자세한 내용은 [이벤트 허브 프로그래밍 가이드](https://msdn.microsoft.com/library/azure/dn789972.aspx "이벤트 허브 프로그래밍 가이드")를 참조하세요.

## 이벤트 허브 입력 데이터 스트림 만들기
---
### 데이터 스트림 입력으로 이벤트 허브 추가하기  ###

1. 스트림 분석 작업의 입력 탭에서 **ADD INPUT**을 클릭하고 기본 옵션인 **데이터 스트림**을 선택하고 오른쪽 단추를 클릭합니다.

    ![image1](./media/stream-analytics-connect-data-event-inputs/01-stream-analytics-create-inputs.png)

2. 그런 다음 **이벤트 허브**를 선택합니다.

    ![image6](./media/stream-analytics-connect-data-event-inputs/06-stream-analytics-create-inputs.png)

3. 다음 필드를 입력하거나 선택하고 오른쪽 단추를 클릭합니다.

    - **입력 별칭**: 이 입력을 참조하도록 작업 쿼리에서 사용될 이름  
    - **서비스 버스 네임스페이스**: 서비스 버스 네임스페이스는 메시징 엔터티 집합에 대한 컨테이너입니다. 새 이벤트 허브를 만들 때 서비스 버스 네임스페이스도 만들었습니다.  
    - **이벤트 허브**: 이벤트 허브 입력의 이름  
    - **이벤트 허브 정책 이름**: 이벤트 허브 구성 탭에서 만들 수 있는 공유 액세스 정책입니다. 각 공유 액세스 정책에는 이름, 사용자가 설정한 사용 권한 및 액세스 키가 있습니다.  
    - **이벤트 허브 소비자 그룹**(선택 사항): 이벤트 허브에서 데이터를 수집하는 소비자 그룹입니다. 지정되지 않은 경우, 스트림 분석 작업은 기본 소비자 그룹을 사용하여 이벤트 허브에서 데이터를 수집합니다. 각 스트림 분석 작업마다 고유한 소비자 그룹을 사용하는 것이 좋습니다.  

    ![image7](./media/stream-analytics-connect-data-event-inputs/07-stream-analytics-create-inputs.png)

4. 다음 설정을 지정합니다.

    - **이벤트 직렬화 형식**: 쿼리가 예상대로 작동하는지 확인하려면 스트림 분석은 들어오는 데이터 스트림으로 사용 중인 직렬화 형식(JSON, CSV 또는 Avro)을 알고 있어야 합니다.  
    - **인코딩**: UTF-8이 이번에만 지원되는 인코딩 형식입니다.  

    ![image8](./media/stream-analytics-connect-data-event-inputs/08-stream-analytics-create-inputs.png)

5. 마법사를 완료하고 스트림 분석이 이벤트 허브에 성공적으로 연결될 수 있는지 확인하려면 확인 단추를 클릭합니다.

## Blob 저장소 데이터 스트림 입력 만들기
---
클라우드에 저장할 구조화되지 않은 대량 데이터가 있는 시나리오에서 Blob 저장소는 비용 효과적이고 확장성 있는 솔루션을 제공합니다. Blob 저장소의 데이터는 일반적으로 데이터를 "있는 그대로" 간주하지만 스트림 분석으로 데이터 스트림으로 처리될 수 있습니다. 스트림 분석을 사용한 Blob 저장소 입력에 대한 일반적인 시나리오 중 하나는 로그 처리이며 여기에서 원격 분석은 의미 있는 데이터를 추출하려면 시스템에서 캡처되고 구문 분석되고 처리되어야 합니다. 스트림 분석에서 Blob 저장소 이벤트의 기본 타임 스탬프는 Blob이 만들어진 타임 스탬프인 점을 유의하십시오. 이벤트 페이로드에서 타임 스탬프를 사용하여 스트림으로 데이터를 처리하려면 [TIMESTAMP BY](https://msdn.microsoft.com/library/azure/dn834998.aspx) 키워드를 사용해야 합니다. Blob 저장소에 대한 자세한 내용은 [Blob 저장소](http://azure.microsoft.com/services/storage/blobs/) 설명서를 참조하세요.

### 데이터 스트림 입력으로 Blob 저장소 추가하기  ###

1. 스트림 분석 작업의 입력 탭에서 **ADD INPUT**을 클릭하고 기본 옵션인 **데이터 스트림**을 선택하고 오른쪽 단추를 클릭합니다.

    ![image1](./media/stream-analytics-connect-data-event-inputs/01-stream-analytics-create-inputs.png)

2. **Blob 저장소**를 선택하고 오른쪽 단추를 클릭합니다.

    ![image2](./media/stream-analytics-connect-data-event-inputs/02-stream-analytics-create-inputs.png)

3. 다음 필드를 입력하거나 선택합니다.

    - **입력 별칭**: 이 입력을 참조하도록 작업 쿼리에서 사용될 이름  
    - **저장소 계정**: 저장소 계정이 스트리밍 작업과 다른 구독에 있는 경우 저장소 계정 이름과 저장소 계정 키가 필요합니다.  
    - **저장소 컨테이너**: 컨테이너는 Microsoft Azure Blob 서비스에 저장된 Blob에 대한 논리적 그룹화를 제공합니다. Blob 서비스에 Blob을 업로드하는 경우 해당 Blob에 대한 컨테이너를 지정해야 합니다.  

    ![image3](./media/stream-analytics-connect-data-event-inputs/03-stream-analytics-create-inputs.png)

4. 옵션에 대한 **고급 설정 구성** 상자를 클릭하여 사용자 지정 경로에 판독값 Blob에 대한 경로 접두사 패턴을 구성할 수 있습니다. 이 필드를 지정하지 않으면 스트림 분석은 컨테이너의 모든 Blob을 읽습니다.

    ![image4](./media/stream-analytics-connect-data-event-inputs/04-stream-analytics-create-inputs.png)

5. 다음 설정을 선택합니다.

    - **이벤트 직렬화 형식**: 쿼리가 예상대로 작동하는지 확인하려면 스트림 분석은 들어오는 데이터 스트림으로 사용 중인 직렬화 형식(JSON, CSV 또는 Avro)을 알고 있어야 합니다.  
    - **인코딩**: UTF-8이 이번에만 지원되는 인코딩 형식입니다.  


    ![image5](./media/stream-analytics-connect-data-event-inputs/05-stream-analytics-create-inputs.png)

6. 마법사를 완료하고 스트림 분석이 Blob 저장소 계정에 성공적으로 연결될 수 있는지 확인하려면 확인 단추를 클릭합니다.

## Blob 저장소 참조 데이터 만들기
---
Blob 저장소를 스트림 분석 작업에 대한 참조 데이터를 정의하는데 사용할 수 있습니다. 조회를 수행하거나 데이터 상관 관계 설정에 사용되는 정적 또는 속도가 느린 변경 데이터입니다. {date} 및 {time} 토큰을 사용하여 입력 구성의 경로 패턴을 지정하여 참조 데이터 새로 고침 지원을 사용하도록 설정할 수 있습니다. 스트림 분석이 이 경로 패턴에 따라 참조 데이터 정의를 업데이트합니다. 예를 들어 "YYYY-MM-DD"의 날짜 형식 및 "HH:mm"의 시간 형식의 `"/sample/{date}/{time}/products.csv"` 패턴은 표준 시간대 2015년 4월 16일 오후 5시 30분에 스트림 분석이 업데이트된 Blob `"/sample/2015-04-16/17:30/products.csv"`을(를) 선택하도록 알려 줍니다.

> [AZURE.NOTE]현재 스트림 분석 작업은 시간이 Blob 이름에 인코드된 시간과 일치하는 경우에만 참조 Blob 새로 고침 데이터를 찾습니다. 예를 들어 작업은 UTC 표준 시간대로 2015년 4월 16일 오후 5시 30분과 오후 5:30:59.999999999 사이에 /sample/2015-04-16/17:30/products.csv를 찾습니다. 시계가 오후 5시 31분을 가리키면 /sample/2015-04-16/17:30/products.csv 찾기를 중지하고 /sample/2015-04-16/17:31/products.csv 찾기를 시작합니다.

작업이 시작되는 경우에만 이전 참조 데이터 Blob이 고려됩니다. 이때 작업은 값이 작업 시작 시간보다 이전이고 이름에 최신 날짜/시간이 인코드되어 있는 Blob(작업 시작 시간 전의 최신 참조 데이터 Blob)을 찾습니다. 이렇게 하면 작업을 시작할 때 비어 있지 않은 참조 데이터가 설정됩니다. 찾을 수 없는 경우 작업이 실패하고 사용자에 게 진단 알림이 표시됩니다.

### 참조 데이터로 Blob 저장소 추가  ###

1. 스트림 분석 작업의 입력 탭에서 **ADD INPUT**을 클릭하고 **참조 데이터**를 선택하고 오른쪽 단추를 클릭합니다.

    ![image9](./media/stream-analytics-connect-data-event-inputs/09-stream-analytics-create-inputs.png)

2.	다음 필드를 입력하거나 선택합니다.

    - **입력 별칭**: 이 입력을 참조하도록 작업 쿼리에서 사용될 이름  
    - **저장소 계정**: 저장소 계정이 스트리밍 작업과 다른 구독에 있는 경우 저장소 계정 이름과 저장소 계정 키가 필요합니다.  
    - **저장소 컨테이너**: 컨테이너는 Microsoft Azure Blob 서비스에 저장된 Blob에 대한 논리적 그룹화를 제공합니다. Blob 서비스에 Blob을 업로드하는 경우 해당 Blob에 대한 컨테이너를 지정해야 합니다.  
    - **경로 패턴**: 지정된 컨테이너 내에서 Blob을 찾는데 사용되는 파일 경로입니다. 경로 내에서 {date}, {time}의 두 변수 인스턴스 중 하나 이상을 지정하도록 선택할 수도 있습니다.  

    ![image10](./media/stream-analytics-connect-data-event-inputs/10-stream-analytics-create-inputs.png)

3. 다음 설정을 선택합니다.

    - **이벤트 직렬화 형식**: 쿼리가 예상대로 작동하는지 확인하려면 스트림 분석은 들어오는 데이터 스트림으로 사용 중인 직렬화 형식(JSON, CSV 또는 Avro)을 알고 있어야 합니다.  
    - **인코딩**: UTF-8이 이번에만 지원되는 인코딩 형식입니다.  

    ![image12](./media/stream-analytics-connect-data-event-inputs/12-stream-analytics-create-inputs.png)

4.	마법사를 완료하고 스트림 분석이 Blob 저장소 계정에 성공적으로 연결될 수 있는지 확인하려면 확인 단추를 클릭합니다.

    ![image11](./media/stream-analytics-connect-data-event-inputs/11-stream-analytics-create-inputs.png)


## 도움말 보기
추가 지원이 필요할 경우 [Azure 스트림 분석 포럼](https://social.msdn.microsoft.com/Forums/ko-KR/home?forum=AzureStreamAnalytics)을 참조하세요.

## 다음 단계

- [Azure 스트림 분석 소개](stream-analytics-introduction.md)
- [Azure 스트림 분석 사용 시작](stream-analytics-get-started.md)
- [Azure 스트림 분석 작업 규모 지정](stream-analytics-scale-jobs.md)
- [Azure 스트림 분석 쿼리 언어 참조](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Azure 스트림 분석 관리 REST API 참조](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!---HONumber=AcomDC_1125_2015-->