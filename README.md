# 틈틈이 CLI 릴리즈 검증

[틈틈이](https://teumteumi.up.railway.app) 상태줄 클라이언트(`statusline.mjs`)의 릴리즈 해시와
서명 공개키를 여기 둡니다. 배포 서버가 아닌 곳에 둔다는 것이 요점입니다.

## 왜 굳이 딴 데 두나

이 클라이언트는 하루에 한 번 서버에 판번호를 묻고, 새 판이 있으면 받아서 자기 자신을 교체합니다.
편한 기능이지만 뒤집어 말하면 우리 서버가 남의 기기에서 코드를 실행시킬 수 있다는 뜻입니다.
그래서 두 겹으로 막습니다.

서명이 첫 겹입니다. 클라이언트는 아래 공개키로 서명된 판만 설치합니다. 개인키는 배포 서버에 없고
릴리즈 담당자 기기에만 있으니, 서버가 털려도 거기서 멈춥니다.

공개가 둘째 겹입니다. 서명은 "우리가 특정한 한 사람에게만 다른 코드를 보내는 것"까지는 못 막습니다.
그건 암호로 막히지 않고 공개로만 드러납니다. 그래서 모든 릴리즈의 SHA-256을 우리 서버 밖인
이곳에 남깁니다. 받은 파일의 해시가 아래 표에 없으면 우리가 공개한 릴리즈가 아닙니다.

이 저장소의 커밋 이력도 같이 증거가 됩니다. 값을 조용히 바꾸면 이력에 남습니다.

## 확인하는 법

내장 명령이 가장 빠릅니다. 설치본 해시, 서버가 지금 주는 파일의 해시, 서명 검증 결과를 한 번에 냅니다.

```bash
node ~/.teum/statusline.mjs verify
```

직접 해보려면:

```bash
# 내 기기에 설치된 파일
shasum -a 256 ~/.teum/statusline.mjs

# 서버가 지금 주는 파일
curl -s https://teumteumi.up.railway.app/cli/statusline.mjs | shasum -a 256

# 서명 검증 (openssl)
curl -s https://teumteumi.up.railway.app/cli/statusline.mjs -o /tmp/teum.mjs
curl -s https://teumteumi.up.railway.app/cli/statusline.mjs.sig | base64 -d > /tmp/teum.sig
curl -s https://raw.githubusercontent.com/kennykimtang/teumteumi-cli-releases/main/pubkey.pem -o /tmp/teum.pub
openssl pkeyutl -verify -pubin -inkey /tmp/teum.pub -rawin -in /tmp/teum.mjs -sigfile /tmp/teum.sig
```

두 해시가 표의 값과 같고 서명이 검증되면, 돌고 있는 코드는 우리가 공개한 바로 그 코드입니다.

## 릴리즈

| 판 | 날짜 | SHA-256 | 서명 |
|---|---|---|---|
| v15 | 2026-08-28 | `74703c338c8a556025291082394693bddc0c12c0c573400a9aea7bc2eb35c08a` | [statusline.v15.mjs.sig](statusline.v15.mjs.sig) |
| v14 | 2026-08-24 | `a93a3653c0ed00bd74de8adab3ebad174a1392efb7924b23c6112c2a7b634757` | [statusline.v14.mjs.sig](statusline.v14.mjs.sig) |
| v13 | 2026-08-21 | `b69b79d93399117029edca42df0182378f43cc5112b345455514ca4279603e59` | [statusline.v13.mjs.sig](statusline.v13.mjs.sig) |
| v12 | 2026-08-21 | `f7b6adc69eded4c411f880b9e5a6c5c949feed796742cbb47185c6127b3c8494` | [statusline.v12.mjs.sig](statusline.v12.mjs.sig) |
| v11 | 2026-08-21 | `7fb29f382604a6e8e7b9e334cb8a09daac2abfc8becff07e73f2394afc5c396b` | [statusline.v11.mjs.sig](statusline.v11.mjs.sig) |
| v10 ⚠️ | 2026-08-20 | `6e5eee8007d8787c176cc6d30f0d9597b054f97e822b7ded297901282bb4fe82` | [statusline.v10.6e5eee80.mjs.sig](statusline.v10.6e5eee80.mjs.sig) |
| v10 ⚠️ | 2026-08-20 | `dc4b4807442fd08c252be17e9de52d70df1b0bb2c284b50e644e4e3a3b74b232` | [statusline.v10.dc4b4807.mjs.sig](statusline.v10.dc4b4807.mjs.sig) |
| v10 | 2026-08-20 | `cf9fde3cd4e5e537c21fa61ca45172d95204d08ad0550c24ca527bfb9b03f274` | [statusline.v10.mjs.sig](statusline.v10.mjs.sig) |
| v9 | 2026-08-18 | `20c2febc6abec3d37caeda4f0508f9d0c1576f27831f0449090a0ffb83d8bb3b` | [statusline.v9.mjs.sig](statusline.v9.mjs.sig) |
| v8 | 2026-08-14 | `2e94b463d59483c88a337b91f527395e971370b49fe52ecc5e79b6300c95d9bb` | [statusline.v8.mjs.sig](statusline.v8.mjs.sig) |
| v7 | 2026-08-10 | `dfebf9bdaba3ab91a4238c9f3629e603d54c707e3edaa5bf68f289ea74ea7a59` | [statusline.v7.mjs.sig](statusline.v7.mjs.sig) |
| v6 | 2026-08-07 | `d7d8944aa0cbf687337767ab67d98d4ecf99e8d722126fc9ff3bd505aed11f6d` | [statusline.v6.mjs.sig](statusline.v6.mjs.sig) |
| v5 | 2026-08-07 | `53b2d4bcbade91e26bf0d2efcc70830530240289825d97a46b362fb150e70ea9` | [statusline.v5.mjs.sig](statusline.v5.mjs.sig) |
| v4 | 2026-08-07 | `7803e9870f030f8a214a3e350d6337304a27bc6804c4f98156d28cbee72f4cca` | (서명 도입 전) |

> v10이 셋인 이유. 2026-08-20에 같은 번호로 세 번 배포했습니다. 우리 실수입니다.
> 자가 갱신은 받은 판이 지금보다 높을 때만 설치하는데, 그래서 이미 v10을 받은 기기는
> 그 뒤의 v10들을 받지 못했습니다. 셋 다 실제로 나간 판이라 해시 앞자리로 구분해 전부 올려 둡니다.
> `verify` 결과가 셋 중 하나와 맞으면 정상입니다. 2026-08-21부터는 배포마다 번호를 올리고,
> 안 올리면 릴리즈 스크립트가 배포를 막습니다.

v3 이전은 서명 체계를 도입하기 전이라 해시가 없습니다. 지금 와서 적으면 검증된 값처럼 보이는데
그렇지 않으므로, 있는 것만 적습니다.

변경 내역: https://teumteumi.up.railway.app/cli/CHANGELOG.md

## 서명 공개키 (Ed25519)

`pubkey.pem`과 같은 값이고, 클라이언트 소스의 `RELEASE_PUBKEY` 상수와도 같아야 합니다.

```
-----BEGIN PUBLIC KEY-----
MCowBQYDK2VwAyEAb0Au+iYXxIPyGv4gxuBq1J7pBioXsjj3qdnu8vkXM2E=
-----END PUBLIC KEY-----
```

## 자가 갱신이 싫다면

꺼도 됩니다. 꺼도 고장 나는 기능은 없고 지금 판에 그대로 머뭅니다.

```bash
export TEUM_CLI_NO_UPDATE=1
```

아예 지우려면 `node ~/.teum/statusline.mjs remove`. 설치할 때 백업해 둔
`~/.claude/settings.json`의 원래 값을 되돌리고, 우리가 넣은 항목만 걷어냅니다.

## 나가는 것과 안 나가는 것

서버로 가는 것은 무작위 기기 ID, 광고 노출 사실(무엇을 몇 초 보여줬는지), 판번호입니다.
대화 내용, 프롬프트, 코드, 파일 목록, 디렉터리 이름, 환경변수는 읽지도 보내지도 않습니다.
네트워크 연결은 `teumteumi.up.railway.app` 한 곳으로만 갑니다.

말로만 하기엔 약한 얘기라 전문을 열어 뒀습니다.
https://teumteumi.up.railway.app/cli/statusline.mjs

## 보안 제보

문제를 찾으시면 알려주세요. 이 저장소 Issues나 서비스 안내 페이지 연락처로 받습니다.
이 저장소 자체가 한 사용자분의 제보로 생겼습니다.
