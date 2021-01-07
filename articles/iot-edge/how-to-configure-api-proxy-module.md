---
title: API 프록시 모듈 구성-Azure IoT Edge | Microsoft Docs
description: IoT Edge 게이트웨이 계층 구조에 대 한 API 프록시 모듈을 사용자 지정 하는 방법을 알아봅니다.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/10/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom:
- amqp
- mqtt
monikerRange: '>=iotedge-2020-11'
ms.openlocfilehash: 894398d63e326db3c6ee9de9bebc426a6e621600
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/21/2020
ms.locfileid: "95024673"
---
# <a name="configure-the-api-proxy-module-for-your-gateway-hierarchy-scenario-preview"></a>게이트웨이 계층 시나리오에 대 한 API 프록시 모듈 구성 (미리 보기)

API 프록시 모듈을 사용 하면 IoT Edge 장치가 클라우드 서비스에 직접 연결 하는 대신 게이트웨이를 통해 HTTP 요청을 보낼 수 있습니다. 이 문서에서는 게이트웨이 계층 요구 사항을 지원 하도록 모듈을 사용자 지정할 수 있도록 구성 옵션을 안내 합니다.

>[!NOTE]
>이 기능에는 Linux 컨테이너를 실행하는 IoT Edge 버전 1.2(공개 미리 보기로 제공됨)가 필요합니다.

일부 네트워크 아키텍처에서 게이트웨이 뒤에 있는 장치 IoT Edge 클라우드에 직접 액세스할 수 없습니다. Cloud services에 연결 하려고 하는 모든 모듈은 실패 합니다. API 프록시 모듈은 게이트웨이 계층 구조를 통과 하도록 모듈 연결을 다시 라우팅하는 방법으로이 구성에서 다운스트림 IoT Edge 장치를 지원 합니다. 클라이언트는 터널링을 사용 하지 않고 각 계층에서 연결을 종료 하 여 HTTPS를 통해 여러 서비스와 통신할 수 있는 안전한 방법을 제공 합니다. API 프록시 모듈은 최상위 계층의 IoT Edge 장치에 도달할 때까지 게이트웨이 계층의 IoT Edge 장치 간에 프록시 모듈 역할을 합니다. 이 시점에서 최상위 계층 IoT Edge 장치에서 실행 되는 서비스는 이러한 요청을 처리 하 고, API 프록시 모듈은 단일 포트를 통해 로컬 서비스의 모든 HTTP 트래픽을 클라우드로 프록시 합니다.

API 프록시 모듈은 하위 계층 장치에서 컨테이너 이미지를 끌어오거나 blob을 저장소로 푸시할 수 있도록 하는 등 게이트웨이 계층 구조에 대 한 많은 시나리오를 사용할 수 있습니다. 프록시 규칙의 구성은 데이터를 라우팅하는 방법을 정의 합니다. 이 문서에서는 몇 가지 일반적인 구성 옵션을 설명 합니다.

## <a name="deploy-the-proxy-module"></a>프록시 모듈 배포

API 프록시 모듈은 MCR (Microsoft Container Registry)에서 사용할 수 `mcr.microsoft.com/azureiotedge-api-proxy:latest` 있습니다.

