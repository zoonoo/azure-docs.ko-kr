---
title: 온-프레미스에서 Azure VMware Solution by CloudSimple에 액세스
titleSuffix: Azure VMware Solution by CloudSimple
description: 방화벽을 통해 온-프레미스 네트워크에서 Azure VMware Solution by CloudSimple에 액세스
author: sharaths-cs
ms.author: dikamath
ms.date: 08/08/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 3cc9ad0c694be8f95ddcffbc3d952d59b83f89f3
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106077213"
---
# <a name="accessing-your-cloudsimple-private-cloud-environment-and-applications-from-on-premises"></a>온-프레미스에서 CloudSimple 프라이빗 클라우드 환경 및 애플리케이션에 액세스

Azure ExpressRoute 또는 사이트 간 VPN을 사용하여 온-프레미스 네트워크에서 CloudSimple로 연결을 설정할 수 있습니다.  CloudSimple 프라이빗 클라우드 vCenter 및 연결을 사용하여 프라이빗 클라우드에서 실행하는 모든 워크로드에 액세스하세요.  온-프레미스 네트워크의 방화벽을 사용하여 연결에서 열리는 포트를 제어할 수 있습니다.  이 문서에서는 몇 가지 일반적인 애플리케이션 포트 요구 사항을 설명합니다.  다른 애플리케이션의 경우 포트 요구 사항에 관한 애플리케이션 설명서를 참조하세요.

## <a name="ports-required-for-accessing-vcenter"></a>vCenter 액세스에 필요한 포트

프라이빗 클라우드 vCenter 및 NSX-T 관리자에 액세스하려면 아래 표에 정의된 포트를 온-프레미스 방화벽에서 열어야 합니다.  

| 포트       | 원본                           | 대상                      | 목적                                                                                                                |
|------------|----------------------------------|----------------------------------|------------------------------------------------------------------------------------------------------------------------|
| 53(UDP)   | 온-프레미스 DNS 서버          | 프라이빗 클라우드 DNS 서버        | 온-프레미스 네트워크에서 프라이빗 클라우드 DNS 서버로 *az.cloudsimple.io* 의 DNS 조회를 전달하는 데 필요합니다.       |
| 53(UDP)   | 프라이빗 클라우드 DNS 서버        | 온-프레미스 DNS 서버          | 프라이빗 클라우드 vCenter에서 온-프레미스 DNS 서버로 온-프레미스 도메인 이름의 DNS 조회를 전달하는 데 필요합니다. |
| 80(TCP)   | 온-프레미스 네트워크              | 프라이빗 클라우드 관리 네트워크 | vCenter URL을 *http* 에서 *https* 로 리디렉션하는 데 필요합니다.                                                           |
| 443(TCP)  | 온-프레미스 네트워크              | 프라이빗 클라우드 관리 네트워크 | 온-프레미스 네트워크에서 vCenter 및 NSX-T 관리자에 액세스하는 데 필요합니다.                                             |
| 8000(TCP) | 온-프레미스 네트워크              | 프라이빗 클라우드 관리 네트워크 | 온-프레미스에서 프라이빗 클라우드로 가상 머신을 vMotion하는 데 필요합니다.                                            |
| 8000(TCP) | 프라이빗 클라우드 관리 네트워크 | 온-프레미스 네트워크              | 프라이빗 클라우드에서 온-프레미스로의 가상 머신을 vMotion하는 데 필요합니다.                                            |

## <a name="ports-required-for-using-on-premises-active-directory-as-an-identity-source"></a>온-프레미스 Active Directory를 ID 소스로 사용하는 데 필요한 포트

프라이빗 클라우드 vCenter에서 온-프레미스 Active Directory를 ID 소스로 구성하려면 표에 정의된 포트를 열어야 합니다.  구성 단계는 [CloudSimple 프라이빗 클라우드에서 Azure AD를 vCenter용 ID 공급자로 사용](./azure-ad.md)을 참조하세요.

