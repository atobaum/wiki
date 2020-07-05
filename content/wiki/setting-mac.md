---
title: "Setting Mac"
summary: ''
layout: wiki
parent: ''
tags:
- MacOS
toc: false
latex: false
date: 2020-07-05T23:19:48+09:00
lastmod: 2020-07-05T23:19:48+09:00
---
# Install Homebrew
```
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install.sh)"
```

# 키보드 맵핑 바꾸기
```
brew install karabiner-elements
```

## 오른쪽 command를 한/영 변환으로 바꾸자
Chnage right_command to f18 in simple modification tab of karabiner-elements. 그리고 `설정 > 키보드 > 단축키 > 입력 소스 > 이전 입력 소스 선택`을 `F18`로 바꾸자.

## 왼쪽 capslock을 control/esc로 바꾸자
다음 complex modification을 사용. capslock과 다른 키를 누르면 control로, capslock만 누르면 esc로 작동한다. 덤으로 키보드 레이아웃을 영문으로 바꿔준다.
```
    {
      "description": "Change caps_lock to control if pressed with other keys, to escape if pressed alone.",
      "manipulators": [
        {
          "type": "basic",
          "from": {
            "key_code": "caps_lock",
            "modifiers": {
              "optional": ["any"]
            }
          },
          "to": [
            {
              "key_code": "left_control"
            }
          ],
          "to_if_alone": [
            {
              "key_code": "escape"
            },
            {
              "select_input_source": {
                "language": "en"
              }
            }
          ]
        }
      ]
    },
```

# 설치한 것
* Chrome
* iTerm2
* Oh-my-zsh
* vs code
* 카카오톡
