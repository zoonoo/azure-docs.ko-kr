---
title: NSG에서 사용하도록 설정하지 않은 RDP 포트로 인해 Azure VM에 연결할 수 없음 | Microsoft Docs
description: Azure Portal의 NSG 구성으로 인해 RDP가 실패하는 문제를 해결하는 방법을 알아봅니다. | Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: cshepard
editor: v-jesits
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/20/2018
ms.author: genli
ms.openlocfilehash: c32612c411f275220f549eea79276fa5a7232fd0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60318938"
---
#  <a name="cannot-connect-remotely-to-a-vm-because-rdp-port-is-not-enabled-in-nsg"></a>NSG에서 사용하도록 설정하지 않은 RDP 포트로 인해 Azure VM에 연결할 수 없음

이 문서에서는 NSG(네트워크 보안 그룹)에서 RDP(원격 데스크톱 프로토콜) 포트를 사용하도록 설정하지 않아 Azure Windows VM(가상 머신)에 연결할 수 없는 문제를 해결하는 방법에 대해 설명합니다.


> [!NOTE] 
> Azure에는 리소스를 만들고 작업하기 위한 두 가지 배포 모델인 [Resource Manager 및 클래식](../../azure-resource-manager/resource-manager-deployment-model.md)이 있습니다. 새 배포에 대해 클래식 배포 모델 대신 Resource Manager 배포 모델을 사용하는 것이 좋습니다. 

## <a name="symptom"></a>증상

RDP 포트가 네트워크 보안 그룹에서 열려 있지 않으므로 Azure에서 VM에 대한 RDP 연결을 만들 수 없습니다.

## <a name="solution"></a>해결 방법 

새 VM을 만들면 기본적으로 인터넷의 모든 트래픽이 차단됩니다. 

NSG에서 RDP 포트를 사용하도록 설정하려면 다음 단계를 수행합니다.
1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. **Virtual Machines**에서 문제가 있는 VM을 선택합니다. 
3. **설정**에서 **네트워킹**을 선택합니다. 
4. **인바운드 포트 규칙**에서 RDP 포트가 올바르게 설정되어 있는지 확인합니다. 구성의 예는 다음과 같습니다. 

    **우선 순위**: 300 </br>
    **포트**: 3389 </br>
    **Name**: Port_3389 </br>
    **포트**: 3389 </br>
    **프로토콜**: TCP </br>
    **원본**: 모두 </br>
    **대상**: 모두 </br>
    **작업**: 허용 </br>

원본 IP 주소를 지정할 때 이 설정을 사용하면 특정 IP 주소 또는 IP 주소 범위의 트래픽만 VM에 연결할 수 있습니다. RDP 세션을 시작하는 데 사용하는 컴퓨터가 범위 내에 있는지 확인합니다.

NSG에 대한 자세한 내용은 [네트워크 보안 그룹](../../virtual-network/security-overview.md)을 참조하세요.

> [!NOTE]
> 3389 RDP 포트는 인터넷에 공개되어 있습니다. 따라서 이 포트는 테스트용으로만 사용하는 것이 좋습니다. 프로덕션 환경의 경우 VPN 또는 프라이빗 연결을 사용하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계

NSG에서 RDP 포트를 이미 사용하도록 설정한 경우 [Azure VM의 RDP 일반 오류 문제 해결](./troubleshoot-rdp-general-error.md)을 참조하세요.



