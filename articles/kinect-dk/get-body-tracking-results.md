---
title: Azure Kinect 신체 추적 결과 가져오기
description: Azure Kinect 신체 추적 SDK를 사용하여 신체 추적 결과를 가져오는 방법을 알아봅니다.
author: qm13
ms.prod: kinect-dk
ms.author: quentinm
ms.reviewer: yijwan
ms.date: 06/26/2019
ms.topic: conceptual
keywords: kinect, azure, 센서, sdk, 신체, 추적, 조인트
ms.openlocfilehash: 1b62022242144d5db51455a32ac04b67c3e5dd7a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "85277036"
---
# <a name="get-body-tracking-results"></a>신체 추적 결과 가져오기

신체 추적 SDK는 신체 추적기 개체를 사용하여 Azure Kinect DK 캡처를 처리하고 신체 추적 결과를 생성합니다. 또한 추적기, 처리 큐 및 출력 큐의 전역 상태를 유지 관리합니다. 신체 추적기를 사용하는 데 세 단계가 있습니다.

- 추적기 만들기
- Azure Kinect DK를 사용하여 깊이 및 IR 이미지 캡처
- 캡처를 큐에 추가하고 결과를 팝업

## <a name="create-a-tracker"></a>추적기 만들기


신체 추적을 사용하는 첫 번째 단계는 추적기를 만드는 것이며 센서 보정 [k4a_calibration_t](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__calibration__t.html) 구조체를 전달해야 합니다. 센서 보정은 Azure Kinect 센서 SDK [k4a_device_get_calibration()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga4e43940d8d8db48da266c7a7842c8d78.html#ga4e43940d8d8db48da266c7a7842c8d78) 함수를 사용하여 쿼리할 수 있습니다.

```C
k4a_calibration_t sensor_calibration;
if (K4A_RESULT_SUCCEEDED != k4a_device_get_calibration(device, device_config.depth_mode, K4A_COLOR_RESOLUTION_OFF, &sensor_calibration))
{
    printf("Get depth camera calibration failed!\n");
    return 0;
}

k4abt_tracker_t tracker = NULL;
k4abt_tracker_configuration_t tracker_config = K4ABT_TRACKER_CONFIG_DEFAULT;
if (K4A_RESULT_SUCCEEDED != k4abt_tracker_create(&sensor_calibration, tracker_config, &tracker))
{
    printf("Body tracker initialization failed!\n");
    return 0;
}
```

## <a name="capture-depth-and-ir-images"></a>깊이 및 IR 이미지 캡처

Azure Kinect DK를 사용한 이미지 캡처는 [이미지 검색](retrieve-images.md) 페이지에서 설명합니다.

>[!NOTE]
> 최상의 성능 및 정확성을 위해 `K4A_DEPTH_MODE_NFOV_UNBINNED` 또는 `K4A_DEPTH_MODE_WFOV_2X2BINNED` 모드를 사용하는 것이 좋습니다. `K4A_DEPTH_MODE_OFF` 또는 `K4A_DEPTH_MODE_PASSIVE_IR` 모드는 사용하지 마세요.

지원되는 Azure Kinect DK 모드는 Azure Kinect DK [하드웨어 사양](hardware-specification.md) 및 [k4a_depth_mode_t](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___enumerations_ga3507ee60c1ffe1909096e2080dd2a05d.html#ga3507ee60c1ffe1909096e2080dd2a05d) 열거형에 설명되어 있습니다.

```C
// Capture a depth frame
switch (k4a_device_get_capture(device, &capture, TIMEOUT_IN_MS))
{
case K4A_WAIT_RESULT_SUCCEEDED:
    break;
case K4A_WAIT_RESULT_TIMEOUT:
    printf("Timed out waiting for a capture\n");
    continue;
    break;
case K4A_WAIT_RESULT_FAILED:
    printf("Failed to read a capture\n");
    goto Exit;
}
```

## <a name="enqueue-the-capture-and-pop-the-results"></a>캡처를 큐에 추가하고 결과를 팝업

추적기는 Azure Kinect DK 캡처를 보다 효율적으로 처리하기 위해 입력 큐와 출력 큐를 내부적으로 유지 관리합니다. [k4abt_tracker_enqueue_capture()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_ga093becd9bb4a63f5f4d56f58097a7b1e.html#ga093becd9bb4a63f5f4d56f58097a7b1e) 함수를 사용하여 새 캡처를 입력 큐에 추가합니다. [k4abt_tracker_pop_result()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_gaaf446fb1579cbbe0b6af824ee0a7458b.html#gaaf446fb1579cbbe0b6af824ee0a7458b) 함수를 사용하여 출력 큐의 결과를 팝업합니다. 시간 제한 값 사용은 애플리케이션에 따라 다르며 큐 대기 시간을 제어합니다.

### <a name="real-time-processing"></a>실시간 처리
실시간 결과가 필요하고 삭제된 프레임을 수용할 수 있는 단일 스레드 애플리케이션에 이 패턴을 사용합니다. [GitHub Azure-Kinect-Samples](https://github.com/microsoft/Azure-Kinect-Samples)에 있는 `simple_3d_viewer` 샘플은 실시간 처리의 예입니다.

```C
k4a_wait_result_t queue_capture_result = k4abt_tracker_enqueue_capture(tracker, sensor_capture, 0);
k4a_capture_release(sensor_capture); // Remember to release the sensor capture once you finish using it
if (queue_capture_result == K4A_WAIT_RESULT_FAILED)
{
    printf("Error! Adding capture to tracker process queue failed!\n");
    break;
}

k4abt_frame_t body_frame = NULL;
k4a_wait_result_t pop_frame_result = k4abt_tracker_pop_result(tracker, &body_frame, 0);
if (pop_frame_result == K4A_WAIT_RESULT_SUCCEEDED)
{
    // Successfully popped the body tracking result. Start your processing
    ...

    k4abt_frame_release(body_frame); // Remember to release the body frame once you finish using it
}
```

### <a name="synchronous-processing"></a>동기 처리
실시간 결과가 필요하지 않거나 삭제된 프레임을 수용할 수 없는 애플리케이션에 이 패턴을 사용합니다.

처리 처리량이 제한될 수 있습니다.

[GitHub Azure-Kinect-Samples](https://github.com/microsoft/Azure-Kinect-Samples)에 있는 `simple_sample.exe` 샘플은 동기 처리의 예입니다.

```C
k4a_wait_result_t queue_capture_result = k4abt_tracker_enqueue_capture(tracker, sensor_capture, K4A_WAIT_INFINITE);
k4a_capture_release(sensor_capture); // Remember to release the sensor capture once you finish using it
if (queue_capture_result != K4A_WAIT_RESULT_SUCCEEDED)
{
    // It should never hit timeout or error when K4A_WAIT_INFINITE is set.
    printf("Error! Adding capture to tracker process queue failed!\n");
    break;
}

k4abt_frame_t body_frame = NULL;
k4a_wait_result_t pop_frame_result = k4abt_tracker_pop_result(tracker, &body_frame, K4A_WAIT_INFINITE);
if (pop_frame_result != K4A_WAIT_RESULT_SUCCEEDED)
{
    // It should never hit timeout or error when K4A_WAIT_INFINITE is set.
    printf("Error! Popping body tracking result failed!\n");
    break;
}
// Successfully popped the body tracking result. Start your processing
...

k4abt_frame_release(body_frame); // Remember to release the body frame once you finish using it
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
>[신체 프레임의 데이터에 액세스](access-data-body-frame.md)
