<properties urlDisplayName="Optimistic concurrency" pageTitle="낙관적 동시성을 사용하여 데이터베이스 쓰기 충돌 처리(Windows 스토어) | 모바일 개발자 센터" metaKeywords="" description="서버와 Windows 스토어 응용 프로그램 둘 다에서 데이터베이스 쓰기 충돌을 처리하는 방법에 대해 알아봅니다." metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Handling database write conflicts" authors="wesmc" manager="dwrede" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="09/23/2014" ms.author="wesmc" />

# 데이터베이스 쓰기 충돌 처리

<div class="dev-center-tutorial-selector sublanding">
<a href="/ko-kr/develop/mobile/tutorials/handle-database-write-conflicts-dotnet/" title="Windows Store C#" class="current">Windows 스토어 C#</a>
<a href="/ko-kr/documentation/articles/mobile-services-windows-store-javascript-handle-database-conflicts/" title="Windows Store JavaScript">Windows 스토어 JavaScript</a>
<a href="/ko-kr/develop/mobile/tutorials/handle-database-write-conflicts-wp8/" title="Windows Phone">Windows Phone</a></div>	


이 자습서는 두 개 이상의 클라이언트가 Windows 스토어 앱에서 동일한 데이터베이스 레코드에 쓸 때 발생하는 충돌을 해결하는 방법을 더욱 잘 이해할 수 있도록 돕기 위한 것입니다. 일부 시나리오에서 두 개 이상의 클라이언트가 동시에 동일 항목의 변경 내용을 작성할 수 있습니다. 충돌 검색 없이, 마지막으로 쓴 내용이 원하는 결과가 아닌 경우에도 이전 업데이트를 덮어씁니다. Azure 모바일 서비스에서는 이러한 충돌을 검색하여 해결할 수 있습니다. 이 항목에서는 서버와 응용 프로그램 모두에서 데이터베이스 쓰기 충돌을 해결하는 단계를 단계별로 안내합니다.

이 자습서에서는 TodoItem 데이터베이스를 업데이트할 때 발생하는 경합을 처리하는 기능을 빠른 시작 앱에 추가합니다. 이 자습서에서는 다음 기본 단계를 단계별로 안내합니다.

1. [업데이트를 허용하도록 응용 프로그램 업데이트]
2. [응용 프로그램에서 충돌 검색 사용]
3. [응용 프로그램에서 데이터베이스 쓰기 충돌 테스트]
4. [서버 스크립트에서 자동으로 충돌 해결 처리]


이 자습서를 사용하려면 다음이 필요합니다.

+ Microsoft Visual Studio 2012 Express for Windows 이상
+ 이 자습서는 모바일 서비스 빠른 시작을 기반으로 합니다. 이 자습서를 시작하기 전에 먼저 [모바일 서비스 시작]을 완료해야 합니다. 
+ [Azure 계정]
+ Azure 모바일 서비스 NuGet 패키지 1.1.0 이상. 최신 버전을 얻으려면 다음 단계를 따르세요.
	1. Visual Studio에서 프로젝트를 열고 솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭한 후 **NuGet 패키지 관리**를 클릭합니다. 

		![][19]

	2. **온라인** 을 확장하고  **Microsoft 및 .NET**을 클릭합니다. 검색 텍스트 상자에 **Azure 모바일 서비스**를 입력합니다.  **Azure 모바일 서비스l** NuGet 패키지에서  **설치 ** 를 클릭합니다.

		![][20]


 

<h2><a name="uiupdate"></a>업데이트를 허용하도록 응용 프로그램 업데이트</h2>

이 섹션에서는 목록 상자 컨트롤에서 각 항목의 텍스트 업데이트를 허용하도록 TodoList 사용자 인터페이스를 업데이트합니다. 목록 상자는 데이터베이스 테이블의 각 항목에 대한 확인란 및 텍스트 상자 컨트롤을 포함합니다. TodoItem의 텍스트 필드를 업데이트할 수 있습니다. 응용 프로그램은 텍스트 상자에서 `LostFocus` 이벤트를 처리하여 데이터베이스의 항목을 업데이트합니다.


