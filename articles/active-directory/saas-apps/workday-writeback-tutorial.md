---
title: '자습서: Azure Active Directory에서 Workday 쓰기 저장 구성 | Microsoft Docs'
description: Azure AD에서 Workday로 특성 쓰기 저장을 구성하는 방법을 알아봅니다.
services: active-directory
author: cmmdesai
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.topic: tutorial
ms.workload: identity
ms.date: 10/14/2020
ms.author: chmutali
ms.openlocfilehash: c65fddcc90b25f70759fb038a72dad0facfa99a9
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/07/2020
ms.locfileid: "94359734"
---
# <a name="tutorial-configure-attribute-writeback-from-azure-ad-to-workday"></a>자습서: Azure AD에서 Workday로 특성 쓰기 저장 구성
이 자습서에서는 Azure AD에서 Workday로 특성을 쓰기 저장하기 위해 수행해야 하는 단계를 보여 줍니다. Workday 쓰기 저장 프로비저닝 앱은 값을 다음 Workday 특성에 할당하도록 지원합니다.
* 회사 이메일 
* Workday 사용자 이름
* 회사 유선 전화 번호(국가 번호, 지역 번호, 전화 번호 및 내선 번호 포함)
* 회사 유선 전화 번호 기본 플래그
* 회사 휴대폰 번호(국가 번호, 지역 번호, 전화 번호 포함)
* 회사 휴대폰 기본 플래그

## <a name="overview"></a>개요

[Workday에서 온-프레미스 AD로](workday-inbound-tutorial.md) 프로비저닝 앱 또는 [Workday에서 Azure AD로](workday-inbound-cloud-only-tutorial.md) 프로비저닝 앱을 사용하여 인바운드 프로비저닝 통합이 설정되면 필요에 따라 회사 이메일 및 전화 번호와 같은 연락처 정보를 Workday에 쓰도록 Workday 쓰기 저장 앱을 구성할 수 있습니다. 

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>이 사용자 프로비전 솔루션에 가장 적합한 사용자

이 Workday 쓰기 저장 사용자 프로비저닝 솔루션이 적합한 조직은 다음과 같습니다.

* IT에서 관리하는 신뢰할 수 있는 특성(예: 이메일 주소, 사용자 이름 및 전화 번호)을 Workday에 쓰기 저장하려는 Microsoft 365를 사용하는 조직

## <a name="configure-integration-system-user-in-workday"></a>Workday에서 통합 시스템 사용자 구성

