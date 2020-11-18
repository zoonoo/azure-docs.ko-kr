---
title: '자습서: Blazor Server 채팅 앱 빌드 - Azure SignalR'
description: 이 자습서에서는 Azure SignalR Service를 사용하여 Blazor Server 앱을 빌드하고 수정하는 방법을 알아봅니다.
author: JialinXin
ms.service: signalr
ms.topic: tutorial
ms.date: 09/09/2020
ms.author: jixin
ms.openlocfilehash: 16fd15a5939cc6c268a80e88401f05042a206075
ms.sourcegitcommit: 5831eebdecaa68c3e006069b3a00f724bea0875a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2020
ms.locfileid: "94516818"
---
# <a name="tutorial-build-a-blazor-server-chat-app"></a>자습서: Blazor Server 채팅 앱 빌드

이 자습서에서는 Blazor Server 앱을 빌드하고 수정하는 방법을 보여 줍니다. 이 문서에서 배울 내용은 다음과 같습니다.

> [!div class="checklist"]
> * Blazor Server 앱을 사용하여 간단한 대화방을 빌드합니다.
> * Razor 구성 요소를 수정합니다.
> * 구성 요소에서 이벤트 처리 및 데이터 바인딩을 사용합니다.
> * Visual Studio에서 Azure App Service에 빠르게 배포합니다.
> * 로컬 SignalR을 Azure SignalR Service로 마이그레이션합니다.

## <a name="prerequisites"></a>사전 요구 사항
* [.NET Core 3.0 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.0)(버전 3.0.100 이상) 설치
* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/)(버전 16.3 이상) 설치
> Visual Studio 2019 미리 보기 버전도 작동하며, 최신 .Net Core 버전을 대상으로 하는 최신 Blazor Server 앱 템플릿과 함께 릴리스됩니다.

