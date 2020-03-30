---
title: 윈도우 가상 데스크톱 FSLogix 프로필 컨테이너 파일 - Azure
description: 이 문서에서는 Windows 가상 데스크톱 및 Azure 파일 내의 FSLogix 프로필 컨테이너에 대해 설명합니다.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/07/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 1dc5d54fa24217c91e14a8f37e092888b2bb6474
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127886"
---
# <a name="fslogix-profile-containers-and-azure-files"></a>FSLogix 프로필 컨테이너 및 Azure Files

Windows 가상 데스크톱 서비스는 사용자 프로필 솔루션으로 FSLogix 프로필 컨테이너를 권장합니다. FSLogix는 Windows 가상 데스크톱과 같은 원격 컴퓨팅 환경에서 프로파일을 로밍하도록 설계되었습니다. 전체 사용자 프로필을 단일 컨테이너에 저장합니다. 로그인시 이 컨테이너는 기본적으로 지원되는 VHD(가상 하드 디스크) 및 하이퍼-V 가상 하드 디스크(VHDX)를 사용하여 컴퓨팅 환경에 동적으로 연결됩니다. 사용자 프로필을 즉시 사용할 수 있으며 기본 사용자 프로필과 정확히 같은 시스템에 나타납니다. 이 문서에서는 Windows 가상 데스크톱에서 Azure Files와 함께 사용되는 FSLogix 프로필 컨테이너가 어떻게 작동하는지 설명합니다.

>[!NOTE]
>Azure의 다른 FSLogix 프로필 컨테이너 저장소 옵션에 대한 비교 자료를 찾고 있는 경우 [FSLogix 프로필 컨테이너에 대한 저장소 옵션을](store-fslogix-profile.md)참조하십시오.

## <a name="user-profiles"></a>사용자 프로필

사용자 프로필에는 데스크톱 설정, 영구 네트워크 연결 및 응용 프로그램 설정과 같은 구성 정보를 포함하여 개인에 대한 데이터 요소가 포함되어 있습니다. 기본적으로 Windows는 운영 체제와 긴밀하게 통합된 로컬 사용자 프로필을 만듭니다.

원격 사용자 프로필은 사용자 데이터와 운영 체제 간의 파티션을 제공합니다. 사용자 데이터에 영향을 주지 않고 운영 체제를 교체하거나 변경할 수 있습니다. RDSH(원격 데스크톱 세션 호스트) 및 VDI(가상 데스크톱 인프라)에서는 다음과 같은 이유로 운영 체제를 교체할 수 있습니다.

- 운영 체제의 업그레이드
- 기존 가상 컴퓨터(VM)의 교체
- 풀린(비영구) RDSH 또는 VDI 환경의 일부인 사용자

Microsoft 제품은 다음과 같은 기술을 포함하여 원격 사용자 프로필에 대한 여러 기술로 작동합니다.
- 로밍 사용자 프로필(RUP)
- 사용자 프로필 디스크(UPD)
- 엔터프라이즈 상태 로밍(ESR)

UPD 및 RUP는 RDSH(원격 데스크톱 세션 호스트) 및 VHD(가상 하드 디스크) 환경에서 사용자 프로필에 가장 널리 사용되는 기술입니다.

### <a name="challenges-with-previous-user-profile-technologies"></a>이전 사용자 프로필 기술의 과제

사용자 프로필에 대한 기존 및 기존 Microsoft 솔루션은 다양한 과제를 안고 있습니다. 이전 솔루션은 RDSH 또는 VDI 환경과 함께 제공되는 모든 사용자 프로필 요구 사항을 처리하지 않았습니다. 예를 들어 UPD는 대용량 OST 파일을 처리할 수 없으며 RUP는 최신 설정을 유지하지 않습니다.

#### <a name="functionality"></a>기능

다음 표에서는 이전 사용자 프로필 기술의 이점과 제한 사항이 보여 주어 있습니다.

