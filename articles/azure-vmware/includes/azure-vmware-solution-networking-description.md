---
title: Azure VMware Solution 네트워킹 및 연결
description: Azure VMware Solution 네트워킹 및 연결에 대해 설명합니다.
ms.topic: include
ms.date: 09/28/2020
ms.openlocfilehash: 47b5fab8430f4e462f1e4a0e8cb6ddb69ec38c02
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/29/2020
ms.locfileid: "92924988"
---
<!-- Used in introduction.md and concepts-networking.md -->

Azure VMware Solution은 온-프레미스 및 Azure 기반 환경 또는 리소스에서 액세스할 수 있는 프라이빗 클라우드 환경을 제공합니다. Azure ExpressRoute 및 VPN 연결과 같은 서비스는 연결을 제공합니다. 이러한 서비스를 사용하려면 특정 네트워크 주소 범위와 방화벽 포트가 필요합니다.

프라이빗 클라우드가 배포되면 관리, 프로비저닝 및 vMotion을 위한 개인 네트워크가 만들어집니다. 이러한 개인 네트워크를 사용하여 vCenter 및 NSX-T Manager와 가상 머신 vMotion 또는 배포에 액세스합니다.  ExpressRoute Global Reach는 프라이빗 클라우드를 온-프레미스 환경에 연결하는 데 사용됩니다. 연결하려면 구독에서 ExpressRoute 회로를 사용하는 가상 네트워크가 필요합니다.

웹 서버 및 가상 머신과 같은 리소스는 Azure Virtual WAN 공용 IP 기능을 통해 인터넷에 액세스할 수 있습니다.  기본적으로 인터넷 액세스는 새로운 프라이빗 클라우드에 대해 사용하지 않도록 설정됩니다. 자세한 내용은 [Azure VMware Solution에서 공용 IP 기능을 사용하는 방법](../public-ip-usage.md)을 참조하세요.