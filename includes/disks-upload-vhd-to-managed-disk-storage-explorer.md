---
title: 포함 파일
description: 포함 파일
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 09/25/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 5104c3b4446a8d3747ce7cc1648ef05dd117eb3d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "74013791"
---
저장소 탐색기 1.10.0을 사용하면 관리되는 디스크를 업로드, 다운로드 및 복사하고 스냅샷을 만들 수 있습니다. 이러한 추가 기능으로 인해 저장소 탐색기를 사용하여 온-프레미스에서 Azure로 데이터를 마이그레이션하고 Azure 리전에서 데이터를 마이그레이션할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

이 문서를 완료하려면 다음이 필요합니다.
- Azure 구독
- 하나 이상의 Azure 관리 디스크
- Azure 저장소 [탐색기의](https://azure.microsoft.com/features/storage-explorer/) 최신 버전

## <a name="connect-to-an-azure-subscription"></a>Azure 구독에 연결

저장소 탐색기가 Azure에 연결되어 있지 않으면 리소스를 관리하는 데 사용할 수 없습니다. 이 섹션에서는 저장소 탐색기를 사용하여 리소스를 관리할 수 있도록 Azure 계정에 연결합니다.

1. Azure 저장소 탐색기를 실행하고 왼쪽에 있는 **플러그인** 아이콘을 클릭합니다.

    ![플러그인 아이콘을 클릭합니다.](media/disks-upload-vhd-to-managed-disk-storage-explorer/plug-in-icon.png)

1. **Azure 계정 추가를**선택한 다음 다음 을 **클릭합니다.**

    ![Azure 계정 추가](media/disks-upload-vhd-to-managed-disk-storage-explorer/connect-to-azure.png)

1. Azure **로그인** 대화 상자에서 Azure 자격 증명을 입력합니다.

    ![Azure 로그인 대화 상자](media/disks-upload-vhd-to-managed-disk-storage-explorer/sign-in.png)

1. 목록에서 구독을 선택하고 **적용**을 클릭합니다.

    ![구독 선택](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-subscription.png)

## <a name="upload-a-managed-disk-from-an-on-prem-vhd"></a>온프레미 VHD에서 관리디스크 업로드

1. 왼쪽 창에서 디스크를 확장하고 **디스크를** 업로드할 리소스 그룹을 선택합니다.

    ![리소스 그룹 1 선택](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg1.png)

1. **업로드**를 선택합니다.

    ![업로드 선택](media/disks-upload-vhd-to-managed-disk-storage-explorer/upload-button.png)

1. **VHD 업로드에서** 소스 VHD, 디스크 이름, OS 유형, 디스크를 업로드할 영역 및 계정 유형을 지정합니다. 일부 지역에서는 가용성 영역이 지원되며 해당 지역의 경우 원하는 영역을 선택할 수 있습니다.
1. 디스크 업로드를 시작하려면 **만들기를** 선택합니다.

    ![vhd 대화 상자 업로드](media/disks-upload-vhd-to-managed-disk-storage-explorer/upload-vhd-dialog.png)

1. 이제 업로드 상태가 **활동**에 표시됩니다.

    ![업로드 상태](media/disks-upload-vhd-to-managed-disk-storage-explorer/activity-uploading.png)

1. 업로드가 완료되고 오른쪽 창에 디스크가 표시되지 않으면 새로 **고침을**선택합니다.

## <a name="download-a-managed-disk"></a>관리 디스크 다운로드

다음 단계에서는 관리디스크를 온프레미 VHD에 다운로드하는 방법을 설명합니다. 다운로드하려면 디스크 의 상태를 **연결 해제해야** 하며 **연결된** 디스크를 다운로드할 수 없습니다.

1. 왼쪽 창에서 아직 확장되지 않은 경우 디스크를 확장하고 **디스크를** 다운로드할 리소스 그룹을 선택합니다.

    ![리소스 그룹 1 선택](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg1.png)

1. 오른쪽 창에서 다운로드할 디스크를 선택합니다.
1. **다운로드를** 선택한 다음 디스크를 저장할 위치를 선택합니다.

    ![관리 디스크 다운로드](media/disks-upload-vhd-to-managed-disk-storage-explorer/download-button.png)

1. **저장을** 선택하면 디스크가 다운로드되기 시작합니다. 다운로드 상태가 **활동**에 표시됩니다.

    ![다운로드 상태](media/disks-upload-vhd-to-managed-disk-storage-explorer/activity-downloading.png)

## <a name="copy-a-managed-disk"></a>관리 디스크 복사

저장소 탐색기를 사용하면 지역 내 또는 여러 지역에서 맨검 디스크를 복사할 수 있습니다. 디스크를 복사하려면 다음을 수행하십시오.

1. 왼쪽의 **디스크** 드롭다운에서 복사할 디스크가 포함된 리소스 그룹을 선택합니다.

    ![리소스 그룹 1 선택](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg1.png)

1. 오른쪽 창에서 복사할 디스크를 선택하고 **복사**를 선택합니다.

    ![관리 디스크 복사](media/disks-upload-vhd-to-managed-disk-storage-explorer/copy-button.png)

1. 왼쪽 창에서 디스크를 붙여넣려는 리소스 그룹을 선택합니다.

    ![리소스 그룹 2 선택](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg2.png)

1. 오른쪽 창에 **붙여넣기를** 선택합니다.

    ![관리 디스크 붙여넣기](media/disks-upload-vhd-to-managed-disk-storage-explorer/paste-button.png)

1. **붙여넣기 디스크** 대화 상자에서 값을 입력합니다. 지원되는 지역에서 가용성 영역을 지정할 수도 있습니다.

    ![디스크 대화 상자 붙여넣기](media/disks-upload-vhd-to-managed-disk-storage-explorer/paste-disk-dialog.png)

1. **붙여넣기를** 선택하면 디스크가 복사를 시작하면 상태가 **활동**에 표시됩니다.

    ![붙여넣기 상태 복사](media/disks-upload-vhd-to-managed-disk-storage-explorer/activity-copying.png)

## <a name="create-a-snapshot"></a>스냅샷 만들기

1. 왼쪽의 **디스크** 드롭다운에서 스냅숏을 지정하려는 디스크가 포함된 리소스 그룹을 선택합니다.

    ![리소스 그룹 1 선택](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg1.png)

1. 오른쪽에서 스냅숏을 스냅숏으로 만들 디스크를 선택하고 **스냅샷 만들기를**선택합니다.

    ![스냅샷 만들기](media/disks-upload-vhd-to-managed-disk-storage-explorer/create-snapshot-button.png)

1. **스냅숏 만들기에서**스냅숏을 만들 리소스 그룹뿐만 아니라 스냅숏의 이름을 지정합니다. 그런 다음 **을 선택합니다.**

    ![스냅샷 대화 상자 만들기](media/disks-upload-vhd-to-managed-disk-storage-explorer/create-snapshot-dialog.png)

1. 스냅숏이 만들어지면 **활동내** **포털에서 열기를** 선택하여 Azure 포털에서 스냅샷을 볼 수 있습니다.

    ![포털에서 스냅샷 열기](media/disks-upload-vhd-to-managed-disk-storage-explorer/open-in-portal.png)

## <a name="next-steps"></a>다음 단계
