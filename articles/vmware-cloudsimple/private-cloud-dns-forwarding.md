---
title: Azure VMware 솔루션-사설 클라우드에서 온-프레미스로 DNS 전달
description: CloudSimple 사설 클라우드 DNS 서버가 온-프레미스 리소스의 조회를 전달 하도록 설정 하는 방법을 설명 합니다.
author: sharaths-cs
ms.author: b-shsury
ms.date: 02/29/2020
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: aa2af4302613aad23bfd78b4883bbb46c5e5ddbb
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/01/2020
ms.locfileid: "76961128"
---
# <a name="enable-cloudsimple-private-cloud-dns-servers-to-forward-dns-lookup-of-on-premises-resources-to-your-dns-servers"></a>CloudSimple 사설 클라우드 DNS 서버에서 온-프레미스 리소스의 DNS 조회를 DNS 서버로 전달 하도록 설정

사설 클라우드 DNS 서버는 모든 온-프레미스 리소스에 대 한 DNS 조회를 DNS 서버로 전달할 수 있습니다.  조회를 사용 하면 사설 클라우드 vSphere 구성 요소가 온-프레미스 환경에서 실행 되는 모든 서비스를 조회 하 고 FQDN (정규화 된 도메인 이름)을 사용 하 여 통신할 수 있습니다.

## <a name="scenarios"></a>시나리오 

온-프레미스 DNS 서버에 대 한 DNS 조회를 전달 하면 다음과 같은 시나리오에서 사설 클라우드를 사용할 수 있습니다.

* 온-프레미스 VMware 솔루션에 대 한 재해 복구 설정으로 사설 클라우드 사용
* 사설 클라우드 vSphere 대 한 id 원본으로 온-프레미스 Active Directory 사용
* 온-프레미스에서 사설 클라우드로 가상 컴퓨터를 마이그레이션하는 데 HCX 사용

## <a name="before-you-begin"></a>시작하기 전에

DNS 전달이 작동 하려면 사설 클라우드 네트워크에서 온-프레미스 네트워크로 네트워크 연결이 있어야 합니다.  다음을 사용 하 여 네트워크 연결을 설정할 수 있습니다.

* [Express 경로를 사용 하 여 온-프레미스에서 CloudSimple로 연결](on-premises-connection.md)
* [사이트 간 VPN gateway 설정](https://docs.microsoft.com/azure/vmware-cloudsimple/vpn-gateway#set-up-a-site-to-site-vpn-gateway)

DNS 전달이 작동 하려면이 연결에서 방화벽 포트를 열어야 합니다.  사용 되는 포트는 TCP 포트 53 또는 UDP 포트 53입니다.

> [!NOTE]
> 사이트 간 VPN을 사용 하는 경우 온-프레미스 DNS 서버 서브넷을 온-프레미스 접두사의 일부로 추가 해야 합니다.

## <a name="request-dns-forwarding-from-private-cloud-to-on-premises"></a>사설 클라우드에서 온-프레미스로 DNS 전달 요청

사설 클라우드에서 온-프레미스로 DNS 전달을 사용 하도록 설정 하려면 [지원 요청](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)을 제출 하 여 다음 정보를 제공 합니다.

* 문제 유형: **기술**
* 구독: **CloudSimple 서비스가 배포 된 구독**
* 서비스: **CloudSimple 별 VMware 솔루션**
* 문제 유형: **advise 또는 어떻게 할까요?** ...
* 문제 하위 유형: **NW의 도움 필요**
* 세부 정보 창에서 온-프레미스 도메인의 도메인 이름을 입력 합니다.
* 세부 정보 창의 사설 클라우드에서 조회를 전달할 온-프레미스 DNS 서버 목록을 제공 합니다.

## <a name="next-steps"></a>다음 단계

* [온-프레미스 방화벽 구성에 대 한 자세한 정보](on-premises-firewall-configuration.md)
* [온-프레미스 DNS 서버 구성](on-premises-dns-setup.md)