Azure Marketplace: [IOT EDGE Api proxy](https://azuremarketplace.microsoft.com/marketplace/apps/azure-iot.azureiotedge-api-proxy?tab=Overview)에서 직접 api 프록시 모듈을 배포할 수도 있습니다.

## <a name="understand-the-proxy-module"></a>프록시 모듈 이해

API 프록시 모듈은 nginx 역방향 프록시를 활용 하 여 네트워크 계층을 통해 데이터를 라우팅합니다. 프록시는 모듈에 포함 됩니다. 즉, 모듈 이미지는 프록시 구성을 지원 해야 합니다. 예를 들어 프록시가 특정 포트에서 수신 대기 하는 경우 모듈에 해당 포트가 열려 있어야 합니다.

프록시는 모듈에 포함 된 기본 구성 파일로 시작 합니다. [모듈](../iot-hub/iot-hub-devguide-module-twins.md)쌍을 사용 하 여 클라우드에서 새 구성을 모듈에 전달할 수 있습니다. 또한 환경 변수를 사용 하 여 배포 시 구성 설정을 설정 하거나 해제할 수 있습니다.

이 문서에서는 먼저 기본 구성 파일에 대해 중점적으로 설명 하 고 환경 변수를 사용 하 여 해당 설정을 사용 하는 방법을 중점적으로 설명 합니다. 그런 다음 구성 파일을 끝에 사용자 지정 하는 방법을 설명 합니다.

### <a name="default-configuration"></a>기본 구성

API 프록시 모듈은 일반적인 시나리오를 지원 하 고 사용자 지정할 수 있는 기본 구성과 함께 제공 됩니다. 모듈의 환경 변수를 통해 기본 구성을 제어할 수 있습니다.

현재 기본 환경 변수는 다음과 같습니다.

| 환경 변수 | Description |
| -------------------- | ----------- |
| `PROXY_CONFIG_ENV_VAR_LIST` | 업데이트 하려는 모든 변수를 쉼표로 구분 된 목록으로 나열 합니다. 이 단계에서는 실수로 잘못 된 구성 설정을 수정 하는 것을 방지 합니다.
| `NGINX_DEFAULT_PORT` | Nginx 프록시가 수신 대기 하는 포트를 변경 합니다. 이 환경 변수를 업데이트 하는 경우 선택 하는 포트가 dockerfile 모듈에도 노출 되 고 배포 매니페스트에서 포트 바인딩으로 선언 되었는지 확인 합니다.<br><br>기본값은 443입니다.<br><br>Azure Marketplace에서 배포 하는 경우 edgeHub 모듈과 충돌을 방지 하기 위해 기본 포트가 8000로 업데이트 됩니다. 자세한 내용은 [열린 포트 최소화](#minimize-open-ports)를 참조 하세요. |
| `DOCKER_REQUEST_ROUTE_ADDRESS` | Docker 요청을 라우팅하는 주소입니다. 최상위 계층 장치에서이 변수를 수정 하 여 레지스트리 모듈을 가리키도록 합니다.<br><br>기본값은 부모 호스트 이름입니다. |
| `BLOB_UPLOAD_ROUTE_ADDRESS` | Blob 레지스트리 요청을 라우팅하는 주소입니다. Blob 저장소 모듈을 가리키도록 최상위 계층 장치에서이 변수를 수정 합니다.<br><br>기본값은 부모 호스트 이름입니다. |

## <a name="minimize-open-ports"></a>열린 포트 최소화

열려 있는 포트 수를 최소화 하기 위해 API 프록시 모듈은 edgeHub 모듈을 대상으로 하는 트래픽을 포함 하 여 모든 HTTPS 트래픽 (포트 443)을 릴레이 해야 합니다. API 프록시 모듈은 기본적으로 443 포트에서 모든 edgeHub 트래픽을 다시 라우팅하도록 구성 됩니다.

열려 있는 포트를 최소화 하도록 배포를 구성 하려면 다음 단계를 사용 합니다.

1. 포트 443에 바인딩하지 않도록 edgeHub 모듈 설정을 업데이트 합니다. 그렇지 않으면 포트 바인딩 충돌이 발생 합니다. 기본적으로 edgeHub 모듈은 포트 443, 5671 및 8883에 바인딩합니다. 포트 443 바인딩을 삭제 하 고 나머지 두 개는 그대로 둡니다.

   ```json
   {
     "HostConfig": {
       "PortBindings": {
         "5671/tcp": [
           {
             "HostPort": "5671"
           }
         ],
         "8883/tcp": [
           {
             "HostPort": "8883"
           }
         ]
       }
     }
   }
   ```

1. 포트 443에서 바인딩하려면 API 프록시 모듈을 구성 합니다.

   1. **NGINX_DEFAULT_PORT** 환경 변수의 값을로 설정 `443` 합니다.
   1. 포트 443에 바인딩하기 위해 컨테이너 만들기 옵션을 업데이트 합니다.

      ```json
      {
        "HostConfig": {
          "PortBindings": {
            "443/tcp": [
              {
                "HostPort": "443"
              }
            ]
          }
        }
      }
      ```

열려 있는 포트를 최소화 하지 않아도 되는 경우 edgeHub 모듈이 포트 443을 사용 하도록 하 고 API 프록시 모듈이 다른 포트에서 수신 하도록 구성할 수 있습니다. 예를 들어 API 프록시 모듈은 **NGINX_DEFAULT_PORT** 환경 변수의 값을로 설정 하 `8000` 고 포트 8000에 대 한 포트 바인딩을 만들어 포트 8000에서 수신 대기할 수 있습니다.

## <a name="enable-container-image-download"></a>컨테이너 이미지 다운로드 사용

API 프록시 모듈의 일반적인 사용 사례는 하위 계층의 IoT Edge 장치가 컨테이너 이미지를 끌어올 수 있도록 하는 것입니다. 이 시나리오에서는 [Docker 레지스트리 모듈](https://hub.docker.com/_/registry) 을 사용 하 여 클라우드에서 컨테이너 이미지를 검색 하 고 최상위 계층에 캐시 합니다. API 프록시는 모든 HTTPS 요청을 릴레이 하 여 하위 계층에서 컨테이너 이미지를 다운로드 하 고 최상위 계층의 레지스트리 모듈에서 제공 합니다.

이 시나리오에서 다운스트림 IoT Edge 장치는 `$upstream` 이미지의 컨테이너 레지스트리 대신 도메인 이름과 API 프록시 모듈 포트 번호를 차례로 가리켜야 합니다. 예: `$upstream:8000/azureiotedge-api-proxy:1.0`

이 사용 사례는 자습서에서 [게이트웨이를 사용 하 여 IoT Edge 장치 계층 구조 만들기](tutorial-nested-iot-edge.md)에 설명 되어 있습니다.

**최상위 계층** 에서 다음 모듈을 구성 합니다.

* Docker 레지스트리 모듈
  * *레지스트리와* 같이 기억 하기 쉬운 이름으로 모듈을 구성 하 고 모듈에서 요청을 수신 하는 포트를 노출 합니다.
  * 컨테이너 레지스트리에 매핑되도록 모듈을 구성 합니다.
* API 프록시 모듈
  * 다음 환경 변수를 구성 합니다.

    | Name | 값 |
    | ---- | ----- |
    | `DOCKER_REQUEST_ROUTE_ADDRESS` | 레지스트리 모듈 이름 및 열린 포트입니다. `registry:5000`)을 입력합니다. |
    | `NGINX_DEFAULT_PORT` | Nginx 프록시가 다운스트림 장치에서 요청을 수신 대기 하는 포트입니다. `8000`)을 입력합니다. |

  * 다음 createOptions를 구성 합니다.

    ```json
    {
       "HostConfig": {
          "PortBindings": {
             "8000/tcp": [
                {
                   "HostPort": "8000"
                }
             ]
          }
       }
    }
    ```

이 시나리오의 **하위 계층** 에서 다음 모듈을 구성 합니다.

* API 프록시 모듈
  * 다음 환경 변수를 구성 합니다.

    | Name | 값 |
    | ---- | ----- |
    | `NGINX_DEFAULT_PORT` | Nginx 프록시가 다운스트림 장치에서 요청을 수신 대기 하는 포트입니다. `8000`)을 입력합니다. |

  * 다음 createOptions를 구성 합니다.

    ```json
    {
       "HostConfig": {
          "PortBindings": {
             "8000/tcp": [
                {
                   "HostPort": "8000"
                }
             ]
          }
       }
    }
    ```  

