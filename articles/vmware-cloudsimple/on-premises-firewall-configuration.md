---
title: 온-프레미스에서 간단한 클라우드로 Azure VMware 솔루션에 액세스
titleSuffix: Azure VMware Solution by CloudSimple
description: 방화벽을 통해 온-프레미스 네트워크에서 CloudSimple으로 Azure VMware 솔루션 에 액세스
author: sharaths-cs
ms.author: dikamath
ms.date: 08/08/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 539665c4756a7dc87078922421b45a88404f58f1
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81868136"
---
# <a name="accessing-your-cloudsimple-private-cloud-environment-and-applications-from-on-premises"></a>온-프레미스에서 CloudSimple 프라이빗 클라우드 환경 및 애플리케이션에 액세스

Azure ExpressRoute 또는 사이트 간 VPN을 사용하여 온-프레미스 네트워크에서 CloudSimple에 대한 연결을 설정할 수 있습니다.  연결을 사용하여 프라이빗 클라우드에서 실행하는 모든 워크로드와 CloudSimple 프라이빗 클라우드 vCenter에 액세스합니다.  온-프레미스 네트워크의 방화벽을 사용하여 연결에서 열리는 포트를 제어할 수 있습니다.  이 문서에서는 일반적인 응용 프로그램 포트 요구 사항 중 일부에 대해 설명합니다.  다른 응용 프로그램의 경우 포트 요구 사항에 대한 응용 프로그램 설명서를 참조하십시오.

## <a name="ports-required-for-accessing-vcenter"></a>vCenter 에 액세스하는 데 필요한 포트

프라이빗 클라우드 vCenter 및 NSX-T 관리자에 액세스하려면 아래 표에 정의된 포트를 온-프레미스 방화벽에서 열어야 합니다.  

| 포트       | 원본                           | 대상                      | 용도                                                                                                                |
|------------|----------------------------------|----------------------------------|------------------------------------------------------------------------------------------------------------------------|
| 53 (UDP)   | 온-프레미스 DNS 서버          | 프라이빗 클라우드 DNS 서버        | 온-프레미스 네트워크에서 프라이빗 클라우드 DNS 서버로 *az.cloudsimple.io* DNS 조회를 전달하는 데 필요합니다.       |
| 53 (UDP)   | 프라이빗 클라우드 DNS 서버        | 온-프레미스 DNS 서버          | DNS를 전달하는 데 필요한 것은 프라이빗 클라우드 vCenter에서 온-프레미스 DNS 서버로 온-프레미스 도메인 이름을 찾습니다. |
| 80 (TCP)   | 온-프레미스 네트워크              | 프라이빗 클라우드 관리 네트워크 | *http에서* *https로*vCenter URL을 리디렉션하는 데 필요합니다.                                                           |
| 443(TCP)  | 온-프레미스 네트워크              | 프라이빗 클라우드 관리 네트워크 | 온-프레미스 네트워크에서 vCenter 및 NSX-T 관리자에 액세스하는 데 필요합니다.                                             |
| 8000 (TCP) | 온-프레미스 네트워크              | 프라이빗 클라우드 관리 네트워크 | 온-프레미스에서 프라이빗 클라우드로 가상 시스템의 vMotion에 필요합니다.                                            |
| 8000 (TCP) | 프라이빗 클라우드 관리 네트워크 | 온-프레미스 네트워크              | 프라이빗 클라우드에서 온-프레미스로 가상 시스템의 vMotion에 필요합니다.                                            |

## <a name="ports-required-for-using-on-premises-active-directory-as-an-identity-source"></a>온-프레미스 활성 디렉터리를 ID 원본으로 사용하는 데 필요한 포트

