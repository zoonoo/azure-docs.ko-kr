---
title: "Azure Active Directory 장치 등록을 사용하여 온-프레미스 조건부 액세스 설정 | Microsoft Docs"
description: "Windows Server 2012 R2에서 AD FS(Active Directory Federation Service)를 사용하여 온-프레미스 응용 프로그램에 대한 조건부 액세스를 사용하도록 설정하는 단계별 가이드입니다."
services: active-directory
documentationcenter: 
author: femila
manager: femila
editor: 
ms.assetid: 6ae9df8b-31fe-4d72-9181-cf50cfebbf05
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: f42e31c99bf43e8f034c8d6f4f98d777661c3d81
ms.openlocfilehash: 204ec83dc1000bb1e10df6fecc303b6ac7795739
ms.lasthandoff: 02/23/2017


---
# <a name="setting-up-on-premises-conditional-access-using-azure-active-directory-device-registration"></a>Azure Active Directory Device Registration을 사용하여 온-프레미스 조건부 액세스 설정
사용자가 작업 공간 연결을 통해 Azure Active Directory Device Registration 서비스에 장치를 등록하도록 요구하여 사용자의 개인 소유 장치를 조직에 알려지도록 표시할 수 있습니다. 다음은 Windows Server 2012 R2에서 AD FS(Active Directory Federation Service)를 사용하여 온-프레미스 응용 프로그램에 대한 조건부 액세스를 사용하도록 설정하는 단계별 가이드입니다.

> [!NOTE]
> Azure Active Directory Device Registration 서비스 조건부 액세스 정책에 등록된 장치를 사용하는 경우 Office 365 라이선스 또는 Azure AD Premium 라이선스가 필요합니다. 여기에는 AD FS(Active Directory Federation Services)에 의해 온-프레미스 리소스에 적용되는 정책이 포함됩니다.
> 
> 

