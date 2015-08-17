<properties
	pageTitle="효율적인 목록 쿼리"
	description="반환되는 항목의 수를 줄일 뿐 아니라 각 항목에 대해 반환되는 정보의 양을 줄이는 방법에 대해 알아보십시오."
	services="batch"
	documentationCenter=""
	authors="davidmu1"
	manager="timlt"
	editor="tysonn"
	tags="azure-resource-manager"/>

<tags
	ms.service="multiple"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows"
	ms.workload="multiple"
	ms.date="07/28/2015"
	ms.author="davidmu"/>

# 효율적인 목록 쿼리

다음 방법은 Azure 배치를 사용하는 거의 모든 응용 프로그램이 수행해야 하고 종종 자주 수행해야 하는 작업의 예입니다.

- [ListTasks](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.iworkitemmanager.listtasks.aspx)
- [ListJobs](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.iworkitemmanager.listjobs.aspx)
- [ListWorkitems](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.iworkitemmanager.listworkitems.aspx)
- [ListPools](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.ipoolmanager.listpools.aspx)
- [ListCertificates](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.icertificatemanager.listcertificates.aspx)

모니터링은 일반적인 사용 사례입니다. 예를 들어, 용량 및 풀(pool) 상태를 확인하는 작업은 모든 풀 VM을 쿼리할 수 있어야 합니다. 또 다른 예는 작업이 큐에 대기 중인지 확인하기 위해 태스크에 대해 쿼리하는 것입니다. 일부 경우 많은 데이터 세트가 필요하지만 다른 경우에는 총 항목 수 또는 특정 상태의 항목 수만 필요합니다.

반환될 수 있는 항목 수가 매우 클 수 있고 항목의 목록을 나타내는 데 필요한 데이터의 크기도 매우 클 수 있다는 것을 아는 것이 중요합니다. 단순히 많은 응답이 발생하는 많은 수의 항목에 대해 쿼리하는 것은 다음과 같이 많은 문제를 발생시킬 수 있습니다.

- 배치 API 응답 시간이 너무 느려질 수 있습니다. 항목 수가 많을수록 배치 서비스에서 필요한 쿼리 시간도 길어집니다. 많은 수의 항목을 청크로 나누어야 하며 이에 따라 여러 서비스 API 호출은 한 목록의 모든 항목을 얻기 위해 서비스에 대한 클라이언트 라이브러리에 의해 이루어져야 합니다.
- 응용 프로그램 호출 배치에 의한 API 처리는 여러 항목을 처리하는 경우보다 시간이 오래 걸립니다.
- 많은 항목 및/또는 큰 항목이 있는 경우 애플리케이션 호출 배치가 더 많은 메모리를 사용합니다.
- 많은 항목 및/또는 큰 항목은 네트워크 트래픽 증가로 이어질 수 있습니다. 이렇게 되면 전송 시간이 오래 걸리며, 응용 프로그램 아키텍처에 따라 배치 계정의 지역 외부에서 전송되는 데이터에 대해 네트워크 요금이 증가할 수 있습니다.

배치 API는 목록에서 반환되는 항목의 수를 줄일 뿐 아니라 각 항목에 대해 반환되는 정보의 양을 줄이는 기능을 제공합니다. [DetailLevel](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.detaillevel.aspx) 형식의 매개 변수는 목록 작업에 대해 지정할 수 있습니다. DetailLevel은 추상 기본 클래스이며 실제로 [ODATADetailLevel](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.aspx) 개체를 만들어 매개 변수로 전달해야 합니다.

모든 API의 경우 다음이 적용됩니다.

