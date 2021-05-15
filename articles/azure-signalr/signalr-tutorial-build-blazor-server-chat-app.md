---
title: '자습서: Blazor Server 채팅 앱 빌드 - Azure SignalR'
description: 이 자습서에서는 Azure SignalR Service를 사용하여 Blazor Server 앱을 빌드하고 수정하는 방법을 알아봅니다.
author: JialinXin
ms.service: signalr
ms.topic: tutorial
ms.date: 09/09/2020
ms.author: jixin
ms.openlocfilehash: 5ed5107cadcfbf247b79c18a6a2e391ab3043565
ms.sourcegitcommit: dd425ae91675b7db264288f899cff6add31e9f69
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/01/2021
ms.locfileid: "108331829"
---
# <a name="tutorial-build-a-blazor-server-chat-app"></a>자습서: Blazor Server 채팅 앱 빌드

이 자습서에서는 Blazor Server 앱을 빌드하고 수정하는 방법을 보여 줍니다. 이 문서에서 배울 내용은 다음과 같습니다.
> [!div class="checklist"] 
> * Blazor Server 앱 템플릿을 사용하여 간단한 대화방을 빌드합니다.
> * Razor 구성 요소를 사용합니다.
> * Razor 구성 요소에서 이벤트 처리 및 데이터 바인딩을 사용합니다.
> * Visual Studio에서 Azure App Service에 빠르게 배포합니다.
> * 로컬 SignalR에서 Azure SignalR Service로 마이그레이션합니다.

## <a name="prerequisites"></a>사전 요구 사항

* [.NET Core 3.0 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.0)(버전 3.0.100 이상) 설치
* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/)(버전 16.3 이상) 설치


