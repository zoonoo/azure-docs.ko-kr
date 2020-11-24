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
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2020
ms.locfileid: "95560397"
---
 Azure Machine Learning 계산 클러스터는 코드에 자격 증명을 포함 하지 않고 Azure 리소스에 대 한 액세스를 인증 하 [는 관리 id](../articles/active-directory/managed-identities-azure-resources/overview.md) 도 지원 합니다. 두 가지 종류의 관리 ID가 있습니다.

* **시스템 할당 관리 id** 는 Azure Machine Learning 계산 클러스터에서 직접 사용 하도록 설정 됩니다. 시스템 할당 id의 수명 주기는 계산 클러스터에 직접 연결 됩니다. 계산 클러스터가 삭제 되 면 azure는 Azure AD에서 자격 증명과 id를 자동으로 정리 합니다.
* **사용자 할당 관리 id** 는 Azure 관리 id 서비스를 통해 제공 되는 독립 실행형 azure 리소스입니다. 사용자 할당 관리 id를 여러 리소스에 할당할 수 있으며, 원하는 기간 동안 지속 됩니다.