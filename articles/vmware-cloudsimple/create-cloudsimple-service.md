---
title: CloudSimple의 Azure VMware 솔루션-CloudSimple service 만들기
description: Azure Portal에서 CloudSimple 서비스를 만드는 방법을 설명 합니다.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 99194c42dbc6ef07301be517021bf0fb4b4e7c23
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2019
ms.locfileid: "72173499"
---
# <a name="create-the-azure-vmware-solution-by-cloudsimple-service"></a>CloudSimple service에서 Azure VMware 솔루션 만들기

CloudSimple로 Azure VMware 솔루션을 시작 하려면 Azure Portal에서 CloudSimple service로 Azure VMware 솔루션을 만듭니다.

> [!IMPORTANT]
> CloudSimple 서비스를 만들기 전에 Azure 구독에 VMwareCloudSimple 리소스 공급자를 등록 해야 합니다. [Azure 구독에서 VMwareCloudSimple 리소스 공급자 사용](enable-cloudsimple-service.md)의 단계를 따릅니다.

## <a name="sign-in-to-azure"></a>Azure에 로그인

[Azure Portal](https://portal.azure.com)에 로그인합니다.

## <a name="create-the-service"></a>서비스 만들기

1. **모든 서비스**를 선택합니다.
2. **Cloudsimple 서비스**를 검색 합니다.
    ![ 검색 CloudSimple Service @ no__t-1
3. **Cloudsimple 서비스**를 선택 합니다.
4. **추가** 를 클릭 하 여 새 서비스를 만듭니다.
    ![Add CloudSimple Service @ no__t-1
5. CloudSimple 서비스를 만들려는 구독을 선택 합니다.
6. 서비스에 대 한 리소스 그룹을 선택 합니다. 새 리소스 그룹을 추가 하려면 **새로 만들기**를 클릭 합니다.
7. 서비스를 식별 하는 이름을 입력 합니다.
8. 서비스 게이트웨이의 CIDR을 입력 합니다. 온-프레미스 서브넷, Azure 서브넷 또는 계획 된 CloudSimple 서브넷과 겹치지 않는/28 서브넷을 지정 합니다. 서비스를 만든 후에는 CIDR을 변경할 수 없습니다.

    ![CloudSimple 서비스 만들기](media/create-cloudsimple-service.png)
9. **확인**을 클릭합니다.

서비스가 만들어지고 서비스 목록에 추가 됩니다.

## <a name="next-steps"></a>다음 단계

* [노드를 프로 비전](create-nodes.md) 하는 방법 알아보기
* [사설 클라우드를 만드는](create-private-cloud.md) 방법 알아보기
* [사설 클라우드 환경을 구성](quickstart-create-private-cloud.md) 하는 방법 알아보기
