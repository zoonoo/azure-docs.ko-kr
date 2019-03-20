---
title: Azure Stack의 업데이트 적용 | Microsoft Docs
description: 가져오기는 Azure Stack 통합 시스템에 대 한 Microsoft 업데이트 패키지를 설치 하는 방법을 알아봅니다.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/11/2019
ms.author: mabrigg
ms.reviewer: justini
ms.lastreviewed: 02/11/2019
ms.openlocfilehash: 9eca22f5a594bf3d61b1d68882c4853f4bfc499d
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/18/2019
ms.locfileid: "58100754"
---
# <a name="apply-updates-in-azure-stack"></a>Azure Stack의 업데이트 적용

*적용 대상: Azure Stack 통합 시스템*

사용할 수는 **업데이트** Azure Stack에 대 한 Microsoft 또는 OEM 업데이트 패키지를 적용 하려면 관리 포털에서 타일입니다.

통합된 시스템 버전 1807을 사용 하는 경우 업데이트 패키지를 다운로드 해야 하는 이전에 Azure Stack에 패키지 파일을 가져올 하 고 업데이트 패키지를 설치 합니다. 자세한 내용은 [패키지를 다운로드 하 여 Azure Stack 업데이트](#update-azure-stack-by-downloading-the-package)

이러한 지침은 작업 Azure Stack 통합 시스템을 업그레이드합니다. Azure Stack 개발 시스템을 사용 하는 경우 최신 버전은 설치 패키지를 다운로드 해야 합니다. 자세한 내용은 [Azure Stack 개발 키트를 설치 합니다.](./asdk/asdk-install.md)

## <a name="update-azure-stack"></a>Azure Stack을 업데이트 합니다.

### <a name="select-and-apply-an-update-package"></a>선택한 업데이트 패키지를 적용 합니다.

1. 관리 포털을 엽니다.

2. 선택 **대시보드**합니다. 선택 된 **업데이트** 바둑판식으로 배열 합니다.

    ![Azure Stack 업데이트 사용 가능](media/azure-stack-apply-updates/azure-stack-updates-1901-dashboard.png)

3. 현재 버전의 Azure Stack에 기록해 둡니다. 다음 정식 버전으로 업데이트할 수 있습니다. 예를 들어 다음 실행 하는 경우 Azure Stack 1811, 버전 출시 하는 경우 1901 합니다.

    ![Azure Stack 업데이트 적용](media/azure-stack-apply-updates/azure-stack-updates-1901-updateavailable.png)

4. 업데이트 목록에서 다음 사용 가능한 버전을 선택 합니다. 선택할 수 있습니다 **보기** 릴리스에서 경우 버전에 대 한 릴리스 정보 항목을 열려면 메모 열 버전 변경 내용을 검토 하 려 합니다.

5. 이제 업데이트를 선택 합니다. 업데이트가 시작됩니다.

### <a name="review-update-history"></a>업데이트 기록 검토

1. 관리 포털을 엽니다.

2. 선택 **대시보드**합니다. 선택 된 **업데이트** 바둑판식으로 배열 합니다.

3. 선택 **업데이트 기록**합니다.

![Azure Stack 업데이트 기록](media/azure-stack-apply-updates/azure-stack-update-history.PNG)

## <a name="update-azure-stack-by-downloading-the-package"></a>패키지를 다운로드 하 여 Azure Stack을 업데이트 합니다.

통합된 시스템 버전 1807을 사용 하는 경우 업데이트 패키지를 다운로드 해야 하는 이전에 Azure Stack에 패키지 파일을 가져올 하 고 업데이트 패키지를 설치 합니다.

## <a name="download-the-update-package"></a>업데이트 패키지 다운로드

Azure Stack에 대 한 Microsoft 또는 OEM 업데이트 패키지를 사용할 수 있는 Azure Stack에서 연결할 수 있는 위치에 패키지를 다운로드 하 고 패키지 콘텐츠를 검토 합니다. 일반적으로 업데이트 패키지는 다음 파일로 구성 됩니다.

- 자동 압축 풀기 `<PackageName>.exe` 파일입니다. 이 파일 예를 들어 최신 누적 업데이트 Windows Server 용 업데이트에 대 한 페이로드를 포함합니다.

- 해당 `<PackageName>.bin` 파일입니다. 연결 된 페이로드에 대 한 압축을 제공 하는 이러한 파일은 *PackageName*.exe 파일입니다.

- `Metadata.xml` 파일입니다. 이 파일에는 예를 들어 게시자, 이름, 필수 구성 요소, 크기 및 지원 경로 URL 업데이트에 대 한 중요 정보가 들어 있습니다.

> [!IMPORTANT]  
> Azure Stack 1901 업데이트 패키지를 적용 한 후.exe,.bin(s), 및는.zip(s).xml 형식을.xml 형식에서 Azure Stack 업데이트 pacakges 패키징 형식을 이동 합니다. 스탬프 연결 된 azure Stack 운영자 영향을 받지 않습니다. Azure Stack 운영자와 연결이 끊어진 아래에 설명 된 동일한 프로세스를 사용 하 여.xml 및.zip 파일을 가져옵니다 하기만 하면 됩니다.

## <a name="import-and-install-updates"></a>가져오기 및 업데이트를 설치 합니다.

다음 절차에는 가져오기 및 관리자 포털에서 업데이트 패키지를 설치 하는 방법을 보여 줍니다.

> [!IMPORTANT]  
> 유지 관리 작업을 사용자에 게 알림 및 예약 하는 일반적인 유지 관리 기간 동안 업무 외 시간 최대한 많은 것이 좋습니다. 유지 관리 작업 사용자 워크 로드와 포털 작업에 영향을 줄 수 있습니다.

1. 관리자 포털에서 선택 **모든 서비스**합니다. 그런 다음 합니다 **데이터 + 저장소** 범주를 선택한 **저장소 계정**합니다. (필터 상자에 입력 하기 시작 하거나 **저장소 계정**를 선택 합니다.)

    ![포털에서 저장소 계정을 찾을 수 있는 위치를 보여 줍니다.](media/azure-stack-apply-updates/ApplyUpdates1.png)

2. 필터 상자에 입력 **업데이트**를 선택 합니다 **updateadminaccount** 저장소 계정입니다.

3. 저장소에서 계정 세부 정보를 아래 **Services**를 선택 **Blob**합니다.
 
    ![저장소 계정의 Blob에 가져오는 방법을 보여 줍니다.](media/azure-stack-apply-updates/ApplyUpdates3.png) 

4. 아래 **Blob service**를 선택 **+ 컨테이너** 컨테이너를 만들려고 합니다. 이름을 입력 (예를 들어 *업데이트 1811*)를 선택한 후 **확인**합니다.
 
     ![저장소 계정에 컨테이너를 추가 하는 방법을 보여 줍니다.](media/azure-stack-apply-updates/ApplyUpdates4.png)

5. 컨테이너를 만든 후 컨테이너 이름을 클릭 하 고 클릭 **업로드** 컨테이너에 패키지 파일을 업로드 합니다.
 
    ![패키지 파일을 업로드 하는 방법을 보여 줍니다.](media/azure-stack-apply-updates/ApplyUpdates5.png)

6. 아래 **blob 업로드**폴더 아이콘을 클릭, 업데이트 패키지의.exe 파일을 찾아서 클릭 **오픈** 파일 탐색기 창에서.
  
7. 아래 **blob 업로드**, 클릭 **업로드**합니다.
  
    ![각 패키지 파일을 업로드 하는 위치를 보여 줍니다.](media/azure-stack-apply-updates/ApplyUpdates6.png)

8. 6-7 단계를 반복 합니다 *PackageName*.bin 및 Metadata.xml 파일입니다. 포함 하는 경우 추가 Notice.txt 파일을 가져오지 않습니다.
9. 을 완료 한 후에 알림 (포털의 오른쪽 위 모서리에 있는 벨 아이콘)를 검토할 수 있습니다. 업로드 완료 알림을 나타내야 합니다.
10. 대시보드에서 타일 업데이트 다시 이동 합니다. 타일은 업데이트를 사용할 수 있음을 나타내야 합니다. 새로 추가 된 업데이트 패키지를 검토 하려면 타일을 클릭 합니다.
11. 업데이트를 설치 하려면로 표시 된 패키지를 선택 **준비** 패키지를 마우스 오른쪽 단추로 클릭 하 고 선택 **지금 업데이트**를 클릭 하거나 합니다 **지금 업데이트** 위쪽에 있는 작업 .
12. 상태를 볼 수 업데이트 패키지를 설치를 클릭 합니다 **업데이트 실행 세부 정보** 영역입니다. 여기에서 클릭할 수도 있습니다 **전체 로그 다운로드** 로그 파일을 다운로드 합니다.
13. 업데이트가 완료 되 면 업데이트 타일에는 업데이트 된 Azure Stack 버전을 표시 합니다.

Azure Stack에 설치한 후 저장소 계정에서 업데이트를 수동으로 삭제할 수 있습니다. Azure Stack는 정기적으로 오래 된 업데이트 패키지가 있는지 확인 하 고 저장소에서 제거 합니다. Azure Stack 걸릴 이전 패키지를 제거 하려면 2 주입니다.

## <a name="next-steps"></a>다음 단계

- [Azure Stack의 업데이트 관리 개요](azure-stack-updates.md)
- [Azure Stack 서비스 정책](azure-stack-servicing-policy.md)
