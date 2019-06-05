---
title: Windows Server 또는 Windows 컴퓨터로 Azure의 데이터 복원
description: Azure에 저장된 데이터를 Windows Server 또는 Windows 컴퓨터로 복원하는 방법에 대해 알아봅니다.
services: backup
author: saurabhsensharma
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 9/7/2018
ms.author: saurse
ms.openlocfilehash: d58b51f06c21c787e4aa720c803ab6533544d55c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60238482"
---
# <a name="restore-files-to-windows-by-using-the-azure-resource-manager-deployment-model"></a>Azure Resource Manager 배포 모델을 사용하여 Windows에 파일 복원

이 문서에서는 백업 자격 증명 모음에서 데이터를 복원하는 방법을 설명합니다. 데이터를 복원하려면 MARS(Microsoft Azure Recovery Services) 에이전트에서 데이터 복구 마법사를 사용합니다. 다음을 수행할 수 있습니다.

* 백업을 수행한 동일한 컴퓨터에 데이터를 복원합니다.
* 다른 컴퓨터에 데이터를 복원합니다.

인스턴트 복원 기능을 사용하여 쓰기 가능한 복구 지점 스냅샷을 복구 볼륨으로 탑재합니다. 그런 다음, 복구 볼륨을 탐색하고 파일을 로컬 컴퓨터에 복사하여 파일을 선택적으로 복원할 수 있습니다.

