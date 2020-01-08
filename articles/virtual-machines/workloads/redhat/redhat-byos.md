---
title: 구독 이미지를 가져오는 Red Hat Enterprise Linux | Microsoft Docs
description: Azure에서 Red Hat Enterprise Linux에 대 한 사용자 고유의 구독 이미지 만들기에 대해 알아봅니다.
services: virtual-machines-linux
documentationcenter: ''
author: asinn826
manager: BorisB2015
editor: ''
ms.assetid: f495f1b4-ae24-46b9-8d26-c617ce3daf3a
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 6/6/2019
ms.author: alsin
ms.openlocfilehash: afd59c44bea238b7dd6f116e491054f8b4a52d48
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75486509"
---
# <a name="red-hat-enterprise-linux-bring-your-own-subscription-images-in-azure"></a>Azure에서 사용자 고유의 구독 이미지 가져오기 Red Hat Enterprise Linux
RHEL (Red Hat Enterprise Linux) 이미지는 PAYG (종 량 제) 또는 BYOS (사용자 고유의 구독) 모델을 통해 Azure에서 사용할 수 있습니다. 이 문서에서는 Azure의 BYOS 이미지에 대 한 개요를 제공 합니다.

## <a name="important-points-to-consider"></a>고려해 야 할 중요 사항

- 이 프로그램에서 제공 하는 RHEL BYOS 이미지는 Azure 갤러리/Marketplace에서 RHEL PAYG 이미지와 비슷한 프로덕션 준비 RHEL 이미지입니다. 이미지를 가져오기 위한 등록 프로세스는 미리 보기 상태입니다.

- 이미지는 [Azure의 Red Hat Enterprise Linux 이미지](./redhat-images.md) 에 설명 된 현재 정책을 따릅니다.

- 표준 지원 정책은 이러한 이미지에서 만든 Vm에 적용 됩니다.

- RHEL BYOS 이미지에서 프로 비전 된 Vm은 RHEL PAYG 이미지와 관련 된 RHEL 요금을 포함 하지 않습니다.

- 이미지의 자격이 부여 되지 않으므로, 구독 관리자를 사용 하 여 Red Hat에서 직접 업데이트를 가져오기 위해 Vm을 등록 하 고 구독 해야 합니다.

- 현재는 BYOS와 Linux 이미지에 대 한 PAYG 청구 모델 간을 동적으로 전환할 수 없습니다. 청구 모델을 전환 하려면 각 이미지에서 VM을 다시 배포 해야 합니다.

