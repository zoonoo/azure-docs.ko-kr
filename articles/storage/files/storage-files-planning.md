---
title: Azure Files 배포에 대한 계획 | Microsoft Docs
description: Azure Files 배포를 계획할 때 고려할 사항을 알아봅니다.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 1/3/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: d5bf3a6df9d7292c18a93737fb7dea5d8c91f984
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536496"
---
# <a name="planning-for-an-azure-files-deployment"></a>Azure Files 배포에 대한 계획
[Azure 파일은](storage-files-introduction.md) 서버가 없는 Azure 파일 공유를 직접 탑재하거나 Azure File Sync를 사용하여 온-프레미스에 Azure 파일 공유를 캐싱하는 두 가지 주요 방법으로 배포할 수 있습니다. 어떤 배포 옵션을 선택하든 배포를 계획할 때 고려해야 할 사항을 변경합니다. 

- **Azure 파일 공유의 직접 마운트**: Azure Files는 SMB 액세스를 제공하기 때문에 Windows, macOS 및 Linux에서 사용할 수 있는 표준 SMB 클라이언트를 사용하여 온-프레미스 또는 클라우드에 Azure 파일 공유를 탑재할 수 있습니다. Azure 파일 공유는 서버가 없기 때문에 프로덕션 시나리오에 배포할 때 파일 서버 또는 NAS 장치를 관리할 필요가 없습니다. 즉, 소프트웨어 패치를 적용하거나 실제 디스크를 교체할 필요가 없습니다. 

- **Azure 파일 동기화를 사용하여 온-프레미스에 Azure 파일 공유 캐시**: Azure File Sync를 사용하면 온-프레미스 파일 서버의 유연성, 성능 및 호환성을 유지하면서 Azure 파일에서 조직의 파일 공유를 중앙 집중화할 수 있습니다. Azure File Sync는 온-프레미스(또는 클라우드) Windows Server를 Azure 파일 공유의 빠른 캐시로 변환합니다. 

이 문서에서는 주로 온-프레미스 또는 클라우드 클라이언트에서 직접 마운트할 Azure 파일 공유를 배포하기 위한 배포 고려 사항을 다룹니다. Azure 파일 동기화 배포를 계획하려면 [Azure 파일 동기화 배포 계획을](storage-sync-files-planning.md)참조하십시오.

## <a name="management-concepts"></a>관리 개념
[!INCLUDE [storage-files-file-share-management-concepts](../../../includes/storage-files-file-share-management-concepts.md)]

Azure 파일 공유를 저장소 계정에 배포할 때 는 다음을 권장합니다.

- Azure 파일 공유를 다른 Azure 파일 공유가 있는 저장소 계정에만 배포합니다. GPv2 저장소 계정을 사용하면 용도가 혼합된 저장소 계정을 사용할 수 있지만 Azure 파일 공유 및 Blob 컨테이너와 같은 저장소 리소스가 저장소 계정의 제한을 공유하므로 리소스를 함께 혼합하면 나중에 성능 문제를 해결하기가 더 어려워질 수 있습니다. 

- Azure 파일 공유를 배포할 때 저장소 계정의 IOPS 제한에 주의합니다. 이상적으로는 저장소 계정으로 파일 공유를 1:1로 매핑하는 것이 이상적이지만 조직과 Azure의 다양한 제한 및 제한으로 인해 항상 가능하지않을 수 있습니다. 하나의 저장소 계정에 하나의 파일 공유만 배포할 수 없는 경우 가장 인기 있는 파일 공유가 동일한 저장소 계정에 함께 배치되지 않도록 활성 상태가 매우 높은 공유와 덜 활성화된 공유를 고려합니다.

- 사용자 환경에서 GPv2 및 FileStorage 계정을 배포하고 GPv1 및 클래식 저장소 계정을 업그레이드할 때만 해당 계정을 업그레이드합니다. 

