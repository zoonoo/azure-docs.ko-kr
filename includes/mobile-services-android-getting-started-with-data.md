이제 모바일 서비스를 사용할 수 있으므로 로컬 컬렉션 대신 모바일 서비스에 항목을 저장하도록 앱을 업데이트할 수 있습니다.

1.  [모바일 서비스 Android SDK][]가 아직 없는 경우 지금 다운로드하고 압축 파일의 압축을 풉니다.

2.  `.jar` 파일(SDK의 `mobileservices` 폴더)을 GetStartedWithData 프로젝트의 `libs` 폴더로 복사합니다.

3.  Eclipse의 Package Explorer에서 `libs` 폴더를 마우스 오른쪽 단추로 클릭하고 **새로 고침**을 클릭하면 복사한 jar 파일이 나타납니다.

    모바일 서비스 SDK 참조가 작업 영역에 추가됩니다.

4.  AndroidManifest.xml 파일을 열어 Azure에서 모바일 서비스에 액세스할 수 있게 하는 다음 줄을 추가합니다.

        <uses-permission android:name="android.permission.INTERNET" />

5.  Package Explorer에서 com.example.getstartedwithdata 패키지에 있는 TodoActivity.java 파일을 열고 다음 코드 줄의 주석 처리를 제거합니다.

        import com.microsoft.windowsazure.mobileservices.MobileServiceClient;
        import com.microsoft.windowsazure.mobileservices.MobileServiceTable;
        import com.microsoft.windowsazure.mobileservices.NextServiceFilterCallback;
        import com.microsoft.windowsazure.mobileservices.ServiceFilter;
        import com.microsoft.windowsazure.mobileservices.ServiceFilterRequest;
        import com.microsoft.windowsazure.mobileservices.ServiceFilterResponse;
        import com.microsoft.windowsazure.mobileservices.ServiceFilterResponseCallback;
        import com.microsoft.windowsazure.mobileservices.TableOperationCallback;
        import com.microsoft.windowsazure.mobileservices.TableQueryCallback;

        import java.net.MalformedURLException;

6.  모바일 서비스와 바꿀 수 있도록 앱에서 현재 사용되고 있는 메모리 내 목록을 제거합니다. **ToDoActivity** 클래스에서 기존 **toDoItemList** 목록을 정의하는 다음 코드 줄을 주석으로 처리합니다.

        public List<ToDoItem> toDoItemList = new ArrayList<ToDoItem>();

7.  파일을 저장하면 프로젝트에서 빌드 오류를 나타냅니다. `toDoItemList` 변수가 사용되는 나머지 세 개 위치를 검색하여 표시된 섹션을 주석으로 처리합니다. 또한 `import java.util.ArrayList`도 제거합니다. 그러면 메모리 내 목록이 완전히 제거됩니다.

8.  이제 모바일 서비스를 추가합니다. 다음 코드 줄의 주석 처리를 제거합니다.

        private MobileServiceClient mClient;
        private private MobileServiceTable<ToDoItem> mToDoTable;

9.  파일 아래쪽에서 ProgressFilter 클래스를 찾아 주석 처리를 제거합니다. 이 클래스는 MobileServiceClient에서 네트워크 작업을 실행하는 동안 '로드' 표시기를 표시합니다.

10. 관리 포털에서 **모바일 서비스**를 클릭한 후 방금 만든 모바일 서비스를 클릭합니다.

11. **대시보드** 탭을 클릭하여 **사이트 URL**을 기록해 두고 **키 관리**를 클릭하여 **응용 프로그램 키**를 기록해 둡니다.

    ![][]

    앱 코드에서 모바일 서비스에 액세스할 때 이 값이 필요합니다.

12. **onCreate** 메서드에서 **MobileServiceClient** 변수를 정의하는 다음 코드 줄의 주석 처리를 제거합니다.

        try {
        // Create the Mobile Service Client instance, using the provided
        // Mobile Service URL and key
            mClient = new MobileServiceClient(
                    "MobileServiceUrl",
                    "AppKey", 
                    this).withFilter(new ProgressFilter());

            // Get the Mobile Service Table instance to use
            mToDoTable = mClient.getTable(ToDoItem.class);
        } catch (MalformedURLException e) {
            createAndShowDialog(new Exception("There was an error creating the Mobile Service. Verify the URL"), "Error");
        }

    모바일 서비스에 액세스하는 데 사용되는 새 MobileServiceClient 인스턴스가 만들어집니다. 모바일 서비스에서 데이터 저장소에 프록시를 설정하는 데 사용되는 MobileServiceTable 인스턴스도 만들어집니다.

13. 위의 코드에서 모바일 서비스의 URL 및 응용 프로그램 키로 `MobileServiceUrl` 및 `AppKey`를 바꿉니다.

14. **checkItem** 메서드에서 다음 줄의 주석 처리를 제거합니다.

        mToDoTable.update(item, new TableOperationCallback<ToDoItem>() {    
            public void onCompleted(ToDoItem entity, Exception exception,
                    ServiceFilterResponse response) {
                if(exception == null){
                    if (entity.isComplete()) {
                        mAdapter.remove(entity);
                    }
                } else {
                    createAndShowDialog(exception, "Error");    
                }
            }
        });

    그러면 항목 업데이트가 모바일 서비스에 전송되고 선택한 항목이 어댑터에서 제거됩니다.

15. **addItem** 메서드에서 다음 줄의 주석 처리를 제거합니다.

        mToDoTable.insert(item, new TableOperationCallback<ToDoItem>() {

            public void onCompleted(ToDoItem entity, Exception exception,
                    ServiceFilterResponse response) {
                if(exception == null){
                    if (!entity.isComplete()) {
                        mAdapter.add(entity);
                    }
                } else {
                    createAndShowDialog(exception, "Error");
                }               
            }
        });

    이 코드는 새 항목을 만들어 원격 모바일 서비스의 테이블에 삽입합니다.

16. **refreshItemsFromTable** 메서드에서 다음 줄의 주석 처리를 해제합니다.

        mToDoTable.where().field("complete").eq(false)
        .execute(new TableQueryCallback<ToDoItem>() {
             public void onCompleted(List<ToDoItem> result, 
                     int count, Exception exception, 
                     ServiceFilterResponse response) {

                        if(exception == null){
                            mAdapter.clear();

                            for (ToDoItem item : result) {
                                mAdapter.add(item);
                            }
                        } else {
                            createAndShowDialog(exception, "Error");
                        }
                    }
                }); 

    그러면 모바일 서비스를 쿼리하여 완료로 표시되지 않은 모든 항목을 반환합니다. 바인딩을 위해 항목이 어댑터에 추가됩니다.

<!-- URLs. -->

  [모바일 서비스 Android SDK]: http://go.microsoft.com/fwlink/p/?LinkID=280126
  []: ./media/download-android-sample-code/mobile-dashboard-tab.png
