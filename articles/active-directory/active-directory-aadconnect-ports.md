<properties
	pageTitle="Azure AD Connect: 포트 | Microsoft Azure"
	description="이 페이지는 Azure AD Connect에 대해 열려야 하는 포트에 대한 기술 참조 페이지입니다."
	services="active-directory"
	documentationCenter=""
	authors="billmath"
	manager="femila"
	editor="curtand"/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/25/2016"
	ms.author="billmath"/>

# 포트 및 프로토콜이 필요한 하이브리드 ID
다음 문서는 하이브리드 ID 솔루션을 구현하는 데 필요한 필수 포트 및 프로토콜에 정보를 제공하는 기술 참조입니다. 다음 그림을 사용하고 해당 테이블을 참조합니다.

![Azure AD Connect의 정의](./media/active-directory-aadconnect-ports/required1.png)

## 테이블 1 - Azure AD Connect 및 온-프레미스 AD
이 테이블은 Azure AD Connect 서버 및 온-프레미스 AD 간의 통신에 필요한 포트와 프로토콜에 대해 설명합니다.

프로토콜 | 포트 | 설명
--------- | --------- |---------
DNS|53(TCP/UDP)| DNS는 대상 포리스트에 대해 조회합니다.
Kerberos|88(TCP/UDP)| AD 포리스트에 대한 Kerberos 인증.
MS-RPC |135(TCP/UDP)| AD 포리스트를 바인딩할 때 Azure AD Connect 마법사의 초기 구성 중에 사용합니다.
LDAP|389(TCP/UDP)| AD에서 데이터를 가져오기 위해 사용합니다. 데이터가 Kerberos 서명 및 봉인으로 암호화됩니다.
LDAP/SSL|636(TCP/UDP)| AD에서 데이터를 가져오기 위해 사용합니다. 데이터 전송이 서명 및 암호화합니다. SSL을 사용하는 경우에만 사용합니다.
RPC |49152- 65535(임의의 높은 RPC 포트)(TCP/UDP)| AD 포리스트를 바인딩할 때 Azure AD Connect의 초기 구성 중에 사용합니다. 자세한 내용은 [KB929851](https://support.microsoft.com/kb/929851), [KB832017](https://support.microsoft.com/kb/832017) 및 [KB224196](https://support.microsoft.com/kb/224196)을 참조하세요.

## 테이블 2 - Azure AD Connect 및 Azure AD
이 테이블은 Azure AD Connect 서버 및 Azure AD 간의 통신에 필요한 포트와 프로토콜에 대해 설명합니다.

프로토콜 |포트 |설명
--------- | --------- |---------
HTTP|80(TCP/UDP)| CRL(인증서 해지 목록)를 다운로드하여 SSL 인증서를 확인하는 데 사용합니다.
HTTPS|443(TCP/UDP)| Azure AD와 동기화하는 데 사용합니다.

방화벽에서 열어야 하는 URL 및 IP 주소의 목록은 [Office 365 URL 및 IP 주소 범위](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)를 참조하세요.

## 테이블 3 - Azure AD Connect 및 페더레이션 서버/WAP
이 테이블은 Azure AD Connect 서버 및 페더레이션 서버/WAP 서버 간의 통신에 필요한 포트와 프로토콜에 대해 설명합니다.

프로토콜 |포트 |설명
--------- | --------- |---------
HTTP|80(TCP/UDP)| CRL(인증서 해지 목록)를 다운로드하여 SSL 인증서를 확인하는 데 사용합니다.
HTTPS|443(TCP/UDP)| Azure AD와 동기화하는 데 사용합니다.
WinRM|5985| WinRM 수신기

## 테이블 4 - WAP 및 페더레이션 서버
이 테이블은 페더레이션 서버 및 WAP 서버 간의 통신에 필요한 포트와 프로토콜에 대해 설명합니다.

프로토콜 |포트 |설명
--------- | --------- |---------
HTTPS|443(TCP/UDP)| 인증에 사용합니다.

## 테이블 5 - WAP 및 사용자
이 테이블은 사용자 및 WAP 서버 간의 통신에 필요한 포트와 프로토콜에 대해 설명합니다.

프로토콜 |포트 |설명
--------- | --------- |--------- |
HTTPS|443(TCP/UDP)| 장치 인증에 사용합니다.
TCP|49443(TCP)| 인증서 인증에 사용합니다.

## 테이블 6a & 6b - (AD FS/동기화)와 Azure AD에 대한 Azure AD Connect Health 에이전트
다음 테이블은 Azure AD Connect Health 에이전트와 Azure AD 간의 통신에 필요한 끝점, 포트 및 프로토콜에 대해 설명합니다.

### 테이블 6a - Azure AD Connect Health 에이전트(AD FS/동기화)와 Azure AD에 대한 포트 및 프로토콜
이 테이블은 Azure AD Connect Health 에이전트 및 Azure AD 간의 통신에 필요한 다음 아웃바운드 포트와 프로토콜에 대해 설명합니다.

프로토콜 |포트 |설명
--------- | --------- |--------- |
HTTPS|443(TCP/UDP)| 아웃바운드
Azure 서비스 버스|5671(TCP/UDP)| 아웃바운드

### 6b - Azure AD Connect Health 에이전트(AD FS/동기화)와 Azure AD에 대한 끝점
끝점의 목록에 대한 자세한 내용은 [Azure AD Connect Health 에이전트에 대한 요구 사항 섹션](active-directory-aadconnect-health-agent-install.md#requirements)을 참조하세요.

<!---HONumber=AcomDC_0928_2016-->