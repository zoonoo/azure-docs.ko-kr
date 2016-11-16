# 아키텍처

## 클라우드 기본 사항

### [Azure용 복원 응용 프로그램 디자인](guidance-resiliency-overview.md)
#### [복원력 검사 목록](guidance-resiliency-checklist.md)
#### [실패 모드 분석](guidance-resiliency-failure-mode-analysis.md)

#### [Microsoft Azure에 빌드된 응용 프로그램에 대한 재해 복구](..\resiliency\resiliency-disaster-recovery-azure-applications.md)
#### [Microsoft Azure에 빌드된 응용 프로그램에 대한 재해 복구 및 고가용성](..\resiliency\resiliency-disaster-recovery-high-availability-azure-applications.md)
#### [Microsoft Azure에 빌드된 응용 프로그램에 대한 고가용성](..\resiliency\resiliency-high-availability-azure-applications.md)
#### [고가용성 검사 목록](..\resiliency\resiliency-high-availability-checklist.md)
#### [Azure 서비스 관련 복원력 지침](..\resiliency\resiliency-service-guidance-index.md)
#### [데이터 손상 또는 삭제 실수로부터 복구](..\resiliency\resiliency-technical-guidance-recovery-data-corruption.md)
#### [Azure에서 로컬 오류로부터 복구](..\resiliency\resiliency-technical-guidance-recovery-local-failures.md)
#### [지역 전체의 서비스 중단으로부터 복구](..\resiliency\resiliency-technical-guidance-recovery-loss-azure-region.md)
#### [온-프레미스에서 Azure로 복구](..\resiliency\resiliency-technical-guidance-recovery-on-premises-azure.md)
#### [Azure 복구력 기술 지침](..\resiliency\resiliency-technical-guidance.md)


## 참조 아키텍처

### 계산 참조 아키텍처
#### [Azure에서 Linux VM 실행](guidance-compute-single-vm-linux.md)
#### [Azure에서 Windows VM 실행](guidance-compute-single-vm.md)
#### [Azure에서 확장성 및 가용성을 위해 여러 VM 실행](guidance-compute-multi-vm.md)
#### [Azure에서 N 계층 아키텍처에 대한 Linux VM 실행](guidance-compute-n-tier-vm-linux.md)
#### [Azure에서 N 계층 아키텍처에 대한 Windows VM 실행](guidance-compute-n-tier-vm.md)
#### [고가용성을 위해 여러 지역에서 Linux VM 실행](guidance-compute-multiple-datacenters-linux.md)
#### [고가용성을 위해 여러 지역에서 Windows VM 실행](guidance-compute-multiple-datacenters.md)

### [Azure에 온-프레미스 네트워크 연결](guidance-connecting-your-on-premises-network-to-azure.md)
#### [Azure ExpressRoute를 사용하여 하이브리드 네트워크 아키텍처 구현](guidance-hybrid-network-expressroute.md)
#### [Azure 및 온-프레미스 VPN을 사용하여 하이브리드 네트워크 아키텍처 구현](guidance-hybrid-network-vpn.md)
#### [고가용성 하이브리드 네트워크 아키텍처 구현](guidance-hybrid-network-expressroute-vpn-failover.md)

### Azure에서 클라우드 경계 보호
#### [Azure에서 보안 하이브리드 네트워크 아키텍처 구현](guidance-iaas-ra-secure-vnet-hybrid.md)
#### [Azure와 인터넷 간의 DMZ 구현](guidance-iaas-ra-secure-vnet-dmz.md)

### [Azure에서 ID 관리](guidance-ra-identity.md)
#### [Azure Active Directory 구현](guidance-identity-aad.md)
#### [ADDS(Active Directory Directory Services)를 Azure로 확장](guidance-identity-adds-extend-domain.md)
#### [Azure에서 ADDS(Active Directory Directory Services) 리소스 포리스트 만들기](guidance-identity-adds-resource-forest.md)
#### [Azure에서 ADFS(Active Directory Federation Services) 구현](guidance-identity-adfs.md)

### PaaS 웹 응용 프로그램 참조 아키텍처
#### [Azure 참조 아키텍처: 기본 웹 응용 프로그램](guidance-web-apps-basic.md)
#### [Azure 참조 아키텍처: 고가용성을 요구하는 웹 응용 프로그램](guidance-web-apps-multi-region.md)
#### [웹 응용 프로그램의 확장성 향상](guidance-web-apps-scalability.md)


## 클라우드 디자인 패턴

## 클라우드 응용 프로그램에 대한 모범 사례

