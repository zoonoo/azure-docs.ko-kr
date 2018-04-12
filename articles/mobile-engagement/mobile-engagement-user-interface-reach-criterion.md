---
title: Azure Mobile Engagement 사용자 인터페이스 - 도달률 기준
description: Azure Mobile Engagement를 사용하여 대상 지정 기준을 사용하여 선택한 사용자 하위 집합에 푸시 캠페인을 보내는 방법을 알아봅니다.
services: mobile-engagement
documentationcenter: ''
author: piyushjo
manager: erikre
editor: ''
ms.assetid: a4ed03a0-55b1-4dd8-b0bd-c475005afb66
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 2adf473c6acea0f128eb14e2616748ff29d5d762
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/05/2018
---
# <a name="how-to-use-targeting-criteria-to-send-push-campaigns-to-a-select-subset-of-your-users"></a>대상 지정 기준을 사용하여 선택한 사용자 하위 집합에 푸시 캠페인을 보내는 방법
> [!IMPORTANT]
> Azure Mobile Engagement는 2018/3/31에 사용이 중지됩니다. 이 페이지는 이후에 삭제됩니다.
> 

"새 기준" 단추를 사용하여 특정 기준에 따라 대상을 지정하는 방식은 Azure Mobile Engagement에서 가장 유용한 개념 중 하나입니다. 이 방법을 사용하면 모든 대상을 스팸으로 처리하는 대신 고객이 반응을 보이도록 할 관련성이 높은 푸시 알림을 보낼 수 있습니다. 표준 기준에 따라 대상을 제한하고 푸시를 시뮬레이트하여 알림을 수신할 사용자 수를 결정할 수 있습니다.

**참고 항목:**

* [UI 설명서 - 도달률 - 새 푸시 캠페인][Link 27]

## <a name="audience-criteria-can-include"></a>대상 기준에는 다음 항목이 포함될 수 있습니다.
* **기술 정보:** 분석 및 모니터 섹션에서 확인할 수 있는 것과 같은 기술 정보를 기준으로 대상을 지정할 수 있습니다. **참고 항목** [UI 설명서 - 분석][Link 15],  [UI 설명서 - 모니터][Link 16]
* **위치:** 지역 펜싱과 함께 "실시간 위치 보고"를 사용하는 응용 프로그램은 지리적 위치를 기준으로 사용하여 GPS 위치에서 대상 그룹을 지정할 수 있습니다. "지연 영역 위치 보고" 호출을 사용하여 휴대폰 위치에서 대상 그룹을 지정할 수도 있습니다(SDK에서 "실시간으로 위치 보고" 및 "지연 영역 위치 보고"를 활성화해야 함). **참고 항목:** [SDK 설명서 - iOS - 통합][Link 5], [SDK 설명서 - Android - 통합][Link 5]
* **도달률 피드백:** 알림, 설문 조사 및 데이터 푸시의 도달률 피드백을 통해 이전 도달률 알림의 피드백에 따라 대상을 지정할 수 있습니다. 이렇게 하면 두세 개의 도달률 캠페인 후 처음보다 더 효율적으로 대상을 지정할 수 있습니다. 또한 이미 특정 이전 캠페인을 받은 사용자에게 캠페인이 전송되지 않도록 설정하여 이미 유사한 콘텐츠가 포함된 알림을 받은 사용자를 필터링하는 데 사용할 수 있습니다. 여전히 활성 상태인 특정 캠페인에 포함된 사용자가 새 푸시를 받지 않도록 제외할 수도 있습니다. **참고 항목:** [UI 설명서 - 도달률 - 푸시 콘텐츠][Link 29]
* **설치 추적:** 사용자가 앱을 설치한 위치를 기준으로 정보를 추적할 수 있습니다. **참고 항목:** [UI 설명서 - 설정][Link 20]
* **사용자 프로필:** 표준 사용자 정보와 직접 만든 사용자 지정 앱 정보를 기준으로 대상을 지정할 수 있습니다. 이러한 대상에는 현재 로그인되어 있는 사용자와 특정 질문(단순히 이전 캠페인에 응답한 방식만이 아니라 앱 자체에서 설정하여 응답하도록 요청한 질문)에 대답한 사용자가 포함됩니다. 앱에 대해 정의한 모든 앱 정보가 이 목록에 표시됩니다.
* 세그먼트: 여러 기준을 포함하는 특정 사용자 동작을 기준으로 작성한 세그먼트에 따라 대상을 지정할 수도 있습니다. 앱에 대해 정의한 모든 세그먼트가 이 목록에 표시됩니다. **참고 항목:** [UI 설명서 - 세그먼트][Link 18]
* **앱 정보:** "설정"에서 사용자 지정 앱 정보 태그를 만들어 사용자 동작을 추적할 수 있습니다. **참고 항목:** [UI 설명서 - 설정][Link 20]

