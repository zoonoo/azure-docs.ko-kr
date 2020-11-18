---
title: Azure 방화벽 원격 작업 지원
description: 이 문서에서는 Azure 방화벽이 원격 작업 강제 요구 사항을 지 원하는 방법을 보여 줍니다.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 05/04/2020
ms.author: victorh
ms.openlocfilehash: 3c0e2033ee559af38a6816bdfa611eea86b14dea
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94658318"
---
# <a name="azure-firewall-remote-work-support"></a>Azure 방화벽 원격 작업 지원

Azure 방화벽은 Azure virtual network 리소스를 보호 하는 관리 되는 클라우드 기반 네트워크 보안 서비스입니다. 고가용성 및 무제한 클라우드 확장성이 내장되어 있는 서비스 형태의 완전한 상태 저장 방화벽입니다.

## <a name="virtual-desktop-infrastructure-vdi-deployment-support"></a>VDI (가상 데스크톱 인프라) 배포 지원

홈 정책에서 작업 하려면 많은 IT 조직이 용량, 네트워크, 보안 및 거 버 넌 스의 기본적인 변화를 해결 해야 합니다. 직원은 집에서 작업 하는 동안 온-프레미스 서비스와 연결 된 계층화 된 보안 정책에 의해 보호 되지 않습니다. Azure의 VDI (가상 데스크톱 인프라) 배포를 통해 조직에서이 변화 하는 환경에 빠르게 대응할 수 있습니다. 그러나 이러한 VDI 배포에 대 한 인바운드/아웃 바운드 인터넷 액세스를 보호 하는 방법이 필요 합니다. [위협 인텔리전스](threat-intel.md) 기반 필터링 기능과 함께 Azure 방화벽 [dnat 규칙](rule-processing.md) 을 사용 하 여 VDI 배포를 보호할 수 있습니다.

## <a name="azure-windows-virtual-desktop-support"></a>Azure Windows 가상 데스크톱 지원

Windows 가상 데스크톱은 Azure에서 실행 되는 포괄적인 데스크톱 및 앱 가상화 서비스입니다. 간단한 관리, 다중 세션 Windows 10, 엔터프라이즈 용 Microsoft 365 앱에 대 한 최적화 및 RDS (원격 데스크톱 서비스) 환경을 지 원하는 유일한 VDI (가상 데스크톱 인프라)입니다. 몇 분 내에 Azure에서 Windows 데스크톱 및 앱을 배포 하 고 크기를 조정할 수 있으며, 기본 제공 보안 및 규정 준수 기능을 활용할 수 있습니다. Windows 가상 데스크톱을 사용 하 여 가상 네트워크에 대 한 인바운드 액세스를 열 필요는 없습니다. 그러나 가상 네트워크에서 실행 되는 Windows 가상 데스크톱 가상 컴퓨터에 대해 아웃 바운드 네트워크 연결을 허용 해야 합니다. 자세한 내용은 [Azure Firewall을 사용하여 Windows Virtual Desktop 배포 보호](protect-windows-virtual-desktop.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

[Windows 가상 데스크톱](../virtual-desktop/index.yml)에 대해 자세히 알아보세요.