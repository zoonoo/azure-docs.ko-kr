<properties
   pageTitle="클래식 배포 모델을 사용하여 Azure에서 Windows 서버 또는 Windows 클라이언트로 데이터 복원 | Microsoft Azure"
   description="Windows 서버 또는 Windows 클라이언트에서 복원하는 방법을 알아보세요."
   services="backup"
   documentationCenter=""
   authors="saurabhsensharma"
   manager="shivamg"
   editor=""/>

<tags
   ms.service="backup"
   ms.workload="storage-backup-recovery"
	 ms.tgt_pltfrm="na"
	 ms.devlang="na"
	 ms.topic="article"
	 ms.date="08/02/2016"
	 ms.author="trinadhk; jimpark; markgal;"/>

# 클래식 배포 모델을 사용하여 Windows 서버 또는 Windows 클라이언트 컴퓨터로 파일 복원

> [AZURE.SELECTOR]
- [클래식 포털](backup-azure-restore-windows-server-classic.md)
- [Azure 포털](backup-azure-restore-windows-server.md)

이 문서는 두가지 유형의 복원 작업을 수행하는 데 필요한 단계를 다룹니다.

- 백업을 수행한 동일한 컴퓨터에 데이터를 복원합니다.
- 데이터를 다른 컴퓨터에 복원합니다.

두 경우 모두 데이터는 Azure 백업 자격 증명 모음에서 검색됩니다.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

## 동일한 컴퓨터로 데이터 복구
파일을 실수로 삭제했는데 (백업이 수행된) 동일한 컴퓨터에서 복원하려는 경우 다음 단계를 사용하면 데이터를 복구할 수 있습니다.

1. **Microsoft Azure 백업** 스냅인을 엽니다.
2. **데이터 복구**를 클릭하여 워크플로를 시작합니다.

    ![데이터 복구](./media/backup-azure-restore-windows-server-classic/recover.png)

3. 동일한 컴퓨터에 백업된 파일을 복원하려면 **이 서버(*yourmachinename*)** 옵션을 선택합니다.

    ![동일한 컴퓨터](./media/backup-azure-restore-windows-server-classic/samemachine.png)

4. **파일 찾아보기** 또는 **파일 검색**을 선택합니다.

    경로가 알려져 있는 하나 이상의 파일을 복원하려는 경우 기본 옵션을 그대로 둡니다. 폴더 구조를 잘 모르지만 파일을 검색하려는 경우 **파일 검색** 옵션을 선택합니다. 이 섹션에서는 기본 옵션을 사용하여 진행합니다.

    ![파일 찾아보기](./media/backup-azure-restore-windows-server-classic/browseandsearch.png)

5. 다음 화면에서는 파일을 복원하려는 볼륨을 선택합니다.

    어떤 지점이라도 복원할 수 있습니다. 달력 컨트롤에 **굵게** 표시되는 날짜는 복원 지점이 사용 가능함을 나타냅니다. 날짜가 선택되면 백업 일정(및 백업 작업의 성공 여부)에 따라 **시간** 드롭다운에서 특정 시점을 선택할 수 있습니다.

    ![볼륨 및 날짜](./media/backup-azure-restore-windows-server-classic/volanddate.png)

6. 복구할 항목을 선택합니다. 복원하려는 폴더/파일을 다중 선택할 수 있습니다.

    ![파일 선택](./media/backup-azure-restore-windows-server-classic/selectfiles.png)

7. 복구 매개 변수를 지정 합니다.

    ![복구 옵션](./media/backup-azure-restore-windows-server-classic/recoveroptions.png)

  - 원래 위치로 복원(파일/폴더가 덮어써짐) 또는 동일한 컴퓨터의 다른 위치로 복원하는 옵션이 있습니다.
  - 복원하려는 파일/폴더가 대상 위치에 있는 경우 복사본 만들기(동일한 파일의 두 버전) 또는 대상 위치의 파일 덮어쓰기 또는 대상에 있는 파일의 복구 건너뛸 수 있습니다.
  - 복구할 파일의 ACL을 복원하는 기본 옵션을 그대로 두는 것이 가장 좋습니다.