온-프레미스의 조건부 액세스 시나리오에 대한 자세한 내용은 [회사 응용 프로그램에 대한 SSO 및 원활한 두 번째 인증 단계를 위해 임의 장치에서 작업 공간 연결](https://technet.microsoft.com/library/dn280945.aspx)을 참조하세요.

이러한 기능은 Azure Active Directory Premium 라이선스를 구입한 고객에게 제공됩니다.

## <a name="supported-devices"></a>지원되는 장치
* Windows 7 도메인 가입 장치
* Windows 8.1 개인 및 도메인 가입 장치
* Safari 브라우저의 경우 iOS 6 이상
* Android 4.0 이상, Samsung GS3 이상 휴대폰, Samsung Note2 이상 태블릿

## <a name="scenario-prerequisites"></a>시나리오 필수 조건
* Office 365 또는 Azure Active Directory Premium 구독
* Azure Active Directory 테넌트
* Windows Server Active Directory(Windows Server 2008 이상)
* Windows Server 2012 R2에서 업데이트된 스키마
* Azure Active Directory Premium에 대한 라이선스
* Azure AD에 대해 SSO로 구성된 Windows Server 2012 R2 페더레이션 서비스
* Windows Server 2012 R2 웹 응용 프로그램 프록시 Microsoft Azure Active Directory Connect(Azure AD Connect). [여기에서 Azure AD Connect를 다운로드하세요](http://www.microsoft.com/en-us/download/details.aspx?id=47594).
* 확인된 도메인.

## <a name="known-issues-in-this-release"></a>이 릴리스의 알려진 문제
* 장치 기반 조건부 액세스 정책을 사용하려면 Azure Active Directory에서 Active Directory로 장치 개체를 쓰기 저장해야 합니다. Active Directory에 장치 개체를 다시 쓰기 저장하는 데 최대 3시간이 걸릴 수 있습니다.
* iOS 7 장치는 항상 클라이언트 인증서 인증 도중 인증서를 선택하라는 메시지를 사용자에게 표시합니다.
* iOS8의 일부 버전, iOS 8.3 버전은 작동하지 않습니다.

## <a name="scenario-assumptions"></a>시나리오의 가정
이 시나리오는 사용자에게 Azure AD 테넌트와 온-프레미스 Active Directory로 구성된 하이브리드 환경이 있다고 가정합니다. 이러한 테넌트는 Azure AD Connect를 사용하여 연결되어야 하며 확인된 도메인과 SSO용 AD FS도 필요합니다. 아래 검사 목록을 통해 위에서 설명한 상태로 환경이 구성되었는지 확인해 보세요.

## <a name="checklist-prerequisites-for-conditional-access-scenario"></a>검사 목록: 조건부 액세스 시나리오를 위한 필수 구성 요소
온-프레미스 Active Directory로 Azure AD 테넌트를 연결합니다.

## <a name="configure-azure-active-directory-device-registration-service"></a>Azure Active Directory Device Registration 서비스 구성
이 가이드를 사용하여 조직에 맞게 Azure Active Directory Device Registration 서비스를 배포하고 구성할 수 있습니다.

이 가이드에서는 Windows Server Active Directory를 구성했으며 Microsoft Azure Active Directory를 구독한다고 가정합니다. 위의 필수 조건을 참조하세요.

Azure Active Directory Device Registration 서비스 및 Azure Active Directory 테넌트를 배포하려면 다음 검사 목록의 작업을 순서대로 완료합니다. 참조 링크를 통해 개념 항목으로 이동하는 경우 개념 항목을 검토한 후 이 검사 목록으로 돌아와서 이 검사 목록의 나머지 태스크를 계속할 수 있습니다. 일부 작업에는 단계가 성공적으로 완료되었는지 확인하는 데 도움이 되는 시나리오 유효성 검사 단계가 포함됩니다.

## <a name="part-1-enable-azure-active-directory-device-registration"></a>1부: Azure Active Directory Device Registration을 사용하도록 설정
아래 검사 목록에 따라 Azure Active Directory Device Registration 서비스를 사용하도록 설정하고 구성합니다.

| 작업 | 참조 |
| --- | --- |
| Azure Active Directory 테넌트에서 장치 등록을 사용하도록 설정하여 장치가 작업 공간에 연결할 수 있게 합니다. 기본적으로 다단계 인증은 서비스에 대해 사용되지 않습니다. 그러나 장치를 등록하는 경우 다단계 인증을 사용하는 것이 좋습니다. ADRS에서 다단계 인증을 사용하도록 설정하기 전에 다단계 인증 공급자에 대해 AD FS가 구성되었는지 확인합니다. |[Azure Active Directory Device Registration 사용](active-directory-conditional-access-device-registration-overview.md) |
| 장치는 잘 알려진 DNS 레코드를 찾아 Azure Active Directory Device Registration 서비스를 검색합니다. 장치가 Azure Active Directory Device Registration 서비스를 검색할 수 있도록 회사 DNS를 구성해야 합니다. |[Azure Active Directory Device Registration 검색 구성](active-directory-conditional-access-device-registration-overview.md) |

## <a name="part-2-deploy-and-configure-windows-server-2012-r2-active-directory-federation-services-and-set-up-a-federation-relationship-with-azure-ad"></a>2부: Windows Server 2012 R2 Active Directory Federation Services를 배포 및 구성하고 Azure AD와의 페더레이션 관계를 설정합니다.
| 작업 | 참조 |
| --- | --- |
| Windows Server 2012 R2 스키마 확장을 사용하여 Active Directory 도메인 서비스 도메인을 배포합니다. 도메인 컨트롤러는 Windows Server 2012 R2로 업그레이드할 필요가 없습니다. 스키마 업그레이드만 수행하면 됩니다. |[Active Directory 도메인 서비스 스키마 업그레이드](#upgrade-your-active-directory-domain-services-schema) |
| 장치는 잘 알려진 DNS 레코드를 찾아 Azure Active Directory Device Registration 서비스를 검색합니다. 장치가 Azure Active Directory Device Registration 서비스를 검색할 수 있도록 회사 DNS를 구성해야 합니다. |[Active Directory 지원 장치 준비](#prepare-your-active-directory-to-support-devices) |

## <a name="part-3-enable-device-writeback-in-azure-ad"></a>3부: Azure AD에서 장치 쓰기 저장 사용
| 작업 | 참조 |
| --- | --- |
| Azure AD Connect에서 장치 쓰기 저장 사용의 2부를 완료합니다. 완료되면 이 가이드로 돌아옵니다. |[Azure AD Connect에서 장치 쓰기 저장 사용](#upgrade-your-active-directory-domain-services-schema) |

## <a name="optional-part-4-enable-multi-factor-authentication"></a>[선택 사항] 4부: Multi-Factor Authentication 사용
Multi-Factor Authentication을 위한 여러 옵션 중 하나를 구성하는 것이 좋습니다. MFA가 필요하도록 하려면 [다단계 보안 솔루션 선택](../multi-factor-authentication/multi-factor-authentication-get-started.md)을 참조하세요. 여기에는 각 솔루션에 대한 설명, 선택한 솔루션을 구성하는 데 도움이 되는 링크가 포함되어 있습니다.

## <a name="part-5-verification"></a>5부: 확인
이제 배포가 완료되었습니다. 몇 가지 시나리오를 시도해 볼 수 있습니다. 아래 링크를 따라 서비스를 시험해 보고 기능을 익혀 보십시오.

| 작업 | 참조 |
| --- | --- |
| Azure Active Directory Device Registration을 사용하여 작업 공간에 몇몇 장치를 연결합니다. iOS, Windows 및 Android 장치를 연결할 수 있습니다. |[Azure Active Directory Device Registration을 사용하여 작업 공간에 장치 연결](#join-devices-to-your-workplace-using-azure-active-directory-device-registration) |
| 관리자 포털을 사용하여 등록된 장치를 보고 사용/사용하지 않도록 설정할 수 있습니다. 이 작업에서는 관리자 포털을 사용하여 등록된 일부 장치를 살펴봅니다. |[Azure Active Directory Device Registration 개요](active-directory-conditional-access-device-registration-overview.md) |
| Azure Active Directory에서 Windows Server Active Directory로 장치 개체가 다시 기록되었는지 확인합니다. |[등록된 장치가 Active Directory에 쓰기 저장되는지 확인](#verify-registered-devices-are-written-back-to-active-directory) |
| 이제 사용자가 장치를 등록할 수 있으므로 등록된 장치만 허용하도록 AD FS에서 응용 프로그램 액세스 정책을 만들 수 있습니다. 이 작업에서는 응용 프로그램 액세스 규칙과 사용자 지정 액세스 거부 메시지를 만들게 됩니다. |[응용 프로그램 액세스 정책 및 사용자 지정 액세스 거부 메시지 만들기](#create-an-application-access-policy-and-custom-access-denied-message) |

## <a name="integrate-azure-active-directory-with-on-premises-active-directory"></a>온-프레미스 Active Directory와 Azure Active Directory 통합
이렇게 하면 Azure AD Connect를 사용하여 온-프레미스 Active Directory와 Azure AD 테넌트를 통합하는 데 도움이 됩니다. 이 단계는 Azure 클래식 포털에도 설명되어 있으나 이 섹션에서 나열한 특별 지침을 참조하십시오.

1. Azure AD의 전역 관리자인 계정을 사용하여 Azure 클래식 포털에 로그인합니다.
2. 왼쪽 창에서 **Active Directory**를 선택합니다.
3. **디렉터리** 탭에서 해당 디렉터리를 선택합니다.
4. **디렉터리 통합** 탭을 선택합니다.
5. **배포 및 관리** 섹션에서 1-3단계에 따라 온-프레미스 디렉터리와 Azure Active Directory를 통합합니다.
   
   1. 도메인을 추가합니다.
   2. Azure AD Connect 설치 및 실행: [Azure AD Connect의 사용자 지정 설치](connect/active-directory-aadconnect-get-started-custom.md) 지침을 사용하여 Azure AD Connect를 설치합니다.
   3. 디렉터리 동기화를 확인하고 관리합니다. 이 단계에는 SSO(Single Sign-On) 지침이 포함되어 있습니다.
   
   > [!NOTE]
   > 아래 링크의 문서에 설명된 대로 AD FS와의 페더레이션을 구성합니다. 미리 보기 기능은 구성할 필요 없습니다.
   > 
   > 

## <a name="upgrade-your-active-directory-domain-services-schema"></a>Active Directory 도메인 서비스 스키마 업그레이드
> [!NOTE]
> Active Directory 스키마 업그레이드는 취소할 수 없습니다. 먼저 테스트 환경에서 이 작업을 수행하는 것이 좋습니다.
> 
> 

1. Enterprise Admin 및 Schema Admin 권한이 둘 다 있는 계정으로 도메인 컨트롤러에 로그인합니다.
2. **[media]\support\adprep** 디렉터리와 하위 디렉터리를 Active Directory 도메인 컨트롤러 중 하나로 복사합니다.
3. 여기서 [media]는 Windows Server 2012 R2 설치 미디어의 경로입니다.
4. 명령 프롬프트에서 adprep 디렉터리로 이동한 다음 **adprep.exe /forestprep**을 실행합니다. 화면의 지시에 따라 스키마 업그레이드를 완료합니다.

## <a name="prepare-your-active-directory-to-support-devices"></a>장치를 지원하도록 Active Directory 준비
> [!NOTE]
> 장치를 지원하도록 Active Directory 포리스트를 준비하기 위해 실행해야 하는 일회성 작업입니다. 이 절차를 완료하려면 엔터프라이즈 관리자 권한으로 로그온해야 하며 Active Directory 포리스트에 Windows Server 2012 R2 스키마가 있어야 합니다.
> 
> 

## <a name="prepare-your-active-directory-forest-to-support-devices"></a>장치를 지원하도록 Active Directory 포리스트 준비
> [!NOTE]
> 장치를 지원하도록 Active Directory 포리스트를 준비하기 위해 실행해야 하는 일회성 작업입니다. 이 절차를 완료하려면 엔터프라이즈 관리자 권한으로 로그온해야 하며 Active Directory 포리스트에 Windows Server 2012 R2 스키마가 있어야 합니다.
> 
> 

### <a name="prepare-your-active-directory-forest"></a>Active Directory 포리스트 준비
1. 페더레이션 서버에서 Windows PowerShell 명령 창을 열고 Initialize-addeviceregistration을 입력합니다.
2. **ServiceAccountName**에 대한 메시지가 표시되면 AD FS에 대한 서비스 계정으로 선택한 서비스 계정의 이름을 입력합니다. gMSA 계정인 경우 **domain\accountname$** 형식으로 계정을 입력합니다. 도메인 계정의 경우 **domain\accountname** 형식을 사용합니다.

### <a name="enable-device-authentication-in-ad-fs"></a>AD FS에서 장치 인증 사용
1. 페더레이션 서버에서 AD FS 관리 콘솔을 열고 **AD FS** > **인증 정책**으로 이동합니다.
2. **전역 기본 인증 편집...**을 선택합니다. from the **전역 기본 인증 편집...** 을 선택합니다.
3. **장치 인증 사용**을 선택한 다음 **확인**을 선택합니다.
4. 기본적으로 AD FS는 Active Directory에서 사용하지 않은 장치를 주기적으로 제거합니다. Azure Active Directory Device Registration을 사용하는 경우 이 작업을 사용하지 않도록 설정하여 Azure에서 장치를 관리할 수 있게 해야 합니다.

### <a name="disable-unused-device-cleanup"></a>사용하지 않은 장치 정리 사용 안 함
1. 페더레이션 서버에서 Windows PowerShell 명령 창을 열고 Set-AdfsDeviceRegistration -MaximumInactiveDays 0을 입력합니다.

### <a name="prepare-azure-ad-connect-for-device-writeback"></a>장치 쓰기 저장을 위해 Azure AD Connect 준비
1. 1부 완료: Azure AD Connect 준비

## <a name="join-devices-to-your-workplace-using-azure-active-directory-device-registration"></a>Azure Active Directory Device Registration을 사용하여 작업 공간에 장치 연결
### <a name="join-an-ios-device-using-azure-active-directory-device-registration"></a>Azure Active Directory Device Registration을 사용하여 iOS 장치 연결
Azure Active Directory Device Registration은 iOS 장치에 대해 무선 프로필 등록 프로세스를 사용합니다. 이 프로세스는 사용자가 Safari 웹 브라우저를 사용하여 프로필 등록 URL에 연결하는 작업으로 시작됩니다. URL 형식은 다음과 같습니다.

    https://enterpriseregistration.windows.net/enrollmentserver/otaprofile/"yourdomainname"

여기서 `yourdomainname` 은 Azure Active Directory를 사용하여 구성한 도메인 이름입니다. 예를 들어 도메인 이름이 contoso.com인 경우 URL은 다음과 같습니다.

    https://enterpriseregistration.windows.net/enrollmentserver/otaprofile/contoso.com

다양한 방법으로 이 URL을 사용자에게 알릴 수 있습니다. 한 가지 권장 방법은 AD FS의 사용자 지정 응용 프로그램 액세스 거부 메시지에 이 URL을 게시하는 것입니다. 이 내용은 이후 섹션: [응용 프로그램 액세스 정책 및 사용자 지정 액세스 거부 메시지 만들기](#create-an-application-access-policy-and-custom-access-denied-message)에서 설명합니다.

### <a name="join-a-windows-81-device-using-azure-active-directory-device-registration"></a>Azure Active Directory Device Registration을 사용하여 Windows 8.1 장치 연결
1. Windows 8.1 장치에서 **PC 설정** > **네트워크** > **작업 공간**으로 이동합니다.
2. 사용자 이름을 UPN 형식으로 입력합니다. 예: dan@contoso.com.
3. **연결**을 선택합니다.
4. 메시지가 표시되면 자격 증명을 사용하여 로그인합니다. 이제 장치가 연결되었습니다.

### <a name="join-a-windows-7-device-using-azure-active-directory-device-registration"></a>Azure Active Directory Device Registration을 사용하여 Windows 7 장치 연결
Windows 7 도메인 가입 장치를 등록하려면 장치 등록 소프트웨어 패키지를 배포해야 합니다. 이 소프트웨어 패키지를 Windows 7용 작업 공간 연결이라고 하며 [Microsoft Connect 웹 사이트](https://connect.microsoft.com/site1164)에서 다운로드할 수 있습니다. 패키지를 사용하는 방법에 대한 지침은 [Windows 7 도메인 가입 장치에 대한 자동 장치 등록 구성](active-directory-conditional-access-automatic-device-registration-windows7.md)을 참조하세요.

## <a name="verify-registered-devices-are-written-back-to-active-directory"></a>등록된 장치가 Active Directory에 쓰기 저장되는지 확인
LDP.exe 또는 ADSI 편집을 사용하여 장치 개체가 Active Directory에 쓰기 저장되었는지 보고 확인할 수 있습니다. 둘 다 Active Directory 관리자 도구에서 사용할 수 있습니다.

기본적으로 Azure Active Directory에서 쓰기 저장된 장치 개체는 AD FS 팜과 동일한 도메인에 배치됩니다.

    CN=RegisteredDevices,defaultNamingContext

## <a name="create-an-application-access-policy-and-custom-access-denied-message"></a>응용 프로그램 액세스 정책 및 사용자 지정 액세스 거부 메시지 만들기
다음 시나리오를 고려합니다. AD FS에서 응용 프로그램 신뢰 당사자 트러스트를 만들고 등록된 장치만 허용하는 발급 권한 부여 규칙을 구성합니다. 이제 등록된 장치만 응용 프로그램에 액세스할 수 있습니다. 사용자가 응용 프로그램에 쉽게 액세스할 수 있게 하려면 장치를 연결하는 방법에 대한 지침을 포함하는 사용자 지정 액세스 거부 메시지를 구성합니다. 이제 사용자가 응용 프로그램에 액세스하기 위해 해당 장치를 원활하게 등록할 수 있습니다.

다음 단계는 이 시나리오를 구현하는 방법을 보여 줍니다.

> [!NOTE]
> 이 섹션에서는 AD FS에서 응용 프로그램에 대한 신뢰 당사자 트러스트를 이미 구성했다고 가정합니다.
> 
> 

1. AD FS MMC 도구를 열고 AD FS > 트러스트 관계 > 신뢰 당사자 트러스트로 이동합니다.
2. 이 새로운 액세스 규칙을 적용할 응용 프로그램을 찾습니다. 응용 프로그램을 마우스 오른쪽 단추로 클릭하고 클레임 규칙 편집...을 선택합니다.
3. **발급 권한 부여 규칙** 탭을 선택한 다음 **규칙 추가...**를 선택합니다.
4. **클레임 규칙** 템플릿 드롭다운 목록에서 **들어오는 클레임에 따라 사용자 허용 또는 거부**를 선택합니다. **다음**을 선택합니다.
5. 클레임 규칙 이름: 필드에 **등록된 장치에서 액세스 허용**
6. 들어오는 클레임 유형: 드롭다운 목록에서 **등록된 사용자**를 선택합니다.
7. 들어오는 클레임 값: 필드에 **true**
8. **이 들어오는 클레임의 사용자에 대한 액세스 허용** 라디오 단추를 선택합니다.
9. **마침**을 선택한 다음 **적용**을 선택합니다.
10. 방금 만든 규칙보다 더 허용되는 규칙을 모두 제거합니다. 예를 들어 기본값인 **모든 사용자에 게 액세스 허용** 규칙을 제거합니다.

이제 응용 프로그램이 사용자가 작업 공간에 등록 및 연결한 장치에서 들어오는 경우에만 액세스를 허용하도록 구성되었습니다. 고급 액세스 정책의 경우 [다단계 액세스 제어를 사용하여 위험 관리](https://technet.microsoft.com/library/dn280949.aspx)를 참조하세요.

다음에는 응용 프로그램에 대한 사용자 지정 오류 메시지를 구성합니다. 오류 메시지를 통해 사용자는 장치를 작업 공간에 연결해야 응용 프로그램에 액세스할 수 있음을 알게 됩니다. 사용자 지정 HTML 및 Windows PowerShell을 사용하여 사용자 지정 응용 프로그램 액세스 거부 메시지를 만들 수 있습니다.

페더레이션 서버에서 Windows PowerShell 명령 창을 열고 다음 명령을 입력합니다. 명령 부분을 시스템과 관련된 항목으로 바꿉니다.

    Set-AdfsRelyingPartyWebContent -Name "relying party trust name" -ErrorPageAuthorizationErrorMessage
이 응용 프로그램에 액세스하려면 먼저 장치를 등록해야 합니다.

**iOS 장치를 사용하는 경우 다음 링크를 선택하여 장치를 연결합니다**.

    a href='https://enterpriseregistration.windows.net/enrollmentserver/otaprofile/yourdomain.com

이 iOS 장치를 작업 공간에 연결합니다.

**Windows 8.1 장치를 사용하는 경우** **PC 설정**> **네트워크** > **작업 공간**으로 이동하여 장치를 연결할 수 있습니다.

여기서 "**신뢰 당사자 트러스트 이름**"은 AD FS에서 응용 프로그램 신뢰 당사자 트러스트 개체의 이름입니다.
여기서 **yourdomain.com** 은 Azure Active Directory를 사용하여 구성한 도메인 이름입니다. 예를 들어 contoso.com입니다.
**Set-AdfsRelyingPartyWebContent** cmdlet에 전달하는 html 콘텐츠에서 줄 바꿈(있는 경우)을 모두 제거해야 합니다.

이제 사용자가 Azure Active Directory Device Registration 서비스로 등록되지 않은 장치에서 응용 프로그램에 액세스하면 아래 스크린샷과 비슷한 페이지가 표시됩니다.

![사용자가 Azure AD로 자신의 장치를 등록하지 않은 경우 표시되는 오류의 스크린샷](./media/active-directory-conditional-access/error-azureDRS-device-not-registered.gif)

## <a name="related-articles"></a>관련 문서
* [Azure Active Directory의 응용 프로그램 관리를 위한 문서 인덱스](active-directory-apps-index.md)