## <a name="identity"></a>ID
Azure 파일 공유에 액세스하려면 파일 공유 의 사용자를 인증하고 공유에 액세스할 수 있는 권한이 있어야 합니다. 이 작업은 파일 공유에 액세스하는 사용자의 ID를 기반으로 수행됩니다. Azure Files는 세 가지 주요 ID 공급자와 통합됩니다.
- **온-프레미스 Active Directory 도메인 서비스(AD DS 또는 온-프레미스 AD DS)** (미리 보기): Azure 저장소 계정은 Windows 서버 파일 서버 또는 NAS 장치와 마찬가지로 고객 소유의 Active Directory 도메인 서비스에 도메인으로 조인될 수 있습니다. 도메인 컨트롤러를 온-프레미스, Azure VM 또는 다른 클라우드 공급자의 VM으로 배포할 수 있습니다. Azure 파일은 도메인 컨트롤러가 호스팅되는 위치에 구애받지 않습니다. 저장소 계정이 도메인 에 가입되면 최종 사용자는 PC에 로그인한 사용자 계정으로 파일 공유를 탑재할 수 있습니다. AD 기반 인증은 Kerberos 인증 프로토콜을 사용합니다.
- **Azure Active Directory 도메인 서비스(Azure AD DS)**: Azure AD DS는 Azure 리소스에 사용할 수 있는 Microsoft 관리 도메인 컨트롤러를 제공합니다. 저장소 계정을 Azure AD DS에 조인하는 도메인은 고객 소유 Active Directory에 도메인을 조인하는 것과 유사한 이점을 제공합니다. 이 배포 옵션은 AD 기반 사용 권한이 필요한 응용 프로그램 리프트 앤 시프트 시나리오에 가장 유용합니다. Azure AD DS는 AD 기반 인증을 제공하기 때문에 이 옵션은 Kerberos 인증 프로토콜도 사용합니다.
- **Azure 저장소 계정 키**: Azure 파일 공유는 Azure 저장소 계정 키와 함께 탑재될 수도 있습니다. 이러한 방식으로 파일 공유를 탑재하려면 저장소 계정 이름이 사용자 이름으로 사용되고 저장소 계정 키는 암호로 사용됩니다. 저장 저장소 계정 키를 사용하여 Azure 파일 공유를 탑재하는 것은 ACL이 있더라도 마운트된 파일 공유에 대한 모든 파일 및 폴더에 대한 전체 권한을 갖기 때문에 관리자 작업입니다. 저장소 계정 키를 사용하여 SMB 위에 탑재하는 경우 NTLMv2 인증 프로토콜이 사용됩니다.

온-프레미스 파일 서버에서 마이그레이션하거나 Windows 파일 서버 또는 NAS 어플라이언스와 같은 방식으로 행동하기 위한 Azure Files에서 새 파일 공유를 만드는 고객의 경우 저장소 계정을 **고객 소유 Active Directory에** 조인하는 것이 좋습니다. 고객 소유 Active Directory에 저장소 계정을 조인하는 도메인에 대해 자세히 알아보려면 [Azure Files Active Directory 개요를](storage-files-active-directory-overview.md)참조하십시오.

