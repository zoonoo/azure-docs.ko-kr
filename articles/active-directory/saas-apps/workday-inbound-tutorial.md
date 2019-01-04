---
title: '자습서: Azure Active Directory를 통해 자동 사용자 프로비전을 위한 Workday 구성 | Microsoft Docs'
description: 사용자 계정을 Workday로 자동으로 프로비전 및 프로비전 해제하도록 Azure Active Directory를 구성하는 방법을 알아봅니다.
services: active-directory
author: cmmdesai
documentationcenter: na
manager: mtillman
ms.assetid: 1a2c375a-1bb1-4a61-8115-5a69972c6ad6
ms.service: active-directory
ms.component: saas-app-tutorial
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/18/2018
ms.author: chmutali
ms.openlocfilehash: 754c3278cb01e010718fa4d3cb257acf6ffe99c9
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/04/2018
ms.locfileid: "52849856"
---
# <a name="tutorial-configure-workday-for-automatic-user-provisioning-preview"></a>자습서: 자동 사용자 프로비저닝을 위한 Workday 구성(미리 보기)

이 자습서의 목표는 Workday에서 Active Directory 및 Azure Active Directory로 작업자 프로필을 가져오기 위해 수행해야 하는 단계를 보여 주고 선택 사항으로 이메일 주소를 Workday에 쓰기 저장하는 방법을 보여 주는 것입니다.

## <a name="overview"></a>개요

[Azure Active Directory 사용자 프로비전 서비스](../manage-apps/user-provisioning.md)는 사용자 계정을 프로비전하기 위해 [Workday 인적 자원 API](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html)와 통합됩니다. Azure AD는 이 연결을 사용하여 다음 사용자 프로비전 워크플로를 가능하게 합니다.

* **사용자를 Active Directory에 프로비전** - Workday에서 선택한 사용자 집합을 하나 이상의 Active Directory 도메인과 동기화합니다.

* **Azure Active Directory에 클라우드 전용 사용자 프로비저닝** - 온-프레미스 Active Directory가 사용되지 않는 시나리오에서는 Azure AD 사용자 프로비저닝 서비스를 사용하여 사용자를 Workday에서 Azure Active Directory로 직접 프로비전할 수 있습니다. 

* **Workday에 이메일 주소 쓰기 저장** - Azure AD 사용자 프로비저닝 서비스는 Azure AD 사용자의 이메일 주소를 Workday에 다시 쓸 수 있습니다. 

### <a name="what-human-resources-scenarios-does-it-cover"></a>여기서 다루는 인적 자원 시나리오

Azure AD 사용자 프로비전 서비스에서 지원되는 Workday 사용자 프로비전 워크플로는 다음과 같은 인적 자원 및 ID 수명 주기 관리 시나리오의 자동화를 지원합니다.

* **신규 직원 고용** - 신규 직원이 Workday에 추가되면 Active Directory, Azure Active Directory 그리고 선택적으로 Office 365 및 [Azure AD에서 지원하는 기타 SaaS 응용 프로그램](../manage-apps/user-provisioning.md)에서 사용자 계정이 자동으로 생성되며, Workday에 이메일 주소를 다시 씁니다.

* **직원 특성 및 프로필 업데이트** - Workday에서 이름, 직함, 관리자 등의 직원 레코드가 업데이트되면 Active Directory, Azure Active Directory 그리고 선택적으로 Office 365 및 [Azure AD에서 지원하는 기타 SaaS 응용 프로그램](../manage-apps/user-provisioning.md)에서 해당 사용자 계정이 자동으로 업데이트됩니다.

* **직원 퇴사** - Workday에서 직원이 퇴사하면 Active Directory, Azure Active Directory 그리고 선택적으로 Office 365 및 [Azure AD에서 지원하는 기타 SaaS 응용 프로그램](../manage-apps/user-provisioning.md)에서 해당 사용자 계정이 자동으로 비활성화됩니다.

* **직원 재고용** - Workday에서 직원이 다시 고용되면 Active Directory, Azure Active Directory 그리고 선택적으로 Office 365 및 [Azure AD에서 지원하는 기타 SaaS 응용 프로그램](../manage-apps/user-provisioning.md)에서 해당 직원의 기존 계정이 자동으로 다시 활성화되거나 다시 프로비전됩니다(기본 설정에 따라).

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>이 사용자 프로비전 솔루션에 가장 적합한 사용자

이 Workday 사용자 프로비전 솔루션은 현재 공개 미리 보기 상태이며 다음과 같은 경우에 가장 적합합니다.

* Workday 사용자 프로비전에 미리 작성된 클라우드 기반 솔루션을 원하는 조직

* Workday에서 Active Directory 또는 Azure Active Directory로 직접 사용자 프로비전이 필요한 조직

* Workday HCM 모듈에서 가져온 데이터를 사용하여 사용자를 프로비전해야 하는 조직([Get_Workers](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html) 참조)

* Workday HCM 모듈에서 감지된 변경 내용 정보에 따라서만 하나 이상의 Active Directory 포리스트, 도메인 및 OU를 동기화하도록 사용자를 조인하고, 이동시키고, 유지해야 하는 조직([Get_Workers](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html) 참조)

* 이메일에 Office 365를 사용하는 조직

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-hybrid-note.md)]

## <a name="solution-architecture"></a>솔루션 아키텍처

이 섹션에서는 일반적인 하이브리드 환경을 위한 종단 간 사용자 프로비전 솔루션 아키텍처에 대해 설명합니다. 두 가지의 관련된 흐름이 있습니다.

* **권한이 있는 HR 데이터 흐름 – Workday에서 온-프레미스 Active Directory로:** 이 흐름에서는 작업자 이벤트(예: 신규 고용, 전근, 종료)가 클라우드 Workday HR 테넌트에서 먼저 발생한 다음, 이벤트 데이터가 Azure AD 및 프로비전 에이전트를 통해 온-프레미스 Active Directory로 흐릅니다. 이벤트에 따라 AD에서 만들기/업데이트/사용/사용 안 함 작업이 이루어질 수 있습니다.
* **이메일 쓰기 저장 흐름 - 온-프레미스 Active Directory에서 Workday로:** Active Directory에서 계정 만들기가 완료되면 Azure AD Connect를 통해 Azure AD와 동기화되고 Active Directory에서 제공되는 이메일 특성을 Workday에 쓰기 저장할 수 있습니다.

![개요](./media/workday-inbound-tutorial/wd_overview.png)

### <a name="end-to-end-user-data-flow"></a>종단 간 사용자 데이터 흐름

1. HR 팀은 Workday HCM에서 작업자 트랜잭션(참가자/이동자/이탈자 또는 신규 고용/전근/종료)을 수행합니다.
2. Azure AD Provisioning Service는 Workday HR에서 예약된 ID 동기화를 실행하고 온-프레미스 Active Directory와의 동기화를 위해 처리해야 하는 변경 내용을 식별합니다.
3. Azure AD Provisioning Service는 AD 계정 만들기/업데이트/사용/사용 안 함 작업이 포함된 요청 페이로드로 온-프레미스 AAD Connect Provisioning Agent를 호출합니다.
4. Azure AD Connect 프로비전 에이전트는 서비스 계정을 사용하여 AD 계정 데이터를 추가/업데이트합니다.
5. Azure AD Connect / AD Sync 엔진이 AD에서 업데이트를 끌어오기 위해 델타 동기화를 실행합니다.
6. Active Directory 업데이트는 Azure Active Directory와 동기화됩니다.
7. Workday 쓰기 저장 커넥터가 구성되어 있으면 사용된 해당 특성에 따라 Workday에 이메일 특성을 쓰기 저장합니다.


## <a name="planning-your-deployment"></a>배포 계획

Workday 통합을 시작하기 전에 다음과 같은 필수 조건을 확인하고, 현재 Active Directory 아키텍처 및 사용자 프로비전 요구 사항을 Azure Active Directory에서 제공하는 솔루션과 일치시키는 방법에 대한 지침을 읽어보세요.

### <a name="prerequisites"></a>필수 조건

이 자습서에 설명된 시나리오에서는 사용자에게 이미 다음 항목이 있다고 가정합니다.

