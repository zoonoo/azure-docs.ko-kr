---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/20/2018
ms.author: glenga
ms.openlocfilehash: 82d122ed236dc72ced7ebafe2301ef5f1143897f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "76963661"
---
성능을 최대화하려면 각 함수 앱에 대해 별도의 스토리지 계정을 사용하세요. 이는 많은 양의 스토리지 트랜잭션을 생성하는 Durable Functions 또는 Event Hub 트리거 함수가 있는 경우에 특히 중요합니다. 애플리케이션 로직이 직접(Storage SDK를 사용하여) 또는 스토리지 바인딩 중 하나를 통해 Azure Storage와 상호 작용하는 경우 전용 스토리지 계정을 사용해야 합니다. 예를 들어 일부 데이터를 Blob Storage에 기록하는 Event Hub 트리거 함수가 있는 경우 두 개의 스토리지 계정을 사용하는데, 하나는 함수 앱용 스토리지 계정이고, 다른 하나는 함수가 저장하는 Blob용 스토리지 계정입니다.