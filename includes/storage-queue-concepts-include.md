---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.custom: seo-python-october2019
ms.openlocfilehash: 24e755edbb18e85bcaa97435dee27418c1457221
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74174985"
---
## <a name="what-is-queue-storage"></a>Queue storage 란?

Azure Queue Storage는 HTTP 또는 HTTPS를 사용하여 인증된 호출을 통해 전 세계 어디에서나 액세스할 수 있는 다수의 메시지를 저장하기 위한 서비스입니다. 단일 큐 메시지의 크기는 최대 64KB일 수 있으며, 하나의 큐에 스토리지 계정의 총 용량 제한까지 수백만 개의 메시지가 포함될 수 있습니다. 큐 저장소는 비동기적으로 처리할 작업의 백로그를 만드는 데 주로 사용 됩니다.

## <a name="queue-service-concepts"></a>큐 서비스 개념

Azure 큐 서비스에는 다음 구성 요소가 포함 되어 있습니다.

![Azure 큐 서비스 구성 요소](./media/storage-queue-concepts-include/azure-queue-service-components.png)

* **URL 형식:** 다음 URL 형식을 사용하여 큐에 주소를 지정할 수 있습니다.   
    http://`<storage account>`.queue.core.windows.net/`<queue>` 
  
    다음 URL은 다이어그램에 있는 큐의 주소를 지정합니다.  
  
    `http://myaccount.queue.core.windows.net/images-to-download`

* **Storage 계정:** Azure Storage에 대한 모든 액세스는 Storage 계정을 통해 수행됩니다. Storage 계정 용량에 대한 자세한 내용은 [Azure Storage 확장성 및 성능 목표](../articles/storage/common/storage-scalability-targets.md) (영문)를 참조하십시오.
* **큐:** 큐에는 메시지 집합이 포함됩니다. 모든 메시지는 큐에 있어야 합니다. 큐 이름은 모두 소문자여야 합니다. 큐의 명명에 대한 자세한 내용은 [큐 및 메타데이터 명명](https://msdn.microsoft.com/library/azure/dd179349.aspx)을 참조하세요.
* **메시지:** 최대 64KB인 임의 형식의 메시지입니다. 메시지가 큐에 남아 있을 수 있는 최대 시간은 7일입니다. 2017-07-29 이상 버전에서 허용되는 최대 TTL(Time to Live)은 모든 양수 또는 메시지가 만료되지 않는 -1입니다. 이 매개 변수를 생략하면 기본 TTL(Time to Live)은 7일입니다.

