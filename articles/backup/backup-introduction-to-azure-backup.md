<properties
	pageTitle="Azure 백업 소개 | Microsoft Azure"
	description="이 문서에서는 고객이 Azure로 그리고 Azure에서 데이터를 백업할 수 있게 해주는 Azure 백업 서비스를 간략하게 설명합니다."
	services="backup"
	documentationCenter=""
	authors="trinadhk"
	manager="shreeshd"
	editor="tysonn"/>

<tags ms.service="backup" ms.workload="storage-backup-recovery" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/18/2015" ms.author="trinadhk"; "jimpark"/>

# Azure 백업 소개
이 문서에서는 고객이 온-프레미스 또는 Azure에 있는 데이터를 백업할 수 있는 Microsoft 클라우드 통합 백업 솔루션에 대해 간략히 소개합니다.

## Azure 백업이란?
Azure 백업은 데이터가 온-프레미스에 있든 Azure에 있든 백업할 수 있는 다중 테넌트 Azure 서비스입니다. 기존의 온-프레미스 또는 오프사이트 백업 솔루션을 신뢰할 수 있고 안전하며 가격 경쟁력이 있는 클라우드 기반 제품으로 대체합니다. 또한 클라우드에서 실행되는 자산을 보호하는 유연성을 제공합니다. Azure 백업은 확장성, 내구성 및 고가용성을 제공하는 최고 수준의 인프라를 기반으로 하여 빌드되었습니다. 이 솔루션을 사용하면 SCDPM(System Center Data Protection Manager) 서버, Windows 서버, Windows 클라이언트 컴퓨터 또는 Azure IaaS 가상 컴퓨터에서 데이터와 응용 프로그램을 백업할 수 있습니다. Azure 백업 및 SCDPM은 Microsoft 클라우드 통합 백업 솔루션을 구성하는 기본 기술입니다.

> [AZURE.VIDEO what-is-azure-backup]

## 클라우드 디자인 요점
기존의 백업 솔루션은 디스크 또는 테이프와 유사한 끝점으로 클라우드를 처리하도록 발전했습니다. 이 방법은 간단하고 쉽게 배포할 수 있으며 일관된 경험을 제공하지만, 사용이 제한되고 기본 플랫폼을 충분히 활용하지 않습니다. 이는 최종 고객에게 비효율적이고, 비용이 많이 드는 솔루션을 의미합니다. 백업 솔루션은 Azure를 "저장소 끝점처럼" 처리하여 공용 클라우드 플랫폼의 풍부하고 강력한 기능을 활용할 수 없습니다. 반면, Azure 백업은 클라우드를 사용하여 강력하고 경제적인 백업 솔루션을 제공하는 진정한 서비스를 제공합니다. 온-프레미스 백업 솔루션(SCDPM)과 통합되어 종단 간 하이브리드 솔루션을 제공합니다.

이 접근 방식의 이점은 다음과 같습니다.

- 저렴한 비용, 복원력 있는 데이터 저장소를 제공하는 효율적인 클라우드 저장소 아키텍처
- 고가용성을 보장하는 비침입적인 서비스 자동 크기 조정
- 일관된 방식의 온 프레미스, 하이브리드 및 IaaS 배포 백업

이 솔루션의 주요 기능은 다음과 같습니다.

1. **신뢰할 수 있는 서비스**: Azure 백업을 채택하면 고가용성 백업 서비스를 사용할 수 있습니다. 다중 테넌트 서비스이며, 기본 계산 또는 저장소 관리에 대해 걱정할 필요가 없습니다.

2. **신뢰할 수 있는 저장소**: Azure 백업은 높은 SLA로 지원되는 신뢰할 수 있는 클라우드 저장소를 기반으로 하여 빌드되었습니다. 저장소를 유지 관리하는 자본 또는 운영 비용에 대해 걱정할 필요가 없습니다. 또한 LRS(로컬 중복 저장소) 또는 GRS(지역 복제 저장소) 저장소에 백업하도록 선택할 수 있습니다. LRS는 동일한 지역에서 3개의 데이터 복사본을 사용하여 로컬 하드웨어 오류로부터 보호하고, GRS는 쌍을 이루는 데이터 센터에서 3개의 추가 복사본(총 6개 복사본)을 제공합니다. 이렇게 하면 백업 데이터의 고가용성을 유지할 수 있습니다. Azure 사이트 수준의 재해가 있는 경우에도 백업 데이터가 안전하게 보호됩니다.

3. **보안**: Azure 백업 데이터는 원본, 전송 중 및 Azure에 저장 시 암호화됩니다. 암호화 키는 원본에 저장되며 전송되거나 Azure에 저장되지 않습니다. 데이터를 복원하려면 암호화 키가 필요하며, 해당 고객만 서비스의 데이터에 완전히 액세스할 수 있습니다.

4. **오프사이트 보호**: 고객은 오프사이트 테이프 백업 솔루션에 대한 요금을 내는 대신, 매우 저렴한 비용으로 테이프와 유사한 의미 체계의 매력적인 솔루션을 제공하는 Azure에 백업할 수 있습니다.

5. **단순성**: Azure 백업은 모든 규모의 배포를 보호하기 위해 크기가 조정될 수 있는 친숙한 인터페이스를 제공합니다. 서비스가 발전함에 따라 중앙 관리와 같은 기능을 통해 단일 위치에서 백업 인프라를 관리할 수 있습니다.

6. **비용 효율적임**: Azure 백업 가격에는 인스턴스별 백업 관리 요금 및 Azure에서 사용된 저장소(블록 Blob 가격) 요금이 포함됩니다. 다른 클라우드 기반 백업 제품과 달리 Azure 백업은 복원 작업에 대한 요금을 고객에게 부과하지 않습니다. 또한 복원 작업 중 송신(아웃바운드) 데이터 전송 비용을 고객에게 청구하지도 않습니다.

7. **클라우드에서 백업**: Azure 백업은 가상 컴퓨터를 종료하지 않고도 실행 중인 Azure IaaS 가상 컴퓨터에 대해 VSS 기반 응용 프로그램 일치 백업을 제공합니다. Azure에서 파일 시스템 일관성을 통해 Linux 가상 컴퓨터도 백업할 수 있습니다.


## 응용 프로그램 및 워크로드

| 워크로드 | 원본 컴퓨터 | Azure 백업 솔루션 |
| --- | --- |---|
| 파일 및 폴더 | Windows Server, Windows 클라이언트 | Azure 백업 에이전트 |
| 파일 및 폴더 | Windows Server, Windows 클라이언트 | System Center DPM |
| Hyper-V 가상 컴퓨터(Windows) | Windows Server | System Center DPM |
| Hyper-V 가상 컴퓨터(Linux) | Windows Server | System Center DPM |
| Microsoft SQL Server | Windows Server | System Center DPM |
| Microsoft SharePoint | Windows Server | System Center DPM |
| Microsoft Exchange | Windows Server | System Center DPM |
| Azure IaaS VM(Windows)| - | Azure 백업 | | Azure IaaS VM(Linux) | - | Azure 백업 |

## 다음 단계
- [Azure 백업 시도](backup-try-azure-backup-in-10-mins.md)
- Azure 백업 서비스에 대한 질문과 대답은 [여기](backup-azure-backup-faq.md)에 나열되어 있습니다.
- [Azure 백업 포럼](http://go.microsoft.com/fwlink/p/?LinkId=290933)을 방문하세요.

<!---HONumber=August15_HO8-->