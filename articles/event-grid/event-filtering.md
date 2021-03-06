---
title: Azure Event Grid에 대한 이벤트 필터링
description: Azure Event Grid 구독을 만들 때 이벤트를 필터링하는 방법을 설명합니다.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 837209d4197c271598155776b8d171a705e1f454
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86120095"
---
# <a name="understand-event-filtering-for-event-grid-subscriptions"></a>Event Grid 구독에 대한 이벤트 필터링 이해

이 문서에서는 엔드포인트에 전송되는 이벤트를 필터링하는 다양한 방법을 설명합니다. 이벤트 구독을 만들 때 필터링에 대한 세 가지 옵션이 있습니다.

* 일정 유형
* 다음 값으로 시작하거나 끝나는 제목
* 고급 필드 및 연산자

## <a name="event-type-filtering"></a>이벤트 유형 필터링

기본적으로 이벤트 원본에 대한 모든 [이벤트 유형](event-schema.md)은 엔드포인트로 전송됩니다. 특정 이벤트 유형만 엔드포인트에 보내도록 결정할 수 있습니다. 예를 들어 리소스에 대한 업데이트 알림을 받을 수 있지만 삭제와 같은 다른 작업에 대한 알림을 받을 수 없습니다. 이런 경우 `Microsoft.Resources.ResourceWriteSuccess` 이벤트 유형별로 필터링합니다. 이벤트 유형으로 배열을 제공하거나 이벤트 원본에 대한 모든 이벤트 유형을 가져오도록 `All`을 지정합니다.

이벤트 유형별로 필터링에 대한 JSON 구문은 다음과 같습니다.

```json
"filter": {
  "includedEventTypes": [
    "Microsoft.Resources.ResourceWriteFailure",
    "Microsoft.Resources.ResourceWriteSuccess"
  ]
}
```

## <a name="subject-filtering"></a>제목 필터링

제목별 간단한 필터링을 위해 제목에 대한 시작 또는 끝 값을 지정합니다. 예를 들어 `.txt`로 끝나는 제목을 지정하여 스토리지 계정에 텍스트 파일 업로드와 관련된 이벤트만을 가져올 수 있습니다. 또는 `/blobServices/default/containers/testcontainer`로 시작하는 제목을 필터링하여 스토리지 계정에 다른 컨테이너가 아닌 해당 컨테이너에 대한 모든 이벤트를 가져올 수 있습니다.

사용자 지정 항목에 이벤트를 게시할 때 구독자가 이벤트에 관심이 있는지 더 쉽게 알 수 있도록 사용자 이벤트에 대한 제목을 만듭니다. 구독자는 제목 속성을 사용하여 이벤트를 필터링 및 라우팅합니다. 구독자가 해당 경로의 세그먼트를 기준으로 필터링할 수 있도록 이벤트가 발생하는 경로를 추가하는 것을 고려합니다. 구독자는 경로를 통해 이벤트를 제한적이거나 광범위하게 필터링할 수 있습니다. 제목에 `/A/B/C`와 같은 3개의 세그먼트 경로를 제공하는 경우 구독자는 첫 번째 세그먼트 `/A`를 기준으로 필터링하여 광범위한 이벤트 집합을 가져올 수 있습니다. 구독자는 `/A/B/C` 또는 `/A/D/E`와 같은 제목이 있는 이벤트를 가져옵니다. 다른 구독자는 `/A/B`를 기준으로 필터링하여 제한된 이벤트 집합을 얻을 수 있습니다.

제목별로 필터링 하기 위한 JSON 구문은 다음과 같습니다.

```json
"filter": {
  "subjectBeginsWith": "/blobServices/default/containers/mycontainer/log",
  "subjectEndsWith": ".jpg"
}

```

## <a name="advanced-filtering"></a>고급 필터링

데이터 필드에서 값을 기준으로 필터링하고 비교 연산자를 지정하려면 고급 필터링 옵션을 사용합니다. 고급 필터링에서 다음을 지정합니다.

* 연산자 형식 - 비교의 형식입니다.
* 키 - 필터링에 사용하는 이벤트 데이터의 필드입니다. 숫자, 부울 또는 문자열일 수 있습니다.
* values-키와 비교할 값입니다.

여러 값이 있는 단일 필터를 지정 하는 경우 **또는** 작업이 수행 되므로 키 필드의 값은 다음 값 중 하나 여야 합니다. 다음은 예제입니다.

```json
"advancedFilters": [
    {
        "operatorType": "StringContains",
        "key": "Subject",
        "values": [
            "/providers/microsoft.devtestlab/",
            "/providers/Microsoft.Compute/virtualMachines/"
        ]
    }
]
```

여러 필터를 지정 하는 경우 **및** 작업이 수행 되므로 각 필터 조건이 충족 되어야 합니다. 다음은 예제입니다. 

```json
"advancedFilters": [
    {
        "operatorType": "StringContains",
        "key": "Subject",
        "values": [
            "/providers/microsoft.devtestlab/"
        ]
    },
    {
        "operatorType": "StringContains",
        "key": "Subject",
        "values": [
            "/providers/Microsoft.Compute/virtualMachines/"
        ]
    }
]
```

