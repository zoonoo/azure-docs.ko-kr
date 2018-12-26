---
title: Azure Firewall 규칙 처리 논리
description: Azure Firewall 규칙 처리 논리 알아보기
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 9/27/2018
ms.author: victorh
ms.openlocfilehash: 542682037a932a2e3b08c71b38b64b2694ad40f3
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/26/2018
ms.locfileid: "47228181"
---
# <a name="azure-firewall-rule-processing-logic"></a>Azure Firewall 규칙 처리 논리
Azure Firewall에는 NAT 규칙, 네트워크 규칙 및 응용 프로그램 규칙이 있습니다. 규칙은 규칙 유형에 따라 처리됩니다.


## <a name="network-rules-and-applications-rules"></a>네트워크 규칙 및 응용 프로그램 규칙 
네트워크 규칙이 가장 먼저 적용되고, 다음으로 응용 프로그램 규칙이 적용됩니다. 규칙은 종료됩니다. 따라서 네트워크 규칙에서 일치 항목이 발견된 경우 응용 프로그램 규칙이 처리되지 않습니다.  네트워크 규칙이 일치하지 않고 패킷 프로토콜이 HTTP/HTTPS이면 응용 프로그램 규칙에 따라 패킷이 평가됩니다. 여전히 일치하는 항목이 없으면 인프라 규칙 컬렉션과 비교하여 패킷이 평가됩니다. 여전히 일치하는 항목이 없으면 패킷이 기본적으로 거부됩니다.

## <a name="nat-rules"></a>NAT 규칙
[자습서: Azure Portal을 사용하여 Azure Firewall DNAT를 통해 인바운드 트래픽 필터링](tutorial-firewall-dnat.md)에 설명된 대로 DNAT(Destination Network Address Translation)를 구성하여 인바운드 연결을 사용할 수 있습니다. DNAT 규칙이 가장 먼저 적용됩니다. 일치 항목이 발견되면 변환된 트래픽을 허용하도록 암시적인 해당 네트워크 규칙이 추가됩니다. 변환된 트래픽을 일치시키는 거부 규칙을 사용하여 네트워크 규칙 컬렉션을 명시적으로 추가함으로써 이 동작을 재정의할 수 있습니다. 이러한 연결에는 응용 프로그램 규칙이 적용되지 않습니다.


## <a name="next-steps"></a>다음 단계

- [Azure Azure Firewall을 배포 및 구성](tutorial-firewall-deploy-portal.md)하는 방법에 대해 알아봅니다.