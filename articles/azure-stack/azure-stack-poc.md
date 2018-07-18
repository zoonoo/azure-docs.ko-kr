---
title: Azure Stack이란? | Microsoft Docs
description: Azure 스택을 사용 하면 데이터 센터에서 Azure 서비스를 실행할 수 있습니다.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: d9e6aee1-4cba-4df5-b5a3-6f38da9627a3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 06/04/2018
ms.author: jeffgilb
ms.reviewer: unknown
ms.custom: mvc
ms.openlocfilehash: 848df59b01cc0c03b9a5f3dae2644d469c8651bb
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/08/2018
ms.locfileid: "35234889"
---
# <a name="what-is-azure-stack"></a>Azure Stack이란?

Microsoft Azure 스택은 데이터 센터에서 Azure 서비스를 제공할 수 있는 하이브리드 클라우드 플랫폼입니다. 이 플랫폼은 개발 중인 비즈니스 요구 사항을 지원 하도록 설계 되었습니다. Azure 스택 가장자리와 연결이 끊어진된 환경 등의 현대적 응용 프로그램에 대 한 새로운 시나리오를 사용 하도록 설정 하거나 특정 보안 및 규정 준수 요구 사항을 충족할 수 있습니다.

Azure 스택 두 가지 배포 옵션 사용자의 요구를 충족 하기 위해 제공 됩니다.

## <a name="azure-stack-integrated-systems"></a>Azure Stack 통합 시스템
통합된 시스템의 Microsoft 파트너 관계를 통해 제공 되는 azure 스택 및 [하드웨어 파트너](https://azure.microsoft.com/overview/azure-stack/integrated-systems/), 혁신 클라우드 속도 제공 하는 솔루션을 만들기 및 관리의 간편함을 계산 합니다. Azure 스택 하는 통합된 하드웨어 및 소프트웨어 시스템을 제공 하는, 때문에 한 유연성 및 클라우드에서 혁신 하도록 하는 기능과 더불어 필요한 제어 합니다. Azure 스택 통합 시스템 4-12 노드에서 하 고 공동으로 하드웨어 파트너 및 Microsoft에서 지원 됩니다.  Azure 스택 통합 시스템을 사용 하 여 새로운 시나리오를 만들고 프로덕션 작업에 대 한 새 솔루션을 배포 합니다.

## <a name="azure-stack-development-kit"></a>Azure Stack 개발 키트

Microsoft [Azure 스택 개발 키트 (ASDK)](.\asdk\asdk-what-is.md) 단일 노드 배포를 평가 하 여 Azure 스택에 대 한 자세한 내용은 사용할 수 있는 Azure 스택입니다.  또한 ASDK 개발자 환경으로 도구 및 Api를 사용 하 여 앱을 빌드 하려는 Azure와 일치 하는 사용할 수 있습니다.

>[!Note]
>ASDK 프로덕션 환경으로 사용할 수 하는 데 사용 되지 않습니다.

ASDK에 다음과 같은 제한이 있습니다.

* ASDK 단일 Azure Active Directory (Azure AD) 또는 Active Directory Federation Services (AD FS) id 공급자와 연결 됩니다. 이 디렉터리에 여러 사용자를 만들고 각 사용자에게 구독을 할당할 수 있습니다.
* 없기 때문에 한 호스트 컴퓨터에서 Azure 스택 구성 요소 배포 제한 된 물리적 리소스를 테 넌 트 리소스에 사용할 수 있는. 이 구성은 소수 자릿수 또는 성능 평가에 적합 하지 않습니다.
* 네트워킹 시나리오는 단일 호스트 및 NIC 배포 요구 사항으로 인해 제한 됩니다.

## <a name="next-steps"></a>다음 단계

- [주요 기능 및 개념](azure-stack-key-features.md)
- [Azure 스택: Azure (pdf)의 확장](https://azure.microsoft.com/resources/azure-stack-an-extension-of-azure/)
