---
title: CloudSimple service 별 VMware 솔루션
description: CloudSimple 서비스 및 개념에 대 한 개요를 설명 합니다.
author: sharaths-cs
ms.author: b-shsury
ms.date: 04/24/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: a43fbebb21be82fd751778d6fec95e0ee9c346ad
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/06/2019
ms.locfileid: "68812516"
---
# <a name="cloudsimple-service-overview"></a>CloudSimple 서비스 개요

CloudSimple 서비스를 사용 하 여 CloudSimple로 Azure VMware 솔루션을 사용할 수 있습니다. 서비스를 만든 후 노드를 프로 비전 하 고, 노드를 예약 하 고, 사설 클라우드를 만들 수 있습니다. CloudSimple 서비스를 사용할 수 있는 각 Azure 지역에서 CloudSimple 서비스를 만듭니다. 서비스는 CloudSimple로 Azure VMware 솔루션의에 지 네트워크를 정의 합니다. Edge 네트워크는 VPN, Azure Express 경로 및 사설 클라우드에 대 한 인터넷 연결을 포함 하는 서비스를 지원 합니다.

## <a name="gateway-subnet"></a>게이트웨이 서브넷

게이트웨이 서브넷은 CloudSimple 서비스 마다 필요 하며 생성 된 지역에 대해 고유 합니다. 게이트웨이 서브넷은에 지 네트워크를 만들 때 사용 되며/28 CIDR 블록이 필요 합니다. 게이트웨이 서브넷 주소 공간은 고유 해야 합니다. CloudSimple 환경과 통신 하는 네트워크와 겹치지 않아야 합니다. CloudSimple과 통신 하는 네트워크에는 온-프레미스 네트워크 및 Azure virtual network가 포함 됩니다. 게이트웨이 서브넷을 만든 후에는 삭제할 수 없습니다. 게이트웨이 서브넷은 서비스를 삭제할 때 제거 됩니다.

## <a name="next-steps"></a>다음 단계

* [Azure에서 CloudSimple 서비스를 만드는](quickstart-create-cloudsimple-service.md)방법에 대해 알아봅니다.
