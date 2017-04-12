---
title: "포트 및 프로토콜이 필요한 하이브리드 ID - Azure | Microsoft Docs"
description: "이 페이지는 Azure AD Connect에 대해 열려야 하는 포트에 대한 기술 참조 페이지입니다."
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: curtand
ms.assetid: de97b225-ae06-4afc-b2ef-a72a3643255b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/15/2017
ms.author: billmath
translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: ee4a47cf53898803e0080d3f9d00cf7617fe4ce8
ms.lasthandoff: 03/27/2017


---
# <a name="hybrid-identity-required-ports-and-protocols"></a>포트 및 프로토콜이 필요한 하이브리드 ID
다음 문서는 하이브리드 ID 솔루션을 구현하는 포트 및 프로토콜에 대한 기술 참조입니다. 다음 그림을 사용하고 해당 테이블을 참조합니다.

![Azure AD Connect의 정의](./media/active-directory-aadconnect-ports/required3.png)

## <a name="table-1---azure-ad-connect-and-on-premises-ad"></a>테이블 1 - Azure AD Connect 및 온-프레미스 AD
이 테이블은 Azure AD Connect 서버 및 온-프레미스 AD 간의 통신에 필요한 포트와 프로토콜에 대해 설명합니다.

| 프로토콜 | 포트 | 설명 |
| --- | --- | --- |
| DNS |53(TCP/UDP) |DNS는 대상 포리스트에 대해 조회합니다. |
| Kerberos |88(TCP/UDP) |AD 포리스트에 대한 Kerberos 인증. |
| MS-RPC |135(TCP/UDP) |AD 포리스트를 바인딩할 때 Azure AD Connect 마법사의 초기 구성 중 및 암호 동기화 중에도 사용합니다. |
| LDAP |389(TCP/UDP) |AD에서 데이터를 가져오기 위해 사용합니다. 데이터가 Kerberos 서명 및 봉인으로 암호화됩니다. |
| LDAP/SSL |636(TCP/UDP) |AD에서 데이터를 가져오기 위해 사용합니다. 데이터 전송이 서명 및 암호화합니다. SSL을 사용하는 경우에만 사용합니다. |
| RPC |49152- 65535(임의의 높은 RPC 포트)(TCP/UDP) |AD 포리스트를 바인딩할 때 Azure AD Connect의 초기 구성 중 및 암호 동기화 중에 사용합니다. 자세한 내용은 [KB929851](https://support.microsoft.com/kb/929851), [KB832017](https://support.microsoft.com/kb/832017) 및 [KB224196](https://support.microsoft.com/kb/224196)을 참조하세요. |

## <a name="table-2---azure-ad-connect-and-azure-ad"></a>테이블 2 - Azure AD Connect 및 Azure AD
이 테이블은 Azure AD Connect 서버 및 Azure AD 간의 통신에 필요한 포트와 프로토콜에 대해 설명합니다.

| 프로토콜 | 포트 | 설명 |
| --- | --- | --- |
| HTTP |80(TCP/UDP) |CRL(인증서 해지 목록)를 다운로드하여 SSL 인증서를 확인하는 데 사용합니다. |
| HTTPS |443(TCP/UDP) |Azure AD와 동기화하는 데 사용합니다. |

방화벽에서 열어야 하는 URL 및 IP 주소의 목록은 [Office 365 URL 및 IP 주소 범위](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)를 참조하세요.

## <a name="table-3---azure-ad-connect-and-ad-fs-federation-serverswap"></a>테이블 3 - Azure AD Connect 및 AD FS 페더레이션 서버/WAP
이 테이블은 Azure AD Connect 서버 및 AD FS 페더레이션 서버/WAP 서버 간의 통신에 필요한 포트와 프로토콜에 대해 설명합니다.  

| 프로토콜 | 포트 | 설명 |
| --- | --- | --- |
| HTTP |80(TCP/UDP) |CRL(인증서 해지 목록)를 다운로드하여 SSL 인증서를 확인하는 데 사용합니다. |
| HTTPS |443(TCP/UDP) |Azure AD와 동기화하는 데 사용합니다. |
| WinRM |5985 |WinRM 수신기 |

## <a name="table-4---wap-and-federation-servers"></a>테이블 4 - WAP 및 페더레이션 서버
이 테이블은 페더레이션 서버 및 WAP 서버 간의 통신에 필요한 포트와 프로토콜에 대해 설명합니다.

| 프로토콜 | 포트 | 설명 |
| --- | --- | --- |
| HTTPS |443(TCP/UDP) |인증에 사용합니다. |

## <a name="table-5---wap-and-users"></a>테이블 5 - WAP 및 사용자
이 테이블은 사용자 및 WAP 서버 간의 통신에 필요한 포트와 프로토콜에 대해 설명합니다.

| 프로토콜 | 포트 | 설명 |
| --- | --- | --- |
| HTTPS |443(TCP/UDP) |장치 인증에 사용합니다. |
| TCP |49443(TCP) |인증서 인증에 사용합니다. |

## <a name="table-6a--6b---pass-through-authentication-with-single-sign-on-sso-and-password-hash-sync-with-single-sign-on-sso"></a>테이블 6a / 6b - Single Sign-on(SSO)으로 통과 인증 및 Single Sign-on(SSO)와 암호 해시 동기화
다음 테이블은 Azure AD Connect 및 Azure AD 간의 통신에 필요한 포트와 프로토콜에 대해 설명합니다.

### <a name="table-6a---pass-through-authentication-with-sso"></a>테이블 6a - SSO로 통과 인증
|프로토콜|포트 번호|설명
| --- | --- | ---
|HTTP|80|SSL과 같은 보안 유효성 검사에 아웃바운드 HTTP 트래픽을 사용하도록 설정합니다.
|HTTPS|443|    Azure AD에 대해 사용자 인증 사용하도록 설정
|HTTPS|10100–10120|    커넥터에서 다시 Azure AD로 응답 사용하도록 설정 
|Azure Service Bus|9352, 5671|    수신 요청에 대한 Azure 서비스와 커넥터 간에 통신을 사용하도록 설정합니다.
|HTTPS|9350|    선택적으로 수신 요청에 더 높은 성능을 발휘하도록 설정합니다.
|HTTPS|8080/443|    커넥터 부트스트랩 시퀀스 및 커넥터 자동 업데이트를 사용하도록 설정합니다.
|HTTPS|9090|    커넥터 등록(커넥터 등록 프로세스에만 필요)을 사용하도록 설정합니다.
|HTTPS|9091|    커넥터 신뢰 인증서 자동 갱신을 사용하도록 설정합니다.

### <a name="table-6b---password-hash-sync-with-sso"></a>테이블 6b - SSO와 암호 해시 동기화

|프로토콜|포트 번호|설명
| --- | --- | ---
|HTTPS|9090|    SSO 등록(SSO 등록 프로세스에만 필요)을 사용하도록 설정합니다.

## <a name="table-7a--7b---azure-ad-connect-health-agent-for-ad-fssync-and-azure-ad"></a>테이블 7a & 7b - (AD FS/동기화)와 Azure AD에 대한 Azure AD Connect Health 에이전트
다음 테이블은 Azure AD Connect Health 에이전트와 Azure AD 간의 통신에 필요한 끝점, 포트 및 프로토콜에 대해 설명합니다.

### <a name="table-7a---ports-and-protocols-for-azure-ad-connect-health-agent-for-ad-fssync-and-azure-ad"></a>테이블 7a - Azure AD Connect Health 에이전트(AD FS/동기화)와 Azure AD에 대한 포트 및 프로토콜
이 테이블은 Azure AD Connect Health 에이전트 및 Azure AD 간의 통신에 필요한 다음 아웃바운드 포트와 프로토콜에 대해 설명합니다.  

| 프로토콜 | 포트 | 설명 |
| --- | --- | --- |
| HTTPS |443(TCP/UDP) |아웃바운드 |
| Azure 서비스 버스 |5671(TCP/UDP) |아웃바운드 |

### <a name="7b---endpoints-for-azure-ad-connect-health-agent-for-ad-fssync-and-azure-ad"></a>7b - Azure AD Connect Health 에이전트(AD FS/동기화)와 Azure AD에 대한 끝점
끝점의 목록에 대한 자세한 내용은 [Azure AD Connect Health 에이전트에 대한 요구 사항 섹션](../connect-health/active-directory-aadconnect-health-agent-install.md#requirements)을 참조하세요.


