---
title: "VMware vCenter 태그 지정을 사용하는 VM 그룹화 | Microsoft Docs"
description: "Azure Migrate 서비스를 사용하여 평가를 실행하기 전에 그룹 만드는 방법을 설명합니다."
services: migration-planner
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 5c279804-aa30-4946-a222-6b77c7aac508
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/21/2017
ms.author: raynew
ms.openlocfilehash: db33e31cdef143aa70809442457e447446a1681a
ms.sourcegitcommit: 5bced5b36f6172a3c20dbfdf311b1ad38de6176a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/27/2017
---
# <a name="group-vms-with-vcenter-tagging"></a>vCenter 태그 지정을 사용하는 VM 그룹화

이 문서에서는 VMware vCenter에서 태그를 지정하여 [Azure Migrate](migrate-overview.md) 평가를 위한 컴퓨터 그룹을 만드는 방법에 대해 설명합니다. 그룹을 만들 때 사용할 태그 범주를 지정합니다. 

## <a name="set-up-tagging"></a>태그 지정 설정

Azure Migrate를 배포하는 동안 온-프레미스 Azure Migrate VM은 vCenter Server에서 관리하는 ESXi 호스트에서 실행 중인 컴퓨터를 검색합니다. 검색 프로세스 전에 vCenter 태그 지정을 설정해야 합니다.

1. VMware vSphere Web Client에서 vCenter Server로 이동합니다.
2. 현재 태그를 검토하려면 **태그**를 클릭합니다.
3. VM에 태그를 지정하려면 **Related Objects** > **Virtual Machines**를 선택한 다음 태그를 지정할 VM을 선택합니다.
4. **요약** > **태그**에서 **할당**을 클릭합니다. 
5. **새 태그**를 클릭하고 태그 이름 및 설명을 지정합니다.
6. 태그에 대한 범주를 만들려면 드롭다운 목록에서 **새 범주**를 선택합니다.
7. 범주 이름 및 설명, 카디널리티를 지정합니다. 그런 후 **OK**를 클릭합니다.

    ![VM 태그](./media/how-to-tag-v-center/vm-tag.png)

## <a name="use-tagging-to-create-groups"></a>태그 지정을 사용하여 그룹 만들기

1. [VMware 평가 자습서](tutorial-assessment-vmware.md#run-the-collector-to-discover-vms)에 설명된 대로 온-프레미스 컴퓨터의 검색을 설정합니다.
2. **그룹화에 대한 태그 범주**에서 평가 그룹의 기반이 될 vCenter 태그 범주를 선택합니다. Azure Migrate는 선택한 범주에 대한 그룹을 자동으로 만듭니다.

    

## <a name="next-steps"></a>다음 단계

[VMware VM 평가를 설정합니다](tutorial-assessment-vmware.md).
