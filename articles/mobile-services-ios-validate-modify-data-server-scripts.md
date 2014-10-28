<properties linkid="develop-mobile-tutorials-validate-modify-and-augment-data-ios" urlDisplayName="Validate Data" pageTitle="Use server scripts to validate and modify data (iOS) | Mobile Dev Center" metaKeywords="" description="Learn how to validate and modify data sent using server scripts from your iOS app." metaCanonical="" services="" documentationCenter="Mobile" title="Validate and modify data in Mobile Services by using server scripts" authors="krisragh" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="01/01/1900" ms.author="krisragh"></tags>

# 모바일 서비스에서 서버 스크립트를 사용하여 데이터 유효성 검사 및 수정

<div class="dev-center-tutorial-selector sublanding">

[Windows 스토어 C#][Windows 스토어 C#][Windows 스토어 JavaScript][Windows 스토어 JavaScript][Windows Phone][Windows Phone][iOS][iOS][Android][Android][HTML][HTML][Xamarin.iOS][Xamarin.iOS][Xamarin.Android][Xamarin.Android]

</div>

이 항목에서는 Azure 모바일 서비스에서 서버 스크립트를 활용하는 방법을 보여 줍니다. 서버 스크립트는 모바일 서비스에서 등록되며 삽입 및 업데이트할 데이터에 대해 유효성 검사 및 데이터 수정을 포함한 다양한 작업을 수행하는 데 사용할 수 있습니다. 이 자습서에서는 데이터의 유효성을 검사하고 데이터를 수정하는 서버 스크립트를 정의하고 등록합니다. 서버 쪽 스크립트의 동작이 클라이언트에 영향을 미치는 경우가 많기 때문에 이런 새로운 동작을 활용하도록 iOS 앱도 업데이트합니다.

이 자습서에서는 다음 기본 단계를 단계별로 안내합니다.

1.  [문자열 길이 유효성 검사 추가][문자열 길이 유효성 검사 추가]
2.  [유효성 검사를 지원하도록 클라이언트 업데이트][유효성 검사를 지원하도록 클라이언트 업데이트]

이 자습서는 이전 자습서인 [데이터 시작][데이터 시작]의 단계 및 샘플 앱을 기반으로 합니다. 이 자습서를 시작하기 전에 먼저 [데이터 시작][데이터 시작]을 완료해야 합니다.

## <a name="string-length-validation"></a>유효성 검사 추가

사용자가 제출하는 데이터 길이의 유효성을 항상 검사하는 것이 좋습니다. 먼저, 모바일 서비스에 전송되는 문자열 데이터 길이의 유효성을 검사하고 너무 긴 경우(이 예제에서는 10자 초과) 문자열을 거부하는 스크립트를 등록합니다.

1.  [Azure 관리 포털][Azure 관리 포털]에 로그인하여 **모바일 서비스**를 클릭한 후 앱을 클릭합니다.

    ![][]

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

    **참고**
    **지우기**를 클릭한 후 **저장**을 클릭하여 **스크립트** 탭에서 등록된 스크립트를 제거할 수 있습니다.

    </div>

## <a name="update-client-validation"></a>클라이언트 업데이트

모바일 서비스가 데이터의 유효성 검사를 하고 오류 응답을 보내므로, 개발자는 유효성 검사의 오류 응답을 처리할 수 있도록 앱을 업데이트해야 합니다.

1.  Xcode에서 [데이터 시작][데이터 시작] 자습서를 완료할 때 수정한 프로젝트를 엽니다.

2.  **실행** 단추(Command + R)를 눌러 프로젝트를 빌드하고 앱을 시작한 다음 텍스트 상자에 텍스트를 10자 미만으로 입력하고 더하기(**+**) 아이콘을 클릭합니다.

    앱은 모바일 서비스에서 반환된 400 응답(잘못된 요청)의 결과로 처리되지 않은 오류를 생성합니다.

3.  QSTodoService.m 파일의 **addItem** 메서드에서 다음 코드 줄을 찾습니다.

        [self logErrorIfNotNil:error]; 

    이 코드 줄 뒤에 있는 완료 블록의 나머지 부분을 다음 코드로 바꿉니다.

        BOOL goodRequest = !((error) && (error.code == MSErrorMessageErrorCode));

        // detect text validation error from service.
        if (goodRequest) // The service responded appropriately
        {
            NSUInteger index = [items count];
            [(NSMutableArray *)items insertObject:result atIndex:index];

            // Let the caller know that we finished
            completion(index);
        }
        else{

            // if there's an error that came from the service
            // log it, and popup up the returned string.
            if (error && error.code == MSErrorMessageErrorCode) {
                NSLog(@"ERROR %@", error);
                UIAlertView *av =
                [[UIAlertView alloc]
                 initWithTitle:@"Request Failed"
                 message:error.localizedDescription
                 delegate:nil
                 cancelButtonTitle:@"OK"
                 otherButtonTitles:nil
                 ];
                [av show];
            }
        }

    이 코드에서는 오류를 출력 창에 기록하고 사용자에게 표시합니다.

4.  앱을 다시 빌드하고 시작합니다.

    ![][3]

    오류가 처리되고 사용자에게 오류 메시지가 표시됩니다.

<!--## <a name="add-timestamp"></a>Add a timestamp  The previous tasks validated an insert and either accepted or rejected it. Now, you will update inserted data by using a server script that adds a timestamp property to the object before it gets inserted.  1. In the **Scripts** tab in the [Management Portal], replace the current **Insert** script with the following function, and then click **Save**.          function insert(item, user, request) {             if (item.text.length > 10) {                 request.respond(statusCodes.BAD_REQUEST, 'Text length must be under 10');             } else {                 item.createdAt = new Date();                 request.execute();             }         }      This function augments the previous insert script by adding a new **createdAt** timestamp property to the object before it gets inserted by the call to **request**.**execute**.       <div class="dev-callout"><b>Note</b>     <p>Dynamic schema must be enabled the first time that this insert script runs. With dynamic schema enabled, Mobile Services automatically adds the <strong>createdAt</strong> column to the <strong>TodoItem</strong> table on the first execution. Dynamic schema is enabled by default for a new mobile service, and it should be disabled before the app is published.</p>     </div>  2. In Visual Studio, press the **F5** key to run the app, then type text (shorter than 10 characters) in **Insert a TodoItem** and click **Save**.      Notice that the new timestamp does not appear in the app UI.  3. Back in the Management Portal, click the **Browse** tab in the **todoitem** table.         Notice that there is now a **createdAt** column, and the new inserted item has a timestamp value.    Next, you need to update the iOS app to display this new column.  ## <a name="update-client-timestamp"></a>Update the client again  The Mobile Service client will ignore any data in a response that it cannot serialize into properties on the defined type. The final step is to update the client to display this new data.  1. In Visual Studio, open the file MainPage.xaml.cs, then replace the existing **TodoItem** class with the following definition:          public class TodoItem         {             public int Id { get; set; }                        [DataMember(Name="text")]             public string Text { get; set; }              [DataMember(Name="complete")]             public bool Complete { get; set; }                          [DataMember(Name="createdAt")]             public DateTime? CreatedAt { get; set; }         }          This new class definition includes the new timestamp property, as a nullable DateTime type.        <div class="dev-callout"><b>Note</b>     <p>The <strong>DataMemberAttribute</strong> tells the client to map the new <strong>CreatedAt</strong> property in the app to the <strong>createdAt</strong> column defined in the TodoItem table, which has a different casing. By using this attribute, your app can have property names on objects that differ from column names in the SQL Database. Without this attribute, an error would occur because of the casing differences.</p>     </div>  5. Add the following XAML element just below the **CheckBoxComplete** element in the MainPage.xaml file:                    <TextBlock Name="WhenCreated" Text="{Binding CreatedAt}" VerticalAlignment="Center"/>      This displays the new **CreatedAt** property in a text box.       6. Press the **F5** key to run the app.      Notice that the timestamp is only displayed for items inserted after you updated the insert script.  7. Replace the existing **RefreshTodoItems** method with the following code:           private void RefreshTodoItems()         {              // This query filters out completed TodoItems and              // items without a timestamp.              items = todoTable                .Where(todoItem => todoItem.Complete == false                    && todoItem.CreatedAt != null)                .ToCollectionView();              ListItems.ItemsSource = items;         }      This method updates the query to also filter out items that do not have a timestamp value.      8. Press the **F5** key to run the app.      Notice that all items created without timestamp value disappear from the UI.  You have completed this working with data tutorial.-->

## <a name="next-steps"> </a>다음 단계

이 자습서를 완료했으므로 이제 데이터 시리즈의 마지막 자습서인 [페이징을 사용하여 쿼리 구체화][페이징을 사용하여 쿼리 구체화]를 검토해 보십시오.

서버 스크립트는 사용자를 인증할 때 및 푸시 알림을 보내기 위해서도 사용됩니다. 자세한 내용은 다음 자습서를 참조하십시오.

-   [스크립트를 통해 사용자 권한 부여][스크립트를 통해 사용자 권한 부여]
    인증된 사용자의 ID를 기준으로 데이터를 필터링하는 방법을 알아봅니다.

-   [푸시 알림 시작][푸시 알림 시작]
    기본적인 푸시 알림을 앱에 보내는 방법을 알아봅니다.

-   [모바일 서비스 서버 스크립트 참조][모바일 서비스 서버 스크립트 참조]
    서버 스크립트의 등록 및 사용에 대해 자세히 알아보십시오.

<!-- Anchors. --> <!-- Images. --> <!-- URLs. -->

  [Windows 스토어 C#]: /ko-kr/documentation/articles/mobile-services-windows-store-dotnet-validate-modify-data-server-scripts "Windows 스토어 C#"
  [Windows 스토어 JavaScript]: /ko-kr/documentation/articles/mobile-services-windows-store-javascript-validate-modify-data-server-scripts "Windows 스토어 JavaScript"
  [Windows Phone]: /ko-kr/documentation/articles/mobile-services-windows-phone-validate-modify-data-server-scripts "Windows Phone"
  [iOS]: /ko-kr/documentation/articles/mobile-services-ios-validate-modify-data-server-scripts "iOS"
  [Android]: /ko-kr/documentation/articles/mobile-services-android-validate-modify-data-server-scripts "Android"
  [HTML]: /ko-kr/documentation/articles/mobile-services-html-validate-modify-data-server-scripts "HTML"
  [Xamarin.iOS]: /ko-kr/documentation/articles/partner-xamarin-mobile-services-ios-validate-modify-data-server-scripts "Xamarin.iOS"
  [Xamarin.Android]: /ko-kr/documentation/articles/partner-xamarin-mobile-services-android-validate-modify-data-server-scripts "Xamarin.Android"
  [문자열 길이 유효성 검사 추가]: #string-length-validation
  [유효성 검사를 지원하도록 클라이언트 업데이트]: #update-client-validation
  [데이터 시작]: /ko-kr/develop/mobile/tutorials/get-started-with-data-ios
  [Azure 관리 포털]: https://manage.windowsazure.com/
  []: ./media/mobile-services-ios-validate-modify-data-server-scripts/mobile-services-selection.png
  [1]: ./media/mobile-services-ios-validate-modify-data-server-scripts/mobile-portal-data-tables.png
  [2]: ./media/mobile-services-ios-validate-modify-data-server-scripts/mobile-insert-script-users.png
  [3]: ./media/mobile-services-ios-validate-modify-data-server-scripts/mobile-quickstart-data-error-ios.png
  [페이징을 사용하여 쿼리 구체화]: /ko-kr/develop/mobile/tutorials/add-paging-to-data-ios
  [스크립트를 통해 사용자 권한 부여]: /ko-kr/develop/mobile/tutorials/authorize-users-in-scripts-ios
  [푸시 알림 시작]: /ko-kr/develop/mobile/tutorials/get-started-with-push-ios
  [모바일 서비스 서버 스크립트 참조]: http://go.microsoft.com/fwlink/?LinkId=262293
