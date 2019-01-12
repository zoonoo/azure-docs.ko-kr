---
title: Azure Stack 빠른 시작-Windows 가상 머신 만들기
description: Azure Stack 빠른 시작-포털을 사용 하 여 Windows VM 만들기
services: azure-stack
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.topic: quickstart
ms.date: 09/12/2018
ms.author: mabrigg
ms.reviewer: unknown
ms.custom: mvc
ms.openlocfilehash: c23778a6c7fd864cf741bad41c455dbb3314563a
ms.sourcegitcommit: f4b78e2c9962d3139a910a4d222d02cda1474440
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/12/2019
ms.locfileid: "54243205"
---
# <a name="quickstart-create-a-windows-server-virtual-machine-with-the-azure-stack-portal"></a>빠른 시작: Azure Stack 포털을 사용 하 여 Windows server 가상 머신 만들기

*적용 대상: Azure Stack 통합 시스템 및 Azure Stack 개발 키트*

Azure Stack 포털을 사용 하 여 Windows Server 2016 가상 컴퓨터를 만들 수 있습니다. 만들고 가상 머신을 사용 하려면이 문서의 단계를 수행 합니다.

> [!NOTE]  
> 이 문서의 화면 이미지는 Azure Stack 버전 1808 도입 된 사용자 인터페이스를 일치 하도록 업데이트 됩니다. 1808 사용에 대 한 지원 추가 *관리 디스크* 관리 되지 않는 디스크 외에도 합니다. 이전 버전을 사용 하는 경우이 문서에 표시 되는 내용을 다른 디스크 선택 항목과 유사한 일부 이미지 됩니다.  


## <a name="sign-in-to-the-azure-stack-portal"></a>Azure Stack 포털에 로그인

Azure Stack 포털에 로그인 합니다. Azure Stack 포털의 주소에 연결 하는 Azure Stack 제품에 따라 다릅니다.

* Azure Stack 개발 키트 (ASDK)에 대 한 이동: https://portal.local.azurestack.external합니다.
* Azure Stack 통합 시스템, 귀하가 Azure Stack 운영자가 제공한 URL로 이동 합니다.

## <a name="create-a-virtual-machine"></a>가상 머신 만들기

1. 클릭 **+ 리소스 만들기** > **계산** > **Windows Server 2016 Datacenter –-수-종**  >   **만들**합니다. 표시 되지 않는 경우 **-수-종 – Windows Server 2016 Datacenter** 항목이 귀하가 Azure Stack 운영자에 게 문의 합니다. 추가 하는 것을 marketplace에 설명 된 대로 요청을 [Windows Server 2016 VM 이미지를 Azure Stack 마켓플레이스에 추가할](../azure-stack-add-default-image.md) 문서.

    ![포털에서 Windows 가상 컴퓨터를 만드는 단계](media/azure-stack-quick-windows-portal/image01.png)
2. 아래 **기본 사항**를 입력 **이름**를 **사용자 이름**, 및 **암호**합니다. **구독**을 선택합니다. 만들기는 **리소스 그룹**를 기존 하나를 선택 하거나를 **위치**를 클릭 하 고 **확인**합니다.

    ![기본 설정 구성](media/azure-stack-quick-windows-portal/image02.png)
3. 아래 **크기** 선택 **표준 D1**를 클릭 하 고 **선택**합니다.  
    ![가상 머신의 크기를 선택 합니다.](media/azure-stack-quick-windows-portal/image03.png)

4. 에 **설정을** 페이지에서 기본값을 원하는 대로 변경 합니다.
   - Azure Stack 버전 1808부터 구성할 수 있습니다 **스토리지** 사용 하도록 선택할 수 있는 *관리 디스크*합니다. 이전 버전 1808 관리 되지 않는 디스크에만 사용할 수 있습니다.  
   ![가상 머신 설정 구성](media/azure-stack-quick-windows-portal/image04.png)  
   구성 준비 되 면 선택 **확인** 를 계속 합니다.

5. 아래 **요약**, 클릭 **확인** 가상 머신을 만듭니다.
    ![요약 보기 및 가상 머신 만들기](media/azure-stack-quick-windows-portal/image05.png)

6. 새 가상 컴퓨터를 보려면 클릭 **모든 리소스**, 가상 머신 이름을 검색 하 고 검색 결과에서 해당 이름을 클릭 합니다.
    ![가상 컴퓨터를 참조 하세요.](media/azure-stack-quick-windows-portal/image06.png)

## <a name="clean-up-resources"></a>리소스 정리

가상 컴퓨터를 사용 하 여 완료 되 면, 가상 컴퓨터 및 해당 리소스를 삭제 합니다. 이렇게 하려면 가상 컴퓨터 페이지의 리소스 그룹을 선택 하 고 클릭 **삭제**합니다.

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 기본 Windows Server 가상 컴퓨터를 배포 했습니다. Azure Stack virtual machines에 대 한 자세한 내용은 계속 [Azure Stack에서 Virtual Machines에 대 한 고려 사항](azure-stack-vm-considerations.md)합니다.
