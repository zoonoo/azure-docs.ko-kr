---
title: 마이그레이션 방법 - 포털
description: Azure Portal을 사용하여 Cloud Services(확장 지원)로 마이그레이션하는 방법
ms.topic: how-to
ms.service: cloud-services-extended-support
ms.subservice: classic-to-arm-migration
author: hirenshah1
ms.author: hirshah
ms.reviewer: mimckitt
ms.date: 2/08/2021
ms.custom: ''
ms.openlocfilehash: b900b691ee02f438b4e222cdd69ac64e46f15d38
ms.sourcegitcommit: beff1803eeb28b60482560eee8967122653bc19c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/07/2021
ms.locfileid: "113431540"
---
# <a name="migrate-to-cloud-services-extended-support-using-the-azure-portal"></a>Azure Portal을 사용하여 Cloud Services(확장 지원)로 마이그레이션

이 문서에서는 Azure Portal을 사용하여 [Cloud Services(클래식)](../cloud-services/cloud-services-choose-me.md)에서 [Cloud Services(확장 지원)](overview.md)로 마이그레이션하는 방법을 보여줍니다.

## <a name="before-you-begin"></a>시작하기 전에

**자신이 해당 구독의 관리자인지 확인합니다.**

이 마이그레이션을 수행하려면 해당 구독에 대해 [Azure Portal](https://portal.azure.com)에 공동 관리자로 추가되어야 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. **허브** 메뉴에서 **구독** 을 선택합니다. 표시되지 않으면 **모든 서비스** 를 선택합니다.
3. 적절한 구독 항목을 찾아 **내 역할** 필드를 확인합니다. 공동 관리자인 경우 해당 값은 *계정 관리자* 여야 합니다.

공동 관리자를 추가할 수 없는 상태라면 서비스 관리자나 [공동 관리자](../role-based-access-control/classic-administrators.md)에게 자신을 해당 구독에 추가해 달라고 요청합니다.

**마이그레이션 리소스 공급자에 등록**

1. [Azure Portal](../azure-resource-manager/management/resource-providers-and-types.md#register-resource-provider-1)을 사용하여 Microsoft.Compute 네임스페이스에서 마이그레이션 리소스 공급자 `Microsoft.ClassicInfrastructureMigrate` 및 미리 보기 기능 `Cloud Services`에 등록합니다.  
1. 등록이 완료될 때까지 5분 정도 기다린 후 승인 상태를 확인합니다. 

## <a name="migrate-your-cloud-service-resources"></a>클라우드 서비스 리소스 마이그레이션

1. [Cloud Services(클래식) 포털 블레이드](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/microsoft.classicCompute%2FdomainNames)로 이동합니다. 
2. 마이그레이션하려는 클라우드 서비스를 선택합니다.
3. **ARM으로 마이그레이션** 블레이드를 선택합니다.

    > [!NOTE]
    > 가상 네트워크(클래식) 내에 있는 Cloud Services(클래식)를 마이그레이션하는 경우 가상 네트워크(클래식) 블레이드를 이동하라는 배너 메시지가 나타납니다.
    > 가상 네트워크(클래식) 블레이드로 이동하여 가상 네트워크(클래식) 및 클라우드 서비스(클래식) 배포 마이그레이션을 완료합니다.
    > :::image type="content" source="media/in-place-migration-portal-2.png" alt-text="Azure Portal에서 가상 네트워크 클래식을 이동하는 것을 보여주는 이미지.":::
 

4. 마이그레이션의 유효성을 검사합니다. 

    유효성 검사에 성공하면 모든 배포가 지원되고 준비된 것입니다.  

    :::image type="content" source="media/in-place-migration-portal-1.png" alt-text="Azure Portal의 ARM으로 마이그레이션 블레이드를 보여주는 이미지":::

    유효성 검사에 실패하면 지원되지 않는 시나리오 목록이 표시되며 마이그레이션을 계속하려면 이를 수정해야 합니다. 

    :::image type="content" source="media/in-place-migration-portal-3.png" alt-text="Azure Portal의 유효성 검사 오류를 보여주는 이미지.":::

5. 마이그레이션을 준비합니다.

    준비에 성공하면 마이그레이션을 커밋할 준비가 된 것입니다.
    
    :::image type="content" source="media/in-place-migration-portal-4.png" alt-text="Azure Portal에서 통과하는 유효성 검사를 보여주는 이미지."::: 

    준비에 실패하면 오류를 검토하고 문제를 해결한 다음, 준비를 다시 시도합니다. 

    :::image type="content" source="media/in-place-migration-portal-5.png" alt-text="유효성 검사 실패 오류를 보여주는 이미지.":::

      준비 후 클라우드 서비스(클래식) 및 클라우드 서비스(확장 지원) Azure Portal 블레이드를 사용한 읽기 작업에 가상 네트워크의 모든 클라우드 서비스를 사용할 수 있습니다. 이제 마이그레이션을 완료하기 전에 클라우드 서비스(확장 지원) 배포를 테스트하여 제대로 작동하는지 확인할 수 있습니다. 
 
    :::image type="content" source="media/in-place-migration-portal-6.png" alt-text="포털 블레이드에서 API 테스트를 보여주는 이미지.":::

6.  **(선택 사항)** 마이그레이션을 중단합니다. 
    
    마이그레이션을 중단하도록 선택한 경우 **중단** 단추를 사용하여 이전 단계를 롤백합니다. 그런 다음, 모든 CRUD 작업에 대해 Cloud Services(클래식) 배포가 잠금 해제됩니다.

    :::image type="content" source="media/in-place-migration-portal-7.png" alt-text="유효성 검사 통과를 보여주는 이미지.":::

    중단에 실패하면 **중단 다시 시도** 를 선택합니다. 다시 시도하면 문제가 해결됩니다. 문제가 해결되지 않으면 고객 지원에 문의하세요. 
 
    :::image type="content" source="media/in-place-migration-portal-8.png" alt-text="유효성 검사 실패 오류 메시지를 보여주는 이미지.":::

7.  마이그레이션을 커밋합니다.

    >[!IMPORTANT]
    > 마이그레이션을 커밋하면 롤백할 수 있는 옵션이 없습니다. 
    
    "예"를 입력하여 마이그레이션을 확인하고 커밋합니다. 이제 마이그레이션이 완료되었습니다. 마이그레이션된 Cloud Services(확장 지원) 배포는 모든 작업에 대해 잠금 해제됩니다." 

## <a name="next-steps"></a>다음 단계

[마이그레이션 후 변경 내용](post-migration-changes.md) 섹션을 검토하여 배포 파일의 변경 내용, 자동화 및 새 Cloud Services(확장 지원) 배포의 기타 특성을 확인합니다.
