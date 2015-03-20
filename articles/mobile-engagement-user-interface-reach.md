<properties 
   pageTitle="Azure Mobile Engagement 사용자 인터페이스 - 도달률" 
   description="Azure Mobile Engagement의 도달률 섹션 사용자 인터페이스 개요" 
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

# <a name="Reach">도달률</a>
 
UI의 도달률 섹션은 푸시 알림 캠페인과 기능을 작성/편집/활성화/완료/모니터링하고 관련 통계를 가져올 수 있는 푸시 캠페인 관리 도구입니다. 하위 수준 푸시 API의 일부 요소와 도달률 API를 통해서도 이러한 캠페인과 기능에 액세스할 수 있습니다. API나 UI 중 어느 쪽을 사용하든 Azure Mobile Engagement 및 도달률을 모두 SDK와 함께 각 플랫폼의 응용 프로그램에 통합해야 도달률 캠페인을 사용할 수 있습니다.

**참고 항목:**

-  [API 설명서 - 도달률 API][Link 4], [API 설명서 - 푸시 API][Link 4], [문제 해결 가이드 - 푸시/도달률][Link 2]
-  <a href="#ReachCampaign" title="Reach Campaign">도달률 캠페인</a>, <a href="#ReachCriterion" title="Reach Criterion">도달률 기준</a>, <a href="#ReachContent" title="Reach Content">도달률 콘텐츠</a>
 
## 4가지 푸시 알림 유형
1.    알림 - 사용자에게 광고 메시지를 보낼 수 있습니다. 사용자는 해당 메시지를 앱 내의 다른 위치로 리디렉션하거나 웹 페이지로 보내거나 앱 외부에 저장할 수 있습니다. 
2.    설문 조사 - 최종 사용자에게 질문을 하여 정보를 수집할 수 있습니다.
3.    데이터 푸시 - 이진 또는 base64 데이터 파일을 보낼 수 있습니다. 데이터 푸시에 포함된 정보는 앱의 현재 사용자 환경을 수정하기 위해 응용 프로그램으로 전송됩니다. 응용 프로그램은 데이터 푸시의 데이터를 처리할 수 있어야 합니다.
4.    타일(Windows Phone에만 해당) - MPNS(Microsoft 푸시 알림 서비스)를 사용하여 XML 데이터가 포함된 네이티브 Windows 푸시를 보낼 수 있습니다. SDK 버전 0.9.0부터 지원됩니다. 타일의 최종 페이로드는 32KB를 초과할 수 없습니다.

**참고 항목:**

-  [개념 - 용어집][Link 6]

## 각 캠페인에 대해 표시되는 3가지 실시간 통계 범주 
1.    푸시 - 캠페인에 지정된 기준에 따라 전송된 푸시의 수입니다. 
2.    회신 - 알림을 앱 외부에서 열거나 앱에서 닫는 방식으로 응답한 사용자의 수입니다. 
3.    작업 - 알림의 링크를 클릭하여 앱, 스토어 또는 웹 브라우저의 새 위치로 리디렉션된 사용자의 수입니다. 

> 참고: 도달률 API 통계에서 더 자세한 캠페인 통계가 제공됩니다.

**참고 항목:**

-  [개념 - 용어집][Link 6], [API 설명서 - 도달률 API - 통계][Link 4]


## 캠페인 세부 정보
아직 활성화되지 않은 캠페인의 이름을 가리켜 해당 캠페인을 편집, 복제, 삭제 또는 활성화할 수 있으며 캠페인을 클릭하여 열 수도 있습니다. 이미 활성화된 캠페인의 이름을 가리켜 해당 캠페인을 복제할 수 있으며 캠페인을 클릭하여 열 수도 있습니다. 그러나 활성화된 캠페인은 변경할 수 없습니다.
 
![Reach1][18]

## 도달률 피드백
이미 활성화된 열려 있는 캠페인의 세부 정보에서 통계 뷰로 전환할 수 있으며, 권한에 따라 통계의 단순 뷰에서 고급 뷰로 전환하여 보다 자세한 정보를 확인할 수 있습니다. 이전 캠페인의 도달률 피드백 정보를 새 캠페인의 대상 지정 기준으로 사용할 수도 있습니다. 도달률 API에서 "통계"를 사용하여 도달률 피드백 통계를 수집할 수도 있습니다. 이전 캠페인을 기준으로 푸시 캠페인 대상을 사용자 지정할 수도 있습니다.


