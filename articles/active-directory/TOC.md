# [Azure Active Directory 설명서](index.md)

# 개요
## [Azure Active Directory란?](fundamentals/active-directory-whatis.md)
## [Azure ID 관리 정보](fundamentals/identity-fundamentals.md)
## [Azure ID 솔루션 이해](fundamentals/understand-azure-identity-solutions.md)
## [하이브리드 ID 솔루션 선택](choose-hybrid-identity-solution.md)
## [Azure 구독 연결](fundamentals/active-directory-how-subscriptions-associated-directory.md)
## [보존 및 데이터 고려 사항](fundamentals/active-directory-data-storage-eu.md)
## [FAQ](fundamentals/active-directory-faq.md)
## [새로운 기능](fundamentals/whats-new.md)


# 시작하기
## [Azure AD Premium에 등록](fundamentals/active-directory-get-started-premium.md)
## [사용자 지정 도메인 이름 추가](fundamentals/add-custom-domain.md)
## [회사 브랜딩 구성](fundamentals/customize-branding.md)
## [Azure AD에 사용자 추가](fundamentals/add-users-azure-active-directory.md)
## [사용자에게 라이선스 할당](fundamentals/license-users-groups.md)
## [셀프 서비스 암호 재설정 구성](authentication/quickstart-sspr.md)
## [Azure AD에 조직의 개인 정보 추가](active-directory-properties-area.md)
## [새 테넌트를 만들기 위해 Azure Active Directory에 액세스](fundamentals/active-directory-access-create-new-tenant.md)


# 방법
## 계획 및 디자인
### [Azure AD 아키텍처 이해](fundamentals/active-directory-architecture.md)
### [Azure Active Directory의 클레임 매핑](develop/active-directory-claims-mapping.md)
### [하이브리드 ID 솔루션 배포](hybrid/plan-hybrid-identity-design-considerations-overview.md)
#### 요구 사항 결정
##### [ID](hybrid/plan-hybrid-identity-design-considerations-business-needs.md)
##### [디렉터리 동기화](hybrid/plan-hybrid-identity-design-considerations-directory-sync-requirements.md)
##### [Multi-Factor Auth](hybrid/plan-hybrid-identity-design-considerations-multifactor-auth-requirements.md)
##### [ID 수명 주기 전략](hybrid/plan-hybrid-identity-design-considerations-lifecycle-adoption-strategy.md)
#### [데이터 보안 계획](hybrid/plan-hybrid-identity-design-considerations-data-protection-strategy.md)
##### [데이터 보호](hybrid/plan-hybrid-identity-design-considerations-dataprotection-requirements.md)
##### [콘텐츠 관리](hybrid/plan-hybrid-identity-design-considerations-contentmgt-requirements.md)
##### [액세스 제어](hybrid/plan-hybrid-identity-design-considerations-accesscontrol-requirements.md)
##### [사고 대응](hybrid/plan-hybrid-identity-design-considerations-incident-response-requirements.md)
#### ID 수명 주기 계획
##### [작업](hybrid/plan-hybrid-identity-design-considerations-hybrid-id-management-tasks.md)
##### [채택 전략](hybrid/plan-hybrid-identity-design-considerations-identity-adoption-strategy.md)
#### [다음 단계](hybrid/plan-hybrid-identity-design-considerations-nextsteps.md)
#### [도구 비교](hybrid/plan-hybrid-identity-design-considerations-tools-comparison.md)

