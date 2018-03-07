---
title: "Azure Site Recovery를 통해 Azure VM을 온-프레미스 VMware로 장애 복구 시 발생하는 오류 문제 해결 | Microsoft Docs"
description: "이 문서에서는 Azure Site Recovery를 사용하여 Azure에서 VMware로 장애 복구하는 동안 발생하는 일반적인 장애 복구 및 다시 보호 오류 문제를 해결하는 방법을 설명합니다."
services: site-recovery
documentationcenter: 
author: rajani-janaki-ram
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.date: 02/27/2017
ms.author: rajanaki
ms.openlocfilehash: 9b1156884a78eb7d68dc9680765b3c1436c0606a
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/28/2018
---
# <a name="troubleshoot-failback-from-azure-to-vmware"></a>Azure에서 VMware로 장애 복구 문제 해결

이 문서에서는 [Azure Site Recovery](site-recovery-overview.md)를 사용하여 Azure로 장애 조치한 후 Azure VM을 온-프레미스 VMware 인프라로 장애 복구할 때 발생할 수 있는 문제를 해결하는 방법에 대해 설명합니다.

장애 복구(failback)는 기본적으로 두 가지 기본 단계로 이루어집니다. 장애 조치 후에 Azure VM을 온-프레미스로 다시 보호해야 복제가 시작됩니다. 두 번째 단계는 Azure에서 장애 조치를 실행하여 온-프레미스 사이트로 장애 복구하는 것입니다. 

## <a name="troubleshoot-reprotection-errors"></a>다시 보호 오류 문제 해결

이 섹션에서는 일반적인 다시 보호 오류와 이를 수정하는 방법에 대해 자세히 설명합니다.

### <a name="error-code-95226"></a>오류 코드 95226

**Azure 가상 컴퓨터가 온-프레미스 구성 서버에 연결하지 못했기 때문에 다시 보호에 실패했습니다.**

이 오류가 발생하는 경우는 다음과 같습니다.

1. Azure VM에서 온-프레미스 구성 서버에 연결할 수 없습니다. VM을 검색하여 구성 서버에 등록할 수 없습니다. 
2. InMage Scout Application 서비스가 장애 조치 후에 Azure VM에서 실행되지 않습니다. 이 서비스는 온-프레미스 구성 서버와 통신하는 데 필요합니다.

이 문제를 해결하려면:

1. Azure VM 네트워크에서 Azure VM이 온-프레미스 구성 서버와 통신할 수 있는지 확인합니다. 이렇게 하려면 온-프레미스 데이터 센터에 사이트 간 VPN을 설정하거나 Azure VM의 가상 네트워크에서 개인 피어링을 사용하여 ExpressRoute 연결을 구성합니다. 
2. VM에서 온-프레미스 구성 서버와 통신할 수 있는 경우 VM에 로그온하고 'InMage Scout Application 서비스'를 확인합니다. 이 서비스가 실행되지 않는 경우 서비스를 수동으로 시작하고 서비스 시작 유형이 '자동'으로 설정되어 있는지 확인합니다.

### <a name="error-code-78052"></a>오류 코드 78052

***가상 머신에 대한 보호를 완료할 수 없습니다.**

이 오류는 장애 조치하는 마스터 대상 서버에 동일한 이름의 VM이 이미 있는 경우에 발생할 수 있습니다.

이 문제를 해결하려면 다음을 수행합니다.
1. 다시 보호에서 이름이 충돌하지 않는 다른 호스트에 컴퓨터를 만들 수 있도록 다른 호스트에 있는 다른 마스터 대상 서버를 선택합니다. 
2. 마스터 대상에 대한 vMotion을 이름 충돌이 발생하지 않는 다른 호스트에 수행할 수도 있습니다. 기존 VM이 이탈된 컴퓨터인 경우 새 VM을 동일한 ESXi 호스트에 만들 수 있도록 이름을 변경합니다.

### <a name="error-code-78093"></a>오류 코드 78093

**응답이 없는 상태에서 VM이 실행되지 않거나 액세스할 수 없습니다.**

장애 조치한 VM을 다시 보호하려면 Azure VM이 실행되어야 합니다. 이는 모바일 서비스를 온-프레미스 구성 서버에 등록하고 프로세스 서버와 통신하여 복제를 시작할 수 있도록 하기 위한 것입니다. 컴퓨터가 잘못된 네트워크에 있거나 실행되지 않는 경우(중지 상태 또는 종료) 구성 서버에서 VM의 모바일 서비스에 연결하여 다시 보호를 시작할 수 없습니다. 

1. 온-프레미스와의 통신을 다시 시작할 수 있도록 VM을 다시 시작합니다.
2. Azure 가상 머신을 시작한 후에 작업 다시 보호 다시 시작

### <a name="error-code-8061"></a>오류 코드 8061

**데이터 저장소가 ESXi 호스트에서 액세스할 수 없습니다.**

[마스터 대상 필수 구성 요소](site-recovery-how-to-reprotect.md#common-things-to-check-after-completing-installation-of-the-master-target-server) 및 [지원되는 데이터 저장소](site-recovery-how-to-reprotect.md#what-datastore-types-are-supported-on-the-on-premises-esxi-host-during-failback)에서 장애 복구를 확인합니다.


## <a name="troubleshoot-failback-errors"></a>장애 복구 오류 문제 해결

이 섹션에서는 장애 복구 시 발생할 수 있는 일반적인 오류에 대해 설명합니다.

### <a name="error-code-8038"></a>오류 코드 8038

**오류로 인해 온-프레미스 가상 머신을 불러오지 못했습니다.**

이 오류는 메모리가 충분히 프로비전되지 않은 호스트에서 온-프레미스 VM을 가동하는 경우에 발생합니다. 이 문제를 해결하려면:

1. ESXi 호스트에 더 많은 메모리를 프로비전합니다.
2. 또한 VM을 부팅하는 데 충분한 메모리가 있는 다른 ESXi 호스트로 VM을 vMotion합니다.
