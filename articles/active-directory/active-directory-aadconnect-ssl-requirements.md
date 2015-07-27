<properties 
	pageTitle="Azure AD Connect - SSL 인증서 요구 사항" 
	description="AD FS와 사용하기 위한 Azure AD Connect SSL 인증서 요구 사항" 
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
	ms.date="05/28/2015" 
	ms.author="billmath"/>

# Azure AD Connect - SSL 인증서 요구 사항

**중요:** 모든 웹 응용 프로그램 프록시 서버뿐 아니라 AD FS 팜의 모든 노드에서 동일한 SSL 인증서를 사용하는 것이 가장 좋습니다.

- 이 인증서는 x509 인증서여야 합니다. 
- 테스트 랩 환경의 페더레이션 서버에서 자체 서명된 인증서를 사용할 수 있지만 프로덕션 환경의 경우 공용 CA에서 인증서를 얻는 것이 좋습니다. 
	- 공개적으로 신뢰할 수 없는 인증서를 사용하면 각 웹 응용 프로그램 프록시 서버에 설치된 인증서가 모든 페더레이션 서버 및 로컬 서버에서 신뢰할 수 있는 지 확인합니다. 
- CryptoAPI 다음 세대(CNG) 및 키 저장소 공급자를 기반으로 하는 인증서는 지원되지 않습니다. 즉, KSP(키 저장소 공급자)가 아닌 CSP(암호화 서비스 공급자)를 기반으로 인증서를 사용해야 합니다. 
- 인증서의 ID는 페더레이션 서비스 이름 (예: fs.contoso.com)과 일치해야 합니다. 
	- ID는 dNSName 유형의 주체 대체 이름(SAN)의 확장 유형이거나, SAN 항목이 없는 경우 공통 이름으로 지정된 주제 이름입니다.  
	- 그 중 하나가 페더레이션 서비스 이름과 일치하는 경우 여러 SAN 항목이 인증서에 있을 수 있습니다. 
	- 작업 공간 연결을 사용하려는 경우, 추가 SAN은 값 "enterpriseregistration." 다음에 조직의 사용자 보안 주체 이름(UPN) 접미사(예: enterpriseregistration.contoso.com)와 필요합니다. 

와일드 카드 인증서가 지원됩니다.
 

<!---HONumber=July15_HO3-->