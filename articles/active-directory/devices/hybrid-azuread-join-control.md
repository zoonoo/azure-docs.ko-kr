---
title: 하이브리드 Azure AD 조인-Azure AD의 제어 된 유효성 검사
description: 한 번에 전체 조직에서 사용 하기 전에 하이브리드 Azure AD join의 제어 된 유효성 검사를 수행 하는 방법에 알아봅니다.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: article
ms.date: 05/30/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: cd5b388f92a875fb2635037a6eae3ff3b6a95793
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66513282"
---
# <a name="controlled-validation-of-hybrid-azure-ad-join"></a>하이브리드 Azure AD 조인의 제어된 유효성 검사

사전 요구 사항이 적으면 서 모두 준비에서 되 면 Windows 장치를 Azure AD 테 넌 트에서 장치로 자동으로 등록 됩니다. Azure AD에서 이러한 장치 id의 상태를 하이브리드 Azure AD 조인 이라고 합니다. 이 문서에서 설명 된 개념에 대 한 자세한 문서에서 찾을 수 있습니다 [Azure Active Directory의 장치 관리 소개](overview.md) 고 [하이브리드 Azure Active Directory 조인 구현 계획 ](hybrid-azuread-join-plan.md).

조직이 제어 유효성 검사를 하이브리드 Azure AD 조인 중 한 번에 전체 조직에서 사용 하기 전에 할 수 있습니다. 이 문서에서는 하이브리드 Azure AD join의 제어 된 유효성 검사를 수행 하는 방법을 설명 합니다.

## <a name="controlled-validation-of-hybrid-azure-ad-join-on-windows-current-devices"></a>Windows 현재 장치의 하이브리드 Azure AD join의 제어 된 유효성 검사

Windows 데스크톱 운영 체제를 실행하는 디바이스의 경우 지원되는 버전은 Windows 10주년 업데이트(버전 1607) 이상입니다. 가장 좋은 방법은 최신 버전의 Windows 10으로 업그레이드하는 것입니다.

Windows 현재 장치의 하이브리드 Azure AD join의 제어 된 유효성 검사를 수행 하려면을 지정 해야 합니다.

1. 있는 경우 Active Directory (AD)에서 서비스 연결 지점 (SCP) 항목을 제거 합니다.
1. 그룹 정책 개체 (GPO)를 사용 하 여 도메인에 가입 된 컴퓨터에 SCP에 대 한 클라이언트 쪽 레지스트리 설정 구성
1. SCP에 대 한 클라이언트 쪽 레지스트리 설정을 GPO를 사용 하 여 AD FS 서버에도 구성 해야 AD FS를 사용 하는 경우  



### <a name="clear-the-scp-from-ad"></a>AD에서 SCP를 지우기

(ADSI 편집) Active Directory 서비스 인터페이스 편집기를 사용 하 여의 AD SCP 개체는 수정할 수 있습니다.

1. 시작 합니다 **ADSI 편집** 에서 데스크톱 응용 프로그램 및 관리 워크스테이션 또는 엔터프라이즈 관리자로 도메인 컨트롤러입니다.
1. 에 연결 합니다 **구성 명명 컨텍스트** 도메인입니다.
1. 이동할 **CN = Configuration, DC = contoso, DC = com** > **CN = Services** > **CN = Device Registration Configuration**
1. 아래에 있는 리프 개체를 마우스 오른쪽 단추로 클릭 **CN = Device Registration Configuration** 선택한 **속성**
   1. 선택 **키워드** 에서 합니다 **특성 편집기** 창과 클릭 **편집**
   1. 값을 선택할 **azureADId** 하 고 **azureADName** (한 번에 하나씩)를 클릭 하 고 **제거**
1. 닫기 **ADSI 편집**


### <a name="configure-client-side-registry-setting-for-scp"></a>SCP에 대 한 클라이언트 쪽 레지스트리 설정을 구성 합니다.

장치 레지스트리에 SCP 항목 구성 레지스트리 설정을 배포 하려면 그룹 정책 개체 (GPO)를 만들려면 다음 예제를 사용 합니다.

1. 그룹 정책 관리 콘솔을 열고 도메인에서 새 그룹 정책 개체를 만듭니다.
   1. 새로 만든된 GPO 이름 (예를 들어 ClientSideSCP)를 제공 합니다.
