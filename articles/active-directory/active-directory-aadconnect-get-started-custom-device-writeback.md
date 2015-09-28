<properties 
	pageTitle="Azure AD Connect에서 장치 쓰기 저장 사용" 
	description="이 문서는 Azure AD Connect를 사용하여 장치 쓰기 저장을 사용하는 방법을 자세히 설명합니다." 
	services="active-directory" 
	documentationCenter="" 
	authors="billmath" 
	manager="msStevenPo" 
	editor="curtand"/>

<tags 
	ms.service="active-directory"  
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/15/2015"
	ms.author="billmath"/>

# Azure AD Connect에서 장치 쓰기 저장 사용

다음 설명서에서는 Azure AD Connect에서 장치 쓰기 저장 기능을 사용하는 방법에 대한 정보를 제공합니다. 쓰기 저장 장치를 다음과 같은 시나리오에서 사용합니다.

장치에 따라 ADFS(2012 R2 이상) 보호된 응용 프로그램에 조건부 액세스를 사용하도록 설정합니다.(신뢰 당사자 트러스트)

응용 프로그램에 대한 액세스 권한이 신뢰할 수 있는 장치에 부여된 추가 보안 및 보증을 제공합니다. 조건부 액세스에 대한 자세한 내용은 [조건부 액세스로 위험 관리](active-directory-conditional-access.md) 및 [Azure Active Directory Device Registration을 사용하여 온-프레미스 조건부 액세스 설정](https://msdn.microsoft.com/library/azure/dn788908.aspx)을 참조하세요.

>[AZURE.Note]Azure Active Directory 장치 등록 서비스 조건부 액세스 정책에 등록된 장치를 사용하는 경우 Office 365 또는 Azure AD Premium에 대한 구독이 필요합니다. 여기에는 AD FS(Active Directory Federation Services)에 의해 온-프레미스 리소스에 적용되는 정책이 포함됩니다.

## 1부: Azure AD Connect 준비
쓰기 저장 장치를 사용하기 위한 준비는 다음 단계를 따릅니다.

1.	Azure AD Connect를 설치한 컴퓨터에서 관리자 모드로 PowerShell을 시작합니다.

2.	Active Directory PowerShell 모듈이 설치되어 있지 않은 경우입니다. 다음 명령을 사용하여 설치합니다.

	Install-WindowsFeature –Name AD-DOMAIN-Services –IncludeManagementTools

3.	엔터프라이즈 관리자 자격 증명으로 다음 명령을 실행하고 PowerShell을 끝냅니다.

	Import-Module ‘C:\\Program Files\\Microsoft Azure Active Directory Connect\\AdPrep\\AdSyncAdPrep.psm1’

	Initialize-ADSyncDeviceWriteback –DomainName <name> -AdConnectorAccount <account>

설명:



- 존재하지 않는 경우 CN=Device Registration Configuration,CN=Services,CN=Configureation,<forest-dn>에서 새 컨테이너 및 개체를 만듭니다.



- 존재하지 않는 경우 CN=RegisteredDevices,<domain-dn>에서 새 컨테이너 및 개체를 생성하고 구성합니다. 이 컨테이너에서 장치 개체를 만듭니다.



- Active Directory에서 장치를 관리하려면 Azure AD 커넥터 계정에 필요한 사용 권한을 설정합니다.



- Azure AD Connect가 여러 포리스트에 설치되었더라도 하나의 포리스트에서 실행해야 합니다.

매개 변수:


- DomainName: 장치 개체를 만드는 Active Directory 도메인입니다. 참고: 지정된 Active Directory 포리스트에 대한 모든 장치는 단일 도메인에 만들어집니다. 


- AdConnectorAccount: 디렉터리에서 개체를 관리하기 위해 Azure AD Connect에서 사용하는 Active Directory 계정입니다.

## 2부: 장치 쓰기 저장 사용
다음 절차를 사용하여 Azure AD Connect에서 장치 쓰기 저장을 사용합니다.

1.	AAD Connect 마법사를 실행합니다. 마법사를 처음으로 사용하는 경우 Express 설정 화면 ![사용자 지정 설치](./media/active-directory-aadconnect-get-started-custom-device-writeback/devicewriteback1.png)에서 사용자 지정을 선택하여 사용자 지정 설치를 수행합니다.
2.	처음이 아닌 경우 추가 작업 페이지에서 동기화 옵션을 사용자 지정하도록 선택하고 다음을 클릭합니다. ![사용자 지정 설치](./media/active-directory-aadconnect-get-started-custom-device-writeback/devicewriteback2.png)
3.	옵션 기능 페이지에서 장치 쓰기 저장은 더 이상 회색으로 나타나지 않습니다. Azure AD Connect 준비 단계가 완료되지 않은 경우 장치 쓰기 저장은 옵션 기능 페이지에서 회색으로 표시됩니다. 쓰기 저장 장치에 대한 상자를 선택하고 다음을 클릭합니다. ![장치 쓰기 저장](./media/active-directory-aadconnect-get-started-custom-device-writeback/devicewriteback3.png)
4.	쓰기 저장 페이지에서 기본 장치 쓰기 저장 포리스트로 제공된 도메인이 표시됩니다. ![사용자 지정 설치](./media/active-directory-aadconnect-get-started-custom-device-writeback/devicewriteback4.png)
5.	추가로 구성을 변경하지 않고 마법사의 설치를 완료합니다. 필요한 경우 [Azure AD Connect의 사용자 지정 설치](active-directory-aadconnect-get-started-custom.md)를 참조하세요.



## 조건부 액세스 사용
이 시나리오를 사용하기 위한 자세한 지침은 [Azure Active Directory 장치 등록을 사용하여 온-프레미스 조건부 액세스 설정](https://msdn.microsoft.com/library/azure/dn788908.aspx) 내에서 사용할 수 있습니다.

## 장치의 Active Directory에 동기화 여부 확인
장치 쓰기 저장은 이제 제대로 작동해야 합니다. AD에 장치 개체를 다시 쓰기 저장하는 데 최대 3시간이 걸릴 수 있습니다. 장치가 제대로 동기화되었는지 확인하려면 동기화 규칙을 완료한 후에 다음을 수행합니다.
 
1.	Active Directory 관리 센터를 시작합니다. 
2.	페더레이션된 도메인 내에서 RegisteredDevices를 확장합니다. ![사용자 지정 설치](./media/active-directory-aadconnect-get-started-custom-device-writeback/devicewriteback5.png)
3.	현재 등록된 장치가 나열됩니다. 

![사용자 지정 설치](./media/active-directory-aadconnect-get-started-custom-device-writeback/devicewriteback6.png)

## 추가 정보 


- [조건부 액세스를 사용한 위험 관리](active-directory-conditional-access.md)
- [Azure Active Directory Device Registration을 사용하여 온-프레미스 조건부 액세스 설정](https://msdn.microsoft.com/library/azure/dn788908.aspx)

<!---HONumber=Sept15_HO3-->