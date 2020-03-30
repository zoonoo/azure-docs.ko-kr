---
title: 클라우드간이별 Azure VMware 솔루션 - 프라이빗 클라우드용 워크로드 DNS 및 DHCP 설정
description: CloudSimple 프라이빗 클라우드 환경에서 실행되는 애플리케이션 및 워크로드에 대해 DNS 및 DHCP를 설정하는 방법에 대해 설명합니다.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: ef1266b783034cf18dc2b3ea4be5ebc01bc12c70
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024690"
---
# <a name="set-up-dns-and-dhcp-applications-and-workloads-in-your-cloudsimple-private-cloud"></a>클라우드심플 프라이빗 클라우드에서 DNS 및 DHCP 애플리케이션 및 워크로드 설정

프라이빗 클라우드 환경에서 실행되는 응용 프로그램 및 워크로드에는 조회 및 IP 주소 할당을 위한 이름 확인 및 DHCP 서비스가 필요합니다.  이러한 서비스를 제공하려면 적절한 DHCP 및 DNS 인프라가 필요합니다.  프라이빗 클라우드 환경에서 이러한 서비스를 제공하도록 가상 컴퓨터를 구성할 수 있습니다.  

## <a name="prerequisites"></a>사전 요구 사항

* VLAN이 구성된 분산 포트 그룹
* 온-프레미스 또는 인터넷 기반 DNS 서버로 설정 라우팅
* 가상 컴퓨터 템플릿 또는 ISO를 사용하여 가상 컴퓨터를 만듭니다.

## <a name="linux-based-dns-server-setup"></a>리눅스 기반 DNS 서버 설정

리눅스는 DNS 서버를 설정하기위한 다양한 패키지를 제공합니다.  다음은 오픈 소스 BIND DNS 서버를 설정하기 위한 지침과 함께 [DigitalOcean의 예제 설정입니다.](https://www.digitalocean.com/community/tutorials/how-to-configure-bind-as-a-private-network-dns-server-on-ubuntu-18-04)

## <a name="windows-based-setup"></a>윈도우 기반 설정

이러한 Microsoft 항목에서는 Windows 서버를 DNS 서버와 DHCP 서버로 설정하는 방법을 설명합니다.

* [DNS 서버로 윈도우 서버](https://docs.microsoft.com/windows-server/networking/dns/dns-top)
* [DHCP 서버로 윈도우 서버](https://docs.microsoft.com/windows-server/networking/technologies/dhcp/dhcp-top)
