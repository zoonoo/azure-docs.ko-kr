---
title: "Azure에서 Windows Server 또는 Windows 컴퓨터로 데이터 복원 | Microsoft Docs"
description: "Azure에 저장된 데이터를 Windows Server 또는 Windows 컴퓨터로 복원하는 방법에 대해 알아봅니다."
services: backup
documentationcenter: 
author: saurabhsensharma
manager: shivamg
editor: 
ms.assetid: 742f4b9e-c0ab-4eeb-8e22-ee29b83c22c4
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 1/30/2017
ms.author: trinadhk;jimpark;markgal;
translationtype: Human Translation
ms.sourcegitcommit: bb07ff13e3e4023e334c706d5f9bd95196ee6051
ms.openlocfilehash: 47c5789fc3379b702d8f6ceafd630bece669b11e


---
# <a name="restore-files-to-a-windows-server-or-windows-client-machine-using-resource-manager-deployment-model"></a>Resource Manager 배포 모델을 사용하여 Windows 서버 또는 Windows 클라이언트 컴퓨터로 파일 복원
> [!div class="op_single_selector"]
> * [Azure 포털](backup-azure-restore-windows-server.md)
> * [클래식 포털](backup-azure-restore-windows-server-classic.md)
>
>

이 문서는 두가지 유형의 복원 작업을 수행하는 데 필요한 단계를 다룹니다.

* 백업을 수행한 동일한 컴퓨터에 데이터를 복원합니다.
* 데이터를 다른 컴퓨터에 복원합니다.

두 경우 모두 데이터는 Azure 복구 서비스 자격 증명 모음에서 검색됩니다.

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="use-instant-restore-to-restore-data-to-the-same-machine"></a>[즉시 복원]을 사용하여 데이터를 동일한 컴퓨터로 복원합니다.
파일을 실수로 삭제했는데 (백업이 수행된) 동일한 컴퓨터에서 복원하려는 경우 다음 단계를 사용하면 데이터를 복구할 수 있습니다.

1. **Microsoft Azure 백업** 스냅인을 엽니다. 스냅인이 설치된 위치를 모르는 경우 컴퓨터 또는 서버에서 **Microsoft Azure Backup**을 검색합니다.

    데스크톱 앱이 검색 결과에 나타나야 합니다.

2. 마법사를 시작하려면 **데이터 복구**를 클릭합니다.

    ![데이터 복구](./media/backup-azure-restore-windows-server/recover.png)

3. **시작** 창에서 동일한 서버 또는 컴퓨터로 데이터를 복원하려면 **이 서버(`<server name>`)**를 선택하고 **다음**을 클릭합니다.

    ![같은 컴퓨터에 데이터를 복원하려면 이 서버 옵션을 선택합니다.](./media/backup-azure-restore-windows-server/samemachine_gettingstarted_instantrestore.png)

4. **복구 모드 선택** 창에서 복원 여부를 선택합니다.
    - **개별 파일 및 폴더** - 특정 파일 및 폴더를 복원하려면 이 옵션을 사용합니다.
    - **볼륨** - 선택한 볼륨의 모든 파일 및 폴더를 복원하려면 이 옵션을 사용합니다.

    그런 후 **다음**을 클릭합니다.

    ![파일 찾아보기](./media/backup-azure-restore-windows-server/samemachine_selectrecoverymode_instantrestore.png)

5. **볼륨 및 날짜 선택** 창에서 복원할 파일 및/또는 폴더가 들어있는 볼륨을 선택합니다.

    달력에서 복구 지점을 선택합니다. 어떤 복구 시점에서라도 복원할 수 있습니다. **굵게** 표시된 날짜는 하나 이상의 복구 지점을 사용 가능함을 나타냅니다. 날짜를 선택하고 여러 복구 지점을 사용할 수 있는 경우 **시간** 드롭다운 메뉴에서 특정 복구 지점을 선택합니다.

    ![볼륨 및 날짜](./media/backup-azure-restore-windows-server/samemachine_selectvolumedate_instantrestore.png)

