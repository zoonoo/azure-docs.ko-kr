<properties title="Configure Active Directory for Azure RemoteApp" pageTitle="Azure RemoteApp에 대해 Active Directory 구성" description="Azure RemoteApp를 사용 하도록 Active Directory를 설정 하는 방법에 알아봅니다" metaKeywords="" services="" solutions="" documentationCenter="" authors="elizapo"  />

<tags ms.service="remoteapp" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="12/08/2014" ms.author="elizapo" />

#Azure RemoteApp에 대해 Active Directory 구성


RemoteApp 하이브리드 컬렉션의 경우 온-프레미스 Active Directory 도메인 인프라와 디렉터리 통합(및 선택적으로 Single Sign-On)이 포함된 Azure Active Directory 테넌트를 설정해야 합니다. 또한 온-프레미스 디렉터리에 일부 Active Directory 개체를 만들어야 합니다. 다음 정보를 사용하여 온-프레미스 Active Directory와 Azure AD를 구성한 후 통합합니다.

##온-프레미스 Active Directory 구성
먼저 온-프레미스 Active Directory를 구성합니다. 사용할 UPN 도메인 접미사를 식별한 후 RemoteApp에 대한 Active Directory 개체를 만들어야 합니다. 

Windows Server 2012 R2 환경에 Active Directory 도메인 서비스를 아직 추가하지 않은 경우 작업 방법에 대한 자세한 내용은 [이러한 지침](http://technet.microsoft.com/ko-kr/library/cc731053(v=WS.10).aspx)을 참조하세요.
###UPN 도메인 접미사 검사 및 구성
RemoteApp에 사용할 Azure AD 도메인과 일치하는 UPN 접미사로 포리스트가 구성되지 않은 경우 추가해야 합니다. 필요한 접미사가 구성되었는지 확인합니다.


1. Active Directory 사용자 및 컴퓨터를 시작합니다.
2.	도메인 이름을 확장하고 **사용자**를 클릭합니다.
3.	**관리자**를 마우스 오른쪽 단추로 클릭하고 **속성**을 클릭합니다.
4.	**계정** 탭의 **사용자 로그온 이름** 필드에서 이 도메인에 대해 구성된 UPN 이름을 확인합니다.
5.	RemoteApp 컬렉션에 사용할 도메인 이름과 일치하는 접미사가 표시되지 않는 경우 다음을 수행합니다.
	1.	Active Directory 도메인 및 트러스트를 시작합니다.
	2.	**Active Directory 도메인 및 트러스트**를 마우스 오른쪽 단추로 클릭하고 **속성**을 클릭합니다.
	3.	접미사 목록을 검토합니다.
	4.	상자에 도메인 이름의 FQDN을 입력하고 **추가**, **확인**을 차례로 클릭합니다. 예: contoso.com. 

		여기서 접미사에 "@"를 포함하지 마세요.

이제 새 사용자를 만들 때 사용자 로그온 이름에서 새 접미사를 선택할 수 있습니다. 또한 사용자 속성의 계정 탭에서 기존 사용자의 접미사를 변경할 수 있습니다.

자세한 내용은 [UPN 접미사 추가](http://technet.microsoft.com/library/cc772007.aspx)를 참조하세요.

###Active Directory에 RemoteApp에 대한 개체 만들기
RemoteApp을 사용하려면 온-프레미스 Active Directory에 다음 두 개의 개체가 있어야 합니다.


- RDSH 끝점을 온-프레미스 도메인에 연결하여 RemoteApp 프로그램에 도메인 리소스에 대한 액세스 권한을 제공할 서비스 계정.
- RemoteApp 컴퓨터 개체를 포함할 OU(조직 구성 단위). 필수는 아니지만 OU를 통해 RemoteApp에 사용할 계정과 정책을 격리시키는 것이 좋습니다.

다음 정보를 사용하여 이러한 각 개체를 만듭니다.

####서비스 계정 만들기:


1. Active Directory 사용자 및 컴퓨터를 시작합니다.
2.	**사용자**를 마우스 오른쪽 단추로 클릭한 후 **새로 만들기 > 사용자**를 클릭합니다.
3.	RemoteApp 서비스 계정의 사용자 이름과 암호를 입력합니다.

	**참고**: RemoteApp 컬렉션을 만들 때 이 계정 정보가 필요합니다.

####새 OU(조직 구성 단위) 만들기:


1. Active Directory 사용자 및 컴퓨터에서 해당 도메인을 마우스 오른쪽 단추로 클릭합니다. **새로 만들기 > 조직 구성 단위**를 클릭합니다.
2. RemoteApp에 대해 만든 서비스 계정을 새 OU에 추가합니다.
3.

	이렇게 하려면 만든 서비스 계정을 찾습니다. 계정을 마우스 오른쪽 단추로 클릭하고 **이동**을 클릭합니다. 새 OU를 선택하고 **확인**을 클릭합니다.


1. 이 
OU에 컴퓨터를 추가하고 삭제할 수 있는 권한을 RemoteApp 서비스 계정에 부여:
	1. Active Directory 사용자 및 컴퓨터 스냅인에서 **보기 > 고급 기능**을 클릭합니다. 속성 정보에 **보안** 탭이 추가됩니다.
	2. RemoteApp 서비스 OU를 마우스 오른쪽 단추로 클릭하고 **속성**을 클릭합니다.
	3. **보안** 탭에서 **추가**를 클릭합니다. RemoteApp 서비스 계정 사용자 개체를 선택하고 **확인**을 클릭합니다.
	4. **고급**을 클릭합니다.
	5. **사용 권한** 탭에서 RemoteApp 서비스 계정을 선택하고 **편집**을 클릭합니다.
	6. **적용 대상** 필드에서 **이 개체 및 모든 하위 개체**를 선택합니다.
	7. **사용 권한** 필드에서 컴퓨터 개체 만들기 및 컴퓨터 개체 삭제 옆의 **허용**을 선택하고 **확인**을 클릭합니다. 
	8. 나머지 두 창에서 **확인**을 클릭합니다.


##Azure Active Directory 구성
이제 온-프레미스 Active Directory가 설정되었으며 Azure AD로 넘어가겠습니다. 온-프레미스 도메인의 UPN 도메인 접미사와 일치하는 사용자 지정 도메인을 만들고 디렉터리 통합을 설정해야 합니다. 하이브리드 컬렉션은 Windows Server Active Directory 배포에서 DirSync와 같은 도구를 사용하여 동기화된 Azure Active Directory 계정만 지원합니다. 특히 암호 동기화 옵션을 사용하여 동기화되거나 AD FS(Active Directory Federation Services) 페더레이션을 구성하여 동기화됩니다. 

다음 정보를 사용하여 Azure Active Directory를 구성합니다.


- [도메인 추가](http://technet.microsoft.com/ko-kr/library/hh969247.aspx) - 이 정보를 사용하여 온-프레미스 Active Directory 도메인의 UPN 도메인 접미사와 일치하는 도메인을 추가합니다.
- [디렉터리 통합](http://technet.microsoft.com/ko-kr/library/jj573653.aspx) - 이 정보를 사용하여 디렉터리 통합 옵션([암호 동기화 사용 DirSync](http://technet.microsoft.com/ko-kr/library/dn441214.aspx) 또는 [페더레이션 사용 DirSync](http://technet.microsoft.com/ko-kr/library/dn441213.aspx))을 선택합니다.

[MFA(Multi-Factor Authentication)](http://technet.microsoft.com/ko-kr/library/dn249466.aspx)를 구성할 수도 있습니다.

<!--HONumber=35.2-->