### [API 디자인 지침](..\best-practices-api-design.md)
### [API 구현 지침](..\best-practices-api-implementation.md)
### [자동 크기 조정 지침](..\best-practices-auto-scaling.md)
### [가용성 검사 목록](..\best-practices-availability-checklist.md)
### [백그라운드 작업 지침](..\best-practices-background-jobs.md)
### [BCDR(무중단 업무 방식 및 재해 복구): Azure 쌍을 이루는 지역](..\best-practices-availability-paired-regions.md)
### [캐싱 지침](..\best-practices-caching.md)
### [CDN(Content Delivery Network) 지침](..\best-practices-cdn.md)
### [데이터 분할 지침](..\best-practices-data-partitioning.md)
### [모니터링 및 진단 지침](..\best-practices-monitoring.md)
### [Microsoft 클라우드 서비스 및 네트워크 보안](..\best-practices-network-security.md)
### [Azure Resource Manager 템플릿 설계의 패턴](..\best-practices-resource-manager-design-templates.md)
### [Azure 리소스에 대한 권장되는 명명 규칙](guidance-naming-conventions.md)
### [Azure Resource Manager에 대한 보안 고려 사항](..\best-practices-resource-manager-security.md)
### [Azure Resource Manager 템플릿에서 상태 공유](..\best-practices-resource-manager-state.md)
### [재시도 일반 지침](..\best-practices-retry-general.md)
### [서비스 관련 재시도 지침](..\best-practices-retry-service-specific.md)
### [확장성 검사 목록](..\best-practices-scalability-checklist.md)


## 시나리오 가이드

### [Azure의 Elasticsearch 참고 자료](guidance-elasticsearch.md)
#### [Azure에서 Elasticsearch 실행](guidance-elasticsearch-running-on-azure.md)
#### [Azure에서 Elasticsearch에 대한 데이터 수집 성능 튜닝](guidance-elasticsearch-tuning-data-ingestion-performance.md)
#### [Azure에서 Elasticsearch로 데이터 집계 및 쿼리 성능 조정](guidance-elasticsearch-tuning-data-aggregation-and-query-performance.md)
#### [Azure의 Elasticsearch에서 복원력 및 복구 구성](guidance-elasticsearch-configuring-resilience-and-recovery.md)
#### [Azure에서 Elasticsearch에 대한 성능 테스트 환경 만들기](guidance-elasticsearch-creating-performance-testing-environment.md)
#### [Elasticsearch에 대한 JMeter 테스트 계획 구현](guidance-elasticsearch-implementing-jmeter-test-plan.md)
#### [Elasticsearch 성능 테스트를 위해 JMeter JUnit 샘플러 배포](guidance-elasticsearch-deploying-jmeter-junit-sampler.md)
#### [자동화된 Elasticsearch 복원력 테스트 실행](guidance-elasticsearch-running-automated-resilience-tests.md)
#### [자동화된 Elasticsearch 성능 테스트 실행](guidance-elasticsearch-running-automated-performance-tests.md)

### [Microsoft Azure에서 다중 테넌트 응용 프로그램에 대한 ID 관리](guidance-multitenant-identity.md)
#### [Microsoft Azure에서 다중 테넌트 응용 프로그램에 대한 ID 관리 소개](guidance-multitenant-identity-intro.md)
#### [Tailspin 설문 조사 응용 프로그램 정보](guidance-multitenant-identity-tailspin.md)
#### [Azure AD 및 OpenID Connect를 사용하여 다중 테넌트 앱에서 인증](guidance-multitenant-identity-authenticate.md)
#### [다중 테넌트 응용 프로그램에서 클레임 기반 ID 작업](guidance-multitenant-identity-claims.md)
#### [다중 테넌트 응용 프로그램에서 등록 및 테넌트 온보딩](guidance-multitenant-identity-signup.md)
#### [다중 테넌트 응용 프로그램에서 응용 프로그램 역할](guidance-multitenant-identity-app-roles.md)
#### [다중 테넌트 응용 프로그램에서 역할 기반 및 리소스 기반 권한 부여](guidance-multitenant-identity-authorize.md)
#### [다중 테넌트 응용 프로그램에서 백 엔드 웹 API 보안 유지](guidance-multitenant-identity-web-api.md)
#### [다중 테넌트 응용 프로그램에서 액세스 토큰 캐시](guidance-multitenant-identity-token-cache.md)
#### [Azure에서 다중 테넌트 앱에 대해 고객의 AD FS로 페더레이션](guidance-multitenant-identity-adfs.md)
#### [Azure AD에서 액세스 토큰을 가져오는 데 클라이언트 어설션 사용](guidance-multitenant-identity-client-assertion.md)
#### [Azure Key Vault를 사용하여 응용 프로그램 암호 보호](guidance-multitenant-identity-keyvault.md)

#### [고가용성의 가상 어플라이언스 배포](guidance-nva-ha.md)


<!--HONumber=Nov16_HO2-->


