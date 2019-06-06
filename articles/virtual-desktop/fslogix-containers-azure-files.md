---
title: FSLogix 프로필 컨테이너 및 Windows 가상 데스크톱-Azure에서에서 Azure Files
description: 이 문서에서는 Windows 가상 데스크톱 및 Azure 파일 내의 FSLogix 프로필 컨테이너를 설명 합니다.
services: virtual-desktop
author: ChJenk
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 05/16/2019
ms.author: v-chjenk
ms.openlocfilehash: b3032aa796b3c79572bbf8b2beb85efc252ff73b
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66497528"
---
# <a name="fslogix-profile-containers-and-azure-files"></a>FSLogix 프로필 컨테이너 및 Azure Files

Windows 가상 데스크톱 미리 보기 서비스는 사용자 프로필 솔루션으로 FSLogix 프로필 컨테이너를 권장합니다. Windows 가상 데스크톱 등의 원격 컴퓨팅 환경에서 프로필을 로밍할 수 FSLogix 설계 되었습니다. 완전 한 사용자 프로필을 단일 컨테이너에 저장합니다. 로그인에이 컨테이너는 기본적으로 지원 되는 가상 하드 디스크 (VHD) 및 Hyper-v 가상 하드 디스크 (VHDX)를 사용 하 여 컴퓨팅 환경에 동적으로 연결 됩니다. 사용자 프로필 즉시 사용할 수 있습니다 및 기본 사용자 프로필을 똑같이 시스템에 표시 됩니다.

