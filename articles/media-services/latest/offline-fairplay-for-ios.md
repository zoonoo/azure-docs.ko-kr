---
title: 오프 라인 Apple FairPlay를 사용 하 여 HLS 콘텐츠 보호-Azure | Microsoft Docs
description: 이 항목에서는 Azure Media Services 사용 하 여 오프 라인 모드에서 Apple FairPlay를 사용 하 여 HLS (HTTP 라이브 스트리밍) 콘텐츠를 동적으로 암호화 하는 방법을 간략하게 설명 합니다.
services: media-services
keywords: HLS, DRM, FairPlay 스트리밍 (FPS), 오프 라인, iOS 10
documentationcenter: ''
author: willzhan
manager: steveng
editor: ''
ms.assetid: 7c3b35d9-1269-4c83-8c91-490ae65b0817
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/08/2019
ms.author: willzhan
ms.openlocfilehash: 83fa8c9c6d98728d48ff4ed8993963cdbd522724
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74974124"
---
# <a name="offline-fairplay-streaming-for-ios"></a>IOS 용 오프 라인 FairPlay 스트리밍 

 Azure Media Services은 다음을 다루는 잘 디자인 된 [콘텐츠 보호 서비스](https://azure.microsoft.com/services/media-services/content-protection/) 집합을 제공 합니다.

- Microsoft PlayReady
- Google widev
    
    Widevine는 Google i n c .에서 제공 하는 서비스로, Google, i n c .의 서비스 약관 및 개인 정보 취급 방침을 따릅니다.
- Apple FairPlay
- AES-128-titkosítás

DRM (디지털 권한 관리)/콘텐츠 AES (Advanced Encryption Standard) 암호화는 다양 한 스트리밍 프로토콜에 대 한 요청에 따라 동적으로 수행 됩니다. DRM 라이선스/a s e 암호 해독 키 배달 서비스도 Media Services에 의해 제공 됩니다.

다양 한 스트리밍 프로토콜에 대 한 온라인 스트리밍의 콘텐츠를 보호 하는 것 외에도 보호 된 콘텐츠에 대 한 오프 라인 모드도 자주 요청 되는 기능입니다. 다음 시나리오에는 오프 라인 모드 지원이 필요 합니다.

* 여행 중과 같은 인터넷 연결을 사용할 수 없을 때 재생 합니다.
* 일부 콘텐츠 공급자는 국가/지역 테두리를 넘어 DRM 라이선스 배달을 허용 하지 않을 수 있습니다. 사용자가 국가/지역 외부로 여행 하는 동안 콘텐츠를 시청 하려는 경우 오프 라인 다운로드가 필요 합니다.
* 일부 국가/지역에서는 인터넷 가용성 및/또는 대역폭이 여전히 제한 됩니다. 사용자는 만족 스러운 보기 환경을 위해 충분히 높은 해상도로 콘텐츠를 볼 수 있도록 먼저를 다운로드 하도록 선택할 수 있습니다. 이 경우 문제는 네트워크 가용성이 아니라 제한 된 네트워크 대역폭입니다. 이상 (이상) (OVP)/online 비디오 플랫폼 (OVP) 공급자가 오프 라인 모드 지원을 요청 합니다.

이 문서에서는 iOS 10 이상을 실행 하는 장치를 대상으로 하는 FairPlay Streaming (FPS) 오프 라인 모드 지원에 대해 설명 합니다. 이 기능은 macOS에서 watchOS, tvOS 또는 Safari와 같은 다른 Apple 플랫폼에 대해 지원 되지 않습니다.

> [!NOTE]
> 오프 라인 DRM은 콘텐츠를 다운로드할 때 라이선스에 대 한 단일 요청을 만드는 경우에만 청구 됩니다. 모든 오류는 청구 되지 않습니다.

## <a name="prerequisites"></a>Előfeltételek

IOS 10 이상 장치에서 FairPlay에 대 한 오프 라인 DRM을 구현 하기 전에 다음을 수행 합니다.

* FairPlay에 대 한 온라인 콘텐츠 보호를 검토 합니다. 

    - [Apple FairPlay-licencek követelményei és konfigurálása](fairplay-license-overview.md)
    - [A DRM dinamikus titkosítási és licenctovábbítási szolgáltatás használata](protect-with-drm.md)
    - 온라인 FPS 스트리밍의 구성을 포함 하는 .NET 샘플: [ConfigureFairPlayPolicyOptions](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L505)
* Apple Developer Network에서 FPS SDK를 가져옵니다. FPS SDK에는 두 가지 구성 요소가 포함 되어 있습니다.

    - FP 서버 SDK에는 KSM (키 보안 모듈), 클라이언트 샘플, 사양 및 테스트 벡터 집합이 포함 되어 있습니다.
    - Fp 배포 팩은 FPS 인증서, 고객별 개인 키 및 응용 프로그램 비밀 키를 생성 하는 방법에 대 한 지침과 함께 D 함수 사양을 포함 합니다. Apple은 사용이 허가 된 콘텐츠 공급자 에게만 FPS 배포 팩을 발급 합니다.
* 복제 https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git 입니다. 

    FairPlay 구성을 추가 하려면 [.net을 사용 하 여 DRM으로 암호화](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/tree/master/AMSV3Tutorials/EncryptWithDRM) 에서 코드를 수정 해야 합니다.  

## <a name="configure-content-protection-in-azure-media-services"></a>Azure Media Services에서 콘텐츠 보호 구성

[GetOrCreateContentKeyPolicyAsync](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L189) 메서드에서 다음을 수행 합니다.

FairPlay 정책 옵션을 구성 하는 코드의 주석 처리를 제거 합니다.

```csharp
ContentKeyPolicyFairPlayConfiguration fairplayConfig = ConfigureFairPlayPolicyOptions();
```

또한 CBCS ContentKeyPolicyOption을 ContentKeyPolicyOptions 목록에 추가 하는 코드의 주석 처리를 제거 합니다.

```csharp
options.Add(
    new ContentKeyPolicyOption()
    {
        Configuration = fairplayConfig,
        Restriction = restriction,
        Name = "ContentKeyPolicyOption_CBCS"
    });
```

## <a name="enable-offline-mode"></a>오프 라인 모드 사용

오프 라인 모드를 사용 하도록 설정 하려면 사용자 지정 StreamingPolicy를 만들고 [CreateStreamingLocatorAsync](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L563)에서 Streamingpolicy를 만들 때 이름을 사용 합니다.
 
```csharp
CommonEncryptionCbcs objStreamingPolicyInput= new CommonEncryptionCbcs()
{
    Drm = new CbcsDrmConfiguration()
    {
        FairPlay = new StreamingPolicyFairPlayConfiguration()
        {
            AllowPersistentLicense = true  //this enables offline mode
        }
    },
    EnabledProtocols = new EnabledProtocols()
    {
        Hls = true,
        Dash = true //Even though DASH under CBCS is not supported for either CSF or CMAF, HLS-CMAF-CBCS uses DASH-CBCS fragments in its HLS playlist
    },

    ContentKeys = new StreamingPolicyContentKeys()
    {
        //Default key must be specified if keyToTrackMappings is present
        DefaultKey = new DefaultKey()
        {
            Label = "CBCS_DefaultKeyLabel"
        }
    }

```

이제 Media Services 계정은 오프 라인 FairPlay 라이선스를 제공 하도록 구성 됩니다.

## <a name="sample-ios-player"></a>샘플 iOS 플레이어

FPS 오프 라인 모드 지원은 iOS 10 이상 에서만 사용할 수 있습니다. FPS Server SDK (버전 3.0 이상)에는 FPS 오프 라인 모드에 대 한 문서와 샘플이 포함 되어 있습니다. 특히 FPS Server SDK (버전 3.0 이상)에는 오프 라인 모드와 관련 된 다음 두 항목이 포함 되어 있습니다.

* 문서: "FairPlay Streaming and HTTP 라이브 스트리밍를 사용 하 여 오프 라인 재생" 2016 년 9 월 14 일 Apple. FPS Server SDK 버전 4.0에서이 문서는 주 FPS 문서로 병합 됩니다.
* 샘플 코드: HLSCatalog 샘플 (Apple의 FPS Server SDK의 일부) \FairPlay Streaming Server SDK 버전 3.1 \ Development\Client\ HLSCatalog_With_FPS \HLSCatalog\.에서 FPS 오프 라인 모드에 대 한 HLSCatalog 샘플 앱에서 다음 코드 파일은 오프 라인 모드 기능을 구현 하는 데 사용 됩니다.

    - AssetPersistenceManager. swift 코드 파일: AssetPersistenceManager는 다음을 수행 하는 방법을 보여 주는이 샘플의 주 클래스입니다.

        - 다운로드를 시작 및 취소 하 고 장치에서 기존 자산을 삭제 하는 데 사용 되는 Api와 같은 HLS 스트림 다운로드를 관리 합니다.
        - 다운로드 진행률을 모니터링 합니다.
    - AssetListTableViewController. swift 및 Assetlisttableviewcontroller. swift 코드 파일: AssetListTableViewController는이 샘플의 기본 인터페이스입니다. 샘플에서 다운로드를 재생, 다운로드, 삭제 또는 취소 하는 데 사용할 수 있는 자산 목록을 제공 합니다. 

다음 단계에서는 실행 중인 iOS 플레이어를 설정 하는 방법을 보여 줍니다. FPS Server SDK 버전 4.0.1의 HLSCatalog 샘플에서 시작 하는 경우 다음 코드를 변경 합니다.

HLSCatalog\Shared\Managers\ContentKeyDelegate.swift에서 다음 코드를 사용 하 여 `requestContentKeyFromKeySecurityModule(spcData: Data, assetID: String)` 메서드를 구현 합니다. "DrmUr"은 HLS URL에 할당 된 변수를 사용 합니다.

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

HLSCatalog\Shared\Managers\ContentKeyDelegate.swift에서 `requestApplicationCertificate()`메서드를 구현 합니다. 이러한 구현은 인증서 (공개 키만)를 장치와 함께 포함할지 아니면 웹에서 인증서를 호스트 하는지에 따라 달라 집니다. 다음 구현에서는 테스트 샘플에 사용 된 호스팅된 응용 프로그램 인증서를 사용 합니다. "CertUrl"을 응용 프로그램 인증서의 URL이 포함 된 변수로 사용 합니다.

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

최종 통합 테스트의 경우 비디오 URL과 응용 프로그램 인증서 URL이 모두 "통합 테스트" 섹션에 제공 됩니다.

HLSCatalog\Shared\Resources\Streams.plist에서 테스트 비디오 URL을 추가 합니다. 콘텐츠 키 ID에 대해 FairPlay 라이선스 획득 URL을 사용 하 여 skd 프로토콜을 고유 값으로 사용 합니다.

![오프 라인 FairPlay iOS 앱 스트림](media/offline-fairplay-for-ios/offline-fairplay-ios-app-streams.png)

사용자 고유의 테스트 비디오 URL, FairPlay 라이선스 취득 URL 및 응용 프로그램 인증서 URL을 사용 합니다 (설정 된 경우). 또는 테스트 샘플을 포함 하는 다음 섹션으로 진행할 수 있습니다.

## <a name="integrated-test"></a>통합 테스트

Media Services의 세 가지 테스트 샘플은 다음 세 가지 시나리오를 다룹니다.

* FP 보호, 비디오, 오디오 및 대체 오디오 트랙 사용
* 비디오와 오디오를 사용 하지만 대체 오디오 트랙이 없는 FPS 보호
* FPS로 보호 됨, 비디오 전용 및 오디오 없음

이러한 샘플은 Azure 웹 앱에서 호스트 되는 해당 응용 프로그램 인증서를 사용 하 여 [이 데모 사이트](https://aka.ms/poc#22)에서 찾을 수 있습니다.
FPS Server SDK의 버전 3 또는 버전 4 샘플을 사용 하 여 마스터 재생 목록에 대체 오디오가 포함 된 경우 오프 라인 모드 동안에는 오디오만 재생 합니다. 따라서 대체 오디오를 제거 해야 합니다. 즉, 이전에 나열 된 두 번째와 세 번째 샘플은 온라인 및 오프 라인 모드에서 작동 합니다. 온라인 스트리밍은 정상적으로 작동 하는 반면, 먼저 나열 된 샘플은 오프 라인 모드 동안에만 오디오를 재생 합니다.

## <a name="faq"></a>Gyakori kérdések

다음 질문과 대답은 문제 해결에 대 한 지원을 제공 합니다.

- **오프 라인 모드에서 오디오만 재생 되 고 비디오는 재생 되지 않는 이유는 무엇 인가요?** 이 동작은 샘플 앱을 설계 하는 것 처럼 보입니다. 오프 라인 모드에서 대체 오디오 트랙이 있는 경우 (HLS의 경우) iOS 10과 iOS 11은 모두 대체 오디오 트랙을 기본값으로 설정 합니다. FPS 오프 라인 모드에 대 한이 동작을 보완 하려면 스트림에서 대체 오디오 트랙을 제거 합니다. Media Services에서이 작업을 수행 하려면 동적 매니페스트 필터 "오디오만 = false"를 추가 합니다. 즉, HLS URL은. p s/매니페스트 (format = m3u8-aapl-v3-aapl, audio 전용 = false)로 끝납니다. 
- **오디오 전용 = false를 추가한 후에도 오프 라인 모드에서 비디오가 없는 오디오만 재생 하는 이유는 무엇 인가요?** CDN (content delivery network) 캐시 키 디자인에 따라 콘텐츠가 캐시 될 수 있습니다. 캐시를 제거 합니다.
- **Ios 10 외에도 iOS 11에서 FPS 오프 라인 모드도 지원 되나요?** Igen. FP (FPS) 오프 라인 모드는 iOS 10 및 iOS 11에서 지원 됩니다.
- **FPS Server SDK에서 "FairPlay 스트리밍 및 HTTP 라이브 스트리밍를 사용 하 여 오프 라인 재생" 문서를 찾을 수 없는 이유는 무엇 인가요?** FPS Server SDK 버전 4부터이 문서는 "FairPlay Streaming 프로그래밍 가이드"에 병합 되었습니다.
- **IOS 장치에서 다운로드/오프 라인 파일 구조는 무엇 인가요?** IOS 장치에서 다운로드 한 파일 구조는 다음 스크린샷 처럼 보입니다. `_keys` 폴더에는 다운로드 한 FPS 라이선스와 각 라이선스 서비스 호스트에 대 한 하나의 저장소 파일이 저장 됩니다. `.movpkg` 폴더는 오디오 및 비디오 콘텐츠를 저장 합니다. 이름 앞에 대시로 끝나는 이름의 첫 번째 폴더는 비디오 콘텐츠를 포함 합니다. 숫자 값은 비디오 변환의 PeakBandwidth입니다. 대시 뒤에 0이 오는 이름의 두 번째 폴더는 오디오 콘텐츠를 포함 합니다. "Data" 라는 세 번째 폴더에는 FPS 콘텐츠의 마스터 재생 목록이 포함 되어 있습니다. 마지막으로, boot .xml은 `.movpkg` 폴더 내용에 대 한 전체 설명을 제공 합니다. 

![오프 라인 FairPlay iOS 샘플 앱 파일 구조](media/offline-fairplay-for-ios/offline-fairplay-file-structure.png)

샘플 부팅 .xml 파일:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<HLSMoviePackage xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://apple.com/IMG/Schemas/HLSMoviePackage" xsi:schemaLocation="http://apple.com/IMG/Schemas/HLSMoviePackage /System/Library/Schemas/HLSMoviePackage.xsd">
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

## <a name="next-steps"></a>Következő lépések

Az [AES-128 használatával történő védelem](protect-with-aes128.md) ismertetése
