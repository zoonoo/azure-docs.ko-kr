<properties
	pageTitle="모바일 서비스를 사용하여 Blob 저장소에 이미지 업로드(Android) | 모바일 서비스"
	description="모바일 서비스를 사용하여 Azure 저장소에 이미지를 업로드하고 Android 앱에서 이미지에 액세스하는 방법에 대해 알아봅니다."
	services="mobile-services"
	documentationCenter="android"
	authors="RickSaling"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="java"
	ms.topic="article"
	ms.date="10/05/2015"
	ms.author="ricksal"/>

# Android 장치에서 Azure 저장소에 이미지 업로드

[AZURE.INCLUDE [mobile-services-selector-upload-data-blob-storage](../../includes/mobile-services-selector-upload-data-blob-storage.md)]

이 항목에서는 Azure 모바일 서비스 Android 앱을 활성화하여 Azure 저장소에 이미지를 업로드하는 방법을 보여줍니다.

모바일 서비스에서는 SQL 데이터베이스를 사용하여 데이터를 저장합니다. 그러나 이 Azure 저장소에 이진 대형 개체(BLOB) 데이터를 저장하는 것이 효율적입니다. 이 자습서에서는 모바일 서비스 빠른 시작 앱을 사용하여 Android 카메라로 사진을 찍고 Azure 저장소에 이미지를 업로드할 수 있습니다.


## 시작에 필요한 항목

이 자습서를 시작하기 전에 먼저 모바일 서비스 빠른 시작을 완료해야 합니다.[모바일 서비스 시작]

또한 이 자습서를 사용하려면 다음이 필요합니다.

+ [Azure 저장소 계정](../storage-create-storage-account.md)
+ 카메라와 Android 장치

## 앱 작동 방식

사진 이미지의 업로드는 다단계 프로세스입니다.

- 먼저 사진을 찍고 Azure 저장소에서 사용하는 새 메타 데이터 필드를 포함하는 SQL 데이터베이스에 TodoItem 행을 삽입합니다.
- 새 모바일 서비스 SQL **삽입** 스크립트는 SAS(공유 액세스 서명)에 Azure 저장소를 요청합니다.
- 해당 스크립트는 blob의 SAS 및 URI를 클라이언트에 반환합니다.
- 클라이언트는 SAS 및 blob URI를 사용하여 사진을 업로드합니다.

그렇다면 SAS는 무엇인가요?

클라이언트 앱 내에서 Azure 저장소 서비스에 데이터를 업로드하는 데 필요한 자격 증명을 저장하는 것은 안전하지 않습니다. 대신 이러한 자격 증명을 모바일 서비스에 저장하고, 이를 통해 새로운 이미지 업로드에 권한을 부여하는 SAS(공유 액세스 서명)를 생성해야 합니다. 만료 기간이 5분인 자격 증명, SAS는 모바일 서비스에 의해 클라이언트 앱으로 안전하게 반환됩니다. 그러면 앱은 이 임시 자격 증명을 사용하여 이미지를 업로드합니다. 자세한 내용은 [공유 액세스 서명, 1부: SAS 모델 이해](storage-dotnet-shared-access-signature-part-1.md)를 참조하세요.

