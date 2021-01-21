---
title: Azure Files 대금 청구 이해 | Microsoft Docs
description: Azure 파일 공유에 대해 프로 비전 된 종 량 제 청구 모델을 해석 하는 방법에 대해 알아봅니다.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 01/20/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 19ecbea70d9cb6b8cc31c72ed3c1294cd137ce93
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/21/2021
ms.locfileid: "98632481"
---
# <a name="understanding-azure-files-billing"></a>Azure Files 청구 이해
Azure Files는 프로 비전 되 고 종 량 제 인 두 개의 고유한 청구 모델을 제공 합니다. 프로 비전 된 모델은 **FileStorage** storage 계정 종류에 배포 된 파일 공유 인 프리미엄 파일 공유에만 사용할 수 있습니다. 종 량 제 모델은 **GPv2 (범용 버전 2)** 저장소 계정 종류에 배포 된 파일 공유 인 표준 파일 공유에만 사용할 수 있습니다. 이 문서에서는 월간 Azure Files 청구서를 이해 하는 데 도움이 되도록 두 모델의 작동 방식을 설명 합니다.

Azure Files에 대 한 현재 가격은 [Azure Files 가격 책정 페이지](https://azure.microsoft.com/pricing/details/storage/files/)에서 찾을 수 있습니다.

## <a name="provisioned-model"></a>프로 비전 된 모델
Azure Files는 프리미엄 파일 공유에 프로 비전 된 모델을 사용 합니다. 프로 비전 된 비즈니스 모델에서 사용 하는 내용에 따라 비용이 청구 되는 것이 아니라 Azure Files 서비스에 저장소 요구 사항을 사전에 지정 합니다. 이는 온-프레미스에서 하드웨어를 구입 하는 것과 유사 합니다. 즉, 특정 양의 저장소로 Azure 파일 공유를 프로 비전 할 때 공간 사용을 시작 하는 경우 온-프레미스에서 실제 미디어의 비용을 지불 하지 않는 것과 마찬가지로 사용 여부에 관계 없이 해당 저장소에 대 한 비용을 지불 합니다. 온-프레미스에서 물리적 미디어를 구매 하는 것과 달리 프로 비전 된 파일 공유는 저장소 및 IO 성능 특성에 따라 동적으로 확장 또는 축소할 수 있습니다.

프리미엄 파일 공유를 프로 비전 할 때 워크 로드에 필요한 Gid의 수를 지정 합니다. 프로 비전 하는 각 GiB는 고정 비율에 대 한 추가 IOPS 및 처리량을 되며 합니다. 보장 되는 기본 IOPS 외에도 각 프리미엄 파일 공유는 최상의 노력을 기반으로 버스트를 지원 합니다. IOPS 및 처리량에 대 한 수식은 다음과 같습니다.

- 기준 IOPS = 400 + 1 * 프로 비전 된 GiB. (최대 10만 IOPS).
- 버스트 제한 = 최대 (4000, 3 * 기준 IOPS).
- 송신 율 = 60 MiB/s + 0.06 * 프로 비전 된 GiB.
- 수신 율 = 40 MiB/s + 0.04 * 프로 비전 된 GiB.

프로 비전 된 파일 공유 크기는 언제 든 지 늘릴 수 있지만 마지막 증가 이후 24 시간 후에만 감소할 수 있습니다. 할당량 증가 없이 24 시간 동안 기다린 후에는 다시 늘릴 때까지 공유 할당량을 원하는 횟수 만큼 줄일 수 있습니다. IOPS/처리량 크기 변경은 프로 비전 된 크기가 변경 된 후 몇 분 내에 적용 됩니다.

사용 된 GiB에서 프로 비전 된 공유의 크기를 줄일 수 있습니다. 이 작업을 수행 하는 경우 데이터가 손실 되지 않지만 사용 된 크기에 대 한 요금이 청구 되며, 사용 되는 크기가 아니라 프로 비전 된 공유의 성능 (기준선 IOPS, 처리량 및 버스트 IOPS)을 수신 하 게 됩니다.

다음 표에서는 프로 비전 된 공유 크기에 대 한 이러한] 열의 공식을의 몇 가지 예를 보여 줍니다.

