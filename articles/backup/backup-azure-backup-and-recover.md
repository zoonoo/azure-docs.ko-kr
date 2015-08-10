<properties
   pageTitle="Azure Backup - backup and restore from a Windows Server or Windows Client" | Microsoft Azure
   description="백업 및 Windows Server 또는 Windows 클라이언트에서 복원하는 방법을 알아봅니다"
   services="backup"
   documentationCenter=""
   authors="Jim-Parker"
   manager="jwhit"
   editor=""/>

<tags
   ms.service="backup"
   ms.workload="storage-backup-recovery"
	 ms.tgt_pltfrm="na"
	 ms.devlang="na"
	 ms.topic="article"
	 ms.date="07/01/2015"
	 ms.author="jimpark"; "aashishr"/>

# Windows 서버 또는 Windows 클라이언트 컴퓨터에서 백업 및 복원
이 문서는 Windows 서버 또는 Windows 클라이언트 컴퓨터에서 백업하는 데 필요한 단계를 설명합니다. 또한 동일한 컴퓨터에 백업된 파일을 복원하는 데 필요한 단계와 다른 컴퓨터에 백업된 파일을 복원하는 데 필요한 단계를 다룹니다.

## 파일 백업

1. 컴퓨터가 등록되면 Microsoft Azure 백업 mmc 스냅인을 엽니다.

    ![검색 결과](./media/backup-azure-backup-and-recover/result.png)

2. **백업 예약** 을 클릭합니다.

    ![백업 예약](./media/backup-azure-backup-and-recover/schedulebackup.png)

3. 백업하려는 항목을 선택합니다. Windows 서버/Windows 클라이언트에서 Azure 백업을 사용하여(즉, System Center Data Protection Manager 없이) 파일 및 폴더를 보호할 수 있습니다.

    ![백업할 항목](./media/backup-azure-backup-and-recover/items.png)

4. 백업 일정 및 보존 정책을 지정합니다(자세한 설명은 이 [문서](backup-azure-backup-cloud-as-tape.md) 참조).

5. 초기 백업을 보내는 방법을 선택합니다. 초기 시드 완료 옵션은 백업하려는 데이터의 양과 인터넷 업로드 연결 속도에 따라 달라집니다. 대기 시간이 길고 대역폭이 낮은 연결을 통해 GB/TB 단위의 데이터를 백업하려면 가장 가까운 Azure 데이터 센터에 디스크를 배송하여 초기 백업을 완료하는 것이 좋습니다. 이를 "오프라인 백업"이라고 하며, 이에 대한 세부 정보는 이 [문서](backup-azure-backup-import-export.md)에 나와 있습니다. 충분한 대역폭 연결이 있다면 네트워크를 통해 초기 백업을 완료하는 것이 좋습니다.

    ![초기 백업](./media/backup-azure-backup-and-recover/initialbackup.png)

6. 프로세스가 완료되면 mmc 스냅인으로 돌아가서 **지금 백업**을 클릭하여 네트워크를 통한 초기 시드 작업을 완료하세요.

    ![지금 백업](./media/backup-azure-backup-and-recover/backupnow.png)

7. 초기 시드 작업이 완료되면 Azure 백업 콘솔의 **작업** 보기에 상태가 표시됩니다.

    ![IR 완료](./media/backup-azure-backup-and-recover/ircomplete.png)

## 동일한 컴퓨터에서 데이터 복구
파일을 실수로 삭제했는 데 (백업이 수행된) 동일한 컴퓨터에서 파일/볼륨을 복원하려는 경우 다음 단계를 사용하면 데이터를 복구할 수 있습니다.

1. **Microsoft Azure 백업** 스냅인을 엽니다.

2. **데이터 복구**를 클릭하여 워크플로를 시작합니다.

    ![데이터 복구](./media/backup-azure-backup-and-recover/recover.png)

3. 동일한 컴퓨터에 백업된 파일을 복원하려면 **이 서버(\*yourmachinename\*)** 옵션을 선택합니다.

    ![동일한 컴퓨터](./media/backup-azure-backup-and-recover/samemachine.png)

4. **파일 찾아보기** 또는 **파일 검색**을 선택할 수도 있습니다. 경로가 알려져 있는 하나 이상의 파일을 복원하려는 경우 기본 옵션을 그대로 둡니다. 폴더 구조를 잘 모르지만 파일을 검색하려는 경우 **파일 검색** 옵션을 선택합니다. 이 섹션에서는 기본 옵션을 사용하여 진행합니다.

    ![파일 찾아보기](./media/backup-azure-backup-and-recover/browseandsearch.png)