## <a name="enable-blob-upload"></a>Blob 업로드 사용

API 프록시 모듈의 또 다른 사용 사례는 더 낮은 계층에서 IoT Edge 장치를 사용 하도록 설정 하 여 blob을 업로드 하는 것입니다. 이 사용 사례를 사용 하면 모듈 로그 업로드 또는 지원 번들 업로드와 같은 하위 계층 장치에서 문제 해결 기능을 사용할 수 있습니다.

이 시나리오에서는 최상위 계층의 [IoT Edge 모듈에서 Azure Blob Storage](https://azuremarketplace.microsoft.com/marketplace/apps/azure-blob-storage.edge-azure-blob-storage) 를 사용 하 여 Blob 만들기 및 업로드를 처리 합니다.

**최상위 계층** 에서 다음 모듈을 구성 합니다.

* IoT Edge 모듈의 Azure Blob Storage입니다.
* API 프록시 모듈
  * 다음 환경 변수를 구성 합니다.

    | Name | 값 |
    | ---- | ----- |
    | `BLOB_UPLOAD_ROUTE_ADDRESS` | Blob 저장소 모듈 이름 및 열린 포트입니다. `azureblobstorageoniotedge:1102`)을 입력합니다. |
    | `NGINX_DEFAULT_PORT` | Nginx 프록시가 다운스트림 장치에서 요청을 수신 대기 하는 포트입니다. `8000`)을 입력합니다. |

  * 다음 createOptions를 구성 합니다.

    ```json
    {
       "HostConfig": {
          "PortBindings": {
             "8000/tcp": [
                {
                   "HostPort": "8000"
                }
             ]
          }
       }
    }
    ```

