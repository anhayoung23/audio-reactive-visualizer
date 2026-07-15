# 오디오 - 시각화

실시간 오디오 스펙트럼을 3D 지형 형태로 시각화하는 TouchDesigner 프로젝트입니다.

![TouchDesigner](https://img.shields.io/badge/TouchDesigner-000000?style=flat-square&logo=touchdesigner&logoColor=white)

<br>

<img src="media/demo.gif" width="100%" alt="demo preview" />

<br>

## Overview

[한 문단 — 이 작업이 무엇인지, 어떤 계기로 시작했는지 간단히 소개]

오디오 입력을 분석해 스펙트럼 데이터를 3D 지형 형태로 변환합니다. 소리의 진폭과 주파수 변화가 형태의 높낮이와 색으로 번역되며, 시간에 따라 계속 흐르고 변화하는 표면을 만들어냅니다. 색상은 별도의 셰이더 없이 Ramp와 Constant MAT의 조합만으로 구성해, 오디오 진폭에 따라 자연스러운 컬러 하모니가 실시간으로 이어지도록 했습니다.

## Concept

[작품이 다루는 주제와 왜 이 표현 방식을 택했는지]

소리는 눈에 보이지 않지만 끊임없이 흐르고 변화합니다. 이 작업은 그 비가시적인 흐름을 눈에 보이는 지형으로 옮겨, 청각적 경험을 시각적 언어로 재구성하는 실험입니다.

## Tech Stack

| 영역 | 사용 기술 |
|---|---|
| 오디오 분석 | Spectrum CHOP, Audio Device In CHOP |
| 지오메트리 생성 | CHOP to SOP (오디오 진폭 → 높이/포인트 attribute) |
| 지오메트리 | Geometry COMP (Merge SOP, Transform SOP, Point SOP) |
| 재질/컬러 | Ramp TOP + Constant MAT 조합으로만 컬러 구성 |

## Pipeline

```
Audio Device In CHOP
    ↓
Spectrum CHOP
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

## Key Techniques

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


@2026
