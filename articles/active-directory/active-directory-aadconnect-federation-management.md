<properties
	pageTitle="Azure AD Connect를 사용한 AD FS 관리 및 사용자 지정 | Microsoft Azure"
	description="Azure AD Connect를 사용한 AD FS 관리 및 Azure AD Connect와 Powershell을 사용한 사용자 AD FS 로그인 환경의 사용자 지정입니다."
	services="active-directory"
	documentationCenter=""
	authors="anandyadavmsft"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/14/2016"
	ms.author="anandy"/>

# Azure AD Connect를 사용한 AD FS 관리 및 사용자 지정

이 문서에서는 Azure AD Connect 및 AD FS 팜 전체 구성에 필요할 수 있는 기타 일반적인 AD FS 작업을 사용하여 수행할 수 있는 다양한 AD FS 관련 작업에 대해 자세히 설명합니다.

## AD FS 관리

Azure AD Connect는 Azure AD Connect 마법사를 사용하여 최소한의 사용자의 개입만으로 수행할 수 있는 다양한 AD FS 관련 작업을 제공합니다. 마법사를 실행하여 Azure AD Connect 설치를 완료한 후 다시 마법사를 실행하여 추가 작업을 수행할 수 있습니다.

### 트러스트 복구

Azure AD Connect는 AD FS와 Azure ADtrust의 현재 상태를 확인하고 적절한 조치를 취하여 트러스트를 복구할 수 있습니다. 다음 단계에 따라 Azure AD 및 AD FS 트러스트를 복구합니다.

사용 가능한 작업 목록에서 **Azure AD 및 ADFS 트러스트 복구**를 선택합니다.

![](media\active-directory-aadconnect-federation-management\RepairADTrust1.PNG)

**Azure AD에 연결** 페이지에서 Azure AD에 대한 전역 관리자 자격 증명을 제공하고 다음을 클릭합니다.

![](media\active-directory-aadconnect-federation-management\RepairADTrust2.PNG)

**원격 액세스 자격 증명** 페이지에서 도메인 관리자에 대한 자격 증명을 제공합니다.

![](media\active-directory-aadconnect-federation-management\RepairADTrust3.PNG)

다음을 클릭하면 Azure AD Connect가 인증서 상태를 확인하고 문제가 있으면 표시합니다.

![](media\active-directory-aadconnect-federation-management\RepairADTrust4.PNG)

**구성 준비** 페이지에서는 트러스트를 복구하기 위해 수행할 작업 목록이 표시됩니다.

![](media\active-directory-aadconnect-federation-management\RepairADTrust5.PNG)

계속해서 설치를 클릭하고 트러스트를 복구합니다.

>[AZURE.NOTE] Azure AD Connect는 자체 서명된 인증서에 대해서만 복구 또는 조치를 취할 수 있습니다. 타사 인증서는 Azure AD Connect에서 복구할 수 없습니다.

### 새 AD FS 서버 추가

> [AZURE.NOTE] Azure AD Connect는 AD FS 서버를 추가하는 데 PFX 인증서 파일이 필요합니다. 따라서 Azure AD Connect를 사용하여 AD FS 팜을 구성한 경우에만 이 작업을 수행할 수 있습니다.

**추가 페더레이션 서버 배포**를 선택하고 다음을 클릭합니다.

![](media\active-directory-aadconnect-federation-management\AddNewADFSServer1.PNG)

**Azure AD에 연결** 페이지에서 Azure AD에 대한 전역 관리자 자격 증명을 제공하고 다음을 클릭합니다.

![](media\active-directory-aadconnect-federation-management\AddNewADFSServer2.PNG)

다음 페이지에서 도메인 관리자 자격 증명을 제공합니다.

![](media\active-directory-aadconnect-federation-management\AddNewADFSServer3.PNG)

