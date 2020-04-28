---
title: 모듈에 액세스 Azure Data Box Edge에서 계산 네트워크 관리 | Microsoft Docs
description: Data Box Edge에서 계산 네트워크를 확장 하 여 외부 IP를 통해 모듈에 액세스 하는 방법을 설명 합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 05/17/2019
ms.author: alkohli
ms.openlocfilehash: 907647725dd6795b3b6482476de7442fbbf66114
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "65917237"
---
# <a name="enable-compute-network-on-your-azure-data-box-edge"></a>Azure Data Box Edge에서 compute 네트워크를 사용 하도록 설정

이 문서에서는 Azure Data Box Edge에서 실행 되는 모듈이 장치에서 사용 하도록 설정 된 계산 네트워크에 액세스할 수 있는 방법을 설명 합니다.

네트워크를 구성 하려면 다음 단계를 수행 합니다.

- 계산을 위해 Data Box Edge 장치에서 네트워크 인터페이스를 사용 하도록 설정
- Data Box Edge에서 계산 네트워크에 액세스 하는 모듈을 추가 합니다.
- 모듈이 사용 가능한 네트워크 인터페이스에 액세스할 수 있는지 확인 합니다.

이 자습서에서는 웹 서버 앱 모듈을 사용 하 여 시나리오를 보여 줍니다.

## <a name="prerequisites"></a>사전 요구 사항

시작 하기 전에 다음이 필요 합니다.

- 장치 설정이 완료 된 Data Box Edge 장치
- [자습서: 장치에서 Azure Data Box Edge를 사용 하 여 데이터 변환](data-box-edge-deploy-configure-compute-advanced.md#configure-compute) 에 따라 계산 단계를 **구성** 했습니다. 장치에 연결 된 IoT Hub 리소스, IoT 장치 및 IoT Edge 장치가 있어야 합니다.

## <a name="enable-network-interface-for-compute"></a>Compute에 대해 네트워크 인터페이스를 사용 하도록 설정

외부 네트워크를 통해 장치에서 실행 되는 모듈에 액세스 하려면 장치에서 네트워크 인터페이스에 IP 주소를 할당 해야 합니다. 로컬 웹 UI에서 이러한 컴퓨팅 설정을 관리 할 수 있습니다.

로컬 웹 UI에서 다음 단계를 수행하여 컴퓨팅 설정을 구성합니다.

1. 로컬 웹 UI에서 **구성 > 컴퓨팅 설정**으로 이동합니다.  

2. 장치에서 실행할 계산 모듈에 연결 하는 데 사용 하려는 네트워크 인터페이스를 **사용 하도록 설정** 합니다.

    - 고정 IP 주소를 사용하는 경우 네트워크 인터페이스의 IP 주소를 입력합니다.
    - DHCP를 사용 하는 경우 IP 주소가 자동으로 할당 됩니다. 이 예에서는 DHCP를 사용 합니다.

    ![계산 설정 1 사용](media/data-box-edge-extend-compute-access-modules/enable-compute-setting-1.png)

3. **적용**을 선택하여 설정을 적용합니다. DHCP를 사용 하는 경우 네트워크 인터페이스에 할당 된 IP 주소를 기록해 둡니다.

    ![컴퓨팅 설정 사용](media/data-box-edge-extend-compute-access-modules/enable-compute-setting-2.png)

## <a name="add-webserver-app-module"></a>웹 서버 앱 모듈 추가

Data Box Edge 장치에 웹 서버 앱 모듈을 추가 하려면 다음 단계를 수행 합니다.

1. Data Box Edge 장치와 연결 된 IoT Hub 리소스로 이동한 다음 **IoT Edge 장치**를 선택 합니다.
2. Data Box Edge 장치와 연결 된 IoT Edge 장치를 선택 합니다. **장치 세부 정보**에서 **모듈 설정**을 선택 합니다. **모듈 추가**에서 **+ 추가** 를 선택 하 고 **모듈 IoT Edge**선택 합니다.
3. **사용자 지정 모듈 IoT Edge** 블레이드에서 다음을 수행 합니다.

    1. 배포 하려는 웹 서버 앱 모듈의 **이름을** 지정 합니다.
    2. 모듈 이미지에 대 한 **이미지 URI** 를 제공 합니다. 제공 된 이름 및 태그와 일치 하는 모듈이 검색 됩니다. 이 경우 `nginx:stable` 는 공용 [Docker 리포지토리에서](https://hub.docker.com/_/nginx/)안정적인 nginx 이미지 (안정적인 것으로 태그가 지정 됨)를 끌어옵니다.
    3. **컨테이너 만들기 옵션**에서 다음 샘플 코드를 붙여넣습니다.  

        ```
        {
            "HostConfig": {
                "PortBindings": {
                    "80/tcp": [
                        {
                            "HostPort": "8080"
                        }
                    ]
                }
            }
        }
        ```

        이 구성을 통해 TCP 포트 8080에서 *http* 를 통한 COMPUTE 네트워크 IP를 사용 하 여 모듈에 액세스할 수 있습니다 (기본 웹 서버 포트는 80로 설정 됨).

        ![IoT Edge 사용자 지정 모듈 블레이드에서 포트 정보를 지정 합니다.](media/data-box-edge-extend-compute-access-modules/module-information.png)

    4. **저장**을 선택합니다.

## <a name="verify-module-access"></a>모듈 액세스 확인

1. 모듈이 성공적으로 배포 되 고 실행 중인지 확인 합니다. **장치 세부 정보** 페이지의 **모듈** 탭에서 모듈의 런타임 상태가 **실행 중**이어야 합니다.  
2. 웹 서버 앱 모듈에 연결 합니다. 브라우저 창을 열고 다음을 입력 합니다.

    `http://<compute-network-IP-address>:8080`

    웹 서버 앱이 실행 되 고 있는 것을 볼 수 있습니다.

    ![지정 된 포트를 통해 모듈에 대 한 연결 확인](media/data-box-edge-extend-compute-access-modules/verify-connect-module-1.png)

## <a name="next-steps"></a>다음 단계

- [Azure Portal을 통해 사용자를 관리](data-box-edge-manage-users.md)하는 방법을 알아봅니다.
