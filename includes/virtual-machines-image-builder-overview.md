---
author: cynthn
ms.author: cynthn
ms.date: 08/03/2020
ms.topic: include
ms.service: virtual-machines-linux
manager: gwallace
ms.openlocfilehash: 8ad191ca0d31abf317bab521dfbbc7c2567c3450
ms.sourcegitcommit: 3d56d25d9cf9d3d42600db3e9364a5730e80fa4a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/03/2020
ms.locfileid: "87545154"
---
조직에서는 표준화된 VM(가상 머신) 이미지를 사용하여 클라우드로 마이그레이션하고 배포의 일관성을 유지할 수 있습니다. 이미지에는 일반적으로 미리 정의된 보안 및 구성 설정과 필수 소프트웨어가 포함되어 있습니다. 사용자 고유의 이미징 파이프라인을 설정하려면 시간, 인프라 및 설정이 필요하지만, Azure VM Image Builder를 사용하면 이미지를 설명하는 간단한 구성을 제공하고, 서비스에 제출하고, 이미지를 빌드하고 배포하기만 하면 됩니다.
 
Azure VM Image Builder(Azure Image Builder)를 사용하여 Windows 또는 Linux 기반 Azure Marketplace 이미지, 기존 사용자 지정 이미지 또는 RHEL(Red Hat Enterprise Linux) ISO로 시작하고 사용자 지정 항목을 추가할 수 있습니다. 이 Image Builder는 [HashiCorp Packer](https://packer.io/)를 토대로 구축되었으므로 기존 Packer 셸 프로비저닝 프로그램 스크립트를 가져올 수도 있습니다. [Azure Shared Image Gallery](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries)에서 이미지를 호스트하는 위치를 관리형 이미지 또는 VHD로 지정할 수도 있습니다.

> [!IMPORTANT]
> Azure Image Builder는 현재 퍼블릭 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="preview-features"></a>미리 보기 기능

미리 보기에서는 다음 기능이 지원됩니다.

- 골드 기준 이미지를 만들기: 최소 보안 및 회사 구성을 포함하는 이러한 이미지를 만들 수 있으므로 부서에서 요구에 맞게 추가로 사용자 지정할 수 있습니다.
- 기존 이미지 패치: Image Builder에서 기존 사용자 지정 이미지를 지속적으로 패치할 수 있습니다.
- Image Builder를 기존 가상 네트워크에 연결: 기존 구성 서버(DSC, Chef, Puppet 등), 파일 공유 또는 라우팅할 수 있는 다른 서버/서비스에 연결할 수 있습니다.
- Azure Shared Image Gallery와 통합: 이미지를 전역적으로 배포하고, 버전을 관리하고, 크기를 조정할 수 있으며 이미지 관리 시스템을 사용할 수 있습니다.
- 기존 이미지 빌드 파이프라인과 통합: 파이프라인에서 Image Builder를 호출하거나 간단한 Preview Image Builder Azure DevOps 태스크를 사용합니다.
- 기존 이미지 사용자 지정 파이프라인을 Azure로 마이그레이션 기존 스크립트, 명령 및 프로세스를 사용하여 이미지 사용자 지정
- VHD 형식의 이미지를 만들어 Azure Stack 지원
 

## <a name="regions"></a>영역
이러한 지역에서는 Azure Image Builder 서비스를 미리 보기로 사용할 수 있습니다. 이러한 영역 외부로 이미지를 배포할 수 있습니다.
- 미국 동부
- 미국 동부 2
- 미국 중서부
- 미국 서부
- 미국 서부 2
- 북유럽
- 서유럽

## <a name="os-support"></a>OS 지원
AIB는 다음과 같은 Azure Marketplace 기본 OS 이미지를 지원합니다.
- Ubuntu 18.04
- Ubuntu 16.04
- RHEL 7.6, 7.7
- CentOS 7.6, 7.7
- SLES 12 SP4
- SLES 15, SLES 15 SP1
- Windows 10 RS5 Enterprise/Enterprise 다중 세션/Professional
- Windows 2016
- Windows 2019

RHEL ISO 지원은 더 이상 지원되지 않습니다.
## <a name="how-it-works"></a>작동 방법


![Azure Image Builder의 개념도](./media/virtual-machines-image-builder-overview/image-builder.png)

Azure Image Builder는 Azure 리소스 공급자가 액세스할 수 있는 완전 관리형 Azure 서비스입니다. Azure Image Builder 프로세스는 원본, 사용자 지정 및 배포의 세 가지 주요 부분으로 구성되며 템플릿에 표시됩니다. 아래 다이어그램에는 구성 요소와 해당 속성 일부가 표시됩니다. 
 


**Image Builder 프로세스** 

![Azure Image Builder 프로세스의 개념도](./media/virtual-machines-image-builder-overview/image-builder-process.png)

1. 이미지 템플릿을 .json 파일로 만듭니다. 이 .json 파일에는 이미지 원본, 사용자 지정 및 배포에 대한 정보가 포함되어 있습니다. [Azure Image Builder GitHub 리포지토리](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts)에 여러 예제가 나와 있습니다.
1. 서비스에 제출하면 지정한 리소스 그룹에 이미지 템플릿 아티팩트가 만들어집니다. 백그라운드에서 Image Builder는 필요에 따라 원본 이미지나 ISO 및 스크립트를 다운로드합니다. 이러한 리소스는 구독에서 자동으로 생성 되는 별도의 리소스 그룹에 저장 됩니다. IT_ _ 형식으로 되어 있습니다. \<DestinationResourceGroup> \<TemplateName> 
1. 이미지 템플릿이 만들어지면 이미지를 빌드할 수 있습니다. 배경에서 이미지 작성기는 템플릿 및 원본 파일을 사용 하 여 VM (기본 크기: Standard_D1_v2), 네트워크, 공용 IP, NSG 및 IT_ \<DestinationResourceGroup> _ 리소스 그룹의 저장소를 만듭니다 \<TemplateName> .
1. 이미지를 만들 때 이미지 작성기는 템플릿에 따라 이미지를 배포한 다음 해당 \<DestinationResourceGroup> \<TemplateName> 프로세스에 대해 만들어진 IT_ _ 리소스 그룹의 추가 리소스를 삭제 합니다.


## <a name="permissions"></a>사용 권한
(AIB)에 등록하면 AIB 서비스에 스테이징 리소스 그룹(IT_*)을 만들고, 관리하고, 삭제할 수 있는 권한이 부여되며, 이미지 빌드에 필요한 리소스를 추가할 수 있는 권한이 부여됩니다. 성공적으로 등록하는 동안 구독에서 사용할 수 있는 AIB SPN(서비스 주체 이름)이 이 작업을 수행합니다.

Azure VM Image Builder에서 관리형 이미지 또는 공유 이미지 갤러리에 이미지를 배포하도록 허용하려면 이미지를 읽고 쓸 수 있는 권한이 있는 Azure 사용자 할당 ID를 만들어야 합니다. Azure Storage에 액세스하는 경우 프라이빗 컨테이너를 읽을 수 있는 권한이 필요합니다.

처음에는 ID를 만드는 방법에 대한 [Azure 사용자 할당 관리 ID](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli) 설명서를 따라야 합니다.

권한을 부여해야 하는 ID가 일단 있으면 Azure 사용자 지정 역할 정의를 사용한 후 사용자 할당 관리 ID를 할당하여 사용자 지정 역할 정의를 사용할 수 있습니다.

사용 권한은 [여기](https://github.com/danielsollondon/azvmimagebuilder/blob/master/aibPermissions.md#azure-vm-image-builder-permissions-explained-and-requirements)에 좀 더 자세히 설명되며 구현 방법을 보여 주는 예제가 나와 있습니다.

> [!Note]
> 이전에 AIB SPN을 사용하고 이미지 리소스 그룹에 SPN 권한을 부여한 적이 있을 것입니다. 향후 기능을 허용하기 위해 이 모델에서 전환할 것입니다. 2020년 5월 26일부터 Image Builder는 사용자 할당 ID가 없는 템플릿을 수락하지 않으며 기존 템플릿은 [사용자 ID](https://docs.microsoft.com/azure/virtual-machines/linux/image-builder-json?toc=%2Fazure%2Fvirtual-machines%2Fwindows%2Ftoc.json&bc=%2Fazure%2Fvirtual-machines%2Fwindows%2Fbreadcrumb%2Ftoc.json#identity)를 사용하여 서비스에 다시 제출해야 합니다. 여기에 나오는 예제는 사용자 할당 ID를 만들고 템플릿에 추가하는 방법을 보여 줍니다. 자세한 내용은 이 [설명서](https://github.com/danielsollondon/azvmimagebuilder#service-updates-and-latest-release-information)에서 이러한 변경 및 릴리스 업데이트를 검토하세요.

## <a name="costs"></a>비용
Azure Image Builder를 사용하여 이미지를 만들고, 빌드하고, 저장하는 경우 컴퓨팅, 네트워킹 및 스토리지 비용이 일부 발생합니다. 이러한 비용은 사용자 지정 이미지를 수동으로 만들 때 발생하는 비용과 비슷합니다. 리소스의 경우 Azure 요금에 청구됩니다. 

이미지 생성 프로세스 동안 파일이 다운로드되어 `IT_<DestinationResourceGroup>_<TemplateName>` 리소스 그룹에 저장되므로 스토리지 비용이 약간 발생합니다. 이러한 상황을 유지하지 않으려면 이미지를 빌드한 후 **이미지 템플릿**을 삭제합니다.
 
Image Builder는 VM에 필요한 D1v2 VM 크기, 스토리지 및 네트워킹을 사용하여 VM을 만듭니다. 이러한 리소스는 빌드 프로세스가 지속되는 동안 유지되며, Image Builder에서 이미지 만들기를 완료하면 삭제됩니다. 
 
Azure Image Builder에서는 선택한 지역에 이미지를 배포하므로 네트워크 송신 요금이 발생할 수 있습니다.

## <a name="hyper-v-generation"></a>Hyper-v 생성
현재 이미지 작성기는 Hyper-v 1 세대 이미지 및 Vm을 지원 합니다.
 
## <a name="next-steps"></a>다음 단계 
 
Azure Image Builder를 사용해 보려면 [Linux](../articles/virtual-machines/linux/image-builder.md) 또는 [Windows](../articles/virtual-machines/windows/image-builder.md) 이미지 빌드에 대한 문서를 참조하세요.
 
