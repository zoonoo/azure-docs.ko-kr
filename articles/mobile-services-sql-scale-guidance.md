<properties 
	pageTitle="Azure SQL 데이터베이스에서 지원되는 모바일 서비스 크기 조정 - Azure 모바일 서비스" 
	description="SQL 데이터베이스에서 지원하는 모바일 서비스에서 확장성 문제를 진단 및 해결하는 방법에 대해 알아봅니다." 
	services="mobile-services" 
	documentationCenter="" 
	authors="lindydonna" 
	manager="dwrede" 
	editor="mollybos"/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="11/11/2014" 
	ms.author="donnam"/>
# Azure SQL 데이터베이스에서 지원되는 모바일 서비스 크기 조정

Azure 모바일 서비스를 사용하면 작업을 매우 쉽게 시작하고 SQL 데이터베이스에 데이터를 저장하는 클라우드 호스팅 백 엔드에 연결되는 앱을 쉽게 작성할 수 있습니다. 앱이 커지면 포털에서 계산 및 네트워킹 용량을 추가하여 크기 조정 설정을 조정하는 것처럼 간단하게 서비스 인스턴스 크기를 조정할 수 있습니다. 하지만 서비스 지원 SQL 데이터베이스를 크기 조정하기 위해서는 서비스의 부하 증가에 따라 사전적인 일부 계획과 모니터링이 필요합니다. 이 문서에서는 SQL 지원 모바일 서비스의 지속적인 성능 향상을 보장하기 위한 몇 가지 모범 사례에 대해 설명합니다.

이 항목에서는 다음 기본 섹션을 안내합니다.

