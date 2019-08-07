---
title: Azure 구독을 CloudSimple로 Azure VMware 솔루션의 리소스 풀에 매핑
description: Azure 구독에 대해 CloudSimple을 통해 Azure VMware 솔루션의 리소스 풀을 매핑하는 방법에 대해 설명 합니다.
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/05/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 1bf721f35500d2ff1344996e7750c5e574f40f31
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/06/2019
ms.locfileid: "68816273"
---
# <a name="map-resource-pools-from-your-private-cloud-to-your-azure-subscription"></a>사설 클라우드에서 Azure 구독에 리소스 풀 매핑

Azure 구독 매핑을 사용 하면 사설 클라우드 vCenter의 리소스 풀을 Azure 구독에 매핑할 수 있습니다. CloudSimple 서비스를 만든 구독만 매핑할 수 있습니다.  Azure Portal에서 VMware 가상 컴퓨터를 만들면 매핑된 리소스 풀에 가상 컴퓨터가 배포 됩니다.  CloudSimple 포털에서 사설 클라우드에 대 한 Azure 구독을 보고 관리할 수 있습니다.

구독은 사설 클라우드의 여러 vCenter 리소스 풀에 매핑될 수 있습니다.  각 사설 클라우드의 리소스 풀을 매핑해야 합니다.  Azure Portal에서 VMware 가상 컴퓨터를 만드는 데에는 매핑된 리소스 풀만 사용할 수 있습니다.

> [!IMPORTANT]
> 리소스 풀을 매핑하면 모든 자식 리소스 풀도 매핑됩니다. 자식 리소스 풀이 이미 매핑된 경우 부모 리소스 풀을 매핑할 수 없습니다.

## <a name="before-you-begin"></a>시작하기 전 주의 사항

이 문서에서는 구독에 CloudSimple 서비스와 사설 클라우드가 있다고 가정 합니다.  CloudSimple 서비스를 만들려면 [빠른 시작-서비스 만들기](quickstart-create-cloudsimple-service.md)를 참조 하세요.  사설 클라우드를 만들어야 하는 경우 [빠른 시작-사설 클라우드 환경 구성](quickstart-create-private-cloud.md)을 참조 하세요.

VCenter 클러스터 (루트 리소스 풀)를 구독에 매핑할 수 있습니다.  새 리소스 풀을 만들려면 VMware 설명서 사이트에서 [리소스 풀 만들기](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.resmgmt.doc/GUID-0F6C6709-A5DA-4D38-BE08-6CB1002DD13D.html) 문서를 참조 하세요.

## <a name="default-resource-group"></a>기본 리소스 그룹

Azure Portal에서 새 CloudSimple 가상 컴퓨터를 만들면 리소스 그룹을 선택할 수 있습니다.  매핑된 리소스 풀의 사설 클라우드 vCenter에서 만든 가상 머신은 Azure Portal에 표시 됩니다.  검색 된 가상 머신은 기본 Azure 리소스 그룹에 배치 됩니다.  기본 리소스 그룹의 이름을 변경할 수 있습니다.

## <a name="map-azure-subscription"></a>Azure 구독 매핑

1. [Cloudsimple 포털](access-cloudsimple-portal.md)에 액세스 합니다.

2. **리소스** 페이지를 열고 매핑할 사설 클라우드를 선택 합니다.

3. **Azure 구독 매핑**을 선택 합니다.

4. **Azure 구독 매핑 편집**을 클릭 합니다.

5. 사용 가능한 리소스 풀을 매핑하려면 왼쪽에서 해당 리소스 풀을 선택 하 고 오른쪽 화살표를 클릭 합니다.

6. 매핑을 제거 하려면 오른쪽에서 해당 매핑을 선택 하 고 왼쪽 화살표를 클릭 합니다.

    ![Azure 구독](media/resources-azure-mapping.png)

7. **확인**을 클릭합니다.

## <a name="change-default-resource-group-name"></a>기본 리소스 그룹 이름 변경

1. [Cloudsimple 포털](access-cloudsimple-portal.md)에 액세스 합니다.

2. **리소스** 페이지를 열고 매핑할 사설 클라우드를 선택 합니다.

3. **Azure 구독 매핑**을 선택 합니다.

4. Azure 리소스 그룹 이름에서 **편집** 을 클릭 합니다.

    ![리소스 그룹 이름 편집](media/resources-edit-resource-group-name.png)

5. 리소스 그룹의 새 이름을 입력 하 고 **제출**을 클릭 합니다.

    ![새 리소스 그룹 이름 입력](media/resources-new-resource-group-name.png)

## <a name="next-steps"></a>다음 단계

* [Azure에서 VMware Vm 사용](quickstart-create-vmware-virtual-machine.md)
* [Cloudsimple virtual machines](cloudsimple-virtual-machines.md) 에 대해 자세히 알아보기