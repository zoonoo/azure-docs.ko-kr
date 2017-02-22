
---
title: "Azure Stack의 지역 관리 | Microsoft Docs"
description: "Azure Stack의 지역 관리를 간략하게 설명합니다."
services: azure-stack
documentationcenter: 
author: efemmano
manager: dsavage
editor: 
ms.assetid: e94775d5-d473-4c03-9f4e-ae2eada67c6c
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/13/2017
ms.author: efemmano
translationtype: Human Translation
ms.sourcegitcommit: fc1a1b1a5f51e45d553d9883c05810b5cf6ed0c0
ms.openlocfilehash: 4a27767f3f383f246413ee9fe3bda018881714af


---
# <a name="region-management-in-azure-stack"></a>Azure Stack의 지역 관리
Azure Stack Technical Preview 2에서는 지역 개념이 도입되었습니다. 지역이란 Azure Stack 인프라를 구성하는 하드웨어 리소스로 구성된 논리적 엔터티입니다. Azure Stack 인프라 수명 주기를 성공적으로 운영하는 데 필요한 모든 리소스를 지역 관리 내에서 찾을 수 있습니다.

Azure Stack POC(개념 증명)는 단일 노드 배포이며 한 지역과 동일합니다. 별도의 하드웨어에서 Azure Stack POC Azure의 인스턴스를 설정하면 이 인스턴스는 다른 지역이 됩니다.

## <a name="information-available-through-the-region-management-tile"></a>지역 관리 타일을 통해 제공되는 정보
이 Azure Stack 미리 보기 릴리스에서는 **지역 관리** 타일에 제공되는 지역 관리 기능 집합이 도입되었습니다. 이 타일에서 Azure Stack 지역 및 구성 요소를 모니터링하고 업데이트할 수 있으며, 구성 요소는 지역별로 다릅니다.

 ![지역 관리 타일](media/azure-stack-manage-region/image1.png)

 이 기술 미리 보기의 새 기능은 다음과 같습니다. 

  ![지역 관리 블레이드의 창 설명](media/azure-stack-manage-region/image2.png)
  
1. **빠른 시작**. 여기서 Azure Stack 관련 문서 및 링크를 찾을 수 있습니다. 

2. **경고**. 이 타일은 시스템 수준 경고를 나열하고 각 경고에 대한 세부 정보를 제공합니다.

3. **인프라 역할**. 인프라 역할은 Azure Stack을 실행하는 데 필요한 구성 요소입니다. 역할을 클릭하면 특정 역할과 연결된 경고 및 이 역할이 실행되고 있는 역할 인스턴스를 볼 수 있습니다.

  >[!NOTE]
  Azure Stack Technical Preview 2에서는 상태 컨트롤러 역할만 경고를 표시합니다.

4. **리소스 공급자**. 리소스 공급자는 Azure Stack을 실행하는 데 필요한 구성 요소에서 제공하는 테넌트 기능을 관리하는 장소입니다. 리소스 공급자마다 관리 환경이 제공됩니다. 이 환경에는 특정 공급자에 대한 경고, 메트릭 및 리소스 공급자에 한정된 기타 관리 기능이 포함될 수 있습니다.

  >[!NOTE]
  Azure Stack Technical Preview 2에서는 저장소, 계산 및 네트워크 리소스 공급자만이 연결된 환경을 제공합니다.

5. **업데이트**. 이 타일에서는 버전, 업데이트 가용성 및 업데이트 기록을 포함하여 Azure Stack 인프라의 업데이트 상태를 볼 수 있습니다. 자세한 내용은 [Azure Stack의 업데이트 관리](azure-stack-updates.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계
[Azure Stack의 업데이트 관리](azure-stack-updates.md)







<!--HONumber=Feb17_HO3-->