1. Visual Studio에서 [모바일 서비스 시작] 자습서에서 다운로드한 TodoList 프로젝트를 엽니다.
2. Visual Studio 솔루션 탐색기에서 MainPage.xaml을 열고 `ListView` 정의를 아래 표시된 `ListView`로 바꾸고 변경 내용을 저장합니다.

		<ListView Name="ListItems" Margin="62,10,0,0" Grid.Row="1">
			<ListView.ItemTemplate>
				<DataTemplate>
					<StackPanel Orientation="Horizontal">
						<CheckBox Name="CheckBoxComplete" IsChecked="{Binding Complete, Mode=TwoWay}" Checked="CheckBoxComplete_Checked" Margin="10,5" VerticalAlignment="Center"/>
						<TextBox x:Name="ToDoText" Height="25" Width="300" Margin="10" Text="{Binding Text, Mode=TwoWay}" AcceptsReturn="False" LostFocus="ToDoText_LostFocus"/>
					</StackPanel>
				</DataTemplate>
			</ListView.ItemTemplate>
		</ListView>


3. MainPage.xaml.cs에서 다음 `using` 지시문을 페이지 맨 위에 추가합니다.

		using System.Threading.Tasks;


4. Visual Studio 솔루션 탐색기에서 MainPage.xaml.cs를 열고, 아래와 같이 텍스트 상자 `LostFocus` 이벤트에 대한 MainPage에 이벤트 처리기를 추가합니다.


        private async void ToDoText_LostFocus(object sender, RoutedEventArgs e)
        {
            TextBox tb = (TextBox)sender;
            TodoItem item = tb.DataContext as TodoItem;
            //let's see if the text changed
            if (item.Text != tb.Text)
            {
                item.Text = tb.Text;
                await UpdateToDoItem(item);
            }
        }

4. MainPage.xaml.cs에서 아래와 같이 이벤트 처리기에서 참조되는 MainPage `UpdateToDoItem()` 메서드에 대한 정의를 추가합니다.

        private async Task UpdateToDoItem(TodoItem item)
        {
            Exception exception = null;			
            try
            {
                //update at the remote table
                await todoTable.UpdateAsync(item);
            }
            catch (Exception ex)
            {
                exception = ex;
            }			
            if (exception != null)
            {
                await new MessageDialog(exception.Message, "Update Failed").ShowAsync();
            }
        }

이제 응용 프로그램에서는 TextBox가 포커스를 잃게 되면 각 항목의 변경된 텍스트를 데이터베이스에 다시 씁니다.

<h2><a name="enableOC"></a>응용 프로그램에서 충돌 검색 사용</h2>

일부 시나리오에서 두 개 이상의 클라이언트가 동시에 동일 항목의 변경 내용을 작성할 수 있습니다. 충돌 검색 없이, 마지막으로 쓴 내용이 원하는 결과가 아닌 경우에도 이전 업데이트를 덮어씁니다. [낙관적 동시성 제어]에서는 각 트랜잭션이 커밋할 수 있으므로 리소스 잠금을 사용하지 않는다고 가정합니다. 트랜잭션을 커밋하기 전에 낙관적 동시성 제어는 다른 트랜잭션에서 데이터를 수정하지 않았음을 확인합니다. 데이터가 수정된 경우에는 커밋 중인 트랜잭션이 롤백됩니다. Azure 모바일 서비스는 각 테이블에 추가되는 `__version` 시스템 속성 열을 사용하여 각 항목의 변경을 추적하여 낙관적 동시성 제어를 지원합니다. 이 섹션에서는 응용 프로그램에서 `__version` 시스템 속성을 통해 이러한 쓰기 충돌을 검색할 수 있도록 설정합니다. 마지막 쿼리 이후에 레코드가 변경된 경우 업데이트 시도 중에 `MobileServicePreconditionFailedException`을 통해 응용 프로그램에 통지됩니다. 그러면 응용 프로그램에서 변경 내용을 데이터베이스에 커밋할지 데이터베이스에 대한 마지막 변경 내용을 그대로 적용할지 여부를 선택할 수 있습니다. 모바일 서비스의 시스템 속성에 대한 자세한 내용은 [시스템 속성]을 참조하세요.

