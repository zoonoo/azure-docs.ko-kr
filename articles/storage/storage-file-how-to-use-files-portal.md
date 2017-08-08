---
title: "Azure Portal에서 Azure File Storage를 관리하는 방법 | Microsoft Docs"
description: "Azure Portal을 사용하여 Azure File Storage를 관리하는 방법을 알아봅니다."
services: storage
documentationcenter: 
author: RenaShahMSFT
manager: aungoo
editor: tysonn
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/27/2017
ms.author: renash
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: d8ffb4359b0efe8da2f3bccb81c987bdeedf1a39
ms.contentlocale: ko-kr
ms.lasthandoff: 07/21/2017

---

# <a name="how-to-use-azure-file-storage-from-the-azure-portal"></a>Azure Portal에서 Azure File Storage를 사용하는 방법
[Azure Portal](https://portal.azure.com)에서는 Azure File Storage를 관리하기 위한 사용자 인터페이스를 제공합니다. 웹 브라우저에서 다음 작업을 수행할 수 있습니다.

* 파일 공유 만들기
* 파일 공유에 대해 파일 업로드 및 다운로드
* 각 파일 공유의 실제 사용량 모니터링
* 파일 공유 크기 할당량 조정
* Windows 또는 Linux 클라이언트에서 파일 공유를 탑재하는 데 사용할 탑재 명령 복사

## <a name="create-file-share"></a>파일 공유 만들기
1. Azure 포털에 로그인합니다.
2. 탐색 메뉴에서 **저장소 계정** 또는 **저장소 계정(클래식)**을 클릭합니다.
    
    ![포털에서 파일 공유를 만드는 방법을 보여주는 스크린 샷](media/storage-file-how-to-use-files-portal/use-files-portal-create-file-share1.png)

3. 저장소 계정 선택

    ![포털에서 파일 공유를 만드는 방법을 보여주는 스크린 샷](media/storage-file-how-to-use-files-portal/use-files-portal-create-file-share2.png)

4. "파일" 서비스를 선택합니다.

    ![포털에서 파일 공유를 만드는 방법을 보여주는 스크린 샷](media/storage-file-how-to-use-files-portal/use-files-portal-create-file-share3.png)

5. "파일 공유"를 클릭하고 링크를 따라 첫 번째 파일 공유를 만듭니다.

    ![포털에서 파일 공유를 만드는 방법을 보여주는 스크린 샷](media/storage-file-how-to-use-files-portal/use-files-portal-create-file-share4.png)

6. 파일 공유 이름 및 파일 공유 파일의 크기(최대 5120GB)를 입력하여 첫 번째 파일 공유를 만듭니다. 파일 공유가 만들어지면 SMB 2.1 또는 SMB 3.0을 지원하는 모든 파일 시스템에서 마운트할 수 있습니다. 파일 공유의 **할당량**을 클릭하여 파일 크기를 최대 5,120GB까지 변경할 수 있습니다. Azure File Storage를 사용하는 저장소 비용을 예측하려면 [Azure 가격 계산기](https://azure.microsoft.com/pricing/calculator/)를 참조하세요.

    ![포털에서 파일 공유를 만드는 방법을 보여주는 스크린 샷](media/storage-file-how-to-use-files-portal/use-files-portal-create-file-share5.png)

## <a name="upload-and-download-files"></a>파일 업로드 및 다운로드
1. 이미 만든 파일 공유 중 하나를 선택합니다.

    ![포털에서 파일을 업로드 및 다운로드하는 방법을 보여주는 스크린 샷](media/storage-file-how-to-use-files-portal/use-files-portal-upload-file1.png)

2. **업로드** 를 클릭하여 업로드하는 파일에 대한 사용자 인터페이스를 엽니다.

    ![포털에서 파일을 업로드하는 방법을 보여주는 스크린 샷](media/storage-file-how-to-use-files-portal/use-files-portal-upload-file2.png)

## <a name="connect-to-file-share"></a>파일 공유에 연결
-  **연결**을 클릭하여 Windows 및 Linux에서 파일 공유를 탑재하기 위한 명령줄을 가져옵니다. Linux 사용자의 경우 다른 Linux 배포에 대한 자세한 탑재 지침은 [Linux에서 Azure File storage 사용 방법](storage-how-to-use-files-linux.md)도 참조할 수 있습니다.

    ![파일 공유를 마운트하는 방법을 보여주는 스크린 샷](media/storage-file-how-to-use-files-portal/use-files-portal-connect.png)
-  Windows 또는 Linux에서 파일 공유를 탑재하기 위한 명령을 복사하고 Azure VM 또는 온-프레미스 컴퓨터에서 이 명령을 실행할 수 있습니다.

    ![Windows 및 Linux용 탑재 명령을 보여주는 스크린샷](media/storage-file-how-to-use-files-portal/use-files-portal-show-mount-commands.png)

**팁:**  
탑재할 저장소 계정 액세스 키를 찾으려면 연결 페이지 아래쪽에서 **이 저장소 계정에 대한 액세스 키 보기**를 클릭합니다.

## <a name="see-also"></a>참고 항목
Azure 파일 저장소에 대한 자세한 내용은 다음 링크를 참조합니다.

* [FAQ](storage-files-faq.md)
* [문제 해결](storage-troubleshoot-file-connection-problems.md)
