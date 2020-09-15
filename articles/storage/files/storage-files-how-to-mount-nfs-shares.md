---
title: Azure NFS 파일 공유 탑재-Azure Files
description: 네트워크 파일 시스템 공유를 탑재 하는 방법에 대해 알아봅니다.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 09/15/2020
ms.author: rogarana
ms.subservice: files
ms.custom: references_regions
ms.openlocfilehash: 23465c76fa62ed65583b97b0c9406dc791a4327f
ms.sourcegitcommit: 6e1124fc25c3ddb3053b482b0ed33900f46464b3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/15/2020
ms.locfileid: "90564859"
---
# <a name="how-to-mount-an-nfs-file-share"></a>NFS 파일 공유를 탑재 하는 방법

[Azure Files](storage-files-introduction.md)는 사용하기 쉬운 Microsoft 클라우드 파일 시스템입니다. Azure 파일 공유는 SMB (서버 메시지 블록 프로토콜) 또는 NFS (네트워크 파일 시스템) 프로토콜을 사용 하 여 Linux 배포판에 탑재할 수 있습니다. 이 문서는 NFS를 탑재 하는 방법에 중점을 두었습니다. SMB 탑재에 대 한 자세한 내용은 [Linux에서 Azure Files 사용](storage-how-to-use-files-linux.md)을 참조 하세요. 사용 가능한 각 프로토콜에 대 한 자세한 내용은 [Azure 파일 공유 프로토콜](storage-files-compare-protocols.md)을 참조 하세요.

## <a name="limitations"></a>제한 사항

[!INCLUDE [files-nfs-limitations](../../../includes/files-nfs-limitations.md)]

### <a name="regional-availability"></a>국가별 가용성

[!INCLUDE [files-nfs-regional-availability](../../../includes/files-nfs-regional-availability.md)]

## <a name="prerequisites"></a>필수 구성 요소

- [NFS 공유를 만듭니다](storage-files-how-to-create-nfs-shares.md).

    > [!IMPORTANT]
    > NFS 공유는 신뢰할 수 있는 네트워크 에서만 액세스할 수 있습니다. NFS 공유에 대 한 연결은 다음 원본 중 하나에서 시작 해야 합니다.

- 다음 네트워킹 솔루션 중 하나를 사용 합니다.
    - [개인 끝점을 만들거나](storage-files-networking-endpoints.md#create-a-private-endpoint) (권장) [공용 끝점에 대 한 액세스를 제한](storage-files-networking-endpoints.md#restrict-public-endpoint-access)합니다.
    - [Azure Files와 함께 사용 하기 위해 Linux에서 지점 및 사이트 간 (P2S) VPN을 구성](storage-files-configure-p2s-vpn-linux.md)합니다.
    - [Azure Files와 함께 사용 하기 위해 사이트 간 VPN을 구성](storage-files-configure-s2s-vpn.md)합니다.
    - [Express](../../expressroute/expressroute-introduction.md)경로를 구성 합니다.

## <a name="disable-secure-transfer"></a>보안 전송 사용 안 함

1. Azure Portal에 로그인 하 여 만든 NFS 공유를 포함 하는 저장소 계정에 액세스 합니다.
1. **Configuration(구성)** 을 선택합니다.
1. **보안 전송 필요**에 대해 **사용 안 함** 을 선택 합니다.
1. **저장**을 선택합니다.

    :::image type="content" source="media/storage-files-how-to-mount-nfs-shares/storage-account-disable-secure-transfer.png" alt-text="보안 전송이 사용 하지 않도록 설정 된 저장소 계정 구성 화면의 스크린샷":::

## <a name="mount-an-nfs-share"></a>NFS 공유 탑재

1. 파일 공유를 만든 후 공유를 선택 하 고 **Linux에서 연결**을 선택 합니다.
1. 사용할 탑재 경로를 입력 한 다음 스크립트를 복사 합니다.
1. 클라이언트에 연결 하 고 제공 된 탑재 스크립트를 사용 합니다.

    :::image type="content" source="media/storage-files-how-to-create-mount-nfs-shares/mount-nfs-file-share-script.png" alt-text="파일 공유 연결 블레이드의 스크린샷":::

이제 NFS 공유를 탑재 했습니다.

## <a name="next-steps"></a>다음 단계

[Azure Files 배포에 대 한 계획](storage-files-planning.md)문서를 Azure Files에 대해 자세히 알아보세요.