[문제가 있나요? 알려주세요.](https://aka.ms/asrs/qsblazor)

## <a name="build-a-local-chat-room-in-blazor-server-app"></a>Blazor Server 앱에서 로컬 대화방 빌드

Visual Studio 2019 버전 16.2.0부터 Azure SignalR Service가 웹 애플리케이션 게시 프로세스에 기본 제공되므로 웹앱과 SignalR 서비스 간의 종속성을 훨씬 더 편리하게 관리할 수 있습니다. 코드를 변경하지 않고 로컬 개발 환경의 로컬 SignalR 인스턴스 작업과 Azure App Service용 Azure SignalR Service 작업을 동시에 수행할 수 있습니다.

1. Blazor 채팅 앱을 만듭니다.
   1. Visual Studio에서 **새 프로젝트 만들기** 를 선택합니다. 
   1. **Blazor 앱** 을 선택합니다. 
   1. 애플리케이션의 이름을 지정하고 폴더를 선택합니다. 
   1. **Blazor 서버 앱** 템플릿을 선택합니다.
    
       > [!NOTE]
       > Visual Studio에서 대상 프레임워크를 올바르게 인식할 수 있도록 .NET Core SDK 3.0 이상을 이미 설치했는지 확인합니다.
   
       [ ![새 프로젝트 만들기에서 Blazor 앱 템플릿을 선택합니다.](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-create.png) ](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-create.png#lightbox)
   
   5. .NET CLI에서 [`dotnet new`](/dotnet/core/tools/dotnet-new) 명령을 실행하여 프로젝트를 만들 수도 있습니다.
   
       ```dotnetcli
       dotnet new blazorserver -o BlazorChat
       ```
   
1. `BlazorChatSampleHub.cs`라는 새 C# 파일을 추가하고 채팅 앱의 `Hub` 클래스에서 파생되는 새 클래스 `BlazorSampleHub`를 만듭니다. 허브를 만드는 방법에 대한 자세한 내용은 [허브 만들기 및 사용](/aspnet/core/signalr/hubs#create-and-use-hubs)을 참조하세요. 
   
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
   
1. 허브에 대한 엔드포인트를 `Startup.Configure()` 메서드에 추가합니다.
   
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

1. SignalR 클라이언트를 구현할 `Pages` 폴더 아래에 `ChatRoom.razor`라는 새 [Razor 구성 요소](/aspnet/core/blazor/components/)를 만듭니다. 아래 단계를 수행하거나 [ChatRoom.razor](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/BlazorChat/Pages/ChatRoom.razor) 파일을 사용합니다.

   1. [`@page`](/aspnet/core/mvc/views/razor#page) 지시문 및 using 문을 추가합니다. [`@inject`](/aspnet/core/mvc/views/razor#inject) 지시문을 사용하여 [`NavigationManager`](/aspnet/core/blazor/fundamentals/routing#uri-and-navigation-state-helpers) 서비스를 삽입합니다.
      
      ```razor
      @page "/chatroom"
      @inject NavigationManager navigationManager
      @using Microsoft.AspNetCore.SignalR.Client;
      ```

   1. `@code` 섹션에서 다음 구성원을 새 SignalR 클라이언트에 추가하여 메시지를 보내고 받습니다.
      
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

   1. `@code` 섹션 앞에 SignalR 클라이언트와 상호 작용하는 UI 태그를 추가합니다.
      
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

1. `NavMenu.razor` 구성 요소를 업데이트하여 `NavLink`의 대화방에 연결할 새 `NavMenuCssClass` 구성 요소를 삽입합니다.

   ```razor
   <li class="nav-item px-3">
       <NavLink class="nav-link" href="chatroom">
           <span class="oi oi-chat" aria-hidden="true"></span> Chat room
       </NavLink>
   </li>
   ```
   
1. `site.css` 파일에 몇 가지 CSS 클래스를 추가하여 채팅 페이지의 UI 요소에 스타일을 지정합니다.

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

1. <kbd>F5</kbd> 키를 눌러 앱을 실행합니다. 이제 채팅을 시작할 수 있습니다.

   [ ![Bob과 Alice 간의 애니메이션 채팅이 표시됩니다. Alice는 Hello라고 하고, Bob은 Hi라고 합니다.](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat.gif) ](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat.gif#lightbox)
   
[문제가 있나요? 알려주세요.](https://aka.ms/asrs/qsblazor)

## <a name="publish-to-azure"></a>Azure에 게시

Azure App Service에 Blazor 앱을 배포하는 경우 [Azure SignalR Service](/aspnet/core/signalr/scale#azure-signalr-service)를 사용하는 것이 좋습니다. Azure SingalR Service를 사용하면 Blazor Server 앱을 다수의 동시 SignalR 연결로 확장할 수 있습니다. 또한 SignalR 서비스의 글로벌 및 고성능 데이터 센터는 지리적 위치로 인한 대기 시간을 줄이는 데 큰 도움이 됩니다.

> [!IMPORTANT]
> Blazor Server 앱에서 UI 상태는 서버 쪽에서 유지됩니다. 즉, 상태를 유지하려면 고정 서버 세션이 필요합니다. 단일 앱 서버가 있는 경우 고정 세션이 기본적으로 보장됩니다. 그러나 여러 애플리케이션 서버가 있는 경우 클라이언트 협상 및 연결이 서로 다른 서버로 이동할 수 있으며, 이로 인해 Blazor 앱에서 일관되지 않은 UI 상태 관리가 발생할 수 있습니다. 따라서 아래 *appsettings.js* 에 표시된 것처럼 고정 서버 세션을 사용하도록 설정하는 것이 좋습니다.
>
> ```json
> "Azure:SignalR:ServerStickyMode": "Required"
> ```

1. 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시** 로 이동합니다. 다음 설정을 사용합니다.
   * **대상**: Azure
   * **특정 대상**: 모든 유형의 **Azure App Service** 가 지원됩니다.
   * **App Service**: App Service 인스턴스를 만들거나 선택합니다.

   [ ![애니메이션은 Azure를 대상으로 선택한 다음, 특정 대상으로 Azure App Serice를 선택하는 방법을 보여 줍니다.](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-profile.gif) ](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-profile.gif#lightbox)

1. Azure SignalR Service 종속성을 추가합니다.

   게시 프로필을 만든 후 **서비스 종속성** 아래에서 Azure SignalR 서비스를 추가하라는 권장 사항 메시지를 볼 수 있습니다. **구성** 을 선택하여 해당 창에서 Azure SignalR Service를 새로 만들거나 기존 항목을 선택합니다.

   [ ![게시 시 구성 링크가 강조 표시됩니다.](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-dependency.png) ](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-dependency.png#lightbox)

   서비스 종속성은 Azure에서 앱이 Azure SignalR Service로 자동으로 전환되도록 하기 위해 아래 작업을 수행합니다.

   * Azure SignalR Service를 사용하도록 [`HostingStartupAssembly`](/aspnet/core/fundamentals/host/platform-specific-configuration)를 업데이트합니다.
   * Azure SignalR Service NuGet 패키지 참조를 추가합니다.
   * 종속성 설정을 저장하도록 프로필 속성을 업데이트합니다.
   * 선택한 대로 비밀 저장소를 구성합니다.
   * *appsettings.json* 에 구성을 추가하여 앱이 Azure SignalR Service를 대상으로 하도록 지정합니다.

   [ ![변경 내용 요약에서 확인란은 모든 종속성을 선택하는 데 사용됩니다.](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-dependency-summary.png) ](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-dependency-summary.png#lightbox)

1. 앱을 게시합니다.

   이제 앱을 게시할 준비가 되었습니다. 게시 프로세스가 완료되면 앱이 브라우저에서 자동으로 시작됩니다.
 
   > [!NOTE]
   > Azure App Service 배포 시작 대기 시간으로 인해 앱을 시작하는 데 어느 정도 시간이 필요할 수 있습니다. 브라우저 디버거 도구(일반적으로 <kbd>F12</kbd> 키를 눌러 실행)를 사용하여 트래픽이 Azure SignalR Service로 리디렉션되었는지 확인할 수 있습니다.

   [ ![Blazor SignalR Chat 샘플에는 사용자 이름의 텍스트 상자와 채팅을 시작하기 위한 Chat! 단추가 있습니다.](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-azure.png) ](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-azure.png#lightbox)
   
[문제가 있나요? 알려주세요.](https://aka.ms/asrs/qsblazor)

## <a name="enable-azure-signalr-service-for-local-development"></a>로컬 개발에 Azure SignalR Service를 사용하도록 설정

1. 다음 명령을 사용하여 Azure SignalR SDK에 대한 참조를 추가합니다.

   ```dotnetcli
   dotnet add package Microsoft.Azure.SignalR --version 1.5.1
   ```

1. 아래에 설명된 대로, `Startup.ConfigureServices()`에 `AddAzureSingalR()`에 대한 호출을 추가합니다.

   ```cs
   public void ConfigureServices(IServiceCollection services)
   {
       ...
       services.AddSignalR().AddAzureSignalR();
       ...
   }
   ```

1. *appsettings.json* 에서 또는 [비밀 관리자](/aspnet/core/security/app-secrets?tabs=visual-studio#secret-manager) 도구를 사용하여 Azure SignalR Service 연결 문자열을 구성합니다.

> [!NOTE]
> 2단계는 SignalR SDK를 사용하도록 [호스팅 시작 어셈블리](/aspnet/core/fundamentals/host/platform-specific-configuration)를 구성하는 것으로 바뀔 수 있습니다.
>
> 1. *appsettings.json* 에서 Azure SignalR Service를 설정하는 구성을 추가합니다.
>
>     ```json
>     "Azure": {
>       "SignalR": {
>         "Enabled": true,
>         "ConnectionString": <your-connection-string>       
>       }
>     }
>    
>    ```
>
> 1. Azure SignalR SDK를 사용하도록 호스팅 시작 어셈블리를 구성합니다. *launchSettings.json* 을 편집하고 `environmentVariables` 내부에 다음 예제와 같은 구성을 추가합니다.
>
>     ```json
>    "environmentVariables": {
>         ...,
>        "ASPNETCORE_HOSTINGSTARTUPASSEMBLIES": "Microsoft.Azure.SignalR"
>      }
>  
>     ```
>

[문제가 있나요? 알려주세요.](https://aka.ms/asrs/qsblazor)

## <a name="clean-up-resources"></a>리소스 정리

이 자습서에서 만든 리소스를 정리하려면 Azure Portal을 사용하여 해당 리소스 그룹을 삭제합니다.

## <a name="additional-resources"></a>추가 리소스

* [ASP.NET Core Blazor](/aspnet/core/blazor)

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 작업 방법을 알아보았습니다.

> [!div class="checklist"]
> * Blazor Server 앱 템플릿을 사용하여 간단한 대화방을 빌드합니다.
> * Razor 구성 요소를 사용합니다.
> * Razor 구성 요소에서 이벤트 처리 및 데이터 바인딩을 사용합니다.
> * Visual Studio에서 Azure App Service에 빠르게 배포합니다.
> * 로컬 SignalR에서 Azure SignalR Service로 마이그레이션합니다.

고가용성에 대해 자세히 알아봅니다.
> [!div class="nextstepaction"]
> [복원력 및 재해 복구](signalr-concept-disaster-recovery.md)
