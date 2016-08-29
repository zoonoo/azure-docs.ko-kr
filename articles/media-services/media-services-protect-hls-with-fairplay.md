<properties 
	pageTitle="Azure 미디어 서비스를 사용하여 Apple FairPlay로 보호되는 HLS 콘텐츠 스트리밍" 
	description="이 항목에서는 Azure 미디어 서비스를 사용하여 Apple FairPlay에서 HLS(HTTP 라이브 스트리밍) 콘텐츠를 동적으로 암호화하는 방법과 개요를 설명합니다. 또한 미디어 서비스 라이선스 배달 서비스를 사용하여 클라이언트에 FairPlay 라이선스를 제공하는 방법을 보여 줍니다." 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako" 
	manager="erikre" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/15/2016"
	ms.author="juliako"/>

#Azure 미디어 서비스를 사용하여 Apple FairPlay로 보호되는 HLS 콘텐츠 스트리밍 

Azure 미디어 서비스를 사용하여 다음 형식으로 HLS(HTTP 라이브 스트리밍) 콘텐츠를 동적으로 암호화할 수 있습니다.

- **AES-128 봉투 암호화되지 않은 키** - 전체 청크가 **AES-128 CBC** 모드로 암호화됩니다. 스트림의 암호 해독은 iOS 및 OSX 플레이어에서 고유하게 지원됩니다. 자세한 내용은 [이 문서](media-services-protect-with-aes128.md)(영문)를 읽어보세요.

- **Apple FairPlay** - 개별 비디오 및 오디오 샘플이 **AES-128 CBC** 모드로 암호화됩니다. **FairPlay 스트리밍**(FPS)은 장치 운영 체제에 통합되며, iOS 및 Apple TV에서 고유하게 지원됩니다. OS X의 Safari는 EME(Encrypted Media Extensions) 인터페이스 지원을 통해 FPS를 지원합니다.

다음 이미지는 "FairPlay 동적 암호화" 워크플로를 보여 줍니다.

![FairPlay로 보호](./media/media-services-content-protection-overview/media-services-content-protection-with-fairplay.png)

이 항목에는 Azure 미디어 서비스를 사용하여 Apple FairPlay에서 HLS 콘텐츠를 동적으로 암호화하는 방법을 설명합니다. 또한 미디어 서비스 라이선스 배달 서비스를 사용하여 클라이언트에 FairPlay 라이선스를 제공하는 방법을 보여 줍니다.

	
## 요구 사항 및 고려 사항

