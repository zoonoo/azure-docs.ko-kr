---
title: Check Point Cloudguard Connect를 사용하여 Azure 가상 허브 보호
description: Azure 가상 허브를 보호하기 위한 Check Point CloudGuard Connect에 대해 알아보기
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: conceptual
ms.date: 10/30/2020
ms.author: victorh
ms.openlocfilehash: 3c61dc689d19e1a7d6f9b6dbefae846e9458d750
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93146861"
---
# <a name="secure-virtual-hubs-using-check-point-cloudguard-connect"></a>Check Point Cloudguard Connect를 사용하여 가상 허브 보호

Check Point CloudGuard Connect는 Azure Firewall Manager의 신뢰할 수 있는 보안 파트너입니다. 고급 위협 방지 기능을 통해 전 세계에 분산된 지점과 인터넷 간(B2I) 연결 또는 가상 네트워크와 인터넷 간(V2I) 연결을 보호합니다. 

Azure Firewall Manager의 간단한 구성을 사용하여 CloudGuard Connect SECaaS(Security as a Service)를 통해 인터넷에 분기 허브 및 가상 네트워크 연결을 라우팅할 수 있습니다. 트래픽은 허브에서 IPsec VPN 터널의 Check Point 클라우드 서비스로 전송되는 동안 보호됩니다.

Check Point 포털에서 자동 동기화를 사용하도록 설정하면 Azure Portal에서 *보안됨* 으로 표시된 모든 리소스가 자동으로 보호됩니다. 자산을 두 번 관리할 필요가 없습니다. Azure Portal에서 한 번만 보호하도록 선택하면 됩니다.

Check Point는 여러 보안 서비스를 하나로 통합합니다. 통합 보안 트래픽은 한 번 해독되며 단일 패스로 검사됩니다. 애플리케이션 제어, URL 필터링 및 콘텐츠 인식(DLP)은 안전한 웹 사용을 시행하고 데이터를 보호합니다. IPS 및 바이러스 백신은 알려진 네트워크 익스플로잇으로부터 사용자를 보호합니다. Anti-Bot은 명령 및 제어 서버에 대한 연결을 차단하고 호스트가 감염된 경우 경고를 표시합니다.

위협 에뮬레이션(샌드박싱)은 알려지지 않은 제로 데이 위협으로부터 사용자를 보호합니다. Check Point SandBlast Zero-Day Protection은 클라우드에 의해 호스팅되는 샌드박싱 기술이며, 파일을 신속하게 격리 및 검사합니다. 가상 샌드박스에서 실행되며 악성 동작이 네트워크에 침투하기 전에 발견해 냅니다. 위협에 대응하는 데 귀중한 시간을 낭비하지 않기 위해 피해가 발생하기 전에 위협을 방지합니다. 

## <a name="deployment-example"></a>배포 예

신뢰할 수 있는 Azure 보안 파트너로 Check Point CloudGuard Connect를 배포하는 방법을 보려면 다음 비디오를 시청합니다.

> [!VIDEO https://www.youtube.com/embed/C8AuN76DEmU]

## <a name="next-steps"></a>다음 단계

- [보안 파트너 공급자 배포](deploy-trusted-security-partner.md)