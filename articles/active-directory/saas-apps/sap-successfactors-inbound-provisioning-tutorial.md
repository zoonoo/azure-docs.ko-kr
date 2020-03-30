---
title: '자습서: Azure Active Directory에서 successFactors 인바운드 프로비저닝 구성 | 마이크로 소프트 문서'
description: SuccessFactors에서 인바운드 프로비저닝을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: cmmdesai
documentationcenter: na
manager: jodadzie
ms.assetid: 1ff90231-1312-463e-8949-7d976e433fc3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/05/2019
ms.author: chmutali
ms.openlocfilehash: d9317a68c8967fbe0728e8c47e59dd33367c6163
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249686"
---
# <a name="tutorial-configure-sap-successfactors-to-active-directory-user-provisioning-preview"></a>자습서: 활성 디렉터리 사용자 프로비저닝에 SAP SuccessFactors 구성(미리 보기)
이 자습서의 목적은 SuccessFactors 직원 중앙에서 Active Directory(AD) 및 Azure AD로 사용자를 프로비전하기 위해 수행해야 하는 단계를 표시하고, 선택적 전자 메일 주소 쓰기 백에서 SuccessFactors로 변환하는 것입니다. 이 통합은 공개 미리 보기에 있으며 SuccessFactors 직원 센트럴에서 [70개](../app-provisioning/sap-successfactors-attribute-reference.md) 이상의 사용자 특성을 검색할 수 있습니다.

>[!NOTE]
>SuccessFactors에서 프로비전하려는 사용자에게 온-프레미스 AD 계정과 선택적으로 Azure AD 계정이 필요한 경우 이 자습서를 사용합니다. SuccessFactors의 사용자에게 Azure AD 계정(클라우드 전용 사용자)만 필요한 경우 Azure AD 사용자 프로비저닝에 [SAP SuccessFactors 구성에](sap-successfactors-inbound-provisioning-cloud-only-tutorial.md) 대한 자습서를 참조하십시오. 


## <a name="overview"></a>개요

