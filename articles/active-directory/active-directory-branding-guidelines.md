<properties
   pageTitle="응용 프로그램에 대한 브랜딩 지침"
   description="Azure Active Directory의 개발자 중심 리소스에 대한 포괄적인 가이드"
   services="active-directory"
   documentationCenter="dev-center-name"
   authors="msmbaldwin"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="04/29/2015"
   ms.author="mbaldwin"/>


# 응용 프로그램에 대한 브랜딩 지침


이 항목에서는 Azure Active Directory를 사용해 응용 프로그램을 개발할 때 사용해야 하는 브랜딩 지침에 대해 설명합니다. 이 지침은 Azure AD에서 관리되는 회사 또는 학교 계정을 응용 프로그램을 등록하고 로그인하는 데 사용하려는 고객을 안내하는 데 도움이 됩니다.

## Microsoft의 개인 계정과 회사 또는 학교 계정

Microsoft는 두 종류의 사용자 계정을 관리합니다.

- **개인 계정**(이전의 Windows Live ID). 이 계정은 *개인* 사용자와 Microsoft 사이의 관계를 나타내며 Microsoft의 소비자 장치 및 서비스에 액세스하는 데 사용됩니다. 이 계정은 개인적인 용도를 위한 것입니다.

- **회사 또는 학교 계정.** 이 계정은 Azure Active Directory를 사용하는 조직을 대신하여 Microsoft에서 관리합니다. 이 계정은 Office 365 및 Microsoft의 다른 비즈니스 서비스에 로그인하는 데 사용됩니다.

Microsoft 회사 또는 학교 계정은 일반적으로 회사, 학교, 정부 기관 등의 조직이 직원, 학생, 연방 직원 등의 최종 사용자에게 할당합니다. 이 계정은 Azure AD 플랫폼을 통해 클라우드에서 직접 마스터되거나 온-프레미스 디렉터리(예: Windows Server Active Directory)에서 Azure AD로 동기화됩니다. Microsoft는 회사 또는 학교 계정의 *보유자*이지만, 이 계정을 소유하고 제어하는 것은 조직입니다.

## 응용 프로그램에서 Azure AD 계정 언급

Microsoft는 Azure 또는 Active Directory 브랜드 이름에 최종 사용자를 노출하지 않으며 이 규칙을 강제합니다.

- 사용자가 로그인되어 있으면 가능한 한 조직의 이름 및 로고를 사용해야 합니다. 이것이 "조직"과 같은 일반적인 용어를 사용하는 것보다 좋습니다.

- 사용자가 로그인되어 있지 않으면 사용자의 계정을 “회사 또는 학교 계정”으로 언급하고 이 계정을 Microsoft가 관리한다는 것을 전달하기 위해 Microsoft 로고를 사용해야 합니다. 사용자에게 혼동을 줄 수 있는 "엔터프라이즈 계정", "비즈니스 계정" 또는 "회사 계정"과 같은 용어는 사용하지 마세요.

## 사용자 계정 픽토그램
이 지침의 이전 버전에서는 "파란색 배지" 픽토그램을 사용하도록 권장했습니다. 사용자와 개발자의 의견을 반영하여 이제 그 대신 Microsoft 로고를 사용할 것을 권장합니다. 그러면 사용자들이 Office 365 또는 기타 Microsoft 비즈니스 서비스에 사용하는 계정을 앱 로그인에 다시 사용할 수 있다는 점을 이해하는 데 도움이 됩니다.

## Azure AD를 사용한 등록 및 로그인

앱의 등록 경로와 로그인 경로가 별도로 제공될 수 있으며, 다음 섹션에서는 두 시나리오에 대한 시각적인 지침을 제공합니다.

**최종 사용자 등록을 지원하는 앱의 경우(예: 무료 평가판 또는 프리미엄(freemium) 모델)**: 사용자가 Microsoft의 회사 또는 학교 계정을 사용하여 앱에 액세스하는 데 사용할 수 있는 **로그인** 단추를 표시할 수 있습니다. 사용자가 처음으로 앱에 액세스할 때 Azure AD에서 동의하도록 요구하는 메시지가 표시됩니다.

**관리자만 동의할 수 있는 권한을 요구하는 앱 또는 조직 라이선스가 필요한 앱의 경우**: 관리 취득과 사용자 로그인을 분리해야 합니다. **"이 앱 가져오기" 단추**는 관리자에게 로그인하도록 리디렉션한 다음 조직의 사용자를 대신하여 동의하도록 요청합니다. 이 기능은 최종 사용자 동의를 요청하는 메시지를 표시하지 않게 하는 기능을 앱에 추가합니다.

## 앱 구입에 대한 시각적 지침

