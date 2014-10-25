<properties umbracoNaviHide="0" pageTitle="Azure AD Integration with Google Apps" metaKeywords="Azure Google Apps Integration" description="Learn about integrating Azure AD with Google Apps." linkid="documentation-services-identity-windows-azure-ad-integration-with-google=apps" urlDisplayName="Azure AD Integration with Google Apps" headerExpose="" footerExpose="" disqusComments="0" editor="lisatoft" manager="terrylan" title="Azure AD Integration with Google Apps" authors="" />

<tags ms.service="active-directory" ms.workload="identity" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author></tags>

# Azure AD 및 Google Apps 통합

이 자습서는 Azure 및 Google Apps의 통합을 보여 주기 위한 것입니다. 이 자습서에 설명된 시나리오에서는 사용자에게 이미 다음 항목이 있다고 가정합니다.

-   유효한 Azure 구독
-   Googe Apps의 테스트 테넌트

Google Apps에 유효한 테넌트가 아직 없는 경우 Google Apps for Business 웹 사이트에서 체험 계정을 등록할 수 있습니다.

이 자습서에 설명된 시나리오는 다음 구성 요소로 이루어져 있습니다.

Google Apps에 응용 프로그램 통합 사용
Single Sign-On 구성
Google Apps API 액세스 사용
사용자 지정 도메인 추가
사용자 프로비전 구성

# Google Apps에 응용 프로그램 통합 사용

이 섹션은 Google Apps에 응용 프로그램 통합을 사용하도록 설정하는 방법을 간략하게 설명하기 위한 것입니다.

## Google Apps에 응용 프로그램 통합을 사용하도록 설정하려면

1.  Azure 관리 포털의 왼쪽 탐색 창에서 **Active Directory**를 클릭합니다.
2.  **디렉터리** 목록에서 디렉터리 통합을 사용하도록 설정할 디렉터리를 선택합니다.
3.  응용 프로그램 보기를 열려면 디렉터리 보기의 최상위 메뉴에서 **응용 프로그램**을 클릭합니다.
4.  맨 아래에서 **추가**를 클릭하여 **응용 프로그램 추가** 대화 상자를 엽니다.
5.  **Azure AD와 앱 통합** 대화 상자에서 **응용 프로그램에 대한 액세스 관리**를 클릭합니다.
6.  페이지를 관리할 **응용 프로그램 선택**의 응용 프로그램 목록에서 **Google Apps**를 선택합니다.
7.  **완료** 단추를 클릭하여 응용 프로그램을 추가하고 대화 상자를 닫습니다.

# Single Sign-On 구성

이 섹션은 사용자가 SAML 프로토콜 기반 페더레이션을 사용하여 Azure AD의 계정으로 Google Apps에 인증할 수 있게 하는 방법을 간략하게 설명하기 위한 것입니다.

## 페더레이션 Single Sign-On을 구성하려면

1.  Azure 관리 포털의 왼쪽 탐색 창에서 **Active Directory**를 선택하여 Active Directory 대화 상자 페이지를 엽니다.
2.  디렉터리 목록에서 해당 디렉터리를 선택하여 디렉터리의 구성 페이지를 엽니다.
3.  최상위 메뉴에서 **응용 프로그램**을 선택합니다.
4.  응용 프로그램 목록에서 **Google Apps**를 선택하여 Google Apps 구성 대화 상자를 엽니다.
5.  **Single Sign-On 구성** 대화 상자를 열려면 **Single Sign-On 구성**을 클릭합니다.

    ![Google\_Tutorial\_01][]

6.  이 앱에 대한 Single Sign-On 모드 선택 대화 상자 페이지에서 사용자가 Azure AD의 계정으로 인증을 모드로 선택하고 다음 단추를 클릭합니다.

    ![Google\_Tutorial\_02][]

7.  **앱 URL 구성** 대화 상자의 **Google Apps 테넌트 URL** 텍스트 상자에 Google Apps 테넌트 URL을 입력하고 **다음** 단추를 클릭합니다.

    ![Google\_Tutorial\_03][]

8.  **Google Apps에서 Single Sign-On 구성** 대화 상자에서 다음 단계를 수행하고 **완료** 단추를 클릭합니다.

    -   **인증서 다운로드**를 클릭하고 인증서를 **c:\\googleapps.cer**로 저장합니다.
    -   Google Apps 로그인 페이지를 열고 로그온합니다.

    ![Google\_Tutorial\_04][]

    -   **관리 콘솔**에서 보안을 클릭합니다.

    ![Google\_Tutorial\_05][]

    보안 아이콘이 표시되지 않는 경우 페이지 맨 아래에 있는 기타 컨트롤을 클릭해야 합니다.

