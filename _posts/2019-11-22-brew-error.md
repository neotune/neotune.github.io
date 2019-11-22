---
layout: article
title: 맥 zsh 쉘에서 git branch 명령어 실행 시 branch 리스트가 화면 전환되어 출력될때
mathjax: true
---
## brew 명령어 실행시 Ignoring commonarker 오류 출력

brew 설치 후 install 실행시 `Ignoring commonmarker-0.17.13 because its extensions are not built` 와 같은 메세지가 계속적으로 출력되고 있었습니다.

실제 명령어 수행에는 큰 이상은 없지만 메세지가 자꾸 신경쓰여 해당 문구가 출력되지 않도록 하는 방법을 설명하였습니다.

**단 설명드리는 방법은 문제를 해결하는 방법 중에 한가지 이며 설명드리는 방법으로 해결이 안될 수도 있음을 알려드립니다.** 

### 출력되는 메세지 sample

```bash
neotune@mac-neotune brew install jython
Ignoring commonmarker-0.17.13 because its extensions are not built.  Try: gem pristine commonmarker --version 0.17.13
Ignoring commonmarker-0.17.13 because its extensions are not built.  Try: gem pristine commonmarker --version 0.17.13
==> Downloading https://homebrew.bintray.com/bottles/jython-2.7.1.mojave.bottle.1.tar.gz
==> Downloading from https://akamai.bintray.com/e6/e64dc854f84ba16de19059102aa5c94d01b9ed2a996e3bafa7df1f6f2c19e3ca?__gda__=exp=1574390614~hmac=afcb679b5fb8ac48d0dfe
######################################################################## 100.0%
==> Pouring jython-2.7.1.mojave.bottle.1.tar.gz
```

### 해결방법

1. `brew install` 실행시 나왔던 메세지 중 `try:` 이후 출력된 명령어를 sudo 권한으로 실행 (ex : sudo gem pristine commonmarker --version 0.17.13)
  ```bash
  $ sudo gem pristine commonmarker --version 0.17.13
  Password:
  Ignoring commonmarker-0.17.13 because its extensions are not built.  Try: gem pristine commonmarker --version 0.17.13
  Restoring gems to pristine condition...
  Building native extensions.  This could take a while...
  Restored commonmarker-0.17.13 
  ```

2. 정상 실행 여부 확인을 위해 아래 명령어 실행 (ex : gem list | grep commonmarker)
  ```bash
  # 해결 완료 상태 일 경우
  $ gem list | grep commonmarker
  commonmarker (0.17.13) 
  # 해결 안된 상태 일 경우
  $ gem list | grep commonmarker
  Ignoring commonmarker-0.17.13 because its extensions are not built.  Try: gem pristine commonmarker --version 0.17.13
  commonmarker (0.17.13) 
  ```