- 각 속성 이름은 개체의 속성에 매핑되는 문자열입니다.
- 모든 속성 이름은 대/소문자를 구분하지만, 속성 값은 대/소문자를 구분하지 않습니다.
- 날짜/시간 문자열은 두 형식 중 하나를 가질 수 있으며 DateTime으로 시작해야 합니다.
	- W3CDTF(예: creationTime gt DateTime'2011-05-08T08:49:37Z')
	- RFC1123(예: creationTime gt DateTime'Sun, 08 May 2011 08:49:37 GMT’)
- 부울 문자열은 "true" 또는 "false"입니다.
- 잘못된 속성이나 연산자는 지정된 경우 예외는 "400 (잘못 된 요청)" 내부 예외와 함께 생성됩니다.
- Select 및 Expand 절이 포함된 DetailLevel 매개 변수는 IPoolManager.GetPool()와 같이 적절한 "Get" 메서드로 전달될 수도 있습니다.

ODataDetailLevel 개체에는 생성자 내에 지정되어 있거나 직접 설정된 3개의 공용 속성이 있습니다. 세 속성은 모두 다음과 같은 문자열입니다.

- [FilterClause](#filter) – 필터링하여 반환되는 항목의 수를 줄일 수 있음
- [SelectClause](#select) – 반환되는 특정 속성 값을 지정하여, 항목 및 응답 크기를 줄임
- [ExpandClause](#expand) – 다수의 호출과는 달리 한 번의 호출로 필요한 모든 데이터를 반환

### <a id="filter"></a> FilterClause

필터 문자열에 의해 반환되는 항목 수를 줄일 수 있습니다. 필요한 항목만 반환되도록 하나 이상의 속성 값을 지정할 수 있습니다. 예를 들어, 활성 작업 항목만 나열하는 경우, 작업의 실행 중인 태스크만 나열하는 경우, 태스크를 실행할 준비가 된 VM만 나열하는 경우가 있습니다.

[FilterClause](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.filterclause.aspx)는 하나 이상의 식으로 구성된 문자열로, 이 식은 속성 이름, 연산자 및 값으로 이루어져 있습니다. 지정할 수 있는 속성은 각 API 호출에 각 속성에 대해 고유하며 각 속성에 대해 지원되는 연산자도 마찬가지입니다. 논리 연산자 "And" 및 "Or"를 사용하여 여러 식을 결합할 수 있습니다.

예를 들어, 태스크를 나열하기 위한 필터는 다음과 같습니다.

	startswith(name, 'MyTask') and (state eq 'Running')

### <a id="select"></a> SelectClause

Select 문자열을 사용하여 각 항목에 대해 반환되는 속성 값을 제한할 수 있습니다. 항목에 대한 속성 목록을 지정하면 이들 속성 값만 반환됩니다.

[SelectClause](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.selectclause.aspx)는 속성 이름을 쉼표로 구분한 목록으로 구성된 문자열입니다. 목록 작업에 의해 반환된 항목의 모든 속성을 지정할 수 있습니다.

	"name, state, stateTransitionTime"

### <a id="expand"></a> ExpandClause

확장 문자열로 API 호출 수를 줄일 수 있습니다. 목록을 얻은 다음 목록의 각 항목에 대한 호출을 수행하는 것과는 달리, 하나의 목록 API 호출을 통해 각 목록 항목에 대한 자세한 정보를 얻을 수 있습니다.

[ExpandClause](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.expandclause.aspx)는 Select 절과 유사하며 Expand 절은 특정 데이터를 결과에 반환할지 여부를 제어합니다. Expand 절은 작업 항목 목록, 작업 목록, 풀 목록 및 태스크 목록에 대해서만 지원되며, 현재는 통계 정보만 지원합니다. 모든 속성이 필요하고 Select 절이 없는 경우, Expand 절은 통계 정보를 얻는 데 사용되어야 합니다. Select 절이 속성의 하위 집합을 얻는 데 사용되는 경우 통계는 Select 절에 지정할 수 있으며 Expand 절은 null 상태로 남겨 둘 수 있습니다.

> [AZURE.NOTE]최대의 효율성과 응용 프로그램의 최고 성능을 위해 목록 API 호출에 위해 항상 필터와 Select 절을 사용하는 것이 좋습니다.

<!---HONumber=August15_HO6-->