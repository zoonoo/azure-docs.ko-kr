---
author: cynthn
ms.author: cynthn
ms.date: 01/23/2020
ms.topic: include
ms.service: virtual-machines-linux
manager: gwallace
ms.openlocfilehash: 00fc3a01e6f42c2704af9dbc807dce193ff2971c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80117148"
---
표준화된 VM(가상 머신) 이미지를 통해 조직은 클라우드로 마이그레이션하고 배포의 일관성을 보장할 수 있습니다. 이미지에는 일반적으로 미리 정의된 보안 및 구성 설정과 필요한 소프트웨어가 포함됩니다. 자체 이미징 파이프라인을 설정하려면 시간, 인프라 및 설정이 필요하지만 Azure VM Image Builder를 사용하면 이미지를 설명하는 간단한 구성을 제공하고 서비스에 제출하고 이미지가 빌드되고 배포됩니다.
 
Azure VM 이미지 빌더(Azure Image Builder)를 사용하면 Windows 또는 Linux 기반 Azure 마켓플레이스 이미지, 기존 사용자 지정 이미지 또는 RHEL(Red Hat Enterprise Linux) ISO로 시작하여 사용자 지정을 추가할 수 있습니다. 이미지 빌더는 [HashiCorp Packer에](https://packer.io/)빌드되어 있으므로 기존 Packer 셸 프로비저 스크립트를 가져올 수도 있습니다. [Azure 공유 이미지 갤러리에서](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries)관리되는 이미지 또는 VHD로 이미지를 호스팅할 위치를 지정할 수도 있습니다.

> [!IMPORTANT]
> Azure 이미지 빌더는 현재 공개 미리 보기 상태입니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="preview-features"></a>미리 보기 기능

미리 보기의 경우 다음 기능이 지원됩니다.

- 최소한의 보안 및 회사 구성을 포함하는 골든 베이스라인 이미지를 생성하고 부서가 필요에 따라 추가로 사용자 지정할 수 있도록 합니다.
- 기존 이미지의 패치, 이미지 빌더는 지속적으로 기존의 사용자 정의 이미지를 패치 할 수 있습니다.
- 이미지 빌더를 기존 가상 네트워크에 연결하여 기존 구성 서버(DSC, Chef, Puppet 등), 파일 공유 또는 기타 라우팅 가능한 서버/서비스에 연결할 수 있습니다.
- Azure 공유 이미지 갤러리와의 통합을 통해 이미지를 전역적으로 배포, 버전 및 확장할 수 있으며 이미지 관리 시스템을 제공합니다.
- 기존 이미지 빌드 파이프라인과의 통합, 파이프라인에서 이미지 빌더를 호출하거나 간단한 미리 보기 이미지 빌더 Azure DevOps 작업을 사용합니다.
- 기존 이미지 사용자 지정 파이프라인을 Azure로 마이그레이션합니다. 기존 스크립트, 명령 및 프로세스를 사용하여 이미지를 사용자 지정합니다.
- VHD 형식으로 이미지를 생성합니다.
 

## <a name="regions"></a>영역
Azure 이미지 빌더 서비스는 이러한 리전에서 미리 볼 수 있습니다. 이미지는 이러한 영역 외부에 배포할 수 있습니다.
- 미국 동부
- 미국 동부 2
- 미국 중서부
- 미국 서부
- 미국 서부 2
- 북유럽
- 서유럽

## <a name="os-support"></a>OS 지원
AIB는 Azure 마켓플레이스 기본 OS 이미지를 지원합니다.
- Ubuntu 18.04
- Ubuntu 16.04
- RHEL 7.6, 7.7
- 센트로스 7.6, 7.7
- SLES 12 SP4
- SLES 15, SLES 15 SP1
- 가상 데스크톱(EVD)을 위한 윈도우 10 RS5 엔터프라이즈/전문가/엔터프라이즈 
- Windows 2016
- 윈도우 2019

RHEL IsSo 지원이 더 이상 사용되지 않는 경우 자세한 내용은 템플릿 설명서를 참조하십시오.

## <a name="how-it-works"></a>작동 방법


![Azure 이미지 빌더의 개념 적 그리기](./media/virtual-machines-image-builder-overview/image-builder.png)

Azure 이미지 빌더는 Azure 리소스 공급자가 액세스할 수 있는 완전히 관리되는 Azure 서비스입니다. Azure Image Builder 프로세스에는 소스, 사용자 지정 및 배포의 세 가지 주요 부분이 있으며 템플릿에 표시됩니다. 아래 다이어그램은 일부 속성과 함께 구성 요소를 보여 주었습니다. 
 


**이미지 빌더 프로세스** 

![Azure 이미지 빌더 프로세스의 개념 그리기](./media/virtual-machines-image-builder-overview/image-builder-process.png)

1. 이미지 템플릿을 .json 파일로 만듭니다. 이 .json 파일에는 이미지 소스, 사용자 지정 및 배포에 대한 정보가 포함되어 있습니다. [Azure 이미지 빌더 GitHub 리포지토리에는](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts)여러 예제가 있습니다.
1. 서비스에 제출하면 지정한 리소스 그룹에 이미지 템플릿 아티팩트가 생성됩니다. 백그라운드에서 이미지 빌더는 필요에 따라 소스 이미지 또는 ISO 및 스크립트를 다운로드합니다. 이러한 파일은 IT_\<>>_\<구독에서 자동으로 생성되는 별도의 리소스 그룹에 저장됩니다. 
1. 이미지 템플릿이 만들어지면 이미지를 빌드할 수 있습니다. 백그라운드에서 이미지 빌더는 템플릿 및 소스 파일을 사용하여 IT_\<DestinationResourceGroup>_\<TemplateName> 리소스 그룹에 VM(기본 크기: Standard_D1_v2), 네트워크, 공용 IP, NSG 및 저장소를 만듭니다.
1. 이미지 생성의 일부로 이미지 빌더는 템플릿에 따라 이미지를 배포한 다음 프로세스용으로\<만들어진 IT_\<DestinationResourceGroup>_ TemplateName> 리소스 그룹의 추가 리소스를 삭제합니다.


## <a name="permissions"></a>사용 권한

Azure VM 이미지 빌더가 관리되는 이미지 또는 공유 이미지 갤러리에 이미지를 배포할 수 있도록 하려면 서비스 "Azure 가상 시스템 이미지 빌더"(앱 ID: cf32a0cc-373c-473c-9156-0db111f6a6dfc)에 대한 '기여자' 권한을 제공해야 합니다. )을 사용합니다. 

