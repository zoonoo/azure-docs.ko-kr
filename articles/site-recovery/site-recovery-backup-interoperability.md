---
title: Azure 백업을 사용하여 Azure 사이트 복구 사용 지원
description: Azure 사이트 복구 및 Azure 백업을 함께 사용할 수 있는 방법에 대한 개요를 제공합니다.
author: sideeksh
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: sideeksh
ms.openlocfilehash: d8c896bc655339fbd7d51f738fb629450e8c12ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "72376212"
---
# <a name="support-for-using-site-recovery-with-azure-backup"></a>Azure 백업을 사용하여 사이트 복구 를 사용하기 위한 지원

이 문서에서는 [Azure Backup](https://docs.microsoft.com/azure/backup/backup-overview) [서비스와](site-recovery-overview.md) 함께 사이트 복구 서비스를 사용하는 것에 대한 지원을 요약합니다.

**작업** | **Site Recovery 지원** | **세부 정보**
--- | --- | ---
**서비스를 함께 배포** | 지원됨 | 서비스는 상호 운용 가능하며 함께 구성할 수 있습니다.
**파일 백업/복원** | 지원됨 | VM에 대해 백업 및 복제를 사용하도록 설정하고 백업을 수행하면 원본 측 VM 또는 VM 그룹에서 파일을 복원하는 데 문제가 없습니다. 복제는 복제 상태를 변경하지 않고 평소와 같이 계속됩니다.
**디스크 복원** | 현재 지원 없음 | 백업된 디스크를 복원하는 경우 VM에 대한 복제를 다시 사용하지 않도록 설정하고 다시 활성화해야 합니다.
**VM 복원** | 현재 지원 없음 | VM 또는 VM 그룹을 복원하는 경우 VM에 대한 복제를 사용하지 않도록 설정하고 다시 활성화해야 합니다.  


