<properties
	pageTitle="Azure 백업 자격 증명 모음 및 서버 관리 | Microsoft Azure"
	description="이 자습서를 사용하여 Azure 백업 저장소 및 서버를 관리하는 방법을 알아봅니다."
	services="backup"
	documentationCenter=""
	authors="aashishr"
	manager="jwhit"
	editor="tysonn"/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/05/2015"
	ms.author="jimpark; aashishr; giridham"/>


# Azure 백업 자격 증명 모음 및 서버 관리
이 문서에서 관리 포털을 통해 사용할 수 있는 백업 관리 작업의 개요를 찾을 수 있습니다.

1. [관리 포털](https://manage.windowsazure.com)에 로그인합니다.
2. **복구 서비스**를 클릭한 후 백업 저장소의 이름을 클릭하여 빠른 시작 페이지를 표시합니다.

    빠른 시작 페이지의 위쪽에 있는 옵션을 선택하여 사용 가능한 관리 작업을 볼 수 있습니다.

    ![보호된 항목](./media/backup-azure-manage-windows-server/RS_tabs.png)

## 대시보드
**대시보드**를 선택하여 서버의 사용 개요를 확인합니다. 대시보드 아래쪽에서 수행할 수 있는 작업은 다음과 같습니다.

- **인증서 관리**. 서버를 등록하는 데 인증서가 사용된 경우 이 옵션을 통해 인증서를 업데이트합니다. 저장소 자격 증명을 사용하는 경우에는 **인증서 관리**를 사용해서는 안 됩니다.
- **삭제**. 현재 백업 저장소를 삭제합니다. 백업 자격 증명 모음이 더 이상 사용되지 않는 경우 삭제하여 저장소 공간을 확보할 수 있습니다. **삭제**는 등록된 모든 서버가 저장소에서 삭제된 후에만 사용할 수 있습니다.
- **저장소 자격 증명**. 이 간략 상태 메뉴 항목을 사용하여 저장소 자격 증명을 구성합니다.

## 보호된 항목
**보호된 항목**을 선택하여 서버에서 백업된 항목을 표시합니다. 이 목록은 정보 제공 목적으로만 사용됩니다.

![보호된 항목](./media/backup-azure-manage-windows-server/RS_protecteditems.png)

## 등록된 항목
**등록된 항목**을 선택하여 이 저장소에 등록된 서버의 이름을 표시합니다.

![삭제된 서버](./media/backup-azure-manage-windows-server/RS_deletedserver.png)

여기서 다음 작업을 수행할 수 있습니다.

- **다시 등록 허용** - 서버에 이 옵션이 선택된 경우 에이전트에서 **등록 마법사**를 사용하여 백업 저장소에 서버를 다시 등록할 수 있습니다. 인증서 오류로 인해 또는 서버를 다시 빌드해야 한 경우 다시 등록해야 할 수도 있습니다.
- **삭제** - 백업 저장소에서 서버를 삭제합니다. 서버와 관련해서 저장된 모든 데이터가 즉시 삭제됩니다.

## 다음 단계
- [Azure에서 Windows Server 또는 Windows 클라이언트 복원](backup-azure-restore-windows-server.md)
- Azure 백업에 대한 자세한 내용은 [Azure 백업 개요](backup-introduction-to-azure-backup.md)를 참조하세요.
- [Azure 백업 포럼](http://go.microsoft.com/fwlink/p/?LinkId=290933)을 방문하세요.

<!---HONumber=Oct15_HO3-->