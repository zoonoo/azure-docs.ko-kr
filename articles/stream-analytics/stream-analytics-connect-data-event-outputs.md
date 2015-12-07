<properties 
	pageTitle="스트림 분석 출력 연결 | Microsoft Azure" 
	description="스트림 분석 솔루션에 대한 출력 대상에 연결하고 구성하는 방법을 알아봅니다." 
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
	ms.date="11/06/2015" 
	ms.author="jeffstok"/>

# 스트림 분석 출력 소스 연결

## 스트림 분석 출력 이해 ##
---
스트림 분석 작업을 만드는 경우 고려할 사항 중 하나는 작업의 출력을 사용하는 방법입니다. 데이터 변환 소비자에게 스트림 분석 작업 결과가 어떻게 표시되나요? 출력을 분석하기 위해 어떤 도구를 사용하나요? 데이터 보존 또는 웨어하우징이 요구 사항인가요?

Azure 스트림 분석은 작업 출력을 저장 및 표시하는 7가지 다른 방법을 제공합니다. SQL 데이터베이스, Blob 저장소, 이벤트 허브, 서비스 버스 큐, 서비스 버스 토픽, Power BI 및 테이블 저장소. 작업 출력을 쉽게 볼 수 있는 편의성과 데이터 웨어하우징 및 기타 용도로 작업 출력을 사용 및 저장할 수 있는 유연성을 모두 제공합니다.

