---
title: 스토리지 FSLogix 프로필 컨테이너 윈도우 가상 데스크톱 - Azure
description: Azure 저장소에 Windows 가상 데스크톱 FSLogix 프로필을 저장하기 위한 옵션입니다.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 71ba24784dee7771acbe19bf0261c7dc02478b24
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127525"
---
# <a name="storage-options-for-fslogix-profile-containers-in-windows-virtual-desktop"></a>Windows 가상 데스크톱의 FSLogix 프로필 컨테이너에 대한 저장소 옵션

Azure는 FSLogix 프로필 컨테이너를 저장하는 데 사용할 수 있는 여러 저장소 솔루션을 제공합니다. 이 문서에서는 Azure가 Windows 가상 데스크톱 FSLogix 사용자 프로필 컨테이너에 대해 제공하는 저장소 솔루션을 비교합니다.

Windows 가상 데스크톱은 FSLogix 프로필 컨테이너를 권장 사용자 프로필 솔루션으로 제공합니다. FSLogix는 Windows 가상 데스크톱과 같은 원격 컴퓨팅 환경에서 프로파일을 로밍하도록 설계되었습니다. 로그인 시 이 컨테이너는 기본적으로 지원되는 VHD(가상 하드 디스크)와 하이퍼-V 가상 하드 디스크(VHDX)를 사용하여 컴퓨팅 환경에 동적으로 연결됩니다. 사용자 프로필을 즉시 사용할 수 있으며 기본 사용자 프로필과 정확히 같은 시스템에 나타납니다.

다음 표에서는 Windows 가상 데스크톱 FSLogix 프로필 컨테이너 사용자 프로필에 대해 Azure Storage가 제공하는 저장소 솔루션을 비교합니다.

## <a name="azure-platform-details"></a>Azure 플랫폼 세부 정보

|기능|Azure 파일|Azure NetApp Files|직접 스토리지 공간|
|--------|-----------|------------------|---------------------|
|사용 사례|범용 가상 컴퓨터|NetApp 온-프레미스에서 의 한-성능 또는 마이그레이션|플랫폼 간|
|플랫폼 서비스|예, Azure 네이티브 솔루션|예, Azure 네이티브 솔루션|아니요, 자체 관리|
|국가별 가용성|모든 지역|[지역 선택](https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all)|모든 지역|
|중복|로컬 중복/영역 중복/지리적 중복|로컬 중복|로컬 중복/영역 중복/지리적 중복|
|등급 및 성능|Standard<br>Premium<br>주당 최대 100k IOPS, 약 3ms 대기 시간에서 주당 5GBps|Standard<br>Premium<br>울트라<br>최대 320k(16K) IOPS, 약 1ms 대기 시간에서 부피당 4.5GBps|표준 HDD: 디스크당 최대 500 IOPS 제한<br>표준 SSD: 디스크당 최대 4k IOPS 제한<br>프리미엄 SSD: 디스크당 최대 20k IOPS 제한<br>스토리지 스페이스 다이렉트용 프리미엄 디스크를 권장합니다.|
|용량|주당 100TiB|볼륨당 100 TiB, 구독당 최대 12.5PiB|디스크당 최대 32TiB|
|필수 인프라|최소 공유 크기 1 GiB|최소 용량 풀 4 TiB, 최소 볼륨 크기 100 GiB|Azure IaaS(+ 클라우드 감시) 또는 디스크가 없는 VM 3개 이상에 있는 VM 2개|
|프로토콜|중소기업 2.1/3. 및 휴식|NFSv3, NFSv4.1(미리보기), SMB 3.x/2.x|NFSv3, NFSv4.1, SMB 3.1|

## <a name="azure-management-details"></a>Azure 관리 세부 정보

|기능|Azure 파일|Azure NetApp Files|직접 스토리지 공간|
|--------|-----------|------------------|---------------------|
|액세스 권한|클라우드, 온-프레미스 및 하이브리드(Azure 파일 동기화)|클라우드, 온-프레미스(익스프레스루트를 통해)|클라우드, 온-프레미스|
|Backup|Azure 백업 스냅숏 통합|Azure NetApp 파일 스냅샷|Azure 백업 스냅숏 통합|
|보안 및 규정 준수|[모든 Azure 지원 인증서](https://www.microsoft.com/trustcenter/compliance/complianceofferings)|ISO 완료|[모든 Azure 지원 인증서](https://www.microsoft.com/trustcenter/compliance/complianceofferings)|
|Azure Active Directory 통합|[네이티브 Active 디렉터리 및 Azure Active 디렉터리 도메인 서비스](https://docs.microsoft.com/azure/storage/files/storage-files-active-directory-overview)|[Azure Active 디렉터리 도메인 서비스 및 네이티브 활성 디렉터리](../azure-netapp-files/azure-netapp-files-faqs.md#does-azure-netapp-files-support-azure-active-directory)|네이티브 Active Directory 또는 Azure Active Directory 도메인 서비스는 지원만 합니다.|

저장소 방법을 선택한 후에는 가격 책정 계획에 대한 정보를 Windows [가상 데스크톱 가격](https://azure.microsoft.com/pricing/details/virtual-desktop/) 책정을 확인하십시오.

## <a name="next-steps"></a>다음 단계

FSLogix 프로필 컨테이너, 사용자 프로필 디스크 및 기타 사용자 프로필 기술에 대한 자세한 내용은 [FSLogix 프로필 컨테이너 및 Azure 파일의](fslogix-containers-azure-files.md)표를 참조하십시오.

고유한 FSLogix 프로필 컨테이너를 만들 준비가 되면 다음 자습서 중 하나를 시작하십시오.

- [Windows 가상 데스크톱의 Azure 파일에서 FSLogix 프로필 컨테이너 시작](https://techcommunity.microsoft.com/t5/Windows-IT-Pro-Blog/Getting-started-with-FSLogix-profile-containers-on-Azure-Files/ba-p/746477)
- [Azure NetApp 파일을 사용하여 호스트 풀에 대한 FSLogix 프로필 컨테이너 만들기](create-fslogix-profile-container.md)
- [Azure의 UPD 저장소를 위한 2노드 저장소 공간 직접 확장 파일 서버 배포의](/windows-server/remote/remote-desktop-services/rds-storage-spaces-direct-deployment/) 지침은 사용자 프로필 디스크 대신 FSLogix 프로필 컨테이너를 사용할 때도 적용됩니다.

또한 처음부터 시작하여 Windows 가상 데스크톱에서 [테넌트 만들기에서](tenant-setup-azure-active-directory.md)고유한 Windows 가상 데스크톱 솔루션을 설정할 수도 있습니다.
