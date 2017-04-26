---
title: "AppSource에 Cortana Intelligence 앱을 게시하나요? | Microsoft Docs"
description: "AppSource에 Cortana Intelligence 또는 Microsoft R Services 앱을 게시하기 위한 단계별 가이드"
services: marketplace
documentationcenter: 
author: hiavi
manager: judym
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/24/2017
ms.author: abathula
translationtype: Human Translation
ms.sourcegitcommit: 0c4554d6289fb0050998765485d965d1fbc6ab3e
ms.openlocfilehash: 62aa3148406068f06d1fbdaf556ff2d526c0e17b
ms.lasthandoff: 04/13/2017


---
# <a name="publish-a-cortana-intelligence-app-to-appsource"></a>AppSource에 Cortana Intelligence 앱을 게시하나요?
AppSource에 Cortana Intelligence 또는 Microsoft R Services 앱을 게시하기 위한 단계별 가이드

## <a name="1-introduction"></a>1. 소개

Microsoft AppSource에 Cortana Intelligence / Microsoft R Solutions 앱 게시에 관심을 가져 주셔서 감사합니다. Microsoft는 고객, 재판매인 및 구현 파트너가 비즈니스 솔루션을 검색 및 체험하고 자신의 투자를 최대한 활용할 수 있는 최고의 장소를 제공하기 위해 당사의 전담 파트너(ISV/SI)와 협력하게 되어 기쁘게 생각합니다. 이 가이드에서는 솔루션을 게시하기 위한 각 단계를 차례로 안내합니다.

## <a name="2-getting-started"></a>2. 시작

지침을 따릅니다.