온-프레미스 활성 디렉터리를 Private Cloud vCenter에서 ID 원본으로 구성하려면 테이블에 정의된 포트를 열어야 합니다.  구성 단계에 [대 한 CloudSimple 개인 클라우드에 vCenter에 대 한 ID 공급자로 Azure AD 사용을 참조](https://docs.microsoft.com/azure/vmware-cloudsimple/azure-ad/) 하십시오.

| 포트         | 원본                           | 대상                                         | 용도                                                                                                                                          |
|--------------|----------------------------------|-----------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------|
| 53 (UDP)      | 프라이빗 클라우드 DNS 서버        | 온-프레미스 DNS 서버                             | DNS를 전달하는 데 필요한 것은 프라이빗 클라우드 vCenter에서 온-프레미스 DNS 서버로 온-프레미스 활성 디렉터리 도메인 이름을 찾습니다.          |
| 389(TCP/UDP) | 프라이빗 클라우드 관리 네트워크 | 온-프레미스 활성 디렉터리 도메인 컨트롤러     | 사용자 인증을 위해 프라이빗 클라우드 vCenter 서버에서 활성 디렉터리 도메인 컨트롤러로 LDAP 통신에 필요합니다.                |
| 636(TCP)     | 프라이빗 클라우드 관리 네트워크 | 온-프레미스 활성 디렉터리 도메인 컨트롤러     | 개인 클라우드 vCenter 서버에서 사용자 인증을 위한 활성 디렉터리 도메인 컨트롤러에 대한 LDAP(보안 LDAPS) 통신에 필요합니다. |
| 3268(TCP)    | 프라이빗 클라우드 관리 네트워크 | 온-프레미스 활성 디렉터리 글로벌 카탈로그 서버 | 다중 도메인 컨트롤러 배포에서 LDAP 통신에 필요합니다.                                                                        |
| 3269(TCP)    | 프라이빗 클라우드 관리 네트워크 | 온-프레미스 활성 디렉터리 글로벌 카탈로그 서버 | 다중 도메인 컨트롤러 배포에서 LDAPS 통신에 필요합니다.                                                                       |                                           |

## <a name="common-ports-required-for-accessing-workload-virtual-machines"></a>워크로드 가상 시스템에 액세스하는 데 필요한 공통 포트

Private Cloud에서 실행되는 액세스 워크로드 가상 시스템에는 온-프레미스 방화벽에서 포트를 열어야 합니다.  아래 표는 필요한 공통 포트중 일부와 그 용도를 보여줍니다.  응용 프로그램 별 포트 요구 사항은 응용 프로그램 설명서를 참조하십시오.

| 포트         | 원본                         | 대상                          | 용도                                                                              |
|--------------|--------------------------------|--------------------------------------|--------------------------------------------------------------------------------------|
| 22 (TCP)      | 온-프레미스 네트워크            | 프라이빗 클라우드 워크로드 네트워크       | 프라이빗 클라우드에서 실행되는 Linux 가상 머신에 대한 보안 셸 액세스.              |
| 3389 (TCP)    | 온-프레미스 네트워크            | 프라이빗 클라우드 워크로드 네트워크       | 프라이빗 클라우드에서 실행되는 가상 머신을 Windows로 이동합니다.                 |
| 80 (TCP)      | 온-프레미스 네트워크            | 프라이빗 클라우드 워크로드 네트워크       | 프라이빗 클라우드에서 실행되는 가상 머신에 배포된 모든 웹 서버에 액세스합니다.        |
| 443(TCP)     | 온-프레미스 네트워크            | 프라이빗 클라우드 워크로드 네트워크       | Private Cloud에서 실행되는 가상 시스템에 배포된 모든 보안 웹 서버에 액세스합니다. |
| 389(TCP/UDP) | 프라이빗 클라우드 워크로드 네트워크 | 온-프레미스 활성 디렉터리 네트워크 | Windows 워크로드 가상 컴퓨터를 온-프레미스 활성 디렉터리 도메인에 조인합니다.       |
| 53 (UDP)      | 프라이빗 클라우드 워크로드 네트워크 | 온-프레미스 네트워크                  | 온-프레미스 DNS 서버에 워크로드 가상 시스템에 대한 DNS 서비스 액세스.         |

## <a name="next-steps"></a>다음 단계

* [VLAN 및 서브넷 생성 및 관리](https://docs.microsoft.com/azure/vmware-cloudsimple/create-vlan-subnet/)
* [Azure ExpressRoute를 사용하여 온-프레미스 네트워크에 연결](https://docs.microsoft.com/azure/vmware-cloudsimple/on-premises-connection/)
* [온-프레미스에서 사이트 간 VPN 설정](https://docs.microsoft.com/azure/vmware-cloudsimple/vpn-gateway/)
