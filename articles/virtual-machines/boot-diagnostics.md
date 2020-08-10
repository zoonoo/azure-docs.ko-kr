---
title: Azure 부팅 진단
description: Azure 부팅 진단 및 관리 되는 부팅 진단 개요
services: virtual-machines
ms.service: virtual-machines
author: mimckitt
ms.author: mimckitt
ms.topic: conceptual
ms.date: 08/04/2020
ms.openlocfilehash: 9ffd5a6397fa01efcf9aece93333dcb5e5b418cc
ms.sourcegitcommit: 1a0dfa54116aa036af86bd95dcf322307cfb3f83
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/10/2020
ms.locfileid: "88042975"
---
# <a name="azure-boot-diagnostics"></a>Azure 부팅 진단

부팅 진단은 VM 부팅 실패를 진단할 수 있도록 하는 Azure VM (가상 머신)에 대 한 디버깅 기능입니다. 부팅 진단을 통해 사용자는 직렬 로그 정보와 스크린샷을 수집 하 여 부팅 하는 VM의 상태를 관찰할 수 있습니다.

## <a name="boot-diagnostics-storage-account"></a>부트 진단 저장소 계정
기본적으로 부팅 진단은 Azure Portal를 사용 하 여 만든 모든 Vm에 대해 사용 하도록 설정 되며 관리 되는 저장소 계정을 활용 합니다. 관리 되는 저장소 계정을 사용 하면 사용자가 VM 배포 시간을 크게 향상 시킬 수 있습니다. 따라서 고객은 모든 Vm에 대해 관리 되는 저장소 계정으로 부팅 진단을 사용 하는 것이 좋습니다.

> [!NOTE]
> Azure 고객은 2020 년 10 월 1 일까 지 관리 저장소 계정으로 부팅 진단을 사용 하도록 할 때 저장소에 대 한 요금이 청구 되지 않습니다.

대체 부팅 진단 환경은 사용자 지정 저장소 계정을 사용 하는 것입니다. 사용자는 새 저장소 계정을 만들거나 기존 저장소 계정을 사용할 수 있습니다. 사용자 지정 저장소 계정에 대 한 자세한 내용은 [저장소 계정 개요](https://docs.microsoft.com/azure/storage/common/storage-account-overview)를 참조 하세요.

:::image type="content" source="./media/boot-diagnostics/boot-diagnostics-portal.png" alt-text="부팅 진단을 사용 하도록 설정 하는 방법을 보여 주는 스크린샷":::

## <a name="boot-diagnostics-view"></a>부트 진단 보기
가상 컴퓨터 블레이드에 있는 부팅 진단 옵션은 Azure Portal의 *지원 및 문제 해결* 섹션 아래에 있습니다. 부트 진단을 선택 하면 스크린샷 및 일련 로그 정보가 표시 됩니다. 직렬 로그는 커널 메시징을 포함 하 고 스크린샷은 Vm의 현재 상태에 대 한 스냅숏입니다. VM에서 Windows 또는 Linux를 실행 하 고 있는지 여부에 따라 예상 되는 스크린샷은 어떻게 나타나는지 결정 합니다. Windows의 경우 사용자에 게 데스크톱 배경 및 Linux가 표시 됩니다. 사용자에 게 로그인 프롬프트가 표시 됩니다.

:::image type="content" source="./media/boot-diagnostics/boot-diagnostics-linux.png" alt-text="Linux 부팅 진단 스크린샷":::
:::image type="content" source="./media/boot-diagnostics/boot-diagnostics-windows.png" alt-text="Windows 부팅 진단 스크린샷":::


## <a name="limitations"></a>제한 사항
- 부트 진단은 ARM (Azure Resource Manager) Vm에 대해서만 사용할 수 있습니다. 
- 부팅 진단은 premium storage 계정을 지원 하지 않습니다. premium storage 계정이 부팅 진단에 사용 되 면 `StorageAccountTypeNotSupported` VM을 시작할 때 오류가 발생 합니다. 
- Azure 직렬 콘솔은 현재 부트 진단에 대 한 관리 되는 저장소 계정을 지원 하지 않습니다.

## <a name="next-steps"></a>다음 단계

[Azure 직렬 콘솔](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-overview) 에 대 한 자세한 내용과 부팅 진단을 사용 하 여 [azure의 가상 컴퓨터 문제를 해결](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics)하는 방법을 알아보세요.
