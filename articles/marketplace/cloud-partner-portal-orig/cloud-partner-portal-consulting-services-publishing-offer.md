---
title: 컨설팅 서비스 | Microsoft Docs
description: 컨설팅 서비스 제품을 정의 및 게시할 때 참조할 가이드입니다.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: b3f6b9166afce9ca0cdeaa1c9dd6e10a5a976453
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2018
ms.locfileid: "48807965"
---
<a name="defining-and-publishing-your-consulting-services-offer"></a>컨설팅 서비스 제품 정의 및 게시
======================================================

이 가이드에서는 클라우드 파트너 포털에서 컨설팅 서비스 제품을 정의하고 게시할 때 참조할 수 있는 정보를 제공합니다.

<a name="define-your-consulting-services-offer"></a>컨설팅 서비스 제품 정의
-------------------------------------

패키지로 만든 컨설팅 서비스 제품을 정의합니다. 이 과정에서는 고정 범위, 고정 기간, 예상/고정 가격 또는 무료 제공 여부 및 단일 고객 대상 사전 판매 중심 서비스를 중점적으로 결정합니다. 그리고 신규 거래 성사 과정에서 자주 사용되었으며 효율성이 확인된 반복 가능한 패키지 기능을 선택합니다.

<a name="publish-a-consulting-service-offer"></a>컨설팅 서비스 제품 게시
----------------------------------

다음 섹션에서는 컨설팅 서비스 제품을 게시하는 프로세스를 설명합니다.

1.  새 제품 만들기
2.  제품 설정 정의
3.  상점 정보 입력/게시할 위치(Azure Marketplace 또는 AppSource) 지정
4.  제품 게시

### <a name="create-a-new-offer"></a>새 제품 만들기

새 제품을 만들려면 다음 단계를 완료합니다.

1.  클라우드 파트너 포털의 주 메뉴에서 **새 제품**을 선택합니다.
2.  새 제품 메뉴에서 **컨설팅 서비스**를 선택합니다.

    ![새 컨설팅 서비스 제품 만들기](media/consulting-services-publishing-offer/cppselectnewconsultingoffer.png)

### <a name="define-offer-settings"></a>제품 설정 정의

새 제품 화면에서 처음으로 수행하는 단계는 제품 ID 만들기입니다.
제품 ID는 다음의 세 부분으로 구성됩니다.

1.  제품 ID
2.  게시자 ID
3.  이름

다음 섹션에서 이러한 각 부분에 대해 설명합니다.

#### <a name="offer-id"></a>제품 ID

제품을 처음 제출할 때 만드는 고유한 이름입니다. 제품 ID에는 소문자 영숫자 문자, 대시 또는 밑줄만 사용해야 합니다. 이 ID는 URL에 표시되며 검색 엔진 결과에 영향을 줍니다. 예를 들어 *yourcompanyname\_exampleservice*와 같은 ID를 지정할 수 있습니다.

예제에 나와 있는 것처럼 제품 ID가 게시자 ID에 추가되는 방식으로 고유한 식별자가 생성됩니다. 이 식별자는 검색 엔진에서 인덱싱하며 예약 가능한 고유 링크로 표시됩니다.

**제품이 라이브로 전환된 후에는 제품 ID를 업데이트할 수 없습니다.**

#### <a name="publisher-id"></a>게시자 ID

계정과 관련된 ID입니다. 조직 계정으로 로그인하는 경우 게시자 ID가 드롭다운 메뉴에 표시됩니다.

#### <a name="name"></a>이름

AppSource 또는 Azure Marketplace에서 제품 이름으로 표시되는 이름입니다.

**중요:** 여기에는 실제 서비스 이름만 입력해야 하며 서비스의 유형과 제공 기간은 포함하면 안 됩니다.

Edgewater Fullscope의 다음 예제는 제품 이름이 조합되는 방식을 보여 줍니다. 제품 이름은 다음과 같이 표시됩니다.

![새 컨설팅 서비스 제품 만들기](media/consulting-services-publishing-offer/cppsampleconsultingoffer.png)

제품 이름은 다음의 네 부분으로 구성됩니다.

