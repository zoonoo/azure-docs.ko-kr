<properties
	pageTitle="# Windows 7 도메인 가입 장치에 대한 자동 장치 등록 구성| Microsoft Azure"
	description="Windows 7 도메인 가입 장치를 Azure AD에 자동으로 등록하도록 구성하는 단계 및 System Center Configuration Manager와 같은 소프트웨어 배포 시스템을 사용하여 Windows 7 도메인 가입 장치에 장치 등록 소프트웨어 패키지를 배포하는 단계입니다."
	services="active-directory"
	documentationCenter=""
	authors="femila"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/07/2016"
	ms.author="femila"/>

# Windows 7 도메인 가입 장치에 대한 자동 장치 등록 구성

IT 관리자는 Windows 7 도메인 가입 장치를 Azure AD에 자동으로 등록하도록 구성할 수 있습니다. 이렇게 하려면 System Center Configuration Manager와 같은 소프트웨어 배포 시스템을 사용하여 Windows 7 도메인 가입 장치에 장치 등록 소프트웨어 패키지를 배포해야 합니다. Windows 도메인 가입 장치의 Azure Active Directory 자동 장치 등록에 나열된 필수 조건을 읽고 완료해야 합니다.

##Windows 7 도메인 가입 장치에 장치 등록 소프트웨어 패키지 설치

Windows 7용 장치 등록은 [다운로드 가능한 MSI 패키지](https://connect.microsoft.com/site1164)로 제공됩니다. Active Directory 도메인에 가입된 Windows 7 컴퓨터에 패키지를 설치해야 합니다. System Center Configuration Manager와 같은 소프트웨어 배포 시스템을 사용하여 패키지를 배포해야 합니다. MSI 패키지는 /quiet 매개 변수를 사용하여 표준 자동 설치 옵션을 지원합니다. 소프트웨어 패키지는 [Microsoft Connect 웹 사이트](https://connect.microsoft.com/site1164)에서 다운로드할 수 있습니다. 여기서 Windows 7용 작업 공간 연결을 선택하고 다운로드할 수 있습니다.

![](./media/active-directory-conditional-access/device-registration-process-windows7.gif)

## 작업 공간 연결 및 Azure Active Directory
Windows 7 도메인 가입 장치에 대한 장치 등록은 사용자 인터페이스를 요구하거나 포함하지 않습니다. 컴퓨터에 설치되고 나면 컴퓨터에 로그인하는 모든 도메인 사용자가 Azure AD의 장치 개체에 자동으로 등록됩니다. 물리적 장치의 등록된 사용자마다 하나의 장치 개체가 Azure AD에 포함됩니다.

설치 관리자는 사용자 컨텍스트에서 실행되고 사용자 로그인 시 트리거되는 예약된 작업을 시스템에 만듭니다. 작업은 사용자 로그인이 완료된 후 사용자 및 장치를 Azure AD에 자동으로 등록합니다. **Microsoft** > **작업 공간 연결**의 작업 스케줄러 라이브러리에서 예약된 작업을 찾을 수 있습니다. 작업은 컴퓨터에 로그인하는 모든 Active Directory 사용자를 실행 및 등록합니다. 다음 그림은 자동 장치 등록의 단계별 프로세스를 나열합니다.

![](./media/active-directory-conditional-access/automatic-device-registration-windows7.png)

1. 사용자(정보 작업자)가 Active Directory 도메인 자격 증명을 사용하여 Windows 7 클라이언트 컴퓨터에 로그온합니다.
1. 작업 공간 연결 예약된 작업이 실행됩니다.
1. 사용자가 Windows 통합 인증을 사용하여 AD FS에 자동으로 인증됩니다.
1. Windows 7 PC가 Azure AD의 사용자에 등록됩니다.
1. 장치 개체 및 인증서가 Azure AD에 만들어집니다. 개체는 user@device를 나타냅니다.
1. 작업 공간 연결 인증서가 컴퓨터에 저장됩니다.

## Windows 7 도메인 가입 장치 등록 취소

다음을 수행하여 Windows 7 도메인 가입 장치의 등록을 취소할 수 있습니다. System Center Configuration Manager와 같은 소프트웨어 배포 시스템을 사용하여 Windows 7 도메인 가입 장치에서 작업 공간 연결 소프트웨어 패키지를 제거합니다.

그런 다음 Windows 7 컴퓨터에서 명령 프롬프트를 열고 다음 명령을 실행하여 장치 등록을 취소합니다.

    %ProgramFiles%\Microsoft Workplace Join\AutoWorkplace.exe /leave

>[AZURE.NOTE]
이 명령은 컴퓨터에 로그인한 각 도메인 사용자의 컨텍스트에서 실행해야 합니다. Windows 7 도메인 가입 장치에 대한 이벤트 뷰어 및 오류

Windows 7 컴퓨터의 Windows 이벤트 로그에는 작업 공간 연결과 관련된 메시지가 표시됩니다. 성공 및 실패 작업 공간 연결 이벤트에 대한 메시지를 모두 확인할 수 있습니다. 이벤트 로그는 이벤트 뷰어의 응용 프로그램 및 서비스 로그 > Microsoft 작업 공간 연결 아래에 있습니다.

## 추가 항목

- [Azure Active Directory Device Registration 개요](active-directory-conditional-access-device-registration-overview.md)
- [도메인 가입 Windows 장치의 Azure Active Directory 자동 장치 등록](active-directory-conditional-access-automatic-device-registration.md)
- [Windows 8.1 도메인 가입 장치에 대한 자동 장치 등록 구성](active-directory-conditional-access-automatic-device-registration-windows-8-1.md)
- [Windows 10 도메인에 가입된 장치의 Azure Active Directory 자동 장치 등록](active-directory-azureadjoin-devices-group-policy.md)

<!---HONumber=AcomDC_0413_2016-->