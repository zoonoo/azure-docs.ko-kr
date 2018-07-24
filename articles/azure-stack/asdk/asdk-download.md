---
title: 다운로드 하 고 Azure Stack 개발 키트 (ASDK) 추출 | Microsoft Docs
description: 다운로드 하 고 Azure Stack 개발 키트 (ASDK)를 추출 하는 방법을 설명 합니다.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/16/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: b55bc7f6aab522a6313498b6fdccc88870796224
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/23/2018
ms.locfileid: "39213030"
---
# <a name="download-and-extract-the-azure-stack-development-kit-asdk"></a>다운로드 하 고 Azure Stack 개발 키트 (ASDK)를 추출 합니다.
개발 키트 호스트 컴퓨터를 ASDK를 설치 하기 위한 기본 요구를 충족 하는지 확인에 한 후 다음 단계 다운로드는 Cloudbuilder.vhdx 가져오려는 ASDK 배포 패키지를 추출 하는 것입니다.

## <a name="download-the-asdk"></a>ASDK 다운로드
1. 다운로드를 시작 하기 전에 컴퓨터가 다음 필수 조건을 충족 하는지 확인 합니다.

  - 컴퓨터에는 적어도 60GB의 사용 가능한 디스크 공간 네 개의 별도 동일한 논리 하드 드라이브에 또한 운영 체제 디스크에 있어야 합니다.
  - [.NET framework 4.6 (또는 이후 버전)](https://aka.ms/r6mkiy) 설치 해야 합니다.

2. [시작 페이지로 이동](https://azure.microsoft.com/overview/azure-stack/try/?v=try) 고 수 있는 Azure Stack 개발 키트 다운로드, 세부 정보를 제공 클릭 **제출**합니다.
3. 다운로드 및 실행 합니다 [Azure Stack 개발 키트에 대 한 배포 검사](https://go.microsoft.com/fwlink/?LinkId=828735&clcid=0x409) 필수 구성 요소 검사기 스크립트입니다. 이 독립 실행형 스크립트 Azure Stack 개발 키트에 대 한 설치 하면 필수 조건 검사를 거칩니다. Azure Stack 개발 키트에 대 한 더 큰 패키지를 다운로드 하기 전에 하드웨어 및 소프트웨어 요구 사항을 충족 확인 하는 방법을 제공 합니다.
4. 아래 **소프트웨어를 다운로드**, 클릭 **Azure Stack 개발 키트**합니다.

  > [!NOTE]
  > ASDK 다운로드 (AzureStackDevelopmentKit.exe)는 약 10GB입니다.

## <a name="extract-the-asdk"></a>ASDK 추출
1. 다운로드가 완료 되 면 클릭 **실행** ASDK self-extractor (AzureStackDevelopmentKit.exe)를 시작 합니다.
2. 검토 하 고에서 표시 된 사용권 계약에 동의 합니다 **사용권 계약** Self-extractor 마법사 및 클릭 한 다음 페이지 **다음**합니다.
3. 에 표시 되는 개인정보취급방침 정보를 검토 합니다 **중요 알림** Self-extractor 마법사 및 클릭 한 다음 페이지 **다음**합니다.
4. 추출할 Azure Stack 설치 파일 위치를 선택 합니다 **대상 위치 선택** Self-extractor 마법사 및 클릭 한 다음 페이지 **다음**합니다. 기본 위치가 *현재 폴더*\Azure Stack 개발 키트. 
5. 대상 위치에 대 한 요약을 검토 합니다 **추출 준비가** Self-extractor 마법사 및 클릭 한 다음 페이지 **추출** CloudBuilder.vhdx (약 25 GB)를 추출 및 ThirdPartyLicenses.rtf 파일입니다. 이 프로세스는 완료 하려면 다소 시간이 걸립니다.
6. 복사 하거나 이동 CloudBuilder.vhdx 파일 (C:\CloudBuilder.vhdx) C:\ 드라이브의 루트 ASDK 호스트 컴퓨터.

> [!NOTE]
> 파일을 추출한 후 삭제할 수 있습니다 합니다. EXE 및 합니다. 하드 디스크 공간을 복구 BIN 파일입니다. 또는 ASDK 다시 배포 해야 할 경우 파일을 다시 다운로드 하도록 않아도 되도록 이러한 파일을 백업할 수 있습니다.


## <a name="next-steps"></a>다음 단계
[ASDK 호스트 컴퓨터를 준비 합니다.](asdk-prepare-host.md)