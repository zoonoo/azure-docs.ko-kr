---
title: Widevine 라이선스 템플릿 개요 | Microsoft Docs
description: 이 항목에서는 Widevine 라이선스를 구성 하는 데 사용 되는 widevine 라이선스 템플릿에 대 한 개요를 제공 합니다.
author: juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: 0e6f1f05-7ed6-4ed6-82a0-0cc2182b075a
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: c7511279e66ab598e4ae3c26f053915b7393b39d
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74978393"
---
# <a name="widevine-license-template-overview"></a>Widevine 라이선스 템플릿 개요 
Azure Media Services를 사용 하 여 Google Widevine 라이선스를 구성 하 고 요청할 수 있습니다. 플레이어에서 Widevine 보호 된 콘텐츠를 재생 하려고 하면 라이선스 배달 서비스로 요청을 보내 라이선스를 얻습니다. 라이선스 서비스에서 요청을 승인 하는 경우 서비스에서 라이선스를 발급 합니다. 클라이언트에 전송 되 고 지정 된 콘텐츠의 암호를 해독 하 고 재생 하는 데 사용 됩니다.

Widevine 라이선스 요청은 JSON 메시지로 형식이 지정 됩니다.  

>[!NOTE]
> 값 없이 "{}"로 빈 메시지를 만들 수 있습니다. 그러면 기본값으로 라이선스 템플릿이 만들어집니다. 기본값은 대부분의 경우에 적용 됩니다. Microsoft 기반 라이선스 배달 시나리오는 항상 기본값을 사용 해야 합니다. "Provider" 및 "content_id" 값을 설정 해야 하는 경우 공급자는 Widevine 자격 증명과 일치 해야 합니다.

    {  
       "payload": "<license challenge>",
       "content_id": "<content id>" 
       "provider": "<provider>"
       "allowed_track_types": "<types>",
       "content_key_specs": [  
          {  
             "track_type": "<track type 1>"
          },
          {  
             "track_type": "<track type 2>"
          },
          …
       ],
       "policy_overrides": {  
          "can_play": <can play>,
          "can persist": <can persist>,
          "can_renew": <can renew>,
          "rental_duration_seconds": <rental duration>,
          "playback_duration_seconds": <playback duration>,
          "license_duration_seconds": <license duration>,
          "renewal_recovery_duration_seconds": <renewal recovery duration>,
          "renewal_server_url": "<renewal server url>",
          "renewal_delay_seconds": <renewal delay>,
          "renewal_retry_interval_seconds": <renewal retry interval>,
          "renew_with_usage": <renew with usage>
       }
    }

## <a name="json-message"></a>JSON 메시지
| Név | Value (Díj) | Leírás |
| --- | --- | --- |
| 페이로드와 |Base64 인코딩 문자열 |클라이언트에서 보낸 라이선스 요청입니다. |
| content_id |Base64 인코딩 문자열 |각 content_key_specs에 대 한 키 ID 및 콘텐츠 키를 파생 시키는 데 사용 되는 식별자입니다. track_type. |
| 공급자 |sztring |콘텐츠 키와 정책을 조회 하는 데 사용 됩니다. Microsoft 키 배달이 Widevine 라이선스 배달에 사용 되는 경우이 매개 변수는 무시 됩니다. |
| policy_name |sztring |이전에 등록 된 정책의 이름입니다. Választható. |
| allowed_track_types |열거형 |SD_ONLY 또는 SD_HD. 라이선스에 포함 되는 콘텐츠 키를 제어 합니다. |
| content_key_specs |JSON 구조의 배열 "콘텐츠 키 사양" 섹션을 참조 하세요.  |반환할 콘텐츠 키에 대 한 세밀 한 제어입니다. 자세한 내용은 "콘텐츠 키 사양" 섹션을 참조 하세요. Allowed_track_types 및 content_key_specs 값 중 하나만 지정할 수 있습니다. |
| use_policy_overrides_exclusively |부울, true 또는 false |Policy_overrides에 지정 된 정책 특성을 사용 하 고 이전에 저장 된 모든 정책을 생략 합니다. |
| policy_overrides |JSON 구조 "정책 재정의" 섹션을 참조 하세요. |이 라이선스에 대 한 정책 설정입니다.  이 자산에 미리 정의 된 정책이 있는 경우 이러한 지정 된 값이 사용 됩니다. |
| session_init |JSON 구조 "세션 초기화" 섹션을 참조 하세요. |선택적 데이터가 라이선스에 전달 됩니다. |
| parse_only |부울, true 또는 false |라이선스 요청이 구문 분석 되지만 라이선스는 발급 되지 않습니다. 그러나 라이선스 요청의 값이 응답에 반환 됩니다. |