- Azure Disk Encryption (ADE)는 이러한 RHEL BYOS 이미지에서 지원 됩니다. ADE 지원은 현재 미리 보기 상태입니다. ADE를 구성 하기 전에 구독 관리자를 사용 하 여 Red Hat에 등록 해야 합니다. 등록 되 면 ADE를 구성 하려면: [Linux IaaS vm에 대 한 Azure Disk Encryption 사용](https://docs.microsoft.com/azure/virtual-machines/linux/disk-encryption-overview) 을 참조 하세요.

- 이미지는 변경 되지 않지만 (표준 업데이트 및 패치 외) 등록 프로세스는 미리 보기 상태 이며 프로세스를 간소화 하기 위해 흐름이 추가로 향상 됩니다.

- 최종 구현에 관계 없이 이러한 이미지 또는 해당 스냅숏에서 이미 프로 비전 된 Vm에 대 한 모든 권한이 있습니다.

## <a name="requirements-and-conditions-to-access-the-rhel-byos-images"></a>RHEL BYOS 이미지에 액세스 하기 위한 요구 사항 및 조건

1. [Red Hat Cloud access 프로그램](https://www.redhat.com/en/technologies/cloud-computing/cloud-access) 용어에 대해 잘 알고 [Red Hat의 클라우드 액세스 등록 페이지](https://access.redhat.com/cloude/manager/image_imports/new)에 등록 하세요.

1. [RHEL BYOS 액세스 요청 폼](https://aka.ms/rhel-byos)을 완료 합니다. 을 사용 하 여 RHEL BYOS 이미지에 액세스 하는 데 사용 하는 Azure 구독 뿐만 아니라 Red Hat 계정 번호를 직접 사용 해야 합니다.

1. Red Hat 및 Microsoft 모두에 의해 검토 및 승인 되 면 Azure 구독에 이미지 액세스를 사용할 수 있습니다.

### <a name="expected-time-for-image-access"></a>이미지 액세스 예상 시간

RHEL BYOS 양식을 완료 하 고 사용 약관에 동의 하면 Red Hat는 영업일 기준 3 일 이내에 BYOS 이미지에 대 한 자격을 확인 하 고, 유효한 경우 한 영업일 후에 BYOS 이미지에 대 한 액세스 권한을 받게 됩니다.

## <a name="use-the-rhel-byos-images-from-the-azure-portal"></a>Azure Portal에서 RHEL BYOS 이미지 사용

1. RHEL BYOS 이미지에 대 한 구독을 사용 하도록 설정한 후에는 **리소스를 만든** 다음 **모두 보기**를 탐색 하 여 [Azure Portal](https://portal.azure.com) 에서 찾을 수 있습니다.

1. 페이지 맨 위에 개인 제안이 있는 것을 볼 수 있습니다.

    ![Marketplace 개인 제안](./media/rhel-byos-privateoffers.png)

1. 자주색 링크를 클릭 하거나 페이지 아래쪽으로 스크롤하여 개인 제품을 볼 수 있습니다.

1. UI에서의 나머지 부분은 기존 Red Hat 이미지와 다르지 않습니다. RHEL 버전을 선택 하 고 프롬프트에 따라 VM을 프로 비전 합니다. 이 프로세스를 통해 최종 단계에서 이미지의 조건에 동의할 수도 있습니다.

>[!NOTE]
>지금까지 이러한 단계는 프로그래밍 방식 배포를 위해 RHEL BYOS 이미지를 사용 하도록 설정 하지 않습니다. 아래 "추가 정보" 섹션에 설명 된 대로 추가 단계가 필요 합니다.

이 문서의 나머지 부분에서는 CLI 메서드를 사용 하 여 이미지에서 용어를 프로 비전 하 고 사용 하는 방법에 중점을 둡니다. UI 및 CLI는 최종 결과 (프로 비전 된 RHEL BYOS VM)와 관련 하 여 완벽 하 게 교환할 수 있습니다.

## <a name="use-the-rhel-byos-images-from-the-azure-cli"></a>Azure CLI에서 RHEL BYOS 이미지 사용
다음 지침 집합에서는 Azure CLI 사용 하 여 RHEL VM에 대 한 초기 배포 프로세스를 안내 합니다. 이 지침에서는 [Azure CLI 설치](https://docs.microsoft.com/cli/azure/install-azure-cli)되어 있다고 가정 합니다.

>[!IMPORTANT]
>다음 모든 명령에 대 한 게시자, 제품, 계획 및 이미지 참조의 모든 소문자를 사용 해야 합니다.

1. 원하는 구독에 있는지 확인 합니다.
    ```azurecli
    az account show -o=json
    ```

1. RHEL BYOS VM에 대 한 리소스 그룹을 만듭니다.
    ```azurecli
    az group create --name <name> --location <location>
    ```

1. 이미지 사용 조건에 동의 합니다.
    ```azurecli
    az vm image terms accept --publisher redhat --offer rhel-byos --plan <SKU value here> -o=jsonc

    # Example:
    az vm image terms accept --publisher redhat --offer rhel-byos --plan rhel-lvm75 -o=jsonc

    OR

    az vm image terms accept --urn RedHat:rhel-byos:rhel-lvm8:8.0.20190620
    ```
    >[!NOTE]
    >이러한 용어 *는 이미지 SKU 당 Azure 구독 당 한 번만*허용 해야 합니다.

1. 필드 다음 명령을 사용 하 여 VM 배포의 유효성을 검사 합니다.
    ```azurecli
    az vm create -n <VM name> -g <resource group name> --image <image urn> --validate

    # Example:
    az vm create -n rhel-byos-vm -g rhel-byos-group --image RedHat:rhel-byos:rhel-lvm75:7.5.20190620
    ```

1. `--validate` 인수 없이 위와 동일한 명령을 실행 하 여 VM을 프로 비전 합니다.
    ```azurecli
    az vm create -n <VM name> -g <resource group name> --image <image urn> --validate
    ```

1. VM에 SSH를 적용 하 고 자격이 없는 이미지가 있는지 확인 합니다. 이렇게 하려면 `sudo yum repolist`를 실행 합니다. 출력에는 구독 관리자를 사용 하 여 Red Hat에 VM을 등록 하 라는 메시지가 표시 됩니다.

## <a name="additional-information"></a>추가 정보
- 이 제품에 대해 사용 하도록 설정 되지 않은 구독에서 VM을 프로 비전 하려고 하면 다음과 같은 오류가 표시 되며 Microsoft 또는 Red Hat에 연락 하 여 구독을 사용 하도록 설정 해야 합니다.
    ```
    "Offer with PublisherId: redhat, OfferId: rhel-byos, PlanId: rhel-lvm75 is private and can not be purchased by subscriptionId: GUID"
    ```

- RHEL BYOS 이미지에서 스냅숏을 만들고 [공유 이미지 갤러리](https://docs.microsoft.com/azure/virtual-machines/linux/shared-image-galleries)에 이미지를 게시 하는 경우 스냅숏의 원래 원본과 일치 하는 계획 정보를 제공 해야 합니다. 예를 들어 명령은 다음과 같을 수 있습니다. 최종 줄에서 계획 매개 변수를 확인 합니다.
    ```azurecli
    az vm create –image \
    "/subscriptions/GUID/resourceGroups/GroupName/providers/Microsoft.Compute/galleries/GalleryName/images/ImageName/versions/1.0.0" \
    -g AnotherGroupName --location EastUS2 -n VMName \
    --plan-publisher redhat --plan-product rhel-byos --plan-name rhel-lvm75
    ```

- Automation을 사용 하 여 RHEL BYOS 이미지에서 Vm을 프로 비전 하는 경우 위에 표시 된 것과 유사한 계획 매개 변수를 제공 해야 합니다. 예를 들어 Terraform을 사용 하는 경우 계획 [블록](https://www.terraform.io/docs/providers/azurerm/r/virtual_machine.html#plan)에 계획 정보를 제공 합니다.

## <a name="next-steps"></a>다음 단계
* [Azure Red Hat 업데이트 인프라](./redhat-rhui.md)에 대해 자세히 알아보세요.
* Azure의 Red Hat 이미지에 대한 자세한 내용은 [설명서 페이지](./redhat-images.md)로 이동합니다.
* 모든 RHEL 버전에 대한 Red Hat 지원 정책 관련 정보는 [Red Hat Enterprise Linux 수명 주기](https://access.redhat.com/support/policy/updates/errata) 페이지에서 확인할 수 있습니다.