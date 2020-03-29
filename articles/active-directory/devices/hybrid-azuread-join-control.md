---
title: 하이브리드 Azure AD 조인의 제어된 유효성 검사 - Azure AD
description: 전체 조직에서 한 번에 활성화하기 전에 하이브리드 Azure AD 조인에 대한 제어된 유효성 검사를 수행하는 방법을 알아봅니다.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: f43db805ccbb7d4e546c51bbe39350f4bbba2efb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80049990"
---
# <a name="controlled-validation-of-hybrid-azure-ad-join"></a>하이브리드 Azure AD 조인의 제어된 유효성 검사

모든 필수 구성 조건이 준비되면 Windows 장치는 Azure AD 테넌트의 장치로 자동으로 등록됩니다. Azure AD에서 이러한 장치 ID의 상태를 하이브리드 Azure AD 조인이라고 합니다. 이 문서에서 다루는 개념에 대한 자세한 내용은 [Azure Active Directory의 장치 관리 소개](overview.md) 및 하이브리드 Azure Active [Directory 조인 구현 계획](hybrid-azuread-join-plan.md)문서에서 확인할 수 있습니다.

조직은 전체 조직에서 한 번에 활성화하기 전에 하이브리드 Azure AD 조인에 대한 제어된 유효성 검사를 수행할 수 있습니다. 이 문서에서는 하이브리드 Azure AD 조인의 제어된 유효성 검사를 수행하는 방법을 설명합니다.

## <a name="controlled-validation-of-hybrid-azure-ad-join-on-windows-current-devices"></a>Windows 현재 장치에서 하이브리드 Azure AD 조인의 제어된 유효성 검사

Windows 데스크톱 운영 체제를 실행하는 디바이스의 경우 지원되는 버전은 Windows 10주년 업데이트(버전 1607) 이상입니다. 가장 좋은 방법은 최신 버전의 Windows 10으로 업그레이드하는 것입니다.

Windows 현재 장치에서 하이브리드 Azure AD 조인에 대한 제어된 유효성 검사를 수행하려면 다음을 수행해야 합니다.

