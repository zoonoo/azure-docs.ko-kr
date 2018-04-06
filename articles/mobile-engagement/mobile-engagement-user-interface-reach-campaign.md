---
title: Azure Mobile Engagement 사용자 인터페이스 - 도달률 캠페인
description: Azure Mobile Engagement를 사용하여 푸시 알림 캠페인을 만들고 관리하는 방법을 알아봅니다.
services: mobile-engagement
documentationcenter: ''
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 2fe124a2-a86f-4136-81ba-a9d298ec798a
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 9d457a8d5f68f70f6bf7a5f086b5e1d65bd1ea95
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2018
---
# <a name="how-to-create-and-manage-push-notification-campaigns"></a>푸시 알림 캠페인을 만들고 관리하는 방법
> [!IMPORTANT]
> Azure Mobile Engagement는 2018/3/31에 사용이 중지됩니다. 이 페이지는 이후에 삭제됩니다.
> 

UI의 도달률 섹션을 사용하면 복합 수식을 통해 푸시 알림을 보내는 데 필요한 모든 정보를 제공하여 새 푸시 캠페인을 만들 수 있습니다. 푸시 캠페인 옵션은 알림, 설문 조사, 데이터 푸시 및 타일(Windows Phone에만 해당)의 4가지 캠페인 유형에 따라 약간씩 다릅니다.

### <a name="option-applies-to"></a>옵션 적용 대상
* 언어: 모두(알림, 설문 조사, 데이터 푸시, 타일)
* 캠페인: 모두(알림, 설문 조사, 데이터 푸시, 타일)
* 알림: 알림, 설문 조사
* 콘텐츠: 각 캠페인 유형에서 고유함
* 대상: 모두(알림, 설문 조사, 데이터 푸시, 타일)
* 시간 프레임: 알림, 설문 조사, 타일
* 테스트: 모두(알림, 설문 조사, 데이터 푸시, 타일)

![도달률 캠페인1][20]

## <a name="languages"></a>언어
언어 드롭다운 메뉴를 사용하여 서로 다른 언어를 사용하도록 설정된 장치에 각기 다른 푸시 버전을 보낼 수 있습니다. 기본적으로 모든 장치는 사용하도록 설정된 언어에 관계없이 같은 푸시를 받습니다. 장치에서 다른 언어를 사용하도록 설정한 사용자는 기본 언어 버전 푸시를 받습니다. 대부분의 푸시 캠페인 옵션에서는 선택한 각 추가 언어에 대해 대체 콘텐츠를 지정할 수 있습니다. 

![도달률 캠페인2][21]

### <a name="language-differences-apply-to"></a>다른 언어 적용 대상
* 언어: 기본 언어 외에 고유 언어를 선택할 수 있습니다.
* 캠페인: 모든 언어에 대해 동일합니다.
* 알림: 기본 언어 이외의 각 언어에 대해 고유합니다.
* 콘텐츠: 기본 언어 이외의 각 언어에 대해 고유합니다.
* 대상: 개별 언어 기준별로 필터링할 수 있습니다.
* 시간 프레임: 모든 언어에 대해 동일합니다.
* 테스트: 한 번에 한 언어로 전송할 수 있습니다.

### <a name="supported-languages"></a>지원되는 언어
* 아랍어(ar) 
* 불가리아어(bg) 
* 카탈로니아어(ca) 
* 중국어(zh) 
* 크로아티아어(hr) 
* 체코어(cs) 
* 덴마크어(da) 
* 네덜란드어(nl) 
* 영어(en) 
* 핀란드어(fi) 
* 프랑스어(fr) 
* 독일어(de) 
* 그리스어(el) 
* 히브리어(he) 
* 힌디어(hi) 
* 헝가리어(hu) 
* 인도네시아어(id) 
* 이탈리아어(it) 
* 일본어(ja) 
* 한국어(ko) 
* 라트비아어(lv) 
* 리투아니아어(lt) 
* 말레이어(macrolanguage)(ms) 
* 노르웨이어 복말(nb) 
* 폴란드어(pl) 
* 포르투갈어(pt) 
* 루마니아어(ro) 
* 러시아어(ru) 
* 세르비아어(sr) 
* 슬로바키아어(sk) 
* 슬로베니아어(sl) 
* 스페인어(es) 
* 스웨덴어(sv) 
* 타갈로그어(tl) 
* 태국어(th) 
* 터키어(tr) 
* 우크라이나어(uk) 
* 베트남어(vi) 

