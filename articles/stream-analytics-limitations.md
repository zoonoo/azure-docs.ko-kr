<properties title="Azure Stream Analytics limitations in the preview release" pageTitle=" 미리 보기 릴리스의 스트림 분석 제한 | Azure" description="Azure 스트림 분석 작업의 공개 미리 보기 릴리스에 대한 제한 사항을 알아봅니다." metaKeywords="" services="stream analytics" solutions="" documentationCenter="" authors="jgao" videoId="" scriptId="" manager="paulettm" editor="cgronlun"/>

<tags ms.service="stream-analytics" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="data-services" ms.date="10/28/2014" ms.author="jgao" />

#Azure 스트림 분석 미리 보기 제한 사항 및 알려진 문제

이 문서에서는 미리 보기 릴리스의 [Azure 스트림 분석][stream.analytics.documentation]에 대한 제한 사항 및 의 알려진 문제에 대해 설명합니다.  대부분의 경우 이러한 제한은 사용자의 초기 의견을 얻기 위해 또는 현재의 용량 제한을 기반으로 구현됩니다. 
<!--Table of contents for topic, the words in brackets must match the heading wording exactly-->

##이 문서에서는 다음을 수행합니다.
+ [제한 사항](#limitations) 
+ [릴리스 정보 및 알려진 문제](#knownissues)
+ [다음 단계]

##<a name="limitations"></a> 제한 사항

###국가별 가용성
스트림 분석 작업은 미국 중부 및 서유럽 지역에서만 미리 보기 릴리스에 프로비전될 수 있습니다.

###크기 조정 
**스트리밍 단위 할당량**

현재 용량 제약으로 인해 구독당 지역별로 12개의 스트리밍 단위 할당량이 적용됩니다. 자세한 내용은 [Azure 스트림 분석 작업 규모 지정][stream.analytics.scale.jobs]을 참조하세요. 비즈니스 요구에 따라 이러한 한도를 완화해야 할 경우 [Microsoft 지원 서비스][microsoft.support]에 문의하시면 일반 사용자 제공의 제약 조건 내에서 요구를 수용하기 위해 최선을 다할 것입니다. 

**스트리밍 단위 사용량**

이 미리 보기 릴리스에서 작업에 제공되는 스트리밍 단위 수는 경우에 따라 선택되거나 청구된 금액보다 약간 더 높을 수 있습니다.  또한 스트리밍 단위는 하향 조정되지 않습니다. 즉, 관찰된 성능이 계산 리소스의 가용성에 따라 더 높을 수 있습니다.

**파티션 키**

PARTITION BY로 쿼리를 확장하는 경우 분할할 필드는 PartitionId여야 합니다.  다른 사용자 정의 필드의 분할은 향후 릴리스에서 사용할 수 있습니다.
작업 규모 조정에 대한 자세한 내용은 [Azure 스트림 분석 작업 규모 지정][stream.analytics.scale.jobs]을 참조하세요.

###입력



**문자 인코딩**

CSV 및 JSON 입력 소스의 경우 UTF-8이 지원되는 유일한 인코딩 형식입니다.


###쿼리 복잡성
스트림 분석 작업 쿼리 정의에서 지원되는 집계 함수의 최대 수는 7입니다.

###수명 주기 관리

**작업 업그레이드**

현재 스트림 분석에서는 실행 중인 작업의 정의 또는 구성을 실시간으로 편집할 수 없습니다.  실행 중인 작업의 입력, 출력, 쿼리, 규모 또는 구성을 변경하려면 먼저 작업을 중지해야 합니다.

**작업 중지 및 다시 시작**

작업을 중지하면 작업 진행에 대한 어떤 상태도 보존되지 않습니다. 즉, 현재는 작업이 마지막으로 중지된 지점부터 작업을 다시 시작할 방법이 없습니다.  이 제한은 향후 릴리스에서 해결될 것입니다.  작업 시작 및 중지에 대한 모범 사례는 [Azure 스트림 분석 개발자 가이드][stream.analytics.developer.guide]를 참조하세요. 

###모니터링
작업 사용 현황 및 성능(예: 대기 시간)과 관련된 일부 메트릭은 미리 보기 릴리스에서 사용할 수 없습니다.  미리 보기 릴리스는 크기가 아닌 이벤트 개수에 따라서만 작업 처리량을 사용합니다.

##<a name="knownissues"></a>릴리스 정보/알려진 문제

여기에는 Azure 스트림 분석에 대한 알려진 문제 목록이 나와 있습니다. 이 섹션은 목록에서 항목을 제거하거나 새로운 문제가 발생하거나, 기존 항목을 좀 더 자세히 알아보는 과정에서 바뀔 수 있습니다.


###승격된 이벤트 허브 권한 필요
현재 스트림 분석에는 이벤트 허브 입력 및 출력 소스에 대해 관리 권한이 있는 공유 액세스 정책이 필요합니다.

###Azure 저장소 계정 구성 지연
처음에 한 지역에서 스트림 분석 작업을 만들면 해당 지역에서 스트림 분석 작업을 모니터링하기 위해 새 저장소 계정을 만들거나 기존 계정을 지정하라는 메시지가 표시됩니다.  모니터링을 구성하는 데 다소 시간이 소요되므로 동일한 지역에서 30분 이내에 다른 스트림 분석 작업을 만들면 모니터링 저장소 계정 드롭다운에 최근에 구성한 계정이 표시되지 않고 또 다른 저장소 계정을 지정하도록 요구됩니다.  불필요한 저장소 계정을 만들지 않으려면 한 지역에서 추가 작업을 프로비전하기 전에 해당 지역에서 처음 작업을 만든 후에 30분 정도 기다립니다. 

###작업에 이벤트 허브에 대한 기본 소비자 그룹 사용
이벤트 허브를 입력으로 지정하면 스트림 분석 작업은 기본 소비자 그룹을 사용하여 이벤트 허브에서 데이터를 수집합니다.  추가 구성 없이 이렇게 하면 다른 수신기가 이벤트 허브를 액세스할 수 없게 됩니다.  이벤트 허브가 둘 이상의 수신기를 보유하게 하려면 추가 소비자 그룹을 구성해야 합니다.  자세한 내용은 [이벤트 허브 개발자 가이드][azure.event.hubs.developer.guide]를 참조하세요.

###입력/출력 추가 - 이벤트 허브 
이벤트 허브 소스에 대한 입력 및 출력 추가 대화 상자의 세 번째 페이지에는 현재 구독의 서비스 버스 네임스페이스 목록과 다른 구독의 이벤트 허브에 연결하는 옵션을 포함하는 이벤트 허브라는 드롭다운이 표시됩니다.  동일한 구독의 이벤트 허브에 연결하려면 해당 서비스 버스 네임스페이스를 선택합니다.  구독 외부의 이벤트 허브에 연결하려면 "다른 구독의 이벤트 허브 사용"을 선택합니다.  


###동일한 쿼리 단계를 두 번 이상 참조할 수 없음
이 미리 보기 릴리스에서는 WITH 키워드를 사용하여 정의한 지정된 하위 쿼리 단계를 두 번 이상 참조할 수 없습니다.  이러한 참조가 영향을 줄 수 있는 일반적인 시나리오로 동일한 단계의 별칭을 사용한 셀프 조인을 들 수 있습니다.  이 동작 문제를 해결하려면 동일한 하위 쿼리 및 다른 이름을 갖는 별도의 두 단계를 만듭니다.

###지원되지 않는 형식 변환으로 인한 NULL 값 생성k
[Azure 스트림 분석 쿼리 언어 참조][stream.analytics.query.language.reference]의 데이터 형식 섹션에서 지원되지 않는 형식 변환을 사용한 이벤트 값은 NULL 값을 발생합니다.  이 미리 보기 릴리스에는 이러한 변환 예외에 대한 오류가 로깅되지 않습니다. 

###메모리 문제 부족
순서가 지정되지 않은 이벤트 및/또는 많은 양의 상태를 유지하는 복합 쿼리에 대해 허용 오차가 큰 기간을 지정해서 스트리밍 분석 작업을 수행하면 메모리가 부족하여 작업이 다시 시작됩니다. 시작 및 중지 작업은 작업의 작업 로그에 표시됩니다.  이러한 동작을 방지하려면 여러 파티션에 걸쳐 쿼리 출력 크기를 조정합니다.  향후 릴리스에서는 영향 받는 작업을 다시 시작하지 않고 성능을 저하시켜 이러한 제한 문제를 해결할 예정입니다.

###분할된 쿼리에서 빈 이벤트 허브를 공유하면 출력이 제공되지 않음
분할되지 않은 하위 쿼리가 있는 분할된 쿼리를 두 번째 단계로 실행할 경우 입력의 이벤트 허브 파티션 중 하나가 완전히 비어 있으면 쿼리는 결과를 생성하지 않습니다. 이에 대한 오류는 작업의 작업 로그에 반영됩니다.  이 문제를 방지하려면 항상 모든 이벤트 허브 파티션에 들어오는 이벤트가 있는지 확인하세요.

###SQL 데이터베이스 이벤트 볼륨 제한
SQL 데이터베이스를 출력 소스로 사용할 경우 고용량의 데이터가 있으면 스트림 분석 작업 시간이 초과될 수 있습니다. 이 문제를 해결하려면 집계 또는 조인 연산자를 사용하여 출력 볼륨을 줄이거나 Blob 저장소 또는 이벤트 허브를 출력 소스로 대신 선택하세요.

###큰 blob 입력이 지원되지 않음
Blob 저장소에 있는 큰 파일을 사용하면 스트림 분석 작업이 충돌할 수 있습니다.  이 문제를 방지하려면 각 Blob을 10MB 이하로 유지하세요.

###LEFT OUTER JOIN이 지원되지 않음
LEFT OUTER JOIN 작업은 스트림 분석 쿼리 언어에서 사용할 수 있지만 지원되지 않습니다.  몇 분 동안 LEFT OUTER JOIN을 사용하여 쿼리를 실행하면 메모리 소비 문제가 발생합니다.  수명이 짧은 쿼리 실험 외의 경우에는 이 작업을 사용하지 않는 것이 좋습니다.  이 제한은 향후 릴리스에서 처리될 예정입니다.


##<a name="nextsteps"></a>다음 단계

- [Azure 스트림 분석 소개][stream.analytics.introduction]
- [Azure 스트림 분석을 사용하여 시작][stream.analytics.get.started]
- [Azure 스트림 분석 작업 규모 지정][stream.analytics.scale.jobs]
- [Azure 스트림 분석 제한 사항 및 알려진 문제][stream.analytics.limitations]
- [Azure 스트림 분석 쿼리 언어 참조][stream.analytics.query.language.reference]
- [Azure 스트림 분석 관리 REST API 참조][stream.analytics.rest.api.reference] 

<!--Anchors-->
[제한 사항]: #Limitations
[릴리스 정보 및 알려진 문제]: #Release-notes-and-known-issues
[다음 단계]: #next-steps

<!--Image references-->
[5]: ./media/markdown-template-for-new-articles/octocats.png
[6]: ./media/markdown-template-for-new-articles/pretty49.png
[7]: ./media/markdown-template-for-new-articles/channel-9.png


<!--Link references-->

[stream.analytics.documentation]: http://go.microsoft.com/fwlink/?LinkId=512093
[stream.analytics.scale.jobs]: ../stream-analytics-scale-jobs/
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide/
[stream.analytics.introduction]: ../stream-analytics-introduction/
[stream.analytics.get.started]: ../stream-analytics-get-started/
[stream.analytics.limitations]: ../stream-analytics-limitations/


[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301


[microsoft.support]: http://support.microsoft.com/
[azure.event.hubs.developer.guide]: http://msdn.microsoft.com/ko-kr/library/azure/dn789972.aspx

[다른 azure.microsoft.com 설명서 항목에 대한 링크 1]: ../virtual-machines-windows-tutorial/
[다른 azure.microsoft.com 설명서 항목에 대한 링크 2]: ../web-sites-custom-domain-name/
[다른 azure.microsoft.com 설명서 항목에 대한 링크 3]: ../storage-whatis-account/
