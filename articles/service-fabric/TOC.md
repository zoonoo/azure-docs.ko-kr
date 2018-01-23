# [Service Fabric 설명서](/azure/service-fabric)
# 개요
## [Service Fabric이란?](service-fabric-overview.md)

# 빠른 시작
## [.NET 응용 프로그램 만들기](service-fabric-quickstart-dotnet.md)
## [Linux 컨테이너 응용 프로그램 배포](service-fabric-quickstart-containers-linux.md)
## [Windows 컨테이너 응용 프로그램 배포](service-fabric-quickstart-containers.md)
## Java 빠른 시작
### [Spring Boot 응용 프로그램 배포](service-fabric-quickstart-java-spring-boot.md)
### [Reliable Services 응용 프로그램 배포](service-fabric-quickstart-java-reliable-services.md)

# 자습서
## .NET 앱 배포
### [1- .NET 응용 프로그램 빌드](service-fabric-tutorial-create-dotnet-app.md)
### [2- 응용 프로그램 배포](service-fabric-tutorial-deploy-app-to-party-cluster.md)
### [3- CI/CD 구성](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)
### [4- 모니터링 및 진단](service-fabric-tutorial-monitoring-aspnet.md)

## 기존 .NET 앱 컨테이너화
### [1- Docker Compose를 사용하여 .NET 앱 배포](service-fabric-host-app-in-a-container.md)
### [2 - 컨테이너 모니터링](service-fabric-tutorial-monitoring-wincontainers.md)

## Linux 컨테이너 앱 만들기
### [1 - 컨테이너 이미지 만들기](service-fabric-tutorial-create-container-images.md)
### [2 - 컨테이너 패키지 및 배포](service-fabric-tutorial-package-containers.md)
### [3 - 장애 조치(failover) 및 비율 크기 조정](service-fabric-tutorial-containers-failover.md)

## 클러스터 만들기 및 관리
### 1 - Azure에서 클러스터 만들기
#### [1a- Windows 클러스터 만들기](service-fabric-tutorial-create-vnet-and-windows-cluster.md)
#### [1b- Linux 클러스터 만들기](service-fabric-tutorial-create-vnet-and-linux-cluster.md)
### [2-클러스터 크기 조정](service-fabric-tutorial-scale-cluster.md)
### [3- 클러스터 런타임 업그레이드](service-fabric-tutorial-upgrade-cluster.md)
### [4- Service Fabric을 사용하여 API Management 배포](service-fabric-tutorial-deploy-api-management.md)

# 샘플
## [코드 샘플](https://azure.microsoft.com/resources/samples/?service=service-fabric)
## [Azure PowerShell](service-fabric-powershell-samples.md)
## [Service Fabric CLI](samples-cli.md)

# 개념
## [마이크로 서비스 이해](service-fabric-overview-microservices.md)
## [큰 그림](service-fabric-content-roadmap.md)
## [응용 프로그램 시나리오](service-fabric-application-scenarios.md)
## [패턴 및 시나리오](service-fabric-patterns-and-scenarios.md)
## [Architecture](service-fabric-architecture.md)
## [용어](service-fabric-technical-overview.md)

## [지원되는 프로그래밍 모델](service-fabric-choose-framework.md)
### [컨테이너](service-fabric-containers-overview.md)
#### [Docker Compose(미리 보기)](service-fabric-docker-compose.md)
#### [리소스 관리](service-fabric-resource-governance.md)
### [Reliable Services](service-fabric-reliable-services-introduction.md)
#### [Reliable Services 수명 주기 - C#](service-fabric-reliable-services-lifecycle.md)
#### [Reliable Services 수명 주기 - Java](service-fabric-reliable-services-lifecycle-java.md)
#### [신뢰할 수 있는 컬렉션](service-fabric-reliable-services-reliable-collections.md)
#### [신뢰할 수 있는 컬렉션 지침 및 권장 사항](service-fabric-reliable-services-reliable-collections-guidelines.md)
#### [신뢰할 수 있는 컬렉션 작업](service-fabric-work-with-reliable-collections.md)
#### [트랜잭션 및 잠금](service-fabric-reliable-services-reliable-collections-transactions-locks.md)
#### [신뢰할 수 있는 동시 큐](service-fabric-reliable-services-reliable-concurrent-queue.md)
#### [신뢰할 수 있는 컬렉션 serialization](service-fabric-reliable-services-reliable-collections-serialization.md)
#### [신뢰할 수 있는 상태 관리자 및 신뢰할 수 있는 컬렉션 내부](service-fabric-reliable-services-reliable-collections-internals.md)
#### [고급 사용](service-fabric-reliable-services-advanced-usage.md)