작업자 데이터를 검색할 수 있는 권한이 있는 Workday 통합 시스템 사용자 계정을 만들려면 [통합 시스템 사용자 구성](workday-inbound-tutorial.md#configure-integration-system-user-in-workday) 섹션을 참조하세요. 

## <a name="configuring-azure-ad-attribute-writeback-to-workday"></a>Workday에 Azure AD 특성 쓰기 저장 구성

다음 지침에 따라 Azure Active Directory에서 Workday로 사용자 이메일 주소 및 사용자 이름 쓰기 저장을 구성합니다.

* [쓰기 저장 커넥터 앱 추가 및 Workday에 대한 연결 만들기](#part-1-adding-the-writeback-connector-app-and-creating-the-connection-to-workday)
* [쓰기 저장 특성 매핑 구성](#part-2-configure-writeback-attribute-mappings)
* [사용자 프로비저닝 사용 및 시작](#enable-and-launch-user-provisioning)

### <a name="part-1-adding-the-writeback-connector-app-and-creating-the-connection-to-workday"></a>1부: 쓰기 저장 커넥터 앱 추가 및 Workday에 대한 연결 만들기

**Workday 쓰기 저장 커넥터를 구성하려면**

1. [https://editor.swagger.io](<https://portal.azure.com>) 로 이동합니다.

2. Azure Portal에서 **Azure Active Directory** 를 검색하고 선택합니다.

3. **엔터프라이즈 애플리케이션**, **모든 애플리케이션** 을 차례로 선택합니다.

4. **애플리케이션 추가** 를 선택한 후 **모두** 범주를 선택합니다.

5. **Workday 쓰기 저장** 을 검색하고, 갤러리에서 해당 앱을 추가합니다.

6. 앱이 추가되고 앱 세부 정보 화면이 표시되면 **프로비저닝** 을 선택합니다.

7. **프로비저닝** **모드** 를 **자동** 으로 변경합니다.

8. 다음과 같이 **관리자 자격 증명** 섹션을 완료합니다.

   * **관리 사용자 이름** – 테넌트 도메인 이름이 추가된 Workday 통합 시스템 계정의 사용자 이름을 입력합니다. *username\@contoso4* 와 비슷합니다.

   * **관리자 암호 –** Workday 통합 시스템 계정의 암호를 입력합니다.

   * **테넌트 URL –** 해당 테넌트의 Workday 웹 서비스 엔드포인트에 대한 URL을 입력합니다. 이 값은 `https://wd3-impl-services1.workday.com/ccx/service/contoso4/Human_Resources`와 같은 형태여야 하며, 여기서 *contoso4* 를 올바른 테넌트 이름으로 바꾸고 *wd3-impl* 을 올바른 환경 문자열로 바꾸면 됩니다(필요한 경우).

   * **알림 메일 –** 이메일 주소를 입력하고 "오류가 발생하면 이메일 보내기" 확인란을 선택합니다.

   * **연결 테스트** 단추를 클릭합니다. 연결 테스트가 성공하면 맨 위에서 **저장** 단추를 클릭합니다. 연결 테스트가 실패하면 Workday에서 Workday URL 및 자격 증명이 유효한지 다시 확인합니다.

### <a name="part-2-configure-writeback-attribute-mappings"></a>2부: 쓰기 저장 특성 매핑 구성

이 섹션에서는 Azure AD에서 Workday로 쓰기 저장 특성의 흐름 방식을 구성합니다. 

1. [프로비저닝] 탭의 **매핑** 아래에서 매핑 이름을 클릭합니다.

2. **원본 개체 범위** 필드에서 필요에 따라 필터링할 수 있으며, Azure Active Directory의 사용자 세트가 쓰기 저장에 포함되어야 합니다. 기본 범위는 "Azure AD의 모든 사용자"입니다.

3. **특성 매핑** 섹션에서 Workday 작업자 ID 또는 직원 ID가 저장된 Azure Active Directory의 특성을 나타내도록 일치하는 ID를 업데이트합니다. Workday 작업자 ID 또는 직원 ID를 Azure AD의 extensionAttribute1-15와 동기화한 후 Azure AD에서 이 특성을 사용하여 Workday에서 사용자를 다시 일치시키는 방법이 주로 사용됩니다.

4. 일반적으로 Azure AD *userPrincipalName* 특성을 Workday *UserID* 특성에 매핑하고 Azure AD *mail* 특성을 Workday *EmailAddress* 특성에 매핑합니다. 

     >[!div class="mx-imgBorder"]
     >![Azure Portal](./media/workday-inbound-tutorial/workday-writeback-mapping.png)

5. 아래 공유된 지침을 사용하여 전화 번호 특성 값을 Azure AD에서 Workday로 매핑합니다. 

     | Workday 전화 번호 특성 | 필요한 값 | 매핑 지침 |
     |-------------------------|----------------|------------------|
     | WorkphoneLandlineIsPrimary | true/false | 출력이 "true" 또는 "false" 문자열 값인 상수 또는 식 매핑입니다. |
     | WorkphoneLandlineCountryCodeName | [3자 ISO 3166-1 국가 번호](https://en.wikipedia.org/wiki/ISO_3166-1_alpha-3) | 출력이 3자 국가 번호인 상수 또는 식 매핑입니다. |
     | WorkphoneLandlineCountryCodeNumber | [국제 국가 통화 코드](https://en.wikipedia.org/wiki/List_of_country_calling_codes) | 출력이 유효한 국가 번호(+ 기호 없음)인 상수 또는 식 매핑입니다. |
     | WorkphoneLandlineNumber | 전체 전화 번호(지역 번호 포함) | *telephoneNumber* 특성에 매핑됩니다. 정규식을 사용하여 공백, 대괄호 및 국가 번호를 제거합니다. 아래 예제를 참조하세요. |
     | WorkphoneLandlineExtension | 내선 번호 | *telephoneNumber* 에 내선 번호가 포함되어 있는 경우 정규식을 사용하여 값을 추출합니다. |
     | WorkphoneMobileIsPrimary | true/false | 출력이 "true" 또는 "false" 문자열 값인 상수 매핑 또는 식 매핑입니다. |
     | WorkphoneMobileCountryCodeName | [3자 ISO 3166-1 국가 번호](https://en.wikipedia.org/wiki/ISO_3166-1_alpha-3) | 출력이 3자 국가 번호인 상수 또는 식 매핑입니다. |
     | WorkphoneMobileCountryCodeNumber | [국제 국가 통화 코드](https://en.wikipedia.org/wiki/List_of_country_calling_codes) | 출력이 유효한 국가 번호(+ 기호 없음)인 상수 또는 식 매핑입니다. |
     | WorkphoneMobileNumber | 전체 전화 번호(지역 번호 포함) | *mobile* 특성에 매핑됩니다. 정규식을 사용하여 공백, 대괄호 및 국가 번호를 제거합니다. 아래 예제를 참조하세요. |

     > [!NOTE]
     > Change_Work_Contact Workday 웹 서비스를 호출하는 경우 Azure AD에서 보내는 상수 값은 다음과 같습니다. <br>
     > * **Communication_Usage_Type_ID** 가 "WORK" 상수 문자열로 설정됩니다. <br>
     > * **Phone_Device_Type_ID** 가 휴대폰 번호의 경우 "Mobile" 상수 문자열로 설정되고, 유선 전화 번호의 경우 "Landline"으로 설정됩니다. <br>
     > 
     > Workday 테넌트에서 다른 Type_ID를 사용하는 경우 쓰기 저장 오류가 발생합니다. 이러한 오류를 방지하기 위해 Workday **참조 ID 유지 관리** 작업을 사용하고, Azure AD에서 사용하는 값과 일치하도록 Type_ID를 업데이트할 수 있습니다. <br>
     >  

     **참조 정규식 - 예제 1**

     Azure AD의 전화 번호가 SSPR(셀프 서비스 암호 재설정)에 필요한 형식으로 설정된 경우 아래 정규식을 사용합니다. <br>
     예: 전화 번호 값이 +1 1112223333이면 정규식에서 1112223333을 출력합니다.

     ```C#
     Replace([telephoneNumber], , "\\+(?<isdCode>\\d* )(?<phoneNumber>\\d{10})", , "${phoneNumber}", , )
     ```

     **참조 정규식 - 예제 2**

     Azure AD의 전화 번호가 (XXX) XXX-XXXX 형식으로 설정된 경우 아래 정규식을 사용합니다. <br>
     예: 전화 번호 값이 (111) 222-3333이면 정규식에서 1112223333을 출력합니다.

     ```C#
     Replace([mobile], , "[()\\s-]+", , "", , )
     ```

6. 매핑을 저장하려면 특성 매핑 섹션 맨 위에서 **저장** 을 클릭합니다.

## <a name="enable-and-launch-user-provisioning"></a>사용자 프로비저닝 사용 및 시작

Workday 프로비전 앱 구성이 완료되면 Azure Portal에서 프로비전 서비스를 켤 수 있습니다.

> [!TIP]
> 기본적으로 프로비전 서비스를 켜면 범위 내 모든 사용자의 프로비전 작업이 시작됩니다. 매핑 오류 또는 Workday 데이터 문제가 있는 경우 프로비전 작업이 실패하고 격리 상태로 전환될 수 있습니다. 이를 방지하기 위해 **원본 개체 범위** 필터를 구성하고 모든 사용자의 전체 동기화를 시작하기 전에 몇몇 테스트 사용자로 특성 매핑을 테스트하는 것이 좋습니다. 매핑이 작동하고 원하는 결과를 제공하는지 확인한 후에는 필터를 제거하거나 점진적으로 더 많은 사용자를 포함하도록 해당 필터를 점진적으로 확장할 수 있습니다.

1. **프로비전** 탭에서 **프로비전 상태** 를 **켜기** 로 설정합니다.

1. **범위** 드롭다운에서 **모든 사용자 및 그룹 동기화** 를 선택합니다. 이 옵션을 사용하면 쓰기 저장 앱에서 **매핑** -> **원본 개체 범위** 아래에 정의된 범위 지정 규칙에 따라 모든 사용자의 매핑된 특성을 Azure AD에서 Workday로 쓰기 저장합니다. 

   > [!div class="mx-imgBorder"]
   > ![쓰기 저장 범위 선택](./media/sap-successfactors-inbound-provisioning/select-writeback-scope.png)

   > [!NOTE]
   > Workday 쓰기 저장 프로비저닝 앱은 **할당된 사용자 및 그룹만 동기화** 옵션을 지원하지 않습니다.
 

2. **저장** 을 클릭합니다.

3. 이 작업을 수행하면 초기 동기화가 시작되며, 이 경우 동기화하는 데 걸리는 시간이 원본 디렉터리의 사용자 수에 따라 달라질 수 있습니다. 진행률 표시줄을 확인하여 동기화 주기의 진행 상황을 추적할 수 있습니다. 

4. 언제든지 Azure Portal에서 **프로비저닝 로그** 탭을 확인하여 프로비저닝 서비스에서 수행한 작업을 확인합니다. 감사 로그에는 프로비저닝 서비스에서 수행하는 모든 개별 동기화 이벤트(예: 원본에서 가져와서 대상 애플리케이션으로 내보내는 사용자)가 나열됩니다.  

5. 초기 동기화가 완료되면 아래와 같이 **프로비저닝** 탭에 요약 보고서가 작성됩니다.

     > [!div class="mx-imgBorder"]
     > ![프로비저닝 진행률 표시줄](./media/sap-successfactors-inbound-provisioning/prov-progress-bar-stats.png)

## <a name="known-issues-and-limitations"></a>알려진 문제 및 제한 사항

* 쓰기 저장 앱은 **Communication_Usage_Type_ID** 및 **Phone_Device_Type_ID** 매개 변수에 대해 미리 정의된 값을 사용합니다. Workday 테넌트에서 다른 값을 이러한 특성에 사용하는 경우 쓰기 저장 작업이 실패합니다. 제안되는 해결 방법은 Workday에서 Type_ID를 업데이트하는 것입니다. 
* 쓰기 저장 앱이 보조 전화 번호를 업데이트하도록 구성된 경우 Workday에서 기존 보조 전화 번호를 대체하지 않습니다. 하나 이상의 보조 전화 번호를 작업자 레코드에 추가합니다. 이 동작에 대한 해결 방법은 없습니다. 


## <a name="next-steps"></a>다음 단계

* [프로비저닝 작업에 대한 로그를 검토하고 보고서를 받아보는 방법을 알아봅니다](../app-provisioning/check-status-user-account-provisioning.md).
* [Workday와 Azure Active Directory 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.](workday-tutorial.md)
* [Azure Active Directory와 다른 SaaS 애플리케이션을 통합하는 방법을 알아봅니다.](tutorial-list.md)
* [프로비저닝 구성을 내보내고 가져오는 방법을 알아봅니다.](../app-provisioning/export-import-provisioning-configuration.md)

