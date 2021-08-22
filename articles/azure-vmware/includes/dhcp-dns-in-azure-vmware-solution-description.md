---
title: Azure VMware Solution의 DHCP 및 DNS 설명
description: Azure VMware Solution DHCP 및 DNS 설명입니다.
ms.topic: include
ms.date: 05/28/2021
author: shortpatti
ms.author: v-patsho
ms.service: azure-vmware
ms.openlocfilehash: 98f2fd71cc4657a3837278c3b7a761678b8a1005
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121750110"
---
<!-- Used in tutorial-network-checklist.md and configure-dhcp-azure-vmware-solution.md -->

프라이빗 클라우드 환경에서 실행되는 애플리케이션 및 워크로드에는 조회 및 IP 주소 할당을 위한 이름 확인 및 DHCP 서비스가 필요합니다. 이러한 서비스를 제공하려면 적절한 DHCP 및 DNS 인프라가 필요합니다. 프라이빗 클라우드 환경에서 이러한 서비스를 제공하도록 가상 머신을 구성할 수 있습니다.  

WAN을 통해 브로드캐스트 DHCP 트래픽을 온-프레미스로 다시 라우팅하는 대신, NSX에 기본 제공되는 DHCP 서비스를 사용하거나 프라이빗 클라우드에서 로컬 DHCP 서버를 사용합니다.
