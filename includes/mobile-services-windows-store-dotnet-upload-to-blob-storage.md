##<a name="add-select-images"></a>퀵 스타트 클라이언트 앱을 업데이트하여 이미지 캡처 및 업로드

1. Visual Studio 2012에서 Package.appxmanifest 파일을 열고 **기능** 탭에서 **웹캠** 및 **마이크** 기능을 사용하도록 설정합니다.

   	![](./media/mobile-services-windows-store-dotnet-upload-to-blob-storage/mobile-app-manifest-camera.png)
 
   	이제 앱에서 컴퓨터에 연결된 카메라를 사용할 수 있습니다. 앱을 처음으로 실행하면 카메라 액세스를 허용하라는 메시지가 표시됩니다.

1. MainPage.xaml 파일을 열어 첫 번째 **Task** 요소 바로 다음에 있는 **StackPanel** 요소를 다음 코드로 바꿉니다.

        <StackPanel Orientation="Horizontal" Margin="72,0,0,0">
            <TextBox Name="TextInput" Margin="5" MaxHeight="40" MinWidth="300"></TextBox>
            <Button Name="btnTakePhoto" Style="{StaticResource PhotoAppBarButtonStyle}"
                    Click="OnTakePhotoClick" />
            <Button Name="ButtonSave" Style="{StaticResource UploadAppBarButtonStyle}" 
                    Click="ButtonSave_Click"/>
        </StackPanel>

2. **DataTemplate**의 **StackPanel** 요소를 다음 코드로 바꿉니다.

        <StackPanel Orientation="Vertical">
            <CheckBox Name="CheckBoxComplete" IsChecked="{Binding Complete, Mode=TwoWay}" 
                        Checked="CheckBoxComplete_Checked" Content="{Binding Text}" 
                        Margin="10,5" VerticalAlignment="Center"/>
            <Image Name="ImageUpload" Source="{Binding ImageUri, Mode=OneWay}"
                    MaxHeight="250"/>
        </StackPanel> 

   	이 코드는 이미지를 **ItemTemplate**에 추가하고 바인딩 소스를 Blob 저장소 서비스의 업로드된 이미지 URI로 설정합니다.

3. MainPage.xaml.cs 프로젝트 파일을 열고 다음 **using** 문을 추가합니다.
	
		using Windows.Media.Capture;
		using Windows.Storage;
		using Windows.UI.Popups;
		using Microsoft.WindowsAzure.Storage.Auth;
		using Microsoft.WindowsAzure.Storage.Blob;
    
4. TodoItem 클래스에 다음 속성을 추가합니다.

        [JsonProperty(PropertyName = "containerName")]
        public string ContainerName { get; set; }
		
        [JsonProperty(PropertyName = "resourceName")]
        public string ResourceName { get; set; }
		
        [JsonProperty(PropertyName = "sasQueryString")]
        public string SasQueryString { get; set; }
		
        [JsonProperty(PropertyName = "imageUri")]
        public string ImageUri { get; set; } 

   	>[AZURE.NOTE]TodoItem 개체에 새 속성을 추가하려면 모바일 서비스에서 동적 스키마를 사용하도록 설정해야 합니다. 동적 스키마를 사용하도록 설정한 경우 새 속성에 매핑된 새 열이 TodoItem 테이블에 자동으로 추가됩니다.

5. MainPage 클래스에 다음 코드를 추가합니다.

        // Use a StorageFile to hold the captured image for upload.
        StorageFile media = null;
		
		private async void OnTakePhotoClick(object sender, RoutedEventArgs e)
		{
			// Capture a new photo or video from the device.
			CameraCaptureUI cameraCapture = new CameraCaptureUI();
			media = await cameraCapture
				.CaptureFileAsync(CameraCaptureUIMode.PhotoOrVideo);
        }

  	이 코드는 이미지를 캡처하고 저장소 파일에 해당 이미지를 저장하는 카메라 UI를 표시합니다.

6. 기존 `InsertTodoItem` 메서드를 다음 코드로 바꿉니다.
 
        private async void InsertTodoItem(TodoItem todoItem)
        {
            string errorString = string.Empty;
			
            if (media != null)
            {
                // Set blob properties of TodoItem.
                todoItem.ContainerName = "todoitemimages";
                todoItem.ResourceName = media.Name;
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

                // Get the new image as a stream.
                using (var fileStream = await media.OpenStreamForReadAsync())
                {                   					
                    // Upload the new image as a BLOB from the stream.
                    CloudBlockBlob blobFromSASCredential =
                        container.GetBlockBlobReference(todoItem.ResourceName);
                    await blobFromSASCredential.UploadFromStreamAsync(fileStream.AsInputStream());
                }
				
				// When you request an SAS at the container-level instead of the blob-level,
				// you are able to upload multiple streams using the same container credentials.
            }
			
            // Add the new item to the collection.
            items.Add(todoItem);
        }

	이 코드는 요청을 모바일 서비스로 전송하여 이미지 파일 이름을 비롯한 새 TodoItem을 삽입합니다. 응답에는 SAS가 포함되며 이는 Blob 저장소의 이미지 및 데이터 바인딩의 이미지 URI를 삽입하는 데 사용됩니다.

마지막 단계로, 앱을 테스트하고 제대로 업로드되었는지 확인합니다.
		
##<a name="test"></a>앱에서 이미지 업로드 테스트

1. Visual Studio에서 F5 키를 눌러 앱을 실행합니다.

2. **Insert a TodoItem**의 텍스트 상자에 텍스트를 입력한 후 **Photo**를 클릭합니다.

   	![](./media/mobile-services-windows-store-dotnet-upload-to-blob-storage/mobile-quickstart-blob-appbar.png)

  	그러면 카메라 캡처 UI가 표시됩니다.

3. 이미지를 클릭하여 사진을 찍은 후 **OK**를 클릭합니다.
  
   	![](./media/mobile-services-windows-store-dotnet-upload-to-blob-storage/mobile-quickstart-blob-camera.png)

4. **Upload**를 클릭하여 새 항목을 삽입하고 이미지를 업로드합니다.

	![](./media/mobile-services-windows-store-dotnet-upload-to-blob-storage/mobile-quickstart-blob-appbar2.png)

5. 새 항목이 업로드된 이미지와 함께 목록 보기에 표시됩니다.

	![](./media/mobile-services-windows-store-dotnet-upload-to-blob-storage/mobile-quickstart-blob-ie.png)

   	>[AZURE.NOTE]이미지는 새 항목의 <code>imageUri</code> 속성이 <strong>이미지</strong> 컨트롤에 연결될 때 Blob 저장소 서비스에서 자동으로 다운로드됩니다.

<!---HONumber=62-->