### [Reliable Actors](service-fabric-reliable-actors-introduction.md)
#### [Architecture](service-fabric-reliable-actors-platform.md)
#### [수명 주기 및 가비지 수집](service-fabric-reliable-actors-lifecycle.md)
#### [상태 관리](service-fabric-reliable-actors-state-management.md)
#### [다형성](service-fabric-reliable-actors-polymorphism.md)
#### [다시 표시](service-fabric-reliable-actors-reentrancy.md)
#### [형식 직렬화](service-fabric-reliable-actors-notes-on-actor-type-serialization.md)

## 응용 프로그램 및 서비스
### [응용 프로그램 모델](service-fabric-application-model.md)
### [응용 프로그램 및 서비스 매니페스트](service-fabric-application-and-service-manifests.md)
### [호스팅 모델](service-fabric-hosting-model.md)

### [서비스 상태](service-fabric-concepts-state.md)
### [서비스 분할](service-fabric-concepts-partitioning.md)
### [응용 프로그램 확장성](service-fabric-concepts-scalability.md)
### [서비스 가용성](service-fabric-availability-services.md)
### [복제 및 인스턴스 수명 주기](service-fabric-concepts-replica-lifecycle.md)
### [재구성](service-fabric-concepts-reconfiguration.md)

### [서비스 통신](service-fabric-connect-and-communicate-with-services.md)
#### [DNS 서비스](service-fabric-dnsservice.md)
#### [역방향 프록시](service-fabric-reverseproxy.md)
#### [보안 통신에 대한 역방향 프록시 구성](service-fabric-reverseproxy-configure-secure-communication.md)
#### [역방향 프록시 진단](service-fabric-reverse-proxy-diagnostics.md)
#### [ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md)

### [응용 프로그램 수명 주기](service-fabric-application-lifecycle.md)
#### [응용 프로그램 업그레이드](service-fabric-application-upgrade.md)
##### [구성](service-fabric-visualstudio-configure-upgrade.md)
##### [응용 프로그램 업그레이드 매개 변수](service-fabric-application-upgrade-parameters.md)
##### [응용 프로그램 업그레이드에서 데이터 직렬화](service-fabric-application-upgrade-data-serialization.md)
##### [응용 프로그램 업그레이드 고급 토픽](service-fabric-application-upgrade-advanced.md)
#### [여러 환경에 대한 응용 프로그램 관리](service-fabric-manage-multiple-environment-app-configuration.md)
#### [오류 분석을 사용하여 앱 테스트](service-fabric-testability-overview.md)
#### [ImageStoreConnectionString 설정](service-fabric-image-store-connection-string.md)

### [서비스 리소스](service-fabric-service-manifest-resources.md)