## 사용자 관리
### [Azure AD에 새 사용자 추가](fundamentals/add-users-azure-active-directory.md)
### [사용자 프로필 관리](fundamentals/active-directory-users-profile-azure-portal.md)
### [사용자 암호 다시 설정](fundamentals/active-directory-users-reset-password-azure-portal.md)
### [공유 계정](active-directory-sharing-accounts.md)
### [관리자 역할에 사용자 할당](fundamentals/active-directory-users-assign-role-azure-portal.md)
### [다른 디렉터리(B2B)에서 게스트 사용자 추가](b2b/what-is-b2b.md)
#### [B2B 사용자를 추가하는 관리자](b2b/add-users-administrator.md)
#### [B2B 사용자를 추가하는 지식 근로자](b2b/add-users-information-worker.md)
#### [API 및 사용자 지정](b2b/customize-invitation-api.md)
#### [Google 페더레이션](b2b/google-federation.md)
#### [코드 및 Azure PowerShell 샘플](b2b/code-samples.md)
#### [셀프 서비스 등록 포털 샘플](b2b/self-service-portal.md)
#### [초대 전자 메일](b2b/invitation-email-elements.md)
#### [초대 상환](b2b/redemption-experience.md)
#### [초대 없이 B2B 사용자 추가](b2b/add-user-without-invite.md)
#### [초대 허용 또는 차단](b2b/allow-deny-list.md)
#### [B2B에 대한 조건부 액세스](b2b/conditional-access.md)
#### [B2B 공유 정책](b2b/delegate-invitations.md)
#### [역할에 B2B 사용자 추가](b2b/add-guest-to-role.md)
#### [동적 그룹 및 B2B 사용자](b2b/use-dynamic-groups.md)
#### [조직 나가기](b2b/leave-the-organization.md)
#### [감사 및 보고서](b2b/auditing-and-reporting.md)
#### [하이브리드 조직에 대한 B2B](b2b/hybrid-organizations.md)
##### [B2B 사용자에게 로컬 앱에 대한 액세스 권한 부여](b2b/hybrid-cloud-to-on-premises.md)
##### [로컬 사용자에게 클라우드 앱에 대한 액세스 권한 부여](b2b/hybrid-on-premises-to-cloud.md)
#### [B2B 및 Office 365 외부 공유](b2b/o365-external-user.md)
#### [B2B 라이선스](b2b/licensing-guidance.md)
#### [현재 제한 사항](b2b/current-limitations.md)
#### [FAQ](b2b/faq.md)
#### [B2B 문제 해결](b2b/troubleshoot.md)
#### [B2B 사용자 이해](b2b/user-properties.md)
#### [B2B 사용자 토큰](b2b/user-token.md)
#### [Azure AD용 B2B 통합 앱](b2b/configure-saas-apps.md)
#### [B2B 사용자 클레임 매핑](b2b/claims-mapping.md)
#### [B2B 공동 작업 및 B2C 비교](b2b/compare-with-b2c.md)
#### [B2B에 대한 지원 받기](b2b/get-support.md)

## [그룹 및 구성원 관리](fundamentals/active-directory-manage-groups.md)
### [그룹 관리](fundamentals/active-directory-groups-create-azure-portal.md)
### [그룹 및 해당 멤버 삭제](fundamentals/active-directory-groups-delete-group.md)
### [그룹 설정 관리](fundamentals/active-directory-groups-settings-azure-portal.md)
## [보고서 관리](reports-monitoring/overview-reports.md)
### [로그인 활동](reports-monitoring/concept-sign-ins.md)
### [감사 활동](reports-monitoring/concept-audit-logs.md)
### [위험에 노출된 사용자](reports-monitoring/concept-user-at-risk.md)
### [위험한 로그인](reports-monitoring/concept-risky-sign-ins.md)
### [위험 이벤트](reports-monitoring/concept-risk-events.md)
### [Azure Monitor를 사용한 로그 모니터링](reports-monitoring/concept-activity-logs-in-azure-monitor.md)
### [FAQ](reports-monitoring/reports-faq.md)

