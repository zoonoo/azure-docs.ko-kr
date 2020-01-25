---
author: cynthn
ms.author: cynthn
ms.date: 01/23/2020
ms.topic: include
ms.service: virtual-machines-linux
manager: gwallace
ms.openlocfilehash: ec1b77118f94501363d950d72a65a67ece79ff77
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/24/2020
ms.locfileid: "76748791"
---
표준화 된 VM (가상 머신) 이미지를 사용 하면 조직에서 클라우드로 마이그레이션하고 배포의 일관성을 유지할 수 있습니다. 이미지에는 일반적으로 미리 정의 된 보안 및 구성 설정 및 필수 소프트웨어가 포함 되어 있습니다. 사용자 고유의 이미징 파이프라인을 설정 하려면 시간, 인프라 및 설정이 필요 하지만, Azure VM 이미지 작성기를 사용 하면 이미지를 설명 하는 간단한 구성을 제공 하 고, 서비스에 제출 하 고, 이미지를 빌드하고 배포할 수 있습니다.
 
Azure VM 이미지 작성기 (Azure 이미지 작성기)를 사용 하 여 Windows 또는 Linux 기반 Azure Marketplace 이미지, 기존 사용자 지정 이미지 또는 Red Hat Enterprise Linux (RHEL) ISO로 시작 하 고 사용자 지정 항목을 추가할 수 있습니다. 이미지 작성기는 [HashiCorp](https://packer.io/)패키지를 기반으로 하기 때문에 기존 팩 er shell provisioner 스크립트를 가져올 수도 있습니다. [Azure 공유 이미지 갤러리](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries)에서 이미지를 호스트 하는 위치를 관리 되는 이미지 또는 VHD로 지정할 수도 있습니다.

> [!IMPORTANT]
> Azure 이미지 작성기는 현재 공개 미리 보기로 제공 됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="preview-features"></a>미리 보기 기능

미리 보기의 경우 다음 기능이 지원 됩니다.

- 최소 보안 및 회사 구성을 포함 하는 골든 기준 이미지를 만들고 부서에서 요구에 맞게 추가로 사용자 지정할 수 있습니다.
- 기존 이미지를 패치 하면 이미지 작성기를 사용 하 여 기존 사용자 지정 이미지를 지속적으로 패치할 수 있습니다.
- Azure 공유 이미지 갤러리와 통합 하면 이미지를 전역적으로 배포, 버전 관리 및 크기 조정할 수 있으며 이미지 관리 시스템을 제공 합니다.
- 기존 이미지 빌드 파이프라인과 통합 파이프라인에서 이미지 작성기를 호출 하거나 간단한 미리 보기 이미지 빌더 Azure DevOps 작업을 사용 합니다.
- 기존 이미지 사용자 지정 파이프라인을 Azure로 마이그레이션합니다. 기존 스크립트, 명령 및 프로세스를 사용 하 여 이미지를 사용자 지정 합니다.
- Red Hat을 사용 하 여 고유한 구독을 지원 합니다. 사용이 불가능 한 Red Hat 구독과 함께 사용할 Red Hat Enterprise 이미지를 만듭니다.
- VHD 형식으로 이미지 만들기
 

## <a name="regions"></a>개 지역
이러한 지역에서 Azure 이미지 작성기 서비스를 미리 볼 수 있습니다. 이미지는 이러한 영역 외부에 배포할 수 있습니다.
- 미국 동부
- 미국 동부 2
- 미국 중서부
- 미국 서부
- 미국 서부 2

## <a name="os-support"></a>OS 지원
AIB는 Azure Marketplace 기본 OS 이미지를 지원 합니다.
- Ubuntu 18.04
- Ubuntu 16.04
- RHEL 7.6, 7.7
- CentOS 7.6, 7.7
- SLES 12 SP4
- SLES 15, SLES 15 SP1
- Windows 10 RS5 Enterprise/Professional/Enterprise for Virtual Desktop (EVD) 
- Windows 2016
- Windows 2019

AIB은 다음에 대 한 소스로 RHEL ISO의를 지원 합니다.
- RHEL 7.3
- RHEL 7.4
- RHEL 7.5

RHEL 7.6 Iso은 지원 되지 않지만 테스트 되 고 있습니다.

## <a name="how-it-works"></a>작동 원리


![Azure 이미지 작성기의 개념 그리기](./media/virtual-machines-image-builder-overview/image-builder.png)

Azure 이미지 작성기는 Azure 리소스 공급자가 액세스할 수 있는 완전히 관리 되는 Azure 서비스입니다. Azure 이미지 작성기 프로세스에는 세 가지 주요 부분이 있습니다. 원본, 사용자 지정 및 배포는 템플릿에 표시 됩니다. 아래 다이어그램에서는 일부 속성을 포함 하는 구성 요소를 보여 줍니다. 
 


**이미지 작성기 프로세스** 

![Azure 이미지 작성기 프로세스의 개념 그리기](./media/virtual-machines-image-builder-overview/image-builder-process.png)

1. 이미지 템플릿을 json 파일로 만듭니다. 이 json 파일에는 이미지 원본, 사용자 지정 및 배포에 대 한 정보가 포함 되어 있습니다. [Azure 이미지 작성기 GitHub 리포지토리에](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts)는 여러 예제가 있습니다.
1. 서비스에 제출 하면 지정 하는 리소스 그룹에 이미지 템플릿 아티팩트가 만들어집니다. 배경에서 이미지 작성기는 필요에 따라 원본 이미지 또는 ISO 및 스크립트를 다운로드 합니다. 이러한 리소스는 구독에서 자동으로 생성 되는 별도의 리소스 그룹에 저장 됩니다. IT_\<DestinationResourceGroup > _\<TemplateName > 형식입니다. 
1. 이미지 템플릿이 만들어지면 이미지를 빌드할 수 있습니다. 배경에서 이미지 작성기는 템플릿 및 원본 파일을 사용 하 여 VM을 만듭니다 (기본 크기: Standard_D1_v2), 네트워크, 공용 IP, NSG 및 저장소\<DestinationResourceGroup > _\<TemplateName > 리소스 그룹 IT_ 합니다.
1. 이미지를 만드는 과정에서 이미지 작성기는 템플릿에 따라 이미지를 배포한 다음 해당 프로세스에 대해 만들어진 IT_\<DestinationResourceGroup > _\<TemplateName > 리소스 그룹의 추가 리소스를 삭제 합니다.


## <a name="permissions"></a>권한

Azure VM 이미지 작성기에서 관리 되는 이미지 또는 공유 이미지 갤러리에 이미지를 배포 하도록 허용 하려면 "Azure 가상 컴퓨터 이미지 작성기" (앱 ID: cf32a0cc-373c-47c9-9156-0db11f6a6dfc)에 대 한 ' 참가자 ' 권한을 제공 해야 합니다. )를 설정 합니다. 