-   **기간:** 편집기의 상점 정보 탭에서 정의합니다. 기간은 시간, 일 또는 주 단위로 표시할 수 있습니다.
-   **서비스 유형:** 편집기의 상점 정보 탭에서 정의합니다. 서비스 유형에는 평가, 브리핑, 구현, 개념 증명 및 워크샵이 있습니다.
-   **전치사:** 검토자가 삽입합니다.
-   **이름:** 제품 설정 페이지에서 정의한 이름입니다.

다음 목록에 적절하게 지정된 몇 가지 제품 이름이 나와 있습니다.

-   전문 서비스 필수 사항: 1시간 브리핑
-   클라우드 마이그레이션 플랫폼: 1시간 브리핑
-   PowerApps 및 Microsoft Flow: 1시간 워크샵
-   Azure Machine Learning Services: 3주 PoC
-   Brick and Click Retail 솔루션: 1시간 브리핑
-   BYOD(Bring Your Own Data): 1주 워크샵
-   Cloud Analytics: 3일 워크샵
-   Power BI 교육: 3일 워크샵
-   영업 관리 솔루션: 1주 구현
-   CRM 빠른 시작: 1일 워크샵
-   Dynamics 365 for Sales: 2일 평가

**제품 설정** 탭에 필요한 정보를 모두 입력한 후에는 제출 내용을 저장할 수 있습니다. 이제 제품 이름이 편집기 위에 표시되며, 모든 제품으로 돌아오면 이름을 확인할 수 있습니다.

### <a name="enter-storefront-details"></a>상점 정보 입력

다음으로는 상점의 세부 정보를 입력해야 합니다. 상점 정보는 다음 섹션으로 구성되어 있습니다.

-   제품 세부 정보
-   게시자 정보
-   목록 세부 정보
-   마케팅 아티팩트

#### <a name="offer-details"></a>제품 세부 정보

제품 세부 정보 섹션에는 다음 필드가 포함되어 있습니다.

-   제품 요약
-   제품 설명

##### <a name="offer-summary"></a>제품 요약

제품 요약은 제품 이름 바로 아래에 표시되는 제품의 간략한 설명입니다. 제품 요약 입력 시에는 일반 텍스트를 사용해야 하며 줄 바꿈을 포함하면 안 됩니다. 아래에는 해당 제품 이름에 적합한 제품 요약의 예제가 나와 있습니다.

*예 1*

-   **제품 이름:** Cloud Analytics: 3일 워크샵
-   **제품 요약:** Microsoft Azure 및 Power BI의 개요를 살펴보고, 현재 환경을 평가하고, 미니 POC를 진행합니다.

*예 2*

-   **제품 이름:** 산업용 Azure IoT: 30일 개념 증명
-   **제품 요약:** 대시보드, 보고서 및 알림이 포함된 Azure IoT Hub 솔루션에 현장의 장비를 안전하게 연결하기 위한 산업용 커넥티드 제품 파일럿을 작성합니다.

*예 3*

-   **제품 이름:** 전문 서비스: 1시간 브리핑
-   **제품 요약:** 전문 서비스용으로 향상된 프로젝트, 청구 및 리소스 관리 기능을 제공하는 미리 구성된 확장형 Dynamics 365 for Operations 솔루션의 데모와 개요를 제공합니다.

*예제 4*

-   **제품 이름:** 사용자 환경의 Power BI: 4시간 워크샵
-   **제품 요약:** 첫 번째 대시보드를 작동/실행하고 모범 사례를 알아봅니다(수강자 최대 12명, 현장 진행).

*예제 5*

-   **제품 이름:** Dynamics와 프로젝트: 3일 평가
-   **제품 요약:** 전문 서비스 업체 및 프로젝트 중심 기업용 ERP 솔루션에 대한 요구 사항 수집 및 평가를 진행합니다.

##### <a name="offer-description"></a>제품 설명

컨설팅 서비스 제품의 설명입니다. 적절한 제품 설명에는 컨설팅 과정의 정확한 세부 정보와 고객에게 제공되는 최종 결과물 관련 정보가 포함됩니다. 또한 고객이 제공받을 서비스를 명확하게 파악할 수 있어야 합니다.

연락용 전자 메일 링크나 전화 번호는 제품 설명에 포함하지 마세요. 제품의 문의처 단추를 클릭하면 제품에서 확인된 잠재 고객이 잠재 고객 관리 대상에 업로드됩니다.