### 작업
#### [로그인 보고서 다운로드](reports-monitoring/quickstart-download-sign-in-report.md)
#### [감사 보고서 다운로드](reports-monitoring/quickstart-download-audit-report.md)
#### [명명된 위치 구성](reports-monitoring/quickstart-configure-named-locations.md)
#### [활동 보고서 보기](reports-monitoring/howto-find-activity-reports.md)
#### [Azure AD Power BI 콘텐츠 팩 사용](reports-monitoring/howto-power-bi-content-pack.md)
#### [위험 플래그가 지정된 사용자 수정](reports-monitoring/howto-remediate-users-flagged-for-risk.md)
#### [Azure 이벤트 허브로 활동 로그 경로 지정](reports-monitoring/quickstart-azure-monitor-stream-logs-to-event-hub.md)
#### [Azure Storage 계정에 활동 로그 보관](reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md)
#### [Azure Monitor를 사용하여 활동 로그를 Splunk와 통합](reports-monitoring/tutorial-integrate-activity-logs-with-splunk.md)
#### [Azure Monitor를 사용하여 활동 로그를 SumoLogic과 통합](reports-monitoring/howto-integrate-activity-logs-with-sumologic.md)

### 참고 자료
#### [보존](reports-monitoring/reference-reports-data-retention.md)
#### [대기 시간](reports-monitoring/reference-reports-latencies.md)
#### [감사 활동 참조](reports-monitoring/reference-audit-activities.md)
#### [로그인 작업 오류 코드](reports-monitoring/reference-sign-ins-error-codes.md)
#### [Azure Monitor에서 감사 로그 스키마 해석](reports-monitoring/reference-azure-monitor-audit-log-schema.md)
#### [Azure Monitor에서 로그인 로그 스키마 해석](reports-monitoring/reference-azure-monitor-sign-ins-log-schema.md)

### 문제 해결
#### [Azure AD 활동 로그에서 데이터 누락](reports-monitoring/troubleshoot-missing-audit-data.md)
#### [다운로드에서 누락된 데이터](reports-monitoring/troubleshoot-missing-data-download.md)
#### [Azure AD 활동 로그 콘텐츠 팩 오류](reports-monitoring/troubleshoot-content-pack.md)
#### [Azure AD 보고 API의 오류](reports-monitoring/troubleshoot-graph-api.md)

### [프로그래밍 방식 액세스](reports-monitoring/concept-reporting-api.md)
#### [필수 구성 요소](reports-monitoring/howto-configure-prerequisites-for-reporting-api.md)
#### [인증서 사용](reports-monitoring/tutorial-access-api-with-certificates.md)

## [암호 관리](authentication/concept-sspr-howitworks.md)

## 앱 관리
### [개요](manage-apps/what-is-application-management.md)
### [시작](manage-apps/plan-an-application-integration.md)
### [SaaS 앱 통합 자습서](saas-apps/tutorial-list.md)

### [SaaS 앱에 대한 사용자 프로비전 및 프로비전 해제](manage-apps/user-provisioning.md) 
#### [앱 통합 자습서](saas-apps/tutorial-list.md) 
#### [SCIM 사용 앱에 대한 프로비전 자동화](manage-apps/use-scim-to-provision-users-and-groups.md) 
#### [특성 매핑 사용자 지정](manage-apps/customize-application-attributes.md) 
#### [특성 매핑을 위한 식 작성](manage-apps/functions-for-customizing-application-data.md) 
#### [범위 지정 필터 사용](manage-apps/define-conditional-rules-for-provisioning-user-accounts.md) 
#### [자동 사용자 프로비전에 대한 보고](manage-apps/check-status-user-account-provisioning.md) 
#### [사용자 프로비전 문제 해결](active-directory-application-provisioning-content-map.md) 

### [앱 프록시를 사용하여 원격으로 앱에 액세스](manage-apps/application-proxy.md)
#### 시작하기
##### [앱 프록시 사용](manage-apps/application-proxy-enable.md)
##### [앱 게시](manage-apps/application-proxy-publish-azure-portal.md)
##### [사용자 지정 도메인](manage-apps/application-proxy-configure-custom-domain.md)
#### [Single Sign-On](manage-apps/application-proxy-single-sign-on.md)
##### [KCD와 SSO](manage-apps/application-proxy-configure-single-sign-on-with-kcd.md)
##### [헤더와 SSO](manage-apps/application-proxy-configure-single-sign-on-with-ping-access.md)
##### [암호 보관을 사용하는 SSO](manage-apps/application-proxy-configure-single-sign-on-password-vaulting.md)
#### 개념
##### [커넥터](manage-apps/application-proxy-connectors.md)
##### [보안](manage-apps/application-proxy-security.md)
##### [네트워크](manage-apps/application-proxy-network-topology.md)


