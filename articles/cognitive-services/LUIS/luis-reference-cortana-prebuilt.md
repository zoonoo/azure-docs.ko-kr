---
title: Cortana 미리 작성된 앱 참조 | Microsoft Docs
description: LUIS(Language Understanding Intelligent Service)에서 미리 작성된 응용 프로그램인 Cortana 개인 비서에 대한 참조입니다.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 12/13/2017
ms.author: v-geberr
ms.openlocfilehash: cd808c30a6781fc0252992c13ba247486e35ad44
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35373462"
---
# <a name="cortana-prebuilt-app-reference"></a>Cortana 미리 작성된 앱 참조
이 참조에서는 Cortana 미리 작성된 앱에서 인식하는 의도와 엔터티를 나열합니다.

## <a name="cortana-prebuilt-intents"></a>Cortana 미리 작성된 의도
미리 작성된 개인 비서 응용 프로그램에서 식별할 수 있는 의도는 다음과 같습니다.

| 의도 | 발화 예제 |
|--------| ------------------|
| builtin.intent.alarm.alarm_other|7시 30분인 내 알람을 8시로 업데이트합니다. <br/>내 알람을 오전 8시에서 오전 9시로 변경합니다. |
| builtin.intent.alarm.delete_alarm|알람을 삭제합니다. <br/>내 "깨우기" 알람을 삭제합니다.|
| builtin.intent.alarm.find_alarm|내 깨우기 알람 시간은 몇 시로 설정되어 있나요? <br/> 내 깨우기 알람이 작동되고 있나요? |
| builtin.intent.alarm.set_alarm|내 깨우기 알람을 켜세요.<br/>항생제를 복용하기 위해 12시에 호출되는 알람을 설정할 수 있나요?|
| builtin.intent.alarm.snooze|5분 동안 알람을 다시 알립니다.<br/>알람을 다시 알립니다.|
| builtin.intent.alarm.time_remaining| "깨우기"까지 얼마나 남아 있나요? <br/> 다음 알람까지 남아 있는 시간은?|
| builtin.intent.alarm.turn_off_alarm | 내 오전 7시 알람을 끕니다. <br/> 내 깨우기 알람을 끄세요. |
| builtin.intent.calendar.change_calendar_entry| 약속을 변경합니다.<br/>내 회의를 오늘에서 내일로 연기합니다.|
|builtin.intent.calendar.check_availability|금요일에는 바쁜가요?<br/>Brian은 토요일에 한가한가요?|
|builtin.intent.calendar.connect_to_meeting|회의에 연결합니다.<br/>온라인 회의에 참가합니다.|
|builtin.intent.calendar.create_calendar_entry|나는 금요일에 Tony와의 약속을 예약해야 합니다.<br/>일요일 오후 2시에 Lisa와 약속을 합니다.|
|builtin.intent.calendar.delete_calendar_entry|내 약속을 삭제합니다.<br/>내 달력에서 내일 오후 3시로 잡힌 회의를 제거합니다.|
|builtin.intent.calendar.find_calendar_entry|내 달력을 표시합니다.<br/>주간 달력을 표시합니다.|
|builtin.intent.calendar.find_calendar_when|Jon과의 다음 회의는 언제인가요?<br/>월요일에 Larry와 약속한 시간은 언제인가요?|
|builtin.intent.calendar.find_calendar_where|내 6시 회의의 장소를 보여 주세요.<br/>Jon과의 회의 장소는 어디인가요?|
|builtin.intent.calendar.find_calendar_who|이 회의는 누구와 하는가요?<br/>초대받은 다른 사람은 누구인가요?|
|builtin.intent.calendar.find_calendar_why|11시에 열린 내 회의의 내용은 무엇인가요?<br/>Jon과의 회의에서 다룰 내용은 무엇인가요?|
|builtin.intent.calendar.find_duration|다음 회의는 얼마나 걸릴까요?<br/>오늘 오후 내 회의는 몇 분이나 걸릴까요?|
|builtin.intent.calendar.time_remaining|다음 약속까지 남아 있는 시간은?<br/>회의 전까지 얼마나 더 있어야 할까요?|
|builtin.intent.communication.add_contact|이 번호를 저장하고 이름을 Jose로 지정합니다.<br/>내 연락처 목록에 Jason을 넣습니다.|
|builtin.intent.communication.answer_phone|대답<br/>통화 받기|
|builtin.intent.communication.assign_nickname|Nickolas의 애칭을 편집합니다.<br/>John Doe의 애칭을 추가합니다.|
|builtin.intent.communication.call_voice_mail|음성 메일<br/>음성 메일을 호출합니다.|
|builtin.intent.communication.find_contact|내 연락처를 보여 주세요.<br/>연락처를 찾습니다.|
|builtin.intent.communication.forwarding_off|내 착신 전환을 중지합니다.<br/>착신 전환을 해제합니다.|
|builtin.intent.communication.forwarding_on|집 전화로 전화를 걸도록 착신 전환을 설정합니다.<br/>집 전화로 착신 전환합니다.|
|builtin.intent.communication.ignore_incoming|해당 전화에 응답하지 않습니다<br/>바쁘기 때문에 지금은 안돼!|
|builtin.intent.communication.ignore_with_message|해당 전화에 응답하지 않는 대신 메시지를 보냅니다.<br/>무시하고 문자 메시지를 다시 보냅니다.|
|builtin.intent.communication.make_call|Bob과 John에게 전화하세요.<br/>엄마와 아빠한테 전화해!|
|builtin.intent.communication.press_key|별표를 누릅니다.<br/>1 2 3을 누릅니다.|
|builtin.intent.communication.read_aloud|문자 메시지를 읽습니다.<br/>메시지에서 그녀가 뭐라고 말했나요?|
|builtin.intent.communication.redial|마지막 통화의 전화를 다시 겁니다.<br/>전화를 다시 겁니다.|
|builtin.intent.communication.send_email|Mike Waters에게 "지난 주에 함께 했던 저녁 식사가 아주 멋졌어요"라는 이메일을 보냅니다.<br/>Bob에게 이메일을 보냅니다.|
|builtin.intent.communication.send_text|Bob과 John에게 문자 메시지를 보냅니다.<br/>Message|
|builtin.intent.communication.speakerphone_off|스피커를 꺼주세요.<br/>스피커 폰을 해제합니다.|
|builtin.intent.communication.speakerphone_on|스피커 폰 모드입니다.<br/>스피커 폰 모드로 설정합니다.|
|builtin.intent.mystuff.find_attachment|John이 어제 이메일로 보낸 문서를 찾습니다. <br/>John의 문서를 찾습니다.|
|builtin.intent.mystuff.find_my_stuff|어제부터 쇼핑 목록을 수정하고 싶었습니다.<br/>9월부터 내 화학 노트를 찾고 있습니다.
|builtin.intent.mystuff.search_messages|메시지를 여세요. <br/>메시지의 최대 전달 수|
|builtin.intent.mystuff.transform_my_stuff|내 쇼핑 목록을 남편과 공유합니다.<br/>내 쇼핑 목록을 삭제합니다.|
|builtin.intent.ondevice.open_setting|Cortana 설정을 엽니다. <br/>알림으로 이동합니다.|
|builtin.intent.ondevice.pause|음악을 끕니다.<br/>음악 끄기|
|builtin.intent.ondevice.play_music|나는 고독한 마음의 소유자를 듣고 싶어요.<br/>몇 가지 가스펠 음악을 재생합니다.|
|builtin.intent.ondevice.recognize_song|어떤 노래인지 말해줘.<br/>노래의 제목을 분석하고 검색합니다.|
|builtin.intent.ondevice.repeat|이 트랙을 반복합니다.<br/>이 노래를 다시 재생합니다.|
|builtin.intent.ondevice.resume|음악을 재시작합니다.<br/>음악을 다시 시작합니다.|
|builtin.intent.ondevice.skip_back|트랙을 백업합니다.<br/>이전 노래|
|builtin.intent.ondevice.skip_forward|다음 노래<br/>트랙 앞으로 건너뛰기|
|builtin.intent.ondevice.turn_off_setting|WiFi 해제<br/>비행기 모드는 사용하지 않습니다.|
|builtin.intent.ondevice.turn_on_setting|WiFi 설정<br/>Bluetooth를 켭니다.|
|builtin.intent.places.add_favorite_place|이 주소를 내 즐겨찾기에 추가합니다.<br/>이 위치를 내 즐겨찾기에 저장합니다.|
|builtin.intent.places.book_public_transportation|기차표를 구입합니다.<br/>트램 승차권을 예약합니다|
|builtin.intent.places.book_taxi|이 시간에 태워 줄 수 있나요?<br/>택시를 찾습니다.|
|builtin.intent.places.check_area_traffic|520번 도로에서 교통은 어떤가요?<br/>i-5 도로의 교통은 어떤가요?|
|builtin.intent.places.check_into_place|Joya에 체크 인합니다.<br/>여기서 체크 인하세요.|
|builtin.intent.places.check_route_traffic|Kirkland로 가는 길의 교통을 보여 주세요.<br/>시애틀로 가는 교통은 어떤가요?|
|builtin.intent.places.find_place|내가 어디에서 살고 있나요? <br/>내게 상위 3개의 일식 레스토랑을 알려주세요!|
|builtin.intent.places.get_address|Robson 가에 있는 Guu 주소를 보여 주세요.<br/>가장 가까운 Starbucks 커피숍의 주소는 어떻게 되나요?|
|builtin.intent.places.get_coupon|내 지역의 TV 거래를 찾습니다.<br/>Mountain View에서 할인 혜택을 제공하고 있습니다.|
|builtin.intent.places.get_distance|Holiday Inn 호텔은 얼마나 멀리 떨어져 있나요?<br/>Tahoe 호수까지의 거리는 어떻게 되나요?|
|builtin.intent.places.get_hours|Del Corso 바의 월요일 영업 시간은 어떻게 되나요?<br/>도서관은 언제 열리나요?|
|builtin.intent.places.get_menu|Applebee의 메뉴를 보여 주세요.<br/>Sizzler의 메뉴에 포함된 음식은 무엇인가요?|
|builtin.intent.places.get_phone_number|Home Depot 회사의 번호를 주세요.<br/>가장 가까운 Starbucks의 전화 번호는 어떻게 되나요?|
|builtin.intent.places.get_price_range|Red Lobster 해변 식당에서의 저녁 식사 비용은 얼마인가요?<br/>Purple 카페는 얼마나 비싼가요?|
|builtin.intent.places.get_reviews|현지 하드웨어 상점에 대한 평가 보고서를 보여 주세요.<br/>레스토랑 평가 보고서를 보고 싶습니다.|
|builtin.intent.places.get_route|내게 방향을 알려주세요.|도보로 Pizza Hut에 갈 수 있습니다.|
|builtin.intent.places.get_star_rating|French Laundry 레스토랑의 별 등급은 무엇인가요?<br/>몬테레이의 해양 수족관이 좋은가요?|
|builtin.intent.places.get_transportation_schedule|샌프란시스코 페리는 몇 시에 떠나요?<br/>시애틀로 가는 마지막 열차는 언제인가요?|
|builtin.intent.places.get_travel_time|샌프란시스코에서 덴버까지 주행 시간은 어떻게 되나요?<br/>여기서 샌프란시스코까지 가려면 얼마나 걸리나요?|
|builtin.intent.places.make_call|벨뷰에 있는 Smith 박사에게 전화를 겁니다.<br/>1번 가에 있는 Home Depot에 전화를 겁니다.|
|builtin.intent.places.rate_place|이 레스토랑에 대한 등급을 제공합니다.<br/>Yelp에서 Il Fornaio 레스토랑을 5성 등급으로 평가하고 있습니다.|
|builtin.intent.places.show_map|현재 내 위치는 어디인가요? <br/>내 위치는 어디인가요?|
|builtin.intent.places.takes_reservations|Olive Garden에서 예약을 할 수 있나요?<br/>아트 갤러리에서 예약을 받나요?|
|builtin.intent.reminder.change_reminder|미리 알림을 변경합니다.<br/>내 그림 날짜 미리 알림을 30분 앞당깁니다.|
|builtin.intent.reminder.create_single_reminder|오전 7시에 일어나도록 미리 알립니다.<br/>Luca와 함께 오후 4시 40분에 할로윈 사탕을 받으러 다니도록 미리 알립니다.|
|builtin.intent.reminder.delete_reminder|이 미리 알림을 삭제합니다.<br/>내 그림 미리 알림을 삭제합니다.|
|builtin.intent.reminder.find_reminder|오늘로 설정된 미리 알림이 있나요?<br/>Luca의 파티에 대해 설정된 미리 알림이 있나요?|
|builtin.intent.reminder.read_aloud|미리 알림을 큰 소리로 읽습니다.<br/>해당 미리 알림을 읽습니다.|
|builtin.intent.reminder.snooze|내일까지 해당 미리 알림을 다시 알립니다.<br/>이 미리 알림을 일시 중지합니다.|
|builtin.intent.reminder.turn_off_reminder|미리 알림을 해제합니다.<br/>공항 픽업 미리 알림을 해제합니다.|
|builtin.intent.weather.change_temperature_unit|화씨 온도에서 절대 온도로 변경합니다.<br/>화씨 온도에서 섭씨 온도로 변경합니다.|
|builtin.intent.weather.check_weather|곧 다가올 시애틀 여행에 대한 일기 예보를 보여 주세요.<br/>이번 주말의 날씨는 어떨까요?|
|builtin.intent.weather.check_weather_facts|12월의 하와이 날씨는 어떤가요?<br/>작년 이맘때의 온도는 어땠나요?|
|builtin.intent.weather.compare_weather|텍사스 주의 댈러스와 휴스턴의 최고 및 최저 온도를 비교해 주세요.<br/>솔트레이크시티와 뉴욕의 날씨는 어떻게 비교되나요?|
|builtin.intent.weather.get_frequent_locations|가장 자주 가는 곳을 알려주세요.<br/>가장 자주 멈추는 곳을 보여 주세요.|
|builtin.intent.weather.get_weather_advisory|날씨 경고<br/>새크라멘토에 대한 기상 주의보를 보여 주세요.|
|builtin.intent.weather.get_weather_maps|기상도를 표시합니다.<br/>아프리카에 대한 기상도를 보여 주세요.|
|builtin.intent.weather.question_weather|내일 아침에는 안개가 낄까요?<br/>이번 주말에 눈을 치워야 하나요?|
|builtin.intent.weather.show_weather_progression|지역 기상 레이더를 표시합니다.<br/>레이더를 시작합니다.|
|builtin.intent.none|나이가 어떻게 되세요?<br/>카메라를 엽니다.|

