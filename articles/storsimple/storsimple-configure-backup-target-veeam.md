---
title: "Veeam을 사용하여 Microsoft Azure StorSimple 구성 | Microsoft Docs"
description: "Veeam을 사용하는 StorSimple 백업 대상 구성을 설명합니다."
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
ms.date: 12/06/2016
ms.author: hkanna
translationtype: Human Translation
ms.sourcegitcommit: dfecc291c441b862499d7eaaab421c7b72f929b2
ms.openlocfilehash: 2f71afc6c60cc22ef73a902ee67466313221def2

---

# <a name="configure-storsimple-with-veeam"></a>Veeam을 사용하여 StorSimple 구성

## <a name="overview"></a>개요

Microsoft Azure StorSimple은 지수 데이터 증가의 복잡성을 해결하는 하이브리드 클라우드 저장소 솔루션입니다. 이 솔루션은 온-프레미스 솔루션의 확장으로 Azure Storage를 사용하여 자동으로 온-프레미스 솔루션 및 클라우드 저장소에서 데이터를 계층화합니다.

이 문서에서는 Veeam을 사용한 StorSimple 통합 및 두 솔루션을 모두 통합하는 모범 사례를 설명합니다. 또한 StorSimple과 함께 통합하기 위해 Veeam을 구성하는 방법에 대한 권장 사항을 제공합니다. Veeam을 구성하여 백업 요구 사항 및 SLA를 충족하는 방법에 대한 Veeam 모범 사례, 백업 아키텍처 및 관리자 권한을 따릅니다.

이 문서에서는 구성 단계와 주요 개념을 보여 줍니다. 하지만 단계별 가이드를 구성 또는 설치 가이드는 아닙니다. 문서에서는 기본 구성 요소 및 인프라가 작업 순서로 배치되고 설명하는 개념을 지원할 준비가 되었다고 가정합니다.

## <a name="why-storsimple-as-a-backup-target"></a>StorSimple이 백업 대상인 이유는 무엇인가요?

StorSimple은 다음과 같은 이유로 훌륭한 백업 대상입니다.

-   빠른 백업 대상을 제공하기 위해 변경 없이 사용할 백업 응용 프로그램에 대한 표준 로컬 저장소를 제공합니다. StorSimple은 최근 백업의 빠른 복원을 위해 사용할 수도 있습니다.

-   해당 클라우드 계층은 클라우드 저장소 계정과 원활하게 통합되어 비용 효율적인 Microsoft Azure Storage를 사용합니다.

-   재해 복구를 위해 오프사이트 저장소를 자동으로 제공합니다.


## <a name="target-audience"></a>대상 사용자

이 문서의 사용자는 저장소, Windows Server 2012 R2, 이더넷, 클라우드 서비스 및 Veeam의 지식을 사용하는 대상 백업 관리자, 저장소 관리자 및 저장소 설계자를 포함합니다.

## <a name="supported-versions"></a>지원되는 버전

-   Veeam 9 이상

