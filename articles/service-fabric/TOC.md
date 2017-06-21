
# 개요
## [Service Fabric이란?](service-fabric-overview.md)
## [마이크로 서비스 이해](service-fabric-overview-microservices.md)
## [응용 프로그램 시나리오](service-fabric-application-scenarios.md)
## [패턴 및 시나리오](service-fabric-patterns-and-scenarios.md)
## [아키텍처](service-fabric-architecture.md)
## [용어](service-fabric-technical-overview.md)
## [주요 내용](service-fabric-content-roadmap.md)

# 시작
## 개발 환경 설정
### [Windows](service-fabric-get-started.md)
### [Linux](service-fabric-get-started-linux.md)
### [Mac OS](service-fabric-get-started-mac.md)
## 첫 번째 응용 프로그램 만들기
### [Windows에서 C#](service-fabric-create-your-first-application-in-visual-studio.md)
### [Linux에서 Java](service-fabric-create-your-first-linux-application-with-java.md)
### [Linux에서 C#](service-fabric-create-your-first-linux-application-with-csharp.md)
## [로컬 클러스터에 앱 배포](service-fabric-get-started-with-a-local-cluster.md)
## [컨테이너에서 .NET 앱 배포](service-fabric-host-app-in-a-container.md)
## [Azure에서 첫 번째 클러스터 만들기](service-fabric-get-started-azure-cluster.md)
## [첫 번째 독립 실행형 클러스터 만들기](service-fabric-get-started-standalone-cluster.md)
## [첫 번째 컨테이너 앱 만들기](service-fabric-get-started-containers.md)

# 방법
## 응용 프로그램 빌드
  
### 개념
#### [지원되는 프로그래밍 모델](service-fabric-choose-framework.md)
#### [응용 프로그램 모델](service-fabric-application-model.md)
#### [호스팅 모델](service-fabric-hosting-model.md)
#### [서비스 매니페스트 리소스](service-fabric-service-manifest-resources.md)
#### [서비스 상태](service-fabric-concepts-state.md)
#### [서비스 분할](service-fabric-concepts-partitioning.md)
#### [서비스 가용성](service-fabric-availability-services.md)
#### [응용 프로그램 확장성](service-fabric-concepts-scalability.md)
#### [ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md)

### [앱 용량 계획](service-fabric-capacity-planning.md)

### 게스트 실행 가능한 서비스 빌드
#### [게스트 실행 파일 배포](service-fabric-deploy-existing-app.md)
#### [여러 개의 게스트 실행 파일 배포](service-fabric-deploy-multiple-apps.md)

### 컨테이너 서비스 빌드
#### [개요](service-fabric-containers-overview.md)
#### [Windows 컨테이너 배포](service-fabric-deploy-container.md)
#### [Linux 컨테이너 배포](service-fabric-deploy-container-linux.md)
#### [Docker Compose(미리 보기)](service-fabric-docker-compose.md)
#### [컨테이너 및 서비스에 대한 리소스 관리](service-fabric-resource-governance.md)
#### [볼륨 및 로깅 드라이버](service-fabric-containers-volume-logging-drivers.md)

### Reliable Service 서비스 빌드
#### [개요](service-fabric-reliable-services-introduction.md)
#### 개념
##### [Reliable Services 수명 주기 - C#](service-fabric-reliable-services-lifecycle.md)
##### [Reliable Services 수명 주기 - Java](service-fabric-reliable-services-lifecycle-java.md)

#### 신뢰할 수 있는 컬렉션
##### [신뢰할 수 있는 컬렉션](service-fabric-reliable-services-reliable-collections.md)
##### [신뢰할 수 있는 컬렉션 지침 및 권장 사항](service-fabric-reliable-services-reliable-collections-guidelines.md)
##### [신뢰할 수 있는 컬렉션 작업](service-fabric-work-with-reliable-collections.md)
##### [트랜잭션 및 잠금](service-fabric-reliable-services-reliable-collections-transactions-locks.md)
##### [신뢰할 수 있는 동시 큐](service-fabric-reliable-services-reliable-concurrent-queue.md)
##### [신뢰할 수 있는 컬렉션 serialization](service-fabric-reliable-services-reliable-collections-serialization.md)
##### [신뢰할 수 있는 상태 관리자 및 신뢰할 수 있는 컬렉션 내부](service-fabric-reliable-services-reliable-collections-internals.md)