## <a name="example"></a>예:
앱 내 구매 작업을 수행한 사용자에게만 알림을 푸시하려는 경우 다음을 수행합니다.

1. 응용 프로그램 설정 페이지로 이동하여 "앱 정보" 메뉴를 선택하고 "새 앱 정보"를 선택합니다.
2. "inAppPurchase"라는 새 부울 앱을 등록합니다.
3. 사용자가 앱 내 구매를 정상적으로 수행하면 응용 프로그램에서 sendAppInfo("inAppPurchase", ...) 함수를 사용하여 이 앱 정보를 "true"로 설정하도록 지정합니다.
4. 응용 프로그램에서 이 작업을 수행하지 않으려는 경우 장치 API를 사용하여 백 엔드에서 해당 작업을 수행할 수 있습니다.
5. 그런 후에는 대상을 "inAppPurchase"가 "true"로 설정된 사용자로 제한하는 기준을 사용하여 알림을 만들면 됩니다.

> [!NOTE]
> 앱 정보 태그 이외의 기준에 따라 대상을 지정하려는 경우에는 푸시를 보내기 전에 Azure Mobile Engagement에서 사용자 장치의 정보를 수집해야 하므로 푸시가 지연될 수 있습니다. 배지 업데이트 등의 복합 푸시 구성 옵션을 사용하는 경우에도 푸시가 지연될 수 있습니다. Azure Mobile Engagement에서 가장 빠르게 푸시를 보내려는 경우 푸시 API에서 "일회성" 캠페인을 사용합니다. 두 번째로 빠른 방법은 도달률 API나 UI에서 도달률 캠페인에 대해 앱 정보 태그만 푸시 기준으로 사용하는 것입니다. 앱 정보 태그는 서버 쪽에 저장되기 때문입니다. 푸시 캠페인에 대해 다른 대상 지정 기준을 사용하는 푸시 방법이 가장 유동적이기는 하지만 속도는 가장 느립니다. Azure Mobile Engagement는 캠페인을 보내려면 장치를 쿼리해야 하기 때문입니다.

![도달률 기준1][29] 

## <a name="criterion-options-apply-to"></a>기준 옵션 적용 대상:
* **기술 정보**     
* 펌웨어 이름: 펌웨어 이름
* 펌웨어 버전: 펌웨어 버전
* 장치 모델: 장치 모델
* 장치 제조업체: 장치 제조업체
* 응용 프로그램 버전: 응용 프로그램 버전
* 통신 회사 이름: 통신 회사 이름, 정의되지 않음
* 통신 회사 국가: 통신 회사 국가, 정의되지 않음
* 네트워크 유형: 네트워크 유형
* 로캘: 로캘
* 화면 크기: 화면 크기
* **위치**:      
* 마지막으로 확인된 지역: 국가, 지역, 구/군/시
* 실시간 지리적 펜스: 지점(이름/작업), 원형 지점(이름, 위도, 경도, 미터 단위 반경)
* **도달률 피드백**     
* 알림 피드백: 알림, 피드백
* 설문 조사 피드백: 설문 조사, 피드백
* 설문 조사 응답 피드백: 설문 조사 응답 피드백, 질문, 선택 항목
* 데이터 푸시 피드백: 데이터 푸시, 피드백
* **설치 추적**     
* 스토어: 스토어, 정의되지 않음
* 원본: 원본, 정의되지 않음
* **사용자 프로필**     
* 성별: 남성 또는 여성, 정의되지 않음
* 생년월일: 연산자, 날짜, 정의되지 않음
* 옵트인(opt in): true 또는 false, 정의되지 않음
* **앱 정보**      
* 문자열: 문자열, 정의되지 않음
* 날짜: 연산자, 날짜, 정의되지 않음
* 정수: 연산자, 숫자, 정의되지 않음
* 부울: true 또는 false, 정의되지 않음
* **세그먼트**    
* 드롭다운 목록의 세그먼트 이름, 제외 항목(이 세그먼트에 포함되지 않은 대상 사용자)

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