1. [문제 진단](#Diagnosing)
2. [인덱싱](#Indexing)
3. [스키마 디자인](#Schema)
4. [쿼리 디자인](#Query)
5. [서비스 아키텍처](#Architecture)
6. [고급 문제 해결](#Advanced)

<a name="Diagnosing"></a>
## 문제 진단

모바일 서비스에서 로드 중 문제가 발생한다고 의심될 경우 먼저 확인할 곳은 [Azure 관리 포털][]에서 서비스에 대한 **대시보드** 탭입니다. 여기에서 확인할 일부 항목은 다음과 같습니다.

- **API 호출** 및 **활성 장치** 측정기를 포함하는 사용량 측정기가 할당량을 넘지 않는지 확인합니다.
- **끝점 모니터링** 상태는 서비스가 작동 중인지를 나타냅니다(서비스에서 표준 계층을 사용 중이고 끝점 모니터링이 활성화된 경우에만 사용 가능). 

위 조건이 충족되지 않을 경우 *Scale* 탭에서 크기 조정 설정을 조정해야 할 수 있습니다. 그래도 문제가 해결되지 않으면 계속해서 Azure SQL 데이터베이스가 문제의 원인인지 여부를 조사할 수 있습니다. 다음 몇 개의 섹션에서는 발생한 문제를 진단하기 위한 몇 가지 서로 다른 접근 방식에 대해 설명합니다.

### 올바른 SQL 데이터베이스 계층 선택 

현재 앱 요구 사항에 맞게 올바른 계층을 선택했는지 확인하기 위해서는 사용자가 선택한 여러 데이터베이스 계층에 대한 이해가 중요합니다. Azure SQL 데이터베이스는 여러 계층에서 다음 두 가지 데이터베이스 버전을 제공합니다.

- Web 및 Business 버전(사용 중지됨)
- Basic, Standard 및 Premium Edition 

Web 및 Business Edition은 현재 완전히 지원되지만 [Web 및 Business Edition 지원 종료 FAQ](http://msdn.microsoft.com/library/azure/dn741330.aspx)에 설명한 대로 2015년 4월 24일에 지원이 종료됩니다. 새 고객에게 이러한 변화를 준비하면서 Basic, Standard 및 Premium Edition을 사용할 것을 권장합니다. 이 새로운 버전에서는 데이터베이스 성능 문제를 이해하고 더 쉽게 해결할 수 있게 해주는 다양한 새로운 계층 및 모니터링 기능이 제공됩니다. 모든 새 모바일 서비스는 새 Edition을 사용하여 생성됩니다.

Web 및 Business Edition부터 Basic, Standard 및 Premium Edition을 사용하여 모바일 서비스를 변환하려면 다음 단계를 따르세요.

1. [Azure 관리 포털][]을 실행합니다.
2. 도구 모음에서 **+NEW**를 선택한 후 **데이터 서비스**, **SQL 데이터베이스**, **빠른 생성**을 선택합니다.
3. 데이터베이스 이름을 입력한 후 **서버** 필드에서 **새 SQL 데이터베이스 서버**를 선택합니다. 그러면 새로운 Basic, Standard 및 Premium Edition을 사용하는 서버가 생성됩니다. 
4. 나머지 필드를 채우고 **SQL 데이터베이스 만들기**를 선택합니다. 그러면 Basic 계층을 사용하여 100MB 데이터베이스가 생성됩니다.
5. 바로 전에 만든 데이터베이스를 사용하도록 모바일 서비스를 구성합니다. 해당 서비스에 대한 **구성** 탭으로 이동한 후 도구 모음에서 **데이터베이스 변경**을 선택합니다. 다음 화면의 **SQL 데이터베이스** 필드에서 **기존 SQL 데이터베이스 사용**을 선택한 후 **다음**을 선택합니다. 다음 화면에서 5단계에서 만든 데이터베이스를 선택했는지 확인한 후 **확인**을 선택합니다.

데이터베이스에 대한 적합한 계층을 선택할 때의 몇 가지 권장 사항은 다음과 같습니다.

- **Basic** - 개발 시 또는 단일 데이터베이스 쿼리가 한 번에 하나만 예상되는 소규모 프로덕션 서비스의 경우 사용합니다.
- **Standard** - 여러 개의 동시 데이터베이스 쿼리가 예상되는 프로덕션 서비스의 경우 사용합니다.
- **Premium** - 동시 쿼리 수가 많고, 최대 로드가 높고, 모든 요청에 대해 낮은 대기 시간이 필요한 대규모 프로덕션 서비스의 경우 사용합니다.

각 계층의 사용 시기에 대한 자세한 내용은 [새 서비스 계층을 사용해야 하는 이유](http://msdn.microsoft.com/library/azure/dn369873.aspx#Reasons)를 참조하세요.

### 데이터베이스 메트릭 분석

여러 데이터베이스 계층에 익숙해진 다음에는 계층 간 및 계층 내 크기 조정에 대한 이유를 알 수 있게 해주는 데이터베이스 성능 메트릭에 대해 알아볼 수 있습니다.

1. [Azure 관리 포털][]을 실행합니다.
2. 모바일 서비스 탭에서 사용하려는 서비스를 선택합니다.
3. **구성** 탭을 선택합니다.
4. **데이터베이스 설정** 섹션에서 **SQL 데이터베이스**를 선택합니다. 그러면 포털의 Azure SQL 데이터베이스 탭으로 이동됩니다.
5. **모니터** 탭으로 이동합니다.
6. **메트릭 추가** 단추를 사용하여 관련 메트릭이 표시되는지 확인합니다. 여기에는 다음이 포함됩니다.
    - *CPU 비율*(Basic/Standard/Premium 계층에서만 사용 가능)
    - *물리적 데이터 읽기 비율*(Basic/Standard/Premium 계층에서만 사용 가능) 
    - *로그 쓰기 비율*(Basic/Standard/Premium 계층에서만 사용 가능)
    - *저장소* 
7. 서비스에 문제가 발생한 시기의 시간 창에서 메트릭을 조사합니다. 

    ![Azure Management Portal - SQL Database Metrics][PortalSqlMetrics]

특정 메트릭의 사용률이 장시간 동안 80%를 초과할 경우 이는 성능 문제를 나타낼 수 있습니다. 데이터베이스 사용률 이해에 대한 자세한 내용은 [리소스 사용 이해](http://msdn.microsoft.com/library/azure/dn369873.aspx#Resource)를 참조하세요.

메트릭으로 데이터베이스 사용량이 높게 나타나면 첫 번째 해결 단계로 **데이터베이스를 더 높은 서비스 계층으로 크기 조정**해야 할 수 있습니다. 문제를 즉시 해결하기 위해서는 데이터베이스에 대한 **크기 조정** 탭을 사용하여 데이터베이스 크기를 늘려야 합니다. 이로 인해 청구 요금이 증가하게 됩니다.
![Azure Management Portal - SQL Database Scale][PortalSqlScale]

가능한 한 빠른 시간 내에 다음과 같이 추가적인 완화 단계를 수행합니다.

- **데이터베이스를 튜닝합니다.**
  데이터베이스를 최적화하면 데이터베이스 사용량을 줄이고 더 큰 계층으로 확장할 필요가 없는 경우가 많습니다. 
- **서비스 아키텍처를 고려합니다.**
  서비스 로드는 시간별로 고르게 분산되지 않고 "갑작스러운" 요구량 증가가 발생하는 경우가 많습니다. 이러한 갑작스러운 증가를 처리하기 위해 데이터베이스를 확장하여, 수요가 낮을 때 활용률 저하를 겪는 대신 서비스 아키텍처를 조정하여 그러한 증가 문제를 방지하거나 데이터베이스 적중 없이 이를 처리할 수 있는 경우가 많습니다.

이 문서의 남은 섹션에서는 이러한 완화 노력을 구현하는 데 도움이 되는 세부적인 지침을 제공합니다.


### 경고 구성

리소스 소진 시 대응할 시간을 충분히 확보할 수 있도록 사전에 중요 데이터베이스 메트릭에 대한 경보를 구성하는 것이 유용한 경우가 많습니다. 

1. 경고를 설정하려는 데이터베이스의 **모니터링** 탭으로 이동합니다.
2. 이전 섹션에 설명한 것처럼 관련 메트릭이 표시되는지 확인합니다.
3. 경고에 대해 설정한 메트릭을 선택하고 **규칙 추가**를 선택합니다.
    ![Azure Management Portal - SQL Alert][PortalSqlAddAlert]
4. 경고의 이름 및 설명을 제공합니다.
    ![Azure Management Portal - SQL Alert Name and 설명][PortalSqlAddAlert2]
5. 경고 임계값으로 사용할 값을 지정합니다. 반응 시간을 일부 허용하려면 **80%** 정도로 사용하는 것이 좋습니다. 또한 적극적으로 모니터링하는 메일 주소를 지정해야 합니다. 
    ![Azure Management Portal - SQL Alert Threshold and Email][PortalSqlAddAlert3]

SQL 문제 진단에 대 한 자세한 내용은 이 문서의 맨 아래에 있는 [고급 진단](#AdvancedDiagnosing) 을 참조하세요.

<a name="Indexing"></a>
## 인덱싱

쿼리 성능 관련 문제가 발견될 때 가장 먼저 조사해야 하는 것은 인덱스 디자인입니다. 인덱스는 SQL 엔진의 쿼리 실행에 직접적인 영향을 주기 때문에 중요합니다. 

예를 들어, 특정 필드를 기준으로 요소를 조회할 경우가 많으면, 해당 열에 대한 인덱스를 추가할 것을 고려해야 합니다. 그렇지 않으면 SQL 엔진이 테이블 스캔을 수행하고 각 물리적 레코드(또는 최소한 쿼리 열)를 읽어야 하며, 레코드가 디스크 전체에 분포되어 있을 수 있습니다.

따라서 특정 열에 대해 WHERE 또는 JOIN 문을 자주 수행할 때는 이를 인덱싱하는 것이 중요합니다. 자세한 내용은 [인덱스 만들기](#CreatingIndexes) 를 참조하세요.

인덱스가 좋고 테이블 스캔이 나쁘다면 안전을 위해 테이블의 모든 열을 인덱싱해야 할까요?  간단히 말하자면, "그렇지 않습니다." 인덱스는 공간을 차지하고 자체 오버헤드가 있으므로 테이블에서 삽입이 수행될 때마다 인덱싱된 각 열에 대한 인덱스 구조를 업데이트해야 합니다. 열 인덱스를 선택하는 방법에 대한 지침은 아래를 참조하세요.

### 인덱스 디자인 지침

위에서 설명한 것처럼 인덱스는 그 자체로 성능 및 저장소 오버헤드 면에서 비용이 높을 수 있기 때문에 테이블에 인덱스를 추가하는 것이 항상 올바른 선택이 될 수는 없습니다.

#### 쿼리 고려 사항

- 조건자(예: WHERE 절) 및 조인 조건에서 자주 사용되는 열에는 인덱스를 추가하지만 아래와 같이 데이터베이스 균형을 고려해야 합니다.
- 여러 개의 쿼리를 사용하여 동일한 행을 업데이트하는 대신 단일 문을 사용하여 가능한 한 많은 행을 삽입 또는 수정하는 쿼리를 작성합니다. 문이 하나만 있으면 데이터베이스 엔진이 유지 관리할 인덱스 수를 보다 효과적으로 최적화할 수 있습니다.
	
#### 데이터베이스 고려 사항

한 테이블에 인덱스 수가 많을 경우에는 테이블의 데이터가 변경될 때 모든 인덱스를 조정해야 하기 때문에 INSERT, UPDATE, DELETE 및 MERGE 문의 성능에 영향을 줍니다.

- **매우 자주 업데이트되는** 테이블의 경우, 자주 업데이트되는 열에 대해서는 인덱싱하지 않는 것이 좋습니다.
- **자주 업데이트되지 않지만** 대량의 데이터를 포함하고 있는 테이블의 경우에는 인덱스를 많이 사용하는 것이 좋습니다. 그러면 쿼리 최적화 프로그램에 최상의 액세스 방법을 찾을 수 있는 옵션이 더 많이 제공되므로 데이터를 수정하지 않는 쿼리(예: SELECT 문)의 성능이 향상될 수 있습니다.

작은 테이블의 경우에는 간단한 테이블을 검색하는 것보다 쿼리 최적화 프로그램에서 데이터 인덱스 검색을 트래버스하느라 시간이 더 오래 걸릴 수 있으므로 인덱싱하는 것이 좋지 않을 수 있습니다. 따라서 작은 테이블에서는 인덱스가 전혀 사용되지 않을 수 있지만 테이블 데이터의 변경에 따라 여전히 유지 관리해야 합니다.


<a name="CreatingIndexes"></a>
### 인덱스 만들기

#### JavaScript 백 엔드

JavaScript 백 엔드에 있는 열에 대해 인덱스를 설정하려면 다음을 수행합니다.

1. [Azure 관리 포털][]에서 모바일 서비스를 엽니다.
2. **데이터** 탭을 클릭합니다.
3. 수정하려는 테이블을 선택합니다.
4. **열** 탭을 클릭합니다.
5. 열을 선택합니다. 명령 모음에서 **인덱스 설정**을 클릭합니다.

	![Mobile Services Portal - Set Index][SetIndexJavaScriptPortal]

또한 이 뷰 내에서 인덱스를 제거할 수도 있습니다.

#### .NET 백 엔드

Entity Framework에서 인덱스를 정의하려면 인덱싱하려는 필드에서 `[Index]` 특성을 사용합니다. 예를 들면 다음과 같습니다.

    public class TodoItem : EntityData
    {
        public string Text { get; set; }

		[Index]
        public bool Complete { get; set; }
    }
		 
인덱스에 대한 자세한 내용은 [Entity Framework의 인덱스 주석][]을 참조하세요. 인덱스 최적화에 대한 추가 팁은 이 문서의 맨 아래에 있는 [고급 인덱싱](#AdvancedIndexing) 을 참조하세요.

<a name="Schema"></a>
## 스키마 디자인

다음은 SQL 데이터베이스의 스키마로 변환되는 개체에 대한 데이터 유형을 선택할 때 알고 있어야 하는 몇 가지 문제에 대한 설명입니다. SQL은 여러 데이터 유형에 대한 인덱싱 및 저장소 처리를 위해 최적화된 사용자 지정 방식이 있기 때문에 스키마를 튜닝하면 상당한 성능 개선 효과를 볼 수 있는 경우가 많습니다.

- **제공된 ID 열을 사용합니다**. 모든 모바일 서비스 테이블에는 기본 키로 구성된 기본 ID 열이 포함되며, 여기에 인덱스가 설정되어 있습니다. 추가 ID 열을 만들 필요는 없습니다.
- **모델에서 올바른 데이터 형식을 사용합니다.** 모델의 특정 속성이 숫자 또는 부울임을 알고 있는 경우 모델에서 문자열 대신 해당 형식으로 정의해야 합니다. JavaScript 백 엔드에서는 `"true"` 대신 `true`, `"5"` 대신 `5`와 같은 리터럴을 사용합니다. .NET 백 엔드에서는 모델의 속성을 선언할 때 `int` 및 `bool`을 사용합니다. 이렇게 하면 SQL에서 해당 형식에 올바른 스키마를 만들어 쿼리를 보다 효율적으로 만들 수 있습니다.  

<a name="Query"></a>
## 쿼리 디자인

다음은 데이터베이스를 쿼리할 때 고려해야 할 몇 가지 지침입니다.

- **항상 데이터베이스에서 조인 작업을 실행 합니다.** 결합할 레코드가 공통 필드를 공유하는 둘 이상의 테이블에서 레코드를 결합해야 합니다( *join*이라고도 함). 이 작업은 두 테이블의 모든 항목을 아래로 끈 다음 모든 항목을 반복하므로 잘못 수행될 경우 비효율적일 수 있습니다. 이런 종류의 작업은 데이터베이스 자체에서 유지되는 것이 가장 좋지만 실수로 클라이언트나 모바일 서비스 코드에서 수행되는 경우도 가끔 있습니다.
    - 앱 코드에서는 조인을 수행하지 마세요.
    - 모바일 서비스 코드에서 조인을 수행하지 마세요. JavaScript 백 엔드를 사용할 때는 [테이블 개체](http://msdn.microsoft.com/library/windowsazure/jj554210.aspx)가 조인을 처리하지 않습니다. 조인이 데이터베이스에서 수행되도록 [mssql 개체](http://msdn.microsoft.com/library/windowsazure/jj554212.aspx)를 직접 사용하세요. 자세한 내용은 [관계형 테이블 조인](http://azure.microsoft.com/documentation/articles/mobile-services-how-to-use-server-scripts/#joins)을 참조하세요. .NET 백 엔드를 사용하고 LINQ를 통해 쿼리할 때는 데이터베이스 수준에서 Entity Framework에 의해 조인이 자동으로 처리됩니다.
- **페이징을 구현 합니다.** 데이터베이스를 쿼리하면 많은 수의 레코드가 클라이언트로 반환되는 경우가 있을 수 있습니다. 작업의 크기 및 대기 시간을 최소화하려면 페이징을 구현하는 것이 좋습니다.
    - 기본적으로 모바일 서비스는 모든 들어오는 쿼리를 페이지 크기 50으로 제한하며, 사용자는 최대 1,000개까지 레코드를 수동으로 요청할 수 있습니다. 자세한 내용은 [Windows 스토어](http://azure.microsoft.com/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library/#paging), [iOS](http://azure.microsoft.com/documentation/articles/mobile-services-ios-how-to-use-client-library/#paging), [Android](http://azure.microsoft.com/documentation/articles/mobile-services-android-how-to-use-client-library/#paging), [HTML/JavaScript](http://azure.microsoft.com/documentation/articles/mobile-services-html-how-to-use-client-library/#paging) 및 [Xamarin](http://azure.microsoft.com/documentation/articles/partner-xamarin-mobile-services-how-to-use-client-library/#paging)에 대해 "페이지에서 데이터 반환"을 참조하세요.
    - 모바일 서비스 코드에서 생성되는 쿼리에 대해서는 기본 페이지 크기가 없습니다. 앱에서 페이징이 구현되지 않을 경우 또는 방어적인 수단으로서 쿼리에 대한 기본 제한을 적용할 수 있습니다. JavaScript 백 엔드에서는 [쿼리 개체](http://msdn.microsoft.com/library/azure/jj613353.aspx)에 대해 **take** 연산자를 사용합니다. .NET 백 엔드를 사용할 경우에는 LINQ 쿼리의 일부로 [Take 메서드](http://msdn.microsoft.com/library/vstudio/bb503062(v=vs.110).aspx)를 사용할 수 있습니다.  

쿼리 계획을 분석하는 방법을 비롯하여 쿼리 디자인 향상에 대한 자세한 내용은 이 문서의 맨 아래에 있는 [고급 쿼리 디자인](#AdvancedQuery) 을 참조하세요.

<a name="Architecture"></a>
## 서비스 아키텍처

앱에서 새로운 콘텐츠를 확인하라고 모든 고객에게 푸시 알림을 보내려는 시나리오를 가정해 보세요. 이때 사용자가 알림을 탭하면 앱이 실행되고 모바일 서비스에 대한 호출 및 SQL 데이터베이스에 대한 쿼리 실행이 트리거될 것입니다. 단 몇 분 이내에 수백만 명의 고객이 이 작업을 수행한다면 앱의 평균 상태 로드보다 훨씬 높게 SQL 로드가 증가할 것입니다. 이 문제를 해결하기 위해서는 부하가 급증되는 시간 동안 앱을 상위 SQL 계층으로 확장한 후 다시 축소시킬 수 있지만 이러한 방식은 수동 개입이 필요하고 비용 증가로 이어질 수 있습니다. 모바일 서비스 아키텍처를 자주 조금만 조정하면 클라이언트가 SQL 데이터베이스에 미치는 로드를 균형적으로 조정하고 문제가 되는 갑작스러운 수요 증가 문제를 없앨 수 있습니다. 이러한 수정은 고객 환경에 대한 최소한의 영향만으로 쉽게 구현할 수 있는 경우가 많습니다. 다음은 몇 가지 예제입니다.

- **시간에 따라 부하를 분산시킵니다.** 급격한 수요 증가가 예상되는 특정 이벤트(예: 브로드캐스트 푸시 알림)의 타이밍을 제어하고 해당 이벤트의 타이밍이 중요 하지 않은 경우 시간에 따라 분산시키는 것이 좋습니다. 위의 예에서 앱 고객은 거의 동시에가 아니라 하루 동안 일괄 처리로 새 앱 콘텐츠에 대한 알림을 받을 수 있습니다. 각 일괄 처리에 시차를 적용하여 전달할 수 있는 그룹으로 고객을 일괄 처리하는 것이 좋습니다. 알림 허브를 사용하고 추가 태그를 사용하여 일괄 처리를 추적한 다음 해당 태그에 푸시 알림을 제공하면 이 전략을 구현하는 쉬운 방법을 제공합니다. 태그에 대한 자세한 내용은 [알림 허브를 사용하여 속보 보내기](http://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-send-breaking-news/)를 참조하세요.
- **해당될 때마다 Blob 및 테이블 저장소를 사용합니다.** 부하가 급증하는 시간 동안 고객이 보는 콘텐츠가 매우 정적이어서 해당 콘텐츠에 대해 관계형 쿼리 기능이 필요할 것 같지 않으므로 SQL 데이터베이스에 저장할 필요가 없는 경우가 자주 있습니다. 이런 경우 Blob이나 테이블 저장소에는 콘텐츠를 저장하는 것이 좋습니다. 장치에서 직접 Blob 저장소의 공용 Blob에 액세스할 수 있습니다. 안전한 방식으로 Blob에 액세스하거나 테이블 저장소를 사용하려면 모바일 서비스 사용자 지정 API를 통해 저장소 액세스 키를 보호해야 합니다. 자세한 내용은 [모바일 서비스를 사용하여 Azure 저장소에 이미지 업로드](http://azure.microsoft.com/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-upload-data-blob-storage/)를 참조하세요.
- **메모리 내 캐시를 사용합니다**. 또 다른 방법은 트래픽이 크게 증가되는 기간 동안 일반적으로 액세스되는 데이터를 [Azure 캐시](http://azure.microsoft.com/services/cache/)와 같은 메모리 내 캐시에 저장하는 방법입니다. 즉, 요청이 수신되었을 때 데이터베이스를 반복적으로 쿼리하는 대신 필요한 정보를 메모리에서 인출할 수 있게 하는 것입니다.

<a name="Advanced"></a>
## 고급 문제 해결
이 섹션에서는 지금까지의 과정을 통해서도 문제가 완전히 해결되지 않았을 때 유용할 수 있는 보다 효과적인 고급 작업에 대해 설명합니다.

### 필수 조건
이 섹션의 일부 진단 작업을 수행하기 위해서는 **SQL Server Management Studio**와 같은 SQL 데이터베이스를 위한 관리 도구 또는 **Azure 관리 포털**에서 기본 제공되는 관리 기능에 액세스할 수 있어야 합니다.

SQL Server Management Studio는 고급 기능을 제공하는 무료 Windows 응용 프로그램입니다. Windows 컴퓨터에 액세스할 수 없는 경우(예: Mac을 사용하는 경우), [Windows Server를 실행하는 가상 컴퓨터 만들기](http://azure.microsoft.com/documentation/articles/virtual-machines-windows-tutorial/)에 설명된 것처럼 Azure에서 가상 컴퓨터를 프로비전하고 원격으로 연결할 수 있습니다. SQL Server Management Studio 실행을 위해 주로 VM을 사용하려는 경우 **Basic A0**(이전의 "Extra Small") 인스턴스만으로도 충분합니다. 

Azure 관리 포털은 보다 제한적이지만 로컬 설치 없이 사용 가능한 기본 제공되는 관리 환경을 제공합니다.

다음 단계에서는 모바일 서비스를 지원하는 SQL 데이터베이스에 대한 연결 정보를 얻는 방법과 두 가지 도구를 사용하여 이에 연결하는 방법을 안내합니다. 무엇이든 원하는 도구를 선택할 수 있습니다.

#### SQL 연결 정보 얻기 
1. [Azure 관리 포털][]을 실행합니다.
2. 모바일 서비스 탭에서 사용하려는 서비스를 선택합니다.
3. **구성** 탭을 선택합니다.
4. **데이터베이스 설정** 섹션에서 **SQL 데이터베이스**를 선택합니다. 그러면 포털의 Azure SQL 데이터베이스 탭으로 이동됩니다.
5. **이 IP 주소에 대한 Azure 방화벽 규칙 설정**을 선택합니다.
6. **데이터베이스에 연결** 섹션에서 서버 주소를 확인합니다(예: *mcml4otbb9.database.windows.net*).

#### SQL Server Management Studio
1. [SQL Server 버전 - Express](http://www.microsoft.com/server-cloud/products/sql-server-editions/sql-server-express.aspx)로 이동합니다.
2. **SQL Server Management Studio** 섹션을 찾아서 바로 아래에 있는 **다운로드** 단추를 선택합니다.
3. 응용 프로그램을 성공적으로 실행할 수 있을 때까지 설정 단계를 완료합니다.

    ![SQL Server Management Studio][SSMS]

4. **서버에 연결** 대화 상자에서 다음 값을 입력합니다.
    - 서버 이름: *앞에서 확인한 서버 주소*
    - 인증: *SQL Server 인증*
    - 로그인: *서버를 만들 때 선택한 로그인*
    - 암호: *서버를 만들 때 선택한 암호*
5. 그러면 이제 서버에 연결됩니다.

#### SQL 데이터베이스 관리 포털
1. 사용자의 데이터베이스에 대한 Azure SQL 데이터베이스 탭에서 **관리** 단추를 선택합니다. 
2. 다음 값을 사용하여 연결을 구성합니다.
    - 서버: *올바른 값으로 미리 설정되어 있어야 합니다*
    - 데이터베이스: *빈 상태로 둡니다*
    - 사용자 이름: *서버를 만들 때 선택한 로그인*
    - 암호: *서버를 만들 때 선택한 암호*
3. 그러면 이제 서버에 연결됩니다.

    ![Azure Management Portal - SQL Database][PortalSqlManagement]

<a name="AdvancedDiagnosing"></a>
### 고급 진단

**Azure 관리 포털**에서는 많은 진단 작업을 쉽게 완료할 수 있지만 일부 고급 진단 작업의 경우 **SQL Server Management Studio** 또는 **SQL 데이터베이스 관리 포털**을 통해서만 가능합니다.  여기에서는 데이터베이스에 대한 진단 정보로 자동으로 채워진 일련의 뷰인 동적 관리 뷰를 사용합니다. 이 섹션에서는 다양한 메트릭을 검사하기 위해 이러한 뷰에 대해 실행할 수 있는 쿼리 집합을 제공합니다. 자세한 내용은 [동적 관리 뷰를 사용하여 SQL 데이터베이스 모니터링][]을 참조하세요.

SQL Server Management Studio에서 데이터베이스에 연결하기 위해 이전 섹션의 단계를 완료한 후 **개체 탐색기**에서 해당 데이터베이스를 선택합니다. **뷰**를 확장한 후 **시스템 뷰**를 확장하면 관리 뷰 목록이 표시됩니다. 아래의 쿼리를 실행하려면 **개체 탐색기**에서 데이터베이스를 선택한 상태에서 **새 쿼리**를 선택한 후 쿼리를 붙여 넣고 **실행**을 선택합니다.

![SQL Server management Studio - dynamic management views][SSMSDMVs]

또는 SQL 데이터베이스 관리 포털을 사용하는 경우 먼저 데이터베이스를 선택하고 **새 쿼리**를 선택합니다.

![SQL Database Management Portal - new query][PortalSqlManagementNewQuery]

아래의 쿼리를 실행하려면 창으로 전달하고 **실행**을 선택합니다.

![SQL Database Management Portal - run query][PortalSqlManagementRunQuery]

#### 고급 메트릭

Basic, Standard 및 Premium 계층을 사용 중인 경우 관리 포털에서 일부 메트릭을 바로 사용할 수 있습니다. 하지만 Web 및 Business 계층을 사용할 경우에는 저장소 메트릭만 포털을 통해 사용할 수 있습니다. 다행스럽게도, 사용 중인 계층에 관계없이 **[sys.resource\_stats](http://msdn.microsoft.com/library/dn269979.aspx)** 관리 뷰를 사용하면 이러한 메트릭 및 기타 메트릭을 쉽게 가져올 수 있습니다. 다음과 같은 쿼리를 고려해 보세요.

    SELECT TOP 10 * 
    FROM sys.resource_stats 
    WHERE database_name = 'todoitem_db' 
    ORDER BY start_time DESC

> [AZURE.NOTE] 
> 이 쿼리는 서버의 **master** 데이터베이스에서 실행하세요. **sys.resource\_stats** 뷰는 이 데이터베이스에서 만 제공됩니다.

결과에는 포함되는 유용한 메트릭은 CPU(계층 제한의 %), 저장소(MB), 물리적 데이터 읽기(계층 제한의 %), 로그 쓰기(계층 제한의 %), 메모리(계층 제한의 %), 작업자 수, 세션 수 등입니다. 

#### SQL 연결 이벤트

**[sys.event\_log](http://msdn.microsoft.com/library/azure/jj819229.aspx)** 뷰에는 연결 관련 이벤트에 대한 세부 정보가 포함됩니다.

    select * from sys.event_log 
    where database_name = 'todoitem_db'
    and event_type like 'throttling%'
    order by start_time desc

> [AZURE.NOTE] 
> 이 쿼리는 서버의 **master** 데이터베이스에서 실행하세요. **sys.event\_log** 뷰는 이 데이터베이스에서 만 제공됩니다.

<a name="AdvancedIndexing"></a>
### 고급 인덱싱

테이블 또는 뷰에 포함될 수 있는 인덱스 유형은 다음과 같습니다.

- **클러스터형**. 클러스터형 인덱스는 디스크에 레코드가 물리적으로 저장되는 방식을 지정합니다. 데이터 행 자체는 하나의 순서로만 정렬할 수 있기 때문에 테이블당 클러스터형 인덱스는 하나만 있어야 합니다.

- **비클러스터형**. 비클러스터형 인덱스는 데이터 행과 별도로 저장되며 인덱스 값을 기준으로 조회를 수행하는 데 사용됩니다. 한 테이블에 있는 모든 비클러스터형 인덱스는 클러스터형 인덱스의 키 값을 조회 키로 사용합니다.

실제 비유를 제공하려면 책이나 기술 매뉴얼을 고려해보세요. 각 페이지의 콘텐츠를 레코드라고 할 때 페이지 번호는 클러스터형 인덱스가 되고, 책 뒷면에 있는 색인은 비클러스터형 인덱스가 됩니다. 색인의 각 항목은 클러스터형 인덱스인 페이지 번호를 가리킵니다.

> [AZURE.NOTE] 
> 기본적으로 Azure 모바일 서비스의 JavaScript 백 엔드는 **\_createdAt**을 클러스터형 인덱스로 설정합니다. 이 열을 제거하거나 다른 클러스터형 인덱스를 원할 경우에는 아래의 [클러스터형 인덱스 디자인 지침](#ClusteredIndexes) 을따라야 합니다. .NET 백 엔드에서 `EntityData` 클래스는 주석 `[Index(IsClustered = true)]`를 사용하여 `[CreatedAt]`를 클러스터형 인덱스로 정의합니다.

<a name="ClusteredIndexes"></a>
#### 클러스터형 인덱스 디자인 지침

모든 테이블의 열(또는 복합 키의 경우 복수 열)에는 다음 속성을 갖는 클러스터형 인덱스가 있습니다.

- 좁음 - 작은 데이터 형식을 사용하거나 적은 개수는 좁은 열에 대한 [복합 키][기본 및 외래 키 제약 조건]입니다.
- 고유 또는 거의 고유
- 정적 - 값이 자주 변경되지 않습니다.
- 계속 증가 
- (선택 사항) 고정 너비
- (선택 사항) null 아님

**좁음** 속성을 사용하는 이유는 테이블의 다른 모든 인덱스가 클러스터형 인덱스의 키 값을 조회 키로 사용하기 때문입니다. 책 뒤에 있는 색인의 예에서 클러스터형 인덱스는 페이지 번호이고 작은 번호입니다. 클러스터형 인덱스에 장 제목을 대신 포함한 경우에는 키 값이 장 이름, 페이지 번호가 될 것이므로 색인 자체가 훨씬 커질 수 있습니다.

이 경우 키는 레코드의 물리적 위치를 유지할 필요가 없도록 **정적** 및 **계속 증가**여야 합니다. 즉, 레코드를 물리적으로 이동하거나 레코드가 저장된 페이지를 분할하여 잠재적으로 저장소를 분할해야 합니다. 

클러스터형 인덱스는 다음을 수행하는 쿼리의 경우에 가장 유용할 수 있습니다.

- BETWEEN, >, >=, < 및 <=과 같은 연산자를 사용하여 값 범위를 반환하는 경우 
	- 클러스터형 인덱스를 사용하여 첫 번째 값이 포함된 행을 찾은 다음, 이후 인덱싱된 값을 포함하는 행은 물리적으로 인접성이 보장됩니다. 
- JOIN 절을 사용합니다. 이러한 항목은 일반적으로 외래 키 열입니다.
- ORDER BY 또는 GROUP BY 절을 사용합니다.
	- ORDER BY 또는 GROUP BY 절에 지정된 열의 인덱스는 행이 이미 정렬되어 있으므로 데이터베이스 엔진이 데이터를 정렬할 필요를 없앨 수 있습니다. 그러면 쿼리 성능이 향상됩니다.

#### Entity Framework에서 클러스터형 인덱스 만들기

Entity Framework를 사용하여 .NET 백 엔드에서 클러스터형 인덱스를 설정하려면 주석의 `IsClustered` 속성을 설정합니다. 예를 들어 이 속성은 `Microsoft.WindowsAzure.Mobile.Service.EntityData`에서 `CreatedAt`의 정의입니다.

	[Index(IsClustered = true)]
	[DatabaseGenerated(DatabaseGeneratedOption.Identity)]
	[TableColumnAttribute(TableColumnType.CreatedAt)]
	public DateTimeOffset? CreatedAt { get; set; }

#### 데이터베이스 스키마에서 인덱스 만들기

JavaScript 백 엔드의 경우, SQL Server Management Studio 또는 Azure SQL 데이터베이스 포털을 통해 데이터베이스 스키마를 직접 변경하여 테이블의 클러스터형 인덱스를 수정하는 것만 가능합니다.

다음 설명서에서는 데이터베이스 스키마를 직접 수정하여 클러스터형 또는 비클러스터형 인덱스를 설정하는 방법에 대해 설명합니다.  

- [PRIMARY KEY 제약 조건 만들기 및 수정][]
- [비클러스터형 인덱스 만들기][]
- [클러스터형 인덱스 만들기][]
- [고유 인덱스 만들기][]

#### 누락된 최상위 N개 인덱스 찾기 
개별 쿼리의 리소스 사용량에 대해 보다 자세한 정보를 제공하거나 추가할 인덱스에 대한 추론 정보를 제공하는 동적 관리 뷰에서 SQL 쿼리를 작성할 수 있습니다. 다음 쿼리에 서는 사용자 쿼리에 대해 예상되는 누적 향상이 가장 높은 10개의 누락 인덱스를 확인하여 내림차순으로 보여 줍니다.

    SELECT TOP 10 *
    FROM sys.dm_db_missing_index_group_stats
    ORDER BY avg_total_user_cost * avg_user_impact * (user_seeks + user_scans)
    DESC;

다음 예제 쿼리는 이러한 테이블 간에 조인을 실행하여 각 누락 쿼리에 포함되어야 하는 열 목록을 가져오고  'index advantage'을 계산하여 해당 인덱스를 고려해야 하는지 여부를 결정합니다.

    SELECT * from 
    (
        SELECT 
        (user_seeks+user_scans) * avg_total_user_cost * (avg_user_impact * 0.01) AS index_advantage, migs.*
        FROM sys.dm_db_missing_index_group_stats migs
    ) AS migs_adv,
      sys.dm_db_missing_index_groups mig,
      sys.dm_db_missing_index_details mid
    WHERE
      migs_adv.group_handle = mig.index_group_handle and
      mig.index_handle = mid.index_handle
      AND migs_adv.index_advantage > 10
    ORDER BY migs_adv.index_advantage DESC;

자세한 내용은 [동적 관리 뷰를 사용하여 SQL 데이터베이스 모니터링][] 및 [누락 인덱스 동적 관리 뷰](sys-missing-index-stats)를 참조하세요.

<a name="AdvancedQuery"></a>
### 고급 쿼리 디자인 

데이터베이스에서 가장 비용이 높은 쿼리를 진단하기가 어려운 경우가 많습니다. 

#### 최상위 N개 쿼리 찾기

다음 예제는 평균 CPU 시간별로 상위 5개의 쿼리에 대한 정보를 반환합니다. 이 예제는 쿼리 해시에 따라 쿼리를 집계하므로 논리적으로 동일한 쿼리는 해당 누적 리소스 소비량에 따라 그룹화됩니다.

	SELECT TOP 5 query_stats.query_hash AS "Query Hash", 
	    SUM(query_stats.total_worker_time) / SUM(query_stats.execution_count) AS "Avg CPU Time",
	    MIN(query_stats.statement_text) AS "Statement Text"
	FROM 
	    (SELECT QS.*, 
	    SUBSTRING(ST.text, (QS.statement_start_offset/2) + 1,
	    ((CASE statement_end_offset 
	        WHEN -1 THEN DATALENGTH(st.text)
	        ELSE QS.statement_end_offset END 
	            - QS.statement_start_offset)/2) + 1) AS statement_text
	     FROM sys.dm_exec_query_stats AS QS
	     CROSS APPLY sys.dm_exec_sql_text(QS.sql_handle) as ST) as query_stats
	GROUP BY query_stats.query_hash
	ORDER BY 2 DESC;

자세한 내용은 [동적 관리 뷰를 사용하여 SQL 데이터베이스 모니터링][]을 참조하세요. 쿼리 실행 외에도 **SQL 데이터베이스 관리 포털**은 데이터베이스에 대해 **요약**을 선택하고 **쿼리 성능**을 선택하여 이 데이터를 볼 수 있는 효과적인 바로 가기를 제공합니다.

![SQL Database Management Portal - query performance][PortalSqlManagementQueryPerformance]

#### 쿼리 계획 분석

대량의 쿼리가 식별된 다음 또는 새 쿼리를 사용하여 코드를 배포하고 성능을 조사하려는 경우, 도구를 통해 **쿼리 계획** 분석을 위한 효과적인 지원을 제공할 수 있습니다. 쿼리 계획을 사용하면 지정된 SQL 쿼리가 실행될 때 대량의 CPU 시간 및 IO 리소스를 소비하는 작업을 확인할 수 있습니다. **SQL Server Management Studio**에서 쿼리 계획을 분석하려면 강조 표시된 도구 모음 단추를 사용합니다.

![SQL Server Management Studio - query plan][SSMSQueryPlan]

**SQL 데이터베이스 관리 포털**에서 쿼리 계획을 분석하려면 강조 표시된 도구 모음 단추를 사용합니다.

![SQL Database Management Portal - query plan][PortalSqlManagementQueryPlan]

## 참고 항목

- [Azure SQL 데이터베이스 설명서][]
- [Azure SQL 데이터베이스 성능 및 크기 조정][]
- [Azure SQL 데이터베이스 문제 해결][]

### 인덱싱

- [인덱스 기본 사항][]
- [일반 인덱스 디자인 지침][]
- [고유 인덱스 디자인 지침][]
- [클러스터형 인덱스 디자인 지침][]
- [기본 및 외래 키 제약 조건][]
- [키 비용 확인][]

### Entity Framework
- [Entity Framework 5 성능 고려 사항][]
- [Code First 데이터 주석][]

<!-- IMAGES -->
 
[SSMS]: ./media/mobile-services-sql-scale-guidance/1.png
[PortalSqlManagement]: ./media/mobile-services-sql-scale-guidance/2.png
[PortalSqlMetrics]: ./media/mobile-services-sql-scale-guidance/3.png
[PortalSqlScale]: ./media/mobile-services-sql-scale-guidance/4.png
[PortalSqlAddAlert]: ./media/mobile-services-sql-scale-guidance/5.png
[PortalSqlAddAlert2]: ./media/mobile-services-sql-scale-guidance/6.png
[PortalSqlAddAlert3]: ./media/mobile-services-sql-scale-guidance/7.png
[SetIndexJavaScriptPortal]: ./media/mobile-services-sql-scale-guidance/set-index-portal-ui.png
[SSMSDMVs]: ./media/mobile-services-sql-scale-guidance/8.png
[PortalSqlManagementNewQuery]: ./media/mobile-services-sql-scale-guidance/9.png
[PortalSqlManagementRunQuery]: ./media/mobile-services-sql-scale-guidance/10.png
[PortalSqlManagementQueryPerformance]: ./media/mobile-services-sql-scale-guidance/11.png
[SSMSQueryPlan]: ./media/mobile-services-sql-scale-guidance/12.png
[PortalSqlManagementQueryPlan]: ./media/mobile-services-sql-scale-guidance/13.png

<!-- LINKS -->

[Azure 관리 포털]: http://manage.windowsazure.com

[Azure SQL 데이터베이스 설명서]: http://azure.microsoft.com/documentation/services/sql-database/
[SQL Server Management Studio를 사용하여 SQL 데이터베이스 관리]: http://go.microsoft.com/fwlink/p/?linkid=309723&clcid=0x409
[동적 관리 뷰를 사용하여 SQL 데이터베이스 모니터링]: http://go.microsoft.com/fwlink/p/?linkid=309725&clcid=0x409
[Azure SQL 데이터베이스 성능 및 크기 조정]: http://go.microsoft.com/fwlink/p/?linkid=397217&clcid=0x409
[Azure SQL 데이터베이스 문제 해결]: http://msdn.microsoft.com/library/azure/ee730906.aspx

<!-- MSDN -->
[PRIMARY KEY 제약 조건 만들기 및 수정]: http://technet.microsoft.com/library/ms181043(v=sql.105).aspx
[클러스터형 인덱스 만들기]: http://technet.microsoft.com/library/ms186342(v=sql.120).aspx
[고유 인덱스 만들기]: http://technet.microsoft.com/library/ms187019.aspx
[비클러스터형 인덱스 만들기]: http://technet.microsoft.com/library/ms189280.aspx

[기본 및 외래 키 제약 조건]: http://msdn.microsoft.com/library/ms179610(v=sql.120).aspx
[인덱스 기본 사항]: http://technet.microsoft.com/library/ms190457(v=sql.105).aspx
[일반 인덱스 디자인 지침]: http://technet.microsoft.com/library/ms191195(v=sql.105).aspx 
[고유 인덱스 디자인 지침]: http://technet.microsoft.com/library/ms187019(v=sql.105).aspx
[클러스터형 인덱스 디자인 지침]: http://technet.microsoft.com/library/ms190639(v=sql.105).aspx

[sys-missing-index-stats]: http://technet.microsoft.com/library/ms345421.aspx

<!-- EF -->
[Entity Framework 5 성능 고려 사항]: http://msdn.microsoft.com/data/hh949853
[Code First 데이터 주석]: http://msdn.microsoft.com/data/jj591583.aspx
[Entity Framework의 인덱스 주석]:http://msdn.microsoft.com/data/jj591583.aspx#Index

<!-- BLOG LINKS -->
[키 비용 확인]: http://www.sqlskills.com/blogs/kimberly/how-much-does-that-key-cost-plus-sp_helpindex9/

<!--HONumber=47-->
