---
title: Microsoft PlayReady 또는 Apple FairPlay를 사용 하 여 HLS 콘텐츠 보호-Azure | Microsoft Docs
description: 이 항목에서는 Azure Media Services 사용 하 여 Apple FairPlay에서 HLS (HTTP 라이브 스트리밍) 콘텐츠를 동적으로 암호화 하는 방법을 간략하게 설명 합니다. 또한 Media Services 라이선스 배달 서비스를 사용 하 여 클라이언트에 FairPlay 라이선스를 제공 하는 방법을 보여 줍니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: 873bc4ab5e435b91ff4400a39c92db0d0bb9baa8
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74968768"
---
# <a name="protect-your-hls-content-with-apple-fairplay-or-microsoft-playready"></a>Apple FairPlay 또는 Microsoft PlayReady로 HLS 콘텐츠 보호

> [!NOTE]
> Az oktatóanyag elvégzéséhez egy Azure-fiókra lesz szüksége. További információkért lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).   > 새 기능이 나 기능이 Media Services v2에 추가 되지 않습니다. <br/>Próbálja ki a legújabb verziót, ami a [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). 또한 [v2에서 v3로 마이그레이션 지침](../latest/migrate-from-v2-to-v3.md) 을 참조 하세요.
>

Azure Media Services를 사용 하면 다음 형식을 사용 하 여 HLS (HTTP 라이브 스트리밍) 콘텐츠를 동적으로 암호화할 수 있습니다.  

* **AES-128 봉투 암호화 되지 않은 키**

    전체 청크는 **AES-128 CBC** 모드를 사용 하 여 암호화 됩니다. 스트림의 암호 해독은 iOS 및 OS X 플레이어에서 기본적으로 지원 됩니다. 자세한 내용은 [AES-128 동적 암호화 및 키 배달 서비스 사용](media-services-protect-with-aes128.md)을 참조 하세요.
* **Apple FairPlay**

    개별 비디오 및 오디오 샘플은 **AES-128 CBC** 모드를 사용 하 여 암호화 됩니다. Fp ( **FairPlay Streaming** )는 IOS 및 Apple TV를 기본적으로 지 원하는 장치 운영 체제에 통합 되어 있습니다. OS X에서 Safari를 사용 하면 EME (암호화 된 미디어 확장) 인터페이스 지원을 통해 FPS를 사용할 수 있습니다.
* **Microsoft PlayReady**

다음 이미지는 **HLS + FairPlay 또는 PlayReady 동적 암호화** 워크플로를 보여 줍니다.

![동적 암호화 워크플로 다이어그램](./media/media-services-content-protection-overview/media-services-content-protection-with-FairPlay.png)

이 문서에서는 Media Services를 사용 하 여 Apple FairPlay에서 HLS 콘텐츠를 동적으로 암호화 하는 방법을 보여 줍니다. 또한 Media Services 라이선스 배달 서비스를 사용 하 여 클라이언트에 FairPlay 라이선스를 제공 하는 방법을 보여 줍니다.

> [!NOTE]
> PlayReady로 HLS 콘텐츠를 암호화 하려는 경우 공통 콘텐츠 키를 만들고 자산에 연결 해야 합니다. 또한 [PlayReady 동적 일반 암호화 사용](media-services-protect-with-playready-widevine.md)에 설명 된 대로 콘텐츠 키의 권한 부여 정책을 구성 해야 합니다.
>
>

## <a name="requirements-and-considerations"></a>요구 사항 및 고려 사항