6. 복원할 복구 지점을 선택했으면 **탑재**를 클릭합니다.

    Azure Backup은 로컬 복구 지점을 마운트하고 이를 복구 볼륨으로 사용합니다.

7. **파일 찾아보기 및 복구** 창에서 **찾아보기**를 클릭하여 Windows 탐색기를 열고 원하는 파일 및 폴더를 찾습니다.

    ![복구 옵션](./media/backup-azure-restore-windows-server/samemachine_browserecover_instantrestore.png)

   * 원래 위치로 복원(파일/폴더가 덮어써짐) 또는 동일한 컴퓨터의 다른 위치로 복원하는 옵션이 있습니다.
   * 복원하려는 파일/폴더가 대상 위치에 있는 경우 복사본 만들기(동일한 파일의 두 버전) 또는 대상 위치의 파일 덮어쓰기 또는 대상에 있는 파일의 복구 건너뛸 수 있습니다.
   * 복구할 파일의 ACL을 복원하는 기본 옵션을 그대로 두는 것이 가장 좋습니다.
8. Windows 탐색기에서 복원할 파일 및/또는 폴더를 복사하여 서버 또는 컴퓨터의 로컬 위치에 붙여 넣습니다. 복구 볼륨에서 직접 파일을 열거나 스트리밍하여 올바른 버전이 복구되었는지 확인할 수 있습니다.

    ![탑재된 볼륨의 파일 및 폴더를 복사하여 로컬 위치에 붙여 넣기](./media/backup-azure-restore-windows-server/samemachine_copy_instantrestore.png)

9. 파일 및/또는 폴더 복원이 완료되면 **찾아보기 및 복구 파일** 창에서 **마운트 해제**를 클릭합니다. 그런 다음 **예**를 클릭하여 볼륨 마운트 해제를 확인합니다.

    ![볼륨을 마운트 해제하고 확인](./media/backup-azure-restore-windows-server/samemachine_unmount_instantrestore.png)

    > [!Important]
    > 마운트 해제를 클릭하지 않으면 복구 볼륨이 마운트된 후&6;시간 동안 마운트된 상태로 유지됩니다. 볼륨이 마운트되는 동안 백업 작업은 실행되지 않습니다. 볼륨이 마운트된 시간 동안 실행되도록 스케줄된 모든 백업 작업은 복구 볼륨이 마운트 해제된 후에 실행됩니다.
    >


## <a name="use-instant-restore-to-restore-data-to-an-alternate-machine"></a>즉시 복원을 사용하여 데이터를 대체 컴퓨터에 복원
전체 서버가 손실된 경우에도 Azure 백업에서 데이터를 다른 컴퓨터에 복구할 수 있습니다. 다음 단계는 워크플로를 보여줍니다.