이 시나리오의 **하위 계층** 에서 다음 모듈을 구성 합니다.

* API 프록시 모듈
  * 다음 환경 변수를 구성 합니다.

    | Name | 값 |
    | ---- | ----- |
    | `NGINX_DEFAULT_PORT` | Nginx 프록시가 다운스트림 장치에서 요청을 수신 대기 하는 포트입니다. `8000`)을 입력합니다. |

  * 다음 createOptions를 구성 합니다.

    ```json
    {
       "HostConfig": {
          "PortBindings": {
             "8000/tcp": [
                {
                   "HostPort": "8000"
                }
             ]
          }
       }
    }
    ```

다음 단계를 사용 하 여 지원 번들 또는 로그 파일을 최상위 계층에 있는 blob storage 모듈에 업로드 합니다.

1. Azure Storage 탐색기 또는 REST Api를 사용 하 여 blob 컨테이너를 만듭니다. 자세한 내용은 [IoT Edge에서 Azure Blob Storage를 사용 하 여에 지에 데이터 저장](how-to-store-data-blob.md)을 참조 하세요.
1. [IoT Edge 배포에서 로그 검색](how-to-retrieve-iot-edge-logs.md)의 단계에 따라 로그 또는 지원 번들 업로드를 요청 하지만 `$upstream` blob 저장소 모듈 주소 대신 도메인 이름과 오픈 프록시 포트를 사용 합니다. 예를 들면 다음과 같습니다.

   ```json
   {
      "schemaVersion": "1.0",
      "sasUrl": "https://$upstream:8000/myBlobStorageName/myContainerName?SAS_key",
      "since": "2d",
      "until": "1d",
      "edgeRuntimeOnly": false
   }
   ```

## <a name="edit-the-proxy-configuration"></a>프록시 구성 편집

기본 구성 파일은 API 프록시 모듈에 포함 되지만 모듈 쌍을 사용 하 여 클라우드를 통해 새 구성을 모듈에 전달할 수 있습니다.

사용자 고유의 구성을 작성 하는 경우에도 환경을 사용 하 여 배포 당 설정을 조정할 수 있습니다. 다음 구문을 사용합니다.

* `${MY_ENVIRONMENT_VARIABLE}`환경 변수의 값을 검색 하는 데 사용 합니다.
* 조건문을 사용 하 여 환경 변수 값에 따라 설정을 설정 하거나 해제 합니다.

   ```conf
   #if_tag ${MY_ENVIRONMENT_VARIABLE}
       statement to execute if environment variable evaluates to 1
   #endif_tag ${MY_ENVIRONMENT_VARIABLE}

   #if_tag !${MY_ENVIRONMENT_VARIABLE}
       statement to execute if environment variable evaluates to 0
   #endif_tag !${MY_ENVIRONMENT_VARIABLE}
   ```

API 프록시 모듈은 프록시 구성을 구문 분석할 때 먼저에 나열 된 모든 환경 변수를 대체를 `PROXY_CONFIG_ENV_VAR_LIST` 사용 하 여 제공 된 값으로 바꿉니다. 그런 다음 및 쌍 사이의 모든 항목을 `#if_tag` `#endif_tag` 바꿉니다. 그런 다음 모듈은 nginx 역방향 프록시에 구문 분석 된 구성을 제공 합니다.

프록시 구성을 동적으로 업데이트 하려면 다음 단계를 사용 합니다.

1. 구성 파일을 작성 합니다. 이 기본 템플릿은 [nginx_default_config](https://github.com/Azure/iotedge/blob/master/edge-modules/api-proxy-module/templates/nginx_default_config.conf) 참조로 사용할 수 있습니다.
1. 구성 파일의 텍스트를 복사 하 여 b a s e 64로 변환 합니다.
1. 인코딩된 구성 파일을 `proxy_config` 모듈 쌍의 원하는 속성 값으로 붙여넣습니다.

   ![인코딩된 구성 파일을 proxy_config 속성 값으로 붙여넣기](./media/how-to-configure-api-proxy-module/change-config.png)

## <a name="next-steps"></a>다음 단계

API 프록시 모듈을 사용 하 여 [다운스트림 IoT Edge 장치를 Azure IoT Edge 게이트웨이에 연결](how-to-connect-downstream-iot-edge-device.md)합니다.