다음은 Media Services를 사용 하 여 FairPlay로 암호화 된 HLS를 전달 하 고 FairPlay 라이선스를 제공 하는 경우에 필요 합니다.

  * Egy Azure-fiók. További részletek: [Ingyenes Azure-próbafiók](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F).
  * Egy Media Services-fiók. 하나를 만들려면 Azure Portal를 [사용 하 여 Azure Media Services 계정 만들기](media-services-portal-create-account.md)를 참조 하세요.
  * [Apple 개발 프로그램](https://developer.apple.com/)을 사용 하 여 등록 합니다.
  * Apple에서는 콘텐츠 소유자가 [배포 패키지](https://developer.apple.com/contact/fps/)를 가져와야 합니다. Media Services를 사용 하 여 이미 KSM (키 보안 모듈)을 구현 했으며 최종 FPS 패키지를 요청 하 고 있음을 명시 합니다. 최종 FPS 패키지에는 인증을 생성 하 고 ASK (응용 프로그램 비밀 키)를 얻는 지침이 있습니다. ASK를 사용 하 여 FairPlay를 구성 합니다.
  * Azure Media Services .NET SDK 버전 **3.6.0** 이상

Media Services 키 배달 쪽에서 다음 항목을 설정 해야 합니다.

  * **AC (앱 인증서)** : 개인 키가 포함 된 .pfx 파일입니다. 이 파일을 만들고 암호를 사용 하 여 암호화 합니다.

       키 배달 정책을 구성 하는 경우 해당 암호와 .pfx 파일을 Base64 형식으로 제공 해야 합니다.

      다음 단계에서는 FairPlay에 대 한 .pfx 인증서 파일을 생성 하는 방법을 설명 합니다.

    1. [https://slproweb.com/products/Win32OpenSSL.html](https://slproweb.com/products/Win32OpenSSL.html )에서 OpenSSL을 설치합니다.

        Apple에서 제공 하는 FairPlay 인증서 및 기타 파일이 있는 폴더로 이동 합니다.
    2. Futtassa az alábbi parancsot a parancssorból. 이렇게 하면 .cer 파일이 pem 파일로 변환 됩니다.

        "C:\OpenSSL-Win32\bin\openssl.exe" x509-FairPlay-out Fairplay-out.pfx에 알립니다.
    3. Futtassa az alábbi parancsot a parancssorból. 이렇게 하면 pem 파일이 개인 키를 사용 하 여 .pfx 파일로 변환 됩니다. 그런 다음 .pfx 파일의 암호는 OpenSSL에 의해 요청 됩니다.

        "C:\OpenSSL-Win32\bin\openssl.exe" pkcs12-Fairplay-out.pfx-passin file: privatekey-pem-pass.txt: file:입니다.
  * **앱 인증서 암호**: .pfx 파일을 만들기 위한 암호입니다.
  * **앱 인증서 암호 ID**: 다른 Media Services 키를 업로드 하는 것과 비슷한 방식으로 암호를 업로드 해야 합니다. Media Services ID를 가져오려면 **Contentkeytype** 열거형 값을 사용 합니다. 키 배달 정책 옵션 내에서 사용 해야 합니다.
  * **iv**: 임의의 값 16 바이트입니다. 자산 배달 정책의 iv와 일치 해야 합니다. Iv를 생성 하 고 두 위치 (자산 배달 정책 및 키 배달 정책 옵션)에 배치 합니다.
  * **ASK**: Apple 개발자 포털을 사용 하 여 인증을 생성할 때이 키가 수신 됩니다. 각 개발 팀은 고유한 ASK를 받습니다. ASK의 복사본을 저장 하 고 안전한 장소에 저장 합니다. FairPlayAsk로 ASK를 나중에 Media Services 구성 해야 합니다.
  * **ASK id**:이 id는 MEDIA SERVICES에 ask를 업로드할 때 가져옵니다. **Contentkeytype. FairPlayAsk** 열거형 값을 사용 하 여 ASK를 업로드 해야 합니다. 결과적으로 Media Services ID가 반환 되 고 키 배달 정책 옵션을 설정할 때 사용 해야 합니다.

다음 작업은 FPS 클라이언트 쪽에서 설정 해야 합니다.

  * **AC (앱 인증서)** : 운영 체제에서 일부 페이로드를 암호화 하는 데 사용 하는 공개 키를 포함 하는 .cer/. m s d 파일입니다. 플레이어에 게 필요한 Media Services에 대해 알고 있어야 합니다. 키 배달 서비스는 해당 개인 키를 사용 하 여 암호를 해독 합니다.

FairPlay 암호화 된 스트림을 재생 하려면 먼저 실제 ASK를 가져온 다음 실제 인증서를 생성 합니다. 이 프로세스에서는 세 부분으로 구성 됩니다.

  * der 파일
  * .pfx 파일
  * .pfx에 대 한 암호

다음 클라이언트는 **AES-128 CBC** 암호화로 HLS를 지원 합니다. OS X, Apple TV, IOS의 Safari.

## <a name="configure-fairplay-dynamic-encryption-and-license-delivery-services"></a>FairPlay 동적 암호화 및 라이선스 배달 서비스 구성
다음은 Media Services 라이선스 배달 서비스를 사용 하 고 동적 암호화를 사용 하 여 FairPlay으로 자산을 보호 하는 일반적인 단계입니다.

1. Hozzon létre egy objektumot, és töltse fel bele a fájlokat.
2. Kódolja a fájlt tartalmazó objektumot az adaptív sávszélességű MP4 típusú beállításkészlettel.
3. Hozzon létre egy tartalomkulcsot, és társítsa a kódolt objektumhoz.  
4. Konfigurálja a tartalomkulcs hitelesítési szabályzatát. 다음을 지정합니다.

   * 배달 방법 (이 경우 FairPlay)입니다.
   * FairPlay 정책 옵션 구성 FairPlay를 구성 하는 방법에 대 한 자세한 내용은 아래 샘플의 **ConfigureFairPlayPolicyOptions ()** 메서드를 참조 하세요.

     > [!NOTE]
     > 일반적으로 FairPlay 정책 옵션은 한 번만 구성 하면 됩니다. 한 가지 인증 집합과 ASK가 있기 때문입니다.
     >
     >
   * 제한 (개방형 또는 토큰).
   * 키를 클라이언트에 배달 하는 방법을 정의 하는 키 배달 유형과 관련 된 정보입니다.
5. 자산 배달 정책을 구성 합니다. 배달 정책 구성에는 다음이 포함 됩니다.

   * 배달 프로토콜 (HLS)입니다.
   * 동적 암호화 유형 (일반적인 CBC 암호화)입니다.
   * 라이선스 취득 URL입니다.

     > [!NOTE]
     > FairPlay 및 다른 DRM (Digital Rights Management) 시스템으로 암호화 된 스트림을 배달 하려면 별도의 배달 정책을 구성 해야 합니다.
     >
     > * IAssetDeliveryPolicy (CENC)를 사용 하 Common Encryption 여 HTTP (대시)를 통해 동적 적응 스트리밍을 구성 하는 한 가지 (CENC) (PlayReady + Widevto) 및 PlayReady로 부드러운 설정
     > * HLS에 대 한 FairPlay를 구성 하는 다른 IAssetDeliveryPolicy
     >
     >
6. Hozzon létre egy OnDemand-lokátort a streamelési URL-cím lekéréséhez.

## <a name="use-fairplay-key-delivery-by-player-apps"></a>플레이어 앱에서 FairPlay 키 배달 사용
IOS SDK를 사용 하 여 플레이어 앱을 개발할 수 있습니다. FairPlay 콘텐츠를 재생 하려면 라이선스 교환 프로토콜을 구현 해야 합니다. 이 프로토콜은 Apple에서 지정 되지 않았습니다. 키 배달 요청을 보내는 방법에는 각 앱이 있습니다. Media Services FairPlay 키 배달 서비스는 다음과 같은 형식으로 SPC를 www 형식 url 인코딩된 게시 메시지로 제공 합니다.

    spc=<Base64 encoded SPC>

> [!NOTE]
> Azure Media Player FairPlay 재생을 지원 합니다. 자세한 내용은 [Azure Media Player 설명서](https://amp.azure.net/libs/amp/latest/docs/index.html) 를 참조 하세요.
>
>

## <a name="streaming-urls"></a>스트리밍 Url
자산이 둘 이상의 DRM으로 암호화 된 경우 스트리밍 URL에서 암호화 태그를 사용 해야 합니다. (format = l3u8-aapl ', encryption = ' xxx ').

A következő szempontokat kell figyelembe venni:

* 0 또는 1 개의 암호화 유형만 지정할 수 있습니다.
* 자산에 암호화를 하나만 적용 한 경우 URL에 암호화 유형을 지정할 필요가 없습니다.
* 암호화 유형은 대/소문자를 구분 하지 않습니다.
* 다음 암호화 유형을 지정할 수 있습니다.  
  * **cenc**: 일반 암호화 (PlayReady 또는 widevto)
  * **cbcs-aapl**: FairPlay
  * **cbc**: AES 봉투 암호화

## <a name="create-and-configure-a-visual-studio-project"></a>Egy Visual Studio-projekt létrehozása és konfigurálása

1. Állítsa be a fejlesztési környezetet, és töltse fel az app.config fájlt a kapcsolatadatokkal a [.NET-keretrendszerrel történő Media Services-fejlesztést](media-services-dotnet-how-to-use.md) ismertető dokumentumban leírtak szerint. 
2. Adja hozzá a következő elemeket az app.config fájlban megadott **appSettings** szakaszhoz:

    ```xml
    <add key="Issuer" value="http://testissuer.com"/>
    <add key="Audience" value="urn:test"/>
    ```

## <a name="example"></a>Példa

다음 샘플에서는 Media Services를 사용 하 여 FairPlay로 암호화 된 콘텐츠를 배달 하는 기능을 보여 줍니다. 이 기능은 .NET 용 Azure Media Services SDK 버전 3.6.0에서 도입 되었습니다. 

Írja felül a Program.cs fájlban található kódot az itt látható kóddal.

>[!NOTE]
>A különböző AMS-szabályzatok (például a Locator vagy a ContentKeyAuthorizationPolicy) esetében a korlát 1 000 000 szabályzat. Ha mindig ugyanazokat a napokat/hozzáférési engedélyeket használja (például olyan keresők szabályzatait, amelyek hosszú ideig érvényben maradnak, vagyis nem feltöltött szabályzatokat), a szabályzatazonosítónak is ugyanannak kell lennie. További információkért tekintse meg [ezt](media-services-dotnet-manage-entities.md#limit-access-policies) a cikket.

Módosítsa úgy a változókat, hogy a bemeneti fájlok tárolásához Ön által használt mappákra mutassanak.

```csharp
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
        private static readonly string _AADTenantDomain =
            ConfigurationManager.AppSettings["AMSAADTenantDomain"];
        private static readonly string _RESTAPIEndpoint =
            ConfigurationManager.AppSettings["AMSRESTAPIEndpoint"];
        private static readonly string _AMSClientId =
            ConfigurationManager.AppSettings["AMSClientId"];
        private static readonly string _AMSClientSecret =
            ConfigurationManager.AppSettings["AMSClientSecret"];

        private static readonly Uri _sampleIssuer =
            new Uri(ConfigurationManager.AppSettings["Issuer"]);
        private static readonly Uri _sampleAudience =
            new Uri(ConfigurationManager.AppSettings["Audience"]);

        // Field for service context.
        private static CloudMediaContext _context = null;

        private static readonly string _mediaFiles =
            Path.GetFullPath(@"../..\Media");

        private static readonly string _singleMP4File =
            Path.Combine(_mediaFiles, @"BigBuckBunny.mp4");

        static void Main(string[] args)
        {
            AzureAdTokenCredentials tokenCredentials =
                new AzureAdTokenCredentials(_AADTenantDomain,
                    new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                    AzureEnvironments.AzureCloudEnvironment);

            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

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

                // Generate a test token based on the data in the given TokenRestrictionTemplate.
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

            Console.WriteLine("Upload {0}", assetFile.Name);

            assetFile.Upload(singleFilePath);
            Console.WriteLine("Done uploading {0}", assetFile.Name);

            return inputAsset;
        }

        static public IAsset EncodeToAdaptiveBitrateMP4Set(IAsset inputAsset)
        {
            var encodingPreset = "Adaptive Streaming";

            IJob job = _context.Jobs.Create(String.Format("Encoding {0}", inputAsset.Name));

            var mediaProcessors =
            _context.MediaProcessors.Where(p => p.Name.Contains("Media Encoder Standard")).ToList();

            var latestMediaProcessor =
            mediaProcessors.OrderBy(mp => new Version(mp.Version)).LastOrDefault();

            ITask encodeTask = job.Tasks.AddNew("Encoding", latestMediaProcessor, encodingPreset, TaskOptions.None);
            encodeTask.InputAssets.Add(inputAsset);
            encodeTask.OutputAssets.AddNew(String.Format("{0} as {1}", inputAsset.Name, encodingPreset), AssetCreationOptions.StorageEncrypted);

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

            var assetFile = asset.AssetFiles.LoList().Where(f => f.Name.ToLower().
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
```

## <a name="additional-notes"></a>További megjegyzések

* Widevine는 Google i n c .에서 제공 하는 서비스로, Google, i n c .의 서비스 약관 및 개인 정보 취급 방침을 따릅니다.

## <a name="next-steps-media-services-learning-paths"></a>Következő lépések: Media Services képzési tervek
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