## 코드 샘플
[여기](https://github.com/Azure/mobile-services-samples/tree/master/UploadImages)는 해당 앱의 완료된 클라이언트 원본 코드 부분입니다. 실행하려면 이 자습서의 모바일 서비스 백 엔드 부분을 완료해야 합니다.

## 관리 포털에서 등록된 삽입 스크립트 업데이트

[AZURE.INCLUDE [mobile-services-configure-blob-storage](../../includes/mobile-services-configure-blob-storage.md)]


## 빠른 시작 앱을 업데이트하여 이미지를 캡처 및 업로드합니다.

### Azure 저장소 Android 클라이언트 라이브러리 참조

1. 라이브러리에 참조를 추가하려면 **앱** > **build.gradle** 파일에서 `dependencies` 섹션에 이 줄을 추가합니다.

		compile 'com.microsoft.azure.android:azure-storage-android:0.6.0@aar'


2. `minSdkVersion` 값을 15로 변경합니다.(카메라 API에 필요함)

3. **Gradle 파일과 프로젝트 동기화** 아이콘을 누릅니다.

### 카메라 및 저장소에 대한 매니페스트 파일을 업데이트합니다.

1. 이 줄을 **AndroidManifest.xml**에 추가하여 카메라를 사용할 수 있어야 하는 앱을 지정합니다.

	    <uses-feature android:name="android.hardware.camera"
	        android:required="true" />

2. 이 줄을 **AndroidManifest.xml**에 추가하여 외부 저장소에 쓸 수 있는 권한이 필요한 앱을 지정합니다.

	    <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />

	Android 외부 저장소에 반드시 SD 카드가 필요하지는 않습니다. 자세한 것은 [파일 저장](http://developer.android.com/training/basics/data-storage/files.html)을 참조하세요.

### 새로운 사용자 인터페이스에 대한 리소스 파일을 업데이트 합니다.

1. 다음을 *값* 디렉터리의 **strings.xml** 파일에 추가하여 새 단추에 제목을 추가합니다.

	    <string name="preview_button_text">Take Photo</string>
	    <string name="upload_button_text">Upload</string>

2. **res => layout** 폴더의 **activity\_to\_do.xml** 파일에서 **추가** 단추의 기존 코드 앞에 이 코드를 추가합니다.

         <Button
             android:id="@+id/buttonPreview"
             android:layout_width="120dip"
             android:layout_height="wrap_content"
             android:onClick="takePicture"
             android:text="@string/preview_button_text" />

3. **추가** 단추 요소를 다음 코드로 대체합니다.

         <Button
             android:id="@+id/buttonUpload"
             android:layout_width="100dip"
             android:layout_height="wrap_content"
             android:onClick="uploadPhoto"
             android:text="@string/upload_button_text" />


### 사진 캡처에 코드를 추가합니다.

1. **ToDoActivity.java**에서 이 코드를 추가하여 고유 이름을 가진 **파일** 개체를 만듭니다.

		// Create a File object for storing the photo
	    private File createImageFile() throws IOException {
	        // Create an image file name
	        String timeStamp = new SimpleDateFormat("yyyyMMdd_HHmmss").format(new Date());
	        String imageFileName = "JPEG_" + timeStamp + "_";
	        File storageDir = Environment.getExternalStoragePublicDirectory(Environment.DIRECTORY_PICTURES);
	        File image = File.createTempFile(
	                imageFileName,  /* prefix */
	                ".jpg",         /* suffix */
	                storageDir      /* directory */
	        );
	        return image;
	    }

5. 이 코드를 추가하여 Android 카메라 앱을 시작합니다. 사진을 찍은 다음 사진이 괜찮아 보이는 경우 **저장** 키를 누르면 방금 만든 파일에 저장합니다.

		// Run an Intent to start up the Android camera
	    static final int REQUEST_TAKE_PHOTO = 1;
	    public Uri mPhotoFileUri = null;
	    public File mPhotoFile = null;

	    public void takePicture(View view) {
	        Intent takePictureIntent = new Intent(MediaStore.ACTION_IMAGE_CAPTURE);
	        // Ensure that there's a camera activity to handle the intent
	        if (takePictureIntent.resolveActivity(getPackageManager()) != null) {
	            // Create the File where the photo should go
	            try {
	                mPhotoFile = createImageFile();
	            } catch (IOException ex) {
	                // Error occurred while creating the File
	                //
	            }
	            // Continue only if the File was successfully created
	            if (mPhotoFile != null) {
	                mPhotoFileUri = Uri.fromFile(mPhotoFile);
	                takePictureIntent.putExtra(MediaStore.EXTRA_OUTPUT, mPhotoFileUri);
	                startActivityForResult(takePictureIntent, REQUEST_TAKE_PHOTO);
	            }
	        }
	    }

### 이 코드를 추가하여 사진 파일을 Blob 저장소로 업로드합니다.


1. 이 코드를 **ToDoItem.java**에 추가하여 먼저 새 메타 데이터 필드를 `ToDoItem` 개체로 추가합니다.

		/**
	     *  imageUri - points to location in storage where photo will go
	     */
	    @com.google.gson.annotations.SerializedName("imageUri")
	    private String mImageUri;

	    /**
	     * Returns the item ImageUri
	     */
	    public String getImageUri() {
	        return mImageUri;
	    }

	    /**
	     * Sets the item ImageUri
	     *
	     * @param ImageUri
	     *            Uri to set
	     */
	    public final void setImageUri(String ImageUri) {
	        mImageUri = ImageUri;
	    }

	    /**
	     * ContainerName - like a directory, holds blobs
	     */
	    @com.google.gson.annotations.SerializedName("containerName")
	    private String mContainerName;

	    /**
	     * Returns the item ContainerName
	     */
	    public String getContainerName() {
	        return mContainerName;
	    }

	    /**
	     * Sets the item ContainerName
	     *
	     * @param ContainerName
	     *            Uri to set
	     */
	    public final void setContainerName(String ContainerName) {
	        mContainerName = ContainerName;
	    }

	    /**
	     *  ResourceName
	     */
	    @com.google.gson.annotations.SerializedName("resourceName")
	    private String mResourceName;

	    /**
	     * Returns the item ResourceName
	     */
	    public String getResourceName() {
	        return mResourceName;
	    }

	    /**
	     * Sets the item ResourceName
	     *
	     * @param ResourceName
	     *            Uri to set
	     */
	    public final void setResourceName(String ResourceName) {
	        mResourceName = ResourceName;
	    }

	    /**
	     *  SasQueryString - permission to write to storage
	     */
	    @com.google.gson.annotations.SerializedName("sasQueryString")
	    private String mSasQueryString;

	    /**
	     * Returns the item SasQueryString
	     */
	    public String getSasQueryString() {
	        return mSasQueryString;
	    }

	    /**
	     * Sets the item SasQueryString
	     *
	     * @param SasQueryString
	     *            Uri to set
	     */
	    public final void setSasQueryString(String SasQueryString) {
	        mSasQueryString = SasQueryString;
	    }

2. 0-매개 변수 생성자를 이 코드로 대체합니다.

	    /**
	     * ToDoItem constructor
	     */
	    public ToDoItem() {
	        mContainerName = "";
	        mResourceName = "";
	        mImageUri = "";
	        mSasQueryString = "";
	    }

3. 여러 매개 변수 생성자를 이 코드로 대체합니다.

	    /**
	     * Initializes a new ToDoItem
	     *
	     * @param text
	     *            The item text
	     * @param id
	     *            The item id
	     */
	    public ToDoItem(String text,
	                    String id,
	                    String containerName,
	                    String resourceName,
	                    String imageUri,
	                    String sasQueryString) {
	        this.setText(text);
	        this.setId(id);
	        this.setContainerName(containerName);
	        this.setResourceName(resourceName);
	        this.setImageUri(imageUri);
	        this.setSasQueryString(sasQueryString);
	    }


4. **ToDoActivity.java** 파일에서 **ToDoActivity.java**의 **addItem** 메서드를 이미지를 업로드하는 다음 코드로 대체합니다.

	    /**
	     * Add a new item
	     *
	     * @param view
	     *            The view that originated the call
	     */
	    public void uploadPhoto(View view) {
	        if (mClient == null) {
	            return;
	        }

	        // Create a new item
	        final ToDoItem item = new ToDoItem();

	        item.setText(mTextNewToDo.getText().toString());
	        item.setComplete(false);
	        item.setContainerName("todoitemimages");

	        // Use a unigue GUID to avoid collisions.
	        UUID uuid = UUID.randomUUID();
	        String uuidInString = uuid.toString();
	        item.setResourceName(uuidInString);

	        // Send the item to be inserted. When blob properties are set this
	        // generates an SAS in the response.
	        AsyncTask<Void, Void, Void> task = new AsyncTask<Void, Void, Void>(){
	            @Override
	            protected Void doInBackground(Void... params) {
	                try {
		                    final ToDoItem entity = addItemInTable(item);

		                    // If we have a returned SAS, then upload the blob.
		                    if (entity.getSasQueryString() != null) {

	                       // Get the URI generated that contains the SAS
	                        // and extract the storage credentials.
	                        StorageCredentials cred =
								new StorageCredentialsSharedAccessSignature(entity.getSasQueryString());
	                        URI imageUri = new URI(entity.getImageUri());

	                        // Upload the new image as a BLOB from a stream.
	                        CloudBlockBlob blobFromSASCredential =
	                                new CloudBlockBlob(imageUri, cred);

	                        blobFromSASCredential.uploadFromFile(mPhotoFileUri.getPath());
  	                    }

	                    runOnUiThread(new Runnable() {
	                        @Override
	                        public void run() {
	                            if(!entity.isComplete()){
	                                mAdapter.add(entity);
	                            }
	                        }
	                    });
	                } catch (final Exception e) {
	                    createAndShowDialogFromTask(e, "Error");
	                }
	                return null;
	            }
	        };

	        runAsyncTask(task);

	        mTextNewToDo.setText("");
	    }


이 코드는 모바일 서비스에 새 TodoItem을 삽입하기 위한 요청을 보냅니다. 응답은 로컬 저장소에서 Azure 저장소의 Blob로 이미지를 업로드하는 데 사용되는 SAS를 포함하고 있습니다.


## 이미지 업로드 테스트

1. Android Studio에서 **실행**을 누릅니다. 대화 상자에서 사용할 장치를 선택합니다.

2. 앱 UI가 표시되면 **할 일 항목 추가**라는 텍스트 상자에 텍스트를 입력합니다.

3. **사진 촬영**을 누릅니다. 카메라 앱이 시작되면 사진을 찍습니다. 확인 표시를 클릭하여 사진을 허용합니다.

4. **업로드**를 누릅니다. 어떻게 ToDoItem이 평소 대로 목록에 추가되었는지 확인합니다.

5. Microsoft Azure 포털에서 저장소 계정으로 이동하고 **컨테이너** 탭을 누근 다음 목록에서 컨테이너의 이름을 누릅니다.

6. 업로드된 Blob 파일의 목록이 표시됩니다. 하나를 선택하고 **다운로드**를 누릅니다.

7. 이제 업로드된 이미지가 브라우저 창에 표시됩니다.


## <a name="next-steps"> </a>다음 단계

이제 모바일 서비스를 Blob 서비스와 통합하여 이미지를 안전하게 업로드할 수 있게 되었으므로 몇 가지 기타 백엔드 서비스 및 통합 항목을 확인해보십시오.

+ [SendGrid로 모바일 서비스에서 메일 보내기]

  SendGrid 전자 메일 서비스를 사용해 모바일 서비스에 전자 메일 기능을 추가하는 방법에 대해 알아보십시오. 이 항목에서는 SendGrid를 사용해 전자 메일을 보내기 위해 서버 쪽 스크립트를 추가하는 방법에 대해 설명합니다.

+ [모바일 서비스에서 백 엔드 작업 예약]

  모바일 서비스 작업 스케줄러 기능을 사용하여, 예약된 시간에 실행되는 서버 스크립트 코드를 정의하는 방법에 대해 알아보십시오.

+ [모바일 서비스 서버 스크립트 참조]

  서버 스크립트를 사용하여 서버 쪽 작업을 수행하고 기타 Azure 구성 요소 및 외부 리소스와 통합하는 방법을 안내하는 참조 항목입니다.

+ [모바일 서비스 .NET 방법 개념 참조]

  모바일 서비스를 .NET과 함께 사용하는 방법에 대해 알아보십시오.


<!-- Anchors. -->
[Install the Storage Client library]: #install-storage-client
[Update the client app to capture images]: #add-select-images
[Update the insert script to generate an SAS]: #update-scripts
[Upload images to test the app]: #test
[Next Steps]: #next-steps

<!-- Images. -->

[2]: ./media/mobile-services-windows-store-dotnet-upload-data-blob-storage/mobile-add-storage-nuget-package-dotnet.png


<!-- URLs. -->
[SendGrid로 모바일 서비스에서 메일 보내기]: store-sendgrid-mobile-services-send-email-scripts.md
[모바일 서비스에서 백 엔드 작업 예약]: mobile-services-schedule-recurring-tasks.md
[Send push notifications to Windows Store apps using Service Bus from a .NET back-end]: http://go.microsoft.com/fwlink/?LinkId=277073&clcid=0x409
[모바일 서비스 서버 스크립트 참조]: mobile-services-how-to-use-server-scripts.md
[모바일 서비스 시작]: mobile-services-javascript-backend-windows-store-dotnet-get-started.md

[Azure Management Portal]: https://manage.windowsazure.com/
[How To Create a Storage Account]: ../storage-create-storage-account.md
[Azure Storage Client library for Store apps]: http://go.microsoft.com/fwlink/p/?LinkId=276866
[모바일 서비스 .NET 방법 개념 참조]: mobile-services-windows-dotnet-how-to-use-client-library.md
[App settings]: http://msdn.microsoft.com/library/windowsazure/b6bb7d2d-35ae-47eb-a03f-6ee393e170f7

<!---HONumber=Oct15_HO2-->