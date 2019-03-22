---
title: 지역화 문자열 ID - Azure Active Directory B2C | Microsoft Docs
description: Azure Active Directory B2C의 사용자 지정 정책에서 api.signuporsignin의 ID를 사용하여 콘텐츠 정의의 ID를 지정하는 방법을 설명합니다.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 5f937a86e4e063ad1e8346dc629a917ae7ce8b65
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/18/2019
ms.locfileid: "58077210"
---
# <a name="localization-string-ids"></a>지역화 문자열 ID

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

**Localization** 요소를 사용하면 사용자 경험용 정책에서 여러 로캘이나 언어를 지원할 수 있습니다. 이 문서에서는 정책에서 사용할 수 있는 지역화 ID 목록을 제공합니다. UI 지역화 개념을 숙지하려면 [지역화](localization.md)를 참조하세요.

## <a name="sign-up-or-sign-in-page-elements"></a>등록 또는 로그인 페이지 요소

ID가 `api.signuporsignin`인 콘텐츠 정의에 사용되는 ID는 다음과 같습니다.

| ID | 기본값 |
| -- | ------------- |
| **local_intro_email** | 기존 계정으로 로그인 |
| **logonIdentifier_email** | 메일 주소 |
| **requiredField_email** | 이메일을 입력하세요. |
| **invalid_email** | 올바른 이메일 주소를 입력하세요. |
| **email_pattern** | ^[a-zA-Z0-9.!#$%&’'*+/=?^_\`{\|}~-]+@[a-zA-Z0-9-]+(?:\\.[a-zA-Z0-9-]+)*$ |
| **local_intro_username** | 사용자 이름으로 로그인 |
| **logonIdentifier_username** | 사용자 이름 |
| **requiredField_username** | 사용자 이름을 입력하세요. |
| **암호** | 암호 |
| **requiredField_password** | 암호를 입력하세요. |
| **invalid_password** | 입력한 암호의 형식이 잘못되었습니다. |
| **forgotpassword_link** | 암호를 잊으셨습니까? |
| **createaccount_intro** | 아직 계정이 없으세요? |
| **createaccount_link** | 지금 등록하십시오. |
| **divider_title** | 또는 |
| **cancel_message** | 사용자가 암호를 잊음 |
| **button_signin** | 로그인 |
| **social_intro** | 소셜 계정으로 로그인 |
  **remember_me** |로그인 유지|
| **unknown_error** | 로그인하는 동안 문제가 발생했습니다. 나중에 다시 시도하세요. |

다음 예제에서는 등록 또는 로그인 페이지의 몇 가지 사용자 인터페이스 요소 사용 방법을 보여 줍니다.

![등록 또는 로그인 페이지 UX 요소](./media/localization-string-ids/localization-susi.png)

ID 공급자의 ID는 사용자 경험 **ClaimsExchange** 요소에서 구성됩니다. ID 공급자의 제목을 지역화하려는 경우 **ElementType**은 `ClaimsProvider`로 설정하고 **StringId**는 `ClaimsExchange`의 ID로 설정합니다.

```XML
<OrchestrationStep Order="2" Type="ClaimsExchange">
  <Preconditions>
    <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
      <Value>objectId</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="FacebookExchange" TechnicalProfileReferenceId="Facebook-OAUTH" />
    <ClaimsExchange Id="MicrosoftExchange" TechnicalProfileReferenceId="MSA-OIDC" />
    <ClaimsExchange Id="GoogleExchange" TechnicalProfileReferenceId="Google-OAUTH" />
    <ClaimsExchange Id="SignUpWithLogonEmailExchange" TechnicalProfileReferenceId="LocalAccount" />
  </ClaimsExchanges>
</OrchestrationStep>
```

다음 예제에서는 Facebook ID 공급자를 아랍어로 지역화합니다.

```XML
<LocalizedString ElementType="ClaimsProvider" StringId="FacebookExchange">فيس بوك</LocalizedString>
```

## <a name="sign-up-or-sign-in-error-messages"></a>등록 또는 로그인 오류 메시지

| ID | 기본값 |
| -- | ------------- |
| **UserMessageIfInvalidPassword** | 암호가 올바르지 않습니다. |
| **UserMessageIfClaimsPrincipalDoesNotExist** | 계정을 찾을 수 없는 것 같습니다. |
| **UserMessageIfOldPasswordUsed** | 이전 암호를 사용한 것 같습니다. |  
| **DefaultMessage** | 잘못된 사용자 이름 또는 암호 |  
| **UserMessageIfUserAccountDisabled** | 계정이 잠겨 있습니다. 잠금 해제하려면 고객 지원 담당자에게 문의한 다음 다시 시도하세요. |  
| **UserMessageIfUserAccountLocked** | 권한 없는 사용을 방지하기 위해 계정이 일시적으로 잠겨 있습니다. 나중에 다시 시도하십시오. |  
| **AADRequestsThrottled** | 현재 요청이 너무 많습니다. 잠시 기다렸다가 다시 시도하세요. |  

## <a name="sign-up-and-self-asserted-pages-user-interface-elements"></a>등록 및 자체 어설션된 페이지 사용자 인터페이스 요소

아래에는 ID가 `api.localaccountsignup`인 콘텐츠 정의 또는 `api.selfasserted`로 시작하는 모든 콘텐츠 정의(예: `api.selfasserted.profileupdate` 및 `api.localaccountpasswordreset`)의 ID가 나와 있습니다.

| ID | 기본값 |
| -- | ------------- |
| **ver_sent** | 확인 코드를 보낸 대상: |
| **ver_but_default** | 기본값 |
| **cancel_message** | 사용자가 자체 어설션된 정보의 입력을 취소함 |
| **preloader_alt** | 기다려 주세요. |
| **ver_but_send** | 확인 코드 보내기 |
| **alert_yes** | 예 |
| **error_fieldIncorrect** | 하나 이상의 필드가 잘못 채워졌습니다. 항목을 확인하고 다시 시도하세요. |
| **year** | Year |
| **verifying_blurb** | 정보를 처리하는 동안 잠시 기다려 주세요. |
| **button_cancel** | 취소 |
| **ver_fail_no_retry** | 여러 번 틀렸습니다. 나중에 다시 시도하세요. |
| **month** | 월 |
| **ver_success_msg** | 이메일 주소를 확인했습니다. 이제 계속할 수 있습니다. |
| **months** | 1월, 2월, 3월, 4월, 5월, 6월, 7월, 8월, 9월, 10월, 11월, 12월 |
| **ver_fail_server** | 이메일 주소를 확인하는 동안 문제가 발생했습니다. 유효한 이메일 주소를 입력하고 다시 시도하세요. |
| **error_requiredFieldMissing** | 필수 필드가 누락되어 있습니다. 모든 필수 필드를 작성하고 다시 시도하세요. |
| **initial_intro** | 다음 세부 정보를 제공하세요. |
| **ver_but_resend** | 새 코드 전송 |
| **button_continue** | 생성 |
| **error_passwordEntryMismatch** | 암호 입력 필드가 일치하지 않습니다. 두 필드에 같은 암호를 입력하고 다시 시도하세요. |
| **ver_incorrect_format** | 잘못된 형식 |
| **ver_but_edit** | 이메일 변경 |
| **ver_but_verify** | 코드 확인 |
| **alert_no** | 아닙니다. |
| **ver_info_msg** | 받은 편지함으로 확인 코드를 보냈습니다. 확인 코드를 아래 입력란에 복사하세요. |
| **day** | 일 |
| **ver_fail_throttled** | 이 이메일 주소를 확인해 달라는 요청이 너무 많습니다. 잠시 기다렸다가 다시 시도하세요. |
| **helplink_text** | 이것은 무엇인가요? |
| **ver_fail_retry** | 해당 코드가 잘못되었습니다. 나중에 다시 시도하세요. |
| **alert_title** | 세부 정보 입력 취소 |
| **required_field** | 이 정보는 필수 항목입니다. |
| **alert_message** | 세부 정보 입력을 취소하시겠습니까? |
| **ver_intro_msg** | 확인이 필요합니다. [보내기] 단추를 클릭하세요. |
| **ver_input** | 확인 코드 |

## <a name="sign-up-and-self-asserted-pages-error-messages"></a>등록 및 자체 어설션된 페이지 오류 메시지

| ID | 기본값 |
| -- | ------------- |
| **UserMessageIfClaimsPrincipalAlreadyExists** | 지정한 ID를 사용하는 사용자가 이미 있습니다. 다른 ID를 선택하세요. |
| **UserMessageIfClaimNotVerified** | 확인되지 않은 클레임: {0} |
| **UserMessageIfIncorrectPattern** | {0}에 대한 잘못된 패턴 |
| **UserMessageIfMissingRequiredElement** | 필요한 {0} 요소가 없습니다. |
| **UserMessageIfValidationError** | {0}의 유효성 검사 오류 |
| **UserMessageIfInvalidInput** | {0}에 잘못된 입력이 있습니다. |
| **ServiceThrottled** | 현재 요청이 너무 많습니다. 잠시 기다린 후에 다시 시도하세요. |

다음 예제에서는 등록 페이지의 몇 가지 사용자 인터페이스 요소 사용 방법을 보여 줍니다.

![등록 페이지 UX 요소](./media/localization-string-ids/localization-sign-up.png)

다음 예제에서는 사용자가 확인 코드 보내기 단추를 클릭한 후 등록 페이지의 몇 가지 사용자 인터페이스 요소 사용 방법을 보여 줍니다.

![등록 페이지 이메일 확인 UX 요소](./media/localization-string-ids/localization-email-verification.png)


## <a name="phone-factor-authentication-page-user-interface-elements"></a>전화 인증 페이지 사용자 인터페이스 요소

ID가 `api.phonefactor`인 콘텐츠 정의용 ID는 다음과 같습니다. 

| ID | 기본값 |
| -- | ------------- |
| **button_verify** | 내게 전화 걸기 |
| **country_code_label** | 국가 코드 |
| **cancel_message** | 사용자가 다단계 인증을 취소함 |
| **text_button_send_second_code** | 새 코드 보내기 |
| **code_pattern** | \\d{6} |
| **intro_mixed** | 다음 번호가 기록에 남아 있습니다. 본인 인증을 위해 SMS 또는 전화로 코드를 보내겠습니다. |
| **intro_mixed_p** | 다음 번호가 기록에 남아 있습니다. 본인 인증을 위해 전화를 받거나 SMS로 코드를 받을 번호를 선택하세요. |
| **button_verify_code** | 코드 확인 |
| **requiredField_code** | 받은 확인 코드를 입력하세요. |
| **invalid_code** | 받은 6자리 코드를 입력하세요. |
| **button_cancel** | 취소 |
| **local_number_input_placeholder_text** | 전화 번호 |
| **button_retry** | 다시 시도 |
| **alternative_text** | 내 휴대폰이 없음 |
| **intro_phone_p** | 다음 번호가 기록에 남아 있습니다. 본인 인증을 위해 전화를 받을 번호를 선택하세요. |
| **intro_phone** | 다음 번호가 기록에 남아 있습니다. 본인 인증을 위해 전화를 걸겠습니다. |
| **enter_code_text_intro** | 아래에 인증 코드를 입력하거나  |
| **intro_entry_phone** | 전화를 걸어 인증할 수 있도록 아래에 숫자를 입력하세요. |
| **intro_entry_sms** | SMS를 통해 인증 코드를 보낼 수 있도록 아래에 숫자를 입력하세요. |
| **button_send_code** | 코드 보내기 |
| **invalid_number** | 유효한 전화 번호를 입력하세요. |
| **intro_sms** | 다음 번호가 기록에 남아 있습니다. 본인 인증을 위해 SMS로 코드를 보내겠습니다. |
| **intro_entry_mixed** | SMS 또는 전화를 통해 인증 코드를 보낼 수 있도록 아래에 숫자를 입력하세요. |
| **number_pattern** | ^\\+(?:[0-9][\\x20-]?){6,14}[0-9]$ |
| **intro_sms_p** |다음 번호가 기록에 남아 있습니다. 본인 인증을 위해 SMS로 코드를 받을 번호를 선택하세요. |
| **requiredField_countryCode** | 국가 번호를 선택하세요. |
| **requiredField_number** | 전화 번호를 입력하세요. |
| **country_code_input_placeholder_text** |국가 또는 지역 |
| **number_label** | 전화 번호 |
| **error_tryagain** | 제공한 전화 번호가 통화 중이거나 사용될 수 없습니다. 번호를 확인 후 다시 시도하세요. |
| **error_incorrect_code** | 입력한 확인 코드가 레코드와 일치하지 않습니다. 다시 시도하거나 새 코드를 요청하세요. |
| **countryList** | {\"DEFAULT\":\"국가/지역\",\"AF\":\"아프가니스탄\",\"AX\":\"올란드 제도\",\"AL\":\"알바니아\",\"DZ\":\"알제리\",\"AS\":\"아메리칸 사모아\",\"AD\":\"안도라\",\"AO\":\"안도라\",\"AI\":\"앵귈라\",\"AQ\":\"남극\",\"AG\":\"앤티가 바부다\",\"AR\":\"아르헨티나\",\"AM\":\"아르메니아\",\"AW\":\"아루바\",\"AU\":\"오스트레일리아\",\"AT\":\"오스트리아\",\"AZ\":\"오스트리아\",\"BS\":\"바하마\",\"BH\":\"바레인\",\"BD\":\"방글라데시\",\"BB\":\"바베이도스\",\"BY\":\"벨라루스\",\"BE\":\"벨기에\",\"BZ\":\"벨리스\",\"BJ\":\"베냉\",\"BM\":\"버뮤다\",\"BT\":\"부탄\",\"BO\":\"볼리비아\",\"BQ\":\"보네르\",\"BA\":\"보스니아 헤르체고비나\",\"BW\":\"보츠와나\",\"BV\":\"부베섬\",\"BR\":\"브라질\",\"IO\":\"영국령 인도양 식민지\",\"VG\":\"영국령 버진아일랜드\",\"BN\":\"브루나이\",\"BG\":\"불가리아\",\"BF\":\"부르키나파소\",\"BI\":\"부룬디\",\"CV\":\"카보베르데\",\"KH\":\"캄보디아\",\"CM\":\"카메룬\",\"CA\":\"캐나다\",\"KY\":\"케이맨 제도\",\"CF\":\"중앙 아프리카 공화국\",\"TD\":\"차드\",\"CL\":\"칠레\",\"CN\":\"중국\",\"CX\":\"크리스마스섬\",\"CC\":\"코코스 제도\",\"CO\":\"콜롬비아\",\"KM\":\"코모로스\",\"CG\":\"콩고\",\"CD\":\"콩고민주공화국\",\"CK\":\"쿡 제도\",\"CR\":\"코스타리카\",\"CI\":\"코트디부아르\",\"HR\":\"크로아티아\",\"CU\":\"쿠바\",\"CW\":\"퀴라소\",\"CY\":\"키프로스\",\"CZ\":\"체코 공화국\",\"DK\":\"덴마크\",\"DJ\":\"지부티\",\"DM\":\"도미니카\",\"DO\":\"도미니카 공화국\",\"EC\":\"에콰도르\",\"EG\":\"이집트\",\"SV\":\"엘살바도르\",\"GQ\":\"적도 기니\",\"ER\":\"에리트리아\",\"EE\":\"에스토니아\",\"ET\":\"에티오피아\",\"FK\":\"포클랜드 제도\",\"FO\":\"페로 제도\",\"FJ\":\"피지\",\"FI\":\"핀란드\",\"FR\":\"프랑스\",\"GF\":\"프랑스령 기아나\",\"PF\":\"프랑스령 폴리네시아\",\"TF\":\"프랑스 남쪽 영역\",\"GA\":\"가봉\",\"GM\":\"감비아\",\"GE\":\"그루지야\",\"DE\":\"독일\",\"GH\":\"가나\",\"GI\":\"지브롤터\",\"GR\":\"그리스\",\"GL\":\"그린란드\",\"GD\":\"그레나다\",\"GP\":\"과들루프\",\"GU\":\"괌\",\"GT\":\"과테말라\",\"GG\":\"건지\",\"GN\":\"기니\",\"GW\":\"기니비사우\",\"GY\":\"가이아나\",\"HT\":\"아이티\",\"HM\":\"허드 맥도널드 제도\",\"HN\":\"온두라스\",\"HK\":\"홍콩 특별행정구\",\"HU\":\"헝가리\",\"IS\":\"아이슬란드\",\"IN\":\"인도\",\"ID\":\"인도네시아\",\"IR\":\"이란\",\"IQ\":\"Iraq\",\"IE\":\"아일랜드\",\"IM\":\"맨 섬\",\"IL\":\"이스라엘\",\"IT\":\"이탈리아\",\"JM\":\"자메이카\",\"JP\":\"일본\",\"JE\":\"저지\",\"JO\":\"요르단\",\"KZ\":\"카자흐스탄\",\"KE\":\"케냐\",\"KI\":\"키리바시\",\"KR\":\"대한민국\",\"KW\":\"쿠웨이트\",\"KG\":\"키르기스스탄\",\"LA\":\"라오스\",\"LV\":\"라트비아\",\"LB\":\"레바논\",\"LS\":\"레소토\",\"LR\":\"라이베리아\",\"LY\":\"리비아\",\"LI\":\"리히텐슈타인\",\"LT\":\"리투아니아\",\"LU\":\"룩셈부르크\",\"MO\":\"마카오 특별행정구\",\"MK\":\"마케도니아(FYROM)\",\"MG\":\"마다가스카르\",\"MW\":\"말라위\",\"MY\":\"말레이시아\",\"MV\":\"몰디브\",\"ML\":\"말리\",\"MT\":\"몰타\",\"MH\":\"마셜 제도\",\"MQ\":\"마르티니크\",\"MR\":\"모리타니아\",\"MU\":\"모리셔스\",\"YT\":\"마요트\",\"MX\":\"멕시코\",\"FM\":\"미크로네시아\",\"MD\":\"몰도바\",\"MC\":\"모나코\",\"MN\":\"몽골\",\"ME\":\"몬테네그로\",\"MS\":\"몬트세라트\",\"MA\":\"모로코\",\"MZ\":\"모잠비크\",\"MM\":\"미얀마\",\"NA\":\"나미비아\",\"NR\":\"나우루\",\"NP\":\"네팔\",\"NL\":\"네덜란드\",\"NC\":\"뉴칼레도니아\",\"NZ\":\"뉴질랜드\",\"NI\":\"니카라과\",\"NE\":\"니제르\",\"NG\":\"나이지리아\",\"NU\":\"니우에\",\"NF\":\"노퍽섬\",\"KP\":\"북한\",\"MP\":\"북마리아나제도\",\"NO\":\"노르웨이\",\"OM\":\"오만\",\"PK\":\"파키스탄\",\"PW\":\"팔라우\",\"PS\":\"팔레스타인 자치 정부\",\"PA\":\"파나마\",\"PG\":\"파푸아뉴기니\",\"PY\":\"파라과이\",\"PE\":\"페루\",\"PH\":\"필리핀\",\"PN\":\"핏케언 제도\",\"PL\":\"폴란드\",\"PT\":\"포르투갈\",\"PR\":\"푸에르토리코\",\"QA\":\"카타르\",\"RE\":\"리유니온\",\"RO\":\"루마니아\",\"RU\":\"러시아\",\"RW\":\"르완다\",\"BL\":\"생바르텔레미\",\"KN\":\"세인트키츠 네비스\",\"LC\":\"세인트루시아\",\"MF\":\"셍마르탱\",\"PM\":\"생피에르 미클롱\",\"VC\":\"세인트빈센트그레나딘\",\"WS\":\"사모아\",\"SM\":\"산마리노\",\"ST\":\"상투메 프린시페\",\"SA\":\"사우디아라비아\",\"SN\":\"세네갈\",\"RS\":\"세르비아\",\"SC\":\"세이셸\",\"SL\":\"시에라리온\",\"SG\":\"싱가포르\",\"SX\":\"신트마르턴\",\"SK\":\"슬로바키아\",\"SI\":\"슬로베니아\",\"SB\":\"솔로몬 제도\",\"SO\":\"소말리아\",\"ZA\":\"남아프리카\",\"GS\":\"사우스조지아 사우스샌드위치 제도\",\"SS\":\"남수단\",\"ES\":\"스페인\",\"LK\":\"스리랑카\",\"SH\":\"세인트헬레나 어센션 트리스탄다쿠냐\",\"SD\":\"수단\",\"SR\":\"수리남\",\"SJ\":\"스발바르\",\"SZ\":\"스와질란드\",\"SE\":\"스웨덴\",\"CH\":\"스위스\",\"SY\":\"시리아\",\"TW\":\"대만\",\"TJ\":\"타지키스탄\",\"TZ\":\"탄자니아\",\"TH\":\"태국\",\"TL\":\"동티모르 \",\"TG\":\"토고\",\"TK\":\"토켈라우\",\"TO\":\"통가\",\"TT\":\"트리니다드토바고\",\"TN\":\"튀니지\",\"TR\":\"터키\",\"TM\":\"투르크메니스탄\",\"TC\":\"터크스 케이커스 제도\",\"TV\":\"투발루\",\"UM\":\"미국령 해외 제도\",\"VI\":\"미국령 버진아일랜드\",\"UG\":\"우간다\",\"UA\":\"우크라이나\",\"AE\":\"아랍에미리트\",\"GB\":\"영국\",\"US\":\"미국\",\"UY\":\"우루과이\",\"UZ\":\"우즈베키스탄\",\"VU\":\"바누아투\",\"VA\":\"바티칸 시국\",\"VE\":\"베네수엘라\",\"VN\":\"베트남\",\"WF\":\"월리스 푸투나\",\"YE\":\"예멘\",\"ZM\":\"잠비아\",\"ZW\":\"짐바브웨\"} |
| **error_448** | 제공한 전화 번호에 연결할 수 없습니다. |
| **error_449** | 사용자가 다시 시도 횟수를 초과했습니다. |
| **verification_code_input_placeholder_text** | 확인 코드 |

다음 예제에서는 MFA 등록 페이지의 몇 가지 사용자 인터페이스 요소 사용 방법을 보여 줍니다.

![등록 페이지 이메일 확인 UX 요소](./media/localization-string-ids/localization-mfa1.png)

다음 예제에서는 MFA 유효성 검사 페이지의 몇 가지 사용자 인터페이스 요소 사용 방법을 보여 줍니다.

![등록 페이지 이메일 확인 UX 요소](./media/localization-string-ids/localization-mfa2.png)







