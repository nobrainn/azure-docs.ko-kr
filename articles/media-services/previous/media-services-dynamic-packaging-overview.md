---
title: Azure Media Services 동적 패키징 개요 | Microsoft Docs
description: 이 문서에서는 Microsoft Azure Media Services 동적 패키징을 간략하게 설명 합니다.
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/21/2019
ms.author: juliako
ms.openlocfilehash: 2c4654ce175e64c375c96164aed274bf8078d196
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87060129"
---
# <a name="dynamic-packaging"></a>동적 패키징

> [!div class="op_single_selector" title1="사용 중인 Media Services의 버전을 선택 합니다."]
> * [버전 3](../latest/dynamic-packaging-overview.md)
> * [버전 2](media-services-dynamic-packaging-overview.md)

> [!NOTE]
> Media Services v2에는 새로운 특징 또는 기능이 추가되지 않습니다. <br/>[Media Services v3](../latest/index.yml)의 최신 버전을 확인하세요. 또한 [v2에서 v3로의 마이그레이션 지침](../latest/migrate-from-v2-to-v3.md)을 참조하세요.

Microsoft Azure Media Services를 사용하여 많은 미디어 소스 파일 형식, 미디어 스트리밍 형식 및 콘텐츠 보호 형식을 다양한 클라이언트 기술(예: iOS, XBOX, Silverlight, Windows 8)에 제공할 수 있습니다. 이러한 클라이언트는 여러 가지 프로토콜을 이해합니다. 예를 들어 iOS에는 HLS(HTTP 라이브 스트리밍) V4 형식이 필요하고 Silverlight와 Xbox에는 부드러운 스트리밍이 필요합니다. MPEG DASH, HLS 또는 부드러운 스트리밍을 이해하는 클라이언트에 제공하려는 적응 비트 전송률(다중 비트 전송률) MP4(ISO 기본 미디어 14496-12) 파일 집합이나 적응 비트 전송률 부드러운 스트리밍 파일 집합이 있는 경우 Media Services 동적 패키징을 이용해야 합니다.

동적 패키징을 사용 하는 경우 적응 비트 전송률 MP4 파일 또는 적응 비트 전송률 부드러운 스트리밍 파일 집합이 포함 된 자산을 만들어야 합니다. 이렇게 하면 매니페스트 또는 조각 요청의 지정된 형식에 따라 주문형 스트리밍 서버는 사용자가 선택한 프로토콜로 스트림을 받을 수 있도록 합니다. 따라서 사용자는 단일 스토리지 형식으로 파일을 저장하고 해당 파일에 대한 요금을 지불하기만 하면 되며, Media Services 서비스에서 클라이언트의 요청에 따라 적절한 응답을 작성하고 제공합니다.

다음 다이어그램에서는 기존의 인코딩 및 정적 패키징 워크플로를 보여 줍니다.

![정적 Encoding](./media/media-services-dynamic-packaging-overview/media-services-static-packaging.png)

다음 다이어그램에서는 동적 패키징 워크플로를 보여 줍니다.

![동적 Encoding](./media/media-services-dynamic-packaging-overview/media-services-dynamic-packaging.png)

## <a name="common-scenario"></a>일반적인 시나리오

1. 입력 파일(mezzanine 파일이라고 함)을 업로드합니다. H.264, MP4 또는 WMV를 예로 들 수 있습니다(지원되는 형식의 목록은 [미디어 인코더 표준에서 지원하는 형식](media-services-media-encoder-standard-formats.md)참조).
2. mezzanine 파일을 H.264 MP4 적응 비트 전송률 집합으로 인코딩합니다.
3. 주문형 로케이터를 만들어 적응 비트 전송률 MP4 집합이 포함된 자산을 게시합니다.
4. 콘텐츠에 액세스하고 콘텐츠를 스트리밍할 스트리밍 URL을 작성합니다.

## <a name="preparing-assets-for-dynamic-streaming"></a>동적 스트리밍을 위한 자산 준비

동적 스트리밍을 위해 자산을 준비 하려면 다음 옵션을 사용할 수 있습니다.

- [마스터 파일을 업로드합니다](media-services-dotnet-upload-files.md).
- [미디어 인코더 표준 인코더를 사용하여 H.264 MP4 적응 비트 전송률 집합을 생성합니다](media-services-dotnet-encode-with-media-encoder-standard.md).
- [콘텐츠를 스트림합니다](media-services-deliver-content-overview.md).

## <a name="audio-codecs-supported-by-dynamic-packaging"></a>동적 패키징으로 지원되는 오디오 코덱

동적 패키징은 [AAC](https://en.wikipedia.org/wiki/Advanced_Audio_Coding) (AAC, AAC V1, he AAC v2), [돌비 Digital Plus](https://en.wikipedia.org/wiki/Dolby_Digital_Plus)(향상 된 AC-3 또는 E-E-ac3), 돌비 Atmos 또는 [DTS](https://en.wikipedia.org/wiki/DTS_%28sound_system%29) (dts EXPRESS, dts LBR, dts hd, dts hd 무손실)로 인코딩된 오디오를 포함 하는 MP4 파일을 지원 합니다. 돌비 (Common Streaming Format) 또는 CMAF (Common Media Application Format)로 조각화 된 MP4를 사용 하 고 CMAF와 함께 HLS (HTTP 라이브 스트리밍)를 통해 MPEG (common Streaming Format)와 같은 표준에는 돌비 Atmos 콘텐츠 스트리밍이 지원 됩니다.

> [!NOTE]
> 동적 패키징은 [Dolby Digital](https://en.wikipedia.org/wiki/Dolby_Digital)(AC3) 오디오(레거시 코덱임)를 포함하는 파일을 지원하지 않습니다.

## <a name="media-services-learning-paths"></a>Media Services 학습 경로

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>피드백 제공

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