다음 페이지에서 Azure AD Connect는 Azure AD Connect를 사용하여 새 AD FS 팜을 구성하는 동안 제공한 pfx 파일의 암호를 요청합니다. 암호 입력을 클릭하여 PFX 파일에 대한 암호를 제공합니다.

![](media\active-directory-aadconnect-federation-management\AddNewADFSServer4.PNG)

![](media\active-directory-aadconnect-federation-management\AddNewADFSServer5.PNG)

다음 페이지에서 추가 서버 이름 또는 AD FS 팜에 추가할 IP 주소를 제공합니다.

![](media\active-directory-aadconnect-federation-management\AddNewADFSServer6.PNG)

다음을 클릭하고 마지막 구성 페이지로 이동합니다. Azure AD Connect가 AD FS 팜에 서버 추가를 완료한 후 연결을 확인하는 옵션이 제공됩니다.

![](media\active-directory-aadconnect-federation-management\AddNewADFSServer7.PNG)

![](media\active-directory-aadconnect-federation-management\AddNewADFSServer8.PNG)

### 새 AD FS WAP 서버 추가

> [AZURE.NOTE] Azure AD Connect는 WAP 서버를 추가하는 데 PFX 인증서 파일이 필요합니다. 따라서 Azure AD Connect를 사용하여 AD FS 팜을 구성한 경우에만 이 작업을 수행할 수 있습니다.

사용 가능한 작업 목록에서 **웹 응용 프로그램 프록시 배포**를 선택합니다.

![](media\active-directory-aadconnect-federation-management\WapServer1.PNG)

다음 페이지에서 Azure 전역 관리자 자격 증명을 제공합니다.

![](media\active-directory-aadconnect-federation-management\wapserver2.PNG)

다음으로 **SSL 인증서 지정** 페이지가 나타나면 Azure AD Connect를 사용하여 AD FS 팜을 구성하는 동안 제공한 PFX 파일에 대한 암호를 제공해야 합니다.

![](media\active-directory-aadconnect-federation-management\WapServer3.PNG)

![](media\active-directory-aadconnect-federation-management\WapServer4.PNG)

다음 페이지에서 WAP로 추가할 서버를 추가합니다. WAP 서버가 도메인에 가입되거나 가입되지 않을 수 있으므로 마법사가 추가 중인 서버에 대한 관리자 자격 증명을 요청합니다.

![](media\active-directory-aadconnect-federation-management\WapServer5.PNG)

**프록시 트러스트 자격 증명** 페이지에서 프록시 트러스트를 구성하고 AD FS 팜의 주 서버에 액세스하는 관리자 자격 증명을 제공합니다.

![](media\active-directory-aadconnect-federation-management\WapServer6.PNG)

**구성 준비** 페이지에서 마법사는 수행할 작업 목록을 표시합니다.

![](media\active-directory-aadconnect-federation-management\WapServer7.PNG)

설치를 클릭하여 구성을 완료합니다. 구성이 완료되면 마법사에서 서버에 대한 연결을 확인하는 옵션을 제공합니다. 확인을 클릭하여 연결을 확인합니다.

![](media\active-directory-aadconnect-federation-management\WapServer8.PNG)

### 새 페더레이션된 도메인 추가

Azure AD Connect를 사용하면 Azure AD와 페더레이션될 새 도메인을 쉽게 추가할 수 있습니다. Azure AD Connect는 페더레이션에 대한 새 도메인을 추가할 뿐 아니라 Azure AD와 페더레이션된 여러 도메인이 있는 경우 발급자를 올바르게 반영하기 위해 클레임 규칙을 수정합니다.

새 페더레이션된 도메인을 추가하려면 작업 **추가 Azure AD 도메인 추가**를 선택합니다.

![](media\active-directory-aadconnect-federation-management\AdditionalDomain1.PNG)

마법사의 다음 페이지에서 Azure AD 전역 관리자 자격 증명을 제공합니다.