1. MainPage.xaml.cs에서 쓰기 충돌 검색을 지원하도록 설정하는 **__version** 시스템 속성을 포함하도록 **TodoItem** 클래스 정의를 다음 코드로 업데이트합니다.

		public class TodoItem
		{
			public string Id { get; set; }			
			[JsonProperty(PropertyName = "text")]
			public string Text { get; set; }			
			[JsonProperty(PropertyName = "complete")]
			public bool Complete { get; set; }			
			[JsonProperty(PropertyName = "__version")]
			public string Version { set; get; }
		}

	<div class="dev-callout"><strong>참고</strong>
	<p>형식화되지 않은 테이블을 사용할 경우 테이블의 SystemProperties에 Version 플래그를 추가하여 낙관적 동시성을 사용하도록 설정합니다.</p>
	<pre><code>//Enable optimistic concurrency by retrieving __version
todoTable.SystemProperties |= MobileServiceSystemProperties.Version;
</code></pre>
	</div>


2. `TodoItem` 클래스에 `Version` 속성을 추가하여 마지막 쿼리 이후에 레코드가 변경된 경우 업데이트 중에 `MobileServicePreconditionFailedException` 예외를 발생하여 응용 프로그램에 통지합니다. 이 예외에는 서버 항목의 최신 버전이 포함되어 있습니다. MainPage.xaml.cs에서 `UpdateToDoItem()` 메서드에 예외를 처리하는 다음 코드를 추가합니다.

        private async Task UpdateToDoItem(TodoItem item)
        {
            Exception exception = null;			
            try
            {
                //update at the remote table
                await todoTable.UpdateAsync(item);
            }
            catch (MobileServicePreconditionFailedException<TodoItem> writeException)
            {
                exception = writeException;
            }
            catch (Exception ex)
            {
                exception = ex;
            }			
            if (exception != null)
            {
                if (exception is MobileServicePreconditionFailedException)
                {
                    //Conflict detected, the item has changed since the last query
                    //Resolve the conflict between the local and server item
                    await ResolveConflict(item, ((MobileServicePreconditionFailedException<TodoItem>) exception).Item);
                }
                else
                {
                    await new MessageDialog(exception.Message, "Update Failed").ShowAsync();
                }
            }
        }


3. MainPage.xaml.cs에서 `UpdateToDoItem()`에 참조되는 `ResolveConflict()` 메서드에 대한 정의를 추가합니다. 충돌을 해결하려면 사용자의 결정을 커밋하기 전에 로컬 항목의 버전을 서버의 업데이트된 버전으로 설정합니다. 그렇지 않으면 충돌이 계속 발생합니다.


        private async Task ResolveConflict(TodoItem localItem, TodoItem serverItem)
        {
            //Ask user to choose the resolution between versions
            MessageDialog msgDialog = new MessageDialog(String.Format("Server Text: \"{0}\" \nLocal Text: \"{1}\"\n", 
                                                        serverItem.Text, localItem.Text), 
                                                        "CONFLICT DETECTED - Select a resolution:");
            UICommand localBtn = new UICommand("Commit Local Text");
            UICommand ServerBtn = new UICommand("Leave Server Text");
            msgDialog.Commands.Add(localBtn);
            msgDialog.Commands.Add(ServerBtn);			
            localBtn.Invoked = async (IUICommand command) =>
            {
                // To resolve the conflict, update the version of the 
                // item being committed. Otherwise, you will keep
                // catching a MobileServicePreConditionFailedException.
                localItem.Version = serverItem.Version;				
                // Updating recursively here just in case another 
                // change happened while the user was making a decision
                await UpdateToDoItem(localItem);
            };			
            ServerBtn.Invoked = async (IUICommand command) =>
            {
				RefreshTodoItems();
            };			
            await msgDialog.ShowAsync();
        }



