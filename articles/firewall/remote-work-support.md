---
title: Azure 방화벽 원격 작업 지원
description: 이 문서에서는 Azure 방화벽이 원격 인력 요구 사항을 지원하는 방법을 보여 줍니다.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: victorh
ms.openlocfilehash: 5abe9344b0512433c48df50335cce5cf1e3e3547
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80289642"
---
# <a name="azure-firewall-remote-work-support"></a>Azure 방화벽 원격 작업 지원

Azure 방화벽은 Azure 가상 네트워크 리소스를 보호하는 관리되는 클라우드 기반 네트워크 보안 서비스입니다. 고가용성 및 무제한 클라우드 확장성이 내장되어 있는 서비스 형태의 완전한 상태 저장 방화벽입니다. 

## <a name="firewall-rules"></a>방화벽 규칙

Azure 방화벽을 사용하여 Azure 방화벽 [DNAT 규칙을](rule-processing.md)사용하여 Azure 가상 네트워크에 대한 VDI(가상 데스크톱 인프라) 인바운드 RDP 액세스를 보호할 수 있습니다. WVD(Windows 가상 데스크톱)는 가상 네트워크에 대한 인바운드 액세스를 열 필요가 없습니다. 그러나 가상 네트워크에서 실행되는 WVD 가상 시스템에 대한 아웃바운드 네트워크 연결 집합을 허용해야 합니다. 자세한 내용은 [Windows 가상 데스크톱이란 무엇입니까?](../virtual-desktop/overview.md#requirements)

Azure 방화벽 응용 프로그램 규칙을 사용하여 이 아웃바운드 액세스를 구성할 수 있습니다. 자세한 내용은 [자습서: Azure 포털을 사용하여 Azure 방화벽 배포 및 구성을](tutorial-firewall-deploy-portal.md)참조하세요.

## <a name="next-steps"></a>다음 단계

[Windows 가상 데스크톱에](https://docs.microsoft.com/azure/virtual-desktop/)대해 자세히 알아보십시오.