![](media\active-directory-aadconnect-federation-management\AdditionalDomain2.PNG)

원격 액세스 자격 증명에서 도메인 관리자 자격 증명을 제공합니다.

![](media\active-directory-aadconnect-federation-management\additionaldomain3.PNG)

다음 페이지에서 마법사는 온-프레미스 디렉터리를 페더레이션하려는 Azure AD 도메인 목록을 제공합니다. 목록에서 도메인을 선택합니다.

![](media\active-directory-aadconnect-federation-management\AdditionalDomain4.PNG)

도메인을 선택하면 마법사는 마법사가 수행할 추가 작업 및 구성의 영향에 대한 적절한 정보를 제공합니다. 경우에 따라 Azure AD에서 아직 확인되지 않은 도메인을 선택하는 경우 마법사는 도메인을 확인하는 데 유용한 정보를 제공합니다. 도메인을 확인하는 방법에 대한 자세한 내용은 [Azure Active Directory에서 사용자 지정 도메인 이름 추가 및 확인](active-directory-add-domain-add-verify-general.md)을 참조하세요.

다음을 클릭하면 **구성 준비** 페이지에 Azure AD Connect가 수행할 작업 목록이 표시됩니다. 설치를 클릭하여 구성을 완료합니다.

![](media\active-directory-aadconnect-federation-management\AdditionalDomain5.PNG)

## AD FS 사용자 지정

다음 섹션에서는 AD FS 로그인 페이지를 사용자 지정하는 데 수행해야 할 수 있는 일반적인 작업 중 일부를 수행할 수 있는 방법에 대해 자세히 설명합니다.

### 사용자 지정 회사 로고 또는 일러스트레이션 추가

로그인 페이지에 표시되는 회사의 로고를 변경하려면 다음 PowerShell Windows PowerShell cmdlet 및 구문을 사용합니다.

> [AZURE.NOTE] 로고의 권장 크기는 파일 크기가 10KB 이하인 96dpi에서 260x35입니다.

    Set-AdfsWebTheme -TargetName default -Logo @{path="c:\Contoso\logo.PNG"}

> [AZURE.NOTE] TargetName 매개 변수는 필수입니다. AD FS와 함께 제공되는 기본 테마의 이름은 default입니다.
 

### 로그인 설명 추가

로그인 페이지에 로그인 페이지 설명을 추가하려면 다음 Windows PowerShell PowerShell cmdlet 및 구문을 사용합니다.

    Set-AdfsGlobalWebContent -SignInPageDescriptionText "<p>Sign-in to Contoso requires device registration. Click <A href='http://fs1.contoso.com/deviceregistration/'>here</A> for more information.</p>"

### AD FS 클레임 규칙 수정

