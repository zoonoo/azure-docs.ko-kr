
1. 솔루션 보기에서 Xamarin.Android 앱의 **Components** 폴더를 확장하여 Azure 모바일 서비스 패키지가 설치되었는지 확인합니다. 

2. **Components** 폴더를 마우스 오른쪽 단추로 클릭하고 **Get More Components...**를 클릭하고 **Google Cloud Messaging Client** 구성 요소를 검색하여 이를 프로젝트에 추가합니다.

1. ToDoActivity.cs 프로젝트 파일을 열고 클래스에 다음 using 문을 추가합니다.

		using Gcm.Client;

2. **TodoActivity** 클래스에 다음 새 코드를 추가합니다.

        // Create a new instance field for this activity.
        static ToDoActivity instance = new ToDoActivity();

        // Return the current activity instance.
        public static ToDoActivity CurrentActivity
        {
            get
            {
                return instance;
            }
        }
        // Return the Mobile Services client.
        public MobileServiceClient CurrentClient
        {
            get
            {
                return client;
            }
        }

	그러면 서비스 프로세스에서 모바일 서비스 클라이언트 인스턴스에 액세스할 수 있습니다.

3. 다음과 같이 기존 모바일 서비스 클라이언트 선언을 public으로 변경합니다.

		public MobileServiceClient client { get; private set; }

4.	**MobileServiceClient**가 만들어지고 나면 다음 코드를 **OnCreate** 메서드에 추가합니다.

        // Set the current instance of TodoActivity.
        instance = this;

        // Make sure the GCM client is set up correctly.
        GcmClient.CheckDevice(this);
        GcmClient.CheckManifest(this);

        // Register the app for push notifications.
        GcmClient.Register(this, ToDoBroadcastReceiver.senderIDs);

이제 **ToDoActivity**이 푸시 알림 추가를 위해 준비됩니다.

<!---HONumber=July15_HO4-->