##### [TMG 또는 UAG에서 업그레이드](manage-apps/application-proxy-migration.md)

#### 고급 구성
##### [별도 네트워크에 게시](manage-apps/application-proxy-connector-groups.md)
##### [프록시 서버](manage-apps/application-proxy-configure-connectors-with-proxy-servers.md)
##### [클레임 인식 앱](manage-apps/application-proxy-configure-for-claims-aware-applications.md)
##### [네이티브 클라이언트 앱](manage-apps/application-proxy-configure-native-client-application.md)
##### [자동 설치](manage-apps/application-proxy-register-connector-powershell.md)
##### [사용자 지정 홈 페이지](manage-apps/application-proxy-configure-custom-home-page.md)
##### [인라인 링크 변환](manage-apps/application-proxy-configure-hard-coded-link-translation.md)
##### [와일드카드](manage-apps/application-proxy-wildcard.md)
##### [개인 데이터 제거](manage-apps/application-proxy-remove-personal-data.md)


#### 게시 연습
##### [원격 데스크톱](manage-apps/application-proxy-integrate-with-remote-desktop-services.md)
##### [SharePoint](manage-apps/application-proxy-integrate-with-sharepoint-server.md)
##### [Microsoft 팀](manage-apps/application-proxy-integrate-with-teams.md)
##### [Tableau](manage-apps/application-proxy-integrate-with-tableau.md)
##### [Qlik](manage-apps/application-proxy-qlik.md)
#### [PowerShell](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#application_proxy_application_management) 

#### [문제 해결](manage-apps/application-proxy-troubleshoot.md)

### 엔터프라이즈 앱 관리
#### [응용 프로그램 추가](manage-apps/add-application-portal.md)
#### [테넌트 앱 보기](manage-apps/view-applications-portal.md)
#### [Single Sign-on 구성](manage-apps/configure-single-sign-on-portal.md)
#### [사용자 할당](manage-apps/assign-user-or-group-access-portal.md)
#### [브랜딩 사용자 지정](manage-apps/change-name-or-logo-portal.md)
#### [사용자 로그인 비활성화](manage-apps/disable-user-sign-in-portal.md)
#### [사용자 제거](manage-apps/remove-user-or-group-access-portal.md)

#### [사용자 계정 프로비전 관리](manage-apps/configure-automatic-user-provisioning-portal.md)

#### [SAML 앱에 대한 고급 인증서 서명](manage-apps/certificate-signing-options.md)
#### [사용자의 환경에서 응용 프로그램 숨기기](manage-apps/hide-application-from-user-portal.md)
### [HRD 정책을 사용하여 로그인 자동 가속 구성](manage-apps/configure-authentication-for-federated-users-portal.md)
### [AD FS 앱을 Azure AD에 마이그레이션](manage-apps/migrate-adfs-apps-to-azure.md) 
### [앱에 대한 액세스 관리](manage-apps/what-is-access-management.md)
#### [SSO 액세스](manage-apps/what-is-single-sign-on.md)
#### [SSO 인증서](manage-apps/manage-certificates-for-federated-single-sign-on.md)
#### [테넌트 제한 사항](manage-apps/tenant-restrictions.md)
#### [SCIM 프로비전 사용자 사용](manage-apps/use-scim-to-provision-users-and-groups.md)

### [Azure AD 응용 프로그램 동의 환경 이해](develop/application-consent-experience.md)

### 문제 해결



#### 액세스 패널
##### [표시되지 않는 앱](manage-apps/access-panel-troubleshoot-application-not-appearing.md)
##### [예기치 않은 앱 표시](manage-apps/access-panel-troubleshoot-unexpected-application.md)
##### [로그인 할 수 없음](manage-apps/access-panel-troubleshoot-web-sign-in-problem.md)
##### [브라우저 확장 설치 시 오류](manage-apps/access-panel-extension-problem-installing.md)
##### [셀프 서비스 앱 액세스 사용 방법](manage-apps/access-panel-manage-self-service-access.md)
##### [셀프 서비스 앱 액세스 사용 시 오류](manage-apps/access-panel-troubleshoot-self-service-access.md)

