---
title: "Xamarin.Forms 앱에서 Azure 저장소에 연결"
description: "Azure Blob 저장소에 연결하여 할 일 목록 Xamarin.Forms 모바일 앱에 이미지를 추가합니다."
documentationcenter: xamarin
author: adrianhall
manager: erikre
editor: 
services: app-service\mobile
ms.assetid: bb1a1437-0a31-46bb-9237-1b692b0ede21
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 10/01/2016
ms.author: adrianha
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 9dea1f48904a0d505f95636f178b24f8a6e174a7


---
# <a name="connect-to-azure-storage-in-your-xamarinforms-app"></a>Xamarin.Forms 앱에서 Azure 저장소에 연결
## <a name="overview"></a>개요
Azure 모바일 앱 클라이언트 및 서버 SDK는 /tables 끝점에 대해 CRUD 작업을 사용하여 구조화된 데이터의 오프라인 동기화를 지원합니다. 일반적으로 이 데이터는 데이터베이스 또는 유사한 저장소에 저장되고 이러한 데이터 저장소는 큰 이진 데이터를 효율적으로 저장할 수 없습니다. 또한 일부 응용 프로그램에는 다른 곳(예: blob 저장소, 파일 공유)에 저장된 관련 데이터가 있고 /tables 끝점의 레코드와 다른 데이터 간의 연결을 만들 수 있도록 하는 데 유용합니다.

이 항목에서는 모바일 앱 할 일 목록 빠른 시작에 이미지에 대한 지원을 추가하는 방법을 보여줍니다. [Xamarin.Forms 앱 만들기]자습서를 먼저 완료해야 합니다.

이 자습서에서는 저장소 계정을 만들고 모바일 앱 백 엔드에 연결 문자열을 추가합니다. 그런 다음 새 모바일 앱 형식 `StorageController<T>` 에서 새 서버 프로젝트로 새 상속을 추가합니다.