## [클러스터](service-fabric-deploy-anywhere.md)
### [클러스터 보안](service-fabric-cluster-security.md)
### [Linux와 Windows 간의 기능 차이점](service-fabric-linux-windows-differences.md)
### Azure의 클러스터
#### [노드 유형 및 VM Scale Sets](service-fabric-cluster-nodetypes.md)
#### [클러스터 네트워킹 패턴](service-fabric-patterns-networking.md)
### [클러스터 리소스 관리자](service-fabric-cluster-resource-manager-introduction.md)
#### [Architecture](service-fabric-cluster-resource-manager-architecture.md)
#### [클러스터 설명](service-fabric-cluster-resource-manager-cluster-description.md)
#### [응용 프로그램 그룹 개요](service-fabric-cluster-resource-manager-application-groups.md)
#### [클러스터 Resource Manager 설정 구성](service-fabric-cluster-resource-manager-configure-services.md)
#### [리소스 사용 메트릭](service-fabric-cluster-resource-manager-metrics.md)
#### [서비스 선호도 사용](service-fabric-cluster-resource-manager-advanced-placement-rules-affinity.md)
#### [서비스 배치 정책](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md)
#### [클러스터 관리](service-fabric-cluster-resource-manager-management-integration.md)
#### [클러스터 조각 모음](service-fabric-cluster-resource-manager-defragmentation-metrics.md)
#### [클러스터 균형 조정](service-fabric-cluster-resource-manager-balancing.md)
#### [제한](service-fabric-cluster-resource-manager-advanced-throttling.md)
#### [서비스 이동](service-fabric-cluster-resource-manager-movement-cost.md)

## 모니터링 및 진단
### [응용 프로그램 모니터링 및 진단](service-fabric-diagnostics-overview.md)
### 이벤트 생성
#### [플랫폼 수준 이벤트 생성](service-fabric-diagnostics-event-generation-infra.md)
##### [조작 채널](service-fabric-diagnostics-event-generation-operational.md)
##### [Reliable Services 이벤트](service-fabric-reliable-services-diagnostics.md)
##### [Reliable Actors 이벤트](service-fabric-reliable-actors-diagnostics.md)
##### [성능 메트릭](service-fabric-diagnostics-event-generation-perf.md)
##### [원격 서비스 모니터링](service-fabric-reliable-serviceremoting-diagnostics.md)
#### [응용 프로그램 수준 이벤트 생성](service-fabric-diagnostics-event-generation-app.md)
### 응용 프로그램 및 클러스터 상태 검사
#### [Service Fabric 상태 모니터링](service-fabric-health-introduction.md)
#### [서비스 상태 보고 및 확인](service-fabric-diagnostics-how-to-report-and-check-service-health.md)
#### [사용자 지정 상태 보고서 추가](service-fabric-report-health.md)
#### [시스템 상태 보고서를 사용하여 문제 해결](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)
#### [상태 보고서 보기](service-fabric-view-entities-aggregated-health.md)
### 이벤트 집계
#### [EventFlow를 사용하여 이벤트 집계](service-fabric-diagnostics-event-aggregation-eventflow.md)
#### Azure 진단을 사용하여 이벤트 집계
##### [Windows](service-fabric-diagnostics-event-aggregation-wad.md)
##### [Linux](service-fabric-diagnostics-event-aggregation-lad.md)
### 이벤트 분석
#### [Application Insights를 사용하여 이벤트 분석](service-fabric-diagnostics-event-analysis-appinsights.md)
#### [OMS를 사용하여 이벤트 분석](service-fabric-diagnostics-event-analysis-oms.md)
### [로컬 클러스터 문제 해결](service-fabric-troubleshoot-local-cluster-setup.md)

## [API Management와 통합](service-fabric-api-management-overview.md)

# 방법 가이드
## 개발 환경 설정
### [Windows](service-fabric-get-started.md)
### [Linux](service-fabric-get-started-linux.md)
### [Mac OS](service-fabric-get-started-mac.md)
### [Service Fabric CLI 설정](service-fabric-cli.md)

## 계획 및 준비
### [클러스터 용량 계획](service-fabric-cluster-capacity.md)
### [독립 실행형 클러스터 배포에 대한 계획](service-fabric-cluster-standalone-deployment-preparation.md)
### [재해 복구 준비](service-fabric-disaster-recovery.md)
### [응용 프로그램 용량 계획](service-fabric-capacity-planning.md)