#### 앱 추가
##### [앱 유형 선택](manage-apps/choose-application-type.md)
##### [일반적인 문제 - 갤러리 앱](manage-apps/adding-gallery-app-common-problems.md)
##### [일반적인 문제 - 비갤러리 앱](manage-apps/adding-non-gallery-app-common-problems.md)

#### 응용 프로그램 프록시
##### [앱 페이지를 표시할 때 문제](manage-apps/application-proxy-page-appearance-broken-problem.md)
##### [너무 긴 응용 프로그램 로드](manage-apps/application-proxy-page-load-speed-problem.md)
##### [응용 프로그램 페이지에서 링크가 작동하지 않음](manage-apps/application-proxy-page-links-broken-problem.md)
##### [내 앱에 대한 포트 열기 작업](manage-apps/application-proxy-connectivity-ports-how-to.md)
##### [내 앱에 대한 커넥터 그룹에서 작동하지 않는 커넥터](manage-apps/application-proxy-connectivity-no-working-connector.md)
##### [관리자 포털에서 구성](manage-apps/application-proxy-config-how-to.md)
##### [내 앱에 Single Sign-On 구성](manage-apps/application-proxy-config-sso-how-to.md)
##### [관리 포털에서 앱을 만들 때 문제](manage-apps/application-proxy-config-problem.md)
##### [Kerberos 제한 위임 구성](manage-apps/application-proxy-back-end-kerberos-constrained-delegation-how-to.md)
##### [PingAccess로 구성](manage-apps/application-proxy-back-end-ping-access-how-to.md)
##### ["Can't Access this Corporate Application"(회사 응용 프로그램에 액세스할 수 없음) 오류](manage-apps/application-proxy-sign-in-bad-gateway-timeout-error.md)
##### [응용 프로그램 프록시 에이전트 커넥터 설치 시 문제](manage-apps/application-proxy-connector-installation-problem.md)


#### 응용 프로그램 등록
##### [응용 프로그램 개체에 대한 필드 입력](develop/registration-config-specific-application-property-how-to.md)
##### [토큰 수명 기본값 변경](develop/registration-config-change-token-lifetime-how-to.md)

#### 인증
##### [엔드포인트 구성](develop/registration-config-how-to.md)

#### 조건부 액세스
##### [고객이 장치 등록 사전 요구 사항을 충족시키지 못함](active-directory-conditional-access.md)
##### [조건부 액세스 정책의 잘못된 설정으로 인해 테넌트가 차단됨](active-directory-conditional-access-device-remediation.md)
##### [회사 네트워크 규칙이 적용되는 방법 및 시기](https://aka.ms/calocation)
##### [사용자가 Azure AD에 등록할 수 있는 장치 수를 늘리는 방법](active-directory-azureadjoin-setup.md)
##### [Exchange Online에 대한 조건부 액세스를 설정하는 방법](https://aka.ms/csforexchange)
##### [Windows 7 장치에 대한 조건부 액세스를 설정하는 방법](active-directory-conditional-access.md#device-based-conditional-access)
##### [조건부 액세스가 지원되는 응용 프로그램](active-directory-conditional-access-supported-apps.md)

#### API 찾기
##### [API 찾기](develop/api-find-an-api-how-to.md)

#### 액세스 관리
##### [사용자 및 그룹을 앱에 할당](manage-apps/methods-for-assigning-users-and-groups.md)
##### [앱에 대한 사용자 액세스 권한 제거](manage-apps/methods-for-removing-user-access.md)
##### [셀프 서비스 앱 할당 구성](manage-apps/manage-self-service-access.md)
##### [할당된 예기치 않은 사용자](manage-apps/ways-users-get-assigned-to-applications.md)
##### [응용 프로그램 목록의 예기치 않은 앱](manage-apps/application-types.md)

