<properties 
	pageTitle="미디어 서비스 사용 방법(.NET) - Azure 기능 가이드" 
	description="Azure 미디어 서비스를 사용하여 리소스 인코딩, 암호화, 스트리밍 등의 일반적인 작업을 수행하는 방법에 대해 설명합니다." 
	services="media-services" 
	documentationCenter="java" 
	authors="rmcmurray" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/20/2015" 
	ms.author="robmcm"/>

# 미디어 서비스 사용 방법

이 가이드에서는 Java를 사용하여 Azure 미디어 서비스로 프로그래밍을 시작하는 방법을 보여 줍니다. 이 가이드에는 미디어 서비스의 기술 개요, 미디어 서비스를 위해 Azure 계정을 구성하는 단계 및 일반적인 프로그래밍 작업을 수행하는 방법을 보여 주는 코드가 포함되어 있습니다. 

## <a id="media_services"></a>미디어 서비스 정의

Azure 미디어 서비스는 Microsoft Media Platform 및 타사 미디어 구성 요소의 장점을 통합하는 확장 가능한 미디어 플랫폼을 Azure에 구성합니다. 미디어 서비스는 업계 파트너가 구성 요소 기술을 확장하거나 대체할 수 있는 미디어 파이프라인을 클라우드에 제공합니다. ISV 및 미디어 공급자는 미디어 서비스를 사용하여 종단 간 미디어 솔루션을 빌드할 수 있습니다. 이 개요에서는 미디어 서비스에 대한 일반적인 아키텍처 및 개발 시나리오에 대해 설명합니다.

다음 다이어그램은 기본 미디어 서비스 아키텍처를 보여 줍니다.

![미디어 서비스 아키텍처](./media/media-services-java-how-to-use/wams-01.png)

### 미디어 서비스 기능 지원
최신 미디어 서비스 릴리스는 클라우드에서 미디어 응용 프로그램을 개발하기 위해 다음 기능 집합을 제공합니다. 

- **수집**. 수집 작업은 자산을 업로드하고 Azure 저장소에 저장되기 전에 암호화하여 자산을 시스템으로 가져옵니다. 미디어 서비스는 RTM 릴리스에서 파트너 구성 요소와 통합되어 빠른 UDP(User Datagram Protocol) 업로드 솔루션을 제공할 예정입니다.
- **인코딩**. 인코딩 작업에는 미디어 자산 인코딩, 변형 및 변환이 포함됩니다. 미디어 서비스에 포함되어 있는 미디어 인코더를 사용하여 클라우드에서 인코딩 작업을 실행할 수 있습니다. 인코딩 옵션은 다음과 같습니다.
   - Azure 미디어 인코더를 사용하고 업계 최고의 IIS 부드러운 스트리밍, MP4, Apple HTTP 라이브 스트리밍으로 변환 등의 다양한 표준 코덱 및 형식으로 작업
   - 입력과 출력을 총체적으로 제어하여 전체 라이브러리 또는 개별 파일 변환
   - 지원되는 다양한 파일 형식, 형식 및 코덱 집합([미디어 서비스에 대해 지원되는 파일 형식][] 참조)
   - 지원되는 형식 변환. 미디어 서비스를 통해 ISO MP4(.mp4)를 부드러운 스트리밍 파일 형식(PIFF 1.3)(.ismv; .isma)으로 변환할 수 있습니다. 부드러운 스트리밍 파일 형식(PIFF)을 Apple HTTP 라이브 스트리밍(.msu8, .ts)으로 변환할 수도 있습니다.
