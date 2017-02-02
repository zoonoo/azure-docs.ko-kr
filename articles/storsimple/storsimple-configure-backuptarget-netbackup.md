---
title: "Veritas NetBackup을 사용하여 StorSimple 구성 | Microsoft Docs"
description: "Veritas NetBackup을 사용하는 StorSimple 백업 대상 구성을 설명합니다."
services: storsimple
documentationcenter: 
author: hkanna
manager: matd
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/05/2016
ms.author: hkanna
translationtype: Human Translation
ms.sourcegitcommit: 45fc96674d52a1e1273eed390195c59889b86a38
ms.openlocfilehash: 50c4be545f3b852d99968548784ffdff048d5355

---

# <a name="configure-storsimple-with-veritas-netbackup8482"></a>Veritas NetBackup을 사용하여 StorSimple 구성

## <a name="overview"></a>개요

Microsoft Azure StorSimple은 온-프레미스 솔루션의 확장인 Azure Storage 계정을 사용하고 온-프레미스 저장소 및 클라우드 저장소에 데이터를 자동으로 계층화하여 지수 데이터 증가의 복잡성을 해결하는 하이브리드 클라우드 저장소 솔루션입니다.

이 문서에서는 Veritas NetBackup을 사용한 StorSimple 통합 및 두 솔루션을 모두 통합하는 모범 사례를 설명합니다. 또한 StorSimple과 함께 통합하기 위해 Veritas NetBackup을 구성하는 방법에 대한 권장 사항을 제공합니다. Veritas NetBackup을 구성하여 백업 요구 사항 및 SLA를 충족하는 방법에 대한 Veritas 모범 사례, 백업 설계자 및 관리자 권한을 따릅니다.


이 문서에서는 구성 단계와 주요 개념을 보여 줍니다. 하지만 단계별 가이드를 구성 또는 설치 가이드는 아닙니다. 문서에서는 기본 구성 요소 및 인프라가 작업 순서로 배치되고 설명하는 개념을 지원할 준비가 되었다고 가정합니다.

## <a name="why-storsimple-as-a-backup-target"></a>StorSimple이 백업 대상인 이유는 무엇인가요?

StorSimple은 다음과 같은 이유로 훌륭한 백업 대상입니다.

-   빠른 백업 대상을 제공하기 위해 변경 없이 사용할 백업 응용 프로그램에 대한 표준 로컬 저장소를 제공합니다. StorSimple은 최근 백업의 빠른 복원을 위해 사용할 수도 있습니다.

-   해당 클라우드 계층은 클라우드 저장소 계정과 원활하게 통합되어 비용 효율적인 Microsoft Azure Storage를 사용합니다.

-   재해 복구를 위해 오프사이트 저장소를 자동으로 제공합니다.


## <a name="target-audience"></a>대상 사용자

이 문서의 사용자는 저장소, Windows Server 2012 R2, 이더넷, 클라우드 서비스 및 NetBackup의 기술을 사용하는 대상 백업 관리자, 저장소 관리자 및 저장소 설계자를 포함합니다.

## <a name="supported-versions"></a>지원되는 버전

-   NetBackup 7.7.x 이상

