<properties 
	pageTitle="Azure Multi-Factor Authentication 보고서" 
	description="사용자가 증명 프로세스를 다시 수행하도록 하는 등의 사용자 설정을 변경하는 방법에 대해 설명합니다." 
	documentationCenter="" 
	services="multi-factor-authentication" 
	authors="billmath" 
	manager="swadhwa" 
	editor="curtand"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/02/2015" 
	ms.author="billmath"/>

# 클라우드에서 Azure Multi-Factor Authentication을 사용하여 사용자 설정 관리

관리자는 다음 사용자 및 장치 설정을 관리할 수 있습니다.

- [연락처 메서드를 다시 제공하도록 선택된 사용자 요구](#require-selected-users-to-provide-contact-methods-again)
- [사용자 기존 앱 암호 삭제](#delete-users-existing-app-passwords)
- [사용자에 대해 일시 중단된 모든 장치에서 MFA 복원(공개 미리 보기)](#restore-mfa-on-all-suspended-devices-for-a-user)






컴퓨터 또는 장치를 분실하거나 도난당한 경우 또는 사용자 액세스를 제거해야 하는 경우에 유용합니다.


## 연락처 메서드를 다시 제공할 선택된 사용자 요구

이 설정은 사용자가 로그인할 때 등록 프로세스를 다시 완료 하도록 합니다. 비브라우저 앱은 사용자가 해당 앱 암호를 소지한 경우 계속 작동됩니다. 또한 **선택한 사용자에 의해 생성된 모든 기존 앱 암호 삭제**를 선택하여 사용자 앱 암호를 삭제할 수 있습니다.

### 연락처 메서드를 다시 제공할 사용자를 요구하는 방법

<ol>
<li>Azure 관리 포털에 로그인합니다.</li>
<li>왼쪽에서 Active Directory를 클릭합니다.</li>
<li>디렉터리 아래에서 연락처 메서드를 다시 제공하도록 요구하려는 사용자에 대한 디렉터리를 클릭합니다.</li>
<li>위쪽에서 사용자를 클릭합니다.</li>
<li>페이지의 아래쪽에서 Multi-Factor Auth 관리를 클릭합니다. 다단계 인증 페이지가 열립니다. <li>관리하고 이름 옆에 있는 상자에 체크 표시하려는 사용자를 찾습니다. 위쪽에서 보기를 변경해야 할 수도 있습니다.</li> <li>이렇게 하면 오른쪽에 **사용자 설정 관리** 링크가 표시됩니다. 이 단추를 클릭합니다.</li> <li>**연락처 메서드를 다시 제공하도록 선택된 사용자 요구**에 체크 표시합니다.</li>

![연락처 메서드 제공](./media/multi-factor-authentication-manage-users-and-devices/reproofup.png)

<li>저장을 클릭합니다.</li>
<li>닫기를 클릭합니다.</li>

## 사용자 기존 앱 암호 삭제

사용자가 만든 모든 앱 암호가 삭제됩니다. 이러한 앱 암호와 연결된 비브라우저 앱은 새로운 앱 암호가 만들어질 때까지 작동이 중단됩니다.

### 사용자 기존 앱 암호를 삭제하는 방법

<ol>
<li>Azure 관리 포털에 로그인합니다.</li>
<li>왼쪽에서 Active Directory를 클릭합니다.</li>
<li>디렉터리 아래에서 앱 암호를 삭제하려는 사용자에 대한 디렉터리를 클릭합니다.</li>
<li>위쪽에서 사용자를 클릭합니다.</li>
<li>페이지의 아래쪽에서 Multi-Factor Auth 관리를 클릭합니다. 다단계 인증 페이지가 열립니다. <li>관리하고 이름 옆에 있는 상자에 체크 표시하려는 사용자를 찾습니다. 위쪽에서 보기를 변경해야 할 수도 있습니다.</li> <li>이렇게 하면 오른쪽에 **사용자 설정 관리** 링크가 표시됩니다. 이 단추를 클릭합니다.</li> <li>**선택한 사용자에 의해 생성된 모든 기존 앱 암호 삭제**에 체크 표시합니다.</li> ![앱 암호 삭제](./media/multi-factor-authentication-manage-users-and-devices/deleteapppasswords.png)<li>저장을 클릭합니다.</li> <li>닫기를 클릭합니다.</li>





## 사용자에 대해 일시 중단된 모든 장치에서 MFA 복원

관리자는 장치 및 브라우저에서 Multi-Factor Authentication을 재설정할 수 있습니다. 사용자의 장치 및 브라우저에 대해 Multi-Factor Authentication을 복원하여 재설정합니다. 이 작업을 수행하는 경우 사용자의 모든 장치 및 브라우저에서 일시 중단이 제거됩니다.

### 사용자에 대해 일시 중단된 모든 장치에서 MFA를 복원하는 방법

<ol>
<li>Azure 관리 포털에 로그인합니다.</li>
<li>왼쪽에서 Active Directory를 클릭합니다.</li>
<li>디렉터리 아래에서 MFA를 복원하려는 사용자에 대한 디렉터리를 클릭합니다.</li>
<li>위쪽에서 사용자를 클릭합니다.</li>
<li>페이지의 아래쪽에서 Multi-Factor Auth 관리를 클릭합니다. 다단계 인증 페이지가 열립니다. <li>관리하고 이름 옆에 있는 상자에 체크 표시하려는 사용자를 찾습니다. 위쪽에서 보기를 변경해야 할 수도 있습니다.</li> <li>이렇게 하면 오른쪽에 **사용자 설정 관리** 링크가 표시됩니다. 이 단추를 클릭합니다.</li> <li>일시 중단된 모든 장치에서 Multi-factor Authentication 복원에 체크 표시합니다.</li> ![앱 암호 삭제](./media/multi-factor-authentication-manage-users-and-devices/rememberdevices.png) <li>저장을 클릭합니다.</li> <li>닫기를 클릭합니다.</li>

<!---HONumber=August15_HO6-->