제품 설명은 Markdown 형식으로 입력합니다. HTML용 서식 또는 Markdown에 대해 잘 모르는 경우 [Microsoft Docs 사이트](https://docs.microsoft.com/contribute/how-to-write-use-markdown)의 리소스를 검토할 수 있습니다.

이러한 형식을 사용하면 고객이 가장 쉽게 확인할 수 있는 제품을 만들 수 있습니다.

사용자는 텍스트 양이 많으면 모두 읽지 않을 가능성이 높으므로, 제품 설명은 문자 제한 범위 내에서 간략하게 작성합니다. 제품에 대해 자세히 설명하는 마케팅 브로슈어, 팩트 시트 및 기타 문서를 업로드하는 옵션도 제공됩니다.

다음 예제에는 적절하게 작성된 제품 설명과 관련 이름 및 요약이 나와 있습니다.

**제품 이름:** Cloud Analytics: 3일 워크샵

**제품 요약:** Microsoft Azure 및 Power BI의 개요를 살펴보고, 현재 환경을 평가하고, 미니 POC를 진행합니다.

**제품 설명:** 기술/실무 책임자를 대상으로 하는 이 워크샵은 고객 시설 현장에서 3일 동안 진행됩니다.

***안건***

1일차

-   Azure Data Lake, HDInsight 또는 Azure SQL Data Warehouse를 사용하여 Microsoft 클라우드 내에서 데이터를 보호하고 크기를 조정하고 구성하는 방법을 중점적으로 파악합니다.

2일차

-   Microsoft R 및 Azure Machine Learning을 사용하여 고급 분석 솔루션을 구성하고 배포하는 방법을 살펴봅니다.

3일차

-   Power BI를 통해 작업 가능한 분석 정보를 수집하고 분석 기능을 조작하는 방법을 살펴봅니다. Power BI 대시보드를 공동으로 빌드하는 공동 작업 세션이 포함됩니다.

고객은 워크샵을 완료하면 Microsoft 클라우드의 데이터 및 분석 솔루션용으로 높은 수준의 계획 및 구현 로드맵을 정의할 수 있게 됩니다.

*이 형식에 따른 제품의 샘플 Markdown 파일:*

    This 3-day workshop is for technical and business leaders and is held on-site at the client’s facility.

      ### Agenda

      **Day 1**

      * Focuses on how to secure, scale, and organize data within the Microsoft cloud using Azure Data Lake, HDInsight, or Azure SQL Data Warehouse

      **Day 2**

      * Covers how to configure and deploy advanced analytics solutions with Microsoft R and Azure Machine Learning

      **Day 3**

      * Covers how to draw actionable insights and operationalize analytics with Power BI, including a collaborative session to co-build a Power BI dashboard.


      ### Deliverables
      By the end of the workshop, the client will be able to define a high-level plan and an implementation roadmap for data and analytics solutions in the Microsoft cloud.


#### <a name="publisher-information"></a>**게시자 정보**

**MPN ID**

9자리 MPN(Microsoft 파트너 네트워크) ID입니다. MPN ID가 없는 경우 Microsoft 파트너 센터에서 받을 수 있습니다.

**파트너 센터 ID**

새 파트너 센터 ID(있는 경우)입니다.

**MPN ID**

제품을 라이브로 전환하기 전에 AppSource에서 미리 보는 데 사용할 비밀 키를 입력합니다.
이 ID는 암호가 아닙니다.

#### <a name="listing-details"></a>목록 세부 정보

**컨설팅 서비스 유형**

Microsoft는 단일 고객 및 현장에서 또는 가상으로 진행되는 평가/구현/개념 증명/브리핑/워크샵 제품(브리핑은 현장에서 진행해야 함)용 고정 범위, 고정 기간, 예상/고정 가격 또는 무료 제공 여부 및 사전 판매 중심 서비스만을 중점적으로 제공합니다.

포함되는 5가지 서비스 유형은 다음과 같습니다.

-   **평가:** 솔루션의 적용 가능성을 확인하고 예상 비용 및 타이밍을 제공하기 위한 고객 환경 평가입니다.
-   **브리핑:**: 프레임워크, 데모 및 고객 예제를 사용하여 고객의 관심을 끌기 위한 솔루션 또는 컨설팅 서비스를 소개합니다. 브리핑은 현장에서 진행해야 합니다.
-   **구현:** 완벽하게 작동하는 솔루션으로 이어지는 완전한 설치. 이 파일럿의 경우에는 1주일 이내에 구현할 수 있는 솔루션만 포함하는 것이 좋습니다.
-   **개념 증명:** 솔루션이 고객의 요구 사항을 충족하는지 확인하기 위한 제한된 범위의 구현입니다.
-   **워크샵:** 고객 쪽에서 수행되는 대화형 활동으로 고객의 데이터 또는 환경에서 작성된 교육 세션, 브리핑, 평가 또는 데모가 포함될 수 있습니다.

**국가/하위 지역 가용성**

컨설팅 서비스 제품을 제공할 국가와 지역을 선택합니다. 한 제품을 여러 국가나 지역에 게시할 수는 없습니다. 각 국가나 지역에 새 제품을 만들어야 합니다.

**산업**

컨설팅 서비스 제품과 가장 관련성이 높은 산업을 선택합니다.

**Duration**

기간 아래에서 3, 4 등의 숫자를 선택한 다음 시간, 일, 주를 선택합니다.

**기본 제품**

Azure Marketplace에 게시하려면 기본 제품으로 **Azure**를 선택한 후에 관련 솔루션 영역을 선택합니다.

AppSource에 게시하려면 기본 제품으로 **Dynamics 365**, **Power BI** 또는 **PowerApps**를 선택합니다. 관련성이 있는 기타 적용 가능한 제품을 선택할 수도 있습니다. 그러면 컨설팅 서비스 제품이 AppSource에서 이러한 각 제품과 연관된 목록에 표시됩니다.

**관련 역량**

이 제품과 관련된 역량을 선택합니다. 선택한 역량은 제품 세부 정보와 함께 표시됩니다.

#### <a name="marketing-artifacts"></a>마케팅 아티팩트

**회사 로고(.png 형식, 48x48픽셀)**

제품 갤러리 보기 페이지의 제품 타일에 표시할 이미지를 업로드합니다. 이미지는 해상도가 48x48픽셀인 .png 이미지여야 합니다.

**회사 로고(.png 형식, 216x216픽셀)**

제품 세부 정보 페이지에 표시할 이미지를 업로드합니다. 이미지는 해상도가 216x216픽셀인 .png 이미지여야 합니다.

**비디오(최대 4개)**

고객 사례 연구 비디오 또는 고객 참조 비디오를 4개까지 업로드합니다. 이러한 비디오가 없으면 제품과 관련된 회사의 전문 지식을 설명하는 비디오를 업로드합니다. Power BI 또는 PowerApps 솔루션 쇼케이스가 있으면 여기에 쇼케이스 비디오를 업로드합니다. YouTube 또는 Vimeo 비디오 링크를 사용해야 합니다.

**문서(최대 3개)**

컨설팅 서비스 제품에 대해 자세히 설명하는 마케팅 브로슈어를 업로드합니다. 회사 개요, 팩트 시트 또는 사례 연구를 업로드할 수도 있습니다.

**스크린샷(최대 5개)**

제품, 제품 결과물 또는 회사에 대한 추가 정보를 제공하는 이미지를 5개까지 업로드합니다. 마케팅 브로슈어의 일부분, 프레젠테이션의 관련 슬라이드 또는 회사의 주요 연혁이나 전문 지식을 소개하는 이미지 등을 업로드할 수 있습니다.

### <a name="publish-your-offer"></a>제품 게시

제품 설정, 상점 정보 및 연락처를 모두 입력한 후에 **게시**를 선택하고 전자 메일 주소를 입력합니다. Microsoft에서 제품을 게시할 준비가 되면 제품이 라이브로 전환되기 전에 미리 볼 수 있도록 이메일이 수신됩니다. 언제든지 포털로 돌아와서 제품 상태를 확인할 수 있습니다.

게시 프로세스 중에는 제품이 “게시 취소됨” 또는 “게시 실패” 상태로 표시될 수 있습니다. 이러한 상태는 게시 프로세스에서 Microsoft가 제품을 편집하기 위해 설정하는 정상적인 현상입니다. 따라서 제품이 “게시 취소됨”으로 표시되더라도 그대로 두면 됩니다.
