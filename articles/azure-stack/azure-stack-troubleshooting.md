---
title: "Microsoft Azure 스택 문제 해결 | Microsoft Docs"
description: "Azure 스택 문제 해결 합니다."
services: azure-stack
documentationcenter: 
author: heathl17
manager: byronr
editor: 
ms.assetid: a20bea32-3705-45e8-9168-f198cfac51af
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: helaw
ms.openlocfilehash: 3b40a657ee8eb391d14a38cb95acc0729a8dda21
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="microsoft-azure-stack-troubleshooting"></a>Microsoft Azure 스택 문제 해결

*적용 대상: Azure 스택 개발 키트*

이 문서에서는 Azure 스택에 대 한 일반적인 문제 해결 정보를 제공 합니다. 

Azure 스택 기술 개발 키트 평가 환경으로 제공 되므로 Microsoft 고객 지원 서비스에서 공식 지원 되지 않습니다.  문서화 되지 문제가 발생 하는 경우을 선택 했는지 확인는 [Azure 스택 MSDN 포럼](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack) 정보 및 추가 지원이 필요 합니다.  

이 섹션에 설명 된 문제 해결을 위한 권장 사항 여러 원본에서 파생 된 및 그렇지 해당 하는 문제를 해결할 수 있습니다. 코드 예제는 있는 그대로 제공 되 고 예상된 결과 보장할 수 없습니다. 이 섹션은 제품을 향상 되는 대로 자주 편집 하 고 업데이트 적용 됩니다.

## <a name="deployment"></a>배포
### <a name="deployment-failure"></a>배포 실패
사용할 수 있습니다 설치 하는 동안 오류가 발생 하면 실패 한 단계에서 배포를 다시 시작 하려면 배포 스크립트의 다시 실행된 옵션을 사용 합니다.  


### <a name="at-the-end-of-the-deployment-the-powershell-session-is-still-open-and-doesnt-show-any-output"></a>배포 후에 PowerShell 세션 계속 열려 및 모든 출력은 표시 되지 않습니다.
이 문제를 선택한 경우에 PowerShell 명령 창의 기본 동작 결과 때문일 수 있습니다. 개발 키트 배포는 실제로 했지만 스크립트 창을 선택할 때 일시 중지 되었습니다. 이 경우 "select" 명령 창의 제목 표시줄에는 단어 검색 하 여 확인할 수 있습니다.  선택 취소 하려면 ESC 키를 누릅니다 하 고 그 뒤 완료 메시지를 표시 합니다.

## <a name="templates"></a>템플릿
### <a name="azure-template-wont-deploy-to-azure-stack"></a>Azure 템플릿 스택 Azure에 배포 되지 않음
사항을 확인 합니다.

