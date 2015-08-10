<properties 
	pageTitle="디렉터리 통합 도구 비교" 
	description="이 페이지는 다양한 디렉터리 통합 도구를 비교하는 포괄적인 표를 제공합니다." 
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
	ms.date="06/24/2015" 
	ms.author="billmath"/>

# 디렉터리 통합 도구 비교

디렉터리 통합 도구는 수년에 걸쳐 성장하고 발전했습니다. 이 문서는 이러한 도구에 대한 통합 뷰와 각각에서 사용할 수 있는 기능에 대한 비교를 제공하는데 도움이 됩니다.

>[AZURE.NOTE]Azure AD Connect는 이전에 DirSync 및 AAD 동기화로 릴리스된 구성 요소와 기능을 통합합니다. 이러한 도구는 이제 개별적으로 릴리스되지 않습니다. 향후의 모든 향상 기능은 Azure AD Connect 업데이트에 포함되므로, 최신 기능을 구할 위치를 항상 알 수 있습니다.
>
>현재는 디렉터리 동기화가 계속 지원되지만 이후 특정 시점이 되면 더 이상 사용되지 않을 것입니다. 더 이상 사용되지 않게 되면 특정 기간 동안만 지원됩니다. 해당 기간 후에는 디렉터리 동기화 지원이 종료됩니다.


각 테이블에 다음 키를 사용합니다.

● = 현재 사용 가능</br> FR = 이후 릴리스</br> PP = 공개 미리 보기</br>


## 온-프레미스와 클라우드 간 동기화

| 기능 | Azure Active Directory 연결 | Azure Active Directory 동기화 서비스(AAD Sync) | Azure Active Directory 동기화 도구(디렉터리 동기화)| Forefront Identity Manager(FIM) 2010 R2 |
| :-------- |:--------:|:--------:|:--------:|:--------:
| 단일 온-프레미스 AD 포리스트에 연결 | ● | ● | ● | ● |
| 여러 온-프레미스 AD 포리스트에 연결 |● | ● | | ● |
| 여러 온-프레미스 Exchange 조직에 연결 | ● | | | |
| 단일 온-프레미스 LDAP 디렉터리에 연결 | FR | | | ● |
| 여러 온-프레미스 LDAP 디렉터리에 연결 |FR | | | ● |
| 온-프레미스 AD 및 온-프레미스 LDAP 디렉터리에 연결 |FR | | | ● |
| 사용자 지정 시스템(예: SQL, Oracle, MySQL 등)에 연결 | FR | | | ● |
| 고객 정의 특성(디렉터리 확장) 동기화 | ● | | | |

## 클라우드와 온-프레미스 간 동기화

| 기능 | Azure Active Directory 연결 | Azure Active Directory 동기화 서비스 | Azure Active Directory 동기화 도구(디렉터리 동기화) | Forefront Identity Manager(FIM) 2010 R2 |
| :-------- |:--------:|:--------:|:--------:|:--------:
| 장치의 쓰기 저장 | ● | | ● | |
| 특성 쓰기 저장(Exchange 하이브리드 배포) | ● | ● | ● | ● |
| 사용자 및 그룹 개체의 쓰기 저장 | ●| | | |
| 암호의 쓰기 저장(SSPR(셀프 서비스 암호 재설정) 및 암호 변경에서) | ● | ● | | |



## 인증 기능 지원

| 기능 | Azure Active Directory 연결 | Azure Active Directory 동기화 서비스 | Azure Active Directory 동기화 도구(디렉터리 동기화) | Forefront Identity Manager(FIM) 2010 R2 |
| :-------- |:--------:|:--------:|:--------:|:--------:
| 단일 온-프레미스 AD 포리스트에 대한 암호 동기화 | ● | ● | ● | |
| 여러 온-프레미스 AD 포리스트에 대한 암호 동기화 | ●| ● | | |
| 페더레이션이 있는 Single Sign-On | ● | ● | ● | ● |
| 암호의 쓰기 저장(SSPR 및 암호 변경에서) |● | ● | | |



## 설정 및 설치

| 기능 | Azure Active Directory 연결 | Azure Active Directory 동기화 서비스 | Azure Active Directory 동기화 도구(디렉터리 동기화) | Forefront Identity Manager(FIM) 2010 R2 |
| :-------- |:--------:|:--------:|:--------:|:--------:
| 도메인 컨트롤러에서 설치 지원 | ● | ● | ● | |
| SQL Express를 사용하여 설치 지원 | ● | ● | ● | |
| 디렉터리 동기화에서 쉽게 업그레이드 |● | | | |
| 지역화 Windows Server 언어 | FR | ● | ● | |
| Windows Server 2008 및 Windows Server 2008 R2 지원 | ● 동기화용, 페더레이션용이 아님| ● | ● | ● |
| Windows Server 2012 및 Windows Server 2012 R2 지원 | ● | ● | ● | 2012만 해당 |


## 필터링 및 구성

기능 | Azure Active Directory 연결 | Azure Active Directory 동기화 서비스 | Azure Active Directory 동기화 도구(디렉터리 동기화) | Forefront Identity Manager(FIM) 2010 R2  
:-------- |:--------:|:--------:|:--------:|:--------:|
도메인 및 조직 구성 단위에 대한 필터링 | ● | ● | ● | ●  
개체의 특성 값에 대한 필터링 | ● | ● | ● | ● 
최소 특성 집합이 동기화되도록 허용(MinSync) | ● | ● | |   
다양한 서비스 템플릿이 특성 흐름에 적용되도록 허용 |● | ● | |   
AD에서 Azure AD로의 흐름에서 특성 제거 허용 | ● | ● | |  
특성 흐름에 대한 고급 사용자 지정 허용 | ● | ● | | ●  

<!---HONumber=July15_HO5-->