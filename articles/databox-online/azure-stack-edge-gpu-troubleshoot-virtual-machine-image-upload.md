---
title: Azure Stack Edge Pro GPU에서 가상 머신 이미지 업로드 문제 해결 | Microsoft Docs
description: Azure Stack Edge Pro GPU에서 가상 머신 이미지를 업로드, 다운로드 또는 삭제할 때 발생하는 문제를 해결하는 방법을 설명합니다.
services: databox
author: v-dalc
ms.service: databox
ms.subservice: edge
ms.topic: troubleshooting
ms.date: 05/13/2021
ms.author: alkohli
ms.openlocfilehash: 3ec5cfb952e666832fe887f65672e228004423bc
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/26/2021
ms.locfileid: "110483166"
---
# <a name="troubleshoot-virtual-machine-image-uploads-in-azure-stack-edge-pro-gpu"></a>Azure Stack Edge Pro GPU에서 가상 머신 이미지 업로드 문제 해결

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

이 문서에서는 Azure Stack Edge Pro GPU 장치에서 VM(가상 머신) 이미지를 다운로드하고 관리할 때 발생하는 문제를 해결하는 방법을 설명합니다.


## <a name="unable-to-add-vm-image-to-blob-container"></a>Blob 컨테이너에 VM 이미지를 추가할 수 없습니다

**오류 설명:** Azure Portal에서 Blob 컨테이너에 VM 이미지를 업로드하려고 할 때 **추가** 단추를 사용할 수 없으며 이미지를 업로드할 수 없습니다. **추가** 단추는 리소스 그룹에 대한 필수 참가자 역할 권한이 없거나 디바이스에 대한 구독이 없는 경우 사용할 수 없습니다.

**추천 솔루션:** 리소스 그룹 또는 스토리지 계정에 파일을 추가하는 데 필요한 기여자 권한이 있는지 확인합니다. 자세한 내용은 [Azure Stack Edge 리소스에 대한 필수 조건](azure-stack-edge-deploy-prep.md#prerequisites)을 참조하세요.


## <a name="invalid-blob-type-for-the-source-blob-uri"></a>원본 Blob URI에 대한 잘못된 Blob 유형

**오류 설명:** 블록 Blob으로 저장된 VHD를 다운로드할 수 없습니다. 다운로드하려면 VHD를 페이지 Blob으로 저장해야 합니다.

**추천 솔루션:** VHD를 페이지 Blob으로 Azure Storage 계정에 업로드합니다. 그런 다음, Blob을 다운로드합니다. 업로드 지침은 [업로드에 Storage Explorer 사용](azure-stack-edge-gpu-deploy-virtual-machine-templates.md#use-storage-explorer-for-upload)을 참조하세요.


## <a name="only-blobs-formatted-as-vhds-can-be-imported"></a>VHD로 형식이 지정된 Blob만 가져올 수 있습니다

**오류 설명:** 서식 지정 요구 사항을 충족하지 않아 VHD를 가져올 수 없습니다. 가져오려면 가상 하드 디스크는 고정 크기인 1세대 VHD여야 합니다.

**추천 솔루션:** 

- [Azure Stack Edge Pro GPU에 VM을 배포하기 위해 Windows VHD에서 범용 이미지 준비](azure-stack-edge-gpu-prepare-windows-vhd-generalized-image.md)의 단계에 따라 원본 VHD 또는 VHDX에서 1세대 가상 머신용 고정 크기 VHD를 만듭니다.

- PowerShell을 사용하려는 경우:

   - Hyper-V의 경우 Windows PowerShell 모듈에서 [Convert-VHD](/powershell/module/hyper-v/convert-vhd?view=windowsserver2019-ps&preserve-view=true)를 사용할 수 있습니다. Convert-VHD를 사용하여 2세대 VM에서 1세대로 VM 이미지를 변환할 수 없습니다. 대신 [Azure Stack Edge Pro GPU에 VM을 배포하기 위해 Windows VHD에서 범용 이미지 준비](azure-stack-edge-gpu-prepare-windows-vhd-generalized-image.md)의 포털 프로시저를 사용하세요.
  
   - 현재 VHD 유형을 찾아야 하는 경우 [Get-VHD](/powershell/module/hyper-v/get-vhd?view=windowsserver2019-ps&preserve-view=true)를 사용합니다.


## <a name="the-condition-specified-using-http-conditional-headers-is-not-met"></a>HTTP 조건부 헤더를 사용하여 지정한 조건이 충족되지 않습니다.

**오류 설명:** Azure에서 다운로드하려고 할 때 VHD를 변경하면 Azure의 VHD가 다운로드 중인 VHD와 일치하지 않으므로 다운로드가 실패합니다. 이 오류는 Azure에 VHD 업로드가 완료되기 전에 다운로드를 시도할 때에도 발생합니다.

**추천 솔루션:** VHD 업로드가 완료되고 VHD가 변경되지 않을 때까지 기다립니다. 그런 다음 VHD 다운로드를 다시 시도합니다.


## <a name="next-steps"></a>다음 단계

* [Azure Portal을 통해 VM을 배포](azure-stack-edge-gpu-deploy-virtual-machine-portal.md)하는 방법을 알아봅니다.
* [Azure PowerShell을 통해 VM을 배포](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md)하는 방법을 알아봅니다.
