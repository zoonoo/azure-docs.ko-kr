<properties
	pageTitle="Azure AD Connect Health FAQ"
	description="이 FAQ는 Azure AD Connect Health에 대한 질문에 답변합니다. 이 FAQ는 요금 청구 모델, 기능, 제한 및 지원을 포함한 서비스 사용에 대한 질문을 다룹니다."
	services="active-directory"
	documentationCenter=""
	authors="billmath"
	manager="stevenpo"
	editor="curtand"/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/14/2016"
	ms.author="vakarand"/>


# Azure AD Connect Health FAQ(질문과 대답)

이 FAQ는 Azure AD Connect Health에 대한 질문에 답변합니다. 이 FAQ는 요금 청구 모델, 기능, 제한 및 지원을 포함한 서비스 사용에 대한 질문을 다룹니다.

## 일반적인 질문



**Q: 여러 Azure AD 디렉터리를 관리합니다. Azure Active Directory Premium을 사용하는 테넌트로 전환하려면 어떻게 해야 하나요?**

맨 위 오른쪽 모서리에 있는 현재 로그인한 사용자 이름을 선택하고 적절한 계정을 선택하면 서로 다른 Azure AD 디렉터리 간에 전환할 수 있습니다. 계정이 여기에 나열되어 있지 않으면 로그아웃을 선택한 다음 Azure Active Directory Premium이 사용하도록 설정된 디렉터리의 글로벌 관리자 자격 증명을 사용하여 로그인합니다.

## 설치 관련 질문



**Q: 개별 서버에 Azure AD Connect Health Agent를 설치하면 어떤 영향이 있나요?**

ADFS 서버 또는 도메인 컨트롤러에 Microsoft Identity Health Agent를 설치해도 CPU, 메모리 소비, 네트워크 대역폭 및 저장소에는 최소한의 영향만 미칩니다.

아래 숫자는 근사치입니다.

- CPU 사용: ~1% 증가
- 메모리 소비: 전체 시스템 메모리의 최대 10%
- 네트워크 대역폭 사용: ~1MB/ADFS 요청 1000개

>[AZURE.NOTE]Azure와 통신할 수 없는 에이전트의 경우 에이전트는 정의된 최대 제한까지 로컬로 데이터를 저장합니다. 에이전트가 제한에 도달해서 에이전트가 서비스에 데이터를 업로드할 수 없는 경우 새 ADFS 트랜잭션은 "오래 전에 서비스된 항목" 기준으로 "캐시된" 트랜잭션을 덮어씁니다.

- AD Health Agent의 로컬 버퍼 저장소: ~20MB
- 감사 채널에 필요한 데이터 저장소


모든 데이터를 처리하려면 AD Health Agent의 AD FS 감사 채널에 대해 1,024MB(1GB)의 디스크 공간을 프로비전하는 것이 좋습니다.

**Q: Azure AD Connect Health Agent를 설치하는 동안 내 서버를 다시 부팅해야 하나요?**

아니요. 에이전트를 설치하는 데 서버를 다시 부팅할 필요는 없습니다. 그러나 일부 필수 구성 요소 설치 단계에서 서버를 다시 부팅해야 할 수 있습니다.

예를 들어 Windows Server 2008 R2에 .Net 4.5 Framework를 설치하는 경우 서버를 다시 부팅해야 합니다.


**Q: Azure AD Connect Health Services는 통과 http 프록시를 통해 작동하나요?**

