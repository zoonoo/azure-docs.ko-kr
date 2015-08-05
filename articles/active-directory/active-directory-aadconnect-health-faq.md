<properties 
	pageTitle="Azure AD Connect Health FAQ" 
	description="이 FAQ는 Azure AD Connect Health에 대한 질문에 답변합니다. 이 FAQ는 요금 청구 모델, 기능, 제한 및 지원을 포함한 서비스 사용에 대한 질문을 다룹니다." 
	services="active-directory" 
	documentationCenter="" 
	authors="billmath" 
	manager="swadhwa" 
	editor="curtand"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/12/2015"
	ms.author="billmath"/>


# Azure AD Connect Health FAQ(질문과 대답)

이 FAQ는 Azure AD Connect Health에 대한 질문에 답변합니다. 이 FAQ는 요금 청구 모델, 기능, 제한 및 지원을 포함한 서비스 사용에 대한 질문을 다룹니다.

## 일반적인 질문



**Azure Active Directory에 여러 테넌트가 있습니다. Azure Active Directory Premium을 사용하는 테넌트로 전환하려면 어떻게 해야 하나요?**

왼쪽 탐색 모음에서 "홈"을 선택한 다음 오른쪽 위 모서리에서 현재 로그인된 사용자 이름을 선택하고 적합한 테넌트 계정을 선택하여 Azure AD 테넌트를 전환할 수 있습니다. 테넌트 계정이 여기에 나열되어 있지 않으면 로그아웃을 선택한 다음 Azure Active Directory Premium 테넌트의 글로벌 테넌트 관리자 자격 증명을 사용하여 로그인합니다.


## 설치 관련 질문



**Q: 개별 서버에 Azure AD Connect Health Agent를 설치하면 어떤 영향이 있나요?**

ADFS 서버에 Microsoft Identity Health Agent를 설치해도 CPU, 메모리 소비, 네트워크 대역폭 및 저장소에는 최소한의 영향만 미칩니다.

아래 숫자는 근사치입니다.

- CPU 사용: ~1% 증가
- 메모리 소비: 전체 시스템 메모리의 최대 10%
- 네트워크 대역폭 사용: ~1MB/ADFS 요청 1000개
>[AZURE.NOTE]에이전트가 Azure와 통신할 수 없는 경우 에이전트는 데이터를 전체 시스템 메모리의 최대 10%까지 로컬로 저장합니다. 에이전트가 전체 실제 메모리의 10%에 도달한 후, 에이전트가 데이터를 서비스로 업로드하지 못했다면, 서비스한지 가장 오래된 "캐싱된" 트랜잭션부터 새로운 ADFS 트랜잭션으로 대체됩니다.


- AD Health Agent의 로컬 버퍼 저장소: ~20MB
- 감사 채널에 필요한 데이터 저장소


모든 데이터를 처리하려면 AD Health Agent의 AD FS 감사 채널에 대해 1,024MB(1GB)의 디스크 공간을 프로비전하는 것이 좋습니다.

**Q: Azure AD Connect Health Agent를 설치하는 동안 내 서버를 다시 부팅해야 하나요?**

아니요. 에이전트를 설치하는 데 서버를 다시 부팅할 필요는 없습니다. 그러나 일부 필수 구성 요소 설치 단계에서 서버를 다시 부팅해야 할 수 있습니다.

예를 들어 Windows Server 2008 R2에 .Net 4.5 Framework를 설치하는 경우 서버를 다시 부팅해야 합니다.


**Q: Azure AD Connect Health Services는 통과 http 프록시를 통해 작동하나요?**

예, 등록 프로세스와 일반 작업 모두 아웃바운드 http 요청을 전달하도록 설정된 명시적 프록시를 통해 작동할 수 있습니다. 이 경우에는 에이전트가 Microsoft Windows HTTP Services 대신 System.Net을 사용하여 웹 요청을 수행했으므로 “Netsh WinHttp set Proxy”가 작동하지 않습니다.

Register-AdHealthAgent를 실행하기 전 언제든지 수행하세요(설치 최종 단계).


- 1단계 - machine.config 파일에 항목 추가


machine.config 파일을 찾습니다. 파일은 %windir%\Microsoft.NET\Framework64[version]\config\machine.config</li>에 있습니다.

machine.config 파일의 <configuration></configuration> 요소 아래에 다음 항목을 추가합니다.
 
		
	<system.net>  
			<defaultProxy useDefaultCredentials="true">
       		<proxy 
        usesystemdefault="true" 
        proxyaddress="http://YOUR.PROXY.HERE.com"  
        bypassonlocal="true"/>
		</defaultProxy>
	</system.net> 

 

추가 <defaultProxy> 정보는 [여기](https://msdn.microsoft.com/library/kd3cf2ex(v=vs.110).aspx)에서 확인할 수 있습니다.

이 설정은 http .NET 요청을 수행할 때 명시적으로 정의된 프록시를 사용하도록 시스템 전체의 .NET 응용 프로그램을 구성합니다. 자동 업데이트 중에 실행이 취소되므로 개별 app.config는 수정하지 않는 것이 좋습니다. 파일 한 개만 변경해야 하며 machine.config만 수정하는 경우 업데이트 시에도 유지됩니다.

- 2단계 - 인터넷 옵션에서 프록시 구성

Internet Explorer -> 설정 -> 인터넷 옵션 -> 연결 -> LAN 설정을 엽니다.

Lan에 프록시 서버 사용을 선택합니다.

HTTP 및 HTTPS/보안의 프록시 포트가 다른 경우 고급을 선택합니다.




**Q: Azure AD Connect Health Services는 Http 프록시에 연결할 때 기본 인증을 지원하나요?**

아니요. 기본 인증에 필요한 임의 사용자 이름/암호 지정 메커니즘은 현재 지원되지 않습니다.


## 작업 관련 질문



**Q: 내 AD FS 응용 프로그램 프록시 서버 또는 웹 응용 프로그램 프록시 서버에서 감사를 사용하도록 설정해야 하나요?**

아니요, AD FS 응용 프로그램 프록시 서버나 웹 응용 프로그램 프록시 서버에서 감사를 사용하도록 설정하지 않아도 됩니다. 감사는 AD FS 페더레이션 서버에서만 사용하도록 설정하면 됩니다.



**Q: Azure AD Connect Health 경고는 어떻게 해결하나요?**

Azure AD Connect Health 경고는 성공 조건에서 해결됩니다. Azure AD Connect Health Agent가 정기적으로 성공 조건을 검색하여 서비스에 보고합니다. 일부 경고의 경우 시간을 기준으로 경고가 제거됩니다. 즉 동일한 오류 조건이 경고 생성으로부터 48시간 내에 관찰되지 않으면 경고가 자동으로 해결됩니다.




**Q: Azure AD Connect Health Agent가 작동하도록 하기 위해 열어야 하는 방화벽 포트는 무엇인가요?**

Azure AD Connect Health Agent가 Azure AD Health 서비스 끝점과 통신할 수 있도록 하려면 TCP/UDP 포트 80 및 443을 열어야 합니다.

<!---HONumber=July15_HO4-->