* 서식 파일 사용 가능한 또는 미리 보기에서 Azure 스택이 이미 Microsoft Azure 서비스를 사용 해야 합니다.
* 특정 리소스에 사용 되는 Api은 로컬 Azure 스택 인스턴스에 의해 지원 되며 유효한 위치 ("local" vs "미국 동부" 또는 "남 인도" Azure에서 Azure 스택 개발 키트에서)를 대상으로 합니다.
* 검토 [이 문서](https://github.com/Azure/AzureStack-QuickStart-Templates/blob/master/README.md) 테스트 AzureRmResourceGroupDeployment cmdlet에 대 한 Azure 리소스 관리자 구문의 방법 차이로 catch입니다.

Azure 스택 서식 파일에 이미 제공을 사용할 수도 있습니다는 [GitHub 리포지토리](http://aka.ms/AzureStackGitHub/) 를 시작할 수 있도록 합니다.

## <a name="virtual-machines"></a>가상 컴퓨터
### <a name="default-image-and-gallery-item"></a>기본 이미지와 갤러리 항목
Azure 스택의 Vm을 배포 하기 전에 Windows Server 이미지 및 갤러리 항목을 먼저 추가 해야 합니다.

### <a name="after-restarting-my-azure-stack-host-some-vms-may-not-automatically-start"></a>내 Azure 스택 호스트를 다시 시작한 후 일부 Vm 자동으로 시작할 수 없습니다.
호스트를 다시 부팅 한 후 Azure 스택 서비스를 즉시 사용할 수 없는 경우 발생할 수 있습니다.  Azure 스택 때문에 이것이 [인프라 Vm](azure-stack-architecture.md#virtual-machine-roles) 및 RPs 잠시 일관성을 확인 하려면 사용 하지만 결국 자동으로 시작 됩니다.

해당 테 넌 트 Vm이 Azure 스택 개발 키트 호스트를 다시 부팅 한 후 자동으로 시작 하지 않는 경우도 있습니다.  이것은 알려진된 문제 이며만 온라인 상태로 전환 하는 몇 가지 수동 단계 필요 합니다.

1.  Azure 스택 개발 키트 호스트에서 시작 **장애 조치 클러스터 관리자** 시작 메뉴에서 합니다.
2.  클러스터 선택 **S Cluster.azurestack.local**합니다.
3.  **역할**을 선택합니다.
4.  테 넌 트 Vm에 표시 됩니다는 *저장* 상태입니다.  모든 인프라 Vm을 실행 하는 테 넌 트 Vm을 마우스 오른쪽 단추로 클릭 하 고 선택 **시작** VM을 다시 시작할 수 있습니다.

### <a name="i-have-deleted-some-virtual-machines-but-still-see-the-vhd-files-on-disk-is-this-behavior-expected"></a>일부 가상 컴퓨터를 삭제 한 했지만 아직 디스크에 VHD 파일을 참조 하십시오. 이 동작이 예상
예, 예상 되는 동작입니다. 때문에 이러한 방식으로 설계 된:

* VM을 삭제 하는 경우 Vhd는 삭제 되지 않습니다. 디스크는 리소스 그룹에 별도 리소스입니다.
* 저장소 계정을 삭제 되는 삭제 하는 즉시 Azure 리소스 관리자를 통해 (포털, PowerShell) 표시 되지만 가비지 수집 실행 될 때까지 포함 될 수 있습니다 디스크 저장소에 보관 계속 됩니다.

"마지막 줄" Vhd를 참조 하는 경우에 삭제 된 저장소 계정에 대 한 폴더의 일부 인지 확인 해야 합니다. 저장소 계정을 삭제 되지 않은 경우 정상적인는 여전히 남아 있습니다.

자세한 내용은에서 보존 임계값 및 요청 시 다시 구성 하는 방법에 대 한 [저장소 계정을 관리](azure-stack-manage-storage-accounts.md)합니다.

## <a name="storage"></a>저장소
### <a name="storage-reclamation"></a>저장소 확보
포털에 표시 하려면 회수 된 용량에 대 한 최대 14 시간까지 걸릴 수 있습니다. 공간 확보는 블록 blob 저장소에 내부 컨테이너 파일 사용량 비율이 비롯 한 다양 한 요인에 따라 달라 집니다. 따라서 얼마나 많은 데이터가 삭제 됩니다에 따라 보장은 없습니다 가비지 수집기를 실행 하는 경우 회수 될 수 있는 공간의 크기입니다.

## <a name="powershell"></a>PowerShell
### <a name="resource-providers-not-registered"></a>리소스 공급자 등록 되지 않았습니다
PowerShell과 함께 테 넌 트 구독에 연결할 때 리소스 공급자 자동으로 등록 되지 않은 것을 확인할 수 있습니다. 사용 하 여는 [Connect 모듈](https://github.com/Azure/AzureStack-Tools/tree/master/Connect), PowerShell에서 다음 명령을 실행 하거나 (후 [설치 하 고 연결](azure-stack-connect-powershell.md) 테 넌 트로): 
  
       Get-AzureRMResourceProvider | Register-AzureRmResourceProvider

## <a name="cli"></a>CLI

* CLI 대화형 모드 즉 `az interactive` Azure 스택 명령은 아직 지원 되지 않습니다.
* Azure 스택에서 사용할 수 있는 가상 컴퓨터 이미지의 목록을 가져오려면를 사용 하 여는 `az vm images list --all` 대신 명령을 `az vm image list` 명령입니다. 지정 하는 `--all` 옵션을 선택 하면 응답 Azure 스택 환경에서 사용할 수 있는 이미지에만 반환 하는지 확인 합니다. 
* Azure에서 제공 되는 가상 컴퓨터 이미지 별칭 Azure 스택 적용할 수 없습니다. 전체 URN 매개 변수를 사용 해야 가상 컴퓨터 이미지를 사용할 때 (Canonical: UbuntuServer:14.04.3-LTS:1.0.0) 이미지 별칭 대신 합니다. 이 URNmust에서 파생 된 이미지 사양과 일치는 `az vm images list` 명령입니다.
* 기본적으로 CLI 2.0 기본 가상 컴퓨터 이미지 크기도 "Standard_DS1_v2"를 사용합니다. 그러나이 크기는 없습니다 아직 Azure 스택에서 사용할 수 있는, 지정 하면는 `--size` 가상 컴퓨터를 만들 때에 명시적으로 매개 변수입니다. 사용 하 여 Azure 스택에서 사용할 수 있는 가상 컴퓨터 크기 목록을 가져올 수는 `az vm list-sizes --location <locationName>` 명령입니다.


## <a name="windows-azure-pack-connector"></a>Windows Azure 팩 커넥터
* Azure 스택 개발 키트를 배포한 후 azurestackadmin 계정의 암호를 변경 하면 다중 클라우드 모드를 더 이상 구성할 수 없습니다. 따라서 대상 Windows Azure Pack 환경에 연결할 수 없습니다.
* 다중 클라우드 모드 설정한 후:
    * 사용자 포털 설정을 재설정 한 후에 대시보드를 볼 수 있습니다. (사용자 포털에서 포털 설정 아이콘을 클릭 (오른쪽 위 모퉁이에서 기어 아이콘). 아래에서 **기본 설정을 복원**, 클릭 **적용**.)
    * 대시보드 제목 나타나지 않을 수 있습니다. 이 문제가 발생 한 경우 해야 수동으로 다시 추가할 수 있습니다.
    * 일부 타일을 대시보드에 처음 추가 제대로 표시 되지 않습니다. 이 문제를 해결 하려면 브라우저를 새로 고칩니다.