예. 진행 중인 작업은 HTTP 프록시를 사용하여 아웃바운드 http 요청을 전달하도록 상태 에이전트를 구성할 수 있습니다. 자세한 내용은 [HTTP 프록시를 사용하도록 Azure AD Connect Health 에이전트 구성](active-directory-aadconnect-health-agent-install.md#configure-azure-ad-connect-health-agents-to-use-http-proxy)을 참조하세요.

에이전트를 등록하는 동안 프록시를 구성해야 하는 경우 Internet Explorer 프록시 설정을 수정해야 합니다. <br> Internet Explorer -> 설정 -> 인터넷 옵션 -> 연결 -> LAN 설정을 엽니다.<br> LAN에 프록시 서버 사용을 선택합니다.<br> HTTP 및 HTTPS/보안의 프록시 포트가 다른 경우 고급을 선택합니다.<br>


**Q: Azure AD Connect Health Services는 Http 프록시에 연결할 때 기본 인증을 지원하나요?**

아니요. 기본 인증에 필요한 임의 사용자 이름/암호 지정 메커니즘은 현재 지원되지 않습니다.


**Q: AD DS용 Azure AD Connect Health에서 어떤 버전의 AD DS를 지원하나요?**

다음 OS 버전에 설치된 AD DS의 모니터링이 지원됩니다.

- Windows Server 2008 R2
- Windows Server 2012
- Windows Server 2012 R2

## 작업 관련 질문



**Q: 내 AD FS 응용 프로그램 프록시 서버 또는 웹 응용 프로그램 프록시 서버에서 감사를 사용하도록 설정해야 하나요?**

아니요, AD FS 응용 프로그램 프록시 서버나 웹 응용 프로그램 프록시 서버에서 감사를 사용하도록 설정하지 않아도 됩니다. 감사는 AD FS 페더레이션 서버에서만 사용하도록 설정하면 됩니다.



**Q: Azure AD Connect Health 경고는 어떻게 해결하나요?**

Azure AD Connect Health 경고는 성공 조건에서 해결됩니다. Azure AD Connect Health Agent가 정기적으로 성공 조건을 검색하여 서비스에 보고합니다. 일부 경고의 경우 시간을 기준으로 경고가 제거됩니다. 즉, 동일한 오류 조건이 경고 생성으로부터 72시간 내에 관찰되지 않으면 경고가 자동으로 해결됩니다.




**Q: Azure AD Connect Health Agent가 작동하도록 하기 위해 열어야 하는 방화벽 포트는 무엇인가요?**

Azure AD Connect Health 에이전트가 Azure AD Health 서비스 끝점과 통신할 수 있도록 하려면 TCP/UDP 포트 80, 443 및 5671을 열어야 합니다.


**Q: Azure AD Connect Health 포털에 같은 이름을 가진 두 대의 서버가 표시되는 이유는 무엇인가요?**

서버에서 에이전트를 제거하는 경우 서버는 Azure AD Connect 포털에서 자동으로 제거되지 않습니다. 따라서 수동으로 서버에서 에이전트를 제거하거나 서버 자체를 제거한 경우 Azure AD Connect Health 포털에서 서버 항목을 수동으로 삭제해야 합니다. 자세한 내용은 [서버 또는 서비스 인스턴스 삭제](active-directory-aadconnect-health-operations.md#delete-a-server-or-service-instance)를 참조하세요. 또한 서버를 재이미징하거나 동일한 세부 정보(예: 컴퓨터 이름)로 새 서버를 만들었지만 Azure AD Connect Health 포털에서 서버를 제거하지 않고 새 서버에 에이전트를 설치한 경우 서버에 대해 두 개의 항목이 표시될 수 있습니다. 이 경우 이전 서버에 속한 항목을 수동으로 삭제해야 합니다. 이 항목이 있는 데이터는 일반적으로 만료됩니다.

## 관련 링크

* [Azure AD Connect Health](active-directory-aadconnect-health.md)
* [Azure AD Connect Health Agent 설치](active-directory-aadconnect-health-agent-install.md)
* [Azure AD Connect Health 작업](active-directory-aadconnect-health-operations.md)
* [AD FS와 함께 Azure AD Connect Health 사용](active-directory-aadconnect-health-adfs.md)
* [동기화에 대한 Azure AD Connect Health 사용](active-directory-aadconnect-health-sync.md)
* [AD DS와 함께 Azure AD Connect Health 사용](active-directory-aadconnect-health-adds.md)
* [Azure AD Connect Health 버전 내역](active-directory-aadconnect-health-version-history.md)

<!---HONumber=AcomDC_0720_2016-->