#### 다중 테넌트 앱
##### [새 앱 구성](develop/setup-multi-tenant-app.md)
##### [앱 갤러리에 추가](develop/registration-config-multi-tenant-application-add-to-gallery-how-to.md)

#### 권한
##### [API에 대한 사용 권한 선택](develop/perms-for-given-api.md)
##### [내 앱에 권한 부여](develop/registration-config-grant-permissions-how-to.md)
##### [위임된 권한 대 응용 프로그램 권한](develop/delegated-and-app-perms.md)
##### [응용 프로그램 동의](develop/consent-framework.md)

#### 프로비전
##### [소요되는 시간](manage-apps/application-provisioning-when-will-provisioning-finish-specific-user.md)
##### [소요 시간 - 갤러리 앱](manage-apps/application-provisioning-when-will-provisioning-finish.md)
##### [사용자 프로비저닝 구성 - 갤러리 앱](manage-apps/application-provisioning-config-how-to.md)
##### [사용자 프로비저닝 구성 시 문제 - 갤러리 앱](manage-apps/application-provisioning-config-problem.md)
##### [사용자가 갤러리 앱에 프로비전을 구성하는 동안 관리자 자격 증명 저장 시 문제](manage-apps/application-provisioning-config-problem-storage-limit.md)
##### [프로비전되지 않은 사용자 - 갤러리 앱](manage-apps/application-provisioning-config-problem-no-users-provisioned.md)
##### [잘못 프로비전된 사용자 - 갤러리 앱](manage-apps/application-provisioning-config-problem-wrong-users-provisioned.md)

#### SSO(Single sign-on)
##### [메서드 선택](manage-apps/single-sign-on-modes.md)
##### [구성](develop/registration-config-sso-how-to.md)
##### [페더레이션 구성 - 갤러리 앱](manage-apps/configure-federated-single-sign-on-gallery-applications.md)
##### [페더레이션된 일반적인 문제 구성 - 갤러리 앱](manage-apps/configure-federated-single-sign-on-gallery-applications-problems.md)
##### [페더레이션된 구성 - 비갤러리 앱](manage-apps/configure-federated-single-sign-on-non-gallery-applications.md)
##### [페더레이션된 일반적인 문제 구성 - 비갤러리 앱](manage-apps/configure-federated-single-sign-on-non-gallery-applications-problems.md)
##### [암호 구성 - 갤러리 앱](manage-apps/configure-password-single-sign-on-gallery-applications.md)
##### [암호 구성 일반적인 문제 - 갤러리 앱](manage-apps/configure-password-single-sign-on-gallery-applications-problems.md)
##### [암호 구성 - 비갤러리 앱](manage-apps/configure-password-single-sign-on-non-gallery-applications.md)
##### [암호 구성 일반적인 문제 - 비갤러리 앱](manage-apps/configure-password-single-sign-on-non-gallery-applications-problems.md)

#### 사용자 로그인 문제
##### [예기치 않은 동의 확인 프롬프트](manage-apps/application-sign-in-unexpected-user-consent-prompt.md)
##### [사용자 동의 오류](manage-apps/application-sign-in-unexpected-user-consent-error.md)
##### [사용자 지정 포털에서 로그인 시 문제](manage-apps/application-sign-in-other-problem-deeplink.md)
##### [액세스 패널에서 로그인 시 문제](manage-apps/application-sign-in-other-problem-access-panel.md)
##### [응용 프로그램 로그인 페이지에서의 오류](manage-apps/application-sign-in-problem-application-error.md)
##### [암호 Single Sign-On의 문제 - 비 갤러리 앱](manage-apps/application-sign-in-problem-password-sso-non-gallery.md)
##### [암호 Single Sign-On의 문제 - 갤러리 앱](manage-apps/application-sign-in-problem-password-sso-gallery.md)
##### [Microsoft 앱에 로그인 시 문제](manage-apps/application-sign-in-problem-first-party-microsoft.md)
##### [페더레이션된 Single Sign-On의 문제 - 비갤러리 앱](manage-apps/application-sign-in-problem-federated-sso-non-gallery.md)
##### [페더레이션된 Single Sign-On의 문제 - 갤러리 앱](manage-apps/application-sign-in-problem-federated-sso-gallery.md)
##### [사용자 지정 개발 앱의 문제](manage-apps/application-sign-in-problem-custom-dev.md)
##### [온-프레미스 앱의 문제 - 응용 프로그램 프록시](manage-apps/application-sign-in-problem-on-premises-application-proxy.md)

