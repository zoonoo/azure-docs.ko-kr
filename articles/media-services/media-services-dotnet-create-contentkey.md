<properties 
	pageTitle=".NET을 사용하여 Contentkey 만들기" 
	description="자산에 대한 보안 액세스를 제공하는 콘텐츠 키를 만드는 방법에 대해 알아봅니다." 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/07/2015" 
	ms.author="juliako"/>


#.NET을 사용하여 Contentkey 만들기

> [AZURE.SELECTOR]
- [REST](media-services-rest-create-contentkey.md)
- [.NET](media-services-dotnet-create-contentkey.md)

미디어 서비스를 사용하면 암호화된 자산을 만들어서 제공할 수 있습니다. **ContentKey**는 **자산**에 대한 보안 액세스를 제공합니다.

새 자산을 만들 때(예: [파일 업로드](media-services-dotnet-upload-files.md) 전) **StorageEncrypted**, **CommonEncryptionProtected** 또는 **EnvelopeEncryptionProtected** 암호화 옵션을 지정할 수 있습니다.

클라이언트에 자산을 제공할 때는 다음 두 암호화 중 하나를 사용하여 [자산이 동적으로 암호화되도록 구성](media-services-dotnet-configure-asset-delivery-policy.md)할 수 있습니다. **DynamicEnvelopeEncryption** 또는 **DynamicCommonEncryption**.

암호화된 자산은 **ContentKey**와 연관되어야 합니다. 이 문서에서는 콘텐츠 키를 만드는 방법을 설명합니다.

>[AZURE.NOTE]Media Services .NET SDK를 사용하여 새 **StorageEncrypted** 자산을 만들 때, **ContentKey**가 자동으로 생성되며 해당 자산과 연결됩니다.

##ContentKeyType

콘텐츠 키를 만들 때 설정해야 하는 값 중 하나가 콘텐츠 키 유형입니다. 다음 값 중 하나를 선택합니다.

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
    }

##<a id="envelope_contentkey"></a>봉투 유형의 ContentKey 만들기

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

다음을 호출합니다.

	IContentKey key = CreateEnvelopeTypeContentKey(encryptedsset);



##<a id="common_contentkey"></a>일반 유형의 ContentKey 만들기    

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
다음을 호출합니다.

	IContentKey key = CreateCommonTypeContentKey(encryptedsset); 


##미디어 서비스 학습 경로

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##피드백 제공

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

<!---HONumber=Nov15_HO3-->