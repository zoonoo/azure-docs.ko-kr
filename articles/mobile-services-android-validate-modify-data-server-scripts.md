<properties linkid="develop-mobile-tutorials-validate-modify-and-augment-data-android" urlDisplayName="Validate Data - Android" pageTitle="Use server scripts to validate and modify data (Android) | Mobile Dev Center" metaKeywords="" description="Learn how to validate and modify data sent using server scripts from your Android app." metaCanonical="" services="" documentationCenter="Mobile" title="Validate and modify data in Mobile Services by using server scripts" authors="ricksal" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="Mobile-Android" ms.devlang="Java" ms.topic="article" ms.date="01/01/1900" ms.author="ricksal" />

# 모바일 서비스에서 서버 스크립트를 사용하여 데이터 유효성 검사 및 수정

<div class="dev-center-tutorial-selector sublanding"> 
    <a href="/ko-kr/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet" title="Windows 스토어 C#">Windows 스토어 C#</a><a href="/ko-kr/develop/mobile/tutorials/validate-modify-and-augment-data-js" title="Windows 스토어 JavaScript">Windows 스토어 JavaScript</a><a href="/ko-kr/develop/mobile/tutorials/validate-modify-and-augment-data-wp8" title="Windows Phone">Windows Phone</a><a href="/ko-kr/develop/mobile/tutorials/validate-modify-and-augment-data-ios" title="iOS">iOS</a><a href="/ko-kr/develop/mobile/tutorials/validate-modify-and-augment-data-android" title="Android">Android</a><a href="/ko-kr/develop/mobile/tutorials/validate-modify-and-augment-data-html" title="HTML">HTML</a><a href="/ko-kr/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-ios" title="Xamarin.iOS">Xamarin.iOS</a><a href="/ko-kr/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-android" title="Xamarin.Android" class="current">Xamarin.Android</a>
</div>

이 항목에서는 Azure 모바일 서비스에서 서버 스크립트를 활용하는 방법을 보여 줍니다. 서버 스크립트는 모바일 서비스에서 등록되며 삽입 및 업데이트할 데이터에 대해 유효성 검사 및 데이터 수정을 포함한 다양한 작업을 수행하는 데 사용할 수 있습니다. 이 자습서에서는 데이터의 유효성을 검사하고 데이터를 수정하는 서버 스크립트를 정의하고 등록합니다. 서버 쪽 스크립트의 동작이 클라이언트에 영향을 미치는 경우가 많기 때문에 이런 새로운 동작을 활용하도록 Android 앱도 업데이트합니다.

이 자습서에서는 다음 기본 단계를 단계별로 안내합니다.

1.  [문자열 길이 유효성 검사 추가][문자열 길이 유효성 검사 추가]
2.  [유효성 검사를 지원하도록 클라이언트 업데이트][유효성 검사를 지원하도록 클라이언트 업데이트]
3.  [삽입 시 타임스탬프 추가][삽입 시 타임스탬프 추가]
4.  [타임스탬프를 표시하도록 클라이언트 업데이트][타임스탬프를 표시하도록 클라이언트 업데이트]

이 자습서는 이전 자습서인 [데이터 시작][데이터 시작]의 단계 및 샘플 앱을 기반으로 합니다. 이 자습서를 시작하기 전에 먼저 [데이터 시작][데이터 시작]을 완료해야 합니다.

## <a name="string-length-validation"></a>유효성 검사 추가

사용자가 제출하는 데이터 길이의 유효성을 항상 검사하는 것이 좋습니다. 먼저, 모바일 서비스에 전송되는 문자열 데이터 길이의 유효성을 검사하고 너무 긴 경우(이 예제에서는 10자 초과) 문자열을 거부하는 스크립트를 등록합니다.

1.  [Azure 관리 포털][Azure 관리 포털]에 로그인하여 **모바일 서비스**를 클릭한 후 앱을 클릭합니다.

    ![][0]

2.  **데이터** 탭을 클릭한 후 **TodoItem** 테이블을 클릭합니다.

    ![][1]

3.  **스크립트**를 클릭한 후 **삽입** 작업을 선택합니다.

    ![][2]

4.  기존 스크립트를 다음 함수로 바꾼 후 **저장**을 클릭합니다.

        function insert(item, user, request) {
            if (item.text.length > 10) {
                request.respond(statusCodes.BAD_REQUEST, 'Text length must be 10 characters or less.');
            } else {
                request.execute();
            }
        }

    이 스크립트는 **text** 속성의 길이를 확인하여 길이가 10자를 초과하는 경우 오류 응답을 보냅니다. 그렇지 않으면 **execute** 메서드가 호출되어 삽입이 완료됩니다.

    <div class="dev-callout"> 