-   [StorSimple 업데이트 3 이상](storsimple-overview.md#storsimple-workload-summary)

## <a name="key-concepts"></a>주요 개념

다른 모든 저장소 솔루션을 성공적으로 사용하려면 솔루션의 저장소 성능, SLA, 변경 비율 및 용량 증가 요구 사항을 신중하게 평가하는 것이 중요합니다. 기본 아이디어는 해당 작업을 수행하기 위해 클라우드 계층, 액세스 시간 및 처리량을 클라우드에 도입하여 StorSimple의 기능에서 중심적인 역할을 수행하는 것입니다.

StorSimple은 잘 정의된 데이터(핫 데이터)의 작업 집합에서 작동하는 응용 프로그램에 대한 저장소를 제공하도록 설계되었습니다. 이 모델에서 데이터의 작업 집합은 로컬 계층에 저장되고 데이터의 나머지 비작업/콜드/보관 집합은 클라우드로 계층화됩니다. 다음 그림에서 이 모델을 나타냅니다. 평편한 녹색선은 StorSimple 장치의 로컬 계층에 저장된 데이터를 나타냅니다. 빨강선은 모든 계층에서 StorSimple 솔루션에 저장된 데이터의 총량을 나타냅니다. 평편한 녹색선과 빨강색 지수 곡선 사이의 간격은 클라우드에 저장된 데이터의 총량을 나타냅니다.

**StorSimple 계층**
![Storsimple 계층 다이어그램](./media/storsimple-configure-backup-target-using-netbackup/image1.jpg)


이 아키텍처를 염두에 두면 StorSimple이 백업 대상으로 작동하는 데 적합하다는 점을 알 수 있습니다. StorSimple을 통해 다음을 수행할 수 있습니다.

-   데이터의 로컬 작업 집합에서 복원을 가장 자주 수행합니다.

-   복원이 자주 수행되지 않는 오프사이트 재해 복구 및 오래된 데이터에 대해 클라우드를 사용합니다.

## <a name="storsimple-benefits"></a>StorSimple 이점

StorSimple은 온-프레미스 및 클라우드 저장소에 대한 원활한 액세스를 활용하여 Microsoft Azure와 원활하게 통합된 온-프레미스 솔루션을 제공합니다.

StorSimple은 온-프레미스 장치(SSD(반도체 장치) 및 SAS(일련 번호 연결 SCSI) 저장소 포함)와 Azure Storage 간에 자동 계층을 사용합니다. 자동 계층은 SSD 및 SAS 계층에서 자주 액세스되는 데이터를 로컬로 유지하고 Azure Storage에 자주 액세스되지 않는 데이터를 이동시킵니다.

StorSimple는 다음과 같은 이점을 제공합니다.

-   전례 없는 중복 제거 수준을 달성하기 위해 클라우드를 사용하는 고유한 중복 제거 및 압축 알고리즘

-   고가용성

-   Azure 지역에서 복제를 사용하여 지역에서 복제

-   Azure 통합

-   클라우드의 데이터 암호화

-   향상된 재해 복구 및 규정 준수

StorSimple에서 기본 및 보조 백업 대상이라는 두 가지 기본 배포 시나리오를 제공하더라도 기본적으로는 일반 블록 저장소 장치임을 기억하세요. StorSimple는 모든 압축 및 중복 제거를 수행하고 클라우드의 데이터를 응용 프로그램 및 파일 시스템 모두에 대해 원활하게 검색합니다.

StorSimple에 대한 자세한 내용은 [StorSimple 8000 시리즈: 하이브리드 클라우드 저장소 솔루션](storsimple-overview.md)을 참조하고 [기술 StorSimple 8000 시리즈 사양](storsimple-technical-specifications-and-compliance.md)을 검토합니다.


> [!IMPORTANT] 
> 백업 대상인 StorSimple 장치는 StorSimple 8000 업데이트 3 이상에서만 지원됩니다.


## <a name="architecture-overview"></a>아키텍처 개요

다음 테이블은 어플라이언스 모델-아키텍처 초기 지침을 포함합니다.

#### <a name="storsimple-capacities-for-local-and-cloud-storage"></a>로컬 및 클라우드 저장소의 StorSimple 용량


| 저장소 용량       | 8100          | 8600            |
|------------------------|---------------|-----------------|
| 로컬 저장소 용량 | &lt; 10TiB\*  | &lt; 20TiB\*  |
| 클라우드 저장소 용량 | &gt; 200TiB\* | &gt; 500TiB\* |

\*저장소 크기는 중복 제거 또는 압축을 사용한다고 가정하지 않습니다.

#### <a name="storsimple-capacities-for-primary-and-secondary-backups"></a>기본 및 보조 백업의 StorSimple 용량


| 백업 시나리오  | 로컬 저장소 용량                                         | 클라우드 저장소 용량                      |
|------------------|----------------------------------------------------------------|---------------------------------------------|
| 기본 백업   | 빠른 복구(RPO)를 위해 로컬 저장소에 저장된 최근 백업 | 클라우드 용량에 적합한 백업 기록(RPO) |
| 보조 백업 | 클라우드 용량에 백업 데이터의 보조 복사본을 저장할 수 있습니다.  |

## <a name="storsimple-as-a-primary-backup-target"></a>기본 백업 대상인 StorSimple

이 시나리오에서 StorSimple 볼륨은 백업에 대한 유일한 리포지토리인 백업 응용 프로그램에 표시됩니다. 다음 그림에서는 모든 백업이 백업 및 복원 모두에 대해 StorSimple 계층화된 볼륨을 사용하는 솔루션 아키텍처를 보여 줍니다.

![기본 백업 대상 논리 다이어그램인 StorSimple](./media/storsimple-configure-backup-target-using-netbackup/primarybackuptargetlogicaldiagram.png)

### <a name="primary-target-backup-logical-steps"></a>기본 대상 백업 논리 단계

1.  백업 서버는 대상 백업 에이전트를 연결하고 백업 에이전트는 데이터를 백업 서버에 전송합니다.

2.  백업 서버는 데이터를 StorSimple 계층화된 볼륨에 작성합니다.

3.  백업 서버는 카탈로그 데이터베이스를 업데이트하고 백업 작업을 완료합니다.

4.  스냅숏 스크립트는 StorSimple 클라우드 스냅숏 관리를 트리거합니다(start-delete).

5.  보존 정책에 따라 백업 서버는 만료된 백업을 삭제합니다.

###  <a name="primary-target-restore-logical-steps"></a>기본 대상 복원 논리 단계

1.  백업 서버는 저장소 리포지토리에서 적절한 데이터를 복원하기 시작합니다.

2.  백업 에이전트는 백업 서버에서 데이터를 수신합니다.

3.  백업 서버는 복원 작업을 완료합니다.

## <a name="storsimple-as-a-secondary-backup-target"></a>보조 백업 대상인 StorSimple

이 시나리오에서 StorSimple 볼륨은 주로 장기 보존 또는 보관에 사용됩니다.

다음 그림에서는 초기 백업 및 복원이 고성능 볼륨을 대상으로 하는 아키텍처를 보여 줍니다. 이러한 백업을 복사하고 지정된 일정의 StorSimple 계층화된 볼륨에 보관합니다.

충분한 공간 및 성능을 가진 고성능 볼륨의 크기를 조절하여 보존 정책, 용량 및 성능 요구 사항을 처리하는 것이 중요합니다.

![보조 백업 대상 논리 다이어그램인 StorSimple](./media/storsimple-configure-backup-target-using-netbackup/secondarybackuptargetlogicaldiagram.png)

### <a name="secondary-target-backup-logical-steps"></a>보조 대상 백업 논리 단계

1.  백업 서버는 대상 백업 에이전트를 연결하고 백업 에이전트는 데이터를 백업 서버에 전송합니다.

2.  백업 서버는 고성능 저장소에 데이터를 작성합니다.

3.  백업 서버는 카탈로그 데이터베이스를 업데이트하고 백업 작업을 완료합니다.

4.  보존 정책에 따라 백업 서버는 StorSimple에 백업을 복사합니다.

5.  스냅숏 스크립트는 StorSimple 클라우드 스냅숏 관리를 트리거합니다(start-delete).

6.  보존 정책에 따라 백업 서버는 만료된 백업을 삭제합니다.

### <a name="secondary-target-restore-logical-steps"></a>보조 대상 복원 논리 단계

1.  백업 서버는 저장소 리포지토리에서 적절한 데이터를 복원하기 시작합니다.

2.  백업 에이전트는 백업 서버에서 데이터를 수신합니다.

3.  백업 서버는 복원 작업을 완료합니다.

## <a name="deploy-the-solution"></a>솔루션 배포

이 솔루션의 배포는 네트워크 인프라 준비, 백업 대상으로 StorSimple 장치 배포, 마지막으로 Veritas NetBackup 배포와 같은 세 단계로 구성됩니다. 이러한 각 단계는 다음 섹션에서 자세히 설명합니다.

### <a name="configure-the-network"></a>네트워크 구성

Azure 클라우드에서 통합 솔루션인 StorSimple에는 Azure 클라우드에 대한 활성 작업 연결이 필요합니다. 이 연결은 클라우드 스냅숏, 관리, 메타데이터 전송과 같은 작업에서 오래되고 액세스 빈도가 적은 데이터를 Azure 클라우드 저장소에 계층화하는 데 사용됩니다.

솔루션을 최적으로 수행하려면 다음과 같은 네트워킹 모범 사례를 따르는 것이 좋습니다.

-   StorSimple 계층을 Azure에 연결하는 링크는 적절한 QoS(서비스의 품질)를 인프라 스위치에 적용하여 RPO/RTO SLA와 일치하도록 대역폭 요구 사항을 충족해야 합니다.

-   Azure Blob Storage 액세스 대기 시간은 최대 80ms 범위에 있어야 합니다.

### <a name="deploy-storsimple"></a>StorSimple 배포

단계별 StorSimple 배포 가이드의 경우 [온-프레미스 StorSimple 장치 배포](storsimple-deployment-walkthrough-u2.md)로 이동합니다.

### <a name="deploy-netbackup"></a>NetBackup 배포

단계별 NetBackup 7.7.x 배포 가이드는 [NetBackup 7.7.x 설명서](https://www.veritas.com/support/article.000094423)에서 찾을 수 있습니다

## <a name="configure-the-solution"></a>솔루션 구성

이 섹션에서는 몇 가지 구성 예를 보여 줍니다. 다음 예제/권장 사항에서는 가장 기본적이고 기본 구현을 보여 줍니다. 이 구현은 특정 백업 요구 사항에 직접 적용되지 않을 수 있습니다.

### <a name="configure-storsimple"></a>StorSimple 구성

| StorSimple 배포 작업                                                                                                                 | 추가 설명                                                                                                                                                                                                                                                                                      |
|---------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 온-프레미스 StorSimple 장치 배포                                                                                                 | 지원되는 버전: 업데이트 3 이상                                                                                                                                                                                                                                                                 |
| 백업 대상 모드 사용                                                                                                                   | 다음 명령을 사용하여 설정/해제 및 상태를 가져옵니다. 자세한 내용은 [StorSimple에 원격으로 연결](storsimple-remote-connect.md)로 이동합니다.</br> 백업 모드 사용:`Set-HCSBackupApplianceMode -enable`</br>  백업 모드 사용 안 함:`Set-HCSBackupApplianceMode -disable`</br> 백업 모드 설정의 현재 상태`Get-HCSBackupApplianceMode` |
| 백업 데이터를 저장하는 볼륨에 대한 일반적인 볼륨 컨테이너를 만듭니다.   볼륨 컨테이너에 있는 모든 데이터의 중복을 제거합니다. | StorSimple 볼륨 컨테이너는 중복 제거 도메인을 정의합니다.                                                                                                                                                                                                                                             |
| StorSimple 볼륨 만들기                                                                                                                 | 볼륨 크기가 클라우드 스냅숏 기간에 영향을 주기 때문에 가능한 예상되는 사용률에 가까운 크기의 볼륨을 만듭니다. 볼륨의 크기를 조정하는 방법에 대한 자세한 내용은 [보존 정책](#retention-policies)으로 이동합니다.</br> </br> StorSimple 계층화된 볼륨을 사용하고 **자주 액세스하지 않는 보관 데이터에 이 볼륨 사용** 확인란을 선택합니다. </br> 로컬로 고정된 볼륨만 해당은 지원되지 않습니다.|
| 모든 백업 대상 볼륨에 대한 고유한 StorSimple 백업 정책을 만듭니다.                                                               | StorSimple 백업 정책은 볼륨 일관성 그룹을 정의합니다.                                                                                                                                                                                                                                       |
| 일정을 스냅숏으로 사용하지 않도록 설정합니다.                                                                                                    | 스냅숏은 후처리 작업으로 트리거됩니다.                                                                                                                                                                                                                                                         |
|                                                                                                     |                                             |



### <a name="configure-host-backup-server-storage"></a>호스트 서버 백업 저장소 구성

다음 지침에 따라 호스트 백업 서버 저장소를 구성했는지 확인합니다.  

- Windows 디스크 관리자에서 만든 스팬 볼륨을 지원하지 않습니다.
- 64KB 할당 단위 크기인 NTFS를 사용하여 볼륨을 포맷합니다.
- NetBackup 서버에 직접 StorSimple 볼륨을 매핑합니다. 
    - 물리적 서버에 대한 iSCSI를 사용합니다.
    - 가상 서버에 대한 통과 디스크를 사용합니다.


## <a name="best-practices-for-storsimple-and-netbackup"></a>StorSimple 및 NetBackup에 대한 모범 사례

다음 지침에 따라 솔루션을 구성합니다.

### <a name="operating-system"></a>운영 체제

-   NTFS 파일 시스템에 대한 Windows Server 암호화 및 중복 제거를 사용하지 않도록 설정합니다.

-   StorSimple 볼륨에 Windows Server 조각 모음을 사용하지 않도록 설정합니다.

-   StorSimple 볼륨에 Windows Server 인덱싱을 사용하지 않도록 설정합니다.

-   StorSimple 볼륨에 대해서가 아니라 원본 호스트에서 바이러스 검사를 수행합니다.

-   작업 관리자에서 기본 [Windows Server 유지 관리](https://msdn.microsoft.com/library/windows/desktop/hh848037.aspx)를 사용하지 않도록 설정합니다.

    - Windows Task Scheduler에서 유지 관리 구성 도구를 사용하지 않도록 설정합니다.

        또는

    - 다운로드: [PSEXEC – Microsoft Sysinternals](https://technet.microsoft.com/sysinternals/bb897553.aspx)

      - PSEXEC를 다운로드한 후에 관리자 권한으로 Windows PowerShell을 실행하고 다음을 입력합니다.

            `psexec \\%computername% -s schtasks /change /tn “MicrosoftWindowsTaskSchedulerMaintenance Configurator" /disable`

### <a name="storsimple"></a>StorSimple

-   StorSimple 장치가 [업데이트 3 이상](storsimple-install-update-3.md)으로 업데이트되도록 합니다.

-   iSCSI 및 클라우드 트래픽을 격리합니다. StorSimple과 백업 서버 간의 트래픽에 대한 전용 iSCSI 연결을 사용합니다.

-   StorSimple 장치가 전용 백업 대상인지 확인합니다. 이러한 워크로드가 RTO/RPO에 영향을 주므로 혼합 워크로드는 지원되지 않습니다.

### <a name="netbackup"></a>NetBackup

-   NetBackup 데이터베이스는 서버에 대해 로컬이어야 하고 StorSimple 볼륨에 상주하지 않아야 합니다.

-   DR의 경우 StorSimple 볼륨에 NetBackup 데이터베이스를 백업합니다.

-   이 솔루션에 대한 NetBackup 전체 백업과 증분 백업을 지원합니다. 가상 및 차등 백업을 사용하지 않는 것이 좋습니다.

-   백업 데이터 파일에는 특정 작업에 대한 데이터만 있어야 합니다. 예를 들어 다른 작업에 미디어 추가가 허용되지 않습니다.

및 이러한 요구 사항을 구현하는 방법에 대한 최신 NetBackup 설정 모범 사례에 대한 자세한 내용은 [NetBackup의 설명서](https://www.veritas.com)를 참조하세요.


## <a name="retention-policies"></a>보존 정책

자주 사용하는 백업 보존 정책 중 하나는 GFS(Grandfather, Father, and Son)입니다. 이 정책에서 증분 백업은 매일 수행됩니다. 매주 및 매월 전체 백업이 수행됩니다. 이 정책은 6개의 StorSimple 계층화된 볼륨을 발생시킵니다.

-   하나의 볼륨에는 매주, 매월 및 매년 전체 백업이 포함됩니다.

-   다른 5개의 볼륨은 매일 증분 백업을 저장합니다.

다음 예제에서는 GFS 회전에 대해 알아봅니다. 이 예제에서는 다음을 가정합니다.

-   중복 제거되지 않거나 압축되지 않은 데이터를 사용합니다.

-   전체 백업은 각각 1TiB입니다.

-   매일 증분 백업은 각각 500GiB입니다.

-   4개의 매주 백업이 한 달 동안 보관됩니다.

-   12개의 매월 백업이 1년 동안 보관됩니다.

-   1개의 매년 백업이 10년 동안 보관됩니다.

앞의 가정을 기반으로 매월 및 매년 전체 백업에 대한 26TiB의 StorSimple 계층화된 볼륨을 만듭니다. 매일 증분 백업 각각에 대한 5TiB의 StorSimple 계층화된 볼륨을 만듭니다.

| 백업 유형 보존 | 크기 TiB | GFS 승수\*                                       | 총 용량 TiB          |
|-----------------------|----------|--------------------------------------------------------|-----------------------------|
| 매주 전체           | 1        | 4                                                      | 4                           |
| 매일 증분     | 0.5      | 20(주기는 월별 주 수와 동일함) | 12(추가 할당량의 경우 2) |
| 매월 전체          | 1        | 12                                                     | 12                          |
| 매년 전체           | 1        | 10                                                     | 10                          |
| GFS 요구 사항       |          |                                                        | 38                          |
| 추가 할당량      | 4        |                                                        | 42개의 총 GFS 요구 사항   |

\*GFS 승수는 백업 정책을 충족하기 위해 보호하고 유지해야 하는 복사본의 수입니다.

## <a name="configure-netbackup-storage"></a>NetBackup 저장소 구성


1.  NetBackup 관리 콘솔에서 [장치]를 선택하고\> [디스크 풀 구성 마법사]를 시작하고\> [AdvancedDisk]를\> 선택하고\> [다음]을 클릭합니다.

    ![NetBackup 관리 콘솔 디스크 풀 구성](./media/storsimple-configure-backup-target-using-netbackup/nbimage1.png)

1.  서버를 선택합니다.

    ![NetBackup 관리 콘솔, 서버 선택](./media/storsimple-configure-backup-target-using-netbackup/nbimage2.png)

1.  다음 및 StorSimple 볼륨 선택

    ![NetBackup 관리 콘솔, StorSimple 디스크 선택](./media/storsimple-configure-backup-target-using-netbackup/nbimage3.png)

1.  그런 다음 이름을 지정하고 마침 및 옆에 있는 다음을 클릭합니다.

    ![NetBackup 관리 콘솔, 이름 및 설명 화면](./media/storsimple-configure-backup-target-using-netbackup/nbimage4.png)

    설정을 검토한 후에 [마침]을 클릭합니다.

    1.  각 할당의 끝에서 모범 사례 목록에 있는 해당 권장 사항과 일치하도록 저장소 장치 설정을 변경합니다.

    2.  StorSimple 볼륨 할당을 마칠 때까지 1-4단계를 반복합니다.

    ![NetBackup 관리 콘솔 디스크 구성](./media/storsimple-configure-backup-target-using-netbackup/nbimage5.png)

## <a name="storsimple-as-a-primary-backup-target"></a>기본 백업 대상인 StorSimple

> [!NOTE]
> 클라우드에 계층화된 백업의 데이터를 복원해야 하는 경우 클라우드 속도로 복원이 실행된다는 점에 유의하세요.

다음 그림에서는 백업 작업에 대한 일반적인 볼륨의 매핑을 설명합니다. 이 경우에 모든 주간 백업이 토요일 전체 디스크에 매핑되고 증분 백업은 월요일-금요일 증분 디스크에 매핑됩니다. StorSimple 계층화된 볼륨에서 모든 백업 및 복원이 수행됩니다.

![기본 백업 대상 구성 논리 다이어그램 ](./media/storsimple-configure-backup-target-using-netbackup/primarybackuptargetdiagram.png)

#### <a name="storsimple-as-a-primary-backup-target-grandfather-father-and-son-gfs-schedule-example"></a>기본 백업 대상 GFS(Grandfather, Father, and Son) 일정 예제인 StorSimple

| 4주 동안 GFS 회전 일정, 매월 및 매년 |               |             |
|--------------------------------------------------------------------------|---------------|-------------|
| 빈도/백업 유형   | 전체          | 증분(1-5일차)  |
| 매주(1-4주차)    | 토요일 | 월요일-금요일 |
| 매월     | 토요일  |             |
| 매년      | 토요일  |             |


### <a name="assigning-storsimple-volumes-to-a-netbackup-backup-job"></a>NetBackup 백업 작업에 StorSimple 볼륨 할당

다음 시퀀스에서는 대상 호스트인 NetBackup이 NetBackup 에이전트 지침에 따라 구성되어 있다고 가정합니다.

1.  NetBackup 관리 콘솔에서 [정책\>]을 선택하고 [\>새 정책]을 마우스 오른쪽 단추로 클릭합니다.

    ![NetBackup 관리 콘솔 화면](./media/storsimple-configure-backup-target-using-netbackup/nbimage6.png)

1.  정책 이름 선택 및 정책 마법사 사용

    ![NetBackup 관리 콘솔, 새 정책](./media/storsimple-configure-backup-target-using-netbackup/nbimage7.png)

1.  이 경우에 적절한 백업 유형, [파일 시스템] 및 [다음]을 차례로 선택합니다.

    ![NetBackup 관리 콘솔, 새 정책](./media/storsimple-configure-backup-target-using-netbackup/nbimage8.png)

1.  그런 다음 표준을 선택합니다.

    ![NetBackup 관리 콘솔, 새 정책](./media/storsimple-configure-backup-target-using-netbackup/nbimage9.png)

1.  호스트를 선택하고 클라이언트 운영 체제 탐지 및 추가 확인란을 선택합니다.

    ![NetBackup 관리 콘솔, 새 정책](./media/storsimple-configure-backup-target-using-netbackup/nbimage10.png)

1.  그리고 [다음]

2.  그런 다음 이 경우에 G:\\DataChange3에서 백업해야 하는 드라이브를 선택합니다\\

    ![NetBackup 관리 콘솔, 새 정책](./media/storsimple-configure-backup-target-using-netbackup/nbimage11.png)

1.  요구 사항을 충족하는 회전을 선택합니다.

    ![NetBackup 관리 콘솔, 새 정책](./media/storsimple-configure-backup-target-using-netbackup/nbimage12.png)

1.  그리고 다음 및 다음을 수행하고 마칩니다. 정책을 만든 후에 일정을 수정합니다.

2.  만든 정책을 확장하고 일정을 선택합니다.

    ![NetBackup 관리 콘솔, 새 정책](./media/storsimple-configure-backup-target-using-netbackup/nbimage13.png)

1.  differential-inc를 마우스 오른쪽 단추로 클릭하고 이 경우에 Mon-inc에서 복사를 선택하고 확인을 클릭합니다

    ![NetBackup 관리 콘솔, 새 정책 일정](./media/storsimple-configure-backup-target-using-netbackup/nbimage14.png)

1.  그런 다음 새로 만든 일정을 마우스 오른쪽 단추로 클릭하고 변경을 선택합니다.

2.  [속성] 탭에서 [정책 저장소 재정의]를 선택하고 월요일 증분 백업을 배치할 볼륨을 선택합니다. 이 경우에 SS1

    ![NetBackup 관리 콘솔, 일정 변경](./media/storsimple-configure-backup-target-using-netbackup/nbimage15.png)

1.  시작 창에서 백업을 위한 창을 선택합니다. 이 경우에 월요일 오후 8시에서 오후 9시까지입니다.

    ![NetBackup 관리 콘솔, 일정 변경](./media/storsimple-configure-backup-target-using-netbackup/nbimage16.png)

1.  확인을 선택합니다.

2.  증분 백업의 각각에 대해 11~15단계를 반복하고 적절한 볼륨 및 일정을 선택합니다.

3.  Differential-inc 일정을 마우스 오른쪽 단추로 클릭하고 삭제합니다.

4.  Differential-inc 일정을 삭제한 후에 요구 사항에 맞게 전체 일정을 수정합니다.

    ![NetBackup 관리 콘솔, 일정 변경](./media/storsimple-configure-backup-target-using-netbackup/nbimage17.png)

1.  이 경우에 시작 창을 토요일 오전 6시로 변경

    ![NetBackup 관리 콘솔, 일정 변경](./media/storsimple-configure-backup-target-using-netbackup/nbimage18.png)

1.  최종 일정은 다음과 같습니다.

    ![NetBackup 관리 콘솔, 일정 변경](./media/storsimple-configure-backup-target-using-netbackup/nbimage19.png)

## <a name="storsimple-as-a-secondary-backup-target"></a>보조 백업 대상인 StorSimple

> [!NOTE]
> 클라우드에 계층화된 백업의 데이터를 복원해야 하는 경우 클라우드 속도로 복원이 실행된다는 점에 유의하세요.

이 모델에서 임시 캐시의 역할을 하는 StorSimple 이외의 저장소 미디어가 있어야 합니다. 예를 들어 RAID 볼륨을 사용하여 공간, IO 및 대역폭을 수용할 수 있습니다. RAID 5, 50 및 10을 사용하는 것이 좋습니다.

다음 그림에서는 서버에 대한 일반적인 단기 보존 로컬 볼륨 및 장기 보존 보관 볼륨에 대해 설명합니다. 이 경우에 서버에 대한 로컬 RAID 볼륨에서 모든 백업을 실행합니다. 이러한 백업이 정기적으로 복제되고 보관 볼륨에 보관됩니다. 서버에 대한 로컬 RAID 볼륨 크기를 조정하여 단기 보존 용량 및 성능 요구 사항을 처리하는 것이 중요합니다.

#### <a name="storsimple-as-a-secondary-backup-target-gfs-example"></a>보조 백업 대상 GFS 예제인 StorSimple

![보조 백업 대상 논리 다이어그램인 StorSimple ](./media/storsimple-configure-backup-target-using-netbackup/secondarybackuptargetdiagram.png)

다음 테이블에서는 백업이 개별 및 총 용량 요구 사항을 포함하여 로컬 및 StorSimple 디스크에서 실행되도록 구성해야 하는 방법을 보여 줍니다.

#### <a name="backup-configuration-and-capacity-requirements"></a>백업 구성 및 용량 요구 사항

| 백업 유형 및 보존                    |구성된 저장소| 크기(TiB) | GFS 승수 | 총 용량 (TiB)        |
|----------------------------------------------|-----|----------|----------------|------------------------|
| 1주차(전체 및 증분) |로컬 디스크(단기)| 1        | 1              | 1           |
| StorSimple 2-4주차           |StorSimple 디스크(장기) | 1        | 4              | 4                   |
| 매월 전체                                 |StorSimple 디스크(장기) | 1        | 12             | 12                   |
| 매년 전체                               |StorSimple 디스크(장기) | 1        | 1              | 1                   |
|GFS 볼륨 크기 요구 사항 | |          |                | 18*|

\*총 용량에는 17TiB의 StorSimple 디스크 및 1TiB 로컬 RAID 볼륨이 포함되어 있습니다.


#### <a name="gfs-example-schedule"></a>GFS 예제 일정

|GFS 회전 매주, 매월 및 매년 일정|                    |                   |                   |                   |                   |                   |
|--------------------------------------------------------------------------|--------------------|-------------------|-------------------|-------------------|-------------------|-------------------|
| 주                                                                     | 전체               | 증분 1일차        | 증분 2일차        | 증분 3일차        | 증분 4일차        | 증분 5일차        |
| 1주차                                                                   | 로컬 RAID 볼륨  | 로컬 RAID 볼륨 | 로컬 RAID 볼륨 | 로컬 RAID 볼륨 | 로컬 RAID 볼륨 | 로컬 RAID 볼륨 |
| 2주차                                                                   | StorSimple 2-4주차 |                   |                   |                   |                   |                   |
| 3주차                                                                   | StorSimple 2-4주차 |                   |                   |                   |                   |                   |
| 4주차                                                                   | StorSimple 2-4주차 |                   |                   |                   |                   |                   |
| 매월                                                                  | StorSimple 매월 |                   |                   |                   |                   |                   |
| 매년                                                                   | StorSimple 매년  |                   |                   |                   |                   |                   |


### <a name="assign-storsimple-volumes-to-netbackup-archiveduplication-job"></a>NetBackup 보관/복제 작업에 StorSimple 볼륨을 할당합니다.

NetBackup에서 제공하는 저장소 및 미디어 관리를 위해 다양한 옵션을 통해 Veritas 또는 NetBackup 설계자와 상의하여 SLP 요구 사항을 제대로 평가합니다.

1.  초기 디스크 풀을 정의하면 계속 진행하여 3개의 저장소 수명 주기 정책을 정의합니다.

    1.  LocalRAIDVolume

    2.  StorSimpleWeek2-4

    3.  StorSimpleMonthlyFulls

    4.  StorSimpleYearlyFulls

    [저장소]의 관리 콘솔에서 [저장소 수명 주기 정책]을 선택하고 [새 저장소 수명 주기 정책]을 선택합니다.

    [NetBackup 관리 콘솔, 저장소 수명 주기 정책](./media/storsimple-configure-backup-target-using-netbackup/nbimage20.png)

1.  이름을 선택하고 추가를 클릭합니다.

    ![NetBackup 관리 콘솔, 저장소 수명 주기 정책](./media/storsimple-configure-backup-target-using-netbackup/nbimage21.png)

1.  속성 탭에서 [백업], [대상 저장소], 적절한 보존 및 확인을 선택합니다. 이 경우에는 LocalRAIDVolume 및 일주일 보존입니다.

    ![NetBackup 관리 콘솔, 저장소 수명 주기 정책](./media/storsimple-configure-backup-target-using-netbackup/nbimage22.png)

1.  첫 번째 백업 작업/리포지토리를 정의했습니다.

2.  이전 작업을 강조 표시하는 동안 [추가]를 클릭하고 [대상 저장소] 및 적절한 보존을 선택합니다. LocalRAIDVolume에서 StorSimpleWeek2-4까지인 경우에는 1개월 보존입니다.

    ![NetBackup 관리 콘솔, 새 저장소 수명 주기 정책](./media/storsimple-configure-backup-target-using-netbackup/nbimage23.png)

1.  이전 작업을 강조 표시하는 동안 [추가]를 선택합니다. 이제 일년 동안 매월 백업을 추가합니다.

    ![NetBackup 관리 콘솔, 저장소 수명 주기 정책 변경](./media/storsimple-configure-backup-target-using-netbackup/nbimage24.png)

1.  적절한 SLP 보존 정책을 보유할 때까지 5~6단계를 반복합니다.

    ![NetBackup 관리 콘솔, 저장소 수명 주기 정책](./media/storsimple-configure-backup-target-using-netbackup/nbimage25.png)

1.  적절한 SLP 보존 정책을 정의하게 되면 기본 대상 섹션인 StorSimple에서 설명한 대로 [정책]에서 백업 정책을 정의합니다.

1.  [일정]에서 [전체]를 선택하고 마우스 오른쪽 단추로 클릭하고 [변경]을 선택합니다.

    ![NetBackup 관리 콘솔, 일정 변경](./media/storsimple-configure-backup-target-using-netbackup/nbimage26.png)

1.  [정책 저장소 덮어쓰기] 선택을 선택하고 1~8단계에서 만든 SLP를 선택합니다.

    ![NetBackup 관리 콘솔, 일정 변경](./media/storsimple-configure-backup-target-using-netbackup/nbimage27.png)

1.  확인을 클릭한 다음 [증분 백업 일정]을 반복합니다.

    ![NetBackup 관리 콘솔, 일정 변경](./media/storsimple-configure-backup-target-using-netbackup/nbimage28.png)

1.  확인을 클릭한 다음 [증분 백업 일정]을 반복합니다.

| 백업 유형 보존 | 크기 TiB | GFS 승수\*                                       | 총 용량 TiB          |
|-----------------------|----------|--------------------------------------------------------|-----------------------------|
| 매주 전체           | 1        | 4                                                      | 4                           |
| 매일 증분     | 0.5      | 20 "주기는 월별 주 수와 동일함" | 12(추가 할당량의 경우 2) |
| 매월 전체          | 1        | 12                                                     | 12                          |
| 매년 전체           | 1        | 10                                                     | 10                          |
| GFS 요구 사항       |          |                                                        | 38                          |
| 추가 할당량      | 4        |                                                        | 42개의 총 GFS 요구 사항입니다.   |
\*GFS 승수는 백업 정책을 충족하기 위해 보호하고 유지해야 하는 복사본의 수입니다.

### <a name="storsimple-cloud-snapshots"></a>StorSimple 클라우드 스냅숏

StorSimple 클라우드 스냅숏은 StorSimple 장치에 있는 데이터를 보호합니다. 테이프를 오프사이드 시설로 전달하는 것과 같고 Azure GRS(지역 중복 저장소)를 사용하는 경우 여러 사이트에 테이프를 배송하는 것과 같습니다. 장치 복원이 재해에 필요한 경우 다른 StorSimple 장치를 온라인 상태로 전환하고 장애 조치를 수행할 수 있습니다. 장애 조치에 따라 가장 최근의 클라우드 스냅숏에서 클라우드 속도로 데이터에 액세스할 수 있습니다.

다음 섹션에서는 백업 후처리 동안 StorSimple 클라우드 스냅숏을 트리거하고 삭제하는 간단한 스크립트를 만드는 방법을 보여 줍니다.

> [!NOTE]
> 수동 또는 프로그래밍 방식으로 만든 스냅숏은 StorSimple 스냅숏 만료 정책을 따르지 않습니다. 이러한 항목은 수동 또는 프로그래밍 방식으로 삭제되어야 합니다.

### <a name="start-delete-cloud-snapshots-with-a-script"></a>스크립트를 사용하여 클라우드 스냅숏 시작-삭제

> [!NOTE]
> StorSimple 스냅숏을 삭제하기 전에 규정 준수 및 데이터 보존 영향을 신중하게 평가합니다. 사후 백업 스크립트를 실행하는 방법에 대한 자세한 내용은 [NetBackup 설명서](https://www.veritas.com/support/article.000094423)를 참조하세요.

#### <a name="backup-lifecycle"></a>백업 주기

![백업 주기 다이어그램](./media/storsimple-configure-backup-target-using-netbackup/backuplifecycle.png)

#### <a name="requirements"></a>Requirements:

-   스크립트를 실행하는 서버는 Azure 클라우드에 대한 액세스 권한이 있어야 합니다.

-   사용자 계정에는 필요한 사용 권한이 있어야 합니다.

-   연결된 StorSimple 볼륨을 사용한 StorSimple 백업 정책이 구성되었지만 사용하도록 설정되지 않았습니다.

-   StorSimple 리소스 이름, 등록 키, 장치 이름 및 백업 정책 ID입니다.

#### <a name="steps"></a>단계:

1.  [Azure PowerShell 설치](/powershell-install-configure/)

2.  [게시 설정 및 구독 정보를 다운로드 및 가져오기](https://msdn.microsoft.com/library/dn385850.aspx)

3.  Azure 클래식 포털에서 리소스 이름 및 [StorSimple Manager 서비스의 등록 키](storsimple-deployment-walkthrough-u2.md#step-2-get-the-service-registration-key)를 가져옵니다.

4.  스크립트를 실행하는 서버에서 Windows PowerShell을 관리자 권한으로 실행합니다. 형식:

    -   `Get-AzureStorSimpleDeviceBackupPolicy –DeviceName <device name>`

    백업 정책 ID를 적어둡니다.

5.  메모장에서 새로운 Windows PowerShell 스크립트를 만들고 Azure 게시 설정을 저장한 동일한 위치에 저장합니다. 예: `C:\CloudSnapshot\StorSimpleCloudSnapshot.ps1`

    다음 코드 조각을 복사하여 붙여넣습니다.

    ```powershell
    Import-AzurePublishSettingsFile "c:\\CloudSnapshot Snapshot\\myAzureSettings.publishsettings"
    Disable-AzureDataCollection
    $ApplianceName = <myStorSimpleApplianceName>
    $RetentionInDays = 20
    $RetentionInDays = -$RetentionInDays
    $Today = Get-Date
    $ExpirationDate = $Today.AddDays($RetentionInDays)
    Select-AzureStorSimpleResource -ResourceName "myResource" –RegistrationKey
    Start-AzureStorSimpleDeviceBackupJob –DeviceName $ApplianceName -BackupType CloudSnapshot -BackupPolicyId <BackupId> -Verbose
    $CompletedSnapshots =@()
    $CompletedSnapshots = Get-AzureStorSimpleDeviceBackup -DeviceName $ApplianceName
    Write-Host "The Expiration date is " $ExpirationDate
    Write-Host

    ForEach ($SnapShot in $CompletedSnapshots)
    {
        $SnapshotStartTimeStamp = $Snapshot.CreatedOn
        if ($SnapshotStartTimeStamp -lt $ExpirationDate)

        {
            $SnapShotInstanceID = $SnapShot.InstanceId
            Write-Host "This snpashotdate was created on " $SnapshotStartTimeStamp.Date.ToShortDateString()
            Write-Host "Instance ID " $SnapShotInstanceID
            Write-Host "This snpashotdate is older and needs to be deleted"
            Write-host "\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#"
            Remove-AzureStorSimpleDeviceBackup -DeviceName $ApplianceName -BackupId $SnapShotInstanceID -Force -Verbose
        }
    }
    ```

6.  사용자의 NetBackup 작업 옵션 전후 명령을 편집하여 NetBackup의 백업 작업에 스크립트를 추가합니다.

> [!NOTE]
> 전처리 스크립트인 매일 백업 작업 끝에 StorSimple 클라우드 스냅숏 백업 정책을 실행하는 것이 좋습니다. RPO/RTO를 충족하는 백업 응용 프로그램 환경을 백업하고 복원하는 방법에 대한 자세한 내용은 백업 설계자와 상의하세요.

## <a name="storsimple-as-a-restore-source"></a>복원 원본인 StorSimple


StorSimple에서 복원은 모든 블록 저장소 장치의 복원과 비슷하게 작동합니다. 클라우드에 계층화된 데이터를 복원하는 경우 복원은 클라우드 속도로 실행됩니다. 로컬 데이터의 경우 복원은 장치의 로컬 디스크 속도로 실행됩니다. 복원을 수행하는 방법에 대한 자세한 내용은 [NetBackup 설명서](https://www.veritas.com/support/article.000094423)를 참조하고 NetBackup 복원 모범 사례를 따릅니다.

## <a name="storsimple-failover-and-disaster-recovery"></a>StorSimple 장애 조치(failover) 및 재해 복구

> [!NOTE]
> 백업 대상 시나리오의 경우 StorSimple 클라우드 어플라이언스는 복원 대상으로 지원되지 않습니다.

재해는 다양한 원인으로 인해 발생할 수 있습니다. 다음 테이블에서는 일반적인 DR(재해 복구) 시나리오를 나열합니다.

| 시나리오                                                                    | 영향                                             | 복구 방법                                                                                                                                                                               | 참고 사항                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
|-----------------------------------------------------------------------------|----------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| StorSimple 어플라이언스 오류                                                | 백업 및 복원 작업이 중단됩니다. | 실패한 어플라이언스를 교체하고 [StorSimple 장애 조치 및 재해 복구](storsimple-device-failover-disaster-recovery.md)를 수행합니다. | 어플라이언스 복구 후에 복원을 수행해야 하는 경우 전체 데이터 작업 집합을 클라우드에서 새 어플라이언스로 검색하면 모든 작업이 클라우드 속도로 수행되게 됩니다. 프로세스를 다시 검색하는 이 인덱싱 및 카탈로그는 모든 백업 집합을 검색하고 클라우드 계층에서 로컬 어플라이언스 계층으로 가져오게 되고 이 과정에서 시간이 많이 소요될 수 있습니다.                                                 |
| NetBackup 서버 오류                                              | 백업 및 복원 작업이 중단됩니다. | 백업 서버를 다시 빌드하고 데이터베이스 복원을 수행합니다                                                                                                                                       | NetBackup 서버를 다시 작성하거나 DR 사이트에서 복원해야 합니다. 데이터베이스를 최근 시점으로 복원할 수 있어야 합니다. 복원된 NetBackup 데이터베이스가 최신 백업 작업과 동기화되지 않은 경우 인덱싱 및 카탈로그가 필요합니다. 이 인덱스 및 카탈로그를 다시 검색하는 프로세스는 모든 백업 집합을 클라우드 계층에서 로컬 장치 계층으로 스캔하고 가져올 수 있습니다. 그러면 더욱 시간에 민감해집니다. |
| 사이트 오류로 인해 백업 서버 및 StorSimple 모두 손실됩니다. | 백업 및 복원 작업이 중단됩니다. | StorSimple을 먼저 복원한 다음 NetBackup을 복원합니다.                                                                                                                                          | StorSimple을 먼저 복원한 다음 NetBackup을 복원합니다.                                                                 장치 복구 후에 복원을 수행해야 하는 경우 클라우드에서 새 장치에 전체 데이터 작업 집합을 검색합니다. 결과적으로 모든 작업이 클라우드 속도로 수행됩니다.|

## <a name="references"></a>참조

다음 문서는 이 문서에서 참조되었습니다.

- [StorSimple MPIO 설정](storsimple-configure-mpio-windows-server.md)

- [저장소 시나리오: 씬 프로비전](http://msdn.microsoft.com/library/windows/hardware/dn265487.aspx)

- [GPT 드라이브 사용](http://msdn.microsoft.com/windows/hardware/gg463524.aspx#EHD)

- [공유 폴더에 대한 섀도 복사 사용 및 구성](http://technet.microsoft.com/library/cc771893.aspx)

## <a name="next-steps"></a>다음 단계

자세한 정보:

- [백업 집합에서 복원](storsimple-restore-from-backup-set-u2.md)하는 방법에 대한 정보
- [장치 장애 조치 및 재해 복구](storsimple-device-failover-disaster-recovery.md)를 수행하는 방법에 대한 정보



<!--HONumber=Dec16_HO2-->