- **보호**. 콘텐츠 보호는 보안 전송, 저장소 및 전달을 위해 라이브 스트리밍 또는 주문형 콘텐츠를 암호화하는 작업입니다. 미디어 서비스는 콘텐츠 보호를 위해 DRM 기술을 알 수 없는 솔루션을 제공합니다.  현재 지원되는 DRM 기술은 Microsoft PlayReady Protection 및 MPEG Common Encryption입니다. 추가 DRM 기술도 지원될 예정입니다. 
- **스트림**. 콘텐츠 스트리밍은 라이브 또는 주문형으로 콘텐츠를 클라이언트에 보내는 작업입니다. 또는 클라우드에서 특정 미디어 파일을 가져오거나 다운로드할 수 있습니다. 미디어 서비스는 콘텐츠 스트리밍을 위해 형식을 알 수 없는 솔루션을 제공합니다.  미디어 서비스는 스트리밍과 관련해서 부드러운 스트리밍, Apple HTTP 라이브 스트리밍 및 MP4 형식을 지원합니다. 추가 형식도 지원될 예정입니다. 수백만 명의 사용자로 확장하는 옵션을 지원하는 Azure CDN 또는 타사 CDN을 사용하여 스트리밍 콘텐츠를 매끄럽게 제공할 수도 있습니다.   

### 미디어 서비스 개발 시나리오
미디어 서비스는 다음 표의 설명과 같이 여러 가지 일반적인 미디어 개발 시나리오를 지원합니다. 
<table border="2" cellspacing="0" cellpadding="5" style="border: 2px solid #000000;">
  <thead>
    <tr>
       <th>시나리오</th>
       <th>설명</th>
    </tr>
  </thead>
  <tbody>
    <tr>
        <td>종단 간 워크플로 작성</td>
        <td>클라우드에서 포괄적인 미디어 워크플로 작성. 미디어 업로드에서 콘텐츠 배포에 이르기까지 미디어 서비스는 함께 결합되어 특정 응용 프로그램 워크플로를 처리할 수 있는 다양한 구성 요소를 제공합니다. 현재 기능에는 업로드, 저장소, 인코딩, 형식 변환, 콘텐츠 보호, 주문형 스트리밍 전달 등이 포함됩니다.</td>
    </tr>
    <tr>
        <td>하이브리드 워크플로 작성</td>
        <td>미디어 서비스와 기존 도구 및 프로세스를 통합할 수 있습니다. 예를 들어 현장에서 콘텐츠를 인코딩한 후 여러 형식으로 트랜스코딩하고 Azure CDN 또는 타사 CDN을 통해 전달하기 위해 미디어 서비스에 업로드합니다. 외부 응용 프로그램 및 서비스와 통합하기 위해 표준 REST API를 통해 미디어 서비스를 개별적으로 호출할 수 있습니다.</td>
    </tr>
    <tr>
        <td>미디어 플레이어에 대한 클라우드 지원 제공</td>
        <td>여러 장치(iOS, Android 및 Windows 장치 포함)와 플랫폼에서 미디어를 만들고 관리 및 제공할 수 있습니다.</td>
    </tr>
  </tbody>
</table>

### 미디어 서비스 클라이언트 개발
SDK 및 플레이어 프레임워크를 통해 미디어 클라이언트 응용 프로그램을 빌드하여 미디어 서비스 솔루션의 도달 범위를 확장합니다. 이러한 클라이언트는 다양한 장치와 플랫폼에서 매력적인 사용자 환경을 제공하는 미디어 서비스 응용 프로그램을 빌드하려는 개발자를 위한 것입니다. 빌드하려는 클라이언트 응용 프로그램을 사용할 장치에 따라, Microsoft 및 타사 파트너가 제공하는 SDK 및 플레이어 프레임워크에 대한 옵션을 사용할 수 있습니다.  

사용 가능한 클라이언트 SDK 및 플레이어 프레임워크 목록은 다음과 같습니다.  이러한 SDK 및 플레이어 프레임워크와 기타 계획된 SDK 및 플레이어 프레임워크, 지원할 수 있는 기능에 대한 자세한 내용은 [미디어 서비스 클라이언트 개발](영문)을 참조하세요. 

#### Mac 및 PC 클라이언트 지원  
PC 및 Mac의 경우 Microsoft Silverlight 또는 Adobe Open Source Media Framework를 사용하여 스트리밍 환경을 대상으로 지정할 수 있습니다.

