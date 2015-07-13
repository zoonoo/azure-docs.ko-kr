
이제 등록이 시도되기 전에 사용자가 인증되도록 푸시 알림이 등록되는 방법을 변경해야 합니다.

1. **QSAppDelegate.m**에서 **didFinishLaunchingWithOptions** 구현을 모두 제거합니다.

2. **QSTodoListViewController.m**을 열고 다음 코드를 **viewDidLoad** 메서드 끝에 추가합니다.

```
// Register for remote notifications
[[UIApplication sharedApplication] registerForRemoteNotificationTypes:
UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];
```

<!---HONumber=62-->