9.  **보안** 페이지에서 **고급** 설정을 클릭합니다.

    ![Google\_Tutorial\_06][]

10. 페이지의 **고급** 설정 섹션에서 **Single Sign-On 설정**을 선택합니다.

    ![Google\_Tutorial\_07][]

11. Single Sign-On 설정 페이지에서 다음 단계를 수행합니다.

    ![Google\_Tutorial\_08][]

        + Select <strong>Enable Single Sign-on</strong>.
        + On the <strong>Configure single sign-on at Google Apps</strong> page in the Azure AD Portal, copy the <strong>SINGLE SIGN-ON URL</strong>, and then paste it into the related textbox on the <strong>Security page</strong> in the Google Apps <strong>Admin console</strong>.
        + On the <strong>Configure single sign-on at Google Apps</strong> page in the Azure AD Portal, copy the <strong>Single sign-out service URL</strong>, and then paste it into the related textbox on the <strong>Security</strong> page in the Google Apps <strong>Admin console</strong>.
        + On the <strong>Configure single sign-on at Google Apps</strong> page in the Azure AD Portal, copy the <strong>Change password URL</strong>, and then paste it into the related textbox on the <strong>Security</strong> page in the Google Apps <strong>Admin console</strong>.
        + Click the <strong>Browse</strong> button to locate the <strong>Verification certificate</strong>, and then click Upload.
        + Click <strong>Save</strong> changes.

12. Azure AD 포털의 **Google Apps에서 Single Sign-On 구성** 페이지에서 완료 단추를 클릭합니다.

이제 [액세스 패널][]로 이동하여 Google Apps에 대한 Single Sign-On을 테스트할 수 있습니다.

# Google Apps API 액세스 사용

사용자 프로비전을 위해 Azure Active Directory 및 Google Apps를 통합하는 경우 Google Apps에서 해당 테넌트에 API 액세스를 사용하도록 설정해야 합니다.

## Google Apps API 액세스를 사용하도록 설정하려면

1.  Google Apps 테넌트에 로그온합니다.
2.  **관리 콘솔**에서 **보안**을 클릭합니다.

    ![Google\_Tutorial\_05][]

    보안 아이콘이 표시되지 않는 경우 관리 콘솔 맨 아래에 있는 기타 컨트롤을 클릭합니다.
3.  보안 페이지에서 **API 참조**를 클릭하여 관련 구성 대화 상자 페이지를 엽니다.
4.  **API 액세스 사용**을 선택합니다.

    ![Google\_Tutorial\_09][]

    </p>

# 사용자 지정 도메인 추가

Google Apps를 통해 사용자 프로비전을 구성하려면 Azure AD 도메인과 Google Apps 도메인에 동일한 FQDN(정규화된 도메인 이름)이 있어야 합니다. 그러나 예를 들어 체험 테넌트를 사용하여 이 자습서의 시나리오를 테스트하는 경우 일반적으로 테넌트의 FQDN이 일치하지 않습니다. 이 문제를 해결하기 위해 Azure AD 및 Google Apps에서 사용자 지정 도메인을 구성할 수 있습니다.
사용자 지정 도메인을 구성하려면 공용 도메인의 DNS 영역 파일에 대한 액세스 권한이 필요합니다.

![Google\_Tutorial\_10][]

## Azure AD에서 사용자 지정 도메인을 추가하려면

1.  Azure 관리 포털의 왼쪽 탐색 창에서 **Active Directory**를 선택하여 **Active Directory** 대화 상자 페이지를 엽니다.
2.  디렉터리 목록에서 해당 디렉터리를 선택하여 디렉터리의 구성 페이지를 엽니다.
3.  최상위 메뉴에서 **도메인**을 선택합니다.
4.  **도메인 이름 추가** 텍스트 상자를 열려면 도메인 이름을 입력하고 **추가**를 클릭합니다.
5.  **다음**을 클릭하여 **도메인 이름 확인** 대화 상자 페이지를 엽니다.

    ![Google\_Tutorial\_11][]

6.  **레코드 종류**를 선택하고 DNS 영역 파일에 선택한 레코드를 등록합니다.

    ![Google\_Tutorial\_12][]

7.  **nslookup 명령**을 사용하여 DNS 레코드가 등록되었는지 확인해야 합니다.

    ![Google\_Tutorial\_13][]

## Google Apps에서 사용자 지정 도메인을 추가하려면

1.  Google Apps 테넌트에 로그온합니다.
2.  **관리 콘솔**에서 **도메인**을 클릭합니다.

    ![Google\_Tutorial\_14][]

3.  **사용자 지정 도메인 추가**를 클릭합니다.

    ![Google\_Tutorial\_15][]

