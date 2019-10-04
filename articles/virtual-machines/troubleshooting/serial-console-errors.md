---
title: Azure 직렬 콘솔 오류 | Microsoft Docs
description: Azure 직렬 콘솔 내 일반적인 오류
services: virtual-machines
documentationcenter: ''
author: asinn826
manager: borisb
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm
ms.workload: infrastructure-services
ms.date: 8/20/2019
ms.author: alsin
ms.openlocfilehash: e4596ae2f92e5dfd99dc7c83857e0c9874358fd4
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/04/2019
ms.locfileid: "71949697"
---
# <a name="common-errors-within-the-azure-serial-console"></a>Azure 직렬 콘솔 내 일반적인 오류
Azure 직렬 콘솔에는 알려진 오류 집합이 있습니다. 이러한 오류 및 해당 오류의 완화 단계 목록입니다.

## <a name="common-errors"></a>일반 오류

오류                            |   완화
:---------------------------------|:--------------------------------------------|
*&lt;VMNAME&gt;* 에 대한 부트 진단 설정을 검색할 수 없습니다. 직렬 콘솔을 사용하려면 부트 진단이 VM에 활성화되어 있는지 확인하세요. ![Boot 진단 오류 @ no__t-1 | VM 또는 가상 머신 확장 집합에 [부트 진단](boot-diagnostics.md) 이 사용 하도록 설정 되어 있는지 확인 합니다. 가상 머신 확장 집합 인스턴스에서 직렬 콘솔을 사용 하는 경우 인스턴스에 최신 모델이 있는지 확인 합니다.
VM이 중지된 할당 취소 상태입니다. VM을 시작하고 직렬 콘솔 연결을 다시 시도합니다. ![할당 취소 오류](./media/virtual-machines-serial-console/virtual-machines-serial-console-deallocating-error.png) | 직렬 콘솔에 액세스 하려면 VM 또는 가상 머신 확장 집합 인스턴스가 시작 됨 상태 여야 합니다. VM 또는 가상 머신 확장 집합 인스턴스를 시작 하 고 다시 시도 하세요.
이 VM의 부트 진단 스토리지 계정에 액세스할 경우 &quot;사용할 수 없음&quot; 응답이 발생했습니다. ![저장소 계정 방화벽 오류](./media/virtual-machines-serial-console/virtual-machines-serial-console-firewall-error.png)| 부트 진단에 계정 방화벽이 없는지 확인하세요. 직렬 콘솔이 작동하려면 액세스 가능한 부트 진단 스토리지 계정이 필요합니다. 직렬 콘솔은 기본적으로 부트 진단 스토리지 계정에서 사용하도록 설정된 스토리지 계정 방화벽과 함께 작동할 수 없습니다.
직렬 콘솔에서 이 VM을 사용하는 데 필요한 사용 권한이 없습니다. Virtual Machine Contributor 역할 이상의 권한이 있는지 확인합니다.| 직렬 콘솔 액세스에는 VM 또는 가상 머신 확장 집합에 대 한 참가자 수준 액세스 권한이 있어야 합니다. 자세한 내용은 [개요 페이지](serial-console-overview.md)를 참조 하세요.
이 VM에서 부팅 진단에 사용 되는 저장소 계정 ' '을 (를) 찾을 수 없습니다. 이 VM에 대해 부팅 진단이 사용 하도록 설정 되어 있고,이 저장소 계정이 삭제 되지 않았는지,이 저장소 계정에 대 한 액세스 권한이 있는지 확인 하세요. | VM 또는 가상 머신 확장 집합에 대 한 부트 진단 저장소 계정을 삭제 하지 않았는지 다시 한 번 확인 합니다.
이 VM에 대 한 프로 비전이 아직 성공 하지 않았습니다. VM이 완전히 배포 되었는지 확인 하 고 직렬 콘솔 연결을 다시 시도 하세요. | VM 또는 가상 머신 확장 집합을 계속 프로 비전 할 수 있습니다. 잠시 기다린 후 다시 시도 하세요.
이 VM에 대 한 부팅 진단 저장소 계정에 쓰는 데 필요한 권한이 없습니다. ' '에 대 한 VM 참가자 권한이 적어도 하나 이상 있는지 확인 하세요. | 액세스 직렬 콘솔 하려면 부트 진단 저장소 계정에 대 한 참가자 수준 액세스 권한이 필요 합니다. 자세한 내용은 [개요 페이지](serial-console-overview.md)를 참조 하세요.
부트 진단 스토리지 계정인 *&lt;STORAGEACCOUNTNAME&gt;* 에 대한 리소스 그룹을 확인할 수 없습니다. 이 VM에 부트 진단이 활성화되어 있고 스토리지 계정에 액세스 권한이 있는지 확인합니다. | 액세스 직렬 콘솔 하려면 부트 진단 저장소 계정에 대 한 참가자 수준 액세스 권한이 필요 합니다. 자세한 내용은 [개요 페이지](serial-console-overview.md)를 참조 하세요.
웹 소켓이 닫혀 있거나 열 수 없습니다. | @No__t-0에 대 한 방화벽 액세스를 추가 해야 할 수 있습니다. 보다 자세 하지만 더 긴 방법은 [Microsoft Azure 데이터 센터 IP 범위](https://www.microsoft.com/download/details.aspx?id=41653)에 대 한 방화벽 액세스를 허용 하 여 정기적으로 변경 하는 것입니다.
직렬 콘솔는 계층적 네임 스페이스와 Azure Data Lake Storage Gen2를 사용 하는 저장소 계정에서 작동 하지 않습니다. | 이는 계층적 네임 스페이스의 알려진 문제입니다. 이를 완화 하려면 VM의 부트 진단 저장소 계정이 Azure Data Lake Storage Gen2를 사용 하 여 만들어지지 않았는지 확인 합니다. 이 옵션은 저장소 계정을 만드는 경우에만 설정할 수 있습니다. 이 문제를 완화 하기 위해 Azure Data Lake Storage Gen2 사용 하도록 설정 하지 않고 별도의 부트 진단 저장소 계정을 만들어야 할 수 있습니다.


## <a name="next-steps"></a>다음 단계
* [Linux vm 용 Azure 직렬 콘솔](./serial-console-linux.md) 에 대 한 자세한 정보
* [Windows vm 용 Azure 직렬 콘솔](./serial-console-windows.md) 에 대 한 자세한 정보