---
title: Windows 부팅 관리자 오류-0xC0000428 상태 잘못 된 이미지 해시
titlesuffix: Azure Virtual Machines
description: 이 문서에서는 미리 보기 이미지를 사용 하 고 평가 기간이 만료 되어 Azure VM (가상 머신)을 부팅할 수 없는 문제를 해결 하는 단계를 제공 합니다.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 26c411f9-6c22-4f45-a445-c5781e547828
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 09/03/2020
ms.author: v-miegge
ms.openlocfilehash: f607ebb64b27c45ec696d7fcd431a0ba2342697f
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2020
ms.locfileid: "89447887"
---
# <a name="windows-boot-manager-error---0xc0000428-status-invalid-image-hash"></a>Windows 부팅 관리자 오류-0xC0000428 상태 잘못 된 이미지 해시

이 문서에서는 미리 보기 이미지를 사용 하 고 평가 기간이 만료 되어 Azure VM (가상 머신)을 부팅할 수 없는 문제를 해결 하는 단계를 제공 합니다.

## <a name="symptom"></a>증상

[부팅 진단을](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics) 사용 하 여 VM의 스크린샷을 볼 때 스크린샷에 Windows 부팅 관리자가 표시 되는 것을 볼 수 있습니다.

  `File: \windows\system32\boot\winload.exe`

  `Status: 0xc0000428`

  `Info: Windows cannot verify the digital signature for this file.`

  ![그림 1에는 "Ox0000428" 상태의 Windows 부팅 관리자와 "Windows에서이 파일에 대 한 디지털 서명을 확인할 수 없습니다." 정보가 표시 됩니다.](./media/windows-boot-error-invalid-image-hash/1-cannot-verify-signature.png)

또는 메시지:

  `File: \Windows\system32\winload.exe`

  `Status: 0xc0000428`

  `Info: The digital signature for this file couldn't be verified.`
    
  ![그림 2에는 "Ox0000428" 상태의 Windows 부팅 관리자와 "이 파일에 대 한 디지털 서명을 확인할 수 없습니다." 정보가 표시 됩니다.](./media/windows-boot-error-invalid-image-hash/2-digital-signature-not-verified.png)

## <a name="cause"></a>원인

VM을 빌드하는 데 사용 된 이미지는 RTM (Release to Manufacturing) 이미지가 아닌 만료 날짜가 포함 된 미리 보기 이미지입니다. 

미리 보기 이미지는 지정 된 수명 주기를 가지 며, 표시 되는 스크린샷은 만료 날짜를 전달할 때 표시 되며,이는 이미지의 평가판을 초과 하는 것을 의미 합니다.

### <a name="example-of-preview-images"></a>미리 보기 이미지의 예

`a699494373c04fc0bc8f2bb1389d6106__Windows-Server-Technical-Preview-201505.01-en.us-127GB.vhd`

미리 보기 이미지의 만료 날짜를 연장할 수 없습니다. 미리 보기가 만료 되 면 VM은 더 이상 부팅할 수 없게 됩니다.

- 제품에 따라 평가 기간이 달라질 수 있습니다. 예를 들어 Windows 미리 보기 이미지에는 180 일의 평가 기간이 있습니다.

- Azure에서 미리 보기 버전의 Windows 용 모든 이미지에는 프로덕션에 적합 하지 않으며 지정 된 평가 기간 또는 "미리 보기 릴리스"에만 사용할 수 있다는 설명이 포함 되어 있습니다.

## <a name="solution"></a>솔루션

이미지가 미리 보기 이미지 인 경우 사용 되는 이미지의 만료 날짜를 연장할 수 없습니다. 미리 보기가 아닌 이미지를 사용 하 여 [새 VM을 배포](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal) 해야 합니다. 아래 단계는 미리 보기 이미지를 사용 했는지 여부를 식별 하는 데 도움이 되 고,이 VM에서 새 VM으로 데이터를 전송 하는 데 도움이 되는 리소스를 제공 합니다. 이미지를 미리 보기 이미지로 확실 하 게 식별 한 경우 이제는 만료 되기 때문에 이미지를 복구할 수 없습니다.

