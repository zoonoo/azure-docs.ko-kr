<properties 
   pageTitle="Azure Mobile Engagement 사용자 인터페이스 - 홈" 
   description="Azure Mobile Engagement의 홈 섹션 사용자 인터페이스 개요" 
   services="mobile-engagement" 
   documentationCenter="mobile" 
   authors="v-micada" 
   manager="mattgre" 
   editor=""/>

<tags
   ms.service="mobile-engagement"
   ms.devlang="Java"
   ms.topic="article"
   ms.tgt_pltfrm="mobile"
   ms.workload="required" 
   ms.date="02/17/2015"
   ms.author="v-micada"/>

# Azure Mobile Engagement - 사용자 인터페이스

<div class="dev-center-tutorial-selector sublanding">
<a href="../mobile-engagement-user-interface" title="Introduction">소개</a>
<a href="../mobile-engagement-user-interface-navigation" title="Navigation">탐색</a>
<a href="../mobile-engagement-user-interface-home/" title="Home">홈</a>
<a href="../mobile-engagement-user-interface-my-account" title="My Account">내 계정</a>
<a href="../mobile-engagement-user-interface-analytics" title="Analytics">분석</a>
<a href="../mobile-engagement-user-interface-monitor" title="Monitor">모니터</a>
<a href="../mobile-engagement-user-interface-reach" title="Reach">도달률</a>
<a href="../mobile-engagement-user-interface-segments" title="Segments">세그먼트</a>
<a href="../mobile-engagement-user-interface-dashboard" title="Dashboard">대시보드</a>
<a href="../mobile-engagement-user-interface-settings" title="Settings">설정</a>
</div>

# 홈
 
UI의 홈 섹션에는 내 응용 프로그램의 모든 응용 프로그램 목록과 응용 프로그램에 대한 권한을 다른 사용자에게 부여하는 기능이 포함되어 있습니다. 누구나 계정을 만들면 UI의 홈 페이지에 액세스할 수 있지만, 다른 사용자들이 내 프로젝트의 사용자 지정 응용 프로그램에 액세스하도록 하려면 해당 사용자에게 권한을 부여해야 합니다.

**참고 항목:**

-  [문제 해결 가이드 - 서비스][Link 2]
 
## 내 응용 프로그램
 
![Home1][2]

응용 프로그램에 대한 이 간략한 개요에서 상세한 리본 옵션을 확인하기 위해 열 응용 프로그램을 선택할 수 있습니다. 응용 프로그램 이름을 클릭하면 응용 프로그램에서 가장 자주 방문하는 리본 위치가 반환되며, 기어 아이콘을 클릭하면 응용 프로그램의 "설정" 페이지로 직접 이동할 수 있습니다. 응용 프로그램 테이블에 표시되는 정보를 검색, 필터링 또는 정렬할 수 있습니다. 열 머리글을 끌어서 놓는 방법으로 순서를 변경할 수도 있습니다. 
 
응용 프로그램 개요에는 다음 항목이 포함됩니다.

- 전체 사용자(총 사용자 수)
- 새 사용자 추세(지난 2주 동안의 새 사용자 증가 수)
- 활성 사용자(지난 30일 동안의 활성 사용자 수)
- 활성 사용자 추세(지난 2주 동안의 활성 사용자 증가 수)
 
또한 응용 프로그램을 비교하는 차트도 확인할 수 있습니다.

- 비교 차트 표시(응용 프로그램 데이터를 차트 형식으로 표시하는 데 사용할 수 있음)
- 확인란(특정 응용 프로그램을 비교 차트에 추가하거나 비교 차트에서 제거함)
 
![Home2][3]

## 내 프로젝트
 
![Home5][6]

프로젝트를 사용하여 응용 프로그램을 그룹화하고 응용 프로그램에 대한 권한을 부여할 수 있습니다. 새 프로젝트 단추를 사용하면 새 프로젝트의 "이름"과 "설명"만 입력하여 새 프로젝트를 만들 수 있습니다. 프로젝트를 만든 후에는 프로젝트 이름을 클릭하여 제품 이름과 설명을 편집하고 이 프로젝트에 표시할 모든 응용 프로그램을 선택할 수 있습니다. 또한 해당 프로젝트를 삭제할 수도 있습니다. 이렇게 해도 해당 프로젝트가 참조하는 응용 프로그램은 삭제되지 않습니다. 그러나 자신의 소유가 아니며 다른 프로젝트에서 액세스할 수 없는 모든 응용 프로그램에는 액세스할 수 없게 됩니다. 그러므로 프로젝트를 삭제할 때는 주의해야 합니다. 
전자 메일 주소를 기준으로 사용자를 프로젝트에 초대할 수도 있습니다. Azure Mobile Engagement에서 계정을 이미 만든 사용자에게만 권한을 부여할 수 있습니다. 

**역할의 종류** 