## <a name="campaign"></a>캠페인
캠페인 섹션을 사용하면 캠페인 이름과 범주를 설정할 수 있을 뿐 아니라 푸시 캠페인의 대상 섹션을 무시하고 대신 하위 수준 푸시 API의 일부 요소와 도달률 API를 통해 이 캠페인을 보낼지 여부도 설정할 수 있습니다. 미리 정의된 설정에 따라 앱 내 알림을 제어하기 위해 사용자 지정 알림 템플릿에서 범주를 사용할 수 있습니다. 도달률 API를 통해 기존 "범주" 목록을 가져올 수 있습니다.

> [!WARNING]
> 도달률 캠페인의 "캠페인" 섹션에서 "대상을 무시하고 API를 통해 사용자에게 푸시 전송" 옵션을 사용하는 경우에는 캠페인이 자동으로 전송되지 않으며 도달률 API를 통해 수동으로 캠페인을 전송해야 합니다.

![도달률 캠페인3][22]

### <a name="option-applies-to"></a>옵션 적용 대상
* 이름: 모두
* 범주: 알림, 설문 조사
* 대상을 무시하고 API를 통해 사용자에게 푸시 전송: 모두

## <a name="notification"></a>알림
알림 섹션을 사용하여 푸시의 기본적인 설정을 지정할 수 있습니다. 이러한 설정으로는 푸시 제목, 메시지, 앱 내 이미지, 푸시 해제 가능 여부 등이 포함됩니다. 대부분의 알림 설정은 사용 중인 장치의 플랫폼에 따라 다릅니다. 푸시를 전송할 위치로 "앱 내" 또는 "앱 외부" 중 하나를 선택하거나 둘 다를 선택할 수 있습니다. 사용자는 장치 운영 체제 수준에서 푸시를 "옵트인(opt in)" 또는 "옵트아웃(opt out)"할 수 있으며 Azure Mobile Engagement에서는 이 설정을 재정의할 수 없습니다. 또한 "앱 내" 푸시와 "앱 외부" 푸시는 도달률 API에서 처리합니다. 푸시 API를 사용하여 "앱 외부" 푸시를 처리할 수도 있습니다. 앱 외부나 앱 내의 다른 위치로 연결하는 딥 링크를 포함한 사진이나 HTML 콘텐츠를 사용하여 푸시를 사용자 지정할 수 있습니다. 이렇게 하려면 Android SDK 2.1.0 이상의 범주가 필요합니다. 아이콘 또는 iOS 배지를 변경하고 텍스트 또는 웹 콘텐츠(html 콘텐츠와 앱 내부/외부의 다른 위치에 대한 URL 링크가 포함된 팝업)를 보낼 수 있습니다. 푸시가 수신되면 Android 장치가 벨소리를 재생하거나 진동하도록 설정할 수도 있습니다. 장치가 벨소리를 재생하거나 진동하도록 하려면 Android 매니페스트 파일에 올바른 SDK 권한이 있어야 합니다. 모든 장치에서 화면 크기가 다르므로 현재 Android의 "큰 사진" 크기에 대한 업계 표준은 없지만 거의 모든 화면 크기에서는 400x100 크기의 사진을 사용하면 됩니다.

### <a name="delivery-types"></a>배달 유형
* 앱 외부 전용: 사용자가 응용 프로그램을 사용하지 않을 때 알림이 배달됩니다.
* 앱 외부 전용 알림을 보내려면 Apple 또는 Google의 인증서(APNS 또는 GCM 인증서)가 필요합니다.
* 앱 내 전용: 응용 프로그램이 실행 중일 때만 알림이 표시됩니다.
* 알림은 Capptain 배달 시스템을 통해 사용자에게 전송됩니다. 푸시의 시각적 레이아웃/표시를 완전히 사용자 지정할 수 있습니다.
* 항상: 이 옵션을 사용하면 응용 프로그램이 실행 중인지 여부에 관계없이 알림을 보낼 수 있습니다.

