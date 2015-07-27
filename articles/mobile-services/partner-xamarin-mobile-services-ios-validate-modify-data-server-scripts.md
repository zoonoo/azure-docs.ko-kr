<properties
	pageTitle="서버 스크립트를 사용하여 데이터 유효성 검사 및 수정(Xamarin iOS) | 모바일 개발자 센터"
	description="Xamarin iOS 앱에서 서버 스크립트를 사용하여 전송된 데이터의 유효성을 검사하고 데이터를 수정하는 방법에 대해 알아봅니다."
	services="mobile-services"
	documentationCenter="xamarin"
	authors="ggailey777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-xamarin-ios"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="04/24/2015"
	ms.author="ggailey777"/>

# 서버 스크립트를 사용하여 모바일 서비스의 데이터 유효성 검사 및 수정

[AZURE.INCLUDE [mobile-services-selector-validate-modify-data](../../includes/mobile-services-selector-validate-modify-data.md)]

이 항목에서는 Azure 모바일 서비스에서 서버 스크립트를 활용하는 방법을 보여 줍니다. 서버 스크립트는 모바일 서비스에서 등록되며 삽입 및 업데이트할 데이터에 대해 유효성 검사 및 데이터 수정을 포함한 다양한 작업을 수행하는 데 사용할 수 있습니다. 이 자습서에서는 데이터의 유효성을 검사하고 데이터를 수정하는 서버 스크립트를 정의하고 등록합니다. 서버 쪽 스크립트의 동작이 클라이언트에 영향을 미치는 경우가 많기 때문에 이런 새로운 동작을 활용하도록 iOS 앱도 업데이트합니다. 완성된 코드는 [ValidateModifyData 앱][GitHub] 샘플에서 확인할 수 있습니다.

이 자습서에서는 다음 기본 단계를 단계별로 안내합니다.

1. [문자열 길이 유효성 검사 추가]
2. [유효성 검사를 지원하도록 클라이언트 업데이트]
3. [삽입 시 타임스탬프 추가]
4. [타임스탬프를 표시하도록 클라이언트 업데이트]

이 자습서는 이전 자습서인 [데이터 시작]의 단계 및 샘플 앱을 기반으로 합니다. 이 자습서를 시작하기 전에 먼저 [데이터 시작]을 완료해야 합니다.

## <a name="string-length-validation"></a>유효성 검사 추가

사용자가 제출하는 데이터 길이의 유효성을 항상 검사하는 것이 좋습니다. 먼저, 모바일 서비스에 전송되는 문자열 데이터 길이의 유효성을 검사하고 너무 긴 경우(이 예제에서는 10자 초과) 문자열을 거부하는 스크립트를 등록합니다.

1. [Azure 관리 포털]에 로그인하여 **Mobile Services**를 클릭한 후 앱을 클릭합니다.

	![][0]

2. **데이터** 탭을 클릭한 후 **TodoItem** 테이블을 클릭합니다.

   	![][1]

3. **스크립트**를 클릭한 후 **삽입** 작업을 선택합니다.

   	![][2]

4. 기존 스크립트를 다음 함수로 바꾼 후 **Save**를 클릭합니다.

				function insert(item, user, request) {
        	if (item.text.length > 10) {
            request.respond(statusCodes.BAD_REQUEST, 'Text length must be 10 characters or less.');
          } else {
            request.execute();
          }
        }

    이 스크립트는 **text** 속성의 길이를 확인하여 길이가 10자를 초과하는 경우 오류 응답을 보냅니다. 그렇지 않으면 **execute** 메서드가 호출되어 삽입이 완료됩니다.

    > [AZURE.NOTE]**지우기**를 클릭한 후 **저장**을 클릭하여 **스크립트** 탭에서 등록된 스크립트를 제거할 수 있습니다.

## <a name="update-client-validation"></a>클라이언트 업데이트

모바일 서비스가 데이터의 유효성 검사를 하고 오류 응답을 보내므로, 개발자는 유효성 검사의 오류 응답을 처리할 수 있도록 앱을 업데이트해야 합니다.

1. Xamarin Studio에서 [데이터 시작] 자습서를 완료할 때 수정한 프로젝트를 엽니다.

