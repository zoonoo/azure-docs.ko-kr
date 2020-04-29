---
title: Azure 방화벽 원격 작업 지원
description: 이 문서에서는 Azure 방화벽이 원격 작업 강제 요구 사항을 지 원하는 방법을 보여 줍니다.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: victorh
ms.openlocfilehash: 5abe9344b0512433c48df50335cce5cf1e3e3547
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80289642"
---
# <a name="azure-firewall-remote-work-support"></a>Azure 방화벽 원격 작업 지원

Azure 방화벽은 Azure virtual network 리소스를 보호 하는 관리 되는 클라우드 기반 네트워크 보안 서비스입니다. 고가용성 및 무제한 클라우드 확장성이 내장되어 있는 서비스 형태의 완전한 상태 저장 방화벽입니다. 

## <a name="firewall-rules"></a>방화벽 규칙

Azure 방화벽을 사용 하 여 azure 방화벽 [Dnat 규칙](rule-processing.md)을 사용 하 여 azure 가상 네트워크에 대 한 VDI (가상 데스크톱 인프라) 인바운드 RDP 액세스를 보호할 수 있습니다. WVD (Windows 가상 데스크톱)를 사용 하 여 가상 네트워크에 대 한 인바운드 액세스를 열 필요가 없습니다. 그러나 가상 네트워크에서 실행 되는 WVD 가상 컴퓨터에 대해 아웃 바운드 네트워크 연결 집합을 허용 해야 합니다. 자세한 내용은 [Windows 가상 데스크톱 이란?](../virtual-desktop/overview.md#requirements) 을 참조 하세요.

Azure 방화벽 응용 프로그램 규칙을 사용 하 여이 아웃 바운드 액세스를 구성할 수 있습니다. 자세한 내용은 [자습서: Azure Portal 사용 하 여 Azure 방화벽 배포 및 구성](tutorial-firewall-deploy-portal.md)을 참조 하세요.

## <a name="next-steps"></a>다음 단계

[Windows 가상 데스크톱](https://docs.microsoft.com/azure/virtual-desktop/)에 대해 자세히 알아보세요.