![도달률 캠페인4][23]

### <a name="option-applies-to"></a>옵션 적용 대상
* 알림: 알림, 설문 조사

## <a name="content"></a>Content
콘텐츠 섹션을 사용하면 알림, 설문 조사, 데이터 푸시, 타일(Windows Phone 전용)의 콘텐츠를 수정할 수 있습니다. 푸시 캠페인의 콘텐츠 설정은 캠페인의 유형에 따라 다릅니다. 

### <a name="see-also"></a>참고 항목
* [UI 설명서 - 도달률 - 푸시 콘텐츠][Link 29]

![도달률 캠페인5][24]

## <a name="audience"></a>대상
대상 섹션을 사용하여 표준 항목 목록을 정의해 캠페인을 제한하거나 사용자 지정된 기준에 따라 캠페인을 제한할 수 있습니다. 대상을 제한하는 표준 옵션 집합을 사용하면 신규/이전 사용자 또는 네이티브 푸시 사용자에게만 푸시를 보낼 수 있습니다. 또한 할당량을 설정하여 푸시를 받는 사용자 수를 제한할 수도 있습니다. 사용자를 대상으로 지정하는 기준을 하나 이상 포함하도록 캠페인을 필터링하는 방법에 대한 식을 수동으로 편집할 수 있습니다. 대상 식은 수동으로 입력할 수 있습니다. 이러한 식은 기준 간의 관계를 명시적으로 정의해야 합니다. 기준을 설명하는 식별자는 대문자로 시작해야 하며 공백은 포함할 수 없습니다. 'and', 'or', 'not' 연산자와 '(', ')'를 사용하여 기준 간의 관계를 설명할 수 있습니다. 예: "Criterion1 or (Criterion1 and not Criterion2)".

> [!NOTE]
> 캠페인에 큰 대상을 포함하면 서버 쪽 대상 검색 속도가 느려질 수 있습니다(특히 여러 캠페인을 동시에 시작하려는 경우).

* 가능하면 캠페인을 한 번에 하나만 시작합니다.
* 많이 필요한 경우 캠페인을 한 번에 4개까지만 시작합니다.
* 앱을 아직 설치해 두었으며 사용 중인 사용자만 검색하면 되도록 활성 사용자에게만 푸시를 보냅니다. 이렇게 하려면 "네이티브 푸시를 사용하여 연결할 수 있는 사용자만 참여" 및 "활성 사용자만 참여" 확인란을 선택합니다.
  대상을 정의한 후에는 시뮬레이트 단추를 사용하여 해당 푸시를 수신할 사용자의 수를 확인할 수 있습니다. 그러면 이 대상을 통해 대상으로 지정될 수 있는 알려진 사용자 수가 계산됩니다. 이 결과는 무작위 사용자 샘플을 기준으로 한 예상치입니다. 응용 프로그램을 제거한 사용자도 이 대상에 포함되기는 하지만 이러한 사용자에게는 푸시가 배달되지 않습니다.

### <a name="see-also"></a>참고 항목
* [UI 설명서 - 도달률 - 새 푸시 기준][Link 28]

![도달률 캠페인6][25]

### <a name="edit-expression"></a>식 편집
![도달률 캠페인7][26]

### <a name="limit-your-audience-option-applies-to"></a>대상 제한 옵션 적용 대상
* 사용자 하위 집합만 참여: 모두(알림, 설문 조사, 데이터 푸시, 타일)
* 이전 사용자만 참여: 모두(알림, 설문 조사, 데이터 푸시, 타일)
* 새 사용자만 참여: 모두(알림, 설문 조사, 데이터 푸시, 타일)
* 유휴 사용자만 참여: 알림, 설문 조사, 타일
* 활성 사용자만 참여: 모두(알림, 설문 조사, 데이터 푸시, 타일)
* 네이티브 푸시를 사용하여 연결할 수 있는 사용자만 참여: 알림, 설문 조사

