
## <a name="update-app"></a>사용자 지정 API를 호출하도록 앱 업데이트

1. 기존 단추 옆에 "Complete All" 레이블의 단추를 추가하고 두 개의 단추를 한 줄 아래로 이동합니다. Eclipse에서 퀵 스타트 프로젝트의  *res\layout\activity_to_do.xml* 파일을 열고 이름이  `buttonAddToDo`인 **Button** 요소가 포함된 **LinearLayout** 요소를 찾습니다. **LinearLayout**을 복사하여 원래 요소 바로 뒤에 붙여넣습니다. 첫 번째 **LinearLayout**에서 **Button** 요소를 삭제합니다.

2. 두 번째 **LinearLayout**에서 **EditText** 요소를 삭제하고 다음 코드를 기존 **Button** 요소 바로 뒤에 추가합니다. 

        <Button
            android:id="@+id/buttonCompleteItem"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:onClick="completeItem"
            android:text="@string/complete_button_text" />

	이 코드는 페이지의 별도 줄에 있는 기존 단추 옆에 새 단추를 추가합니다.

3. 이제 두 번째 **LinearLayout**은 다음과 같습니다.

	     <LinearLayout
	        android:layout_width="match_parent" 
	        android:layout_height="wrap_content" 
	        android:background="#71BCFA"
	        android:padding="6dip"  >
	        <Button
	            android:id="@+id/buttonAddToDo"
	            android:layout_width="wrap_content"
	            android:layout_height="wrap_content"
	            android:onClick="addItem"
	            android:text="@string/add_button_text" />
	        <Button
	            android:id="@+id/buttonCompleteItem"
	            android:layout_width="wrap_content"
	            android:layout_height="wrap_content"
	            android:onClick="completeItem"
	            android:text="@string/complete_button_text" />
	    </LinearLayout>
	

4. res\values\string.xml 파일을 열고 다음 코드 줄을 추가합니다.

    	<string name="complete_button_text">Complete All</string>



5. 패키지 탐색기에서,  *src* 폴더(`com.example.{your projects name}`)에서 프로젝트 이름을 마우스 오른쪽 단추로 클릭하고 **새로 만들기**를 선택한 다음 **클래스**를 선택합니다. 대화 상자의 클래스 이름 필드에 **MarkAllResult**를 입력하고 OK를 선택한 후 결과 클래스 정의를 다음 코드로 바꿉니다.

		import com.google.gson.annotations.SerializedName;
		
		public class MarkAllResult {
		    @SerializedName("count")
		    public int mCount;
		    
		    public int getCount() {
		        return mCount;
			}
		
			public void setCount(int count) {
			        this.mCount = count;
			}
		}

	이 클래스는 사용자 지정 API에서 반환한 행 개수 값을 유지하는 데 사용됩니다. 

6. **refreshItemsFromTable** 메서드를 **ToDoActivity.java** 파일에서 찾아  `try` 블록에 있는 코드의 첫 줄이 다음과 유사한 지 확인합니다.

        final MobileServiceList<ToDoItem> result = mToDoTable.where().field("complete").eq(false).execute().get();

	이 코드는 완료된 항목이 쿼리에서 반환되지 않도록 항목을 필터링합니다.

7. **ToDoActivity.java**가 파일의 시작 부분에 다음 가져오기가 포함되어 있는지 확인합니다.

		import com.google.common.util.concurrent.FutureCallback;
		import com.google.common.util.concurrent.Futures;
		import com.google.common.util.concurrent.ListenableFuture;

8. **ToDoActivity.java** 파일에서 다음 메서드를 추가합니다.

	public void completeItem(View view) {
	    
	    ListenableFuture<MarkAllResult> result = mClient.invokeApi( "completeAll2", MarkAllResult.class ); 
	    	
	    	Futures.addCallback(result, new FutureCallback<MarkAllResult>() {
	    		@Override
	    		public void onFailure(Throwable exc) {
	    			createAndShowDialog((Exception) exc, "Error");
	    		}
	    		
	    		@Override
	    		public void onSuccess(MarkAllResult result) {
	    			createAndShowDialog(result.getCount() + " item(s) marked as complete.", "Completed Items");
	                refreshItemsFromTable();	
	    		}
	    	});
	    }
	
	이 메서드는 새 단추의 **Click** 이벤트를 처리합니다. **invokeApi** 메서드가 클라이언트에서 호출되어 POST 요청을 새 사용자 지정 API로 보냅니다. 사용자 지정 API에서 반환하는 결과는 오류와 마찬가지로 메시지 대화 상자에 표시됩니다.

## 앱 테스트

1. **실행** 메뉴에서 **실행**을 클릭하여 Android 에뮬레이터에서 프로젝트를 시작합니다.

	그러면 Android SDK를 사용하여 빌드된 앱이 실행되며 클라이언트 라이브러리를 사용하여 모바일 서비스의 항목을 반환합니다.


2. 앱에서 **Insert a TodoItem**에 일부 텍스트를 입력한 후 **Add**를 클릭합니다.

3. 목록에 todo 항목을 여러 개 추가할 때까지 이전 단계를 반복합니다.

4. **모두 완료** 단추를 클릭합니다.

  	![](./media/mobile-services-android-call-custom-api/mobile-custom-api-android-completed.png)

	완료 표시된 항목 수를 나타내는 메시지 대화 상자가 표시되고 필터링된 쿼리가 다시 실행되어 목록에서 모든 항목을 지웁니다.


<!--HONumber=52-->