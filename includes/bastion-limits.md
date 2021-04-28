---
title: 포함 파일
description: 포함 파일
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: include
ms.date: 03/25/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 53785d539479153acd65d5fd347ff337543614cc
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108142239"
---
| 워크로드 유형* | 제한** |
| --- | --- |
| 밝음 |100 |
| 중간 |50 |
| Heavy |5 |

*이러한 워크로드 유형은 [원격 데스크톱 워크로드](/windows-server/remote/remote-desktop-services/remote-desktop-workloads)에 정의됩니다.<br>
**이러한 제한은 Azure Bastion에 대한 RDP 성능 테스트를 기반으로 합니다. 숫자는 다른 진행 중인 RDP 세션 또는 기타 진행 중인 SSH 세션으로 인해 달라질 수 있습니다.