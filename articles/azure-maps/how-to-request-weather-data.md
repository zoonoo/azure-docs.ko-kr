---
title: Azure Maps 날씨 서비스를 사용 하 여 실시간 및 예측 날씨 데이터 요청
description: Microsoft Azure Maps 날씨 서비스를 사용 하 여 실시간 (현재) 및 예상 (분, 시간별, 일별) 날씨 데이터를 요청 하는 방법을 알아봅니다.
author: anastasia-ms
ms.author: v-stharr
ms.date: 10/27/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: b5db19a6549e7e4675213973554ff18bf46dda1e
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/29/2020
ms.locfileid: "92915425"
---
# <a name="request-real-time-and-forecasted-weather-data-using-azure-maps-weather-services"></a>Azure Maps 날씨 서비스를 사용 하 여 실시간 및 예측 날씨 데이터 요청

Azure Maps [날씨 서비스](https://docs.microsoft.com/rest/api/maps/weather) 는 개발자가 매우 동적인 기록, 실시간 및 예측 날씨 데이터와 시각화를 솔루션에 통합할 수 있도록 하는 RESTful api 집합입니다. 이 문서에서는 실시간 및 예측 날씨 데이터를 요청 하는 방법을 보여 줍니다.

이 문서에서는 다음 방법에 대해 알아봅니다.

* [현재 조건 가져오기 API](https://docs.microsoft.com/rest/api/maps/weather/getcurrentconditionspreview)를 사용 하 여 실시간 (현재) 날씨 데이터를 요청 합니다.
* [심각한 날씨 경고 가져오기 API](https://docs.microsoft.com/rest/api/maps/weather/getsevereweatheralertspreview)를 사용 하 여 심각한 날씨 경고를 요청 합니다.
* [매일 예측 예측 API](https://docs.microsoft.com/rest/api/maps/weather/getdailyforecastpreview)를 사용 하 여 매일 예측을 요청 합니다.
* [시간별 예측 API](https://docs.microsoft.com/rest/api/maps/weather/gethourlyforecastpreview)를 사용 하 여 시간별 예측을 요청 합니다.
* [Get Minute 예측 API](https://docs.microsoft.com/rest/api/maps/weather/getminuteforecastpreview)를 사용 하 여 분 단위로 예측을 요청 합니다.

## <a name="prerequisites"></a>사전 요구 사항

1. [Azure Maps 계정을 만듭니다](quick-demo-map-app.md#create-an-azure-maps-account).
2. 기본 키 또는 구독 키라고도 하는 [기본 구독 키를 가져옵니다](quick-demo-map-app.md#get-the-primary-key-for-your-account). Azure Maps의 인증에 대한 자세한 내용은 [Azure Maps의 인증 관리](./how-to-manage-authentication.md)를 참조하세요.

    >[!IMPORTANT]
    >[Get Minute 예측 API](https://docs.microsoft.com/rest/api/maps/weather/getminuteforecastpreview) 에는 S1 가격 책정 계층 키가 필요 합니다. 다른 모든 Api에는 S0 가격 책정 계층 키가 필요 합니다.

이 자습서에서는 [Postman](https://www.postman.com/) 애플리케이션을 사용하지만 다른 API 개발 환경을 선택할 수도 있습니다.

## <a name="request-real-time-weather-data"></a>실시간 날씨 데이터 요청

[현재 조건 가져오기 API](https://docs.microsoft.com/rest/api/maps/weather/getcurrentconditionspreview) 는 지정 된 좌표 위치에 대 한 강, 온도, 바람 등의 자세한 날씨 조건을 반환 합니다. 또한 지난 6 시간 또는 특정 위치에 대 한 24 시간에서 관측값을 검색할 수 있습니다. 응답에는 날짜 및 시간 관찰, 날씨 조건, 날씨 아이콘, 강 표시기 플래그 및 온도에 대 한 간략 한 설명이 포함 됩니다. RealFeel™ 온도 및 ultraviolet (UV) 인덱스도 반환 됩니다.

이 예제에서는 [현재 조건 가져오기 API](https://docs.microsoft.com/rest/api/maps/weather/getcurrentconditionspreview) 를 사용 하 여 시애틀, WA에 위치한 좌표에서 현재 날씨 조건을 검색 합니다.

1. Postman 앱을 엽니다. Postman 앱의 위쪽 근처에서 **새로 만들기** 를 선택합니다. **새로 만들기** 창에서 **컬렉션** 을 선택합니다.  컬렉션 이름을 지정하고, **만들기** 단추를 선택합니다. 이 문서의 나머지 예제에는이 컬렉션을 사용 합니다.

2. 요청을 만들려면 **새로 만들기** 를 다시 선택합니다. **새로 만들기** 창에서 **요청** 을 선택합니다. 요청에 대한 **요청 이름** 을 입력합니다. 이전 단계에서 만든 컬렉션을 선택한 다음, **저장** 을 선택합니다.

3. 작성기 탭에서 **GET** HTTP 메서드를 선택 하 고 다음 URL을 입력 합니다. 이 요청 및 이 문서에 언급된 기타 요청에 대한 `{Azure-Maps-Primary-Subscription-key}`를 기본 구독 키로 바꿉니다.

    ```http
    https://atlas.microsoft.com/weather/currentConditions/json?api-version=1.0&query=47.60357,-122.32945&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

4. 파란색 **보내기** 단추를 클릭 합니다. 응답 본문에는 현재 날씨 정보가 포함 됩니다.

    ```json
    {
    "results": [
        {
            "dateTime": "2020-10-19T20:39:00+00:00",
            "phrase": "Cloudy",
            "iconCode": 7,
            "hasPrecipitation": false,
            "isDayTime": true,
            "temperature": {
                "value": 12.4,
                "unit": "C",
                "unitType": 17
            },
            "realFeelTemperature": {
                "value": 13.7,
                "unit": "C",
                "unitType": 17
            },
            "realFeelTemperatureShade": {
                "value": 13.7,
                "unit": "C",
                "unitType": 17
            },
            "relativeHumidity": 87,
            "dewPoint": {
                "value": 10.3,
                "unit": "C",
                "unitType": 17
            },
            "wind": {
                "direction": {
                    "degrees": 23.0,
                    "localizedDescription": "NNE"
                },
                "speed": {
                    "value": 4.5,
                    "unit": "km/h",
                    "unitType": 7
                }
            },
            "windGust": {
                "speed": {
                    "value": 9.0,
                    "unit": "km/h",
                    "unitType": 7
                }
            },
            "uvIndex": 1,
            "uvIndexPhrase": "Low",
            "visibility": {
                "value": 9.7,
                "unit": "km",
                "unitType": 6
            },
            "obstructionsToVisibility": "",
            "cloudCover": 100,
            "ceiling": {
                "value": 1494.0,
                "unit": "m",
                "unitType": 5
            },
            "pressure": {
                "value": 1021.2,
                "unit": "mb",
                "unitType": 14
            },
            "pressureTendency": {
                "localizedDescription": "Steady",
                "code": "S"
            },
            "past24HourTemperatureDeparture": {
                "value": -2.1,
                "unit": "C",
                "unitType": 17
            },
            "apparentTemperature": {
                "value": 15.0,
                "unit": "C",
                "unitType": 17
            },
            "windChillTemperature": {
                "value": 12.2,
                "unit": "C",
                "unitType": 17
            },
            "wetBulbTemperature": {
                "value": 11.3,
                "unit": "C",
                "unitType": 17
            },
            "precipitationSummary": {
                "pastHour": {
                    "value": 0.0,
                    "unit": "mm",
                    "unitType": 3
                },
                "past3Hours": {
                    "value": 0.0,
                    "unit": "mm",
                    "unitType": 3
                },
                "past6Hours": {
                    "value": 0.0,
                    "unit": "mm",
                    "unitType": 3
                },
                "past9Hours": {
                    "value": 0.0,
                    "unit": "mm",
                    "unitType": 3
                },
                "past12Hours": {
                    "value": 0.0,
                    "unit": "mm",
                    "unitType": 3
                },
                "past18Hours": {
                    "value": 0.0,
                    "unit": "mm",
                    "unitType": 3
                },
                "past24Hours": {
                    "value": 0.4,
                    "unit": "mm",
                    "unitType": 3
                }
            },
            "temperatureSummary": {
                "past6Hours": {
                    "minimum": {
                        "value": 12.2,
                        "unit": "C",
                        "unitType": 17
                    },
                    "maximum": {
                        "value": 14.0,
                        "unit": "C",
                        "unitType": 17
                    }
                },
                "past12Hours": {
                    "minimum": {
                        "value": 12.2,
                        "unit": "C",
                        "unitType": 17
                    },
                    "maximum": {
                        "value": 14.0,
                        "unit": "C",
                        "unitType": 17
                    }
                },
                "past24Hours": {
                    "minimum": {
                        "value": 12.2,
                        "unit": "C",
                        "unitType": 17
                    },
                    "maximum": {
                        "value": 15.6,
                        "unit": "C",
                        "unitType": 17
                    }
                }
            }
        }
    ]
    }
    ```

## <a name="request-severe-weather-alerts"></a>심각한 날씨 경고 요청

[심각한 날씨 경고를 가져오는 AZURE MAPS API](https://docs.microsoft.com/rest/api/maps/weather/getsevereweatheralertspreview) 는 공식 정부 Meteorological 기관에서 전 세계에서 사용할 수 있는 심각한 날씨 경고를 반환 하 고 전 세계에 전 세계 날씨 경고 공급자를 제공 합니다. 이 서비스는 경고 유형, 범주, 수준 및 요청 된 위치에 대 한 활성 경고에 대 한 자세한 설명 (예: 허리케인, thunderstorms, 번개, 열 웨이브 또는 포리스트 실행)과 같은 세부 정보를 반환할 수 있습니다. 예를 들어 물류 관리자는 회사 위치 및 계획 된 경로와 함께 지도의 심각한 날씨 조건을 시각화 하 고, 드라이버 및 로컬 작업자를 사용 하 여 더 세부적으로 조정할 수 있습니다.

이 예제에서는 샤이엔, WY에 있는 좌표에서 현재 날씨 조건을 검색 하기 위해 [심각한 날씨 경고 가져오기 API](https://docs.microsoft.com/rest/api/maps/weather/getsevereweatheralertspreview) 를 사용 합니다.

>[!NOTE]
>이 예제에서는이 문서를 작성할 때 심각한 날씨 경고를 검색 합니다. 요청 된 위치에 더 이상 심각한 날씨 경고가 없을 수 있습니다. 이 예를 실행할 때 실제 심각한 경고 데이터를 검색 하려면 다른 좌표 위치에 있는 데이터를 검색 해야 합니다.

1. Postman 앱을 열고 **새로 만들기** 를 클릭 한 다음 **요청** 을 선택 합니다. 요청에 대한 **요청 이름** 을 입력합니다. 이전 섹션에서 만든 컬렉션을 선택 하거나 새 컬렉션을 만든 다음 **저장** 을 선택 합니다.

2. 작성기 탭에서 **GET** HTTP 메서드를 선택 하 고 다음 URL을 입력 합니다. 이 요청 및 이 문서에 언급된 기타 요청에 대한 `{Azure-Maps-Primary-Subscription-key}`를 기본 구독 키로 바꿉니다.

    ```http
    https://atlas.microsoft.com/weather/severe/alerts/json?api-version=1.0&query=41.161079,-104.805450&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

3. 파란색 **보내기** 단추를 클릭 합니다. 심각한 날씨 경고가 없으면 응답 본문에 빈 배열이 포함 됩니다 `results[]` . 심각한 날씨 경고가 있는 경우 응답 본문에는 다음 JSON 응답과 같은 내용이 포함 됩니다.

    ```json
    {
    "results": [
        {
            "countryCode": "US",
            "alertId": 2194734,
            "description": {
                "localized": "Red Flag Warning",
                "english": "Red Flag Warning"
            },
            "category": "FIRE",
            "priority": 54,
            "source": "U.S. National Weather Service",
            "sourceId": 2,
            "alertAreas": [
                {
                    "name": "Platte/Goshen/Central and Eastern Laramie",
                    "summary": "Red Flag Warning in effect until 7:00 PM MDT.  Source: U.S. National Weather Service",
                    "startTime": "2020-10-05T15:00:00+00:00",
                    "endTime": "2020-10-06T01:00:00+00:00",
                    "latestStatus": {
                        "localized": "Continue",
                        "english": "Continue"
                    },
                    "alertDetails": "...RED FLAG WARNING REMAINS IN EFFECT FROM 9 AM THIS MORNING TO\n7 PM MDT THIS EVENING FOR STRONG GUSTY WINDS AND LOW HUMIDITY...\n\n* WHERE...Fire weather zones 303, 304, 305, 306, 307, 308, 309,\n  and 310 in southeast Wyoming. Fire weather zone 313 in Nebraska.\n\n* WIND...West to northwest 15 to 30 MPH with gusts around 40 MPH.\n\n* HUMIDITY...10 to 15 percent.\n\n* IMPACTS...Any fires that develop will likely spread rapidly.\n  Outdoor burning is not recommended.\n\nPRECAUTIONARY/PREPAREDNESS ACTIONS...\n\nA Red Flag Warning means that critical fire weather conditions\nare either occurring now...or will shortly. A combination of\nstrong winds...low relative humidity...and warm temperatures can\ncontribute to extreme fire behavior.\n\n&&",
                    "alertDetailsLanguageCode": "en"
                }
            ]
            },...
        ]
    }
    ```

## <a name="request-daily-weather-forecast-data"></a>일일 날씨 예측 데이터 요청

[매일 예측 예측 API](https://docs.microsoft.com/rest/api/maps/weather/getdailyforecastpreview) 는 온도 및 윈드와 같은 자세한 일일 날씨 예보를 반환 합니다. 요청은 지정 된 좌표 위치에 대해 1, 5, 10, 15, 25 또는 45 일을 반환할 일 수를 지정할 수 있습니다. 응답에는 온도, 바람, 강, 공기 품질 및 UV 인덱스와 같은 세부 정보가 포함 됩니다.  이 예에서는를 설정 하 여 5 일간 요청 `duration=5` 합니다.

>[!IMPORTANT]
>S0 가격 책정 계층에서는 다음 1, 5, 10, 15 일에 대 한 일일 예측을 요청할 수 있습니다. S1 가격 책정 계층에서 다음 25 일 및 45 일에 대해 매일 예측을 요청할 수도 있습니다.

이 예제에서는 [일별 예측 예측 API](https://docs.microsoft.com/rest/api/maps/weather/getdailyforecastpreview) 를 사용 하 여 시애틀, WA에 위치한 좌표에 대 한 5 일 일기 예보를 검색 합니다.

1. Postman 앱을 열고 **새로 만들기** 를 클릭 한 다음 **요청** 을 선택 합니다. 요청에 대한 **요청 이름** 을 입력합니다. 이전 섹션에서 만든 컬렉션을 선택 하거나 새 컬렉션을 만든 다음 **저장** 을 선택 합니다.

2. 작성기 탭에서 **GET** HTTP 메서드를 선택 하 고 다음 URL을 입력 합니다. 이 요청 및 이 문서에 언급된 기타 요청에 대한 `{Azure-Maps-Primary-Subscription-key}`를 기본 구독 키로 바꿉니다.

    ```http
    https://atlas.microsoft.com/weather/forecast/daily/json?api-version=1.0&query=47.60357,-122.32945&duration=5&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

3. 파란색 **보내기** 단추를 클릭 합니다. 응답 본문에는 5 일간의 날씨 예측 데이터가 포함 됩니다. 간단히 하기 위해 아래 JSON 응답은 첫째 날의 예측을 보여 줍니다.
    ```json
    {
    "summary": {
        "startDate": "2020-10-18T17:00:00+00:00",
        "endDate": "2020-10-19T23:00:00+00:00",
        "severity": 2,
        "phrase": "Snow, mixed with rain at times continuing through Monday evening and a storm total of 3-6 cm",
        "category": "snow/rain"
    },
    "forecasts": [
        {
            "date": "2020-10-19T04:00:00+00:00",
            "temperature": {
                "minimum": {
                    "value": -1.1,
                    "unit": "C",
                    "unitType": 17
                },
                "maximum": {
                    "value": 1.3,
                    "unit": "C",
                    "unitType": 17
                }
            },
            "realFeelTemperature": {
                "minimum": {
                    "value": -6.0,
                    "unit": "C",
                    "unitType": 17
                },
                "maximum": {
                    "value": 0.5,
                    "unit": "C",
                    "unitType": 17
                }
            },
            "realFeelTemperatureShade": {
                "minimum": {
                    "value": -6.0,
                    "unit": "C",
                    "unitType": 17
                },
                "maximum": {
                    "value": 0.7,
                    "unit": "C",
                    "unitType": 17
                }
            },
            "hoursOfSun": 1.8,
            "degreeDaySummary": {
                "heating": {
                    "value": 18.0,
                    "unit": "C",
                    "unitType": 17
                },
                "cooling": {
                    "value": 0.0,
                    "unit": "C",
                    "unitType": 17
                }
            },
            "airAndPollen": [
                {
                    "name": "AirQuality",
                    "value": 23,
                    "category": "Good",
                    "categoryValue": 1,
                    "type": "Ozone"
                },
                {
                    "name": "Grass",
                    "value": 0,
                    "category": "Low",
                    "categoryValue": 1
                },
                {
                    "name": "Mold",
                    "value": 0,
                    "category": "Low",
                    "categoryValue": 1
                },
                {
                    "name": "Ragweed",
                    "value": 0,
                    "category": "Low",
                    "categoryValue": 1
                },
                {
                    "name": "Tree",
                    "value": 0,
                    "category": "Low",
                    "categoryValue": 1
                },
                {
                    "name": "UVIndex",
                    "value": 0,
                    "category": "Low",
                    "categoryValue": 1
                }
            ],
            "day": {
                "iconCode": 22,
                "iconPhrase": "Snow",
                "hasPrecipitation": true,
                "precipitationType": "Mixed",
                "precipitationIntensity": "Light",
                "shortPhrase": "Chilly with snow, 2-4 cm",
                "longPhrase": "Chilly with snow, accumulating an additional 2-4 cm",
                "precipitationProbability": 90,
                "thunderstormProbability": 0,
                "rainProbability": 54,
                "snowProbability": 85,
                "iceProbability": 8,
                "wind": {
                    "direction": {
                        "degrees": 36.0,
                        "localizedDescription": "NE"
                    },
                    "speed": {
                        "value": 9.3,
                        "unit": "km/h",
                        "unitType": 7
                    }
                },
                "windGust": {
                    "direction": {
                        "degrees": 70.0,
                        "localizedDescription": "ENE"
                    },
                    "speed": {
                        "value": 25.9,
                        "unit": "km/h",
                        "unitType": 7
                    }
                },
                "totalLiquid": {
                    "value": 4.3,
                    "unit": "mm",
                    "unitType": 3
                },
                "rain": {
                    "value": 0.5,
                    "unit": "mm",
                    "unitType": 3
                },
                "snow": {
                    "value": 2.72,
                    "unit": "cm",
                    "unitType": 4
                },
                "ice": {
                    "value": 0.0,
                    "unit": "mm",
                    "unitType": 3
                },
                "hoursOfPrecipitation": 9.0,
                "hoursOfRain": 1.0,
                "hoursOfSnow": 9.0,
                "hoursOfIce": 0.0,
                "cloudCover": 96
            },
            "night": {
                "iconCode": 29,
                "iconPhrase": "Rain and snow",
                "hasPrecipitation": true,
                "precipitationType": "Mixed",
                "precipitationIntensity": "Light",
                "shortPhrase": "Showers of rain and snow",
                "longPhrase": "A couple of showers of rain or snow this evening; otherwise, cloudy; storm total snowfall 1-3 cm",
                "precipitationProbability": 65,
                "thunderstormProbability": 0,
                "rainProbability": 60,
                "snowProbability": 54,
                "iceProbability": 4,
                "wind": {
                    "direction": {
                        "degrees": 16.0,
                        "localizedDescription": "NNE"
                    },
                    "speed": {
                        "value": 16.7,
                        "unit": "km/h",
                        "unitType": 7
                    }
                },
                "windGust": {
                    "direction": {
                        "degrees": 1.0,
                        "localizedDescription": "N"
                    },
                    "speed": {
                        "value": 35.2,
                        "unit": "km/h",
                        "unitType": 7
                    }
                },
                "totalLiquid": {
                    "value": 4.3,
                    "unit": "mm",
                    "unitType": 3
                },
                "rain": {
                    "value": 3.0,
                    "unit": "mm",
                    "unitType": 3
                },
                "snow": {
                    "value": 0.79,
                    "unit": "cm",
                    "unitType": 4
                },
                "ice": {
                    "value": 0.0,
                    "unit": "mm",
                    "unitType": 3
                },
                "hoursOfPrecipitation": 4.0,
                "hoursOfRain": 1.0,
                "hoursOfSnow": 3.0,
                "hoursOfIce": 0.0,
                "cloudCover": 94
            },
            "sources": [
                "AccuWeather"
            ]
        },...
    ]
    }
    ```

## <a name="request-hourly-weather-forecast-data"></a>시간별 날씨 예측 데이터 요청

[시간별 예측 API 가져오기](https://docs.microsoft.com/rest/api/maps/weather/gethourlyforecastpreview) 는 지정 된 좌표 위치에 대해 다음 1, 12, 24 (1 일), 72 (3 일), 120 (5 일) 및 240 시간 (10 일)에 대 한 시간을 기준으로 자세한 날씨 예측을 반환 합니다. API는 온도, 습도, 바람, 강 및 UV 인덱스와 같은 세부 정보를 반환 합니다.

>[!IMPORTANT]
>S0 가격 책정 계층에서는 다음 1, 12, 24 시간 (1 일) 및 72 시간 (3 일)에 대해 매시간 예측을 요청할 수 있습니다. S1 가격 책정 계층에서 다음 120 (5 일) 및 240 시간 (10 일)에 대해 매시간 예측을 요청할 수도 있습니다.

이 예제에서는 [매시간 예측 API](https://docs.microsoft.com/rest/api/maps/weather/gethourlyforecastpreview) 를 사용 하 여 시애틀, WA에 위치한 좌표에서 다음 12 시간에 대 한 시간별 일기 예보를 검색 합니다.

1. Postman 앱을 열고 **새로 만들기** 를 클릭 한 다음 **요청** 을 선택 합니다. 요청에 대한 **요청 이름** 을 입력합니다. 이전 섹션에서 만든 컬렉션을 선택 하거나 새 컬렉션을 만든 다음 **저장** 을 선택 합니다.

2. 작성기 탭에서 **GET** HTTP 메서드를 선택 하 고 다음 URL을 입력 합니다. 이 요청 및 이 문서에 언급된 기타 요청에 대한 `{Azure-Maps-Primary-Subscription-key}`를 기본 구독 키로 바꿉니다.

    ```http
    https://atlas.microsoft.com/weather/forecast/hourly/json?api-version=1.0&query=47.60357,-122.32945&duration=12&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

3. 파란색 **보내기** 단추를 클릭 합니다. 응답 본문에는 다음 12 시간 동안 일기 예보 데이터가 포함 됩니다. 간단히 하기 위해 아래 JSON 응답은 첫 번째 시간에 대 한 예측을 보여 줍니다.

    ```json
    {
    "forecasts": [
        {
            "date": "2020-10-19T21:00:00+00:00",
            "iconCode": 12,
            "iconPhrase": "Showers",
            "hasPrecipitation": true,
            "precipitationType": "Rain",
            "precipitationIntensity": "Light",
            "isDaylight": true,
            "temperature": {
                "value": 14.7,
                "unit": "C",
                "unitType": 17
            },
            "realFeelTemperature": {
                "value": 13.3,
                "unit": "C",
                "unitType": 17
            },
            "wetBulbTemperature": {
                "value": 12.0,
                "unit": "C",
                "unitType": 17
            },
            "dewPoint": {
                "value": 9.5,
                "unit": "C",
                "unitType": 17
            },
            "wind": {
                "direction": {
                    "degrees": 242.0,
                    "localizedDescription": "WSW"
                },
                "speed": {
                    "value": 9.3,
                    "unit": "km/h",
                    "unitType": 7
                }
            },
            "windGust": {
                "speed": {
                    "value": 14.8,
                    "unit": "km/h",
                    "unitType": 7
                }
            },
            "relativeHumidity": 71,
            "visibility": {
                "value": 9.7,
                "unit": "km",
                "unitType": 6
            },
            "cloudCover": 100,
            "ceiling": {
                "value": 1128.0,
                "unit": "m",
                "unitType": 5
            },
            "uvIndex": 1,
            "uvIndexPhrase": "Low",
            "precipitationProbability": 51,
            "rainProbability": 51,
            "snowProbability": 0,
            "iceProbability": 0,
            "totalLiquid": {
                "value": 0.3,
                "unit": "mm",
                "unitType": 3
            },
            "rain": {
                "value": 0.3,
                "unit": "mm",
                "unitType": 3
            },
            "snow": {
                "value": 0.0,
                "unit": "cm",
                "unitType": 4
            },
            "ice": {
                "value": 0.0,
                "unit": "mm",
                "unitType": 3
            }
        }...
    ]
    }
    ```
## <a name="request-minute-by-minute-weather-forecast-data"></a>요청 분 분 날씨 예측 데이터

 [Get 분 예측 API](https://docs.microsoft.com/rest/api/maps/weather/getminuteforecastpreview) 는 다음 120 분 동안 지정 된 위치에 대해 분 단위로 예측을 반환 합니다. 사용자는 1, 5 분, 15 분 간격으로 날씨 예측을 요청할 수 있습니다. 응답에는 강 형식 (예: 비, 눈 또는 양쪽의 혼합 포함), 시작 시간 및 강 강도 값 (dBZ) 등의 세부 정보가 포함 됩니다.

이 예제에서는 [Get Minute 예측 API](https://docs.microsoft.com/rest/api/maps/weather/getminuteforecastpreview) 를 사용 하 여 시애틀, WA에 있는 좌표에서 분 단위 일기 예보를 검색 합니다. 날씨 예측은 다음 120 분 동안 제공 됩니다. 쿼리는 15 분 간격으로 예측이 제공 되도록 요청 하지만 매개 변수를 1 또는 5 분으로 조정할 수 있습니다.

1. Postman 앱을 열고 **새로 만들기** 를 클릭 한 다음 **요청** 을 선택 합니다. 요청에 대한 **요청 이름** 을 입력합니다. 이전 섹션에서 만든 컬렉션을 선택 하거나 새 컬렉션을 만든 다음 **저장** 을 선택 합니다.

2. 작성기 탭에서 **GET** HTTP 메서드를 선택 하 고 다음 URL을 입력 합니다. 이 요청 및 이 문서에 언급된 기타 요청에 대한 `{Azure-Maps-Primary-Subscription-key}`를 기본 구독 키로 바꿉니다.

    ```http
    https://atlas.microsoft.com/weather/forecast/minute/json?api-version=1.0&query=47.60357,-122.32945&interval=15&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

3. 파란색 **보내기** 단추를 클릭 합니다. 응답 본문에는 다음 120 분 동안의 날씨 예측 데이터가 15 분 간격으로 포함 됩니다.

    ```json
    {
    "summary": {
        "briefPhrase60": "No precipitation for at least 60 min",
        "shortPhrase": "No precip for 120 min",
        "briefPhrase": "No precipitation for at least 120 min",
        "longPhrase": "No precipitation for at least 120 min",
        "iconCode": 7
    },
    "intervalSummaries": [
        {
            "startMinute": 0,
            "endMinute": 119,
            "totalMinutes": 120,
            "shortPhrase": "No precip for %MINUTE_VALUE min",
            "briefPhrase": "No precipitation for at least %MINUTE_VALUE min",
            "longPhrase": "No precipitation for at least %MINUTE_VALUE min",
            "iconCode": 7
        }
    ],
    "intervals": [
        {
            "startTime": "2020-10-19T20:51:00+00:00",
            "minute": 0,
            "dbz": 0.0,
            "shortPhrase": "No Precipitation",
            "iconCode": 7,
            "cloudCover": 100
        },
        {
            "startTime": "2020-10-19T21:06:00+00:00",
            "minute": 15,
            "dbz": 0.0,
            "shortPhrase": "No Precipitation",
            "iconCode": 7,
            "cloudCover": 100
        },
        {
            "startTime": "2020-10-19T21:21:00+00:00",
            "minute": 30,
            "dbz": 0.0,
            "shortPhrase": "No Precipitation",
            "iconCode": 7,
            "cloudCover": 100
        },
        {
            "startTime": "2020-10-19T21:36:00+00:00",
            "minute": 45,
            "dbz": 0.0,
            "shortPhrase": "No Precipitation",
            "iconCode": 7,
            "cloudCover": 100
        },
        {
            "startTime": "2020-10-19T21:51:00+00:00",
            "minute": 60,
            "dbz": 0.0,
            "shortPhrase": "No Precipitation",
            "iconCode": 7,
            "cloudCover": 100
        },
        {
            "startTime": "2020-10-19T22:06:00+00:00",
            "minute": 75,
            "dbz": 0.0,
            "shortPhrase": "No Precipitation",
            "iconCode": 7,
            "cloudCover": 100
        },
        {
            "startTime": "2020-10-19T22:21:00+00:00",
            "minute": 90,
            "dbz": 0.0,
            "shortPhrase": "No Precipitation",
            "iconCode": 7,
            "cloudCover": 100
        },
        {
            "startTime": "2020-10-19T22:36:00+00:00",
            "minute": 105,
            "dbz": 0.0,
            "shortPhrase": "No Precipitation",
            "iconCode": 7,
            "cloudCover": 100
        }
        ]
    }
    ```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure Maps 날씨 서비스 개념](https://docs.microsoft.com/azure/azure-maps/weather-services-concepts)

> [!div class="nextstepaction"]
> [Azure Maps 날씨 서비스 REST API](https://docs.microsoft.com/rest/api/maps/weather
)