> [!TIP]
> 이 자습서에는 사용 가능한 [동반 샘플](https://azure.microsoft.com/documentation/samples/app-service-mobile-dotnet-todo-list-files/) 이 있고 이는 Azure 계정에 배포될 수 있습니다. 
> 
> 

## <a name="prerequisites"></a>필수 조건
* 다른 필수 조건을 나열하는 [Xamarin.Forms 앱 만들기] 자습서를 완료합니다. 이 문서에서는 해당 자습서에서 완성된 앱을 사용합니다.

> [!NOTE]
> Azure 계정을 등록하기 전에 Azure 앱 서비스를 시작하려는 경우 [앱 서비스 체험](https://tryappservice.azure.com/?appServiceName=mobile)으로 이동하세요. 여기서 신용 카드와 약정 없이 앱 서비스에서 수명이 짧은 스타터 모바일 앱을 즉시 만들 수 있습니다.
> 
> 

## <a name="create-a-storage-account"></a>저장소 계정 만들기
1. [Azure 저장소 계정 만들기]자습서에 따라 저장소 계정을 만듭니다. 
2. Azure 포털에서 새로 만든 저장소 계정으로 이동하고 **키** 아이콘을 클릭합니다. **기본 연결 문자열**을 복사합니다.
3. 모바일 앱 백 엔드로 이동합니다. **모든 설정** -> **응용 프로그램 설정** -> **연결 문자열**에서 `MS_AzureStorageAccountConnectionString`라는 새 키를 만들고 저장소 계정에서 복사한 값을 사용합니다. **사용자 지정** 을 키 형식으로 사용합니다.

## <a name="add-a-storage-controller-to-the-server"></a>서버에 저장소 컨트롤러를 추가합니다.
Azure 저장소의 SAS 토큰에 대한 요청에 응답하는 서버 프로젝트에 새 컨트롤러를 추가하고 레코드에 해당하는 파일의 목록을 반환해야 합니다.

* [서버 프로젝트에 저장소 컨트롤러를 추가합니다.](#add-controller-code)
* [저장소 컨트롤러에서 등록된 경로](#routes-registered)
* [클라이언트 및 서버 통신](#client-communication)

### <a name="a-nameadd-controller-codeaadd-a-storage-controller-to-your-server-project"></a><a name="add-controller-code"></a>서버 프로젝트에 저장소 컨트롤러를 추가합니다.
1. Visual Studio에서 .NET 서버 프로젝트를 엽니다. NuGet 패키지 [Microsoft.Azure.Mobile.Server.Files]를 추가합니다. **시험판 포함**을 선택하도록 합니다.
2. Visual Studio에서 .NET 서버 프로젝트를 엽니다. **컨트롤러** 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** -> **컨트롤러** -> **Web API 2 컨트롤러 - 비어 있음**을 선택합니다. 컨트롤러 이름을 `TodoItemStorageController`로 지정합니다.
3. 다음 using 명령문을 추가합니다.
   
        using Microsoft.Azure.Mobile.Server.Files;
        using Microsoft.Azure.Mobile.Server.Files.Controllers;
4. 기본 클래스를 `StorageController`로 변경합니다.
   
        public class TodoItemStorageController : StorageController<TodoItem>
5. 클래스에 다음 메서드를 추가합니다.
   
        [HttpPost]
        [Route("tables/TodoItem/{id}/StorageToken")]
        public async Task<HttpResponseMessage> PostStorageTokenRequest(string id, StorageTokenRequest value)
        {
            StorageToken token = await GetStorageTokenAsync(id, value);
   
            return Request.CreateResponse(token);
        }
   
        // Get the files associated with this record
        [HttpGet]
        [Route("tables/TodoItem/{id}/MobileServiceFiles")]
        public async Task<HttpResponseMessage> GetFiles(string id)
        {
            IEnumerable<MobileServiceFile> files = await GetRecordFilesAsync(id);
   
            return Request.CreateResponse(files);
        }
   
        [HttpDelete]
        [Route("tables/TodoItem/{id}/MobileServiceFiles/{name}")]
        public Task Delete(string id, string name)
        {
            return base.DeleteFileAsync(id, name);
        }
6. 특성 라우팅을 설정하려면 Web API 구성을 업데이트합니다. **Startup.MobileApp.cs**에서 `config` 변수 정의 뒤의 `ConfigureMobileApp()` 메서드에 다음 줄을 추가합니다.
   
        config.MapHttpAttributeRoutes();
7. 서버 프로젝트를 모바일 앱 백 엔드에 게시합니다.

### <a name="a-nameroutes-registeredaroutes-registered-by-the-storage-controller"></a><a name="routes-registered"></a>저장소 컨트롤러에서 등록된 경로
새 `TodoItemStorageController` 는 관리하는 레코드에서 두 개의 하위 리소스를 노출합니다.

* StorageToken
  
  * HTTP 게시: 저장소 토큰 만들기
    
      `/tables/TodoItem/{id}/MobileServiceFiles`
* MobileServiceFiles
  
  * HTTP 가져오기: 레코드와 관련된 파일의 목록 검색하기
    
      `/tables/TodoItem/{id}/MobileServiceFiles`
  * HTTP 삭제: 파일 리소스 식별자에 지정된 파일 삭제
    
      `/tables/TodoItem/{id}/MobileServiceFiles/{fileid}`

### <a name="a-nameclient-communicationaclient-and-server-communication"></a><a name="client-communication"></a>클라이언트 및 서버 통신
`TodoItemStorageController` 에는 Blob을 업로드하거나 다운로드할 경로가 *없습니다* . 우선 SAS 토큰(공유 액세스 서명)을 안전하게 특정 Blob 또는 컨테이너에 액세스하도록 가져온 후에 모바일 클라이언트가 작업을 수행하기 위해 Blob 저장소와 *직접* 상호 작용하기 때문입니다. 그렇지 않으면 저장소에 대한 액세스는 모바일 백 엔드의 확장성 및 가용성에 의해 제한될 것이기에 중요한 아키텍처 설계입니다. 대신 직접 Azure 저장소에 연결하여 모바일 클라이언트가 자동 분할 및 지역 배포 등의 기능을 활용할 수 있습니다.

공유 액세스 서명은 저장소 계정의 리소스에 대한 위임된 권한을 제공합니다. 즉, 계정 액세스 키를 공유할 필요 없이 지정된 권한 집합을 사용하여 지정된 기간 동안 클라이언트에게 저장소 계정의 개체에 대한 제한된 권한을 부여할 수 있습니다. 자세한 내용은 [공유 액세스 서명 이해]를 참조하세요.

아래 다이어그램에서는 클라이언트와 서버 상호 작용을 보여줍니다. 파일을 업로드하기 전에 클라이언트는 서비스에서 SAS 토큰을 요청합니다. 서비스는 저장소 연결 문자열을 사용하여 새 SAS를 생성하며 이는 클라이언트에 반환됩니다. SAS는 시간을 제한하고 특정 파일 또는 컨테이너에 대한 사용 권한을 제한합니다. 모바일 클라이언트는 SAS 및 Azure 저장소 클라이언트 SDK를 사용하여 Blob 저장소에 파일을 업로드합니다.

![SAS 토큰 요청](./media/app-service-mobile-xamarin-forms-blob-storage/storage-token-diagram.png)

## <a name="update-your-client-app-to-add-image-support"></a>클라이언트 앱을 업데이트하여 이미지 지원 추가
Visual Studio 또는 Xamarin Studio에서 Xamarin.Forms 빠른 시작 프로젝트를 엽니다. NuGet 패키지를 설치하고 이식 가능한 라이브러리 프로젝트 및 iOS, Android 및 Windows 클라이언트 프로젝트를 업데이트합니다.

* [NuGet 패키지 추가](#add-nuget)
* [IPlatform 인터페이스 추가](#add-iplatform)
* [FileHelper 클래스 추가](#add-filehelper)
* [파일 동기화 처리기를 추가합니다.](#file-sync-handler)
* [TodoItemManager 업데이트](#update-todoitemmanager)
* [세부 정보 보기 추가](#add-details-view)
* [기본 보기 업데이트 ](#update-main-view)
* [Android 프로젝트 업데이트](#update-android), [iOS 프로젝트](#update-ios), [Windows 프로젝트](#update-windows)

> [!NOTE]
> 이 자습서는 Windows Phone이 아닌 Android, iOS 및 Windows 스토어 플랫폼에 대한 지침만을 포함합니다.
> 
> 

### <a name="a-nameadd-nugetaadd-nuget-packages"></a><a name="add-nuget"></a>NuGet 패키지 추가
솔루션을 마우스 오른쪽 단추로 클릭하고 **솔루션에 대한 NuGet 패키지 관리**를 선택합니다. 다음 NuGet 패키지를 솔루션의 **모든** 프로젝트에 추가합니다. **시험판 포함**을 선택하도록 합니다.

* [Microsoft.Azure.Mobile.Client.Files]
* [Microsoft.Azure.Mobile.Client.SQLiteStore]
* [PCLStorage]

이 샘플에서는 편의를 위해 [PCLStorage] 라이브러리를 사용하지만 Azure 모바일 앱 클라이언트 SDK에서는 필요하지 않습니다.

[PCLStorage]: https://www.nuget.org/packages/PCLStorage/

### <a name="a-nameadd-iplatformaadd-iplatform-interface"></a><a name="add-iplatform"></a>IPlatform 인터페이스 추가
기본 이식 가능한 라이브러리 프로젝트에 새 인터페이스 `IPlatform` 를 만듭니다. 이 작업은 [Xamarin.Forms DependencyService] 패턴을 수행하여 런타임 시 오른쪽 플랫폼 관련 클래스를 로드합니다. 나중에 각 클라이언트 프로젝트에 플랫폼 특정 구현을 추가합니다.

1. 다음 using 명령문을 추가합니다.
   
        using Microsoft.WindowsAzure.MobileServices.Files;
        using Microsoft.WindowsAzure.MobileServices.Files.Metadata;
        using Microsoft.WindowsAzure.MobileServices.Sync;
2. 구현을 다음으로 바꿉니다.
   
        public interface IPlatform
        {
            Task <string> GetTodoFilesPathAsync();
   
            Task<IMobileServiceFileDataSource> GetFileDataSource(MobileServiceFileMetadata metadata);
   
            Task<string> TakePhotoAsync(object context);
   
            Task DownloadFileAsync<T>(IMobileServiceSyncTable<T> table, MobileServiceFile file, string filename);
        }

### <a name="a-nameadd-filehelperaadd-filehelper-class"></a><a name="add-filehelper"></a>FileHelper 클래스 추가
1. 기본 이식 가능한 라이브러리 프로젝트에 새 클래스 `FileHelper` 를 만듭니다. 다음 using 명령문을 추가합니다.
   
        using System.IO;
        using PCLStorage;
        using System.Threading.Tasks;
        using Xamarin.Forms;
2. 클래스 정의를 추가합니다.
   
        public class FileHelper
        {
            public static async Task<string> CopyTodoItemFileAsync(string itemId, string filePath)
            {
                IFolder localStorage = FileSystem.Current.LocalStorage;
   
                string fileName = Path.GetFileName(filePath);
                string targetPath = await GetLocalFilePathAsync(itemId, fileName);
   
                var sourceFile = await localStorage.GetFileAsync(filePath);
                var sourceStream = await sourceFile.OpenAsync(FileAccess.Read);
   
                var targetFile = await localStorage.CreateFileAsync(targetPath, CreationCollisionOption.ReplaceExisting);
   
                using (var targetStream = await targetFile.OpenAsync(FileAccess.ReadAndWrite)) {
                    await sourceStream.CopyToAsync(targetStream);
                }
   
                return targetPath;
            }
   
            public static async Task<string> GetLocalFilePathAsync(string itemId, string fileName)
            {
                IPlatform platform = DependencyService.Get<IPlatform>();
   
                string recordFilesPath = Path.Combine(await platform.GetTodoFilesPathAsync(), itemId);
   
                    var checkExists = await FileSystem.Current.LocalStorage.CheckExistsAsync(recordFilesPath);
                    if (checkExists == ExistenceCheckResult.NotFound) {
                        await FileSystem.Current.LocalStorage.CreateFolderAsync(recordFilesPath, CreationCollisionOption.ReplaceExisting);
                    }
   
                return Path.Combine(recordFilesPath, fileName);
            }
   
            public static async Task DeleteLocalFileAsync(Microsoft.WindowsAzure.MobileServices.Files.MobileServiceFile fileName)
            {
                string localPath = await GetLocalFilePathAsync(fileName.ParentId, fileName.Name);
                var checkExists = await FileSystem.Current.LocalStorage.CheckExistsAsync(localPath);
   
                if (checkExists == ExistenceCheckResult.FileExists) {
                    var file = await FileSystem.Current.LocalStorage.GetFileAsync(localPath);
                    await file.DeleteAsync();
                }
            }
        }

### <a name="a-namefile-sync-handlera-add-a-file-sync-handler"></a><a name="file-sync-handler"></a> 파일 동기화 처리기를 추가합니다.
기본 이식 가능한 라이브러리 프로젝트에 새 클래스 `TodoItemFileSyncHandler` 를 만듭니다. 이 클래스는 파일을 추가 또는 제거하는 경우 Azure SDK에서 콜백을 포함하여 코드에 알립니다.

Azure 모바일 클라이언트 SDK는 실제로 파일 데이터를 저장하지 않습니다. 클라이언트 SDK는 파일이 로컬 장치에 저장되는지 여부 및 방법을 차례로 결정하는 `IFileSyncHandler`의 구현을 호출합니다.

1. 다음 using 명령문을 추가합니다.
   
        using System.Threading.Tasks;
        using Microsoft.WindowsAzure.MobileServices.Files.Sync;
        using Microsoft.WindowsAzure.MobileServices.Files;
        using Microsoft.WindowsAzure.MobileServices.Files.Metadata;
        using Xamarin.Forms;
2. 클래스 정의를 다음으로 바꿉니다. 
   
        public class TodoItemFileSyncHandler : IFileSyncHandler
        {
            private readonly TodoItemManager todoItemManager;
   
            public TodoItemFileSyncHandler(TodoItemManager itemManager)
            {
                this.todoItemManager = itemManager;
            }
   
            public Task<IMobileServiceFileDataSource> GetDataSource(MobileServiceFileMetadata metadata)
            {
                IPlatform platform = DependencyService.Get<IPlatform>();
                return platform.GetFileDataSource(metadata);
            }
   
            public async Task ProcessFileSynchronizationAction(MobileServiceFile file, FileSynchronizationAction action)
            {
                if (action == FileSynchronizationAction.Delete) {
                    await FileHelper.DeleteLocalFileAsync(file);
                }
                else { // Create or update. We're aggressively downloading all files.
                    await this.todoItemManager.DownloadFileAsync(file);
                }
            }
        }

### <a name="a-nameupdate-todoitemmanageraupdate-todoitemmanager"></a><a name="update-todoitemmanager"></a>TodoItemManager 업데이트
1. **TodoItemManager.cs**에서 `#define OFFLINE_SYNC_ENABLED` 줄의 주석 처리를 제거합니다.
2. **TodoItemManager.cs**에서 다음 using 문을 추가합니다.
   
        using System.IO;
        using Xamarin.Forms;
        using Microsoft.WindowsAzure.MobileServices.Files;
        using Microsoft.WindowsAzure.MobileServices.Files.Sync;
        using Microsoft.WindowsAzure.MobileServices.Eventing;
3. `TodoItemManager`의 생성자에서 `DefineTable()`를 호출한 후에 다음을 추가합니다.
   
        // Initialize file sync
        this.client.InitializeFileSyncContext(new TodoItemFileSyncHandler(this), store);
4. 생성자에서 `InitializeAsync`에 대한 호출을 다음으로 바꿉니다. 이렇게 하면 레코드가 로컬 저장소에서 수정될 때 콜백이 있는지 확인합니다. 파일 동기화 기능은 이러한 콜백을 사용하여 파일 동기화 처리기를 트리거합니다.
   
        this.client.SyncContext.InitializeAsync(store, StoreTrackingOptions.NotifyLocalAndServerOperations);
5. `SyncAsync()`에서 `PushAsync()`를 호출한 후에 다음을 추가합니다.
   
        await this.todoTable.PushFileChangesAsync();
6. 다음 메서드를 `TodoItemManager`에 추가합니다.
   
        internal async Task DownloadFileAsync(MobileServiceFile file)
        {
            var todoItem = await todoTable.LookupAsync(file.ParentId);
            IPlatform platform = DependencyService.Get<IPlatform>();
   
            string filePath = await FileHelper.GetLocalFilePathAsync(file.ParentId, file.Name); 
            await platform.DownloadFileAsync(this.todoTable, file, filePath);
        }
   
        internal async Task<MobileServiceFile> AddImage(TodoItem todoItem, string imagePath)
        {
            string targetPath = await FileHelper.CopyTodoItemFileAsync(todoItem.Id, imagePath);
            return await this.todoTable.AddFileAsync(todoItem, Path.GetFileName(targetPath));
        }
   
        internal async Task DeleteImage(TodoItem todoItem, MobileServiceFile file)
        {
            await this.todoTable.DeleteFileAsync(file);
        }
   
        internal async Task<IEnumerable<MobileServiceFile>> GetImageFilesAsync(TodoItem todoItem)
        {
            return await this.todoTable.GetFilesAsync(todoItem);
        }

### <a name="a-nameadd-details-viewaadd-a-details-view"></a><a name="add-details-view"></a>세부 정보 보기 추가
이 섹션에서는 할 일 항목에 대한 새로운 세부 정보 보기를 추가합니다. 사용자가 할 일 항목을 선택하고 항목에 추가될 새 이미지를 허용하는 경우에 뷰가 생성됩니다.

1. 새 클래스 **TodoItemImage** 를 다음 구현으로 이식 가능한 라이브러리 프로젝트에 추가합니다.
   
        public class TodoItemImage : INotifyPropertyChanged
        {
            private string name;
            private string uri;
   
            public MobileServiceFile File { get; private set; }
   
            public string Name
            {
                get { return name; }
                set
                {
                    name = value;
                    OnPropertyChanged(nameof(Name));
                }
            }
   
            public string Uri
            {
                get { return uri; }      
                set
                {
                    uri = value;
                    OnPropertyChanged(nameof(Uri));
                }
            }
   
            public TodoItemImage(MobileServiceFile file, TodoItem todoItem)
            {
                Name = file.Name;
                File = file;
   
                FileHelper.GetLocalFilePathAsync(todoItem.Id, file.Name).ContinueWith(x => this.Uri = x.Result);
            }
   
            public event PropertyChangedEventHandler PropertyChanged;
   
            private void OnPropertyChanged(string propertyName)
            {
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(propertyName));
            }
        }
2. **App.cs**를 편집합니다. `MainPage` 의 초기화를 다음으로 바꿉니다.
   
        MainPage = new NavigationPage(new TodoList());
3. **App.cs**에서 다음 속성을 추가합니다.
   
        public static object UIContext { get; set; }
4. 이식 가능한 라이브러리 프로젝트를 마우스 오른쪽 단추로 클릭하고 **추가** -> **새 항목** -> **플랫폼 간** -> **Xaml 페이지 형성**을 선택합니다. 보기 이름을 `TodoItemDetailsView`로 지정합니다.
5. **TodoItemDetailsView.xaml** 을 열고 ContentPage의 본문을 다음으로 바꿉니다.
   
          <Grid>
            <Grid.RowDefinitions>
              <RowDefinition Height="Auto"/>
              <RowDefinition Height="Auto"/>
              <RowDefinition Height="*"/>
            </Grid.RowDefinitions>
            <Button Clicked="OnAdd" Text="Add image"></Button>
            <ListView x:Name="imagesList"
                      ItemsSource="{Binding Images}"
                      IsPullToRefreshEnabled="false"
                      Grid.Row="2">
              <ListView.ItemTemplate>
                <DataTemplate>
                  <ImageCell ImageSource="{Binding Uri}"
                             Text="{Binding Name}">
                  </ImageCell>
                </DataTemplate>
              </ListView.ItemTemplate>
            </ListView>
          </Grid>
6. **TodoItemDetailsView.xaml.cs** 를 편집하고 다음 using 문을 추가합니다.
   
        using System.Collections.ObjectModel;
        using Microsoft.WindowsAzure.MobileServices.Files;
7. `TodoItemDetailsView` 의 구현을 다음으로 바꿉니다.
   
        public partial class TodoItemDetailsView : ContentPage
        {
            private TodoItemManager manager;
   
            public TodoItem TodoItem { get; set; }        
            public ObservableCollection<TodoItemImage> Images { get; set; }
   
            public TodoItemDetailsView(TodoItem todoItem, TodoItemManager manager)
            {
                InitializeComponent();
                this.Title = todoItem.Name;
   
                this.TodoItem = todoItem;
                this.manager = manager;
   
                this.Images = new ObservableCollection<TodoItemImage>();
                this.BindingContext = this;
            }
   
            public async Task LoadImagesAsync()
            {
                IEnumerable<MobileServiceFile> files = await this.manager.GetImageFilesAsync(TodoItem);
                this.Images.Clear();
   
                foreach (var f in files) {
                    var todoImage = new TodoItemImage(f, this.TodoItem);
                    this.Images.Add(todoImage);
                }
            }
   
            public async void OnAdd(object sender, EventArgs e)
            {
                IPlatform mediaProvider = DependencyService.Get<IPlatform>();
                string sourceImagePath = await mediaProvider.TakePhotoAsync(App.UIContext);
   
                if (sourceImagePath != null) {
                    MobileServiceFile file = await this.manager.AddImage(this.TodoItem, sourceImagePath);
   
                    var image = new TodoItemImage(file, this.TodoItem);
                    this.Images.Add(image);
                }
            }
        }

### <a name="a-nameupdate-main-viewaupdate-the-main-view"></a><a name="update-main-view"></a>기본 보기 업데이트
할 일 항목을 선택할 때 기본 보기를 업데이트하여 세부 정보 보기를 엽니다.

**TodoList.xaml.cs**에서 `OnSelected`의 구현을 다음으로 바꿉니다.

    public async void OnSelected(object sender, SelectedItemChangedEventArgs e)
    {
        var todo = e.SelectedItem as TodoItem;

        if (todo != null) {
            var detailsView = new TodoItemDetailsView(todo, manager);
            await detailsView.LoadImagesAsync();
            await Navigation.PushAsync(detailsView);
        }

        todoList.SelectedItem = null;
    }

### <a name="a-nameupdate-androidaupdate-the-android-project"></a><a name="update-android"></a>Android 프로젝트 업데이트
새 이미지를 캡처하도록 파일을 다운로드하고 카메라를 사용하기 위한 코드를 포함하여 Android 프로젝트에 플랫폼 특정 코드를 추가합니다. 

이 코드는 Xamarin.Forms [DependencyService](https://developer.xamarin.com/guides/xamarin-forms/dependency-service/) 를 사용하여 런타임 시 오른쪽 플랫폼 관련 클래스를 로드합니다.

1. 구성 요소 **Xamarin.Mobile** 을 Android 프로젝트에 추가합니다.
2. 다음 구현을 사용하여 새 클래스 `DroidPlatform` 을 추가합니다. 프로젝트의 기본 네임스페이스로 "YourNamespace"를 대체합니다.
   
        using System;
        using System.IO;
        using System.Threading.Tasks;
        using Android.Content;
        using Microsoft.WindowsAzure.MobileServices.Files;
        using Microsoft.WindowsAzure.MobileServices.Files.Metadata;
        using Microsoft.WindowsAzure.MobileServices.Files.Sync;
        using Microsoft.WindowsAzure.MobileServices.Sync;
        using Xamarin.Media;
   
        [assembly: Xamarin.Forms.Dependency(typeof(YourNamespace.Droid.DroidPlatform))]
        namespace YourNamespace.Droid
        {
            public class DroidPlatform : IPlatform
            {
                public async Task DownloadFileAsync<T>(IMobileServiceSyncTable<T> table, MobileServiceFile file, string filename)
                {
                    var path = await FileHelper.GetLocalFilePathAsync(file.ParentId, file.Name);
                    await table.DownloadFileAsync(file, path);
                }
   
                public async Task<IMobileServiceFileDataSource> GetFileDataSource(MobileServiceFileMetadata metadata)
                {
                    var filePath = await FileHelper.GetLocalFilePathAsync(metadata.ParentDataItemId, metadata.FileName);
                    return new PathMobileServiceFileDataSource(filePath);
                }
   
                public async Task<string> TakePhotoAsync(object context)
                {
                    try {
                        var uiContext = context as Context;
                        if (uiContext != null) {
                            var mediaPicker = new MediaPicker(uiContext);
                            var photo = await mediaPicker.TakePhotoAsync(new StoreCameraMediaOptions());
   
                            return photo.Path;
                        }
                    }
                    catch (TaskCanceledException) {
                    }
   
                    return null;
                }
   
                public Task<string> GetTodoFilesPathAsync()
                {
                    string appData = Environment.GetFolderPath(Environment.SpecialFolder.MyDocuments);
                    string filesPath = Path.Combine(appData, "TodoItemFiles");
   
                    if (!Directory.Exists(filesPath)) {
                        Directory.CreateDirectory(filesPath);
                    }
   
                    return Task.FromResult(filesPath);
                }
            }
        }
3. **MainActivity.cs**를 편집합니다. `OnCreate`에서 `LoadApplication()`를 호출하기 전에 다음을 추가합니다.
   
        App.UIContext = this;

### <a name="a-nameupdate-iosaupdate-the-ios-project"></a><a name="update-ios"></a>iOS 프로젝트 업데이트
iOS 프로젝트에 플랫폼 특정 코드를 추가합니다.

1. 구성 요소 **Xamarin.Mobile** 을 iOS 프로젝트에 추가합니다.
2. 다음 구현을 사용하여 새 클래스 `TouchPlatform` 을 추가합니다. 프로젝트의 기본 네임스페이스로 "YourNamespace"를 대체합니다.
   
        using System;
        using System.Collections.Generic;
        using System.IO;
        using System.Text;
        using System.Threading.Tasks;
        using Microsoft.WindowsAzure.MobileServices.Files;
        using Microsoft.WindowsAzure.MobileServices.Files.Metadata;
        using Microsoft.WindowsAzure.MobileServices.Files.Sync;
        using Microsoft.WindowsAzure.MobileServices.Sync;
        using Xamarin.Media;
   
        [assembly: Xamarin.Forms.Dependency(typeof(YourNamespace.iOS.TouchPlatform))]
        namespace YourNamespace.iOS
        {
            class TouchPlatform : IPlatform
            {
                public async Task DownloadFileAsync<T>(IMobileServiceSyncTable<T> table, MobileServiceFile file, string filename)
                {
                    var path = await FileHelper.GetLocalFilePathAsync(file.ParentId, file.Name);
                    await table.DownloadFileAsync(file, path);
                }
   
                public async Task<IMobileServiceFileDataSource> GetFileDataSource(MobileServiceFileMetadata metadata)
                {
                    var filePath = await FileHelper.GetLocalFilePathAsync(metadata.ParentDataItemId, metadata.FileName);
                    return new PathMobileServiceFileDataSource(filePath);
                }
   
                public async Task<string> TakePhotoAsync(object context)
                {
                    try {
                        var mediaPicker = new MediaPicker();
                        var mediaFile = await mediaPicker.PickPhotoAsync();
                        return mediaFile.Path;
                    }
                    catch (TaskCanceledException) {
                        return null;
                    }
                }
   
                public Task<string> GetTodoFilesPathAsync()
                {
                    string filesPath = Path.Combine(Environment.GetFolderPath(Environment.SpecialFolder.MyDocuments), "TodoItemFiles");
   
                    if (!Directory.Exists(filesPath)) {
                        Directory.CreateDirectory(filesPath);
                    }
   
                    return Task.FromResult(filesPath);
                }
            }
        }
3. **AppDelegate.cs**를 편집하고 `SQLitePCL.CurrentPlatform.Init()`에 대한 호출의 주석 처리를 제거합니다.

### <a name="a-nameupdate-windowsaupdate-the-windows-project"></a><a name="update-windows"></a>Windows 프로젝트 업데이트
1. Visual Studio 확장 [Windows 8.1용 SQLite](http://go.microsoft.com/fwlink/?LinkID=716919)를 설치합니다. 
   자세한 내용은 [Windows 앱에 오프라인 동기화 사용](app-service-mobile-windows-store-dotnet-get-started-offline-data.md)자습서를 참조하세요. 
2. **Package.appxmanifest**를 편집하고 **웹캠** 기능을 확인합니다.
3. 다음 구현을 사용하여 새 클래스 `WindowsStorePlatform` 을 추가합니다. 프로젝트의 기본 네임스페이스로 "YourNamespace"를 대체합니다.
   
        using System;
        using System.Threading.Tasks;
        using Microsoft.WindowsAzure.MobileServices.Files;
        using Microsoft.WindowsAzure.MobileServices.Files.Metadata;
        using Microsoft.WindowsAzure.MobileServices.Files.Sync;
        using Microsoft.WindowsAzure.MobileServices.Sync;
        using Windows.Foundation;
        using Windows.Media.Capture;
        using Windows.Storage;
        using YourNamespace;
   
        [assembly: Xamarin.Forms.Dependency(typeof(WinApp.WindowsStorePlatform))]
        namespace WinApp
        {
            public class WindowsStorePlatform : IPlatform
            {
                public async Task DownloadFileAsync<T>(IMobileServiceSyncTable<T> table, MobileServiceFile file, string filename)
                {
                    var path = await FileHelper.GetLocalFilePathAsync(file.ParentId, file.Name);
                    await table.DownloadFileAsync(file, path);
                }
   
                public async Task<IMobileServiceFileDataSource> GetFileDataSource(MobileServiceFileMetadata metadata)
                {
                    var filePath = await FileHelper.GetLocalFilePathAsync(metadata.ParentDataItemId, metadata.FileName);
                    return new PathMobileServiceFileDataSource(filePath);
                }
   
                public async Task<string> GetTodoFilesPathAsync()
                {
                    var storageFolder = ApplicationData.Current.LocalFolder;
                    var filePath = "TodoItemFiles";
   
                    var result = await storageFolder.TryGetItemAsync(filePath);
   
                    if (result == null) {
                        result = await storageFolder.CreateFolderAsync(filePath);
                    }
   
                    return result.Name; // later operations will use relative paths
                }
   
                public async Task<string> TakePhotoAsync(object context)
                {
                    try {
                        CameraCaptureUI dialog = new CameraCaptureUI();
                        Size aspectRatio = new Size(16, 9);
                        dialog.PhotoSettings.CroppedAspectRatio = aspectRatio;
   
                        StorageFile file = await dialog.CaptureFileAsync(CameraCaptureUIMode.Photo);
                        return file.Path;
                    }
                    catch (TaskCanceledException) {
                        return null;
                    }
                }
            }
        }

## <a name="summary"></a>요약
이 문서는 Azure 모바일 클라이언트 및 서버 SDK에서 새 파일 지원을 사용하여 Azure 저장소로 작동하는 방법을 설명합니다. 

* 저장소 계정을 만들고 모바일 앱 백 엔드에 연결 문자열을 추가합니다. 백 엔드에는 Azure 저장소 키가 있습니다. Azure 저장소에 액세스해야 할 때 마다 모바일 클라이언트가 SAS 토큰(공유 액세스 서명)을 요청합니다. Azure Storage에서 SAS 토큰에 대해 자세히 알아보려면 [공유 액세스 서명 이해]를 참조하세요.
* SAS 토큰 요청을 처리하고 레코드와 연관된 파일을 가져오기 위해 `StorageController`를 하위 클래스로 가진 컨트롤러를 만듭니다. 기본적으로 파일은 레코드 ID를 컨테이너 이름의 일부로 사용하여 레코드와 연관됩니다. 동작은 `IContainerNameResolver`의 구현을 지정하여 사용자 지정될 수 있습니다. 또한 SAS 토큰 정책은 사용자 지정할 수 있습니다.
* Azure 모바일 클라이언트 SDK는 실제로 파일 데이터를 저장하지 않습니다. 대신, 클라이언트 SDK는 `IFileSyncHandler`를 호출하며 그 다음 파일이 로컬 장치에 저장되는 방법(및 여부)을 결정합니다. 동기화 처리기는 다음과 같이 등록됩니다.
  
        client.InitializeFileSync(new MyFileSyncHandler(), store);
  
      + `IFileSyncHandler.GetDataSource` 은 Azure 모바일 클라이언트 SDK가 파일 데이터를 필요로 할 때(예:업로드 프로세스의 일부로) 호출됩니다. 이렇게 하면 파일이 로컬 장치에 저장되고 필요한 경우 해당 정보를 반환하는 방법(및 여부)를 관리하는 기능을 제공합니다.
  
      + `IFileSyncHandler.ProcessFileSynchronizationAction` 은 파일 동기화 흐름의 일부로 호출됩니다. 파일 참조 및 FileSynchronizationAction 열거형 값이 제공되므로 응용 프로그램이 해당 이벤트를 처리해야 하는 방법을 결정할 수 있습니다(예: 생성되거나 업데이트될 때 파일을 자동으로 다운로드, 해당 파일이 서버에서 삭제될 때 로컬 장치에서 파일 삭제).
* `MobileServiceFile`은 `IMobileServiceTable` 또는 `IMobileServiceSyncTable`을 사용하여 각각 온라인 또는 오프라인 모드에서 사용될 수 있습니다. 오프라인 시나리오에서 업로드는 앱이 `PushFileChangesAsync`를 호출할 때 발생합니다. 이로 인해 오프라인 작업 큐를 처리할 수 있습니다. 각 파일 작업의 경우 Azure 모바일 클라이언트 SDK는 `IFileSyncHandler` 인스턴스에서 `GetDataSource` 메서드를 호출하여 업로드할 파일 콘텐츠를 검색합니다.
* 항목의 파일을 검색하기 위해 ``GetFilesAsync` method on the  `IMobileServiceTable<T>` or IMobileServiceSyncTable<T>` 인스턴스를 호출합니다. 이 메서드는 제공된 데이터 항목과 연결된 파일의 목록을 반환합니다. (참고: 해당 작업은 *로컬* 작업이며 마지막으로 동기화되는 경우 개체의 상태를 기준으로 파일을 반환합니다. 서버에서 업데이트된 파일 목록을 얻으려면 동기화 작업을 먼저 시작해야 합니다.)
  
        IEnumerable<MobileServiceFile> files = await myTable.GetFilesAsync(myItem);
* 파일 동기화 기능은 클라이언트가 푸시 또는 끌어오기 작업의 일부로 받은 레코드를 검색하기 위해 로컬 저장소에서 레코드 변경 알림을 사용합니다. `StoreTrackingOptions` 매개 변수를 사용하는 동기화 컨텍스트에 대해 로컬 및 서버 알림을 설정하여 수행합니다. 
  
        this.client.SyncContext.InitializeAsync(store, StoreTrackingOptions.NotifyLocalAndServerOperations);
  
      + 다른 저장소 추적 옵션은 로컬 전용 또는 서버 전용 알림 등으로 사용할 수 있습니다. `IMobileServiceClient`의 `EventManager` 속성을 사용하는 사용자 지정 콜백을 추가하거나 소유할 수 있습니다.
  
            jobService.MobileService.EventManager.Subscribe<StoreOperationCompletedEvent>(StoreOperationEventHandler);
* 연결이 명명 규칙을 통해 수행되기 때문에 Blob 저장소를 직접 수정하여 레코드에서 파일을 추가하거나 제거할 수 있습니다. 그러나 이 경우 항상 **연결된 Blob이 수정되는 경우 레코드 타임스탬프를 업데이트**해야 합니다. Azure 모바일 클라이언트 SDK는 파일을 추가하거나 제거하는 경우 항상 레코드를 업데이트합니다. 
  
    이 요구 사항에 대한 이유는 일부 모바일 클라이언트가 이미 로컬 저장소에 레코드를 가지고 있기 때문입니다. 이러한 클라이언트가 증분 끌어오기를 수행하는 경우 이 레코드는 반환되지 않고 클라이언트는 새로 연결된 파일에 대해 쿼리하지 않습니다. 이 문제를 방지하려면 Azure 모바일 클라이언트 SDK를 사용하지 않는 Blob 저장소를 수행할 때 레코드 타임스탬프를 업데이트하는 것이 좋습니다.
* 클라이언트 프로젝트는 [Xamarin.Forms DependencyService] 패턴을 사용하여 런타임 시 올바른 플랫폼 관련 클래스를 로드합니다. 이 샘플에서는 각 플랫폼 특정 프로젝트에서 구현을 통해 인터페이스 `IPlatform` 을 정의했습니다.

<!-- URLs. -->

[Visual Studio Community 2013]: https://go.microsoft.com/fwLink/p/?LinkID=534203
[Xamarin.Forms 앱 만들기]: app-service-mobile-xamarin-forms-get-started.md
[Xamarin.Forms DependencyService]: https://developer.xamarin.com/guides/xamarin-forms/dependency-service/
[Microsoft.Azure.Mobile.Client.Files]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client.Files/
[Microsoft.Azure.Mobile.Client.SQLiteStore]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client.SQLiteStore/
[Microsoft.Azure.Mobile.Server.Files]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Files/
[공유 액세스 서명 이해]: ../storage/storage-dotnet-shared-access-signature-part-1.md
[Azure 저장소 계정 만들기]:  ../storage/storage-create-storage-account.md#create-a-storage-account



<!--HONumber=Nov16_HO3-->