기본 설정에 따라 Azure PowerShell 또는 Azure CLI를 사용 하 여 이미지를 쿼리하여 미리 보기 이미지 인지 확인할 수 있습니다. 이러한 명령을 사용 하 여 이미지가 미리 보기 이미지 인지 확인할 수 있습니다.

### <a name="query-using-azure-powershell"></a>Azure PowerShell를 사용 하 여 쿼리

1. Windows PowerShell 응용 프로그램을 엽니다.
1. 다음 명령을 실행 합니다.

   ```powershell
   $locName = "<LOCATION>" 
   $pubName = "<PUBLISHER NAME>" 
   $offerName = "<OFFER NAME>" 
   $skuName= "<YEAR WHEN THIS IMAGE WAS RELEASED>"
   Get-AzVMImagePublisher -Location $locName | Select $pubName
   Get-AzVMImageOffer -Location $locName -PublisherName $pubName | Select Offer
   Get-AzVMImageSku -Location $locName -PublisherName $pubName -Offer $offerName | Select Skus
   Get-AzVMImage -Location $locName -PublisherName $pubName -Offer $offerName -Skus $skuName | Select Version
   ```

- 이전 명령에서 `<LOCATION>` ,, 및를 `<PUBLISHER NAME>` `<OFFER NAME>` `<YEAR WHEN THIS IMAGE WAS RELEASED>` 명시 된 정보로 바꿉니다. 또한 "<" 및 ">" 기호를 제거 합니다.

  다음 예제를 참조하십시오.

  ```powershell
  $locName = "West US" 
  $pubName = "MicrosoftWindowsServer" 
  $offerName = "WindowsServer" 
  $skuName= "2016-Datacenter"
  Get-AzVMImagePublisher -Location $locName | Select $pubName
  Get-AzVMImageOffer -Location $locName -PublisherName $pubName | Select Offer
  Get-AzVMImageSku -Location $locName -PublisherName $pubName -Offer $offerName | Select Skus
  Get-AzVMImage -Location $locName -PublisherName $pubName -Offer $offerName -Skus $skuName | Select Version
  ```

### <a name="query-using-the-azure-cli"></a>Azure CLI를 사용 하 여 쿼리

1. 아직 설치 하지 않은 경우 [Azure CLI를 설치](https://docs.microsoft.com/cli/azure/install-azure-cli)해야 합니다.
1. 다운로드 한 후 명령 프롬프트 또는 PowerShell을 사용 하 여 명령을 입력 한 `az login` 다음 계정 자격 증명으로 로그인 합니다.
1. 로그인 한 후 다음 명령을 입력 합니다.

   ```powershell
   az vm image list-publishers --location "<LOCATION>"
   az vm image list-offers --location "West US" --publisher "<PUBLISHER NAME>"
   az vm image list-skus --location "West US" --publisher "<PUBLISHER NAME>" --offer "<OFFER NAME>"
   az vm image list  --location "West US" --publisher "<PUBLISHER NAME>" --offer "<OFFER NAME>" --sku "<YEAR WHEN THIS IMAGE WAS RELEASED>"
   ```

- 이전 명령에서 `<LOCATION>` ,, 및를 `<PUBLISHER NAME>` `<OFFER NAME>` `<YEAR WHEN THIS IMAGE WAS RELEASED>` 명시 된 정보로 바꿉니다. 또한 "<" 및 ">" 기호를 제거 합니다.

  다음 예제를 참조하십시오.

  ```powershell
  az vm image list-publishers --location "West US"
  az vm image list-offers --location "West US" --publisher "MicrosoftWindowsServer"
  az vm image list-skus --location "West US" --publisher "MicrosoftWindowsServer" --offer "WindowsServer"
  az vm image list  --location "West US" --publisher "MicrosoftWindowsServer" --offer "WindowsServer" --sku "2016-Datacenter"
  ```