-   [StorSimple 업데이트 3 이상](/storsimple-overview#storsimple-workload-summary)



## <a name="key-concepts"></a>주요 개념

다른 모든 저장소 솔루션을 성공적으로 사용하려면 솔루션의 저장소 성능, SLA, 변경 비율 및 용량 증가 요구 사항을 신중하게 평가하는 것이 중요합니다. 기본 아이디어는 해당 작업을 수행하기 위해 클라우드 계층, 액세스 시간 및 처리량을 클라우드에 도입하여 StorSimple의 기능에서 중심적인 역할을 수행하는 것입니다.

StorSimple은 잘 정의된 데이터(핫 데이터)의 작업 집합에서 작동하는 응용 프로그램에 대한 저장소를 제공하도록 설계되었습니다. 이 모델에서 데이터의 작업 집합은 로컬 계층에 저장되고 데이터의 나머지 비작업/콜드/보관 집합은 클라우드로 계층화됩니다. 다음 그림에서 이 모델을 나타냅니다. 평편한 녹색선은 StorSimple 장치의 로컬 계층에 저장된 데이터를 나타냅니다. 빨강선은 모든 계층에서 StorSimple 솔루션에 저장된 데이터의 총량을 나타냅니다. 평편한 녹색선과 빨강색 지수 곡선 사이의 간격은 클라우드에 저장된 데이터의 총량을 나타냅니다.

**StorSimple 계층**
![Storsimple 계층 다이어그램](./media/storsimple-configure-backup-target-using-veeam/image1.jpg)

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

다음 테이블은 장치 모델-아키텍처 초기 지침을 포함합니다.

#### <a name="storsimple-capacities-for-local-and-cloud-storage"></a>로컬 및 클라우드 저장소의 StorSimple 용량


| 저장소 용량       | 8100       | 8600       |
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

![기본 백업 대상 논리 다이어그램인 StorSimple](./media/storsimple-configure-backup-target-using-veeam/primarybackuptargetlogicaldiagram.png)

### <a name="primary-target-backup-logical-steps"></a>기본 대상 백업 논리 단계

1.  백업 서버는 대상 백업 에이전트를 연결하고 백업 에이전트는 데이터를 백업 서버에 전송합니다.

2.  백업 서버는 데이터를 StorSimple 계층화된 볼륨에 작성합니다.

3.  백업 서버는 카탈로그 데이터베이스를 업데이트하고 백업 작업을 완료합니다.

4.  스냅숏 스크립트는 StorSimple 클라우드 스냅숏 관리를 트리거합니다.

5.  보존 정책에 따라 백업 서버는 만료된 백업을 삭제합니다.

### <a name="primary-target-restore-logical-steps"></a>기본 대상 복원 논리 단계

1.  백업 서버는 저장소 리포지토리에서 적절한 데이터를 복원하기 시작합니다.

2.  백업 에이전트는 백업 서버에서 데이터를 수신합니다.

3.  백업 서버는 복원 작업을 완료합니다.

## <a name="storsimple-as-a-secondary-backup-target"></a>보조 백업 대상인 StorSimple

이 시나리오에서 StorSimple 볼륨은 주로 장기 보존 또는 보관에 사용됩니다.

다음 그림에서는 초기 백업 및 복원이 고성능 볼륨을 대상으로 하는 아키텍처를 보여 줍니다. 이러한 백업을 복사하고 지정된 일정의 StorSimple 계층화된 볼륨에 보관합니다.

충분한 공간 및 성능을 가진 고성능 볼륨의 크기를 조절하여 보존 정책, 용량 및 성능 요구 사항을 처리하는 것이 중요합니다.

![보조 백업 대상 논리 다이어그램인 StorSimple](./media/storsimple-configure-backup-target-using-veeam/secondarybackuptargetlogicaldiagram.png)

### <a name="secondary-target-backup-logical-steps"></a>보조 대상 백업 논리 단계

1.  백업 서버는 대상 백업 에이전트를 연결하고 백업 에이전트는 데이터를 백업 서버에 전송합니다.

2.  백업 서버는 고성능 저장소에 데이터를 작성합니다.

3.  백업 서버는 카탈로그 데이터베이스를 업데이트하고 백업 작업을 완료합니다.

4.  보존 정책에 따라 백업 서버는 StorSimple에 백업을 복사합니다.

5.  스냅숏 스크립트는 StorSimple 클라우드 스냅숏 관리를 트리거합니다.

6.  보존 정책에 따라 백업 서버는 만료된 백업을 삭제합니다.

### <a name="secondary-target-restore-logical-steps"></a>보조 대상 복원 논리 단계

1.  백업 서버는 저장소 리포지토리에서 적절한 데이터를 복원하기 시작합니다.

2.  백업 에이전트는 백업 서버에서 데이터를 수신합니다.

3.  백업 서버는 복원 작업을 완료합니다.

## <a name="deploy-the-solution"></a>솔루션 배포

이 솔루션의 배포는 네트워크 인프라 준비, 백업 대상으로 StorSimple 장치 배포, 마지막으로 Veeam 소프트웨어 배포와 같은 세 단계로 구성됩니다. 이러한 각 단계는 다음 섹션에서 자세히 설명합니다.

### <a name="configure-the-network"></a>네트워크 구성

Azure 클라우드에서 통합 솔루션인 StorSimple에는 Azure 클라우드에 대한 활성 작업 연결이 필요합니다. 이 연결은 클라우드 스냅숏, 관리, 메타데이터 전송과 같은 작업에서 오래되고 액세스 빈도가 적은 데이터를 Azure 클라우드 저장소에 계층화하는 데 사용됩니다.

솔루션을 최적으로 수행하려면 다음과 같은 네트워킹 모범 사례를 따르는 것이 좋습니다.

-   StorSimple 계층을 Azure에 연결하는 링크는 적절한 QoS(서비스의 품질)를 인프라 스위치에 적용하여 RPO/RTO SLA와 일치하도록 대역폭 요구 사항을 충족해야 합니다.

-   Azure Blob Storage 액세스 대기 시간은 최대 80ms 범위에 있어야 합니다.

### <a name="deploy-storsimple"></a>StorSimple 배포

단계별 StorSimple 배포 가이드의 경우 [온-프레미스 StorSimple 장치 배포](storsimple-deployment-walkthrough-u2.md)로 이동합니다.

### <a name="deploy-veeam"></a>Veeam 배포

Veeam 설치 모범 사례의 경우 [Veeam 도움말 센터(기술 문서)](https://www.veeam.com/documentation-guides-datasheets.html)에서 [Veeam9에 대한 모범 사례](https://bp.veeam.expert/) 및 사용자 가이드로 이동합니다.

## <a name="configure-the-solution"></a>솔루션 구성

이 섹션에서는 몇 가지 구성 예를 보여 줍니다. 다음 예제/권장 사항에서는 가장 기본적이고 기본 구현을 보여 줍니다. 이 구현은 특정 백업 요구 사항에 직접 적용되지 않을 수 있습니다.

### <a name="configure-storsimple"></a>StorSimple 구성

| StorSimple 배포 작업                                                                                                                 | 추가 설명                                                                                                                                                                                                                                                                                      |
|---------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 온-프레미스 StorSimple 장치를 배포합니다.                                                                                    | 지원되는 버전: 업데이트 3 이상                                                                                                                                                                                                                                                                 |
| 백업 대상 모드를 사용하도록 설정합니다.                                                                                                                | 다음 명령을 사용하여 설정/해제 및 상태를 가져옵니다. 자세한 내용은 [StorSimple에 원격으로 연결](storsimple-remote-connect.md)로 이동합니다.</br> 백업 모드 사용:`Set-HCSBackupApplianceMode -enable`</br>  백업 모드 사용 안 함:`Set-HCSBackupApplianceMode -disable`</br> 백업 모드 설정의 현재 상태`Get-HCSBackupApplianceMode` |
| 백업 데이터를 저장하는 볼륨에 대한 일반적인 볼륨 컨테이너를 만듭니다. 볼륨 컨테이너에 있는 모든 데이터의 중복을 제거합니다. | StorSimple 볼륨 컨테이너는 중복 제거 도메인을 정의합니다.                                                                                                                                                                                                                                             |
| StorSimple 볼륨 만들기                                                                                                                 | 볼륨 크기가 클라우드 스냅숏 기간에 영향을 주기 때문에 가능한 예상되는 사용률에 가까운 크기의 볼륨을 만듭니다. 볼륨의 크기를 조정하는 방법에 대한 자세한 내용은 [보존 정책](#retention-policies)으로 이동합니다.</br> </br> StorSimple 계층화된 볼륨을 사용하고 **자주 액세스하지 않는 보관 데이터에 이 볼륨 사용** 확인란을 선택합니다. </br> 로컬로 고정된 볼륨만 해당은 지원되지 않습니다.        |
| 모든 백업 대상 볼륨에 대한 고유한 StorSimple 백업 정책을 만듭니다.                                                               | StorSimple 백업 정책은 볼륨 일관성 그룹을 정의합니다.                                                                                                                                                                                                                                       |
| 일정을 스냅숏으로 사용하지 않도록 설정합니다.                                                                                                    | 스냅숏은 후처리 작업으로 트리거됩니다.                                                                                                                                                                                                                                                         |
|                                                                                                     |                                             |


### <a name="configure-host-backup-server-storage"></a>호스트 서버 백업 저장소 구성

다음 지침에 따라 호스트 백업 서버 저장소를 구성했는지 확인합니다.  

- 이러한 볼륨이 지원되지 않으므로 Windows 디스크 관리자에서 만든 스팬 볼륨을 사용하지 않습니다.
- 64KB 할당 단위 크기인 NTFS를 사용하여 볼륨을 포맷합니다.
- Veeam 서버에 직접 StorSimple 볼륨을 매핑합니다. 
    - 물리적 서버에 대한 iSCSI를 사용합니다.


## <a name="best-practices-for-storsimple-and-veeam"></a>StorSimple 및 Veeam에 대한 모범 사례

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

### <a name="veeam"></a>Veeam

-   Veeam 데이터베이스는 서버에 대해 로컬이어야 하고 StorSimple 볼륨에 상주하지 않아야 합니다.

-   DR의 경우 StorSimple 볼륨에 Veeam 데이터베이스를 백업합니다.

-   이 솔루션에 대한 Veeam 전체 백업과 증분 백업을 지원합니다. 가상 및 차등 백업을 사용하지 않는 것이 좋습니다.

-   백업 데이터 파일에는 특정 작업에 대한 데이터만 있어야 합니다. 예를 들어 다른 작업에 미디어 추가가 허용되지 않습니다.

-   작업 검증을 사용하지 않도록 설정합니다. 필요한 경우 최신 백업 작업 후에 검증을 예약해야 합니다. 이 작업이 백업 창에 주는 영향을 이해하는 것이 중요합니다.

-   미디어 사전 할당을 사용하지 않도록 설정합니다.

-   병렬 처리를 사용하도록 설정해야 합니다.

-   압축을 사용하지 않도록 설정합니다.

-   백업 작업에서 중복 제거를 사용하지 않도록 설정합니다.

-   최적화를 **LAN 대상**으로 설정합니다.

-   2주마다 **전체 활성 백업 만들기**를 사용하도록 설정합니다.

-   백업 리포지토리에서 **VM당 백업 파일 사용**을 구성합니다.

-   **작업당 여러 업로드 스트림 사용**을 8로 설정합니다(최대 16개 허용). StorSimple 장치에서 CPU 사용률에 따라 위아래로 조정합니다.

## <a name="retention-policies"></a>보존 정책

자주 사용하는 백업 보존 정책 중 하나는 GFS(Grandfather, Father, and Son)입니다. 이 정책에서 증분 백업은 매일 수행됩니다. 매주 및 매월 전체 백업이 수행됩니다. 이 정책은 6개의 StorSimple 계층화된 볼륨을 발생시킵니다.

-   하나의 볼륨에는 매주, 매월 및 매년 전체 백업이 포함됩니다.

-   다른 5개의 볼륨은 매일 증분 백업을 저장합니다.

다음 예제에서는 GFS 회전에 대해 알아봅니다. 다음 예제에서는 다음을 가정합니다.

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
| 추가 할당량      | 4        |                                                        | 42개의 총 GFS 요구 사항입니다.   |

\*GFS 승수는 백업 정책을 충족하기 위해 보호하고 유지해야 하는 복사본의 수입니다.

## <a name="configuring-veeam-storage"></a>Veeam 저장소 구성

1.  **백업 인프라** 설정으로 이동합니다. **백업 리포지토리**를 선택한 다음 **백업 리포지토리 추가**를 마우스 오른쪽 단추로 클릭하고 선택합니다.

    ![Veeam 관리 콘솔, 백업 리포지토리 화면](./media/storsimple-configure-backup-target-using-veeam/veeamimage1.png)

1.  리포지토리의 **이름**과 **설명**을 제공합니다. **다음 >**을 클릭합니다.

    ![Veeam 관리 콘솔, 이름 및 설명 화면](./media/storsimple-configure-backup-target-using-veeam/veeamimage2.png)

1.  백업 저장소를 **Microsoft Windows Server**로 선택합니다. Veeam Server를 선택합니다. **다음 >**을 클릭합니다.

    ![Veeam 관리 콘솔, 백업 리포지토리의 유형 선택](./media/storsimple-configure-backup-target-using-veeam/veeamimage3.png)

1.  **위치**를 지정하려면 필수 볼륨을 찾아 선택합니다. **최대 동시 작업 제한:**을 4로 설정합니다. 이렇게 하면 각 VM이 처리되는 것처럼 4개의 가상 디스크가 처리됩니다.
**고급**을 클릭합니다.

    ![Veeam 관리 콘솔, 볼륨 선택](./media/storsimple-configure-backup-target-using-veeam/veeamimage4.png)


1.  **VM당 백업 파일 사용**을 선택합니다.

    ![Veeam 관리 콘솔, 저장소 호환성 설정](./media/storsimple-configure-backup-target-using-veeam/veeamimage5.png)

1.  **탑재 서버의 vPower NFS 서비스(권장)**를 사용하도록 설정합니다.

    ![Veeam 관리 콘솔, 백업 리포지토리 화면](./media/storsimple-configure-backup-target-using-veeam/veeamimage6.png)

1.  설정을 검토하고 다음 페이지로 진행합니다.

    ![Veeam 관리 콘솔, 백업 리포지토리 화면](./media/storsimple-configure-backup-target-using-veeam/veeamimage7.png)

    리포지토리는 Veeam 서버에 추가됩니다.

## <a name="storsimple-as-a-primary-backup-target"></a>기본 백업 대상인 StorSimple

> [!IMPORTANT]
> 클라우드에 계층화된 백업의 데이터를 복원해야 하는 경우 클라우드 속도로 복원이 실행됩니다.

다음 그림에서는 백업 작업에 대한 일반적인 볼륨의 매핑을 설명합니다. 이 경우에 모든 주간 백업이 토요일 전체 디스크에 매핑되고 증분 백업은 월요일-금요일 증분 디스크에 매핑됩니다. StorSimple 계층화된 볼륨에서 모든 백업 및 복원이 수행됩니다.

![기본 백업 대상 구성 논리 다이어그램 ](./media/storsimple-configure-backup-target-using-veeam/primarybackuptargetdiagram.png)

#### <a name="storsimple-as-a-primary-backup-target-grandfather-father-and-son-gfs-schedule-example"></a>기본 백업 대상 GFS(Grandfather, Father, and Son) 일정 예제인 StorSimple

| 4주 동안 GFS 회전 일정, 매월 및 매년 |               |             |
|--------------------------------------------------------------------------|---------------|-------------|
| 빈도/백업 유형   | 전체          | 증분(1-5일차)  |
| 매주(1-4주차)    | 토요일 | 월요일-금요일 |
| 매월     | 토요일  |             |
| 매년      | 토요일  |             |


### <a name="assigning-storsimple-volumes-to-a-veeam-backup-job"></a>Veeam 백업 작업에 StorSimple 볼륨 할당

1.  백업 대상은 기본 백업 대상 시나리오의 경우 기본 Veeam StorSimple 또는 보조 백업 대상 시나리오의 경우 DAS/NAS/JBOD를 사용하여 매일 작업을 만듭니다. **백업 및 복제 &gt; 백업**으로 이동합니다. 사용자 환경에 따라 VMware 또는 Hyper-V를 마우스 오른쪽 단추로 클릭하고 선택합니다.

    ![Veeam 관리 콘솔, 새 백업 작업](./media/storsimple-configure-backup-target-using-veeam/veeamimage8.png)

1.  매일 백업 작업의 **이름**과 **설명**을 제공합니다.

    ![Veeam 관리 콘솔, 새 백업 작업 화면](./media/storsimple-configure-backup-target-using-veeam/veeamimage9.png)

1.  백업할 가상 컴퓨터를 선택합니다.

    ![Veeam 관리 콘솔, 새 백업 작업 화면](./media/storsimple-configure-backup-target-using-veeam/veeamimage10.png)

1.  적절한 **프록시 백업** 및 **백업 리포지토리**를 설정합니다. 로컬로 연결된 저장소에 사용자 환경의 RPO/RTO 정의당 **디스크에 유지할 복원 지점**을 설정합니다. 고급을 클릭합니다.

    ![Veeam 관리 콘솔, 새 백업 작업 화면](./media/storsimple-configure-backup-target-using-veeam/veeamimage11.png)

    **백업** 탭에서 **증분**을 선택합니다. **정기적으로 가상 전체 백업 만들기**의 선택을 취소해야 합니다. **정기적으로 전체 활성 백업 만들기**를 사용하도록 설정하고 선택된 **전체 활성 백업**에 매주를 선택하여 매주 토요일마다 활성화합니다.

    ![Veeam 관리 콘솔, 새 백업 작업 고급 설정 화면](./media/storsimple-configure-backup-target-using-veeam/veeamimage12.png)

    [저장소] 탭에서 중복 제거 및 압축을 사용하지 않도록 설정하고 파일 블록 스왑 및 삭제를 사용하도록 설정합니다. **저장소 최적화**를 안정된 성능 및 중복 제거에 대한 **LAN 대상**으로 설정합니다.

    ![Veeam 관리 콘솔, 새 백업 작업 고급 설정 화면](./media/storsimple-configure-backup-target-using-veeam/veeamimage13.png)

    중복 제거 및 압축 설정에 대한 세부 정보는 [데이터 압축 및 중복 제거](https://helpcenter.veeam.com/backup/vsphere/compression_deduplication.html)를 참조하세요.

1.  **응용 프로그램 인식 처리 사용**을 선택할 수 있습니다.

    ![Veeam 관리 콘솔, 새 백업 작업 게스트 처리 화면](./media/storsimple-configure-backup-target-using-veeam/veeamimage14.png)

1.  사용자가 선택한 지정 시간에 매일 한 번씩 실행되도록 일정을 설정합니다.

    ![Veeam 관리 콘솔, 새 백업 작업 스케줄러 화면](./media/storsimple-configure-backup-target-using-veeam/veeamimage15.png)

## <a name="storsimple-as-a-secondary-backup-target"></a>보조 백업 대상인 StorSimple

> [!NOTE]
> 클라우드에 계층화된 백업의 데이터의 경우 클라우드 속도로 복원을 수행합니다.

이 모델에서 임시 캐시의 역할을 하는 StorSimple 이외의 저장소 미디어가 있어야 합니다. 예를 들어 RAID 볼륨을 사용하여 공간, IO 및 대역폭을 수용할 수 있습니다. RAID 5, 50 및 10을 사용하는 것이 좋습니다.

다음 그림에서는 서버에 대한 일반적인 단기 보존 로컬 볼륨 및 장기 보존 보관 볼륨에 대해 설명합니다. 이 경우에 서버에 대한 로컬 RAID 볼륨에서 모든 백업을 실행합니다. 이러한 백업이 정기적으로 복제되고 보관 볼륨에 보관됩니다. 서버에 대한 로컬 RAID 볼륨 크기를 조정하여 단기 보존 용량 및 성능 요구 사항을 처리하는 것이 중요합니다.



| 백업 유형 및 보존                    |구성된 저장소| 크기(TiB) | GFS 승수 | 총 용량(TiB)        |
|----------------------------------------------|-----|----------|----------------|------------------------|
| 1주차(전체 및 증분) |로컬 디스크(단기)| 1        | 1              | 1           |
| StorSimple 2-4주차           |StorSimple 디스크(장기) | 1        | 4              | 4                   |
| 매월 전체                                 |StorSimple 디스크(장기) | 1        | 12             | 12                   |
| 매년 전체                               |StorSimple 디스크(장기) | 1        | 1              | 1                   |
|GFS 볼륨 크기 요구 사항 | |          |                | 18*|

\*총 용량에는 17TiB의 StorSimple 디스크 및 1TiB 로컬 RAID 볼륨이 포함되어 있습니다.

![보조 백업 대상 논리 다이어그램인 StorSimple ](./media/storsimple-configure-backup-target-using-veeam/secondarybackuptargetdiagram.png)

#### <a name="gfs-example-schedule"></a>GFS 예제 일정

| GFS 회전 매주, 매월 및 매년 일정|                    |                   |                   |                   |                   |                   |
|--------------------------------------------------------------------------|--------------------|-------------------|-------------------|-------------------|-------------------|-------------------|
| 주                                                                     | 전체               | 증분 1일차        | 증분 2일차        | 증분 3일차        | 증분 4일차        | 증분 5일차        |
| 1주차                                                                   | 로컬 RAID 볼륨  | 로컬 RAID 볼륨 | 로컬 RAID 볼륨 | 로컬 RAID 볼륨 | 로컬 RAID 볼륨 | 로컬 RAID 볼륨 |
| 2주차                                                                   | StorSimple 2-4주차 |                   |                   |                   |                   |                   |
| 3주차                                                                   | StorSimple 2-4주차 |                   |                   |                   |                   |                   |
| 4주차                                                                   | StorSimple 2-4주차 |                   |                   |                   |                   |                   |
| 매월                                                                  | StorSimple 매월 |                   |                   |                   |                   |                   |
| 매년                                                                   | StorSimple 매년  |                   |                   |                   |                   |                   |


### <a name="assigning-storsimple-volumes-to-a-veeam-copy-job"></a>Veeam 복사 작업에 StorSimple 볼륨 할당

1.  새 백업 복사 작업 마법사를 시작합니다. **작업 > 복사본 백업**으로 이동합니다. 사용자 환경에 따라 VMware 또는 Hyper-V를 선택합니다.

    ![Veeam 관리 콘솔, 새 백업 복사 작업 화면](./media/storsimple-configure-backup-target-using-veeam/veeamimage16.png)

1.  작업 이름과 설명을 지정합니다.

    ![Veeam 관리 콘솔, 새 백업 복사 작업 화면](./media/storsimple-configure-backup-target-using-veeam/veeamimage17.png)

1.  처리할 VM을 선택하고 백업을 선택하고 이전에 만든 매일 백업을 선택합니다.

    ![Veeam 관리 콘솔, 새 백업 복사 작업 화면](./media/storsimple-configure-backup-target-using-veeam/veeamimage18.png)

1.  필요한 경우 백업 복사 작업에서 개체를 제외합니다.

1.  **백업 리포지토리**를 선택하고 **유지할 지점 복원**을 정의하고 **보관을 위해 다음 복원 지점 유지**를 사용하도록 설정했는지 확인합니다. 백업 빈도를 정의한 다음 **고급**을 클릭합니다.

    ![Veeam 관리 콘솔, 새 백업 복사 작업 화면](./media/storsimple-configure-backup-target-using-veeam/veeamimage19.png)

1.  다음 고급 설정을 지정합니다.

    -   **유지 관리** 탭에서 저장소 수준 손상 가드를 사용하지 않도록 설정합니다.

    -   **저장소** 탭에서 중복 제거 및 압축을 사용하지 않도록 설정합니다.

    ![Veeam 관리 콘솔, 새 백업 복사 작업 고급 설정 화면](./media/storsimple-configure-backup-target-using-veeam/veeamimage20.png)

    ![Veeam 관리 콘솔, 새 백업 복사 작업 고급 설정 화면](./media/storsimple-configure-backup-target-using-veeam/veeamimage21.png)

1.  데이터 전송이 다이렉트되도록 지정합니다.

1.  사용자의 필요에 따라 백업 복사 창 일정을 정의하고 완료합니다.


자세한 내용은 [백업 복사 작업 만들기](https://helpcenter.veeam.com/backup/hyperv/backup_copy_create.html)로 이동합니다.

## <a name="storsimple-cloud-snapshots"></a>StorSimple 클라우드 스냅숏

StorSimple 클라우드 스냅숏은 StorSimple 장치에 있는 데이터를 보호합니다. 테이프를 오프사이트 시설로 전달하는 것과 같습니다. Azure GRS(지역 중복 저장소)를 사용하는 경우 여러 사이트에 테이프를 배송하는 것과 같습니다. 장치 복원이 재해에 필요한 경우 다른 StorSimple 장치를 온라인 상태로 전환하고 장애 조치를 수행할 수 있습니다. 장애 조치에 따라 가장 최근의 클라우드 스냅숏에서 클라우드 속도로 데이터에 액세스할 수 있습니다.


다음 섹션에서는 백업 후처리 동안 StorSimple 클라우드 스냅숏을 트리거하고 삭제하는 간단한 스크립트를 만드는 방법을 보여 줍니다. 

> [!NOTE] 
> 수동 또는 프로그래밍 방식으로 만든 스냅숏은 StorSimple 스냅숏 만료 정책을 따르지 않습니다. 이러한 스냅숏은 수동 또는 프로그래밍 방식으로 삭제되어야 합니다.

### <a name="start-and-delete-cloud-snapshots-with-a-script"></a>스크립트를 사용하여 클라우드 스냅숏 시작 및 삭제

> [!NOTE] 
> StorSimple 스냅숏을 삭제하기 전에 규정 준수 및 데이터 보존 영향을 신중하게 평가합니다. 사후 백업 스크립트를 실행하는 방법에 대한 자세한 내용은 Veeam 설명서를 참조하세요.


#### <a name="backup-lifecycle"></a>백업 주기


![백업 주기 다이어그램](./media/storsimple-configure-backup-target-using-veeam/backuplifecycle.png)

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

5.  메모장에서 Windows PowerShell 스크립트를 만들고 Azure 게시 설정을 저장한 동일한 위치에 저장합니다. 예: `C:\\CloudSnapshot\\StorSimpleCloudSnapshot.ps1`

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

6.  백업 작업에 스크립트를 추가하려면 Veeam 작업 고급 옵션을 편집합니다. 

![Veeam 백업 고급 설정 스크립트 탭](./media/storsimple-configure-backup-target-using-veeam/veeamimage22.png)

전처리 스크립트인 매일 백업 작업 끝에 StorSimple 클라우드 스냅숏 백업 정책을 실행하는 것이 좋습니다. RPO/RTO를 충족하는 백업 응용 프로그램 환경을 백업하고 복원하는 방법에 대한 자세한 내용은 백업 아키텍처를 참조하세요.

## <a name="storsimple-as-a-restore-source"></a>복원 원본인 StorSimple
==============================

StorSimple 장치에서 복원은 모든 블록 저장소 장치의 복원처럼 작동합니다. 클라우드에 계층화된 데이터를 복원하는 경우 복원은 클라우드 속도로 실행됩니다. 로컬 데이터의 경우 복원은 장치의 로컬 디스크 속도로 실행됩니다.

Veeam을 사용하면 Veeam 콘솔에 있는 기본 제공 탐색기를 사용하여 StorSimple을 통해 빠른 세분화 파일 수준의 복구가 가능합니다. Veeam 탐색기를 사용하여 전자 메일 메시지, Active Directory 개체 또는 백업의 SharePoint 항목과 같은 개별 항목을 복구합니다. 온-프레미스 VM을 중지하지 않고 복구를 수행할 수 있습니다. Microsoft SQL 및 Oracle 데이터베이스에 대한 지정 시간 복구를 수행할 수도 있습니다. Veeam 및 StorSimple을 통해 빠르고 쉽게 Azure에서 항목 수준 복구 프로세스를 수행할 수 있습니다. 복원을 수행하는 방법에 대한 자세한 내용은 Veeam 설명서를 참조하세요.


- [https://www.veeam.com/microsoft-exchange-recovery.html](https://www.veeam.com/microsoft-exchange-recovery.html)

- [https://www.veeam.com/microsoft-active-directory-explorer.html](https://www.veeam.com/microsoft-active-directory-explorer.html)

- [https://www.veeam.com/microsoft-sql-server-explorer.html](https://www.veeam.com/microsoft-sql-server-explorer.html)

- [https://www.veeam.com/microsoft-sharepoint-recovery-explorer.html](https://www.veeam.com/microsoft-sharepoint-recovery-explorer.html)

- [https://www.veeam.com/oracle-backup-recovery-explorer.html](https://www.veeam.com/oracle-backup-recovery-explorer.html)


## <a name="storsimple-failover-and-disaster-recovery"></a>StorSimple 장애 조치(failover) 및 재해 복구

> [!NOTE]
> 백업 대상 시나리오의 경우 StorSimple 클라우드 어플라이언스는 복원 대상으로 지원되지 않습니다.

재해는 다양한 원인으로 인해 발생할 수 있습니다. 다음 테이블에서는 일반적인 DR(재해 복구) 시나리오를 나열합니다.


| 시나리오                                                                    | 영향                                             | 복구 방법                                                                                                                                                                               | 참고 사항                                                                                                                                                                                                                                                                                                                                                                                                                                    |
|-----------------------------------------------------------------------------|----------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| StorSimple 장치 오류입니다.                                               | 백업 및 복원 작업이 중단됩니다. | 실패한 장치를 교체하고 [StorSimple 장애 조치 및 재해 복구](storsimple-device-failover-disaster-recovery.md)를 수행합니다. | 장치 복구 후에 즉시 복원이 수행되는 경우 클라우드에서 새 장치에 전체 작업 집합을 검색합니다. 결과적으로 모든 작업이 클라우드 속도로 수행됩니다. 또한 인덱스 및 카탈로그를 다시 검색하면 모든 백업 집합을 장치의 클라우드 계층에서 로컬 계층으로 스캔하고 가져올 수 있습니다. 그러면 더욱 시간이 많이 걸립니다.                                 |
| Veeam Server 오류입니다.                                                     | 백업 및 복원 작업이 중단됩니다. | [Veeam 도움말 센터(기술 문서)](https://www.veeam.com/documentation-guides-datasheets.html)에 설명한 대로 백업 서버를 다시 빌드하고 데이터베이스 복원을 수행합니다.     | Veeam 서버를 다시 작성하거나 DR 사이트에서 복원해야 합니다. 데이터베이스를 가장 최근의 지점으로 복원합니다. 복원된 Veeam 데이터베이스가 최신 백업 작업과 동기화되지 않은 경우 인덱싱 및 카탈로그가 필요합니다. 이 인덱스 및 카탈로그를 다시 검색하면 모든 백업 집합을 클라우드 계층에서 로컬 장치 계층으로 스캔하고 가져올 수 있습니다. 그러면 더욱 시간이 많이 걸립니다. |
| 사이트 오류로 인해 백업 서버 및 StorSimple 모두 손실됩니다. | 백업 및 복원 작업이 중단됩니다. | StorSimple을 먼저 복원한 다음 Veeam을 복원합니다.                                                                                                                                                  | StorSimple을 먼저 복원한 다음 Veeam을 복원합니다. 장치 복구 후에 복원을 수행해야 하는 경우 클라우드에서 새 장치에 전체 데이터 작업 집합을 검색합니다. 결과적으로 모든 작업이 클라우드 속도로 수행됩니다.                                                                                                                                                                            |


## <a name="references"></a>참조

다음 문서는 이 문서에서 참조되었습니다.

- [StorSimple MPIO 설정](storsimple-configure-mpio-windows-server.md)

- [저장소 시나리오: 씬 프로비전](http://msdn.microsoft.com/library/windows/hardware/dn265487.aspx)

- [GPT 드라이브 사용](http://msdn.microsoft.com/windows/hardware/gg463524.aspx#EHD)

- [공유 폴더에 대한 섀도 복사 사용 및 구성](http://technet.microsoft.com/library/cc771893.aspx)



<!--HONumber=Dec16_HO2-->