## 첫 번째 항목 만들기...
### [Visual Studio에서 C# 응용 프로그램](service-fabric-create-your-first-application-in-visual-studio.md)
### [Windows 컨테이너 응용 프로그램](service-fabric-get-started-containers.md)
### [Linux 컨테이너 응용 프로그램](service-fabric-get-started-containers-linux.md)
### [Windows의 C# Reliable Services 응용 프로그램](service-fabric-reliable-services-quick-start.md)
### [Linux의 Java Reliable Services 응용 프로그램](service-fabric-reliable-services-quick-start-java.md)
### [Linux의 C# Reliable Services 응용 프로그램](service-fabric-create-your-first-linux-application-with-csharp.md)
### [Windows의 C# Reliable Actors 응용 프로그램](service-fabric-reliable-actors-get-started.md)
### [Linux의 Java Reliable Actors 응용 프로그램](service-fabric-create-your-first-linux-application-with-java.md)
### [Windows의 게스트 실행 가능 응용 프로그램](quickstart-guest-app.md)
### [독립 실행형 클러스터](service-fabric-get-started-standalone-cluster.md)

## 응용 프로그램 빌드

### 게스트 실행 가능한 서비스 빌드
#### [게스트 실행 파일 배포](service-fabric-deploy-existing-app.md)
#### [여러 개의 게스트 실행 파일 배포](service-fabric-deploy-multiple-apps.md)
### 컨테이너 서비스 빌드
#### [컨테이너 보안](service-fabric-securing-containers.md)
#### [Docker Compose(미리 보기)](service-fabric-docker-compose.md)
#### [컨테이너 및 서비스에 대한 리소스 관리](service-fabric-resource-governance.md)
#### [볼륨 및 로깅 드라이버](service-fabric-containers-volume-logging-drivers.md)
#### [컨테이너 내 서비스](service-fabric-services-inside-containers.md)
#### [컨테이너 네트워킹 모드](service-fabric-networking-modes.md)

### Reliable Services 서비스 빌드
#### 신뢰할 수 있는 컬렉션
##### [신뢰할 수 있는 컬렉션 작업](service-fabric-work-with-reliable-collections.md)
##### [신뢰할 수 있는 동시 큐](service-fabric-reliable-services-reliable-concurrent-queue.md)
##### [신뢰할 수 있는 컬렉션 serialization](service-fabric-reliable-services-reliable-collections-serialization.md)

#### 서비스와 통신
##### [Reliable Services와 통신](service-fabric-reliable-services-communication.md)

##### [원격 서비스 - C#](service-fabric-reliable-services-communication-remoting.md)
##### [원격 서비스 - Java](service-fabric-reliable-services-communication-remoting-java.md)
##### [WCF](service-fabric-reliable-services-communication-wcf.md)
##### [보안 통신 - C#](service-fabric-reliable-services-secure-communication.md)
##### [보안 통신 - Java](service-fabric-reliable-services-secure-communication-java.md)

#### [구성](service-fabric-reliable-services-configuration.md)
#### [알림 보내기](service-fabric-reliable-services-notifications.md)
#### [Backup 및 복원](service-fabric-reliable-services-backup-restore.md)

### Reliable Actors 서비스 빌드
#### [알림 보내기](service-fabric-reliable-actors-events.md)
#### [타이머 및 미리 알림 설정](service-fabric-reliable-actors-timers-reminders.md)
#### [KvsActorStateProvider 구성](service-fabric-reliable-actors-kvsactorstateprovider-configuration.md)
#### [통신 설정 구성](service-fabric-reliable-actors-fabrictransportsettings.md)
#### [ReliableDictionaryActorStateProvider 구성](service-fabric-reliable-actors-reliabledictionarystateprovider-configuration.md)

### [이전 Java 응용 프로그램을 마이그레이션하여 Maven 지원](service-fabric-migrate-old-javaapp-to-use-maven.md)

### [보안 통신에 대한 역방향 프록시 구성](service-fabric-reverseproxy-configure-secure-communication.md)

### [ASP.NET Core 서비스 빌드](service-fabric-add-a-web-frontend.md)

### 보안 구성
#### [응용 프로그램 비밀 관리](service-fabric-application-secret-management.md)  
#### [응용 프로그램에 대한 보안 정책 구성](service-fabric-application-runas-security.md)

