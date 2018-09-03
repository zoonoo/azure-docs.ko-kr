---
title: Microsoft Azure Data Box Disk 설정 | Microsoft Docs
description: 이 자습서를 사용하여 Azure Data Box Disk를 설정하는 방법 알아보기
services: databox
documentationcenter: NA
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: databox
ms.devlang: NA
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/28/2018
ms.author: alkohli
Customer intent: As an IT admin, I need to be able to order Data Box Disk to upload on-premises data from my server onto Azure.
ms.openlocfilehash: 6fcc7823a7e2f2f1e280622a1fa05d4417a71546
ms.sourcegitcommit: a1140e6b839ad79e454186ee95b01376233a1d1f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2018
ms.locfileid: "43143485"
---
# <a name="tutorial-unpack-connect-and-unlock-azure-data-box-disk"></a>자습서: Azure Data Box Disk 압축 풀기, 연결 및 잠금 해제

이 자습서는 Azure Data Box Disk의 압축을 풀고, 연결 및 잠금 해제하는 방법을 설명합니다.

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * Data Box Disk 압축 풀기
> * Data Box Disk를 연결하고 잠금을 해제합니다.

## <a name="prerequisites"></a>필수 조건

시작하기 전에 다음 사항을 확인합니다.

1. [자습서: Azure Data Box Disk 주문](data-box-disk-deploy-ordered.md)을 완료했습니다.
2. 디스크를 받고 포털에서 작업 상태가 **배달됨**으로 업데이트되었습니다.
3. Data Box Disk 잠금 해제 도구를 설치할 수 있는 호스트 컴퓨터가 있습니다. 호스트 컴퓨터는 다음 사항이 필수입니다.
    - [지원되는 운영 체제](data-box-disk-system-requirements.md)를 실행합니다.
    - [Windows PowerShell 4가 설치되어](https://www.microsoft.com/download/details.aspx?id=40855) 있습니다.
    - [.NET Framework 4.5.1이 설치](https://www.microsoft.com/download/details.aspx?id=30653)되어 있습니다.
    - [BitLocker를 사용하도록 설정](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-how-to-deploy-on-windows-server)되어 있습니다.
    - [Windows Management Framework 4가 설치](https://www.microsoft.com/en-us/download/details.aspx?id=40855)되어 있습니다. 

## <a name="unpack-your-disks"></a>디스크 압축 풀기

 다음 단계를 수행하여 디스크의 압축을 풉니다.

1. Data Box Disks는 작은 배송 상자에 넣어 발송됩니다. 상자를 열고 해당 콘텐츠를 제거합니다. 상자에 1~5개의 SSD(반도체 디스크) 및 디스크당 USB 연결 케이블이 있는지 확인합니다. 변조의 증거 또는 기타 명백한 손상에 대해 상자를 검사합니다. 

    ![Data Box Disk 배송 패키지](media/data-box-disk-deploy-set-up/data-box-disk-ship-package1.png)

2. 배송 상자가 훼손되었거나 심각하게 손상된 경우 상자를 열지 마십시오. 디스크가 올바른 작업 주문에 있는지 여부를 평가하는 데 도움을 얻고 대체물을 배송 받아야 하는지 Microsoft 지원에 문의합니다.
3. 상자에 반송 배송을 위한 포장용 레이블(현재 레이블 아래)을 포함하는 투명한 케이스가 있는지 확인합니다. 이 레이블이 손실되거나 손상된 경우 Azure Portal에서 항상 새 레이블을 다운로드하고 인쇄할 수 있습니다. 

    ![Data Box Disk 포장용 레이블](media/data-box-disk-deploy-set-up/data-box-disk-package-ship-label.png)

4. 디스크의 반송 배송을 위해 상자와 패키징 폼을 보관합니다.

## <a name="connect-and-unlock-your-disks"></a>디스크 연결 및 잠금 해제

다음 단계를 수행하여 디스크를 연결하고 잠금 해제합니다.

1. 포함된 케이블을 사용하여 필수 구성 요소에서 설명한 것처럼 지원되는 OS를 실행하는 Windows 컴퓨터에 디스크를 연결합니다. 

    ![Data Box Disk 연결](media/data-box-disk-deploy-set-up/data-box-disk-connect-unlock.png)    
    
2. Azure Portal에서 **일반 > 장치 세부 정보**로 이동합니다. 
3. **Data Box Disk 잠금 해제 도구 다운로드**를 클릭합니다. 

    ![Data Box Disk 잠금 해제 도구 다운로드](media/data-box-disk-deploy-set-up/data-box-disk-unload1.png)     

4. 데이터를 복사하는 데 사용할 동일한 컴퓨터에서 도구를 추출합니다.
5. 명령 프롬프트 창을 열거나 동일한 컴퓨터에서 관리자 권한으로 Windows PowerShell을 실행합니다.
6. (선택 사항) 디스크를 잠금 해제하는 데 사용하는 컴퓨터가 운영 체제 요구 사항을 충족하는지 확인하려면 시스템 검사 명령을 실행합니다. 샘플 출력은 다음과 같습니다. 

    ```powershell
    Windows PowerShell
    Copyright (C) Microsoft Corporation. All rights reserved.
    
    PS C:\DataBoxDiskUnlockTool\DiskUnlock> .\DataBoxDiskUnlock.exe /SystemCheck
    Successfully verified that the system can run the tool.
    PS C:\DataBoxDiskUnlockTool\DiskUnlock>
    ``` 

7. Azure Portal에서 **일반 > 장치 세부 정보**로 이동합니다. 복사 아이콘을 사용하여 암호를 복사합니다.
8. `DataBoxDiskUnlock.exe`를 실행하여 암호를 제공합니다. 디스크에 할당한 드라이브 문자가 표시됩니다. 샘플 출력은 다음과 같습니다.

    ```powershell
    PS C:\WINDOWS\system32> cd C:\DataBoxDiskUnlockTool\DiskUnlock
    PS C:\DataBoxDiskUnlockTool\DiskUnlock> .\DataBoxDiskUnlock.exe
    Enter the passkeys (format: passkey1;passkey2;passkey3):
    testpasskey1
    
    Following volumes are unlocked and verified.
    Volume drive letters: D:
    
    PS C:\DataBoxDiskUnlockTool\DiskUnlock>
    ```

9. 이후 디스크 재삽입을 위해 6-8단계를 반복합니다. Data Box Disk 잠금 해제 도구와 관련하여 도움이 필요하면 도움말 명령을 사용합니다.   

    ```powershell
    PS C:\DataBoxDiskUnlockTool\DiskUnlock> .\DataBoxDiskUnlock.exe /help
    USAGE:
    DataBoxUnlock /PassKeys:<passkey_list_separated_by_semicolon>
    
    Example: DataBoxUnlock /PassKeys:<your passkey>
    Example: DataBoxUnlock /SystemCheck
    Example: DataBoxUnlock /Help
    
    /PassKeys:       Get this passkey from Azure DataBox Disk order. The passkey unlocks your disks.
    /SystemCheck:    This option checks if your system meets the requirements to run the tool.
    /Help:           This option provides help on cmdlet usage and examples.
    
    PS C:\DataBoxDiskUnlockTool\DiskUnlock>
    ```  
10. 디스크가 잠금 해제되면 디스크의 콘텐츠를 볼 수 있습니다.    

    ![Data Box Disk 콘텐츠](media/data-box-disk-deploy-set-up/data-box-disk-content.png) 

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure Data Box Disk 항목에 대해 다음과 같은 내용을 알아보았습니다.

> [!div class="checklist"]
> * Data Box Disk 압축 풀기
> * Data Box Disk 연결 및 잠금 해제


Data Box Disk에서 데이터를 복사하는 방법을 알아보려면 다음 자습서로 계속 진행하세요.

> [!div class="nextstepaction"]
> [Data Box Disk에서 데이터 복사](./data-box-disk-deploy-copy-data.md)

