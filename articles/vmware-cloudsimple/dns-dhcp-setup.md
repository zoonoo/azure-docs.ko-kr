---
title: Azure VMware 솔루션 (AVS)-AVS 사설 클라우드에 대 한 워크 로드 DNS 및 DHCP 설정
description: AVS 사설 클라우드 환경에서 실행 되는 응용 프로그램 및 워크 로드에 대해 DNS 및 DHCP를 설정 하는 방법을 설명 합니다.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: f2a5cae868f2d8f3689f05dd9d466715ab2008a3
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024690"
---
# <a name="set-up-dns-and-dhcp-applications-and-workloads-in-your-avs-private-cloud"></a>AVS 사설 클라우드에서 DNS 및 DHCP 응용 프로그램 및 워크 로드 설정

AVS 사설 클라우드 환경에서 실행 되는 응용 프로그램 및 작업에는 조회 및 IP 주소 할당을 위한 이름 확인 및 DHCP 서비스가 필요 합니다. 이러한 서비스를 제공 하려면 적절 한 DHCP 및 DNS 인프라가 필요 합니다. 이러한 서비스를 사용자의 AVS 사설 클라우드 환경에 제공 하도록 가상 컴퓨터를 구성할 수 있습니다. 

## <a name="prerequisites"></a>필수 조건

* VLAN이 구성 된 분산 포트 그룹
* 온-프레미스 또는 인터넷 기반 DNS 서버로 설정 라우팅
* 가상 컴퓨터 템플릿 또는 ISO로 가상 컴퓨터 만들기

## <a name="linux-based-dns-server-setup"></a>Linux 기반 DNS 서버 설치

Linux에서는 DNS 서버를 설정 하기 위한 다양 한 패키지를 제공 합니다. 다음은 오픈 소스 바인드 DNS 서버를 설정 하는 방법에 대 한 지침과 함께 [DigitalOcean의 설정 예제](https://www.digitalocean.com/community/tutorials/how-to-configure-bind-as-a-private-network-dns-server-on-ubuntu-18-04) 입니다.

## <a name="windows-based-setup"></a>Windows 기반 설치

이 Microsoft 항목에서는 Windows server를 DNS 서버 및 DHCP 서버로 설정 하는 방법을 설명 합니다.

* [Windows Server를 DNS 서버로](https://docs.microsoft.com/windows-server/networking/dns/dns-top)
* [DHCP 서버로 서의 Windows Server](https://docs.microsoft.com/windows-server/networking/technologies/dhcp/dhcp-top)