**참고 항목:**

-  <a href="#ReachCampaign">UI 설명서 - 도달률 - 새 푸시 캠페인</a>, [API 설명서 - 도달률 API - 통계][Link 4]
![Reach2][19]

## <a name="ReachCampaign">도달률 - 새 푸시 캠페인</a>
 
UI의 도달률 섹션을 사용하면 복합 수식을 통해 푸시 알림을 보내는 데 필요한 모든 정보를 제공하여 새 푸시 캠페인을 만들 수 있습니다. 푸시 캠페인의 옵션은 4가지 캠페인 유형인 알림, 설문 조사, 데이터 푸시, 타일(Windows Phone에만 해당)마다 약간씩 다릅니다.

**옵션 적용 대상**

- 언어:    모두(알림, 설문 조사, 데이터 푸시, 타일)
- 캠페인:    모두(알림, 설문 조사, 데이터 푸시, 타일)
- 알림:     알림, 설문 조사
- 콘텐츠:    각 캠페인 유형에서 고유함
- 대상:     모두(알림, 설문 조사, 데이터 푸시, 타일)
- 기간:     알림, 설문 조사, 타일
- 테스트    모두(알림, 설문 조사, 데이터 푸시, 타일)
 
![Reach-Campaign1][20]

## 언어:
언어 드롭다운 메뉴를 사용하여 서로 다른 언어를 사용하도록 설정된 장치에 각기 다른 푸시 버전을 보낼 수 있습니다. 기본적으로 모든 장치는 사용하도록 설정된 언어에 관계없이 같은 푸시를 받습니다. 장치에서 다른 언어를 사용하도록 설정한 사용자는 기본 언어 버전 푸시를 받습니다. 대부분의 푸시 캠페인 옵션에서는 선택한 각 추가 언어에 대해 대체 콘텐츠를 지정할 수 있습니다. 
 
![Reach-Campaign2][21]

**다른 언어 적용 대상**

- 언어:    기본 언어 외에 고유 언어를 선택할 수 있습니다.
- 캠페인:    모든 언어에 대해 동일합니다.
- 알림:    기본 언어 이외의 각 언어에 대해 고유합니다.
- 콘텐츠:    기본 언어 이외의 각 언어에 대해 고유합니다.
- 대상:     개별 언어 기준별로 필터링할 수 있습니다.
- 기간:     모든 언어에 대해 동일합니다.
- 테스트    한 번에 한 언어로 전송할 수 있습니다.
 
**지원되는 언어**

- 아랍어(ar) 
- 불가리아어(bg) 
- 카탈로니아어(ca) 
- 중국어(zh) 
- 크로아티아어(hr) 
- 체코어(cs) 
- 덴마크어(da) 
- 네덜란드어(nl) 
- 영어(en) 
- 핀란드어(fi) 
- 프랑스어(fr) 
- 독일어(de) 
- 그리스어(el) 
- 히브리어(he) 
- 힌디어(hi) 
- 헝가리어(hu) 
- 인도네시아어(id) 
- 이탈리아어(it) 
- 일본어(ja) 
- 한국어(ko) 
- 라트비아어(lv) 
- 리투아니아어(lt) 
- 말레이어(macrolanguage)(ms) 
- 노르웨이어 복말(nb) 
- 폴란드어(pl) 
- 포르투갈어(pt) 
- 루마니아어(ro) 
- 러시아어(ru) 
- 세르비아어(sr) 
- 슬로바키아어(sk) 
- 슬로베니아어(sl) 
- 스페인어(es) 
- 스웨덴어(sv) 
- 타갈로그어(tl) 
- 태국어(th) 
- 터키어(tr) 
- 우크라이나어(uk) 
- 베트남어(vi) 
 
## 캠페인:
캠페인 섹션을 사용하면 캠페인 이름과 범주를 설정할 수 있을 뿐 아니라 푸시 캠페인의 대상 섹션을 무시하고 대신 하위 수준 푸시 API의 일부 요소와 도달률 API를 통해 이 캠페인을 보낼지 여부도 설정할 수 있습니다. 미리 정의된 설정에 따라 앱 내 알림을 제어하기 위해 사용자 지정 알림 템플릿에서 범주를 사용할 수 있습니다. 도달률 API를 통해 기존 "범주" 목록을 가져올 수 있습니다.

