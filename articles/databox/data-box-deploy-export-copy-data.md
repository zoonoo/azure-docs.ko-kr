---
title: Azure Data Box에서 SMB를 통해 데이터를 복사하는 자습서 | Microsoft Docs
description: SMB를 통해 Azure Data Box에 데이터를 복사하는 방법 알아보기
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 07/10/2020
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: 204e89bdf8c660a7e96438f8cb3b8a18aeaec306
ms.sourcegitcommit: a07a01afc9bffa0582519b57aa4967d27adcf91a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2020
ms.locfileid: "91742386"
---
# <a name="tutorial-copy-data-from-azure-data-box-via-smb-preview"></a>자습서: SMB를 통해 Azure Data Box에서 데이터 복사(미리 보기)

이 자습서에서는 로컬 웹 UI를 사용하여 Data Box에서 온-프레미스 데이터 서버로 데이터를 연결하고 복사하는 방법을 설명합니다. Data Box 디바이스에는 Azure Storage 계정에서 내보낸 데이터가 포함되어 있습니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
>
> * 필수 구성 요소
> * Data Box에 연결
> * Data Box에서 데이터 복사

[!INCLUDE [Data Box feature is in preview](../../includes/data-box-feature-is-preview-info.md)]

## <a name="prerequisites"></a>필수 구성 요소

시작하기 전에 다음 사항을 확인합니다.

1. Azure Data Box에 대한 주문을 배치했습니다.
    - 가져오기 순서는 [자습서: Azure Data Box 주문](data-box-deploy-ordered.md)을 완료했습니다.
    - 내보내기 순서는 [자습서: Azure Data Box 주문](data-box-deploy-export-ordered.md)을 완료했습니다.
2. Data Box를 받았고 포털의 주문 상태가 **배달됨**입니다.
3. Data Box에서 데이터를 복사할 호스트 컴퓨터가 있습니다. 호스트 컴퓨터는 다음 사항이 필수입니다.
   * [지원되는 운영 체제](data-box-system-requirements.md)를 실행합니다.
   * 고속 네트워크에 연결되어 있어야 합니다. 10GbE 연결이 하나 이상 있는 것이 좋습니다. 10GbE 연결을 사용할 수 없으면 1GbE 데이터 링크를 사용해도 되지만, 이 경우 복사 속도가 떨어집니다.

## <a name="connect-to-data-box"></a>Data Box에 연결

[!INCLUDE [data-box-shares](../../includes/data-box-shares.md)]

Windows Server 호스트 컴퓨터를 사용하는 경우 다음 단계에 따라 Data Box에 연결합니다.

1. 첫 번째 단계는 세션을 인증하고 시작하는 것입니다. **연결 및 복사**로 이동합니다. **자격 증명 가져오기**를 선택하여 스토리지 계정과 연결된 공유의 액세스 자격 증명을 가져옵니다. 

    ![공유 자격 증명 가져오기](media/data-box-deploy-export-copy-data/get-share-credentials-1.png)

2. [액세스 공유 및 데이터 복사] 대화 상자에서 공유에 해당하는 **사용자 이름** 및 **암호**를 복사합니다. **확인**을 선택합니다.
    
    ![공유 자격 증명 가져오기 2](media/data-box-deploy-export-copy-data/get-share-credentials-2.png)

3. 호스트 컴퓨터에서 스토리지 계정과 연결된 공유(다음 예제의 *exportbvtdataset2*)에 액세스하려면 명령 창을 엽니다. 명령 프롬프트에 다음을 입력합니다.

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

    데이터 형식에 따라 공유 경로는 다음과 같습니다.
    - Azure 블록 Blob - `\\169.254.143.85\exportbvtdataset2_BlockBlob`
    - Azure 페이지 Blob - `\\169.254.143.85\exportbvtdataset2_PageBlob`
    - Azure 파일 - `\\169.254.143.85\exportbvtdataset2_AzFile`

4. 메시지가 표시되면 공유 암호를 입력합니다. 다음 샘플은 이전 명령을 통해 공유에 연결하는 방법을 보여줍니다.

    ```
    C:\Users\Databoxuser>net use \\169.254.143.85\exportbvtdataset2_BlockBlob /u:exportbvtdataset2
    Enter the password for 'exportbvtdataset2' to connect to '169.254.143.85':
    The command completed successfully.
    ```

4. Windows + R을 누르고 **실행** 창에서 `\\<device IP address>`를 지정합니다. **확인**을 선택하여 파일 탐색기를 엽니다.
    
    ![파일 탐색기를 통해 공유에 연결](media/data-box-deploy-export-copy-data/connect-shares-file-explorer-1.png)

    이제 공유가 폴더로 표시될 것입니다.
    
    ![파일 탐색기를 통해 공유에 연결 2](media/data-box-deploy-export-copy-data/connect-shares-file-explorer-2.png)

    
Linux 클라이언트를 사용하는 경우 다음 명령을 사용하여 SMB 공유를 탑재합니다. 아래에서 "vers" 매개 변수는 Linux 호스트에서 지원하는 SMB 버전입니다. 아래 명령에서 적절한 버전을 삽입합니다. Data Box에서 지원하는 SMB 버전은 [Linux 클라이언트에 지원되는 파일 시스템](https://docs.microsoft.com/azure/databox/data-box-system-requirements#supported-file-systems-for-linux-clients)을 참조하세요. 

```console
sudo mount -t nfs -o vers=2.1 169.254.143.85:/exportbvtdataset2_BlockBlob /home/databoxubuntuhost/databox
```

## <a name="copy-data-from-data-box"></a>Data Box에서 데이터 복사

Data Box 공유에 연결된 후에는 데이터를 복사합니다.

[!INCLUDE [data-box-export-review-logs](../../includes/data-box-export-review-logs.md)]


 SMB 공유에 연결한 후 데이터 복사를 시작합니다. Robocopy처럼 SMB 호환 파일 복사 도구를 사용하여 데이터를 복사할 수 있습니다. Robocopy 명령을 사용하여 여러 복사 작업을 시작할 수 있습니다. 


Robocopy 명령에 대한 자세한 내용은 [Robocopy 및 몇 가지 예제](https://social.technet.microsoft.com/wiki/contents/articles/1073.robocopy-and-a-few-examples.aspx)를 참조하세요.

복사가 완료되면 **대시보드**로 이동하여 디바이스에서 사용 중인 공간과 여유 공간을 확인합니다.

이제 Data Box를 Microsoft로 보낼 수 있습니다.


## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure Data Box 항목에 대해 다음과 같은 내용을 알아보았습니다.

> [!div class="checklist"]
>
> * 필수 구성 요소
> * Data Box에 연결
> * Data Box에서 데이터 복사

Data Box를 Microsoft로 다시 배송하는 방법을 알아보려면 다음 자습서로 계속 진행하세요.

> [!div class="nextstepaction"]
> [Microsoft로 Azure Data Box 배송](./data-box-deploy-export-picked-up.md)

