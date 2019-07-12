---
title: VMware 솔루션 CloudSimple 서비스
description: CloudSimple 서비스 및 개념의 개요를 설명합니다.
author: sharaths-cs
ms.author: b-shsury
ms.date: 04/24/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 0bebc68129ee2ff79241bcefec1ce0c3ca0b472d
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67595305"
---
# <a name="cloudsimple-service-overview"></a>CloudSimple 서비스 개요

CloudSimple 서비스를 사용 하 여 Azure CloudSimple VMware 솔루션을 사용할 수 있습니다. 서비스를 만든 후 노드를 프로 비전 하 고, 노드를 예약 하 고, 사설 클라우드를 만들 수 있습니다. CloudSimple 서비스를 사용할 수 있는 각 Azure 지역에서 CloudSimple 서비스를 만듭니다. 서비스는 CloudSimple 하 여 Azure VMware 솔루션의 경계 네트워크를 정의합니다. 경계 네트워크는 VPN, Azure ExpressRoute 및 인터넷 연결에 사설 클라우드를 포함 하는 서비스를 지원 합니다.

## <a name="gateway-subnet"></a>게이트웨이 서브넷

게이트웨이 서브넷을 CloudSimple 서비스별 필요 하며 생성 된 지역에 고유 합니다. 게이트웨이 서브넷 경계 네트워크를 만들 때 사용 되 고 을/28 필요 CIDR 블록입니다. 게이트웨이 서브넷 주소 공간은 고유 해야 합니다. CloudSimple 환경과 통신 하는 네트워크와 겹치지 않아야 합니다. CloudSimple와 통신 하는 네트워크는 온-프레미스 네트워크와 Azure 가상 네트워크를 포함 합니다. 게이트웨이 서브넷을 만든 후 삭제할 수 없습니다. 게이트웨이 서브넷은 서비스가 삭제 되 면 제거 됩니다.

## <a name="next-steps"></a>다음 단계

* 에 대해 알아봅니다 하는 방법 [Azure에서 CloudSimple 서비스를 만들](quickstart-create-cloudsimple-service.md)합니다.