## SQL 데이터베이스를 출력으로 사용 ##
---
기본적으로 관계형 데이터 또는 관계형 데이터베이스에 호스트된 내용에 종속된 응용 프로그램에 대한 출력으로 SQL 데이터베이스를 사용할 수 있습니다. Azure SQL 데이터베이스에 대한 자세한 내용은 [Azure SQL 데이터베이스](http://azure.microsoft.com/services/sql-database/)를 참조하세요.

### 매개 변수 ###

SQL 데이터베이스를 사용하려면 SQL 데이터베이스에 대한 다음 정보가 필요합니다.

1. 서버 이름
2. 데이터베이스 이름
3. 유효한 사용자 이름/암호 조합
4. 출력 테이블 이름. 이 테이블은 이미 있어야 합니다. 작업에서 만들지 않습니다.

### 출력으로 SQL 데이터베이스 추가 ###

![graphic1][graphic1]

작업의 출력 탭으로 이동한 다음 **ADD OUTPUT** 명령을 클릭하고 다음을 클릭합니다.

![graphic2][graphic2]

출력으로 **SQL 데이터베이스**를 선택합니다.

![graphic3][graphic3]

다음 페이지에서 데이터베이스 정보를 입력합니다. 출력 별칭은 쿼리 출력을 이 데이터베이스로 보내기 위해 쿼리에서 사용되는 이름입니다. 단일 출력만 있는 경우 기본 별칭은 "output"입니다.

![graphic4][graphic4]

스트림 분석 작업과 동일한 구독 내에 데이터베이스가 있는 경우 "현재 구독의 SQL 데이터베이스 사용"을 선택하는 옵션을 사용할 수 있습니다. 드롭다운 목록에서 데이터베이스를 선택합니다.

![graphic5][graphic5]

이 출력을 추가하려면 다음을 클릭합니다. 두 개의 작업이 시작되며, 첫 번째 작업은 출력을 추가하는 것입니다.

![graphic6][graphic6]

두 번째 작업은 연결을 테스트하는 것입니다. Azure 스트림 분석은 SQL 데이터베이스에 연결하고 입력한 자격 증명이 올바른지, 그리고 테이블에 액세스할 수 있는지를 확인하려고 합니다.

## 출력으로 Blob 저장소 사용 ##
---
Azure Blob 저장소 및 사용법에 대한 소개 내용은 [Blob 사용 방법](./articles/storage/storage-dotnet-how-to-use-blobs.md) 설명서를 참조하세요.

### 매개 변수 ###

Blob 저장소 출력을 사용하려면 다음 정보가 필요합니다.

1. 저장소 계정이 스트리밍 작업과 다른 구독에 있는 경우 저장소 계정 이름과 저장소 계정 키를 제공하는 필드가 나타납니다.
2. 컨테이너 이름
3. 파일 이름 접두사
4. 데이터에 사용되는 직렬화 형식(Avro, CSV, JSON)

### 출력으로 Blob 저장소 추가 ###

Blob 저장소로 출력을 선택합니다.

![graphic20][graphic20]

아래와 같이 세부 정보를 제공합니다.

![graphic21][graphic21]

## 출력으로 이벤트 허브 사용 ##
---
### 개요 ###
 
이벤트 허브는 확장성이 높은 이벤트 ingestor이며 일반적으로 스트림 분석 데이터 유입에 대한 가장 일반적인 방법입니다. 다수의 이벤트를 강력하게 처리하므로 작업 출력에도 완벽합니다. 출력으로 이벤트 허브를 사용하는 한 가지 예는 스트림 분석 작업의 출력이 다른 스트리밍 작업의 입력이 되는 경우입니다. 이벤트 허브에 대한 자세한 내용은 포털의 [이벤트 허브](https://azure.microsoft.com/services/event-hubs/ "이벤트 허브(영문)")를 참조하세요.
 
### 매개 변수 ###

이벤트 허브 데이터 스트림을 구성하는 데 필요한 몇 개의 매개 변수가 있습니다.

1. 서비스 버스 네임스페이스: 이벤트 허브의 서비스 버스 네임스페이스입니다. 서비스 버스 네임스페이스는 메시징 엔터티 집합에 대한 컨테이너입니다. 새 이벤트 허브를 만들 때 서비스 버스 네임스페이스도 생성됩니다. 
2. 이벤트 허브 이름: 이벤트 허브의 이름입니다. 새 이벤트 허브를 만들 때 지정된 이름입니다. 
3. 이벤트 허브 정책 이름: 이벤트 허브에 액세스 하기 위해 공유된 액세스 정책의 이름입니다. 공유 액세스 정책은 구성 탭의 이벤트 허브에 대해 구성될 수 있습니다. 각 공유 액세스 정책에는 이름, 사용 권한 집합 및 생성된 액세스 키가 있습니다.
4. 이벤트 허브 정책 이름: 이벤트 허브에 액세스하기 위해 공유된 액세스 정책의 기본 또는 보조 키입니다.  
5. 파티션 키 열: 이벤트 허브 출력에 대한 선택적 매개 변수입니다. 이 열에는 이벤트 허브 출력에 대한 파티션 키가 포함됩니다.

### 출력으로 이벤트 허브 추가 ###

1. 페이지의 맨 위에서 **출력**을 클릭한 다음 **출력 추가**를 클릭합니다. 출력 옵션으로 이벤트 허브를 선택하고 창의 맨 아래에서 오른쪽 단추를 클릭합니다.

    ![graphic38][graphic38]

2. 출력 필드에 관련 정보를 제공하고, 작업이 완료되면 창의 맨 아래에서 오른쪽 단추를 클릭하여 계속 진행합니다.

    ![graphic36][graphic36]

3. 이벤트 직렬화 형식, 인코딩 형식 및 형식이 적절한 값으로 설정되었는지 확인하고 확인란을 클릭하여 작업을 완료합니다.

    ![graphic37][graphic37]

## 출력으로 Power BI 사용 ##
---
### 개요 ###
Power BI를 스트림 분석 작업에 대한 출력으로 이용하여 스트림 분석 사용자에게 풍부한 시각화 환경을 제공할 수 있습니다. 작업 대시보드, 보고서 생성 및 메트릭 제어 보고에 이 기능을 이용할 수 있습니다. Power BI에 대한 자세한 내용은 [Power BI](https://powerbi.microsoft.com/) 사이트를 참조하세요.

### 매개 변수 ###

Power BI 출력을 구성하는 데 필요한 몇 개의 매개 변수가 있습니다.

1. 출력 별칭 – 참조하기 쉬운 출력 별칭입니다. 이 출력 별칭은 작업에 대한 여러 출력을 포함하려는 경우에 특히 유용합니다. 이 경우 쿼리에서 이 별칭을 참조합니다. 예를 들어 출력 별칭 값 = "OutPbi"를 사용합니다.
2. 데이터 집합 이름 - Power BI 출력에 사용할 데이터 집합 이름을 제공합니다. 예를 들어 "pbidemo"를 사용합니다.
2. 테이블 이름 - Power BI 출력의 데이터 집합 아래에 테이블 이름을 제공합니다. 예를 들어 "pbidemo"를 사용합니다. **현재, 스트림 분석 작업의 Power BI 출력에는 하나의 데이터 집합에 하나의 테이블만 있을 수 있습니다.**

### 출력으로 Power BI 추가 ###

1.  페이지의 맨 위에서 **출력**을 클릭한 다음 **출력 추가**를 클릭합니다. 출력 옵션으로 Power BI를 선택합니다.

    ![graphic22][graphic22]

2.  다음과 같은 화면이 표시됩니다.

    ![graphic23][graphic23]

3.  이 단계에서 Power BI 출력에 대한 권한을 부여할 회사 또는 학교 계정을 제공합니다. 아직 Power BI에 등록하지 않은 경우 **지금 등록**을 선택합니다.
4.  다음과 같은 화면이 표시됩니다.

    ![graphic24][graphic24]


>	[AZURE.NOTE] One should not explicitly create the dataset and table in the Power BI dashboard. The dataset and table will be automatically populated when the job is started and the job starts pumping output into Power BI. Note that if the job query doesn’t return any results, the dataset and table will not be created. Also be aware that if Power BI already had a dataset and table with the same name as the one provided in this Stream Analytics job, the existing data will be overwritten.

*	**확인**, **연결 테스트**를 클릭하면 이제 출력 구성이 완료되었습니다.


## 출력으로 Azure 테이블 저장소 사용 ##
---
테이블 저장소는 가용성이 높고 확장성이 큰 저장소를 제공하므로, 응용 프로그램이 사용자 요구에 맞게 자동으로 확장할 수 있습니다. 테이블 저장소는 스키마에 대한 제약 조건이 적은 구조화된 데이터에 활용할 수 있는 Microsoft의 NoSQL 키/특성 저장소입니다. Azure 테이블 저장소는 지속적이고 효율적인 검색을 위해 데이터를 저장하는 데 사용할 수 있습니다. Azure 테이블 저장소에 대한 자세한 내용은 [Azure 테이블 저장소](./articles/storage/storage-introduction.md)를 참조하세요.

### 매개 변수 ###

Azure 테이블 저장소를 사용하려면 다음 정보가 필요합니다.

1. 저장소 계정 이름(이 저장소가 스트리밍 작업과 다른 구독에 있는 경우)
2. 저장소 계정 키(이 저장소가 스트리밍 작업과 다른 구독에 있는 경우)
3. 출력 테이블 이름(없는 경우 생성됨).
4. 파티션 키(필수).
5. 행 키.

파티션 키와 행 키의 더 나은 디자인을 위해 [Azure 테이블 저장소에 대한 확장 가능한 분할 전략 설계](https://msdn.microsoft.com/library/azure/hh508997.aspx)(영문) 문서를 참조하세요.

### 출력으로 Azure 테이블 저장소 추가 ###

![graphic11][graphic11]

작업의 출력 탭으로 이동한 다음 **ADD OUTPUT** 명령을 클릭하고 다음을 클릭합니다.

![graphic12][graphic12]

출력으로 **테이블 저장소**를 선택합니다.

![graphic13][graphic13]

다음 페이지에서 Azure 테이블 정보를 입력합니다. 출력 별칭은 쿼리 출력을 이 테이블로 보내기 위해 쿼리에서 사용할 수 있는 이름입니다.

![graphic14][graphic14]

![graphic15][graphic15]

일괄 처리 크기는 일괄 작업에 대한 레코드 수입니다. 일반적으로 대부분의 작업은 기본값으로 충분합니다. 이 설정을 수정하는 방법에 대한 자세한 내용은 [테이블 일괄 작업 사양](https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tablebatchoperation.aspx)을 참조하세요.

Azure 저장소 계정이 작업을 만드는 데 사용되는 것과 동일한 구독 내에 있는 경우 "현재 구독의 저장소 계정 사용"을 선택한 다음 드롭다운에서 저장소 계정을 선택합니다.

이 출력을 추가하려면 다음을 클릭합니다. 두 개의 작업이 시작되며, 첫 번째 작업은 출력을 추가하는 것입니다.

![graphic16][graphic16]

두 번째 작업은 연결을 테스트하는 것입니다. Azure 스트림 분석은 해당 저장소 계정에 연결하고 입력된 자격 증명이 올바른지 확인하려고 합니다.

## 서비스 버스 큐 ##
---
### 서비스 버스 큐 개념 소개 ###
서비스 버스 큐는 하나 이상의 경쟁 소비자에게 FIFO(선입선출) 메시지 배달을 제공합니다. 일반적으로 메시지가 큐에 추가된 임시 순서대로 받는 사람이 메시지를 받고 처리하며, 각 메시지가 하나의 메시지 소비자에 의해서만 수신 및 처리됩니다.

서비스 버스 큐에 대한 자세한 내용은 [서비스 버스 큐, 토픽 및 구독](https://msdn.microsoft.com/library/azure/hh367516.aspx "서비스 버스 큐, 토픽 및 구독") 및 [서비스 버스 큐 소개](http://blogs.msdn.com/b/appfabric/archive/2011/05/17/an-introduction-to-service-bus-queues.aspx "서비스 버스 큐 소개")를 참조하세요.

### 매개 변수 ###

서비스 버스 큐 출력을 사용하려면 다음 정보가 필요합니다.

1. 출력 별칭 – 참조하기 쉬운 출력 별칭입니다. 이 출력 별칭은 작업에 대한 여러 출력을 포함하려는 경우에 특히 유용합니다. 이 경우 작업 쿼리에서 이 별칭을 참조합니다.
2. 네임스페이스 및 서비스 버스 이름
3. 큐 이름 - 큐는 이벤트 허브 및 토픽과 유사한 메시징 엔터티입니다. 다양한 장치 및 서비스에서 이벤트 스트림을 수집하도록 설계됩니다. 큐를 만들 때 특정 이름도 지정됩니다.
4. 데이터에 사용되는 직렬화 형식(Avro, CSV, JSON)

### 출력으로 서비스 버스 큐 추가 ###

![graphic31][graphic31]

아래와 같이 세부 정보를 제공하고 다음을 선택합니다.

![graphic32][graphic32]

데이터 형식 및 직렬화가 올바른지 확인합니다.

![graphic33][graphic33]

## 서비스 버스 토픽 ##
---
### 서비스 버스 토픽 개념 소개 ###
서비스 버스 큐는 보낸 사람에서 받는 사람으로의 일대일 통신 방법을 제공하는 반면, 서비스 버스 토픽은 일대다 형태의 통신을 제공합니다.

서비스 버스 토픽에 대한 자세한 내용은 [서비스 버스 큐, 토픽 및 구독](https://msdn.microsoft.com/library/azure/hh367516.aspx "서비스 버스 큐, 토픽 및 구독") 및 [서비스 버스 토픽 소개](http://blogs.msdn.com/b/appfabric/archive/2011/05/25/an-introduction-to-service-bus-topics.aspx "서비스 버스 토픽 소개")를 참조하세요.

### 매개 변수 ###

서비스 버스 토픽 출력을 사용하려면 다음 정보가 필요합니다.

1. 출력 별칭 – 참조하기 쉬운 출력 별칭입니다. 이 출력 별칭은 작업에 대한 여러 출력을 포함하려는 경우에 특히 유용합니다. 이 경우 쿼리에서 이 별칭을 참조합니다.
2. 네임스페이스 및 서비스 버스 이름
3. 토픽 이름 - 토픽은 이벤트 허브 및 큐와 유사한 메시징 엔터티입니다. 다양한 장치 및 서비스에서 이벤트 스트림을 수집하도록 설계됩니다. 토픽을 만들 때 특정 이름도 지정됩니다. 구독을 만들지 않으면 토픽에 전송된 메시지를 사용할 수 없으므로 토픽 아래에 구독이 하나 이상 있는지 확인합니다.
4. 데이터에 사용되는 직렬화 형식(Avro, CSV, JSON)

### 출력으로 서비스 버스 토픽 추가 ###

서비스 버스 토픽으로 출력을 선택합니다.

![graphic34][graphic34]

아래와 같이 세부 정보를 제공하고 다음을 선택합니다.

![graphic35][graphic35]

데이터 형식 및 직렬화가 올바른지 확인합니다.

![graphic33][graphic33]


## 도움말 보기
추가 지원이 필요할 경우 [Azure 스트림 분석 포럼](https://social.msdn.microsoft.com/Forums/ko-KR/home?forum=AzureStreamAnalytics)을 참조하세요.

## 다음 단계

- [Azure 스트림 분석 소개](stream-analytics-introduction.md)
- [Azure 스트림 분석 사용 시작](stream-analytics-get-started.md)
- [Azure 스트림 분석 작업 규모 지정](stream-analytics-scale-jobs.md)
- [Azure 스트림 분석 쿼리 언어 참조](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Azure 스트림 분석 관리 REST API 참조](https://msdn.microsoft.com/library/azure/dn835031.aspx)




[graphic1]: ./media/stream-analytics-connect-data-event-outputs/1-stream-analytics-connect-data-event-input-output.png
[graphic2]: ./media/stream-analytics-connect-data-event-outputs/2-stream-analytics-connect-data-event-input-output.png
[graphic3]: ./media/stream-analytics-connect-data-event-outputs/3-stream-analytics-connect-data-event-input-output.png
[graphic4]: ./media/stream-analytics-connect-data-event-outputs/4-stream-analytics-connect-data-event-input-output.png
[graphic5]: ./media/stream-analytics-connect-data-event-outputs/5-stream-analytics-connect-data-event-input-output.png
[graphic6]: ./media/stream-analytics-connect-data-event-outputs/6-stream-analytics-connect-data-event-input-output.png
[graphic7]: ./media/stream-analytics-connect-data-event-outputs/7-stream-analytics-connect-data-event-input-output.png
[graphic8]: ./media/stream-analytics-connect-data-event-outputs/8-stream-analytics-connect-data-event-input-output.png
[graphic9]: ./media/stream-analytics-connect-data-event-outputs/9-stream-analytics-connect-data-event-input-output.png
[graphic10]: ./media/stream-analytics-connect-data-event-outputs/10-stream-analytics-connect-data-event-input-output.png
[graphic11]: ./media/stream-analytics-connect-data-event-outputs/11-stream-analytics-connect-data-event-input-output.png
[graphic12]: ./media/stream-analytics-connect-data-event-outputs/12-stream-analytics-connect-data-event-input-output.png
[graphic13]: ./media/stream-analytics-connect-data-event-outputs/13-stream-analytics-connect-data-event-input-output.png
[graphic14]: ./media/stream-analytics-connect-data-event-outputs/14-stream-analytics-connect-data-event-input-output.png
[graphic15]: ./media/stream-analytics-connect-data-event-outputs/15-stream-analytics-connect-data-event-input-output.png
[graphic16]: ./media/stream-analytics-connect-data-event-outputs/16-stream-analytics-connect-data-event-input-output.png
[graphic17]: ./media/stream-analytics-connect-data-event-outputs/17-stream-analytics-connect-data-event-input-output.png
[graphic18]: ./media/stream-analytics-connect-data-event-outputs/18-stream-analytics-connect-data-event-input-output.png
[graphic19]: ./media/stream-analytics-connect-data-event-outputs/19-stream-analytics-connect-data-event-input-output.png
[graphic20]: ./media/stream-analytics-connect-data-event-outputs/20-stream-analytics-connect-data-event-input-output.png
[graphic21]: ./media/stream-analytics-connect-data-event-outputs/21-stream-analytics-connect-data-event-input-output.png
[graphic22]: ./media/stream-analytics-connect-data-event-outputs/22-stream-analytics-connect-data-event-input-output.png
[graphic23]: ./media/stream-analytics-connect-data-event-outputs/23-stream-analytics-connect-data-event-input-output.png
[graphic24]: ./media/stream-analytics-connect-data-event-outputs/24-stream-analytics-connect-data-event-input-output.png
[graphic25]: ./media/stream-analytics-connect-data-event-outputs/25-stream-analytics-connect-data-event-input-output.png
[graphic26]: ./media/stream-analytics-connect-data-event-outputs/26-stream-analytics-connect-data-event-input-output.png
[graphic27]: ./media/stream-analytics-connect-data-event-outputs/27-stream-analytics-connect-data-event-input-output.png
[graphic28]: ./media/stream-analytics-connect-data-event-outputs/28-stream-analytics-connect-data-event-input-output.png
[graphic29]: ./media/stream-analytics-connect-data-event-outputs/29-stream-analytics-connect-data-event-input-output.png
[graphic30]: ./media/stream-analytics-connect-data-event-outputs/30-stream-analytics-connect-data-event-input-output.png
[graphic31]: ./media/stream-analytics-connect-data-event-outputs/31-stream-analytics-connect-data-event-input-output.png
[graphic32]: ./media/stream-analytics-connect-data-event-outputs/32-stream-analytics-connect-data-event-input-output.png
[graphic33]: ./media/stream-analytics-connect-data-event-outputs/33-stream-analytics-connect-data-event-input-output.png
[graphic34]: ./media/stream-analytics-connect-data-event-outputs/34-stream-analytics-connect-data-event-input-output.png
[graphic35]: ./media/stream-analytics-connect-data-event-outputs/35-stream-analytics-connect-data-event-input-output.png
[graphic36]: ./media/stream-analytics-connect-data-event-outputs/36-stream-analytics-connect-data-event-input-output.png
[graphic37]: ./media/stream-analytics-connect-data-event-outputs/37-stream-analytics-connect-data-event-input-output.png
[graphic38]: ./media/stream-analytics-connect-data-event-outputs/38-stream-analytics-connect-data-event-input-output.png

<!---HONumber=AcomDC_1125_2015-->