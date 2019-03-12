---
title: Azure Stack 네트워킹 소개 | Microsoft Docs
description: Azure Stack 네트워킹에 알아봅니다
services: azure-stack
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.topic: conceptual
ms.date: 01/14/2019
ms.author: mabrigg
ms.reviewer: scottnap
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: 67465a64f38ce4767b384cf5395179c0dbcb99fe
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/12/2019
ms.locfileid: "57769902"
---
# <a name="introduction-to-azure-stack-networking"></a>Azure Stack 네트워킹 소개

*적용 대상: Azure Stack 통합 시스템 및 Azure Stack 개발 키트*

Azure Stack 다양을 함께 또는 별도로 사용할 수 있는 네트워킹 기능을 제공 합니다.

- **Azure Stack 리소스 간의 연결**  
    클라우드의 안전한 개인 가상 네트워크에서 Azure 리소스를 서로 연결합니다.
- **인터넷 연결**  
    Azure Stack 리소스 간에 인터넷을 통해 통신 합니다.
- **온-프레미스 연결**  
    인터넷을 통해 가상 개인 네트워크 (VPN) 또는 Azure Stack에 전용된 연결을 통해 Azure Stack 리소스를 온-프레미스 네트워크를 연결 합니다.
- **부하 분산 및 트래픽 방향**  
    동일한 위치에 있는 서버에 대한 트래픽 및 다른 위치에 있는 서버에 대한 직접 트래픽의 부하를 분산합니다.
- **보안**  
    네트워크 서브넷 또는 개별 VM(가상 머신) 간에 네트워크 트래픽을 필터링합니다.
- **라우팅**  
    기본 라우팅을 사용 하거나 완벽 하 게 온-프레미스 리소스와 Azure Stack 간에 라우팅을 제어 합니다.
- **관리 효율성**  
    모니터링 및 네트워킹 리소스에 Azure Stack을 관리 합니다.
- **배포 및 구성 도구**  
    웹 기반 포털 또는 플랫폼 간 명령줄 도구를 사용하여 네트워크 리소스를 배포하고 구성합니다.


## <a name="next-steps"></a>다음 단계

* [Azure Stack 네트워킹 고려 사항](azure-stack-network-differences.md)