## <a name="content-key-specs"></a>콘텐츠 키 사양
기존 정책이 있는 경우 콘텐츠 키 사양에 값을 지정할 필요가 없습니다. 이 콘텐츠와 연결 된 기존 정책은 HDCP (고대역폭 디지털 Content Protection) 및 CGMS (Copy General Management System)와 같은 출력 보호를 결정 하는 데 사용 됩니다. 기존 정책이 Widevine 라이선스 서버에 등록 되지 않은 경우 콘텐츠 공급자는 라이선스 요청에 값을 삽입할 수 있습니다.   

Use_policy_overrides_exclusively 옵션에 관계 없이 모든 트랙에 대해 각 content_key_specs 값을 지정 해야 합니다. 

| Név | Value (Díj) | Leírás |
| --- | --- | --- |
| content_key_specs. track_type |sztring |트랙 유형 이름입니다. 라이선스 요청에 content_key_specs를 지정 하는 경우 모든 추적 유형을 명시적으로 지정 해야 합니다. 이렇게 하지 않으면 지난 10 초 동안 재생에 실패 합니다. |
| content_key_specs  <br/> security_level |uint32 |재생을 위한 클라이언트 견고성 요구 사항을 정의 합니다. <br/> -소프트웨어 기반 흰색 상자 암호화가 필요 합니다. <br/> -소프트웨어 암호화 및 난독 처리 된 디코더가 필요 합니다. <br/> -하드웨어 기반의 신뢰할 수 있는 실행 환경에서 키 자료 및 암호화 작업을 수행 해야 합니다. <br/> -하드웨어 기반의 신뢰할 수 있는 실행 환경에서 콘텐츠의 암호화 및 디코딩을 수행 해야 합니다.  <br/> -미디어의 암호화, 디코딩 및 모든 처리 (압축 및 압축 해제)는 하드웨어 지원 신뢰할 수 있는 실행 환경 내에서 처리 되어야 합니다. |
| content_key_specs <br/> required_output_protection. hdc |string, HDCP_NONE, HDCP_V1 중 하나 HDCP_V2 |HDCP가 필요한 지 여부를 나타냅니다. |
| content_key_specs <br/>kulcs |인코딩이<br/>인코딩된 문자열 |이 트랙에 사용할 콘텐츠 키입니다. 지정 하는 경우 track_type 또는 key_id 필요 합니다. 콘텐츠 공급자는이 옵션을 사용 하 여 Widevine 라이선스 서버에서 키를 생성 하거나 조회 하는 대신이 트랙에 대 한 콘텐츠 키를 삽입할 수 있습니다. |
| content_key_specs key_id |Base64 인코딩된 문자열 binary, 16 바이트 |키의 고유 식별자입니다. |

## <a name="policy-overrides"></a>정책 재정의
| Név | Value (Díj) | Leírás |
| --- | --- | --- |
| policy_overrides. can_play |부울, true 또는 false |콘텐츠 재생이 허용 됨을 나타냅니다. Az alapértelmezett érték a false (hamis). |
| policy_overrides. can_persist |부울, true 또는 false |오프 라인에서 사용 하기 위해 라이선스가 비휘발성 저장소에 유지 될 수 있음을 나타냅니다. Az alapértelmezett érték a false (hamis). |
| policy_overrides. can_renew |부울, true 또는 false |이 라이선스에 대 한 갱신이 허용 됨을 나타냅니다. True 이면 라이선스의 기간을 하트 비트로 확장할 수 있습니다. Az alapértelmezett érték a false (hamis). |
| policy_overrides. license_duration_seconds |int64 |이 특정 라이선스의 시간 창을 나타냅니다. 값이 0 이면 기간에 제한이 없음을 나타냅니다. 기본값은 0입니다. |
| policy_overrides. rental_duration_seconds |int64 |재생을 허용 하는 동안 시간 창을 나타냅니다. 값이 0 이면 기간에 제한이 없음을 나타냅니다. 기본값은 0입니다. |
| policy_overrides. playback_duration_seconds |int64 |라이선스 기간 내에서 재생이 시작 된 후의 보기 기간입니다. 값이 0 이면 기간에 제한이 없음을 나타냅니다. 기본값은 0입니다. |
| policy_overrides. renewal_server_url |sztring |이 라이선스에 대 한 모든 하트 비트 (갱신) 요청은 지정 된 URL로 전달 됩니다. 이 필드는 can_renew true 인 경우에만 사용 됩니다. |
| policy_overrides. renewal_delay_seconds |int64 |갱신을 먼저 시도 하기 전까지 license_start_time 된 시간 (초)입니다. 이 필드는 can_renew true 인 경우에만 사용 됩니다. 기본값은 0입니다. |
| policy_overrides. renewal_retry_interval_seconds |int64 |오류가 발생 한 경우 후속 라이선스 갱신 요청 사이의 지연 시간 (초)을 지정 합니다. 이 필드는 can_renew true 인 경우에만 사용 됩니다. |
| policy_overrides. renewal_recovery_duration_seconds |int64 |갱신을 시도 하는 동안 재생을 계속할 수 있는 시간 창으로, 라이선스 서버와의 백 엔드 문제로 인해 실패 합니다. 값이 0 이면 기간에 제한이 없음을 나타냅니다. 이 필드는 can_renew true 인 경우에만 사용 됩니다. |
| policy_overrides. renew_with_usage |부울, true 또는 false |사용이 시작 될 때 갱신을 위해 라이선스가 전송 됨을 나타냅니다. 이 필드는 can_renew true 인 경우에만 사용 됩니다. |

