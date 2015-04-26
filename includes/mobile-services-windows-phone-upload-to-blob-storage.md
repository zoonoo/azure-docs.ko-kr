
##<a name="add-select-images"></a>빠른 시작 클라이언트 앱을 업데이트하여 이미지 캡처 및 업로드

이 섹션에서는 [모바일 서비스 시작] 자습서의 프로젝트를 업데이트하여 사진을 찍고 해당 사진을 Azure Blob 저장소에 업로드합니다. 이 자습서에서는  `Microsoft.Phone.Tasks` 네임스페이스의 [CameraCaptureTask]를 사용하여 이미지를 캡처합니다. 이 클래스는 Windows Phone 장치에서 카메라 UI를 시작하여 사진을 캡처하고 이미지를 Windows Phone 장치의 카메라 앨범에 자동으로 저장합니다. 이미지를 카메라 앨범에 저장하지 않으려는 경우  `Microsoft.Devices` 네임스페이스의 [PhotoCamera] 클래스를 사용하세요.

1. Visual Studio의 솔루션 탐색기에서 프로젝트의 **속성**을 확장합니다. 그런 다음, WMAppManifest.xml 파일을 열고 **기능** 탭에서 **ID\_CAP\_ISV\_CAMERA**를 클릭하여 카메라를 사용하도록 설정합니다. 파일을 닫아 변경 내용을 저장합니다.

   	![](./media/mobile-services-windows-phone-upload-to-blob-storage/mobile-upload-blob-app-WMAppmanifest-wp8.png)

   	이제 앱에서 컴퓨터에 연결된 카메라를 사용할 수 있습니다. 앱을 처음으로 실행하면 카메라 액세스를 허용하라는 메시지가 표시됩니다.

2. MainPage.xaml 파일을 열고 이름이 **ContentPanel**인 **Grid** 요소를 다음 코드로 바꿉니다.

        <!--ContentPanel - place additional content here-->
        <Grid x:Name="ContentPanel" Grid.Row="1" Margin="12,0,12,0">
            <Grid.RowDefinitions>
                <RowDefinition Height="Auto" />
                <RowDefinition Height="Auto" />
                <RowDefinition Height="Auto" />
                <RowDefinition Height="Auto" />
                <RowDefinition Height="Auto" />
                <RowDefinition Height="*" />
            </Grid.RowDefinitions>
            <Grid.ColumnDefinitions>
                <ColumnDefinition Width="2*" />
                <ColumnDefinition Width="2*" />
            </Grid.ColumnDefinitions>
            <TextBlock Grid.Row="0" Grid.ColumnSpan="2" Text="Enter some text below, click Capture Image to add a captured image. Then click Save to insert a new TodoItem item into your database" TextWrapping="Wrap" Margin="12"/>
            <TextBox Grid.Row="1" Grid.ColumnSpan="2" Name="TextInput" Text="" />
            <Button Name="ButtonCaptureImage" Grid.Row="2" Click="ButtonCaptureImage_Click">Capture Image</Button>
            <Button Grid.Row ="2" Grid.Column="1" Name="ButtonSave" Click="ButtonSave_Click">Save</Button>
            <TextBlock Grid.Row="3" Grid.ColumnSpan="2" Text="Click refresh below to load the unfinished TodoItems from your database. Use the checkbox to complete and update your TodoItems" TextWrapping="Wrap" Margin="12" />
            <Button Grid.Row="4" Grid.ColumnSpan="2" Name="ButtonRefresh" Click="ButtonRefresh_Click">Refresh</Button>
            <phone:LongListSelector Grid.Row="5" Grid.ColumnSpan="2" Name="ListItems">
                <phone:LongListSelector.ItemTemplate>
                    <DataTemplate>
                        <StackPanel Orientation="Vertical">
                            <CheckBox Name="CheckBoxComplete" IsChecked="{Binding Complete, Mode=TwoWay}" Checked="CheckBoxComplete_Checked" Content="{Binding Text}" Margin="10,5" VerticalAlignment="Center"/>
                            <Image Name="ImageUpload" Source="{Binding ImageUri, Mode=OneWay}" MaxHeight="150"/>
                        </StackPanel>
                    </DataTemplate>
                </phone:LongListSelector.ItemTemplate>
            </phone:LongListSelector>
        </Grid>

   	이 코드는 [CameraCaptureTask]를 시작하는 새 단추를 추가하고, **ItemTemplate**에 이미지를 추가하고, 바인딩 소스를 Blob 저장소 서비스에 업로드된 이미지 URI로 설정합니다.

3. MainPage.xaml.cs 프로젝트 파일을 열고 다음 **using** 문을 추가합니다.
	
		using Microsoft.Phone.Tasks;
		using System.IO;
		using Microsoft.WindowsAzure.Storage.Auth;
		using Microsoft.WindowsAzure.Storage.Blob;
    
4. MainPage.xaml.cs 프로젝트 파일에서 다음 속성을 추가하여 TodoItem 클래스를 업데이트합니다.

        [JsonProperty(PropertyName = "containerName")]
        public string ContainerName { get; set; }
		
        [JsonProperty(PropertyName = "resourceName")]
        public string ResourceName { get; set; }
		
        [JsonProperty(PropertyName = "sasQueryString")]
        public string SasQueryString { get; set; }
		
        [JsonProperty(PropertyName = "imageUri")]
        public string ImageUri { get; set; } 

5. MainPage.xaml.cs 프로젝트 파일에서 MainPage 클래스를 업데이트합니다. 다음 코드를 추가하여 [CameraCaptureTask] 및 캡처된 이미지를 참조할 스트림 개체를 선언합니다.

        // Using the CameraCaptureTask to allow the user to capture a todo item image //
        CameraCaptureTask cameraCaptureTask;
		
        // Using a stream reference to upload the image to blob storage.
        Stream imageStream = null;

