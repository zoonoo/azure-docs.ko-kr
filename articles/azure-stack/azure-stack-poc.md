---
title: Azure Stack이란? | Microsoft Docs
description: Azure Stack을 사용하면 데이터 센터에서 Azure 서비스를 실행할 수 있습니다.
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
ms.date: 10/15/2018
ms.author: jeffgilb
ms.reviewer: unknown
ms.custom: mvc
ms.openlocfilehash: b2b81938eafe104369e52e72f9958e2adf2cca6f
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/16/2018
ms.locfileid: "49345223"
---
# <a name="what-is-azure-stack"></a>Azure Stack이란?

Microsoft Azure Stack은 데이터 센터에서 Azure 서비스를 제공할 수 있는 하이브리드 클라우드 플랫폼입니다. 이 플랫폼은 개발 중인 비즈니스 요구 사항을 지원하도록 설계 되었습니다. Azure Stack은 에지와 연결이 끊어진 환경 등의 최신 응용 프로그램에 대한 새로운 시나리오를 사용하도록 설정하거나 특정 보안 및 규정 준수 요구 사항을 충족할 수 있습니다.

Azure Stack 두 가지 배포 옵션 사용자의 요구를 충족하기 위해 제공됩니다.

## <a name="azure-stack-integrated-systems"></a>Azure Stack 통합 시스템
Azure Stack 통합 시스템은 Microsoft와 [하드웨어 파트너](https://azure.microsoft.com/overview/azure-stack/integrated-systems/)의 파트너십을 통해 제공되며, 클라우드 기반 혁신 및 컴퓨팅 관리 단순성을 제공하는 솔루션을 만듭니다. Azure Stack은 통합된 하드웨어 및 소프트웨어 시스템으로 제공되므로 클라우드에서 혁신할 수있는 기능과 함께 필요한 유연성과 제어 기능을 사용할 수 있습니다. Azure Stack 통합 시스템의 규모는 4-12 노드이며 하드웨어 파트너와 Microsoft가 공동으로 지원합니다.  Azure Stack 통합 시스템을 사용하여 새로운 시나리오를 만들고 프로덕션 워크로드에 맞는 새로운 솔루션을 배포합니다.

## <a name="azure-stack-development-kit"></a>Azure Stack 개발 키트

Microsoft [ASDK(Azure Stack Development Kit)](.\asdk\asdk-what-is.md)는 Azure Stack에 대한 평가 및 학습에 사용할 수 있는 Azure Stack의 단일 노드 배포입니다.  또한 ASDK를 개발자 환경으로 사용하여 Azure와 일치하는 API 및 툴링을 사용하여 앱을 빌드할 수 있습니다.

>[!Note]
>ASDK는 프로덕션 환경으로 사용되지 않습니다.

ASDK에는 다음과 같은 제한 사항이 있습니다.

* ASDK는 단일 Azure AD(Azure Active Directory) 또는 AD FS(Active Directory Federation Service) ID 공급자와 연결됩니다. 이 디렉터리에 여러 사용자를 만들고 각 사용자에게 구독을 할당할 수 있습니다.
* Azure Stack 구성 요소는 하나의 호스트 컴퓨터에 배포되므로 테넌트 리소스에 사용할 수 있는 물리적 리소스가 제한적입니다. 이 구성은 규모 또는 성능 평가를 위한 것이 아닙니다.
* 네트워크 시나리오는 단일 호스트 및 NIC 배포 요구 사항으로 인해 제한됩니다.

## <a name="next-steps"></a>다음 단계

[주요 기능 및 개념](azure-stack-key-features.md)

[Azure Stack: Azure(pdf)의 확장명](https://azure.microsoft.com/resources/azure-stack-an-extension-of-azure/)
