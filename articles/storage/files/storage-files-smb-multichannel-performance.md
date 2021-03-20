---
title: SMB 다중 채널 성능-Azure Files
description: SMB 다중 채널 성능에 대해 알아봅니다.
author: gunjanj
ms.service: storage
ms.topic: conceptual
ms.date: 11/16/2020
ms.author: gunjanj
ms.subservice: files
ms.openlocfilehash: a9edd93aa265622732be4a7582cce9900959bf6d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100374986"
---
# <a name="smb-multichannel-performance"></a>SMB 다중 채널 성능

Smb 다중 채널 (미리 보기)을 Azure Files SMB 3.x 클라이언트에서 FileStorage 계정의 프리미엄 파일 공유에 대 한 여러 네트워크 연결을 설정할 수 있습니다. SMB 3.0 프로토콜은 Windows Server 2012 및 Windows 8 클라이언트에서 SMB 다중 채널 기능을 도입 했습니다. 이로 인해 SMB 다중 채널을 지 원하는 모든 Azure Files SMB 2.x 클라이언트는 Azure premium 파일 공유 기능을 활용할 수 있습니다. 저장소 계정에서 SMB 다중 채널을 사용 하도록 설정 하는 데에는 추가 비용이 없습니다.

## <a name="benefits"></a>이점

SMB 다중 채널을 사용 하면 클라이언트는 소유 비용을 줄이는 동시에 성능을 향상 시키는 여러 네트워크 연결을 사용할 수 있습니다. Azure Files 여러 Nic에 대 한 대역폭 집계를 통해 성능을 향상 시키고, Nic에 대해 RSS (수신측 배율) 지원을 활용 하 여 IO 부하를 여러 Cpu에 분산 합니다.

- **향상 된 처리량**: 여러 연결을 사용 하면 여러 경로를 통해 데이터를 병렬로 전송할 수 있으므로 IO 크기가 더 큰 파일 크기를 사용 하 고 단일 vm 또는 더 작은 vm 집합에서 높은 처리량을 요구 하는 작업을 상당히 활용할 수 있습니다. 이러한 작업 중 일부에는 콘텐츠 생성 또는 코드 변환, genomics 및 금융 서비스 위험 분석을 위한 미디어 및 엔터테인먼트가 포함 됩니다.
- **높은 IOPS**: NIC RSS 기능을 사용 하면 여러 개의 연결을 포함 하는 여러 cpu에서 효과적인 부하 분산을 수행할 수 있습니다. 이렇게 하면 VM Cpu의 IOPS 규모와 유효 사용률을 높일 수 있습니다. 데이터베이스 응용 프로그램과 같이 작은 IO 크기의 작업에 유용 합니다.
- **네트워크 내결함성**: 여러 연결을 사용 하면 클라이언트가 개별 연결을 더 이상 사용 하지 않으므로 중단 위험이 줄어듭니다.
- **자동 구성**: 클라이언트 및 저장소 계정에서 SMB 다중 채널을 사용 하는 경우 기존 연결의 동적 검색을 허용 하 고 필요에 따라 추가 연결 경로를 만들 수 있습니다.
- **비용 최적화**: 작업은 프리미엄 공유에 연결 하는 동안 단일 vm 또는 작은 vm 집합에서 더 높은 규모를 달성할 수 있습니다. 이렇게 하면 워크 로드를 실행 하 고 관리 하는 데 필요한 Vm 수를 줄임으로써 총 소유 비용을 줄일 수 있습니다.

SMB 다중 채널에 대 한 자세한 내용은 [Windows 설명서](/azure-stack/hci/manage/manage-smb-multichannel)를 참조 하세요.

