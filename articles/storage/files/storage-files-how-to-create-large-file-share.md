---
title: 대량 파일 공유 사용 및 만들기-Azure Files
description: 이 문서에서는 많은 파일 공유를 사용 하도록 설정 하 고 만드는 방법을 알아봅니다.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 00db5905c008644bc21704179363849756fa7dcc
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72518422"
---
# <a name="how-to-enable-and-create-large-file-shares"></a>대량 파일 공유를 사용 하도록 설정 하 고 만드는 방법

원래 표준 파일 공유는 TiB 최대 5 개까지 확장할 수 있었습니다. 이제 대용량 파일 공유를 사용 하 여 최대 100 TiB까지 확장할 수 있습니다. 프리미엄 파일 공유는 기본적으로 최대 100 TiB 확장 됩니다. 

표준 파일 공유를 사용 하 여 최대 100 TiB 확장 하려면 저장소 계정에서 대용량 파일 공유를 사용 하도록 설정 해야 합니다. 기존 계정을 사용 하도록 설정 하거나 새 계정을 만들어 대량 파일 공유를 사용할 수 있습니다.

## <a name="prerequisites"></a>전제 조건

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다.

## <a name="restrictions"></a>제한

대량 파일 공유 사용 계정에서는 LRS 또는 ZRS를 지원 합니다. 지금은 많은 파일 공유 사용 계정이 GZRS, GRS 또는 RA-GRS를 지원 하지 않습니다. 계정에 대 한 대량 파일 공유를 사용 하도록 설정 하는 작업은 되돌릴 수 없습니다. 사용 하도록 설정 되 면 계정을 GZRS, GRS 또는 RA-GRS로 변환할 수 없습니다.

## <a name="create-a-new-storage-account"></a>새 스토리지 계정 만들기

[Azure portal](https://portal.azure.com)에 로그인합니다.

1. Azure Portal에서 **모든 서비스**를 선택합니다. 리소스 목록에 **스토리지 계정**을 입력합니다. 입력을 시작하면 입력한 내용을 바탕으로 목록이 필터링됩니다. **Storage 계정**을 선택합니다.
1. 나타나는 **Storage 계정** 창에서 **추가**를 선택합니다.
1. 스토리지 계정을 만들 구독을 선택합니다.
1. **리소스 그룹** 필드 아래에서 **새로 만들기**를 선택합니다. 다음 이미지에 표시된 대로 새 리소스 그룹의 이름을 입력합니다.

    ![포털에서 리소스 그룹을 만드는 방법을 보여주는 스크린샷](media/storage-files-how-to-create-large-file-share/create-large-file-share.png)

1. 그런 다음, 스토리지 계정의 이름을 입력합니다. 선택하는 이름이 Azure에서 고유해야 합니다. 또한 이름의 길이가 3~24자여야 하고, 숫자 및 소문자만 포함할 수 있습니다.
1. 저장소 계정에 대 한 위치를 선택 하 고 [LFS에 대해 지원 되는 지역 중 하나](storage-files-planning.md#regional-availability)인지 확인 합니다.
1. 복제를 **로컬 중복 저장소** 또는 **영역 중복 저장소**로 설정 합니다.
1. 다음 필드는 기본값으로 유지합니다.

   |필드  |Value  |
   |---------|---------|
   |배포 모델     |Resource Manager         |
   |성능 중심     |Standard         |
   |계정 종류     |StorageV2(범용 v2)         |
   |액세스 계층     |핫         |

1. **고급** 을 선택 하 고 **대량 파일 공유**에 대해 **사용** 을 선택 합니다.
1. **검토 + 만들기**를 선택하여 스토리지 계정 설정을 검토하고 계정을 만듭니다.

    ![large-file-shares-advanced-enable](media/storage-files-how-to-create-large-file-share/large-file-shares-advanced-enable.png)

1. **만들기**를 선택합니다.

## <a name="enable-on-existing-account"></a>기존 계정에서 사용

기존 계정에서 대량 파일 공유를 사용 하도록 설정할 수도 있습니다. 이 작업을 수행 하는 경우이 계정은 더 이상 GZRS, GRS 또는 RA-GRS로 변환할 수 없습니다. 이 옵션은이 계정에서 취소할 수 없습니다.

1. [Azure Portal](https://portal.azure.com) 를 열고에서 대량 파일 공유를 사용 하도록 설정할 저장소 계정으로 이동 합니다.
1. 저장소 계정을 열고 **구성**을 선택 합니다.
1. **Large file 공유**에서 **사용** 을 선택한 다음 저장을 선택 합니다.
1. **개요** 를 선택 하 고 **새로 고침**을 선택 합니다.

![enable-large-file-shares-on-existing](media/storage-files-how-to-create-large-file-share/enable-large-file-shares-on-existing.png)

이제 저장소 계정에서 대량 파일 공유를 사용 하도록 설정 했습니다.

다음 오류가 표시 되 면 "계정에 대 한 대량 파일 공유를 아직 사용할 수 없습니다." 사용자의 지역에서 롤아웃이 완료 될 가능성이 있거나 긴급 한 요구 사항이 있는 경우 지원에 문의 하 여 잠시 기다릴 수 있습니다.

## <a name="create-a-large-file-share"></a>대량 파일 공유 만들기

대량 파일 공유를 만드는 것은 표준 파일 공유 만들기와 거의 동일 합니다. 주요 차이점은 최대 100 TiB 할당량을 설정할 수 있다는 것입니다.

1. 저장소 계정에서 **파일 공유**를 선택 합니다.
1. **+ 파일 공유**를 선택합니다.
1. 파일 공유에 대 한 이름을 입력 하 고 원하는 할당량 크기 (선택 사항)를 최대 100 TiB 한 다음, **만들기**를 선택 합니다. 

![large-file-shares-create-share](media/storage-files-how-to-create-large-file-share/large-file-shares-create-share.png)

## <a name="expand-existing-file-shares"></a>기존 파일 공유 확장

저장소 계정에서 큰 파일 공유를 사용 하도록 설정 하면 기존 공유를 상위 할당량으로 확장할 수 있습니다.

1. 저장소 계정에서 **파일 공유**를 선택 합니다.
1. 파일 공유를 마우스 오른쪽 단추로 클릭 하 고 **할당량**을 선택 합니다.
1. 원하는 새 크기를 입력 하 고 **확인**을 선택 합니다.

![update-large-file-share-quota](media/storage-files-how-to-create-large-file-share/update-large-file-share-quota.png)

## <a name="next-steps"></a>다음 단계

* [파일 공유 연결 및 탑재 - Windows](storage-how-to-use-files-windows.md)
* [파일 공유 연결 및 탑재 - Linux](../storage-how-to-use-files-linux.md)
* [파일 공유 연결 및 탑재 - macOS](storage-how-to-use-files-mac.md)