# [개요](active-directory-b2c-overview.md)

# 시작
## [Azure AD B2C 디렉터리 만들기](active-directory-b2c-get-started.md)
## [응용 프로그램 등록](active-directory-b2c-app-registration.md)
## [기본 제공 정책](active-directory-b2c-reference-policies.md)
## 모바일 및 데스크톱 앱
### [개요](active-directory-b2c-reference-oauth-code.md)
### iOS
#### [MSAL을 사용한 iOS Swift](https://github.com/Azure-Samples/active-directory-b2c-ios-swift-native-msal)
#### [AppAuth를 사용한 iOS ObjC](active-directory-b2c-devquickstarts-ios.md)
### Android
#### [MSAL을 사용한 Android](https://github.com/Azure-Samples/active-directory-b2c-android-native-msal)
#### [AppAuth를 사용한 Android](active-directory-b2c-devquickstarts-android.md)
### [.NET](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop)
### [Xamarin](https://github.com/Azure-Samples/active-directory-b2c-xamarin-native)
## 웹 앱
### [개요](active-directory-b2c-reference-oidc.md)
### [ASP.NET](active-directory-b2c-devquickstarts-web-dotnet-susi.md)
### [ASP.NET Core](https://github.com/Azure-Samples/active-directory-b2c-dotnetcore-webapp)
### [Node.JS](active-directory-b2c-devquickstarts-web-node.md)
## 단일 페이지 앱
### [개요](active-directory-b2c-reference-spa.md)
### [MSAL을 사용하여 JavaScript](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp)
### [Hello를 사용하여 JavaScript](https://github.com/Azure-Samples/active-directory-b2c-javascript-singlepageapp-dotnet-webapi)
## Web API
### [ASP.NET](active-directory-b2c-devquickstarts-api-dotnet.md)
### [ASP.NET Core](https://github.com/Azure-Samples/active-directory-b2c-dotnetcore-webapi)
### [Node.JS](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi)
### [.NET Web API 호출](active-directory-b2c-devquickstarts-web-api-dotnet.md)

# 가이드
## ID 공급자 구성
### [Microsoft 계정](active-directory-b2c-setup-msa-app.md)
### [Facebook](active-directory-b2c-setup-fb-app.md)
### [Google](active-directory-b2c-setup-goog-app.md)
### [Amazon](active-directory-b2c-setup-amzn-app.md)
### [LinkedIn](active-directory-b2c-setup-li-app.md)
### [Twitter](active-directory-b2c-setup-twitter-app.md)
### [Weibo](active-directory-b2c-setup-weibo-app.md)
### [QQ](active-directory-b2c-setup-qq-app.md)
### [WeChat](active-directory-b2c-setup-wechat-app.md)
## [셀프 서비스 암호 재설정 설정하기](active-directory-b2c-reference-sspr.md)
## [UI 사용자 지정](active-directory-b2c-reference-ui-customization.md)
### [사용자 지정을 위한 도우미 도구](active-directory-b2c-reference-ui-customization-helper-tool.md)
## [Azure AD Graph API 사용](active-directory-b2c-devquickstarts-graph-dotnet.md)
## [액세스 토큰 가져오기](active-directory-b2c-access-tokens.md)
## [메일 검증을 사용하지 않도록 설정](active-directory-b2c-reference-disable-ev.md)
## [토큰 구성](active-directory-b2c-token-session-sso.md)
## [MFA 사용](active-directory-b2c-reference-mfa.md)
## [사용자 지정 특성 사용](active-directory-b2c-reference-custom-attr.md)
## [암호 사용자 지정](active-directory-b2c-reference-password-complexity.md)
## [언어 사용자 지정](active-directory-b2c-reference-language-customization.md)
## [보고 API 사용](active-directory-b2c-reference-usage-reporting-api.md)
## [사용자 마이그레이션](active-directory-b2c-user-migration.md)

