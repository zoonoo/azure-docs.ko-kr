---
title: "오프라인 Apple FairPlay로 HLS 콘텐츠 보호 - Azure | Microsoft Docs"
description: "이 항목에서는 오프라인 모드에서 Azure Media Services를 사용하여 Apple FairPlay에서 HLS(HTTP 라이브 스트리밍) 콘텐츠를 동적으로 암호화하는 방법과 개요를 설명합니다."
services: media-services
keywords: "HLS, DRM, FairPlay 스트리밍(FPS), Offline, iOS 10"
documentationcenter: 
author: willzhan
manager: steveng
editor: 
ms.assetid: 7c3b35d9-1269-4c83-8c91-490ae65b0817
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/01/2017
ms.author: willzhan, dwgeo
ms.openlocfilehash: bf5828ecd6b6bd2e862c4d7709014ecac47c6be0
ms.sourcegitcommit: cc03e42cffdec775515f489fa8e02edd35fd83dc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/07/2017
---
# <a name="offline-fairplay-streaming"></a>오프라인 FairPlay 스트리밍
Microsoft Azure Media Services는 다음을 다루는 잘 디자인된 [콘텐츠 보호 서비스](https://azure.microsoft.com/services/media-services/content-protection/) 집합을 제공합니다.
- Microsoft PlayReady
- Google Widevine
- Apple FairPlay
- AES-128 암호화

콘텐츠의 DRM/AES 암호화는 다양한 스트리밍 프로토콜에 대한 요청에 따라 동적으로 수행됩니다. DRM 라이선스/AES 암호 해독 키 배달 서비스는 또한 Azure Media Services에서 제공합니다.

다양한 스트리밍 프로토콜을 통한 온라인 스트리밍에 대한 콘텐츠를 보호하는 것 외에도, 보호된 콘텐츠에 대한 오프 라인 모드 또한 자주 요청되는 기능입니다. 다음 시나리오에 대해 오프라인 모드 지원이 필요합니다.
1. 여행하는 동안과 같은 인터넷 연결을 할 수 없을 때 재생.
2. 일부 콘텐츠 공급자는 해당 국가의 국경 너머로 DRM 라이선스 배달을 허용하지 않을 수도 있습니다. 사용자를 해외 여행을 하면서 콘텐츠를 보려 할 경우 오프 라인 다운로드가 필요합니다.
3. 일부 국가에서는 인터넷 가용성 및/또는 대역폭이 여전히 제한됩니다. 사용자가 만족스러운 보기 환경을 위해 충분히 높은 해상도로 콘텐츠를 보고자 먼저 다운로드를 선택할 수도 있습니다. 이 경우 대개 문제는 네트워크 가용성이 아니라 제한된 네트워크 대역폭입니다. OTT/OVP 공급자가 오프라인 모드 지원을 요청하고 있습니다.

이 문서에서는 iOS 10 이상을 실행하는 장치를 대상으로 하는 FairPlay 스트리밍(FPS) 오프라인 모드 지원에 대해 설명합니다. 이 기능은 macOS에서 watchOS, tvOS, 또는 Safari와 같은 다른 Apple 플랫폼을 지원하지 않습니다.

## <a name="preliminary-steps"></a>준비 단계
iOS 10+ 장치에서 FairPlay에 대한 오프라인 DRM을 구현하기 전에 먼저 다음을 수행해야 합니다.
1. FairPlay에 대한 온라인 콘텐츠 보호를 숙지하도록 합니다. 이 내용은 다음 문서/예제에서 자세히 다룹니다.
- [일반적으로 사용 가능한 Azure Media Services용 Apple FairPlay 스트리밍](https://azure.microsoft.com/blog/apple-FairPlay-streaming-for-azure-media-services-generally-available/)
- [Microsoft PlayReady 또는 Apple FairPlay로 HLS 콘텐츠 보호](https://docs.microsoft.com/azure/media-services/media-services-protect-hls-with-FairPlay)
- [온라인 FPS 스트리밍에 대한 샘플](https://azure.microsoft.com/resources/samples/media-services-dotnet-dynamic-encryption-with-FairPlay/)
2. Apple Developer Network에서 FPS SDK를 가져옵니다. FPS SDK에는 두 가지 구성 요소가 들어 있습니다.
- FPS Server SDK에는 KSM(키 보안 모듈), 클라이언트 샘플, 사양 및 테스트 벡터 집합이 들어 있습니다.
- FPS Deployment Pack은 FPS 인증서, 고객별 개인 키 및 ASK(Application Secret Key, 응용 프로그램 비밀 키)를 생성하는 방법에 관한 지침과 함께 사양인 D 함수를 포함합니다. Apple은 사용이 허가된 콘텐츠 공급자에게만 FPS Deployment Pack을 발급합니다.

## <a name="configuration-in-azure-media-services"></a>Microsoft Azure Media Services의 구성
[Microsoft Azure Media Services .NET SDK](https://www.nuget.org/packages/windowsazure.mediaservices)를 통한 FPS 오프라인 모드 구성인 경우, Microsoft Azure Media Services.NET SDK v를 사용해야 합니다. 4.0.0.4 이상은 FPS 오프라인 모드를 구성하는 데 필요한 API를 제공합니다.
위의 가정에서 표시한 대로 온라인 모드 FPS 콘텐츠 보호 구성에 대한 작업 코드가 있다고 가정합니다. FPS에 대한 온라인 모드 콘텐츠 보호 구성 코드가 있다면 다음 두 가지 변경 만이 필요합니다.

## <a name="code-change-in-fairplay-configuration"></a>FairPlay 구성에서 코드 변경
오프라인 DRM 시나리오를 사용하도록 설정하는 경우 참인 objDRMSettings.EnableOfflineMode라는 "오프라인 모드 사용" 부울을 정의하겠습니다. 이 지표에 따라 FairPlay 구성을 다음과 같이 변경합니다.

```csharp
if (objDRMSettings.EnableOfflineMode)
    {
        FairPlayConfiguration = Microsoft.WindowsAzure.MediaServices.Client.FairPlay.FairPlayConfiguration.CreateSerializedFairPlayOptionConfiguration(
            objX509Certificate2,
            pfxPassword,
            pfxPasswordId,
            askId,
            iv, 
            RentalAndLeaseKeyType.PersistentUnlimited,
            0x9999);
    }
    else
    {
        FairPlayConfiguration = Microsoft.WindowsAzure.MediaServices.Client.FairPlay.FairPlayConfiguration.CreateSerializedFairPlayOptionConfiguration(
            objX509Certificate2,
            pfxPassword,
            pfxPasswordId,
            askId,
            iv);
    }
```

## <a name="code-change-in-asset-delivery-policy-configuration"></a>자산 배달 정책 구성의 코드 변경
두 번째 변경은 세 번째 키를 사전 <AssetDeliveryPolicyConfigurationKey, string>에 추가하는 것입니다.
세 번째 AssetDeliveryPolicyConfigurationKey는 아래와 같이 추가되어야 합니다. 
```csharp
// FPS offline mode
    if (drmSettings.EnableOfflineMode)
    {
        objDictionary_AssetDeliveryPolicyConfigurationKey.Add(AssetDeliveryPolicyConfigurationKey.AllowPersistentLicense, "true");
        Console.WriteLine("FPS OFFLINE MODE: AssetDeliveryPolicyConfigurationKey.AllowPersistentLicense added into asset delivery policy configuration.");
    }

    // for IAssetDelivery for FPS
    IAssetDeliveryPolicy objIAssetDeliveryPolicy = objCloudMediaContext.AssetDeliveryPolicies.Create(
            drmSettings.AssetDeliveryPolicyName,
            AssetDeliveryPolicyType.DynamicCommonEncryptionCbcs,
            AssetDeliveryProtocol.HLS,
            objDictionary_AssetDeliveryPolicyConfigurationKey);
```

이 단계 후에 FPS 자산 배달 정책의 Dictionary<AssetDeliveryPolicyConfigurationKey, string>에는 다음 세 가지 항목이 포함됩니다.
1. 사용된 FPS KSM/키 서버 및 여러 자산에서 동일한 자산 배달 정책을 다시 사용하려는지 여부 등의 요인에 따라 AssetDeliveryPolicyConfigurationKey.FairPlayBaseLicenseAcquisitionUrl 또는 AssetDeliveryPolicyConfigurationKey.FairPlayLicenseAcquisitionUrl
2. AssetDeliveryPolicyConfigurationKey.CommonEncryptionIVForCbcs
3. AssetDeliveryPolicyConfigurationKey.AllowPersistentLicense

이제 미디어 서비스 계정을 배달된 오프라인 FairPlay 라이선스에 구성합니다.

## <a name="sample-ios-player"></a>샘플 iOS 플레이어
첫째, FPS 오프라인 모드 지원은 iOS 10 이상에서만 제공됩니다. FPS 오프라인 모드에 대한 문서와 샘플을 포함하는 FPS Server SDK(v 3.0 이상)를 가져와야 합니다. 특히 FPS Server SDK(v3.0 이상)에는 오프라인 모드와 관련된 다음 두 항목이 들어 있습니다.
1. 문서: FairPlay 스트리밍 및 HTTP 라이브 스트리밍을 사용하는 오프라인 재생. Apple, 9/14/2016. FPS Server SDK v 4.0에서 이 문서는 기본 FPS 스트리밍 문서에 병합되었습니다.
2. 샘플 코드: \FairPlay Streaming Server SDK v3.1\Development\Client\HLSCatalog_With_FPS\HLSCatalog\에 있는 FPS 오프라인 모드에 대한 HLSCatalog 샘플. HLSCatalog 샘플 앱에서 다음 코드 파일은 특히 오프라인 모드 기능을 구현하기 위한 것입니다.
- AssetPersistenceManager.swift 코드 파일: AssetPersistenceManager는 다음을 보여주는 이 샘플의 기본 클래스입니다.
    - 다운로드 시작 및 취소, 사용자 장치에서 기존 자산 삭제 등을 위한 API와 같은 HLS 스트림 다운로드를 관리하는 방법.
    - 다운로드 진행률을 모니터링하는 방법.
- AssetListTableViewController.swift 및 AssetListTableViewCell.swif 코드 파일: AssetListTableViewController는 이 샘플의 기본 인터페이스입니다. 샘플이 재생, 다운로드, 삭제 및 다운로드 취소할 수 있는 자산 목록을 제공합니다. 

실행 중인 iOS 플레이어를 설정하기 위한 자세한 단계는 다음과 같습니다. FPS 서버 SDK v 4.0.1의 HLSCatalog 샘플에서 시작한다고 가정해 보겠습니다.  다음 코드를 변경해야 합니다.

HLSCatalog\Shared\Managers\ContentKeyDelegate.swift에서 다음 코드를 사용하여 `requestContentKeyFromKeySecurityModule(spcData: Data, assetID: String)` 메서드를 구현합니다. drmUr을 HLS 스트리밍 URL에 할당된 변수로 간주해 봅니다.

```swift
    var ckcData: Data? = nil
    
    let semaphore = DispatchSemaphore(value: 0)
    let postString = "spc=\(spcData.base64EncodedString())&assetId=\(assetIDString)"
    
    if let postData = postString.data(using: .ascii, allowLossyConversion: true), let drmServerUrl = URL(string: self.drmUrl) {
        var request = URLRequest(url: drmServerUrl)
        request.httpMethod = "POST"
        request.setValue(String(postData.count), forHTTPHeaderField: "Content-Length")
        request.setValue("application/x-www-form-urlencoded", forHTTPHeaderField: "Content-Type")
        request.httpBody = postData
        
        URLSession.shared.dataTask(with: request) { (data, _, error) in
            if let data = data, var responseString = String(data: data, encoding: .utf8) {
                responseString = responseString.replacingOccurrences(of: "<ckc>", with: "").replacingOccurrences(of: "</ckc>", with: "")
                ckcData = Data(base64Encoded: responseString)
            } else {
                print("Error encountered while fetching FairPlay license for URL: \(self.drmUrl), \(error?.localizedDescription ?? "Unknown error")")
            }
            
            semaphore.signal()
            }.resume()
    } else {
        fatalError("Invalid post data")
    }
    
    semaphore.wait()
    return ckcData
```

HLSCatalog\Shared\Managers\ContentKeyDelegate.swift에서 `requestApplicationCertificate()` 메서드를 구현합니다. 이 구현은 장치에 인증서(공개 키만)를 포함 또는 웹에 인증서를 호스트하는지 여부에 달려 있습니다. 다음은 테스트 샘플에서 사용된 호스팅된 응용 프로그램 인증서를 사용한 구현입니다. CertUrl을 응용 프로그램 인증서 URL을 담고 있는 변수로 간주해 봅니다.

```swift
func requestApplicationCertificate() throws -> Data {

        var applicationCertificate: Data? = nil
        do {
            applicationCertificate = try Data(contentsOf: URL(string: certUrl)!)
        } catch {
            print("Error loading FairPlay application certificate: \(error)")
        }
        
        return applicationCertificate
    }
```

최종 통합된 테스트인 경우 동영상 URL 및 응용 프로그램 인증서 URL은 "통합 테스트" 섹션에 제공됩니다.

HLSCatalog\Shared\Resources\Streams.plist에서 테스트 동영상 URL을 추가하고, 콘텐츠 키 ID의 경우, skd 프로토콜을 고유 값으로 하여 FairPlay 라이선스 획득 URL만을 사용할 수 있습니다.

![오프라인 FairPlay iOS 앱 스트림](media/media-services-protect-hls-with-offline-FairPlay/media-services-offline-FairPlay-ios-app-streams.png)

테스트 동영상 URL, FairPlay 라이선스 획득 URL 및 응용 프로그램 인증서 URL에 대해서는 자체적인 것이 설정되어 있다면 사용할 수 있으며, 또는 테스트 샘플을 포함한 다음 섹션으로 진행할 수 있습니다.

## <a name="integrated-test"></a>통합 테스트
세 가지 테스트 샘플이 다음 세 시나리오를 다루는 Azure Media Services에서 설정되었습니다.
1.  보호된 FPS–동영상, 오디오 및 대체 오디오 트랙 사용.
2.  보호된 FPS-동영상 및 오디오 사용 그러나 대체 오디오 트랙 없음.
3.  보호된 FPS-동영상만 사용, 오디오 없음.

이들 샘플은 Azure 웹앱에 호스트되는 해당 응용 프로그램 인증서를 사용하여 이 [데모 사이트](http://aka.ms/poc#22)에서 찾을 수 있습니다.
FPS Server SDK의 v3 또는 v4 샘플을 사용하여 마스터 재생 목록에 대체 오디오가 들어 있다면 오프라인 모드 동안에 오디오만을 재생합니다. 따라서 대체 오디오를 제거해야 합니다. 즉, 위의 세 가지 샘플 중에서 (2)와 (3)은 온라인과 오프라인 모드에서 모두 작동합니다. 그러나 (1)은 온라인 스트리밍이 제대로 작동하는 한편 오프라인 모드 동안에는 오디오만 재생합니다.

## <a name="faq"></a>FAQ
문제 해결을 위해 몇 가지 자주 묻는 질문:
- **왜 오프라인 모드 동안 동영상은 없이 오디오만 재생됩니까?** 이 동작은 의도적으로 샘플 앱의 것입니다. 대체 오디오 트랙이 존재할 때(HLS에 대한 경우임), 오프라인 모드 동안 iOS 10과 iOS 11은 기본 설정에 따라 오디오 트랙을 대체합니다. FPS 오프라인 모드에 대한 이 동작을 보완하려면 스트림에서 대체 오디오 트랙을 제거해야 합니다. Azure Media Services에서 이를 실행하기 위해 단순히 동적 매니페스트 필터 "audio-only=false"를 추가할 수 있습니다. 즉, HLS URL은.ism/manifest(format=m3u8-aapl,audio-only=false)로 끝납니다. 
- **audio-only=false를 추가한 후 왜 여전히 오프라인 동안 동영상 없이 오디오를 재생합니까?** CDN 캐시 키 디자인에 따라 콘텐츠가 캐시될 수도 있습니다. 캐시를 제거해야 합니다.
- **FPS 오프라인 모드 또한 iOS 10 외에도 iOS 11에서 지원됩니까?** 예, FPS 오프라인 모드는 iOS 10과 iOS 11 모두에서 지원됩니다.
- **FPS Server SDK에서 FairPlay 스트리밍 및 HTTP 라이브 스트리밍을 사용하여 오프라인 재생 문서를 찾을 수 없는 이유는 무엇인가요?** FPS Server SDK v 4부터 이 문서는 FairPlay Streaming Programming Guide 문서에 병합되었습니다.
- **마지막 매개변수는 FPS 오프라인 모드에 대한 다음 API에서 무엇을 의미합니까?**
`Microsoft.WindowsAzure.MediaServices.Client.FairPlay.FairPlayConfiguration.CreateSerializedFairPlayOptionConfiguration(objX509Certificate2, pfxPassword, pfxPasswordId, askId, iv, RentalAndLeaseKeyType.PersistentUnlimited, 0x9999);`

이 API에 대한 설명서는 [여기](https://docs.microsoft.com/en-us/dotnet/api/microsoft.windowsazure.mediaservices.client.FairPlay.FairPlayconfiguration.createserializedFairPlayoptionconfiguration?view=azure-dotnet)에서 찾을 수 있습니다. 매개 변수는 시를 단위로 하는 오프라인 임대 기간을 나타냅니다.
- **iOS 장치에서 다운로드된/오프라인 파일 구조체는 무엇입니까?** iOS 장치에 다운로드된 파일 구조체는 아래(스크린샷)와 같습니다. `_keys` 폴더는 다운로드된 FPS 라이선스, 각 라이선스 서비스 호스트에 대한 하나의 저장소 파일을 저장합니다. `.movpkg` 폴더는 오디오 및 동영상 콘텐츠를 저장합니다. 대시에 이어 숫자로 끝나는 이름의 첫 번째 폴더는 동영상 콘텐츠를 포함합니다. 숫자 값은 동영상 변환의 "PeakBandwidth"입니다. 대시에 이어 0으로 끝나는 이름의 두 번째 폴더는 오디오 콘텐츠를 포함합니다. "Data"라는 이름의 세 번째 폴더는 FPS 콘텐츠의 마스터 재생 목록을 포함합니다. Boot.xml은 `.movpkg` 폴더 콘텐츠에 대한 자세한 설명을 제공합니다.(샘플 boot.xml 파일은 아래 참조)

![오프라인 FairPlay iOS 샘플 앱 파일 구조체](media/media-services-protect-hls-with-offline-FairPlay/media-services-offline-FairPlay-file-structure.png)

샘플 boot.xml 파일:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<HLSMoviePackage xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://apple.com/IMG/Schemas/HLSMoviePackage" xsi:schemaLocation="http://apple.com/IMG/Schemas/HLSMoviePackage /System/Library/Schemas/HLSMoviePackage.xsd">
  <Version>1.0</Version>
  <HLSMoviePackageType>PersistedStore</HLSMoviePackageType>
  <Streams>
    <Stream ID="1-4DTFY3A3VDRCNZ53YZ3RJ2NPG2AJHNBD-0" Path="1-4DTFY3A3VDRCNZ53YZ3RJ2NPG2AJHNBD-0" NetworkURL="https://willzhanmswest.streaming.mediaservices.windows.net/e7c76dbb-8e38-44b3-be8c-5c78890c4bb4/MicrosoftElite01.ism/QualityLevels(127000)/Manifest(aac_eng_2_127,format=m3u8-aapl)">
      <Complete>YES</Complete>
    </Stream>
    <Stream ID="0-HC6H5GWC5IU62P4VHE7NWNGO2SZGPKUJ-310656" Path="0-HC6H5GWC5IU62P4VHE7NWNGO2SZGPKUJ-310656" NetworkURL="https://willzhanmswest.streaming.mediaservices.windows.net/e7c76dbb-8e38-44b3-be8c-5c78890c4bb4/MicrosoftElite01.ism/QualityLevels(161000)/Manifest(video,format=m3u8-aapl)">
      <Complete>YES</Complete>
    </Stream>
  </Streams>
  <MasterPlaylist>
    <NetworkURL>https://willzhanmswest.streaming.mediaservices.windows.net/e7c76dbb-8e38-44b3-be8c-5c78890c4bb4/MicrosoftElite01.ism/manifest(format=m3u8-aapl,audio-only=false)</NetworkURL>
  </MasterPlaylist>
  <DataItems Directory="Data">
    <DataItem>
      <ID>CB50F631-8227-477A-BCEC-365BBF12BCC0</ID>
      <Category>Playlist</Category>
      <Name>master.m3u8</Name>
      <DataPath>Playlist-master.m3u8-CB50F631-8227-477A-BCEC-365BBF12BCC0.data</DataPath>
      <Role>Master</Role>
    </DataItem>
  </DataItems>
</HLSMoviePackage>
```

## <a name="summary"></a>요약
이 문서에서 다음을 포함하여 FPS 오프라인 모드를 구현에 관한 자세한 단계 및 정보를 제공합니다.
1. AMS.NET API를 통한 Azure Media Services 콘텐츠 보호 구성. 이렇게 하면 AMS에서 동적 FairPlay 암호화 및 FairPlay 라이선스 배달이 구성됩니다.
2. Apple FPS 서버 SDK에서 샘플을 기반으로 하는 iOS 플레이어. 이렇게 하여 FPS 콘텐츠를 온라인 스트리밍 모드 또는 오프라인 모드로 재생할 수 있는 iOS 플레이어를 설정합니다.
3. 오프라인 모드 및 온라인 스트리밍을 모두 테스트하기 위한 샘플 FPS 비디오.
4. FPS 오프라인 모드에 대한 FAQ.
