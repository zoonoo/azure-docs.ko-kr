<properties
	pageTitle="Azure DPM 백업 | Microsoft Azure 소개"
	description="Azure 백업 서비스를 사용하여 DPM 서버를 백업하는 방법 소개"
	services="backup"
	documentationCenter=""
	authors="SamirMehta"
	manager="jwhit"
	editor=""/>

<tags ms.service="backup" ms.workload="storage-backup-recovery" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/05/2015" ms.author="sammehta"; "jimpark"/>

# DPM을 통해 Azure에서 워크로드 백업 준비

> [AZURE.SELECTOR]
- [Azure Backup](backup-azure-microsoft-azure-backup.md)
- [SCDPM](backup-azure-dpm-introduction.md)

이 문서에서는 Microsoft Azure 백업을 사용하여 System Center Data Protection Manager(DPM) 서버와 워크로드를 보호하는 방법을 소개합니다. 이 문서를 읽어 보면 다음을 이해하게 됩니다.

- Azure DPM 서버 백업 작동 방식
- 원활한 백업 경험을 위한 필수 조건
- 발생하는 일반적인 오류 및 오류를 처리하는 방법
- 지원되는 시나리오

System Center DPM 백업 파일 및 애플리케이션 데이터 DPM에 백업된 데이터는 테이프나 디스크에 저장하거나 Microsoft Azure 백업을 사용하여 Azure에 백업할 수 있습니다. DPM 은 Azure 백업과 다음과 같이 상호작용합니다.

- **물리적 서버 또는 온-프레미스 가상 컴퓨터로 배포하는 DPM** — DPM을 물리적 서버 또는 온-프레미스 Hyper-V 가상 컴퓨터로 배포하는 경우, 디스크나 테이프 백업에 더해 데이터를 Azure 백업 자격 증명 모음에 백업할 수 있습니다.
- **Azure 가상 컴퓨터로 배포하는 DPM** — System Center 2012 R2 업데이트 3부터 DPM을 Azure 가상 컴퓨터로 배포할 수 있습니다. DPM을 Azure 가상 컴퓨터로 배포하는 경우, 데이터를 DPM Azure 가상 컴퓨터에 연결된 Azure 디스크에 백업하거나 데이터 스토리지를 Azure 백업 자격 증명 모음에 백업하여 오프로드할 수 있습니다.

## DPM 서버를 백업하는 이유

DPM 서버 백업에 Azure 백업을 사용할 경우의 비즈니스 이점은 다음과 같습니다.

- 온-프레미스 DPM 배포의 경우, 테이프에 대한 장기 배포 대신 Azure 백업을 사용할 수 있습니다.
- Azure로 DPM을 배포하는 경우, Azure 백업을 사용하면 Azure 디스크에서 저장소를 오프로드할 수 있어 오래된 데이터를 Azure 백업에 저장하고 새 데이터를 디스크에 저장하여 강화할 수 있습니다.

## DPM 서버 백업 작동 방식
가상 컴퓨터를 백업하려면 먼저 데이터의 지정 시간 스냅숏이 필요합니다. Azure 백업 서비스는 예약된 시간에 백업 작업을 시작하고 스냅숏을 만드는 백업 확장을 트리거합니다. 백업 확장은 일관성을 유지하기 위해 게스트 내 VSS 서비스와 조정되고, 일관성이 달성되면 Azure 저장소 서비스의 Blob 스냅숏 API를 호출합니다. 이 작업은 종료하지 않고 가상 컴퓨터의 일관된 디스크 스냅숏을 가져오기 위한 것입니다.

스냅숏이 생성된 후 Azure 백업 서비스가 백업 자격 증명 모음으로 데이터를 전송합니다. 서비스는 마지막 백업에서 변경된 블록만 식별하고 전송하여 백업 자격 증명 모음 및 네트워크를 효율적으로 만듭니다. 데이터 전송이 완료되면 스냅숏이 제거되고 복구 지점이 생성됩니다. 이 복구 지점은 Azure 관리 포털에서 확인할 수 있습니다.

