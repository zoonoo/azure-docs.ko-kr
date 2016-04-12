<properties
   pageTitle="Azure 백업 FAQ의 공개 미리 보기 릴리스 | Microsoft Azure"
   description="이 버전의 FAQ는 Azure 백업 서비스의 공개 미리 보기 릴리스를 지원합니다. 백업 에이전트, 백업 및 보존, 복구, 보안 및 Azure 백업 솔루션과 관련하여 자주 묻는 질문에 대한 대답입니다."
   services="backup"
   documentationCenter=""
   authors="markgalioto"
   manager="jwhit"
   editor=""
   keywords="백업 솔루션; 백업 서비스"/>

<tags
   ms.service="backup"
   ms.workload="storage-backup-recovery"
	 ms.tgt_pltfrm="na"
	 ms.devlang="na"
	 ms.topic="get-started-article"
	 ms.date="03/30/2016"
	 ms.author="trinadhk; markgal; jimpark;"/>

# Azure 백업 서비스의 공개 미리 보기 릴리스- FAQ

> [AZURE.SELECTOR]
- [클래식 모드에 대한 백업 FAQ](backup-azure-backup-faq.md)
- [ARM 모드에 대한 백업 FAQ](backup-azure-backup-ibiza-faq.md)

이 문서에서는 Azure 백업 서비스 공개 미리 보기 릴리스에 관련된 정보를 제공합니다. 이 문서는 새로운 자주 묻는 질문이 도착한 경우 업데이트되어 [Azure 백업 FAQ](backup-azure-backup-faq)를 보충합니다. Azure 백업 FAQ는 Azure 백업 서비스에 대한 질문 및 답변의 전체 집합을 제공합니다.

이 문서 또는 관련 문서의 Disqus 섹션에서 Azure 백업에 대한 질문을 할 수 있습니다. 또한 [토론 포럼](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazureonlinebackup)에 Azure 백업 서비스에 대한 질문도 게시할 수 있습니다.

## 공개 미리 보기 릴리스의 내용은 무엇인가요?
공개 미리 보기 릴리스에서는 Azure VM을 보호할 경우의 복구 서비스 자격 증명 모음 및 ARM 지원을 소개합니다. 복구 서비스 자격 증명 모음은 자격 증명 모음의 차세대 기능입니다. Azure 백업 및 ASR(Azure Site Recovery) 서비스에서 사용하는 자격 증명 모음입니다. V.2 자격 증명 모음으로 생각합니다.

## 복구 서비스 및 백업 자격 증명 모음

**Q1. 복구 서비스 자격 증명 모음은 v.2이고 백업 자격 증명 모음(v.1)도 여전히 지원이 되나요?** <br/> A1. 예, 백업 자격 증명 모음도 계속 지원됩니다. 클래식 포털에서 백업 자격 증명 모음을 만듭니다. Azure 포털에서 복구 서비스 자격 증명 모음을 만듭니다.

**Q2. 복구 서비스 자격 증명 모음에 백업 자격 증명 모음을 마이그레이션할 수 있나요?** <br/> A2. 아니요, 안타깝지만 지금은 백업 저장소의 컨텐츠를 복구 서비스 자격 증명 모음에 마이그레이션할 수 없습니다. 이 기능을 추가하려고 노력하고 있지만 공개 미리 보기의 일환으로 사용할 수 없습니다.

**Q3. 복구 서비스 자격 증명 모음은 v.1 또는 v.2 VM을 지원하나요?** <br/> A3. 복구 서비스 자격 증명 모음은 v.1 및 v.2 VM을 지원합니다. 복구 서비스 자격 증명 모음에 클래식 포털에서 만들어진 VM(즉, V.1) 또는 Azure 포털에서 만들어진 VM(즉, V.2)을 백업할 수 있습니다.


## Azure VM에 대한 ARM 지원

**Q1. Azure VM에 대한 ARM 지원을 제한하나요?** <br/> A1. 현재 ARM에 대해 PowerShell cmdlet을 사용할 수 없습니다. 리소스 그룹에 리소스를 추가하려면 Azure 포털 UI를 사용해야 합니다.

<!---HONumber=AcomDC_0406_2016-->