[Azure Active Directory 사용자 프로비전 서비스는](../app-provisioning/user-provisioning.md) 사용자의 ID 수명 주기를 관리하기 위해 [SuccessFactors 직원 중앙과](https://www.successfactors.com/products-services/core-hr-payroll/employee-central.html) 통합됩니다. 

Azure AD 사용자 프로비저닝 서비스에서 지원하는 SuccessFactors 사용자 프로비저닝 워크플로를 사용하면 다음과 같은 인적 자원 및 ID 수명 주기 관리 시나리오를 자동화할 수 있습니다.

* **새 직원 채용** - 새 직원이 SuccessFactors에 추가되면 사용자 계정이 Active Directory, Azure Active Directory 및 선택적으로 Office 365 및 [Azure AD에서 지원하는 기타 SaaS 응용 프로그램에서](../app-provisioning/user-provisioning.md)자동으로 만들어지며, SuccessFactors에 이메일 주소를 다시 기록합니다.

* **직원 특성 및 프로필 업데이트** - 직원 레코드가 SuccessFactors(예: 이름, 제목 또는 관리자)에서 업데이트되면 사용자 계정이 Active Directory, Azure Active Directory 및 선택적으로 Office 365 및 Azure [AD에서 지원하는 기타 SaaS 응용 프로그램에서](../app-provisioning/user-provisioning.md)자동으로 업데이트됩니다.

* **직원 종료** - SuccessFactors에서 직원이 종료되면 사용자 계정이 Active Directory, Azure Active Directory 및 선택적으로 Office 365 및 [Azure AD에서 지원하는 기타 SaaS 응용 프로그램에서](../app-provisioning/user-provisioning.md)자동으로 비활성화됩니다.

* **직원 재채용** - Employee가 SuccessFactors에서 다시 고용되면 이전 계정을 Active Directory, Azure Active Directory 및 선택적으로 Office 365 및 [Azure AD에서 지원하는 기타 SaaS 응용 프로그램에](../app-provisioning/user-provisioning.md)자동으로 다시 활성화하거나 다시 프로비전할 수 있습니다.

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>이 사용자 프로비전 솔루션에 가장 적합한 사용자

이 SuccessFactors 에서 Active Directory 사용자 프로비저닝 솔루션에 적합합니다.

* SuccessFactors 사용자 프로비저닝을 위한 사전 구축된 클라우드 기반 솔루션을 원하는 조직

* SuccessFactors에서 Active Directory로 직접 사용자 프로비전이 필요한 조직

* [성공 요인 직원 중앙 (EC)에서](https://www.successfactors.com/products-services/core-hr-payroll/employee-central.html) 얻은 데이터를 사용 하 여 사용자를 프로비저닝 해야 하는 조직

* [성공팩터 직원 중앙(EC)에서](https://www.successfactors.com/products-services/core-hr-payroll/employee-central.html) 검색된 변경 정보만기반으로 사용자를 하나 이상의 Active Directory 포리스트, 도메인 및 CU에 동기화해야 하는 조직

* 이메일에 Office 365를 사용하는 조직

## <a name="solution-architecture"></a>솔루션 아키텍처

이 섹션에서는 일반적인 하이브리드 환경을 위한 엔드투엔드 사용자 프로비전 솔루션 아키텍처에 대해 설명합니다. 두 가지의 관련된 흐름이 있습니다.

* **신뢰할 수 있는 HR 데이터 흐름 – SuccessFactors에서 온-프레미스 활성 디렉터리까지:** 이 흐름 작업자 이벤트(예: 새 채용, 이전, 종료)에서는 먼저 클라우드 SuccessFactors 직원 센트럴에서 발생한 다음 이벤트 데이터가 Azure AD 및 프로비저닝 에이전트를 통해 온-프레미스 활성 디렉터리로 흐릅니다. 이벤트에 따라 AD에서 만들기/업데이트/사용/사용 안 함 작업이 이루어질 수 있습니다.
* **이메일 쓰기 철회 흐름 – 온-프레미스 활성 디렉토리에서 SuccessFactors까지:** Active Directory에서 계정 만들기가 완료되면 Azure AD Connect 동기화를 통해 Azure AD와 동기화되고 전자 메일 특성을 SuccessFactors로 다시 쓸 수 있습니다.

  ![개요](./media/sap-successfactors-inbound-provisioning/sf2ad-overview.png)

### <a name="end-to-end-user-data-flow"></a>엔드투엔드 사용자 데이터 흐름

1. HR 팀은 SuccessFactors 직원 센트럴에서 근로자 트랜잭션(가입자/이동자/휴가자 또는 신규 고용/이전/종료)을 수행합니다.
2. Azure AD 프로비저닝 서비스는 SuccessFactors EC에서 예약된 ID 동기화를 실행하고 온-프레미스 Active Directory와 동기화하기 위해 처리해야 하는 변경 내용을 식별합니다.
3. Azure AD 프로비전 서비스는 AD 계정 생성/업데이트/활성화/비활성화 작업을 포함하는 요청 페이로드를 사용하여 온-프레미스 Azure AD Connect 프로비전 에이전트를 호출합니다.
4. Azure AD Connect 프로비전 에이전트는 서비스 계정을 사용하여 AD 계정 데이터를 추가/업데이트합니다.
5. Azure AD Connect Sync 엔진은 델타 동기화를 실행하여 AD에서 업데이트를 가져옵니다.
6. Active Directory 업데이트는 Azure Active Directory와 동기화됩니다.
7. [SuccessFactors 쓰기 백 앱이](sap-successfactors-writeback-tutorial.md) 구성된 경우 사용된 일치 특성에 따라 SuccessFactors에 전자 메일 특성을 다시 씁니다.

## <a name="planning-your-deployment"></a>배포 계획

SuccessFactors에서 AD로 클라우드 HR 기반 사용자 프로비전을 구성하려면 다음과 같은 다양한 측면을 다루는 상당한 계획이 필요합니다.
* Azure AD Connect 프로비전 에이전트 설정 
* 배포할 AD 사용자 프로비저닝 앱에 대한 SuccessFactors 수
* 일치하는 ID, 특성 매핑, 변환 및 범위 지정 필터

이러한 항목에 대한 포괄적인 지침은 [클라우드 HR 배포 계획을](../app-provisioning/plan-cloud-hr-provision.md) 참조하십시오. 

## <a name="configuring-successfactors-for-the-integration"></a>통합을 위한 성공 요소 구성

모든 SuccessFactors 프로비저닝 커넥터의 공통 요구 사항은 SuccessFactors OData API를 호출할 수 있는 올바른 권한이 있는 SuccessFactors 계정의 자격 증명이 필요하다는 것입니다. 이 섹션에서는 SuccessFactors에서 서비스 계정을 만들고 적절한 권한을 부여하는 단계를 설명합니다. 

* [SuccessFactors에서 API 사용자 계정 생성/식별](#createidentify-api-user-account-in-successfactors)
* [API 사용 권한 역할 만들기](#create-an-api-permissions-role)
* [API 사용자에 대한 권한 그룹 만들기](#create-a-permission-group-for-the-api-user)
* [권한 그룹에 권한 부여 역할 부여](#grant-permission-role-to-the-permission-group)

### <a name="createidentify-api-user-account-in-successfactors"></a>SuccessFactors에서 API 사용자 계정 생성/식별
SuccessFactors 관리자 팀 또는 구현 파트너와 협력하여 OData API를 호출하는 데 사용할 SuccessFactors에서 사용자 계정을 만들거나 식별합니다. Azure AD에서 프로비저닝 앱을 구성할 때 이 계정의 사용자 이름 및 암호 자격 증명이 필요합니다. 

### <a name="create-an-api-permissions-role"></a>API 사용 권한 역할 만들기

* 관리자 센터에 액세스할 수 있는 사용자 계정으로 SAP SuccessFactors에 로그인합니다.
* 사용 *권한 역할 관리를*검색한 다음 검색 결과에서 **권한 역할 관리를** 선택합니다.
  ![권한 역할 관리](./media/sap-successfactors-inbound-provisioning/manage-permission-roles.png)
* 권한 역할 목록에서 **새 만들기를 클릭합니다.**
  > [!div class="mx-imgBorder"]
  > ![새 권한 역할 만들기](./media/sap-successfactors-inbound-provisioning/create-new-permission-role-1.png)
* 새 사용 권한 역할에 대한 **역할 이름** 및 **설명을** 추가합니다. 이름과 설명은 역할이 API 사용 권한에 대한 것임을 나타내야 합니다.
  > [!div class="mx-imgBorder"]
  > ![권한 역할 세부 정보](./media/sap-successfactors-inbound-provisioning/permission-role-detail.png)
* 권한 설정에서 **권한...** **Manage Integration Tools** **관리자가 기본 인증을 통해 OData API에 액세스할 수 있도록 허용하는**확인란을 선택합니다.
  > [!div class="mx-imgBorder"]
  > ![통합 도구 관리](./media/sap-successfactors-inbound-provisioning/manage-integration-tools.png)
* 동일한 상자에서 아래로 스크롤하여 **직원 중앙 API를**선택합니다. ODATA API를 사용하여 읽고 ODATA API를 사용하여 편집하려면 아래와 같이 권한을 추가합니다. 성공 요소에 쓰기 시나리오에 대해 동일한 계정을 사용하려는 경우 편집 옵션을 선택합니다. 
  > [!div class="mx-imgBorder"]
  > ![쓰기 권한 읽기](./media/sap-successfactors-inbound-provisioning/odata-read-write-perm.png)

  >[!NOTE]
  >이 프로비저닝 앱에서 검색한 특성의 전체 목록은 [SuccessFactors 특성 참조를](../app-provisioning/sap-successfactors-attribute-reference.md) 참조하십시오.

* **완료**를 클릭합니다. **변경 내용 저장**을 클릭합니다.

### <a name="create-a-permission-group-for-the-api-user"></a>API 사용자에 대한 권한 그룹 만들기

* SuccessFactors 관리 센터에서 권한 *그룹 관리를*검색한 다음 검색 결과에서 **권한 그룹 관리를** 선택합니다.
  > [!div class="mx-imgBorder"]
  > ![권한 그룹 관리](./media/sap-successfactors-inbound-provisioning/manage-permission-groups.png)
* 권한 그룹 관리 창에서 **새 .**
  > [!div class="mx-imgBorder"]
  > ![새 그룹 추가](./media/sap-successfactors-inbound-provisioning/create-new-group.png)
* 새 그룹에 대한 그룹 이름을 추가합니다. 그룹 이름은 그룹이 API 사용자를 위한 것임을 나타내야 합니다.
  > [!div class="mx-imgBorder"]
  > ![권한 그룹 이름](./media/sap-successfactors-inbound-provisioning/permission-group-name.png)
* 그룹에 구성원을 추가합니다. 예를 들어 피플 풀 드롭다운 메뉴에서 **사용자 이름을** 선택한 다음 통합에 사용할 API 계정의 사용자 이름을 입력할 수 있습니다. 
  > [!div class="mx-imgBorder"]
  > ![그룹 멤버 추가](./media/sap-successfactors-inbound-provisioning/add-group-members.png)
* **완료를** 클릭하여 권한 그룹 만들기를 완료합니다.

### <a name="grant-permission-role-to-the-permission-group"></a>권한 그룹에 권한 부여 역할 부여

* SuccessFactors 관리 센터에서 *권한 관리 역할을*검색한 다음 검색 결과에서 권한 역할 **관리를** 선택합니다.
* 권한 **역할 목록에서**API 사용 권한에 대해 만든 역할을 선택합니다.
* **이 역할 부여에서...** **Add...**
* 드롭다운 메뉴에서 **권한 그룹을** 선택한 다음 **선택...을** 클릭하여 그룹 창을 열어 위에서 만든 그룹을 검색하고 선택합니다. 
  > [!div class="mx-imgBorder"]
  > ![권한 그룹 추가](./media/sap-successfactors-inbound-provisioning/add-permission-group.png)
* 권한 그룹에 대한 권한 역할 부여를 검토합니다. 
  > [!div class="mx-imgBorder"]
  > ![권한 역할 및 그룹 세부 정보](./media/sap-successfactors-inbound-provisioning/permission-role-group.png)
* **변경 내용 저장**을 클릭합니다.

## <a name="configuring-user-provisioning-from-successfactors-to-active-directory"></a>SuccessFactors에서 활성 디렉터리로 사용자 프로비저닝 구성

이 섹션에서는 통합 범위 내에서 SuccessFactors에서 각 Active Directory 도메인으로 사용자 계정 프로비저닝을 위한 단계를 제공합니다.

* [프로비저닝 커넥터 앱을 추가하고 프로비저닝 에이전트를 다운로드합니다.](#part-1-add-the-provisioning-connector-app-and-download-the-provisioning-agent)
* [온-프레미스 프로비전 에이전트 설치 및 구성](#part-2-install-and-configure-on-premises-provisioning-agents)
* [SuccessFactors 및 활성 디렉터리에 대한 연결 구성](#part-3-in-the-provisioning-app-configure-connectivity-to-successfactors-and-active-directory)
* [특성 매핑 구성](#part-4-configure-attribute-mappings)
* [사용자 프로비저닝 사용 및 시작](#enable-and-launch-user-provisioning)

### <a name="part-1-add-the-provisioning-connector-app-and-download-the-provisioning-agent"></a>Part 1: 프로비저닝 커넥터 앱을 추가하고 프로비저닝 에이전트를 다운로드합니다.

**Active Directory 프로비저닝에 SuccessFactors를 구성하려면 다음을 수행하십시오.**

1. [https://editor.swagger.io](<https://portal.azure.com>) 으로 이동합니다.

2. 왼쪽 탐색 모음에서 **Azure Active Directory**를 선택합니다.

3. **엔터프라이즈 애플리케이션**, **모든 애플리케이션**을 차례로 선택합니다.

4. **애플리케이션 추가**를 선택하고 **모두** 범주를 선택합니다.

5. 활성 **디렉터리 사용자 프로비저닝에 SuccessFactors를**검색하고 갤러리에서 해당 앱을 추가합니다.

6. 앱이 추가되고 앱 세부 정보 화면이 표시되면 **프로비전**을 선택합니다.

7. **프로비전** **모드**를 **자동**으로 변경합니다.

8. 프로비저닝 에이전트를 다운로드하려면 표시된 정보 배너를 클릭합니다. 
   > [!div class="mx-imgBorder"]
   > ![에이전트 다운로드](./media/sap-successfactors-inbound-provisioning/download-pa-agent.png "에이전트 화면 다운로드")


### <a name="part-2-install-and-configure-on-premises-provisioning-agents"></a>2부: 온-프레미스 프로비저닝 에이전트 설치 및 구성

온-프레미스에서 Active Directory에 프로비전 에이전트를 설치하려면 .NET 4.7.1+ 프레임워크와 원하는 Active Directory 도메인에 대한 네트워크 액세스 권한이 있는 서버에 프로비저닝 에이전트를 설치해야 합니다.

> [!TIP]
> [여기](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed)에 제공된 지침을 사용하여 서버에서 .NET Framework 버전을 확인할 수 있습니다.
> 서버에 .NET 4.7.1 이상이 설치되어 있지 않으면 [여기](https://support.microsoft.com/help/4033342/the-net-framework-4-7-1-offline-installer-for-windows)에서 다운로드할 수 있습니다.  

다운로드한 에이전트 설치 관리자를 서버 호스트로 전송하고 아래에 제공된 단계를 수행하여 에이전트 구성을 완료합니다.

1. 새 에이전트를 설치하려는 Windows 서버에 로그인합니다.

1. 프로비저닝 에이전트 설치 관리자를 시작하고 약관에 동의한 다음 **설치** 버튼을 클릭합니다.

   ![화면 설치](./media/workday-inbound-tutorial/pa_install_screen_1.png "화면 설치")
   
1. 설치가 완료되면 마법사가 시작되고 **Azure AD 연결** 화면이 표시됩니다. **인증** 단추를 클릭하여 Azure AD 인스턴스에 연결합니다.

   ![Azure AD 연결](./media/workday-inbound-tutorial/pa_install_screen_2.png "Azure AD 연결")
   
1. 글로벌 관리자 자격 증명을 사용하여 Azure AD 인스턴스에 대해 인증합니다.

   ![관리자 오트](./media/workday-inbound-tutorial/pa_install_screen_3.png "관리자 오트")

   > [!NOTE]
   > Azure AD 관리자 자격 증명은 Azure AD 테넌트에 연결하는 데만 사용됩니다. 에이전트는 자격 증명을 서버에 로컬로 저장하지 않습니다.

1. Azure AD의 인증에 성공하면 **Active Directory 연결** 화면이 표시됩니다. 이 단계에서는 AD 도메인 이름을 입력하고 **디렉터리 추가** 단추를 클릭합니다.

   ![디렉토리 추가](./media/workday-inbound-tutorial/pa_install_screen_4.png "디렉터리 추가")
  
1. 이제 AD 도메인에 연결하는 데 필요한 자격 증명을 입력하라는 메시지가 표시됩니다. 동일한 화면에서 **도메인 컨트롤러 우선 순위 선택**을 사용하여 에이전트가 프로비전 요청을 보내는 데 사용할 도메인 컨트롤러를 지정할 수 있습니다.

   ![도메인 자격 증명](./media/workday-inbound-tutorial/pa_install_screen_5.png)
   
1. 도메인을 구성한 후 설치 관리자는 구성된 도메인 목록을 표시합니다. 이 화면에서 5 및 6단계를 반복하여 도메인을 더 추가하거나 **다음**을 클릭하여 에이전트 등록을 진행할 수 있습니다.

   ![구성된 도메인](./media/workday-inbound-tutorial/pa_install_screen_6.png "구성된 도메인")

   > [!NOTE]
   > 여러 AD 도메인이 있는 경우(예: na.contoso.com, emea.contoso.com) 각 도메인을 목록에 개별적으로 추가하세요.
   > 부모 도메인(예: contoso.com)만 추가하는 것은 충분하지 않습니다. 각 자식 도메인을 에이전트에 등록해야 합니다.
   
1. 구성 세부 정보를 검토하고 **확인**을 클릭하여 에이전트를 등록합니다.
  
   ![화면 확인](./media/workday-inbound-tutorial/pa_install_screen_7.png "화면 확인")
   
1. 구성 마법사가 에이전트 등록의 진행률을 표시합니다.
  
   ![에이전트 등록](./media/workday-inbound-tutorial/pa_install_screen_8.png "에이전트 등록")
   
1. 에이전트 등록이 완료되면 **종료**를 클릭하여 마법사를 끝낼 수 있습니다.
  
   ![종료 화면](./media/workday-inbound-tutorial/pa_install_screen_9.png "종료 화면")
   
1. 에이전트 설치를 확인하고 “서비스” 스냅인을 열고 “Microsoft Azure AD Connect Provisioning Agent”라는 서비스를 찾아 해당 에이전트가 실행 중인지 확인합니다.
  
   ![Services](./media/workday-inbound-tutorial/services.png)

### <a name="part-3-in-the-provisioning-app-configure-connectivity-to-successfactors-and-active-directory"></a>3부: 프로비저닝 앱에서 SuccessFactors 및 Active Directory에 대한 연결을 구성합니다.
이 단계에서는 Azure 포털에서 SuccessFactors 및 Active Directory와의 연결을 설정합니다. 

1. Azure 포털에서 [1부에서](#part-1-add-the-provisioning-connector-app-and-download-the-provisioning-agent) 만든 활성 디렉터리 사용자 프로비저닝 앱으로 SuccessFactors로 돌아갑니다.

1. 다음과 같이 **관리자 자격 증명** 섹션을 완료합니다.

   * **관리자 사용자 이름** - 회사 ID가 추가된 SuccessFactors API 사용자 계정의 사용자 이름을 입력합니다. 그것은 형식을 가지고 : **사용자\@이름 companyID**

   * **관리자 암호 -** SuccessFactors API 사용자 계정의 암호를 입력합니다. 

   * **테넌트 URL –** SuccessFactors OData API 서비스 끝점의 이름을 입력합니다. http 또는 https 없이 서버의 호스트 이름만 입력합니다. 이 값은 다음과 같아야 합니다 **><.**

   * **Active Directory 포리스트 -** 에이전트에 등록된 Active Directory 도메인의 “이름”입니다. 드롭다운에서 프로비전을 위한 대상 도메인을 선택합니다. 이 값은 일반적으로 *contoso.com* 형태의 문자열입니다.

   * **Active Directory 컨테이너 -** 에이전트가 기본적으로 사용자 계정을 만드는 컨테이너 DN을 입력합니다.
        예: *OU=사용자, DC=콘토소, DC=com*
        > [!NOTE]
        > 이 설정은 *parentDistinguishedName* 특성이 특성 매핑에 구성되지 않은 경우에만 사용자 계정 생성에 사용됩니다. 이 설정은 사용자 검색 또는 업데이트 작업에는 사용되지 않습니다. 전체 도메인 하위 트리는 검색 작업의 범위에 속합니다.

   * **알림 메일 –** 메일 주소를 입력하고 “오류가 발생하면 메일 보내기” 확인란을 선택합니다.
    > [!NOTE]
    > Azure AD 프로비전 서비스는 프로비전 작업이 [격리](/azure/active-directory/manage-apps/application-provisioning-quarantine-status) 상태가 되면 이메일 알림을 보냅니다.

   * **연결 테스트** 단추를 클릭합니다. 연결 테스트가 성공하면 맨 위에서 **저장** 단추를 클릭합니다. 오류가 발생하면 에이전트 설정에서 구성된 SuccessFactors 자격 증명 및 AD 자격 증명이 유효한지 다시 한 번 확인합니다.
    >[!div class="mx-imgBorder"]
    >![Azure Portal](./media/sap-successfactors-inbound-provisioning/sf2ad-provisioning-creds.png)

   * 자격 증명이 성공적으로 저장되면 **매핑** 섹션에는 **SuccessFactors 사용자를 온프레미스 활성 디렉터리로 동기화하는** 기본 매핑이 표시됩니다.

### <a name="part-4-configure-attribute-mappings"></a>파트 4: 특성 매핑 구성

이 섹션에서는 사용자 데이터가 SuccessFactors에서 Active Directory로 흐르는 방식을 구성합니다.

1. 매핑 아래의 프로비저닝 탭에서 **SuccessFactors 사용자를 온프레미스 활성 디렉터리로 동기화를** **클릭합니다.**

1. 소스 **개체 범위** 필드에서 특성 기반 필터 집합을 정의하여 SuccessFactors의 사용자 집합을 AD에 프로비전할 범위로 선택할 수 있습니다. 기본 범위는 "SuccessFactors의 모든 사용자"입니다. 예제 필터:

   * 예: personId를 가진 사용자에 대한 범위1000000과 2000000 사이(2000000 제외)

      * 속성: personId외부

      * 연산자: REGEX Match

      * 값: (1[0-9][0-9][0-9][0-9][0-9][0-9])

   * 예: 정규직 직원만 포함하고 비정규직 직원은 포함하지 않음

      * 특성: EmployeeID

      * 연산자: IS NOT NULL

   > [!TIP]
   > 프로비전 앱을 처음 구성하는 경우 특성 매핑 및 식을 테스트하고 확인하여 원하는 결과를 제공하는지 확인해야 합니다. 소스 **개체 범위** 아래의 범위 지정 필터를 사용하여 SuccessFactors의 몇 명의 테스트 사용자와 함께 매핑을 테스트하는 것이 좋습니다. 매핑이 작동하는지 확인한 후에는 필터를 제거하거나 점진적으로 더 많은 사용자를 포함하도록 해당 필터를 점진적으로 확장할 수 있습니다.

   > [!CAUTION] 
   > 프로비저닝 엔진의 기본 동작은 범위를 벗어난 사용자를 사용하지 않도록/삭제하는 것입니다. 성공요소에서 AD 통합에 바람직하지 않을 수 있습니다. 이 기본 동작을 재정의하려면 [범위를 벗어난 사용자 계정의 삭제](../app-provisioning/skip-out-of-scope-deletions.md) 를 건너뜁니다.
  
1. **대상 개체 작업** 필드에서 Active Directory에서 수행할 작업을 전역적으로 필터링할 수 있습니다. **만들기** 및 **업데이트가** 가장 일반적입니다.

1. 특성 **매핑** 섹션에서 개별 SuccessFactors 특성이 Active Directory 특성에 매핑되는 방식을 정의할 수 있습니다.

  >[!NOTE]
  >응용 프로그램에서 지원하는 SuccessFactors 특성의 전체 목록은 [SuccessFactors 특성 참조를](../app-provisioning/sap-successfactors-attribute-reference.md) 참조하십시오.


1. 기존 특성 매핑을 클릭하여 업데이트하거나 화면 맨 아래에서 **새 매핑 추가**를 클릭하여 새 매핑을 추가합니다. 개별 특성 매핑은 다음 속성을 지원합니다.

      * **매핑 유형**

         * **직접** – SuccessFactors 특성의 값을 변경 없이 AD 특성에 씁니다.

         * **상수** - AD 특성에 고정적인 상수 문자열 값을 씁니다.

         * **표현식** - 하나 이상의 SuccessFactors 특성을 기반으로 AD 특성에 사용자 지정 값을 작성할 수 있습니다. [자세한 내용은 식에 대한 이 문서를 참조하세요](../app-provisioning/functions-for-customizing-application-data.md).

      * **소스 특성** - SuccessFactors의 사용자 특성

      * **기본값** – 선택 사항입니다. 원본 특성의 값이 비어 있는 경우 매핑에서 이 값을 대신 씁니다.
            이 값을 비워두는 것이 가장 일반적인 구성입니다.

      * **대상 특성** – Active Directory의 사용자 특성입니다.

      * **이 특성을 사용하여 개체 일치** – SuccessFactors와 Active Directory 사이의 사용자를 고유하게 식별하는 데 이 매핑을 사용해야 하는지 여부입니다. 이 값은 일반적으로 Active Directory의 직원 ID 특성 중 하나에 매핑되는 SuccessFactors에 대한 작업자 ID 필드에 설정됩니다.

      * **일치 우선 순위** – 여러 일치 특성을 설정할 수 있습니다. 일치 특성이 여러 개 있으면 이 필드에 정의된 순서대로 평가됩니다. 일치 항목이 발견되는 즉시 더 이상 일치 특성을 평가하지 않습니다.

      * **이 매핑 적용**

         * **항상** – 사용자 만들기 및 업데이트 작업 모두에 이 매핑 적용

         * **만들기 작업 시에만** - 사용자 만들기 작업 시에만 이 매핑을 적용합니다.

1. 매핑을 저장하려면 속성 매핑 섹션 상단의 **저장을** 클릭합니다.

특성 매핑 구성이 완료되면 이제 [사용자 프로비저닝 서비스를 사용하도록 설정하고 시작](#enable-and-launch-user-provisioning)할 수 있습니다.

## <a name="enable-and-launch-user-provisioning"></a>사용자 프로비저닝 사용 및 시작

SuccessFactors 프로비저닝 앱 구성이 완료되면 Azure 포털에서 프로비저닝 서비스를 켤 수 있습니다.

> [!TIP]
> 기본적으로 프로비전 서비스를 켜면 범위 내 모든 사용자의 프로비전 작업이 시작됩니다. 매핑 또는 SuccessFactors 데이터 문제에 오류가 있는 경우 프로비저닝 작업이 실패하고 격리 상태로 전환될 수 있습니다. 이를 방지하기 위해 **원본 개체 범위** 필터를 구성하고 모든 사용자의 전체 동기화를 시작하기 전에 몇몇 테스트 사용자로 특성 매핑을 테스트하는 것이 좋습니다. 매핑이 작동하고 원하는 결과를 제공하는지 확인한 후에는 필터를 제거하거나 점진적으로 더 많은 사용자를 포함하도록 해당 필터를 점진적으로 확장할 수 있습니다.

1. **프로비전** 탭에서 **프로비전 상태**를 **켜기**로 설정합니다.

2. **저장**을 클릭합니다.

3. 이 작업은 초기 동기화를 시작하며 SuccessFactors 테넌트에 있는 사용자 수에 따라 가변적인 시간이 걸릴 수 있습니다. 진행률 표시줄을 확인하여 동기화 주기의 진행률을 추적할 수 있습니다. 

4. 언제든지 Azure Portal에서 **감사 로그** 탭을 확인하여 프로비전 서비스에서 수행한 작업을 확인합니다. 감사 로그에는 SuccessFactors에서 읽혀지고 이후에 Active Directory에 추가하거나 업데이트하는 사용자와 같이 프로비저닝 서비스에서 수행하는 모든 개별 동기화 이벤트가 나열됩니다. 

5. 초기 동기화가 완료되면 아래와 같이 **프로비전** 탭에 감사 요약 보고서가 작성됩니다.

   > [!div class="mx-imgBorder"]
   > ![프로비저닝 진행률 표시줄](./media/sap-successfactors-inbound-provisioning/prov-progress-bar-stats.png)

## <a name="next-steps"></a>다음 단계

* [인바운드 프로비저닝을 위한 지원되는 SuccessFactors 특성에 대해 자세히 알아보기](../app-provisioning/sap-successfactors-attribute-reference.md)
* [성공 인자에 이메일 쓰기를 구성하는 방법에 대해 알아봅니다.](sap-successfactors-writeback-tutorial.md)
* [프로비저닝 작업에 대한 로그를 검토하고 보고서를 받아보는 방법을 알아봅니다](../app-provisioning/check-status-user-account-provisioning.md).
* [SuccessFactors와 Azure Active Directory 간에 단일 사인온을 구성하는 방법에 대해 알아봅니다.](successfactors-tutorial.md)
* [Azure Active Directory와 다른 SaaS 애플리케이션을 통합하는 방법을 알아봅니다.](tutorial-list.md)
* [프로비저닝 구성내보내기 및 가져오기 방법 알아보기](../app-provisioning/export-import-provisioning-configuration.md)