#### 시작
##### [Windows에서 C#](service-fabric-reliable-services-quick-start.md)
##### [Linux에서 Java](service-fabric-reliable-services-quick-start-java.md)

#### [구성](service-fabric-reliable-services-configuration.md)
#### [알림 보내기](service-fabric-reliable-services-notifications.md)
#### [백업 및 복원](service-fabric-reliable-services-backup-restore.md)

#### 서비스와 통신
##### [Reliable Services와 통신](service-fabric-reliable-services-communication.md)
##### [원격 서비스 - C#](service-fabric-reliable-services-communication-remoting.md)
##### [원격 서비스 - Java](service-fabric-reliable-services-communication-remoting-java.md)
##### [WCF](service-fabric-reliable-services-communication-wcf.md)
##### [보안 통신 - C#](service-fabric-reliable-services-secure-communication.md)
##### [보안 통신 - Java](service-fabric-reliable-services-secure-communication-java.md)

#### [고급 사용](service-fabric-reliable-services-advanced-usage.md)

### Reliable Actor 서비스 빌드
#### [개요](service-fabric-reliable-actors-introduction.md)
#### 개념
##### [아키텍처](service-fabric-reliable-actors-platform.md)
##### [수명 주기 및 가비지 수집](service-fabric-reliable-actors-lifecycle.md)
##### [상태 관리](service-fabric-reliable-actors-state-management.md)
##### [다형성](service-fabric-reliable-actors-polymorphism.md)
##### [다시 표시](service-fabric-reliable-actors-reentrancy.md)
##### [형식 직렬화](service-fabric-reliable-actors-notes-on-actor-type-serialization.md)

#### 시작
##### [Windows에서 C#](service-fabric-reliable-actors-get-started.md)
##### [Linux에서 Java](service-fabric-reliable-actors-get-started-java.md)

#### [알림 보내기](service-fabric-reliable-actors-events.md) 
#### [타이머 및 미리 알림 설정](service-fabric-reliable-actors-timers-reminders.md)
#### [KvsActorStateProvider 구성](service-fabric-reliable-actors-kvsactorstateprovider-configuration.md)
#### [통신 설정 구성](service-fabric-reliable-actors-fabrictransportsettings.md) 
#### [ReliableDictionaryActorStateProvider 구성](service-fabric-reliable-actors-reliabledictionarystateprovider-configuration.md)

### 서비스와 통신
#### [서비스 통신](service-fabric-connect-and-communicate-with-services.md)
#### [DNS 서비스](service-fabric-dnsservice.md)
#### [역방향 프록시](service-fabric-reverseproxy.md)
#### [보안 통신에 대한 역방향 프록시 구성](service-fabric-reverseproxy-configure-secure-communication.md)

### [웹 프런트 엔드 추가](service-fabric-add-a-web-frontend.md)

### IDE에서 작업
#### [Java 개발을 위해 Eclipse 플러그인 시작](service-fabric-get-started-eclipse.md)
#### [Visual Studio에서 앱 관리](service-fabric-manage-application-in-visual-studio.md)
#### [Visual Studio에서 보안 연결 구성](service-fabric-visualstudio-configure-secure-connections.md)
#### [여러 환경에 대한 응용 프로그램 구성](service-fabric-manage-multiple-environment-app-configuration.md)

### 보안 구성
#### [응용 프로그램 비밀 관리](service-fabric-application-secret-management.md)  
#### [응용 프로그램에 대한 보안 정책 구성](service-fabric-application-runas-security.md)

