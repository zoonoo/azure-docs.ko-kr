---
title: '자습서: SCC LifeCycle과 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory에서 SCC LifeCycle를 사용하여 Single Sign-On, 자동화된 프로비전 등을 사용하도록 설정하는 방법을 알아봅니다.
services: active-directory
author: jeevansd
documentationcenter: na
manager: mtillman
ms.assetid: 9748bf38-ffc3-4d51-a1ae-207ce57104fa
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/03/2018
ms.author: jeedes
ms.openlocfilehash: 943698311d10b479879fd92d2482d827650e2b0c
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/14/2018
---
# <a name="tutorial-azure-active-directory-integration-with-scc-lifecycle"></a>자습서: SCC LifeCycle과 Azure Active Directory 통합
이 자습서는 Azure와 SCC LifeCycle의 통합을 보여주기 위한 것입니다.  

이 자습서에 설명된 시나리오에서는 사용자에게 이미 다음 항목이 있다고 가정합니다.

* 유효한 Azure 구독
* SCC LifeCycle SSO(Single Sign-On)가 설정된 구독

이 자습서를 완료한 후 SCC LifeCycle에 할당한 Azure AD 사용자가 SCC LifeCycle 회사 사이트(서비스 공급자가 시작한 로그온)에서 또는 [액세스 패널 소개](active-directory-saas-access-panel-introduction.md)를 사용하여 응용 프로그램에 Single Sign-On할 수 있습니다.

이 자습서에 설명된 시나리오는 다음 구성 요소로 이루어져 있습니다.

1. SCC LifeCycle에 응용 프로그램 통합 사용
2. SSO(Single Sign-On) 구성
3. 사용자 프로비전 구성
4. 사용자 할당

![시나리오](./media/active-directory-saas-scc-lifecycle-tutorial/IC794120.png "시나리오")

## <a name="enable-the-application-integration-for-scc-lifecycle"></a>SCC LifeCycle에 응용 프로그램 통합 사용
이 섹션은 SCC LifeCycle에 응용 프로그램 통합을 사용하도록 설정하는 방법을 간략하게 설명하기 위한 것입니다.

**SCC LifeCycle에 응용 프로그램 통합을 사용하도록 설정하려면 다음 단계를 수행합니다.**

1. Azure 클래식 포털의 왼쪽 탐색 창에서 **Active Directory**를 클릭합니다.
   
    ![Active Directory](./media/active-directory-saas-scc-lifecycle-tutorial/IC700993.png "Active Directory")
2. **디렉터리** 목록에서 디렉터리 통합을 사용하도록 설정할 디렉터리를 선택합니다.
3. 응용 프로그램 보기를 열려면 디렉터리 보기의 최상위 메뉴에서 **응용 프로그램** 을 클릭합니다.
   
    ![응용 프로그램](./media/active-directory-saas-scc-lifecycle-tutorial/IC700994.png "응용 프로그램")
4. 페이지 맨 아래에 있는 **추가** 를 클릭합니다.
   
    ![응용 프로그램 추가](./media/active-directory-saas-scc-lifecycle-tutorial/IC749321.png "응용 프로그램 추가")
5. **수행할 작업** 대화 상자에서 **갤러리에서 응용 프로그램 추가**를 클릭합니다.
   
    ![갤러리에서 응용 프로그램 추가](./media/active-directory-saas-scc-lifecycle-tutorial/IC749322.png "갤러리에서 응용 프로그램 추가")
6. **검색 상자**에 **SCC LifeCycle**를 입력합니다.
   
    ![응용 프로그램 갤러리](./media/active-directory-saas-scc-lifecycle-tutorial/IC794121.png "응용 프로그램 갤러리")
7. 결과 창에서 **SCC LifeCycle**를 선택하고 **완료**를 클릭하여 응용 프로그램을 추가합니다.
   
    ![SCC LifeCycle](./media/active-directory-saas-scc-lifecycle-tutorial/IC795082.png "SCC LifeCycle")
   
## <a name="configure-single-sign-on"></a>Single Sign-On 구성

