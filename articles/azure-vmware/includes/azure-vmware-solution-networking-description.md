---
title: Azure VMWare Solution 네트워킹 및 연결
description: Azure VMWare Solution 네트워킹 및 연결에 대해 설명합니다.
ms.topic: include
ms.date: 09/28/2020
ms.openlocfilehash: 1d43a8a49fc56a6a5b534a89fd1dfc2f5ae2d478
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2020
ms.locfileid: "91574462"
---
<!-- Used in introduction.md and concepts-networking.md -->

Azure VMware Solution은 온-프레미스 및 Azure 기반 환경 또는 리소스에서 액세스할 수 있는 프라이빗 클라우드 환경을 제공합니다. Azure ExpressRoute 및 VPN 연결과 같은 서비스는 연결을 제공합니다. 이러한 서비스를 사용하려면 특정 네트워크 주소 범위와 방화벽 포트가 필요합니다.

프라이빗 클라우드가 배포되면 관리, 프로비저닝 및 vMotion을 위한 개인 네트워크가 만들어집니다. 이러한 개인 네트워크를 사용하여 vCenter 및 NSX-T Manager와 가상 머신 vMotion 또는 배포에 액세스합니다.  ExpressRoute Global Reach는 프라이빗 클라우드를 온-프레미스 환경에 연결하는 데 사용됩니다. 연결하려면 구독에서 ExpressRoute 회로를 사용하는 가상 네트워크가 필요합니다.



>[!NOTE]
>프라이빗 클라우드를 배포할 때 프로덕션 네트워크에서 VM을 사용할 수 있도록 인터넷 및 Azure 서비스에 대한 액세스가 프로비저닝되고 제공됩니다.  인터넷 액세스는 기본적으로 새 프라이빗 클라우드에 사용하지 않도록 설정되며, 언제든지 사용하거나 사용하지 않도록 설정할 수 있습니다.