4. API 앱의 게이트웨이 블레이드로 이동합니다.

	![게이트웨이 클릭](./media/app-service-api-gateway-config-auth/gateway.png)

7. **게이트웨이** 블레이드에서 **설정**을 클릭한 후 **ID**를 클릭합니다.

	![설정 클릭](./media/app-service-api-gateway-config-auth/clicksettingsingateway.png)

	![ID 클릭](./media/app-service-api-gateway-config-auth/clickidentity.png)

	**ID** 블레이드에서 Azure Active Directory 및 기타 여러 공급자를 사용하여 인증을 구성하기 위한 다른 블레이드로 이동할 수 있습니다.

	![ID 블레이드](./media/app-service-api-gateway-config-auth/identityblade.png)
  
3. 사용할 ID 공급자를 선택하고 해당 문서의 단계에 따라 해당 공급자를 사용하여 API 앱을 구성합니다. 이러한 문서는 모바일 앱용으로 작성되었지만 절차는 API 앱에 대해서도 동일합니다. 일부 절차를 수행하려면 [Azure 포털]을 사용해야 합니다.

 - [Microsoft 계정](../articles/app-service-mobile/app-service-mobile-how-to-configure-microsoft-authentication-preview.md)
 - [Facebook 로그인](../articles/app-service-mobile/app-service-mobile-how-to-configure-facebook-authentication-preview.md)
 - [Twitter 로그인](../articles/app-service-mobile/app-service-mobile-how-to-configure-twitter-authentication-preview.md)
 - [Google 로그인](../articles/app-service-mobile/app-service-mobile-how-to-configure-google-authentication-preview.md)
 - [Azure Active Directory](../articles/app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication-preview.md)

예를 들어 다음 스크린샷은 Azure Active Directory 인증을 설정한 후 [Azure 포털] 페이지 및 [Azure Preview 포털] 블레이드에 표시되어야 하는 내용을 보여 줍니다.

Azure Preview 포털에서 **Azure Active Directory** 블레이드에는 Azure 포털의 Azure Active Directory 탭에서 만든 응용 프로그램의 **클라이언트 ID**가 있고, **허용된 테넌트**에는 Azure Active Directory 테넌트(예: "contoso.onmicrosoft.com")가 있습니다.

![Azure Active Directory 블레이드](./media/app-service-api-gateway-config-auth/tdinaadblade.png)

Azure 포털에서 **Azure Active Directory** 탭에서 만든 응용 프로그램의 **구성** 탭에는 Azure Preview 포털의 **Azure Active Directory** 블레이드에서 가져온 **로그온 URL**, **앱 ID URI** 및 **회신 URL**이 있습니다.

![](./media/app-service-api-gateway-config-auth/oldportal1.png)

![](./media/app-service-api-gateway-config-auth/oldportal2.png)

![](./media/app-service-api-gateway-config-auth/oldportal3.png)

![](./media/app-service-api-gateway-config-auth/oldportal4.png)

이미지의 회신 URL은 동일한 URL을 두 번 보여 주는데 하나는 `http:`를 사용한 것이고 다른 하나는 `https:`를 사용한 것입니다.

<!---HONumber=Oct15_HO3-->