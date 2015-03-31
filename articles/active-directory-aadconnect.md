<properties pageTitle="Azure Active Directory 연결" description="Azure Active Directory 연결 마법사는 온-프레미스 Windows Server Active Directory를 Azure Active Directory와 연결하는 데 사용되는 단일 도구 및 단계별 환경입니다." services="active-directory" documentationCenter="" authors="Gayana" manager="terrylan" editor="" />

<tags ms.service="active-directory" ms.workload="identity" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="02/27/2015" ms.author="gabag" />

<h1 id="vnettut1">Azure Active Directory 연결</h1>

Azure Active Directory 연결 마법사는 온-프레미스 디렉터리를 Azure Active Directory와 연결하는 데 사용되는 단일 도구 및 단계별 환경입니다.  이 마법사는 동기화 서비스, 암호 동기화 또는 AD FS(Active Directory Federation Services) 등의 디렉터리 통합을 실행하고 Azure AD PowerShell 모듈과 같은 필수 조건을 얻는 데 필요한 모든 구성 요소를 배포 및 구성합니다.

>[AZURE.NOTE] **Azure Active Directory 연결은 이전에 DirSync 및 AAD 동기화로 릴리스된 기능을 포함합니다. 이러한 도구는 더 이상 개별적으로 릴리스되지 않습니다.** 

> **이제 Azure Active Directory 연결 한 곳에서 동기화, 로그온 및 기타 온-프레미스와 Azure AD 통합의 모든 사항을 처리할 수 있습니다.**


현재 Azure Active Directory 동기화 도구(DirSync), Azure Active Directory 동기화 서비스(AAD 동기화) 또는 Forefront Identity Manager 2010 R2를 평가하거나 사용 중인 경우 자세한 내용은 [디렉터리 통합 도구](http://msdn.microsoft.com/library/azure/dn757582.aspx "Directory Integration Tools")를 참조하세요.


## Azure Active Directory 연결 공개 미리 보기 

현재 Azure Active Directory 연결 공개 미리 보기에서는 하나 이상의 Windows Server Active Directory 포리스트를 Azure AD와 통합할 수 있는 단계별 환경을 제공합니다. 

[Azure AD 연결 공개 미리 보기 다운로드](http://connect.microsoft.com/site1164/program8612 "Azure Active Directory Connect") 

이 미리 보기 버전의 Azure Active Directory 연결에서는 다음을 수행할 수 있습니다. 

- 4번의 클릭으로 기본 설정을 선택하여 단일 포리스트를 빠르고 쉽게 구성
- 사용자 지정 설정을 선택하여 여러 포리스트를 구성하거나 SSO(Single Sign-On) 환경을 위한 AD FS 선택
- Exchange 하이브리드 모드, 암호 쓰기 저장 및 대체 ID 특성과 같은 추가 동기화 옵션 구성

## 추가 리소스
[Azure Active Directory 설명서](http://azure.microsoft.com/documentation/services/active-directory/)

<!--HONumber=47-->