| 기술 | 최신 설정 | Win32 설정 | OS 설정 | 사용자 데이터 | 서버 SKU에서 지원 | Azure의 백 엔드 스토리지 | 온-프레미스 백 엔드 스토리지 | 버전 지원 | 후속 로그인 시간 |메모|
| ---------- | :-------------: | :------------: | :---------: | --------: | :---------------------: | :-----------------------: | :--------------------------: | :-------------: | :---------------------: |-----|
| **사용자 프로필 디스크(UPD)** | yes | yes | yes | yes | yes | 예 | yes | 승리 7+ | yes | |
| **로밍 사용자 프로필(RUP), 유지 관리 모드** | 예 | yes | yes | yes | yes| 예 | yes | 승리 7+ | 예 | |
| **엔터프라이즈 상태 로밍(ESR)** | yes | 예 | yes | 예 | 참고 사항 보기 | yes | 예 | Win 10 | 예 | 서버 SKU의 기능이지만 지원되는 사용자 인터페이스가 없습니다. |
| **사용자 경험 가상화(UE-V)** | yes | yes | yes | 예 | yes | 예 | yes | 승리 7+ | 예 |  |
| **원 드라이브 클라우드 파일** | 예 | 예 | 예 | yes | 참고 사항 보기 | 참고 사항 보기  | 참고 사항 보기 | 승리 10 RS3 | 예 | 서버 SKU에서 테스트되지 않았습니다. Azure의 백 엔드 저장소는 동기화 클라이언트에 따라 다릅니다. 온-프레임백 엔드 스토리지에는 동기화 클라이언트가 필요합니다. |

#### <a name="performance"></a>성능

UPD는 성능 요구 사항을 해결하기 위해 [S2D(스토리지 스페이스 Direct)가](/windows-server/remote/remote-desktop-services/rds-storage-spaces-direct-deployment/) 필요합니다. UPD는 SMB(서버 메시지 블록) 프로토콜을 사용합니다. 사용자가 기록되는 VM에 프로필을 복사합니다. S2DUPD는 우리가 윈도우 가상 데스크톱에 권장하는 솔루션입니다.  

#### <a name="cost"></a>비용

S2D 클러스터는 필요한 성능을 달성하지만 기업 고객에게는 비용이 많이 들지만 중소기업(SMB) 고객에게는 특히 비용이 많이 듭니다. 이 솔루션의 경우 기업은 공유를 위해 디스크를 사용하는 VM의 비용과 함께 저장소 디스크에 대한 비용을 지불합니다.

#### <a name="administrative-overhead"></a>관리 오버헤드

S2D 클러스터에는 패치, 업데이트 및 보안 상태로 유지 관리되는 운영 체제가 필요합니다. 이러한 프로세스와 S2D 재해 복구 설정의 복잡성으로 인해 S2D는 전담 IT 직원이 있는 기업에서만 가능합니다.

## <a name="fslogix-profile-containers"></a>FSLogix 프로파일 컨테이너

