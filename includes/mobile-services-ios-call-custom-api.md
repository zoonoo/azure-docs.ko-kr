## <a name="update-app"></a><span class="short-header">앱 업데이트 </span>사용자 지정 API를 호출하도록 앱 업데이트

1.  단추를 클릭하여 사용자 지정 API를 호출할 수 있는 단추를 만듭니다. **유틸리티** 창 아래쪽에 있는 **개체 라이브러리**에서 **모서리가 둥근 직사각형 단추**를 끌어서 텍스트 필드 아래 또는 옆에 놓습니다. 두 번 클릭하여 **"All"**이라는 텍스트를 추가합니다.

    그러면 새 단추 **"All"**이 추가됩니다.

2.  **QSTodoService.m** 코드 파일을 열고 `refreshDataOnSuccess` 메서드를 찾아 다음 코드가 포함되었는지 확인합니다.

        - (void)refreshDataOnSuccess:(QSCompletionBlock)completion
        {          
            // Create a predicate that finds items where complete is false
            NSPredicate * predicate = [NSPredicate predicateWithFormat:@"complete == NO"];

            // Query the TodoItem table and update the items property with the results from the service
            [self.table readWithPredicate:predicate completion:^(NSArray *results, NSInteger totalCount, NSError *error)
            {
                [self logErrorIfNotNil:error];

                items = [results mutableCopy];

                // Let the caller know that we finished
                completion();
            }];                                 
        }

    이 코드는 완료된 항목이 쿼리에서 반환되지 않도록 항목을 필터링합니다.

3.  이제 이 개체를 보기 컨트롤러 소스 코드에 연결합니다. **Ctrl 키를 누른 채** 새 **"All"** 단추를 클릭하고 마우스를 끌어서 **QSTodoListViewController.h**의 `@end` 줄 앞에 놓습니다. **QSTodoListViewController**에서 이름이 `onCompleteAll`인 새 **작업**에 개체를 연결합니다. Xcode에서 `@end` 줄 앞에 다음 줄을 자동으로 삽입합니다.

           - (IBAction)onCompleteAll:(id)sender;

4.  이 `onCompleteAll` 메서드의 목표는 새 단추의 Click 이벤트를 처리하는 것입니다. 이 이벤트는 사용자 지정 클래스에 추가할 새 `completeAll` 메서드를 호출하며 이 메서드는 POST 요청을 새 사용자 지정 API로 보냅니다. 사용자 지정 API에서 반환하는 결과는 오류와 마찬가지로 메시지 대화 상자에 표시됩니다. **QSTodoListViewController.m**을 편집하여 `@end` 줄 앞에 다음 구현을 추가합니다.

           - (IBAction)onCompleteAll:(id)sender {
            [self.todoService completeAll:^(id result, NSHTTPURLResponse* response, NSError* error)
             {
                 if (error)
                 {
                     NSString* errorMessage = @"There was a problem! ";
                     errorMessage = [errorMessage stringByAppendingString:[error localizedDescription]];
                     UIAlertView* myAlert = [[UIAlertView alloc]
                                             initWithTitle:@"Error!"
                                             message:errorMessage
                                             delegate:nil
                                             cancelButtonTitle:@"Okay"
                                             otherButtonTitles:nil];
                     [myAlert show];
                     [self refresh];
                 } else {
                     NSString* successMessage = [NSString stringWithFormat:@"%d items marked as complete", [[result objectForKey:@"count"] integerValue]];                   
                     UIAlertView* myAlert = [[UIAlertView alloc]
                                             initWithTitle:@"Success!"
                                             message:successMessage
                                             delegate:nil
                                             cancelButtonTitle:@"Okay"
                                             otherButtonTitles:nil];
                     [myAlert show];
                     [self refresh];
                 }
             }];
           }

5.  위 코드는 **QSTodoService**에서 아직 정의되지 않은 새 메서드 `completeAll`을 참조합니다. **QSTodoService.h**를 편집하여 `@end` 줄 앞에 다음 줄을 추가합니다.

        - (void) completeAll:(MSAPIBlock)completion;

6.  **QSTodoService.m**에서 `@end` 줄 앞에 `completeAll`의 해당 구현을 추가합니다. iOS는 임의 유형의 JSON 역직렬화를 지원하지 않는 점에서 JavaScript와 유사합니다. 따라서 iOS에는 `invokeAPI` 메서드로 구성된 사용자 지정 API를 호출할 수 있는 간단한 API도 있습니다.

        - (void) completeAll:(MSAPIBlock)completion
        {
            [self.client
             invokeAPI:@"completeall"
             body:nil
             HTTPMethod:@"POST"
             parameters:nil
             headers:nil
             completion:completion ];
        }

## <a name="test-app"></a>앱 테스트

1.  Xcode에서 배포 대상 에뮬레이터(iPhone 또는 iPad)를 선택하고 **Run** 단추(또는 **Command+R** 키)를 눌러 프로젝트를 다시 빌드하고 앱을 시작합니다. 그러면 모바일 서비스에서 항목을 쿼리하는, iOS SDK로 빌드한 Azure 모바일 서비스 클라이언트가 실행됩니다.

2.  텍스트 필드에 텍스트를 입력한 후 **+** 단추를 클릭합니다. 그러면 새 항목이 모바일 서비스에 삽입으로 전송됩니다.

3.  목록에 항목을 여러 개 추가할 때까지 이전 단계를 반복합니다.

4.  **All** 단추를 탭합니다. 완료 표시된 항목 수를 나타내는 경고 상자가 표시되고 필터링된 쿼리가 다시 실행되어 목록에서 모든 항목을 지웁니다.

    ![][0]

  [0]: ./media/mobile-services-ios-call-custom-api/mobile-custom-api-ios-completed.png