저장소 계정 키를 사용하여 Azure 파일 공유에 액세스하려는 경우 [네트워킹](#networking) 섹션에 설명된 대로 서비스 끝점을 사용하는 것이 좋습니다.

## <a name="networking"></a>네트워킹
Azure 파일 공유는 저장소 계정의 공용 끝점을 통해 어디서나 액세스할 수 있습니다. 즉, 사용자의 로그온 ID에 의해 승인된 요청과 같은 인증된 요청은 Azure 내부 또는 외부에서 안전하게 시작될 수 있습니다. 많은 고객 환경에서 Azure VM의 마운트가 성공하더라도 온-프레미스 워크스테이션에서 Azure 파일 공유의 초기 마운트가 실패합니다. 그 이유는 많은 조직과 ISP(인터넷 서비스 공급자)가 SMB가 통신하는 데 사용하는 포트를 차단하기 때문입니다. 포트 445에서 시작되는 액세스를 허용하거나 거부하는 ISP에 대한 요약을 확인하려면 [TechNet](https://social.technet.microsoft.com/wiki/contents/articles/32346.azure-summary-of-isps-that-allow-disallow-access-from-port-445.aspx)으로 이동합니다.

Azure 파일 공유에 대한 액세스를 차단 해제하려면 두 가지 주요 옵션이 있습니다.

- 조직의 온-프레미스 네트워크에 대한 포트 445 차단 을 해제합니다. Azure 파일 공유는 SMB 3.0 및 FileREST API와 같은 인터넷 안전 프로토콜을 사용하여 공용 끝점을 통해서만 외부적으로 액세스할 수 있습니다. 조직의 아웃바운드 포트 규칙을 변경하는 것 외에 고급 네트워킹 구성이 필요하지 않으므로 온-프레미스에서 Azure 파일 공유에 액세스하는 가장 쉬운 방법이지만 SMB 프로토콜의 레거시 및 더 이상 사용되지 않는 버전인 SMB 1.0을 제거하는 것이 좋습니다. 이 작업을 수행하는 방법에 대해 알아보려면 [Windows/Windows 서버 보안](storage-how-to-use-files-windows.md#securing-windowswindows-server) 및 [Linux 보안을](storage-how-to-use-files-linux.md#securing-linux)참조하십시오.

- ExpressRoute 또는 VPN 연결을 통해 Azure 파일 공유에 액세스합니다. 네트워크 터널을 통해 Azure 파일 공유에 액세스하면 SMB 트래픽이 조직 경계를 통과하지 않으므로 온-프레미스 파일 공유와 같이 Azure 파일 공유를 탑재할 수 있습니다.   

기술적 관점에서 공용 끝점을 통해 Azure 파일 공유를 탑재하는 것이 훨씬 더 쉽지만 대부분의 고객은 ExpressRoute 또는 VPN 연결을 통해 Azure 파일 공유를 탑재할 것으로 예상됩니다. 이렇게 하려면 환경에 맞게 다음을 구성해야 합니다.  

- **ExpressRoute, 사이트 간 또는 사이트 간 VPN을 사용하는 네트워크 터널링**: 가상 네트워크로 터널링하면 포트 445가 차단된 경우에도 온-프레미스에서 Azure 파일 공유에 액세스할 수 있습니다.
- **비공개 끝점**: 비공개 엔드포인트는 가상 네트워크의 주소 공간 내에서 저장소 계정에 전용 IP 주소를 제공합니다. 이렇게 하면 Azure 저장소 클러스터가 소유한 모든 IP 주소 범위까지 온-프레미스 네트워크를 열 지 않고도 네트워크 터널링을 수행할 수 있습니다. 
- **DNS 전달**: 저장소 계정(예: `storageaccount.file.core.windows.net` 퍼블릭 클라우드 리전)의 이름을 해결하여 개인 엔드포인트의 IP 주소로 확인하도록 온-프레미스 DNS를 구성합니다.

Azure 파일 공유 배포와 관련된 네트워킹을 계획하려면 [Azure Files 네트워킹 고려 사항을](storage-files-networking-overview.md)참조하십시오.

## <a name="encryption"></a>암호화
Azure Files는 Azure 파일 공유를 탑재/액세스할 때 사용되는 암호화와 관련된 전송 중인 암호화와 디스크에 데이터가 저장될 때 데이터가 암호화되는 방식과 관련된 미사용 암호화의 두 가지 유형의 암호화를 지원합니다. 

### <a name="encryption-in-transit"></a>전송 중 암호화
기본적으로 모든 Azure 스토리지 계정은 전송 중 암호화를 사용하도록 설정되어 있습니다. 즉, SMB를 통해 파일 공유를 탑재하거나 FileREST 프로토콜(예: Azure Portal, PowerShell/CLI 또는 Azure SDK)을 통해 액세스할 때 Azure Files는 암호화 또는 HTTPS를 사용하는 SMB 3.0 이상을 통해 만든 연결만 허용합니다. SMB 3.0을 지원하지 않는 클라이언트 또는 SMB 3.0을 지원하지만 SMB 암호화를 지원하지 않는 클라이언트는 전송 중 암호화를 사용하도록 설정된 경우 Azure 파일 공유를 탑재할 수 없습니다. 암호화를 통해 SMB 3.0을 지원하는 운영 체제에 대한 자세한 내용은 자세한 [Windows](storage-how-to-use-files-windows.md), [macOS](storage-how-to-use-files-mac.md) 및 [Linux](storage-how-to-use-files-linux.md)용 설명서를 참조하세요. 모든 현재 버전의 PowerShell, CLI 및 SDK는 HTTPS를 지원합니다.  

Azure 스토리지 계정에 대해 전송 중 암호화를 사용하지 않도록 설정할 수 있습니다. 암호화를 사용하지 않도록 설정하면 Azure Files는 암호화 없이 SMB 2.1, SMB 3.0, 암호화되지 않은 FileREST API 호출도 허용합니다. 전송 중 암호화를 사용하지 않도록 설정하는 주된 이유는 Windows Server 2008 R2 또는 이전 버전의 Linux 배포와 같은 이전 운영 체제에서 실행되어야 하는 레거시 애플리케이션을 지원하기 위해서입니다. Azure Files는 Azure 파일 공유와 동일한 Azure 지역 내에서만 SMB 2.1 연결을 허용합니다. Azure 파일 공유의 Azure 지역 외부(예: 온-프레미스 또는 다른 Azure 지역)에 있는 SMB 2.1 클라이언트는 파일 공유에 액세스할 수 없습니다.

전송 중 데이터의 암호화를 사용하도록 설정하는 것이 좋습니다.

전송 중 암호화에 대한 자세한 내용은 [Azure Storage에서 보안 전송 필요](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)를 참조하세요.

### <a name="encryption-at-rest"></a>휴지 상태의 암호화
[!INCLUDE [storage-files-encryption-at-rest](../../../includes/storage-files-encryption-at-rest.md)]

## <a name="storage-tiers"></a>스토리지 계층
[!INCLUDE [storage-files-tiers-overview](../../../includes/storage-files-tiers-overview.md)]

일반적으로 Azure Files 기능 및 다른 서비스와의 상호 운용성은 프리미엄 파일 공유와 표준 파일 공유 간에 동일하지만 몇 가지 중요한 차이점이 있습니다.
- **청구 모델**
    - 프리미엄 파일 공유는 프로비저닝된 청구 모델을 사용하여 청구되므로 실제로 요청하는 저장소양보다 프로비저닝한 저장소 양에 대한 비용을 지불합니다. 
    - 표준 파일 공유는 종량제 모델을 사용하여 청구되며, 여기에는 실제로 소비하는 저장소 의 수에 대한 기본 저장소 비용과 공유 사용 방법에 따라 추가 트랜잭션 비용이 포함됩니다. 표준 파일 공유를 사용하면 Azure 파일 공유를 더 많이 사용하는 경우 청구서가 증가합니다(읽기/쓰기/마운트).
- **중복 옵션**
    - 프리미엄 파일 공유는 로컬 중복(LRS) 및 영역 중복(ZRS) 저장소에만 사용할 수 있습니다. 
    - 표준 파일 공유는 로컬 중복, 영역 중복, 지역 중복(GRS) 및 지리 영역 중복(GZRS) 저장소에 사용할 수 있습니다.
- **파일 공유의 최대 크기**
    - 프리미엄 파일 공유는 추가 작업 없이 최대 100TiB에 대해 프로비전할 수 있습니다.
    - 기본적으로 표준 파일 공유는 최대 5TiB에 불과하지만 *대용량 파일 공유* 저장소 계정 기능 플래그를 선택하여 공유 제한을 100TiB로 늘릴 수 있습니다. 표준 파일 공유는 로컬 중복 또는 영역 중복 저장소 계정에 대해 최대 100TiB까지만 사용할 수 있습니다. 파일 공유 크기 증가에 대한 자세한 내용은 [큰 파일 공유 활성화 및 만들기를](https://docs.microsoft.com/azure/storage/files/storage-files-how-to-create-large-file-share)참조하십시오.
- **국가별 가용성**
    - 모든 리전에서 프리미엄 파일 공유를 사용할 수 있는 것은 아니며 영역 중복 지원은 더 작은 리전 하위 집합에서 사용할 수 있습니다. 해당 지역에서 프리미엄 파일 공유를 현재 사용할 수 있는지 확인하려면 Azure의 지역 페이지에서 [사용할 수](https://azure.microsoft.com/global-infrastructure/services/?products=storage) 있는 제품을 참조하세요. ZRS를 지원하는 지역을 확인하려면 [지역별 Azure 가용성 영역 지원을](../../availability-zones/az-overview.md#services-support-by-region)참조하십시오. 새로운 지역 및 프리미엄 계층 기능의 우선 순위를 지정하려면 이 [설문 조사를](https://aka.ms/pfsfeedback)작성하십시오.
    - 표준 파일 공유는 모든 Azure 지역에서 사용할 수 있습니다.
- Azure Kubernetes 서비스(AKS)는 버전 1.13 이상에서 프리미엄 파일 공유를 지원합니다.

파일 공유가 프리미엄 또는 표준 파일 공유로 만들어지면 자동으로 다른 계층으로 변환할 수 없습니다. 다른 계층으로 전환하려면 해당 계층에서 새 파일 공유를 만들고 원래 공유의 데이터를 만든 새 공유로 수동으로 복사해야 합니다. Windows용 `robocopy` 또는 `rsync` macOS 및 Linux용으로 해당 복사본을 수행하는 것이 좋습니다.

### <a name="understanding-provisioning-for-premium-file-shares"></a>프리미엄 파일 공유에 대한 프로비저닝 이해
프리미엄 파일 공유는 고정 GiB/IOPS/처리량 비율을 기준으로 프로비전됩니다. 공유에 프로비전되는 각 GiB당 IOPS 1개, 초당 0.1MiB의 처리량이 공유당 최대 한도까지 지급됩니다. 허용되는 최소 프로비저닝 용량은 100GiB(최소 IOPS/처리량)입니다.

최상의 노력 원칙에 따라 모든 공유는 60분 이상의 기간 동안(공유 크기별로 다름) 프로비전된 스토리지 용량(GiB)당 IOPS를 3개까지 버스트할 수 있습니다. 새 공유에는 프로비전된 용량을 기준으로 전체 버스트 크레딧이 초기 제공됩니다.

주식은 1 GiB 단위로 프로비저닝되어야 합니다. 최소 크기는 100 GiB, 다음 크기는 101 GiB 등입니다.

> [!TIP]
> 기준 IOPS = 1 * 프로비저닝된 GiB. (최대 100,000 IOPS까지).
>
> 버스트 제한 = 3 * 기준 IOPS. (최대 100,000 IOPS까지).
>
> 송신 비율 = 60 MiB/s + 0.06 * 프로비저닝된 GiB
>
> 침투 속도 = 40 MiB/s + 0.04 * 프로비저닝된 GiB

프로비저닝된 공유 크기는 공유 할당량에 따라 지정됩니다. 공유 할당량은 언제든지 늘릴 수 있지만 마지막 증가 이후 24시간 후에만 감소할 수 있습니다. 할당량 증가 없이 24시간을 기다린 후 공유 할당량을 원하는 횟수만큼 줄여 다시 늘릴 수 있습니다. IOPS/처리량 규모 변경은 크기 변경 후 몇 분 이내에 적용됩니다.

사용된 GiB 보다 낮은 프로비저닝 된 공유의 크기를 줄일 수 있습니다. 이렇게 하면 데이터가 손실되지 않지만 사용된 크기에 대한 요금이 청구되고 사용된 크기가 아닌 프로비저닝된 공유의 성능(기본 IOPS, 처리량 및 버스트 IOPS)을 수신합니다.

다음 표에서는 프로비저닝된 공유 크기에 대한 이러한 수식의 몇 가지 예를 보여 줍니다.

|용량(GiB) | 기준 IOPS | 버스트 IOPS | 출구 (MiB / s) | 인그레스 (MiB/ s) |
|---------|---------|---------|---------|---------|
|100         | 100     | 최대 300     | 66   | 44   |
|500         | 500     | 최대 1,500   | 90   | 60   |
|1,024       | 1,024   | 최대 3,072   | 122   | 81   |
|5,120       | 5,120   | 최대 15,360  | 368   | 245   |
|10,240      | 10,240  | 최대 30,720  | 675 | 450   |
|33,792      | 33,792  | 최대 100,000 | 2,088 | 1,392   |
|51,200      | 51,200  | 최대 100,000 | 3,132 | 2,088   |
|102,400     | 100,000 | 최대 100,000 | 6,204 | 4,136   |

> [!NOTE]
> 파일 공유 성능은 컴퓨터 네트워크 제한, 사용 가능한 네트워크 대역폭, IO 크기, 병렬 처리 등 여러 요인에 따라 달라질 수 있습니다. 예를 들어, 8KiB 읽기/쓰기 IO 크기, 단일 Windows 가상 머신, *표준 F16s_v2*내부 테스트를 기반으로 SMB를 통해 프리미엄 파일 공유에 연결하면 20K 읽기 IOPS 및 15K 쓰기 IOPS를 달성할 수 있습니다. 512개의 MiB 읽기/쓰기 IO 크기를 사용하면 동일한 VM이 1.1 GiB/s 송신및 370MiB/s 의 처리량을 달성할 수 있습니다. 최대 성능 규모를 달성하려면 여러 VM에 부하를 분산시입니다. 몇 [troubleshooting guide](storage-troubleshooting-files-performance.md) 가지 일반적인 성능 문제 및 해결 해결 방법을 참조하세요.

#### <a name="bursting"></a>파열
프리미엄 파일 공유는 IOPS를 최대 3배까지 버스트할 수 있습니다. 버스팅은 자동화되어 신용 시스템을 기반으로 작동합니다. 버스팅은 최선의 노력을 기울여 작동하며 버스트 제한은 보장되지 않으며 파일 공유는 *한계까지* 버스트 할 수 있습니다.

파일 공유에 대한 트래픽이 기준 IOPS 미만일 때마다 크레딧이 버스트 버킷에 누적됩니다. 예를 들어 100GiB 공유에는 100개의 기준 IOPS가 있습니다. 공유의 실제 트래픽이 특정 1초 간격동안 40 IOPS인 경우 60개의 사용되지 않는 IOPS는 버스트 버킷에 적립됩니다. 그런 다음 이러한 크레딧은 나중에 작업이 기준 IoP를 초과할 때 사용됩니다.

> [!TIP]
> 버스트 버킷 크기 = 기준 IOPS * 2 * 3600.

공유가 기준 IOPS를 초과하고 버스트 버킷에 크레딧이 있을 때마다 폭발합니다. 크레딧이 남아 있는 한 주식은 계속 터질 수 있지만, 50TiB 미만의 주식은 최대 1시간 동안만 버스트 한도에 머무를 수 있습니다. TiB가 50개를 초과하는 주식은 기술적으로 이 1시간 제한을 초과할 수 있으며 최대 2시간까지 누적된 버스트 크레딧 수를 기준으로 합니다. 기준 IOPS를 초과하는 각 IO는 하나의 크레딧을 소비하며 모든 크레딧이 소비되면 공유는 기준 IOPS로 돌아갑니다.

공유 크레딧에는 세 가지 상태가 있습니다.

- 파일 공유가 기준 IOPS 미만을 사용하는 경우 발생합니다.
- 파일 공유가 급증할 때 감소합니다.
- 크레딧또는 기준 IOPS가 사용 중인 경우 상수로 유지됩니다.

새 파일 공유는 버스트 버킷의 전체 크레딧 수로 시작합니다. 서버의 제한으로 인해 공유 IOPS가 기준 IOPS 미만으로 떨어지면 버스트 크레딧이 누적되지 않습니다.

### <a name="enable-standard-file-shares-to-span-up-to-100-tib"></a>표준 파일 공유를 최대 100TiB까지 사용할 수 있습니다.
[!INCLUDE [storage-files-tiers-enable-large-shares](../../../includes/storage-files-tiers-enable-large-shares.md)]

#### <a name="limitations"></a>제한 사항
[!INCLUDE [storage-files-tiers-large-file-share-availability](../../../includes/storage-files-tiers-large-file-share-availability.md)]

## <a name="redundancy"></a>중복
[!INCLUDE [storage-files-redundancy-overview](../../../includes/storage-files-redundancy-overview.md)]

## <a name="migration"></a>마이그레이션
대부분의 경우 조직에 대한 새 파일 공유를 설정하는 대신 온-프레미스 파일 서버 또는 NAS 장치에서 Azure Files로 기존 파일 공유를 마이그레이션합니다. Microsoft와 제3자 모두 파일 공유로 마이그레이션을 수행하는 데 필요한 많은 도구가 있지만 대략 두 가지 범주로 나눌 수 있습니다.

- **ACL 및 타임스탬프와 같은 파일 시스템 특성을 유지하는 도구:**
    - **[Azure 파일 동기화](storage-sync-files-planning.md)**: Azure 파일 동기화는 원하는 최종 배포가 온-프레미스 존재를 유지하지 않는 경우에도 Azure 파일 공유에 데이터를 통합하는 방법으로 사용할 수 있습니다. Azure 파일 동기화는 기존 Windows Server 2012 R2, Windows Server 2016 및 Windows Server 2019 배포에 설치할 수 있습니다. Azure File Sync를 인제스트 메커니즘으로 사용하는 이점은 최종 사용자가 기존 파일 공유를 계속 사용할 수 있다는 것입니다. 모든 데이터가 백그라운드에서 업로드를 완료한 후 Azure 파일 공유로 잘라낼 수 있습니다.
    - **[로보카피](https://technet.microsoft.com/library/cc733145.aspx)**: 로보카피는 윈도우와 윈도우 서버와 함께 제공되는 잘 알려진 복사 도구입니다. 파일 공유를 로컬로 탑재하고 탑재된 위치를 Robocopy 명령의 대상으로 사용하는 방식으로 Robocopy를 사용하여 데이터를 Azure Files로 전송할 수 있습니다.

- **파일 시스템 특성을 유지 하지 않는 도구:**
    - **데이터 상자**: 데이터 상자는 Azure로 물리적 인 선박 데이터에 오프라인 데이터 전송 메커니즘을 제공합니다. 이 메서드는 처리량을 늘리고 대역폭을 절약하도록 설계되었지만 현재 타임스탬프 및 ACL과 같은 파일 시스템 특성을 지원하지 는 않습니다.
    - **[AzCopy](../common/storage-use-azcopy-v10.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)**: AzCopy는 간단한 명령과 최적의 성능으로 데이터를 Azure Files 및 Azure Blob Storage에서 복사하도록 디자인된 명령줄 유틸리티입니다.

## <a name="next-steps"></a>다음 단계
* [Azure 파일 동기화 배포 계획](storage-sync-files-planning.md)
* [Azure Files 배포](storage-files-deployment-guide.md)
* [Azure 파일 동기화 배포](storage-sync-files-deployment-guide.md)