<h2><a name="test-app"></a>응용 프로그램에서 데이터베이스 쓰기 충돌 테스트</h2>

이 섹션에서는 Windows 스토어 앱 패키지를 빌드하여 두 번째 컴퓨터 또는 가상 컴퓨터에 앱을 설치합니다. 그런 다음 두 컴퓨터 모두에서 앱을 실행하고 쓰기 충돌을 생성하여 코드를 테스트합니다. 앱의 두 인스턴스에서 모두 동일한 항목의 `text` 속성을 업데이트하려고 시도하고, 사용자가 충돌을 해결해야 합니다.


1. Windows 스토어 앱 패키지를 만들어 두 번째 컴퓨터 또는 가상 컴퓨터에 설치합니다. 이렇게 하려면 Visual Studio에서 **프로젝트**->**스토어**->**앱 패키지 만들기**를 클릭합니다.

	![][0]

2. 이 패키지를 Windows 스토어에 업로드하지 않을 것이므로 패키지 만들기 화면에서 **아니요**를 클릭합니다. 그런 후 **다음**을 클릭합니다.

	![][1]

3. 패키지 선택 및 구성 화면에서 기본 설정을 적용하고 **만들기**를 클릭합니다.

	![][10]

4. 패키지 만들기 완료 화면에서 **출력 위치** 링크를 클릭하여 패키지 위치를 엽니다.

   	![][11]

5. 패키지 폴더 "todolist_1.0.0.0_AnyCPU_Debug_Test"를 두 번째 컴퓨터에 복사합니다. 아래와 같이 두 번째 컴퓨터에서 패키지 폴더를 열고 **Add-AppDevPackage.ps1** PowerShell 스크립트를 마우스 오른쪽 단추로 클릭하고 **PowerShell에서 실행**을 클릭합니다. 프롬프트에 따라 앱을 설치합니다.

	![][12]
  
5. **디버그**->**디버깅 시작**을 클릭하여 Visual Studio에서 앱의 인스턴스 1을 실행합니다. 두 번째 컴퓨터의 시작 화면에서 아래쪽 화살표를 클릭하여 "이름별 앱"을 표시합니다. 그런 다음 **todolist** 앱을 클릭하여 앱의 인스턴스 2를 실행합니다. 

	앱 인스턴스 1	
	![][2]

	앱 인스턴스 2	
	![][2]


6. 앱의 인스턴스 1에서 마지막 항목의 텍스트를 **테스트 쓰기 1**로 업데이트한 후 `LostFocus` 이벤트 처리기가 데이터베이스를 업데이트하도록 다른 텍스트 상자를 클릭합니다. 예제는 아래 스크린샷을 참조하세요.
	
	앱 인스턴스 1	
	![][3]

	앱 인스턴스 2	
	![][2]

7. 이때 앱 인스턴스 2의 해당 항목은 이전 버전입니다. 앱의 해당 인스턴스에서 **Text** 속성에 대해 `테스트 쓰기 2` 를 입력합니다. 그런 다음 `LostFocus` 이벤트 처리기가 데이터베이스를 이전 `_version` 속성으로 업데이트하도록 다른 텍스트 상자를 클릭합니다.

	앱 인스턴스 1	
	![][4]

	앱 인스턴스 2	
	![][5]

8. 업데이트 시도에 사용된 `__version` 값이 서버 `__version` 값과 일치하지 않으므로 모바일 서비스 SDK는 앱에서 이 충돌을 해결할 수 있도록 `MobileServicePreconditionFailedException`을 발생합니다. 충돌을 해결하려면 **로컬 텍스트 커밋**을 클릭하여 인스턴스 2의 값을 커밋할 수 있습니다. 인스턴스 2의 값을 취소하고 커밋된 앱 인스턴스 1의 값을 그대로 유지하려면 **서버 텍스트 유지**를 클릭합니다. 

	앱 인스턴스 1	
	![][4]

	앱 인스턴스 2	
	![][6]



