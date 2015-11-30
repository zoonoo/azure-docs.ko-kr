<properties 
	pageTitle="조직에서 Microsoft Passport for Work 사용 | Microsoft Azure" 
	description="조직에서 Microsoft Passport를 사용하도록 설정하는 배포 지침입니다." 
	services="active-directory" 
	documentationCenter="" 
	authors="femila" 
	manager="stevenpo" 
	editor=""
	tags="azure-classic-portal"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/17/2015" 
	ms.author="femila"/>

# 조직에서 Microsoft Passport for Work 사용

Windows 10 도메인에 가입된 장치와 Azure AD를 연결한 후 조직에서 Microsoft Passport for Work를 사용하도록 설정하려면 다음을 수행합니다.

## Technical Preview용 System Center Configuration Manager 버전 1509 배포
Microsoft Passport 키 기반의 사용자 인증서를 배포하려면 다음이 필요합니다.

- **Technical Preview용 System Center Configuration Manager 버전 1509**. 자세한 내용은 [Microsoft System Center Configuration Manager Technical Preview](https://technet.microsoft.com/library/dn965439.aspx#BKMK_TP3Update) 및 [System Center Configuration Manager 팀 블로그](http://blogs.technet.com/b/configmgrteam/archive/2015/09/23/now-available-update-for-system-center-config-manager-tp3.aspx)를 참조하세요.
- **PKI 인프라**: 사용자 인증서를 사용하여 Microsoft Passport for Work를 사용하도록 설정하려면 PKI 인프라가 제 위치에 있어야 합니다. PKI 인프라가 없거나 사용자 인증에 대해 사용하지 않으려는 경우 새 버전 Windows Server의 DC(도메인 컨트롤러)를 배포하면 됩니다.
 - **새 버전 Windows Server의 DC 배포**: 새로운 Windows Server 빌드 10551 이상 버전(ISO는 [Signiant Media Exchange](https://datatransfer.microsoft.com/signiant_media_exchange/spring/main?sdkAccessible=true)에서 다운로드 가능)에서 다음 단계에 따라 [기존 도메인에 복제본 DC를 설치](https://technet.microsoft.com/ko-KR/library/jj574134.aspx)하거나 [새로운 환경을 만드는 경우 새 Active Directory 포리스트를 설치](https://technet.microsoft.com/ko-KR/library/jj574134.aspx)합니다.

## Active Directory에서 그룹 정책을 통해 Microsoft Passport for Work 구성

 Active Directory 그룹 정책을 사용하여 사용자가 Windows에 로그온 시 사용자 Microsoft Passport 자격 증명을 프로비전하도록 Windows 10 도메인에 가입된 장치를 구성할 수 있습니다.

1. 	서버 관리자를 열고 **도구** > **그룹 정책 관리**로 이동합니다.
2.	그룹 정책 관리에서 Azure AD 연결을 사용하도록 설정하려는 도메인에 해당하는 도메인 노드로 이동합니다.
3.	**그룹 정책 개체**를 마우스 오른쪽 단추로 클릭하고 **새로 만들기**를 선택합니다. 예를 들어 그룹 정책 개체에 이름을 지정하고 **Microsoft Passport를 사용하도록 설정**합니다. **확인**을 클릭합니다.
4.	새 그룹 정책 개체를 마우스 오른쪽 단추로 클릭하고 **편집**을 선택합니다.
5.	**컴퓨터 구성** > **정책** > **관리 템플릿** > **Windows 구성 요소** > **Passport for Work**로 이동합니다.
6.	**Passport for Work 사용**을 마우스 오른쪽 단추로 클릭한 다음 **편집**을 선택합니다.
7.	**사용** 라디오 단추를 선택하고 **적용**을 클릭합니다. **확인**을 클릭합니다.
8.	이제 그룹 정책 개체를 선택한 위치에 연결할 수 있습니다. 조직의 모든 Windows 10 도메인 가입 장치에 대해 이 정책을 사용하도록 설정하려면 그룹 정책을 도메인에 연결합니다. 예:
 - Windows 10 도메인에 가입된 컴퓨터가 배치될 AD의 특정 OU(조직 구성 단위)입니다.
 - Azure AD에 자동 등록될 Windows 10 도메인에 가입된 컴퓨터를 포함하는 특정 보안 그룹입니다.

## Configuration Manager에서 PowerShell 배포를 통해 Microsoft Passport for Work 구성 

다음 PowerShell 명령을 실행합니다.

    powershell.exe -ExecutionPolicy Bypass -NoLogo -NoProfile -Command "& {New-ItemProperty "HKLM:\Software\Policies\Microsoft\PassportForWork" -Name "Enabled" -Value 1 -PropertyType "DWord" -Force}"

## Configuration Manager에서 "Passport for Work" 등록 인증서를 등록하도록 인증서 프로필 구성
Passport for Work 인증서 기반 로그온/Microsoft Hello를 사용하려면 스마트 카드 로그온 EKU가 있는 템플릿을 선택하여 인증서 프로필(자산 및 호환성 -> 준수 설정 -> 회사 리소스 액세스 -> 인증서 프로필)을 구성합니다.

##Passport for Work 컨테이너가 사용되고 인증서 평가를 요청할 때 트리거되는 예약된 작업 설정
Passport for Work 컨테이너를 만드는 동안 수신 대기하고 인증서 평가를 요청하는 예약된 작업을 만드는 데 관리자에게 필요한 단기적인 수정입니다. 이렇게 하면 다음 로그온에 사용되는 컨테이너, PIN 및 해당 기능 설정 지연 시간을 줄일 수 있습니다.

**예약된 작업을 만들려면 다음 명령 또는 UI 사용**(schtasks /create /xml %0..<EnrollCertificate.xml> /tn <Task Name>)

아래는 샘플 xml입니다.

    <?xml version="1.0" encoding="UTF-16"?>
    <Task version="1.2" xmlns="http://schemas.microsoft.com/windows/2004/02/mit/task">
       <RegistrationInfo>
         <Date>2015-09-04T17:48:45.9973761</Date>
          <Author><DomainName/UserName></Author>
        <URI>\Enroll Certificates</URI>
      </RegistrationInfo>
      <Triggers>
        <EventTrigger>
          <Enabled>true</Enabled>
          <Subscription>&lt;QueryList&gt;&lt;Query Id="0" Path="Microsoft-Windows-User Device Registration/Admin"&gt;&lt;Select Path="Microsoft-Windows-User Device Registration/Admin"&gt;*[System[Provider[@Name='Microsoft-Windows-User Device Registration'] and EventID=300]]&lt;/Select&gt;&lt;/Query&gt;&lt;/QueryList&gt;</Subscription>
        </EventTrigger>
      </Triggers>
      <Principals>
      </Principals>
      <Settings>
        <MultipleInstancesPolicy>IgnoreNew</MultipleInstancesPolicy>
        <DisallowStartIfOnBatteries>false</DisallowStartIfOnBatteries>
        <StopIfGoingOnBatteries>true</StopIfGoingOnBatteries>
        <AllowHardTerminate>true</AllowHardTerminate>
        <StartWhenAvailable>true</StartWhenAvailable>
        <RunOnlyIfNetworkAvailable>false</RunOnlyIfNetworkAvailable>
        <IdleSettings>
          <StopOnIdleEnd>true</StopOnIdleEnd>
          <RestartOnIdle>false</RestartOnIdle>
        </IdleSettings>
        <AllowStartOnDemand>true</AllowStartOnDemand>
        <Enabled>true</Enabled>
        <Hidden>false</Hidden>
        <RunOnlyIfIdle>false</RunOnlyIfIdle>
        <WakeToRun>false</WakeToRun>
        <ExecutionTimeLimit>PT1H</ExecutionTimeLimit>
        <Priority>7</Priority>
        <RestartOnFailure>
          <Interval>PT1M</Interval>
          <Count>3</Count>
        </RestartOnFailure>
      </Settings>
      <Actions Context="Author">
        <Exec>
          <Command>wmic</Command>
          <Arguments>/namespace:\\root\ccm\dcm path SMS_DesiredConfiguration call TriggerEvaluation 1,0,"ScopeId_73F3BB5E-5EDC-4928-87BD-4E75EB4BBC34/ConfigurationPolicy_db89c51e-1d1b-4a17-8e42-a5459b742ccd",8</Arguments>
        </Exec>
      </Actions>
    </Task>

여기서 ScopeId\_73F3BB5E-5EDC-4928-87BD-4E75EB4BBC34/ConfigurationPolicy\_db89c51e-1d1b-4a17-8e42-a5459b742ccd는 "Configuration Manager에서 Passport for Work 등록 인증서를 등록하도록 인증서 프로필 구성" 단계에서 만든 인증서 프로필에 대한 ID이고, <8>은 버전입니다.

## 추가 정보
* [엔터프라이즈를 위한 Windows 10: 작업에 장치를 사용하는 방법](active-directory-azureadjoin-windows10-devices-overview.md)
* [Azure Active Directory 조인을 통해 클라우드 기능을 Windows 10 장치로 확장](active-directory-azureadjoin-user-upgrade.md)
* [Microsoft Passport를 통해 암호 없이 ID 인증](active-directory-azureadjoin-passport.md)
* [Azure AD 조인에 대한 사용 시나리오에 대해 알아보기](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Windows 10 환경용 Azure AD에 도메인 가입된 장치 연결](active-directory-azureadjoin-devices-group-policy.md)
* [Azure AD 조인 설정](active-directory-azureadjoin-setup.md)

<!---HONumber=Nov15_HO4-->