<properties
	pageTitle="온-프레미스 VMWare 사이트 간 보호 설정"
	description="이 문서를 사용하여 Azure Site Recovery를 사용하는 두 VMware 사이트 간의 보호를 구성할 수 있습니다."
	services="site-recovery"
	documentationCenter=""
	authors="rayne-wiselman"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="site-recovery"
	ms.workload="backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/03/2015"
	ms.author="raynew"/>


# 온-프레미스 VMWare 사이트 간 보호 설정


## 개요

Azure Site Recovery의 InMage Scout는 온-프레미스 VMWare 사이트 간의 실시간 복제 기능을 제공합니다. InMage Scout는 Azure Site Recovery 서비스 구독에 포함되어 있습니다.


## 필수 조건

- **Azure 계정**—[Microsoft Azure](http://azure.microsoft.com/) 계정이 있어야 합니다. [무료 평가판](pricing/free-trial/)으로 시작할 수 있습니다.


## 1단계: 자격 증명 모음 만들기

1. [관리 포털](https://portal.azure.com)에 로그인합니다.
2. **데이터 서비스** > **복구 서비스** > **사이트 복구 자격 증명 모음**을 클릭합니다.
3. **새로 만들기** > **빠른 생성**을 클릭합니다.
4. **이름**에 자격 증명 모음을 식별하기 위한 이름을 입력합니다.
5. **하위 지역**에서 자격 증명 모음에 대한 지리적 하위 지역을 선택합니다. 지원되는 하위 지역을 확인하려면 [Azure Site Recovery 가격 정보](pricing/details/site-recovery/)에서 지리적 가용성을 참조하세요.

상태 표시줄을 점검하여 자격 증명 모음이 성공적으로 만들어졌는지 확인합니다. 자격 증명 모음은 기본 복구 서비스 페이지에서 **활성**으로 나열됩니다.

## 2단계: 자격 증명 모음을 구성하고 InMage Scout 구성 요소 다운로드

1. **자격 증명 모음 만들기**를 클릭합니다.
2. **복구 서비스** 페이지에서 자격 증명 모음을 클릭하여 빠른 시작 페이지를 엽니다.
3. 드롭다운 목록에서 **두 개의 온-프레미스 VMWare 사이트 간**을 선택합니다.
4. InMage Scout를 다운로드합니다. 모든 필수 구성 요소에 대한 설치 파일은 다운로드한 zip 파일에 있습니다.


## 3단계: 구성 요소 업데이트를 설치합니다.

최신 [업데이트](#updates)에 대해 읽습니다. 다음 순서로 업데이트 파일을 설치합니다.

1. 하나인 경우 RX 서버
2. 서버 구성
3. 프로세스 서버
3. 마스터 대상 서버
4. vContinuum 서버.

다음과 같이 설치합니다.

1. [update](http://aka.ms/scoutupdates) zip 파일을 다운로드합니다. 이 zip 파일에는 다음 파일이 포함됩니다.

	-  RX\_8.0.1.0\_GA\_Update\_1\_3279231\_23Jun15.tar.gz
	-  CX\_Windows\_8.0.2.0\_GA\_Update\_2\_4306954\_21Aug15.exe
	-  UA\_Windows\_8.0.1.0\_GA\_Update\_1\_3259401\_23Jun15.exe
	-  UA\_RHEL6-64\_8.0.1.0\_GA\_Update\_1\_3259401\_23Jun15.tar.gz
	-  vCon\_Windows\_8.0.1.0\_GA\_Update\_1\_3259523\_23Jun15.exe
2. zip 파일의 압축을 풉니다.
2. **RX 서버**: **RX\_8.0.1.0\_GA\_Update\_1\_3279231\_23Jun15.tar.gz**를 RX 서버에 복사하고 압축을 풉니다. 압축을 푼 폴더에서 **/Install**을 실행합니다.
2. **구성 서버/프로세스 서버**: **CX\_Windows\_8.0.2.0\_GA\_Update\_2\_4306954\_21Aug15.exe**를 구성 서버 및 프로세스 서버에 복사합니다. 실행하려면 두 번 클릭합니다.
3. **Windows 마스터 대상 서버**: 통합된 에이전트를 업데이트하려면**UA\_Windows\_8.0.1.0\_GA\_Update\_1\_3259401\_23Jun15.exe**를 마스터 대상 서버에 복사합니다. 실행하려면 두 번 클릭합니다. Windows용으로 통합된 에이전트는 원본 서버에 적용할 수 없습니다. Windows 마스터 대상 서버에만 설치해야 합니다.
4. **Linux 마스터 대상 서버**: 통합된 에이전트를 업데이트하려면**UA\_RHEL6-64\_8.0.1.0\_GA\_Update\_1\_3259401\_23Jun15.tar.gz**를 마스터 대상 서버로 복사하고 압축을 풉니다. 압축을 푼 폴더에서 **/Install**을 실행합니다.
5. **vContinuum 서버**: **vCon\_Windows\_8.0.1.0\_GA\_Update\_1\_3259523\_23Jun15.exe**를 vContinuum 서버에 복사합니다. VContinuum 마법사를 닫았는지 확인합니다. 실행하려면 파일을 두 번 클릭합니다.

## 4단계: 복제 설정
5. 원본과 대상 VMware 사이트 간 복제를 설정합니다.
6. 지침은 제품과 함께 다운로드된 InMage Scout 설명서를 사용합니다. 또는 다음과 같이 설명서에 액세스할 수 있습니다.

	- [릴리스 정보](http://download.microsoft.com/download/4/5/0/45008861-4994-4708-BFCD-867736D5621A/InMage_Scout_Standard_Release_Notes.pdf)
	- [호환성 매트릭스](http://download.microsoft.com/download/C/D/A/CDA1221B-74E4-4CCF-8F77-F785E71423C0/InMage_Scout_Standard_Compatibility_Matrix.pdf)
	- [사용자 가이드](http://download.microsoft.com/download/E/0/8/E08B3BCE-3631-4CED-8E65-E3E7D252D06D/InMage_Scout_Standard_User_Guide_8.0.1.pdf)
	- [RX 사용자 가이드](http://download.microsoft.com/download/A/7/7/A77504C5-D49F-4799-BBC4-4E92158AFBA4/InMage_ScoutCloud_RX_User_Guide_8.0.1.pdf)
	- [빠른 설치 가이드](http://download.microsoft.com/download/6/8/5/685E761C-8493-42EB-854F-FE24B5A6D74B/InMage_Scout_Standard_Quick_Install_Guide.pdf)


## 업데이트

### ASR Scout 8.0.1 업데이트 03Dec15

업데이트 03-Dec-15의 수정에 포함되는 내용:

- **구성 서버** - 사이트 복구에 구성 서버가 등록되었을 때 31일 무료 계량 기능이 예상대로 작동하지 않는 문제를 해결합니다.
- **통합 에이전트** - 버전 8.0에서 8.0.1로 업그레이드할 때 마스터 대상 서버에 업데이트가 설치되지 않는 마스터 대상에 대한 업데이트 1 문제를 해결합니다.

>[AZURE.NOTE]
>
>-	모든 ASR 업데이트는 누적됩니다.
>-	시스템에 적용되면 CS 및 RX 업데이트를 롤백할 수 없습니다.


### ASR Scout 8.0.1 업데이트 1

이 최신 업데이트에는 버그 수정과 새 기능을 포함합니다.

- 서버 인스턴스 당 31일간 무료 보호됩니다. 기능을 테스트 하거나 개념 증명을 설정할 수 있습니다.
	- 장애 조치 및 장애 복구를 포함한 서버의 모든 작업은 Scout ASR을 사용하여 서버를 먼저 보호한 시점부터 시작하여 처음 31일에 대해 무료입니다.
	- 32일째부터는 보호된 각 서버가 사이트를 소유하는 고객에게 Azure Site Recovery 보호를 위해 표준 인스턴스 요금으로 청구됩니다.
	- 언제든지 현재 부과되고 있는 보호된 서버의 수는 Azure Site Recovery 자격 증명의 대시보드 페이지에서 사용할 수 있습니다.
- vCLI 5.5 업데이트 2에 대한 지원이 추가됩니다.
- 원본 서버에서 Linux 운영 체제에 대한 추가 지원:
	- RHEL 6 업데이트 6
	- RHEL 5 업데이트 11
	- CentOS 6 업데이트 6
	- CentOS 5 업데이트 11
- 다음과 같은 문제를 해결하는 버그 수정:
	- 서버 구성 또는 RX 서버에 대한 자격 증명 모음 등록에 실패합니다.
	- 다시 시작하는 동안 가상 컴퓨터가 다시 보호되면 클러스터 볼륨이 예상대로 표시되지 않습니다.
	- 장애 복구는 마스터 대상 서버가 온-프레미스 프로덕션 가상 컴퓨터에서 다른 ESXi 서버에서 호스트 되는 경우 실패합니다.
	- 구성 파일 사용 권한은 8.0.1로 업그레이드 중 변경되어 보호 및 작업에 영향을 줍니다.
	- 다시 동기화 임계값은 예상대로 적용되지 않으며, 일치하지 않는 복제 동작을 수행합니다.
	- 구성 서버 인터페이스에 제대로 나타나지 않는 RPO 설정입니다. 압축되지 않은 데이터 값은 압축된 값을 잘못 표시합니다.
	-  제거 작업은 vContinuum 마법사에서 예상대로 삭제되지 않으며 복제는 구성 서버 인터페이스에서 삭제되지 않습니다.
	-  vContinuum 마법사에서 MSCS 가상 컴퓨터를 보호하는 동안 디스크 보기에서 **세부 정보**를 클릭하면 디스크는 자동으로 선택되지 않습니다.
	- P2V 시나리오가 CIMnotify와 같은 HP 서비스에 필요한 동안, CqMgHost는 복구 가상 컴퓨터에서 수동으로 이동되지 않으므로 추가 부팅 시간이 필요합니다.
	- 마스터 대상 서버에 26개 이상의 디스크가 있으면 Linux 가상 컴퓨터 보호 작업이 실패합니다.

## 다음 단계

문의 사항은 [Azure 복구 서비스 포럼](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)에 게시하세요.

<!---HONumber=AcomDC_1210_2015-->