---
title: Azure Data Box Gateway를 사용하여 데이터 전송 | Microsoft Docs
description: Data Box Gateway 장치에 공유를 추가하고 연결하는 방법에 대해 알아봅니다.
services: databox-edge-gateway
documentationcenter: NA
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: databox-edge-gateway
ms.devlang: NA
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/24/2018
ms.author: alkohli
ms.custom: ''
ms.openlocfilehash: 9560166f9733e1370f99f5f76a6ca3a58567865e
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/26/2018
ms.locfileid: "47220245"
---
# <a name="tutorial-transfer-data-with-azure-data-box-gateway-preview"></a>자습서: Azure Data Box Gateway를 사용하여 데이터 전송(미리 보기)


## <a name="introduction"></a>소개

이 문서에서는 Data Box Gateway에서 공유를 추가하고 연결하는 방법에 대해 설명합니다. 공유가 추가되면 Data Box Gateway 장치에서 데이터를 Azure로 전송할 수 있습니다.

이 절차를 완료하는 데 약 10분이 걸릴 수 있습니다. 

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 공유 추가
> * 공유에 연결

> [!IMPORTANT]
> - Data Box Gateway는 미리 보기로 제공되고 있습니다. 이 솔루션을 주문하고 배포하기 전에 [미리 보기에 대한 Azure 서비스 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 검토하세요. 
 
## <a name="prerequisites"></a>필수 조건

Data Box Gateway에 공유를 추가하기 전에 다음 사항을 확인합니다.

* [Hyper-V에서 Data Box Gateway 프로비전](data-box-gateway-deploy-provision-hyperv.md) 또는 [VMware에서 Data Box Gateway 프로비전](data-box-gateway-deploy-provision-vmware.md)에서 설명한 대로 가상 장치를 프로비전하고 연결했습니다. 

    가상 장치는 [Azure Data Box Gateway 연결 및 활성화](data-box-gateway-deploy-connect-setup-activate.md)에서 자세히 설명한 대로 활성화되어 공유를 만들고 데이터를 전송할 준비가 됩니다.


## <a name="add-a-share"></a>공유 추가

공유를 만들려면 [Azure Portal](https://portal.azure.com/)에서 다음 단계를 수행합니다.

1. Azure Portal로 돌아갑니다. **모든 리소스**로 이동하여 Data Box Gateway 리소스를 검색합니다.
    
2. 필터링된 리소스 목록에서 Data Box Gateway 리소스를 선택한 다음, **개요**로 이동합니다. 장치 명령 모음에서 **+ 공유 추가**를 클릭합니다.
   
   ![공유 추가](./media/data-box-gateway-deploy-add-shares/click-add-share.png)

4. **공유 추가**에서 공유 설정을 지정합니다. 공유에 대한 고유한 이름을 제공합니다. 

   공유 이름에는 숫자, 소문자 및 하이픈만 포함될 수 있습니다. 공유 이름은 3 -63자이며 문자 또는 숫자로 시작해야 합니다. 각 하이픈의 앞과 뒤에는 하이픈이 아닌 문자가 있어야 합니다.
    
5. 공유에 대한 **유형**을 선택합니다. 유형은 SMB 또는 NFS일 수 있으며, SMB가 기본값입니다. SMB는 Windows 클라이언트에 대한 표준이며, NFS는 Linux 클라이언트에 사용됩니다. SMB 공유 또는 NFS 공유 중에서 어느 것을 선택하는지에 따라 제공되는 옵션이 약간 다릅니다. 

6. 공유가 상주할 저장소 계정을 제공해야 합니다. 컨테이너가 아직 없으면 저장소 계정에 공유 이름이 포함된 컨테이너가 만들어집니다. 컨테이너가 이미 있으면 기존 컨테이너가 사용됩니다. 
    
7. **저장소 서비스**는 블록 Blob, 페이지 Blob 또는 파일 중에서 선택합니다. 선택하는 서비스 유형은 데이터를 Azure에 저장할 형식에 따라 달라집니다. 예를 들어, 이 인스턴스에서는 데이터가 Blob 블록으로 Azure에 저장되도록 하므로 [블록 Blob]을 선택합니다. [페이지 Blob]을 선택하는 경우 데이터가 512바이트로 정렬되었는지 확인해야 합니다. VHDX는 항상 512바이트로 정렬됩니다.
   
8. 이 단계는 SMB 공유 또는 NFS 공유 중에서 어느 것을 만드는지에 따라 달라집니다. 
     
    - **SMB 공유를 만드는 경우** - 모든 권한 로컬 사용자 필드의 **새로 만들기** 또는  **기존 항목 사용** 중에서 선택합니다. 새 로컬 사용자를 만드는 경우 **사용자 이름**, **암호**, **암호 확인**을 차례로 제공합니다. 이렇게 하면 로컬 사용자에게 해당 권한이 할당됩니다. 여기에서 권한을 할당한 후에 파일 탐색기를 사용하여 해당 권한을 수정할 수 있습니다.
    
        ![SMB 공유 추가](./media/data-box-gateway-deploy-add-shares/add-share-smb-1.png)
        
        이 공유 데이터에 대해 **읽기 작업만 허용**을 선택하면 읽기 전용 사용자를 지정하는 옵션이 제공됩니다.
        
    - **NFS 공유를 만드는 경우** - 공유에 액세스할 수 있도록 허용된 클라이언트의 IP 주소를 제공해야 합니다.

        ![NFS 공유 추가](./media/data-box-gateway-deploy-add-shares/add-share-nfs-1.png)
   
9. **만들기**를 클릭하여 공유를 만듭니다. 
    
    공유 만들기가 진행 중이라는 알림이 표시됩니다. 지정한 설정으로 공유가 만들어지면 **공유** 블레이드를 업데이트하여 새 공유가 반영됩니다. 
    
    ![업데이트된 공유 목록](./media/data-box-gateway-deploy-add-shares/updated-list-of-shares.png) 

## <a name="connect-to-the-share"></a>공유에 연결

공유에 연결하려면 Data Box Gateway에 연결된 Windows Server 클라이언트에서 다음 단계를 수행합니다.


1. 명령 창을 엽니다. 명령 프롬프트에 다음을 입력합니다.

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

    메시지가 표시되면 공유 암호를 입력합니다. 이 명령의 출력 샘플은 다음과 같습니다.

    ```powershell
    Microsoft Windows [Version 18.8.16299.192) 
    (c) 2817 microsoft Corporation. All rights reserved . 
    
    C: \Users\GatewayUser>net use \\10.10.10.60\newtestuser /u:Tota11yNewUser 
    Enter the password for 'TotallyNewUser' to connect to '10.10.10.60' • 
    The command completed successfully. 
    
    C: \Users\GatewayUser>
    ```   


2. Windows + R을 누르고 **실행** 창에서 `\\<device IP address>`를 지정합니다. **확인**을 클릭합니다. 그러면 [파일 탐색기]가 열립니다. 폴더로 만든 공유가 표시됩니다. 공유(폴더)를 선택하고 두 번 클릭하여 콘텐츠를 봅니다.
 
    ![SMB 공유에 연결](./media/data-box-gateway-deploy-add-shares/connect-to-share2.png)-->

    공유가 생성되면 데이터가 이러한 공유에 기록되고, 장치에서 해당 데이터를 클라우드로 푸시합니다.

### <a name="connect-to-an-nfs-share"></a>NFS 공유에 연결

Data Box 에지에 연결된 Linux 클라이언트에서 다음 단계를 수행합니다.

1. 클라이언트에 NFSv4 클라이언트가 설치되어 있는지 확인합니다. NFS 클라이언트를 설치하려면 다음 명령을 사용합니다.

   `sudo apt-get install nfs-common`

    자세한 내용을 알아보려면 [NFSv4 클라이언트 설치](https://help.ubuntu.com/community/SettingUpNFSHowTo#NFSv4_client)로 이동하세요.

2. NFS 클라이언트가 설치되면 다음 명령을 사용하여 Data Box Gateway 장치에서 만든 NFS 공유를 탑재합니다.

   `sudo mount <device IP>:/<NFS share on device> /home/username/<Folder on local Linux computer>`

    탑재를 설정하기 전에 로컬 컴퓨터에서 탑재 지점으로 작동할 디렉터리가 이미 만들어져 있고 파일이나 하위 폴더가 포함되어 있지 않은지 확인합니다.

    다음 예제에서는 NFS를 통해 게이트웨이 장치의 공유에 연결하는 방법을 보여 줍니다. 가상 장치 IP는 `10.10.10.60`이며, `mylinuxshare2` 공유는 ubuntuVM에 탑재되고, 탑재 지점은 `/home/databoxubuntuhost/gateway`입니다.

    `sudo mount -t nfs 10.10.10.60:/mylinuxshare2 /home/databoxubuntuhost/gateway`

> [!NOTE] 
> 미리 보기 릴리스에 적용되는 주의 사항은 다음과 같습니다.
> - 공유에 파일이 만들어지면 파일 이름 바꾸기가 지원되지 않습니다. 
> - 공유에서 파일을 삭제하더라도 저장소 계정의 항목은 삭제되지 않습니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음과 같은 Data Box Gateway 항목에 대해 알아보았습니다.

> [!div class="checklist"]
> * 공유 추가
> * 공유에 연결


Data Box Gateway를 관리하는 방법을 알아보려면 다음 자습서로 계속 진행하세요.

> [!div class="nextstepaction"]
> [로컬 웹 UI를 사용하여 Data Box Gateway 관리](http://aka.ms/dbg-docs)


