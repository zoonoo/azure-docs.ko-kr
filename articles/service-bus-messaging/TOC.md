# 개요
## [Service Bus 메시징이란?](service-bus-messaging-overview.md)
## [Service Bus 기본 사항](service-bus-fundamentals-hybrid-solutions.md)
## [Service Bus 아키텍처](service-bus-architecture.md)
## [FAQ](service-bus-faq.md)

# 시작
## [네임스페이스 만들기](service-bus-create-namespace-portal.md)
## 큐 사용
### [.NET](service-bus-dotnet-get-started-with-queues.md)
### [Java](service-bus-java-how-to-use-queues.md)
### [Node.js](service-bus-nodejs-how-to-use-queues.md)
### [PHP](service-bus-php-how-to-use-queues.md)
### [Python](service-bus-python-how-to-use-queues.md)
### [Ruby](service-bus-ruby-how-to-use-queues.md)
### [REST](/rest/api/servicebus/queues)
## 토픽 및 구독 사용
### [.NET](service-bus-dotnet-how-to-use-topics-subscriptions.md)
### [Java](service-bus-java-how-to-use-topics-subscriptions.md)
### [Node.js](service-bus-nodejs-how-to-use-topics-subscriptions.md)
### [PHP](service-bus-php-how-to-use-topics-subscriptions.md)
### [Python](service-bus-python-how-to-use-topics-subscriptions.md)
### [Ruby](service-bus-ruby-how-to-use-topics-subscriptions.md)

# 방법
## 계획 및 디자인
### [관리 서비스 ID(미리 보기)](service-bus-managed-service-identity.md)
### [Role-Based Access Control(미리 보기)](service-bus-role-based-access-control.md)
### [프리미엄 메시징](service-bus-premium-messaging.md)
### [Azure 큐와 Service Bus 큐 비교](service-bus-azure-and-service-bus-queues-compared-contrasted.md)
### [다중 계층 Service Bus 응용 프로그램 빌드](service-bus-dotnet-multi-tier-app-using-service-bus-queues.md)
### [성능 최적화](service-bus-performance-improvements.md)
### [지역에서 재해 복구 및 지역에서 복제](service-bus-geo-dr.md)
### [비동기 메시징 및 고가용성](service-bus-async-messaging.md)
### [중단 및 재해 처리](service-bus-outages-disasters.md)

## 개발
### 메시지 처리
#### [큐, 토픽 및 구독](service-bus-queues-topics-subscriptions.md)
#### [메시지, 페이로드 및 직렬화](service-bus-messages-payloads.md)
#### [메시지 전송, 잠금 및 정산](message-transfers-locks-settlement.md)
#### [메시지 시퀀싱 및 타임스탬프](message-sequencing.md)
#### [메시지 만료(TTL(Time To Live))](message-expiration.md)
### [인증 및 권한 부여](service-bus-authentication-and-authorization.md)
#### [ACS에서 SAS로 마이그레이션](service-bus-migrate-acs-sas.md)
#### [공유 액세스 서명을 사용한 인증](service-bus-sas.md)
### [항목 필터 및 작업](topic-filters.md)
### [분할 큐 및 항목](service-bus-partitioning.md)
### [메시지 세션](message-sessions.md)
### AMQP
#### [AMQP 개요](service-bus-amqp-overview.md)
#### [.NET](service-bus-amqp-dotnet.md)
#### [Java](service-bus-amqp-java.md)
#### [Java 메시지 서비스 및 AMQP](service-bus-java-how-to-use-jms-api-amqp.md)
#### [AMQP 프로토콜 가이드](service-bus-amqp-protocol-guide.md)
#### [AMQP 요청-응답 기반 작업](service-bus-amqp-request-response.md)
### 고급 기능
#### [배달하지 못한 편지 큐](service-bus-dead-letter-queues.md)
#### [메시지 프리페치](service-bus-prefetch.md)
#### [중복 메시지 검색](duplicate-detection.md)
#### [메시지 카운터](message-counters.md)
#### [메시지 지연](message-deferral.md)
#### [메시지 찾아보기](message-browsing.md)
#### [엔터티를 자동 전달에 연결](service-bus-auto-forwarding.md)
#### [트랜잭션 처리](service-bus-transactions.md)
#### [쌍을 이루는 네임스페이스 구현](service-bus-paired-namespaces.md)
### [종단 간 추적 및 진단](service-bus-end-to-end-tracing.md)
## 관리
### [Azure Monitoring을 사용하여 Service Bus 모니터링](service-bus-metrics-azure-monitor.md)
### [Service Bus 관리 라이브러리](service-bus-management-libraries.md)
### [진단 로그](service-bus-diagnostic-logs.md)
### [메시징 엔터티 일시 중단 및 다시 활성화](entity-suspend.md)
### [Azure 리소스 관리자 템플릿 사용](service-bus-resource-manager-overview.md)
#### [네임스페이스 만들기](service-bus-resource-manager-namespace.md)
#### [네임스페이스 및 큐 만들기](service-bus-resource-manager-namespace-queue.md)
#### [토픽 및 구독이 있는 네임스페이스 만들기](service-bus-resource-manager-namespace-topic.md)
#### [네임스페이스 및 큐에 대한 권한 부여 규칙 만들기](service-bus-resource-manager-namespace-auth-rule.md)
#### [토픽, 구독 및 규칙이 있는 네임스페이스 만들기](service-bus-resource-manager-namespace-topic-with-rule.md)
#### 
### [Azure PowerShell을 사용하여 엔터티 프로비전](service-bus-manage-with-ps.md)

# 참고 자료
## .NET
### [Microsoft.ServiceBus.Messaging(.NET Framework)](/dotnet/api/microsoft.servicebus.messaging)
### [Microsoft.Azure.ServiceBus(.NET 표준)](/dotnet/api/microsoft.azure.servicebus)
## [Java](/java/api/overview/azure/servicebus)
## [Azure PowerShell](/powershell/module/azurerm.servicebus)
## [REST](/rest/api/servicebus)
## [예외](service-bus-messaging-exceptions.md)
## [할당량](service-bus-quotas.md)
## [SQLFilter 구문](service-bus-messaging-sql-filter.md)
## [SQLRuleAction 구문](service-bus-messaging-sql-rule-action.md)

# 리소스
## [Azure 로드맵](https://azure.microsoft.com/roadmap/?category=enterprise-integration)
## [블로그](https://blogs.msdn.microsoft.com/servicebus/)
## [MSDN 포럼](https://social.msdn.microsoft.com/forums/home?forum=servbus)
## [가격 책정](https://azure.microsoft.com/pricing/details/service-bus/)
## [요금 계산기](https://azure.microsoft.com/pricing/calculator/)
## [가격 정보](service-bus-pricing-billing.md)
## [샘플](service-bus-samples.md)
## [ServiceBus360](https://www.servicebus360.com/)
## [Service Bus 탐색기](https://github.com/paolosalvatori/ServiceBusExplorer)
## [서비스 업데이트](https://azure.microsoft.com/updates/?product=service-bus)
## [스택 오버플로](http://stackoverflow.com/questions/tagged/azureservicebus)
## [비디오](https://azure.microsoft.com/documentation/videos/index/?services=service-bus)


