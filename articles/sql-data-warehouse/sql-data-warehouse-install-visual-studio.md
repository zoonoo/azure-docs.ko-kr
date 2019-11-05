---
title: SQL Data Warehouse 용 Visual Studio 2019 설치 | Microsoft Docs
description: Azure SQL Data Warehouse용 Visual Studio 및 SSDT(SQL Server 개발 도구) 설치
services: sql-data-warehouse
ms.custom: vs-azure
ms.workload: azure-vs
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 10/17/2019
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 0ca59080b4056ba74e2f05038e07d647f12de765
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73485047"
---
# <a name="getting-started-with-visual-studio-2019-for-sql-data-warehouse"></a>SQL Data Warehouse 용 Visual Studio 2019 시작
Visual Studio **2019** SQL SERVER DATA TOOLS (SSDT)는 다음을 수행 하는 데 사용할 수 있는 단일 도구입니다.

- SQL Data Warehouse에 대 한 응용 프로그램 연결, 쿼리 및 개발 
- 개체 탐색기를 활용 하 여 테이블, 뷰, 저장 프로시저 등을 비롯 한 데이터 모델의 모든 개체를 시각적으로 탐색할 수 있습니다.
- 개체에 대 한 T-sql DDL (데이터 정의 언어) 스크립트를 생성 합니다.
- SSDT Database 프로젝트를 사용 하 여 상태 기반 방법을 사용 하 여 데이터 웨어하우스 개발
- Azure Repos를 사용 하 여 데이터베이스 프로젝트를 Git와 같은 원본 제어 시스템과 통합
- Azure DevOps와 같은 자동화 서버를 사용 하 여 연속 통합 및 배포 파이프라인 설정

> [!NOTE]
> 현재 Visual Studio SSDT 데이터베이스 프로젝트는 미리 보기 상태입니다. 이 기능에 대한 주기적인 업데이트를 수신하려면 [UserVoice]에서 투표해주세요.

## <a name="install-visual-studio-2019-preview"></a>Visual Studio 2019 미리 보기 설치
Visual studio **16.3**를 다운로드 하 여 설치 하려면 visual [Visual Studio 2019 다운로드][] 를 참조 하세요. 설치 하는 동안 데이터 저장소 및 처리 워크 로드를 선택 합니다. Visual Studio 2019에는 독립 실행형 SSDT 설치가 더 이상 필요 하지 않습니다.

## <a name="reporting-issues-with-ssdt-visual-studio-2019-preview"></a>SSDT Visual Studio 2019 (미리 보기)와 관련 된 문제 보고
SQL Data Warehouse와 함께 SSDT를 사용 하는 경우 문제를 보고 하려면 메일 배포 목록 <sqldwssdtpreview@service.microsoft.com>

## <a name="next-steps"></a>다음 단계
최신 버전의 SSDT를 만들었으므로 SQL Data Warehouse에 [연결할][connect] 준비가 되었습니다.

<!--Anchors-->

<!--Image references-->

<!--Articles-->
[connect]: ./sql-data-warehouse-query-visual-studio.md

<!--Other-->
[Visual Studio 2019 다운로드]: https://visualstudio.microsoft.com/downloads/
[Installing Visual Studio]: https://msdn.microsoft.com/library/e2h7fzkw.aspx
[SSDT Download]: https://msdn.microsoft.com/library/mt204009.aspx
[UserVoice]: https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/13313247-database-project-from-visual-studio-to-support-azu
