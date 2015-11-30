<properties
   pageTitle="Azure에 Windows Server 또는 Windows 클라이언트 파일 및 폴더 백업 | Microsoft Azure"
   description="이 간단한 절차로 Windows Server 또는 Windows 클라이언트를 Azure에 백업합니다. 몇 번의 간단한 단계로 Windows 파일 및 폴더를 클라우드로 백업할 수 있습니다."
   services="backup"
   documentationCenter=""
   authors="aashishr"
   manager="jwhit"
   editor=""
   keywords="windows server 백업; 백업 windows server"/>

<tags ms.service="backup" ms.workload="storage-backup-recovery" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="11/17/2015" ms.author="jimpark"; "aashishr"/>

# Azure에 Windows Server 또는 Windows 클라이언트 파일 및 폴더 백업
이 간단한 절차로 Windows 파일 및 폴더를 Azure로 간단하게 백업합니다. 아직 수행하지 않은 경우 계속 진행하기 전에 [필수 조건](backup-configure-vault.md#before-you-start)을 완료하여 Windows 컴퓨터를 백업하기 위한 환경을 준비합니다.

## 파일 백업
1. 컴퓨터가 등록되면 Microsoft Azure 백업 mmc 스냅인을 엽니다.

    ![검색 결과](./media/backup-azure-backup-windows-server/result.png)

2. **백업 예약**을 클릭합니다.

    ![Windows Server 백업 예약](./media/backup-azure-backup-windows-server/schedulebackup.png)

3. 백업하려는 항목을 선택합니다. Windows 서버/Windows 클라이언트에서 Azure 백업을 사용하여(즉, System Center Data Protection Manager 없이) 파일 및 폴더를 보호할 수 있습니다.

    ![Windows Server 백업에 대한 항목](./media/backup-azure-backup-windows-server/items.png)

4. 백업 일정 및 보존 정책을 지정합니다(자세한 설명은 다음 [문서](backup-azure-backup-cloud-as-tape.md) 참조).

5. 초기 백업을 보내는 방법을 선택합니다. 초기 시드 완료 옵션은 백업하려는 데이터의 양과 인터넷 업로드 연결 속도에 따라 달라집니다. 대기 시간이 길고 대역폭이 낮은 연결을 통해 GB/TB 단위의 데이터를 백업하려면 가장 가까운 Azure 데이터 센터에 디스크를 배송하여 초기 백업을 완료하는 것이 좋습니다. 이를 "오프라인 백업"이라고 하며, 이에 대한 세부 정보는 이 [문서](backup-azure-backup-import-export.md)에 나와 있습니다. 충분한 대역폭 연결이 있다면 네트워크를 통해 초기 백업을 완료하는 것이 좋습니다.

    ![초기 Windows Server 백업](./media/backup-azure-backup-windows-server/initialbackup.png)

6. 일정 백업 프로세스가 완료된 후에 mmc 스냅인으로 돌아가서 **지금 백업**을 클릭하여 네트워크를 통한 초기 시드 작업을 완료하세요.

    ![지금 Windows Server 백업](./media/backup-azure-backup-windows-server/backupnow.png)

7. 초기 시드 작업이 완료된 후에 Azure 백업 콘솔의 **작업** 보기에 상태가 표시됩니다.

    ![IR 완료](./media/backup-azure-backup-windows-server/ircomplete.png)

## 다음 단계
- [Windows Server 또는 Windows 클라이언트 관리](backup-azure-manage-windows-server.md)
- [Azure에서 Windows Server 또는 Windows 클라이언트 복원](backup-azure-restore-windows-server.md)
- [Azure 백업 - FAQ](backup-azure-backup-faq.md)

<!---HONumber=Nov15_HO4-->