1. 서비스 연결 지점이 있는 경우 AD(활성 디렉터리)에서 서비스 연결 지점(SCP) 항목을 지웁니다.
1. GPO(그룹 정책 개체)를 사용하여 도메인 에 가입한 컴퓨터에서 SCP에 대한 클라이언트 측 레지스트리 설정 구성
1. AD FS를 사용하는 경우 GPO를 사용하여 AD FS 서버에서 SCP에 대한 클라이언트 측 레지스트리 설정도 구성해야 합니다.  
1. 장치 동기화를 활성화하려면 Azure AD Connect에서 [동기화 옵션을 사용자 지정해야](../hybrid/how-to-connect-post-installation.md#additional-tasks-available-in-azure-ad-connect) 할 수도 있습니다. 


### <a name="clear-the-scp-from-ad"></a>AD에서 SCP 지우기

Active Directory 서비스 인터페이스 편집기(ADSI 편집)를 사용하여 AD에서 SCP 개체를 수정합니다.

1. 엔터프라이즈 관리자로 **ADSI 편집** 데스크톱 응용 프로그램 및 관리 워크스테이션 또는 도메인 컨트롤러를 시작합니다.
1. 도메인의 **구성 명명 컨텍스트에** 연결합니다.
1. **CN=구성, DC=contoso, DC=com** > **CN=서비스** > **CN=장치 등록 구성으로** 찾아보기
1. 리프 오브젝트 **CN=62a0ff2e-9713-943f-0d221bd30080을** 마우스 오른쪽 버튼으로 클릭하고 **속성을** 선택합니다.
   1. **속성 편집기** 창에서 **키워드를** 선택하고 **편집을** 클릭합니다.
   1. **azureADId** 및 **azureADName(한** 번에 하나씩)의 값을 선택하고 **제거를** 클릭합니다.
1. **ADSI 편집** 닫기


### <a name="configure-client-side-registry-setting-for-scp"></a>SCP에 대한 클라이언트 측 레지스트리 설정 구성

다음 예제를 사용하여 그룹 정책 개체(GPO)를 만들어 장치 레지스트리에 SCP 항목을 구성하는 레지스트리 설정을 배포합니다.

1. 그룹 정책 관리 콘솔을 열고 도메인에 새 그룹 정책 개체를 만듭니다.
   1. 새로 만든 GPO 이름을 제공합니다(예: ClientSideSCP).
1. GPO를 편집하고 다음 경로를 찾습니다: **컴퓨터 구성** > **환경 설정** > **Windows 설정** > **레지스트리**
1. 레지스트리를 마우스 오른쪽 단추로 클릭하고 **새** > **레지스트리 항목을** 선택합니다.
   1. **일반** 탭에서 다음을 구성합니다.
      1. 작업: **업데이트**
      1. 하이브: **HKEY_LOCAL_MACHINE**
      1. 키 경로: **소프트웨어\마이크로소프트\윈도우\현재버전\CDJ\AAD**
      1. 값 이름: **테넌트 Id**
      1. 값 유형: **REG_SZ**
      1. 값 데이터: Azure AD 인스턴스의 GUID 또는 **디렉터리 ID(이** 값은 **Azure 포털** > **Azure Active Directory** > **속성** > **ID에서**찾을 수 있음)
   1. **확인**을 클릭합니다.
1. 레지스트리를 마우스 오른쪽 단추로 클릭하고 **새** > **레지스트리 항목을** 선택합니다.
   1. **일반** 탭에서 다음을 구성합니다.
      1. 작업: **업데이트**
      1. 하이브: **HKEY_LOCAL_MACHINE**
      1. 키 경로: **소프트웨어\마이크로소프트\윈도우\현재버전\CDJ\AAD**
      1. 값 이름: **테넌트 이름**
      1. 값 유형: **REG_SZ**
      1. 값 데이터: AD FS와 같은 페더레이션 환경을 사용하는 경우 확인된 **도메인 이름입니다.** 인증된 **도메인 이름** 또는 onmicrosoft.com 도메인 `contoso.onmicrosoft.com` 이름(예: 관리되는 환경을 사용하는 경우)
   1. **확인**을 클릭합니다.
1. 새로 만든 GPO에 대한 편집기 닫기
1. 새로 만든 GPO를 제어된 롤아웃 모집단에 속하는 도메인 조인 컴퓨터를 포함하는 원하는 OU에 연결

### <a name="configure-ad-fs-settings"></a>AD FS 설정 구성

AD FS를 사용하는 경우 먼저 GPO를 AD FS 서버에 연결하여 위에서 언급한 지침을 사용하여 클라이언트 측 SCP를 구성해야 합니다. SCP 개체는 장치 개체에 대한 권한의 소스를 정의합니다. 온-프레미스 또는 Azure AD일 수 있습니다. 클라이언트 측 SCP가 AD FS에 대해 구성되면 장치 개체의 소스가 Azure AD로 설정됩니다.

> [!NOTE]
> AD FS 서버에서 클라이언트 측 SCP를 구성하지 못한 경우 장치 ID의 소스는 온-프레미스로 간주됩니다. 그런 다음 ADFS 장치 등록의 특성 "MaximumInactiveDays"에 정의된 기간 이후에 온-프레미스 디렉터리에서 장치 개체를 삭제하기 시작합니다. ADFS 장치 등록 개체는 [Get-AdfsDevice등록 cmdlet](/powershell/module/adfs/get-adfsdeviceregistration?view=win10-ps)을 사용하여 찾을 수 있습니다.

## <a name="controlled-validation-of-hybrid-azure-ad-join-on-windows-down-level-devices"></a>Windows 하위 수준 장치에서 하이브리드 Azure AD 조인의 제어된 유효성 검사

Windows 하위 수준 디바이스를 등록하려면 조직에서는 Microsoft Download Center에 제공되는 [비 Windows 10 컴퓨터용 Microsoft Workplace Join](https://www.microsoft.com/download/details.aspx?id=53554)을 설치해야 합니다.

 [Microsoft Endpoint Configuration Manager](/configmgr/)와 같은 소프트웨어 배포 시스템을 사용하여 패키지를 배포할 수 있습니다. 패키지는 quiet 매개 변수를 사용하여 표준 자동 설치 옵션을 지원합니다. 구성 관리자의 현재 분기는 완료된 등록을 추적하는 기능과 같은 이전 버전보다 나은 이점이 추가로 제공됩니다.

설치 관리자는 사용자 컨텍스트에서 실행되는 예약된 작업을 시스템에 만듭니다. 사용자가 Windows에 로그인할 때 이 작업이 트리거됩니다. 이 작업은 Azure AD에서 인증을 받은 후 사용자 자격 증명으로 Azure AD에 디바이스를 자동으로 가입합니다.

장치 등록을 제어하려면 선택한 Windows 하위 수준 장치 그룹에 Windows 설치 관리자 패키지를 배포해야 합니다.

> [!NOTE]
> AD에서 SCP가 구성되지 않은 경우 GPO(그룹 정책 개체)를 사용하여 도메인 에 가입한 컴퓨터에서 [SCP에 대한 클라이언트 측 레지스트리 설정 구성에](#configure-client-side-registry-setting-for-scp)설명된 것과 동일한 방법을 따라야 합니다.


모든 것이 예상대로 작동하는지 확인한 후 [Azure AD Connect를 사용하여 SCP를 구성하여](hybrid-azuread-join-managed-domains.md#configure-hybrid-azure-ad-join)나머지 Windows 현재 및 하위 수준 장치를 Azure AD에 자동으로 등록할 수 있습니다.

## <a name="next-steps"></a>다음 단계

[하이브리드 Azure Active Directory 조인 구현 계획](hybrid-azuread-join-plan.md)
