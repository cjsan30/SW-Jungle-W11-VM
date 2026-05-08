# Week11 VM - KAIST Pintos

이 저장소는 KAIST Pintos 기반 운영체제 과제 중 가상 메모리(VM) 단계 작업을 위한 프로젝트입니다.

Docker와 VSCode DevContainer 설정은 포함되어 있지만, 이 README는 Docker 사용법이 아니라 현재 프로젝트의 구조, 빌드 방법, 테스트 방법을 기준으로 정리합니다.

## 프로젝트 개요

Pintos는 교육용 운영체제 코드베이스입니다. 이 저장소에서는 `pintos/` 디렉터리 아래의 커널, 사용자 프로그램, 가상 메모리, 파일시스템 코드를 수정하고 테스트합니다.

주요 목표는 다음과 같습니다.

- 가상 메모리 서브시스템 구현 및 개선
- 페이지 폴트 처리
- Supplemental Page Table 관리
- 프레임 할당과 교체
- 스왑 및 파일 기반 페이지 처리
- `mmap`, stack growth, copy-on-write 등 VM 관련 테스트 통과

## 디렉터리 구조

```text
.
├─ .devcontainer/        # VSCode DevContainer 개발 환경 설정
├─ .vscode/              # VSCode 실행/디버깅 보조 설정
├─ pintos/               # Pintos 본체
│  ├─ threads/           # 스레드, 스케줄러, 동기화, 인터럽트
│  ├─ userprog/          # 사용자 프로세스, 시스템 콜, 예외 처리
│  ├─ vm/                # 가상 메모리 구현 대상
│  ├─ filesys/           # 파일시스템과 버퍼 캐시
│  ├─ devices/           # 타이머, 디스크, 키보드 등 장치 코드
│  ├─ include/           # 공용 헤더
│  ├─ lib/               # 커널/유저 공용 라이브러리
│  ├─ tests/             # Pintos 테스트 케이스
│  └─ utils/             # pintos 실행 유틸리티
├─ AGENTS.md             # AI 에이전트 작업 기준
├─ CLAUDE.md             # Claude Code 작업 기준
├─ PROJECT1_STUDY_PLAN.md
└─ README.md
```

## 개발 환경

권장 개발 환경은 저장소에 포함된 DevContainer입니다.

- Ubuntu 22.04
- x86-64
- GCC / make / GDB
- QEMU
- Python 3
- VSCode Dev Containers

VSCode에서 프로젝트 루트를 연 뒤 `Dev Containers: Reopen in Container`를 실행하면 `.devcontainer/Dockerfile` 기준으로 개발 환경이 구성됩니다.

컨테이너 내부 셸에서는 다음 명령으로 Pintos 환경을 활성화합니다.

```bash
source pintos/activate
```

## 빌드

수정 대상 모듈 디렉터리로 이동한 뒤 `make`를 실행합니다.

```bash
source pintos/activate
cd pintos/vm
make
```

다른 모듈을 빌드할 때는 해당 디렉터리에서 실행합니다.

```bash
cd pintos/threads
make

cd pintos/userprog
make

cd pintos/filesys
make
```

빌드 산출물을 정리하려면 다음을 사용합니다.

```bash
make clean
```

## 테스트

VM 프로젝트 전체 테스트는 `pintos/vm`에서 실행합니다.

```bash
source pintos/activate
cd pintos/vm
make check
```

특정 테스트만 실행할 때는 `make check` 대신 Pintos 테스트 유틸리티를 사용할 수 있습니다.

```bash
cd pintos/vm
pintos -- run <test-name>
```

예시:

```bash
pintos -- run lazy-anon
pintos -- run mmap-read
pintos -- run page-merge-seq
```

테스트 결과는 보통 각 모듈의 `build/` 하위에 생성됩니다. 빌드 결과, 디스크 이미지, 로그 등 생성 산출물은 특별한 이유가 없으면 커밋하지 않습니다.

## 주요 작업 파일

VM 작업에서 자주 확인하는 파일은 다음과 같습니다.

- `pintos/vm/vm.c`: VM 초기화, 페이지 할당, 페이지 폴트 처리, SPT 관리
- `pintos/vm/anon.c`: 익명 페이지와 스왑 처리
- `pintos/vm/file.c`: 파일 기반 페이지와 mmap 처리
- `pintos/vm/uninit.c`: 지연 로딩용 uninit page 처리
- `pintos/include/vm/vm.h`: page, frame, SPT 관련 핵심 타입과 API
- `pintos/include/vm/anon.h`: 익명 페이지 인터페이스
- `pintos/include/vm/file.h`: 파일 페이지 인터페이스
- `pintos/userprog/process.c`: 사용자 프로세스 로딩, 스택 구성, fork/exec 흐름
- `pintos/userprog/syscall.c`: 시스템 콜 처리와 파일/메모리 관련 검증

## 제출 아카이브

제출용 아카이브가 필요한 경우 `pintos/` 디렉터리에서 생성합니다.

```bash
source pintos/activate
cd pintos
make TEAM=<team-number> archive
```

## 참고 문서

- KAIST Pintos 공식 문서: https://casys-kaist.github.io/pintos-kaist/
- 저장소 내 Pintos README: `pintos/README.md`
