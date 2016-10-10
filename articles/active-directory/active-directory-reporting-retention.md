<properties
	pageTitle="Azure Active Directory 보고서 보존 정책 | Microsoft Azure"
	description="Azure Active Directory에서 보고서 데이터 보존 정책"
	services="active-directory"
	documentationCenter=""
	authors="dhanyahk"
	manager="femila"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="identity"
	ms.date="03/07/2016"
	ms.author="dhanyahk"/>

# Azure Active Directory 보고서 보존 정책

*이 설명서는 [Azure Active Directory Reporting 가이드](active-directory-reporting-guide.md)의 일부입니다.*

Azure AD(Azure Active Directory)의 보고서는 특정 일수 동안 여기에 지정된 데이터를 보존합니다.

보고서 | 설명
------------------------------------------------------- | -----------
알 수 없는 원본에서 로그인 | 30일
여러 번의 실패 후 로그인 | 30일
여러 지역에서의 로그인 | 30일
의심스러운 작업이 있는 IP 주소에서 로그인 | 30일
감염 가능성이 있는 장치에서 로그인 | 30일
비정상적인 로그인 작업 | 30일
비정상적인 로그인 활동을 포함하는 사용자 | 30일
자격 증명이 손실된 사용자 | 30일
감사 보고서 | 180일
암호 재설정 활동(Azure AD) | 30일
암호 재설정 활동(ID 관리자) | 30일
암호 재설정 등록 활동(Azure AD) | 30일
암호 재설정 등록 활동(ID 관리자) | 30일
셀프 서비스 그룹 활동(Azure AD) | 30일
셀프 서비스 그룹 활동(ID 관리자) | 30일
응용 프로그램 사용 현황 | 30일
계정 프로비전 활동 | 30일
암호 롤오버 상태 | 30일
계정 프로비전 오류 | 30일
RMS 사용 현황 | 30일
가장 활동적인 RMS 사용자 | 30일
RMS 장치 사용 현황 | 30일
RMS 사용 응용 프로그램 사용 현황 | 30일

<!---HONumber=AcomDC_0928_2016-->