## <a name="prebuilt-entities"></a>미리 작성된 엔터티 

미리 작성된 개인 비서 응용 프로그램에서 식별할 수 있는 엔터티의 몇 가지 예제는 다음과 같습니다.

|엔터티 |발화의 엔터티 예제 |
|-------|------------------|
|builtin.alarm.alarm_state | 내 깨우기 알람을 끕니다(`off`).    <br/> 내 깨우기 알람이 `on`인가요?  | 
|builtin.alarm.duration |`10 minutes` 동안 다시 알립니다.    <br/> 알람을 `5 minutes` 동안 다시 알립니다  |
|builtin.alarm.start_date | 알람을 `monday` 오전 7시로 설정합니다.   <br/> 알람을 `tomorrow` 정오로 설정합니다.   |
|builtin.alarm.start_time | `30 minutes`에 대한 알람을 만듭니다.    <br/> `in 20 minutes`으로 알람을 설정합니다.   |
|builtin.alarm.title | 내 `wake up` 알람이 설정되어 있나요? <br/> `take antibiotics`라는 알람을 월요일부터 금요일까지 12시 15분으로 설정할 수 있나요? |
|builtin.calendar.absolute_location | 내일 `123 main street`에서의 약속을 만듭니다.   <br/> 회의는 6월 5일 `cincinnati`에서 개최됩니다.    |
|builtin.calendar.contact_name|내 달력에 마케팅 회의를 배치하고 `joe`가 참석하는지 확인합니다. <br/>나는 Il Fornaio에서 점심 식사를 하도록 설정하고 `paul`을 초대하고 싶습니다. |   
|builtin.calendar.destination_calendar|내 `work` 일정에 이를 추가합니다.   <br/>내 `personal` 달력에 이를 배치합니다. |
|builtin.calendar.duration| 약속을 오늘 밤 6시부터 `an hour` 동안으로 설정합니다. <br/>  Joe와 `2 hour` 회의를 예약합니다. |  
|builtin.calendar.end_date | 내일부터 `next monday`까지 '휴가'라는 일정 항목을 만듭니다. <br/>    `monday, october 5th`까지 내 시간을 '다른 용무 중'으로 차단합니다. | 
|builtin.calendar.end_time | 회의는 `5:30 PM`에 끝납니다. <br/> 11에서 `noon`까지로 예약합니다.  |   
|builtin.calendar.implicit_location | dmv에서의 약속을 취소합니다. <br/> Miles의 생일 파티 위치를 `poppy restaurant`으로 변경합니다. |    
|builtin.calendar.move_earlier_time | 회의를 `an hour` 앞당깁니다. <br/> 치과 약속을 `30 minutes` 앞당깁니다.       |
|builtin.calendar.move_later_time | 내 치과 의사 약속을 옮기라. `30 minutes`    <br/> 회의를 `an hour` 앞당깁니다. |  
|builtin.calendar.original_start_date | 이발소 예약을 '화요일'에서 '수요일'로 변경합니다. <br/> Ken과의 회의를 `monday`에서 화요일로 변경합니다. |
|builtin.calendar.original_start_time | 내 회의를 `2:00`에서 3으로 변경합니다.  <br/> 내 치과 약속을 `3:30`에서 4로 변경합니다. |  
|builtin.calendar.start_date | `flag day`에서 내 파티는 몇 시에 시작하나요? <br/> 점심을 `friday after next` 정오로 예약합니다. 
|builtin.calendar.start_time | `this morning`으로 예약하고 싶습니다. <br/> `morning`에 예약하고 싶습니다. |  
|builtin.calendar.title | `vet appointment`  <br/> `dentist` 화요일 |
|builtin.communication.audio_device_type | `bluetooth`를 사용하여 전화를 겁니다.  <br/> 내 `headset`을 사용하여 전화를 겁니다. | 
|builtin.communication.contact_name | `bob jones` 문자 메시지  <br/> | `sarah` 전화|
|builtin.communication.destination_platform | `london`에서 Dave에게 전화를 겁니다. <br/> 그의 `work line`로 전화를 겁니다. |    
|builtin.communication.from_relationship_name | 내 `daughter`의 전화를 보여 주세요. <br/> `mom`의 이메일을 읽습니다.   |   
|builtin.communication.key | `star`를 누릅니다. <br/>  `hash` 키를 누릅니다.    |
|builtin.communication.message | Carly에게 `i'm running late`라는 이메일을 보냅니다. <br/> Angus Smith에게 `good luck on your exam`라는 문자 메시지를 보냅니다. |    
|builtin.communication.message_category | `follow up`로 표시된 새 이메일입니다.  <br/> `high priority`가 표시된 새 이메일입니다. |    
|builtin.communication.message_type | `email`을 보냅니다.   <br/> 내 `text` 메시지를 큰 소리로 읽습니다. |
|builtin.communication.phone_number | `1-800-328-9459`에 전화를 걸고 싶습니다. <br/>     `555-555-5555`에 전화를 겁니다. |   
|builtin.communication.relationship_name | `husband`에게 문자 메시지를 보냅니다. <br/>  `family`에게 이메일을 보냅니다.| 
|builtin.communication.slot_attribute | `recipient`를 변경합니다. <br/>    `text`를 변경합니다. | 
|builtin.comminication.source_platform | `skype`에서 그에게 전화를 겁니다. <br/> 내 `personal line`에서 그에게 전화를 겁니다. |
|builtin.mystuff.attachment| `attached` 문서가 있습니다. <br/> Bob이 보낸 이메일 `attachment`을 찾습니다. |
|builtin.mystuff.contact_name| Lisa가 `me`게 보낸 스프레드시트를 찾습니다. <br/> 어젯밤 `susan`에게 보낸 문서는 어디에 있나요? |
|builtin.mystuff.data_source | `c:\dev\` <br/> 내 `desktop`입니다. <br/> |`"resolution": { "resolution_type": "metadataItems", "metadataType": CanonicalEntity", "value": "desktop" }`|
|builtin.mystuff.data_type | 어젯밤에 작업한 `document`를 찾습니다. <br/> |`"resolution": {"resolution_type": "metadataItems", "metadataType": "CanonicalEntity", "value":Document" }` <br/> Mike의 `visio diagram`을 가져옵니다.  |
|builtin.mystuff.end_date| `between yesterday and today`에 작업한 문서를 보여 주세요.   <br/> `before thursday the 31st`에 작업한 문서를 찾습니다. |
|builtin.mystuff.end_time| `before noon`에 저장한 파일을 찾습니다. <br/> `before 4pm`에 작업한 문서를 찾습니다.|      
|builtin.mystuff.file_action| 어제 `saved`한 스프레드시트를 엽니다. <br/> Kevin이 `created` 스프레드시트를 찾습니다.| 
|builtin.mystuff.from_contact_name | `jason`이 내게 보낸 제안서를 찾습니다. <br/> `isaac`의 마지막 이메일을 엽니다. |
|builtin.mystuff.keyword | 내게 `french conjugation` 파일을 보여 주세요. <br/> 내가 어제 작성한 `marketing plan` 초안을 찾습니다. |
|builtin.mystuff.location | 내가 `work`에서 편집한 문서입니다. <br/> 내가 `paris`에서 찍은 사진입니다. |
|builtin.mystuff.message_category | 내 `new` 이메일을 찾습니다. <br/> 내 `high priority` 이메일을 검색합니다. |
|builtin.mystuff.message_type | 내 `email`을 확인합니다. <br/>  내 `text` 메시지를 보여 주세요.  |
|builtin.mystuff.source_platform | 내 `hotmail` 이메일에서 John의 이메일을 검색합니다.    <br/> 내가 `work`에서 보낸 문서를 찾습니다. |
|builtin.mystuff.start_date | 메모를 `january`부터 찾습니다. <br/> 내가 `after january 1st`에 Rob에게 보낸 이메일을 찾습니다. |
|builtin.mystuff.start_time | 오후 2시 전이지만 `after noon`에 때때로 Rob에게 보낸 이메일을 찾습니까? <br/> Kristin이 내게 보내고 `last night`에 내가 편집한 워크시트를 찾습니다. | 
|builtin.mystuff.title | `c:\dev\mystuff.txt` <br/> `*.txt` |
|builtin.mystuff.transform_action | John이 내게 보낸 파일을 `download`합니다. <br/> 내 주석 지침 문서를 `open`. |
|builtin.note.note_text | `pork chops, applesauce and milk`가 포함된 식료품 목록을 만듭니다. <br/> `buy milk`라는 메모를 만듭니다. |
|builtin.note.title | `grocery list`이라는 메모를 만듭니다. <br/> `people to call`이라는 메모를 만듭니다. |
|builtin.ondevice.music_artist_name | `rufus wainwright`가 부른 모든 노래를 재생합니다. <br/> `garth brooks` 음악을 재생합니다. |   
|builtin.ondevice.music_genre | `classic rock` 노래를 보여 주세요. <br/> 바로크 시대의 `classical` 음악을 재생합니다. |
|builtin.ondevice.music_playlist | `workout` 재생 목록에 있는 모든 Britney Spears의 노래를 무작위로 재생합니다. <br/> `breakup` 재생 목록을 재생합니다.
|builtin.ondevice.music_song_name | `summertime`을 재생합니다. <br/> `me and bobby mcgee`를 재생합니다. | 
|builtin.ondevice.setting_type | `quiet hours` <br/> `airplane mode` |
|builtin.places.absolute_location | 나를 `5th and pike` 교차로로 데려가 주세요. <br/> 아니요, `1 microsoft way redmond wa 98052` 방향을 원합니다. |
|builtin.places.atmosphere | `interesting` 외출 장소를 찾습니다.    <br/> `casual` 레스토랑은 어디서 찾을 수 있을까요? |  
|builtin.places.audio_device_type |우체국에 `hands free`로 전화를 겁니다. <br/> Papa John에게 `speakerphone`으로 전화를 겁니다. |    
|builtin.places.avoid_route | `toll road`를 피하세요. <br/> `construction on 101`을 피하면서 샌프란시스코로 데려가 주세요. |  
|builtin.places.cuisine | Mountain View 인근의 `halal` Deli입니다.   <br/> The Peninsula 호텔에 있는 `kosher` 고급 식당입니다. |
|builtin.places.date | `next friday the 12th`로 예약합니다. <br/>  `mondays`에는 Mashiko 공원이 열리나요? |
|builtin.places.discount_type | Macy’s 백화점의 `coupon`을 찾습니다. <br/> 내게 `coupon`을 찾아주세요. |
|builtin.places.distance | 여기서 `within 5 miles` 이내에 좋은 식당이 있나요? <br/> `within 15 miles` 이내에 있는 곳을 찾습니다. |   
|builtin.places.from_absolute_location | `45 elm street`에서 집으로 방향입니다. <br/> `san francisco`에서 팔로 알토로 가는 방향을 알려주세요.  |
|builtin.places.from_place_name | `post office`에서 56번 중심가로 운전하고 있습니다. <br/> `home depot`에서 Lowes로 가는 방향을 알려주세요. |
|builtin.places.from_place_type | `work`에서 시내로 가는 방향입니다. <br/>  `drug store`에서 집으로 가는 방향을 알려주세요. |
|builtin.places.meal_type | `dinner`를 위한 인근의 장소입니다. <br/> 비즈니스 `lunch`에 적합한 장소를 찾습니다. | 
|builtin.places.nearby | 내게서 `near` 멋진 가게를 보여 주세요.  <br/> 여기 `around`에 좋은 레바논 식당이 있나요? |
|builtin.places.open_status | 쇼핑몰은 언제 `closed`? <br/> 상점의 `opening` 시간을 알려주세요. | 
|builtin.places.place_name | `central park`으로 데려가 주세요. <br/> `eiffel tower`를 찾습니다. |   
|builtin.places.place_type | `atms` <br/> `post office` |
|builtin.places.prefer_route | `shortest` 경로의 방향을 표시합니다. <br/> `fastest` 경로를 이용합니다. | 
|builtin.places.price_range| `moderately affordable` 장소를 주세요. <br/>  나는 `expensive` 것을 원합니다.   |
|builtin.places.product | 이 근처 어디에서 `fresh fish`을 구할 수 있나요?  <br/> 이 근처 어디에서 `bare minerals`를 파는가요? |
|builtin.places.public_transportation_route | `m2` 버스의 버스 시간표 <br/> `3x` 버스 노선 |  
|builtin.places.rating | `3 star` 등급의 레스토랑을 표시합니다. <br/> `3 stars or higher`인 결과를 표시합니다.  |
|builtin.places.reservation_number | `seven`인용 테이블을 예약합니다. <br/>  Il Fornaio에서 `two` 사람을 예약합니다. |
|builtin.places.results_number | 여기서 가장 가까운 `10`개의 커피숍을 보여 주세요. <br/> 상위 `3`개의 수족관을 보여 주세요.  
|builtin.places.service_provided | 어디서 버스를 타고 `whale watch`에 갈 수 있을까요? <br/> `fix my brakes`할 수 있는 정비사가 필요합니다. |    
|builtin.places.time | 토요일 `8 am`에 열리는 곳을 원합니다.| Mashiko 공원이 `now` 열려 있나요? |
|builtin.places.transportation_company | `new jersey transit`에 대한 열차 시간표입니다. <br/> `bart`로 거기에 갈 수 있나요? | 
|builtin.places.transportation_type | `on foot` 갈 수 있는 음반 가게는 어디에 있습니까? <br/>  Mashiko 공원으로 가는 `biking` 자전거 길을 알려주세요.|
|builtin.places.travel_time | `less than 15 minutes`로 운전할 수 있으면 좋겠습니다. <br/>   `under 15 minutes`에 갈 수 있는 곳을 원합니다. |   
|builtin.reminder.absolute_location | `chicago`에 도착하면 아빠한테 전화하라고 미리 알려주세요. <br/> `seattle`로 돌아가면 가스를 구하라고 미리 알려주세요. |
|builtin.reminder.contact_name | `bob`이 전화를 하면 그에게 농담을 하도록 미리 알려주세요. <br/> `arthur`와 이야기할 때 통학 버스에 대해 언급하는 미리 알림을 만듭니다. |
|builtin.reminder.leaving_absolute_location | `1200 main`을 떠날 때 Craig를 데려 가라고 하는 미리 알림 |
|builtin.reminder.leaving_implicit_location | `work`를 떠날 때 가스를 구하라고 미리 알려주세요.  |
|builtin.reminder.original_start_date | 잔디밭에 대한 미리 알림을 `saturday`에서 일요일로 변경합니다. <br/> 학교에 대한 미리 알림을 `monday`에서 화요일로 변경합니다.   |
|builtin.reminder.relationship_name | 내 `husband`이 전화를 하면 그에게 학부모 모임에 대해 말하라고 미리 알려주세요. <br/> `mom`이 전화를 하면 내게 다시 미리 알려주세요.|
|builtin.reminder.reminder_text | Smith 박사가 전화를 하면 내게 `bring up my small spot of patchy skin`하도록 미리 알려줄 수 있나요?  <br/> 4시 40분에 `pick up dry cleaning`하도록 미리 알려주세요.   |
|builtin.reminder.start_date | `thursday after next` 오후 8시를 미리 알려주세요.  <br/> `next thursday the 18th` 오후 8시를 미리 알려주세요.    |
|builtin.reminder.start_time | `in 30 minutes`의 미리 알림을 만듭니다. <br/> `this evening at 7`에 화초에 물을 주도록 하는 미리 알림을 만듭니다. |  
|builtin.weather.absolute_location | `boston`에 비가 올까요? <br/> `seattle`에 대한 일기 예보는 어떤가요?  |
|builtin.weather.date_range | `this weekend` 뉴욕의 날씨 <br/>   플로리다 주 할리우드의 `five day` 일기 예보를 찾습니다. |
|builtin.weather.suitable_for | 이번 주말 반바지로 `hiking`을 할 수 있을까요?   <br/> 오늘 게임에 `walk` 갈 만큼 충분히 좋을까요? | 
|builtin.weather.temperature_unit | 오늘 `kelvin` 온도는 몇 도입니까?   <br/> `celsius` 온도를 보여 주세요. |  
|builtin.weather.time_range | `tonight` 눈이 올 것 같은가요? <br/> `now` 바람이 많이 부는가요?  |
|builtin.weather.weather_condition | `precipitation`을 표시합니다. <br/> 지금 Tahoe 호수의 `snow`이 얼마나 두꺼운가요?  |

