---
title: '자습서: Concur와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory에서 Concur를 사용하여 Single Sign-On, 자동화된 프로비전 등을 사용하도록 설정하는 방법을 알아봅니다.
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila

ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/11/2016
ms.author: jeedes

---
# 자습서: Concur와 Azure Active Directory 통합
이 자습서는 Azure 및 Concur의 통합을 보여주기 위한 것입니다. 이 자습서에 설명된 시나리오에서는 사용자에게 이미 다음 항목이 있다고 가정합니다.

* 유효한 Azure 구독
* Concur 내의 테넌트

이 자습서에 설명된 시나리오는 다음 구성 요소로 이루어져 있습니다.

1. Concur에 응용 프로그램 통합 사용
2. Single Sign-On 구성
3. 사용자 프로비전 구성
4. 사용자 할당

![시나리오](./media/active-directory-saas-concur-tutorial/IC769766.png "시나리오")

> [!NOTE]
> SAML 통해 페더레이션된 SSO에 대한 Concur 구독의 구성은 별도 작업이며 수행하려면 Concur에 문의해야 합니다.
> 
> 

## Concur에 응용 프로그램 통합 사용
이 섹션은 Concur에 응용 프로그램 통합을 사용하도록 설정하는 방법을 간략하게 설명하기 위한 것입니다.

### Concur에 응용 프로그램 통합을 사용하도록 설정하려면 다음 단계를 수행합니다.
1. Azure 클래식 포털의 왼쪽 탐색 창에서 **Active Directory**를 클릭합니다.
   
   ![Active Directory](./media/active-directory-saas-concur-tutorial/IC700993.png "Active Directory")
2. **디렉터리** 목록에서 디렉터리 통합을 사용하도록 설정할 디렉터리를 선택합니다.
3. 응용 프로그램 보기를 열려면 디렉터리 보기의 최상위 메뉴에서 **응용 프로그램**을 클릭합니다.
   
   ![응용 프로그램](./media/active-directory-saas-concur-tutorial/IC700994.png "응용 프로그램")
4. **응용 프로그램 갤러리**를 열려면 **앱 추가**를 클릭한 다음 **조직에서 사용할 응용 프로그램 추가**를 클릭합니다.
   
   ![원하는 작업을 선택하세요.](./media/active-directory-saas-concur-tutorial/IC700995.png "원하는 작업을 선택하세요.")
5. **검색 상자**에 **Concur**를 입력합니다.
   
   ![응용 프로그램 갤러리](./media/active-directory-saas-concur-tutorial/IC721727.png "응용 프로그램 갤러리")
6. 결과 창에서 **Concur**를 선택하고 **완료**를 클릭하여 응용 프로그램을 추가합니다.
   
   ![Concur](./media/active-directory-saas-concur-tutorial/IC721728.png "Concur")
   
   ## Single Sign-On 구성

이 섹션은 사용자가 SAML 프로토콜 기반 페더레이션을 사용하여 Azure AD의 계정으로 Concur에 인증할 수 있게 하는 방법을 간략하게 설명하기 위한 것입니다.

> [!NOTE]
> SAML 통해 페더레이션된 SSO에 대한 Concur 구독의 구성은 별도 작업이며 수행하려면 Concur에 문의해야 합니다.
> 
> 

### Single Sign-On을 구성하려면 다음 단계를 수행합니다.
1. Azure 클래식 포털의 **Concur** 응용 프로그램 통합 페이지에서 **Single Sign-On 구성**을 클릭하여 **Single Sign-On 구성** 대화 상자를 엽니다.
   
   ![Single Sign-On 구성](./media/active-directory-saas-concur-tutorial/IC769767.png "Single Sign-On 구성")
2. **Concur에 대한 사용자 로그온 방법을 선택하십시오.** 페이지에서 **Microsoft Azure AD Single Sign-on**을 선택하고 **다음**을 클릭합니다.
   
   ![Single Sign-On 구성](./media/active-directory-saas-concur-tutorial/IC769768.png "Single Sign-On 구성")
3. **앱 URL 구성** 페이지에 있는 **Concur 로그인 URL** 텍스트 상자에 Concur 테넌트 로그인 URL를 입력하고 **다음**을 클릭합니다.
   
   ![로그인 URL 구성](./media/active-directory-saas-concur-tutorial/IC769769.png "로그인 URL 구성")
4. **Concur의 Single Sign-On을 구성** 페이지에서 다음 단계를 수행합니다.
   
   ![로그인 URL 구성](./media/active-directory-saas-concur-tutorial/IC769770.png "로그인 URL 구성")
   
   1. 메타데이터를 다운로드를 클릭한 다음 컴퓨터에 데이터 파일을 저장합니다.
   2. 테넌트에 SSO를 구성하려면 Concur 지원팀에 문의합니다.
   3. Single Sign-On 구성 확인을 선택하고 **완료**를 클릭하여 **Single Sign-On 구성** 대화 상자를 닫습니다.
   
   > [!NOTE]
   > SAML 통해 페더레이션된 SSO에 대한 Concur 구독의 구성은 별도 작업이며 수행하려면 Concur에 문의해야 합니다.
   > 
   > 