<b>참고</b> 
<p><strong>지우기</strong>를 클릭한 후 <strong>저장</strong>을 클릭하여 <strong>스크립트</strong> 탭에서 등록된 스크립트를 제거할 수 있습니다.</p></div>

## <a name="update-client-validation"></a>클라이언트 업데이트

모바일 서비스가 데이터의 유효성 검사를 하고 오류 응답을 보내므로, 개발자는 앱이 유효성 검사의 오류 응답을 올바르게 처리하는지 확인해야 합니다.

1.  Eclipse에서 [데이터 시작하기][데이터 시작] 자습서를 완료했을 때 생성된 프로젝트를 엽니다.

2.  ToDoActivity.java 파일에서 **addItem** 메서드를 찾아 createAndShowDialog 메서드 호출을 다음 코드로 바꿉니다.

        createAndShowDialog(exception.getCause().getMessage(), "Error");

    그러면 모바일 서비스에서 반환하는 오류 메시지가 표시됩니다.

3.  **실행** 메뉴에서 **실행**을 클릭하여 앱을 시작한 다음 텍스트 상자에 텍스트를 11자 이상 입력하고 **추가** 단추를 클릭합니다.

  오류가 처리되고 사용자에게 오류 메시지가 표시됩니다.

## <a name="add-timestamp"></a>타임스탬프 추가

이전 작업에서는 삽입의 유효성을 검사하여 삽입을 허용하거나 거부했습니다. 이제, 삽입되기 전에 개체에 타임스탬프 속성을 추가하는 서버 스크립트를 사용하여 삽입된 데이터를 업데이트합니다.

<div class="dev-callout"><b>참고</b>
<p>여기 나온 <b>createdAt</b> 타임스탬프 속성은 이제 중복됩니다. 모바일 서비스는 각 테이블의 <b>__createdAt</b> 시스템 속성을 자동으로 만듭니다.</p>
</div>

1.  [관리 포털][Azure 관리 포털]의 **스크립트** 탭에서 현재 **삽입** 스크립트를 다음 함수로 바꾼 후 **저장**을 클릭합니다.

        function insert(item, user, request) {
            if (item.text.length > 10) {
                request.respond(statusCodes.BAD_REQUEST, 'Text length must be under 10');
            } else {
                item.createdAt = new Date();
                request.execute();
            }
        }

    이 함수는 **request**.**execute** 호출에 의해 삽입되기 전에 개체에 새 **createdAt** 타임스탬프 속성을 추가하여 이전 삽입 스크립트를 보강합니다.

    <div class="dev-callout"><b>참고</b>
<p>이 삽입 스크립트를 처음 실행할 때는 동적 스키마를 사용하도록 설정해야 합니다. 동적 스키마를 사용하도록 설정하면 첫 번째 실행 시 모바일 서비스가 <strong>createdAt</strong> 열을 <strong>TodoItem</strong> 테이블에 자동으로 추가합니다. 동적 스키마는 기본적으로 새 모바일 서비스에 대해 사용하도록 설정되어 있으며 앱을 게시하기 전에 이 스키마를 사용하지 않도록 설정해야 합니다.</p>
</div>

2.  **실행** 메뉴에서 **실행**을 클릭하여 앱을 시작한 다음 텍스트 상자에 텍스트(10자 미만)를 입력하고 **추가**를 클릭합니다.

    새 타임스탬프는 앱 UI에서 표시되지 않습니다.

3.  관리 포털로 돌아가서 **todoitem** 테이블에서 **찾아보기** 탭을 클릭합니다.

    이제 **createdAt** 열이 생겼고 새로 삽입된 항목에 타임스탬프 값이 추가되었습니다.

다음으로, 이 새 열을 표시하도록 Android 앱을 업데이트해야 합니다.

## <a name="update-client-timestamp"></a>클라이언트 다시 업데이트

모바일 서비스 클라이언트는 정의 형식에 대한 속성으로 직렬화할 수 없는 응답 데이터를 무시합니다. 마지막 단계는 이 새 데이터를 표시하도록 클라이언트를 업데이트하는 것입니다.

1.  Package Explorer에서 ToDoItem.java 파일을 열고 다음 **import** 문을 추가합니다.

        import java.util.Date;

2.  **TodoItem** 클래스의 전용 필드 정의에 다음 코드를 추가합니다.

        /**
         * Timestamp of the item inserted by the service.
         */
        @com.google.gson.annotations.SerializedName("createdAt")
        private Date mCreatedAt;

    <div class="dev-callout"><b>참고</b>
<p><code data-inline="1">SerializedName</code> 주석은 앱의 새 <code data-inline="1">mCreatedAt</code> 속성을 TodoItem 테이블에 정의된 <code data-inline="1">createdAt</code> 열(이름이 다름)에 매핑하도록 클라이언트에 지시합니다. 이 주석을 사용하면 앱이 SQL 데이터베이스의 열 이름과 다른 개체 속성 이름을 가질 수 있습니다. 이 주석을 사용하지 않는 경우 대/소문자 차이 때문에 오류가 발생합니다.</p>
</div>

