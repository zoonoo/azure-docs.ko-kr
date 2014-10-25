이제 등록이 시도되기 전에 사용자가 인증되도록 푸시 알림이 등록되는 방법을 변경해야 합니다.

1.  **QSAppDelegate.m**에서 **didFinishLaunchingWithOptions**의 구현을 모두 제거합니다.

        - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:
        (NSDictionary *)launchOptions
        {
            // Register for remote notifications
            [[UIApplication sharedApplication] registerForRemoteNotificationTypes:
            UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];
            return YES;
        }

2.  프로젝트 파일 **QSTodoListViewController.m**을 열고 **viewDidLoad** 메서드에 위에서 제거한 코드를 추가하여 다음을 추가합니다.

        - (void)viewDidAppear:(BOOL)animated
        {
            MSClient *client = self.todoService.client;

            if (client.currentUser != nil) {
                return;
            }

            [client loginWithProvider:@"facebook" controller:self animated:YES completion:^(MSUser *user, NSError *error) {
                [self refresh];
            }];

            // Register for remote notifications
            [[UIApplication sharedApplication] registerForRemoteNotificationTypes:
            UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];
        }


