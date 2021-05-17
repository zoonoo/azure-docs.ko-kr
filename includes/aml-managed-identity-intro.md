---
title: 포함 파일
description: 포함 파일
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 08/24/2020
ms.openlocfilehash: 70b636b7bb508b71475a7464983b091d5d10e0e1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96026870"
---
 Azure Machine Learning 컴퓨팅 클러스터는 코드에 자격 증명을 포함하지 않고 Azure 리소스에 대한 액세스를 인증하는 [관리 ID](../articles/active-directory/managed-identities-azure-resources/overview.md)도 지원합니다. 두 가지 종류의 관리 ID가 있습니다.

* **시스템 할당 관리 ID** 는 Azure Machine Learning 컴퓨팅 클러스터에서 직접 활성화됩니다. 시스템 할당 ID의 수명 주기는 컴퓨팅 클러스터와 직접적으로 연관됩니다. 컴퓨팅 클러스터가 삭제되면 Azure는 Azure AD에서 자격 증명과 ID를 자동으로 정리합니다.
* **사용자 할당 관리 ID** 는 Azure 관리 ID 서비스를 통해 제공되는 독립 실행형 Azure 리소스입니다. 사용자 할당 관리 ID를 여러 리소스에 할당할 수 있으며, 원하는 기간 동안 지속됩니다.