<properties 
	pageTitle=".NET을 사용하여 자산 배포 정책 구성" 
	description="이 항목에서는 다른 자산 배달 하는 정책을 구성하는 방법을 보여 줍니다." 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="02/06/2015" 
	ms.author="juliako"/>

#방법: 자산 배달 정책 구성
[AZURE.INCLUDE [media-services-selector-asset-delivery-policy](../includes/media-services-selector-asset-delivery-policy.md)]

이 문서는 [워크플로 주문형 미디어 서비스 비디오] 시리즈의(media-services-video-on-demand-workflow.md) 및 [미디어 서비스 라이브 스트리밍 워크플로](media-services-live-streaming-workflow.md) 일부입니다. 

미디어 서비스 콘텐츠 배달 워크플로의 단계 중 하나는 스트리밍하려는 자산에 대한 배달 정책을 구성하는 것입니다. 자산 배달 정책은 어떤 스트리밍 프로토콜(예: MPEG DASH, HLS, 부드러운 스트리밍 또는 모두)로 사용자의 자산을 동적으로 패키지할 지와 같은 사용자가 원하는 자산 배달 방법과 사용자의 자산을 동적으로 암호화할 지 여부 및 방법(봉투 또는 일반 암호화)를 미디어 서비스에 알려줍니다. 

이 항목에서는 자산 배달을 정책을 만들어야하는 이유와 방법을 설명합니다. 

