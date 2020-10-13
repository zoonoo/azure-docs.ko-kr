---
title: Azure Lab Services-공유 이미지 갤러리에 사용자 지정 이미지 업로드
description: 사용자 지정 이미지를 공유 이미지 갤러리에 업로드 하는 방법을 설명 합니다. 대학 IT 부서에서는 특히 도움이 되는 이미지 가져오기를 찾을 수 있습니다.
ms.date: 09/30/2020
ms.topic: how-to
ms.openlocfilehash: cd701215eb375b7f9b867ba05082afc7ed348ff7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91712400"
---
# <a name="upload-a-custom-image-to-shared-image-gallery"></a>Shared Image Gallery에 사용자 지정 이미지 업로드

공유 이미지 갤러리는 Azure Lab Services에서 랩을 만들기 위한 고유한 사용자 지정 이미지를 가져오는 데 사용할 수 있습니다. 대학 IT 부서에서는 다음과 같은 이유로 특히 유용 하 게 사용할 수 있습니다. 

* 랩의 템플릿 VM을 사용 하 여 이미지를 수동으로 만들 필요는 없습니다.
* SCCM, 끝점 관리자 등의 다른 도구를 사용 하 여 만든 이미지를 업로드할 수 있습니다.

이 문서에서는 사용자 지정 이미지를 가져와서 Azure Lab Services에 사용 하기 위해 수행할 수 있는 단계를 설명 합니다. 

> [!IMPORTANT]
> 물리적 랩 환경에서 Az Labs로 이미지를 이동 하는 경우 appropriatly를 재구성 해야 합니다. 실제 랩에서 기존 이미지를 다시 사용 하면 안 됩니다. <br/>자세한 내용은 [물리적 랩에서 Azure Lab Services로 이동](https://techcommunity.microsoft.com/t5/azure-lab-services/moving-from-a-physical-lab-to-azure-lab-services/ba-p/1654931) 블로그 게시물을 참조 하세요.

## <a name="bring-custom-image-from-a-physical-lab-environment"></a>실제 랩 환경에서 사용자 지정 이미지 가져오기

다음 단계는 실제 랩 환경에서 시작 하는 사용자 지정 이미지를 가져오는 방법을 보여 줍니다. 그런 다음이 환경에서 VHD를 만들고 Azure Lab Services 내에서 사용할 수 있도록 Azure의 공유 이미지 갤러리로 가져옵니다.

실제 랩 환경에서 VHD를 만들기 위한 많은 옵션이 있습니다. 다음 단계는 Windows Hyper-v VM에서 VHD를 만드는 방법을 보여 줍니다.

1. 이미지에서 만든 실제 랩 환경에서 Hyper-v VM으로 시작 합니다.
    1. VM은 1 세대 VM으로 만들어야 합니다.
    1. VM은 고정 디스크 크기를 사용 해야 합니다. 또한이 창에서 디스크의 크기를 지정할 수 있습니다. 디스크 크기는 128 GB 보다 크지 않아야 합니다.<br/>    
    디스크 크기가 128 GB 인 이미지는 Azure Lab Services에서 지원 되지 >. 
       
        :::image type="content" source="./media/upload-custom-image-shared-image-gallery/connect-virtual-hard-disk.png" alt-text="가상 하드 디스크 연결":::   
    1. 일반적으로 VM을 이미지 합니다.
1. [VM에 연결 하 고 Azure에 대해 준비](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image)합니다.
    1. [Azure에 대한 Windows 구성 설정](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image#set-windows-configurations-for-azure)
    1. [VM 연결을 보장 하는 데 필요한 최소한의 Windows 서비스를 확인 합니다.](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image#check-the-windows-services)
    1. [원격 데스크톱 레지스트리 설정 업데이트](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image#update-remote-desktop-registry-settings)
    1. [Windows 방화벽 규칙 구성](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image#configure-windows-firewall-rules)
    1. Windows 업데이트 설치
    1. [여기에 표시 된 대로 Azure VM 에이전트 및 추가 구성을 설치 합니다.](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image#complete-the-recommended-configurations) 
    
    위의 단계에서는 특수 이미지를 만듭니다. 일반화 된 이미지를 만드는 경우 [SysPrep](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image#determine-when-to-use-sysprep)도 실행 해야 합니다. <br/>
        이미지에 포함 된 소프트웨어에 필요한 사용자 디렉터리 (파일, 사용자 계정 정보 등이 포함 될 수 있음)를 유지 관리 하려는 경우 특수 이미지를 만들어야 합니다.
1. **Hyper-v** 는 기본적으로 **VHDX** 파일을 만들기 때문에 VHD 파일로 변환 해야 합니다.
    1. **Hyper-v 관리자**  ->  **작업**  ->  **디스크 편집**으로 이동 합니다.
    1. 여기에서는 VHDX에서 VHD로 디스크를 **변환** 하는 옵션을 사용할 수 있습니다.
    1. 디스크 크기를 확장 하려는 경우 128을 초과 하지 않아야 합니다.        
        :::image type="content" source="./media/upload-custom-image-shared-image-gallery/choose-action.png" alt-text="가상 하드 디스크 연결" 탭을 사용 하 여 디스크 크기를 선택할 수 있습니다. 앞서 언급 했 듯이 크기는 128 > 하지 않아야 합니다.
1. 관리 디스크의 스냅숏을 만듭니다.
    [포털 또는 powershell을 사용 하 여 스냅숏 만들기](https://docs.microsoft.com/azure/virtual-machines/windows/snapshot-copy-managed-disk)에 설명 된 대로 powershell에서 Azure Portal를 사용 하거나 Storage 탐색기 내에서이 작업을 수행할 수 있습니다.
1. 공유 이미지 갤러리에서 이미지 정의 및 버전을 만듭니다.
    1. [이미지 정의를 만듭니다](https://docs.microsoft.com/azure/virtual-machines/windows/shared-images-portal#create-an-image-definition).
    1. 특수/일반화 된 이미지를 만드는 경우에도 여기를 지정 해야 합니다.
1. Azure Lab Services에서 랩을 만들고 공유 이미지 갤러리에서 사용자 지정 이미지를 선택 합니다.

    원래 Hyper-v VM에 OS가 설치 된 후 디스크를 확장 한 경우 할당 되지 않은 디스크 공간을 사용 하려면 Windows에서 C 드라이브를 확장 해야 합니다. 이렇게 하려면 랩을 만든 후에 템플릿 VM에 로그인 한 다음, [기본 볼륨 확장](https://docs.microsoft.com/windows-server/storage/disk-management/extend-a-basic-volume)에 표시 되는 것과 유사한 단계를 수행 합니다. UI를 통해 PowerShell을 사용 하는 것 외에도이 작업을 수행할 수 있는 옵션이 있습니다.

## <a name="next-steps"></a>다음 단계

* [공유 이미지 갤러리 개요](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries)
* [공유 이미지 갤러리를 사용 하는 방법](how-to-use-shared-image-gallery.md)