## <a name="time-frame"></a>시간 프레임
기간 섹션을 사용하여 푸시를 보낼 시간을 설정할 수도 있습니다. 캠페인을 즉시 시작하려는 경우에는 기간을 비워 두면 됩니다. 최종 사용자의 표준 시간대를 사용하는 경우 아시아 지역 최종 사용자의 경우에는 캠페인이 예상보다 하루 일찍 시작할 수 있으며 세계의 모든 표준 시간대가 캠페인에 대해 설정된 기간과 일치할 때까지 작은 푸시 배치가 한 번에 하나씩 전송됩니다. 또한 캠페인은 푸시를 시작하기 전에 휴대폰에서 시간을 요청해야 하므로, 최종 사용자의 표준 시간대를 사용하면 캠페인이 지연될 수도 있습니다.

> [!NOTE]
> 종료 날짜가 없는 캠페인은 푸시를 로컬로 캐시할 수 있으므로 캠페인을 수동으로 종료한 후에도 푸시가 표시될 수 있습니다. 이 동작을 방지하려면이 캠페인에 대해 구체적인 종료 시간을 설정합니다.

### <a name="see-also"></a>참고 항목
* [도달률 - 방법 - 예약][Link 3] 

![도달률 캠페인8][27]

### <a name="settings-apply-to"></a>설정 적용 대상
* 시간 프레임: 알림, 설문 조사, 타일

## <a name="test"></a>테스트
테스트 섹션을 사용하면 캠페인을 저장하기 전에 원하는 테스트 장치로 이 푸시를 보낼 수 있습니다. 이 캠페인에 대해 사용자 지정 언어를 구성한 경우에는 각 언어에서 푸시를 테스트할 수 있습니다. 테스트 장치는 "내 계정"에서 설정할 수 있습니다.

> [!NOTE]
> 푸시를 "테스트"하는 단추를 사용할 때는 서버 쪽 데이터가 기록되지 않으며 실제 푸시 캠페인에 대해서만 데이터기 기록됩니다.

### <a name="see-also"></a>참고 항목
* [UI 설명서 - 내 계정][Link 14]

![도달률 캠페인9][28]

