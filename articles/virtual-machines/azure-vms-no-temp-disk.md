---
title: 로컬 임시 디스크가 없는 Azure VM의 크기 FAQ
description: 이 문서에서는 로컬 임시 디스크를 포함하지 않는 Microsoft Azure VM의 크기에 대한 FAQ(자주 묻는 질문)의 답변을 제공합니다.
author: brbell
ms.service: virtual-machines
ms.topic: conceptual
ms.subservice: sizes
ms.author: brbell
ms.reviewer: mimckitt
ms.date: 06/15/2020
ms.openlocfilehash: bd4dcbdc7ab13d18ef7f2d7102c56d1bd8d8758d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104582105"
---
# <a name="azure-vm-sizes-with-no-local-temporary-disk"></a>로컬 임시 디스크가 없는 Azure VM의 크기 
이 문서에서는 로컬 임시 디스크가 없는 Azure VM의 크기에 대한 FAQ(자주 묻는 질문)의 답변을 제공합니다(예: 로컬 임시 디스크 없음). 해당 VM 크기에 대한 자세한 내용은 [Dv4 및 Dsv4 시리즈에 대한 사양(범용 워크로드)](dv4-dsv4-series.md) 또는 [Ev4 및 Esv4 시리즈 사양(메모리 최적화 워크로드)](ev4-esv4-series.md)을 참조하세요.

## <a name="what-does-no-local-temp-disk-mean"></a>로컬 임시 디스크가 없다는 건 무슨 의미인가요? 
과거에는 작은 로컬 디스크(예: D: 드라이브)가 포함된 VM 용량(예: Standard_D2s_v3, Standard_E48_v3)을 사용했습니다. 이제 VM 크기가 새롭게 바뀌어서 그 작은 로컬 디스크는 더 이상 없습니다. 그러나 계속 표준 HDD, 프리미엄 SSD 또는 울트라 SSD에 연결할 수는 있습니다.

## <a name="what-if-i-still-want-a-local-temp-disk"></a>여전히 로컬 임시 디스크를 필요한 경우 어떻게 하나요?
워크로드에 로컬 임시 디스크가 필요한 경우 새로운 [Ddv4 및 Ddsv4](ddv4-ddsv4-series.md), 혹은 [Edv4 및 Edsv4](edv4-edsv4-series.md) VM 크기를 사용할 수도 있습니다. 이러한 크기는 이전 v3 크기 대비 50% 더 큰 임시 디스크를 제공합니다.

> [!NOTE]
> 로컬 임시 디스크는 영구적이지 않습니다. 데이터를 영구적으로 유지하려면 표준 HDD, 프리미엄 SSD 또는 울트라 SSD 옵션을 사용하세요. 

## <a name="what-are-the-differences-between-these-new-vm-sizes-and-the-general-purpose-dv3dsv3-or-the-memory-optimized-ev3esv3-vm-sizes-that-i-am-used-to"></a>해당 새 VM 크기가 지금까지 익숙했던 범용 Dv3/Dsv3 또는 메모리 최적화 Ev3/Esv3 VM 크기와 다른 점은 무엇인가요? 
| 로컬 임시 디스크를 사용하는 v3 VM 제품군   | 로컬 임시 디스크를 사용하는 새 v4 제품군 | 로컬 임시 디스크가 없는 새 v4 제품군 |
|---|---|---|
| Dv3   | Ddv4 | Dv4 |
| Dsv3 | Ddsv4  | Dsv4 |
| Ev3   | Edv4  | Ev4 |
| Esv3 | Edsv4 |    Esv4 | 

## <a name="can-i-resize-a-vm-size-that-has-a-local-temp-disk-to-a-vm-size-with-no-local-temp-disk"></a>로컬 임시 디스크가 있는 VM 크기를 로컬 임시 디스크가 없는 VM 크기로 조정할 수 있나요?  
아니요. 크기 조정에 사용할 수 있는 조합은 다음과 같습니다. 

1. VM(로컬 임시 디스크 있음) -> VM(로컬 임시 디스크 있음) 및 
2. VM(로컬 임시 디스크 없음) -> VM(로컬 임시 디스크 없음) 

