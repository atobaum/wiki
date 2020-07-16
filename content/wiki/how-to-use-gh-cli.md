---
title: "Github cli 사용법"
summary: 'Github cli 사용법을 알아보자'
layout: wiki
parent: ''
tags: [ "github", "cli" ]
toc: false
latex: false
date: 2020-07-16T20:34:01+09:00
lastmod: 2020-07-16T20:34:01+09:00
---

# 설치
```bash
brew install github/gh/gh
yay -S github-cli
```
맘에 드는 것을 골라 설치한다. 나머지는 숙제.

# 사용법
맘에 드는 git repo로 간다.

## pr
```bash
gh pr status
```

```bash
gh pr reivew -a -b
```

- `-a --approve`
- `-c --comment`
- `-r --request-changes`
- `-b --body string`

### Ex
```bash
# approve the pull request of the current branch
$ gh pr review --approve

# leave a review comment for the current branch
$ gh pr review --comment -b "interesting"

# add a review for a specific pull request
$ gh pr review 123

# request changes on a specific pull request
$ gh pr review 123 -r -b "needs more ASCII art"
```

```bash
gh pr checkout {<number> | <url> | <branch>} 
```

```bash
gh pr merge [<number> | <url> | <branch>]
```
- `-d, --delete-branch`   Delete the local and remote branch after merge (default true)
- `-m, --merge`           Merge the commits with the base branch
- `-r, --rebase`          Rebase the commits onto the base branch
- `-s, --squash`          Squash the commits into one commit and merge it into the base branch

아무 옵션 안쓰면 물어보니 걱정하지 말자.

## issue
가능한 명령
- `create`: 옵션 안붙이면 물어본다.
- `status`
- `list`
- `view [<number>]`
- `close [<number>]`