<h2><a name="scriptsexample"></a>서버 스크립트에서 자동으로 충돌 해결 처리</h2>

서버 스크립트에서 쓰기 충돌을 검색하고 해결할 수 있습니다. 사용자 조작 대신 스크립팅된 논리를 사용하여 충돌을 해결할 수 있는 경우에 유용합니다. 이 섹션에서는 응용 프로그램의 TodoItem 테이블에 서버 쪽 스크립트를 추가합니다. 이 스크립트에서 충돌을 해결하는 데 사용하는 논리는 다음과 같습니다.

+  TodoItem의 `  complete` 필드가 true로 설정되어 있으면 완료된 것으로 간주되므로 `text`를 더 이상 변경할 수 없습니다.
+  TodoItem의 `  complete` 필드가 계속 false이면 `text` 업데이트 시도가 커밋됩니다.

다음 단계에서는 서버 업데이트 스크립트를 추가하여 테스트하는 과정을 안내합니다.

1. [Azure 관리 포털]에 로그인하여 **모바일 서비스**를 클릭한 후 앱을 클릭합니다. 

   	![][7]

2. **데이터** 탭을 클릭한 후 **TodoItem** 테이블을 클릭합니다.

   	![][8]

3. **스크립트**를 클릭한 후 **업데이트** 작업을 선택합니다.

   	![][9]

4. 기존 스크립트를 다음 함수로 바꾼 후 **저장**을 클릭합니다.

		function update(item, user, request) { 
			request.execute({ 
				conflict: function (serverRecord) {
					// Only committing changes if the item is not completed.
					if (serverRecord.complete === false) {
						//write the updated item to the table
						request.execute();
					}
					else
					{
						request.respond(statusCodes.FORBIDDEN, 'The item is already completed.');
					}
				}
			}); 
		}   
5. 두 컴퓨터 모두에서 **todolist** 앱을 실행합니다. 인스턴스 2의 마지막 항목에 대해 TodoItem `text`를 변경합니다. 그런 다음 `LostFocus` 이벤트 처리기에서 데이터베이스를 업데이트하도록 다른 텍스트 상자를 클릭합니다.

	앱 인스턴스 1	
	![][4]

	앱 인스턴스 2	
	![][5]

6. 앱 인스턴스 1에서 마지막 text 속성에 대해 다른 값을 입력합니다. 그런 다음 `LostFocus` 이벤트 처리기가 데이터베이스를 잘못된 `__version` 속성으로 업데이트하도록 다른 텍스트 상자를 클릭합니다.

	앱 인스턴스 1	
	![][13]

	앱 인스턴스 2	
	![][14]

7. 항목이 완료로 표시되어 있지 않으므로 서버 스크립트에서 업데이트를 허용하여 충돌을 해결했기 때문에 예외가 발생하지 않았습니다. 업데이트가 실제로 성공했는지 확인하려면 인스턴스 2에서 **새로 고침**을 클릭하여 데이터베이스를 다시 쿼리합니다.

	앱 인스턴스 1	
	![][15]

	앱 인스턴스 2	
	![][15]

8. 인스턴스 1에서 확인란을 클릭하여 마지막 Todo 항목을 완료합니다.

	앱 인스턴스 1	
	![][16]

	앱 인스턴스 2	
	![][15]

9. 인스턴스 2에서 마지막 TodoItem 텍스트를 업데이트하고 `LostFocus` 이벤트를 트리거합니다. 충돌에 대한 응답으로 스크립트는 항목이 이미 완료되었으므로 업데이트를 거부하여 충돌을 해결했습니다. 

	앱 인스턴스 1	
	![][17]

	앱 인스턴스 2	
	![][18]