> Warning: 도달률 캠페인의 "캠페인" 섹션에서 "대상을 무시하고 API를 통해 사용자에게 푸시 전송" 옵션을 사용하는 경우에는 캠페인이 자동으로 전송되지 않으며 도달률 API를 통해 수동으로 캠페인을 전송해야 합니다.
 
![Reach-Campaign3][22]
 
**옵션 적용 대상**

- 이름:    모두
- Category:    알림, 설문 조사
- 대상을 무시하고 API를 통해 사용자에게 푸시 전송    모두
 
## 알림:
알림 섹션을 사용하여 푸시의 기본적인 설정을 지정할 수 있습니다. 이러한 설정으로는 푸시의 제목, 메시지, 앱 내 이미지, 푸시 해제 가능 여부 등이 포함됩니다. 대부분의 알림 설정은 사용 중인 장치의 플랫폼에 따라 다릅니다. 푸시를 전송할 위치로 "앱 내" 또는 "앱 외부" 중 하나를 선택하거나 둘 다를 선택할 수 있습니다. 사용자는 장치 운영 체제 수준에서 푸시를 "옵트인(opt in)" 또는 "옵트아웃(opt out)"할 수 있으며 Azure Mobile Engagement에서는 이 설정을 재정의할 수 없습니다. 또한 "앱 내" 푸시와 "앱 외부" 푸시는 도달률 API에서 처리합니다. 푸시 API를 사용하여 "앱 외부" 푸시를 처리할 수도 있습니다. 앱 외부나 앱 내의 다른 위치로 연결하는 딥 링크를 포함한 사진이나 HTML 콘텐츠를 사용하여 푸시를 사용자 지정할 수 있습니다. 이렇게 하려면 Android SDK 2.1.0 이상의 범주가 필요합니다. 아이콘 또는 iOS 배지를 변경하고 텍스트 또는 웹 콘텐츠(html 콘텐츠와 앱 내부/외부의 다른 위치에 대한 URL 링크가 포함된 팝업)를 보낼 수 있습니다. 푸시가 수신되면 Android 장치가 벨소리를 재생하거나 진동하도록 설정할 수도 있습니다. 장치가 벨소리를 재생하거나 진동하도록 하려면 Android 매니페스트 파일에 올바른 SDK 권한이 있어야 합니다. 모든 장치에서 화면 크기가 다르므로 현재 Android의 "큰 사진" 크기에 대한 업계 표준은 없지만 거의 모든 화면 크기에서는 400x100 크기의 사진을 사용하면 됩니다.

## 배달 유형
-    앱 외부 전용: 사용자가 응용 프로그램을 사용하지 않을 때 알림이 배달됩니다.
- 앱 외부 전용 알림을 보내려면 Apple 또는 Google의 인증서(APNS 또는 GCM 인증서)가 필요합니다.
- 앱 내 전용: 응용 프로그램이 실행 중일 때만 알림이 표시됩니다.
- 알림은 Capptain 배달 시스템을 통해 사용자에게 전송됩니다. 푸시의 시각적 레이아웃/표시를 완전히 사용자 지정할 수 있습니다.
- 항상: 이 옵션을 사용하면 응용 프로그램이 실행 중인지 여부에 관계없이 알림을 보낼 수 있습니다.

 
![Reach-Campaign4][23]

**옵션 적용 대상**

- 알림:     알림, 설문 조사
 
## 콘텐츠:
콘텐츠 섹션을 사용하면 알림, 설문 조사, 데이터 푸시, 타일(Windows Phone 전용)의 콘텐츠를 수정할 수 있습니다. 푸시 캠페인의 콘텐츠 설정은 캠페인의 유형에 따라 다릅니다. 

**참고 항목:**

- <a href="#ReachContent">UI 설명서 - 도달률 - 푸시 콘텐츠</a>
 
![Reach-Campaign5][24]