이 문서에서는 Azure Files를 사용 하는 FSLogix 프로필 컨테이너 설명 하겠습니다. 정보는 기능인 Windows 가상 데스크톱의 컨텍스트에서 [3 월 21에서 이전에 발표](https://www.microsoft.com/microsoft-365/blog/2019/03/21/windows-virtual-desktop-public-preview/)합니다.

## <a name="user-profiles"></a>사용자 프로필

사용자 프로필을 바탕 화면 설정, 영구적인 네트워크 연결 등 응용 프로그램 설정 구성 정보를 포함 하 여 개별에 대 한 데이터 요소를 포함 합니다. 기본적으로 Windows 운영 체제와 긴밀 하 게 통합 된 로컬 사용자 프로필을 만듭니다.

원격 사용자 프로필 사용자 데이터 및 운영 체제 간에 파티션을 제공합니다. 운영 체제를 대체 하거나 사용자 데이터를 영향을 주지 않고 변경할 수 있습니다. 세션 호스트 RDSH (원격 데스크톱) 및 가상 데스크톱 인프라 (VDI)에서 운영 체제는 다음과 같은 이유로 대체 될 수 있습니다.

- 운영 체제 업그레이드
- 기존 가상 머신 (VM)의 대체
- 풀링된 (비영구) RDSH 또는 VDI 환경에 포함 되는 사용자

Microsoft 제품 원격 사용자 프로필의 경우 이러한 기술을 비롯 한 여러 가지 기술을 사용 하 여 작동 합니다.
- 로밍 사용자 프로필 (RUP)
- 사용자 프로필 디스크 (UPD)
- 엔터프라이즈 상태 로밍 (ESR)

UPD RUP와 세션 호스트 RDSH (원격 데스크톱) 및 가상 하드 디스크 (VHD) 환경에서 사용자 프로필에 대 한 가장 널리 사용 되는 기술입니다.

### <a name="challenges-with-previous-user-profile-technologies"></a>이전 사용자 프로필 기술을 통해 과제

다양 한 챌린지를 사용 하 여 사용자 프로필에 대 한 기존 솔루션과 레거시 Microsoft 솔루션 제공 되었습니다. 이전 솔루션이 없는 RDSH 또는 VDI 환경을 함께 제공 되는 모든 사용자 프로필 요구를 처리 합니다. 예를 들어, UPD 큰 OST 파일을 처리할 수 없습니다 및 RUP 최신 설정이 유지 되지 않습니다.

#### <a name="functionality"></a>기능

다음 표에서 이전 사용자 프로필 기술의 장단점을 보여 줍니다.

| 기술 | 최신 설정 | Win32 설정 | OS 설정 | 사용자 데이터 | 서버 SKU에서 지원 | Azure에서 백 엔드 저장소 | 백 엔드 저장소 온-프레미스 | 버전 지원 | 후속 로그인 시간 |메모|
| ---------- | :-------------: | :------------: | :---------: | --------: | :---------------------: | :-----------------------: | :--------------------------: | :-------------: | :---------------------: |-----|
| **사용자 프로필 디스크 (UPD)** | 예 | 예 | 예 | 예 | 예 | no | 예 | Win 7 이상 | 예 | |
| **로밍 사용자 프로필 (RUP)를 유지 관리 모드** | 아닙니다. | 예 | 예 | 예 | 예| no | 예 | Win 7 이상 | 아닙니다. | |
| **엔터프라이즈 상태 로밍 (ESR)** | 예 | no | 사용자 계정 컨트롤 | 아닙니다. | 참고 사항 보기 | 예 | 아닙니다. | Win 10 | 아닙니다. | 지원 사용자 인터페이스가 없는 서버 SKU에서 함수 |
| **User Experience Virtualization (Ue-v)** | 예 | 예 | 예 | no | 사용자 계정 컨트롤 | no | 예 | Win 7 이상 | 아닙니다. |  |
| **OneDrive 클라우드 파일** | 아닙니다. | 아니요 | 아니요 | 예 | 참고 사항 보기 | 참고 사항 보기  | 정보를 참조 하세요. | Win 10 RS3 | 아닙니다. | 서버 SKU에서 테스트 되지 않았습니다. Azure에서 백 엔드 저장소 동기화 클라이언트에 따라 달라 집니다. 백 엔드 저장소 온-프레미스 클라이언트를 동기화 해야합니다. |

#### <a name="performance"></a>성능

UPD 필요 [저장소 공간 다이렉트 (s2d)](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-storage-spaces-direct-deployment) 성능 요구 사항을 해결 하려면. UPD 서버 메시지 블록 (SMB) 프로토콜을 사용합니다. 프로필에 사용자가 로그는 VM에 복사 합니다. S2D 사용 하 여 UPD는 RDS 팀 서비스 미리 보기 중 Windows 가상 데스크톱에 대 한 권장 솔루션 이었습니다.  

#### <a name="cost"></a>비용

S2D 클러스터 필요한 성능을 달성 하는 동안에 엔터프라이즈 고객에 게 저렴 하지만 특히 중소기업 (SMB) 고객에 대 한 비용이 많이 드는 비용은입니다. 이 솔루션에서는 비즈니스 비용 공유 디스크를 사용 하는 Vm 함께 프리미엄 저장소 디스크에 대 한 요금을 지불 합니다.

#### <a name="administrative-overhead"></a>관리 오버 헤드

S2D 클러스터 패치, 업데이트 및 보안 상태를 유지 관리 되는 운영 체제 필요 합니다. 이러한 프로세스와 S2D 재해 복구 설정의 복잡성에 S2D를은 전담된 IT 직원이 기업 적합 확인 합니다.

## <a name="fslogix-profile-containers"></a>FSLogix 프로필 컨테이너

2018 년 11 월 19 [Microsoft 획득 FSLogix](https://blogs.microsoft.com/blog/2018/11/19/microsoft-acquires-fslogix-to-enhance-the-office-365-virtualization-experience/)합니다. FSLogix 많은 프로필 컨테이너 문제를 해결합니다. 그중에서 키 다음과 같습니다.

- **성능:** 합니다 [FSLogix 프로필 컨테이너](https://fslogix.com/products/profile-containers) 은 고성능 및 캐시 된 exchange 모드를 지금까지 차단 하는 성능 관련 문제를 해결 합니다.
- **OneDrive:** 비즈니스용 OneDrive FSLogix 프로필 컨테이너 없이 비영구적 RDSH 또는 VDI 환경에서 지원 되지 않습니다. [비즈니스 및 FSLogix 모범 사례에 대 한 OneDrive](https://fslogix.com/products/technical-faqs/284-onedrive-for-business-and-fslogix-best-practices) 작용 하는 방법을 설명 합니다. 자세한 내용은 [동기화 클라이언트를 사용 하 여 가상 데스크톱에](https://docs.microsoft.com/deployoffice/rds-onedrive-business-vdi)입니다.
- **추가 폴더:** FSLogix 추가 폴더를 포함 하도록 사용자 프로필을 확장 하는 기능을 제공 합니다.

취득, 이후 Microsoft FSLogix 프로필 컨테이너를 사용 하 여 기존 사용자 프로필 솔루션 UPD 대체를 시작 합니다.

## <a name="azure-files-integration-with-azure-active-directory"></a>Azure Active Directory를 사용 하 여 azure 파일 통합

FSLogix 컨테이너 성능 프로 파일링 기능과 클라우드 활용 합니다. 2018 년 9 월 24 일에 Microsoft Azure Files의 공개 미리 보기를 발표 [Azure Active Directory 인증을 지 원하는 Azure Files](https://azure.microsoft.com/blog/azure-active-directory-integration-for-smb-access-now-in-public-preview/)합니다. 비용과 관리 오버 헤드를 주소 지정 하 여 Azure Active Directory 인증을 사용 하 여 Azure Files에는 새 Windows 가상 데스크톱 서비스의 사용자 프로필에 대 한 premium 솔루션입니다.

## <a name="best-practices-for-windows-virtual-desktop"></a>Windows 가상 데스크톱에 대 한 모범 사례

Windows 가상 데스크톱 크기, 유형 및 고객이 사용 중인 Vm의 수에 대 한 모든 권한을 제공 합니다. 자세한 내용은 [Windows 가상 데스크톱 미리 보기란 무엇 인가요?](https://docs.microsoft.com/azure/virtual-desktop/overview)합니다.

Windows 가상 데스크톱에 있도록 환경 모범 사례를 따릅니다.

- Azure 파일 저장소 계정 세션 호스트 Vm과 동일한 지역에 있어야 합니다.
- Azure 파일 사용 권한에 설명 된 권한이 같아야 [요구 사항-프로필 컨테이너](https://docs.fslogix.com/display/20170529/Requirements+-+Profile+Containers)합니다.
- 동일한 형식 및 크기의 동일한 마스터 이미지를 기반으로 하는 VM의 각 호스트 풀을 빌드해야 합니다.
- 각 호스트 풀 VM 확장 및 업데이트 관리를 지원 하기 위해 동일한 리소스 그룹에 있어야 합니다.
- 최적의 성능을 위해 저장소 솔루션과 동일한 데이터에서 프로필 컨테이너가 있어야 FSLogix 센터 위치입니다.
- 마스터 이미지에 포함 된 저장소 계정을 동일한 지역 및 Vm은 프로 비전 되는 구독에 있어야 합니다.

## <a name="next-steps"></a>다음 단계

Windows 가상 데스크톱 환경을 설정 하려면 다음 지침을 사용 합니다.

- 데스크톱 가상화 솔루션 구축을 시작 하려면 참조 [Windows 가상 데스크톱에서 테 넌 트를 만드는](https://docs.microsoft.com/azure/virtual-desktop/tenant-setup-azure-active-directory)합니다.
- Windows Virtual Desktop 테 넌 트 내에서 호스트 풀을 만들려면 참조 [Azure Marketplace를 사용 하 여 호스트 풀을 만들](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-azure-marketplace)합니다.
- 완전히 관리 되는 설정 하는 클라우드에서 파일 공유를 참조 하십시오 [Azure 파일 공유 설정](https://docs.microsoft.com/azure/storage/files/storage-files-active-directory-enable)합니다.
- FSLogix 프로필 컨테이너를 구성 하려면 참조 [호스트 풀에 대 한 사용자 프로필 공유 설정](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-user-profile)합니다.
- 참조 호스트 풀에 사용자 할당 [Windows 가상 데스크톱에 대 한 앱 그룹을 관리](https://docs.microsoft.com/azure/virtual-desktop/manage-app-groups)합니다.
- 웹 브라우저에서 Windows 가상 데스크톱 리소스에 액세스 하려면 참조 [Windows 가상 데스크톱에 연결](https://docs.microsoft.com/azure/virtual-desktop/connect-web)합니다.
