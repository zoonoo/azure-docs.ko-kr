<ol>

1.  Visual Studio 솔루션 탐색기에서 모바일 서비스 프로젝트의 **컨트롤러** 폴더를 확장합니다. TodoItemController.cs를 열고 다음 코드로 `PostTodoItem` 메서드 정의를 업데이트합니다.

         public async Task<IHttpActionResult> PostTodoItem(TodoItem item)
         {
             TodoItem current = await InsertAsync(item);
             WindowsPushMessage message = new WindowsPushMessage();
             message.XmlPayload = @"<?xml version=""1.0"" encoding=""utf-8""?>" +
                                  @"<toast><visual><binding template=""ToastText01"">" +
                                  @"<text id=""1"">" + item.Text + @"</text>" +
                                  @"</binding></visual></toast>";
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


