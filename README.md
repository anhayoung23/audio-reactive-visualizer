# Resonant Flow

실시간으로 흐르는 소리를 계속 변화하는 지형으로 번역하는 제너레이티브 오디오-비주얼 작업입니다. 음의 높낮이와 볼륨, 음색이 형태의 굴곡과 색으로 옮겨지며, 청각적 흐름을 시각적 언어로 재구성합니다.

![TouchDesigner](https://img.shields.io/badge/TouchDesigner-000000?style=flat-square&logo=touchdesigner&logoColor=white)

<br>

<img src="media/demo.gif" width="100%" alt="demo preview" />

<br>

## Overview

소리는 눈에 보이지 않지만 끊임없이 흐르고 변화합니다. Resonant Flow는 실시간 오디오 입력을 분석해, 그 흐름을 3D 지형(terrain) 형태의 시각적 구조로 옮기는 작업입니다. 진폭과 주파수의 변화가 곧 형태의 높낮이와 색으로 번역되며, 소리가 멈추지 않는 한 지형도 고정되지 않고 계속 흐르며 변화합니다. 색상은 별도의 셰이더 없이 Ramp와 Constant MAT의 조합만으로 구성해, 오디오 진폭에 따라 자연스러운 컬러 하모니가 실시간으로 이어지도록 했습니다.

## Concept

청각적 경험은 본래 형태가 없지만, 우리는 늘 그것을 몸으로 느끼고 기억합니다. Resonant Flow는 그 감각의 번역 — 들리는 것을 보이는 것으로, 순간을 지속되는 형태로 바꾸는 실험입니다. 지형이 매 순간 다시 그려지듯, 이 작업은 고정된 결과물이 아니라 소리와 함께 계속 생성되는 하나의 흐름으로 존재합니다.

## Tech Stack

| 영역 | 사용 기술 |
|---|---|
| 오디오 분석 | Audio Device In CHOP, Audio Spectrum CHOP (로그 스케일링, FFT 16384), Stretch CHOP (리샘플링) |
| 지오메트리 생성 | CHOP to SOP (오디오 진폭 → 높이/포인트 attribute) |
| 지오메트리 | Geometry COMP (Merge SOP, Transform SOP, Point SOP) |
| 재질/컬러 | Ramp TOP + Constant MAT 조합으로만 컬러 구성 |

## Pipeline

```
Audio Device In CHOP
    ↓
Audio Spectrum CHOP (FFT 16384, Logarithmic Scaling, High Frequency Boost)
    ↓
Stretch CHOP (샘플 수를 지오메트리 포인트 수에 맞게 리샘플링)
    ↓
CHOP to SOP (오디오 값 → 포인트 높이 + spectrum attribute)
    ↓
Merge SOP → Transform SOP
    ↓
Point SOP (spectrum attribute → 텍스처 좌표 V로 매핑)
    ↓
Geometry COMP (geo1)
    ↓
Constant MAT — Color Map → Ramp TOP (컬러 그라디언트)
    ↓
Render TOP
    ↓
Out TOP (최종 출력)
```

### Audio Spectrum CHOP

<img src="media/audiospectrum_settings.png" width="100%" alt="Audio Spectrum CHOP settings" />

| 파라미터 | 값 | 의미 |
|---|---|---|
| Mode | Visualization | 정밀 분석이 아닌 시각화에 최적화된 스펙트럼 계산 방식 |
| FFT Size | 16384 | 주파수 분해능이 매우 높은 설정 |
| Frequency ↔ Logarithmic Scaling | 1 | 완전 로그 스케일링 — 사람이 고음 차이를 더 세밀히 인지하는 방식에 맞춰 스펙트럼 분포를 재배치 |
| High Frequency Boost | 0.999 | 원래 에너지가 약한 고주파 대역을 거의 최대치로 증폭해 시각적으로 잘 드러나게 보정 |
| Output Length | Match Length To Frequency | FFT Size와 로그 스케일링 설정에 따라 채널 길이 자동 결정 (2048) |

### Stretch CHOP

<img src="media/stretch_settings.png" width="100%" alt="Stretch CHOP settings" />

Audio Spectrum CHOP의 출력(2048 샘플)을 지오메트리 포인트 개수에 맞게 리샘플링하는 노드입니다.

| 파라미터 | 값 | 의미 |
|---|---|---|
| Interpolation | Linear | 샘플 사이를 선형 보간해 부드럽게 리샘플링 |
| Unit Values | Absolute | Start/End를 절대 샘플 개수로 지정 |
| Start / End | 0 / 1000 | 결과 채널 길이를 1000 샘플로 고정 |

스펙트럼 원본 길이(2048)와 지형 지오메트리의 포인트 수(1000)가 서로 다르기 때문에, 각 포인트에 정확히 하나의 스펙트럼 값이 매핑되도록 이 단계에서 리샘플링합니다.

## Key Techniques

- 로그 스케일링과 High Frequency Boost를 적용한 Audio Spectrum 분석으로, 사람이 인지하는 방식에 가깝게 주파수 대역을 시각화
- Stretch CHOP을 통한 오디오 채널-지오메트리 포인트 수 정합(matching) 처리
- 실시간 오디오 스펙트럼 → 포인트 높이 및 컬러 attribute 생성 (CHOP to SOP)
- Ramp TOP + Constant MAT 조합만으로 오디오 진폭에 따른 컬러 하모니 구현 (별도 셰이더 없이)
- Point SOP의 텍스처 좌표(mapv)를 오디오 attribute에 실시간 연동해 색상 매핑
- Geometry COMP 기반 실시간 지형(terrain) 형태 생성

## How to Run

1. TouchDesigner 2025.x 이상 설치
2. `project.toe` 파일 열기
3. Audio Device In CHOP에서 오디오 인풋 디바이스 선택
4. 상단 Realtime 토글 On
5. 오디오를 재생하면 실시간으로 반응하는 비주얼 확인 가능

## Screenshots

<table>
  <tr>
    <td><img src="media/screenshot_01.png" width="100%"/></td>
    <td><img src="media/screenshot_02.png" width="100%"/></td>
  </tr>
</table>

## Reference

이 프로젝트는 [참고한 유튜브 튜토리얼 제목/채널명]을 참고해 제작했습니다.

## Credits

제작 · 안하영 (Ha-young Ahn)