|용량 (GiB) | 기준 IOPS | 버스트 IOPS | 송신 (MiB/s) | 수신 (MiB/s) |
|---------|---------|---------|---------|---------|
|100         | 500     | 최대 4,000     | 66   | 44   |
|500         | 900     | 최대 4,000  | 90   | 60   |
|1,024       | 1424   | 최대 4,000   | 122   | 81   |
|5,120       | 5520   | 최대 15360  | 368   | 245   |
|10240      | 10640  | 최대 30720  | 675   | 450   |
|33792      | 34192  | 최대 10만 | 2088 | 1392   |
|51200      | 51600  | 최대 10만 | 3,132 | 2088   |
|102400     | 100,000 | 최대 10만 | 6204 | 4136   |

효과적인 파일 공유 성능에는 컴퓨터 네트워크 제한, 사용 가능한 네트워크 대역폭, IO 크기, 병렬 처리 등 여러 가지 요인이 적용 됩니다. 예를 들어, KiB 읽기/쓰기 IO 크기가 8 인 내부 테스트를 기반으로 smb 다중 채널을 사용 하지 않는 단일 Windows 가상 머신, *표준 F16s_v2* 는 smb를 통한 프리미엄 파일 공유에 연결 되어 있습니다. 512 MiB 읽기/쓰기 IO 크기를 사용 하는 경우 동일한 VM이 1.1 GiB/s 송신 및 370 MiB/s 수신 처리량을 달성할 수 있습니다. \~프리미엄 공유에서 SMB 다중 채널을 사용 하는 경우 동일한 클라이언트에서 최대 3 배까지 성능을 달성할 수 있습니다. 최대 성능 확장을 실현 하려면 [SMB 다중 채널을 사용 하도록 설정](storage-files-enable-smb-multichannel.md) 하 고 부하를 여러 vm에 분산 합니다. 몇 가지 일반적인 성능 문제 및 해결 방법에 대해서는 [SMB 다중 채널 성능](storage-files-smb-multichannel-performance.md) 및 [문제 해결 가이드](storage-troubleshooting-files-performance.md) 를 참조 하세요.

### <a name="bursting"></a>화
최대 수요를 충족 하기 위해 워크 로드에 추가 성능이 필요한 경우 공유에서 버스트 크레딧을 사용 하 여 수요를 충족 하는 데 필요한 공유 성능을 제공할 수 있습니다. 프리미엄 파일 공유는 IOPS를 4000로 버스트 하거나 최대 3 배까지 증가 시킬 수 있습니다. 버스트는 자동화 되며 신용 시스템을 기반으로 작동 합니다. 버스트는 최상의 노력을 기반으로 하며 버스트 제한은 보장 되지 않으며, 파일 공유는 최대 기간을 60 분 *으로* 제한할 수 있습니다.

크레딧은 파일 공유에 대 한 트래픽이 기준선 IOPS 미만이 될 때마다 버스트 버킷에 누적 됩니다. 예를 들어 100 GiB 공유에는 500 기준선 IOPS가 있습니다. 공유의 실제 트래픽이 1 초 간격으로 100 IOPS 인 경우 400 사용 하지 않는 IOPS는 버스트 버킷으로 제공한 됩니다. 마찬가지로 유휴 1 TiB 공유 하 고 1424 IOPS에서 버스트 크레딧을 발생 합니다. 이러한 크레딧은 나중에 작업이 기준선 IOPS를 초과 하는 경우에 사용 됩니다.

공유가 기준선 IOPS를 초과 하 고 버스트 버킷에 크레딧을 가질 때마다 최대 허용 버스트 속도로 버스트 됩니다. 크레딧이 남아 있는 한, 최대 60 분 동안 공유가 지속 될 수 있지만,이는 발생 한 버스트 크레딧 수를 기준으로 합니다. 기준 IOPS를 초과 하는 각 IO는 하나의 크레딧을 사용 하며, 모든 크레딧이 사용 되 면 해당 공유는 기준선 IOPS로 돌아옵니다.

공유 크레딧에는 세 가지 상태가 있습니다.

- 발생 파일 공유에서 기준선 IOPS 보다 작은 값을 사용 하는 경우입니다.
- 거절-파일 공유에서 기준선 IOPS 및 버스트 모드를 사용 하는 경우
- 상수, 파일 공유에서 정확 하 게 기준선 IOPS를 사용 하는 경우 발생 하는 크레딧이 없거나 사용 되지 않습니다.

