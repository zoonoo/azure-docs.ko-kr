# 개요
## [Azure Stack이란?](azure-stack-poc.md)
## [새로운 기능](azure-stack-whats-new.md)
## [주요 기능 및 개념](azure-stack-key-features.md)
## [POC 아키텍처](azure-stack-architecture.md)

# 평가 및 배포
## 시작
### [배포 필수 조건](azure-stack-deploy.md)
### [배포](azure-stack-run-powershell-script.md)
### [등록](azure-stack-register.md)
## 방법
### [Azure Stack POC에 연결](azure-stack-connect-azure-stack.md)
### [기본 이미지 추가](azure-stack-add-default-image.md)
### [가상 컴퓨터 프로비전](azure-stack-provision-vm.md)
### [저장소 계정을 만드는](azure-stack-provision-storage-account.md)
### [Azure Stack 테넌트 추가](azure-stack-add-new-user-aad.md)
### [Azure Stack POC 다시 배포](azure-stack-redeploy.md)

# 관리
## 개요
### [지역 관리](azure-stack-region-management.md)
### [포털 사용](azure-stack-manage-portals.md)
## 시작
### [Azure 스택에 연결](azure-stack-connect-azure-stack.md)
### 관리 환경 설정
#### [PowerShell 설치](azure-stack-powershell-install.md)
#### [도구 다운로드](azure-stack-powershell-download.md)
#### [PowerShell 구성](azure-stack-powershell-configure.md)
## 방법
### [업데이트 관리](azure-stack-updates.md)
### [상태 및 경고 모니터링](azure-stack-monitor-health.md)
### [네트워크 리소스 관리](azure-stack-viewing-public-ip-address-consumption-in-tp2.md)
### [저장소 리소스 관리](azure-stack-manage-storage-accounts.md)
### [Windows Azure 팩 VM 관리](azure-stack-manage-windows-azure-pack.md)

# 보안 및 규정 준수
## 방법
### [RBAC 관리](azure-stack-manage-permissions.md)
### [AD FS 사용자 추가](azure-stack-add-users-adfs.md)
### [서비스 주체 만들기](Azure-stack-create-service-principals.md)

# 서비스 제공
## 시작
### 계획, 제품 및 할당량 만들기
#### [할당량 설정](azure-stack-setting-quotas.md)
#### [계획 만들기](azure-stack-create-plan.md)
#### [제품 만들기](azure-stack-create-offer.md)
#### [제품 구독](azure-stack-subscribe-plan-provision-vm.md)
#### [Azure Stack에서 제품 위임](azure-stack-delegated-provider.md)
## 방법
### SQL 또는 MySQL을 PaaS로 제공
#### [MySQL 리소스 공급자 배포](azure-stack-mysql-resource-provider-deploy.md)
#### [SQL 리소스 공급자 배포](azure-stack-sql-resource-provider-deploy.md)
### App Service를 PaaS로 제공
#### [Azure Stack 개요에 대한 App Service](azure-stack-app-service-overview.md)
#### [시작하기 전에](azure-stack-app-service-before-you-get-started.md)
#### [App Service 리소스 공급자 배포](azure-stack-app-service-deploy.md)
#### [웹 작업자 역할 추가](azure-stack-app-service-add-worker-roles.md)
#### [배포 소스 구성](azure-stack-app-service-configure-deployment-sources.md)
#### [Azure Stack의 App Service에서 FTP를 사용하도록 설정](azure-stack-app-service-enable-ftp.md)
### Marketplace 채우기
#### [마켓플레이스 개요](azure-stack-marketplace.md)
#### [Marketplace 항목 다운로드](azure-stack-download-azure-marketplace-item.md)
#### [마켓플레이스 항목 만들기 및 게시](azure-stack-create-and-publish-marketplace-item.md)
#### [사용자 지정 가상 컴퓨터 이미지 추가](azure-stack-add-vm-image.md)
#### [Linux 가상 컴퓨터 배포](azure-stack-linux.md)
### 사용량 및 결제
#### [개요](azure-stack-billing-and-chargeback.md)
#### [사용량 데이터 보고](azure-stack-usage-reporting.md)
#### [공급자 사용량 API](azure-stack-provider-resource-api.md)
#### [테넌트 사용량 API](azure-stack-tenant-resource-usage-api.md)
#### [사용 FAQ](azure-stack-usage-related-faq.md)

# 서비스 사용
## Compute
### [VM 이미지 추가](azure-stack-add-vm-image.md)
### [Linux 게스트 사용](azure-stack-linux.md)
## 창고
### [개요](azure-stack-storage-overview.md)
### [차이점 및 고려 사항](azure-stack-acs-differences-tp2.md)
## 네트워크
### [Azure Stack용 iDNS](azure-stack-understanding-dns-in-tp2.md)
### [Azure Stack의 DNS](azure-stack-dns.md)
### [사이트 간 VPN 연결 이해](azure-stack-create-vpn-connection-one-node-tp2.md)
## Key Vault
### [개요](azure-stack-kv-intro.md)
### 시작
#### [포털로 관리](azure-stack-kv-manage-portal.md)
#### [PowerShell로 관리](azure-stack-kv-manage-powershell.md)
### 방법
#### [인증서를 사용하여 VM 만들기](azure-stack-kv-push-secret-into-vm.md)
#### [Key Vault 샘플 앱](azure-stack-kv-sample-app.md)

# 앱 빌드
## 개요
### [Azure Stack에 대한 개발](azure-stack-developer.md)
## 시작
### [Azure 스택에 연결](azure-stack-connect-azure-stack.md)
### 개발 환경 설정
#### [PowerShell 설치](azure-stack-powershell-install.md)
#### [도구 다운로드](azure-stack-powershell-download.md)
#### [PowerShell 구성](azure-stack-powershell-configure.md)
#### [Visual Studio 설치](azure-stack-install-visual-studio.md)
## 방법
### [정책 모듈 사용](azure-stack-policy-module.md)
### 템플릿 사용
#### [템플릿 개요](azure-stack-arm-templates.md)
#### [템플릿 개발](azure-stack-develop-templates.md)
#### [템플릿 확인](azure-stack-validate-templates.md)
#### [포털을 사용하여 배포](azure-stack-deploy-template-portal.md)
#### [PowerShell을 사용하여 배포](azure-stack-deploy-template-powershell.md)
#### [Visual Studio를 사용하여 배포](azure-stack-deploy-template-visual-studio.md)

# 문제 해결
## [알려진 문제](azure-stack-troubleshooting.md)
## [Azure Stack의 진단](azure-stack-diagnostics.md)

# 참조
## [API 버전 프로필 관리](azure-stack-version-profiles.md)

# 리소스
## [MSDN 포럼](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStack)  
## [스택 오버플로](http://stackoverflow.com/questions/tagged/azure-stack)