3.  ToDoItem 클래스에 다음 메서드를 추가하여 새 mCreatedAt 속성을 가져와서 설정합니다.

        /**
         * Sets the timestamp.
         * 
         * @param date
         *            timestamp to set
         */
        public final void setCreatedAt(Date date) {
            mCreatedAt = date;
        }

        /**
         * Returns the timestamp.
         */
        public Date getCreatedAt() {
            return mCreatedAt;
        }

4.  Package Explorer에서 ToDoItemAdapter.java 파일을 열고 다음 **import** 문을 추가합니다.

        import java.text.DateFormat;

5.  GetView 메서드에 다음 코드를 추가합니다.

        String createdAtText = "";
        if (currentItem.getCreatedAt() != null){
            DateFormat formatter = DateFormat.getDateInstance(DateFormat.SHORT);
            createdAtText = formatter.format(currentItem.getCreatedAt());
        }

    이 코드는 타임스탬프 값이 있는 경우 형식이 지정된 날짜 문자열을 생성합니다.

6.  `checkBox.setText(currentItem.getText());` 코드를 찾아 이 코드 줄을 다음으로 바꿉니다.

        checkBox.setText(currentItem.getText() + " " + createdAtText);

    항목에 표시할 타임스탬프 날짜가 추가됩니다.

7.  **Run** 메뉴에서 **Run**을 클릭하여 앱을 시작합니다.

    타임스탬프는 삽입 스크립트를 업데이트한 후 삽입된 항목에만 표시됩니다.

8.  기존 **RefreshItemsFromTable** 메서드를 다음 코드로 바꿉니다.

        private void refreshItemsFromTable() {

            mToDoTable.where().field("complete").eq(false).and().field("createdAt").ne((String)null)
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

    또한 이 메서드는 쿼리를 업데이트하여 타임스탬프 값이 없는 항목을 필터링합니다.

9.  **Run** 메뉴에서 **Run**을 클릭하여 앱을 시작합니다.

    타임스탬프 값 없이 생성된 모든 항목이 UI에서 사라집니다.

데이터 작업에 대한 이 자습서를 완료했습니다.

## <a name="next-steps"> </a>다음 단계

이 자습서를 완료했으므로 이제 데이터 시리즈의 마지막 자습서인 [페이징을 사용하여 쿼리 구체화][페이징을 사용하여 쿼리 구체화]를 검토해 보십시오.

서버 스크립트는 사용자를 인증할 때 및 푸시 알림을 보내기 위해서도 사용됩니다. 자세한 내용은 다음 자습서를 참조하십시오.

-   [스크립트를 통해 사용자 권한 부여][스크립트를 통해 사용자 권한 부여]
    인증된 사용자의 ID를 기준으로 데이터를 필터링하는 방법을 알아봅니다.

-   [푸시 알림 시작][푸시 알림 시작]
    기본적인 푸시 알림을 앱에 보내는 방법을 알아봅니다.

-   [모바일 서비스 서버 스크립트 참조][모바일 서비스 서버 스크립트 참조]
    서버 스크립트의 등록 및 사용에 대해 자세히 알아보십시오.

<!-- Anchors. --> 
<!-- Images. --> 
<!-- URLs. -->

  [문자열 길이 유효성 검사 추가]: #string-length-validation
  [유효성 검사를 지원하도록 클라이언트 업데이트]: #update-client-validation
  [삽입 시 타임스탬프 추가]: #add-timestamp
  [타임스탬프를 표시하도록 클라이언트 업데이트]: #update-client-timestamp
  [데이터 시작]: /ko-kr/develop/mobile/tutorials/get-started-with-data-android
  [Azure 관리 포털]: https://manage.windowsazure.com/
  [0]: ./media/mobile-services-android-validate-modify-data-server-scripts/mobile-services-selection.png
  [1]: ./media/mobile-services-android-validate-modify-data-server-scripts/mobile-portal-data-tables.png
  [2]: ./media/mobile-services-android-validate-modify-data-server-scripts/mobile-insert-script-users.png
  [페이징을 사용하여 쿼리 구체화]: /ko-kr/develop/mobile/tutorials/add-paging-to-data-android
  [스크립트를 통해 사용자 권한 부여]: /ko-kr/develop/mobile/tutorials/authorize-users-in-scripts-android
  [푸시 알림 시작]: /ko-kr/develop/mobile/tutorials/get-started-with-push-android
  [모바일 서비스 서버 스크립트 참조]: http://go.microsoft.com/fwlink/?LinkId=262293
