1. Visual Studio **솔루션 탐색기**에서 모바일 백 엔드 프로젝트의 **Controllers** 폴더를 확장합니다. **TodoItemController.cs**를 엽니다. 파일 맨 위에 다음 `using` 문을 추가합니다.

        using System.Collections.Generic;        
        using Microsoft.Azure.Mobile.Server.Notifications;


2. `PostTodoItem` 메서드를 다음 코드로 바꿉니다.
        
        public async Task<IHttpActionResult> PostTodoItem(TodoItem item)
        {
            TodoItem current = await InsertAsync(item);

            Dictionary<string, string> data = new Dictionary<string, string>()
            {
                { "message", item.Text}
            };

            HttpConfiguration config = this.Configuration;

            GooglePushMessage message = new GooglePushMessage(data, System.TimeSpan.FromHours(1));

            try
            {
                var client = new PushClient(config);
                var result = await client.SendAsync(message);

                ServiceSettingsDictionary settings = config.GetServiceSettingsProvider().GetServiceSettings();
                config.Services.GetTraceWriter().Info(result.State.ToString());
            }
            catch (System.Exception ex)
            {
                config.Services.GetTraceWriter().Error(ex.Message, null, "Push.SendAsync Error");
            }

            return CreatedAtRoute("Tables", new { id = current.Id }, current);
        }

<!---HONumber=Oct15_HO3-->