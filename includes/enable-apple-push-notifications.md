
## <a id="register"></a>푸시 알림을 위한 앱 등록

* [사용자 앱에 대한 앱 ID 등록](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/MaintainingProfiles/MaintainingProfiles.html#//apple_ref/doc/uid/TP40012582-CH30-SW991). 앱을 등록 하는 경우 **앱 서비스**의 선택적 **푸시 알림** 확인란을 선택합니다.

> [AZURE.NOTE]명시적 앱 ID(와일드 카드 앱 ID가 아닌)을 만들고 **번들 ID**로 Xcode 빠른 시작 프로젝트에 있는 정확한 **번들 ID**를 사용합니다. 앱 ID를 등록하는 경우 **앱 서비스**에서 **푸시 알림** 옵션을 확인하는 것도 중요합니다. 이 확인란을 선택하지 않은 경우 푸시 알림은 작동하지 않습니다.

* 다음으로, [앱에 푸시 알림을 사용](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/ConfiguringPushNotifications/ConfiguringPushNotifications.html#//apple_ref/doc/uid/TP40012582-CH32-SW6)합니다. "개발" 또는 "배포" SSL 인증서를 만들 수 있습니다(나중에 모바일 서비스 포털에서 해당 옵션, "샌드박스" 또는 "프로덕션"을 선택합니다.).

* 다음으로, [앱 ID가 푸시 알림을 사용하는지 확인](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/ConfiguringPushNotifications/ConfiguringPushNotifications.html#//apple_ref/doc/uid/TP40012582-CH32-SW8)합니다.

* 마지막으로, [Xcode 빠른 시작 프로젝트에서 프로비전 프로필을 새로 고친](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/ConfiguringPushNotifications/ConfiguringPushNotifications.html#//apple_ref/doc/uid/TP40012582-CH32-SW10) 다음 [프로비전 프로필을 만들거나 다시 생성하여 푸시 알림을 사용하는지 확인](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/ConfiguringPushNotifications/ConfiguringPushNotifications.html#//apple_ref/doc/uid/TP40012582-CH32-SW12)합니다.

<!---HONumber=July15_HO2-->