"앱 가져오기" 링크는 Azure AD 액세스 권한 부여(권한 부여) 페이지로 사용자를 리디렉션해야 합니다. 그러면 조직 관리자가 앱이 Microsoft에서 호스트되는 조직 데이터에 액세스할 수 있게 승인할 수 있습니다. 액세스 권한을 요청하는 방법에 대한 자세한 내용은 [응용 프로그램 추가, 업데이트 및 제거](https://msdn.microsoft.com/library/azure/dn132599.aspx) 항목에서 설명합니다.

관리자가 앱에 동의한 후에는 사용자의 Office 365 앱 시작 관리자 환경(와플 및 [https://portal.office.com/myapps](https://portal.office.com/myapps)에서 액세스 가능)에 앱을 추가할 수 있습니다. 이 기능을 보급하려는 경우 "조직에 이 앱 추가"와 같은 용어를 사용하고 다음과 같은 단추를 표시할 수 있습니다.

![응용 프로그램 종류 및 시나리오](./media/active-directory-branding-guidelines/add-to-my-org.png)
  
그러나 단추에 의존하는 대신 설명 텍스트를 작성하는 것이 좋습니다. 예:
> *미 Office 365 또는Microsoft의 다른 비즈니스 서비스를 사용하는 경우 조직의 데이터에 대한 <your_app_name> 액세스 권한을 부여하면 됩니다. 이렇게 하면 사용자들이 기존 회사 계정으로 <your_app_name>에 액세스할 수 있습니다.*


## 로그인에 대한 시각적 지침
앱은 Azure AD와 통합하는 데 사용하는 프로토콜에 해당하는 로그인 끝점으로 사용자를 리디렉션하는 로그인 단추를 표시해야 합니다. 다음 섹션에서는 이 단추의 모양을 자세히 설명합니다.

### 픽토그램 및 "회사 또는 학교 계정" 
Microsoft 로고와 앱이 지원하는 다른 ID 공급자 중에서 Azure AD를 고유하게 나타내는 “회사 또는 학교”라는 일반 용어를 결합한 것입니다. 공간이 부족하여 "회사 또는 학교 계정"을 사용할 수 없는 경우에는 "회사 계정"으로 줄여도 됩니다.
 
![응용 프로그램 종류 및 시나리오](./media/active-directory-branding-guidelines/work-or-school-account.png)

![응용 프로그램 종류 및 시나리오](./media/active-directory-branding-guidelines/work-account.png)

최종 사용자가 이 단추를 사용할 수 있는지 여부를 인식할 수 있도록, 추가 설명을 제공할 수도 있습니다.

![응용 프로그램 종류 및 시나리오](./media/active-directory-branding-guidelines/work-account-with-explaination.png)
 
## 브랜딩 관련 할 일과 하지 말아야 할 일
**권장** "회사 또는 학교 계정"을Microsoft 로고와 함께 사용하여 Azure AD에서 로그인을 나타냅니다. 공간이 부족한 경우 “회사 계정”이라고 해도 됩니다. **권장 안 함** “엔터프라이즈 계정”, “비즈니스 계정” 또는 “회사 계정”과 같은 다른 용어는 사용하지 않습니다.

**권장 안 함** “Office 365 ID” 또는 “Azure ID”를 사용하지 않습니다. Office 365는 인증을 위해 Azure AD를 사용하지 않는 Microsoft 소비자 서비스의 이름이기도 합니다.

**권장 안 함** Microsoft 로고를 변경하지 않습니다.

**장 안 함** Azure 또는 Active Directory에 최종 사용자를 노출하지 않습니다. 그러나 개발자, IT 전문가, 관리자에 대해서는 이 용어를 사용해도 됩니다.

## 탐색 관련 할 일과 하지 말아야 할 일

**권장** 사용자가 로그아웃한 후 다른 사용자 계정으로 전환하는 방법을 제공합니다. 대부분의 사람들은 Microsoft/Facebook/Google/Twitter의 단일 개인 계정을 가지고 있지만, 사람들은 종종 여러 조직과 연결됩니다. 여러 명의 로그인한 사용자에 대한 지원이 곧 제공됩니다.

## 앱에서 Azure AD 및 Microsoft 계정 지원

앱이 Azure AD 및 Microsoft 계정을 모두 지원하는 경우 앱에 두 개의 개별 로그인 단추를 포함해야 합니다. 한 번 통합하면 Microsoft의 개인 계정과 회사 계정을 모두 지원할 수 있는 업데이트를 제공하기 위해 현재 적극적으로 노력하고 있습니다. 이 업데이트가 제공되면 앱에 하나의 "Microsoft로 로그인" 단추를 표시할 수 있습니다.

 

<!---HONumber=62-->