## 대상:
대상 섹션을 사용하여 표준 항목 목록을 정의해 캠페인을 제한하거나 사용자 지정된 기준에 따라 캠페인을 제한할 수 있습니다. 대상을 제한하는 표준 옵션 집합을 사용하면 신규/이전 사용자 또는 네이티브 푸시 사용자에게만 푸시를 보낼 수 있습니다. 또한 할당량을 설정하여 푸시를 받는 사용자 수를 제한할 수도 있습니다. 사용자를 대상으로 지정하는 기준을 하나 이상 포함하도록 캠페인을 필터링하는 방법에 대한 식을 수동으로 편집할 수 있습니다. 대상 식은 수동으로 입력할 수 있습니다. 이러한 식은 기준 간의 관계를 명시적으로 정의해야 합니다. 기준을 설명하는 식별자는 대문자로 시작해야 하며 공백은 포함할 수 없습니다.  'and', 'or', 'not' 연산자와 '(', ')'를 사용하여 기준 간의 관계를 설명할 수 있습니다. 예제: "Criterion1 or (Criterion1 and not Criterion2)"입니다.

> 참고: 캠페인에 큰 대상을 포함하면 서버 쪽 대상 검색 속도가 느려질 수 있습니다(특히 여러 캠페인을 동시에 시작하려는 경우).

- 가능하면 캠페인을 한 번에 하나만 시작합니다.
- 많이 필요한 경우  캠페인을 한 번에 4개까지만 시작합니다.
- 앱을 아직 설치해 두었으며 사용 중인 사용자만 검색하면 되도록 활성 사용자에게만 푸시를 보냅니다. 이렇게 하려면 "네이티브 푸시를 사용하여 연결할 수 있는 사용자만 참여" 및 "활성 사용자만 참여" 확인란을 선택합니다.
대상을 정의한 후에는 시뮬레이트 단추를 사용하여 해당 푸시를 수신할 사용자의 수를 확인할 수 있습니다. 그러면 이 대상을 통해 대상으로 지정될 수 있는 알려진 사용자 수가 계산됩니다. 이 결과는 무작위 사용자 샘플을 기준으로 한 예상치입니다. 응용 프로그램을 제거한 사용자도 이 대상에 포함되기는 하지만 이러한 사용자에게는 푸시가 배달되지 않습니다.

**참고 항목:**

- <a href="#ReachCriterion">UI 설명서 - 도달률 - 새 푸시 기준</a>

![Reach-Campaign6][25]

## 식 편집
 
![Reach-Campaign7][26]
 
**대상 제한 옵션 적용 대상**

- 사용자 하위 집합만 참여:    모두(알림, 설문 조사, 데이터 푸시, 타일)
- 이전 사용자만 참여:    모두(알림, 설문 조사, 데이터 푸시, 타일)
- 새 사용자만 참여:    모두(알림, 설문 조사, 데이터 푸시, 타일)
- 유휴 사용자만 참여:    알림, 설문 조사, 타일
- 활성 사용자만 참여:    모두(알림, 설문 조사, 데이터 푸시, 타일)
- 네이티브 푸시를 사용하여 연결할 수 있는 사용자만 참여:     알림, 설문 조사
 
## 기간:
기간 섹션을 사용하여 푸시를 보낼 시간을 설정할 수도 있습니다. 캠페인을 즉시 시작하려는 경우에는 기간을 비워 두면 됩니다. 최종 사용자의 표준 시간대를 사용하는 경우 아시아 지역 최종 사용자의 경우에는 캠페인이 예상보다 하루 일찍 시작할 수 있으며 세계의 모든 표준 시간대가 캠페인에 대해 설정된 기간과 일치할 때까지 작은 푸시 배치가 한 번에 하나씩 전송됩니다. 또한 캠페인은 푸시를 시작하기 전에 휴대폰에서 시간을 요청해야 하므로, 최종 사용자의 표준 시간대를 사용하면 캠페인이 지연될 수도 있습니다.

> 참고: 종료 날짜가 없는 캠페인은 푸시를 로컬로 캐시할 수 있으므로 캠페인을 수동으로 종료한 후에도 푸시가 표시될 수 있습니다. 이 동작을 방지하려면이 캠페인에 대해 구체적인 종료 시간을 설정합니다.

**참고 항목:**

- [방법 - 예약][Link 3] 
 
![Reach-Campaign8][27]

**설정 적용 대상**

- 기간:     알림, 설문 조사, 타일
 