>[AZURE.NOTE]동적 패키징 및 동적 암호화를 사용할 수 있으려면 하나 이상의 배율 단위(스트리밍 단위)가 있어야 합니다. 자세한 내용은 [미디어 서비스를 확장하는 방법]을 참조하세요](media-services-manage-origins.md#scale_streaming_endpoints). 
>
>또한 자산은 적응 비트 전송률 MP4 또는 적응 비트 전송률 부드러운 스트리밍 파일 집합을 포함해야 합니다.      

동일한 자산에 다른 정책을 적용할 수 있습니다. 예를 들어, 부드러운 스트리밍에 PlayReady 암호화, MPEG DASH 및 HLS에 AES 봉투(envelope) 암호화를 적용할 수 있습니다. 배달 정책에 정의되지 않은 모든 프로토콜(예: HLS만 프로토콜로 지정하는 단일 정책)은 스트리밍에서 차단됩니다. 정의한 자산 배달 정책이 없는 경우는 예외입니다. 이렇게 하면 모든 프로토콜이 허용됩니다.

저장소에서 암호화된 자산을 배달하려는 경우 자산의 배달 정책을 구성해야 합니다. 자산을 스트리밍하기 전에 스트리밍 서버가 저장소 암호화를 제거하고 지정된 배달 정책을 사용하여 콘텐츠를 스트리밍합니다. 예를 들어 표준 AES 봉투 암호화 키로 암호화된 자산을 배달하려면 정책 유형을 **DynamicEnvelopeEncryption**으로 설정해야 합니다. 저장소 암호화를 제거하고 암호화 되지 않은 자산을 스트리밍하려면, 정책 유형을 **NoDynamicEncryption**로 설정하세요. 이러한 정책 유형을 구성 하는 방법을 보여주는 예제입니다. 

사용자가 자산 배달 정책을 구성하는 방법에 따라 다음 스트리밍 프로토콜을 동적으로 패키지하고, 동적으로 암호화하고 스트림할 수 있습니다. 부드러운 스트리밍, HLS, MPEG DASH 및 HDS 스트림  

다음 목록에서는 부드러운 스트리밍, HLS, DASH, HDS 등의 프로토콜을 스트리밍할 때 사용하는 형식을 보여 줍니다.  

부드러운 스트리밍:

	{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

HLS:

	{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

MPEG DASH

	{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf) 

HDS

	{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=f4m-f4f)

자산을 게시하고 스트리밍 URL을 작성하는 방법에 대한 지침은 [스트리밍 URL 작성](media-services-deliver-streaming-content.md)을 참조하세요

##자산 배달 정책 지우기 

다음 **ConfigureClearAssetDeliveryPolicy** 메서드는 동적 암호화를 적용하지 않고 다음 프로토콜에 있는 스트림에 배포하도록 지정합니다.  MPEG DASH, HLS 및 부드러운 스트리밍 프로토콜 
  
AssetDeliveryPolicy을 만들 때 사용자가 지정하는 값에 대한 자세한 정보는 [AssetDeliveryPolicy 정의할 때 사용 되는 형식]을 참조하세요.(#types) 섹션으로 바로 이동할 수 있습니다. 

    static public void ConfigureClearAssetDeliveryPolicy(IAsset asset)
    {
        IAssetDeliveryPolicy policy =
            _context.AssetDeliveryPolicies.Create("Clear Policy",
            AssetDeliveryPolicyType.NoDynamicEncryption, 
            AssetDeliveryProtocol.HLS | AssetDeliveryProtocol.SmoothStreaming | AssetDeliveryProtocol.Dash, null);

        asset.DeliveryPolicies.Add(policy);
    }

##DynamicCommonEncryption 자산 배달 정책 


다음 **CreateAssetDeliveryPolicy** 메서드는 부드러운 스트리밍 프로토콜(다른 프로토콜은 스트리밍에서 차단됨)에 동적 일반 암호화(**DynamicCommonEncryption**)를 적용하도록 구성된 **AssetDeliveryPolicy**를 만듭니다. 이 메서드는 다음과 같은 두 매개 변수를 사용합니다. **자산**(배포 정책을 적용하려는 자산) 및 **IContentKey**(**CommonEncryption** 유형의 콘텐츠 키, 자세한 내용은 [콘텐츠 키 만들기](media-services-dotnet-create-contentkey.md#common_contentkey)를)참조하세요.

AssetDeliveryPolicy을 만들 때 사용자가 지정하는 값에 대한 자세한 정보는 [AssetDeliveryPolicy 정의할 때 사용 되는 형식](#types)을 참조하세요. 섹션으로 바로 이동할 수 있습니다. 


    static public void CreateAssetDeliveryPolicy(IAsset asset, IContentKey key)
    {
        Uri acquisitionUrl = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.PlayReadyLicense);

        Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
            new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
        {
            {AssetDeliveryPolicyConfigurationKey.PlayReadyLicenseAcquisitionUrl, acquisitionUrl.ToString()},
        };

        var assetDeliveryPolicy = _context.AssetDeliveryPolicies.Create(
                "AssetDeliveryPolicy",
            AssetDeliveryPolicyType.DynamicCommonEncryption,
            AssetDeliveryProtocol.SmoothStreaming,
            assetDeliveryPolicyConfiguration);

        // Add AssetDelivery Policy to the asset
        asset.DeliveryPolicies.Add(assetDeliveryPolicy);

        Console.WriteLine();
        Console.WriteLine("Adding Asset Delivery Policy: " +
            assetDeliveryPolicy.AssetDeliveryPolicyType);
    }



##DynamicEnvelopeEncryption 자산 배달 정책 

다음 **CreateAssetDeliveryPolicy** 메서드는 HLS 및 DASH 프로토콜(다른 프로토콜은 스트리밍에서 차단됨)에 동적 봉투 암호화(**DynamicEnvelopeEncryption**)를 적용하도록 구성된 **AssetDeliveryPolicy**를 만듭니다. 이 메서드는 다음과 같은 두 매개 변수를 사용합니다. **자산**(배포 정책을 적용하려는 자산) 및 **IContentKey**(**EnvelopeEncryption** 유형의 콘텐츠 키, 자세한 내용은 [콘텐츠 키 만들기](media-services-dotnet-create-contentkey.md#envelope_contentkey)를)참조하세요.


AssetDeliveryPolicy을 만들 때 사용자가 지정하는 값에 대한 자세한 정보는 [AssetDeliveryPolicy 정의할 때 사용 되는 형식]을 참조하세요.(#types) 섹션으로 바로 이동할 수 있습니다.   

    private static void CreateAssetDeliveryPolicy(IAsset asset, IContentKey key)
    {
        
        //  Get the Key Delivery Base Url by removing the Query parameter.  The Dynamic Encryption service will
        //  automatically add the correct key identifier to the url when it generates the Envelope encrypted content
        //  manifest.  Omitting the IV will also cause the Dynamice Encryption service to generate a deterministic
        //  IV for the content automatically.  By using the EnvelopeBaseKeyAcquisitionUrl and omitting the IV, this
        //  allows the AssetDelivery policy to be reused by more than one asset.
        //
        Uri keyAcquisitionUri = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.BaselineHttp);
        UriBuilder uriBuilder = new UriBuilder(keyAcquisitionUri);
        uriBuilder.Query = String.Empty;
        keyAcquisitionUri = uriBuilder.Uri;

        // The following policy configuration specifies: 
        //   key url that will have KID=<Guid> appended to the envelope and
        //   the Initialization Vector (IV) to use for the envelope encryption.
        Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
            new Dictionary<AssetDeliveryPolicyConfigurationKey, string> 
        {
            {AssetDeliveryPolicyConfigurationKey.EnvelopeBaseKeyAcquisitionUrl, keyAcquisitionUri.ToString()},
        };

        IAssetDeliveryPolicy assetDeliveryPolicy =
            _context.AssetDeliveryPolicies.Create(
                        "AssetDeliveryPolicy",
                        AssetDeliveryPolicyType.DynamicEnvelopeEncryption,
                        AssetDeliveryProtocol.HLS | AssetDeliveryProtocol.Dash,
                        assetDeliveryPolicyConfiguration);

        // Add AssetDelivery Policy to the asset
        asset.DeliveryPolicies.Add(assetDeliveryPolicy);

        Console.WriteLine();
        Console.WriteLine("Adding Asset Delivery Policy: " + assetDeliveryPolicy.AssetDeliveryPolicyType);
    }


##<a id="types"></a>AssetDeliveryPolicy를 정의할 때 사용되는 형식

###<a id="AssetDeliveryProtocol"></a>AssetDeliveryProtocol 

    /// <summary>
    /// Delivery protocol for an asset delivery policy.
    /// </summary>
    [Flags]
    public enum AssetDeliveryProtocol
    {
        /// <summary>
        /// No protocols.
        /// </summary>
        None = 0x0,

        /// <summary>
        /// Smooth streaming protocol.
        /// </summary>
        SmoothStreaming = 0x1,

        /// <summary>
        /// MPEG Dynamic Adaptive Streaming over HTTP (DASH)
        /// </summary>
        Dash = 0x2,

        /// <summary>
        /// Apple HTTP Live Streaming protocol.
        /// </summary>
        HLS = 0x4,

        /// <summary>
        /// Adobe HTTP Dynamic Streaming (HDS)
        /// </summary>
        Hds = 0x8,

        /// <summary>
        /// Include all protocols.
        /// </summary>
        All = 0xFFFF
    }

###<a id="AssetDeliveryPolicyType"></a>AssetDeliveryPolicyType

    /// <summary>
    /// Policy type for dynamic encryption of assets.
    /// </summary>
    public enum AssetDeliveryPolicyType
    {
        /// <summary>
        /// Delivery Policy Type not set.  An invalid value.
        /// </summary>
        None,

        /// <summary>
        /// The Asset should not be delivered via this AssetDeliveryProtocol. 
        /// </summary>
        Blocked, 

        /// <summary>
        /// Do not apply dynamic encryption to the asset.
        /// </summary>
        /// 
        NoDynamicEncryption,  

        /// <summary>
        /// Apply Dynamic Envelope encryption.
        /// </summary>
        DynamicEnvelopeEncryption,

        /// <summary>
        /// Apply Dynamic Common encryption.
        /// </summary>
        DynamicCommonEncryption
    }

###<a id="ContentKeyDeliveryType"></a>ContentKeyDeliveryType

    /// <summary>
    /// Delivery method of the content key to the client.
    /// </summary>
    public enum ContentKeyDeliveryType
    {
        /// <summary>
        /// None.
        /// </summary>
        None,

        /// <summary>
        /// Use PlayReady License acquistion protocol
        /// </summary>
        PlayReadyLicense,

        /// <summary>
        /// Use MPEG Baseline HTTP key protocol.
        /// </summary>
        BaselineHttp
    }

###<a id="AssetDeliveryPolicyConfigurationKey"></a>AssetDeliveryPolicyConfigurationKey

    /// <summary>
    /// Keys used to get specific configuration for an asset delivery policy.
    /// </summary>
    public enum AssetDeliveryPolicyConfigurationKey
    {
        /// <summary>
        /// No policies.
        /// </summary>
        None,

        /// <summary>
        /// Exact Envelope key URL.
        /// </summary>
        EnvelopeKeyAcquisitionUrl,

        /// <summary>
        /// Base key url that will have KID=<Guid> appended for Envelope.
        /// </summary>
        EnvelopeBaseKeyAcquisitionUrl,
        
        /// <summary>
        /// The initialization vector to use for envelope encryption in Base64 format.
        /// </summary>
        EnvelopeEncryptionIVAsBase64,

        /// <summary>
        /// The PlayReady License Acquisition Url to use for common encryption.
        /// </summary>
        PlayReadyLicenseAcquisitionUrl,

        /// <summary>
        /// The PlayReady Custom Attributes to add to the PlayReady Content Header
        /// </summary>
        PlayReadyCustomAttributes,

        /// <summary>
        /// The initialization vector to use for envelope encryption.
        /// </summary>
        EnvelopeEncryptionIV,
    }

<!--HONumber=52-->