---
title: "Azure Cloud Shell(미리 보기) 기능 | Microsoft Docs"
description: "Azure Cloud Shell의 기능 개요"
services: 
documentationcenter: 
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: 
ms.service: 
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.author: juluk
ms.translationtype: Human Translation
ms.sourcegitcommit: 44eac1ae8676912bc0eb461e7e38569432ad3393
ms.openlocfilehash: e789283ab9b25f634c50b341ca882bbf9c70a225
ms.contentlocale: ko-kr
ms.lasthandoff: 05/17/2017

---

# <a name="features--tools-for-azure-cloud-shell"></a>Azure Cloud Shell에 대한 기능 및 도구
Azure Cloud Shell은 Azure 리소스를 관리 및 개발하기 위한 브라우저 기반 셸 환경입니다.

Cloud Shell은 컴퓨터의 직접 설치, 버전 관리 및 유지 관리에 대한 오버헤드 없이 Azure 리소스 관리를 위한 브라우저에서 액세스할 수 있는 미리 구성된 셸 경험을 제공합니다.

Cloud Shell은 요청별로 컴퓨터를 프로비전하므로 결과적으로 컴퓨터 상태는 세션 간에 유지되지 않습니다. Cloud Shell은 대화형 세션용으로 빌드되었기 때문에 셸은 셸 비활성 시간 10분 후 자동으로 종료됩니다.

## <a name="tools"></a>도구
|Category   |이름   |
|---|---|
|Linux 셸 인터프리터|Bash<br> sh               |
|Azure 도구            |Azure CLI 2.0 및 1.0     |
|텍스트 편집기           |vim<br> nano<br> emacs       |
|소스 제어         |git                    |
|빌드 도구            |make<br> maven<br> npm<br> pip         |
|컨테이너             |Docker<br> Kubectl<br> DC/OS CLI         |
|데이터베이스              |MySQL 클라이언트<br> PostgreSql 클라이언트<br> sqlcmd 유틸리티      |
|기타                  |iPython 클라이언트 |

## <a name="language-support"></a>언어 지원
|language   |버전   |
|---|---|
|.NET       |1.01       |
|Go         |1.7        |
|Node.js    |6.9.4      |
|Python     |2.7 및 3.5|

## <a name="secure-automatic-authentication"></a>안전한 자동 인증
Cloud Shell은 Azure CLI 2.0에 대한 계정 액세스를 안전하고 자동으로 인증합니다.

## <a name="azure-files-persistence"></a>Azure 파일 지속성
Cloud Shell은 임시 컴퓨터를 사용하여 요청별로 할당되므로 Home 외부에 있는 로컬 파일 및 컴퓨터 상태는 세션 간에 유지되지 않습니다.
세션 간에 파일을 유지하기 위해 Cloud Shell에서는 처음 시작할 때 Azure 파일 공유 연결을 안내합니다.
작업이 완료되면 Cloud Shell은 이후의 모든 세션에 대해 저장소를 자동으로 연결합니다.

[Azure 파일 공유를 Cloud Shell에 연결하는 것에 대해 자세히 알아보세요.](persisting-shell-storage.md)

## <a name="next-steps"></a>다음 단계
[Cloud Shell 빠른 시작](quickstart.md) 
[Azure CLI 2.0 알아보기](https://docs.microsoft.com/cli/azure/)