> [!NOTE]
> 즉시 복원을 사용하여 데이터를 복원하려면 [2017년 1월 Azure Backup 업데이트](https://support.microsoft.com/en-us/help/3216528?preview)가 필요합니다. 또한 백업 데이터는 지원 문서에 나열된 로캘의 자격 증명 모음에서 보호되어야 합니다. 즉시 복원을 지원하는 최신 로캘 목록은 [2017년 1월 Azure Backup 업데이트](https://support.microsoft.com/en-us/help/3216528?preview)를 참조하세요.
>

Azure Portal에서는 Recovery Services 자격 증명 모음을 통해 즉시 복원을 사용합니다. 백업 자격 증명 모음에 데이터를 저장한 경우 Recovery Services 자격 증명 모음으로 변환되었을 것입니다. 즉시 복원을 사용하려면 MARS 업데이트를 다운로드하고 즉시 복원을 언급하는 절차를 따르세요.

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="use-instant-restore-to-recover-data-to-the-same-machine"></a>즉시 복원을 사용하여 데이터를 동일한 컴퓨터로 복구합니다.

파일을 실수로 삭제했는데 (백업이 수행된) 동일한 머신에서 복원하려는 경우 다음 단계를 사용하면 데이터를 복구할 수 있습니다.

1. **Microsoft Azure Backup** 스냅인을 엽니다. 스냅인이 설치된 위치를 모르는 경우 컴퓨터 또는 서버에서 **Microsoft Azure Backup**을 검색합니다.

    데스크톱 앱이 검색 결과에 나타나야 합니다.

2. 마법사를 시작하려면 **데이터 복구**를 선택합니다.

    ![데이터 복구를 강조 표시한 Azure Backup 스크린샷](./media/backup-azure-restore-windows-server/recover.png)

3. **시작** 페이지에서 동일한 서버 또는 컴퓨터로 데이터를 복원하려면 **이 서버(`<server name>`)**  > **다음**을 선택합니다.

    ![데이터 복구 마법사 시작 페이지 스크린샷](./media/backup-azure-restore-windows-server/samemachine_gettingstarted_instantrestore.png)

4. 에 **복구 모드 선택** 페이지에서 **개별 파일 및 폴더** > **다음**합니다.

    ![데이터 복구 마법사 복구 모드 선택 페이지 스크린샷](./media/backup-azure-restore-windows-server/samemachine_selectrecoverymode_instantrestore.png)
   > [!IMPORTANT]
   > 개별 파일 및 폴더를 복원하는 옵션에는 .NET Framework 4.5.2 이상이 필요합니다. **개별 파일 및 폴더** 옵션이 표시되지 않는 경우 .NET Framework를 4.5.2 이상 버전으로 업그레이드하고 다시 시도해야 합니다.

   > [!TIP]
   > **개별 파일 및 폴더** 옵션을 사용하면 복구 지점 데이터에 빠르게 액세스할 수 있습니다. 크기가 80GB 이하인 개별 파일을 복구하는 데 적합하며 복구 중에 최대 6MBps의 전송 또는 복사 속도를 제공합니다. **볼륨** 옵션은 지정된 볼륨의 모든 백업 데이터를 복구합니다. 이 옵션은 빠른 전송 속도(최대 60MBps)를 제공하므로 대용량 데이터 또는 전체 볼륨을 복구하는 데 적합합니다.

5. **볼륨 및 날짜 선택** 페이지에서 복원할 파일 및 폴더가 들어있는 볼륨을 선택합니다.

    달력에서 복구 지점을 선택합니다. **굵게** 표시된 날짜는 하나 이상의 복구 지점을 사용 가능함을 나타냅니다. 단일 날짜에서 여러 복구 지점을 사용할 수 있는 경우 **시간** 드롭다운 메뉴에서 특정 복구 지점을 선택합니다.

    ![데이터 복구 마법사 볼륨 및 날짜 선택 페이지 스크린샷](./media/backup-azure-restore-windows-server/samemachine_selectvolumedate_instantrestore.png)

6. 복원할 복구 지점을 선택한 후에 **탑재**를 선택합니다.

    Azure Backup은 로컬 복구 지점을 마운트하고 이를 복구 볼륨으로 사용합니다.

7. **파일 찾아보기 및 복구** 페이지에서 **찾아보기**를 선택하여 Windows 탐색기를 열고 원하는 파일 및 폴더를 찾습니다.

    ![데이터 복구 마법사 파일 찾아보기 및 복구 페이지 스크린샷](./media/backup-azure-restore-windows-server/samemachine_browserecover_instantrestore.png)


8. Windows 탐색기에서 복원할 파일 및 폴더를 복사하여 서버 또는 컴퓨터의 로컬 위치에 붙여넣습니다. 복구 볼륨에서 직접 파일을 열거나 스트리밍하여 올바른 버전을 복구하는지 확인할 수 있습니다.

    ![복사를 강조 표시한 Windows 탐색기 스크린샷](./media/backup-azure-restore-windows-server/samemachine_copy_instantrestore.png)


9. 작업이 완료되면 **파일 찾아보기 및 복구** 페이지에서 **분리**를 선택합니다. 그런 다음, **예**를 선택하여 볼륨을 분리하려고 함을 확인합니다.

    ![데이터 복구 마법사 파일 찾아보기 및 복구 페이지 스크린샷](./media/backup-azure-restore-windows-server/samemachine_unmount_instantrestore.png)

    > [!Important]
    > **분리**를 선택하지 않으면 복구 볼륨이 탑재된 후에 6시간 동안 탑재된 상태로 유지됩니다. 그러나 탑재 시간은 진행 중인 파일 복사의 경우 최대 24시간까지 확장됩니다. 볼륨이 마운트되는 동안 백업 작업은 실행되지 않습니다. 볼륨이 탑재된 시간 동안 실행되도록 예약된 모든 백업 작업은 복구 볼륨이 분리된 후에 실행됩니다.
    >


## <a name="use-instant-restore-to-restore-data-to-an-alternate-machine"></a>즉시 복원을 사용하여 데이터를 대체 컴퓨터에 복원
전체 서버가 손실된 경우에도 Azure Backup에서 데이터를 다른 컴퓨터에 복구할 수 있습니다. 다음 단계는 워크플로를 보여줍니다.


이러한 단계에는 다음과 같은 용어가 포함됩니다.

* *원본 머신* – 처음에 백업이 수행되었고 현재는 사용할 수 없는 원래 머신입니다.
* *대상 컴퓨터* – 데이터가 복구되는 컴퓨터입니다.
* *샘플 자격 증명 모음* – 원본 머신 및 대상 머신이 등록된 Recovery Services 자격 증명 모음입니다. <br/>

> [!NOTE]
> 이전 버전의 운영 체제를 실행하는 대상 머신으로 백업을 복원할 수 없습니다. 예를 들어, Windows 7 컴퓨터에서 가져온 백업은 Windows 8 이상의 컴퓨터에서 복원될 수 있습니다. Windows 8 컴퓨터에서 가져온 백업은 Windows 7 컴퓨터로 복원될 수 없습니다.
>
>

1. 대상 머신에서 **Microsoft Azure Backup** 스냅인을 엽니다.

2. 대상 머신 및 원본 머신이 동일한 Recovery Services 자격 증명 모음에 등록되었는지 확인합니다.

3. **데이터 복구**를 선택하여 **데이터 복구 마법사**를 엽니다.

    ![데이터 복구를 강조 표시한 Azure Backup 스크린샷](./media/backup-azure-restore-windows-server/recover.png)

4. **시작** 페이지에서 **다른 서버**를 선택합니다.

    ![데이터 복구 마법사 시작 페이지 스크린샷](./media/backup-azure-restore-windows-server/alternatemachine_gettingstarted_instantrestore.png)

5. 샘플 자격 증명 모음에 해당하는 자격 증명 모음 파일을 제공하고 **다음**을 선택합니다.

    자격 증명 모음 파일이 유효하지 않거나 만료된 경우 Azure Portal의 샘플 자격 증명 모음에서 새 자격 증명 모음 파일을 다운로드합니다. 유효한 자격 증명 모음을 입력하면 해당하는 백업 자격 증명 모음의 이름이 나타납니다.


6. **백업 서버 선택** 페이지의 표시된 머신 목록에서 원본 머신을 선택하고 암호를 입력합니다. 그런 후 **다음**을 선택합니다.

    ![데이터 복구 마법사 백업 서버 선택 페이지 스크린샷](./media/backup-azure-restore-windows-server/alternatemachine_selectmachine_instantrestore.png)

7. **복구 모드 선택** 창에서 **개별 파일 및 폴더** > **다음**을 선택합니다.

    ![데이터 복구 마법사 복구 모드 선택 페이지 스크린샷](./media/backup-azure-restore-windows-server/alternatemachine_selectrecoverymode_instantrestore.png)

8. **볼륨 및 날짜 선택** 페이지에서 복원할 파일 및 폴더가 들어있는 볼륨을 선택합니다.

    달력에서 복구 지점을 선택합니다. **굵게** 표시된 날짜는 하나 이상의 복구 지점을 사용 가능함을 나타냅니다. 단일 날짜에서 여러 복구 지점을 사용할 수 있는 경우 **시간** 드롭다운 메뉴에서 특정 복구 지점을 선택합니다.

    ![데이터 복구 마법사 볼륨 및 날짜 선택 페이지 스크린샷](./media/backup-azure-restore-windows-server/alternatemachine_selectvolumedate_instantrestore.png)

9. 복구 지점을 대상 머신의 복구 볼륨으로 로컬로 탑재하려면 **탑재**를 선택합니다.

10. **파일 찾아보기 및 복구** 페이지에서 **찾아보기**를 선택하여 Windows 탐색기를 열고 원하는 파일 및 폴더를 찾습니다.

    ![데이터 복구 마법사 파일 찾아보기 및 복구 페이지 스크린샷](./media/backup-azure-restore-windows-server/alternatemachine_browserecover_instantrestore.png)

11. Windows 탐색기에서 복구 볼륨의 파일 및 폴더를 복사하여 대상 머신 위치에 붙여넣습니다. 복구 볼륨에서 직접 파일을 열거나 스트리밍하여 올바른 버전이 복구되었는지 확인할 수 있습니다.

    ![복사를 강조 표시한 Windows 탐색기 스크린샷](./media/backup-azure-restore-windows-server/alternatemachine_copy_instantrestore.png)

12. 작업이 완료되면 **파일 찾아보기 및 복구** 페이지에서 **분리**를 선택합니다. 그런 다음, **예**를 선택하여 볼륨을 분리하려고 함을 확인합니다.

    ![데이터 복구 마법사 파일 찾아보기 및 복구 페이지 스크린샷](./media/backup-azure-restore-windows-server/alternatemachine_unmount_instantrestore.png)

    > [!Important]
    > **분리**를 선택하지 않으면 복구 볼륨이 탑재된 후에 6시간 동안 탑재된 상태로 유지됩니다. 그러나 탑재 시간은 진행 중인 파일 복사의 경우 최대 24시간까지 확장됩니다. 볼륨이 마운트되는 동안 백업 작업은 실행되지 않습니다. 볼륨이 탑재된 시간 동안 실행되도록 예약된 모든 백업 작업은 복구 볼륨이 분리된 후에 실행됩니다.
    >

## <a name="next-steps"></a>다음 단계
파일과 폴더를 복구했으므로 [백업을 관리](backup-azure-manage-windows-server.md)할 수 있습니다.
