
1. Visual Studio 솔루션 탐색기에서 모바일 서비스 프로젝트의 **컨트롤러** 폴더를 확장합니다. TodoItemController.cs를 엽니다. 파일 맨 위에 다음 `using` 문을 추가합니다.

		using System;
		using System.Collections.Generic;

2. `PostTodoItem` 메서드 정의를 다음 코드로 업데이트합니다.

        public async Task<IHttpActionResult> PostTodoItem(TodoItem item)
        {
            TodoItem current = await InsertAsync(item);

            Dictionary<string, string> data = new Dictionary<string, string>()
            {
                { "message", item.Text}
            };
            GooglePushMessage message = new GooglePushMessage(data, TimeSpan.FromHours(1));

            try
            {
                var result = await Services.Push.SendAsync(message);
                Services.Log.Info(result.State.ToString());
            }
            catch (System.Exception ex)
            {
                Services.Log.Error(ex.Message, null, "Push.SendAsync Error");
            }
            return CreatedAtRoute("Tables", new { id = current.Id }, current);
        }

    이 코드는 할 일 항목을 삽입한 후 삽입한 항목의 텍스트가 포함된 푸시 알림을 보냅니다. 오류 이벤트에서 코드는 관리 포털에서 모바일 서비스의 **로그** 탭에서 볼 수 있는 오류 로그 항목을 추가합니다.

3. Azure에 모바일 서비스 프로젝트를 다시 게시합니다.

<!---HONumber=July15_HO4-->