>[AZURE.NOTE]Linux 가상 컴퓨터의 경우 파일 일치 백업만 가능합니다.

## 필수 조건
DPM 데이터를 백업하기 위해 다음과 같이 Azure 백업을 준비합니다.

1. **백업 자격 증명 모음 만들기** — Azure 백업 콘솔에 자격 증명 모음을 만듭니다.
2. **저장소 자격 증명 다운로드** — Azure 백업에서 자격 증명 모음에 대해 만든 관리 인증서를 업로드합니다.
3. **Azure 백업 에이전트 설치 및 서버 등록** — Azure 백업에서 각 DPM 서버에 에이전트를 설치하고 백업 자격 증명 모음에 DPM 서버를 등록합니다.

[AZURE.INCLUDE [backup-create-vault](../../includes/backup-create-vault.md)]

[AZURE.INCLUDE [backup-download-credentials](../../includes/backup-download-credentials.md)]

[AZURE.INCLUDE [backup-install-agent](../../includes/backup-install-agent.md)]


## 요구 사항(및 제한 사항)

- DPM은 물리적 서버 또는 System Center 2012 SP1 또는 System Center 2012 R2에 설치된 Hyper-V 가상 컴퓨터로 실행할 수 있습니다. 최소 System Center 2012 R2 DPM 2012 R2 업데이트 롤업 3에서 실행되는 Azure 가상 컴퓨터 또는 최소 System Center 2012 R2 업데이트 롤업 5에서 실행되는 VMWare의 Windows 가상 컴퓨터로도 실행할 수 있습니다.
- DPM을 System Center 2012 SP1에서 실행 중인 경우, System Center Data Protection Manager SP1용 업데이트 롤업 2를 설치해야 합니다. Azure 백업 에이전트를 설치하기 전에 필수입니다.
- DPM 서버는 Windows PowerShell 및 .Net Framework 4.5가 설치되어 있어야 합니다.
- DPM은 대부분의 워크로드를 Azure 백업에 백업할 수 있습니다. 지원되는 전체 목록은 아래의 Azure 백업 지원 항목을 참조하십시오.
- Azure 백업에 저장된 데이터는 “테이프에 복사” 옵션으로 복구할 수 없습니다.
- Azure 백업 기능을 사용할 수 있는 Azure 계정이 필요합니다. 계정이 없는 경우 몇 분 만에 무료 평가판 계정을 만들 수 있습니다. [Azure 백업 가격 정책](https://azure.microsoft.com/pricing/details/backup/)을 읽어보십시오.
- Azure 백업을 사용하려면 백업하고자 하는 서버에 Azure 백업 에이전트를 설치해야 합니다. 각 서버에는 캐시 위치로 최소한 2.5GB의 사용 가능한 로컬 저장소 공간이 있어야 합니다(캐시 위치로 15GB의 사용 가능한 로컬 저장소 공간이 있는 것이 바람직합니다).
- 데이터는 Azure 자격 증명 모음 저장소에 저장됩니다. Azure 백업 자격 증명 모음에 백업할 수 있는 데이터의 양에는 제한이 없지만 데이터 원본(예를 들면 가상 컴퓨터 또는 데이터베이스)의 크기는 54400GB를 초과해서는 안 됩니다.

다음 파일 형식은 Azure에 대한 백업을 지원합니다.

- 암호화(전체 백업에만 해당)
- 압축(증분 백업 지원)
- 스파스(증분 백업 지원)
- 압축 및 스파스(스파스로 처리됨)

다음은 지원하지 않습니다.

- 대/소문자 구분 파일 시스템의 서버는 지원하지 않습니다.
- 하드 링크(건너뜀)
- 재분석 지점(건너뜀)
- 암호화 및 압축(건너뜀)
- 암호화 및 스파스(건너뜀)
- 압축된 스트림
- 스파스 스트림

>[AZURE.NOTE]System Center 2012 DPM SP1부터는 DPM으로 보호되는 워크로드를 Microsoft Azure 백업을 사용하여 Azure에 백업할 수 있습니다.

<!---HONumber=Oct15_HO2-->