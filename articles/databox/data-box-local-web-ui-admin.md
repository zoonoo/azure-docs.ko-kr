---
title: Microsoft Azure Data Box 로컬 웹 UI 관리 | Microsoft Docs
description: 로컬 웹 UI를 사용하여 Data Box 디바이스를 관리하는 방법을 설명합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: overview
ms.date: 09/11/2018
ms.author: alkohli
ms.openlocfilehash: 9dd519f8efc9700f7a747aa37a9c02414f3e1865
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2018
ms.locfileid: "49093887"
---
# <a name="use-the-local-web-ui-to-administer-your-data-box"></a>로컬 웹 UI를 사용하여 Data Box 관리

이 문서는 Data Box에서 수행할 수 있는 몇 가지 구성 및 관리 작업을 설명합니다. 디바이스의 로컬 웹 UI와 Azure Portal UI를 통해 Data Box를 관리할 수 있습니다. 이 문서에서는 로컬 웹 UI를 사용하여 수행할 수 있는 작업을 중점적으로 설명합니다.

Data Box의 로컬 웹 UI는 디바이스의 초기 구성에 사용됩니다. 로컬 웹 UI를 사용하여 Data Box를 종료하거나 다시 시작하고, 진단 테스트를 실행하고, 소프트웨어를 업데이트하고, 복사 로그를 확인하고, Microsoft 지원에 보낼 로그 패키지를 생성할 수도 있습니다.

이 문서에는 다음 자습서가 포함되어 있습니다.

- 지원 패키지 생성
- 장치 종료 또는 다시 시작
- 디바이스에서 안전하게 데이터 지우기
- 디바이스의 사용 가능한 용량 모니터링
- 체크섬 유효성 검사 건너뛰기 

## <a name="generate-support-package"></a>지원 패키지 생성

디바이스 문제가 발생하는 경우 시스템 로그에서 지원 패키지를 만들 수 있습니다. Microsoft 지원에서는 이 패키지를 사용하여 문제를 해결합니다. 지원 패키지를 만들려면 다음 단계를 수행합니다.

1. 로컬 웹 UI에서 **고객 지원에 문의**로 이동한 다음 **지원 패키지 만들기**를 클릭합니다.

    ![지원 패키지 만들기 1](media/data-box-local-web-ui-admin/create-support-package-1.png)

2. 지원 패키지가 수집됩니다. 이 작업은 몇 분 정도 걸립니다.

    ![지원 패키지 만들기 2](media/data-box-local-web-ui-admin/create-support-package-2.png)

3. 지원 패키지 만들기가 완료되면 **지원 패키지 다운로드**를 클릭합니다. 

    ![지원 패키지 만들기 4](media/data-box-local-web-ui-admin/create-support-package-4.png)

4. 다운로드 위치를 찾아서 선택합니다. 폴더를 열어 내용을 확인합니다.

    ![지원 패키지 만들기 5](media/data-box-local-web-ui-admin/create-support-package-5.png)


## <a name="shut-down-or-restart-your-device"></a>장치 종료 또는 다시 시작

로컬 웹 UI를 사용하여 Data Box를 종료하거나 다시 시작할 수 있습니다. 다시 시작하기 전에 호스트에서 공유를 오프라인으로 전환한 후 디바이스를 다시 시작하는 것이 좋습니다. 이렇게 하면 데이터 손상 가능성이 최소화됩니다. 디바이스를 종료할 때 데이터 복사가 진행 중이지 않은지 확인합니다.

Data Box를 종료하려면 다음 단계를 수행합니다.

1. 로컬 웹 UI에서 **종료 또는 다시 시작**으로 이동합니다.
2. **종료**를 클릭합니다.

    ![Data Box 종료 1](media/data-box-local-web-ui-admin/shut-down-local-web-ui-1.png)

3. 종료를 확인하라는 메시지가 표시되면 **확인**을 클릭하여 계속 진행합니다.

    ![Data Box 종료 2](media/data-box-local-web-ui-admin/shut-down-local-web-ui-2.png)

디바이스가 종료되면 전면 패널의 전원 단추를 사용하여 디바이스를 켭니다.

Data Box를 다시 시작하려면 다음 단계를 수행합니다.

1. 로컬 웹 UI에서 **종료 또는 다시 시작**으로 이동합니다.
2. **다시 시작**을 클릭합니다.

    ![Data Box 다시 시작 1](media/data-box-local-web-ui-admin/restart-local-web-ui-1.png)

3. 다시 시작을 확인하라는 메시지가 표시되면 **확인**을 클릭하여 계속 진행합니다.

   디바이스가 종료되었다가 다시 시작됩니다.

## <a name="view-available-capacity-of-the-device"></a>디바이스의 사용 가능한 용량 확인

디바이스 대시보드를 통해 디바이스에서 사용 가능한 용량과 사용한 용량을 확인할 수 있습니다. 

1. 로컬 웹 UI에서 **대시보드 보기**로 이동합니다.
2. **연결 및 복사** 아래에 장치의 사용 가능한 공간과 사용한 공간이 표시됩니다.

    ![사용 가능한 용량 확인](media/data-box-local-web-ui-admin/verify-used-space-dashboard.png)


## <a name="skip-checksum-validation"></a>체크섬 유효성 검사 건너뛰기

기본적으로는 배송을 준비할 때 데이터의 체크섬이 생성됩니다. 드물지만 파일 형식(작은 파일 크기)에 따라서는 체크섬 생성 속도가 매우 느릴 수 있습니다. 이러한 경우에는 체크섬을 건너뛸 수 있습니다. 

성능이 매우 낮은 경우가 아니면 체크섬은 사용하는 것이 좋습니다.

1. 디바이스 로컬 웹 UI의 오른쪽 위에 있는 설정으로 이동합니다.

    ![체크섬 사용 안 함](media/data-box-local-web-ui-admin/disable-checksum.png)

2. 체크섬 유효성 검사를 **사용 안 함**으로 설정합니다.
3. **적용**을 클릭합니다.

## <a name="next-steps"></a>다음 단계

- [Azure Portal을 통해 Data Box를 관리](data-box-portal-admin.md)하는 방법을 알아봅니다.