이 기능은 다중 스레드 응용 프로그램의 성능 향상을 제공 하지만 일반적으로 단일 스레드 응용 프로그램에는 도움이 되지 않습니다. 자세한 내용은 [성능 비교](#performance-comparison) 섹션을 참조 하세요.

## <a name="limitations"></a>제한 사항

[!INCLUDE [storage-files-smb-multi-channel-restrictions](../../../includes/storage-files-smb-multi-channel-restrictions.md)]

### <a name="regional-availability"></a>국가별 가용성

[!INCLUDE [storage-files-smb-multi-channel-regions](../../../includes/storage-files-smb-multi-channel-regions.md)]

## <a name="configuration"></a>구성

SMB 다중 채널은 기능이 클라이언트 쪽 (클라이언트)과 서비스 측 (Azure storage 계정) 모두에서 사용 하도록 설정 된 경우에만 작동 합니다.

Windows 클라이언트에서 SMB 다중 채널은 기본적으로 사용 하도록 설정 되어 있습니다. 다음 PowerShell 명령을 실행 하 여 구성을 확인할 수 있습니다. 

`Get-smbClientConfiguration | select EnableMultichannel`.
 
Azure storage 계정에서 SMB 다중 채널을 사용 하도록 설정 해야 합니다. [SMB 다중 채널 사용 (미리 보기)](storage-files-enable-smb-multichannel.md)을 참조 하세요.

### <a name="disable-smb-multichannel"></a>SMB 다중 채널 사용 안 함
대부분의 시나리오에서 특히 다중 스레드 작업의 경우 클라이언트는 SMB 다중 채널과의 성능 향상을 확인 해야 합니다. 그러나 단일 스레드 워크 로드와 같은 일부 특정 시나리오 또는 테스트 목적으로 SMB 다중 채널을 사용 하지 않도록 설정할 수 있습니다. 자세한 내용은 [성능 비교](#performance-comparison) 를 참조 하세요.

## <a name="verify-smb-multichannel-is-configured-correctly"></a>SMB 다중 채널이 올바르게 구성 되었는지 확인

1. 프리미엄 파일 공유를 만들거나 기존 파일 공유를 사용 합니다.
1. 클라이언트에서 SMB 다중 채널을 지원 하는지 확인 합니다 (하나 이상의 네트워크 어댑터에서 수신측 배율을 사용 하도록 설정 됨). 자세한 내용은 [Windows 설명서](/azure-stack/hci/manage/manage-smb-multichannel) 를 참조 하십시오.
1. 파일 공유를 클라이언트에 탑재 합니다.
1. 응용 프로그램을 사용 하 여 부하를 생성 합니다.
    Robocopy/MT와 같은 복사 도구나 파일 읽기/쓰기에 대 한 Diskspd와 같은 성능 도구는 로드를 생성할 수 있습니다.
1. 관리자 권한으로 PowerShell을 열고 다음 명령을 사용 합니다. `Get-SmbMultichannelConnection |fl`
1. **Maxchannels** **currentchannels** 속성을 찾습니다.

:::image type="content" source="media/storage-files-smb-multichannel-performance/files-smb-multi-channel-connection.PNG" alt-text="Get-SMBMultichannelConnection 결과의 스크린샷" lightbox="media/storage-files-smb-multichannel-performance/files-smb-multi-channel-connection.PNG":::

## <a name="performance-comparison"></a>성능 비교

읽기/쓰기 워크 로드 패턴에는 단일 스레드 및 다중 스레드의 두 범주가 있습니다. 대부분의 워크 로드는 여러 파일을 사용 하지만 작업은 공유의 단일 파일에 대해 작동 하는 특정 사용 사례가 있을 수 있습니다. 이 섹션에서는 다양 한 사용 사례와 각 사례에 대 한 성능 영향을 설명 합니다. 일반적으로 대부분의 워크 로드는 다중 스레드 이며 워크 로드를 여러 파일에 분산 하 여 SMB 다중 채널을 사용 하 여 상당한 성능 향상을 관찰 해야 합니다.

- **다중 스레드/다중 파일**: 워크 로드 패턴에 따라 여러 채널에 대 한 읽기 및 쓰기 io에 상당한 성능 향상이 표시 됩니다. 성능 향상은 IOPS, 처리량 및 대기 시간 측면에서 2x에서 4x 사이에 차이가 있습니다. 이 범주에 대해 최상의 성능을 위해 SMB 다중 채널을 사용 하도록 설정 해야 합니다.
- **다중 스레드/단일 파일**:이 범주에서는 대부분의 사용 사례에 대해 SMB 다중 채널을 사용 하도록 설정 하는 것이 좋습니다 (특히 워크 로드의 평균 IO > 크기가 ~ 16k 인 경우). SMB 다중 채널을 활용 하는 몇 가지 예제 시나리오에서는 단일 큰 파일의 백업 또는 복구를 사용할 수 있습니다. SMB 다중 채널을 사용 하지 않도록 설정할 수 있는 예외는 작업 부하가 작은 IOs 인 경우입니다. 이 경우에는 ~ 10%의 약간의 성능이 저하 될 수 있습니다. 사용 사례에 따라 부하를 여러 파일에 분산 하거나 기능을 사용 하지 않도록 설정 하는 것이 좋습니다. 자세한 내용은 [구성](#configuration) 섹션을 참조 하세요.
- **단일 스레드/다중 파일 또는 단일 파일**: 대부분의 단일 스레드 워크 로드의 경우 병렬 처리 부족으로 인 한 최소한의 성능 이점이 있습니다. 일반적으로 SMB 다중 채널을 사용 하는 경우에는 ~ 10%의 성능이 약간 저하 됩니다. 이 경우 한 가지 예외를 제외 하 고 SMB 다중 채널을 사용 하지 않도록 설정 하는 것이 이상적입니다. 단일 스레드 작업에서 여러 파일에 부하를 분산 하 고 평균 보다 큰 IO 크기 (> ~ 16k)를 사용 하는 경우 SMB 다중 채널을 사용 하면 성능이 약간 향상 됩니다.

### <a name="performance-test-configuration"></a>성능 테스트 구성

이 문서의 차트에서 사용 된 구성은 단일 RSS를 사용 하는 단일 표준 D32s v3 VM 및 4 개의 채널이 포함 된 NIC입니다. 로드는 diskspd.exe, IO 깊이가 10 인 다중 스레드 및 다양 한 IO 크기의 임의 io를 사용 하 여 생성 되었습니다.

| 크기 | vCPU | 메모리: GiB | 임시 스토리지(SSD) GiB | 최대 데이터 디스크 수 | 최대 캐시 및 임시 저장소 처리량: IOPS/MBps (GiB의 캐시 크기) | 캐시되지 않은 최대 디스크 처리량: IOPS/MBps | 최대 NIC 수|예상 네트워크 대역폭 (Mbps) |
|---|---|---|---|---|---|---|---|---|
| [Standard_D32s_v3](../../virtual-machines/dv3-dsv3-series.md) | 32 | 128 | 256 | 32 | 64000/512 (800)    | 51200/768  | 8|16000 |

:::image type="content" source="media/storage-files-smb-multichannel-performance/files-smb-multi-channel-nic-settings-all-nics.PNG" alt-text="성능 테스트 구성을 보여 주는 스크린샷" lightbox="media/storage-files-smb-multichannel-performance/files-smb-multi-channel-nic-settings-all-nics.PNG":::

### <a name="mutli-threadedmultiple-files-with-smb-multichannel"></a>SMB 다중 채널을 사용 하는 multi-factor/다중 파일

여러 IO 크기의 10 개 파일에 대해 로드가 생성 되었습니다. 테스트 결과 확장은 SMB 다중 채널을 사용 하도록 설정 된 IOPS 및 처리량 테스트 결과에서 상당한 향상 된 기능을 보여 주었습니다. 다음 다이어그램은 결과를 나타냅니다.

:::image type="content" source="media/storage-files-smb-multichannel-performance/diagram-smb-multi-channel-multiple-files-compared-to-single-channel-iops-performance.png" alt-text="성능 다이어그램" lightbox="media/storage-files-smb-multichannel-performance/diagram-smb-multi-channel-multiple-files-compared-to-single-channel-iops-performance.png":::

:::image type="content" source="media/storage-files-smb-multichannel-performance/diagram-smb-multi-channel-multiple-files-compared-to-single-channel-throughput-performance.png" alt-text="처리량 성능 다이어그램." lightbox="media/storage-files-smb-multichannel-performance/diagram-smb-multi-channel-multiple-files-compared-to-single-channel-throughput-performance.png":::

- 단일 NIC에서 읽기의 경우 2, 0003의 성능 향상이 관찰 되었고 쓰기의 경우 IOPS와 처리량을 모두 기준으로 3 x 4를 사용 합니다.
- SMB 다중 채널은 단일 NIC 및 4 개의 채널 제한을 사용 하 여 VM 제한에 도달 하는 IOPS 및 처리량을 허용 합니다.
- 송신 또는 저장소에 대 한 읽기는 계량 되지 않으므로 읽기 처리량은 VM 게시 제한인 16000 Mbps (2 GiB/s)를 초과할 수 있었습니다. 테스트 >2.7 GiB/s를 수행 했습니다. 수신 (또는 저장소에 쓰기)에는 여전히 VM 한도가 적용 됩니다.
- 여러 파일에 대 한 부하 분산은 상당한 개선을 위해 허용 됩니다.

이 테스트에 사용 된 예제 명령은 다음과 같습니다. 

`diskspd.exe -W300 -C5 -r -w100 -b4k -t8 -o8 -Sh -d60 -L -c2G -Z1G z:\write0.dat z:\write1.dat z:\write2.dat z:\write3.dat z:\write4.dat z:\write5.dat z:\write6.dat z:\write7.dat z:\write8.dat z:\write9.dat `.

### <a name="multi-threadedsingle-file-workloads-with-smb-multichannel"></a>SMB 다중 채널을 사용 하는 다중 스레드/단일 파일 작업

로드는 단일 128 GiB 파일에 대해 생성 되었습니다. SMB 다중 채널을 사용 하도록 설정 하면 다중 스레드/단일 파일이 포함 된 수직 확장 테스트가 대부분의 경우 개선 된 것으로 나타났습니다. 다음 다이어그램은 결과를 나타냅니다.

:::image type="content" source="media/storage-files-smb-multichannel-performance/diagram-smb-multi-channel-single-file-compared-to-single-channel-iops-performance.png" alt-text="IOPS 성능 다이어그램." lightbox="media/storage-files-smb-multichannel-performance/diagram-smb-multi-channel-single-file-compared-to-single-channel-iops-performance.png":::

:::image type="content" source="media/storage-files-smb-multichannel-performance/diagram-smb-multi-channel-single-file-compared-to-single-channel-throughput-performance.png" alt-text="단일 파일 처리량 성능을 다이어그램으로 처리 합니다." lightbox="media/storage-files-smb-multichannel-performance/diagram-smb-multi-channel-single-file-compared-to-single-channel-throughput-performance.png":::

- 평균 IO 크기가 더 큰 단일 NIC (> ~ 16k)에서는 읽기 및 쓰기 기능이 크게 향상 되었습니다.
- 더 작은 IO 크기의 경우 SMB 다중 채널을 사용 하는 경우 성능에 대 한 ~ 10%의 약간의 영향을 받았습니다. 이는 부하를 여러 파일에 분산 하거나 기능을 사용 하지 않도록 설정 하 여 완화할 수 있습니다.
- 성능은 여전히 [단일 파일 제한](storage-files-scale-targets.md#file-scale-targets)에 의해 바인딩됩니다.

## <a name="optimizing-performance"></a>성능 최적화

성능을 최적화 하는 데 도움이 되는 팁은 다음과 같습니다.

- 네트워크 대기 시간을 줄이기 위해 저장소 계정 및 클라이언트가 동일한 Azure 지역에 공동 배치 확인 합니다.
- 다중 스레드 응용 프로그램을 사용 하 여 부하를 여러 파일에 분산 합니다.
- SMB 다중 채널의 성능 이점은 부하를 분산 하는 파일 수를 늘립니다.
- 프리미엄 공유 성능은 프로 비전 된 공유 크기 (IOPS/송신/수신) 및 단일 파일 제한에 의해 바인딩됩니다. 자세한 내용은 [프리미엄 파일 공유에 대 한 프로 비전 이해](understanding-billing.md#provisioned-model)를 참조 하세요.
- 단일 VM 클라이언트의 최대 성능은 VM 제한에 계속 바인딩되어 있습니다. 예를 들어 [Standard_D32s_v3](../../virtual-machines/dv3-dsv3-series.md) 최대 대역폭 16000 MBps (또는 2gbps)를 지원할 수 있습니다. VM에서의 송신 (저장소에 쓰기)은 유료 (저장소에서 읽기)입니다. 파일 공유 성능에는 컴퓨터 네트워크 제한, Cpu, 내부 저장소 사용 가능한 네트워크 대역폭, IO 크기, 병렬 처리 및 기타 요인이 적용 됩니다.
- 초기 테스트는 일반적으로 준비 하 고, 결과를 삭제 하 고, 테스트를 반복 합니다.
- 단일 클라이언트에 의해 성능이 제한 되 고 워크 로드가 여전히 프로 비전 된 공유 제한을 초과 하는 경우 여러 클라이언트에 부하를 분산 하 여 더 높은 성능을 얻을 수 있습니다.

### <a name="the-relationship-between-iops-throughput-and-io-sizes"></a>IOPS, 처리량 및 IO 크기 간의 관계

**처리량 = IO 크기 * IOPS**

더 높은 IO 크기는 처리량이 높고 대기 시간이 길수록 네트워크 IOPS 수가 줄어듭니다. 더 작은 IO 크기는 더 높은 IOPS를 구동 하지만 네트워크 처리량과 대기 시간이 줄어듭니다.

## <a name="next-steps"></a>다음 단계

- [FileStorage 계정에서 SMB 다중 채널 사용 (미리 보기)](storage-files-enable-smb-multichannel.md)
- SMB 다중 채널에 대해 자세히 알아보려면 [Windows 설명서](/azure-stack/hci/manage/manage-smb-multichannel) 를 참조 하세요.
