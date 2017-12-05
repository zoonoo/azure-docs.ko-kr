---
title: "Azure 스택 빠른 시작-Windows 가상 컴퓨터 만들기"
description: "Azure 스택 빠른 시작-포털을 사용 하는 Windows VM 만들기"
services: azure-stack
author: ErikjeMS
manager: byronr
ms.service: azure-stack
ms.topic: quickstart
ms.date: 09/15/2017
ms.author: erikje
ms.custom: mvc
ms.openlocfilehash: 64a623b3f5cb443e0676e7d89d7040837be95737
ms.sourcegitcommit: 7136d06474dd20bb8ef6a821c8d7e31edf3a2820
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/05/2017
---
# <a name="create-a-windows-virtual-machine-with-the-azure-stack-portal"></a>Azure 스택 포털과 Windows 가상 컴퓨터 만들기

Azure 스택 포털을 사용 하 여 Windows 가상 컴퓨터를 만들 수 있습니다. 이 포털은 수, 구성, 만들고 있는 리소스를 관리 하는 브라우저 기반 사용자 인터페이스.

## <a name="sign-in-to-the-azure-stack-portal"></a>Azure 스택 포털에 로그인

Azure 스택 포털에 로그인 합니다. Azure 스택 포털의 주소에 따라 Azure 스택 제품에 연결 하는 다릅니다.

* Azure 스택 개발 키트 (ASDK)에 대 한 이동: https://portal.local.azurestack.external 합니다.
* Azure 스택 통합 하는 시스템에 대 한 Azure 스택 연산자를 제공 하는 URL로 이동 합니다.

## <a name="create-a-virtual-machine"></a>가상 컴퓨터 만들기

1. 클릭 **새** > **계산** > **Windows Server 2016 데이터 센터 Eval** > **만들**합니다. 표시 되지 않으면 **Windows Server 2016 데이터 센터 Eval** 항목, 귀하가 Azure 스택 운영자에 게 문의 하십시오. 요청은에 추가할 시장에 설명 된 대로 [Azure 스택 시장에 Windows Server 2016 VM 이미지를 추가](../azure-stack-add-default-image.md) 문서. 
    ![](media/azure-stack-quick-windows-portal/image01.png)
2. 아래 **기본 사항**, 입력 **이름**, **사용자 이름**, 및 **암호**합니다. **구독**을 선택합니다. 만들기는 **리소스 그룹**, 기존 하나를 선택을 선택 하거나는 **위치**, 클릭 하 고 **확인**합니다.

    ![](media/azure-stack-quick-windows-portal/image02.png)
3. 아래 **크기 선택**, 클릭 **표준 D1** > **선택**합니다.
    ![](media/azure-stack-quick-windows-portal/image03.png)
4. 아래 **설정**기본값을 그대로 사용 하 고 클릭 **확인**합니다.
    ![](media/azure-stack-quick-windows-portal/image04.png)
5. 아래 **요약**, 클릭 **확인** 가상 컴퓨터를 만듭니다. 
    ![](media/azure-stack-quick-windows-portal/image05.png)
6. 새 가상 컴퓨터를 보려면 클릭 **모든 리소스**, 다음 가상 컴퓨터를 검색 하 고 해당 이름을 클릭 합니다.
    ![](media/azure-stack-quick-windows-portal/image06.png)

## <a name="clean-up-resources"></a>리소스 정리

가상 컴퓨터를 더 이상 해야 하는 경우 리소스 그룹, 가상 컴퓨터 및 모든 관련된 리소스를 삭제 합니다. 이렇게 하려면 가상 컴퓨터 페이지에서 리소스 그룹을 선택 하 고 클릭 **삭제**합니다.

## <a name="next-steps"></a>다음 단계
이 빠른 시작에서는 간단한 Windows 가상 컴퓨터 배포 했습니다. Azure 스택 가상 컴퓨터에 대 한 자세한 내용은 계속 [스택 Azure의에서 가상 컴퓨터에 대 한 고려 사항](azure-stack-vm-considerations.md)합니다.
