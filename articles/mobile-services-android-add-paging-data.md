<properties linkid="develop-mobile-tutorials-add-paging-to-data-android" urlDisplayName="Add paging to data" pageTitle="Add paging to data (Android) | Mobile Dev Center" metaKeywords="" description="Learn how to use paging to manage the amount of data returned to your Android app from Mobile Services." metaCanonical="" services="" documentationCenter="Mobile" title="Refine Mobile Services queries with paging" authors="" solutions="" manager="" editor="" />

페이징을 사용하여 모바일 서비스 쿼리 구체화
===========================================

[Windows 스토어 C\#](/en-us/develop/mobile/tutorials/add-paging-to-data-dotnet "Windows 스토어 C#")[Windows 스토어 JavaScript](/en-us/develop/mobile/tutorials/add-paging-to-data-js "Windows 스토어 JavaScript")[Windows Phone](/en-us/develop/mobile/tutorials/add-paging-to-data-wp8 "Windows Phone")[iOS](/en-us/develop/mobile/tutorials/add-paging-to-data-ios "iOS")[Android](/en-us/develop/mobile/tutorials/add-paging-to-data-android "Android")[HTML](/en-us/develop/mobile/tutorials/add-paging-to-data-html "HTML")[Xamarin.iOS](/en-us/develop/mobile/tutorials/add-paging-to-data-xamarin-ios "Xamarin.iOS")[Xamarin.Android](/en-us/develop/mobile/tutorials/add-paging-to-data-xamarin-android "Xamarin.Android")

이 항목에서는 페이징을 사용하여 Azure 모바일 서비스에서 Android 앱에 반환되는 데이터의 양을 관리하는 방법을 보여 줍니다. 이 자습서에서는 클라이언트에서 **top** 및 **skip** 쿼리 메서드를 사용하여 데이터의 특정 "페이지"를 요청합니다.

**참고**

모바일 장치 클라이언트에서 데이터 오버플로 방지를 위해 모바일 서비스에서는 자동 페이지 제한을 구현합니다. 기본값은 응답 시 최대 50개 항목입니다. 페이지 크기를 지정하여 응답에서 최대 1,000개 항목을 명시적으로 요청할 수 있습니다.

이 자습서는 이전 자습서인 [데이터 시작](/en-us/develop/mobile/tutorials/get-started-with-data-android)의 단계 및 샘플 앱을 기반으로 합니다. 이 자습서를 시작하기 전에 적어도 데이터 계열 작업에 대한 첫 번째 자습서인 [데이터 시작하기](/en-us/develop/mobile/tutorials/get-started-with-data-android) 자습서를 완료해야 합니다.

1.  Eclipse에서 [데이터 시작하기](/en-us/develop/mobile/tutorials/get-started-with-data-android) 자습서를 완료했을 때 생성된 프로젝트를 엽니다.

2.  **실행** 메뉴에서 **실행**을 클릭하여 앱을 시작한 다음 텍스트 상자에 텍스트를 입력하고 **추가** 단추를 클릭합니다.

3.  TodoItem 테이블에 저장된 항목이 네 개 이상이므로 이전 단계를 세 번 이상 반복합니다.

4.  ToDoActivity.java 파일에서 **RefreshTodoItems** 메서드를 다음 코드로 바꿉니다.

         private void refreshItemsFromTable() {
             // Define a filtered query that returns the top 3 items.
             mToDoTable.where().field("complete").eq(false).top(3)
                     .execute(new TableQueryCallback<ToDoItem>() {

                         public void onCompleted(List<ToDoItem> result, int count,
                                 Exception exception, ServiceFilterResponse response) {
                             if (exception == null) {
                                 mAdapter.clear();

                                 for (ToDoItem item : result) {
                                     mAdapter.add(item);
                                 }

                             } else {
                                 createAndShowDialog(exception, "Error");
                             }
                         }
                     });
         }

  	이 쿼리는 완료로 표시되지 않은 맨 위에 있는 세 개의 항목을 반환합니다.

1.  앱을 다시 빌드하고 시작합니다.

    TodoItem 테이블에서 처음 세 개의 결과만 표시됩니다.

2.  (옵션) 브라우저 개발 도구 또는 [Fiddler]와 같은 메시지 검사 소프트웨어를 사용하여 모바일 서비스에 전송된 요청의 URI를 봅니다.

   	'top(3)' 메서드는 쿼리 URI에서 쿼리 옵션 `$top=3`으로 변환되었습니다.

3.  **RefreshTodoItems** 메서드를 다음 코드로 한 번 더 업데이트합니다.

         private void refreshItemsFromTable() {
             // Define a filtered query that returns the top 3 items.
             mToDoTable.where().field("complete").eq(false).skip(3).top(3)
                     .execute(new TableQueryCallback<ToDoItem>() {

                         public void onCompleted(List<ToDoItem> result, int count,
                                 Exception exception, ServiceFilterResponse response) {
                             if (exception == null) {
                                 mAdapter.clear();

                                 for (ToDoItem item : result) {
                                     mAdapter.add(item);
                                 }

                             } else {
                                 createAndShowDialog(exception, "Error");
                             }
                         }
                     });
         }

  	이 쿼리는 처음 세 개의 결과를 건너뛰고 그다음 세 개의 결과를 반환합니다. 그러면 페이지 크기가 세 개의 항목인 경우 실질적으로 두 번째 데이터 "페이지"가 됩니다.

    **참고**

    이 자습서에서는 하드 코드된 페이징 값을 **Top** 및 **skip** 메서드에 전달하여 간소화된 시나리오를 사용합니다. 실제 앱에서는 Pager 컨트롤이나 이와 비슷한 UI에서 위와 비슷한 쿼리를 사용하여 사용자가 이전 및 다음 페이지로 이동하도록 만들 수 있습니다. **includeInlineCount** 메서드를 호출하여 서버에서 사용 가능한 총 항목 수를 페이징 데이터와 함께 가져올 수도 있습니다.

4.  (옵션) 모바일 서비스에 전송된 요청의 URI를 다시 봅니다.

   	'skip (3)' 메서드는 쿼리 URI에서 쿼리 옵션 `$skip=3`으로 변환되었습니다.

다음 단계
---------

이제 모바일 서비스의 데이터 작업 기본 사항을 설명하는 자습서를 마쳤습니다. 다음의 모바일 서비스 항목에 대해서도 자세히 알아보십시오.

-   [인증 시작하기](/en-us/develop/mobile/tutorials/get-started-with-users-android)
    <br/>Windows 계정으로 앱의 사용자를 인증하는 방법에 대해 알아보십시오.

-   [푸시 알림 시작](/en-us/develop/mobile/tutorials/get-started-with-push-android)
    <br/>기본적인 푸시 알림을 앱에 보내는 방법을 알아봅니다.