기존 사용자 지정 관리 되는 이미지 또는 이미지 버전을 사용 하는 경우 Azure 이미지 작성기에는 해당 리소스 그룹에 대 한 최소한의 ' 읽기 권한자 ' 액세스 권한이 필요 합니다.

Azure CLI를 사용 하 여 액세스 권한을 할당할 수 있습니다.

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/<distributeResoureGroupName>
```

PowerShell을 사용 하 여 액세스 권한을 할당할 수 있습니다.

```azurePowerShell-interactive
New-AzRoleAssignment -ObjectId ef511139-6170-438e-a6e1-763dc31bdf74 -Scope /subscriptions/$subscriptionID/resourceGroups/<distributeResoureGroupName> -RoleDefinitionName Contributor
```


서비스 계정을 찾을 수 없는 경우 역할 할당을 추가 하는 구독이 리소스 공급자에 대해 아직 등록 되지 않았음을 의미할 수 있습니다.


## <a name="costs"></a>비용
Azure 이미지 작성기를 사용 하 여 이미지를 만들고, 빌드하고, 저장 하는 경우 몇 가지 계산, 네트워킹 및 저장소 비용이 발생 합니다. 이러한 비용은 사용자 지정 이미지를 수동으로 만들 때 발생 하는 비용과 비슷합니다. 리소스의 경우 Azure 요금이 청구 됩니다. 

이미지를 만드는 과정에서 파일은 다운로드 되어 `IT_<DestinationResourceGroup>_<TemplateName>` 리소스 그룹에 저장 되며,이로 인해 저장소 비용이 적게 듭니다. 이러한 내용을 유지 하지 않으려면 이미지를 빌드한 후에 **이미지 템플릿을** 삭제 합니다.
 
이미지 작성기는 vm에 필요한 D1v2 VM 크기, 저장소 및 네트워킹을 사용 하 여 VM을 만듭니다. 이러한 리소스는 빌드 프로세스가 지속 되는 동안 지속 되며, 이미지 작성기에서 이미지 만들기를 완료 하면 삭제 됩니다. 
 
Azure 이미지 작성기에서 선택한 지역에 이미지를 배포 하 여 네트워크 송신 요금이 발생할 수 있습니다.
 
## <a name="next-steps"></a>다음 단계 
 
Azure 이미지 작성기를 사용해 보려면 [Linux](../articles/virtual-machines/linux/image-builder.md) 또는 [Windows](../articles/virtual-machines/windows/image-builder.md) 이미지 빌드에 대 한 문서를 참조 하세요.
 
 