## Windows/VS 개발 환경에서 작업
### [Visual Studio에서 응용 프로그램 관리](service-fabric-manage-application-in-visual-studio.md)
### [Visual Studio에서 보안 연결 구성](service-fabric-visualstudio-configure-secure-connections.md)
### [VS에서 .NET 서비스 디버그](service-fabric-debugging-your-application.md)
### [일반적인 오류 및 예외](service-fabric-errors-and-exceptions.md)
### [로컬로 모니터링 및 진단](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)
### [Windows에서 Linux 클러스터 설정](service-fabric-local-linux-cluster-windows.md)

## Linux/Eclipse 개발 환경에서 작업
### [Java 개발을 위해 Eclipse 플러그인 시작](service-fabric-get-started-eclipse.md)
### [Eclipse에서 Java 서비스 디버그](service-fabric-debugging-your-application-java.md)
### [로컬로 모니터링 및 진단](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md)

## Cloud Services에서 마이그레이션
### [Cloud Services와 Service Fabric 비교](service-fabric-cloud-services-migration-differences.md)
### [Service Fabric으로 마이그레이션](service-fabric-cloud-services-migration-worker-role-stateless-service.md)
### [권장 사례](/azure/architecture/service-fabric/migrate-from-cloud-services)

## 응용 프로그램 수명 주기 관리
### [응용 프로그램 패키지 작성](service-fabric-package-apps.md)
### [구성 파일과 매개 변수 사용](service-fabric-how-to-parameterize-configuration-files.md)
### [매개 변수를 사용하여 포트 번호 지정](service-fabric-how-to-specify-port-number-using-parameters.md)
### [환경 변수 지정](service-fabric-how-to-specify-environment-variables.md)

### 응용 프로그램 배포 또는 제거
#### [로컬 클러스터에 응용 프로그램 배포](service-fabric-get-started-with-a-local-cluster.md)
#### [Azure 리소스 관리자](service-fabric-application-arm-resource.md)
#### [Azure PowerShell](service-fabric-deploy-remove-applications.md)
#### [Service Fabric CLI](service-fabric-application-lifecycle-sfctl.md)
#### [FabricClient API](service-fabric-deploy-remove-applications-fabricclient.md)

### 응용 프로그램 업그레이드
#### [Azure Powershell을 사용하여 업그레이드](service-fabric-application-upgrade-tutorial-powershell.md)
#### [Visual Studio를 사용하여 업그레이드](service-fabric-application-upgrade-tutorial.md)
#### [응용 프로그램 업그레이드 문제 해결](service-fabric-application-upgrade-troubleshooting.md)

### 응용 프로그램 및 서비스 테스트
#### [서비스 대 서비스 통신 테스트](service-fabric-testability-scenarios-service-communication.md)
#### 오류 시뮬레이션
##### [제어된 비정상 상황 사용](service-fabric-controlled-chaos.md)
##### [테스트 작업 사용](service-fabric-testability-actions.md)
##### [워크로드 중](service-fabric-testability-workload-tests.md)
##### [테스트 시나리오 사용](service-fabric-testability-scenarios.md)
##### [노드 전환 API 소개](service-fabric-node-transition-apis.md)

### 연속 통합 설정
#### [VSTS를 사용하여 연속 통합 설정](service-fabric-set-up-continuous-integration.md)
#### [Jenkins를 사용하여 Linux 응용 프로그램 배포](service-fabric-cicd-your-linux-applications-with-jenkins.md)

## 클러스터 만들기 및 관리
### Azure의 클러스터
#### 생성
##### [Azure 포털](service-fabric-cluster-creation-via-portal.md)
##### [Azure 리소스 관리자](service-fabric-cluster-creation-via-arm.md)
#### 확장
##### [수동](service-fabric-cluster-scale-up-down.md)
##### [프로그래밍 방식](service-fabric-cluster-programmatic-scaling.md)
#### [업그레이드](service-fabric-cluster-upgrade.md)
#### [액세스 제어 설정](service-fabric-cluster-security-roles.md)
#### [구성](service-fabric-cluster-fabric-settings.md)
#### [Load Balancer에서 포트 열기](create-load-balancer-rule.md)
#### [클러스터 인증서 관리](service-fabric-cluster-security-update-certs-azure.md)
#### [삭제](service-fabric-cluster-delete.md)

