---
title: Azure Static Web Apps 미리 보기의 할당량
description: Azure Static Web Apps 미리 보기와 관련된 할당량에 대해 알아보기
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: overview
ms.date: 05/08/2020
ms.author: cshoe
ms.openlocfilehash: 915f8675ffda0d70347905d11c7d93975b9d4526
ms.sourcegitcommit: 33368ca1684106cb0e215e3280b828b54f7e73e8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92132740"
---
# <a name="quotas-in-azure-static-web-apps-preview"></a>Azure Static Web Apps 미리 보기의 할당량

Azure Static Web Apps 미리 보기에는 다음과 같은 할당량이 있습니다.

> [!IMPORTANT]
> Azure Static Web Apps는 공개 미리 보기로 제공되며 프로덕션 용도로는 사용할 수 없습니다.

| 기능                     | 무료 요금제        |
|-----------------------------|------------------|
| 포함된 대역폭          | 매월 100GB |
| 초과분 대역폭           | 사용할 수 없음      |
| Azure 구독당 앱 | 10               |
| 앱 크기                    | 250MB           |
| 사전 프로덕션 환경 | 1                |
| 사용자 지정 도메인              | 1                |
| 권한 부여<br><br>사용자 지정 역할 및 라우팅 규칙 사용 | 최대 25명의 최종 사용자 초대 및 할당된 역할 |
| Azure 기능             | 사용 가능        |
| SLA                         | None             |

## <a name="github-storage"></a>GitHub 스토리지

GitHub Actions 및 패키지는 자체 할당량 세트가 있는 GitHub 스토리지를 사용합니다. Azure Static Web Apps 사이트를 만들면 GitHub는 배포 후에도 사이트의 아티팩트를 저장합니다.

자세한 내용은 다음 리소스를 참조하세요.

- [작업 스토리지 공간 관리](https://github.community/t5/GitHub-Actions/Managing-Actions-storage-space/td-p/38944)
- [GitHub Actions 요금 청구 정보](https://help.github.com/github/setting-up-and-managing-billing-and-payments-on-github/about-billing-for-github-actions#about-billing-for-github-actions)
- [GitHub Actions에 대한 지출 한도 관리](https://help.github.com/github/setting-up-and-managing-billing-and-payments-on-github/managing-your-spending-limit-for-github-actions)

## <a name="next-steps"></a>다음 단계

- [개요](overview.md)