| 포트         | 원본                           | 대상                                         | 목적                                                                                                                                          |
|--------------|----------------------------------|-----------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------|
| 53(UDP)      | 프라이빗 클라우드 DNS 서버        | 온-프레미스 DNS 서버                             | 프라이빗 클라우드 vCenter에서 온-프레미스 DNS 서버로 온-프레미스 Active Directory 도메인 이름의 DNS 조회를 전달하는 데 필요합니다.          |
| 389(TCP/UDP) | 프라이빗 클라우드 관리 네트워크 | 온-프레미스 Active Directory 도메인 컨트롤러     | 사용자 인증을 위해 프라이빗 클라우드 vCenter 서버에서 Active Directory 도메인 컨트롤러로의 LDAP 통신에 필요합니다.                |
| 636(TCP)     | 프라이빗 클라우드 관리 네트워크 | 온-프레미스 Active Directory 도메인 컨트롤러     | 사용자 인증을 위해 프라이빗 클라우드 vCenter 서버에서 Active Directory 도메인 컨트롤러로의 LDAPS(보안 LDAP) 통신에 필요합니다. |
| 3268(TCP)    | 프라이빗 클라우드 관리 네트워크 | 온-프레미스 Active Directory 글로벌 카탈로그 서버 | 다중 도메인 컨트롤러 배포의 LDAP 통신에 필요합니다.                                                                        |
| 3269(TCP)    | 프라이빗 클라우드 관리 네트워크 | 온-프레미스 Active Directory 글로벌 카탈로그 서버 | 다중 도메인 컨트롤러 배포에서 LDAPS 통신에 필요합니다.                                                                       |

## <a name="common-ports-required-for-accessing-workload-virtual-machines"></a>워크로드 가상 머신에 액세스하는 데 필요한 일반 포트

프라이빗 클라우드에서 실행되는 워크로드 가상 머신에 액세스하려면 온-프레미스 방화벽에서 포트를 열어야 합니다.  아래 표에서는 필요한 공통 포트와 용도를 보여 줍니다.  애플리케이션 관련 포트 요구 사항은 애플리케이션 설명서를 참조하세요.

| 포트         | 원본                         | 대상                          | 목적                                                                              |
|--------------|--------------------------------|--------------------------------------|--------------------------------------------------------------------------------------|
| 22(TCP)      | 온-프레미스 네트워크            | 프라이빗 클라우드 워크로드 네트워크       | 프라이빗 클라우드에서 실행되는 Linux 가상 머신을 위한 보안 셸 액세스.              |
| 3389(TCP)    | 온-프레미스 네트워크            | 프라이빗 클라우드 워크로드 네트워크       | 프라이빗 클라우드에서 실행되는 Windows 가상 머신을 위한 원격 데스크톱.                 |
| 80(TCP)      | 온-프레미스 네트워크            | 프라이빗 클라우드 워크로드 네트워크       | 프라이빗 클라우드에서 실행되는 가상 머신에 배포된 모든 웹 서버에 액세스합니다.        |
| 443(TCP)     | 온-프레미스 네트워크            | 프라이빗 클라우드 워크로드 네트워크       | 프라이빗 클라우드에서 실행되는 가상 머신에 배포된 모든 보안 웹 서버에 액세스합니다. |
| 389(TCP/UDP) | 프라이빗 클라우드 워크로드 네트워크 | 온-프레미스 Active Directory 네트워크 | Windows 워크로드 가상 머신을 온-프레미스 Active Directory 도메인에 조인합니다.       |
| 53(UDP)      | 프라이빗 클라우드 워크로드 네트워크 | 온-프레미스 네트워크                  | 온-프레미스 DNS 서버에 관한 워크로드 가상 머신용 DNS 서비스 액세스.         |

## <a name="next-steps"></a>다음 단계

* [VLAN 및 서브넷 만들기 및 관리](./create-vlan-subnet.md)
* [Azure ExpressRoute를 사용하여 온-프레미스 네트워크에 연결](./on-premises-connection.md)
* [온-프레미스에서 사이트 간 VPN 설정](./vpn-gateway.md)
