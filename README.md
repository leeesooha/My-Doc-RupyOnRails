# README

# Rails 개발 환경 세팅 (MacOS)


## 1) Homebrew 최신 설치 스크립트로 설치
```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

설치 완료 후 안내문에 따라 **shellenv**를 설정합니다.

* **Apple Silicon**

  ```bash
  echo 'eval "$(/opt/homebrew/bin/brew shellenv)"' >> ~/.zshrc
  eval "$(/opt/homebrew/bin/brew shellenv)"
  ```

검증:

```bash
brew -v
```
---

## 2) RubyGems로 Rails 설치 (현재 사용 중인 Ruby에 설치)

이 단계에서 `gem install bundler`, `gem install rails`를 실행하여 Rails를 설치했습니다.

```bash
gem install bundler
gem install rails
```
---

## 3) `rails -v` 명령이 스텁(/usr/bin/rails)을 가리키는 문제 해결

macOS에는 안내용 **스텁 실행파일**(`/usr/bin/rails`)이 있으며, PATH에서 이것이 먼저 잡히면 `sudo gem install rails` 안내만 나타납니다. 실제로 설치된 **진짜 rails**가 위치한 **Gem.bindir**를 PATH 앞에 추가해서 해결합니다.

### (1) 현재 세션에서 즉시 적용

```bash
export PATH="$(ruby -e 'print Gem.bindir'):$PATH"
hash -r
rails -v
```

### (2) 영구 반영(.zshrc)

```bash
echo 'export PATH="$(ruby -e '\''print Gem.bindir'\''):$PATH"' >> ~/.zshrc
source ~/.zshrc
```

검증:

```bash
which ruby
ruby -v
which gem
gem env home
type -a rails    # 여기에서 Gem.bindir 아래의 rails가 /usr/bin/rails 보다 먼저 나와야 정상
rails -v         # Rails 버전 출력되어야 정상
```

---

## 4) (선택) Ruby 버전 관리 권장 – rbenv

장기적으로는 프로젝트별 Ruby 버전을 관리하기 위해 **rbenv** 사용을 권장합니다. (이번 작업 흐름에는 필수는 아니지만, 추후 권장)

```bash
brew install rbenv ruby-build

echo 'eval "$(rbenv init - zsh)"' >> ~/.zshrc
source ~/.zshrc

# 예: Ruby 3.3.4 설치 및 사용
rbenv install 3.3.4
rbenv global 3.3.4
ruby -v

# 필요한 gem 설치
gem install bundler rails
rails -v
```

---

## 5) 프로젝트 시작 빠른 체크리스트

```bash
# 새 Rails 앱 생성 (예: API 모드가 아니라면 기본)
rails new myapp
cd myapp

# 번들 설치
bundle install

# 개발 서버 실행
bin/rails s
# 브라우저에서 http://localhost:3000 접속
```

---

## 6) 트러블슈팅

* **`Rails is not currently installed on this system` 문구**: /usr/bin/rails 스텁이 먼저 실행되는 상태입니다. 3단계의 PATH 설정을 적용하세요.
* **`You don't have write permissions for /Library/Ruby/Gems/...`**: 시스템 Ruby에 쓰려 해서 권한 오류가 납니다. `rbenv local 3.2.2`로 사용자 영역에 설치한 Ruby로 고정하세요.

---