* 전역 관리자 액세스 권한이 있는 유효한 Azure AD Premium P1 구독
* 테스트 및 통합을 위한 Workday 구현 테넌트
* 테스트 목적으로 시스템 통합 사용자를 만들고 직원 데이터를 변경하기 위한 관리자 권한
* Active Directory로 사용자 프로비전을 위해서는 [온-프레미스 프로비전 에이전트](https://go.microsoft.com/fwlink/?linkid=847801)를 호스트하려면 .NET 4.7 이상 런타임과 함께 Windows Server 2012 이상을 실행하는 서버가 필요합니다.
* Active Directory와 Azure AD 간의 동기화를 위한 [Azure AD Connect](../hybrid/whatis-hybrid-identity.md)

### <a name="planning-considerations"></a>고려 사항 계획

Azure AD는 Workday에서 Active Directory, Azure AD, SaaS 앱 등으로 프로비전하고 ID 수명 주기 관리 문제를 해결할 수 있는 다양한 프로비전 커넥터를 제공합니다. 사용하게 될 기능과 솔루션 설정 방법은 조직의 환경과 요구 사항에 따라 달라집니다. 첫 번째 단계로 조직에서 다음 항목 중 몇 개를 갖고 있으며 몇 개가 배포되었는지 점검합니다.

* Active Directory 포리스트를 몇 개나 사용하고 있나요?
* Active Directory 도메인을 몇 개나 사용하고 있나요?
* Active Directory OU(조직 구성 단위)를 몇 개나 사용하고 있나요?
* Azure Active Directory 테넌트를 몇 개나 사용하고 있나요?
* Active Directory와 Azure Active Directory에 모두 프로비전해야 하는 사용자(예: “하이브리드” 사용자)가 있나요?
* Azure Active Directory에만 프로비전하고 Active Directory에는 프로비전하면 안 되는 사용자(예: “클라우드 전용” 사용자)가 있나요?
* 사용자 이메일 주소를 Workday에 다시 써야 하나요?

이러한 질문에 대답한 후에는 아래 지침에 따라 Workday 프로비전 배포를 계획할 수 있습니다.

#### <a name="planning-deployment-of-aad-connect-provisioning-agent"></a>AAD Connect 프로비전 에이전트 배포 계획

Workday에서 AD로 사용자 프로비전 솔루션을 사용하려면 최소 4GB RAM 및 .NET 4.7 이상 런타임과 함께 Windows 2012 R2 이상을 실행하는 서버에 하나 이상의 프로비전 에이전트를 배포해야 합니다. 프로비전 에이전트를 설치하기 전에 다음을 고려해야 합니다.

* 프로비전 에이전트를 실행하는 호스트 서버에 대상 AD 도메인에 대한 네트워크 액세스 권한이 있는지 확인합니다.
* 프로비전 에이전트 구성 마법사는 Azure AD 테넌트에 에이전트를 등록하며 등록 과정 중에 포트 8082에서 *.msappproxy.net에 대한 액세스가 필요합니다. 이 통신을 가능하게 하는 아웃바운드 방화벽 규칙이 있는지 확인합니다.
* 프로비전 에이전트는 서비스 계정을 사용하여 온-프레미스 AD 도메인과 통신합니다. 에이전트를 설치하기 전에 사용자 속성 읽기/쓰기 권한과 만료되지 않는 암호를 사용하여 서비스 계정을 만드는 것이 좋습니다.  
* 프로비전 에이전트 구성 중에는 프로비전 요청을 처리할 도메인 컨트롤러를 선택할 수 있습니다. 지리적으로 분산된 도메인 컨트롤러가 여러 개 있는 경우 기본 설정된 도메인 컨트롤러와 동일한 사이트에 프로비전 에이전트를 설치하여 종단 간 솔루션의 안정성과 성능을 개선합니다.
* 고가용성을 위해 프로비전 에이전트를 둘 이상 배포하고 등록하여 동일한 온-프레미스 AD 도메인 집합을 처리할 수 있습니다.

> [!IMPORTANT]
> 프로덕션 환경에서는 고가용성을 위해 Azure AD 테넌트로 최소 3개의 프로비전 에이전트를 구성하는 것이 좋습니다.

#### <a name="selecting-provisioning-connector-apps-to-deploy"></a>배포할 프로비전 커넥터 앱 선택

Workday 및 Active Directory를 통합할 때 여러 원본 및 대상 시스템을 고려해야 합니다.

| 원본 시스템 | 대상 시스템 | 메모 |
| ---------- | ---------- | ---------- |
| Workday | Active Directory 도메인 | 각 도메인은 고유한 대상 시스템으로 처리됨 |
| Workday | Azure AD 테넌트 | 클라우드 전용 사용자에 필요한 경우 |
| Active Directory 포리스트 | Azure AD 테넌트 | 이 흐름은 현재 AAD Connect에서 처리 |
| Azure AD 테넌트 | Workday | 이메일 주소의 쓰기 저장 |

Workday와 Active Directory 간에 프로비전 워크플로를 용이하게 하기 위해 Azure AD는 Azure AD 앱 갤러리에서 추가할 수 있는 여러 프로비전 커넥터 앱을 제공합니다.

![AAD 앱 갤러리](./media/workday-inbound-tutorial/wd_gallery.png)

* **Workday에서 Active Directory로 프로비전** - 이 앱은 Workday에서 단일 Active Directory 도메인으로 사용자 계정 프로비전을 가능하게 합니다. 도메인이 여러 개 있는 경우 프로비전해야 하는 각 Active Directory 도메인에 대해 Azure AD 앱 갤러리에서 이 앱의 하나의 인스턴스를 추가할 수 있습니다.

* **Workday에서 Azure AD로 프로비전** - AAD Connect는 Active Directory 사용자를 Azure Active Directory와 동기화하는 데 사용되는 도구이지만, 이 앱을 사용하여 Workday에서 단일 Azure Active Directory 테넌트로 클라우드 전용 사용자를 프로비전할 수 있습니다.

* **Workday 쓰기 저장** - 이 앱은 사용자 이메일 주소를 Azure Active Directory에서 Workday로 쓰기 저장할 수 있습니다.

> [!TIP]
> 일반 "Workday" 앱은 Workday와 Azure Active Directory 간의 Single Sign-On을 설정하는 데 사용됩니다. 

#### <a name="determine-workday-to-ad-user-attribute-mapping-and-transformations"></a>Workday에서 AD로 사용자 특성 매핑 및 변환 결정

Active Directory 도메인으로 사용자 프로비전을 구성하기 전에 다음과 같은 질문을 고려하세요. 이러한 질문에 대한 답은 범위 지정 필터 및 특성 매핑을 설정하는 방법을 결정합니다.

* **이 Active Directory 포리스트로 프로비전되어야 하는 Workday의 사용자**

   * *예제: Workday “Company” 특성에 “Contoso” 값이 포함되고 “Worker_Type” 특성에 “기본”이 포함되는 사용자입니다.*

* **다른 OU(조직 단위)에 사용자를 라우팅하는 방법**

   * *예제: Workday “Municipality” 및 “Country_Region_Reference” 특성에 정의된 대로 사무실 위치에 해당하는 OU로 사용자를 라우팅합니다.*

* **Active Directory에서 다음과 같은 특성을 채우는 방법**

   * 일반 이름(cn)
      * *예제: 인사과에서 설정한 대로 Workday User_ID 값을 사용합니다.*
      
   * 직원 ID(employeeId)
      * *예제: Workday Worker_ID 값을 사용합니다.*
      
   * SAM 계정 이름(sAMAccountName)
      * *예제: Workday User_ID 값을 사용하여 잘못된 문자를 제거하도록 Azure AD 프로비전 식을 통해 필터링됩니다.*
      
   * 사용자 계정 이름(userPrincipalName)
      * *예제: 도메인 이름을 추가하도록 Azure AD 프로비전 식에서 Workday User_ID 값을 사용합니다.*

* **Workday와 Active Directory 간에 사용자를 일치시키는 방법**

  * *예제: 특정 Workday “Worker_ID” 값과 일치하는 사용자는 “employeeID”에 같은 값을 가지는 Active Directory 사용자와 일치합니다. Worker_ID 값이 Active Directory에 없는 경우 새로운 사용자를 만듭니다.*
  
* **일치하는 논리가 작동하기 위해 필요한 사용자 ID가 Active Directory 포리스트에 포함되었는지 여부**

  * *예제: 새 Workday 배포인 경우 Active Directory를 올바른 Workday Worker_ID 값(또는 선택한 고유 ID 값)으로 미리 채워서 일치하는 논리를 최대한 단순하게 유지하는 것이 좋습니다.*



이러한 특수 프로비전 커넥터 앱을 설정하고 구성하는 방법은 본 자습서의 나머지 섹션에서 다루겠습니다. 구성을 위해 선택할 앱은 프로비전해야 하는 시스템, 환경에 있는 Active Directory 도메인 및 Azure AD 테넌트 수에 따라 결정됩니다.



## <a name="configure-integration-system-user-in-workday"></a>Workday에서 통합 시스템 사용자 구성

모든 Workday 프로비전 커넥터의 일반적인 요구 사항으로, Workday 시스템 통합 계정이 Workday 인적 자원 API에 연결하는 데 사용할 할 자격 증명이 필요합니다. 이 섹션에서는 Workday에서 통합 시스템 사용자를 만드는 방법을 설명합니다.

> [!NOTE]
> 이 절차를 건너뛰고 Workday 전역 Administrator 계정을 시스템 통합 계정으로 사용할 수도 있습니다. 이 방법이 데모에서는 제대로 작동할 수 있지만 프로덕션 배포에는 권장하지 않습니다.

### <a name="create-an-integration-system-user"></a>통합 시스템 사용자 만들기

**통합 시스템 사용자를 만들려면**

1. Administrator 계정을 사용하여 Workday 테넌트에 로그인합니다. **Workday 애플리케이션**의 검색 상자에서 사용자 만들기를 입력하고 **통합 시스템 사용자 만들기**를 클릭합니다.

    ![사용자 만들기](./media/workday-inbound-tutorial/wd_isu_01.png "사용자 만들기")
2. 새 통합 시스템 사용자에 대한 사용자 이름과 암호를 입력하여 **통합 시스템 사용자 만들기** 작업을 완료합니다.  
 * 이 사용자는 프로그래밍 방식으로 로그인할 것이므로 **다음 로그인할 때 새 암호 필요** 옵션을 선택하지 않습니다.
 * 사용자의 세션이 너무 빨리 시간 초과되지 않도록 **세션 시간 초과 분**을 기본값인 0으로 둡니다.
 * 통합 시스템 암호가 있는 사용자가 Workday에 로그인하지 못하게 하는 추가 보안 계층을 제공하기 때문에 **UI 세션 허용 안 함** 옵션을 선택합니다. 

    ![통합 시스템 사용자 만들기](./media/workday-inbound-tutorial/wd_isu_02.png "통합 시스템 사용자 만들기")

### <a name="create-a-security-group"></a>보안 그룹 만들기
이 단계에서는 무제한 통합 시스템 보안 그룹을 Workday에 만들고 이 그룹에 이전 단계에서 만든 통합 시스템 사용자를 할당합니다.

**보안 그룹을 만들려면**

1. 검색 상자에 보안 그룹 만들기를 입력하고 **보안 그룹 만들기**를 클릭합니다.

    ![보안 그룹 만들기](./media/workday-inbound-tutorial/wd_isu_03.png "보안 그룹 만들기")
2. **보안 그룹 만들기** 작업을 완료합니다.  
   * **테넌트 보안 그룹의 형식**에서 **통합 시스템 보안 그룹(제한되지 않음)** 을 선택합니다.

    ![보안 그룹 만들기](./media/workday-inbound-tutorial/wd_isu_04.png "보안 그룹 만들기")

3. 보안 그룹 생성이 완료되면 멤버를 보안 그룹에 할당할 수 있는 페이지가 표시됩니다. 새 통합 시스템 사용자를 이 보안 그룹에 추가하고 적절한 조직 범위를 선택합니다.
![보안 그룹 편집](./media/workday-inbound-tutorial/wd_isu_05.png "보안 그룹 편집")
 
### <a name="configure-domain-security-policy-permissions"></a>도메인 보안 정책 권한 구성
이 단계에서는 보안 그룹에 대한 작업자 데이터에 “도메인 보안” 정책 권한을 부여합니다.

**도메인 보안 정책 권한을 구성하려면**

1. 검색 상자에 **도메인 보안 구성**을 입력한 다음, **도메인 보안 구성 보고서** 링크를 클릭합니다.  

    ![도메인 보안 정책](./media/workday-inbound-tutorial/wd_isu_06.png "도메인 보안 정책")  
2. **도메인** 텍스트 상자에서 다음 도메인을 검색하고 필터에 하나씩 추가합니다.  
   * *외부 계정 프로비저닝*
   * *작업자 데이터: 공용 작업자 보고서*
   * *사용자 데이터: 회사 연락처 정보*
   * *작업자 데이터: 모든 위치*
   * *작업자 데이터: 현재 직원 충원 정보*
   * *작업자 데이터: 작업자 프로필 직함*
 
    ![도메인 보안 정책](./media/workday-inbound-tutorial/wd_isu_07.png "도메인 보안 정책")  

    ![도메인 보안 정책](./media/workday-inbound-tutorial/wd_isu_08.png "도메인 보안 정책") 

    **확인**을 클릭합니다.

3. 표시되는 보고서에서 **외부 계정 프로비저닝** 옆에 나타나는 줄임표(...)를 선택하고 메뉴 옵션 **도메인 -> 보안 정책 권한 편집**을 클릭합니다.

    ![도메인 보안 정책](./media/workday-inbound-tutorial/wd_isu_09.png "도메인 보안 정책")  

4. **도메인 보안 정책 권한 편집** 페이지에서 **통합 권한** 섹션까지 아래로 스크롤합니다. “+” 기호를 클릭하여 **Get** 및 **Put** 통합 권한이 있는 보안 그룹 목록에 통합 시스템 그룹을 추가합니다.

    ![사용 권한 편집](./media/workday-inbound-tutorial/wd_isu_10.png "사용 권한 편집")  

5. “+” 기호를 클릭하여 **Get** 및 **Put** 통합 권한이 있는 보안 그룹 목록에 통합 시스템 그룹을 추가합니다.

    ![사용 권한 편집](./media/workday-inbound-tutorial/wd_isu_11.png "사용 권한 편집")  

6. 이러한 나머지 보안 정책 각각에 대해 위의 3-5단계를 반복합니다.

   | 작업(Operation) | 도메인 보안 정책 |
   | ---------- | ---------- | 
   | 가져오기 및 넣기 | 작업자 데이터: 공용 작업자 보고서 |
   | 가져오기 및 넣기 | 사용자 데이터: 회사 연락처 정보 |
   | 가져오기 | 작업자 데이터: 모든 위치 |
   | 가져오기 | 작업자 데이터: 현재 직원 충원 정보 |
   | 가져오기 | 작업자 데이터: 작업자 프로필 직함 |

### <a name="configure-business-process-security-policy-permissions"></a>비즈니스 프로세스 보안 정책 권한 구성
이 단계에서는 보안 그룹에 대한 작업자 데이터에 “비즈니스 프로세스 보안” 정책 권한을 부여합니다. Workday 쓰기 저장 앱 커넥터를 설정하는 데 필요합니다. 

**비즈니스 프로세스 보안 정책 권한을 구성하려면**

1. 검색 상자에 **비즈니스 프로세스 정책**을 입력한 다음, **비즈니스 프로세스 보안 정책 편집** 작업 링크를 클릭합니다.  

    ![비즈니스 프로세스 보안 정책](./media/workday-inbound-tutorial/wd_isu_12.png "비즈니스 프로세스 보안 정책")  

2. **비즈니스 프로세스 유형** 텍스트 상자에서 *연락처*를 검색하고 **연락처 변경** 비즈니스 프로세스를 선택하고 **확인**을 클릭합니다.

    ![비즈니스 프로세스 보안 정책](./media/workday-inbound-tutorial/wd_isu_13.png "비즈니스 프로세스 보안 정책")  

3. **비즈니스 프로세스 보안 정책 편집** 페이지에서 **연락처 정보 유지 관리(웹 서비스)** 섹션으로 스크롤합니다.

    ![비즈니스 프로세스 보안 정책](./media/workday-inbound-tutorial/wd_isu_14.png "비즈니스 프로세스 보안 정책")  

4. 새 통합 시스템 보안 그룹을 선택하여 웹 서비스 요청을 시작할 수 있는 보안 그룹 목록에 추가합니다. **완료**를 클릭합니다. 

    ![비즈니스 프로세스 보안 정책](./media/workday-inbound-tutorial/wd_isu_15.png "비즈니스 프로세스 보안 정책")  

 
### <a name="activate-security-policy-changes"></a>보안 정책 변경 사항 활성화

**보안 정책 변경 사항을 활성화하려면**

1. 검색 상자에 활성화를 입력하고 **보류 중인 보안 정책 변경 내용 활성화** 링크를 클릭합니다.

    ![활성화](./media/workday-inbound-tutorial/wd_isu_16.png "활성화") 
2. 감사 목적에 대한 메모를 입력하고 **확인**을 클릭하여 보류 중인 보안 정책 변경 내용의 활성화 태스크를 시작합니다. 

    ![보류 중인 보안 활성화](./media/workday-inbound-tutorial/wd_isu_17.png "보류 중인 보안 활성화")  
1. 다음 화면에서 **확인** 확인란을 선택하여 태스크를 완료한 다음 **확인**을 클릭합니다.

    ![보류 중인 보안 활성화](./media/workday-inbound-tutorial/wd_isu_18.png "보류 중인 보안 활성화")  

## <a name="configuring-user-provisioning-from-workday-to-active-directory"></a>Workday에서 Active Directory로 사용자 프로비전 구성

다음 지침에 따라 Workday에서 통합 범위 안의 각 Active Directory 도메인으로 사용자 계정 프로비전을 구성합니다.

### <a name="part-1-install-and-configure-on-premises-provisioning-agents"></a>1부: 온-프레미스 프로비전 에이전트 설치 및 구성

Active Directory 온-프레미스로 프로비전하려면 .NET 4.7 이상 Framework가 있고 원하는 Active Directory 도메인에 대한 네트워크 액세스 권한이 있는 서버에 에이전트를 설치해야 합니다.

> [!TIP]
> [여기](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed)에 제공된 지침을 사용하여 서버에서 .NET Framework 버전을 확인할 수 있습니다.
> 서버에 .NET 4.7 이상이 설치되어 있지 않으면 [여기](https://support.microsoft.com/help/3186497/the-net-framework-4-7-offline-installer-for-windows)에서 다운로드할 수 있습니다.  

.NET 4.7 이상을 배포했으면 **[온-프레미스 프로비전 에이전트를 여기에서](https://go.microsoft.com/fwlink/?linkid=847801)** 다운로드하고 아래 제공된 단계에 따라 에이전트 구성을 완료할 수 있습니다.

1. 새 에이전트를 설치하려는 Windows Server에 로그인합니다.
2. 프로비전 에이전트 설치 관리자를 시작하고 동의한 다음, **설치** 단추를 클릭합니다.
![설치 화면](./media/workday-inbound-tutorial/pa_install_screen_1.png "설치 화면")

3. 설치가 완료되면 마법사가 시작되고 **Azure AD 연결** 화면이 표시됩니다. **인증** 단추를 클릭하여 Azure AD 인스턴스에 연결합니다.
![Azure AD 연결](./media/workday-inbound-tutorial/pa_install_screen_2.png "Azure AD 연결")

4. 글로벌 관리자 자격 증명을 사용하여 Azure AD 인스턴스에 대해 인증합니다. 
![관리 인증](./media/workday-inbound-tutorial/pa_install_screen_3.png "관리 인증")

5. Azure AD의 인증에 성공하면 **Active Directory 연결** 화면이 표시됩니다. 이 단계에서는 AD 도메인 이름을 입력하고 **디렉터리 추가** 단추를 클릭하세요.
![디렉터리 추가](./media/workday-inbound-tutorial/pa_install_screen_4.png "디렉터리 추가")

6. 이제 AD 도메인에 연결하는 데 필요한 자격 증명을 입력하라는 메시지가 표시됩니다. 동일한 화면에서 **도메인 컨트롤러 우선 순위 선택**을 사용하여 에이전트가 프로비전 요청을 보내는 데 사용할 도메인 컨트롤러를 지정할 수 있습니다.
![도메인 자격 증명](./media/workday-inbound-tutorial/pa_install_screen_5.png "도메인 자격 증명")

7. 도메인을 구성한 후 설치 관리자는 구성된 도메인 목록을 표시합니다. 이 화면에서 5 및 6단계를 반복하여 도메인을 더 추가하거나 **다음**을 클릭하여 에이전트 등록을 진행할 수 있습니다. 
![구성된 도메인](./media/workday-inbound-tutorial/pa_install_screen_6.png "구성된 도메인")

   > [!NOTE]
   > 여러 AD 도메인이 있는 경우(예: na.contoso.com, emea.contoso.com) 각 도메인을 목록에 개별적으로 추가하세요. 부모 도메인(예: contoso.com)만 추가하는 것은 충분하지 않으며 각 자식 도메인을 에이전트에 등록하는 것이 좋습니다. 

8. 구성 세부 정보를 검토하고 **확인**을 클릭하여 에이전트를 등록합니다. 
![확인 화면](./media/workday-inbound-tutorial/pa_install_screen_7.png "확인 화면")

9. 구성 마법사가 에이전트 등록의 진행률을 표시합니다.
![에이전트 등록](./media/workday-inbound-tutorial/pa_install_screen_8.png "에이전트 등록")

10. 에이전트 등록이 완료되면 **종료**를 클릭하여 마법사를 끝낼 수 있습니다. 
![종료 화면](./media/workday-inbound-tutorial/pa_install_screen_9.png "종료 화면")

11. 에이전트 설치를 확인하고 “서비스” 스냅인을 열고 “Microsoft Azure AD Connect 프로비전 에이전트” ![서비스](./media/workday-inbound-tutorial/services.png)라는 서비스를 찾아 해당 에이전트가 실행 중인지 확인합니다.  


**에이전트 문제 해결**

에이전트를 호스팅하는 Windows Server 컴퓨터의 [Windows 이벤트 로그](https://technet.microsoft.com/library/cc722404(v=ws.11).aspx)는 에이전트에서 수행하는 모든 작업에 대한 이벤트를 포함합니다. 이러한 이벤트를 보려면:
    
1. **Eventvwr.msc**를 엽니다.
2. **Windows 로그 > 응용 프로그램**을 선택합니다.
3. 원본 **AAD.Connect.ProvisioningAgent** 아래에서 기록된 모든 이벤트를 봅니다. 
4. 오류 및 경고를 확인합니다.

    
### <a name="part-2-adding-the-provisioning-connector-app-and-creating-the-connection-to-workday"></a>2부: 프로비전 커넥터 앱 추가 및 Workday에 대한 연결 만들기

**Workday에서 Active Directory로의 프로비전을 구성하려면:**

1.  <https://portal.azure.com>으로 이동합니다.

2.  왼쪽 탐색 모음에서 **Azure Active Directory**를 선택합니다.

3.  **엔터프라이즈 응용 프로그램**, **모든 응용 프로그램**을 차례로 선택합니다.

4.  **응용 프로그램 추가**를 선택하고 **모두** 범주를 선택합니다.

5.  **Active Directory에 Workday 프로비전**을 검색하고, 갤러리에서 해당 앱을 추가합니다.

6.  앱이 추가되고 앱 세부 정보 화면이 표시되면 **프로비전**을 선택합니다.

7.  **프로비전** **모드**를 **자동**으로 변경합니다.

8.  다음과 같이 **관리자 자격 증명** 섹션을 완료합니다.

   * **관리 사용자 이름** – 테넌트 도메인 이름이 추가된 Workday 통합 시스템 계정의 사용자 이름을 입력합니다. **다음과 같은 형태여야 합니다. username@contoso4**

   * **관리자 암호 –** Workday 통합 시스템 계정의 암호를 입력합니다.

   * **테넌트 URL –** 해당 테넌트의 Workday 웹 서비스 엔드포인트에 대한 URL을 입력합니다. https://wd3-impl-services1.workday.com/ccx/service/contoso4/Human_Resources와 같은 형태여야 하며, 여기서 contoso4를 올바른 테넌트 이름으로 바꾸고 wd3-impl을 올바른 환경 문자열로 바꾸면 됩니다.

   * **Active Directory 포리스트 -** 에이전트에 등록된 Active Directory 도메인의 “이름”입니다. 일반적으로 *contoso.com* 형태의 문자열입니다.

   * **Active Directory 컨테이너 -** 에이전트가 기본적으로 사용자 계정을 만드는 컨테이너 DN을 입력합니다. 
        예제: *OU=Standard Users,OU=Users,DC=contoso,DC=test*
> [!NOTE]
> 이 설정은 *parentDistinguishedName* 특성이 특성 매핑에 구성되지 않은 경우에만 사용자 계정 생성에 사용됩니다. 이 설정은 사용자 검색 또는 업데이트 작업에는 사용되지 않습니다. 전체 도메인 하위 트리는 검색 작업의 범위에 속합니다.
   * **알림 이메일 –** 이메일 주소를 입력하고 "오류가 발생하면 이메일 보내기" 확인란을 선택합니다.
> [!NOTE]
> Azure AD 프로비전 서비스는 프로비전 작업이 [격리](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning#quarantine) 상태가 되면 이메일 알림을 보냅니다.

   * **연결 테스트** 단추를 클릭합니다. 연결 테스트가 성공하면 맨 위에서 **저장** 단추를 클릭합니다. 실패한 경우 에이전트 설치 시 구성된 Workday 자격 증명과 AD 자격 증명이 유효한지 재차 확인하세요.

![Azure portal](./media/workday-inbound-tutorial/wd_1.png)

### <a name="part-2-configure-attribute-mappings"></a>2부: 특성 매핑 구성 

이 섹션에서는 사용자 데이터가 Workday에서 Active Directory로 흐르는 방식을 구성하겠습니다.

1.  **매핑** 아래의 프로비전 탭에서 **Workday 작업자를 온-프레미스와 동기화**를 클릭합니다.

2.  **원본 개체 범위** 필드에서 특성 기반 필터 집합을 정의하여 AD 프로비전 범위에 포함할 Workday 사용자 집합을 선택할 수 있습니다. 기본 범위는 "Workday의 모든 사용자"입니다. 예제 필터:

   * 예제: 작업자 ID가 1000000-2000000 사이인 사용자를 범위에 포함

      * 특성: WorkerID

      * 연산자: REGEX 일치

      * 값: (1[0-9][0-9][0-9][0-9][0-9][0-9])

   * 예제: 정규직 직원만 포함하고 비정규직 직원은 포함하지 않음 

      * 특성: EmployeeID

      * 연산자: NULL이 아님

3.  **대상 개체 작업** 필드에서 어떤 작업을 Active Directory에서 수행할 수 있도록 허용할 것인지 전역적으로 필터링할 수 있습니다. **만들기** 및 **업데이트**가 가장 일반적입니다.

4.  **특성 매핑** 섹션에서 개별 Workday 특성이 Active Directory 특성에 매핑되는 방식을 정의할 수 있습니다.

5. 기존 특성 매핑을 클릭하여 업데이트하거나 화면 맨 아래에서 **새 매핑 추가**를 클릭하여 새 매핑을 추가합니다. 개별 특성 매핑은 다음 속성을 지원합니다.

      * **매핑 유형**

         * **직접** – Workday 특성 값을 변경하지 않고 AD 특성에 씁니다.

         * **상수** - AD 특성에 고정적인 상수 문자열 값을 씁니다.

         * **식** – 하나 이상의 Workday 특성에 따라 AD 특성에 사용자 지정 값을 쓸 수 있습니다. [자세한 내용은 식에 대한 이 문서를 참조하세요](../manage-apps/functions-for-customizing-application-data.md).

      * **원본 특성** - Workday의 사용자 특성입니다. 원하는 특성이 없는 경우 [Workday 사용자 특성 목록 사용자 지정](#customizing-the-list-of-workday-user-attributes)을 참조하세요.

      * **기본값** – 선택 사항입니다. 원본 특성의 값이 비어 있는 경우 매핑에서 이 값을 대신 씁니다.
            이 값을 비워두는 것이 가장 일반적인 구성입니다.

      * **대상 특성** – Active의 사용자 특성입니다.

      * **이 특성을 사용하여 개체 일치** – Workday와 Active Directory 간에 사용자를 고유하게 식별하는 데 이 매핑을 사용할지 여부를 나타냅니다. 일반적으로 Workday의 작업자 ID 필드에서 설정하며, 대개 Active Directory의 직원 ID 특성 중 하나에 매핑됩니다.

      * **일치 우선 순위** – 여러 일치 특성을 설정할 수 있습니다. 일치 특성이 여러 개 있으면 이 필드에 정의된 순서대로 평가됩니다. 일치 항목이 발견되는 즉시 더 이상 일치 특성을 평가하지 않습니다.

      * **이 매핑 적용**
       
         * **항상** – 사용자 만들기 및 업데이트 작업 시 이 매핑을 적용합니다.

         * **만들기 작업 시에만** - 사용자 만들기 작업 시에만 이 매핑을 적용합니다.

6. 매핑을 저장하려면 특성 매핑 섹션 맨 위에서 **저장**을 클릭합니다.

![Azure portal](./media/workday-inbound-tutorial/WD_2.PNG)

**아래는 몇 가지 일반적인 식을 사용한 Workday와 Active Directory 간의 특성 매핑을 보여주는 예입니다.**

-   parentDistinguishedName 특성에 매핑되는 식을 사용하여 하나 이상의 Workday 원본 특성에 따라 다른 OU에 사용자를 프로비전할 수 있습니다. 이 예제에서는 도시에 따라 사용자를 다른 OU에 배치합니다.

-   도메인 접미사가 있는 Workday 사용자 ID를 연결하여 Active Directory의 userPrincipalName 특성을 생성합니다.

-   [식 작성에 대한 설명서는 여기에 있습니다](../manage-apps/functions-for-customizing-application-data.md). 여기에는 특수 문자를 제거하는 방법에 대한 예제가 포함되어 있습니다.

  
| WORKDAY 특성 | ACTIVE DIRECTORY 특성 |  ID 일치 여부 | 만들기/업데이트 |
| ---------- | ---------- | ---------- | ---------- |
| **WorkerID**  |  EmployeeID | **예** | 만들기 작업 시에만 기록 |
| **UserID**    |  cn    |   |   만들기 작업 시에만 기록 |
| **조인(“@”, [UserID], “contoso.com”)**   | userPrincipalName     |     | 만들기 작업 시에만 기록 
| **Replace(Mid(Replace(\[UserID\], , "(\[\\\\/\\\\\\\\\\\\\[\\\\\]\\\\:\\\\;\\\\|\\\\=\\\\,\\\\+\\\\\*\\\\?\\\\&lt;\\\\&gt;\])", , "", , ), 1, 20), , "([\\\\.)\*\$](file:///\\.)*$)", , "", , )**      |    sAMAccountName            |     |         만들기 작업 시에만 기록 |
| **Switch(\[Active\], , "0", "True", "1", "False")** |  accountDisabled      |     | 만들기 + 업데이트 |
| **FirstName**   | givenName       |     |    만들기 + 업데이트 |
| **LastName**   |   sn   |     |  만들기 + 업데이트 |
| **PreferredNameData**  |  displayName |     |   만들기 + 업데이트 |
| **Company**         | company   |     |  만들기 + 업데이트 |
| **SupervisoryOrganization**  | department  |     |  만들기 + 업데이트 |
| **ManagerReference**   | manager  |     |  만들기 + 업데이트 |
| **BusinessTitle**   |  title     |     |  만들기 + 업데이트 | 
| **AddressLineData**    |  streetAddress  |     |   만들기 + 업데이트 |
| **Municipality**   |   l   |     | 만들기 + 업데이트 |
| **CountryReferenceTwoLetter**      |   co |     |   만들기 + 업데이트 |
| **CountryReferenceTwoLetter**    |  C  |     |         만들기 + 업데이트 |
| **CountryRegionReference** |  st     |     | 만들기 + 업데이트 |
| **WorkSpaceReference** | physicalDeliveryOfficeName    |     |  만들기 + 업데이트 |
| **PostalCode**  |   postalCode  |     | 만들기 + 업데이트 |
| **PrimaryWorkTelephone**  |  telephoneNumber   |     | 만들기 + 업데이트 |
| **Fax**      | facsimileTelephoneNumber     |     |    만들기 + 업데이트 |
| **Mobile**  |    mobile       |     |       만들기 + 업데이트 |
| **LocalReference** |  preferredLanguage  |     |  만들기 + 업데이트 |                                               
| **Switch(\[Municipality\], "OU=Standard Users,OU=Users,OU=Default,OU=Locations,DC=contoso,DC=com", "Dallas", "OU=Standard Users,OU=Users,OU=Dallas,OU=Locations,DC=contoso,DC=com", "Austin", "OU=Standard Users,OU=Users,OU=Austin,OU=Locations,DC=contoso,DC=com", "Seattle", "OU=Standard Users,OU=Users,OU=Seattle,OU=Locations,DC=contoso,DC=com", “London", "OU=Standard Users,OU=Users,OU=London,OU=Locations,DC=contoso,DC=com")**  | parentDistinguishedName     |     |  만들기 + 업데이트 |
  


### <a name="part-4-start-the-service"></a>4부. 서비스 시작
1-3부를 완료했으면 Azure Portal에서 프로비전 서비스를 다시 시작할 수 있습니다.

1.  **프로비전** 탭에서 **프로비전 상태**를 **켜기**로 설정합니다.

2. **저장**을 클릭합니다.

3. 그러면 초기 동기화가 시작되며, Workday에 있는 사용자 수에 따라 동기화에 걸리는 시간이 달라질 수 있습니다.

4. 언제든지 Azure Portal에서 **감사 로그** 탭을 확인하여 프로비전 서비스에서 수행한 작업을 확인합니다. 감사 로그는 Workday에서 어떤 사용자를 읽은 후 Active Directory에 추가 또는 업데이트되는지와 같은 프로비전 서비스에서 수행한 모든 개별 동기화 이벤트를 나열합니다. **[감사 로그를 읽는 방법에 대한 자세한 지침은 프로비전 보고 가이드 참조](../manage-apps/check-status-user-account-provisioning.md)**

1.  새로운 오류나 경고에 대해 에이전트를 호스팅하는 Windows Server 컴퓨터에서 [Windows 이벤트 로그](https://technet.microsoft.com/library/cc722404(v=ws.11).aspx)를 확인합니다. 이러한 이벤트는 서버에서 **Eventvwr.msc**를 시작하고 **Windows 로그 > 응용 프로그램**을 선택하여 볼 수 있습니다. 모든 프로비전 관련 메시지는 원본 **AADSyncAgent** 아래에서 로깅됩니다.

6. 작업이 완료되면 아래와 같이 **프로비전** 탭에 감사 요약 보고서가 작성됩니다.

![Azure portal](./media/workday-inbound-tutorial/WD_3.PNG)


## <a name="configuring-user-provisioning-to-azure-active-directory"></a>Azure Active Directory로 사용자 프로비전 구성
Azure Active Directory로 프로비전을 구성하는 방법은 프로비전 요구 사항에 따라 달라지며, 아래 표에 자세히 설명되어 있습니다.

| 시나리오 | 해결 방법 |
| -------- | -------- |
| **사용자를 Active Directory 및 Azure AD에 프로비전 해야 함** | **[AAD Connect](../hybrid/whatis-hybrid-identity.md)** 사용 |
| **사용자를 Active Directory에만 프로비전 해야 함** | **[AAD Connect](../hybrid/whatis-hybrid-identity.md)** 사용 |
| **사용자를 Azure AD에만(클라우드에만) 프로비전 해야 함** | 앱 갤러리의 **Workday에서 Azure Active Directory로 프로비전** 앱 사용 |

Azure AD Connect 설정에 대한 자세한 지침은 [Azure AD Connect 설명서](../hybrid/whatis-hybrid-identity.md)를 참조하세요.

다음 섹션에서는 클라우드 전용 사용자를 프로비전하기 위해 Workday와 Azure AD 간 연결을 설정합니다.

> [!IMPORTANT]
> Azure AD에만 프로비전해야 하고 온-프레미스 Active Directory에는 프로비전하면 안 되는 클라우드 전용 사용자가 있는 경우 아래 절차만 수행합니다.

### <a name="part-1-adding-the-azure-ad-provisioning-connector-app-and-creating-the-connection-to-workday"></a>1부: Azure AD 프로비전 커넥터 앱 추가 및 Workday에 대한 연결 만들기

**클라우드 전용 사용자에 대한 Workday-Azure Active Directory 프로비전을 구성하려면:**

1.  <https://portal.azure.com>로 이동합니다.

2.  왼쪽 탐색 모음에서 **Azure Active Directory**를 선택합니다.

3.  **엔터프라이즈 응용 프로그램**, **모든 응용 프로그램**을 차례로 선택합니다.

4.  **응용 프로그램 추가**를 선택한 후 **모두** 범주를 선택합니다.

5.  **Workday-Azure AD 프로비전**을 검색하고, 갤러리에서 해당 앱을 추가합니다.

6.  앱이 추가되고 앱 세부 정보 화면이 표시되면 **프로비전**을 선택합니다.

7.  **프로비전** **모드**를 **자동**으로 변경합니다.

8.  다음과 같이 **관리자 자격 증명** 섹션을 완료합니다.

   * **관리 사용자 이름** – 테넌트 도메인 이름이 추가된 Workday 통합 시스템 계정의 사용자 이름을 입력합니다. 다음과 같은 형태여야 합니다. username@contoso4

   * **관리자 암호 –** Workday 통합 시스템 계정의 암호를 입력합니다.

   * **테넌트 URL –** 해당 테넌트의 Workday 웹 서비스 엔드포인트에 대한 URL을 입력합니다. https://wd3-impl-services1.workday.com/ccx/service/contoso4/Human_Resources와 같은 형태여야 하며, 여기서 contoso4를 올바른 테넌트 이름으로 바꾸고 wd3-impl을 올바른 환경 문자열로 바꾸면 됩니다. 이 URL을 알 수 없는 경우 Workday 통합 파트너와 협력하거나 지원 담당자에게 문의하여 사용할 올바른 URL을 확인하세요.

   * **알림 이메일 –** 이메일 주소를 입력하고 "오류가 발생하면 이메일 보내기" 확인란을 선택합니다.

   * **연결 테스트** 단추를 클릭합니다.

   * 연결 테스트가 성공하면 맨 위에서 **저장** 단추를 클릭합니다. 연결 테스트가 실패하면 Workday에서 Workday URL 및 자격 증명이 유효한지 다시 확인합니다.

### <a name="part-2-configure-attribute-mappings"></a>2부: 특성 매핑 구성 

이 섹션에서는 클라우드 전용 사용자의 사용자 데이터가 Workday에서 Azure Active Directory로 흐르는 방식을 구성하겠습니다.

1. **매핑** 아래의 프로비전 탭에서 **Azure AD에 작업자 동기화**를 클릭합니다.

2. **원본 개체 범위** 필드에서 특성 기반 필터 집합을 정의하여 Azure AD 프로비전 범위에 포함할 Workday 사용자 집합을 선택할 수 있습니다. 기본 범위는 "Workday의 모든 사용자"입니다. 예제 필터:

   * 예제: 작업자 ID가 1000000-2000000 사이인 사용자를 범위에 포함

      * 특성: WorkerID

      * 연산자: REGEX 일치

      * 값: (1[0-9][0-9][0-9][0-9][0-9][0-9])

   * 예제: 비정규직 직원만 포함하고 정규직 직원은 포함하지 않음

      * 특성: ContingentID

      * 연산자: NULL이 아님

3. **대상 개체 작업** 필드에서 어떤 작업을 Azure AD에서 수행할 수 있도록 허용할 것인지 전역적으로 필터링할 수 있습니다. **만들기** 및 **업데이트**가 가장 일반적입니다.

4. **특성 매핑** 섹션에서 개별 Workday 특성이 Active Directory 특성에 매핑되는 방식을 정의할 수 있습니다.

5. 기존 특성 매핑을 클릭하여 업데이트하거나 화면 맨 아래에서 **새 매핑 추가**를 클릭하여 새 매핑을 추가합니다. 개별 특성 매핑은 다음 속성을 지원합니다.

   * **매핑 유형**

      * **직접** – Workday 특성 값을 변경하지 않고 AD 특성에 씁니다.

      * **상수** - AD 특성에 고정적인 상수 문자열 값을 씁니다.

      * **식** – 하나 이상의 Workday 특성에 따라 AD 특성에 사용자 지정 값을 쓸 수 있습니다. [자세한 내용은 식에 대한 이 문서를 참조하세요](../manage-apps/functions-for-customizing-application-data.md).

   * **원본 특성** - Workday의 사용자 특성입니다. 원하는 특성이 없는 경우 [Workday 사용자 특성 목록 사용자 지정](#customizing-the-list-of-workday-user-attributes)을 참조하세요.

   * **기본값** – 선택 사항입니다. 원본 특성의 값이 비어 있는 경우 매핑에서 이 값을 대신 씁니다.
            이 값을 비워두는 것이 가장 일반적인 구성입니다.

   * **대상 특성** – Azure AD의 사용자 특성입니다.

   * **이 특성을 사용하여 개체 일치** – Workday와 Azure AD 간에 사용자를 고유하게 식별하는 데 이 매핑을 사용할지 여부를 나타냅니다. 일반적으로 Workday의 작업자 ID 필드에서 설정하며, 대개 Azure AD의 직원 ID 특성(신규) 또는 확장 특성에 매핑됩니다.

   * **일치 우선 순위** – 여러 일치 특성을 설정할 수 있습니다. 일치 특성이 여러 개 있으면 이 필드에 정의된 순서대로 평가됩니다. 일치 항목이 발견되는 즉시 더 이상 일치 특성을 평가하지 않습니다.

   * **이 매핑 적용**

     * **항상** – 사용자 만들기 및 업데이트 작업 시 이 매핑을 적용합니다.

     * **만들기 작업 시에만** - 사용자 만들기 작업 시에만 이 매핑을 적용합니다.

6. 매핑을 저장하려면 특성 매핑 섹션 맨 위에서 **저장**을 클릭합니다.

### <a name="part-3-start-the-service"></a>3부. 서비스 시작
1-2부를 완료했으면 프로비전 서비스를 시작할 수 있습니다.

1. **프로비전** 탭에서 **프로비전 상태**를 **켜기**로 설정합니다.

2. **저장**을 클릭합니다.

3. 그러면 초기 동기화가 시작되며, Workday에 있는 사용자 수에 따라 동기화에 걸리는 시간이 달라질 수 있습니다.

4. 개별 동기화 이벤트는 **감사 로그** 탭에서 볼 수 있습니다. **[감사 로그를 읽는 방법에 대한 자세한 지침은 프로비전 보고 가이드 참조](../manage-apps/check-status-user-account-provisioning.md)**

5. 작업이 완료되면 아래와 같이 **프로비전** 탭에 감사 요약 보고서가 작성됩니다.

## <a name="configuring-writeback-of-email-addresses-to-workday"></a>Workday로 이메일 주소 쓰기 저장 구성
다음 지침에 따라 Azure Active Directory에서 Workday로 사용자 이메일 주소 쓰기 저장을 구성합니다.

### <a name="part-1-adding-the-provisioning-connector-app-and-creating-the-connection-to-workday"></a>1부: 프로비전 커넥터 앱 추가 및 Workday에 대한 연결 만들기

**Workday 쓰기 저장 커넥터를 구성하려면**

1. <https://portal.azure.com>으로 이동합니다.

2. 왼쪽 탐색 모음에서 **Azure Active Directory**를 선택합니다.

3. **엔터프라이즈 응용 프로그램**, **모든 응용 프로그램**을 차례로 선택합니다.

4. **응용 프로그램 추가**를 선택한 후 **모두** 범주를 선택합니다.

5. **Workday 쓰기 저장**을 검색하고, 갤러리에서 해당 앱을 추가합니다.

6. 앱이 추가되고 앱 세부 정보 화면이 표시되면 **프로비전**을 선택합니다.

7. **프로비전** **모드**를 **자동**으로 변경합니다.

8. 다음과 같이 **관리자 자격 증명** 섹션을 완료합니다.

   * **관리 사용자 이름** – 테넌트 도메인 이름이 추가된 Workday 통합 시스템 계정의 사용자 이름을 입력합니다. 다음과 같은 형태여야 합니다. username@contoso4

   * **관리자 암호 –** Workday 통합 시스템 계정의 암호를 입력합니다.

   * **테넌트 URL –** 해당 테넌트의 Workday 웹 서비스 엔드포인트에 대한 URL을 입력합니다. https://wd3-impl-services1.workday.com/ccx/service/contoso4/Human_Resources와 같은 형태여야 하며, 여기서 contoso4를 올바른 테넌트 이름으로 바꾸고 wd3-impl을 올바른 환경 문자열로 바꾸면 됩니다(필요한 경우).

   * **알림 이메일 –** 이메일 주소를 입력하고 "오류가 발생하면 이메일 보내기" 확인란을 선택합니다.

   * **연결 테스트** 단추를 클릭합니다. 연결 테스트가 성공하면 맨 위에서 **저장** 단추를 클릭합니다. 연결 테스트가 실패하면 Workday에서 Workday URL 및 자격 증명이 유효한지 다시 확인합니다.

### <a name="part-2-configure-attribute-mappings"></a>2부: 특성 매핑 구성 

이 섹션에서는 사용자 데이터가 Workday에서 Active Directory로 흐르는 방식을 구성하겠습니다.

1. **매핑** 아래의 프로비전 탭에서 **Azure AD 사용자를 Workday와 동기화**를 클릭합니다.

2. 선택 사항으로 **원본 개체 범위** 필드에서 Azure Active Directory의 사용자 집합 중 Workday에 이메일 주소를 기록할 사용자 집합을 필터링 할 수 있습니다. 기본 범위는 "Azure AD의 모든 사용자"입니다. 

3. **특성 매핑** 섹션에서 Workday 작업자 ID 또는 직원 ID가 저장된 Azure Active Directory의 특성을 나타내도록 일치하는 ID를 업데이트합니다. Workday 작업자 ID 또는 직원 ID를 Azure AD의 extensionAttribute1-15와 동기화한 후 Azure AD에서 이 특성을 사용하여 Workday에서 사용자를 다시 일치시키는 방법이 주로 사용됩니다. 

4. 매핑을 저장하려면 특성 매핑 섹션 맨 위에서 **저장**을 클릭합니다.

### <a name="part-3-start-the-service"></a>3부. 서비스 시작
1-2부를 완료했으면 프로비전 서비스를 시작할 수 있습니다.

1. **프로비전** 탭에서 **프로비전 상태**를 **켜기**로 설정합니다.

2. **저장**을 클릭합니다.

3. 그러면 초기 동기화가 시작되며, Workday에 있는 사용자 수에 따라 동기화에 걸리는 시간이 달라질 수 있습니다.

4. 개별 동기화 이벤트는 **감사 로그** 탭에서 볼 수 있습니다. **[감사 로그를 읽는 방법에 대한 자세한 지침은 프로비전 보고 가이드 참조](../manage-apps/check-status-user-account-provisioning.md)**

5. 작업이 완료되면 아래와 같이 **프로비전** 탭에 감사 요약 보고서가 작성됩니다.

## <a name="customizing-the-list-of-workday-user-attributes"></a>Workday 사용자 특성 목록 사용자 지정
Active Directory 및 Azure AD 둘 다에 대한 Workday 프로비저닝 앱에는 선택할 수 있는 Workday사용자 특성의 기본 목록이 포함되어 있습니다. 그러나 이러한 목록은 포괄적이지 않습니다. Workday는 Workday 테넌트에 고유할 수 있는 수백 개의 가능한 사용자 특성을 지원합니다. 

Azure AD 프로비저닝 서비스는 인사 API의[Get_Workers](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html) 작업에 노출되는 특성을 포함하도록 목록 또는 Workday 특성을 사용자 지정하는 기능을 지원합니다.

이를 수행하려면 [Workday Studio](https://community.workday.com/studio-download)를 사용하여 사용하려는 특성을 나타내는 XPath 식을 추출한 다음, Azure Portal에서 고급 특성 편집기를 사용하여 프로비저닝 구성에 추가해야 합니다.

**Workday 사용자 특성에 대한 XPath 식을 검색하려면**

1. [Workday Studio](https://community.workday.com/studio-download)를 다운로드하고 설치합니다. 설치 관리자에 액세스하려면 Workday 커뮤니티 계정이 필요합니다.

2. 이 URL에서 Workday Human_Resources WDSL 파일을 다운로드합니다. https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Human_Resources.wsdl

3. Workday Studio를 시작합니다.

4. 명령 모음에서 **Workday > 테스터에서 웹 서비스 테스트** 옵션을 선택합니다.

5. **외부**를 선택하고 2단계에서 다운로드한 Human_Resources WSDL 파일을 선택합니다.

    ![Workday Studio](./media/workday-inbound-tutorial/wdstudio1.png)

6. "IMPL-CC"를 실제 인스턴스 유형으로, "TENANT"를 실제 테넌트 이름으로 바꾸어 **위치** 필드를 `https://IMPL-CC.workday.com/ccx/service/TENANT/Human_Resources`로 설정합니다.

7. **작업**을 **Get_Workers**로 설정합니다.

8.  요청/응답 창 아래의 작은 **구성** 링크를 클릭하여 Workday 자격 증명을 설정합니다. **인증**을 선택하고 Workday 통합 시스템 계정의 사용자 이름 및 암호를 입력합니다. 사용자 이름 형식을 name@tenant로 지정하고 **WS-Security UsernameToken** 옵션을 선택된 상태로 둡니다.

    ![Workday Studio](./media/workday-inbound-tutorial/wdstudio2.png)

9. **확인**을 선택합니다.

10. **요청** 창에서 아래의 XML을 붙여 넣고 **Employee_ID**를 Workday 테넌트에 있는 실제 사용자의 직원 ID로 설정합니다. 추출하려는 특성으로 채워진 사용자를 선택합니다.

    ```
    <?xml version="1.0" encoding="UTF-8"?>
    <env:Envelope xmlns:env="http://schemas.xmlsoap.org/soap/envelope/" xmlns:xsd="https://www.w3.org/2001/XMLSchema">
      <env:Body>
        <wd:Get_Workers_Request xmlns:wd="urn:com.workday/bsvc" wd:version="v21.1">
          <wd:Request_References wd:Skip_Non_Existing_Instances="true">
            <wd:Worker_Reference>
              <wd:ID wd:type="Employee_ID">21008</wd:ID>
            </wd:Worker_Reference>
          </wd:Request_References>
          <wd:Response_Group>
            <wd:Include_Reference>true</wd:Include_Reference>
            <wd:Include_Personal_Information>true</wd:Include_Personal_Information>
            <wd:Include_Employment_Information>true</wd:Include_Employment_Information>
            <wd:Include_Management_Chain_Data>true</wd:Include_Management_Chain_Data>
            <wd:Include_Organizations>true</wd:Include_Organizations>
            <wd:Include_Reference>true</wd:Include_Reference>
            <wd:Include_Transaction_Log_Data>true</wd:Include_Transaction_Log_Data>
            <wd:Include_Photo>true</wd:Include_Photo>
            <wd:Include_User_Account>true</wd:Include_User_Account>
            <wd:Include_Roles>true</wd:Include_Roles>
          </wd:Response_Group>
        </wd:Get_Workers_Request>
      </env:Body>
    </env:Envelope>
    ```
 
11. **요청 보내기**(녹색 화살표)를 클릭하여 명령을 실행합니다. 성공한 경우, **응답** 창에 응답이 표시됩니다. 응답에서 입력한 사용자 ID의 데이터를 확인하여 오류가 없는지 검토합니다.

12. 성공한 경우 **응답** 창의 XML을 복사한 후 XML 파일로 저장합니다.

13. Workday Studio의 명령 모음에서 **파일 > 파일 열기...** 를 선택하고 저장한 XML 파일을 엽니다. 그러면 Workday Studio XML 편집기에서 열립니다.

    ![Workday Studio](./media/workday-inbound-tutorial/wdstudio3.png)

14. 파일 트리에서 **/env: Envelope > env: Body > wd:Get_Workers_Response > wd:Response_Data > wd: Worker**를 탐색하여 사용자의 데이터를 찾습니다. 

15. **wd: Worker** 아래에서 추가하려는 특성을 찾은 후 선택합니다.

16. **문서 경로** 필드에서 선택한 특성에 대한 XPath 식을 복사합니다.

1. 복사한 식에서 **/env:Envelope/env:Body/wd:Get_Workers_Response/wd:Response_Data/** 접두사를 제거합니다.

18. 복사한 식의 마지막 항목이 노드인 경우(예: “/wd: Birth_Date”) **/text()** 를 식의 끝에 추가합니다. 마지막 항목이 특성(예: “/@wd: type”)인 경우에는 이 작업이 필요하지 않습니다.

19. 결과는 `wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Birth_Date/text()`와 같아야 합니다. 이 내용을 Azure Portal에 복사해야 합니다.


**프로비저닝 구성에 사용자 지정 Workday 사용자 특성을 추가하려면**

1. [Azure Portal](https://portal.azure.com)을 시작하고 이 자습서 앞부분에 설명된 것처럼 Workday 프로비저닝 응용 프로그램의 프로비저닝 섹션으로 이동합니다.

2. **프로비전 상태**를 **해제**로 설정하고 **저장**을 선택합니다. 이렇게 하면 준비가 되었을 때만 변경 내용을 적용할 수 있게 됩니다.

3. **매핑**에서 **온-프레미스에 관리자 동기화**(또는 **Azure AD에 작업자 동기화**)를 선택합니다.

4. 다음 화면 아래로 스크롤하여 **고급 옵션 표시**를 선택합니다.

5. **Workday에 대한 특성 목록 편집**을 선택합니다.

    ![Workday Studio](./media/workday-inbound-tutorial/wdstudio_aad1.png)

6. 특성 목록에서 입력 필드가 있는 맨 아래로 스크롤합니다.

7. **이름**으로 해당 특성의 표시 이름을 입력합니다.

8. **형식**에 대해 특성에 해당하는 형식(**문자열**이 가장 일반적임)을 선택합니다.

9. **API 식**에 대해 Workday Studio에서 복사한 XPath 식을 입력합니다. 예제: `wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Birth_Date/text()`

10. **특성 추가**를 선택합니다.

    ![Workday Studio](./media/workday-inbound-tutorial/wdstudio_aad2.png)

11. 위의 **저장**을 선택하고 대화 상자에서 **예**를 선택합니다. 특성 매핑 화면이 계속 열려 있으면 닫습니다.

12. 주 **프로비전** 탭으로 돌아가 **온-프레미스에 작업자 동기화**(또는 **Azure AD에 작업자 동기화**)를 선택합니다.

13. **새 매핑 추가**를 선택합니다.

14. 이제 새 사용자 특성이 **원본 특성** 목록에 표시됩니다.

15. 필요에 따라 새 사용자 특성에 대한 매핑을 추가합니다.

16. 완료되면 **프로비전 상태**를 다시 **설정**으로 지정하고 저장합니다.

## <a name="known-issues"></a>알려진 문제

* 온-프레미스 Active Directory의 thumbnailPhoto 사용자 특성에 데이터를 쓰는 것은 현재 지원되지 않습니다.

* "Workday-Azure AD" 커넥터는 현재 AAD 커넥터를 사용하도록 설정된 Azure AD 테넌트에서 지원되지 않습니다.  

## <a name="managing-personal-data"></a>개인 데이터 관리

Active Directory에 대한 Workday 프로비전 솔루션에는 도메인에 가입된 서버에 동기화 에이전트를 설치해야 하며, 이 에이전트는 개인 식별 정보를 포함할 수 있는 로그를 Windows 이벤트 로그에 만듭니다.

## <a name="next-steps"></a>다음 단계

* [프로비전 활동에 대한 로그를 검토하고 보고서를 받아 보는 방법을 살펴봅니다](../manage-apps/check-status-user-account-provisioning.md).
* [Workday와 Azure Active Directory 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.](workday-tutorial.md)
* [Azure Active Directory와 다른 SaaS 응용 프로그램을 통합하는 방법을 알아봅니다.](tutorial-list.md)

