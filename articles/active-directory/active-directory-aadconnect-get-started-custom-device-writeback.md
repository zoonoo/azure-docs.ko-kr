<properties
	pageTitle="Azure AD Connect: 장치 쓰기 저장 사용 | Microsoft Azure"
	description="이 문서는 Azure AD Connect를 사용하여 장치 쓰기 저장을 사용하는 방법을 자세히 설명합니다."
	services="active-directory"
	documentationCenter=""
	authors="billmath"
	manager="StevenPo"
	editor="curtand"/>

<tags
	ms.service="active-directory"  
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/12/2015"
	ms.author="billmath;andkjell"/>

# Azure AD Connect에서 장치 쓰기 저장 사용

다음 설명서에서는 Azure AD Connect에서 장치 쓰기 저장 기능을 사용하는 방법에 대한 정보를 제공합니다. 쓰기 저장 장치를 다음과 같은 시나리오에서 사용합니다.

장치에 따라 ADFS(2012 R2 이상) 보호된 응용 프로그램에 조건부 액세스를 사용하도록 설정합니다.(신뢰 당사자 트러스트)

응용 프로그램에 대한 액세스 권한이 신뢰할 수 있는 장치에 부여된 추가 보안 및 보증을 제공합니다. 조건부 액세스에 대한 자세한 내용은 [조건부 액세스로 위험 관리](active-directory-conditional-access.md) 및 [Azure Active Directory Device Registration을 사용하여 온-프레미스 조건부 액세스 설정](https://msdn.microsoft.com/library/azure/dn788908.aspx)을 참조하세요.

>[AZURE.NOTE]Azure AD Premium에 대한 구독에는 장치 쓰기 저장이 필요합니다.


## 1부: Azure AD Connect 설치
1. 사용자 지정 또는 Express 설정을 사용하여 Azure AD Connect를 설치합니다. 장치 쓰기 저장을 사용하도록 설정하기 전에 모든 사용자 및 그룹을 성공적으로 동기화하고 시작하는 것이 좋습니다.

## 2부: Active Directory 준비
쓰기 저장 장치를 사용하기 위한 준비는 다음 단계를 따릅니다.

1.	Azure AD Connect를 설치한 컴퓨터에서 관리자 모드로 PowerShell을 시작합니다.

2.	Active Directory PowerShell 모듈이 설치되어 있지 않은 경우입니다. 다음 명령을 사용하여 설치합니다.

	`Install-WindowsFeature –Name AD-DOMAIN-Services –IncludeManagementTools`

3.	엔터프라이즈 관리자 자격 증명으로 다음 명령을 실행하고 PowerShell을 끝냅니다.

	`Import-Module 'C:\Program Files\Microsoft Azure Active Directory Connect\AdPrep\AdSyncPrep.psm1'`

	`Initialize-ADSyncDeviceWriteback {Optional:–DomainName [name] Optional:-AdConnectorAccount [account]}`


![Powershell](./media/active-directory-aadconnect-get-started-custom-device-writeback/powershell.png)

설명:

- 존재하지 않는 경우 CN=Device Registration Configuration,CN=Services,CN=Configuration,[forest-dn]에서 새 컨테이너 및 개체를 생성하고 구성합니다.
- 존재하지 않는 경우 CN=RegisteredDevices,[domain-dn]에서 새 컨테이너 및 개체를 생성하고 구성합니다. 이 컨테이너에서 장치 개체를 만듭니다.
- Active Directory에서 장치를 관리하려면 Azure AD 커넥터 계정에 필요한 사용 권한을 설정합니다.
- Azure AD Connect가 여러 포리스트에 설치되었더라도 하나의 포리스트에서 실행해야 합니다.

매개 변수:

- DomainName: 장치 개체를 만드는 Active Directory 도메인입니다. 참고: 지정된 Active Directory 포리스트에 대한 모든 장치는 단일 도메인에 만들어집니다.
- AdConnectorAccount: 디렉터리에서 개체를 관리하기 위해 Azure AD Connect에서 사용하는 Active Directory 계정입니다. Azure AD Connect Sync에서 AD에 연결하기 위해 사용하는 계정입니다. 기본 설정을 사용하여 설치한 경우 접두사가 MSOL\_인 계정입니다.

## 3부: Azure AD Connect에서 장치 쓰기 저장 사용
다음 절차를 사용하여 Azure AD Connect에서 장치 쓰기 저장을 사용합니다.

1.	설치 마법사를 다시 실행합니다. 추가 작업 페이지에서 **동기화 옵션 사용자 지정**을 선택하고 **다음**을 클릭합니다. ![사용자 지정 설치](./media/active-directory-aadconnect-get-started-custom-device-writeback/devicewriteback2.png)
2.	옵션 기능 페이지에서 장치 쓰기 저장은 더 이상 회색으로 나타나지 않습니다. Azure AD Connect 준비 단계가 완료되지 않은 경우 장치 쓰기 저장은 옵션 기능 페이지에서 회색으로 표시됩니다. 장치 쓰기 저장에 대한 상자를 선택하고 **다음**을 클릭합니다. 확인란이 계속 비활성화되어 있는 경우 [문제 해결 섹션](#the-writeback-checkbox-is-still-disabled)을 참조하세요. ![장치 쓰기 저장](./media/active-directory-aadconnect-get-started-custom-device-writeback/devicewriteback3.png)
3.	쓰기 저장 페이지에서 기본 장치 쓰기 저장 포리스트로 제공된 도메인이 표시됩니다. ![사용자 지정 설치](./media/active-directory-aadconnect-get-started-custom-device-writeback/devicewriteback4.png)
4.	추가로 구성을 변경하지 않고 마법사의 설치를 완료합니다. 필요한 경우 [Azure AD Connect의 사용자 지정 설치](active-directory-aadconnect-get-started-custom.md)를 참조하세요.


## 조건부 액세스 사용
이 시나리오를 사용하기 위한 자세한 지침은 [Azure Active Directory 장치 등록을 사용하여 온-프레미스 조건부 액세스 설정](https://msdn.microsoft.com/library/azure/dn788908.aspx) 내에서 사용할 수 있습니다.

## 장치의 Active Directory에 동기화 여부 확인
장치 쓰기 저장은 이제 제대로 작동해야 합니다. AD에 장치 개체를 다시 쓰기 저장하는 데 최대 3시간이 걸릴 수 있습니다. 장치가 제대로 동기화되었는지 확인하려면 동기화 규칙을 완료한 후에 다음을 수행합니다.

1.	Active Directory 관리 센터를 시작합니다.
2.	페더레이션된 도메인 내에서 RegisteredDevices를 확장합니다. ![사용자 지정 설치](./media/active-directory-aadconnect-get-started-custom-device-writeback/devicewriteback5.png)
3.	현재 등록된 장치가 나열됩니다.

![사용자 지정 설치](./media/active-directory-aadconnect-get-started-custom-device-writeback/devicewriteback6.png)

## 문제 해결

### 쓰기 저장 확인란이 계속 비활성화되어 있음
위 단계를 수행했는데도 장치 쓰기 저장 확인란이 활성화되지 않는 경우 다음 단계에 따르면 상자가 활성화되기 전에 설치 마법사가 확인하는 사항을 알 수 있습니다.

먼저 첫 번째로

- 하나 이상의 포리스트에 Windows Server 2012R2가 있는지 확인합니다. 장치 개체 유형이 있어야 합니다.
- 설치 마법사가 이미 실행 중인 경우 변경 내용이 검색되지 않습니다. 이 경우 설치 마법사를 완료하고 다시 실행하세요.
- 초기화 스크립트에서 제공하는 계정이 Active Directory Connector에서 사용하는 올바른 사용자인지 확인합니다. 이를 확인하려면 다음 단계를 수행하세요.
	- 시작 메뉴에서 **동기화 서비스**를 엽니다.
	- **커넥터** 탭을 엽니다.
	- 형식이 Active Directory 도메인 서비스인 커넥터를 찾아 선택합니다.
	- **Actions(작업)**에서 **속성**을 선택합니다.
	- **Connect to Active Directory Forest(Active Directory 포리스트에 연결)**로 이동합니다. 이 화면에 지정된 도메인 및 사용자 이름이 스크립트에 제공된 계정과 일치하는지 확인합니다. ![커넥터 계정](./media/active-directory-aadconnect-get-started-custom-device-writeback/connectoraccount.png)

Active Directory의 구성 확인: - 장치 등록 서비스가 구성 명명 컨텍스트의 (CN=DeviceRegistrationService,CN=Device Registration Services,CN=Device Registration Configuration,CN=Services,CN=Configuration) 아래 위치에 있는지 확인합니다.

![Troubleshoot1](./media/active-directory-aadconnect-get-started-custom-device-writeback/troubleshoot1.png)

- 구성 네임스페이스를 검색하여 구성 개체가 하나만 있는지 확인합니다. 구성 개체가 둘 이상인 경우 중복되는 항목을 삭제합니다.

![Troubleshoot2](./media/active-directory-aadconnect-get-started-custom-device-writeback/troubleshoot2.png)

- Device Registration Service 개체에서 msDS-DeviceLocation 특성이 존재하며 값이 있는지 확인합니다. 이 위치를 조회하고 objectType msDS-DeviceContainer와 함께 있는지 확인합니다.

![Troubleshoot3](./media/active-directory-aadconnect-get-started-custom-device-writeback/troubleshoot3.png)

![Troubleshoot4](./media/active-directory-aadconnect-get-started-custom-device-writeback/troubleshoot4.png)

- Active Directory Connector에서 사용하는 계정에 이전 단계에서 찾은 등록된 장치 컨테이너에 대해 필요한 권한이 있는지 확인합니다. 다음은 이 컨테이너에 대해 예상되는 권한입니다.

![Troubleshoot5](./media/active-directory-aadconnect-get-started-custom-device-writeback/troubleshoot5.png)

- Active Directory 계정에 CN=Device Registration Configuration,CN=Services,CN=Configuration 개체에 대한 권한이 있는지 확인합니다.

![Troubleshoot6](./media/active-directory-aadconnect-get-started-custom-device-writeback/troubleshoot6.png)

## 추가 정보
- [조건부 액세스를 사용한 위험 관리](active-directory-conditional-access.md)
- [Azure Active Directory Device Registration을 사용하여 온-프레미스 조건부 액세스 설정](https://msdn.microsoft.com/library/azure/dn788908.aspx)

## 다음 단계
[Azure Active Directory와 온-프레미스 ID 통합](active-directory-aadconnect.md)에 대해 자세히 알아봅니다.

<!---HONumber=AcomDC_1203_2015-->