## 테스트
테스트 섹션을 사용하면 캠페인을 저장하기 전에 원하는 테스트 장치로 이 푸시를 보낼 수 있습니다. 이 캠페인에 대해 사용자 지정 언어를 구성한 경우에는 각 언어에서 푸시를 테스트할 수 있습니다. 테스트 장치는 "내 계정"에서 설정할 수 있습니다.
> 참고: 푸시를 "테스트"하는 단추를 사용할 때는 서버 쪽 데이터가 기록되지 않으며 실제 푸시 캠페인에 대해서만 데이터기 기록됩니다.

**참고 항목:**

- [UI 설명서 - 내 계정][Link 14]
 
![Reach-Campaign9][28]

## <a name="ReachCriterion">도달률 - 대상 지정을 위한 새 푸시 기준</a>

"새 기준" 단추를 사용하여 특정 기준에 따라 대상을 지정하는 방식은 Azure Mobile Engagement에서 가장 유용한 개념 중 하나입니다. 이 방법을 사용하면 모든 대상을 스팸으로 처리하는 대신 고객이 반응을 보이도록 할 관련성이 높은 푸시 알림을 보낼 수 있습니다. 표준 기준에 따라 대상을 제한하고 푸시를 시뮬레이트하여 알림을 수신할 사용자 수를 결정할 수 있습니다.

**참고 항목:**

- <a href="#ReachCampaign">UI 설명서 - 도달률 - 새 푸시 캠페인</a>

## 대상 기준에는 다음 항목이 포함될 수 있습니다.

- **기술 정보: ** 분석 및 모니터 섹션에서 확인할 수 있는 것과 같은 기술 정보를 기준으로 대상을 지정할 수 있습니다. **참고 항목:** [UI 설명서 - 분석][Link 15],  [UI 설명서 - 모니터][Link 16]
- **위치:** 지리적 펜스 기능을 통해 "실시간 위치 보고"를 사용하는 응용 프로그램은 지리적 위치를 기준으로 사용하여 GPS 위치에서 대상을 지정할 수 있습니다. "지연 영역 위치 보고" 호출을 사용하여 휴대폰 위치에서 대상을 지정할 수도 있습니다. 이렇게 하려면 SDK에서 "실시간 위치 보고" 및 "지연 영역 위치 보고"를 활성화해야 합니다. **참고 항목:** [SDK 설명서 - iOS - 통합][Link 5], [SDK 설명서 - Android - 통합][Link 5]
- **도달률 피드백:** 알림, 설문 조사 및 데이터 푸시에 대해 제공한 도달률 피드백을 통해 이전 도달률 알림의 피드백을 기준으로 하여 대상을 지정할 수 있습니다. 이렇게 하면 2~3회의 도달률 캠페인을 진행한 후 첫 번째 도달률 캠페인에 비해 대상을 보다 효율적으로 지정할 수 있습니다. 피드백을 사용하여 비슷한 콘텐츠에 대해 알림을 이미 받은 사용자를 필터링할 수도 있습니다. 이렇게 하려면 특정 이전 캠페인을 이미 받은 사용자에게는 캠페인을 전송하지 않도록 설정합니다. 아직 새 푸시를 받도록 활성화되어 있는 특정 캠페인에 포함된 사용자를 제외할 수도 있습니다. **참고 항목:** <a href="#ReachContent">UI 설명서 - 도달률 - 푸시 콘텐츠</a>
- **설치 추적:** 사용자가 앱을 설치한 위치를 기준으로 정보를 추적할 수 있습니다. **참고 항목:** [UI 설명서 - 설정][Link 20]
- **사용자 프로필:** 표준 사용자 정보와 직접 만든 사용자 지정 앱 정보를 기준으로 대상을 지정할 수 있습니다. 이러한 대상에는 현재 로그인되어 있는 사용자와 특정 질문(단순히 이전 캠페인에 응답한 방식만이 아니라 앱 자체에서 설정하여 응답하도록 요청한 질문)에 대답한 사용자가 포함됩니다. 앱에 대해 정의한 모든 앱 정보가 이 목록에 표시됩니다.
- 세그먼트 여러 기준을 포함하는 특정 사용자 동작을 기준으로 작성한 세그먼트에 따라 대상을 지정할 수도 있습니다. 앱에 대해 정의한 모든 세그먼트가 이 목록에 표시됩니다. **참고 항목:** [UI 설명서 - 세그먼트][Link 18]
- **앱 정보:** "설정"에서 사용자 지정 앱 정보 태그를 만들어 사용자 동작을 추적할 수 있습니다. **참고 항목:** [UI 설명서 - 설정][Link 20]
## 예제 
앱 내 구매 작업을 수행한 사용자에게만 알림을 푸시하려는 경우 다음을 수행합니다.

