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
ms.date: 01/18/2019
ms.author: mabrigg
ms.reviewer: wfayed
ms.lastreviewed: 01/18/2019
ms.openlocfilehash: 585fc4f1bbddb08d881414b581120b7bc14232ab
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/05/2019
ms.locfileid: "55729951"
---
# <a name="apply-updates-in-azure-stack"></a>Azure Stack의 업데이트 적용

*적용 대상: Azure Stack 통합 시스템*

사용할 수는 **업데이트** Azure Stack에 대 한 Microsoft 또는 OEM 업데이트 패키지를 적용 하려면 관리 포털에서 타일입니다. 업데이트 패키지를 다운로드 하 고, Azure Stack에 패키지 파일을 가져올 하 고, 다음 업데이트 패키지를 설치 해야 합니다.

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