> [!NOTE]
> 즉시 복원을 사용하여 데이터를 대체 컴퓨터로 복원하려면 [2017년 1월 Azure Backup 업데이트](https://support.microsoft.com/en-us/help/3216528?preview)가 필요합니다. 또한 백업 데이터는 지원 문서에 나열된 로캘의 자격 증명 모음에서 보호되어야 합니다. 즉시 복원은 현재 일부 로캘에서만 사용할 수 있습니다.
>

다음 단계에서 사용되는 용어는 다음과 같습니다.

* *원본 컴퓨터* – 처음에 백업이 수행되었고 현재는 사용할 수 없는 컴퓨터입니다.
* *대상 컴퓨터* – 데이터가 복구되는 컴퓨터입니다.
* *샘플 자격 증명 모음* – *원본 컴퓨터* 및 *대상 컴퓨터*가 등록된 복구 서비스 자격 증명 모음입니다. <br/>

> [!NOTE]
> 이전 버전의 운영 체제를 실행하는 대상 시스템으로 백업을 복원할 수 없습니다. 예를 들어, Windows 7 컴퓨터에서 가져온 백업은 Windows 8 이상의 컴퓨터에서 복원할 수 있습니다. Windows 8 컴퓨터에서 가져온 백업은 Windows 7 컴퓨터로 복원할 수 없습니다.
>
>

1. **대상 컴퓨터** 에서 *Microsoft Azure 백업*스냅인을 엽니다.

2. *대상 컴퓨터* 및 *원본 컴퓨터*가 동일한 Recovery Services 자격 증명 모음에 등록됐는지 확인합니다.

3. **데이터 복구**를 클릭하여 **데이터 복구 마법사**를 엽니다.

    ![데이터 복구](./media/backup-azure-restore-windows-server/recover.png)

4. **시작** 창에서 **다른 서버**를 선택합니다.

    ![다른 서버](./media/backup-azure-restore-windows-server/alternatemachine_gettingstarted_instantrestore.png)

5. *샘플 자격 증명 모음*에 해당하는 자격 증명 모음 파일을 제공하고 **다음**을 클릭합니다.

    자격 증명 모음 파일이 유효하지 않거나 만료된 경우 Azure Portal의 *샘플 자격 증명 모음* 에서 새 자격 증명 모음 파일을 다운로드합니다. 유효한 자격 증명 모음을 제공하면 해당 백업 자격 증명 모음의 이름이 나타납니다.

6. **백업 서버 선택** 창에서 표시된 컴퓨터 목록에서 *원본 컴퓨터*를 선택하고 암호를 제공합니다. 그런 후 **다음**을 클릭합니다.

    ![컴퓨터 목록](./media/backup-azure-restore-windows-server/alternatemachine_selectmachine_instantrestore.png)

7. **복구 모드 선택** 창에서 **개별 파일 및 폴더**를 선택하고 **다음**을 클릭합니다.

    ![Search](./media/backup-azure-restore-windows-server/alternatemachine_selectrecoverymode_instantrestore.png)

8. **볼륨 및 날짜 선택** 창에서 복원할 파일 및/또는 폴더가 들어있는 볼륨을 선택합니다.

    달력에서 복구 지점을 선택합니다. 어떤 복구 시점에서라도 복원할 수 있습니다. **굵게** 표시된 날짜는 하나 이상의 복구 지점을 사용 가능함을 나타냅니다. 날짜를 선택하고 여러 복구 지점을 사용할 수 있는 경우 **시간** 드롭다운 메뉴에서 특정 복구 지점을 선택합니다.

    ![검색 항목](./media/backup-azure-restore-windows-server/alternatemachine_selectvolumedate_instantrestore.png)

9. 복구 지점을 *대상 컴퓨터*에 복구 볼륨으로 로컬로 마운트하려면 **탑재**를 클릭합니다.

10. **파일 찾아보기 및 복구** 창에서 **찾아보기**를 클릭하여 Windows 탐색기를 열고 원하는 파일 및 폴더를 찾습니다.

    ![암호화](./media/backup-azure-restore-windows-server/alternatemachine_browserecover_instantrestore.png)

11. Windows 탐색기에서 복구 볼륨의 파일 및/또는 폴더를 복사하여 *대상 컴퓨터* 위치에 붙여 넣습니다. 복구 볼륨에서 직접 파일을 열거나 스트리밍하여 올바른 버전이 복구되었는지 확인할 수 있습니다.

    ![암호화](./media/backup-azure-restore-windows-server/alternatemachine_copy_instantrestore.png)

12. 파일 및/또는 폴더 복원이 완료되면 **찾아보기 및 복구 파일** 창에서 **마운트 해제**를 클릭합니다. 그런 다음 **예**를 클릭하여 볼륨 마운트 해제를 확인합니다.

    ![암호화](./media/backup-azure-restore-windows-server/alternatemachine_unmount_instantrestore.png)

    > [!Important]
    > 마운트 해제를 클릭하지 않으면 복구 볼륨이 마운트된 후&6;시간 동안 마운트된 상태로 유지됩니다. 볼륨이 마운트되는 동안 백업 작업은 실행되지 않습니다. 볼륨이 마운트된 시간 동안 실행되도록 스케줄된 모든 백업 작업은 복구 볼륨이 마운트 해제된 후에 실행됩니다.
    >

## <a name="next-steps"></a>다음 단계
* 파일과 폴더를 복구했으므로 [백업을 관리](backup-azure-manage-windows-server.md)할 수 있습니다.



<!--HONumber=Jan17_HO5-->