### [앱 개발](active-directory-applications-guiding-developers-for-lob-applications.md)


## 디렉터리 관리
### [Azure AD Connect](hybrid/whatis-hybrid-identity.md)
### 사용자 지정 도메인 이름
#### [빠른 시작](fundamentals/add-custom-domain.md)
### [디렉터리 관리](fundamentals/active-directory-administer.md)
### [엔터프라이즈 상태 로밍](active-directory-windows-enterprise-state-roaming-overview.md)
#### [사용](active-directory-windows-enterprise-state-roaming-enable.md)
#### [그룹 정책 설정](active-directory-windows-enterprise-state-roaming-group-policy-settings.md)
#### [Windows 10 설정](active-directory-windows-enterprise-state-roaming-windows-settings-reference.md)
#### [FAQ](active-directory-windows-enterprise-state-roaming-faqs.md)
#### [문제 해결](active-directory-windows-enterprise-state-roaming-troubleshooting.md)


### [Azure AD Connect를 사용하여 온-프레미스 ID 통합](hybrid/whatis-hybrid-identity.md)

### [토큰 수명](develop/active-directory-configurable-token-lifetimes.md)

## ID 보안

### [Privileged Identity Management](privileged-identity-management/pim-configure.md?toc=%2fazure%2factive-directory%2ftoc.json)

## [Azure에서 AD FS 배포](hybrid/how-to-connect-fed-azure-adfs.md)
### [고가용성](active-directory-adfs-in-azure-with-azure-traffic-manager.md)
### [서명 해시 알고리즘 변경](active-directory-federation-sha256-guidance.md)

## [문제 해결](fundamentals/active-directory-troubleshooting-support-howto.md)

## Azure AD 개념 증명(PoC) 배포
### [PoC 플레이 북: 소개](active-directory-playbook-intro.md)
### [PoC 플레이 북: 재료](active-directory-playbook-ingredients.md)
### [PoC 플레이 북: 구현](active-directory-playbook-implementation.md)
### [PoC 플레이 북: 구성 요소](active-directory-playbook-building-blocks.md)

# 참고 자료
## [코드 샘플](https://azure.microsoft.com/resources/samples/?service=active-directory)
## [Azure PowerShell cmdlet](/powershell/azure/overview)
## [Java API 참조](/java/api)
## [.NET API](/active-directory/adal/microsoft.identitymodel.clients.activedirectory)

# 관련 항목
## [Multi-Factor Authentication](/azure/multi-factor-authentication/)
## [Azure AD Connect](hybrid/whatis-hybrid-identity.md)
## [Azure AD Connect Health](hybrid/whatis-hybrid-identity-health.md)
## [개발자용 Azure AD](./develop/active-directory-how-to-integrate.md)
## [Azure AD Privileged Identity Management](./privileged-identity-management/pim-configure.md)

# 리소스
## [Azure AD 배포 계획](./fundamentals/active-directory-deployment-plans.md)
## [Azure 피드백 포럼](https://feedback.azure.com/forums/169401-azure-active-directory)
## [Azure 로드맵](https://azure.microsoft.com/roadmap/?category=security-identity)
## [MSDN 포럼](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WindowsAzureAD)
## [가격](https://azure.microsoft.com/pricing/details/active-directory/)
## [요금 계산기](https://azure.microsoft.com/pricing/calculator/)
## [서비스 업데이트](https://azure.microsoft.com/updates/?product=active-directory)
## [스택 오버플로](http://stackoverflow.com/questions/tagged/azure-active-directory)
## [비디오](https://azure.microsoft.com/documentation/videos/index/?services=active-directory)