## <a name="next-steps"> </a>다음 단계

이 자습서에서는 모바일 서비스에서 데이터 작업 시 발생하는 쓰기 충돌을 처리하도록 Windows 스토어 앱을 설정하는 방법을 알아보았습니다. 이제 다음 자습서 중 하나에서 데이터 시리즈를 완료하세요.

* [스크립트를 사용하여 데이터 유효성 검사 및 수정]
  <br/>모바일 서비스에서 서버 스크립트를 사용하여 앱에서 전송된 데이터의 유효성을 검사하고 데이터를 변경하는 방법에 대해 알아봅니다.

* [페이징을 사용하여 쿼리 구체화]
  <br/>쿼리에 페이징을 사용하여 단일 요청으로 처리되는 데이터 양을 제어하는 방법을 알아봅니다.

데이터 시리즈를 완료한 후에는 다음 Windows 스토어 자습서 중 하나를 시도해 볼 수도 있습니다.

* [인증 시작] 
  <br/>앱 사용자를 인증하는 방법을 알아봅니다.

* [푸시 알림 시작] 
  <br/>모바일 서비스를 사용하여 앱에 매우 기본적인 푸시 알림을 보내는 방법을 알아봅니다.
 
<!-- Anchors. -->
[업데이트를 허용하도록 응용 프로그램 업데이트]: #uiupdate
[응용 프로그램에서 충돌 검색 사용]: #enableOC
[응용 프로그램에서 데이터베이스 쓰기 충돌 테스트]: #test-app
[서버 스크립트에서 자동으로 충돌 해결 처리]: #scriptsexample
[다음 단계]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-create-app-package1.png
[1]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-create-app-package2.png
[2]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-app1.png 
[3]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-app1-write1.png
[4]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-app1-write2.png
[5]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-app2-write2.png
[6]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-app2-write2-conflict.png
[7]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/mobile-services-selection.png
[8]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/mobile-portal-data-tables.png
[9]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/mobile-insert-script-users.png
[10]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-create-app-package3.png
[11]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-create-app-package4.png
[12]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-install-app-package.png
[13]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-app1-write3.png
[14]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-app2-write3.png
[15]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-write3.png
[16]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-checkbox.png
[17]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-2-items.png
[18]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-already-complete.png
[19]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/mobile-manage-nuget-packages-VS.png
[20]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/mobile-manage-nuget-packages-dialog.png

<!-- URLs. -->
[낙관적 동시성 제어]: http://go.microsoft.com/fwlink/?LinkId=330935
[모바일 서비스 시작]: /ko-kr/develop/mobile/tutorials/get-started/#create-new-service
[Azure 계정]: http://www.windowsazure.com/ko-kr/pricing/free-trial/
[스크립트를 사용하여 데이터 유효성 검사 및 수정]: /ko-kr/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet
[페이징을 사용하여 쿼리 구체화]: /ko-kr/develop/mobile/tutorials/add-paging-to-data-dotnet
[모바일 서비스 시작]: /ko-kr/develop/mobile/tutorials/get-started
[데이터 작업 시작]: /ko-kr/develop/mobile/tutorials/get-started-with-data-dotnet
[인증 시작]: /ko-kr/develop/mobile/tutorials/get-started-with-users-dotnet
[푸시 알림 시작]: /ko-kr/develop/mobile/tutorials/get-started-with-push-dotnet

[Azure 관리 포털]: https://manage.windowsazure.com/
[관리 포털]: https://manage.windowsazure.com/
[Windows Phone 8 SDK]: http://go.microsoft.com/fwlink/p/?LinkID=268374
[모바일 서비스 SDK]: http://go.microsoft.com/fwlink/p/?LinkID=268375
[개발자 코드 샘플 사이트]:  http://go.microsoft.com/fwlink/p/?LinkId=271146
[시스템 속성]: http://go.microsoft.com/fwlink/?LinkId=331143
