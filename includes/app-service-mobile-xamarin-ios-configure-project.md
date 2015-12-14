####Xamarin Studio에서 iOS 프로젝트를 구성합니다.

1. Xamarin.Studio에서 **Info.plist**를 열고 앞에서 앱 ID로 만든 Bundle ID를 사용하여 **Bundle 식별자**를 업데이트합니다.

    ![][88]

2. 아래의 **Background Modes**로 스크롤하여 **Enable Background Modes** 상자와 **Remote notifications** 상자를 선택합니다.

    ![][122]

3. Solution Panel에서 프로젝트를 두 번 클릭하여 **Project Options**를 엽니다.

4.  **Build**에서 **iOS Bundle Signing**을 선택하고 방금 이 프로젝트에 대해 설정한 해당 **ID** 및 **프로비저닝 프로필**을 선택합니다.

    ![][120]

    이제 프로젝트에서 코드 서명에 새 프로필을 사용하게 됩니다. 공식 Xamarin 장치 프로비저닝 설명서를 보려면 [Xamarin 장치 프로비저닝]을 참조하세요.

####Visual Studio에서 iOS 프로젝트를 구성합니다.

1. Visual Studio에서 프로젝트를 마우스 오른쪽 단추로 클릭한 다음 **속성**을 클릭합니다.

2. 속성 페이지에서 **iOS 응용 프로그램** 탭을 클릭하고 앞에서 만든 ID를 사용하여 **식별자**를 업데이트합니다.

    ![][123]

3. **iOS Bundle Signing** 탭에서 방금 이 프로젝트에 대해 설정한 해당 **ID** 및 **프로비저닝 프로필**을 선택합니다.

    ![][124]

    이제 프로젝트에서 코드 서명에 새 프로필을 사용하게 됩니다. 공식 Xamarin 장치 프로비저닝 설명서를 보려면 [Xamarin 장치 프로비저닝]을 참조하세요.

[120]:./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-20.png
[88]:./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-21.png
[122]:./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-22.png
[123]:./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-23.png
[124]:./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-24.png

[Xamarin 장치 프로비저닝]: http://developer.xamarin.com/guides/ios/getting_started/installation/device_provisioning/

<!---HONumber=AcomDC_1203_2015-->