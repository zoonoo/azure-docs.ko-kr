<properties 
	pageTitle=".NET을 사용하여 Contentkey 만들기" 
	description="자산에 대한 보안 액세스를 제공하는 콘텐츠 키를 만드는 방법에 대해 알아봅니다." 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/15/2015" 
	ms.author="juliako"/>


# .NET을 사용하여 Contentkey 만들기

이 문서는 [워크플로 주문형 미디어 서비스 비디오](../media-services-video-on-demand-workflow) 및 [미디어 서비스 라이브 스트리밍 워크플로](../media-services-live-streaming-workflow) 시리즈의 일부입니다.  

미디어 서비스를 사용하면 새로운 자산 및 암호화된 자산을 제공할 수 있습니다. **ContentKey**는 **자산**에 보안 액세스를 제공합니다. 

새 자산을 만들 때(예: [파일 업로드](../media-services-dotnet-upload-files/) 전) 다음 암호화 옵션을 지정할 수 있습니다. **StorageEncrypted**, **CommonEncryptionProtected** 또는 **EnvelopeEncryptionProtected**. 

클라이언트에 자산을 제공할 때, 다음 두 암호화 중 하나를 사용하여 [동적으로 암호화되는 자산을 구성](../media-services-dotnet-configure-asset-delivery-policy)할 수 있습니다. **DynamicEnvelopeEncryption** 또는 **DynamicCommonEncryption**.

암호화된 자산은 **ContentKey**와 연관되어야 합니다. 이 문서는 콘텐츠 키를 생성하는 방법을 설명합니다.

>[AZURE.NOTE] Media Services .NET SDK를 사용하여 새 **StorageEncrypted** 자산을 만들 때, **ContentKey**가 자동으로 생성되며 해당 자산과 연결됩니다.

## ContentKeyType

콘텐츠 키를 만들 때 설정해야 하는 값 중 하나가 콘텐츠 키 유형입니다. 다음 값 중에서 하나를 선택합니다. 

    /// <summary>
    /// Specifies the type of a content key.
    /// </summary>
    public enum ContentKeyType
    {
        /// <summary>
        /// Specifies a content key for common encryption.
        /// </summary>
        /// <remarks>This is the default value.</remarks>
        CommonEncryption = 0,

        /// <summary>
        /// Specifies a content key for storage encryption.
        /// </summary>
        StorageEncryption = 1,

        /// <summary>
        /// Specifies a content key for encrypting encoding configuration data that may contain sensitive preset information. 
        /// </summary>
        ConfigurationEncryption = 2,

        /// <summary>
        /// Specifies a content key for url encryption.  Only used internally.
        /// </summary>
        UrlEncryption = 3,

        /// <summary>
        /// Specifies a content key for Envelope encryption.  Only used internally.
        /// </summary>
        EnvelopeEncryption = 4
    }

## <a id="envelope_contentkey"></a>봉투 유형의 ContentKey 만들기

다음 코드 조각은 봉투 암호화 유형의 콘텐츠 키를 만듭니다. 그런 다음 키를 지정된 자산과 연결합니다.

    static public IContentKey CreateEnvelopeTypeContentKey(IAsset asset)
    {
        // Create envelope encryption content key
        Guid keyId = Guid.NewGuid();
        byte[] contentKey = GetRandomBuffer(16);

        IContentKey key = _context.ContentKeys.Create(
                                keyId,
                                contentKey,
                                "ContentKey",
                                ContentKeyType.EnvelopeEncryption);

        asset.ContentKeys.Add(key);

        return key;
    }

    static private byte[] GetRandomBuffer(int size)
    {
        byte[] randomBytes = new byte[size];
        using (RNGCryptoServiceProvider rng = new RNGCryptoServiceProvider())
        {
            rng.GetBytes(randomBytes);
        }

        return randomBytes;
    }

call

	IContentKey key = CreateEnvelopeTypeContentKey(encryptedsset);



## <a id="common_contentkey"></a>일반 유형의 ContentKey 만들기    

다음 코드 조각은 일반 암호화 유형의 콘텐츠 키를 만듭니다. 그런 다음 키를 지정된 자산과 연결합니다.

    static public IContentKey CreateCommonTypeContentKey(IAsset asset)
    {
        // Create common encryption content key
        Guid keyId = Guid.NewGuid();
        byte[] contentKey = GetRandomBuffer(16);

        IContentKey key = _context.ContentKeys.Create(
                                keyId,
                                contentKey,
                                "ContentKey",
                                ContentKeyType.CommonEncryption);

        // Associate the key with the asset.
        asset.ContentKeys.Add(key);

        return key;
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
call

	IContentKey key = CreateCommonTypeContentKey(encryptedsset);
<!--HONumber=45--> 
