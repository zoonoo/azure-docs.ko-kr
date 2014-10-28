사용자가 제출하는 데이터 길이의 유효성을 항상 검사하는 것이 좋습니다. 이 섹션에서는 모바일 서비스로 보낸 문자열 데이터 길이의 유효성을 검사하고 너무 긴 문자열(이 경우에는 10자보다 긴 문자열)을 거부하는 코드를 모바일 서비스에 추가합니다.

1.  **관리자 권한으로 실행** 옵션으로 Visual Studio를 시작하고, [시작][시작] 또는 [데이터 시작][데이터 시작] 자습서에서 작업한 모바일 서비스 프로젝트가 포함된 솔루션을 엽니다.

2.  솔루션 탐색기 창에서 todo 목록 서비스 프로젝트를 확장하고 **Contollers**를 확장합니다. 모바일 서비스 프로젝트의 일부인 TodoItemController.cs 파일을 엽니다.

    ![][]

3.  `PostTodoItem` 메서드를, 텍스트 문자열이 10자보다 크지 않은지 확인할 다음 메서드로 바꿉니다. 텍스트 길이가 10자보다 큰 항목의 경우 메서드에서 내용에 설명 메시지가 포함된 HTTP 상태 코드 400 잘못된 요청을 반환합니다.

        public async Task<IHttpActionResult> PostTodoItem(TodoItem item)
        {
            if (item.Text.Length > 10)
            {
                return BadRequest("The Item's Text length must not be greater than 10.");
            }
            else
            {
                TodoItem current = await InsertAsync(item);
                return CreatedAtRoute("Tables", new { id = current.Id }, current);
            } 
        }

4.  서비스 프로젝트를 마우스 오른쪽 단추로 클릭하고 **빌드**를 클릭하여 모바일 서비스 프로젝트를 빌드합니다. 오류가 발생하지 않았는지 확인합니다.

    ![][1]

5.  서비스 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시**를 클릭합니다. 이전에 [시작][시작] 또는 [데이터 시작][데이터 시작] 자습서에서 사용한 게시 설정으로 Microsoft Azure 계정에 모바일 서비스를 게시합니다.

    > [WACOM.NOTE] 또는 IIS Express에서 서비스를 로컬로 호스트하여 테스트할 수도 있습니다. 자세한 내용은 [데이터 시작][데이터 시작] 자습서를 참조하십시오.

    ![][2]

<!-- URLs. -->

  [시작]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started/
  [데이터 시작]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/
  []: ./media/mobile-services-dotnet-backend-add-validation/mobile-services-open-todoitemcontroller.png
  [1]: ./media/mobile-services-dotnet-backend-add-validation/mobile-services-build-dotnet-service.png
  [2]: ./media/mobile-services-dotnet-backend-add-validation/mobile-services-publish-dotnet-service.png