해결 방법에 관심이 있는 경우 다음 질문을 참조하세요.

> [!NOTE]
> 이미지가 리소스 디스크에 종속되어 있거나 페이지 파일 또는 스왑 파일이 로컬 임시 디스크에 있는 경우 디스크 없는 이미지는 작동하지 않습니다. 대신 ‘디스크 있는’ 대안을 사용하세요. 

## <a name="how-do-i-migrate-from-a-vm-size-with-local-temp-disk-to-a-vm-size-with-no-local-temp-disk"></a>로컬 임시 디스크가 있는 VM 크기에서 로컬 임시 디스크가 없는 VM 크기로 마이그레이션은 어떻게 하나요?  
다음 단계를 수행하여 마이그레이션을 할 수 있습니다. 

1. 로컬 임시 디스크가 있는 가상 머신(예: D: 드라이브)에 로컬 관리자로 연결합니다.
2. [Windows VM에서 D: 드라이브를 데이터 드라이브로 사용](./windows/change-drive-letter.md) 문서의 “일시적으로 pagefile.sys를 C 드라이브로 이동” 섹션의 지침에 따라 페이지 파일을 로컬 임시 디스크(D: 드라이브)에서 C: 드라이브로 이동합니다.

   > [!NOTE]
   > “일시적으로 pagefile.sys를 C 드라이브로 이동” 섹션의 지침을 따라 D: 드라이브를 Windows VM의 데이터 드라이브로 사용해 페이지 파일을 로컬 임시 디스크(D: 드라이브)에서 C: 드라이브로 옮깁니다. **설명된 단계와의 편차 때문에 다음 오류 메시지가 표시됩니다. “리소스 디스크에서 비 리소스 디스크 VM 크기로 변경하거나 그 반대의 경우는 허용되지 않기 때문에 VM 크기를 조정할 수 없습니다.”**

3. [포털 혹은 Azure CLI를 사용하여 스냅샷 만들기](./linux/snapshot-copy-managed-disk.md) 문서에 설명된 단계를 수행해서 VM의 스냅샷을 만듭니다. 
4. [CLI를 사용하여 스냅샷에서 가상 머신 만들기](/previous-versions/azure/virtual-machines/scripts/virtual-machines-linux-cli-sample-create-vm-from-snapshot) 문서에 설명된 단계에 따라 스냅샷을 사용해 새로운 디스크 없는 VM(예: Dv4, Dsv4, Ev4, Esv4 시리즈)을 만듭니다. 

## <a name="do-these-vm-sizes-support-both-linux-and-windows-operating-systems-os"></a>해당 VM 크기는 Linux 및 Windows OS(운영 체제)를 모두 지원하나요?
예.

## <a name="will-this-break-my-custom-scripts-custom-images-or-os-images-that-have-scratch-files-or-page-files-on-a-local-temp-disk"></a>로컬 임시 디스크에 스크래치 파일이나 페이지 파일이 있는 사용자 지정 스크립트, 사용자 지정 이미지, OS 이미지 등이 손상될 수 있나요?
사용자 지정 OS 이미지가 로컬 임시 디스크를 가리키는 경우에 디스크 없는 크기로는 이미지가 제대로 작동하지 않을 수 있습니다.

## <a name="have-questions-or-feedback"></a>질문이나 의견이 있으신가요?
[피드백 양식]( https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR_Y3toRKxchLjARedqtguBRUMzdCQkw0OVVRTldFUUtXSTlLQVBPUkVHSy4u)을 작성하세요. 

## <a name="next-steps"></a>다음 단계 
이 문서에서는 로컬 임시 디스크 없는 Azure VM과 관련된 가장 자주 묻는 질문에 대해 자세히 알아보았습니다. 해당 VM 크기에 대한 자세한 내용은 다음 문서를 참조하세요.

- [Dv4 및 Dsv4 시리즈 사양(범용 워크로드)](dv4-dsv4-series.md)
- [Ev4 및 Esv4 시리즈 사양(메모리 최적화 워크로드)](ev4-esv4-series.md)