2. **실행** 단추를 눌러 프로젝트를 빌드하고 앱을 시작한 후 입력란에 10자를 초과하는 텍스트를 입력하고 더하기(+) 아이콘을 클릭합니다.

	앱에서는 모바일 서비스에서 반환된 400 응답(잘못된 요청)의 결과로 처리되지 않은 오류를 제기합니다.

3. TodoService.cs 파일의 **InsertTodoItemAsync** 메서드에서 현재 <code>try/catch</code> 예외 처리를 찾아 <code>catch</code>를 다음으로 바꿉니다.

    catch (Exception ex) { var exDetail = (ex.InnerException.InnerException as MobileServiceInvalidOperationException); Console.WriteLine(exDetail.Message);

        UIAlertView alert = new UIAlertView() {
            	Title = "Error",
            	Message = exDetail.Message
        } ;
        alert.AddButton("Ok");
        alert.Show();

        return -1;
		}

	이 코드는 사용자에게 오류를 표시하는 팝업 창을 보여 줍니다.

4. **TodoListViewController.cs**에서 **OnAdd** 메서드를 찾습니다. 반환된 <code>index</code>가 <code>-1</code>이 아니도록 메서드를 업데이트합니다(-1일 경우 **InsertTodoItemAsync**에서 예외 처리에 반환됨). 이 경우 <code>TableView</code>에 새 행을 추가하지 않으려고 합니다.

    if (index != -1) { TableView.InsertRows(new { NSIndexPath.FromItemSection(index, 0) }, UITableViewRowAnimation.Top); itemText.Text = ""; }


5. 앱을 다시 빌드하고 시작합니다.

	![][4]

	오류가 처리되고 사용자에게 오류 메시지가 표시됩니다.


## <a name="next-steps"> </a>다음 단계

이 자습서를 완료했으므로 이제 데이터 시리즈의 마지막 자습서인 [페이징을 사용하여 쿼리 구체화]를 계속 검토해 보세요.

서버 스크립트는 사용자를 인증할 때 및 푸시 알림을 보내기 위해서도 사용됩니다. 자세한 내용은 다음 자습서를 참조하십시오.

* [스크립트를 통해 사용자 권한 부여] <br/>인증된 사용자의 ID를 기준으로 데이터를 필터링하는 방법을 알아봅니다.

* [푸시 알림 시작] <br/>기본적인 푸시 알림을 앱에 보내는 방법을 알아봅니다.

* [모바일 서비스 서버 스크립트 참조] <br/>서버 스크립트의 등록 및 사용에 대해 자세히 알아봅니다.

<!-- Anchors. -->
[문자열 길이 유효성 검사 추가]: #string-length-validation
[유효성 검사를 지원하도록 클라이언트 업데이트]: #update-client-validation
[삽입 시 타임스탬프 추가]: #add-timestamp
[타임스탬프를 표시하도록 클라이언트 업데이트]: #update-client-timestamp
[Next Steps]: #next-steps

<!-- Images. -->
[0]: ./media/partner-xamarin-mobile-services-ios-validate-modify-data-server-scripts/mobile-services-selection.png
[1]: ./media/partner-xamarin-mobile-services-ios-validate-modify-data-server-scripts/mobile-portal-data-tables.png
[2]: ./media/partner-xamarin-mobile-services-ios-validate-modify-data-server-scripts/mobile-insert-script-users.png

[4]: ./media/partner-xamarin-mobile-services-ios-validate-modify-data-server-scripts/mobile-quickstart-data-error-ios.png

<!-- URLs. -->
[모바일 서비스 서버 스크립트 참조]: http://go.microsoft.com/fwlink/?LinkId=262293
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-xamarin-ios
[스크립트를 통해 사용자 권한 부여]: /develop/mobile/tutorials/authorize-users-in-scripts-xamarin-ios
[페이징을 사용하여 쿼리 구체화]: /develop/mobile/tutorials/add-paging-to-data-xamarin-ios
[데이터 시작]: /develop/mobile/tutorials/get-started-with-data-xamarin-ios
[Get started with authentication]: /develop/mobile/tutorials/get-started-with-users-xamarin-ios
[푸시 알림 시작]: /develop/mobile/tutorials/get-started-with-push-xamarin-ios

[Management Portal]: https://manage.windowsazure.com/
[Azure 관리 포털]: https://manage.windowsazure.com/
[GitHub]: http://go.microsoft.com/fwlink/p/?LinkId=331330
 

<!---HONumber=July15_HO3-->