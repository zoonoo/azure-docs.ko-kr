---
author: baanders
description: Azure Digital Twins에 대한 파일 포함 - 인스턴스를 설정하기 위한 필수 구성 요소
ms.service: digital-twins
ms.topic: include
ms.date: 10/29/2020
ms.author: baanders
ms.openlocfilehash: 2be13256ff0fcc93aa40db3bcb2f38aa0cf58cbc
ms.sourcegitcommit: afb9e9d0b0c7e37166b9d1de6b71cd0e2fb9abf5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/14/2021
ms.locfileid: "103463760"
---
이 문서에서 Azure Digital Twins로 작업하려면 먼저 **Azure Digital Twins 인스턴스를 설정** 하고 이를 사용하는 데 필요한 권한이 필요합니다. 이전 작업에서 이미 Azure Digital Twins 인스턴스가 설정된 경우 해당 인스턴스를 사용할 수 있습니다.

그렇지 않으면 [*방법: 인스턴스 및 인증 설정*](../articles/digital-twins/how-to-set-up-instance-portal.md)의 지침을 따릅니다. 지침에는 각 단계를 성공적으로 완료하고 새 인스턴스를 사용할 준비가 되었는지 확인하는 단계도 포함되어 있습니다.

Azure Digital Twins 인스턴스를 설정한 후 나중에 인스턴스에 연결하는 데 필요한 다음 값을 기록해 둡니다.
* 인스턴스의 **_호스트 이름_**. 이 값은 Azure Portal에서 찾을 수 있습니다([지침](../articles/digital-twins/how-to-set-up-instance-portal.md#verify-success-and-collect-important-values)).
* 인스턴스를 만드는 데 사용한 **Azure 구독**(해당 이름 또는 ID가 작동함). [Azure Portal](https://portal.azure.com)에서 인스턴스에 대한 동일한 *개요* 페이지를 보면 Azure Digital Twins 인스턴스가 어떤 구독에 있는지 확인할 수 있습니다.
