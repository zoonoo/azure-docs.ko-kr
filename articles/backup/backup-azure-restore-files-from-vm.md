---
title: "Azure 백업: Azure VM 백업에서 파일 및 폴더 복구 | Microsoft Docs"
description: "Azure 가상 컴퓨터 복구 지점에서 파일 복구"
services: backup
documentationcenter: dev-center-name
author: pvrk
manager: shivamg
keywords: "항목 수준 복구, Azure 백업에서 파일 복구, Azure VM에서 파일 복원"
ms.assetid: f1c067a2-4826-4da4-b97a-c5fd6c189a77
ms.service: backup
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 2/6/2017
ms.author: pullabhk;markgal
translationtype: Human Translation
ms.sourcegitcommit: 5c31fcac727a1206cb416cbe7340392a2816729a
ms.openlocfilehash: 93b689c132ec7e391a9107e4e3eb73a48e3ba09d


---
# <a name="recover-files-from-azure-virtual-machine-backup-preview"></a>Azure 가상 컴퓨터 백업에서 파일 복구(미리 보기)

Azure 백업에서는 Azure VM 백업에서 [Azure VM 및 디스크](./backup-azure-arm-restore-vms.md)를 복원하는 기능을 제공합니다. 이제 이 문서에서는 Azure VM 백업에서 파일 및 폴더와 같은 항목을 어떻게 복구할 수 있는지 설명합니다.

> [!Note]
> 이 기능은 Resource Manager 모델을 사용하여 배포된 Azure VM에서 사용 가능하며 Recovery Services 자격 증명 모음에 대해 보호됩니다.
> 현재는 Windows Azure VM 백업으로부터 파일 복구가 미리 보기 상태입니다. Linux Azure VM으로부터 파일 복구는 곧 제공됩니다.
현재는 암호화된 VM 백업으로부터 파일 복구가 지원되지 않습니다.
>

## <a name="mount-the-volume-and-copy-files"></a>볼륨 탑재 및 파일 복사