### <a name="operators"></a>연산자

**숫자** 에 사용할 수 있는 연산자는 다음과 같습니다.

* NumberGreaterThan
* NumberGreaterThanOrEquals
* NumberLessThan
* NumberLessThanOrEquals
* NumberIn
* NumberNotIn

**부울** 에 사용할 수 있는 연산자는 다음과 같습니다. 
- BoolEquals

**문자열** 에 사용할 수 있는 연산자는 다음과 같습니다.

* StringContains
* StringBeginsWith
* StringEndsWith
* StringIn
* StringNotIn

모든 문자열 비교는 대/소문자를 구분 **하지 않습니다** .

### <a name="key"></a>키

Event Grid 스키마의 이벤트의 경우 키에 대해 다음 값을 사용합니다.

* ID
* 항목
* 제목
* EventType
* DataVersion
* 이벤트 데이터(예: Data.key1)

클라우드 이벤트 스키마의 이벤트의 경우 키에 대해 다음 값을 사용합니다.

* EventId
* 원본
* EventType
* EventTypeVersion
* 이벤트 데이터(예: Data.key1)

사용자 지정 입력 스키마의 경우 이벤트 데이터 필드(예: Data.key1)를 사용합니다.

### <a name="values"></a>값

값은 다음이 될 수 있습니다.

* number
* 문자열
* boolean
* array

### <a name="limitations"></a>제한 사항

고급 필터링에는 다음과 같은 제한이 있습니다.

* 5 이벤트 그리드 구독 당 모든 필터의 고급 필터 및 25 필터 값
* 문자열 값당 512자
* **in** 및 **not in** 연산자에 대한 5개의 값
* 문자에 ** `.` (점)** 이 있는 키입니다. 예를 들어 `http://schemas.microsoft.com/claims/authnclassreference` 또는 `john.doe@contoso.com`입니다. 현재는 키에 이스케이프 문자를 사용할 수 없습니다. 

둘 이상의 필터에 동일한 키를 사용할 수 있습니다.

### <a name="examples"></a>예제

### <a name="stringcontains"></a>StringContains

```json
"advancedFilters": [{
    "operatorType": "StringContains",
    "key": "data.key1",
    "values": [
        "microsoft", 
        "azure"
    ]
}]
```

### <a name="stringbeginswith"></a>StringBeginsWith

```json
"advancedFilters": [{
    "operatorType": "StringBeginsWith",
    "key": "data.key1",
    "values": [
        "event", 
        "grid"
    ]
}]
```

### <a name="stringendswith"></a>StringEndsWith

```json
"advancedFilters": [{
    "operatorType": "StringEndsWith",
    "key": "data.key1",
    "values": [
        "jpg", 
        "jpeg", 
        "png"
    ]
}]
```

### <a name="stringin"></a>StringIn

```json
"advancedFilters": [{
    "operatorType": "StringIn",
    "key": "data.key1",
    "values": [
        "exact", 
        "string", 
        "matches"
    ]
}]
```

### <a name="stringnotin"></a>StringNotIn

```json
"advancedFilters": [{
    "operatorType": "StringNotIn",
    "key": "data.key1",
    "values": [
        "aws", 
        "bridge"
    ]
}]
```

### <a name="numberin"></a>NumberIn

```json

"advancedFilters": [{
    "operatorType": "NumberIn",
    "key": "data.counter",
    "values": [
        5,
        1
    ]
}]

```

### <a name="numbernotin"></a>NumberNotIn

```json
"advancedFilters": [{
    "operatorType": "NumberNotIn",
    "key": "data.counter",
    "values": [
        41,
        0,
        0
    ]
}]
```

### <a name="numberlessthan"></a>NumberLessThan

```json
"advancedFilters": [{
    "operatorType": "NumberLessThan",
    "key": "data.counter",
    "value": 100
}]
```

### <a name="numbergreaterthan"></a>NumberGreaterThan

```json
"advancedFilters": [{
    "operatorType": "NumberGreaterThan",
    "key": "data.counter",
    "value": 20
}]
```

### <a name="numberlessthanorequals"></a>NumberLessThanOrEquals

```json
"advancedFilters": [{
    "operatorType": "NumberLessThanOrEquals",
    "key": "data.counter",
    "value": 100
}]
```

### <a name="numbergreaterthanorequals"></a>NumberGreaterThanOrEquals

```json
"advancedFilters": [{
    "operatorType": "NumberGreaterThanOrEquals",
    "key": "data.counter",
    "value": 30
}]
```

### <a name="boolequals"></a>BoolEquals

```json
"advancedFilters": [{
    "operatorType": "BoolEquals",
    "key": "data.isEnabled",
    "value": true
}]
```


## <a name="next-steps"></a>다음 단계

* PowerShell 및 Azure CLI를 사용하여 이벤트 필터링에 대해 알아보려면 [Event Grid에 대한 이벤트 필터링](how-to-filter-events.md)을 참조하세요.
* Event Grid를 빠르게 시작하려면 [Azure Event Grid를 사용하여 사용자 지정 이벤트 만들기 및 라우팅](custom-event-quickstart.md)을 참조하세요.