새 파일 공유는 버스트 버킷의 전체 크레딧 수로 시작 합니다. 서버 제한으로 인해 공유 IOPS가 기준선 IOPS 미만이 면 버스트 크레딧을 계산 하지 않습니다.

## <a name="pay-as-you-go-model"></a>종 량 제 모델
Azure Files는 표준 파일 공유에 종 량 제 비즈니스 모델을 사용 합니다. 종 량 제 비즈니스 모델에서 요금을 지불 하는 금액은 프로 비전 된 금액을 기반으로 하는 것이 아니라 실제로 사용한 양에 따라 결정 됩니다. 높은 수준에서 디스크에 저장 된 데이터의 양에 대 한 비용을 지불 하 고 해당 데이터 사용량에 따라 추가 트랜잭션 집합을 지불 합니다. 종 량 제 모델은 향후 성장 또는 성능 요구 사항을 고려 하 여 과도 하 게 프로 비전 할 필요가 없으며, 워크 로드가 시간에 따라 달라 지는 경우 프로 비전을 해제할 필요가 없기 때문에 비용 효율적일 수 있습니다. 반면에 종 량 제 청구 모델은 최종 사용자가 사용 하기 때문에 종 량 제 모델을 계획 하는 과정에서 계획 하기 어려울 수도 있습니다.

### <a name="differences-in-standard-tiers"></a>표준 계층의 차이점
표준 파일 공유를 만들 때 트랜잭션 최적화, 핫 및 쿨 계층 중에서 선택할 수 있습니다. 3 개의 계층은 모두 정확히 동일한 표준 저장소 하드웨어에 저장 됩니다. 이러한 세 계층의 주요 차이점은 더 높은 쿨 계층에서 저장 데이터 스토리지 가격이 더 낮고, 트랜잭션 가격이 더 높다는 것입니다. 이는 다음을 의미합니다.

- 트랜잭션 최적화는 이름에서 암시하듯이 높은 트랜잭션 워크로드에 대한 가격을 최적화합니다. 트랜잭션 최적화의 경우 저장 데이터 스토리지 가격이 가장 높지만 트랜잭션 가격은 가장 낮습니다.
- 핫은 많은 수의 트랜잭션이 포함되지 않은 활성 워크로드에 적합하며, 저장 데이터 스토리지 가격이 약간 낮지만 트랜잭션 가격은 트랜잭션 최적화에 비해 약간 더 높습니다. 이를 트랜잭션 최적화 된 계층과 쿨 계층 간의 중간으로 생각 합니다.
- 쿨은 작업량이 많지 않은 워크 로드에 대 한 가격을 최적화 하 여 가장 낮은 데이터를 미사용 저장소 가격으로 제공 하지만 최고 트랜잭션 가격은 제공 합니다.

트랜잭션 최적화 계층에서 자주 액세스 하지 않는 작업을 수행 하는 경우에는 한 달에 몇 번의 시간 동안 공유에 대해 트랜잭션을 수행 하지만 데이터 저장 비용을 많이 지불 하 게 됩니다. 이 동일한 공유를 쿨 계층으로 이동 하는 경우에도 트랜잭션 비용에 대해 거의 아무것도 하지 않아도 되지만,이 워크 로드에 대 한 트랜잭션은 매우 드물게 발생 하지만 쿨 계층의 데이터 저장 가격은 훨씬 저렴 합니다. 사용 사례에 적합 한 계층을 선택 하면 비용을 크게 줄일 수 있습니다. 사용 사례에 적합 한 계층을 선택 하면 비용을 크게 줄일 수 있습니다.

마찬가지로 쿨 계층에서 매우 액세스 된 워크 로드를 설정 하는 경우에는 트랜잭션 비용을 훨씬 더 많이 지불 하 고 데이터 저장 비용을 줄일 수 있습니다. 이로 인해 트랜잭션 가격에서 늘어난 비용이 감소 된 데이터 저장 가격의 절감 보다 증가 하는 상황이 발생할 수 있으며,이로 인해 트랜잭션 최적화에 대 한 것 보다 더 많은 비용을 지불할 수 있습니다. 일부 사용 수준에서는 핫 계층이 가장 비용 효율적인 계층이 될 수 있지만 쿨 계층은 트랜잭션 최적화 보다 비용이 더 많이 듭니다.