[문제가 있나요? 알려주세요.](https://aka.ms/asrs/qsblazor)

## <a name="build-a-local-chat-room-in-blazor-server-app"></a>Blazor Server 앱에서 로컬 대화방 빌드

Visual Studio 2019 버전 16.2.0부터 Azure SignalR Service는 기본 제공 웹앱 게시 프로세스이며, 웹앱과 SignalR Service 간의 종속성을 관리하는 것이 훨씬 더 편리합니다. 코드를 변경하지 않고 로컬 개발 환경의 로컬 SignalR 작업과 Azure App Service용 Azure SignalR Service 작업을 동시에 수행할 수 있습니다.

1. Blazor 채팅 앱 만들기
   
   Visual Studio에서 새 프로젝트 만들기 -> Blazor 앱 -> (앱 이름 지정 및 폴더 선택) -> Blazor Server 앱을 차례로 선택합니다. Visual Studio에서 대상 프레임워크를 올바르게 인식할 수 있도록 .NET Core SDK 3.0 이상을 이미 설치했는지 확인합니다.

   [ ![blazor-chat-create](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-create.png) ](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-create.png#lightbox)
   
   또는 cmd를 실행합니다.
   ```dotnetcli
   dotnet new blazorserver -o BlazorChat
   ```
   
1. 채팅을 위해 `Hub`를 구현하는 `BlazorChatSampleHub.cs` 파일을 추가합니다.
   
   ```cs
   using System;
   using System.Threading.Tasks;
   using Microsoft.AspNetCore.SignalR;
   
   namespace BlazorChat
   {
       public class BlazorChatSampleHub : Hub
       {
           public const string HubUrl = "/chat";
   
           public async Task Broadcast(string username, string message)
           {
               await Clients.All.SendAsync("Broadcast", username, message);
           }
   
           public override Task OnConnectedAsync()
           {
               Console.WriteLine($"{Context.ConnectionId} connected");
               return base.OnConnectedAsync();
           }
   
           public override async Task OnDisconnectedAsync(Exception e)
           {
               Console.WriteLine($"Disconnected {e?.Message} {Context.ConnectionId}");
               await base.OnDisconnectedAsync(e);
           }
       }
   }
   ```
   
1. 허브에 대한 엔드포인트를 `Startup.Configure()`에 추가합니다.
   
   ```cs
   app.UseEndpoints(endpoints =>
   {
       endpoints.MapBlazorHub();
       endpoints.MapFallbackToPage("/_Host");
       endpoints.MapHub<BlazorChatSampleHub>(BlazorChatSampleHub.HubUrl);
   });
   ```
   
1. SignalR 클라이언트를 사용할 수 있도록 `Microsoft.AspNetCore.SignalR.Client` 패키지를 설치합니다.

   ```dotnetcli
   dotnet add package Microsoft.AspNetCore.SignalR.Client --version 3.1.7
   ```

1. SignalR 클라이언트를 구현하는 `ChatRoom.razor`를 `Pages` 폴더 아래에 만듭니다. 아래 단계를 수행하거나 [ChatRoom.razor](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/BlazorChat/Pages/ChatRoom.razor)를 복사하기만 하면 됩니다.

   1. 페이지 링크와 참조를 추가합니다.
      
      ```razor
      @page "/chatroom"
      @inject NavigationManager navigationManager
      @using Microsoft.AspNetCore.SignalR.Client;
      ```

   1. 메시지를 보내고 받는 코드를 새 SignalR 클라이언트에 추가합니다.
      
      ```razor
      @code {
          // flag to indicate chat status
          private bool _isChatting = false;
          
          // name of the user who will be chatting
          private string _username;
      
          // on-screen message
          private string _message;
          
          // new message input
          private string _newMessage;
          
          // list of messages in chat
          private List<Message> _messages = new List<Message>();
          
          private string _hubUrl;
          private HubConnection _hubConnection;
      
          public async Task Chat()
          {
              // check username is valid
              if (string.IsNullOrWhiteSpace(_username))
              {
                  _message = "Please enter a name";
                  return;
              };
      
              try
              {
                  // Start chatting and force refresh UI.
                  _isChatting = true;
                  await Task.Delay(1);

                  // remove old messages if any
                  _messages.Clear();
         
                  // Create the chat client
                  string baseUrl = navigationManager.BaseUri;
      
                  _hubUrl = baseUrl.TrimEnd('/') + BlazorChatSampleHub.HubUrl;
      
                  _hubConnection = new HubConnectionBuilder()
                      .WithUrl(_hubUrl)
                      .Build();
      
                  _hubConnection.On<string, string>("Broadcast", BroadcastMessage);
      
                  await _hubConnection.StartAsync();
      
                  await SendAsync($"[Notice] {_username} joined chat room.");
              }
              catch (Exception e)
              {
                  _message = $"ERROR: Failed to start chat client: {e.Message}";
                  _isChatting = false;
              }
          }
      
          private void BroadcastMessage(string name, string message)
          {
              bool isMine = name.Equals(_username, StringComparison.OrdinalIgnoreCase);
      
              _messages.Add(new Message(name, message, isMine));
      
              // Inform blazor the UI needs updating
              StateHasChanged();
          }
      
          private async Task DisconnectAsync()
          {
              if (_isChatting)
              {
                  await SendAsync($"[Notice] {_username} left chat room.");
      
                  await _hubConnection.StopAsync();
                  await _hubConnection.DisposeAsync();
      
                  _hubConnection = null;
                  _isChatting = false;
              }
          }
      
          private async Task SendAsync(string message)
          {
              if (_isChatting && !string.IsNullOrWhiteSpace(message))
              {
                  await _hubConnection.SendAsync("Broadcast", _username, message);
      
                  _newMessage = string.Empty;
              }
          }
      
          private class Message
          {
              public Message(string username, string body, bool mine)
              {
                  Username = username;
                  Body = body;
                  Mine = mine;
              }
      
              public string Username { get; set; }
              public string Body { get; set; }
              public bool Mine { get; set; }
      
              public bool IsNotice => Body.StartsWith("[Notice]");
      
              public string CSS => Mine ? "sent" : "received";
          }
      }
      ```

   1. UI에서 SignalR 클라이언트와 상호 작용할 수 있도록 `@code` 앞에 렌더링 파트를 추가합니다.
      
      ```razor
      <h1>Blazor SignalR Chat Sample</h1>
      <hr />
      
      @if (!_isChatting)
      {
          <p>
              Enter your name to start chatting:
          </p>
      
          <input type="text" maxlength="32" @bind="@_username" />
          <button type="button" @onclick="@Chat"><span class="oi oi-chat" aria-hidden="true"></span> Chat!</button>
      
          // Error messages
          @if (_message != null)
          {
              <div class="invalid-feedback">@_message</div>
              <small id="emailHelp" class="form-text text-muted">@_message</small>
          }
      }
      else
      {
          // banner to show current user
          <div class="alert alert-secondary mt-4" role="alert">
              <span class="oi oi-person mr-2" aria-hidden="true"></span>
              <span>You are connected as <b>@_username</b></span>
              <button class="btn btn-sm btn-warning ml-md-auto" @onclick="@DisconnectAsync">Disconnect</button>
          </div>
          // display messages
          <div id="scrollbox">
              @foreach (var item in _messages)
              {
                  @if (item.IsNotice)
                  {
                      <div class="alert alert-info">@item.Body</div>
                  }
                  else
                  {
                      <div class="@item.CSS">
                          <div class="user">@item.Username</div>
                          <div class="msg">@item.Body</div>
                      </div>
                  }
              }
              <hr />
              <textarea class="input-lg" placeholder="enter your comment" @bind="@_newMessage"></textarea>
              <button class="btn btn-default" @onclick="@(() => SendAsync(_newMessage))">Send</button>
          </div>
      }
      ```

1. REST와 같이 대화방의 항목 메뉴를 `NavMenuCssClass` 아래에 삽입하도록 `NavMenu.razor`를 업데이트합니다.

   ```razor
   <li class="nav-item px-3">
       <NavLink class="nav-link" href="chatroom">
           <span class="oi oi-chat" aria-hidden="true"></span> Chat room
       </NavLink>
   </li>
   ```
   
1. 채팅 영역 거품형 보기에 맞게 최적화하도록 `site.css`를 업데이트합니다. 아래 코드를 끝 부분에 추가합니다.

   ```css
   /* improved for chat text box */
   textarea {
       border: 1px dashed #888;
       border-radius: 5px;
       width: 80%;
       overflow: auto;
       background: #f7f7f7
   }
   
   /* improved for speech bubbles */
   .received, .sent {
       position: relative;
       font-family: arial;
       font-size: 1.1em;
       border-radius: 10px;
       padding: 20px;
       margin-bottom: 20px;
   }
   
   .received:after, .sent:after {
       content: '';
       border: 20px solid transparent;
       position: absolute;
       margin-top: -30px;
   }
   
   .sent {
       background: #03a9f4;
       color: #fff;
       margin-left: 10%;
       top: 50%;
       text-align: right;
   }
   
   .received {
       background: #4CAF50;
       color: #fff;
       margin-left: 10px;
       margin-right: 10%;
   }
   
   .sent:after {
       border-left-color: #03a9f4;
       border-right: 0;
       right: -20px;
   }
   
   .received:after {
       border-right-color: #4CAF50;
       border-left: 0;
       left: -20px;
   }
   
   /* div within bubble for name */
   .user {
       font-size: 0.8em;
       font-weight: bold;
       color: #000;
   }
   
   .msg {
       /*display: inline;*/
   }
   ```

1. <kbd>F5</kbd> 키를 클릭하여 앱을 실행합니다. 아래와 같이 채팅할 수 있습니다.

   [ ![blazor-chat](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat.gif) ](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat.gif#lightbox)
   
[문제가 있나요? 알려주세요.](https://aka.ms/asrs/qsblazor)

## <a name="publish-to-azure"></a>Azure에 게시

   지금까지는 Blazor 앱에서 로컬 SignalR 작업을 수행했습니다. Azure App Service에 배포하는 경우 Blazor Server 앱을 많은 수의 동시 SignalR 연결로 확장할 수 있는 [Azure SignalR Service](/aspnet/core/signalr/scale?view=aspnetcore-3.1#azure-signalr-service)를 사용하는 것이 좋습니다. 또한 SignalR 서비스의 글로벌 및 고성능 데이터 센터는 지리적 위치로 인한 대기 시간을 줄이는 데 큰 도움이 됩니다.

> [!IMPORTANT]
> Blazor Server 앱에서 UI 상태는 서버 쪽에서 유지 관리되며, 이 경우 서버 고정이 필요합니다. 단일 앱 서버가 있는 경우 설계상 서버 고정(server sticky)이 보장됩니다. 그러나 여러 앱 서버가 있는 경우 클라이언트 협상 및 연결이 다른 서버로 이동하여 Blazor 앱에서 UI 오류가 발생할 수 있습니다. 따라서 `appsettings.json`에서 아래와 같이 서버 고정을 사용하도록 설정해야 합니다.
> ```json
> "Azure:SignalR:ServerStickyMode": "Required"
> ```

1. 마우스 오른쪽 단추로 프로젝트를 클릭하고, `Publish`로 이동합니다.

   * 대상: Azure
   * 특정 대상: 모든 유형의 **Azure App Service** 가 지원됩니다.
   * App Service: 새로 만들거나 기존 앱 서비스를 선택합니다.

   [ ![blazor-chat-profile](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-profile.gif) ](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-profile.gif#lightbox)

1. Azure SignalR Service 종속성을 추가합니다.

   게시 프로필이 만들어지면 **서비스 종속성** 아래에서 추천 메시지를 확인할 수 있습니다. **구성** 을 클릭하여 패널에서 새로 만들거나 기존 Azure SignalR Service를 선택합니다.

   [ ![blazor-chat-dependency](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-dependency.png) ](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-dependency.png#lightbox)

   서비스 종속성은 Azure에서 앱이 Azure SignalR Service로 자동으로 전환되도록 하기 위해 아래 작업을 수행합니다.

   * Azure SignalR Service를 사용하도록 [`HostingStartupAssembly`](/aspnet/core/fundamentals/host/platform-specific-configuration?view=aspnetcore-3.1)를 업데이트합니다.
   * Azure SignalR Service NuGet 패키지 참조를 추가합니다.
   * 종속성 설정을 저장하도록 프로필 속성을 업데이트합니다.
   * 비밀 저장소 구성은 사용자의 선택에 따라 달라집니다.
   * 앱에서 선택한 Azure SignalR Service를 대상으로 하는 `appsettings` 구성을 추가합니다.

   [ ![blazor-chat-dependency-summary](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-dependency-summary.png) ](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-dependency-summary.png#lightbox)

1. 앱 게시

   이제 게시할 준비가 되었습니다. 그리고 게시가 완료되면 페이지를 자동으로 찾습니다. 
   > [!NOTE]
   > Azure App Service 배포 시작 대기 시간으로 인해 처음 방문하는 페이지에서 즉시 작동하지 않을 수 있으며, 페이지를 새로 고쳐 약간의 지연을 제공합니다.
   > 또한 <kbd>F12</kbd> 키를 통해 브라우저 디버거 모드를 사용하여 트래픽이 이미 Azure SignalR Service로 리디렉션되었는지 확인할 수 있습니다.

   [ ![blazor-chat-azure](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-azure.png) ](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-azure.png#lightbox)
   
[문제가 있나요? 알려주세요.](https://aka.ms/asrs/qsblazor)

## <a name="further-topic-enable-azure-signalr-service-in-local-development"></a>추가 항목: 로컬 개발에서 Azure SignalR Service를 사용하도록 설정

1. 참조를 Azure SignalR SDK에 추가합니다.

   ```dotnetcli
   dotnet add package Microsoft.Azure.SignalR --version 1.5.1
   ```

1. `Startup.ConfigureServices()`에서 Azure SignalR Service에 대한 호출을 추가합니다.

   ```cs
   public void ConfigureServices(IServiceCollection services)
   {
       ...
       services.AddSignalR().AddAzureSignalR();
       ...
   }
   ```

1. `appsetting.json`에서 또는 [비밀 관리자](/aspnet/core/security/app-secrets?tabs=visual-studio&view=aspnetcore-3.1#secret-manager) 도구를 사용하여 `ConnectionString` Azure SignalR Service를 구성합니다.

> [!NOTE]
> 2단계는 SignalR SDK에 [`HostingStartupAssembly`](/aspnet/core/fundamentals/host/platform-specific-configuration?view=aspnetcore-3.1)를 사용하여 바꿀 수 있습니다.
> 
> 1. `appsetting.json`에서 Azure SignalR Service를 설정하는 구성을 추가합니다.
>    ```js
>    "Azure": {
>      "SignalR": {
>        "Enabled": true,
>        "ServerStickyMode": "Required",
>        "ConnectionString": <your-connection-string>
>      }
>    }
>    ```
> 
> 1. Azure SignalR SDK를 사용하도록 호스팅 시작 어셈블리를 할당합니다. `launchSettings.json`을 편집하고, 아래와 같은 구성을 `environmentVariables` 내에 추가합니다.
>    ```js
>    "environmentVariables": {
>        ...,
>        "ASPNETCORE_HOSTINGSTARTUPASSEMBLIES": "Microsoft.Azure.SignalR"
>      }
>    ```

[문제가 있나요? 알려주세요.](https://aka.ms/asrs/qsblazor)

## <a name="clean-up-resources"></a>리소스 정리

이 자습서에서 만든 리소스를 정리하려면 Azure Portal을 사용하여 해당 리소스 그룹을 삭제합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서 학습할 방법은 다음과 같습니다.

> [!div class="checklist"]
> * Blazor Server 앱을 사용하여 간단한 대화방을 빌드합니다.
> * Razor 구성 요소를 수정합니다.
> * 구성 요소에서 이벤트 처리 및 데이터 바인딩을 사용합니다.
> * Visual Studio에서 Azure App Service에 빠르게 배포합니다.
> * 로컬 SignalR을 Azure SignalR Service로 마이그레이션합니다.

고가용성에 대해 자세히 알아봅니다.
> [!div class="nextstepaction"]
> [복원력 및 재해 복구](signalr-concept-disaster-recovery.md)

## <a name="additional-resources"></a>추가 리소스

* [ASP.NET Core Blazor](/aspnet/core/blazor)
