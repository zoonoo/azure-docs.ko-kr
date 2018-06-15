---
title: Azure에서 온-프레미스로 장애 복구(failback) 후 Azure로 다시 보호하는 동안 발생하는 오류 문제 해결 | Microsoft Docs
description: 이 문서에서는 Azure에서 온-프레미스로 장애 복구(failback) 및 다시 보호 중에 발생하는 일반적인 오류 문제를 해결하는 방법을 설명합니다.
services: site-recovery
documentationcenter: ''
author: rajani-janaki-ram
manager: gauravd
editor: ''
ms.assetid: ''
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 12/19/2017
ms.author: rajanaki
ms.openlocfilehash: d487c1fcf7fb6444c2b8489df839a6450715ae0a
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/27/2018
ms.locfileid: "29677685"
---
# <a name="troubleshoot-errors-reported-during-the-process-of-failback"></a>장애 복구(failback) 프로세스 중에 보고된 오류 문제 해결
장애 복구(failback)는 기본적으로 두 가지 기본 단계로 이루어집니다. 첫 번째 단계는 Azure에서 온-프레미스로 가상 머신을 다시 보호하는 것이고, 두 번째 단계는 실제로 Azure에서 온-프레미스로 장애 복구(failback)를 수행하는 것입니다.

## <a name="troubleshoot-errors-when-reprotecting-a-virtual-machine-back-to-on-premises-after-failover"></a>장애 조치(failover) 후 가상 머신을 온-프레미스로 다시 보호할 때 발생하는 오류 문제 해결
가상 머신을 Azure로 다시 보호하는 동안 다음 오류 중 하나가 나타날 수 있습니다. 문제를 해결하려면 각 오류 조건에 대해 설명된 단계를 따르세요.


### <a name="error-code-95226"></a>오류 코드 95226

*Azure 가상 컴퓨터가 온-프레미스 구성 서버에 연결하지 못했기 때문에 다시 보호에 실패했습니다.*

다음과 같은 경우에 발생합니다. 
1. Azure 가상 머신은 온-프레미스 구성 서버에 연결할 수 없습니다. 따라서 검색되고 구성 서버에 등록될 수 없습니다. 
2. 온-프레미스 구성 서버에 통신하기 위해 실행해야 하는 Azure 가상 머신에 InMage Scout 응용 프로그램 서비스는 사후 장애 조치를 실행하지 않을 수도 있습니다.

이 문제를 해결하려면
1. 가상 머신이 온-프레미스 구성 서버와 통신할 수 있도록 Azure 가상 머신의 네트워크를 구성했는지 확인해야 합니다. 이 작업을 수행하려면 다시 온-프레미스 데이터 센터에 사이트 간 VPN을 설정하거나 Azure 가상 머신의 가상 네트워크에서 개인 피어링으로 ExpressRoute 연결을 구성합니다. 
2. Azure 가상 머신이 온-프레미스 구성 서버와 통신할 수 있도록 네트워크를 이미 구성한 경우 가상 머신에 로그인하고 'InMage Scout 응용 프로그램 서비스'를 확인합니다. InMage Scout 응용 프로그램 서비스가 실행되지 않는 것이 발견되는 경우 서비스를 수동으로 시작하고 서비스 시작 형식을 [자동]으로 설정했는지 확인합니다.

### <a name="error-code-78052"></a>오류 코드 78052
*가상 머신에 대한 보호를 완료할 수 없습니다.* 라는 오류 메시지로 다시 보호에 실패했습니다.

두 가지 이유로 인해 발생할 수 있습니다.
1. 다시 보호하는 가상 컴퓨터는 Windows Server 2016입니다. 현재 이 운영 체제에서는 장애 복구(failback)가 지원되지 않지만 곧 지원될 예정입니다.
2. 이미 장애 복구 중인 Master 대상 서버에서 동일한 이름을 가진 가상 머신이 있습니다.

이 문제를 해결하려면 다른 호스트에서 다른 마스터 대상 서버를 선택하므로 다시 보호는 이름이 충돌하지 않는 다른 호스트에 컴퓨터를 만들 수 있습니다. 이름 충돌이 발생하지 않는 다른 호스트에 마스터 대상의 vMotion을 수행할 수도 있습니다. 기존 가상 머신이 이탈 컴퓨터인 경우 이름을 바꿔서 동일한 ESXi 호스트에 새 가상 머신을 만들 수 있습니다.

### <a name="error-code-78093"></a>오류 코드 78093

*응답이 없는 상태에서 VM이 실행되지 않거나 액세스할 수 없습니다.*

온-프레미스에 장애 조치된 가상 머신을 다시 보호하려면 Azure 가상 머신을 실행해야 합니다. 따라서 모바일 서비스를 구성 서버 온-프레미스에 등록하고 프로세스 서버와 통신하여 복제를 시작할 수 있습니다. 컴퓨터가 잘못된 네트워크에 있거나 실행되지 않는 경우(응답하지 않는 상태 또는 종료) 구성 서버는 다시 보호를 시작하기 위해 가상 컴퓨터에서 모바일 서비스에 연결할 수 없습니다. 다시 온-프레미스와 통신하기 시작할 수 있도록 가상 머신을 다시 시작할 수 있습니다. Azure 가상 머신을 시작한 후에 작업 다시 보호 다시 시작

### <a name="error-code-8061"></a>오류 코드 8061

*데이터 저장소가 ESXi 호스트에서 액세스할 수 없습니다.*

장애 복구(failback)는 [마스터 대상 필수 정보](site-recovery-how-to-reprotect.md#common-things-to-check-after-completing-installation-of-the-master-target-server) 및 [지원 데이터 저장소](site-recovery-how-to-reprotect.md#what-datastore-types-are-supported-on-the-on-premises-esxi-host-during-failback)를 참조합니다.


## <a name="troubleshoot-errors-when-performing-a-failback-of-an-azure-virtual-machine-back-to-on-premises"></a>Azure 가상 머신을 온-프레미스로 다시 장애 복구(failback)할 때 발생하는 오류 문제 해결
Azure 가상 머신을 온-프레미스로 다시 장애 복구(failback)하는 동안 다음 오류 중 하나가 나타날 수 있습니다. 문제를 해결하려면 각 오류 조건에 대해 설명된 단계를 따르세요.

### <a name="error-code-8038"></a>오류 코드 8038

*오류로 인해 온-프레미스 가상 머신을 불러오지 못했습니다.*

이 오류는 메모리가 충분히 프로비전되지 않은 호스트에서 온-프레미스 가상 머신을 실행하는 경우에 발생합니다.

이 문제를 해결하려면

1. ESXi 호스트에 메모리를 더 많이 프로비전합니다.
1. 가상 머신을 부팅하기에 충분한 메모리가 있는 다른 ESXi 호스트로 VM을 vMotion합니다.