## <a name="session-initialization"></a>세션 초기화
| Név | Value (Díj) | Leírás |
| --- | --- | --- |
| provider_session_token |Base64 인코딩 문자열 |이 세션 토큰은 라이선스에 다시 전달 되 고 후속 갱신에 존재 합니다. 세션 토큰은 세션을 넘어 지속 되지 않습니다. |
| provider_client_token |Base64 인코딩 문자열 |라이선스 응답으로 다시 보낼 클라이언트 토큰입니다. 라이선스 요청에 클라이언트 토큰이 포함 된 경우이 값은 무시 됩니다. 클라이언트 토큰은 라이선스 세션 이상으로 유지 됩니다. |
| override_provider_client_token |부울, true 또는 false |False이 고 라이선스 요청에 클라이언트 토큰이 포함 된 경우이 구조에서 클라이언트 토큰이 지정 된 경우에도 요청의 토큰을 사용 합니다. True 이면 항상이 구조체에 지정 된 토큰을 사용 합니다. |

## <a name="configure-your-widevine-licenses-by-using-net-types"></a>.NET 형식을 사용 하 여 Widevine 라이선스 구성
Media Services은 Widevine 라이선스를 구성 하는 데 사용할 수 있는 .NET Api를 제공 합니다. 

### <a name="classes-as-defined-in-the-media-services-net-sdk"></a>Media Services .NET SDK에 정의 된 클래스
다음 클래스는 이러한 형식에 대 한 정의입니다.

    public class WidevineMessage
    {
        public WidevineMessage();

        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public AllowedTrackTypes? allowed_track_types { get; set; }
        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public ContentKeySpecs[] content_key_specs { get; set; }
        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public object policy_overrides { get; set; }
    }

    [JsonConverter(typeof(StringEnumConverter))]
    public enum AllowedTrackTypes
    {
        SD_ONLY = 0,
        SD_HD = 1
    }
    public class ContentKeySpecs
    {
        public ContentKeySpecs();

        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public string key_id { get; set; }
        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public RequiredOutputProtection required_output_protection { get; set; }
        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public int? security_level { get; set; }
        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public string track_type { get; set; }
    }

    public class RequiredOutputProtection
    {
        public RequiredOutputProtection();

        public Hdcp hdcp { get; set; }
    }

    [JsonConverter(typeof(StringEnumConverter))]
    public enum Hdcp
    {
        HDCP_NONE = 0,
        HDCP_V1 = 1,
        HDCP_V2 = 2
    }

### <a name="example"></a>Példa
다음 예제에서는 .NET Api를 사용 하 여 간단한 Widevine 라이선스를 구성 하는 방법을 보여 줍니다.

    private static string ConfigureWidevineLicenseTemplate()
    {
        var template = new WidevineMessage
        {
            allowed_track_types = AllowedTrackTypes.SD_HD,
            content_key_specs = new[]
            {
                new ContentKeySpecs
                {
                    required_output_protection = new RequiredOutputProtection { hdcp = Hdcp.HDCP_NONE},
                    security_level = 1,
                    track_type = "SD"
                }
            },
            policy_overrides = new
            {
                can_play = true,
                can_persist = true,
                can_renew = false
            }
        };

        string configuration = JsonConvert.SerializeObject(template);
        return configuration;
    }

## <a name="additional-notes"></a>További megjegyzések

* Widevine는 Google i n c .에서 제공 하는 서비스로, Google, i n c .의 서비스 약관 및 개인 정보 취급 방침을 따릅니다.

## <a name="media-services-learning-paths"></a>Media Services képzési tervek
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Lásd még:
[PlayReady és/vagy Widevine Dynamic Common Encryption használata](media-services-protect-with-playready-widevine.md)