### 디버그
#### [VS에서 C# 서비스 디버그](service-fabric-debugging-your-application.md)
#### [Eclipse에서 Java 서비스 디버그](service-fabric-debugging-your-application-java.md)
#### [일반적인 오류 및 예외](service-fabric-errors-and-exceptions.md)

### 로컬로 모니터링 및 진단
#### [Windows](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)
#### [Linux](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md)

### Cloud Services에서 마이그레이션
#### [Cloud Services와 Service Fabric 비교](service-fabric-cloud-services-migration-differences.md)
#### [Service Fabric으로 마이그레이션](service-fabric-cloud-services-migration-worker-role-stateless-service.md)
#### [권장 사례](/azure/architecture/service-fabric/migrate-from-cloud-services)

## 응용 프로그램 수명 주기 관리
### [개요](service-fabric-application-lifecycle.md)
### [응용 프로그램 패키지 작성](service-fabric-package-apps.md)
### [ImageStoreConnectionString 설정 이해](service-fabric-image-store-connection-string.md)
### 응용 프로그램 배포 또는 제거
#### [PowerShell](service-fabric-deploy-remove-applications.md)
#### [Visual Studio](service-fabric-publish-app-remote-cluster.md)
#### [FabricClient API](service-fabric-deploy-remove-applications-fabricclient.md)
### 응용 프로그램 업그레이드
#### [개요](service-fabric-application-upgrade.md)
#### [응용 프로그램 업그레이드 구성](service-fabric-visualstudio-configure-upgrade.md)
#### [응용 프로그램 업그레이드 매개 변수](service-fabric-application-upgrade-parameters.md)
#### 업그레이드
##### [PowerShell](service-fabric-application-upgrade-tutorial-powershell.md)
##### [Visual Studio](service-fabric-application-upgrade-tutorial.md)
#### [응용 프로그램 업그레이드 문제 해결](service-fabric-application-upgrade-troubleshooting.md)
#### [응용 프로그램 업그레이드에서 데이터 직렬화](service-fabric-application-upgrade-data-serialization.md)
#### [응용 프로그램 업그레이드 고급 토픽](service-fabric-application-upgrade-advanced.md)

### 응용 프로그램 및 서비스 테스트
#### [오류 분석 개요](service-fabric-testability-overview.md)
#### [서비스 대 서비스 통신 테스트](service-fabric-testability-scenarios-service-communication.md)
#### 오류 시뮬레이션
##### [제어된 비정상 상황 사용](service-fabric-controlled-chaos.md)
##### [테스트 작업 사용](service-fabric-testability-actions.md)
##### [워크로드 중](service-fabric-testability-workload-tests.md)
##### [테스트 시나리오 사용](service-fabric-testability-scenarios.md)
##### [노드 전환 API 소개](service-fabric-node-transition-apis.md)
#### [응용 프로그램 부하 테스트](service-fabric-vso-load-test.md)

### 연속 통합 설정
#### [VSTS를 사용하여 연속 통합 설정](service-fabric-set-up-continuous-integration.md)
#### [Jenkins를 사용하여 Linux Java 앱 배포](service-fabric-cicd-your-linux-java-application-with-jenkins.md)

## 클러스터 만들기 및 관리

### [개요](service-fabric-deploy-anywhere.md)
### 개념
#### [클러스터 설명](service-fabric-cluster-resource-manager-cluster-description.md)
#### [클러스터 보안](service-fabric-cluster-security.md)
#### [Linux와 Windows 간의 기능 차이점](service-fabric-linux-windows-differences.md)

### 계획 및 준비
#### [용량 계획](service-fabric-cluster-capacity.md)
#### [재해 복구](service-fabric-disaster-recovery.md)