- 보기 권한자: 프로젝트에 연결된 응용 프로그램을 볼 수만 있는 사용자입니다. 보기 권한자는 분석 및 모니터 데이터에 액세스하고 도달률 결과를 확인할 수 있습니다. 그러나 정보를 변경하거나 응용 프로그램/사용자를 관리할 수는 없습니다. 또한 도달률 캠페인을 만들거나 해당 상태를 변경할 수도 없습니다.
- 고정되어 있습니다. 보기 권한자가 수행할 수 있는 모든 작업을 수행할 수 있으며 응용 프로그램을 관리할 수도 있는 사용자입니다. 개발자는 응용 프로그램을 사용하거나 사용하지 않도록 설정하고, 패키지/서명 등의 응용 프로그램 정보를 변경하고, 도달률 캠페인을 만들 수 있습니다. 그러나 사용자를 관리할 수는 없습니다. 
- 관리자: 개발자가 수행할 수 있는 모든 작업을 수행할 수 있으며 사용자를 관리할 수도 있는 사용자입니다. 관리자는 사용자를 프로젝트에 참가하도록 초대하고 사용자 역할을 변경하며 프로젝트 정보를 변경할 수 있습니다. "설정"에서 응용 프로그램 수준 권한을 설정할 수도 있습니다.
 
**참고 항목:**

-  [UI 설명서 - 설정][Link 20]

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
[20]: ./media/mobile-engagement-user-interface-reach/Reach-Campaign1.png
[21]: ./media/mobile-engagement-user-interface-reach/Reach-Campaign2.png
[22]: ./media/mobile-engagement-user-interface-reach/Reach-Campaign3.png
[23]: ./media/mobile-engagement-user-interface-reach/Reach-Campaign4.png
[24]: ./media/mobile-engagement-user-interface-reach/Reach-Campaign5.png
[25]: ./media/mobile-engagement-user-interface-reach/Reach-Campaign6.png
[26]: ./media/mobile-engagement-user-interface-reach/Reach-Campaign7.png
[27]: ./media/mobile-engagement-user-interface-reach/Reach-Campaign8.png
[28]: ./media/mobile-engagement-user-interface-reach/Reach-Campaign9.png
[29]: ./media/mobile-engagement-user-interface-reach/Reach-Criterion1.png
[30]: ./media/mobile-engagement-user-interface-reach/Reach-Content1.png
[31]: ./media/mobile-engagement-user-interface-reach/Reach-Content2.png
[32]: ./media/mobile-engagement-user-interface-reach/Reach-Content3.png
[33]: ./media/mobile-engagement-user-interface-reach/Reach-Content4.png
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
[Link 1]: ../mobile-engagement-user-interface/
[Link 2]: ../mobile-engagement-troubleshooting-guide/
[Link 3]: ../mobile-engagement-how-tos/
[Link 4]: http://go.microsoft.com/fwlink/?LinkID=525553
[Link 5]: http://go.microsoft.com/fwlink/?LinkID=525554
[Link 6]: http://go.microsoft.com/fwlink/?LinkId=525555
[Link 7]: https://account.windowsazure.com/PreviewFeatures
[Link 8]: https://social.msdn.microsoft.com/Forums/azure/home?forum=azuremobileengagement
[Link 9]: http://azure.microsoft.com/services/mobile-engagement/
[Link 10]: http://azure.microsoft.com/documentation/services/mobile-engagement/
[Link 11]: http://azure.microsoft.com/pricing/details/mobile-engagement/
[Link 12]: ../mobile-engagement-user-interface-navigation/
[Link 13]: ../mobile-engagement-user-interface-home/
[Link 14]: ../mobile-engagement-user-interface-my-account/
[Link 15]: ../mobile-engagement-user-interface-analytics/
[Link 16]: ../mobile-engagement-user-interface-monitor/
[Link 17]: ../mobile-engagement-user-interface-reach/
[Link 18]: ../mobile-engagement-user-interface-segments/
[Link 19]: ../mobile-engagement-user-interface-dashboard/
[Link 20]: ../mobile-engagement-user-interface-settings/
[Link 21]: ../mobile-engagement-troubleshooting-guide-analytics/
[Link 22]: ../mobile-engagement-troubleshooting-guide-apis/
[Link 23]: ../mobile-engagement-troubleshooting-guide-push-reach/
[Link 24]: ../mobile-engagement-troubleshooting-guide-service/
[Link 25]: ../mobile-engagement-troubleshooting-guide-sdk/
[Link 26]: ../mobile-engagement-troubleshooting-guide-sr-info/
[Link 27]: ../mobile-engagement-how-tos-first-push/
[Link 28]: ../mobile-engagement-how-tos-test-campaign/
[Link 29]: ../mobile-engagement-how-tos-personalize-push/
[Link 30]: ../mobile-engagement-how-tos-differentiate-push/
[Link 31]: ../mobile-engagement-how-tos-schedule-campaign/
[Link 32]: ../mobile-engagement-how-tos-text-view/
[Link 33]: ../mobile-engagement-how-tos-web-view/


 

<!--HONumber=47-->
