---
title: Azure Migrate의 Collector 어플라이언스 업그레이드 | Microsoft Docs
description: Azure Migrate의 Collector 어플라이언스 업그레이드에 대한 정보를 제공합니다.
author: musa-57
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 09/28/2018
ms.author: hamusa
services: azure-migrate
ms.openlocfilehash: 6080096194ec5b11f1d5593fcbb6732d1551667f
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/28/2018
ms.locfileid: "47431395"
---
# <a name="collector-update-release-history"></a>Collector 업데이트 릴리스 기록

이 문서에서는 [Azure Migrate](migrate-overview.md)의 Collector 어플라이언스 업그레이드 정보를 간략하게 제공합니다.

Azure Migrate Collector는 Azure로 마이그레이션하기 전에 평가를 진행하기 위해 온-프레미스 vCenter 환경을 검색하는 데 사용되는 간편한 어플라이언스입니다. [자세히 알아보기](concepts-collector.md).


## <a name="one-time-discovery-upgrade-versions"></a>일회성 검색: 버전 업그레이드

### <a name="version-10914"></a>버전 1.0.9.14

업그레이드 [패키지 1.0.9.14](https://aka.ms/migrate/col/upgrade_9_14)에 대한 해시 값

**알고리즘** | **해시 값**
--- | ---
MD5 | c5bf029e9fac682c6b85078a61c5c79c
SHA1 | af66656951105e42680dfcc3ec3abd3f4da8fdec
SHA256 | 58b685b2707f273aa76f2e1d45f97b0543a8c4d017cd27f0bdb220e6984cc90e

### <a name="version-10913"></a>버전 1.0.9.13

업그레이드 [패키지 1.0.9.13](https://aka.ms/migrate/col/upgrade_9_13)에 대한 해시 값

**알고리즘** | **해시 값**
--- | ---
MD5 | 739f588fe7fb95ce2a9b6b4d0bf9917e
SHA1 | 9b3365acad038eb1c62ca2b2de1467cb8eed37f6
SHA256 | 7a49fb8286595f39a29085534f29a623ec2edb12a3d76f90c9654b2f69eef87e

### <a name="version-10911"></a>버전 1.0.9.11

업그레이드 [패키지 1.0.9.11](https://aka.ms/migrate/col/upgrade_9_11)에 대한 해시 값

**알고리즘** | **해시 값**
--- | ---
MD5 | 0e36129ac5383b204720df7a56b95a60
SHA1 | aa422ef6aa6b6f8bc88f27727e80272241de1bdf
SHA256 | 5f76dbbe40c5ccab3502cc1c5f074e4b4bcbf356d3721fd52fb7ff583ff2b68f

### <a name="version-1097"></a>버전 1.0.9.7

업그레이드 [패키지 1.0.9.7](https://aka.ms/migrate/col/upgrade_9_7)에 대한 해시 값

**알고리즘** | **해시 값**
--- | ---
MD5 | 01ccd6bc0281f63f2a672952a2a25363
SHA1 | 3e6c57523a30d5610acdaa14b833c070bffddbff
SHA256 | e3ee031fb2d47b7881cc5b13750fc7df541028e0a1cc038c796789139aa8e1e6

## <a name="continuous-discovery-upgrade-versions"></a>연속 검색: 업그레이드 버전

연속 검색 어플라이언스용 업그레이드는 아직 제공되지 않습니다.

## <a name="run-an-upgrade"></a>업그레이드 실행

OVA를 다시 다운로드하지 않고도 Collector를 최신 버전으로 업그레이드할 수 있습니다.

1. 이렇게 하려면 아래 목록의 최신 업그레이드 패키지를 다운로드하세요.
2. 다운로드한 핫픽스가 안전한지 확인하려면 관리자 권한 명령 창을 열고 다음 명령을 실행하여 ZIP 파일에 대한 해시를 생성합니다. 생성된 해시는 특정 버전에 대해 언급된 해시와 일치해야 합니다.

    ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```

    예: **C:\>CertUtil -HashFile C:\AzureMigrate\CollectorUpdate_release_1.0.9.14.zip SHA256)**
3. Collector 어플라이언스 VM에 zip 파일을 복사합니다.
4. zip 파일을 마우스 오른쪽 단추로 클릭하고 **압축 풀기**를 선택합니다.
5. **Setup.ps1**을 마우스 오른쪽 단추로 클릭  >  **PowerShell에서 실행**을 선택하고 설치 지침을 따릅니다.


## <a name="next-steps"></a>다음 단계

- Collector 어플라이언스에 대해 [자세히 알아봅니다](concepts-collector.md).
- VMware VM의 [평가를 실행](tutorial-assessment-vmware.md)합니다.
