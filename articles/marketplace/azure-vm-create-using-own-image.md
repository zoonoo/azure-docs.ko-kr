---
title: 사용자 고유의 이미지를 사용 하 여 Azure Marketplace에서 Azure virtual machine 제품 만들기
description: 사용자 고유의 이미지를 사용 하 여 Azure Marketplace에 가상 머신 제품을 게시 하는 방법을 알아봅니다.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: emuench
ms.author: krsh
ms.date: 10/20/2020
ms.openlocfilehash: 42022d1204c3b524ee2e9ef2770f616fba89dc8c
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92284302"
---
# <a name="how-to-create-a-virtual-machine-using-your-own-image"></a>사용자 고유의 이미지를 사용 하 여 가상 머신을 만드는 방법

이 문서에서는 사용자가 제공 하는 VM (가상 머신) 이미지를 만들고 배포 하는 방법을 설명 합니다.

> [!NOTE]
> 이 절차를 시작 하기 전에 VHD (가상 하드 디스크) 요구 사항을 포함 하 여 Azure VM 제품에 대 한 [기술 요구 사항을](marketplace-virtual-machines.md#technical-requirements) 검토 하세요.

승인 된 기본 이미지를 대신 사용 하려면 [승인 된 기준에서 VM 이미지 만들기](azure-vm-create-using-approved-base.md)의 지침을 따르세요.

## <a name="configure-the-vm"></a>VM 구성

이 섹션에서는 Azure VM의 크기를 조정하고, 업데이트하고, 일반화하는 방법을 설명합니다. 이러한 단계는 Azure Marketplace에서 배포할 VM을 준비하는 데 필요합니다.

### <a name="size-the-vhds"></a>Vhd 크기 조정

[!INCLUDE [Discussion of VHD sizing](includes/vhd-size.md)]

### <a name="install-the-most-current-updates"></a>최신 업데이트 설치

[!INCLUDE [Discussion of most current updates](includes/most-current-updates.md)]

### <a name="perform-additional-security-checks"></a>추가 보안 검사 수행

[!INCLUDE [Discussion of addition security checks](includes/additional-security-checks.md)]

### <a name="perform-custom-configuration-and-scheduled-tasks"></a>사용자 지정 구성 및 예약된 작업 수행

[!INCLUDE [Discussion of custom configuration and scheduled tasks](includes/custom-config.md)]

## <a name="upload-the-vhd-to-azure"></a>Azure에 VHD 업로드

[Azure에 업로드할 WINDOWS vhd 또는 VHDX 준비](../virtual-machines/windows/prepare-for-upload-vhd-image.md) 또는 [Linux Vhd 만들기 및 업로드](../virtual-machines/linux/create-upload-generic.md)에 설명 된 대로 업로드할 VM을 구성 하 고 준비 합니다.

## <a name="extract-the-vhd-from-image-if-using-image-building-services"></a>이미지에서 VHD 추출 (이미지 작성 서비스를 사용 하는 경우)

패키지를 사용 하는 경우와 같은 이미지 빌드 서비스를 사용 하는 경우 이미지에서 [VHD를 추출](https://www.packer.io/)해야 할 수 있습니다. 이 작업을 수행 하는 직접적인 방법은 없습니다. Vm을 만들고 VM 디스크에서 VHD를 추출 해야 합니다.

### <a name="create-the-vm-on-the-azure-portal"></a>Azure Portal에서 VM 만들기

[Azure Portal](https://ms.portal.azure.com/)에서 기본 VM 이미지를 만들려면 다음 단계를 수행 합니다.

1. [Azure Portal](https://ms.portal.azure.com/)에 로그인합니다.
2. **가상 머신**을 선택합니다.
3. **+ 추가** 를 선택 하 여 **가상 머신 만들기** 화면을 엽니다.
4. 드롭다운 목록에서 이미지를 선택 하거나 **모든 공용 및 개인 이미지 찾아보기** 를 선택 하 여 사용 가능한 모든 가상 머신 이미지를 검색 하거나 검색 합니다.
5. **Gen 2** VM을 만들려면 **고급** 탭으로 이동 하 여 **gen 2** 옵션을 선택 합니다.

    :::image type="content" source="media/create-vm/vm-gen-option.png" alt-text="Gen 1 또는 Gen 2를 선택 합니다.":::

6. 배포할 VM의 크기를 선택 합니다.

    :::image type="content" source="media/create-vm/create-virtual-machine-sizes.png" alt-text="Gen 1 또는 Gen 2를 선택 합니다.":::

7. VM을 만드는 데 필요한 다른 세부 정보를 입력합니다.
8. **검토 + 만들기**를 선택하여 선택 사항을 검토합니다. **유효성 검사 통과** 메시지가 표시 되 면 **만들기**를 선택 합니다.

Azure에서 지정한 가상 머신의 프로비저닝을 시작합니다. 왼쪽 메뉴에서 **Virtual Machines** 탭을 선택 하 여 진행 상황을 추적 합니다. 가상 컴퓨터를 만든 후의 상태를 **실행 중**으로 변경 합니다.

### <a name="connect-to-your-vm"></a>VM에 연결

[Windows](../virtual-machines/windows/connect-logon.md) 또는 [Linux](../virtual-machines/linux/ssh-from-windows.md#connect-to-your-vm) VM에 연결 하려면 다음 문서를 참조 하세요.

[!INCLUDE [Discussion of addition security checks](includes/size-connect-generalize.md)]

## <a name="next-steps"></a>다음 단계

- 권장 되는 다음 단계: [VM 이미지를 테스트](azure-vm-image-test.md) 하 Azure Marketplace 게시 요구 사항을 충족 하는지 확인 합니다. 선택 사항입니다.
- VM 이미지를 테스트 하지 않는 경우 계속 해 서 [SAS URI를 생성](azure-vm-get-sas-uri.md)합니다.
- 새 Azure 기반 VHD를 만드는 데 어려움이 발생 [한 경우 Azure Marketplace에 대 한 VM FAQ](azure-vm-create-faq.md)를 참조 하세요.