<!--Image references-->
[1]: ./media/mobile-engagement-user-interface-navigation/navigation1.png
[2]: ./media/mobile-engagement-user-interface-home/home1.png
[3]: ./media/mobile-engagement-user-interface-home/home2.png
[4]: ./media/mobile-engagement-user-interface-home/home3.png
[5]: ./media/mobile-engagement-user-interface-home/home4.png
[6]: ./media/mobile-engagement-user-interface-home/home5.png
[7]: ./media/mobile-engagement-user-interface-my-account/myaccount1.png
[8]: ./media/mobile-engagement-user-interface-my-account/myaccount2.png
[9]: ./media/mobile-engagement-user-interface-my-account/myaccount3.png
[10]: ./media/mobile-engagement-user-interface-analytics/analytics1.png
[11]: ./media/mobile-engagement-user-interface-analytics/analytics2.png
[12]: ./media/mobile-engagement-user-interface-analytics/analytics3.png
[13]: ./media/mobile-engagement-user-interface-analytics/analytics4.png
[14]: ./media/mobile-engagement-user-interface-monitor/monitor1.png
[15]: ./media/mobile-engagement-user-interface-monitor/monitor2.png
[16]: ./media/mobile-engagement-user-interface-monitor/monitor3.png
[17]: ./media/mobile-engagement-user-interface-monitor/monitor4.png
[18]: ./media/mobile-engagement-user-interface-reach/reach1.png
[19]: ./media/mobile-engagement-user-interface-reach/reach2.png
[20]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign1.png
[21]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign2.png
[22]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign3.png
[23]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign4.png
[24]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign5.png
[25]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign6.png
[26]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign7.png
[27]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign8.png
[28]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign9.png
[29]: ./media/mobile-engagement-user-interface-reach-criterion/Reach-Criterion1.png
[30]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content1.png
[31]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content2.png
[32]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content3.png
[33]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content4.png
[34]: ./media/mobile-engagement-user-interface-dashboard/dashboard1.png
[35]: ./media/mobile-engagement-user-interface-segments/segments1.png
[36]: ./media/mobile-engagement-user-interface-segments/segments2.png
[37]: ./media/mobile-engagement-user-interface-segments/segments3.png
[38]: ./media/mobile-engagement-user-interface-segments/segments4.png
[39]: ./media/mobile-engagement-user-interface-segments/segments5.png
[40]: ./media/mobile-engagement-user-interface-segments/segments6.png
[41]: ./media/mobile-engagement-user-interface-segments/segments7.png
[42]: ./media/mobile-engagement-user-interface-segments/segments8.png
[43]: ./media/mobile-engagement-user-interface-segments/segments9.png
[44]: ./media/mobile-engagement-user-interface-segments/segments10.png
[45]: ./media/mobile-engagement-user-interface-segments/segments11.png
[46]: ./media/mobile-engagement-user-interface-settings/settings1.png
[47]: ./media/mobile-engagement-user-interface-settings/settings2.png
[48]: ./media/mobile-engagement-user-interface-settings/settings3.png
[49]: ./media/mobile-engagement-user-interface-settings/settings4.png
[50]: ./media/mobile-engagement-user-interface-settings/settings5.png
[51]: ./media/mobile-engagement-user-interface-settings/settings6.png
[52]: ./media/mobile-engagement-user-interface-settings/settings7.png
[53]: ./media/mobile-engagement-user-interface-settings/settings8.png
[54]: ./media/mobile-engagement-user-interface-settings/settings9.png
[55]: ./media/mobile-engagement-user-interface-settings/settings10.png
[56]: ./media/mobile-engagement-user-interface-settings/settings11.png
[57]: ./media/mobile-engagement-user-interface-settings/settings12.png
[58]: ./media/mobile-engagement-user-interface-settings/settings13.png

<!--Link references-->
[Link 1]: mobile-engagement-user-interface.md
[Link 2]: mobile-engagement-troubleshooting-guide.md
[Link 3]: mobile-engagement-how-tos.md
[Link 4]: http://go.microsoft.com/fwlink/?LinkID=525553
[Link 5]: http://go.microsoft.com/fwlink/?LinkID=525554
[Link 6]: http://go.microsoft.com/fwlink/?LinkId=525555
[Link 7]: https://account.windowsazure.com/PreviewFeatures
[Link 8]: https://social.msdn.microsoft.com/Forums/azure/home?forum=azuremobileengagement
[Link 9]: http://azure.microsoft.com/services/mobile-engagement/
[Link 10]: http://azure.microsoft.com/documentation/services/mobile-engagement/
[Link 11]: http://azure.microsoft.com/pricing/details/mobile-engagement/
[Link 12]: mobile-engagement-user-interface-navigation.md
[Link 13]: mobile-engagement-user-interface-home.md
[Link 14]: mobile-engagement-user-interface-my-account.md
[Link 15]: mobile-engagement-user-interface-analytics.md
[Link 16]: mobile-engagement-user-interface-monitor.md
[Link 17]: mobile-engagement-user-interface-reach.md
[Link 18]: mobile-engagement-user-interface-segments.md
[Link 19]: mobile-engagement-user-interface-dashboard.md
[Link 20]: mobile-engagement-user-interface-settings.md
[Link 21]: mobile-engagement-troubleshooting-guide-analytics.md
[Link 22]: mobile-engagement-troubleshooting-guide-apis.md
[Link 23]: mobile-engagement-troubleshooting-guide-push-reach.md
[Link 24]: mobile-engagement-troubleshooting-guide-service.md
[Link 25]: mobile-engagement-troubleshooting-guide-sdk.md
[Link 26]: mobile-engagement-troubleshooting-guide-sr-info.md
[Link 27]: mobile-engagement-user-interface-reach-campaign.md
[Link 28]: mobile-engagement-user-interface-reach-criterion.md
[Link 29]: mobile-engagement-user-interface-reach-content.md

