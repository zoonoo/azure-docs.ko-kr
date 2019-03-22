---
title: BizTalk Services의 여러 상태에서 허용되는 작업 | Microsoft Docs
description: 중지, 시작, 다시 시작, 일시 중단, 계속, 삭제, 크기 조정, 업데이트 구성 및 백업 등 여러 MABS 상태에서 허용되는 작업
services: biztalk-services
documentationcenter: ''
author: MandiOhlinger
manager: anneta
editor: ''
ms.assetid: aea738f3-ec76-4099-a41b-e17fea9e252f
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/08/2016
ms.author: mandia
ms.openlocfilehash: 71463b523f0afdf63a7c89ff2b3137ae8d091b3b
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/18/2019
ms.locfileid: "58086703"
---
# <a name="what-you-can-and-cant-do-using-the-biztalk-service-state"></a>BizTalk 서비스 상태를 사용하여 수행할 수 있는 작업과 수행할 수 없는 작업

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]

BizTalk 서비스의 현재 상태에 따라 BizTalk 서비스에서 수행할 수 있는 작업이나 수행할 수 없는 작업이 있습니다.

예를 들어 새로운 BizTalk Service를 프로비전합니다. 프로비전이 성공적으로 완료되면 BizTalk 서비스가 `active` 상태입니다. 활성 상태에서 BizTalk 서비스를 중지, 일시 중단 및 삭제할 수 있습니다. BizTalk 서비스를 중지한 경우 중지에 실패하면 BizTalk 서비스가 `StopFailed` 상태로 전환됩니다. `StopFailed` 상태에서는 BizTalk 서비스를 다시 시작할 수 있습니다. 재개와 같은 허용되지 않는 작업을 시도한 경우 다음과 같은 오류가 발생합니다.

`Operation not allowed`

## <a name="view-the-possible-states"></a>가능한 상태 보기

다음 표에는 BizTalk 서비스가 특정 상태에 있을 때 수행할 수 있는 작업 또는 동작이 나와 있습니다. ✔ 해당 상태에서 수행할 수 있는 작업을 의미합니다. 빈 항목은 해당 상태에서 수행할 수 없는 작업을 의미합니다.

| 서비스 상태 | 시작 | 중지 | 다시 시작 | 일시 중단 | 다시 시작 | 삭제 | 확장 | 주 지역에서 <br/> 구성 | Backup |
| --- | --- | --- | --- | --- | --- | --- |--- | --- | --- |
| Active |  | ✔ | ✔ | ✔ |  | ✔ |✔ |✔ |✔ |
| 사용 안 함 |  |  |  |  |  | ✔ | |  |  | 
| 일시 중단 |  |  |  |  | ✔ | ✔ | |  | ✔ |
| 중지됨 | ✔ |  | ✔ |  |  | ✔ | |  | ✔ |
| 서비스 업데이트 실패 |  |  |  |  |  | ✔ | |  |  | 
| 사용 안 함 실패 |  |  |  |  |  | ✔ | |  |  | 
| 사용 실패 |  |  |  |  |  | ✔ | |  |  | 
| 시작 실패 <br/> 중지 실패 <br/> 다시 시작 실패 | ✔ | ✔ | ✔ |  |  | ✔ | | ✔ | |
| 일시 중단 실패 <br/> 계속 실패|  |  |  | ✔ | ✔ | ✔ | |  |  | 
| 생성 실패 <br/> 복원 실패 |  |  |  |  |  | ✔ | |  |  | 
| 구성 업데이트 실패  |  |  | ✔ |  |  | ✔ | |✔ | |
| 크기 조정 실패 |  |  |  |  |  | ✔ |✔ | |  |



## <a name="see-also"></a>관련 항목
* [BizTalk Services의 대시보드, 모니터 및 크기 조정 탭에서 수행할 수 있는 작업](https://go.microsoft.com/fwlink/p/?LinkID=302281)<br/>
* [BizTalk Services의 Developer, Basic, Standard 및 Premium Edition에서 제공되는 기능](https://go.microsoft.com/fwlink/p/?LinkID=302279)<br/>
* [BizTalk Services를 백업 및 복원하는 방법](https://go.microsoft.com/fwlink/p/?LinkID=329873)<br/>
* [BizTalk Services에 설명된 제한](https://go.microsoft.com/fwlink/p/?LinkID=302282)<br/>
* [BizTalk Services에 대한 Service Bus 및 Access Control 발급자 이름 및 발급자 키 값 검색](https://go.microsoft.com/fwlink/p/?LinkID=303941)<br/>
* [Azure BizTalk Services SDK로 시작하는 방법](https://go.microsoft.com/fwlink/p/?LinkID=302335)

