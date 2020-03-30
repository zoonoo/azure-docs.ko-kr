---
title: Azure 직렬 콘솔 오류 | 마이크로 소프트 문서
description: Azure 직렬 콘솔 내의 일반적인 오류
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
ms.openlocfilehash: 61ae0ef92fe522a2a038a6076a5e0c0a10ee47b6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80060696"
---
# <a name="common-errors-within-the-azure-serial-console"></a>Azure 직렬 콘솔 내의 일반적인 오류
Azure 직렬 콘솔 내에 알려진 오류 집합이 있습니다. 이 목록은 이러한 오류 및 완화 단계의 목록입니다.

## <a name="common-errors"></a>일반 오류

Error                             |   완화 방법
:---------------------------------|:--------------------------------------------|
"Azure 직렬 콘솔에서 부팅 진단을 사용하도록 설정해야 합니다. 가상 시스템에 대한 부팅 진단을 구성하려면 여기를 클릭하십시오." | VM 또는 가상 시스템 규모 집합에 부팅 진단이 활성화되어 있는지 [확인합니다.](boot-diagnostics.md) 가상 시스템 규모 집합 인스턴스에서 직렬 콘솔을 사용하는 경우 인스턴스에 최신 모델이 있는지 확인합니다.
"Azure 직렬 콘솔을 실행하려면 가상 시스템이 필요합니다. 위의 시작 버튼을 사용하여 가상 컴퓨터를 시작합니다."  | VM 또는 가상 시스템 크기 집합 인스턴스는 직렬 콘솔에 액세스하려면 시작 된 상태여야 합니다(VM을 중지하거나 할당 할당 지정해서는 안 됨). VM 또는 가상 컴퓨터 규모 집합 인스턴스가 실행 중인지 확인하고 다시 시도하십시오.
"이 구독에 대해 Azure 직렬 콘솔이 활성화되지 않은 경우 구독 관리자에게 문의하여 사용하도록 설정합니다." | Azure 직렬 콘솔은 구독 수준에서 비활성화할 수 있습니다. 구독 관리자인 경우 Azure [직렬 콘솔을 활성화및 비활성화할](./serial-console-enable-disable.md)수 있습니다. 구독 관리자가 아닌 경우 구독 관리자에게 연락하여 다음 단계를 확인해야 합니다.
이 VM의 부트 진단 스토리지 계정에 액세스할 경우 &quot;사용할 수 없음&quot; 응답이 발생했습니다. | 부트 진단에 계정 방화벽이 없는지 확인하세요. 직렬 콘솔이 작동하려면 액세스 가능한 부트 진단 스토리지 계정이 필요합니다. 직렬 콘솔은 기본적으로 부트 진단 스토리지 계정에서 사용하도록 설정된 스토리지 계정 방화벽과 함께 작동할 수 없습니다.
직렬 콘솔에서 이 VM을 사용하는 데 필요한 사용 권한이 없습니다. Virtual Machine Contributor 역할 이상의 권한이 있는지 확인합니다.| 직렬 콘솔 액세스는 VM 또는 가상 시스템 규모 집합에서 기여자 수준 이상의 액세스 권한을 갖도록 해야 합니다. 자세한 내용은 개요 [페이지를](serial-console-overview.md)참조하십시오.
이 VM의 부팅 진단에 사용되는 저장소 계정 '을 찾을 수 없습니다. 이 VM에 대해 부팅 진단이 활성화되어 있는지, 이 저장소 계정이 삭제되지 않았으며, 이 저장소 계정에 액세스할 수 있는지 확인합니다. | VM 또는 가상 시스템 규모 집합에 대한 부팅 진단 저장소 계정을 삭제하지 않은지 다시 한 번 확인합니다.
VM에 대한 직렬 콘솔 연결에 '잘못된 요청'(400)이라는 오류가 발생했습니다. | 부팅 진단 URI가 올바르지 않으면 이 기능이 발생할 수 있습니다. 예를 들어 "http://"은 "https://"대신 사용되었습니다. 부팅 진단 URI는 이 명령으로 수정할 수 있습니다.`az vm boot-diagnostics enable --name vmName --resource-group rgName --storage https://<storageAccountUri>.blob.core.windows.net/`
이 VM에 대한 부팅 진단 저장소 계정에 쓸 수 있는 필요한 권한이 없습니다. VM 기고자 권한이 적어도 있는지 확인하십시오. | 직렬 콘솔 액세스에는 부팅 진단 저장소 계정에서 기여자 수준 액세스가 필요합니다. 자세한 내용은 개요 [페이지를](serial-console-overview.md)참조하십시오.
부팅 진단 저장소 계정 * &lt;STORAGEACCOUNTNAME에&gt;* 대한 리소스 그룹을 확인할 수 없습니다. 이 VM에 부트 진단이 활성화되어 있고 스토리지 계정에 액세스 권한이 있는지 확인합니다. | 직렬 콘솔 액세스에는 부팅 진단 저장소 계정에서 기여자 수준 액세스가 필요합니다. 자세한 내용은 개요 [페이지를](serial-console-overview.md)참조하십시오.
이 VM에 대한 프로비전은 아직 성공하지 못했습니다. VM이 완전히 배포되었는지 확인하고 직렬 콘솔 연결을 다시 시도하십시오. | VM 또는 가상 시스템 크기 집합은 여전히 프로비전 중일 수 있습니다. 잠시 기다렸다가 다시 시도하십시오.
웹 소켓이 닫혀 있거나 열 수 없습니다. | 에 방화벽 액세스를 추가해야 `*.console.azure.com`할 수 있습니다. 더 상세하지만 더 긴 방법은 정기적으로 변경되는 Microsoft Azure 데이터 센터 IP 범위에 대한 방화벽 액세스를 허용하는 [것입니다.](https://www.microsoft.com/download/details.aspx?id=41653)
직렬 콘솔은 계층적 네임스페이스가 있는 Azure Data Lake Storage Gen2를 사용하는 저장소 계정에서 작동하지 않습니다. | 계층적 네임스페이스의 알려진 문제입니다. 완화하려면 Azure Data Lake 저장소 Gen2를 사용하여 VM의 부팅 진단 저장소 저장소 계정이 만들어지지 않았는지 확인합니다. 이 옵션은 저장소 계정 생성 시에만 설정할 수 있습니다. 이 문제를 완화하려면 Azure Data Lake Storage Gen2를 사용하도록 설정하지 않고 별도의 부팅 진단 저장소 계정을 만들어야 할 수 있습니다.
VM에 대한 직렬 콘솔 연결에 '금지됨'(SubscriptionNotEnabled) - 멤버쉽 \<이름이 정의되지 않은 ID 구독 id> 정의되지 않은 상태입니다. | 사용자가 Cloud Shell 저장소 계정을 만든 구독이 비활성화된 경우 이 문제가 발생할 수 있습니다. 완화하려면 Cloud [Shell을](https://docs.microsoft.com/azure/cloud-shell/persisting-shell-storage#unmount-clouddrive-1) 시작하고 현재 구독에서 Cloud Shell에 대한 백업 저장소 계정을 다시 프로비전하는 데 필요한 단계를 수행합니다.

## <a name="next-steps"></a>다음 단계
* [Linux VM용 Azure 직렬 콘솔에](./serial-console-linux.md) 대해 자세히 알아보기
* [Windows VM용 Azure 직렬 콘솔에](./serial-console-windows.md) 대해 자세히 알아보기