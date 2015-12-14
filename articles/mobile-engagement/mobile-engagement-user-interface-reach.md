<properties 
   pageTitle="Azure Mobile Engagement 사용자 인터페이스 - 도달률" 
   description="푸시 알림으로 응용 프로그램의 사용자에게 알리는 방법 알아보기" 
   services="mobile-engagement" 
   documentationCenter="" 
   authors="piyushjo" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="mobile-engagement"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="mobile-multiple"
   ms.workload="mobile" 
   ms.date="11/29/2015"
   ms.author="piyushjo"/>


# 푸시 알림으로 응용 프로그램의 사용자에게 알리는 방법

이 문서에서는 **Mobile Engagement** 포털의 **도달률** 탭을 설명합니다. **Mobile Engagement** 포털을 사용하여 모바일 앱을 모니터링하고 관리합니다. 포털 사용을 시작하려면 먼저 **Azure Mobile Engagement** 계정을 만들어야 합니다. 자세한 내용은 [Azure Mobile Engagement 계정 만들기](mobile-engagement-create-account.md)를 참조하세요.

UI의 도달률 섹션은 푸시 알림 캠페인과 기능을 작성/편집/활성화/완료/모니터링하고 관련 통계를 가져올 수 있는 푸시 캠페인 관리 도구입니다. 하위 수준 푸시 API의 일부 요소와 도달률 API를 통해서도 이러한 캠페인과 기능에 액세스할 수 있습니다. API나 UI 중 어느 쪽을 사용하든 Azure Mobile Engagement 및 도달률을 모두 SDK와 함께 각 플랫폼의 응용 프로그램에 통합해야 도달률 캠페인을 사용할 수 있습니다.

>[AZURE.NOTE]**Mobile Engagement** 포털 UI의 여러 섹션에는 **도움말 표시** 단추가 포함되어 있습니다. 섹션에 대해 더 자세한 문맥 정보를 보려면 이 단추를 누릅니다.

 
## 4가지 푸시 알림 유형
1.    알림 - 사용자에게 광고 메시지를 보낼 수 있습니다. 사용자는 해당 메시지를 앱 내의 다른 위치로 리디렉션하거나 웹 페이지로 보내거나 앱 외부에 저장할 수 있습니다. 
2.    설문 조사 - 최종 사용자에게 질문을 하여 정보를 수집할 수 있습니다.
3.    데이터 푸시 - 이진 또는 base64 데이터 파일을 보낼 수 있습니다. 데이터 푸시에 포함된 정보는 앱의 현재 사용자 환경을 수정하기 위해 응용 프로그램으로 전송됩니다. 응용 프로그램은 데이터 푸시의 데이터를 처리할 수 있어야 합니다.


## 각 캠페인에 대해 표시되는 3가지 실시간 통계 범주

1.    푸시 - 캠페인에 지정된 기준에 따라 전송된 푸시의 수입니다. 
2.    회신 - 알림을 앱 외부에서 열거나 앱에서 닫는 방식으로 응답한 사용자의 수입니다. 
3.    작업 - 알림의 링크를 클릭하여 앱, 스토어 또는 웹 브라우저의 새 위치로 리디렉션된 사용자의 수입니다. 

> [AZURE.NOTE]도달률 API 통계에서 더 자세한 캠페인 통계가 제공됩니다.



## 캠페인 세부 정보

아직 활성화되지 않은 캠페인의 이름을 가리켜 해당 캠페인을 편집, 복제, 삭제 또는 활성화할 수 있으며 캠페인을 클릭하여 열 수도 있습니다. 이미 활성화된 캠페인의 이름을 가리켜 해당 캠페인을 복제할 수 있으며 캠페인을 클릭하여 열 수도 있습니다. 그러나 활성화된 캠페인은 변경할 수 없습니다.
 
![Reach1][18]

## 도달률 피드백

캠페인의 세부 정보 또는 통계를 보려면 클릭합니다. 그런 다음 이미 활성화된 열려 있는 캠페인의 세부 정보에서 통계 뷰로 전환할 수 있으며, 권한에 따라 통계의 단순 뷰에서 고급 뷰로 전환하여 보다 자세한 정보를 확인할 수 있습니다. 이전 캠페인의 도달률 피드백 정보를 새 캠페인의 대상 지정 기준으로 사용할 수도 있습니다. 도달률 API에서 **통계**를 사용하여 도달률 피드백 통계를 수집할 수도 있습니다. 이전 캠페인을 기준으로 푸시 캠페인 대상을 사용자 지정할 수도 있습니다.


![Reach2][19]


## 참고 항목

- [개념][Link 6]
- [문제 해결 가이드 서비스][Link 24]

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
 

<!---HONumber=AcomDC_1203_2015-->