1.  [Microsoft를 통해 선보이기 시작 가이드](https://www.microsoftpartnerserverandcloud.com/_layouts/download.aspx?SourceUrl=Hosted%20Documents/CESIPartnerJourneyOverview_1232017.pdf)에서 고급 분석 파트너로 등록합니다.

2.  AppSource에서 [앱 등록](https://appsource.microsoft.com/partners/list-an-app) 양식을 작성합니다.
    - *“앱의 기반 제품 선택*” 질문에서 **기타** 확인란을 선택하고 편집 컨트롤에 “Cortana Intelligence”를 나열합니다.

## <a name="3-solution-evaluation-criteria"></a>3. 솔루션 평가 기준

다음은 앱이 충족해야 하는 기준 목록입니다.

1.  앱은 지정된 기능 영역 내에서 특정 비즈니스 사용 사례 문제를 반복적인 방법으로 해결해야 합니다. 최소 구성/사용자 지정으로 짧은 기간 내에 미리 정의된 값 제안을 전달할 수 있어야 합니다.

2.  솔루션은 다음 분석 구성 요소 중 적어도 하나를 사용해야 합니다.

    1. HDInsight
    2. 기계 학습
    3. Data Lake Analytics
    4. Stream Analytics
    5. Cognitive Services
    6. Bot Framework
    7. Microsoft R Server 독립 실행형 또는 SQL 2016/HDInsight Premium의 R Services

3.  솔루션은 디지털 공식 파트너(DPOR) 또는 클라우드 솔루션 공급자(CSP) 프로그램을 사용하여 적어도 고객당 월 \$1 K를 생성해야 합니다.

4.  솔루션은 사용자 인증 및 리소스 액세스 제어에 대해 동의를 활성화하고 Azure Active Directory 페더레이션된 Single Sign-On(AAD 페더레이션된 SSO)을 사용해야 합니다. 앱이 아직 사용하도록 설정되지 않은 경우 다음을 참조하세요.

    1. [시작](https://identity.microsoft.com/)
    2. [Azure Active Directory와 응용 프로그램 통합](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications#adding-an-application)

5.  솔루션에 Power BI를 사용하고 Power BI 데스크톱 파일(PBIX)을 공유하는 것은 선택 사항이지만 잠재 고객을 더 많이 발생시키는 것으로 입증되었으므로 권장합니다.

    1. 모든 데이터를 PBIX 파일로 가져왔는지 확인합니다.
    2. 외부 참조 데이터 없음 - 당사가 고객을 위해 포함된 보고서를 만듭니다.

## <a name="4-decide-on-the-type-of-offer-you-would-like-to-publish-on-appsource"></a>4. AppSource에 게시할 제품 유형을 결정합니다.

AppSource를 사용하여 파트너가 뷰어에게 두 가지 평가판 경험 유형을 제공할 수 있습니다.
1.  고객이 앱을 모두 혼자 힘으로 경험할 수 있는 고객 주도 평가판
    1.  평가판
        1.  AppSource는 고객(파트너)이 제공하는 URL로 사용자를 이동시켜서 사용자를 AAD 페더레이션된 SSO로 안내하며 시간 제한이 있는 평가판 경험을 제공합니다.
        2.  여기서 앱이 한 사용자의 구성/데이터를 다른 사용자와 격리하는 다중 테넌트 지원이 포함된 SaaS 앱이거나 또는 평가판 경험이 읽기 전용 작업만 필요한 하위 집합 경험만 제공하는 것으로 예상됩니다.

        예: [AFS POP 소매 실행](https://appsource.microsoft.com/product/web-apps/afs.fa9fc926-3bc3-43dd-becd-3ef41b52c10b?tab=Overview), [AvePoint Citizen Services](https://appsource.microsoft.com/product/web-apps/avepoint.7738ac97-fd40-4ed3-aaab-327c3e0fe0b3?tab=Overview)(이 앱은 선택된 사용자 개인 집합에 대한 명확한 경로로 공유되는 경험을 제공함) 등을 참조하세요.
    2.  시험 사용
        1.  AppSource가 시간 제한 및 인스턴스의 과다/과소 풀 유지 관리를 통해 파트너 Azure 구독에서 사례화하고 관리할 수 있는 Azure Resource Manager 템플릿을 사용하여 고객(파트너) 솔루션 또는 그 부분집합을 패키징할 수 있습니다.
        2.  당사는 고객이 이 경로로 이동하도록 선택하는 경우 도움이 되도록 템플릿 및 샘플 코드를 제공할 수 있습니다.
        
        예: Neal Analytics [인벤토리 최적화](https://appsource.microsoft.com/product/web-apps/neal_analytics.8066ad01-1e61-40cd-bd33-9b86c65fa73a?tab=Overview&tag=CISHome)를 참조하세요.

2.    파트너가 후속 처리를 하여 데모/평가판 등을 제공할 수 있도록 고객이 연락처 정보 양식을 작성해야 하는 파트너 주도 평가판.

고급 개요는 [AppSource 평가판 경험 연습](http://aka.ms/trialexperienceforwebapps) 비디오를 참조하세요.

데이터를 보면 고객 주도 평가판이 파트너 주도 평가판에 비해 높은 잠재 고객 창출 잠재력을 가지고 있는 것으로 분명히 나타납니다. 이 방법을 생각해 보시고 원할 경우 양식 😊을 작성하세요.

AppSource에 추가할 제품의 유형을 결정합니다.

## <a name="5-getting-approved-to-use-azure-marketplace-publishing-portal"></a>5. Azure Marketplace 게시 포털을 사용하도록 승인 받기

현재 AAD/MSA 전자 메일 ID는 승인을 받아야 Azure Marketplace 게시 포털에 액세스하여 게시를 시작할 수 있습니다. [appsourcecissupport@microsoft.com](mailto:appsourcecissupport@microsoft.com?subject=Request%20publisher%20account%20creation%20for%20%3cPartner%20Name%3e%20and%20whitelist%20owner/contributer%20AAD/MSA%20email%20IDs)에 대한 다음 정보 공유

-   게시자 표시 이름: &lt;AppSource에 앱의 게시자로 표시되는 회사의 이름&gt;

-   게시자의 고유 이름: &lt;소문자와 하이픈 ‘-’만 사용하는 고유 이름&gt;

-   웹 사이트: &lt;회사의 웹 사이트&gt;

-   소유자 전자 메일: &lt;AAD/MSA 전자 메일 ID&gt;

-   기여자의 전자 메일: &lt;AAD/MSA 전자 메일 ID&gt;

 필요한 경우 기여자를 더 추가하세요.

## <a name="6-get-info-about-your-companys-lead-management"></a>6. 고객 회사의 잠재 고객 관리에 관한 정보 얻기

이러한 잠재 고객은 솔루션에 대한 방문자의 기본 관심 사항이므로 파트너의 잠재 고객 관리 시스템으로 직접 전달됩니다. AppSource는 여러 유형의 잠재 고객 관리 시스템(Dynamics CRM, Salesforce, Marketo 등)을 지원합니다.

전체 프로세스 정보 및 AppSource의 잠재 고객을 설정하기에 적절한 정보를 찾을 수 있는 방법은 [AppSource Marketing 잠재 고객 관리](./cloud-partner-portal-get-customer-leads.md)를 참조하세요.    

## <a name="7-publish-your-app-on-the-publishing-portal"></a>7. 게시 포털에 앱 게시

당사가 건너뛴 필드에 관한 정보가 필요한 경우 <appsourcecissupport@microsoft.com>에 전자 메일을 보내세요.

다음 단계를 수행하세요.

1.  Google Chrome 브라우저에서 [클라우드 파트너 포털](https://cloudpartner.azure.com)을 엽니다.

2.  고객의 허용 목록 AAD/MSA 전자 메일 ID로 로그인합니다.

3.  새 제품 -&gt; Cortana Intelligence를 선택합니다.

    ![publishaaapp1][1]
4.  제품 설정 정보를 작성합니다.

    ![publishaaapp2][2]

    1. 제품 ID는 AppSource에서 고객의 앱을 고유한 AppSource URL의 일부로 고유하게 식별하기 위해 사용됩니다. 예: “appsource-saas-app”. 이는 Azure Resource Manager 템플릿 및 청구 보고서 같은 장소에서 고객이 볼 수 있습니다. 참고로 제품 ID 및 게시자 정보는 일단 저장되면 변경할 수 없습니다.

    2. 제품 이름에 고객 솔루션의 이름을 사용하세요. 회사 이름은 솔루션 이름 아래에 자동으로 추가되므로 추가하지 마세요.

5.  기술 정보 섹션을 작성합니다. 대부분의 정보는 설명이 필요 없으며 추가 정보가 포함된 도구 설명이 있습니다.

    1.  데모 비디오는 편집이 거의 필요 없는 실제 솔루션 기능의 기록이며 AAD를 사용하여 인증하는 사용자, 사용자 대상 기능의 주요 특성 및 Cortana Intelligence 플랫폼과의 통합을 강조합니다.  데모 비디오는 고객에게 공개적으로 제공되지 **않으며** 솔루션을 잠재 고객에게 표시*할 수 있는* 방법을 나타내는 것으로 예상됩니다.  그런 점에서 스크립트로 되어 있으며 반복 가능합니다.

        표준 비디오 형식(예: MPEG)을 내보내는 화면 기록 도구를 사용하여 데모 비디오를 준비할 수 있습니다. 다음은 비즈니스용 Skype를 가지고 있는 경우의 지침입니다.

        1.  [회의 시작](https://support.office.com/article/Start-a-Skype-for-Business-conference-call-8dc8ac52-91ac-4db9-8672-11551fdaf997)
        2. [데스크톱 공유](https://support.office.com/article/Share-your-screen-in-Skype-for-Business-2d436dc9-d092-4ef1-83f1-dd9f7a7cd3fc)
        3. [기록 시작](https://support.office.com/article/Share-sites-or-documents-with-people-outside-your-organization-80e49744-e30f-44db-8d51-16661b1d4232)
        4. 기록을 중단한 후 [기록 관리자를 사용하여 기록을 게시](https://support.office.com/article/Recording-Manager-save-and-publish-59a3beb7-c700-40cf-ab21-bc82a2b06351)합니다.

        기록된 비디오를 공유 URL 생성이 가능한 서비스에 업로드해야 합니다.  예를 들어 [OneDrive/Sharepoint의 게스트 링크](https://support.office.com/article/Share-sites-or-documents-with-people-outside-your-organization-80e49744-e30f-44db-8d51-16661b1d4232)를 사용할 수 있습니다. 

    2.  솔루션 아키텍처 다이어그램을 업로드하는 경우 지침은 [고급 분석 솔루션 워크플로 템플릿](https://partnersprofilesint.blob.core.windows.net/partner-assets/documents/AppSource%20Solution%20Publishing%20Guide%20Docs/Advanced%20Analytics%20Solution%20Workflow%20Template%20V.2017.3.23.pptx)을 참조하세요.

6.  상점 정보 섹션을 작성합니다.

    1.  참고: “제품 설명” 및 “사용 조건” 필드의 경우 HTML 콘텐츠를 가져올 수 있습니다. 아무 온라인 HTML 편집기를 사용하여 HTML 콘텐츠를 적절해 보일 때까지 수정할 수 있습니다. 제품 설명은 해당 제품이 해결하는 비즈니스 사례를 설명하는 영업 메시지, 고객이 얻을 수 있는 투자 수익(ROI) 및 영업 메시지를 뒷받침하는 자료를 매우 명확하고 간결하게 제공해야 합니다.

    2.  앱 유형: “AppSource에 게시할 제품의 유형 결정”이라는 제목의 이전 섹션을 기반으로 다음 옵션을 선택합니다.
        1.  “무료” - 고객에게 시간 제한이 없는 완전 무료 경험을 제공하려는 경우.
        2.  “평가판” - 고객에게 시간 제한이 있는 평가판 / 시험 사용 경험을 제공하려는 경우.
        3.  “평가판 요청” - 고객에게 파트너 주도 평가판 경험을 제공하려는 경우.

    3.  스테이징으로 제품을 시험하는 숨기기 키: 이는 앱을 스테이징으로 실행한 후 미리 보기 URL을 만들기 위해 사용되며 단순하게 유지해도 좋은 암호가 아닙니다. 예: “AppSourceHideKey”

    4.  업로드하는 이미지는 제품 게시 승인을 받기 위해 높은 품질의 읽을 수 있는 이미지여야 합니다.

    5.  사례 연구: 다음 정보를 포함한 사례 연구/스토리를 제공합니다.

        1.  조작 가능한 고객 구현
        2. 실제 고객 또는 익명 고객에 대해 명확하게 정의된 문제 설명
        3. 문제를 해결하기 위해 수행한 단계
        4. 문제 해결에 도움이 되었던 CIS/Microsoft 고급 분석 구성 요소를 참조하세요.
        5.  솔루션을 만들기의 순 이익 또는 투자 수익(ROI)
        6. *바람직하지 않은 경우*: 위와 같은 요구 사항 없이 전체 플랫폼을 단순히 설명하는 마케팅 브로슈어 또는 비디오

    6.  더 많은 잠재 고객을 창출하므로 적극 권장: AppSource에 표시할 사례 연구와 같은 요구 사항을 적용한 비디오. 비디오에 대한 URL을 입력하는 경우 Youtube 또는 vimeo 비디오를 입력할 수 있습니다. 비디오가 다음 두 형식 중 하나인지 확인하세요.
        1. https://vimeo.com/########## 또는
        2. https://www.youtube.com/watch?v=##########

7.  연락처 섹션을 작성하세요. 이 연락처는 사용량 알림, 시장 경고 등을 보내는 데 사용됩니다.

8.  제품을 게시하세요. 솔루션이 인증 프로세스를 통과하여 이동할 때 전자 메일 알림을 받습니다. 제품 게시 UI의 “상태” 탭에서 상태 정보를 볼 수 있습니다.

    1.  고객의 솔루션이 먼저 시작됩니다.
    2.  전자 메일로 받은 AppSource 링크를 클릭하여 고객의 스테이징한 콘텐츠를 확인할 수 있습니다.
    3.  필요한 변경을 하고 다시 제출하세요. 최종 콘텐츠에 만족하면 앱을 프러뎍션에 푸시하세요.
    4.  이 시점에서 당사는 앱의 메타 데이터의 유효성을 검사하고 고객의 앱을 AppSource에 등록하도록 최종 승인하거나 당사가 고객의 앱을 거부하는 경우 거부 사유로 후속 처리를 합니다.

[1]: ./media/cloud-partner-portal-publish-cortana-intelligence-app/publishaaapp1.png
[2]: ./media/cloud-partner-portal-publish-cortana-intelligence-app/publishaaapp2.png    

