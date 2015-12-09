<properties
	pageTitle="Windows Server 백업을 준비하도록 Azure 백업 서비스 구성 | Microsoft Azure"
	description="이 자습서를 통해 Microsoft Azure 클라우드 제품 내 백업 서비스를 사용하여 Windows Server를 클라우드에 백업하는 방법을 알아볼 수 있습니다."
	services="backup"
	documentationCenter=""
	authors="Jim-Parker"
	manager="jwhit"
	editor=""/>

<tags ms.service="backup" ms.workload="storage-backup-recovery" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="hero-article" ms.date="11/26/2015" ms.author="jimpark"; "aashishr"/>

# Windows 컴퓨터를 백업하기 위한 환경 준비

이 문서에서는 Windows Server와 함께 Azure 백업을 사용하는 준비 단계를 안내합니다. Windows Server 또는 Windows 클라이언트를 Azure에 백업하려면 Azure 계정이 필요합니다. 계정이 없는 경우 몇 분 만에 무료 평가판 계정을 만들 수 있습니다. 자세한 내용은 [Azure 무료 체험](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.

>[AZURE.NOTE]이전에는 백업 서버를 등록하려면 X.509 v3 인증서를 만들거나 얻어야 했습니다. 인증서는 계속 지원되지만 이제는 Azure 저장소를 서버에 보다 간단히 등록할 수 있도록 하기 위해 빠른 시작 페이지에서 바로 저장소 자격 증명을 생성할 수 있습니다.

## 시작하기 전에
Windows Server에서 파일 및 데이터를 Azure에 백업하려면 먼저 다음을 수행해야 합니다.

- **백업 자격 증명 모음 만들기** — [Azure 백업 관리 포털](http://manage.windowsazure.com)에 자격 증명 모음을 만듭니다.
- **보관 자격 증명 다운로드** — Azure 백업 모음의 대시보드 페이지에서 백업 자격 증명 모음에 Windows 컴퓨터를 등록하는 데 사용할 보관 자격 증명을 다운로드합니다.
- **Azure 백업 에이전트 설치 및 서버 등록** — 대시보드 페이지에서 [Azure 백업 에이전트](http://aka.ms/azurebackup_agent) 다운로드 링크를 클릭합니다. 에이전트를 설치하고 보관 자격 증명을 사용하여 백업 자격 증명 모음에 서버를 등록합니다.

[AZURE.INCLUDE [백업-만들기-자격 증명 모음-wgif](../../includes/backup-create-vault-wgif.md)]

[AZURE.INCLUDE [backup-download-credentials](../../includes/backup-download-credentials.md)]

[AZURE.INCLUDE [backup-install-agent](../../includes/backup-install-agent.md)]

## 다음 단계
- [Windows Server 또는 Windows 클라이언트 백업](backup-azure-backup-windows-server.md)
- [Windows Server 또는 Windows 클라이언트 관리](backup-azure-manage-windows-server.md)
- [Azure에서 Windows Server 또는 Windows 클라이언트 복원](backup-azure-restore-windows-server.md)
- [Azure 백업 - FAQ](backup-azure-backup-faq.md)
- [Azure 백업 포럼](http://go.microsoft.com/fwlink/p/?LinkId=290933)

<!---HONumber=AcomDC_1203_2015-->