<properties 
	pageTitle="입력 및 출력에 연결 | Microsoft Azure" 
	description="스트림 분석 솔루션에 대해 연결할 입력 소스 및 출력 대상에 연결하고 구성하는 방법에 대해 알아봅니다." 
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
	ms.date="04/24/2015" 
	ms.author="jeffstok"/>

#입력 및 출력에 연결
이 문서에서는 스트림 분석 솔루션에 대 한 입력 원본 및 출력 대상을 구성하는 다양한 방법을 다룹니다.

##SQL을 출력으로 사용

기본적으로 관계형 데이터 또는 데이터베이스에서 호스트되는 내용에 종속된 응용 프로그램에 대한 출력으로 Azure SQL 데이터베이스를 사용할 수 있습니다.

[http://azure.microsoft.com/services/sql-database/](http://azure.microsoft.com/services/sql-database/)

Azure SQL 데이터베이스 사용을 시작하려면, 사용자 데이터베이스에 대한 다음 정보가 있어야 합니다.

1. 서버 이름.
2. 데이터베이스 이름.
3. 유효한 사용자 이름/암호 조합입니다.
4. 출력 테이블 이름.

![graphic1][graphic1]

사용자 작업의 출력 탭으로 이동하고 "ADD OUTPUT" 명령을 클릭하고 다음을 클릭합니다.

![graphic2][graphic2]


사용자 출력으로 "SQL 데이터베이스"를 선택합니다.

![graphic3][graphic3]

다음 페이지에서 데이터베이스 정보를 입력합니다. 출력 별칭은 쿼리 출력을 이 데이터베이스로 지정하기 위해 쿼리에서 사용할 수 있는 이름입니다. 하나의 출력만 있는 경우 기본 별칭은 "출력"입니다.

![graphic4][graphic4]

사용 중인 동일한 구독 내에 존재하는 데이터베이스를 사용하는 경우, "현재 구독에서 SQL 데이터베이스 사용"을 선택한 다음 드롭다운에서 데이터베이스를 선택합니다.

![graphic5][graphic5]

이 출력을 추가하려면 다음을 클릭합니다. 두 작업이 시작되는 것이 보이면 첫 번째 작업이 출력에 추가됩니다.

![graphic6][graphic6]

두 번째 작업은 연결을 테스트하는 것입니다. Azure 스트림 분석은 해당 SQL 데이터베이스에 연결하고 입력한 자격 증명이 올바른지, 그리고 테이블에 액세스할 수 있는지를 확인하려고 시도합니다. 완료된 후 두 메시지 중 하나가 표시되어야 합니다.

![graphic7][graphic7]

![graphic8][graphic8]



나중 메시지가 표시되면, "세부 정보"를 클릭하여 해당 오류에 대한 자세한 정보를 봅니다.

![graphic9][graphic9]

이 예제에서는 제공된 자격 증명이 잘못되었습니다. 자격 증명을 수정할 수 있으며 "연결 테스트" 단추를 클릭하여 다시 테스트를 시도합니다.

![graphic10][graphic10]

##이벤트 허브 사용

###개요
 
이벤트 허브는 확장성이 높은 이벤트 ingestor이며 일반적으로 스트림 분석 데이터 유입에 대한 가장 일반적인 방법입니다. 다양한 장치 및 서비스에서 이벤트 스트림을 수집하도록 설계됩니다. 실시간 분석을 위해 이벤트 허브 및 스트림 분석이 함께 고객에게 종단간 솔루션으로 제공됩니다. 이벤트 허브를 사용하면 고객이 Azure로 이벤트를 실시간으로 피드되고 스트림 분석 작업에서 실시간으로 처리할 수 있습니다. 예를들어, 고객은 이벤트 허브에 웹 클릭, 센서 판독값, 온라인 로그 이벤트를 게시 하고 실시간으로 필터링, 집계 및 조인하기 위한 입력 데이터 스트림으로 이벤트 허브를 사용하여 스트림 분석 작업을 만들 수 있습니다. 이벤트 허브는 데이터 송신에도 사용할 수 있습니다. 출력으로서의 EH의 가장 일반적인 용도는 스트림 분석 작업의 출력이 다른 스트리밍 작업의 입력이 되는 경우입니다.

###소비자 그룹
각 스트림 분석 작업 입력은 이벤트 허브 소비자 그룹 자체를 가지도록 구성되어야 합니다. 작업에 자체 조인 또는 여러 출력이 포함된 경우, 일부 입력은 둘 이상의 판독기에서 읽혀 단일 소비자 그룹의 전체 판독기가 이벤트 허브의 소비자 그룹당 5개의 판독기 제한을 초과하게 됩니다. 이 경우 쿼리는 추가 이벤트 허브를 통해 라우팅되는 여러 쿼리 및 중간 결과로 세분화할 수 있어야 합니다. 이벤트 허브 당 20개의 소비자 그룹으로 제한합니다. 자세한 내용은 이벤트 허브 개발자 가이드를 참조하세요.

 
###매개 변수
 
고객이 이벤트 허브 데이터 스트림을 구성해야하는 몇가지 매개 변수가 있습니다. 다르게 표시되지 않은 경우, 이 매개변수는 이벤트 허브 입력과 출력 데이터 스트림 모두에 적용합니다.

1. 서비스 버스 네임스페이스: 이벤트 허브의 서비스 버스 네임스페이스입니다. 서비스 버스 네임스페이스는 메시징 엔터티 집합에 대한 컨테이너입니다. 새 이벤트 허브를 만들 때 서비스 버스 네임스페이스도 만들었습니다. 
2. 이벤트 허브 이름: 이벤트 허브의 이름입니다. 새 이벤트 허브를 만들 때 지정한 이름입니다. 
3. 이벤트 허브 정책 이름: 이벤트 허브에 액세스 하기 위해 공유된 액세스 정책의 이름입니다. 공유 액세스 정책은 구성 탭의 이벤트 허브에 대해 구성될 수 있습니다. 각 공유 액세스 정책에는 이름, 사용자가 설정한 사용 권한 및 액세스 키가 있습니다.
4. 이벤트 허브 정책 이름: 이벤트 허브에 액세스하기 위해 공유된 액세스 정책의 기본 또는 보조 키입니다.  
5. 이벤트 허브 소비자 그룹: 이벤트 허브 입력에 대한 선택적 매개 변수입니다. 이벤트 허브에서 데이터를 수집하는 소비자 그룹입니다. 지정되지 않은 경우, 스트림 분석 작업은 기본 소비자 그룹을 사용하여 이벤트 허브에서 데이터를 수집합니다. 각 스트림 분석 작업에 대한 고유한 소비자 그룹을 사용하는 것이 좋습니다.

파티션 키 열: 이벤트 허브 출력에 대한 선택적 매개 변수입니다. 이벤트 허브 출력에 대한 파티션 키로 사용되는 데이터 특성 열입니다.

##Azure 테이블 출력 사용

스키마에 제한이 적은 구조화된 데이터에 대해 azure 테이블을 사용할 수 있습니다. Azure 테이블 저장소는 지속적이고 효율적인 검색을 위해 데이터를 저장하는데 사용할 수 있습니다. 자세한 내용은 [Azure 저장소 소개](http://azure.microsoft.com/storage-introduction/)를 참조하세요.
 
Azure 테이블 저장소를 사용하여 시작하려면 테이블에 대한 다음 정보가 있어야 합니다.

1. 저장소 계정 이름(이 저장소가 사용자 스트리밍 작업과 다른 구독인 경우)입니다.
2. 저장소 계정 키(이 저장소가 사용자 스트리밍 작업과 다른 구독인 경우)입니다.
3. 출력 테이블 이름(없는 경우 생성됨).
4. 파티션 키(필수).
5.   행 키(현재는 필수이지만, 고객 피드백에 따라 옵션으로 설정하려고 합니다.)

파티션 키와 행 키의 더 나은 디자인을 위해 [Azure 테이블 저장소에 대한 확장 가능한 분할 전략 설계](https://msdn.microsoft.com/library/azure/hh508997.aspx)(영문) 문서를 참조하세요.


![graphic11][graphic11]


사용자 작업의 출력 탭으로 이동하고 "ADD OUTPUT" 명령을 클릭하고 다음을 클릭합니다.


![graphic12][graphic12]


사용자 출력으로 "테이블 저장소"를 선택합니다.


![graphic13][graphic13]


다음 페이지에서 Azure 테이블 정보를 입력합니다. 출력 별칭은 쿼리 출력을 이 테이블로 지정하기 위해 쿼리에서 사용할 수 있는 이름입니다.


![graphic14][graphic14]
![graphic15][graphic15]

일괄 처리 크기는 일괄 처리 작업에 대한 레코드의 수로, 익숙하지 않은 경우 기본값으로 그대로 두거나 자세한 내용은 [https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tablebatchoperation.aspx](https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tablebatchoperation.aspx)를 참조하세요.


사용 중인 동일한 구독 내에 존재하는 Azure 저장소를 사용하는 경우, "현재 구독에서 저장소 계정 사용"을 선택한 다음 드롭다운에서 저장소 계정을 선택합니다.

이 출력을 추가하려면 다음을 클릭합니다. 두 작업이 시작되는 것이 보이면 첫 번째 작업이 출력에 추가됩니다.

![graphic16][graphic16]

두 번째 작업은 연결을 테스트하는 것입니다. Azure 스트림 분석은 해당 저장소 계정에 연결하고 입력한 자격 증명이 올바른지 확인하려고 시도합니다. 완료된 후 두 메시지 중 하나가 표시되어야 합니다.

![graphic17][graphic17]

나중 메시지가 표시되면, "세부 정보"를 클릭하여 해당 오류에 대한 자세한 정보를 봅니다.

![graphic18][graphic18]

이 예제에서는 제공된 자격 증명이 잘못되었습니다. 자격 증명을 수정할 수 있으며 "연결 테스트" 단추를 클릭하여 다시 테스트를 시도합니다.

![graphic19][graphic19]

## 도움말 보기
추가 지원이 필요할 경우 [Azure 스트림 분석 포럼](https://social.msdn.microsoft.com/Forums/ko-kr/home?forum=AzureStreamAnalytics)을 참조하세요.

## 다음 단계

- [Azure 스트림 분석 소개](stream-analytics-introduction.md)
- [Azure 스트림 분석 사용 시작](stream-analytics-get-started.md)
- [Azure 스트림 분석 작업 규모 지정](stream-analytics-scale-jobs.md)
- [Azure 스트림 분석 쿼리 언어 참조](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Azure 스트림 분석 관리 REST API 참조](https://msdn.microsoft.com/library/azure/dn835031.aspx)




[graphic1]: ./media/stream-analytics-connect-data-event-input-output/1-stream-analytics-connect-data-event-input-output.png
[graphic2]: ./media/stream-analytics-connect-data-event-input-output/2-stream-analytics-connect-data-event-input-output.png
[graphic3]: ./media/stream-analytics-connect-data-event-input-output/3-stream-analytics-connect-data-event-input-output.png
[graphic4]: ./media/stream-analytics-connect-data-event-input-output/4-stream-analytics-connect-data-event-input-output.png
[graphic5]: ./media/stream-analytics-connect-data-event-input-output/5-stream-analytics-connect-data-event-input-output.png
[graphic6]: ./media/stream-analytics-connect-data-event-input-output/6-stream-analytics-connect-data-event-input-output.png
[graphic7]: ./media/stream-analytics-connect-data-event-input-output/7-stream-analytics-connect-data-event-input-output.png
[graphic8]: ./media/stream-analytics-connect-data-event-input-output/8-stream-analytics-connect-data-event-input-output.png
[graphic9]: ./media/stream-analytics-connect-data-event-input-output/9-stream-analytics-connect-data-event-input-output.png
[graphic10]: ./media/stream-analytics-connect-data-event-input-output/10-stream-analytics-connect-data-event-input-output.png
[graphic11]: ./media/stream-analytics-connect-data-event-input-output/11-stream-analytics-connect-data-event-input-output.png
[graphic12]: ./media/stream-analytics-connect-data-event-input-output/12-stream-analytics-connect-data-event-input-output.png
[graphic13]: ./media/stream-analytics-connect-data-event-input-output/13-stream-analytics-connect-data-event-input-output.png
[graphic14]: ./media/stream-analytics-connect-data-event-input-output/14-stream-analytics-connect-data-event-input-output.png
[graphic15]: ./media/stream-analytics-connect-data-event-input-output/15-stream-analytics-connect-data-event-input-output.png
[graphic16]: ./media/stream-analytics-connect-data-event-input-output/16-stream-analytics-connect-data-event-input-output.png
[graphic17]: ./media/stream-analytics-connect-data-event-input-output/17-stream-analytics-connect-data-event-input-output.png
[graphic18]: ./media/stream-analytics-connect-data-event-input-output/18-stream-analytics-connect-data-event-input-output.png
[graphic19]: ./media/stream-analytics-connect-data-event-input-output/19-stream-analytics-connect-data-event-input-output.png
 

<!---HONumber=62-->