2018년 11월 19일, [마이크로소프트는 FSLogix를 인수했다.](https://blogs.microsoft.com/blog/2018/11/19/microsoft-acquires-fslogix-to-enhance-the-office-365-virtualization-experience/) FSLogix는 많은 프로필 컨테이너 문제를 해결합니다. 그 중 핵심은 다음과 같습니다.

- **성능:** [FSLogix 프로필 컨테이너는](/fslogix/configure-profile-container-tutorial/) 고성능이며 캐시된 교환 모드를 차단한 성능 문제를 해결합니다.
- **원 드라이브:** FSLogix 프로필 컨테이너가 없으면 비즈니스용 OneDrive가 비영구 RDSH 또는 VDI 환경에서 지원되지 않습니다. [비즈니스용 OneDrive 및 FSLogix 모범 사례는](/fslogix/overview/) 상호 작용하는 방법을 설명합니다. 자세한 내용은 [가상 데스크톱에서 동기화 클라이언트 사용을](/deployoffice/rds-onedrive-business-vdi/)참조하십시오.
- **추가 폴더:** FSLogix는 추가 폴더를 포함하도록 사용자 프로필을 확장하는 기능을 제공합니다.

인수 이후 Microsoft는 UPD와 같은 기존 사용자 프로필 솔루션을 FSLogix 프로필 컨테이너로 교체하기 시작했습니다.

## <a name="azure-files-integration-with-azure-active-directory-domain-service"></a>Azure Active 디렉터리 도메인 서비스와Azure 파일 통합

FSLogix 프로파일 컨테이너의 성능과 기능은 클라우드를 활용합니다. 2019년 8월 7일, Microsoft Azure Files는 [Azure Active Directory 도메인 서비스(AD DS)를 통해 Azure 파일 인증의](../storage/files/storage-files-active-directory-overview.md)일반 가용성을 발표했습니다. 비용 및 관리 오버헤드를 모두 해결하여 Azure AD DS 인증을 사용하는 Azure 파일은 Windows 가상 데스크톱 서비스의 사용자 프로필에 대한 프리미엄 솔루션입니다.

## <a name="best-practices-for-windows-virtual-desktop"></a>Windows 가상 데스크톱에 대한 모범 사례

Windows 가상 데스크톱은 고객이 사용하는 VM의 크기, 유형 및 개수를 완전히 제어합니다. 자세한 내용은 [Windows 가상 데스크톱이란 무엇입니까?](overview.md)

Windows 가상 데스크톱 환경이 모범 사례를 따르도록 하려면 다음을 수행하십시오.

- Azure Files 저장소 계정은 세션 호스트 VM과 동일한 지역에 있어야 합니다.
- Azure 파일 사용 권한은 [요구 사항 - 프로필 컨테이너에](/fslogix/fslogix-storage-config-ht)설명된 권한과 일치해야 합니다.
- 각 호스트 풀은 동일한 마스터 이미지를 기반으로 동일한 유형 및 크기 VM으로 빌드되어야 합니다.
- 각 호스트 풀 VM은 관리, 확장 및 업데이트를 지원하기 위해 동일한 리소스 그룹에 있어야 합니다.
- 최적의 성능을 위해 스토리지 솔루션과 FSLogix 프로필 컨테이너는 동일한 데이터 센터 위치에 있어야 합니다.
- 마스터 이미지가 포함된 저장소 계정은 VM이 프로비전되는 동일한 리전 및 구독에 있어야 합니다.

## <a name="next-steps"></a>다음 단계

다음 가이드를 사용하여 Windows 가상 데스크톱 환경을 설정합니다.

- 데스크톱 가상화 솔루션을 빌드하려면 [Windows 가상 데스크톱에서 테넌트 만들기를](tenant-setup-azure-active-directory.md)참조하십시오.
- Windows 가상 데스크톱 테넌트 내에서 호스트 풀을 만들려면 [Azure Marketplace를 사용하여 호스트 풀 만들기를](create-host-pools-azure-marketplace.md)참조하십시오.
- 클라우드에서 완전히 관리되는 파일 공유를 설정하려면 [Azure Files 공유 설정을](/azure/storage/files/storage-files-active-directory-enable/)참조하십시오.
- FSLogix 프로필 컨테이너를 구성하려면 [파일 공유를 사용하여 호스트 풀에 대한 프로필 컨테이너 만들기를](create-host-pools-user-profile.md)참조하십시오.
- 호스트 풀에 사용자를 할당하려면 [Windows 가상 데스크톱용 앱 그룹 관리를](manage-app-groups.md)참조하십시오.
- 웹 브라우저에서 Windows 가상 데스크톱 리소스에 액세스하려면 [Windows 가상 데스크톱에 연결 을](connect-web.md)참조하십시오.