1. 응용 프로그램 설정 페이지로 이동하여 "앱 정보" 메뉴를 선택하고 "새 앱 정보"를 선택합니다.
2. "inAppPurchase"라는 새 부울 앱을 등록합니다.
3. 사용자가 앱 내 구매를 정상적으로 수행하면 응용 프로그램에서 sendAppInfo("inAppPurchase", ...) 함수를 사용하여 이 앱 정보를 "true"로 설정하도록 지정합니다.
4. 응용 프로그램에서 이 작업을 수행하지 않으려는 경우 장치 API를 사용하여 백 엔드에서 해당 작업을 수행할 수 있습니다.
5. 그런 후에는 대상을 "inAppPurchase"가 "true"로 설정된 사용자로 제한하는 기준을 사용하여 알림을 만들면 됩니다.
 
> 참고: 앱 정보 태그 이외의 기준에 따라 대상을 지정하려는 경우에는 푸시를 보내기 전에 Azure Mobile Engagement에서 사용자 장치의 정보를 수집해야 하므로 푸시가 지연될 수 있습니다. 배지 업데이트 등의 복합 푸시 구성 옵션을 사용하는 경우에도 푸시가 지연될 수 있습니다. Azure Mobile Engagement에서 가장 빠르게 푸시를 보내려는 경우 푸시 API에서 "일회성" 캠페인을 사용합니다. 두 번째로 빠른 방법은 도달률 API나 UI에서 도달률 캠페인에 대해 앱 정보 태그만 푸시 기준으로 사용하는 것입니다. 앱 정보 태그는 서버 쪽에 저장되기 때문입니다. 푸시 캠페인에 대해 다른 대상 지정 기준을 사용하는 푸시 방법이 가장 유동적이기는 하지만 속도는 가장 느립니다. Azure Mobile Engagement는 캠페인을 보내려면 장치를 쿼리해야 하기 때문입니다.
 
![Reach-Criterion1][29] 

**기준 옵션 적용 대상:**

- **기술 정보**     
- 펌웨어 이름:    펌웨어 이름
- 펌웨어 버전:    펌웨어 버전
- 장치 모델:    장치 모델
- 장치 제조업체:    장치 제조업체
- 응용 프로그램 버전:    응용 프로그램 버전
- 통신 회사 이름:    통신 회사 이름, 정의되지 않음
- 통신 회사 국가:    통신 회사 국가, 정의되지 않음
- 네트워크 유형:    네트워크 유형
- 로캘:    로캘
- 화면 크기:    화면 크기
- **위치**      
- 마지막으로 확인된 지역:    국가, 지역, 구/군/시
- 실시간 지리적 펜스:    지점(이름/작업), 원형 지점(이름, 위도, 경도, 미터 단위 반경)
- **도달률 피드백**     
- 알림 피드백:    알림, 피드백
- 설문 조사 피드백:    설문 조사, 피드백
- 설문 조사 응답 피드백:    설문 조사 응답 피드백, 질문, 선택 항목
- 데이터 푸시 피드백:    데이터 푸시, 피드백
- **설치 추적**     
- 스토어:    스토어, 정의되지 않음
- 원본:    원본, 정의되지 않음
- **사용자 프로필**     
- 성별:    남성 또는 여성, 정의되지 않음
- 생년월일:    연산자, 날짜, 정의되지 않음
- 옵트인(opt in):    true 또는 false, 정의되지 않음
- **앱 정보**      
- 문자열:    문자열, 정의되지 않음
- 날짜:    연산자, 날짜, 정의되지 않음
- 정수:    연산자, 숫자, 정의되지 않음
- 부울:    true 또는 false, 정의되지 않음
- **세그먼트**    
- 드롭다운 목록의 세그먼트 이름, 제외 항목(이 세그먼트에 포함되지 않은 대상 사용자)