### Azure의 클러스터
#### 개념
##### [노드 유형 및 VM Scale Sets](service-fabric-cluster-nodetypes.md)
##### [클러스터 네트워킹 패턴](service-fabric-patterns-networking.md)
#### 생성 
##### [Azure 포털](service-fabric-cluster-creation-via-portal.md)
##### [Azure 리소스 관리자](service-fabric-cluster-creation-via-arm.md)
##### [Visual Studio 및 Azure Resource Manager](service-fabric-cluster-creation-via-visual-studio.md)
#### 크기 조정 
##### [수동](service-fabric-cluster-scale-up-down.md)
##### [프로그래밍 방식](service-fabric-cluster-programmatic-scaling.md)
#### [업그레이드](service-fabric-cluster-upgrade.md)
#### [액세스 제어 설정](service-fabric-cluster-security-roles.md)
#### [구성](service-fabric-cluster-fabric-settings.md)
#### [클러스터 인증서 관리](service-fabric-cluster-security-update-certs-azure.md) 
#### [삭제](service-fabric-cluster-delete.md)

### 독립 실행형 클러스터
#### [배포를 위한 계획 및 준비](service-fabric-cluster-standalone-deployment-preparation.md)
#### 생성
##### [온-프레미스 만들기](service-fabric-cluster-creation-for-windows-server.md)
##### [Azure 가상 컴퓨터에서 만들기](service-fabric-cluster-creation-with-windows-azure-vms.md)
##### [인증서를 사용하여 보안](service-fabric-windows-cluster-x509-security.md)  
##### [Windows 보안을 사용하여 보안](service-fabric-windows-cluster-windows-security.md)
##### [독립 실행형 패키지의 내용](service-fabric-cluster-standalone-package-contents.md)
#### [규모](service-fabric-cluster-windows-server-add-remove-nodes.md)
#### [액세스 제어 설정](service-fabric-cluster-security-roles.md)
#### [구성](service-fabric-cluster-manifest.md)
#### [업그레이드](service-fabric-cluster-upgrade-windows-server.md) 

### [클러스터 시각화](service-fabric-visualizing-your-cluster.md)
### [보안 클러스터에 연결](service-fabric-connect-to-secure-cluster.md)

### [Azure CLI를 사용하여 클러스터 관리](service-fabric-azure-cli.md)
### [클러스터 노드 패치](service-fabric-patch-orchestration-application.md)

### 클러스터 리소스 관리 및 오케스트레이션
#### [클러스터 Resource Manager 개요](service-fabric-cluster-resource-manager-introduction.md)
#### [클러스터 Resource Manager 아키텍처](service-fabric-cluster-resource-manager-architecture.md)
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
#### [인프라 수준 이벤트 생성](service-fabric-diagnostics-event-generation-infra.md)
##### [Reliable Services 이벤트](service-fabric-reliable-services-diagnostics.md)
##### [Reliable Actors 이벤트](service-fabric-reliable-actors-diagnostics.md)
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

# 참조
## [PowerShell(Azure)](/powershell/module/azurerm.servicefabric/)
## [PowerShell](/powershell/azure/overview?view=azureservicefabricps)
## [Azure CLI](/cli/azure/sf)
## [Java API](/java/api/overview/azure/servicefabric)
## [.NET](/dotnet/api/overview/azure/servicefabric?view=azure-dotnet)
## [REST (영문)](/rest/api/servicefabric)

# 리소스
## [Service Fabric에 대한 일반적인 질문](service-fabric-common-questions.md)
## [Service Fabric 지원 옵션](service-fabric-support.md)
## [샘플 코드](http://aka.ms/servicefabricsamples)
## [학습 경로](https://azure.microsoft.com/documentation/learning-paths/service-fabric/)
## [가격 책정](https://azure.microsoft.com/pricing/details/service-fabric/)
## [서비스 업데이트](https://azure.microsoft.com/updates/?product=service-fabric)
## [MSDN 포럼](https://social.msdn.microsoft.com/Forums/home?forum=AzureServiceFabric)
## [비디오](https://azure.microsoft.com/documentation/videos/index/?services=service-fabric)