기존 사용자 지정 관리 되는 이미지 또는 이미지 버전을 사용 하는 경우 Azure 이미지 빌더 해당 리소스 그룹에 대 한 '리더' 액세스의 최소 필요 합니다.

Azure CLI를 사용하여 액세스를 할당할 수 있습니다.

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/<distributeResoureGroupName>
```

PowerShell을 사용하여 액세스를 할당할 수 있습니다.

```azurePowerShell-interactive
New-AzRoleAssignment -ObjectId ef511139-6170-438e-a6e1-763dc31bdf74 -Scope /subscriptions/$subscriptionID/resourceGroups/<distributeResoureGroupName> -RoleDefinitionName Contributor
```


서비스 계정을 찾을 수 없는 경우 역할 할당을 추가하는 구독이 리소스 공급자에 아직 등록되지 않은 것을 의미할 수 있습니다.


## <a name="costs"></a>비용
Azure 이미지 빌더를 통해 이미지를 생성, 빌드 및 저장할 때 일부 계산, 네트워킹 및 저장소 비용이 발생합니다. 이러한 비용은 사용자 지정 이미지를 수동으로 만드는 데 발생하는 비용과 유사합니다. 리소스의 경우 Azure 요금이 부과됩니다. 

이미지 생성 과정에서 파일이 다운로드되어 `IT_<DestinationResourceGroup>_<TemplateName>` 리소스 그룹에 저장되며, 이로 인해 약간의 저장 비용이 발생합니다. 이러한 항목을 유지하지 않으려면 이미지 빌드 후 **이미지 템플릿을** 삭제합니다.
 
이미지 빌더는 D1v2 VM 크기와 VM에 필요한 스토리지 및 네트워킹을 사용하여 VM을 만듭니다. 이러한 리소스는 빌드 프로세스 동안 지속되며 이미지 작성기 작성이 완료되면 삭제됩니다. 
 
Azure Image Builder는 선택한 지역에 이미지를 배포하여 네트워크 송신 요금이 발생할 수 있습니다.
 
## <a name="next-steps"></a>다음 단계 
 
Azure 이미지 빌더를 사용해 보십시오. [Linux](../articles/virtual-machines/linux/image-builder.md) [Windows](../articles/virtual-machines/windows/image-builder.md)
 
 
