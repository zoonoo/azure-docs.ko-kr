<properties 
	pageTitle="효율적인 목록 쿼리" 
	description="목록에 반환 되는 항목의 수를 줄일 하는 방법을 배울 수 있을 뿐아니라 아니라 각 항목에 대해 반환 되는 정보의 양을 줄이고합니다" 
	services="batch" 
	documentationCenter="" 
	authors="davidmu1" 
	manager="timlt" 
	editor="tysonn"/>

<tags 
	ms.service="multiple" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="vm-windows" 
	ms.workload="multiple"
	ms.date="05/09/2015" 
	ms.author="davidmu"/>

# 효율적인 목록 쿼리

다음 메서드는 거의 모든 응용 프로그램을 Azure 일괄 처리를 사용 하 여 수행할 수 있으며 자주 수행 해야하는 경우가 많습니다 있는 작업의 예:

- [ListTasks](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.iworkitemmanager.listtasks.aspx)
- [ListJobs](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.iworkitemmanager.listjobs.aspx)
- [ListWorkitems](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.iworkitemmanager.listworkitems.aspx)
- [ListPools](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.ipoolmanager.listpools.aspx)
- [ListCertificates](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.icertificatemanager.listcertificates.aspx)

모니터링은 일반적인 사용 사례입니다. 용량 및 풀의 상태를 확인 합니다. 모든 풀 VM 선의 예 쿼리할 수 있어야 합니다. 다른 예제 쿼리를 확인 하는 경우 모든 작업은 큐에 대기 중인 작업에 대 한 작업 하는 것입니다. 풍부한 데이터의 경우에 따라 필요는 있지만 다른 경우에만 항목 또는 특정 상태에 대 한 항목의 총 수가 필요 합니다.

반환 될 수 있는 항목 수가 매우 클 수 하 고 항목의 목록을 나타내는 데 필요한 데이터의 크기 또한 매우 클 수 있다는 것은 단순히 많은 큰 응답에서 결과 다양 한 문제가 발생할 수 있는 항목에 대 한 쿼리:

- 일괄 처리 API 응답 시간이 너무 느려 될 수 있습니다. 클수록 긴 항목 수가 쿼리 시간 서비스에 필요한 일괄 처리 합니다. 많은 수의 항목이 청크로 나눌 수 있고 따라서 여러 서비스 API를 호출 수행 하기 위해 클라이언트 라이브러리는 서비스에 하나의 목록에 대 한 모든 항목을 가져오려면 가질 수 있습니다.
- 호출한 일괄 처리 시간이 오래 걸립니다. 응용 프로그램에 의해 처리 되는 API 더 많은 항목에 있는 프로세스가 되도록 합니다.
- 더 많은 메모리를 이용 하 여 더 많은 항목 및/또는 더 큰 항목으로 일괄 처리를 호출 하는 응용 프로그램에 게 됩니다.
- 더 많은 항목 및/또는 더 큰 항목 네트워크 트래픽을 증가 이어질 수 있습니다. 이 전송 하는 더 오래 걸립니다 하 고, 응용 프로그램 아키텍처에 따라 일괄 처리 계정의 지역 외부에서 전송 되는 데이터에 대 한 향상 된 네트워크 요금에 발생할 수 있습니다.

일괄 처리 API 모두 목록에 반환 되는 항목의 수를 줄일 뿐아니라 각 항목에 대해 반환 되는 정보의 양을 줄이고 하는 기능을 제공 합니다. 형식의 매개 변수가 [DetailLevel](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.detaillevel.aspx) 목록 작업에 지정할 수 있습니다. DetailLevel 클래스는 추상 기본 클래스 및 [ODATADetailLevel](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.aspx) 개체를 만들어 매개 변수로 전달 실제로 필요 합니다.

모든 API의 경우 다음이 적용 됩니다이 대 한:

