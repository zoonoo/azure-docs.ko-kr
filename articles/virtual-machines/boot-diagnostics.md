---
title: Azure 부팅 진단
description: Azure 부팅 진단 및 관리 되는 부팅 진단 개요
services: virtual-machines
ms.service: virtual-machines
author: mimckitt
ms.author: mimckitt
ms.topic: conceptual
ms.date: 08/04/2020
ms.openlocfilehash: e2ba5d909a3aa43921f52295d2f7216aac76bc32
ms.sourcegitcommit: d8b8768d62672e9c287a04f2578383d0eb857950
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/11/2020
ms.locfileid: "88067089"
---
# <a name="azure-boot-diagnostics"></a>Azure 부팅 진단

부팅 진단은 VM 부팅 실패를 진단할 수 있도록 하는 Azure VM (가상 머신)에 대 한 디버깅 기능입니다. 부팅 진단을 통해 사용자는 직렬 로그 정보와 스크린샷을 수집 하 여 부팅 하는 VM의 상태를 관찰할 수 있습니다.

## <a name="boot-diagnostics-view"></a>부트 진단 보기
가상 컴퓨터 블레이드에 있는 부팅 진단 옵션은 Azure Portal의 *지원 및 문제 해결* 섹션 아래에 있습니다. 부트 진단을 선택 하면 스크린샷 및 일련 로그 정보가 표시 됩니다. 직렬 로그는 커널 메시징을 포함 하 고 스크린샷은 Vm의 현재 상태에 대 한 스냅숏입니다. VM에서 Windows 또는 Linux를 실행 하 고 있는지 여부에 따라 예상 되는 스크린샷은 어떻게 나타나는지 결정 합니다. Windows의 경우 사용자에 게 데스크톱 배경 및 Linux가 표시 됩니다. 사용자에 게 로그인 프롬프트가 표시 됩니다.

:::image type="content" source="./media/boot-diagnostics/boot-diagnostics-linux.png" alt-text="Linux 부팅 진단 스크린샷":::
:::image type="content" source="./media/boot-diagnostics/boot-diagnostics-windows.png" alt-text="Windows 부팅 진단 스크린샷":::


## <a name="limitations"></a>제한 사항
- 부트 진단은 Azure Resource Manager Vm에만 사용할 수 있습니다. 
- 부팅 진단은 premium storage 계정을 지원 하지 않습니다. premium storage 계정이 부팅 진단에 사용 되 면 `StorageAccountTypeNotSupported` VM을 시작할 때 오류가 발생 합니다. 

## <a name="next-steps"></a>다음 단계

[Azure 직렬 콘솔](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-overview) 에 대 한 자세한 내용과 부팅 진단을 사용 하 여 [azure의 가상 컴퓨터 문제를 해결](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics)하는 방법을 알아보세요.