4.  **이미 소유한 도메인 사용**을 클릭한 후 **계속**을 클릭합니다.

    ![Google\_Tutorial\_16][]

5.  사용자 지정 도메인의 이름을 입력하고 **계속**을 클릭합니다.

    ![Google\_Tutorial\_17][]

6.  도메인의 소유권을 확인하는 단계를 완료합니다.

    페더레이션 Single Sign-On이 이미 구성되어 있는 경우 페더레이션 Single Sign-On 구성에서 Google Apps 테넌트 URL을 업데이트해야 합니다.

# 사용자 프로비전 구성

이 섹션은 Google Apps에 Active Directory 사용자 계정을 프로비전할 수 있도록 설정하는 방법을 간략하게 설명하기 위한 것입니다.

## 사용자 프로비전을 구성하려면

1.  Azure 관리 포털의 왼쪽 탐색 창에서 **Active Directory**를 선택하여 **Active Directory** 대화 상자 페이지를 엽니다.
2.  디렉터리 목록에서 해당 디렉터리를 선택하여 디렉터리의 구성 페이지를 엽니다.
3.  최상위 메뉴에서 **응용 프로그램**을 선택합니다.
    응용 프로그램 목록에서 **Google Apps**를 선택하여 **Google Apps** 구성 대화 상자를 엽니다.
4.  **계정 동기화 구성** 대화 상자를 열려면 **계정 동기화 구성**을 클릭합니다.
5.  **계정 동기화 구성** 대화 상자에서 Google Apps 도메인 이름, Google Apps 사용자 이름 및 Google Apps 암호를 제공하고 **다음** 단추를 클릭합니다.

    ![Google\_Tutorial\_18][]

6.  **확인** 대화 상자 페이지에서 **완료** 단추를 클릭하여 **계정 동기화 구성** 대화 상자를 닫습니다.

이제 테스트 계정을 만들고 10분 동안 기다린 후 계정이 Google Apps에 동기화되었는지 확인할 수 있습니다.


  [Google\_Tutorial\_01]: ./media/integration-azure-google-apps/Google_Tutorial_01.png
  [Google\_Tutorial\_02]: ./media/integration-azure-google-apps/Google_Tutorial_02.png
  [Google\_Tutorial\_03]: ./media/integration-azure-google-apps/Google_Tutorial_03.png
  [Google\_Tutorial\_04]: ./media/integration-azure-google-apps/Google_Tutorial_04.png
  [Google\_Tutorial\_05]: ./media/integration-azure-google-apps/Google_Tutorial_05.png
  [Google\_Tutorial\_06]: ./media/integration-azure-google-apps/Google_Tutorial_06.png
  [Google\_Tutorial\_07]: ./media/integration-azure-google-apps/Google_Tutorial_07.png
  [Google\_Tutorial\_08]: ./media/integration-azure-google-apps/Google_Tutorial_08.png
  [액세스 패널]: https://login.microsoftonline.com/login.srf?wa=wsignin1.0&rpsnv=2&ct=1384289458&rver=6.1.6206.0&wp=MCMBI&wreply=https:%2F%2Faccount.activedirectory.windowsazure.com%2Flanding.aspx%3Ftarget%3D%252fapplications%252fdefault.aspx&lc=1033&id=500633
  [Google\_Tutorial\_09]: ./media/integration-azure-google-apps/Google_Tutorial_09.png
  [Google\_Tutorial\_10]: ./media/integration-azure-google-apps/Google_Tutorial_10.png
  [Google\_Tutorial\_11]: ./media/integration-azure-google-apps/Google_Tutorial_11.png
  [Google\_Tutorial\_12]: ./media/integration-azure-google-apps/Google_Tutorial_12.png
  [Google\_Tutorial\_13]: ./media/integration-azure-google-apps/Google_Tutorial_13.png
  [Google\_Tutorial\_14]: ./media/integration-azure-google-apps/Google_Tutorial_14.png
  [Google\_Tutorial\_15]: ./media/integration-azure-google-apps/Google_Tutorial_15.png
  [Google\_Tutorial\_16]: ./media/integration-azure-google-apps/Google_Tutorial_16.png
  [Google\_Tutorial\_17]: ./media/integration-azure-google-apps/Google_Tutorial_17.png
  [Google\_Tutorial\_18]: ./media/integration-azure-google-apps/Google_Tutorial_18.png

<p><a href="http://social.technet.microsoft.com/wiki/contents/articles/18409.best-practices-for-managing-the-application-access-enhancements-for-windows-azure-active-directory.aspx">Azure Active Directory 응용 프로그램 액세스에 대한 응용 프로그램 액세스 향상 기능을 관리하기 위한 모범 사례</a>도<br />참조하세요.</p>
