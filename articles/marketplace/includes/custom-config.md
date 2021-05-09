---
title: 포함 파일
description: 파일
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: include
author: emuench
ms.author: mingshen
ms.date: 10/09/2020
ms.openlocfilehash: a1bc7cf1fd339ca3660c7b39326f37d2763c74b2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92284325"
---
추가 구성이 필요한 경우 시작 시 실행되는 예약된 작업을 사용하여 VM을 배포한 후 최종 변경을 수행합니다. 또한 다음 사항도 고려합니다.

- 일회성 작업인 경우 작업이 성공적으로 완료된 후 이 작업을 삭제해야 합니다.
- 이러한 두 드라이브만 항상 존재하는 것이 보장되므로 구성이 C 또는 D 이외의 드라이브에 따라 달라져서는 안 됩니다(C 드라이브는 운영 체제 디스크이고 D 드라이브는 임시 로컬 디스크입니다).

Linux 사용자 지정에 대한 자세한 내용은 [Linux용 가상 머신 확장 및 기능](../../virtual-machines/extensions/features-linux.md)을 참조하세요.