AD FS는 클레임을 발급하는 사용자 지정 규칙을 지정하는 옵션을 제공합니다. 사용자 지정 클레임 규칙을 만드는 데 사용할 수 있는 다양한 클레임 언어를 지원합니다. 자세한 내용은 [여기](https://technet.microsoft.com/library/dd807118.aspx) 문서에서 확인할 수 있습니다.

다음 섹션에서는 Azure AD 및 AD FS 페더레이션에 관련된 몇 가지 시나리오에 대한 사용자 지정 규칙을 작성할 수 있는 방법에 대해 자세히 설명합니다.

#### 특성에 나타나는 값에서 변경이 불가능한 ID 조건부

Azure AD Connect에서는 개체가 Azure AD에 동기화되는 경우 원본 앵커로 사용할 특성을 지정할 수 있습니다. 사용자 지정 특성의 값이 비어 있지 않은 경우 조건에 따라 변경이 불가능한 ID 클레임을 발급하려고 할 수 있습니다. 예를 들어 원본 앵커의 특성으로 ms-ds-consistencyguid를 선택했고 특성이 해당 항목에 대한 값을 갖는 경우 ms-ds-consistencyguid로 변경이 불가능한 ID를 발급하려고 할 것이고 그렇지 않으면 변경이 불가능한 ID로 objectGuid를 발급하려고 할 것입니다. 아래 설명된 대로 사용자 지정 클레임 규칙의 집합을 생성할 수 있습니다.

**규칙 1(쿼리 특성)**

    c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname"]
    => add(store = "Active Directory", types = ("http://contoso.com/ws/2016/02/identity/claims/objectguid", "http://contoso.com/ws/2016/02/identity/claims/msdsconcistencyguid"), query = "; objectGuid,ms-ds-consistencyguid;{0}", param = c.Value);

이 규칙에서는 Active Directory의 사용자에 대한 ms-ds-consistencyguid 및 objectguid의 값을 단순히 쿼리합니다. 저장소 이름을 ADFS 배포에 사용할 수 있는 적절한 저장소 이름으로 변경하고, 클레임 형식을 objectGUID 및 ms-ds-consistencyguid에 대해 정의된 페더레이션에 있는 적절한 클레임 형식으로 변경합니다. 내 테스트 환경에서 사용자 지정 클레임 형식을 정의했습니다.

또한 'issue'가 아닌 'add'를 사용하여 엔터티에 대해 나가는 발급을 추가하지 않고 단지 중간 값으로 값을 사용합니다. 변경이 불가능한 ID로 사용할 값을 설정하면 이후 규칙에서 클레임을 발급합니다.

**규칙 2: (사용자에 대한 ms-ds-consistencyguid가 있는지 확인)**

    NOT EXISTS([Type == "http://contoso.com/ws/2016/02/identity/claims/msdsconcistencyguid"])
    => add(Type = "urn:anandmsft:tmp/idflag", Value = "useguid");

이 규칙은 단순히 사용자에 대해 채워진 ms-ds-concistencyguid가 없는 경우 "useguid"로 설정된 임시 플래그 "idflag"를 정의합니다. 이 이면에 숨겨진 논리는 ADFS가 빈 클레임을 허용하지 않는다는 사실입니다. 따라서 규칙 1에서 클레임 http://contoso.com/ws/2016/02/identity/claims/objectguid 및 http://contoso.com/ws/2016/02/identity/claims/msdsconcistencyguid가 추가된 경우 사용자에 대해 값이 채워진 경우에만 msdsconsistencyguid 클레임으로 끝납니다. 값이 채워지지 않은 경우 ADFS는 빈 값이 되고 삭제되는 것을 볼 수 있습니다. 알다시피 모든 개체에는 ObjectGuid가 있으므로 규칙 1이 실행된 후 항상 클레임이 발생합니다.

**규칙 3: ms-ds-consistencyguid를 변경이 불가능한 ID(있는 경우)로 발급** c:[Type == "http://contoso.com/ws/2016/02/identity/claims/msdsconcistencyguid"] => issue(Type = "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier", Value = c.Value);

이는 암시적 EXIST 확인입니다. 클레임에 대한 값이 있으면 변경이 불가능한 ID로 이를 발급합니다. nameidentifier 클레임을 발급하는 중입니다. 사용자 환경에서 변경이 불가능한 ID에 대한 적절한 클레임 유형으로 변경해야 합니다.

**규칙 4: ms-ds-consistencyGuid가 없는 경우 변경이 불가능한 ID로 개체 Guid 발급**

    c1:[Type == "urn:anandmsft:tmp/idflag", Value =~ "useguid"]
    && c2:[Type == "http://contoso.com/ws/2016/02/identity/claims/objectguid"]
    => issue(Type = "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier", Value = c2.Value);

이 규칙에서는 단순히 임시 플래그 'idflag'를 확인하고 값을 기반으로 클레임을 발급할지 여부를 결정합니다.

> [AZURE.NOTE] 이러한 규칙 시퀀스는 중요합니다.

<!---HONumber=AcomDC_0427_2016-->