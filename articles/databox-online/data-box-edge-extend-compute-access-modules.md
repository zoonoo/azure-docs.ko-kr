---
title: Azure 데이터 박스 에지에서 컴퓨팅 네트워크 관리하여 모듈에 액세스| 마이크로 소프트 문서
description: 외부 IP를 통해 모듈에 액세스하기 위해 Data Box Edge의 계산 네트워크를 확장하는 방법에 대해 설명합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 05/17/2019
ms.author: alkohli
ms.openlocfilehash: 907647725dd6795b3b6482476de7442fbbf66114
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "65917237"
---
# <a name="enable-compute-network-on-your-azure-data-box-edge"></a>Azure 데이터 상자 가장자리에서 계산 네트워크 사용

이 문서에서는 Azure Data Box Edge에서 실행 중인 모듈이 장치에서 활성화된 계산 네트워크에 액세스하는 방법에 대해 설명합니다.

네트워크를 구성하려면 다음 단계를 수행합니다.

- 컴퓨팅을 위해 데이터 박스 에지 디바이스에서 네트워크 인터페이스 활성화
- 데이터 박스 에지에서 컴퓨팅 네트워크에 액세스하는 모듈 추가
- 모듈이 활성화된 네트워크 인터페이스에 액세스할 수 있는지 확인

이 자습서에서는 웹 서버 앱 모듈을 사용하여 시나리오를 보여 줍니다.

## <a name="prerequisites"></a>사전 요구 사항

시작하기 전에 다음이 필요합니다.

- 장치 설정이 완료된 데이터 상자 가장자리 장치입니다.
- 자습서에 따라 **계산 구성** 단계를 완료했습니다. [Tutorial: Transform data with Azure Data Box Edge](data-box-edge-deploy-configure-compute-advanced.md#configure-compute) 장치에 연결된 IoT Hub 리소스, IoT 장치 및 IoT Edge 장치가 있어야 합니다.

## <a name="enable-network-interface-for-compute"></a>컴퓨팅을 위한 네트워크 인터페이스 사용

외부 네트워크를 통해 장치에서 실행되는 모듈에 액세스하려면 장치의 네트워크 인터페이스에 IP 주소를 할당해야 합니다. 로컬 웹 UI에서 이러한 컴퓨팅 설정을 관리 할 수 있습니다.

로컬 웹 UI에서 다음 단계를 수행하여 컴퓨팅 설정을 구성합니다.

1. 로컬 웹 UI에서 **구성 > 컴퓨팅 설정**으로 이동합니다.  

2. 장치에서 실행할 계산 모듈에 연결하는 데 사용할 네트워크 인터페이스를 **사용하도록 설정합니다.**

    - 고정 IP 주소를 사용하는 경우 네트워크 인터페이스의 IP 주소를 입력합니다.
    - DHCP를 사용하는 경우 IP 주소가 자동으로 할당됩니다. 이 예제에서는 DHCP를 사용합니다.

    ![계산 설정 활성화 1](media/data-box-edge-extend-compute-access-modules/enable-compute-setting-1.png)

3. **적용**을 선택하여 설정을 적용합니다. DHCP를 사용하는 경우 네트워크 인터페이스에 할당된 IP 주소를 기록합니다.

    ![컴퓨팅 설정 사용](media/data-box-edge-extend-compute-access-modules/enable-compute-setting-2.png)

## <a name="add-webserver-app-module"></a>웹 서버 앱 모듈 추가

다음 단계를 수행하여 데이터 박스 에지 장치에 웹 서버 앱 모듈을 추가합니다.

1. 데이터 박스 에지 장치와 연결된 IoT Hub 리소스로 이동한 다음 **IoT Edge 장치를**선택합니다.
2. 데이터 박스 에지 장치와 연결된 IoT 에지 장치를 선택합니다. 장치 **세부 정보에서** **모듈 설정 을 선택합니다.** **모듈 추가시** **+ 추가를** 선택한 다음 **IoT Edge 모듈을**선택합니다.
3. **IoT Edge 사용자 정의 모듈** 블레이드에서:

    1. 배포할 웹 서버 앱 모듈의 **이름을** 지정합니다.
    2. 모듈 이미지에 대한 **이미지 URI를** 제공합니다. 제공된 이름과 태그와 일치하는 모듈이 검색됩니다. 이 경우 `nginx:stable` 공용 [Docker 리포지토리에서](https://hub.docker.com/_/nginx/)안정적인 nginx 이미지(안정태그로 태그지정)를 가져옵니다.
    3. 컨테이너 **만들기 옵션에서**다음 샘플 코드를 붙여넣습니다.  

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

        이 구성을 사용하면 TCP 포트 8080에서 *http를* 통해 계산 네트워크 IP를 사용하여 모듈에 액세스할 수 있습니다(기본 웹 서버 포트가 80이 됨).

        ![IoT Edge 사용자 지정 모듈 블레이드에서 포트 정보 지정](media/data-box-edge-extend-compute-access-modules/module-information.png)

    4. **저장**을 선택합니다.

## <a name="verify-module-access"></a>모듈 액세스 확인

1. 모듈이 성공적으로 배포되어 실행 중인지 확인합니다. 장치 **세부 정보** 페이지에서 **모듈** 탭에서 모듈의 런타임 상태가 **실행중이어야**합니다.  
2. 웹 서버 앱 모듈에 연결합니다. 브라우저 창을 열고 다음을 입력합니다.

    `http://<compute-network-IP-address>:8080`

    웹 서버 앱이 실행 중임을 알 수 있습니다.

    ![지정된 포트를 통해 모듈에 대한 연결 확인](media/data-box-edge-extend-compute-access-modules/verify-connect-module-1.png)

## <a name="next-steps"></a>다음 단계

- [Azure Portal을 통해 사용자를 관리](data-box-edge-manage-users.md)하는 방법을 알아봅니다.
