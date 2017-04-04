
1. Android Studio의 **프로젝트 탐색기** 에서 ToDoActivity.java 파일을 열고 다음 import 문을 추가합니다.

        import java.util.concurrent.ExecutionException;
        import java.util.concurrent.atomic.AtomicBoolean;

        import android.content.Context;
        import android.content.SharedPreferences;
        import android.content.SharedPreferences.Editor;

        import com.microsoft.windowsazure.mobileservices.authentication.MobileServiceAuthenticationProvider;
        import com.microsoft.windowsazure.mobileservices.authentication.MobileServiceUser;
2. **ToDoActivity** 클래스에 다음 메서드를 추가합니다.

        // You can choose any unique number here to differentiate auth providers from each other. Note this is the same code at login() and onActivityResult().
        public static final int GOOGLE_LOGIN_REQUEST_CODE = 1;
 
        private void authenticate() {
            // Login using the Google provider.
            mClient.login("Google", "{url_scheme_of_your_app}", GOOGLE_LOGIN_REQUEST_CODE);
        }
         
        @Override
        protected void onActivityResult(int requestCode, int resultCode, Intent data) {
            // When request completes
            if (resultCode == RESULT_OK) {
                // Check the request code matches the one we send in the login request
                if (requestCode == GOOGLE_LOGIN_REQUEST_CODE) {
                    MobileServiceActivityResult result = mClient.onActivityResult(data);
                    if (result.isLoggedIn()) {
                        // login succeeded
                        createAndShowDialog(String.format("You are now logged in - %1$2s", mClient.getCurrentUser().getUserId()), "Success");
                        createTable();
                    } else {
                        // login failed, check the error message
                        String errorMessage = result.getErrorMessage();
                        createAndShowDialog(errorMessage, "Error");
                    }
                }
            }
        }

    인증 프로세스를 처리하는 새 메서드가 만들어집니다. 사용자는 Google 로그인을 사용하여 인증됩니다. 대화 상자는 인증된 사용자의 ID를 표시합니다. 양성 인증 없이는 진행할 수 없습니다.

    > [!NOTE]
    > Google 이외의 ID 공급자를 사용하는 경우, 위의 **login** 메서드에 전달된 값을 다음 중 하나로 변경합니다. _MicrosoftAccount_, _Facebook_, _Twitter_, 또는 _windowsazureactivedirectory_.

3. **OnCreate** 메서드에서 `MobileServiceClient` 개체를 인스턴스화하는 코드 다음에 다음 코드 줄을 추가합니다.

        authenticate();

    이 호출을 인증 프로세스를 시작합니다.
4. **OnCreate** 메서드에서 `authenticate();` 뒤에 남은 코드를 새 **CreateTable** 메서드로 이동합니다. 이는 다음과 같습니다.

        private void createTable() {

            // Get the table instance to use.
            mToDoTable = mClient.getTable(ToDoItem.class);

            mTextNewToDo = (EditText) findViewById(R.id.textNewToDo);

            // Create an adapter to bind the items with the view.
            mAdapter = new ToDoItemAdapter(this, R.layout.row_list_to_do);
            ListView listViewToDo = (ListView) findViewById(R.id.listViewToDo);
            listViewToDo.setAdapter(mAdapter);

            // Load the items from Azure.
            refreshItemsFromTable();
        }

5. _RedirectUrlActivity_의 다음 코드 조각을 _AndroidManifest.xml_에 추가하여 리디렉션이 작동되도록 합니다.
 
        <activity android:name="com.microsoft.windowsazure.mobileservices.authentication.RedirectUrlActivity">
            <intent-filter>
                <action android:name="android.intent.action.VIEW" />
                <category android:name="android.intent.category.DEFAULT" />
                <category android:name="android.intent.category.BROWSABLE" />
                <data android:scheme="{url_scheme_of_your_app}"
                    android:host="easyauth.callback"/>
            </intent-filter>
        </activity>

6.  redirectUriScheme을 Android 응용 프로그램의 _build.gradle_에 추가합니다.
 
        android {
            buildTypes {
                release {
                    // … …
                    manifestPlaceholders = ['redirectUriScheme': '{url_scheme_of_your_app}://easyauth.callback']
                }
                debug {
                    // … …
                    manifestPlaceholders = ['redirectUriScheme': '{url_scheme_of_your_app}://easyauth.callback']
                }
            }
        }

7. build.gradle에서 종속성에 com.android.support:customtabs:23.0.1을 추가합니다.

      dependencies {        // ...        compile 'com.android.support:customtabs:23.0.1'    }

8. **실행** 메뉴에서 **앱 실행**을 클릭하여 앱을 시작하고 원하는 ID 공급자로 로그인합니다.

로그인하고 나면 앱이 오류 없이 실행되며 백 엔드 서비스를 쿼리하고 데이터를 업데이트할 수 있게 됩니다.
