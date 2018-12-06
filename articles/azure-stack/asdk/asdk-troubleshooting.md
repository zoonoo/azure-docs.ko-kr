---
title: Microsoft Azure Stack 문제 해결 | Microsoft Docs
description: Azure Stack 개발 키트 ASDK () 문제 해결 정보입니다.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/15/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 3caa45064c41b641aa913e210aa698d818d5355e
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/06/2018
ms.locfileid: "52970274"
---
# <a name="microsoft-azure-stack-development-kit-asdk-troubleshooting"></a>Microsoft Azure Stack 개발 키트 (ASDK) 문제 해결
이 문서는 ASDK에 대 한 일반적인 문제 해결 정보를 제공합니다. 문서화 되지 않은 문제가 발생 하는 경우을 선택 했는지 확인 합니다 [Azure Stack MSDN 포럼](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack) 정보와 추가 지원을 요청 합니다.  

> [!IMPORTANT]
> ASDK 평가 환경 이므로 지원은 없습니다 공식를 통해 Microsoft 고객 지원 서비스 (CSS)를 제공 합니다.

이 섹션에 설명 된 문제 해결을 위한 권장 사항을 여러 원본에서 파생 된와 수도 있고 특정 문제를 해결할 수 있습니다. 코드 예제는 "있는 그대로" 제공 됩니다 하 고 예상된 결과 보장할 수 없습니다. 이 섹션에서는 제품 개선 사항이 구현 된 대로 자주 편집 하 고 업데이트 적용 됩니다.

## <a name="deployment"></a>배포
### <a name="deployment-failure"></a>배포 실패
를 설치 하는 동안 오류가 발생 하면 다시 시작할 수 있습니다 실패 한 단계에서 배포를 사용 하 여 다음 예제와 같이 배포 스크립트의 다시 실행된-옵션:

  ```powershell
  cd C:\CloudDeployment\Setup
  .\InstallAzureStackPOC.ps1 -Rerun
  ```

### <a name="at-the-end-of-the-deployment-the-powershell-session-is-still-open-and-doesnt-show-any-output"></a>배포 후에 PowerShell 세션을 여전히 열려 있고 어떠한 출력도 표시 되지 않습니다.
이 문제를 선택한 경우에 PowerShell 명령 창의 기본 동작 하면 때문일 수 있습니다. 개발 키트 배포에 성공 하지만 스크립트 창을 선택 하는 경우 일시 중지 되었습니다. 명령 창의 제목 표시줄에 "select" 라는 단어를 검색 하 여 설치를 완료 하는 것을 확인할 수 있습니다. ESC 키를 선택 취소를 누른 후 완료 메시지가 표시 되 합니다.

## <a name="virtual-machines"></a>가상 머신
### <a name="default-image-and-gallery-item"></a>기본 이미지 및 갤러리 항목
Azure Stack에서 Vm을 배포 하기 전에 Windows Server 이미지 및 갤러리 항목을 추가 해야 합니다.

### <a name="after-restarting-my-azure-stack-host-some-vms-may-not-automatically-start"></a>Azure Stack 호스트를 다시 시작한 후 일부 Vm 자동으로 시작할 수 없습니다.
호스트를 다시 부팅 한 후 Azure Stack 서비스를 즉시 사용할 수 없는 것을 확인할 수 있습니다. Azure Stack 때문에 이것이 [인프라 Vm](asdk-architecture.md#virtual-machine-roles) 고 RPs 일관성을 확인 하려면 시간 일부 있지만 결국 자동으로 시작 됩니다.

또한 Vm이 Azure Stack 개발 키트 호스트를 다시 부팅 후 자동으로 시작 하지 해당 테 넌 트를 확인할 수 있습니다. 이 알려진된 문제 및 몇 가지 수동 단계를 온라인 상태로 전환 해야 함:

1.  Azure Stack 개발 키트 호스트에서 시작 **장애 조치 클러스터 관리자** 시작 합니다.
2.  클러스터를 선택 **S Cluster.azurestack.local**합니다.
3.  **역할**을 선택합니다.
4.  테 넌 트 Vm에 표시 된 *저장* 상태입니다. 모든 인프라 Vm을 실행 하는 테 넌 트 Vm을 마우스 오른쪽 단추로 클릭 하 고 선택 **시작** VM 다시 시작 합니다.

### <a name="i-have-deleted-some-virtual-machines-but-still-see-the-vhd-files-on-disk-is-this-behavior-expected"></a>일부 가상 컴퓨터를 삭제 했지만 아직 디스크에 VHD 파일을 참조 하세요. 이 동작은 예상
예, 예상 되는 동작입니다. 때문에 이러한 방식으로 설계 되었습니다.

* VM을 삭제 하는 경우 Vhd는 삭제 되지 않습니다. 디스크는 리소스 그룹에 별도 리소스입니다.
* 저장소 계정 삭제 되 면 삭제는 즉시 Azure Resource Manager를 통해 표시 하지만 가비지 수집 실행 될 때까지 포함할 수 있습니다 디스크 저장소에 보관 계속 됩니다.

"분리 된" Vhd를 표시 하는 경우 삭제 된 저장소 계정에 대 한 폴더의 경우 일부가 알아야 것입니다. 저장소 계정 삭제 되지 않은 경우에 보통는 여전히 남아 있습니다.

자세한 내용은에서 보존 임계값 및 주문형 회수를 구성 하는 방법에 대 한 [저장소 계정 관리](../azure-stack-manage-storage-accounts.md).

## <a name="storage"></a>Storage
### <a name="storage-reclamation"></a>저장소 확보
포털에 표시 되도록 회수 용량에 대 한 최대 14 시간까지 걸릴 수 있습니다. 공간 확보는 블록 blob 저장소에 내부 컨테이너 파일의 사용 비율을 비롯 한 다양 한 요인에 따라 달라 집니다. 따라서 얼마나 많은 데이터를 삭제 하는 데에 따라 방법이 가비지 수집기를 실행 하는 경우 회수 될 수 있는 공간의 양 보장할 수 없습니다.

## <a name="next-steps"></a>다음 단계
[Azure Stack 지원 포럼 방문](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack)
