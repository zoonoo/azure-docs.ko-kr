<properties 
	pageTitle="Azure Redis Cache FAQ" 
	description="Azure Redis Cache에 대한 일반적인 질문과 대답, 패턴 및 모범 사례를 알아봅니다." 
	services="redis-cache" 
	documentationCenter="" 
	authors="steved0x" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="cache" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="cache-redis" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/23/2015" 
	ms.author="sdanie"/>

# Azure Redis Cache FAQ

Azure Redis Cache에 대한 일반적인 질문과 대답, 패턴 및 모범 사례를 알아봅니다.

<a name="cache-size"></a>
## 어떤 Redis Cache 제품 및 크기를 사용해야 하나요?
각 Azure Redis Cache 제품은 서로 다른 수준의 **크기**, **대역폭**, **고가용성** 및 **SLA** 옵션을 제공합니다.

캐시 제품을 선택할 때는 다음을 고려해야 합니다.

-	**메모리**: 기본 및 표준 계층에서 250MB-53GB를 제공합니다. 프리미엄 계층은 [요청 시](mailto:wapteams@microsoft.com?subject=Redis%20Cache%20quota%20increase) 추가적으로 사용 가능한 최대 530GB를 제공합니다. 자세한 내용은 [Azure Redis Cache 가격 책정](https://azure.microsoft.com/pricing/details/cache/)을 참조하세요.
-	**네트워크 성능**: 높은 처리량이 필요한 작업이 있는 경우 프리미엄 계층이 표준이나 기본에 비해 더 높은 대역폭을 제공합니다. 또한 각 계층 안에서는 캐시를 호스팅하는 기본 VM으로 인해 캐시 크기가 클수록 대역폭이 큽니다. 자세한 내용은 다음 표를 참조하세요.
-	**처리량**: 프리미엄 계층은 사용 가능한 최대 처리량을 제공합니다. 캐시 서버 또는 클라이언트가 대역폭 제한에 도달하면 클라이언트 측에서 시간 초과가 수신됩니다. 자세한 내용은 다음 표를 참조하세요.
-	**높은 가용성/SLA**: Azure Redis Cache는 표준/프리미엄(미리 보기 기간 이후까지 프리미엄에 대한 SLA 없음) 99.9% 이상의 시간에서 캐시를 사용할 수 있도록 보장합니다. SLA에 대한 자세한 내용은 [ Azure Redis Cache 가격 책정](https://azure.microsoft.com/pricing/details/cache/)을 참조하세요. SLA에서는 캐시 끝점에 대한 연결만 다룹니다. SLA는 데이터 손실로부터의 보호는 다루지 않습니다. 데이터 손실에 대한 복원력을 늘리기 위해 프리미엄 계층에서 Redis 데이터 지속성 기능을 사용하는 것이 좋습니다.
-	**Redis 데이터 지속성**: 프리미엄 계층을 사용하면 Azure 저장소 계정에서 캐시 데이터를 유지할 수 있습니다. 기본/표준 캐시에서 모든 데이터는 메모리에만 저장됩니다. 기본 인프라의 경우 문제는 잠재적인 데이터 손실이 있을 수 있다는 점입니다. 데이터 손실에 대한 복원력을 늘리기 위해 프리미엄 계층에서 Redis 데이터 지속성 기능을 사용하는 것이 좋습니다. Azure Redis Cache는 Redis 지속성에서 RDB 및 AOF(출시 예정) 옵션을 제공합니다. 자세한 내용은 [프리미엄 Azure Redis Cache에 지속성을 구성하는 방법](cache-how-to-premium-persistence.md)을 참조하세요.
-	**Redis 클러스터**: 53GB보다 큰 캐시를 만들거나 여러 Redis 노드에서 데이터를 분할하려는 경우 프리미엄 계층에서 사용 가능한 Redis 클러스터링을 사용할 수 있습니다. 각 노드는 고가용성을 위해 주/복제본 캐시 쌍으로 구성됩니다. 자세한 내용은 [프리미엄 Azure Redis Cache에 클러스터링을 구성하는 방법](cache-how-to-premium-clustering.md)을 참조하세요.
-	**보안 및 네트워크 격리 향상**: Azure 가상 네트워크(VNET) 배포는 Azure Redis Cache의 보안과 격리를 강화하며 액세스를 추가적으로 제한하기 위한 서브넷, 액세스 제어 정책 및 기타 기능을 제공합니다. 자세한 내용은 [프리미엄 Azure Redis Cache에 가상 네트워크 지원을 구성하는 방법](cache-how-to-premium-vnet.md)을 참조하세요.
-	**Redis 구성**: 표준과 프리미엄 계층에서 Redis Keyspace 알림을 구성할 수 있습니다.
-	**최대 클라이언트 연결 수**: 프리미엄 계층은 더 큰 캐시에 대해 더 많은 수의 연결과, Redis에 연결 가능한 최대 클라이언트 수를 제공합니다. [자세한 내용은 가격 책정 페이지를 참조하세요.](https://azure.microsoft.com/pricing/details/cache/)
-	**Redis 서버에 대한 전용 코어**: 프리미엄 계층에서는 모든 캐시 크기에 Redis 전용 코어가 있습니다. 기본/표준 계층에서는 C1 크기 이상에 Redis 서버 전용 코어가 있습니다.
-	**Redis는 단일 스레드**이므로 3개 이상의 코어를 사용해도 2개 코어만 사용하는 경우에 비해 추가적인 이점이 없지만 VM 크기가 더 크면 일반적으로 작은 크기보다 대역폭이 더 큽니다. 캐시 서버 또는 클라이언트가 대역폭 제한에 도달하면 클라이언트 쪽에 시간 초과가 수신됩니다.
-	**성능 향상**: 프리미엄 계층의 캐시는 더 빠른 프로세서가 포함되고 기본 또는 표준 계층에 비해 더 나은 성능을 제공하는 하드웨어에 배포됩니다. 프리미엄 계층 캐시는 처리량은 더 높고 대기 시간은 더 짧습니다.

다음 표에서는 Azure Redis Cache 끝점에 대해 Iaas VM에서 `redis-benchmark.exe`를 사용하는 다양한 크기의 표준 및 프리미엄 캐시를 테스트하는 동안 관찰되는 최대 대역폭 크기를 보여 줍니다. 이러한 값은 보장되지 않으며 해당 수치에 대한 SLA는 없지만 일반적인 수치입니다. 응용 프로그램에 적합한 캐시 크기를 확인하려면 사용자 고유의 응용 프로그램을 부하 테스트해야 합니다.

이 테이블에서 다음과 같은 결론을 내릴 수 있습니다.

-	동일한 크기의 캐시에 대한 처리량이 표준 계층에 비해 프리미엄에서 더 높습니다. 예를 들어 6GB 캐시의 경우 C3의 처리량 49K에 비해 P1의 처리량은 140K RPS입니다.
-	Redis 클러스터를 사용하여 클러스터에서 분할된 데이터베이스(노드) 수를 늘림에 따라 처리량이 선형으로 늘어납니다. 예를 들어 10개의 분할된 데이터베이스에 P4 클러스터를 만드는 경우 가능한 처리량은 250K * 10 = 초당 250만 요청 수입니다.
-	큰 크기의 키에 대한 처리량이 표준 계층에 비해 프리미엄 계층에서 더 높습니다.

| 가격 책정 계층 | 크기 | 사용 가능한 대역폭(Mbps) | 1KB 키 크기 |
|----------------------|--------|----------------------------|--------------------------------|
| **표준 캐시 크기** | &nbsp; | &nbsp; | **초당 요청 수(RPS)** |
| C0 | 250MB | 5 | 600 |
| C1 | 1 GB | 100 | 12200 |
| C2 | 2\.5GB | 200 | 24000 |
| C3 | 6GB | 400 | 49000 |
| C4 | 13GB | 500 | 61000 |
| C5 | 26GB | 1000 | 115000 |
| C6 | 53GB | 2000 | 150000 |
| **프리미엄 캐시 크기** | &nbsp; | &nbsp; | **초당 요청 수(RPS), 분할된 데이터베이스당** |
| P1 | 6GB | 1000 | 140000 |
| P2 | 13GB | 2000 | 220000 |
| P3 | 26GB | 2000 | 220000 |
| P4 | 53GB | 4000 | 250000 |


`redis-benchmark.exe`와 같은 Redis 도구 다운로드에 대한 지침은 [어떻게 Redis 명령을 실행할 수 있나요?](#cache-commands) 섹션을 참조하세요.

<a name="cache-region"></a>
## 어떤 영역에 내 캐시를 배치해야 하나요?

성능을 최적화하고 대기 시간을 최소화하려면 캐시 클라이언트 응용 프로그램과 동일한 영역에 Azure Redis Cache를 배치합니다.

<a name="cache-billing"></a>
## Azure Redis Cache에 대한 요금은 어떻게 청구되나요?

Azure Redis Cache 가격 책정에 대해서는 [여기](http://azure.microsoft.com/pricing/details/cache/)를 참조하세요. 가격 책정 페이지에는 시간 단위로 가격이 나와 있습니다. 캐시는 캐시가 만들어지는 시간부터 삭제되는 시간까지 분 단위로 요금이 청구됩니다. 캐시 요금 청구를 중지 또는 일시 중지하는 옵션은 없습니다.

<a name="cache-timeouts"></a>
## 왜 시간 초과가 표시되나요?

시간 초과는 Redis와 통신하는 데 사용하는 클라이언트에서 발생합니다. 대부분의 경우 Redis 서버는 시간 초과되지 않습니다. Redis 서버에 명령이 전송될 때 명령은 큐에 배치되며 Redis 서버가 결국 명령을 선택하여 실행합니다. 그러나 이 프로세스 중에 클라이언트가 시간 초과될 수 있으며, 이 경우 호출 쪽에서 예외가 발생합니다. 시간 초과 문제를 해결하는 방법에 대한 자세한 내용은 [Azure Redis Cache에 대한 StackExchange.Redis에서 시간 초과 예외 조사](http://azure.microsoft.com/blog/2015/02/10/investigating-timeout-exceptions-in-stackexchange-redis-for-azure-redis-cache/)를 참조하세요.

<a name="cache-monitor"></a>
## 내 캐시의 상태 및 성능을 모니터링하려면 어떻게 해야 하나요?

[Azure Preview 포털](https://portal.azure.com)에서 Microsoft Azure Redis Cache 인스턴스를 모니터링할 수 있습니다. 메트릭을 보고, 메트릭 차트를 시작 보드에 고정하고, 모니터링 차트의 날짜 및 시간 범위를 사용자 지정하고, 차트에서 메트릭을 추가 및 제거하고, 특정 조건이 충족될 경우의 경고를 설정할 수 있습니다. 이러한 도구는 Azure Redis Cache 인스턴스의 상태를 모니터링할 수 있게 해주며 캐싱 응용 프로그램 관리에 도움이 됩니다. 캐시 모니터링에 대한 자세한 내용은 [Azure Redis Cache 모니터링](https://msdn.microsoft.com/library/azure/dn763945.aspx)을 참조하세요.

<a name="cache-disconnect"></a>
## 내 클라이언트가 캐시에서 연결이 끊어진 것은 무엇 때문인가요?

캐시 연결 끊김의 몇 가지 일반적인 이유는 다음과 같습니다.

-	클라이언트 쪽 원인
	-	클라이언트 응용 프로그램이 다시 배포되었습니다.
	-	클라이언트 응용 프로그램이 크기 조정 작업을 수행했습니다.
		-	클라우드 서비스나 웹앱의 경우 자동 크기 조정 때문일 수 있습니다.
	-	클라이언트 쪽의 네트워킹 계층이 변경되었습니다.
	-	클라이언트 또는 클라이언트와 서버 간의 네트워크 노드에서 일시적인 오류가 발생했습니다.
	-	대역폭 임계값 제한에 도달했습니다.
	-	CPU 바인딩된 작업을 완료하는 데 시간이 너무 오래 걸렸습니다.
-	서버 쪽 원인
	-	표준 캐시 제품에서 Azure Redis Cache 서비스가 주 노드에서 보조 노드로 장애 조치(Failover)를 시작했습니다.
	-	Azure에서 캐시가 배포된 인스턴스에 패치를 적용하고 있었습니다.
		-	이 작업은 Redis 서버 업데이트 또는 일반적인 VM 유지 관리를 위한 것일 수 있습니다.

<a name="cache-configuration"></a>
## StackExchange.Redis 구성 옵션은 어떤 기능을 수행하나요?

StackExchange.Redis에는 많은 옵션이 있습니다. 이 섹션에서는 몇 가지 일반적인 설정에 대해 설명합니다. StackExchange.Redis 옵션에 대한 자세한 내용은 [StackExchange.Redis 구성](https://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/Configuration.md)을 참조하세요.

ConfigurationOptions|설명|권장 사항
---|---|---
AbortOnConnectFail|true로 설정하면 네트워크 오류가 발생한 후 연결이 다시 연결되지 않습니다.|false로 설정하여 StackExchange.Redis가 자동으로 다시 연결하도록 합니다.
ConnectRetry|초기 연결 중에 연결 시도를 반복할 횟수입니다.||
ConnectTimeout|연결 작업의 시간 제한(ms)입니다.|

대부분의 경우 클라이언트의 기본값으로 충분합니다. 작업에 따라 옵션을 미세 조정할 수 있습니다.

-	다시 시도
	-	ConnectRetry 및 ConnectTimeout에 대한 일반적인 지침은 빠른 오류 및 다시 시도입니다. 이 값은 클라이언트가 Redis 명령을 실행하고 응답을 수신하는 데 걸리는 평균 시간 및 작업에 따라 달라집니다.
	-	연결 상태를 확인하고 직접 다시 연결하는 대신 StackExchange.Redis가 자동으로 다시 연결하도록 합니다. **ConnectionMultiplexer.IsConnected 속성을 사용하지 마세요**.
	-	때로는 다시 시도 중인 문제가 발생하고 문제가 더욱 가중되어 결코 복구되지 않습니다. 이 경우 Microsoft Patterns & Practices 그룹이 게시한 [다시 시도 일반 지침](https://github.com/mspnp/azure-guidance/blob/master/Retry-General.md)의 설명에 따라 지수 백오프 다시 시도 알고리즘 사용을 고려해야 합니다.
-	시간 제한 값
	-	작업을 고려하여 적절하게 값을 설정합니다. 큰 값을 저장하는 경우 시간 제한을 더 큰 값으로 설정합니다.
		-	ABortOnConnectFail을 false로 설정하여 StackExchange.Redis가 자동으로 다시 연결하도록 합니다.
-	응용 프로그램에 단일 ConnectionMultiplexer 인스턴스를 사용합니다. [ConnectionMultiplexer 클래스를 사용하여 캐시에 연결](https://msdn.microsoft.com/library/azure/dn690521.aspx#Connect)에 표시된 대로, LazyConnection을 사용하여 Connection 속성에 의해 반환되는 단일 인스턴스를 만들 수 있습니다.
-	진단 목적으로 `ConnectionMultiplexer.ClientName` 속성을 앱 인스턴스 고유 이름으로 설정합니다.
-	사용자 지정 작업에 여러 개의 `ConnectionMultiplexer` 인스턴스를 사용합니다.
	-	응용 프로그램에 다양한 부하가 있는 경우 이 모델을 따를 수 있습니다. 예:
		-	큰 키를 처리하기 위한 멀티플렉서 1개가 있습니다. 
		-	작은 키를 처리하기 위한 멀티플렉서 1개가 있습니다. 
		-	사용하는 각 ConnectionMultiplexer의 연결 시간 제한 및 다시 시도 논리에 대해 다른 값을 설정할 수 있습니다.
		-	진단에 도움이 되도록 각 멀티플렉서의 `ClientName` 속성을 설정합니다. 
		-	그러면 `ConnectionMultiplexer`당 대기 시간이 보다 간소화됩니다.

<a name="cache-redis-commands"></a>
## 일반적인 Redis 명령을 사용할 때 고려해야 하는 몇 가지 사항은 무엇인가요?

-	완료하는 데 시간이 오래 걸리는 특정 Redis 명령은 명령의 영향을 알고 있는 경우에만 실행해야 합니다.
	-	예를 들어 [KEYS](http://redis.io/commands/keys) 명령은 키 수에 따라 반환되는 데 시간이 오래 걸릴 수 있으므로 프로덕션에서 실행하지 마세요. Redis는 단일 스레드 서버이며 한 번에 하나씩 명령을 처리합니다. KEYS 후에 실행된 다른 명령이 있는 경우 Redis가 KEYS 명령을 처리할 때까지 처리되지 않습니다.
-	키 크기 - 작은 키/값을 사용해야 하나요, 아니면 큰 키/값을 사용해야 하나요? 일반적으로 시나리오에 따라 다릅니다. 시나리오에서 큰 키가 필요한 경우 ConnectionTimeout 및 다시 시도 값과 다시 시도 논리를 조정할 수 있습니다. Redis 서버 관점에서는 값이 작을수록 더 나은 성능이 관찰됩니다.
	-	Redis에서 큰 값을 저장할 수 없다는 의미는 아닙니다. 다음과 같은 고려 사항에 주의해야 합니다. 대기 시간이 더 길어집니다. 큰 데이터 집합 1개와 작은 데이터 집합 1개가 있는 경우, 이전의 [StackExchange.Redis 구성 옵션은 어떤 기능을 수행하나요?](#cache-configuration) 섹션에 설명된 대로 각각 다른 시간 제한 및 다시 시도 값으로 구성된 여러 개의 ConnectionMultiplexer 인스턴스를 사용할 수 있습니다.


<a name="cache-ssl"></a>
## 언제 비 SSL 포트를 사용하여 Redis에 연결할 수 있도록 해야 하나요?

Redis 서버는 기본적으로 SSL을 지원하지 않지만 Azure Redis Cache는 지원합니다. Azure Redis Cache에 연결 중이며 StackExchange.Redis처럼 클라이언트가 SSL을 지원하는 경우 SSL을 사용해야 합니다.

새 Azure Redis Cache 인스턴스에 대해 비 SSL 포트는 기본적으로 사용되지 않습니다. 클라이언트가 SSL을 지원하지 않는 경우 [Azure Redis Cache에서 캐시 구성](https://msdn.microsoft.com/library/azure/dn793612.aspx) 문서의 [포트 액세스](https://msdn.microsoft.com/library/azure/dn793612.aspx#AccessPorts) 섹션에 있는 지침에 따라 비 SSL 포트를 사용하도록 설정해야 합니다.

`redis-cli`와 같은 Redis 도구는 SSL 포트에서 작동하지 않지만, [Redis용 ASP.NET 세션 상태 공급자 미리 보기 릴리스 발표](http://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx) 블로그 게시물의 지침에 따라 `stunnel`과 같은 유틸리티를 사용하여 도구를 SSL 포트에 안전하게 연결할 수 있습니다.

Redis 도구 다운로드에 대한 지침은 [어떻게 Redis 명령을 실행할 수 있나요?](#cache-commands) 섹션을 참조하세요.

<a name="cache-benchmarking"></a>
## 내 캐시의 성능을 어떻게 벤치마크 및 테스트할 수 있나요?

-	캐시의 상태를 [모니터링](https://msdn.microsoft.com/library/azure/dn763945.aspx)할 수 있도록 [캐시 진단을 사용하도록 설정](https://msdn.microsoft.com/library/azure/dn763945.aspx#EnableDiagnostics)합니다. 미리 보기 포털에서 메트릭을 볼 수 있으며 선택한 도구를 사용하여 메트릭을 [다운로드 및 검토](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring)할 수도 있습니다.
-	redis-benchmark.exe를 사용하여 Redis 서버를 부하 테스트할 수 있습니다.
	-	부하 테스트 클라이언트와 Redis Cache가 동일한 영역에 있는지 확인합니다.
-	redis-cli.exe를 사용하고 INFO 명령을 통해 캐시를 모니터링합니다.
	-	부하로 인해 높은 메모리 조각화가 발생하는 경우 큰 캐시 크기로 확장해야 합니다.
-	Redis 도구 다운로드에 대한 지침은 [어떻게 Redis 명령을 실행할 수 있나요?](#cache-commands) 섹션을 참조하세요.

<a name="cache-commands"></a>
## 어떻게 Redis 명령을 실행할 수 있나요?

[Azure Redis Cache에서 지원되지 않는 Redis 명령](cache-configure.md#redis-commands-not-supported-in-azure-redis-cache)에 나열된 명령을 제외하고, [Redis 명령](http://redis.io/commands#)에 나열된 모든 명령을 사용할 수 있습니다. Redis 명령을 실행하는 옵션은 여러 가지가 있습니다.

-	표준 또는 프리미엄 캐시를 사용하는 경우 [Redis 콘솔](cache-configure.md#redis-console)을 사용하여 Redis 명령을 실행할 수 있습니다. 이 방법은 미리 보기 포털에서 Redis 명령을 안전하게 실행하는 방법입니다.
-	Redis 명령줄 도구를 사용할 수도 있습니다. 이 도구를 사용하려면 다음 단계를 수행합니다.
	-	[Redis 명령줄 도구](https://github.com/MSOpenTech/redis/releases/download/win-2.8.19.1/redis-2.8.19.zip)를 다운로드합니다.
	-	`redis-cli.exe`를 사용하여 캐시에 연결합니다. 다음 예제와 같이 -h 스위치를 사용하여 캐시 끝점을 전달하고 -a를 사용하여 키를 전달합니다.
		-	`redis-cli -h <your cache name>.redis.cache.windows.net -a <key>`
	-	Redis 명령줄 도구는 SSL 포트에서 작동하지 않지만, [Redis용 ASP.NET 세션 상태 공급자 미리 보기 릴리스 발표](http://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx) 블로그 게시물의 지침에 따라 `stunnel`과 같은 유틸리티를 사용하여 도구를 SSL 포트에 안전하게 연결할 수 있습니다.

<a name="cache-common-patterns"></a>
## 몇 가지 일반적인 캐시 패턴 및 고려 사항은 무엇인가요?

-	Microsoft Patterns & Practices에는 다음과 같은 지침이 있습니다.
	-	[캐싱 지침](https://github.com/mspnp/azure-guidance/blob/master/Caching.md)
	-	[Azure 클라우드 응용 프로그램 디자인 및 구현 지침](https://github.com/mspnp/azure-guidance)
-	[Azure Redis Cache를 사용하는 일반적인 캐시 패턴](cache-howto-common-cache-patterns.md)

<a name="cache-reference"></a>
## 다른 일부 Azure 서비스와 달리 Azure Redis Cache에는 왜 MSDN 클래스 라이브러리 참조가 없나요?

Microsoft Azure Redis Cache는 많이 사용되는 오픈 소스 Redis Cache를 기반으로 하며 Microsoft에서 관리하는 안전한 전용 Redis Cache에 액세스할 수 있게 합니다. 다양한 프로그래밍 언어에 대해 다양한 [Redis 클라이언트](http://redis.io/clients)를 사용할 수 있습니다. 각 클라이언트에는 [Redis 명령](http://redis.io/commands)을 사용하여 Redis Cache 인스턴스를 호출하는 자체 API가 있습니다.

클라이언트마다 다르기 때문에 MSDN에 하나의 중앙 집중식 클래스 참조는 없습니다. 대신, 각 클라이언트가 자체 참조 설명서를 유지 관리합니다. 참조 설명서 외에도 Azure.com의 [Redis Cache 설명서](http://azure.microsoft.com/documentatgion/services/redis-cache/) 페이지에는 다양한 언어 및 캐시 클라이언트를 사용하여 Azure Redis Cache를 시작하는 방법을 보여 주는 여러 자습서가 있습니다.


## 내게 적합한 Azure 캐시 기능

>[AZURE.IMPORTANT]신규 개발에서는 항상 Azure Redis Cache를 사용하는 것이 좋습니다.

Azure 캐시에는 현재 다음과 같은 세 가지 제품이 있습니다.

-	Azure Redis 캐시(영문)
-	Azure 관리된 캐시 서비스
-	Azure In-Role Cache


### Azure Redis 캐시(영문)
Azure Redis Cache가 이제 최대 53GB 크기로 출시되었으며 99.9%의 가용성 SLA를 제공합니다. 새 [프리미엄 계층](cache-premium-tier.md)이 미리 보기에 있으며 최대 530GB 크기와 클러스터링, VNET 및 지속성에 대한 지원을 제공합니다.

Azure Redis Cache에서 고객은 Microsoft에서 관리하는 안전한 전용 Redis Cache를 사용할 수 있습니다. Redis에서 제공하는 다양한 기능 집합 및 에코시스템을 활용하면서 Microsoft의 안정적인 호스팅 및 모니터링 기능을 이용할 수 있습니다.

Redis는 키 값 쌍만 다루는 기존 캐시와 달리 높은 성능의 데이터 형식 때문에 널리 사용됩니다. 또한 Redis는 이러한 형식에서의 원자성 작업 실행도 지원합니다(예: 문자열에 추가, 해시에서 값 증분, 목록에 푸시, 교집합, 합집합 및 차집합 계산 또는 정렬된 집합에서 가장 높은 순위의 구성원 가져오기). 기타 기능으로는 트랜잭션, 게시/알림, Lua 스크립팅, TTL(Time-to-Live)이 제한된 키 및 보다 일반적인 캐시 역할을 하도록 Redis를 설정하는 구성 설정이 있습니다.

Redis 성공의 또 다른 중요한 측면은 이를 기반으로 구축된 정상적이고 활발한 오픈 소스 에코시스템입니다. 이는 여러 언어로 사용할 수 있는 다양한 Redis 클라이언트의 집합에 반영됩니다. 따라서 Azure 내에서 작성하는 거의 모든 작업에 이 캐시를 사용할 수 있습니다.

Azure Redis Cache를 시작하는 방법에 대한 자세한 내용은 [Azure Redis Cache를 사용하는 방법](cache-dotnet-how-to-use-azure-redis-cache.md) 및 [Azure Redis Cache 설명서](https://azure.microsoft.com/documentation/services/redis-cache/)를 참조하세요.

### 관리된 캐시 서비스
기존 Azure 관리된 캐시 서비스 고객은 기존 서비스를 계속 사용하거나 Azure Redis Cache로 마이그레이션하여 다양한 기능 집합을 활용할 수 있습니다. 또한 Azure 관리된 캐시 서비스가 이제 출시되었으며 99.9%의 가용성 SLA를 제공합니다.

### In-Role Cache
In-Role Cache를 사용하여 캐시를 자체 호스트하는 경우 계속 그렇게 할 수 있습니다. In-Role Cache는 Microsoft 호스티드 서비스가 아닌 자체 호스트 소프트웨어 구성 요소이므로 SLA를 제공하지 않습니다. In-Role Cache 사용자는 Azure Redis Cache로 마이그레이션하여 풍부한 기능 집합을 활용하고 SLA를 얻을 수 있습니다.

<!---HONumber=Nov15_HO1-->