## 사용자 프로비전 구성
이 섹션은 Concur에 Active Directory 사용자 계정을 프로비전할 수 있도록 설정하는 방법을 간략하게 설명하기 위한 것입니다.

Expense Service에서 앱을 사용하도록 설정하려면 웹 서비스 관리자 프로필을 적절히 설치하고 사용해야 합니다. T&E 관리 기능으로 사용하는 기존 관리자 프로필에 WS Admin 역할을 추가하지 마십시오.

Concur 컨설턴트 또는 클라이언트 관리자는 고유한 웹 서비스 관리자 프로필을 만들어야 하고 클라이언트 관리자는 웹 서비스 관리자 기능에 해당 프로필을 사용해야 합니다.(예: 앱 설정) 이러한 프로필은 클라이언트 관리자의 일별 T&E 관리자 프로필에서 별도로 유지되어야 합니다.(T&E 관리자 프로필에 할당된 WSAdmin 역할이 없어야 함)

앱을 사용하도록 설정하는 데 사용되는 프로필을 만드는 경우 사용자 프로필 필드에 클라이언트 관리자의 이름을 입력합니다. 프로필에 소유권을 할당합니다. 프로 필을 만들게 되면 클라이언트는 웹 서비스 메뉴 내에서 파트너 앱에 "*사용*" 단추를 클릭하여 이 프로필에 로그인해야 합니다.

다음과 같은 이유로 이 작업은 일반 T&E 관리에 사용하는 프로필을 사용하지 말아야 합니다.

1. 앱을 사용하도록 설정한 후에 클라이언트가 표시되는 대화창에서 "*예*"를 클릭해야 합니다. 클릭함으로써 클라이언트는 파트너 응용 프로그램이 데이터에 액세스하도록 승인하므로 사용자나 파트너가 해당 예 단추를 클릭할 수 없습니다.
2. T&E 관리자 프로필을 사용하여 앱을 사용하도록 설정한 클라이언트 관리자가 퇴사하면(비활성화 된 프로필이 발생함) 앱이 다른 WS 관리자 프로필로 설정될 때까지 해당 프로필을 사용하도록 설정된 앱은 작동하지 않습니다. 이 때문에 고유한 WS 관리자 프로필을 만들도록 합니다.
3. 관리자가 퇴사하면 프로필이 비활성화되지 않아도 되기 때문에 활성화된 앱에 영향을 주지 않으면서도 원하는 경우 WS 관리자 프로필로 연결된 이름을 대체 관리자로 변경할 수 있습니다.

### 사용자 프로비전을 구성하려면
1. **Concur** 테넌트에 로그인합니다.
2. **관리** 메뉴에서 **웹 서비스**를 선택합니다.
   
   ![Concur 테넌트](./media/active-directory-saas-concur-tutorial/IC721729.png "Concur 테넌트")
3. 왼쪽의 **웹 서비스** 창에서 **파트너 응용 프로그램 사용**을 선택합니다.
   
   ![파트너 응용 프로그램 사용](./media/active-directory-saas-concur-tutorial/IC721730.png "파트너 응용 프로그램 사용")
4. **응용 프로그램 사용** 목록에서**Azure Active Directory**를 선택하고 **사용**을 클릭합니다.
   
   ![Microsoft Azure Active Directory](./media/active-directory-saas-concur-tutorial/IC721731.png "Microsoft Azure Active Directory")
5. **예**를 클릭하여 **동작 확인** 대화 상자를 닫습니다.
   
   ![작업 확인](./media/active-directory-saas-concur-tutorial/IC721732.png "작업 확인")
6. Azure 클래식 포털의 응용 프로그램 목록에서 **Concur**을 선택하여 **Concur** 대화 상자 페이지를 엽니다.
7. **사용자 프로비전 구성** 대화 상자 페이지를 열려면 **사용자 프로비저닝 구성**을 클릭합니다.
8. Concur 관리자의 사용자 이름 및 암호를 입력하고 **다음**을 클릭합니다.
9. 구성을 마치려면 **확인** 페이지에서 **완료** 단추를 클릭합니다.

이제 테스트 계정을 만들고 10분 동안 기다린 후 계정이 Concur에 동기화되었는지 확인할 수 있습니다.

## 사용자 할당
구성을 테스트하려면 응용 프로그램 사용을 허용하려는 Azure AD 사용자를 할당하여 액세스 권한을 부여해야 합니다.

### Concur에 사용자를 할당하려면 다음 단계를 수행합니다.
1. Azure 클래식 포털에서 테스트 계정을 만듭니다.
2. **Concur** 응용 프로그램 통합 페이지에서 **사용자 할당**을 클릭합니다.
   
   ![사용자 할당](./media/active-directory-saas-concur-tutorial/IC769771.png "사용자 할당")
3. 테스트 사용자를 선택하고 **할당**을 클릭한 다음 **예**를 클릭하여 할당을 확인합니다.
   
   ![예](./media/active-directory-saas-concur-tutorial/IC767830.png "예")

이제 10분 동안 기다린 후 계정이 Concur에 동기화되었는지 확인해야 합니다.

Single Sign-On 설정을 테스트하려면 액세스 패널을 엽니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](active-directory-saas-access-panel-introduction.md)를 참조하십시오.

<!---HONumber=AcomDC_0713_2016-->