6. MainPage.xaml.cs 프로젝트 파일에서 MainPage 클래스를 업데이트합니다. 다음 코드를 추가하여 CameraCaptureTask를 만들고 완료된 이벤트의 이벤트 처리기를 추가하도록 생성자를 업데이트합니다.

        // Constructor
        public MainPage()
        {
            InitializeComponent();
			
            cameraCaptureTask = new CameraCaptureTask();
            cameraCaptureTask.Completed += cameraCaptureTask_Completed;
        }
		
        void cameraCaptureTask_Completed(object sender, PhotoResult e)
        {
            imageStream = e.ChosenPhoto;
        }

7. MainPage.xaml.cs 프로젝트 파일에서 MainPage 클래스를 업데이트합니다. 카메라 UI를 표시하는 다음 코드를 추가하여 사용자가 **Capture Image** 단추를 클릭하면 이미지를 캡처할 수 있도록 합니다.

        private void ButtonCaptureImage_Click(object sender, RoutedEventArgs e)
        {
            cameraCaptureTask.Show();
        }

8. MainPage.xaml.cs 프로젝트 파일에서 MainPage 클래스를 업데이트합니다. 기존  `InsertTodoItem` 메서드를 다음 코드로 바꿉니다.
 
        private async void InsertTodoItem(TodoItem todoItem)
        {
            string errorString = string.Empty;            
			
            if (imageStream != null)
            {
                // Set blob properties of TodoItem.
                todoItem.ContainerName = "todoitemimages";
                todoItem.ResourceName = Guid.NewGuid().ToString() + ".jpg";
            }                       
			
            // Send the item to be inserted. When blob properties are set this
            // generates an SAS in the response.
            await todoTable.InsertAsync(todoItem);  
			
            // If we have a returned SAS, then upload the blob.
            if (!string.IsNullOrEmpty(todoItem.SasQueryString))
            {
                // Get the URI generated that contains the SAS 
                // and extract the storage credentials.
                StorageCredentials cred = new StorageCredentials(todoItem.SasQueryString);
                var imageUri = new Uri(todoItem.ImageUri);
				
                // Instantiate a Blob store container based on the info in the returned item.
                CloudBlobContainer container = new CloudBlobContainer(
                    new Uri(string.Format("https://{0}/{1}",
                        imageUri.Host, todoItem.ContainerName)), cred);                
				
                // Upload the new image as a BLOB from the stream.
                CloudBlockBlob blobFromSASCredential =
                    container.GetBlockBlobReference(todoItem.ResourceName);
                await blobFromSASCredential.UploadFromStreamAsync(imageStream);
				
				// When you request an SAS at the container-level instead of the blob-level,
				// you are able to upload multiple streams using the same container credentials.

                imageStream = null;
            }              
			
            // Add the new item to the collection.
            items.Add(todoItem);
            TextInput.Text = "";
        }


	이 코드는 요청을 모바일 서비스로 전송하여 이미지 파일 이름을 비롯한 새 TodoItem을 삽입합니다. 응답에는 SAS가 포함되며 이는 Blob 저장소의 이미지 및 데이터 바인딩의 이미지 URI를 삽입하는 데 사용됩니다.

마지막 단계로, 앱을 테스트하고 제대로 업로드되었는지 확인합니다.
		
##<a name="test"></a>앱에서 이미지 업로드 테스트

1. Visual Studio에서 F5 키를 눌러 에뮬레이터에서 또는 대상으로 지정된 실제 장치로 앱을 테스트할 수 있습니다.

2. 텍스트 상자에 일부 텍스트를 입력한 후 **Capture Image**를 클릭합니다.

   	![](./media/mobile-services-windows-phone-upload-to-blob-storage/mobile-upload-blob-app-view-wp8.png)

  	그러면 카메라 캡처 UI가 표시됩니다. 

3. 휴대폰에서 이미지 또는 스냅숏 단추를 클릭하여 사진을 찍습니다.
  
   	![](./media/mobile-services-windows-phone-upload-to-blob-storage/mobile-upload-blob-app-view-camera-wp8.png)

4. **accept**를 클릭하여 이미지를 허용하고 카메라 UI를 종료합니다.

    ![](./media/mobile-services-windows-phone-upload-to-blob-storage/mobile-upload-blob-app-view-camera-accept-wp8.png)

5. **Save**를 클릭하여 새 항목을 삽입하고 이미지를 업로드합니다.

	![](./media/mobile-services-windows-phone-upload-to-blob-storage/mobile-upload-blob-app-view-save-wp8.png)

6. 새 항목이 업로드된 이미지와 함께 목록 보기에 표시됩니다.

	![](./media/mobile-services-windows-phone-upload-to-blob-storage/mobile-upload-blob-app-view-final-wp8.png)

   >[AZURE.NOTE]새 항목의 <code>imageUri</code> 속성이 <strong>Image</strong> 컨트롤에 바인딩되면 이미지가 Blob 저장소 서비스에서 자동으로 다운로드됩니다.


[모바일 서비스 시작]: /ko-kr/documentation/articles/mobile-services-windows-phone-get-started
[CameraCaptureTask]: http://msdn.microsoft.com/library/windowsphone/develop/microsoft.phone.tasks.cameracapturetask(v=vs.105).aspx
[PhotoCamera]: http://msdn.microsoft.com/library/windowsphone/develop/microsoft.devices.photocamera(v=vs.105).aspx

<!--HONumber=42-->