1. GPO를 편집 하 고 다음 경로 찾습니다. **컴퓨터 구성** > **기본 설정** > **Windows 설정** > **레지스트리**
1. 선택한 레지스트리를 마우스 오른쪽 단추로 클릭 **새로 만들기** > **레지스트리 항목**
   1. 에 **일반** 탭에서 다음을 구성 합니다.
      1. 작업: **업데이트**
      1. Hive: **HKEY_LOCAL_MACHINE**
      1. 키 경로: **SOFTWARE\Microsoft\Windows\CurrentVersion\CDJ\AAD**
      1. 값 이름: **TenantId**
      1. 값 종류: **REG_SZ**
      1. 방법 2 GUID 또는 **디렉터리 ID** Azure AD 인스턴스 (이 값에서 찾을 수 있습니다 합니다 **Azure portal** > **Azure Active Directory**  >   **속성** > **디렉터리 ID**)
   1. **확인**을 클릭합니다.
1. 선택한 레지스트리를 마우스 오른쪽 단추로 클릭 **새로 만들기** > **레지스트리 항목**
   1. 에 **일반** 탭에서 다음을 구성 합니다.
      1. 작업: **업데이트**
      1. Hive: **HKEY_LOCAL_MACHINE**
      1. 키 경로: **SOFTWARE\Microsoft\Windows\CurrentVersion\CDJ\AAD**
      1. 값 이름: **TenantName**
      1. 값 종류: **REG_SZ**
      1. 방법 2 확인 프로그램 **도메인 이름** Azure AD에서 (예를 들어 `contoso.onmicrosoft.com` 또는 디렉터리에 다른 확인 된 도메인 이름)
   1. **확인**을 클릭합니다.
1. 새로 만든된 GPO에 대 한 편집기를 닫습니다
1. 새로 만든된 GPO를 제어 된 롤아웃 인구에 속해 있는 도메인에 가입 된 컴퓨터를 포함 하 여 원하는 OU에 연결

### <a name="configure-ad-fs-settings"></a>AD FS 설정을 구성 합니다.

AD FS를 사용 하는 경우에 먼저 AD FS 서버에 GPO를 연결 하지만 위에서 언급 한 지침을 사용 하는 클라이언트 쪽 SCP를 구성 해야 합니다. 이 구성은 AD fs와 Azure AD 장치 id에 대 한 소스를 설정 하려면 필요 합니다.

## <a name="controlled-validation-of-hybrid-azure-ad-join-on-windows-down-level-devices"></a>Windows 하위 수준 장치의 하이브리드 Azure AD join의 제어 된 유효성 검사

Windows 하위 수준 장치를 등록 하려면 회사에 설치 해야 합니다 [비 Windows 10 컴퓨터에 대 한 Microsoft Workplace Join](https://www.microsoft.com/download/details.aspx?id=53554) Microsoft 다운로드 센터에서 사용할 수 있습니다.

와 같은 소프트웨어 배포 시스템을 사용 하 여 패키지를 배포할 수 있습니다 [System Center Configuration Manager](https://www.microsoft.com/cloud-platform/system-center-configuration-manager)합니다. 패키지는 quiet 매개 변수를 사용하여 표준 자동 설치 옵션을 지원합니다. 구성 관리자의 현재 분기는 완료된 등록을 추적하는 기능과 같은 이전 버전보다 나은 이점이 추가로 제공됩니다.

설치 관리자는 사용자 컨텍스트에서 실행 되는 시스템에서 예약 된 작업을 만듭니다. 사용자가 Windows에 로그인할 때 이 작업이 트리거됩니다. 이 작업은 Azure AD에서 인증을 받은 후 사용자 자격 증명으로 Azure AD에 디바이스를 자동으로 가입합니다.

장치 등록을 제어 하려면 Windows 하위 수준 장치 선택한 그룹에 Windows Installer 패키지를 배포 해야 합니다.

> [!NOTE]
> AD에서 SCP를 구성 되지 않은 경우에 설명 된 대로 동일한 접근 방식을 따라야 [SCP에 대 한 클라이언트 쪽 레지스트리 설정을 구성](#configure-client-side-registry-setting-for-scp)) 그룹 정책 개체 (GPO)를 사용 하 여 도메인에 가입 된 컴퓨터.


Windows 현재 및 하위 수준 장치의 나머지 부분에서 Azure AD를 사용 하 여 자동으로 등록할 수, 모든 항목이 예상 대로 작동 하는지 확인 한 후 [Azure AD Connect를 사용 하 여 SCP를 구성](hybrid-azuread-join-managed-domains.md#configure-hybrid-azure-ad-join)합니다.

## <a name="next-steps"></a>다음 단계

[하이브리드 Azure Active Directory 조인 구현 계획](hybrid-azuread-join-plan.md)
