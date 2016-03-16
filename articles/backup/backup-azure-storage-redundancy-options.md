<properties
	pageTitle="Azure 백업 저장소 중복 옵션 확인 | Microsoft Azure"
	description="Azure 백업 저장소 중복 옵션을 결정하기 위해 지역 중복 저장소와 로컬 중복 저장소의 차이점을 파악합니다."
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
	ms.date="02/05/2016"
	ms.author="jimpark; markgal"/>


# Azure 백업에 대한 저장소 중복 옵션

비즈니스 요구 사항에 따라 Azure 백업 저장소에 대한 적절한 저장소 중복 옵션이 결정됩니다. 기본 백업 저장소 끝점으로 Azure를 사용 중인 경우(예: Windows Server에서 Azure로 백업하는 경우) 지역 중복 저장소 옵션(기본값)을 선택하는 것이 좋습니다. 3차 백업 저장소 끝점으로 Azure를 사용 중인 경우(예: 온-프레미스에 로컬 백업 복사본을 보관하기 위해 SCDPM을 사용하는 경우 및 장기 보존 요구를 해결하기 위해 Azure를 사용하는 경우) 로컬 중복 저장소를 선택하는 것이 좋습니다.

## GRS(지역 중복 저장소)

지역 중복 저장소는 데이터의 복제본 6개를 유지 관리합니다. GRS를 사용하면 데이터가 기본 영역에서 3번 복제되고 기본 지역으로부터 수백 킬로미터 떨어진 보조 지역에도 3번 복제되며, 최고 수준의 영속성을 제공합니다. Azure 백업은 주 지역에서 오류가 발생할 경우 GRS에 데이터를 저장하여 두 지역에서 데이터가 지속되도록 합니다.

## 로컬 중복 저장소(LRS)

로컬 중복 저장소는 데이터의 복제본 3개를 유지 관리합니다. LRS는 단일 지역의 단일 시설 내에서 3번 복제됩니다. LRS는 전체 Azure 시설의 오류가 아닌 일반적인 하드웨어 오류로부터 데이터를 보호합니다. 이렇게 하면 Azure에 데이터를 저장하는 비용을 크게 줄일 수 있지만 3차 복사본에 허용될 수 있는 데이터 영속성 수준이 낮아집니다.

백업 자격 증명 모음의 **구성** 옵션에서 요구 사항에 맞게 적절한 옵션을 선택할 수 있습니다.

## 다음 단계

- 환경이 [Windows 서버 또는 클라이언트 컴퓨터를 백업할 수 있도록 준비](backup-configure-vault.md)되었는지 확인합니다.
- 여전히 답변이 없는 질문이 있는 경우 [Azure 백업 FAQ](backup-azure-backup-faq.md)를 살펴보세요.
- [Azure 백업 포럼](http://go.microsoft.com/fwlink/p/?LinkId=290933)을 방문하세요.

<!---HONumber=AcomDC_0211_2016-->