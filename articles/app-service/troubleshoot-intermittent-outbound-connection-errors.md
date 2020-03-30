---
title: Azure 앱 서비스에서 간헐적인 아웃바운드 연결 오류 문제 해결
description: Azure App Service에서 간헐적인 연결 오류 및 관련 성능 문제 해결
author: v-miegge
manager: barbkess
ms.topic: troubleshooting
ms.date: 03/24/2020
ms.author: ramakoni
ms.custom: security-recommendations
ms.openlocfilehash: 028ddccdb989d35710e387081b08a3b973d75bdc
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80367552"
---
# <a name="troubleshooting-intermittent-outbound-connection-errors-in-azure-app-service"></a>Azure 앱 서비스에서 간헐적인 아웃바운드 연결 오류 문제 해결

이 문서에서는 [Azure App Service](https://docs.microsoft.com/azure/app-service/overview)에서 간헐적인 연결 오류 및 관련 성능 문제를 해결하는 데 도움이 됩니다. 이 항목에서는 SNAT(소스 주소 네트워크 번역) 포트의 고갈에 대한 자세한 정보 및 문제 해결 방법론을 제공합니다. 이 문서의 어느 시점에서든 추가 도움이 필요한 경우 [MSDN Azure 및 스택 오버플로 포럼의 Azure 전문가에게 문의하십시오.](https://azure.microsoft.com/support/forums/) 또는 Azure 지원 인시던트를 파일로 제출합니다. [Azure 지원 사이트로](https://azure.microsoft.com/support/options/) 이동하여 **지원 받기를**선택합니다.

## <a name="symptoms"></a>증상

Azure App 서비스에서 호스팅되는 응용 프로그램 및 함수는 다음 증상 중 하나 이상을 나타낼 수 있습니다.

* 서비스 계획의 모든 또는 일부 인스턴스에 대한 응답 시간 느리게 조정됩니다.
* 간헐적 5xx 또는 **잘못된 게이트웨이** 오류
* 시간 시간 오류 메시지
* 외부 끝점(예: SQLDB, 서비스 패브릭, 기타 앱 서비스 등)에 연결할 수 없습니다.

## <a name="cause"></a>원인

이러한 증상의 주요 원인은 응용 프로그램 인스턴스가 다음 제한 중 하나에 도달했기 때문에 외부 끝점에 대한 새 연결을 열 수 없기 때문입니다.

* TCP 연결: 만들 수 있는 아웃바운드 연결 수에 제한이 있습니다. 이는 사용된 작업자의 크기와 관련이 있습니다.
* SNAT 포트: [Azure의 아웃바운드 연결에서](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections)설명한 대로 Azure는 원본 네트워크 주소 변환(SNAT) 및 로드 밸런서(고객에게 노출되지 않음)를 사용하여 공용 IP 주소 공간에서 Azure 외부의 끝점과 통신합니다. Azure App 서비스의 각 인스턴스에는 처음에 미리 할당된 **128개의** SNAT 포트 수가 지정됩니다. 이 제한은 동일한 호스트 및 포트 조합에 대한 연결을 여는 데 영향을 줍니다. 앱에서 주소와 포트 조합의 조합에 대한 연결을 만드는 경우 SNAT 포트를 사용하지 않습니다. 동일한 주소와 포트 조합에 대한 반복 호출이 있을 때 SNAT 포트가 사용됩니다. 포트가 해제되면 필요에 따라 포트를 다시 사용할 수 있습니다. Azure 네트워크 로드 밸러버는 4분 동안 기다린 후에만 닫힌 연결에서 SNAT 포트를 회수합니다.

응용 프로그램이나 함수가 새 연결을 빠르게 열면 미리 할당된 128개 포트의 할당량을 빠르게 소모할 수 있습니다. 그런 다음 추가 SNAT 포트를 동적으로 할당하거나 회수된 SNAT 포트를 재사용하여 새 SNAT 포트를 사용할 수 있게 될 때까지 차단됩니다. 새 연결을 만들 수 없기 때문에 차단된 응용 프로그램이나 함수는 이 문서의 **증상** 섹션에 설명된 하나 이상의 문제가 발생하기 시작합니다.

## <a name="avoiding-the-problem"></a>문제 해결

SNAT 포트 문제를 방지한다는 것은 동일한 호스트 및 포트에 반복적으로 새 연결을 만드는 것을 방지해야 한다는 것을 의미합니다.

SNAT 포트 소모를 완화하기 위한 일반적인 전략은 Azure 설명서의 **아웃바운드 연결의** [문제 해결 섹션에서](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#problemsolving) 설명합니다. 이러한 전략 중 Azure App 서비스에서 호스팅되는 앱 및 기능에 는 다음이 적용됩니다.

### <a name="modify-the-application-to-use-connection-pooling"></a>연결 풀링을 사용하도록 애플리케이션 수정

* HTTP 연결을 풀링하는 경우 [HttpClientFactory.](https://docs.microsoft.com/aspnet/core/performance/performance-best-practices#pool-http-connections-with-httpclientfactory)
* SQL Server 연결 풀링에 대한 자세한 내용은 [SQL 서버 연결 풀링(ADO.NET)을](https://docs.microsoft.com/dotnet/framework/data/adonet/sql-server-connection-pooling)검토합니다.
* 엔터티 프레임워크 응용 프로그램을 사용 하 고 풀링을 구현 하는 경우 [DbContext 풀링을](https://docs.microsoft.com/ef/core/what-is-new/ef-core-2.0#dbcontext-pooling)검토 합니다.

다음은 다른 솔루션 스택에 의한 연결 풀링을 구현하기 위한 링크 모음입니다.

#### <a name="node"></a>노드

기본적으로 NodeJS에 대한 연결은 살아 있게 유지되지 않습니다. 다음은 연결 풀링을 구현하는 방법에 대한 예제를 포함하는 연결 풀링을 위한 인기 있는 데이터베이스 및 패키지입니다.

* [Mysql](https://github.com/mysqljs/mysql#pooling-connections)
* [Mongodb](https://blog.mlab.com/2017/05/mongodb-connection-pooling-for-express-applications/)
* [Postgresql](https://node-postgres.com/features/pooling)
* [SQL Server](https://github.com/tediousjs/node-mssql#connection-pools)

HTTP 유지 -살아

* [에이전트 키리살](https://www.npmjs.com/package/agentkeepalive)
* [Node.js v13.9.0 문서](https://nodejs.org/api/http.html)

#### <a name="java"></a>Java

다음은 JDBC 연결 풀링을 구현하는 방법에 대한 예제를 포함하는 JDBC 연결 풀링에 사용되는 인기 있는 라이브러리입니다.

* [톰캣 8](https://tomcat.apache.org/tomcat-8.0-doc/jdbc-pool.html)
* [C3p0](https://github.com/swaldman/c3p0)
* [히카리CP](https://github.com/brettwooldridge/HikariCP)
* [아파치 DBCP](https://commons.apache.org/proper/commons-dbcp/)

HTTP 연결 풀링

* [아파치 연결 관리](https://hc.apache.org/httpcomponents-client-ga/tutorial/html/connmgmt.html)
* [클래스 풀링Http클라이언트커커커넥션매니저](http://hc.apache.org/httpcomponents-client-ga/httpclient/apidocs/org/apache/http/impl/conn/PoolingHttpClientConnectionManager.html)

#### <a name="php"></a>PHP

PHP는 연결 풀링을 지원하지 않지만 백 엔드 서버에 대한 영구 데이터베이스 연결을 사용해 볼 수 있습니다.
 
* MySQL 서버

   * 최신 버전에 대한 [MySQLi 연결](https://www.php.net/manual/mysqli.quickstart.connections.php)
   * 이전 버전의 PHP에 대한 [mysql_pconnect](https://www.php.net/manual/function.mysql-pconnect.php)

* 기타 데이터 소스

   * [PHP 연결 관리](https://www.php.net/manual/en/pdo.connections.php)

#### <a name="python"></a>Python

* [Mysql](https://github.com/mysqljs/mysql#pooling-connections)
* [Mongodb](https://blog.mlab.com/2017/05/mongodb-connection-pooling-for-express-applications/)
* [Postgresql](https://node-postgres.com/features/pooling)
* [SQL Server](https://github.com/tediousjs/node-mssql#connection-pools) (참고 : SQLChemy는 마이크로 소프트 SQL 서버 이외의 다른 데이터베이스와 함께 사용할 수 있습니다)
* [HTTP Keep-alive(세션](https://requests.readthedocs.io/en/master/user/advanced/#keep-alive) [세션-객체를](https://requests.readthedocs.io/en/master/user/advanced/#keep-alive)사용할 때 Keep-Alive는 자동입니다).

다른 환경의 경우 응용 프로그램에서 연결 풀링을 구현하기 위해 공급자 또는 드라이버 관련 문서를 검토합니다.

### <a name="modify-the-application-to-reuse-connections"></a>연결을 다시 사용하도록 애플리케이션 수정

*  Azure 함수에서 연결 관리에 대한 추가 포인터 및 예제를 보려면 [Azure Functions에서 연결 관리를 검토합니다.](https://docs.microsoft.com/azure/azure-functions/manage-connections)

### <a name="modify-the-application-to-use-less-aggressive-retry-logic"></a>덜 적극적인 재시도 논리를 사용하도록 애플리케이션 수정

* 추가 지침 및 예제를 보려면 [다시 시도 패턴을 검토합니다.](https://docs.microsoft.com/azure/architecture/patterns/retry)

### <a name="use-keepalives-to-reset-the-outbound-idle-timeout"></a>keepalive를 사용하여 아웃바운드 유휴 시간 제한 다시 설정

* Node.js 앱에 대한 keepalives를 구현하기 위해 [내 노드 응용 프로그램이 과도한 아웃바운드 호출을 수행하는](https://docs.microsoft.com/azure/app-service/app-service-web-nodejs-best-practices-and-troubleshoot-guide#my-node-application-is-making-excessive-outbound-calls)지 검토합니다.

### <a name="additional-guidance-specific-to-app-service"></a>앱 서비스와 관련된 추가 지침:

* [부하 테스트는](https://docs.microsoft.com/azure/devops/test/load-test/app-service-web-app-performance-test) 일정한 공급 속도로 실제 데이터를 시뮬레이션해야 합니다. 실제 스트레스 하에서 앱과 기능을 테스트하면 SNAT 포트 고갈 문제를 미리 식별하고 해결할 수 있습니다.
* 백 엔드 서비스가 응답을 신속하게 반환할 수 있는지 확인합니다. Azure SQL 데이터베이스의 성능 문제 해결을 위해 [Intelligent Insights를 사용하여 Azure SQL Database 성능 문제를 해결합니다.](https://docs.microsoft.com/azure/sql-database/sql-database-intelligent-insights-troubleshoot-performance#recommended-troubleshooting-flow)
* 앱 서비스 계획을 더 많은 인스턴스로 확장합니다. 크기 조정에 대한 자세한 내용은 [Azure App Service에서 앱 크기 조정](https://docs.microsoft.com/azure/app-service/manage-scale-up)을 참조하세요. 앱 서비스 계획의 각 작업자 인스턴스에는 여러 SNAT 포트가 할당됩니다. 더 많은 인스턴스에 사용량을 분산하는 경우 고유한 원격 끝점당 인스턴스당 SNAT 포트 사용량이 권장 되는 100개의 아웃바운드 연결 한도 미만으로 낮아질 수 있습니다.
* 단일 아웃바운드 IP 주소가 할당되고 연결 및 SNAT 포트에 대한 제한이 훨씬 높은 [ASE(앱 서비스 환경)로](https://docs.microsoft.com/azure/app-service/environment/using-an-ase)이동하는 것이 좋습니다.

아웃바운드 TCP 제한을 피하는 것은 작업자의 크기에 따라 제한이 설정되기 때문에 쉽게 해결할 수 있습니다. [샌드박스 크로스 VM 수치 제한 - TCP 연결에서](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#cross-vm-numerical-limits) 제한을 확인할 수 있습니다.

|제한 이름|설명|소형(A1)|미디엄(A2)|라지 (A3)|격리 계층(ASE)|
|---|---|---|---|---|---|
|Connections|전체 VM에 걸친 연결 수|1920|3968|8064|16,000|

아웃바운드 TCP 제한을 방지하려면 작업자의 크기를 늘리거나 수평으로 확장할 수 있습니다.

## <a name="troubleshooting"></a>문제 해결

두 가지 유형의 아웃바운드 연결 제한과 앱의 수행 을 알면 문제 해결이 더 쉬워집니다. 앱이 동일한 저장소 계정에 대해 많은 호출을 하는 경우 SNAT 제한이 의심될 수 있습니다. 앱에서 인터넷을 통해 끝점에 대한 많은 호출을 생성하는 경우 VM 한도에 도달하고 있다고 의심할 수 있습니다.

원인을 신속하게 파악할 수 있을 만큼 응용 프로그램 동작을 모르는 경우 앱 서비스에서 이러한 결정을 내리는 데 도움이 되는 몇 가지 도구와 기술을 사용할 수 있습니다.

### <a name="find-snat-port-allocation-information"></a>SNAT 포트 할당 정보 찾기

[앱 서비스 진단을](https://docs.microsoft.com/azure/app-service/overview-diagnostics) 사용하여 SNAT 포트 할당 정보를 찾고 앱 서비스 사이트의 SNAT 포트 할당 메트릭을 관찰할 수 있습니다. SNAT 포트 할당 정보를 찾으려면 다음 단계를 따르십시오.

1. 앱 서비스 진단에 액세스하려면 [Azure 포털에서](https://portal.azure.com/)앱 서비스 웹 앱 또는 앱 서비스 환경으로 이동합니다. 왼쪽 탐색에서 **문제 진단 및 해결을 선택합니다.**
2. 가용성 및 성능 범주 선택
3. 범주 에서 사용 가능한 타일 목록에서 SNAT 포트 소모 타일을 선택합니다. 연습은 128 이하로 유지하는 것입니다.
필요한 경우 지원 티켓을 열 수 있으며 지원 엔지니어는 백 엔드에서 메트릭을 가져옵니다.

SNAT 포트 사용량은 메트릭으로 사용할 수 없으므로 SNAT 포트 사용량을 기반으로 자동 크기 조정을 하거나 SNAT 포트 할당 메트릭을 기반으로 자동 배율을 구성할 수 없습니다.

### <a name="tcp-connections-and-snat-ports"></a>TCP 연결 및 SNAT 포트

TCP 연결 및 SNAT 포트는 직접 관련이 없습니다. TCP 연결 사용 감지기는 모든 앱 서비스 사이트의 문제 진단 및 해결 블레이드에 포함되어 있습니다. "TCP 연결"이라는 구를 검색하여 찾습니다.

* SNAT 포트는 외부 네트워크 흐름에만 사용되며 총 TCP 연결에는 로컬 루프백 연결이 포함됩니다.
* 흐름이 프로토콜, IP 주소 또는 포트에서 다른 경우 SNAT 포트는 다른 흐름에서 공유할 수 있습니다. TCP 연결 메트릭은 모든 TCP 연결을 계산합니다.
* TCP 연결 제한은 작업자 인스턴스 수준에서 발생합니다. Azure 네트워크 아웃바운드 로드 분산은 SNAT 포트 제한에 TCP 연결 메트릭을 사용하지 않습니다.
* TCP 연결 제한은 [샌드박스 크로스 VM 수치 제한 - TCP 연결에](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#cross-vm-numerical-limits) 설명되어 있습니다.

|제한 이름|설명|소형(A1)|미디엄(A2)|라지 (A3)|격리 계층(ASE)|
|---|---|---|---|---|---|
|Connections|전체 VM에 걸친 연결 수|1920|3968|8064|16,000|

### <a name="webjobs-and-database-connections"></a>웹 작업 및 데이터베이스 연결
 
WebJobs가 Azure SQL 데이터베이스에 연결할 수 없는 SNAT 포트가 소진된 경우 각 개별 웹 응용 프로그램 프로세스에서 열리는 연결 수를 표시하는 메트릭이 없습니다. 문제가 있는 WebJob을 찾으려면 여러 WebJobs를 다른 앱 서비스 계획으로 이동하여 상황이 개선되는지 또는 계획 중 하나에 문제가 남아 있는지 확인합니다. 문제가 있는 WebJob을 찾을 때까지 이 프로세스를 반복합니다.

### <a name="using-snat-ports-sooner"></a>SNAT 포트 를 더 빨리 사용

모든 SNAT 포트는 아래 조건에 따라 해제되고 동작은 의도적으로 설계되어 있으므로 사용된 SNAT 포트를 더 빨리 릴리스하도록 Azure 설정을 변경할 수 없습니다.
 
* 서버 또는 클라이언트가 FINACK를 보내면 [SNAT 포트는](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#tcp-snat-port-release) 240초 후에 해제됩니다.
* RST가 나타나면 SNAT 포트는 15초 후에 해제됩니다.
* 유휴 시간 지정에 도달하면 포트가 해제됩니다.
 
## <a name="additional-information"></a>추가 정보

* [앱 서비스를 갖춘 SNAT](https://4lowtherabbit.github.io/blogs/2019/10/SNAT/)
* [Azure App Service에서 느린 앱 성능 문제 해결](https://docs.microsoft.com/azure/app-service/troubleshoot-performance-degradation)
