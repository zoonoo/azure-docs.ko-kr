> [!IMPORTANT]
> Mobile Engagement에서 푸시 알림을 받으려면 응용 프로그램에서 `Silent Remote Notifications`을 사용하도록 설정해야 합니다. Info.plist 파일의 UIBackgroundModes 배열에 원격 알림 값을 추가해야 합니다.
> 
> 

1. 프로젝트에서 `info.plist` 파일 열기
2. 목록(`Information Property List`)에서 맨 위 항목을 마우스 오른쪽 단추로 클릭하고 새 행을 추가합니다.
   
    ![](./media/mobile-engagement-ios-silent-push/xcode-plist-add-silent-push1.png)
3. 새 행에 `Required background modes`
   
    ![](./media/mobile-engagement-ios-silent-push/xcode-plist-add-silent-push2.png)
4. 왼쪽 화살표를 클릭하여 행을 확장합니다.
5. 항목 0에 다음 값을 추가합니다. `App downloads content in response to push notifications`
   
    ![](./media/mobile-engagement-ios-silent-push/xcode-plist-add-silent-push3.png)
6. 변경하고 나면 info.plist XML에 다음 키와 값이 포함되어야 합니다.
   
        <key>UIBackgroundModes</key>
        <array>
        <string>remote-notification</string>
        </array>
7. **Xcode 7+** 및**iOS 9+** 를 사용 중인 경우,
   
   * 대상 > 대상 이름 > 기능에서 **푸시 알림**을 사용하도록 설정합니다.