1. [Azure 포털](http://portal.Azure.com)에 로그인합니다. 관련 Recovery Services 자격 증명 모음 및 필요한 백업 항목을 찾습니다.

2. 백업 항목 블레이드에서 **파일 복구(미리 보기)**를 클릭합니다.

    ![Recovery Services 자격 증명 모음 백업 항목 열기](./media/backup-azure-restore-files-from-vm/open-vault-item.png)

    **파일 복구** 블레이드가 열립니다.

    ![파일 복구 블레이드](./media/backup-azure-restore-files-from-vm/file-recovery-blade.png)

3. **복구 지점 선택** 드롭다운 메뉴에서 원하는 파일이 들어 있는 복구 지점을 선택합니다. 기본적으로 최신 복구 지점을 선택합니다.

4. **실행 파일 다운로드**를 클릭하여 복구 지점에서 파일을 복사하는 데 사용할 소프트웨어를 다운로드합니다.

  실행 파일은 로컬 컴퓨터와 지정된 복구 지점 간에 연결을 생성하는 스크립트입니다.

5. 파일을 복구할 컴퓨터에서 실행 파일을 실행합니다. 스크립트는 관리자 자격 증명으로 실행해야 합니다. 제한된 액세스를 포함하는 컴퓨터에서 스크립트를 실행하는 경우 다음에 대한 액세스 권한이 있는지 확인합니다.

    - go.microsoft.com
    - Azure VM 백업에 사용된 Azure 끝점
    - 아웃바운드 포트 3260

    ![파일 복구 블레이드](./media/backup-azure-restore-files-from-vm/executable-output.png)

    복구 지점을 생성하는 데 사용한 컴퓨터와 동일한(또는 호환) 운영 체제가 있는 모든 컴퓨터에서 스크립트를 실행할 수 있습니다. 호환되는 운영 체제는 [호환되는 OS 표](backup-azure-restore-files-from-vm.md#compatible-os)를 참조하세요. 보호된 Azure 가상 컴퓨터에서 Windows 저장소 공간을 사용하는 경우 이 가상 컴퓨터에서는 실행 가능한 스크립트를 실행할 수 없습니다. 대신, Windows 저장소 공간을 사용하는 다른 컴퓨터에서 실행 가능한 스크립트를 실행합니다. 호환되는 운영 체제가 있는 컴퓨터에서 실행 가능한 스크립트를 실행하는 것이 좋습니다.

    ![파일 복구 블레이드](./media/backup-azure-restore-files-from-vm/volumes-attached.png)

### <a name="compatible-os"></a>호환되는 OS

다음 표에서는 서버와 컴퓨터 운영 체제 간의 호환성을 보여 줍니다. 파일을 복구할 경우 호환되지 않는 운영 체제 간에는 파일을 복원할 수 없습니다.

|서버 OS | 호환되는 클라이언트 OS  |
| --------------- | ---- |
| Windows Server 2012 R2 | Windows 8.1 |
| Windows Server 2012    | Windows 8  |
| Windows Server 2008 R2 | Windows 7   |


### <a name="drive-letter-assignments"></a>드라이브 문자 할당

스크립트를 실행하면 운영 체제에서 새 볼륨을 탑재하고 드라이브 문자를 할당합니다. Windows 탐색기 또는 파일 탐색기를 사용하여 해당 드라이브를 탐색할 수 있습니다. 볼륨에 할당된 드라이브 문자는 원래 가상 컴퓨터와 다를 수 있지만 볼륨 이름은 유지됩니다. 예를 들어 원래 가상 컴퓨터에서 볼륨이 "데이터 디스크(E:\)"인 경우 해당 볼륨은 로컬 컴퓨터에서 "데이터 디스크('임의 드라이브 문자 사용 가능':\)로 연결할 수 있습니다. 사용자의 파일/폴더를 찾을 때까지 스크립트 출력에 나와 있는 모든 볼륨을 탐색합니다.  


## <a name="closing-the-connection"></a>연결 닫기

파일을 식별하고 로컬 저장소 위치에 복사한 후에는 추가 드라이브를 제거(또는 분리)합니다. 드라이브를 분리하려면 Azure Portal의 **파일 복구** 블레이드에서 **디스크 분리**를 클릭합니다.

![디스크 분리](./media/backup-azure-restore-files-from-vm/unmount-disks3.png)

디스크가 분리되었으면 분리에 성공했다는 메시지가 사용자에게 표시됩니다. 디스크를 제거할 수 있도록 연결을 새로 고치는 데 몇 분이 소요될 수 있습니다.

## <a name="windows-storage-spaces"></a>Windows 저장소 공간

Windows 저장소 공간은 저장소를 가상화할 수 있는 Windows 저장소의 기술입니다. Windows 저장소 공간으로 업계 표준 디스크를 저장소 풀로 그룹화한 후 해당 저장소 풀에 제공되는 공간에서 저장소 공간이라는 가상 디스크를 만들 수 있습니다.

백업된 Azure VM에서 Windows 저장소 공간을 사용하는 경우 이 VM에서는 실행 가능한 스크립트를 실행할 수 없습니다. 대신, Windows 저장소 공간을 사용하는 다른 컴퓨터에서 실행 가능한 스크립트를 실행합니다. 호환되는 운영 체제가 있는 컴퓨터에서 실행 가능한 스크립트를 실행하는 것이 좋습니다.

## <a name="troubleshooting"></a>문제 해결

가상 컴퓨터에서 파일을 복구하는 동안 문제가 생기는 경우 다음 표에서 추가 정보를 확인하세요.

| 오류 메시지/시나리오 | 가능한 원인 | 권장 작업 |
| ------------------------ | -------------- | ------------------ |
| Exe 출력: *대상에 연결하는 동안 예외가 발생했습니다.* |스크립트가 복구 지점에 액세스할 수 없습니다.    | 컴퓨터가 위에 설명된 액세스 요구 사항을 충족하는지 확인하세요.|  
|    Exe 출력: *ISCSI 세션을 통해 대상이 이미 로그인되었습니다.* |    동일한 컴퓨터에서 스크립트가 이미 실행되었고 드라이브가 연결되었습니다. |    복구 지점의 볼륨이 이미 연결되었습니다. 원래 VM과 동일한 드라이브 문자로 탑재되지 않을 수 있습니다. 파일 탐색기에서 사용 가능한 모든 볼륨을 탐색하여 파일을 찾습니다. |
| Exe 출력: *디스크가 포털을 통해 분리되었고 12시간 제한을 초과했으므로 이 스크립트는 유효하지 않습니다. 포털에서 새 스크립트를 다운로드하세요.* |    디스크가 포털에서 분리되었거나 12시간 제한을 초과했습니다. |    이 특정 exe는 현재 유효하지 않고 실행할 수 없습니다. 복구 지정 시간의 파일에 액세스하려면 포털에서 새 exe를 찾으세요.|
| exe가 실행되는 컴퓨터에서: 분리 단추를 클릭하면 새 볼륨이 분리되지 않습니다. |    컴퓨터에서 ISCSI 초기자가 응답하지 않거나 대상에 대한 연결을 새로 고치지 않고 캐시를 유지 관리하지 않습니다. |    분리 단추를 누른 후 몇 분 정도 기다리세요. 새 볼륨이 계속 분리되지 않으면 모든 볼륨을 탐색하세요. 이렇게 하면 초기자가 강제로 연결을 새로 고치도록 하여 디스크를 사용할 수 없다는 오류 메시지와 함께 볼륨이 분리됩니다.|
| Exe 출력: 스크립트가 성공적으로 실행되지만 스크립트 출력에 "New volumes attached(새 볼륨 연결됨)"이 표시되지 않습니다. |    일시적인 오류입니다.    | 볼륨은 이미 연결되었습니다. Explorer를 열어 탐색합니다. 스크립트를 실행할 때마다 동일한 컴퓨터를 사용하는 경우 컴퓨터를 다시 시작하면 목록이 후속 exe 실행에 표시됩니다. |



<!--HONumber=Feb17_HO1-->


