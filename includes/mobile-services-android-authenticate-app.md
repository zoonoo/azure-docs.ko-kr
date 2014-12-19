
1. Eclipse의 Package Explorer에서 ToDoActivity.java 파일을 열고 다음 import 문을 추가합니다.

		import java.util.concurrent.ExecutionException;
		import java.util.concurrent.atomic.AtomicBoolean;

		import android.content.Context;
		import android.content.SharedPreferences;
		import android.content.SharedPreferences.Editor;

		import com.microsoft.windowsazure.mobileservices.authentication.MobileServiceAuthenticationProvider;
		import com.microsoft.windowsazure.mobileservices.authentication.MobileServiceUser;

2. **ToDoActivity** 클래스에 다음 메서드를 추가합니다. 
	
	private void authenticate() {
	    // Login using the Google provider.
	    
		ListenableFuture<MobileServiceUser> mLogin = mClient.login(MobileServiceAuthenticationProvider.Google);

    	Futures.addCallback(mLogin, new FutureCallback<MobileServiceUser>() {
    		@Override
    		public void onFailure(Throwable exc) {
    			createAndShowDialog((Exception) exc, "Error");
    		}   		
    		@Override
    		public void onSuccess(MobileServiceUser user) {
    			createAndShowDialog(String.format(
                        "You are now logged in - %1$2s",
                        user.getUserId()), "Success");
    			createTable();	
    		}
    	});   	
	}


	인증 프로세스를 처리하는 새 메서드가 만들어집니다. 사용자는 Google 로그인을 사용하여 인증됩니다. 인증된 사용자의 ID를 표시하는 대화 상자가 나타납니다. 양성 인증 없이는 진행할 수 없습니다.

    <div class="dev-callout"><b>참고</b>
	<p>Google 이외의 ID 공급자를 사용하는 경우 위의 <strong>login</strong> 메서드에 전달된 값을 다음 중 하나로 변경합니다. <em>MicrosoftAccount</em>, <em>Facebook</em>, <em>Twitter</em> 또는 <em>windowsazureactivedirectory</em></p>
    </div>

3. **onCreate** 메서드에서 `MobileServiceClient` 개체를 인스턴스화하는 코드 다음에 다음 코드 줄을 추가합니다.

		authenticate();

	이 호출을 인증 프로세스를 시작합니다.

4. **onCreate** 메서드에서 `authenticate();` 뒤의 나머지 코드를 새 **createTable** 메서드로 이동합니다. 다음과 같이 표시됩니다.

		private void createTable() {
	
			// Get the Mobile Service Table instance to use
			mToDoTable = mClient.getTable(ToDoItem.class);
	
			mTextNewToDo = (EditText) findViewById(R.id.textNewToDo);
	
			// Create an adapter to bind the items with the view
			mAdapter = new ToDoItemAdapter(this, R.layout.row_list_to_do);
			ListView listViewToDo = (ListView) findViewById(R.id.listViewToDo);
			listViewToDo.setAdapter(mAdapter);
	
			// Load the items from the Mobile Service
			refreshItemsFromTable();
		}

9. **실행** 메뉴에서 **실행**을 클릭하여 앱을 시작하고 원하는 ID 공급자에 로그인합니다. 

   	로그인하고 나면 앱이 오류 없이 실행되며 모바일 서비스를 쿼리하고 데이터를 업데이트할 수 있게 됩니다.