- 각 속성 이름은 개체의 속성에 매핑되는 문자열입니다.
- 모든 속성 이름은 대/소문자 구분, 이지만 속성 값은 대/소문자
- 날짜/시간 문자열 두 형식 중 하나를 가질 수 및 날짜/시간으로 앞에 필요한
	- W3CDTF (예: creationTime gt DateTime'2011-05-08T08:49:37Z')
	- RFC1123 (예: creationTime gt DateTime'Sun, 2011 년 5 월 8 08시 49분: 37 GMT')
- 부울 문자열은 "true" 또는 "false"
- 예외는 "400 (잘못 된 요청)"으로 생성 됩니다 잘못 된 속성이 나 연산자는 지정 된 경우 내부 예외입니다.
- "Get" 메서드와; 적절 한에 Select 및 Expand 절 DetailLevel 매개 변수를 전달할 수도 있습니다. 예: IPoolManager.GetPool()

ODataDetailLevel 개체에는 세가지 공용 속성 중 하나 하거나 실행할 수 있는 생성자에 지정 된 직접 설정 합니다. 세 속성은 모든 문자열.

- [FilterClause](#filter) – 필터링 하 고 잠재적으로 반환 되는 항목의 수를 줄일
- [SelectClause](#select) – 항목 및 응답 크기를 줄이면 반환 되는 특정 속성 값을 지정 합니다.
- [ExpandClause](#expand) – 여러 번 호출 하는 대신 한번 호출에 필요한 모든 데이터를 반환 합니다.

### <a id="filter"></a> FilterClause입니다

필터 문자열에 의해 반환 되는 항목 수를 줄일 수 있습니다. 하나 이상의 속성 값은 필요한 항목만 반환 되도록 지정할 수 있습니다. 예 유일한 활성 작업 항목 나열을 실행 한 작업에 대 한 작업에만 목록에만 VM의 작업을 실행할 준비가 된 나열 합니다.

A [FilterClause](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.filterclause.aspx) 는 문자열로, 속성 이름, 연산자 및 값으로 이루어진 하는 식으로 하나 이상의 식으로 구성 됩니다. 각 속성에 대해 지원 되는 연산자는 지정할 수 있는 속성 각 API 호출 관련 됩니다. 논리 연산자를 사용 하 여 여러 식을 결합할 수 "및" 및 "또는".

예를 나열 하는 작업에 대 한 필터 수 있습니다.

	startswith(name, 'MyTask') and (state eq 'Running')

### <a id="select"></a> SelectClause

Select 문자열을 사용 하 여 각 항목에 대해 반환 되는 속성 값을 제한할 수 있습니다. 항목에 대 한 속성의 목록이 지정할 수 있으며 그런 다음 해당 속성 값만 반환 됩니다.

A [SelectClause](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.selectclause.aspx) 는 속성 이름의 쉼표로 구분 된 목록으로 구성 된 문자열입니다. 목록 작업에 의해 반환 되는 항목에 있는 모든 속성을 지정할 수 있습니다.

	"name, state, stateTransitionTime"

### <a id="expand"></a> ExpandClause

확장 문자열에서 API 호출 수를 줄일 수 있습니다. 다음 목록의 각 항목에 대 한 호출을 수행 하 고 목록을 얻는 대신 하나의 목록 API 호출을 통해 각 목록 항목에 대 한 자세한 정보를 얻을 수 있습니다.

 [ExpandClause](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.expandclause.aspx) 는 특정 데이터는 결과에 반환 하는지 여부를 Select 절, Expand 절 컨트롤와 비슷합니다. Expand 절은 작업 항목 목록, 작업 목록, 풀 목록 및 작업 목록에만 지원 현재만 지원 하므로 통계 정보. 모든 속성은 필요한 때 select 절 있으면, expand 절이 통계 정보를 얻으려면 사용 해야 합니다. Select 절을 사용 하 여 가져올 속성의 하위 집합을 하는 경우 select 절에 통계를 지정할 수 있으며 다음와 expand 절은 null 남아 있을 수 있습니다.

> [AZURE.NOTE]항상 필터를 사용 하는 최대의 효율성을 확인 하 여 목록 API 호출에 대 한 절과 응용 프로그램에 대 한 최상의 성능을 선택 것이 좋습니다.

<!---HONumber=GIT-SubDir-->