# Unity AI NPC

**음성으로 대화하고 입모양까지 맞추는 Unity AI NPC 구현**

![Unity](https://img.shields.io/badge/Unity_6-000000?style=flat-square&logo=unity&logoColor=white)
![C Sharp](https://img.shields.io/badge/C%23-512BD4?style=flat-square&logo=csharp&logoColor=white)
![OpenAI](https://img.shields.io/badge/OpenAI-412991?style=flat-square&logo=openai&logoColor=white)
![Google Cloud](https://img.shields.io/badge/Google_TTS-4285F4?style=flat-square&logo=googlecloud&logoColor=white)

<!-- TODO: NPC와 대화하는 장면 GIF. 립싱크가 보이면 가장 좋습니다 -->

## 프로젝트 소개

플레이어가 마이크로 말을 걸면 NPC가 알아듣고, 답변을 음성으로 말하며, 그 음성에 맞춰 입모양이 움직이는 시스템입니다. 음성 입력부터 표정 애니메이션까지 하나의 파이프라인으로 연결하는 것이 목표였습니다.

```
마이크 입력 → Whisper (STT) → GPT (대화 생성) → TTS (음성 합성) → FFT 분석 → 립싱크
```

<!-- TODO: 개발 기간 -->

## 주요 구현

| 단계 | 내용 |
|---|---|
| 음성 입력 | 마이크 장치 선택, 녹음, WAV 인코딩 |
| 음성 인식 | OpenAI Whisper API 연동 |
| 대화 생성 | OpenAI Chat API 기반 NPC 응답 |
| 음성 합성 | OpenAI TTS와 Google Cloud TTS 두 가지 구현 후 비교 |
| 립싱크 | 재생 중인 음성을 실시간 분석해 BlendShape 제어 |

## 기술적 포인트

**주파수 분석 기반 립싱크**
음소 데이터를 미리 만들지 않고, 재생 중인 오디오를 실시간으로 분석하는 방식을 택했습니다. 512 크기 FFT로 주파수 스펙트럼을 뽑고, 음소별로 지정한 주파수 대역 임계값과 비교해 대응하는 BlendShape 가중치를 계산합니다. 프레임 간 값은 보간해 입이 튀지 않도록 했습니다.

음소와 BlendShape의 대응 관계, 주파수 임계값, 최대 가중치는 인스펙터에서 조정할 수 있어 캐릭터 모델이 바뀌어도 코드를 수정하지 않습니다.

**TTS 두 가지 비교**
OpenAI TTS와 Google Cloud TTS를 각각 구현해 응답 속도와 음질을 비교했습니다.

<!-- TODO: 어느 쪽을 최종 선택했고 이유가 뭔지. 응답 지연 체감 차이가 있었다면 그것도 -->

**API 키 분리**
모든 API 키를 코드에서 분리해 인스펙터 필드로 노출하고, 저장소에는 빈 값으로 커밋했습니다. 커밋 히스토리 전체에 키가 남아 있지 않습니다.

## 기술 스택

Unity 6 · C# · OpenAI Whisper / Chat / TTS API · Google Cloud Text-to-Speech · UnityWebRequest · SkinnedMeshRenderer BlendShape

## 실행 방법

1. Unity Hub에서 저장소 루트를 프로젝트로 추가합니다.
2. 씬을 열고 매니저 오브젝트의 인스펙터에 본인의 API 키를 입력합니다.
   - OpenAI: `OpenAIManager`, `OpenAITTSManager`, `WhisperManager`
   - Google: `GoogleTextToSpeech`
3. 마이크 권한을 허용하고 Play Mode에서 실행합니다.

| 씬 | 내용 |
|---|---|
| `Whisper_1` `Whisper_2` | 음성 인식 |
| `TTS` `GoogleTTS` | 음성 합성 비교 |
| `TTS_Animation` `TTS_Animation2` | 립싱크 |
| `OpenAPI_1` | 대화 생성 |

## 알려진 정리 사항

일부 스크립트의 주석이 CP949로 저장되어 있어 환경에 따라 깨져 보입니다. UTF-8로 변환 예정입니다.
