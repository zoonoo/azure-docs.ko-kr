---
title: Check Point Cloudguard Connect를 사용 하 여 Azure 가상 허브 보호
description: Azure 가상 허브를 보호 하기 위한 Check Point CloudGuard Connect에 대해 알아봅니다.
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: conceptual
ms.date: 10/30/2020
ms.author: victorh
ms.openlocfilehash: 759b8211aa8790e81c273f3d90d6679e70012ea4
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93133661"
---
# <a name="secure-virtual-hubs-using-check-point-cloudguard-connect"></a>Check Point Cloudguard Connect를 사용 하 여 가상 허브 보호

Check Point CloudGuard Connect는 Azure 방화벽 관리자의 신뢰할 수 있는 보안 파트너입니다. 또한 고급 위협 방지를 사용 하 여 전역으로 분산 된 지사 (B2I) 또는 V2I (virtual network to Internet) 연결을 보호 합니다. 

Azure 방화벽 관리자에서 간단한 구성을 사용 하 여 CloudGuard Connect security as a service (SECaaS)를 통해 지점 허브 및 가상 네트워크 연결을 인터넷으로 라우팅할 수 있습니다. 트래픽은 허브에서 IPsec VPN 터널의 Check Point 클라우드 서비스로 전송 될 때 보호 됩니다.

Check Point 포털에서 자동 동기화를 사용 하도록 설정 하면 Azure Portal에서 *보안이* 설정 된 것으로 표시 된 모든 리소스가 자동으로 보호 됩니다. 자산을 두 번 관리할 필요가 없습니다. Azure Portal에서 한 번만 보안을 선택 하기만 하면 됩니다.

Check Point는 단일 우산에서 여러 보안 서비스를 통합 합니다. 통합 보안 트래픽은 한 번 해독 되 고 단일 패스에서 검사 됩니다. 응용 프로그램 컨트롤, URL 필터링 및 콘텐츠 인식 (DLP)은 안전한 웹 사용을 적용 하 고 데이터를 보호 합니다. IP와 바이러스 백신은 알려진 네트워크 악용 으로부터 사용자를 보호 합니다. 봇은 명령 및 제어 서버에 대 한 연결을 차단 하 고 호스트가 감염 된 경우 경고를 표시 합니다.

샌드 박싱 (위협 에뮬레이션)은 알 수 없는 0 일 위협 으로부터 사용자를 보호 합니다. Check Point SandBlast Zero-Day Protection은 파일이 빠르게 격리 되 고 검사 되는 클라우드 호스팅 샌드 박싱 기술입니다. 네트워크에 들어가기 전에 악의적인 동작을 검색 하기 위해 가상 샌드박스에서 실행 됩니다. 위협에 대응 하는 직원의 귀중 한 시간을 절약 하기 위해 손상이 발생 하기 전에는 위협을 방지 합니다. 

## <a name="deployment-example"></a>배포 예제

다음 동영상을 시청 하 여 Check Point CloudGuard Connect를 신뢰할 수 있는 Azure 보안 파트너로 배포 하는 방법을 확인 하세요.

> [!VIDEO https://youtu.be/C8AuN76DEmU]

## <a name="next-steps"></a>다음 단계

- [보안 파트너 공급자 배포](deploy-trusted-security-partner.md)