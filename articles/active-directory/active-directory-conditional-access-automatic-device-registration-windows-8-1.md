---
title: Windows 8.1 도메인 가입 장치에 대한 자동 장치 등록 구성| Microsoft Docs
description: " Windows 8.1 도메인 가입 장치를 Azure AD에 자동으로 등록하도록 하는 그룹 정책을 구성하는 단계입니다. "
services: active-directory
documentationcenter: ''
author: femila
manager: swadhwa
editor: ''

ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/21/2016
ms.author: Markvi

---
# <a name="configure-automatic-device-registration-for-windows-8.1-domain-joined-devices"></a>Windows 8.1 도메인 가입 장치에 대한 자동 장치 등록 구성
Active Directory 그룹 정책을 사용하여 Windows 8.1 도메인 가입 장치를 Azure AD에 자동으로 등록하도록 구성할 수 있습니다. 그룹 정책을 구성하려면 그룹 정책 관리 기능이 설치되고 도메인에 가입된 하나 이상의 Windows Server 2012 R2 또는 Windows 8.1 컴퓨터가 있어야 합니다. 이 그룹이 도메인에 사용하도록 설정되면 컴퓨터에 로그인하는 모든 도메인 사용자가 Azure AD의 장치 개체에 자동으로 등록됩니다. 물리적 장치의 등록된 사용자마다 하나의 장치 개체가 Azure AD에 포함됩니다. Windows 도메인 가입 장치의 Azure Active Directory 자동 장치 등록에 나열된 필수 조건을 읽고 완료해야 합니다.

> [!NOTE]
> 자동 장치 등록을 설정하는 방법에 대한 최신 지침은 [Azure Active Directory를 사용하여 Windows 도메인 가입 장치의 자동 등록을 설정하는 방법](active-directory-conditional-access-automatic-device-registration-setup.md)을 참조하세요.
> 
> 

## <a name="configure-the-group-policy-for-your-windows-8.1-domain-joined-devices"></a>Windows 8.1 도메인 가입 장치에 대한 그룹 정책 구성
1. 서버 관리자를 열고 **도구** > **그룹 정책 관리**로 이동합니다.
2. 그룹 정책 관리에서 **자동 작업 공간 연결**을 사용하도록 설정하려는 도메인에 해당하는 도메인 노드로 이동합니다.
3. **그룹 정책 개체**를 마우스 오른쪽 단추로 클릭하고 **새로 만들기**를 선택합니다. 그룹 정책 개체에 이름(예: **자동 작업 공간 연결**)을 지정합니다. **확인**을 클릭합니다.
4. 새 그룹 정책 개체를 마우스 오른쪽 단추로 클릭하고 **편집**을 선택합니다.
5. **컴퓨터 구성** > **정책** > **관리 템플릿** > **Windows 구성 요소** > **작업 공간 연결**로 이동합니다.
6. 클라이언트 컴퓨터에 자동으로 작업 공간 연결을 마우스 오른쪽 단추로 클릭하고 **편집**을 선택합니다.
7. 사용 라디오 단추를 선택하고 적용을 클릭합니다. **확인**을 클릭합니다.
8. 이제 그룹 정책 개체를 선택한 위치에 연결할 수 있습니다. 조직의 모든 Windows 8.1 도메인 가입 장치에 대해 이 정책을 사용하도록 설정하려면 그룹 정책을 도메인에 연결합니다.

## <a name="unregistering-your-windows-8.1-domain-joined-devices"></a>Windows 8.1 도메인 가입 장치 등록 취소
다음을 수행하여 Windows 8.1 도메인 가입 장치의 등록을 취소할 수 있습니다. 이전 섹션에서 만든 작업 공간 연결 그룹 정책 설정을 수정합니다. 클라이언트 컴퓨터에 자동으로 작업 공간 연결 정책을 사용 안 함으로 설정합니다. 이렇게 하면 새 장치가 작업 공간에 자동으로 연결되지 않습니다.

아래의 두 옵션 중 하나를 수행하여 기존 Windows 8.1 도메인 가입 컴퓨터의 등록을 취소합니다.

* 옵션 1: **PC 설정을 사용하여 Windows 8.1 도메인 가입 장치의 등록 취소**
  
  1. Windows 8.1 장치에서 **PC 설정** > **네트워크** > **작업 공간**으로 이동합니다.
  2. **나가기**를 선택합니다.
     컴퓨터에 로그인했고 자동으로 작업 공간에 연결된 각 도메인 사용자에 대해 이 프로세스를 반복해야 합니다.
* 옵션 2: 스크립트를 사용하여 Windows 8.1 도메인 가입 장치의 등록 취소
  
  1. Windows 8.1 컴퓨터에서 명령 프롬프트를 열고 다음 명령을 실행합니다. ` %SystemRoot%\System32\AutoWorkplace.exe leave`

이 명령은 컴퓨터에 로그인한 각 도메인 사용자의 컨텍스트에서 실행해야 합니다.

## <a name="event-viewer-&-errors-for-windows-8.1-domain-joined-devices"></a>Windows 8.1 도메인 가입 장치에 대한 이벤트 뷰어 및 오류
Windows 8.1 컴퓨터의 Windows 이벤트 로그에는 장치 등록과 관련된 메시지가 표시됩니다. 성공 및 실패 이벤트에 대한 메시지를 확인할 수 있습니다. 

이벤트 로그는 이벤트 뷰어의 응용 프로그램 및 서비스 **로그** > **Microsoft** > **Windows > 작업 공간 연결** 아래에 있습니다.

## <a name="additional-details"></a>추가 정보
그룹 정책은 사용자 컨텍스트에서 실행되고 사용자 로그인 시 트리거되는 예약된 작업을 시스템에 사용하도록 설정합니다. 작업은 로그인이 완료된 후 사용자 및 장치를 Azure AD에 자동으로 등록합니다. Windows 8.1 장치 작업 스케줄러 라이브러리의 **Microsoft** > **Windows** > **작업 공간 연결** 아래에서 예약된 작업을 찾을 수 있습니다. 작업은 로그인하는 모든 Active Directory 사용자를 실행 및 등록합니다. 

## <a name="additional-topics"></a>추가 항목
* [Azure Active Directory Device Registration 개요](active-directory-conditional-access-device-registration-overview.md)
* [Windows 10 도메인에 가입된 장치의 Azure Active Directory 자동 장치 등록](active-directory-conditional-access-automatic-device-registration.md)
* [Windows 7 도메인 가입 장치에 대한 자동 장치 등록 구성](active-directory-conditional-access-automatic-device-registration-windows7.md)

<!--HONumber=Oct16_HO2-->


