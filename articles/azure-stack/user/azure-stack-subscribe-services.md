---
title: 이 자습서에서는 Azure Stack 제품을 구독 하는 방법을 알아봅니다 | Microsoft Docs
description: 이 자습서에서는 Azure Stack 서비스에 새 구독을 만들고 테스트 가상 머신을 만들어 제품을 테스트 하는 방법을 보여 줍니다.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 11/13/2018
ms.author: sethm
ms.reviewer: ''
ms.openlocfilehash: 35b3e6d59cdb1edc59aa211954b8a69a3ce51bc5
ms.sourcegitcommit: 0b7fc82f23f0aa105afb1c5fadb74aecf9a7015b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/14/2018
ms.locfileid: "51632208"
---
# <a name="tutorial-create-and-test-a-subscription"></a>자습서: 만들기 및 구독 테스트

이 자습서는 제품을 포함 하는 구독을 만들고 다음 테스트 하는 방법을 보여줍니다. 테스트의 경우 클라우드 관리자로 Azure Stack 사용자 포털에 로그인 할 제품을 구독 한 다음 가상 컴퓨터를 만듭니다.

> [!TIP]
> 고급 평가 환경을 더 할 수 있습니다 [특정 사용자에 대 한 구독 만들기](../azure-stack-subscribe-plan-provision-vm.md#create-a-subscription-as-a-cloud-operator) 했다가 사용자 포털에서 해당 사용자로 로그인 합니다. 

이 자습서에서는 Azure Stack 제품을 구독 하는 방법을 보여 줍니다.

학습할 내용:

> [!div class="checklist"]
> * 제품 구독 
> * 제품 테스트

## <a name="subscribe-to-an-offer"></a>제품 구독

사용자로 제품을 구독할 로그인 Azure Stack 사용자 포털에 Azure Stack 연산자에 의해 제공 된 서비스를 검색할 수 있습니다.

1. 사용자가 선택 하 고 포털에 로그인 **구독**합니다.

   ![구독 가져오기](media/azure-stack-subscribe-services/get-subscription.png)

2. **표시 이름** 필드에서 구독의 이름을 입력합니다. 선택한 **제공** 에서 사용할 수 있는 서비스 중 하나를 선택 합니다 **제안 선택** 섹션. 그런 다음 **만들기**를 선택합니다.

   ![제품 만들기](media/azure-stack-subscribe-services/create-subscription.png)

   > [!TIP]
   > 이제 구독 사용을 시작 하려면 사용자 포털을 새로 고쳐야 합니다.

3. 만든 구독을 보려면 **모든 서비스**합니다. 그런 다음 합니다 **일반** 범주 선택 **구독**, 한 다음 새 구독을 선택 합니다. 제품을 구독할 경우 새 서비스에서 새 구독의 일부로 포함 되었습니다. 확인 하려면 포털을 새로 고칩니다. 이 예에서 **가상 머신** 추가 되었습니다.

   ![구독 보기](media/azure-stack-subscribe-services/view-subscription.png)

## <a name="test-the-offer"></a>제품 테스트

사용자 포털에 로그인 하는 동안에 새 구독 기능을 사용 하 여 가상 컴퓨터를 프로 비전 하 여 제품을 테스트할 수 있습니다. 

> [!NOTE]
> 이 테스트는 Windows Server 2016 Datacenter VM을 Azure Stack marketplace에 처음 추가 되어 있는지 필요 합니다. 

1. 사용자 포털에 로그인 합니다.

2. 사용자 포털에서 선택 **Virtual Machines**, 한 다음 **추가**, 다음 **Windows Server 2016 Datacenter**, 클릭 하 고 **만들기**합니다.

3. 에 **기본 사항** 섹션에서 입력을 **이름**, **사용자 이름**, 및 **암호**, 선택는 **구독**, 만들기는 **리소스 그룹** (또는 기존 항목을 선택)를 선택한 후 **확인**합니다.

4. 에 **크기 선택** 섹션에서 **A1 표준**를 클릭 하 고 **선택**.  

5. 에 **설정을** 블레이드에서 기본값을 사용 하 고 선택 **확인**합니다.

6. 에 **요약** 섹션에서 **확인** 가상 머신을 만듭니다.  

7. 새 가상 컴퓨터를 보려면 선택 **가상 머신**다음 새 가상 컴퓨터를 검색 하 고 해당 이름을 클릭 합니다.

    ![모든 리소스](media/azure-stack-subscribe-services/view-vm.png)

> [!NOTE]
> 가상 머신 배포를 완료 하려면 몇 분 정도 걸립니다.


## <a name="next-steps"></a>다음 단계

이 자습서에서 배운 내용을:

> [!div class="checklist"]
> * 제품 구독 
> * 제품 테스트


> [!div class="nextstepaction"]
> [커뮤니티 템플릿에서 VM 만들기](azure-stack-create-vm-template.md)