### 독립 실행형 클러스터
#### 생성
##### [온-프레미스 만들기](service-fabric-cluster-creation-for-windows-server.md)
##### [인증서를 사용하여 보안](service-fabric-windows-cluster-x509-security.md)  
##### [Windows 보안을 사용하여 보안](service-fabric-windows-cluster-windows-security.md)
##### [독립 실행형 패키지의 내용](service-fabric-cluster-standalone-package-contents.md)
#### [규모](service-fabric-cluster-windows-server-add-remove-nodes.md)
#### [액세스 제어 설정](service-fabric-cluster-security-roles.md)
#### [구성](service-fabric-cluster-manifest.md)
#### [업그레이드](service-fabric-cluster-upgrade-windows-server.md)

### [클러스터 시각화](service-fabric-visualizing-your-cluster.md)
### [보안 클러스터에 연결](service-fabric-connect-to-secure-cluster.md)
### [클러스터 노드 패치](service-fabric-patch-orchestration-application.md)

## 모니터링 및 진단
### OMS
#### [OMS Log Analytics 설정](service-fabric-diagnostics-oms-setup.md)
#### [OMS 에이전트 추가](service-fabric-diagnostics-oms-agent.md)
#### [컨테이너 모니터링](service-fabric-diagnostics-oms-containers.md)
### 성능 모니터링
#### [WAD를 사용하여 성능 모니터링](service-fabric-diagnostics-perf-wad.md)


# 참고 자료
## [Azure PowerShell](/powershell/module/azurerm.servicefabric/)
## [PowerShell](/powershell/module/servicefabric/?view=azureservicefabricps)
## [Azure CLI(az sf)](/cli/azure/sf)
## [Service Fabric CLI(sfctl)](service-fabric-sfctl.md)
### [sfctl application](service-fabric-sfctl-application.md)
### [sfctl chaos](service-fabric-sfctl-chaos.md)
### [sfctl cluster](service-fabric-sfctl-cluster.md)
### [sfctl compose](service-fabric-sfctl-compose.md)
### [sfctl is](service-fabric-sfctl-is.md)
### [sfctl node](service-fabric-sfctl-node.md)
### [sfctl partition](service-fabric-sfctl-partition.md)
### [sfctl replica](service-fabric-sfctl-replica.md)
### [sfctl rpm](service-fabric-sfctl-rpm.md)
### [sfctl service](service-fabric-sfctl-service.md)
### [sfctl store](service-fabric-sfctl-store.md)
## [Java API](/java/api/overview/azure/servicefabric)
### [Java REST 클라이언트 API](service-fabric-java-rest-api-usage.md)
## [.NET](/dotnet/api/overview/azure/service-fabric?view=azure-dotnet)
## [REST](/rest/api/servicefabric)
## [서비스 모델 XML 스키마](service-fabric-service-model-schema.md)
## [환경 변수](service-fabric-environment-variables-reference.md)

# 리소스
## [Azure 로드맵](https://azure.microsoft.com/roadmap/)
## [일반적인 질문](service-fabric-common-questions.md)
## [학습 경로](https://azure.microsoft.com/documentation/learning-paths/service-fabric/)
## [MSDN 포럼](https://social.msdn.microsoft.com/Forums/home?forum=AzureServiceFabric)
## [가격 책정](https://azure.microsoft.com/pricing/details/service-fabric/)
## [요금 계산기](https://azure.microsoft.com/pricing/calculator/)
## [샘플 코드](http://aka.ms/servicefabricsamples)
## [지원 옵션](service-fabric-support.md)
## [서비스 업데이트](https://azure.microsoft.com/updates/?product=service-fabric)
## [비디오](https://azure.microsoft.com/documentation/videos/index/?services=service-fabric)
