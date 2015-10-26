<properties
	pageTitle="Azure AD Connect FAQ | Microsoft Azure"
	description="이 페이지에는 Azure AD Connect에 대해 자주 묻는 질문과 대답이 있습니다."
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
	ms.date="10/13/2015"
	ms.author="billmath"/>

# Azure Active Directory Connect FAQ

## 빠른 설치

## 사용자 지정 설치

## 네트워크
**Q: 방화벽, 네트워크 장치 또는 네트워크에서 열려 있는 상태로 유지할 수 있는 최대 시간 연결을 제한하는 다른 것이 있습니다. Azure AD Connect를 사용하는 경우 클라이언트 족 기간 내 클라이언트 쪽 시간 제한 임계값은 얼마나 길어야 합니까?**

모든 네트워킹 소프트웨어, 물리적 장치 또는 열린 상태로 둘 수 있는 최대 시간 연결을 제한하는 다른 것은 Azure AD Connect 클라이언트를 설치한 서버와 Azure Active Directory 간의 연결에 대해 적어도 5분(300초)의 임계값을 사용해야 합니다. 이는 이전에 릴리스된 모든 Microsoft ID 동기화 도구에 적용됩니다.


**Q: Office 365 인증서를 갱신하도록 요청하는 전자 메일을 받는 경우 어떻게 해야 합니까?**

이 문서의 [여기](active-directory-aadconnect-o365-certs.md)에 나온 지침을 사용하여 인증서를 갱신하세요.

## 문제 해결

**Q: Azure AD Connect에 대한 도움을 받으려면 어떻게 합니까?**

[Microsoft KB(기술 자료) 검색](https://www.microsoft.com/ko-KR/Search/result.aspx?q=azure%20active%20directory%20connect&form=mssupport)

- Microsoft KB(기술 자료)에서 Azure AD Connect 지원에 대한 일반적인 고장 수리 문제에 대한 기술 솔루션을 검색하세요.

[Microsoft Azure Active Directory 포럼](https://social.msdn.microsoft.com/Forums/azure/ko-KR/home?forum=WindowsAzureAD)

- 커뮤니티에서 기술 질문 및 대답을 검색하고 찾아보거나 [여기](https://social.msdn.microsoft.com/Forums/azure/ko-KR/newthread?category=windowsazureplatform&forum=WindowsAzureAD&prof=required)를 클릭하여 직접 원하는 질문을 할 수 있습니다.


[Azure AD Connect 고객 지원](https://manage.windowsazure.com/?getsupport=true)

- Azure 포털을 통해 지원을 받으려면 이 링크를 사용합니다.

<!---HONumber=Oct15_HO3-->