8. 이러한 입력을 제공하면 **다음**을 클릭합니다. 이 컴퓨터에 파일을 복원하는 복구 워크플로를 시작합니다.

## 다른 컴퓨터로 복구
전체 서버가 손실된 경우에도 Azure 백업에서 데이터를 다른 컴퓨터에 복구할 수 있습니다. 다음 단계는 워크플로를 보여줍니다.

다음 단계에서 사용되는 용어는 다음과 같습니다.

- *원본 컴퓨터* – 처음에 백업이 수행되었고 현재는 사용할 수 없는 컴퓨터입니다.
- *대상 컴퓨터* – 데이터가 복구되는 컴퓨터입니다.
- *샘플 자격 증명 모음* – *원본 컴퓨터* 및 *대상 컴퓨터*가 등록된 백업 자격 증명 모음입니다. <br/>

> [AZURE.NOTE] 이전 버전의 운영 체제를 실행 중인 컴퓨터에는 컴퓨터에서 수행된 백업을 복원할 수 없습니다. 예를 들어 백업이 Windows 7 컴퓨터에서 수행된 경우 Windows 8 이상의 컴퓨터에서 복원할 수 있습니다. 그러나 그 반대의 경우는 그렇지 않습니다.

1. *대상 컴퓨터*에서 **Microsoft Azure 백업** 스냅인을 엽니다.
2. *대상 컴퓨터* 및 *원본 컴퓨터*가 동일한 백업 자격 증명 모음에 등록됐는지 확인합니다.
3. **데이터 복구**를 클릭하여 워크플로를 시작합니다.

    ![데이터 복구](./media/backup-azure-restore-windows-server-classic/recover.png)

4. **다른 서버**를 선택합니다.

    ![다른 서버](./media/backup-azure-restore-windows-server-classic/anotherserver.png)

5. *샘플 자격 증명 모음*에 해당하는 자격 증명 모음 파일을 제공합니다. 자격 증명 모음 파일이 잘못되었거나 만료된 경우 Azure 클래식 포털의 *샘플 자격 증명 모음* 에서 새 자격 증명 모음 파일을 다운로드합니다. 자격 증명 모음 파일이 제공되면 자격 증명 모음 파일에 대한 백업 자격 증명 모음이 표시됩니다.

6. 표시된 컴퓨터 목록에서 *원본 컴퓨터*를 선택합니다.

    ![컴퓨터 목록](./media/backup-azure-restore-windows-server-classic/machinelist.png)

7. **파일 검색** 또는 **파일 찾아보기** 옵션을 선택합니다. 이 섹션에서는 **파일 검색** 옵션을 사용합니다.

    ![이를 통해 검색](./media/backup-azure-restore-windows-server-classic/search.png)

8. 다음 화면에서는 날짜와 볼륨을 선택합니다. 복원하려는 폴더/파일 이름을 검색합니다.

    ![검색 항목](./media/backup-azure-restore-windows-server-classic/searchitems.png)

9. 파일을 복원해야 하는 위치를 선택합니다.

    ![복원 위치](./media/backup-azure-restore-windows-server-classic/restorelocation.png)

10. *원본 컴퓨터*를 *샘플 자격 증명 모음*으로 등록할 때 제공한 암호화의 암호를 제공합니다.

    ![암호화](./media/backup-azure-restore-windows-server-classic/encryption.png)

11. 입력을 제공하면 제공된 대상에 백업된 파일을 복원하는 작업을 트리거하는 **복구**를 클릭합니다.

## 다음 단계
- [Azure 백업 - FAQ](backup-azure-backup-faq.md)
- [Azure 백업 포럼](http://go.microsoft.com/fwlink/p/?LinkId=290933)을 방문하세요.

## 자세한 정보
- [Azure 백업 개요](http://go.microsoft.com/fwlink/p/?LinkId=222425)
- [Azure 가상 컴퓨터 백업](backup-azure-vms-introduction.md)
- [Microsoft 워크로드 백업](backup-azure-dpm-introduction.md)

<!---HONumber=AcomDC_0803_2016-->