워크로드 및 작업 수준에 따라 표준 파일 공유에 가장 비용 효율적인 계층이 결정됩니다. 실제로 가장 비용 효율적인 계층을 선택 하는 가장 좋은 방법은 공유의 실제 리소스 사용량 (저장 된 데이터, 쓰기 트랜잭션 등)을 살펴보는 것입니다.

### <a name="what-are-transactions"></a>트랜잭션 이란?
트랜잭션은 파일 공유의 내용을 업로드, 다운로드 또는 조작 하는 Azure Files에 대 한 작업 또는 요청입니다. 파일 공유에서 수행 되는 모든 작업은 하나 이상의 트랜잭션으로 변환 되며, 종 량 제 청구 모델을 사용 하는 표준 공유에서는 트랜잭션 비용으로 변환 됩니다.

작성, 나열, 읽기, 기타 및 삭제의 5 가지 기본 트랜잭션 범주가 있습니다. REST API 또는 SMB를 통해 수행 되는 모든 작업은 다음과 같이 이러한 4 가지 범주 중 하나로 버킷 팅 됩니다.

| 작업 유형 | 쓰기 트랜잭션 | 트랜잭션 나열 | 읽기 트랜잭션 | 기타 트랜잭션 | 트랜잭션 삭제 |
|-|-|-|-|-|-|
| 관리 작업 | <ul><li>`CreateShare`</li><li>`SetFileServiceProperties`</li><li>`SetShareMetadata`</li><li>`SetShareProperties`</li></ul> | <ul><li>`ListShares`</li></ul> | <ul><li>`GetFileServiceProperties`</li><li>`GetShareAcl`</li><li>`GetShareMetadata`</li><li>`GetShareProperties`</li><li>`GetShareStats`</li></ul> | | <ul><li>`DeleteShare`</li></ul> |
| 데이터 작업 | <ul><li>`CopyFile`</li><li>`Create`</li><li>`CreateDirectory`</li><li>`CreateFile`</li><li>`PutRange`</li><li>`PutRangeFromURL`</li><li>`SetDirectoryMetadata`</li><li>`SetFileMetadata`</li><li>`SetFileProperties`</li><li>`SetInfo`</li><li>`SetShareACL`</li><li>`Write`</li><li>`PutFilePermission`</li></ul> | <ul><li>`ListFileRanges`</li><li>`ListFiles`</li><li>`ListHandles`</li></ul>  | <ul><li>`FilePreflightRequest`</li><li>`GetDirectoryMetadata`</li><li>`GetDirectoryProperties`</li><li>`GetFile`</li><li>`GetFileCopyInformation`</li><li>`GetFileMetadata`</li><li>`GetFileProperties`</li><li>`QueryDirectory`</li><li>`QueryInfo`</li><li>`Read`</li><li>`GetFilePermission`</li></ul> | <ul><li>`AbortCopyFile`</li><li>`Cancel`</li><li>`ChangeNotify`</li><li>`Close`</li><li>`Echo`</li><li>`Ioctl`</li><li>`Lock`</li><li>`Logoff`</li><li>`Negotiate`</li><li>`OplockBreak`</li><li>`SessionSetup`</li><li>`TreeConnect`</li><li>`TreeDisconnect`</li><li>`CloseHandles`</li><li>`AcquireFileLease`</li><li>`BreakFileLease`</li><li>`ChangeFileLease`</li><li>`ReleaseFileLease`</li></ul> | <ul><li>`ClearRange`</li><li>`DeleteDirectory`</li></li>`DeleteFile`</li></ul> |

> [!Note]  
> NFS 4.1은 프로 비전 된 청구 모델을 사용 하는 프리미엄 파일 공유에만 사용할 수 있으며, 트랜잭션은 프리미엄 파일 공유에 대 한 청구에 영향을 주지 않습니다.

## <a name="see-also"></a>참고 항목
- [가격 책정 페이지를 Azure Files](https://azure.microsoft.com/pricing/details/storage/files/)합니다.
- [Azure Files 배포를 계획](./storage-files-planning.md) 하 고 [Azure 파일 동기화 배포를 계획](./storage-sync-files-planning.md)합니다.
- [파일 공유를 만들고](./storage-how-to-create-file-share.md) [Azure 파일 동기화를 배포](./storage-sync-files-deployment-guide.md)합니다.