-	[Silverlight용 부드러운 스트리밍 클라이언트](http://www.iis.net/download/smoothclient)
-	[Microsoft Media Platform: Silverlight용 플레이어 프레임워크](http://smf.codeplex.com/documentation)
-	[OSMF용 부드러운 스트리밍 플러그 인 2.0](http://go.microsoft.com/fwlink/?LinkId=275022). 이 플러그 인을 사용하는 방법에 대한 자세한 내용은 [Adobe Open Source Media Framework용 부드러운 스트리밍 플러그 인을 사용하는 방법](http://go.microsoft.com/fwlink/?LinkId=275034)(영문)을 참조하세요.

#### Windows 8 응용 프로그램
Windows 8의 경우 HTML, Javascript, XAML, C#, C+ 등의 지원되는 개발 언어와 구문을 사용하여 Windows 스토어 응용 프로그램을 빌드할 수 있습니다.

-	[Smooth Streaming Client SDK for Windows 8](http://go.microsoft.com/fwlink/?LinkID=246146). 이 SDK를 사용하여 Windows 스토어 응용 프로그램을 만드는 방법에 대한 자세한 내용은 [부드러운 스트리밍 Windows 스토어 응용 프로그램을 빌드하는 방법](http://go.microsoft.com/fwlink/?LinkId=271647)(영문)을 참조하세요. HTML5로 부드러운 스트리밍 플레이어를 만드는 방법에 대한 자세한 내용은 [연습: 첫 번째 HTML5 부드러운 스트리밍 플레이어 빌드](http://msdn.microsoft.com/library/jj573656.aspx)(영문)를 참조하세요.

-	[Microsoft Media Platform: Windows 8 Windows 스토어 응용 프로그램용 플레이어 프레임워크](http://playerframework.codeplex.com/wikipage?title=Player%20Framework%20for%20Windows%208%20Metro%20Style%20Apps&referringTitle=Home)

#### Xbox
Xbox는 부드러운 스트리밍 콘텐츠를 사용할 수 있는 Xbox LIVE 응용 프로그램을 지원합니다. Xbox LIVE ADK(응용 프로그램 개발 키트)에는 다음 항목이 포함되어 있습니다.

-	Xbox LIVE ADK용 부드러운 스트리밍 클라이언트
-	Microsoft 미디어 플랫폼: Xbox LIVE ADK용 플레이어 프레임워크

#### 포함된 장치 또는 전용 장치
연결된 TV, 셋톱 박스, Blu-Ray 플레이어, OTT TV 박스, 모바일 장치 등 사용자 지정 응용 프로그램 개발 프레임워크 및 사용자 지정 미디어 파이프라인이 있는 장치. Microsoft는 사용이 허가될 수 있는 다음 포팅 키트를 제공하며 파트너가 플랫폼에 대한 부드러운 스트리밍 재생을 포팅할 수 있게 합니다.

-	[부드러운 스트리밍 클라이언트 포팅 키트](http://www.microsoft.com/mediaplatform/sspk.aspx)
-	[Microsoft PlayReady 장치 포팅 키트](http://www.microsoft.com/PlayReady/Licensing/device_technology.mspx)

#### Windows Phone
Microsoft는 Windows Phone용 프리미엄 비디오 응용 프로그램을 빌드하는 데 사용할 수 있는 SDK를 제공합니다. 

-	[Silverlight용 부드러운 스트리밍 클라이언트](http://www.iis.net/download/smoothclient)
-	[Microsoft Media Platform: Silverlight용 플레이어 프레임워크](http://smf.codeplex.com/documentation)

#### iOS 장치
iPhone, iPod 및 iPad를 비롯한 iOS 장치의 경우 Microsoft는 프리미엄 비디오 콘텐츠를 제공하기 위해 이러한 플랫폼용 응용 프로그램을 빌드하는 데 사용할 수 있는 SDK, 즉 Smooth Streaming SDK for iOS Devices with PlayReady를 제공합니다.  이 SDK는 라이선스 실시권자만 사용할 수 있습니다. 자세한 내용은 [Microsoft에 이메일로 문의](mailto:askdrm@microsoft.com)하세요. iOS 개발에 대한 자세한 내용은 [iOS 개발자 센터](https://developer.apple.com/devcenter/ios/index.action)를 참조하세요.

#### Android 장치
여러 Microsoft 파트너는 Android 장치에서 부드러운 스트리밍을 재생하는 기능을 추가하는 Android 플랫폼용 SDK를 제공합니다. 해당 파트너에 대한 자세한 내용은 [Microsoft에 이메일로 문의](mailto:sspkinfo@microsoft.com?subject=Partner%20SDKs%20for%20Android%20Devices)하세요.


## <a id="setup-account"></a>미디어 서비스용 Azure 계정 설정

미디어 서비스 계정을 설정하려면 Azure 관리 포털을 사용하세요. [미디어 서비스 계정을 만드는 방법][](영문)을 참조하세요. 관리 포털에서 계정을 만들면 미디어 서비스 개발을 위해 컴퓨터를 설정할 준비가 됩니다. 

## <a id="setup-dev"></a>미디어 서비스 개발을 위한 설정

이 섹션에는 Media Services SDK for Java를 사용하는 미디어 서비스 개발에 대한 일반적인 필수 조건이 들어 있습니다.

### 필수 조건

-   신규 또는 기존 Azure 구독의 미디어 서비스 계정. [미디어 서비스 계정을 만드는 방법][](영문)을 참조하세요.
-   [Azure Java 개발자 센터][](영문)에서 설치할 수 있는 Java용 Azure 라이브러리.

## <a if="connect"></a>방법: Java에서 미디어 서비스 사용

다음 코드에서는 자산을 만들고, 미디어 파일을 자산에 업로드하고, 자산 변환 태스크를 포함하는 작업을 실행하고, 변환된 자산의 출력 파일을 다운로드하는 방법을 보여 줍니다.

이 코드를 사용하기 전에 미디어 서비스 계정을 설정해야 합니다. 계정 설정에 대한 자세한 내용은 [미디어 서비스 계정을 만드는 방법](http://www.windowsazure.com/manage/services/media-services/how-to-create-a-media-services-account/)(영문)을 참조하세요.

 `clientId` 및 `clientSecret` 변수를 원하는 값으로 바꿉니다. 코드는 또한 로컬에 저장된 다음 파일에 따라 다릅니다. `c:/media/MPEG4-H264.mp4`. 사용할 자체 파일을 제공해야 합니다. 또한 이 코드에는 출력 파일이 다운로드되는 출력 폴더 `c:/output` 도 필요합니다.

	import java.io.*;
	import java.net.URI;
	import java.net.URISyntaxException;
	import java.security.NoSuchAlgorithmException;
	import java.util.EnumSet;
	import java.util.List;
	
	import com.microsoft.windowsazure.services.blob.client.*;
	import com.microsoft.windowsazure.services.core.Configuration;
	import com.microsoft.windowsazure.services.core.ServiceException;
	import com.microsoft.windowsazure.services.core.storage.StorageException;
	import com.microsoft.windowsazure.services.media.*;
	import com.microsoft.windowsazure.services.media.models.*;
	
	public class HelloMediaServices 
	{
	
	    private static MediaContract mediaService;
	    private static AssetInfo assetToEncode, encodedAsset;
	
	    public static void main(String[] args) 
	    {
	        try 
	        {
	
	            // Set up the MediaContract object to call into the media services.
	            Init();
	            
	            // Upload a local file to a media asset.
	            Upload();
	
	            // Transform the asset.
	            Transform();
	
	            // Retrieve the URL of the asset's transformed output.
	            Download();
	
	            // Delete all assets. 
	            // When you want to delete the assets that have been uploaded, 
	            // comment out the calls to Upload(), Transfer(), and Download(), 
	            // and uncomment the following call to Cleanup().
	            //Cleanup();
	
	            System.out.println("Application completed.");
	        }
	        catch (ServiceException se) 
	        {
	            System.out.println("ServiceException encountered.");
	            System.out.println(se.getMessage());
	        }
	        catch (Exception e) 
	        {
	            System.out.println("Exception encountered.");
	            System.out.println(e.getMessage());
	        }
	    }
	
	    // Initialize the server context to get programmatic access to the Media Services programming objects.
	    // The media services URI, OAuth URI and scope can be used exactly as shown.
	    // Substitute your media service account name and access key for the clientId and clientSecret variables.
	    // You can obtain your media service account name and access key from the Media Services section
	    // of the Azure Management portal, https://manage.windowsazure.com.
	    private static void Init() throws ServiceException 
	    {
	        String mediaServiceUri = "https://media.windows.net/API/";
	        String oAuthUri = "https://wamsprodglobal001acs.accesscontrol.windows.net/v2/OAuth2-13";
	        String clientId = "your_client_id";  // Use your media service account name.
	        String clientSecret = "your_client_secret"; // Use your media service access key. 
	        String scope = "urn:WindowsAzureMediaServices";
	
	        // Specify the configuration values to use with the MediaContract object.
	        Configuration configuration = MediaConfiguration
	                .configureWithOAuthAuthentication(mediaServiceUri, oAuthUri, clientId, clientSecret, scope);
	
	        // Create the MediaContract object using the specified configuration.
	        mediaService = MediaService.create(configuration);
	        
	    }
	
	    // Upload a media file to your Media Services account.
	    // This code creates an asset, an access policy (using Write access) and a locator, 
	    // and uses those objects to upload a local file to the asset.
	    private static void Upload() throws ServiceException, FileNotFoundException, NoSuchAlgorithmException 
	    {
	    	
	    	WritableBlobContainerContract uploader;
	    	
	    	AccessPolicyInfo uploadAccessPolicy;
	    	LocatorInfo uploadLocator = null;
	    	
	        // Create an asset.
	    	assetToEncode = mediaService.create(Asset.create().setName("myAsset").setAlternateId("altId"));
	        System.out.println("Created asset with id: " + assetToEncode.getId());
	
	        // Create an access policy that provides Write access for 15 minutes.
	        uploadAccessPolicy = mediaService.create(AccessPolicy.create("uploadAccessPolicy", 
	        		                                                     15.0, 
	        		                                                     EnumSet.of(AccessPolicyPermission.WRITE)));
	        System.out.println("Created upload access policy with id: "
	                + uploadAccessPolicy.getId());
	
	        // Create a locator using the access policy and asset.
	        // This will provide the location information needed to add files to the asset.
	        uploadLocator = mediaService.create(Locator.create(uploadAccessPolicy.getId(),
	        		assetToEncode.getId(), LocatorType.SAS));
	        System.out.println("Created upload locator with id: " + uploadLocator.getId());
	
	        // Create the blob writer using the locator.
	        uploader = mediaService.createBlobWriter(uploadLocator);
	
	        // The name of the file as it will exist in your Media Services account.
	        String fileName = "MPEG4-H264.mp4";  
	
	        // The local file that will be uploaded to your Media Services account.
	        InputStream input = new FileInputStream(new File("c:/media/" + fileName));
	
	        // Upload the local file to the asset.
	        uploader.createBlockBlob(fileName, input);
	
	        // Inform Media Services about the uploaded files.
	        mediaService.action(AssetFile.createFileInfos(assetToEncode.getId()));
	        System.out.println("File uploaded.");
	        
	       
	        System.out.println("Deleting upload locator and access policy.");
	        mediaService.delete(Locator.delete(uploadLocator.getId()));
	        mediaService.delete(AccessPolicy.delete(uploadAccessPolicy.getId()));
	        
	    }
	
	    // Create a job that contains a task to transform the asset.
	    // In this example, the asset will be transformed using the Azure Media Encoder.
	    private static void Transform() throws ServiceException, InterruptedException 
	    {
	        // Use the Azure Media Encoder, by specifying it by name.
	        // Retrieve the list of media processors that match this name.   	
	    	ListResult<MediaProcessorInfo> mediaProcessors = mediaService
	    			.list(MediaProcessor.list()
	    			.set("$filter", "Name eq 'Azure Media Encoder'"));
	    	
	    	// Use the latest version of the media processor.
	    	MediaProcessorInfo mediaProcessor = null;
	    	for (MediaProcessorInfo info : mediaProcessors)
	    	{
	    		if (null == mediaProcessor || info.getVersion().compareTo(mediaProcessor.getVersion()) > 0)
	    		{
	    			mediaProcessor = info;
	    		}
	    	}
	
	    	System.out.println("Using processor: " + mediaProcessor.getName() +
	    			" " + mediaProcessor.getVersion());
	
	        // Create a task with the specified media processor, in this case to transform the original asset to the H264 Broadband 720p preset.
	        // Information on the various configurations can be found at
	        // http://msdn.microsoft.com/library/windowsazure/jj129582.aspx.
	        // This example uses only one task, but others could be added.
	        Task.CreateBatchOperation task = Task.create(
	                mediaProcessor.getId(),
	                "<taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset>JobOutputAsset(0)</outputAsset></taskBody>")
	                .setConfiguration("H264 Broadband 720p").setName("MyTask");
	
	        // Create a job creator that specifies the asset, priority and task for the job. 
	        Job.Creator jobCreator = Job.create()
	            .setName("myJob")
	            .addInputMediaAsset(assetToEncode.getId())
	            .setPriority(2)
	            .addTaskCreator(task);
	
	        // Create the job within your Media Services account.
	        // Creating the job automatically schedules and runs it.
	        JobInfo jobInfo = mediaService.create(jobCreator);
	        String jobId = jobInfo.getId();
	        System.out.println("Created job with id: " + jobId);
	        // Check to see if the job has completed.
	        CheckJobStatus(jobId);
	        // Done with the job.
	
	        // Retrieve the output asset.
	        ListResult<AssetInfo> outputAssets = mediaService.list(Asset.list(jobInfo.getOutputAssetsLink()));
	        encodedAsset = outputAssets.get(0);
	    }
	
	    // Download the output assets of the transformed asset.
	    private static void Download() throws ServiceException, URISyntaxException, FileNotFoundException, StorageException, IOException 
	    {
	    	
	    	AccessPolicyInfo downloadAccessPolicy = null;
	
	        downloadAccessPolicy =
	                mediaService.create(AccessPolicy.create("Download", 15.0, EnumSet.of(AccessPolicyPermission.READ)));
	        System.out.println("Created download access policy with id: "
	                + downloadAccessPolicy.getId());
	    	
	    	LocatorInfo downloadLocator = null;
	        downloadLocator = mediaService.create(
	        		Locator.create(downloadAccessPolicy.getId(), encodedAsset.getId(), LocatorType.SAS));
	        System.out.println("Created download locator with id: " + downloadLocator.getId());        
	
	        System.out.println("Accessing the output files of the encoded asset.");
	        // Iterate through the files associated with the encoded asset.
	        for(AssetFileInfo assetFile: mediaService.list(AssetFile.list(encodedAsset.getAssetFilesLink())))
	        {
	            String fileName = assetFile.getName();
	            
	            System.out.print("Downloading file: " + fileName + ". ");
	            String locatorPath = downloadLocator.getPath();
	            int startOfSas = locatorPath.indexOf("?");
	
	            String blobPath = locatorPath + fileName;
	            if (startOfSas >= 0) 
	            {
	                blobPath = locatorPath.substring(0, startOfSas) + "/" + fileName + locatorPath.substring(startOfSas);
	            }
	            URI baseuri = new URI(blobPath);
	            CloudBlobClient blobClient;
	            blobClient = new CloudBlobClient(baseuri);
	            
	            // Ensure that you have a c:\output folder, or modify the path specified in the following statement.
	            String localFileName = "c:/output/" + fileName;
	            
	            CloudBlockBlob sasBlob;
	            sasBlob = new CloudBlockBlob(baseuri, blobClient);
	            File fileTarget = new File(localFileName);
	            
	            sasBlob.download(new FileOutputStream(fileTarget));
	            System.out.println("Download complete.");
	            
	        }
	
	        System.out.println("Deleting download locator and access policy.");
	        mediaService.delete(Locator.delete(downloadLocator.getId()));
	        mediaService.delete(AccessPolicy.delete(downloadAccessPolicy.getId()));
	      
	    }
	    
	    // Remove all assets from your Media Services account.
	    // You could instead remove assets by name or ID, etc., but for 
	    // simplicity this example removes all of them.
	    private static void Cleanup() throws ServiceException 
	    {
	        // Retrieve a list of all assets.
	        List<AssetInfo> assets = mediaService.list(Asset.list());
	
	        // Iterate through the list, deleting each asset.
	        for (AssetInfo asset: assets)
	        {
	        	System.out.println("Deleting asset named " + asset.getName() + " (" + asset.getId() + ")");
	            mediaService.delete(Asset.delete(asset.getId()));
	        }
	    }
	
	    // Helper function to check to on the status of the job.
	    private static void CheckJobStatus(String jobId) throws InterruptedException, ServiceException
	    {
	        int maxRetries = 12; // Number of times to retry. Small jobs often take 2 minutes.
	        JobState jobState = null;
	        while (maxRetries > 0) 
	        {
	            Thread.sleep(10000);  // Sleep for 10 seconds, or use another interval.
	            // Determine the job state.
	            jobState = mediaService.get(Job.get(jobId)).getState();
	            System.out.println("Job state is " + jobState);
	
	            if (jobState == JobState.Finished || 
	                jobState == JobState.Canceled || 
	                jobState == JobState.Error) 
	            {
	                // The job is done.
	                break;
	            }
	            // The job is not done. Sleep and loop if max retries 
	            // has not been reached.
	            maxRetries--;
	        }
	  
	    }
	
	}

만드는 자산은 Azure 저장소에 저장됩니다. 그렇지만 Azure 미디어 서비스 API(Azure 저장소 API가 아님)만 사용하여 자산을 추가하거나 업데이트하거나 삭제하도록 합니다.

### 사용할 수 있는 미디어 프로세서 확인

위의 코드에서는 특정 미디어 프로세서 이름을 통해 액세스하여 미디어 프로세서를 사용했습니다. 둘 이상의 버전이 있으면 마지막 버전이 사용됩니다. 사용할 수 있는 미디어 프로세서를 확인하려면 다음 코드를 사용할 수 있습니다.

    for (MediaProcessorInfo mediaProcessor:  mediaService.list(MediaProcessor.list()))
    {
        System.out.print(mediaProcessor.getName() + ", ");
        System.out.print(mediaProcessor.getId() + ", ");  
        System.out.print(mediaProcessor.getVendor() + ", ");  
        System.out.println(mediaProcessor.getVersion());  
    }

또는 다음 코드에 미디어 프로세서의 ID를 이름으로 검색하는 방법이 나와 있습니다.

    String mediaProcessorName = "Storage Decryption"; 
    EntityListOperation<MediaProcessorInfo> operation;
    MediaProcessorInfo processor;

    operation = MediaProcessor.list();
    operation.getQueryParameters().putSingle("$filter", "Name eq '" + mediaProcessorName + "'");
    processor = mediaService.list(operation).get(0); 
    System.out.println("Processor named " + mediaProcessorName + 
                       " has ID of " + processor.getId());

### 작업 취소
처리가 끝나지 않은 작업을 취소해야 할 경우 다음 코드에 작업 ID별로 작업을 취소하는 방법이 나와 있습니다.

    mediaService.action(Job.cancel(jobId));

## 추가 리소스

미디어 서비스 Javadoc 설명서는 [Java용 Azure 라이브러리 설명서][](영문)를 참조하세요.

<!-- URLs. -->

  [미디어 서비스 계정을 만드는 방법]: http://go.microsoft.com/fwlink/?linkid=256662
  [Azure Java 개발자 센터]: http://www.windowsazure.com/develop/java/
  [Java용 Azure 라이브러리 설명서]: http://dl.windowsazure.com/javadoc/
  [미디어 서비스 클라이언트 개발]: http://msdn.microsoft.com/library/windowsazure/dn223283.aspx



<!--HONumber=52--> 