# ID 경험 프레임워크
## [사용자 지정 정책](active-directory-b2c-overview-custom.md)
## 가이드
### [시작](active-directory-b2c-get-started-custom.md)
### OIDC/OAUTH 공급자 구성
#### [Azure AD](active-directory-b2c-setup-aad-custom.md)
#### [Microsoft 계정](active-directory-b2c-custom-setup-msa-idp.md)
#### [Google+](active-directory-b2c-custom-setup-goog-idp.md)
#### [LinkedIn](active-directory-b2c-custom-setup-li-idp.md)
#### [Twitter](active-directory-b2c-custom-setup-twitter-idp.md)
### SAML 공급자 구성
#### [ADFS](active-directory-b2c-custom-setup-adfs2016-idp.md)
#### [Salesforce](active-directory-b2c-setup-sf-app-custom.md)
### RESTful API 통합
#### [사용자 입력 유효성 검사](active-directory-b2c-rest-api-validation-custom.md)
#### [추가 클레임 가져오기](active-directory-b2c-rest-api-step-custom.md)
#### [사용자 고유의 RESTful API 추가](active-directory-b2c-custom-rest-api-netfw.md)
#### [기본 인증을 사용하여 RESTful API 보안](active-directory-b2c-custom-rest-api-netfw-secure-basic.md)
#### [인증서 인증을 사용하여 RESTful API 보안](active-directory-b2c-custom-rest-api-netfw-secure-cert.md)
### 사용자 흐름
#### [사용자 입력 구성](active-directory-b2c-configure-signup-self-asserted-custom.md)
#### [사용자 지정 특성](active-directory-b2c-create-custom-attributes-profile-edit-custom.md)
#### [UI 사용자 지정](active-directory-b2c-ui-customization-custom.md)
#### [동적으로 UI 사용자 지정](active-directory-b2c-ui-customization-custom-dynamic.md)
#### [토큰 사용자 지정](active-directory-b2c-reference-manage-sso-and-token-configuration.md)
#### [암호 복잡성](active-directory-b2c-reference-password-complexity-custom.md)
#### [암호 변경](active-directory-b2c-reference-password-change-custom.md)
#### [로그인 유지 사용](active-directory-b2c-reference-kmsi-custom.md)
### 문제 해결
#### [Application Insights를 사용하여 로그 수집](active-directory-b2c-troubleshoot-custom.md)
## 참조
### [릴리스 정보](active-directory-b2c-developer-notes-custom.md)
### [SSO 세션 공급자](active-directory-b2c-reference-sso-custom.md)
### [보안 프레임워크 정의](active-directory-b2c-reference-trustframeworks-defined-ief-custom.md) 

# 참조
## [코드 샘플](https://azure.microsoft.com/en-us/resources/samples/?service=active-directory-b2c)
## 용어
### [응용 프로그램 유형](active-directory-b2c-apps.md)
### [인증 프로토콜](active-directory-b2c-reference-protocols.md)
### [토큰](active-directory-b2c-reference-tokens.md)
## [지역 가용성 및 데이터 상주](active-directory-b2c-reference-tenant-type.md)
## [청구 사용](active-directory-b2c-how-to-enable-billing.md)
## [위협 관리](active-directory-b2c-reference-threat-management.md)
## [디렉터리를 만들 때의 문제](active-directory-b2c-support-create-directory.md)
## [앱 확장](active-directory-b2c-reference-extensions-app.md)

# 관련 항목
## [Azure Active Directory](../active-directory/active-directory-whatis.md)
## [Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication.md)

# 리소스
## [Azure AD B2C 피드백 포럼](https://feedback.azure.com/forums/169401-azure-active-directory/category/160596-b2c)
## [Azure 로드맵](https://azure.microsoft.com/roadmap/?category=security-identity)
## [질문과 대답](active-directory-b2c-faqs.md)
## [가격](https://azure.microsoft.com/pricing/details/active-directory-b2c/)
## [요금 계산기](https://azure.microsoft.com/pricing/calculator/)
## [서비스 업데이트](https://azure.microsoft.com/updates/?product=active-directory-b2c)
## [스택 오버플로](https://stackoverflow.com/questions/tagged/azure-ad-b2c)
## [지원](active-directory-b2c-support.md)
## [비디오](https://azure.microsoft.com/documentation/videos/index/?services=active-directory-b2c)