## <a name="ReachContent">도달률 - 각 캠페인 유형의 푸시 콘텐츠</a>
 
새 도달률 캠페인의 콘텐츠 섹션을 사용하면 알림, 설문 조사, 데이터 푸시 및 타일(Windows Phone 전용)의 콘텐츠를 수정할 수 있습니다. 푸시 캠페인의 콘텐츠 설정은 캠페인의 유형에 따라 다릅니다. 
 
## 콘텐츠 형식
- 알림
- 설문 조사
- 데이터 푸시
- 파일(Windows Phone 전용)
 
## 알림의 내용
 
 ![Reach-Content1][30] 

## 알림의 유형을 선택합니다.
-    알림 전용: 단순한 표준 알림입니다. 즉, 사용자가 알림을 클릭해도 추가 뷰는 표시되지 않으며 해당 알림과 연결된 작업만 수행됩니다.
-    텍스트 알림: 사용자가 텍스트 뷰를 확인하도록 하는 알림입니다.
-    웹 알림: 사용자가 웹 뷰를 확인하도록 하는 알림입니다.

**참고 항목:**

- [방법 - 알림][Link 3] 

**웹 뷰 알림 정보**

여기서 입력하는 JavaScript 코드 또는 HTML 코드에서 나타나는 "{deviceid}" 패턴은 자동으로 알림을 표시하는 장치의 식별자로 바뀝니다. 따라서 백오피스에서 호스트되는 외부 웹 서비스에서 Azure Mobile Engagement 장치 식별자를 쉽게 검색할 수 있습니다.
기본적으로 제공되는 작업 및 종료 단추를 포함하지 않고 전체 화면 웹 뷰를 만들려는 경우 웹 뷰 알림의 JavaScript 코드에서 다음 함수를 사용하면 됩니다. 

-    알림 작업 수행: ReachContent.actionContent()
-    알림 종료: ReachContent.exitContent()
 
## 작업 선택

**작업 URL 정보**

대상으로 지정된 장치의 운영 체제에서 해석할 수 있는 모든 URL을 작업 URL로 사용할 수 있습니다.
사용자가 특정 화면으로 이동하도록 하는 데 사용되는 URL 등 응용 프로그램이 지원할 수 있는 모든 전용 URL도 작업 URL로 사용할 수 있습니다.
URL에서 나타나는 각 {deviceid} 패턴은 자동으로 작업을 수행하는 장치의 식별자로 바뀝니다. 따라서 백오피스에서 호스트되는 외부 웹 서비스에서 Azure Mobile Engagement 장치 식별자를 쉽게 검색할 수 있습니다.

- **Android + iOS 작업**
    - 웹 페이지 열기
    - http://[web-site-domain] 
    - 예제:http://www.azure.com
    - 전자 메일 보내기
    - mailto:[e-mail-recipient]?subject=[subject]&body=[message] 
    - 예: mailto:foo@example.com?subject=Greetings%20from%20Azure%20Mobile%20Engagement!&body=Good%20stuff!
    - 문자 메시지 보내기
    - sms:[phone-number] 
    - 예: sms:2125551212
    - 특정 번호로 전화 걸기
    - tel:[phone-number] 
    - 예: tel:2125551212
- **Android 전용 작업**
    - PlayStore에서 응용 프로그램 다운로드
    - market://details?id=[app package] 
    - 예: market://details?id=com.microsoft.office.word
    - 지리적 위치에 따른 검색 시작
    - geo:0,0?q=[검색 쿼리] 
    - 예: geo:0,0?q=starbucks,paris
