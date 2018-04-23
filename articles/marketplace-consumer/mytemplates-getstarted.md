---
title: 개인 템플릿을 사용하여 시작 | Microsoft Docs
description: Azure 포털, Azure CLI, PowerShell을 사용하여 개인 템플릿을 추가, 관리 및 공유합니다.
services: marketplace-customer
documentationcenter: ''
author: msmbaldwin
manager: asimm
editor: ''
tags: marketplace, azure-resource-manager
keywords: ''
ms.assetid: 6ec20778-b578-4885-acb5-104b0e51ea1a
ms.service: marketplace
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/18/2016
ms.author: mbaldwin
ms.openlocfilehash: c716f54a1361d41dbad00e2e45562d5fbf8fd6ca
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="get-started-with-private-templates-on-the-azure-portal"></a>Azure Portal에서 개인 템플릿을 사용하여 시작
[Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md) 템플릿은 배포를 정의하는 데 사용된 선언적 템플릿입니다. 솔루션에 대해 배포할 리소스를 정의하고, 여러 환경의 값을 입력하는 데 사용할 수 있는 변수 및 매개 변수를 지정합니다. 템플릿은 배포에 대한 값을 생성하는 데 사용할 수 있는 식과 JSON으로 구성됩니다.

[Azure Portal](https://portal.azure.com)에서 [Azure Marketplace](https://azure.microsoft.com/marketplace/)의 확장으로 **Microsoft.Gallery** 리소스 공급자와 함께 새 **템플릿** 기능을 사용하여 사용자가 개인 라이브러리에서 개인 템플릿을 만들고 관리하며 배포하도록 할 수 있습니다.

> [!NOTE]
> 포털에서 개인 템플릿을 사용하는 대신 [관리 응용 프로그램](../managed-applications/overview.md)을 통해 서비스 카탈로그 응용 프로그램을 만드는 것이 좋습니다. 조직에서 사용자가 서비스 카탈로그 응용 프로그램을 사용할 수 있습니다.

이 문서는 Azure Portal을 사용하여 개인 **템플릿** 을 추가하고, 관리하고, 공유하는 과정을 설명합니다.

## <a name="guidance"></a>지침
다음 제안을 통해 솔루션을 사용할 때 **템플릿**을 완벽하게 활용할 수 있습니다.

* **템플릿**은 Resource Manager 템플릿 및 추가 메타데이터를 포함하는 캡슐화된 리소스입니다. Marketplace에 있는 항목과 비슷하게 작동합니다. 주요 차이점은 공용 Marketplace 항목이 아니라 비공개 항목이라는 점입니다.
* **템플릿** 라이브러리는 자신의 배포를 사용자 지정해야 하는 사용자에 대해 제대로 작동합니다.
* **템플릿** 은 Azure 내에서 간단한 리포지토리가 필요한 사용자에 대해 제대로 작동합니다.
* 기존 Resource Manager 템플릿으로 시작합니다. [github](https://github.com/Azure/azure-quickstart-templates)에서 템플릿을 찾거나 기존 리소스 그룹에서 [템플릿을 내보냅니다](../azure-resource-manager/resource-manager-export-template.md).
* **템플릿** 은 템플릿을 게시하는 사용자와 연결됩니다. 게시자 이름은 읽기 액세스 권한이 있는 모든 사람에게 표시됩니다.
* **템플릿** 은 Resource Manager 리소스이며 게시된 후에는 이름을 바꿀 수 없습니다.

## <a name="add-a-template-resource"></a>템플릿 리소스 추가
Azure 포털에서 **템플릿** 리소스를 만드는 두 가지 방법이 있습니다.

### <a name="method-1-create-a-new-template-resource-from-a-running-resource-group"></a>방법 1: 실행 중인 리소스 그룹에서 새 템플릿 리소스 만들기
1. Azure Portal에서 기존 리소스 그룹으로 이동합니다. **설정**에서 **템플릿 내보내기**를 선택합니다.
2. Resource Manager 템플릿을 내보낸 후는 **템플릿 저장** 단추를 사용하여 **템플릿** 리포지토리에 저장합니다. 템플릿 내보내기에 대한 전체 세부 정보는 [여기](../azure-resource-manager/resource-manager-export-template.md)에서 확인하세요.
   <br /><br />
   ![리소스 그룹 내보내기](media/rg-export-portal1.PNG)
3. **템플릿에 저장** 명령 단추를 선택합니다.
   <br /><br />
4. 다음 정보를 입력합니다.
   
   * 이름 – 템플릿 개체의 이름(참고: 이 필드는 Azure Resource Manager 기반 이름입니다. 모든 명명 제한 사항을 적용하고 만든 후에는 변경할 수 없습니다).
   * 설명 – 템플릿에 대한 간단한 요약입니다.
     
     ![템플릿 저장](media/save-template-portal1.PNG)
5. **저장**을 클릭합니다.
   
   > [!NOTE]
   > 내보낸 Resource Manager 템플릿에 오류가 있는 경우 포털에 알림이 표시되지만 이 Resource Manager 템플릿을 해당 템플릿에 계속 저장할 수 있습니다. 내보낸 Resource Manager 템플릿을 다시 배포하기 전에 Resource Manager 템플릿 문제를 확인하여 수정해야 합니다.
   > 
   > 

### <a name="method-2-add-a-new-template-resource-from-browse"></a>방법 2: 찾아보기에서 새 템플릿 리소스 추가
**찾아보기 > 템플릿**에서 +추가 명령 단추를 사용하여 처음부터 새 **템플릿**을 추가할 수도 있습니다. 이름, 설명 및 Resource Manager 템플릿 JSON을 제공합니다.

![템플릿 추가](media/add-template-portal1.PNG)

> [!NOTE]
> Microsoft.Gallery는 테넌트 기반 Azure 리소스 공급자입니다. 템플릿 리소스는 리소스를 만든 사용자와 연결됩니다. 특정 구독에 연결되지 않습니다. 템플릿을 배포할 때 구독을 선택합니다.
> 
> 

## <a name="view-template-resources"></a>템플릿 리소스 보기
사용 가능한 모든 **템플릿**은 **찾아보기 > 템플릿**에서 볼 수 있습니다. 사용 가능한 템플릿에는 만든 템플릿 뿐만 아니라 다양한 수준의 권한을 사용하여 공유된 템플릿이 포함됩니다. 자세한 내용은 [액세스 제어](#access-control-for-a-tenant-resource-provider)를 참조하세요.

![템플릿 보기](media/view-template-portal1.PNG)

목록에 있는 항목을 클릭하여 **템플릿** 에 대한 세부 정보를 볼 수 있습니다.

![템플릿 보기](media/view-template-portal2c.png)

## <a name="edit-a-template-resource"></a>템플릿 리소스 편집
찾아보기 목록에서 항목을 마우스 오른쪽 단추로 클릭하거나 편집 명령 단추를 선택하여 **템플릿**에 대한 편집 흐름을 시작할 수 있습니다.

![템플릿 편집](media/edit-template-portal1a.PNG)

설명 또는 Resource Manager 템플릿 텍스트를 편집할 수 있습니다. 리소스 관리자 리소스 이름이기 때문에 이름은 편집할 수 없습니다. Resource Manager 템플릿 JSON을 편집할 때 유효한 JSON인지 확인하기 위해 유효성을 확인합니다. **확인**을 선택한 후 **저장**을 선택하여 업데이트된 템플릿을 저장합니다.

![템플릿 편집](media/edit-template-portal2a.PNG)

템플릿이 저장된 후에는 확인 알림이 표시됩니다.

![템플릿 편집](media/edit-template-portal3b.png)

## <a name="deploy-a-template-resource"></a>템플릿 리소스 배포
**읽기** 권한이 있는 **템플릿**을 배포할 수 있습니다. 배포를 진행하기 위해 Resource Manager 템플릿 매개 변수의 값을 입력합니다.

![템플릿 배포](media/deploy-template-portal1b.png)

## <a name="share-a-template-resource"></a>템플릿 리소스 공유
**템플릿** 리소스를 동료와 공유할 수 있습니다. 공유는 [Azure에서 모든 리소스에 대한 역할 할당](../role-based-access-control/role-assignments-portal.md)과 유사하게 동작합니다. **템플릿** 소유자는 템플릿 리소스와 상호 작용할 수 있는 다른 사용자에게 사용 권한을 제공합니다. **템플릿**을 공유하는 개인 또는 사용자 그룹은 Resource Manager 템플릿 및 해당 갤러리 속성을 볼 수 있습니다.

### <a name="access-control-for-the-microsoftgallery-resources"></a>Microsoft.Gallery 리소스에 대한 액세스 제어
| 역할 | 권한 |
| --- | --- |
| 소유자 |공유를 포함하여 템플릿 리소스에 대한 모든 권한 허용 |
| 판독기 |템플릿 리소스에 대한 읽기 및 실행(배포) 허용 |
| 참가자 |템플릿 리소스에 대한 편집 및 삭제 권한 허용. 사용자는 다른 사용자와 템플릿을 공유할 수 없습니다. |

마우스 오른쪽 단추를 클릭하거나 특정 항목을 볼 때 찾아보기 항목에서 **공유**를 선택합니다.

![템플릿 공유](media/share-template-portal1a.png)

 이제 특정 **템플릿**에 대한 액세스를 제공하는 데 역할, 사용자 또는 그룹을 선택할 수 있습니다. 사용 가능한 역할은 소유자, 판독기 및 참가자입니다.

![템플릿 공유](media/share-template-portal2b.png)

![템플릿 공유](media/share-template-portal3b.png)

**선택** 및 **확인**을 클릭합니다. 이제 리소스에 추가된 사용자 또는 그룹을 확인할 수 있습니다.

![템플릿 공유](media/share-template-portal4b.png)

> [!NOTE]
> 템플릿은 동일한 Azure Active Directory 테넌트의 사용자 및 그룹과만 공유할 수 있습니다. 테넌트에 없는 이메일 주소와 템플릿을 공유하는 경우 사용자에게 게스트로 테넌트에 가입하라는 초대가 전송됩니다.
> 
> 

## <a name="next-steps"></a>다음 단계
* Resource Manager 템플릿을 만드는 방법에 대한 자세한 내용은 [템플릿 작성](../azure-resource-manager/resource-group-authoring-templates.md)
* Resource Manager 템플릿에서 사용할 수 있는 함수를 이해하려면 [템플릿 함수](../azure-resource-manager/resource-group-template-functions.md)를 참조하세요.

