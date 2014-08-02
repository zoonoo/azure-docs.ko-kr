

사용자 지정 API를 호출하도록 앱 업데이트
----------------------------------------

1.  Visual Studio에서 퀵 스타트 프로젝트의 MainPage.xaml 파일을 열고 이름이 `ButtonRefresh`인 **Button** 요소를 찾아 다음 XAML 코드로 바꿉니다.

         <StackPanel Orientation="Horizontal">
             <Button Margin="72,0,0,0" Name="ButtonRefresh" 
                     Click="ButtonRefresh_Click">Refresh</Button>
             <Button Margin="12,0,0,0" Name="ButtonCompleteAll" 
                     Click="ButtonCompleteAll_Click">Complete All</Button>
         </StackPanel>

    이 코드는 페이지에 새 단추를 추가합니다.

2.  MainPage.xaml.cs 코드 파일을 열어 다음 클래스 정의 코드를 추가합니다.

         public class MarkAllResult
         {
             public int Count { get; set; }
         }

    이 클래스는 사용자 지정 API에서 반환한 행 개수 값을 유지하는 데 사용됩니다.

3.  **MainPage** 클래스에서 **RefreshTodoItems** 메서드를 찾아 `query`가 다음 **Where** 메서드를 사용하여 정의되었는지 확인합니다.

         .Where(todoItem => todoItem.Complete == false)

    이 코드는 완료된 항목이 쿼리에서 반환되지 않도록 항목을 필터링합니다.

4.  **MainPage** 클래스에 다음 메서드를 추가합니다.

         private async void ButtonCompleteAll_Click(object sender, RoutedEventArgs e)
         {
             string message;
             try
             {
                 // Asynchronously call the custom API using the POST method. 
                 var result = await App.MobileService
                     .InvokeApiAsync<MarkAllResult>("completeAll", 
                     System.Net.Http.HttpMethod.Post, null);
                 message =  result.Count + " item(s) marked as complete.";
                 RefreshTodoItems();
             }
             catch (MobileServiceInvalidOperationException ex)
             {
                 message = ex.Message;                
             }
            
             var dialog = new MessageDialog(message);
             dialog.Commands.Add(new UICommand("OK"));
             await dialog.ShowAsync();
         }

    이 메서드는 새 단추의 **Click** 이벤트를 처리합니다. [InvokeApiAsync](http://msdn.microsoft.com/en-us/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceclient.invokeapiasync.aspx)(영문) 메서드가 클라이언트에서 호출되어 POST 요청을 새 사용자 지정 API로 보냅니다. 사용자 지정 API에서 반환하는 결과는 오류와 마찬가지로 메시지 대화 상자에 표시됩니다.

앱 테스트
---------

1.  Visual Studio에서 **F5** 키를 눌러 프로젝트를 다시 빌드하고 앱을 시작합니다.

2.  앱에서 **Insert a TodoItem**에 일부 텍스트를 입력하고 **Save**를 클릭합니다.

3.  목록에 todo 항목을 여러 개 추가할 때까지 이전 단계를 반복합니다.

4.  **Complete All** 단추를 클릭합니다.

	![](./media/mobile-services-windows-store-dotnet-call-custom-api/mobile-custom-api-windows-store-completed.png)

    완료 표시된 항목 수를 나타내는 메시지 대화 상자가 표시되고 필터링된 쿼리가 다시 실행되어 목록에서 모든 항목을 지웁니다.