- **iOS 전용 작업**
    - 앱 스토어에서 응용 프로그램 다운로드
    - http://itunes.apple.com/[country]/app/[app name]/id[app id]?mt=8 
    - 예제:http://itunes.apple.com/fr/app/briquet-virtuel/id430154748?mt=8
    - Windows 작업
    - 웹 페이지 열기
    - http://[web-site-domain] 
    - 예제:http://www.azure.com
    - 전자 메일 보내기
    - mailto:[e-mail-recipient]?subject=[subject]&body=[message] 
    - 예: mailto:foo@example.com?subject=Greetings%20from%20Azure%20Mobile%20Engagement!&body=Good%20stuff!
    - 문자 메시지 보내기(Skype 스토어 앱 필요)
    - sms:[phone-number] 
    - 예: sms:2125551212
    - 특정 번호로 전화 걸기(Skype 스토어 앱 필요)
    - tel:[phone-number] 
    - 예: tel:2125551212
    - PlayStore에서 응용 프로그램 다운로드
    - ms-windows-store:PDP?PFN=[app package ID] 
    - 예: ms-windows-store:PDP?PFN=4d91298a-07cb-40fb-aecc-4cb5615d53c1
    - Bing 지도 검색 수행
    - bingmaps:?q=[search query] 
    - 예: bingmaps:?q=starbucks,paris
    - 사용자 지정 체계 사용
    - [사용자 지정 체계]://[사용자 지정 체계 매개 변수] 
    - 예: myCustomProtocol://myCustomParams
    - 패키지 데이터 사용(확장명 읽기를 위한 스토어 앱 필요)
    - [폴더][데이터].[확장명] 
    - 예: myfolderdata.txt
 
## 추적 URL 작성
-    사용자가 다른 응용 프로그램 중 하나를 다운로드하는 데 사용할 수 있는 추적 URL을 작성하는 지침은 <UI 설명서>의 "설정" 섹션을 참조하세요.
 
## 알림 텍스트 정의
알림의 제목, 내용 및 단추 텍스트를 입력합니다. 
사용자가 이 캠페인에 응답한 방법과 관련한 도달률 피드백을 기반으로 하여 이후 캠페인의 대상을 지정할 수 있습니다. 이 캠페인에 대한 피드백(푸시만, 회신, 작업, 종료)에 따라 대상을 지정할 수 있습니다.

**참고 항목:**
- <a href="#ReachCriterion">UI 설명서 - 도달률 - 새 푸시 기준</a>

## 설문 조사의 내용
 
![Reach-Content2][31] 
알림의 제목, 설명 및 단추 텍스트를 입력합니다. 
그런 다음 질문 및 질문에 대한 대답의 선택 항목을 추가합니다.
사용자가 이 캠페인에 응답한 방법과 관련한 도달률 피드백을 기반으로 하여 이후 캠페인의 대상을 지정할 수 있습니다. 이 캠페인에 대한 응답 방법(푸시만, 회신, 작업, 종료)에 따라 대상을 지정할 수 있습니다. 설문 조사 대답 피드백을 기준으로 대상을 지정할 수도 있습니다. 이 경우 질문과 대답 선택 항목이 기준으로 사용됩니다.

**참고 항목:**

- <a href="#ReachCriterion">UI 설명서 - 도달률 - 새 푸시 기준</a>
 
## 데이터 푸시의 내용
 
![Reach-Content3][32] 

## 데이터 형식을 선택합니다.
- 텍스트
- 이진 데이터
- Base64 데이터

## 데이터의 내용을 정의합니다.
- 텍스트 데이터를 푸시하도록 선택한 경우 텍스트를 복사하여 "콘텐츠" 상자에 붙여 넣습니다.
- 이진 또는 base64 데이터를 푸시하도록 선택한 경우에는 "파일 업로드" 단추를 사용하여 파일을 업로드합니다.
- 사용자가 이 캠페인에 응답한 방법과 관련한 도달률 피드백을 기반으로 하여 이후 캠페인의 대상을 지정할 수 있습니다. 이 캠페인에 대한 응답 방법(푸시만, 회신, 작업, 종료)에 따라 대상을 지정할 수 있습니다.

**참고 항목:**

- <a href="#ReachCriterion">UI 설명서 - 도달률 - 새 푸시 기준</a>

## 타일의 내용(Windows Phone 전용)

![Reach-Content4][33]

## 타일의 내용을 정의합니다.
타일 페이로드는 Windows Phone 장치에서 앱의 타일에 표시할 텍스트입니다.
타일 푸시는 Windows Phone용 네이티브 푸시의 MPNS(Microsoft 푸시 알림 서비스) 버전입니다. 타일 푸시 유형은 유일하게 응답이 없는 푸시 유형이므로 타일 푸시 캠페인의 결과를 기준으로 이후 캠페인의 대상을 지정할 수는 없습니다. 

**참고 항목:**

- [API 설명서 - 도달률 API - 네이티브 푸시][Link 4]

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
