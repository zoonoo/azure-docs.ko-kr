
# <a name="call-the-microsoft-graph-api-from-a-javascript-single-page-application-spa"></a>JavaScript SPA(단일 페이지 응용 프로그램)에서 Microsoft Graph API 호출

이 가이드에서는 JavaScript SPA(단일 페이지 응용 프로그램)가 개인 및 회사 계정으로 로그인하고, 액세스 토큰을 얻고, Microsoft Graph API 또는 Azure Active Directory v2 끝점에서 액세스 토큰이 필요한 기타 API를 호출할 수 있는 방법에 대해 설명합니다.

### <a name="how-this-guide-works"></a>이 가이드의 작동 방식

![이 가이드에서 생성된 샘플 앱의 작동 원리](media/active-directory-singlepageapp-javascriptspa-introduction/javascriptspa-intro.png)

<!--start-collapse-->
### <a name="more-information"></a>추가 정보

이 가이드에서 만든 샘플 응용 프로그램은 JavaScript SPA가 Azure Active Directory v2 끝점에서 토큰을 수락하는 Microsoft Graph API 또는 Web API를 쿼리하도록 합니다. 이 시나리오에서는 사용자가 로그인한 후에 토큰이 권한 부여 헤더를 통해 HTTP 요청에 추가됩니다. 토큰 획득 및 갱신은 MSAL(Microsoft 인증 라이브러리)에서 처리합니다.
<!--end-collapse-->

<!--start-collapse-->
### <a name="libraries"></a>라이브러리

이 가이드에서는 다음 라이브러리를 사용합니다.

|라이브러리|설명|
|---|---|
|[msal.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)|JavaScript용 Microsoft 인증 라이브러리 미리 보기|
<!--end-collapse-->