- AMS를 사용하여 FairPlay로 암호화된 HLS를 제공하고 FairPlay 라이선스를 배달하려면 다음이 필요합니다.

	- Azure 계정. 자세한 내용은 [Azure 무료 체험](/pricing/free-trial/?WT.mc_id=A261C142F)을 참조하세요.
	- 미디어 서비스 계정. 미디어 서비스 계정을 만들려면 [계정 만들기](media-services-create-account.md)를 참조하세요.
	- [Apple Development Program](https://developer.apple.com/)에 등록합니다.
	- Apple에서는 [배포 패키지](https://developer.apple.com/contact/fps/)를 얻으려면 콘텐츠 소유자를 요구합니다. Azure 미디어 서비스로 KSM(키 보안 모듈)을 이미 구현했고 최종 FPS 패키지를 요청하고 있음을 요청에 명시하세요. 최종 FPS 패키지에는 FairPlay를 구성하는 데 사용할 인증서를 생성하고 ASK를 가져오기 위한 지침이 포함됩니다.

	- Azure 미디어 서비스 .NET SDK 버전 **3.6.0** 이상.

- AMS 키 배달 쪽에서 다음을 설정해야 합니다.
	- **AC(앱 인증서)** - 개인 키가 포함된 .pfx 파일입니다. 이 파일은 고객이 생성하며 해당 고객의 암호로 암호화됩니다.
		
	 	고객은 키 배달 정책을 구성할 때 이 암호와 base64 형식의 .pfx를 제공해야 합니다.

		다음 단계에서는 FairPlay에 대한 pfx 인증서를 생성하는 방법을 설명합니다.
		
		1. https://slproweb.com/products/Win32OpenSSL.html에서 OpenSSL을 설치합니다.
		
			FairPlay 인증서 및 Apple에서 전달하는 다른 파일이 있는 폴더로 이동합니다.
		
		2. cer을 pem으로 변환하기 위한 명령줄:
		
			"C:\\OpenSSL-Win32\\bin\\openssl.exe" x509 -inform der -in fairplay.cer -out fairplay-out.pem
		
		3. 개인 키를 사용하여 pem을 pfx로 변환하기 위한 명령줄(pfx 파일의 암호는 OpenSSL에서 요청함)
		
			"C:\\OpenSSL-Win32\\bin\\openssl.exe" pkcs12 -export -out fairplay-out.pfx -inkey privatekey.pem -in fairplay-out.pem -passin file:privatekey-pem-pass.txt
		
	- **앱 인증서 암호** - .pfx 파일을 만들기 위한 고객 암호입니다.
	- **앱 인증서 암호 ID** - 고객은 다른 AMS 키를 업로드할 때와 유사한 방법으로 **ContentKeyType.FairPlayPfxPassword** 열거형 값을 사용하여 암호를 업로드해야 합니다. 그 결과로 AMS ID가 제공되며, 키 배달 정책 옵션 내에서 이를 사용해야 합니다.
	- **iv** - 16바이트 임의 값이 자산 배달 정책의 iv와 일치해야 합니다. 고객은 IV를 생성하여 두 곳, 즉 자산 배달 정책 및 키 배달 정책 옵션에 두어야 합니다.
	- **ASK** - Apple 개발자 포털을 사용하여 인증서를 생성하는 경우 ASK(응용 프로그램 암호 키)가 제공됩니다. 각 개발 팀에 고유한 ASK가 제공됩니다. ASK 복사본을 저장하여 안전한 장소에 보관하세요. 나중에 ASK를 Azure 미디어 서비스의 FairPlayAsk로 구성해야 합니다.
	-  **ASK ID** - 고객이 ASk를 AMS에 업로드할 때 가져옵니다. 고객은 **ContentKeyType.FairPlayASk** 열거형 값을 사용하여 ASk를 업로드해야 합니다. 결과적으로 AMS ID가 반환되고 이 ID는 키 배달 정책 옵션을 설정할 때 사용해야 합니다.

- FPS 클라이언트 쪽에서 다음을 설정해야 합니다.
 	- **AC(앱 인증서)** - 일부 페이로드를 암호화하기 위해 OS에서 사용하는 공개 키가 포함된 .cer/.der 파일입니다. 이는 플레이어에 필요하기 때문에 AMS에서 알고 있어야 합니다. 키 배달 서비스는 해당 개인 키를 사용하여 암호를 해독합니다.

- FairPlay 암호화 스트림을 재생하려면 먼저 실제 ASK를 가져온 다음 실제 인증서를 생성해야 합니다. 해당 프로세스에서는 다음 세 가지 부분을 만듭니다.

	-  .der,
	-  .pfx 및
	-  .pfx에 대한 암호
 
- **AES-128 CBC** 암호화와 함께 HLS를 지원하는 클라이언트: OS X의 Safari, Apple TV, iOS

##FairPlay 동적 암호화 및 라이선스 배달 서비스 구성 단계

다음은 FairPlay로 자산을 보호하고 미디어 서비스 라이선스 배달 서비스를 사용하며 동적 암호화를 사용할 때 수행해야 하는 일반적인 단계입니다.

1. 자산을 만들고 파일을 자산에 업로드합니다.
1. 파일이 들어 있는 자산을 적응 비트 전송률 MP4 집합으로 인코딩합니다.
1. 콘텐츠 키를 만들고 인코딩된 자산에 연결합니다.
1. 콘텐츠 키의 권한 부여 정책을 구성합니다. 콘텐츠 키 권한 부여 정책을 만들 때 다음을 지정해야 합니다.
	
	- 배달 방법(이 예제의 경우 FairPlay)
	- FairPlay 정책 옵션 구성. FairPlay를 구성하는 방법에 대한 자세한 내용은 아래 샘플의 ConfigureFairPlayPolicyOptions() 메서드를 참조하세요.
	
		>[AZURE.NOTE] 일반적으로 하나의 인증서 및 ASK 집합만 포함하게 되므로 FairPlay 정책 옵션을 한 번만 구성하려고 합니다.
	- 제한 사항(열기 또는 토큰)
	- 클라이언트에 키가 배달되는 방식을 정의하는 키 배달 유형에 특정한 정보
	
2. 자산 배달 정책을 구성합니다. 배달 정책 구성에는 다음이 포함됩니다.

	- 배달 프로토콜(HLS)
	- 동적 암호화 형식(일반 CBC 암호화)
	- 라이선스 획득 URL
	
	>[AZURE.NOTE]FairPlay와 또 다른 DRM으로 암호화된 스트림을 배달하려면 별도의 배달 정책을 구성해야 합니다.
	>
	>- CENC(PlayReady + WideVine) 및 Smooth with PlayReady로 구성하기 위한 하나의 IAssetDeliveryPolicy
	>- HLS에 대한 FairPlay를 구성하기 위한 또 다른 IAssetDeliveryPolicy

1. 스트리밍 URL을 얻기 위해 주문형 로케이터를 만듭니다.

##플레이어/클라이언트 앱별 FairPlay 키 배달 사용

고객은 iOS SDK를 사용하여 플레이어 앱을 개발할 수 있습니다. FairPlay 콘텐츠를 재생하려면 고객이 라이선스 교환 프로토콜을 구현해야 합니다. Apple에서는 라이선스 교환 프로토콜을 지정하지 않습니다. 키 배달 요청을 전송하는 방법은 앱마다 다릅니다. AMS FairPlay 키 배달 서비스에서는 SPC가 다음 형식의 www-form-url 인코딩된 게시 메시지로 도착해야 합니다.

	spc=<Base64 encoded SPC>

>[AZURE.NOTE] Azure 미디어 플레이어는 기본적으로 FairPlay 재생을 지원하지 않습니다. MAC OSX에서 FairPlay를 재생하려면 고객이 Apple 개발자 계정에서 샘플 플레이어를 가져와야 합니다.
 

##.NET 예제


다음 샘플에서는 .Net 버전 3.6.0용 Azure 미디어 서비스 SDK에 도입된 기능(Azure 미디어 서비스를 사용하여 FairPlay로 암호화된 콘텐츠를 배달하는 기능)을 보여 줍니다. 다음 Nuget 패키지 명령은 패키지를 설치하는 데 사용되었습니다.

	PM> Install-Package windowsazure.mediaservices -Version 3.6.0


1. 콘솔 프로젝트를 만듭니다.
1. 설치하려면 NuGet을 사용하고 Azure 미디어 서비스 .NET SDK를 추가합니다.
2. 추가 참조를 추가합니다. System.Configuration.
2. 계정 이름 및 키 정보가 들어 있는 구성 파일을 추가합니다.
	
		<?xml version="1.0" encoding="utf-8"?>
		<configuration>
		    <startup> 
		        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5" />
		    </startup>
			  <appSettings>
			
			    <add key="MediaServicesAccountName" value="AccountName"/>
			    <add key="MediaServicesAccountKey" value="AccountKey"/>
			
			    <add key="Issuer" value="http://testacs.com"/>
			    <add key="Audience" value="urn:test"/>
			  </appSettings>
		</configuration>

1. 콘텐츠를 배달하는 출발점이 될 스트리밍 끝점에 하나 이상의 스트리밍 단위를 구성합니다. 자세한 내용은 [스트리밍 끝점 구성](media-services-dotnet-get-started.md#configure-streaming-endpoint-using-the-portal)을 참조하세요.

1. Program.cs 파일에 있는 코드를 이 섹션에 나와 있는 코드로 덮어씁니다.
			
		
		using System;
		using System.Collections.Generic;
		using System.Configuration;
		using System.IO;
		using System.Linq;
		using System.Threading;
		using Microsoft.WindowsAzure.MediaServices.Client;
		using Microsoft.WindowsAzure.MediaServices.Client.ContentKeyAuthorization;
		using Microsoft.WindowsAzure.MediaServices.Client.DynamicEncryption;
		using Microsoft.WindowsAzure.MediaServices.Client.FairPlay;
		using Newtonsoft.Json;
		using System.Security.Cryptography.X509Certificates;
		
		namespace DynamicEncryptionWithFairPlay
		{
		    class Program
		    {
		        // Read values from the App.config file.
		        private static readonly string _mediaServicesAccountName =
		            ConfigurationManager.AppSettings["MediaServicesAccountName"];
		        private static readonly string _mediaServicesAccountKey =
		            ConfigurationManager.AppSettings["MediaServicesAccountKey"];
		
		        private static readonly Uri _sampleIssuer =
		            new Uri(ConfigurationManager.AppSettings["Issuer"]);
		        private static readonly Uri _sampleAudience =
		            new Uri(ConfigurationManager.AppSettings["Audience"]);
		
		        // Field for service context.
		        private static CloudMediaContext _context = null;
		        private static MediaServicesCredentials _cachedCredentials = null;
		
		        private static readonly string _mediaFiles =
		            Path.GetFullPath(@"../..\Media");
		
		        private static readonly string _singleMP4File =
		            Path.Combine(_mediaFiles, @"BigBuckBunny.mp4");
		
		        static void Main(string[] args)
		        {
		            // Create and cache the Media Services credentials in a static class variable.
		            _cachedCredentials = new MediaServicesCredentials(
		                            _mediaServicesAccountName,
		                            _mediaServicesAccountKey);
		            // Used the cached credentials to create CloudMediaContext.
		            _context = new CloudMediaContext(_cachedCredentials);
		
		            bool tokenRestriction = false;
		            string tokenTemplateString = null;
		
		            IAsset asset = UploadFileAndCreateAsset(_singleMP4File);
		            Console.WriteLine("Uploaded asset: {0}", asset.Id);
		
		            IAsset encodedAsset = EncodeToAdaptiveBitrateMP4Set(asset);
		            Console.WriteLine("Encoded asset: {0}", encodedAsset.Id);
		
		            IContentKey key = CreateCommonCBCTypeContentKey(encodedAsset);
		            Console.WriteLine("Created key {0} for the asset {1} ", key.Id, encodedAsset.Id);
		            Console.WriteLine("FairPlay License Key delivery URL: {0}", key.GetKeyDeliveryUrl(ContentKeyDeliveryType.FairPlay));
		            Console.WriteLine();
		
		            if (tokenRestriction)
		                tokenTemplateString = AddTokenRestrictedAuthorizationPolicy(key);
		            else
		                AddOpenAuthorizationPolicy(key);
		
		            Console.WriteLine("Added authorization policy: {0}", key.AuthorizationPolicyId);
		            Console.WriteLine();
		
		            CreateAssetDeliveryPolicy(encodedAsset, key);
		            Console.WriteLine("Created asset delivery policy. \n");
		            Console.WriteLine();
		
		            if (tokenRestriction && !String.IsNullOrEmpty(tokenTemplateString))
		            {
		                // Deserializes a string containing an Xml representation of a TokenRestrictionTemplate
		                // back into a TokenRestrictionTemplate class instance.
		                TokenRestrictionTemplate tokenTemplate =
		                    TokenRestrictionTemplateSerializer.Deserialize(tokenTemplateString);
		
		                // Generate a test token based on the the data in the given TokenRestrictionTemplate.
		                // Note, you need to pass the key id Guid because we specified 
		                // TokenClaim.ContentKeyIdentifierClaim in during the creation of TokenRestrictionTemplate.
		                Guid rawkey = EncryptionUtils.GetKeyIdAsGuid(key.Id);
		                string testToken = TokenRestrictionTemplateSerializer.GenerateTestToken(tokenTemplate, null, rawkey,
		                                                                        DateTime.UtcNow.AddDays(365));
		                Console.WriteLine("The authorization token is:\nBearer {0}", testToken);
		                Console.WriteLine();
		            }
		
		            string url = GetStreamingOriginLocator(encodedAsset);
		            Console.WriteLine("Encrypted HLS URL: {0}/manifest(format=m3u8-aapl)", url);
		
		            Console.ReadLine();
		        }
		
		        static public IAsset UploadFileAndCreateAsset(string singleFilePath)
		        {
		            if (!File.Exists(singleFilePath))
		            {
		                Console.WriteLine("File does not exist.");
		                return null;
		            }
		
		            var assetName = Path.GetFileNameWithoutExtension(singleFilePath);
		            IAsset inputAsset = _context.Assets.Create(assetName, AssetCreationOptions.None);
		
		            var assetFile = inputAsset.AssetFiles.Create(Path.GetFileName(singleFilePath));
		
		            Console.WriteLine("Created assetFile {0}", assetFile.Name);
		
		            var policy = _context.AccessPolicies.Create(
		                                    assetName,
		                                    TimeSpan.FromDays(30),
		                                    AccessPermissions.Write | AccessPermissions.List);
		
		            var locator = _context.Locators.CreateLocator(LocatorType.Sas, inputAsset, policy);
		
		            Console.WriteLine("Upload {0}", assetFile.Name);
		
		            assetFile.Upload(singleFilePath);
		            Console.WriteLine("Done uploading {0}", assetFile.Name);
		
		            locator.Delete();
		            policy.Delete();
		
		            return inputAsset;
		        }
		
		        static public IAsset EncodeToAdaptiveBitrateMP4Set(IAsset inputAsset)
		        {
		            var encodingPreset = "H264 Multiple Bitrate 720p";
		
		            IJob job = _context.Jobs.Create(String.Format("Encoding into Mp4 {0} to {1}",
		                                    inputAsset.Name,
		                                    encodingPreset));
		
		            var mediaProcessors =
		                _context.MediaProcessors.Where(p => p.Name.Contains("Media Encoder Standard")).ToList();
		
		            var latestMediaProcessor =
		                mediaProcessors.OrderBy(mp => new Version(mp.Version)).LastOrDefault();
		
		            ITask encodeTask = job.Tasks.AddNew("Encoding", latestMediaProcessor, encodingPreset, TaskOptions.None);
		            encodeTask.InputAssets.Add(inputAsset);
		            encodeTask.OutputAssets.AddNew(String.Format("{0} as {1}", inputAsset.Name, encodingPreset), 	AssetCreationOptions.StorageEncrypted);
		
		            job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
		            job.Submit();
		            job.GetExecutionProgressTask(CancellationToken.None).Wait();
		
		            return job.OutputMediaAssets[0];
		        }
		
		        static public IContentKey CreateCommonCBCTypeContentKey(IAsset asset)
		        {
		            // Create HLS SAMPLE AES encryption content key
		            Guid keyId = Guid.NewGuid();
		            byte[] contentKey = GetRandomBuffer(16);
		
		            IContentKey key = _context.ContentKeys.Create(
		                                    keyId,
		                                    contentKey,
		                                    "ContentKey",
		                                    ContentKeyType.CommonEncryptionCbcs);
		
		            // Associate the key with the asset.
		            asset.ContentKeys.Add(key);
		
		            return key;
		        }
		
		
		        static public void AddOpenAuthorizationPolicy(IContentKey contentKey)
		        {
		            // Create ContentKeyAuthorizationPolicy with Open restrictions 
		            // and create authorization policy          
		
		            List<ContentKeyAuthorizationPolicyRestriction> restrictions = new List<ContentKeyAuthorizationPolicyRestriction>
		                    {
		                        new ContentKeyAuthorizationPolicyRestriction
		                        {
		                            Name = "Open",
		                            KeyRestrictionType = (int)ContentKeyRestrictionType.Open,
		                            Requirements = null
		                        }
		                    };
		
		
		            // Configure FairPlay policy option.
		            string FairPlayConfiguration = ConfigureFairPlayPolicyOptions();
		
		            IContentKeyAuthorizationPolicyOption FairPlayPolicy =
		                _context.ContentKeyAuthorizationPolicyOptions.Create("",
		                ContentKeyDeliveryType.FairPlay,
		                restrictions,
		                FairPlayConfiguration);
		
		
		            IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
		                        ContentKeyAuthorizationPolicies.
		                        CreateAsync("Deliver Common CBC Content Key with no restrictions").
		                        Result;
		
		            contentKeyAuthorizationPolicy.Options.Add(FairPlayPolicy);
		
		            // Associate the content key authorization policy with the content key.
		            contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
		            contentKey = contentKey.UpdateAsync().Result;
		        }
		
		        public static string AddTokenRestrictedAuthorizationPolicy(IContentKey contentKey)
		        {
		            string tokenTemplateString = GenerateTokenRequirements();
		
		            List<ContentKeyAuthorizationPolicyRestriction> restrictions = new List<ContentKeyAuthorizationPolicyRestriction>
		                    {
		                        new ContentKeyAuthorizationPolicyRestriction
		                        {
		                            Name = "Token Authorization Policy",
		                            KeyRestrictionType = (int)ContentKeyRestrictionType.TokenRestricted,
		                            Requirements = tokenTemplateString,
		                        }
		                    };
		
		            // Configure FairPlay policy option.
		            string FairPlayConfiguration = ConfigureFairPlayPolicyOptions();
		
		
		            IContentKeyAuthorizationPolicyOption FairPlayPolicy =
		                _context.ContentKeyAuthorizationPolicyOptions.Create("Token option",
		                       ContentKeyDeliveryType.FairPlay,
		                       restrictions,
		                       FairPlayConfiguration);
		
		            IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
		                        ContentKeyAuthorizationPolicies.
		                        CreateAsync("Deliver Common CBC Content Key with token restrictions").
		                        Result;
		
		            contentKeyAuthorizationPolicy.Options.Add(FairPlayPolicy);
		
		            // Associate the content key authorization policy with the content key
		            contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
		            contentKey = contentKey.UpdateAsync().Result;
		
		            return tokenTemplateString;
		        }
		
		        private static string ConfigureFairPlayPolicyOptions()
		        {
		            // For testing you can provide all zeroes for ASK bytes together with the cert from Apple FPS SDK. 
		            // However, for production you must use a real ASK from Apple bound to a real prod certificate.
		            byte[] askBytes = Guid.NewGuid().ToByteArray();
		            var askId = Guid.NewGuid();
		            // Key delivery retrieves askKey by askId and uses this key to generate the response.
		            IContentKey askKey = _context.ContentKeys.Create(
		                                    askId,
		                                    askBytes,
		                                    "askKey",
		                                    ContentKeyType.FairPlayASk);
		
		            //Customer password for creating the .pfx file.
		            string pfxPassword = "<customer password for creating the .pfx file>";
		            // Key delivery retrieves pfxPasswordKey by pfxPasswordId and uses this key to generate the response.
		            var pfxPasswordId = Guid.NewGuid();
		            byte[] pfxPasswordBytes = System.Text.Encoding.UTF8.GetBytes(pfxPassword);
		            IContentKey pfxPasswordKey = _context.ContentKeys.Create(
		                                    pfxPasswordId,
		                                    pfxPasswordBytes,
		                                    "pfxPasswordKey",
		                                    ContentKeyType.FairPlayPfxPassword);
		
		            // iv - 16 bytes random value, must match the iv in the asset delivery policy.
		            byte[] iv = Guid.NewGuid().ToByteArray();
		
		            //Specify the .pfx file created by the customer.
		            var appCert = new X509Certificate2("path to the .pfx file created by the customer", pfxPassword, X509KeyStorageFlags.Exportable);
		
		            string FairPlayConfiguration =
		                Microsoft.WindowsAzure.MediaServices.Client.FairPlay.FairPlayConfiguration.CreateSerializedFairPlayOptionConfiguration(
		                    appCert,
		                    pfxPassword,
		                    pfxPasswordId,
		                    askId,
		                    iv);
		
		            return FairPlayConfiguration;
		        }
		
		        static private string GenerateTokenRequirements()
		        {
		            TokenRestrictionTemplate template = new TokenRestrictionTemplate(TokenType.SWT);
		
		            template.PrimaryVerificationKey = new SymmetricVerificationKey();
		            template.AlternateVerificationKeys.Add(new SymmetricVerificationKey());
		            template.Audience = _sampleAudience.ToString();
		            template.Issuer = _sampleIssuer.ToString();
		            template.RequiredClaims.Add(TokenClaim.ContentKeyIdentifierClaim);
		
		            return TokenRestrictionTemplateSerializer.Serialize(template);
		        }
		
		        static public void CreateAssetDeliveryPolicy(IAsset asset, IContentKey key)
		        {
		            var kdPolicy = _context.ContentKeyAuthorizationPolicies.Where(p => p.Id == key.AuthorizationPolicyId).Single();
		
		            var kdOption = kdPolicy.Options.Single(o => o.KeyDeliveryType == ContentKeyDeliveryType.FairPlay);
		
		            FairPlayConfiguration configFP = JsonConvert.DeserializeObject<FairPlayConfiguration>(kdOption.KeyDeliveryConfiguration);
		
		            // Get the FairPlay license service URL.
		            Uri acquisitionUrl = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.FairPlay);
		
					// The reason the below code replaces "https://" with "skd://" is because
					// in the IOS player sample code which you obtained in Apple developer account, 
					// the player only recognizes a Key URL that starts with skd://. 
					// However, if you are using a customized player, 
					// you can choose whatever protocol you want. 
					// For example, "https". 

		            Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
		                new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
		                {
		                    {AssetDeliveryPolicyConfigurationKey.FairPlayLicenseAcquisitionUrl, acquisitionUrl.ToString().Replace("https://", "skd://")},
		                    {AssetDeliveryPolicyConfigurationKey.CommonEncryptionIVForCbcs, configFP.ContentEncryptionIV}
		                };
		
		            var assetDeliveryPolicy = _context.AssetDeliveryPolicies.Create(
		                    "AssetDeliveryPolicy",
		                AssetDeliveryPolicyType.DynamicCommonEncryptionCbcs,
		                AssetDeliveryProtocol.HLS,
		                assetDeliveryPolicyConfiguration);
		
		            // Add AssetDelivery Policy to the asset
		            asset.DeliveryPolicies.Add(assetDeliveryPolicy);
		
		        }
		
		
		        /// <summary>
		        /// Gets the streaming origin locator.
		        /// </summary>
		        /// <param name="assets"></param>
		        /// <returns></returns>
		        static public string GetStreamingOriginLocator(IAsset asset)
		        {
		
		            // Get a reference to the streaming manifest file from the  
		            // collection of files in the asset. 
		
		            var assetFile = asset.AssetFiles.Where(f => f.Name.ToLower().
		                                         EndsWith(".ism")).
		                                         FirstOrDefault();
		
		            // Create a 30-day readonly access policy. 
		            IAccessPolicy policy = _context.AccessPolicies.Create("Streaming policy",
		                TimeSpan.FromDays(30),
		                AccessPermissions.Read);
		
		            // Create a locator to the streaming content on an origin. 
		            ILocator originLocator = _context.Locators.CreateLocator(LocatorType.OnDemandOrigin, asset,
		                policy,
		                DateTime.UtcNow.AddMinutes(-5));
		
		            // Create a URL to the manifest file. 
		            return originLocator.Path + assetFile.Name;
		        }
		
		        static private void JobStateChanged(object sender, JobStateChangedEventArgs e)
		        {
		            Console.WriteLine(string.Format("{0}\n  State: {1}\n  Time: {2}\n\n",
		                ((IJob)sender).Name,
		                e.CurrentState,
		                DateTime.UtcNow.ToString(@"yyyy_M_d__hh_mm_ss")));
		        }
		
		        static private byte[] GetRandomBuffer(int length)
		        {
		            var returnValue = new byte[length];
		
		            using (var rng =
		                new System.Security.Cryptography.RNGCryptoServiceProvider())
		            {
		                rng.GetBytes(returnValue);
		            }
		
		            return returnValue;
		        }
		    }
		}


##다음 단계: 미디어 서비스 학습 경로

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##피드백 제공

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

<!---HONumber=AcomDC_0817_2016-->