이 섹션은 사용자가 SAML 프로토콜 기반 페더레이션을 사용하여 Azure AD의 계정으로 SCC LifeCycle에 인증할 수 있게 하는 방법을 간략하게 설명하기 위한 것입니다.

**Single Sign-On을 구성하려면 다음 단계를 수행합니다.**

1. Azure 클래식 포털의 **SCC LifeCycle** 응용 프로그램 통합 페이지에서 **Single Sign-On 구성**을 클릭하여 **Single Sign-On 구성 ** 대화 상자를 엽니다.
   
    ![Single Sign-On 구성](./media/active-directory-saas-scc-lifecycle-tutorial/IC794122.png "Single Sign-On 구성")
2. **SCC LifeCycle에 대한 사용자 로그온 방법 선택** 페이지에서 **Microsoft Azure AD Single Sign-On**을 선택하고 **다음**을 클릭합니다.
   
    ![Single Sign-On 구성](./media/active-directory-saas-scc-lifecycle-tutorial/IC794123.png "Single Sign-On 구성")
3. **앱 URL 구성** 페이지의 **로그온 URL** 텍스트 상자에 다음 패턴 “*https://bs1.scc.com/lc7/welcome/customer/PICTtest.aspx*”을 사용하여 SCC LifeCycle 응용 프로그램에 로그온하기 위해 사용자가 사용한 URL을 입력한 후 **다음**을 클릭합니다.
   
    ![앱 URL 구성](./media/active-directory-saas-scc-lifecycle-tutorial/IC794124.png "앱 URL 구성")
4. **SCC LifeCycle에서 Single Sign-On 구성** 페이지에서 **메타데이터 다운로드**를 클릭한 다음 컴퓨터에 로컬로 메타데이터 파일을 저장합니다.
   
   ![Single Sign-On 구성](./media/active-directory-saas-scc-lifecycle-tutorial/IC795083.png "Single Sign-On 구성")
5. SCC LifeCycle 지원팀에 해당 메타데이터 파일을 전달합니다.
   
   >[!NOTE]
   >Single Sign-On은 SCC LifeCycle 지원팀에서 사용할 수 있어야 합니다.
   > 
   > 

6. Azure 클래식 포털에서 Single Sign-On 구성 확인을 선택하고 **완료**를 클릭하여 **Single Sign-On 구성** 대화 상자를 닫습니다.
   
    ![Single Sign-On 구성](./media/active-directory-saas-scc-lifecycle-tutorial/IC794125.png "Single Sign-On 구성")
   
## <a name="configure-user-provisioning"></a>사용자 프로비전 구성

Azure AD 사용자가 SCC LifeCycle에 로그인할 수 있도록 하려면 SCC LifeCycle로 프로비전되어야 합니다. SCC LifeCycle를 프로비전하는 사용자를 구성할 작업 항목이 없습니다.

할당된 사용자가 SCC LifeCycle에 로그인하려고 하는 경우, 필요하면 SCC LifeCycle 계정이 자동으로 생성됩니다.

>[!NOTE]
>다른 SCC LifeCycle 사용자 계정 생성 도구 또는 SCC LifeCycle이 제공한 API를 사용하여 AAD 사용자 계정을 프로비전할 수 있습니다.
> 
> 

## <a name="assign-users"></a>사용자 할당
구성을 테스트하려면 응용 프로그램 사용을 허용하려는 Azure AD 사용자를 할당하여 액세스 권한을 부여해야 합니다.

**SCC LifeCycle에 사용자를 할당하려면 다음 단계를 수행합니다.**

1. Azure 클래식 포털에서 테스트 계정을 만듭니다.
2. **SCC LifeCycle **응용 프로그램 통합 페이지에서 **사용자 할당**을 클릭합니다.
   
    ![사용자 할당](./media/active-directory-saas-scc-lifecycle-tutorial/IC794126.png "사용자 할당")
3. 테스트 사용자를 선택하고 **할당**을 클릭한 다음 **예**를 클릭하여 할당을 확인합니다.
   
    ![예](./media/active-directory-saas-scc-lifecycle-tutorial/IC767830.png "예")

SSO 설정을 테스트하려면 액세스 패널을 엽니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](active-directory-saas-access-panel-introduction.md)를 참조하세요.

