# Hermes Telegram Topic Name Lock

Hermes Agent가 대화 내용에 맞춰 Telegram 토픽 이름을 자동으로 바꾸는 동작을 끄는 에이전트 스킬입니다. 직접 정한 이름을 유지하고 싶은 경우 사용합니다.

이 저장소에는 이 스킬과 사용 안내만 들어 있습니다. Hermes 본체나 개인 봇 설정은 포함하지 않습니다.

## Codex에 설치

터미널에서 실행합니다. 같은 이름의 폴더가 이미 있다면 먼저 기존 내용을 확인하세요.

```sh
mkdir -p ~/.codex/skills
git clone https://github.com/Frompter-Wonny/hermes-telegram-topic-name-lock.git \
  ~/.codex/skills/hermes-telegram-topic-name-lock
```

새 대화를 열고 다음처럼 요청하세요.

```text
$hermes-telegram-topic-name-lock
실행 중인 내 Hermes 텔레그램 봇 모두의 토픽 자동 이름 변경을 꺼줘.
```

특정 봇만 적용하려면 프로필명을 지정합니다.

```text
$hermes-telegram-topic-name-lock
work 프로필만 토픽 자동 이름 변경을 꺼줘.
```

다른 에이전트에서도 [SKILL.md](SKILL.md)를 읽혀 같은 절차를 수행할 수 있습니다. 설정을 바꾸려면 Hermes가 설치된 컴퓨터에 접근할 수 있어야 합니다.

## 직접 적용하기

1. `hermes --version`과 `hermes gateway status`로 설치와 실행 대상을 확인합니다. 설치 버전에서 `disable_topic_auto_rename`를 지원하는지도 확인하세요. 이 스킬은 설치된 코드의 플래그 검사와 설정 로더를 확인하도록 되어 있습니다. 지원되지 않는 버전에서는 이 설정만 추가해도 효과가 없을 수 있습니다.
2. 대상 설정을 백업합니다. 기본 경로는 `~/.hermes/config.yaml`, 별도 프로필은 `~/.hermes/profiles/<profile>/config.yaml`입니다. `HERMES_HOME`을 지정했다면 실제 서비스 경로를 사용합니다.
3. 기존 YAML 구조 안에 아래 항목을 병합합니다. 기존 설정을 통째로 덮어쓰거나 `gateway:`를 중복 선언하지 마세요. 값은 문자열이 아닌 YAML 불리언 `true`입니다.

```yaml
gateway:
  platforms:
    telegram:
      extra:
        disable_topic_auto_rename: true
```

4. 해당 게이트웨이를 재시작하고 상태를 확인합니다.

```sh
# 기본 봇
hermes gateway restart
hermes gateway status

# 별도 프로필: work를 자신의 프로필명으로 바꾸세요
hermes --profile work gateway restart
hermes --profile work gateway status
```

5. 새 프로세스가 실행됐는지, 실제 설정 로더가 값을 참으로 읽는지 확인합니다. 이후 평소 대화에서 토픽 이름이 유지되는지 확인하세요. 재시작 요청은 진행 중인 작업 때문에 지연될 수 있습니다.

프로필마다 설정이 독립적일 수 있으므로 기본 봇에 적용한 것만으로 다른 봇까지 적용됐다고 볼 수 없습니다. 설정이 이미 `true`인데 이름이 바뀐다면 실제 프로필 경로, 설정 우선순위, 재시작 여부와 다른 이름 변경 경로를 확인하세요.

## 변경 범위와 복구

이 옵션은 Hermes의 해당 자동 이름 변경 경로를 차단합니다. 토픽·대화를 삭제하거나 예전에 바뀐 이름을 복구하는 절차는 포함하지 않습니다. 일반 세션 제목 생성과 토픽 생성 설정도 변경하지 않습니다.

되돌리려면 추가한 키를 제거하거나 이전 값으로 복구한 뒤 해당 게이트웨이를 재시작합니다. 그사이 다른 설정을 수정했다면 백업 전체를 덮어쓰지 말고 이 항목만 복구하세요.

## 근거와 호환성

확인 기준은 **설치 버전의 실제 코드와 설정 로더**입니다. 특정 최소 버전이나 모든 버전의 동작을 보장하지 않습니다. 공개 문서에 이 키가 설명되어 있다고 가정하지 않습니다.

- [Hermes 공식 저장소](https://github.com/NousResearch/hermes-agent)
- [공식 설정 안내](https://hermes-agent.nousresearch.com/docs/user-guide/configuration)
- 확인할 코드: `disable_topic_auto_rename`를 읽는 Telegram 토픽 이름 변경 경로와 `gateway.platforms`를 병합하는 설정 로더. 일부 버전에서는 `gateway/run_topics.py`, `gateway/config_loader.py`에 있습니다.

MIT 라이선스로 자유롭게 사용·수정·배포할 수 있습니다.
