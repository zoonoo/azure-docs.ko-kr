---
author: v-amallick
ms.service: Azure Backup
ms.topic: include
ms.date: 04/16/2021
ms.author: v-amallick
ms.openlocfilehash: e7af0819a94661a1008d96b7d0738c30a4a80c18
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107716752"
---
1. 백업용으로 선택된 Azure VM의 경우 Azure Backup은 지정된 백업 일정에 따라 백업 작업을 시작합니다.
1. 첫 번째 백업 중에 VM이 실행되고 있으면 백업 확장이 VM에 설치됩니다.
    - Windows VM의 경우 [VMSnapshot 확장](../articles/virtual-machines/extensions/vmsnapshot-windows.md)이 설치됩니다.
    - Linux VM의 경우 [VMSnapshotLinux 확장](../articles/virtual-machines/extensions/vmsnapshot-linux.md)이 설치됩니다.
1. 실행 중인 Windows VM의 경우 Backup이 Windows VSS(볼륨 섀도 복사본 서비스)와 조정되어 VM의 일관성 있는 앱 스냅샷을 생성합니다.
    - 기본적으로 Backup은 전체 VSS 백업을 수행합니다.
    - Backup이 일관성 있는 앱 스냅샷을 만들 수 없는 경우, VM이 중지된 동안에는 애플리케이션 쓰기가 발생하지 않으므로 기본 스토리지의 일관성 있는 파일 스냅샷을 생성합니다.
1. Linux VM의 경우 Backup이 일관성 있는 파일 백업을 수행합니다. 일관성 있는 앱 스냅샷의 경우 수동으로 사전/사후 스크립트를 사용자 지정해야 합니다.
1. Backup이 스냅샷을 수행한 후에는 데이터를 자격 증명 모음으로 전송합니다.
    - 각 VM 디스크를 병렬로 백업하여 백업을 최적화합니다.
    - 백업되는 각 디스크에서 Azure Backup은 디스크의 블록을 읽고 이전 백업 이후에 변경된 데이터 블록(델타)만 식별 및 전송합니다.
    - 스냅샷 데이터가 자격 증명 모음에 즉시 복사되지 않을 수도 있습니다. 사용량이 많은 시간에는 몇 시간이 걸릴 수 있습니다. 일별 백업 정책에서 VM의 총 백업 시간은 24시간 미만입니다.
1. Azure Backup을 사용하도록 설정한 후 Windows VM에 대한 변경 내용은 다음과 같습니다.
    - Microsoft Visual C++ 2013 재배포 가능 패키지(x64) - 12.0.40660이 VM에 설치됨
    - VSS(볼륨 섀도 복사본 서비스)의 시작 유형이 수동에서 자동으로 변경됨
    - IaaSVmProvider Windows 서비스가 추가됨

1. 데이터 전송이 완료되면 스냅샷이 제거되고 복구 지점이 생성됩니다.

![Azure 가상 머신 백업 아키텍처](../articles/backup/media/backup-azure-vms-introduction/vmbackup-architecture.png)
