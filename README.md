# 회의록 앱 모델 파일

회의록 데스크톱 앱이 첫 실행 때 내려받는 모델 파일 중, 공개된 원본 모델을 이 프로젝트가 직접 변환하거나 추출한 파일을 배포하는 저장소입니다. 앱 코드는 이 저장소에 없습니다.

- 파일은 **Releases**에 첨부되어 있습니다 (태그 `models-v1`).
- 앱은 `https://github.com/lunaticscode/meeting-note-model/releases/download/models-v1/<파일 이름>`에서 받고, 받은 뒤 아래 SHA-256으로 검증합니다. **파일을 바꾸면 앱이 받지 못하므로, 내용이 바뀌면 새 태그(`models-v2` 등)로 올리고 앱의 주소와 체크섬을 함께 바꿉니다.**
- 회사 데이터나 회의 내용은 들어 있지 않습니다. 공개 모델을 형식만 바꾼 파일입니다.

## 파일 목록

| 파일 | 크기 (바이트) | SHA-256 | 원본 | 라이선스 |
|---|---|---|---|---|
| `kanana-1.5-2.1b-instruct-2505-Q5_K_M.gguf` | 1,736,633,056 | `a4b364d492a3fa6fa94206c30edc088185870861737e37aeee8f3874eafbd95f` | [kakaocorp/kanana-1.5-2.1b-instruct-2505](https://huggingface.co/kakaocorp/kanana-1.5-2.1b-instruct-2505) | Apache-2.0 |
| `bge-m3-Q8_0.gguf` | 634,553,600 | `a4e4206b8db7acb8bcb4456bdbc05c3c94d5b86e8fd0b15c6a8aa7e87bf34e8c` | [BAAI/bge-m3](https://huggingface.co/BAAI/bge-m3) | MIT |
| `pyannote-segmentation-3.0.onnx` | 5,992,913 | `220ad67ca923bef2fa91f2390c786097bf305bceb5e261d4af67b38e938e1079` | [pyannote/segmentation-3.0](https://huggingface.co/pyannote/segmentation-3.0) (sherpa-onnx ONNX 변환본) | MIT |

## 만든 방법

### Kanana 1.5 2.1B instruct → GGUF Q5_K_M
- 원본: `kakaocorp/kanana-1.5-2.1b-instruct-2505`, revision `7df4bc35ccd610e451809d7106e1c3cf82bfd44c`
  - `model.safetensors` (SHA-256 `9a9d12e01656388e49114191ab235b8bec3b2214264341732798c698f0dd039f`), `config.json`, `generation_config.json`, `special_tokens_map.json`, `tokenizer.json`, `tokenizer_config.json`
- 변환: llama.cpp `b11977`의 `convert_hf_to_gguf.py` (Python 3.12, `--outtype f16`)
- 양자화: llama.cpp `b11977`의 `llama-quantize` → `Q5_K_M`

### BGE-M3 → GGUF Q8_0
- 원본: `BAAI/bge-m3`, revision `5617a9f61b028005a4858fdac845db406aefb181`
  - `pytorch_model.bin` (SHA-256 `b5e0ce3470abf5ef3831aa1bd5553b486803e83251590ab7ff35a117cf6aad38`), `config.json`, `tokenizer.json`, `tokenizer_config.json`, `special_tokens_map.json`, `sentencepiece.bpe.model`
- 변환: llama.cpp `b11977`의 `convert_hf_to_gguf.py` (`--outtype f16`) → `llama-quantize` → `Q8_0`

### pyannote segmentation-3.0 → ONNX
- 원본: sherpa-onnx가 공개한 ONNX 변환본 [`sherpa-onnx-pyannote-segmentation-3-0.tar.bz2`](https://github.com/k2-fsa/sherpa-onnx/releases/download/speaker-segmentation-models/sherpa-onnx-pyannote-segmentation-3-0.tar.bz2) (SHA-256 `24615ee884c897d9d2ba09bb4d30da6bb1b15e685065962db5b02e76e4996488`)
- 압축 안의 `sherpa-onnx-pyannote-segmentation-3-0/model.onnx`를 그대로 꺼내 이름만 바꿨습니다 (내용 변경 없음)

같은 원본·같은 도구 버전이면 같은 파일이 나오며, 앱 소스의 `pnpm setup:engines`가 이 과정을 그대로 수행합니다.

## 라이선스

각 파일은 원본 모델의 라이선스를 따릅니다. 원본 라이선스 전문은 `licenses/` 폴더에 있습니다.

- `licenses/kanana-1.5-2.1b-instruct-Apache-2.0.txt` — Kanana 1.5 2.1B instruct (Kakao Corp.), Apache License 2.0. 원본 저장소의 `LICENSE` (revision `7df4bc35`)
- `licenses/bge-m3-MIT.txt` — BGE-M3 (BAAI), MIT License. 모델 카드가 MIT로 표기하며 모델 저장소에 LICENSE 파일이 없어, BAAI의 공식 프로젝트 저장소 [FlagOpen/FlagEmbedding](https://github.com/FlagOpen/FlagEmbedding)의 `LICENSE`를 넣었습니다
- `licenses/pyannote-segmentation-3.0-MIT.txt` — pyannote segmentation-3.0 (CNRS), MIT License. sherpa-onnx 압축 파일에 함께 들어 있는 `LICENSE`

이 저장소의 파일은 원본을 변환(GGUF 형식 변환·양자화)하거나 형식을 바꾼 것이며, 원본 저작자가 만들거나 보증한 파일이 아닙니다.
