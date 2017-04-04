---
title: "개인 Docker 레지스트리 만들기 - Azure Portal | Microsoft Docs"
description: "Azure Portal을 사용하여 개인 Docker 컨테이너 레지스트리 만들기 및 관리 시작"
services: container-registry
documentationcenter: 
author: stevelas
manager: balans
editor: dlepow
tags: 
keywords: 
ms.assetid: 53a3b3cb-ab4b-4560-bc00-366e2759f1a1
ms.service: container-registry
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/14/2016
ms.author: stevelas
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: e74c5428f0e31d9d3cf06b85aa8cefde868e9d67
ms.lasthandoff: 03/27/2017

---

# <a name="create-a-private-docker-container-registry-using-the-azure-portal"></a>Azure Portal을 사용하여 개인 Docker 컨테이너 레지스트리 만들기
Azure Portal을 사용하여 컨테이너 레지스트리를 만들고 설정을 관리합니다. [Azure CLI 2.0 명령](container-registry-get-started-azure-cli.md)을 사용하거나 Container Registry [REST API](https://go.microsoft.com/fwlink/p/?linkid=834376)에서 프로그래밍 방식으로 컨테이너 레지스트리를 만들고 관리할 수도 있습니다.

배경 지식 및 개념은 [개요](container-registry-intro.md)를 참조하세요.



## <a name="create-a-container-registry"></a>컨테이너 레지스트리 만들기
1. [Azure Portal](https://portal.azure.com)에서 **+ 새로 만들기**를 클릭합니다.
2. **Azure 컨테이너 레지스트리**에 대한 마켓플레이스를 검색합니다.
3. 게시자가 **Microsoft**인 **Azure 컨테이너 레지스트리**를 선택합니다.
    ![Azure Marketplace의 컨테이너 레지스트리 서비스](./media/container-registry-get-started-portal/container-registry-marketplace.png)
4. **만들기**를 클릭합니다. **Azure 컨테이너 레지스트리** 블레이드가 표시됩니다.

    ![컨테이너 레지스트리 설정](./media/container-registry-get-started-portal/container-registry-settings.png)
5. **Azure 컨테이너 레지스트리** 블레이드에서 다음 정보를 입력합니다. 완료하면 **만들기**를 클릭합니다.

    a. **레지스트리 이름**: 특정 레지스트리에 대한 전역적으로 고유한 최상위 도메인 이름입니다. 이 예제의 경우 레지스트리 이름이 *myRegistry01*이지만 자신만의 고유한 이름으로 대체합니다. 이름은 문자와 숫자만 포함할 수 있습니다.

    b. **리소스 그룹**: 기존 [리소스 그룹](../azure-resource-manager/resource-group-overview.md#resource-groups)을 선택하거나 새 리소스 그룹의 이름을 입력합니다.

    c. **위치**: **미국 중남부**와 같이 서비스를 [사용할 수 있는](https://azure.microsoft.com/regions/services/) Azure 데이터 센터 위치를 선택합니다.

    d. **관리 사용자**: 필요한 경우 관리 사용자가 레지스트리에 액세스할 수 있도록 설정합니다. 이 설정은 레지스트리를 만든 후에 변경할 수 있습니다.

    > [!IMPORTANT]
    > 관리 사용자 계정을 통해 액세스를 제공하는 것 외에, 컨테이너 레지스트리는 Azure Active Directory 서비스 주체에 의해 지원되는 인증을 지원합니다. 자세한 내용 및 고려 사항은 [컨테이너 레지스트리로 인증](container-registry-authentication.md)을 참조하세요.


    e. **저장소 계정**: 기본 설정을 사용하여 [저장소 계정](../storage/storage-introduction.md)을 만들거나 동일한 위치에서 기존 저장소 계정을 선택합니다. Premium Storage는 현재 지원되지 않습니다.


## <a name="manage-registry-settings"></a>레지스트리 설정 관리
레지스트리를 만든 후 포털의 **Container Registry** 블레이드를 시작하여 레지스트리 설정을 찾습니다. 예를 들어 레지스트리에 로그인하기 위해 설정이 필요하거나 관리 사용자를 사용하거나 사용하지 않도록 설정할 수 있습니다.

1. **Container Registry** 블레이드에서 레지스트리의 이름을 클릭합니다.

    ![컨테이너 레지스트리 블레이드](./media/container-registry-get-started-portal/container-registry-blade.png)
2. 액세스 설정을 관리하려면 **액세스 키**를 클릭합니다.

    ![컨테이너 레지스트리 액세스](./media/container-registry-get-started-portal/container-registry-access.png)
3. 다음 설정에 유의하세요.

   * **로그인 서버** - 레지스트리에 로그인하기 위해 사용하는 정규화된 이름입니다. 이 예제에서는 `myregistry01.azurecr.io`입니다.
   * **관리 사용자** - 레지스트리의 관리 사용자 계정을 사용하거나 사용하지 않도록 설정합니다.
   * **사용자 이름** 및 **암호** - 레지스트리에 로그인하는 데 사용할 수 있는 관리 사용자(사용하는 경우)의 자격 증명입니다. 암호는 선택적으로 다시 생성할 수 있습니다. 다른 암호를 다시 생성하는 동안에 하나의 암호를 사용하여 레지스트리에 대한 연결을 유지할 수 있도록 두 개의 암호가 생성됩니다. 대신 서비스 주체로 인증하려면 [개인 Docker 컨테이너 레지스트리로 인증](container-registry-authentication.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계
* [Docker CLI를 사용하여 첫 번째 이미지 푸시](container-registry-get-started-docker-cli.md)