5. 다음 화면에서는 파일을 복원하려는 볼륨을 선택합니다. 이 화면에서는 어느 시점도 복원할 수 있습니다. 달력 컨트롤에 **굵게** 표시되는 날짜는 복원 지점이 사용 가능함을 나타냅니다. 날짜가 선택되면 백업 일정(및 백업 작업의 성공 여부)에 따라 **시간** 드롭다운에서 특정 시점을 선택할 수 있습니다.

    ![볼륨 및 날짜](./media/backup-azure-backup-and-recover/volanddate.png)

6. 복구 하려는 항목을 선택 합니다. 복원하려는 폴더/파일을 다중 선택할 수 있습니다.

    ![파일 선택](./media/backup-azure-backup-and-recover/selectfiles.png)

7. 복구 매개 변수를 지정 합니다.

    ![복구 옵션](./media/backup-azure-backup-and-recover/recoveroptions.png)

  - 원래 위치로 복원(파일/폴더가 덮어써짐) 또는 동일한 컴퓨터의 다른 위치로 복원하는 옵션이 있습니다.
  - 복원하려는 파일/폴더가 대상 위치에 있는 경우 복사본 만들기(동일한 파일의 두 버전) 또는 대상 위치의 파일 덮어쓰기 또는 대상에 있는 파일의 복구 건너뛰기 옵션이 있습니다.
  - 복구할 파일의 ACL을 복원하는 기본 옵션을 그대로 두는 것이 가장 좋습니다.

8. 이러한 입력이 제공되면 이 컴퓨터에 파일을 복원 하는 복구 워크플로가 시작됩니다.

## 다른 컴퓨터로 복구
전체 서버가 손실된 경우에도 파일/볼륨을 다른 컴퓨터에 복구할 수 있습니다. 다음 단계는 워크플로를 보여줍니다.

단계에서 사용 되는 용어 체계는 다음과 같습니다:-*원본 컴퓨터*– 백업하고 있고 현재 사용할 수 없는 원본 컴퓨터.-*대상 컴퓨터*– 데이터가 검색 되는 컴퓨터입니다.-*샘플 자격 증명 모음*– The 백업 자격 증명을 등록된 *원본 컴퓨터*및*대상 컴퓨터*로 부터 저장합니다<br/>

> [AZURE.NOTE]이전 버전의 운영 체제를 실행 중인 컴퓨터에는 컴퓨터에서 수행된 백업을 복원할 수 없습니다. 예를 들어 백업이 Windows 7 컴퓨터에서 수행된 경우 Windows 8 이상의 컴퓨터에서 복원할 수 있습니다. 그러나 그 반대의 경우는 그렇지 않습니다.

1. *대상 컴퓨터*에서 **Microsoft Azure 백업** 스냅인을 엽니다.

2. *대상 컴퓨터* 및 *원본 컴퓨터*가 동일한 백업 자격 증명 모음에 복원됐는지 확인합니다.

3. **데이터 복구**를 클릭하여 워크플로를 시작합니다.

    ![데이터 복구](./media/backup-azure-backup-and-recover/recover.png)

4. **다른 서버**를 선택합니다.

    ![다른 서버](./media/backup-azure-backup-and-recover/anotherserver.png)

5. *샘플 자격 증명 모음*에 해당하는 자격 증명 모음 파일을 제공합니다. 자격 증명 모음 파일이 잘못되었거나 만료된 경우 Azure 포털의 *샘플 자격 증명 모음* 에서 새 자격 증명 모음 파일을 다운로드합니다. 자격 증명 모음 파일이 제공되면 자격 증명 모음 파일에 대한 백업 자격 증명 모음이 표시됩니다.

6. 표시된 컴퓨터 목록에서 *원본 컴퓨터*를 선택합니다.

    ![컴퓨터 목록](./media/backup-azure-backup-and-recover/machinelist.png)

7. 이전과 마찬가지로 **파일 검색** 또는 **파일 찾아보기** 옵션을 선택합니다. 이 섹션에서는 **파일 검색** 옵션을 사용합니다.

    ![이를 통해 검색](./media/backup-azure-backup-and-recover/search.png)

8. 다음 화면에서는 날짜와 볼륨을 선택합니다. 복원하려는 폴더/파일 이름을 검색합니다.

    ![검색 항목](./media/backup-azure-backup-and-recover/searchitems.png)

9. 파일을 복원해야 하는 위치를 선택합니다.

    ![복원 위치](./media/backup-azure-backup-and-recover/restorelocation.png)

10. *원본 컴퓨터*를 *샘플 자격 증명 모음*으로 등록할 때 제공한 암호화의 암호를 제공합니다.

    ![암호화](./media/backup-azure-backup-and-recover/encryption.png)

11. 입력을 제공했으면 제공된 대상에 백업된 파일을 복원하는 작업을 트리거하는 **복구** 단추를 트리거합니다.

## 다음 단계
- [Azure 백업 - FAQ](backup-azure-backup-faq.md)

<!-------HONumber=July15_HO5-->