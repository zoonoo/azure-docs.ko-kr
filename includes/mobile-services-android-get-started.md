이 자습서의 최종 단계는 새 앱을 빌드하고 실행하는 것입니다.

### Android Studio에 프로젝트 로드 및 Gradle 동기화

1. 압축된 프로젝트 파일을 저장한 위치로 이동한 후 컴퓨터에서 Android Studio 프로젝트 디렉터리에 파일 압축을 풉니다.

2. Android Studio를 엽니다. 프로젝트로 작업 중인 경우 해당 프로젝트가 나타나면 프로젝트를 닫습니다(File => Close Project).

3. **기존 Android Studio 프로젝트 열기**를 선택하고 프로젝트 위치를 찾은 후 **확인**을 클릭합니다. 프로젝트를 로드하고 Gradle와 동기화를 시작합니다.

 	![](./media/mobile-services-android-get-started/android-studio-import-project.png)

4. Gradle 동기화 활동이 완료될 때까지 기다립니다. "찾을 대상 실패" 오류가 나타나면 Android Studio에서 사용되는 버전이 샘플과 일치하지 않기 때문입니다. 이 문제를 해결하는 가장 쉬운 방법은 오류 메시지에서 **누락된 플랫폼 설치 및 프로젝트 동기화** 링크를 클릭하는 것입니다. 추가 버전 오류 메시지를 받으면 오류가 나타나지 않을 때까지 이 과정을 반복합니다.
    - "최신 및 가장 큰" 버전의 Android를 사용하여 실행하려는 경우 이 문제를 해결할 다른 방법이 있습니다. *앱* 디렉터리의 *build.gradle* 파일에서 **targetSdkVersion**를 업데이트하여 컴퓨터에 이미 설치된 버전과 일치시킬 수 있으며 이는 **SDK Manager** 아이콘을 클릭하고 어떤 버전이 나열되는지 확인하여 찾을 수 있습니다. 다음으로 **Gradle 파일과 프로젝트 동기화** 아이콘을 누릅니다. 빌드 도구의 버전에 대한 오류 메시지가 발생할 수 있고 동일한 방식으로 해결합니다.

### 앱 실행

에뮬레이터를 사용하거나 실제 장치를 사용하여 앱을 실행할 수 있습니다.

1. 장치를 실행하려면 USB 케이블로 컴퓨터에 연결합니다. [개발에 대한 장치를 설정](https://developer.android.com/training/basics/firstapp/running-app.html)해야 합니다. Windows 컴퓨터에서 개발하는 경우 USB 드라이버를 다운로드하고 설치해야 합니다.

2. Android 에뮬레이터를 사용하여 실행하려면 AVD(Android Virtual Device)를 하나 이상 정의해야 합니다. AVD Manager 아이콘을 클릭하여 이러한 장치를 만들고 관리합니다.

3. **실행** 메뉴에서 **실행**을 클릭하여 프로젝트를 시작하고 표시되는 대화 상자에서 장치 또는 에뮬레이터를 선택합니다.

4. 앱이 나타나면 _자습서 완료_ 등의 의미 있는 텍스트를 입력하고 **추가**를 클릭합니다.

   	![](./media/mobile-services-android-get-started/mobile-quickstart-startup-android.png)

   	Azure에 호스트된 새 모바일 서비스에 POST 요청이 전송됩니다. 요청에서 데이터가 TodoItem 테이블에 삽입됩니다. TodoItem 테이블에 저장된 항목이 모바일 서비스에서 반환된 후 데이터가 목록에 표시됩니다.

	> [AZURE.NOTE]모바일 서비스에 액세스하여 데이터를 쿼리 및 삽입하는 코드를 검토할 수 있습니다. 이 코드는 ToDoActivity.java 파일에 있습니다.

8. 관리 포털로 돌아가서 **데이터** 탭을 클릭한 후 **TodoItems** 테이블을 클릭합니다.

   	![](./media/mobile-services-android-get-started/mobile-data-tab1.png)

   	이를 통해 앱에서 테이블로 삽입된 데이터를 찾을 수 있습니다.

   	![](./media/mobile-services-android-get-started/mobile-data-browse.png)

<!---HONumber=Nov15_HO3-->