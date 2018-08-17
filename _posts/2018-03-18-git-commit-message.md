---
layout: post
current: post
cover:  assets/images/covers/git-cover.jpg
navigation: True
title: Git Commit Message
date: 2018-03-18 23:42:22 +0900
tags: [git]
class: post-template
subclass: 'post tag-git'
author: youngsup
---
# Git Commit Message 작성하기

## tl;dr

* 첫 줄의 제목은 50자 이내의 명령문으로 작성하며, 대문자로 시작하고 마침표를 사용하지 않는다.

* 두번째 줄은 제목과 본문을 구분하기 위해 비운다.

* 나머지 줄에는 구체적으로 어떤 문제를 해결하고 왜 해결해야 했는지 적는다.
  * Explain what and why, not how
  * Wrap the body at 72 characters

Commit message template:

```text
# If applied, this commit will...

# Explain why this change is being made

# Provide links to any relevant tickets, articles or other resources
```

커밋 메시지 템플릿을 적용하기 위해, 위의 내용을 파일로 저장하고(e.g., ~/.git_commit_msg.txt) 다음 명령을 실행한다:

```shell
$ git config --global commit.template ~/.git_commit_msg.txt
```

![git-commit-template]({{ "/assets/images/git-commit-message/git-commit-template.png" | absolute_url }})

## 구체적인 작성 요령

### 1. 첫 줄(제목 행)은 무엇을 끝냈는지 명령문으로 작성

* 명령문은 Git 커밋 제목 행에 완벽하게 부합한다.
* **Git 자체가 우리 대신 자동으로 커밋을 생성하는 경우, 명령조를 사용한다.**

**아래 문장에 커밋 내용을 대입하였을 때 문장으로써 적절하면, 그것은 적절한 Git 커밋 제목 행이다.**

* If applied, this commit will __your subject line here__
* 만약 이 커밋이 적용되면 이 커밋은 __커밋 제목 행을 여기에__

**좋은 예**:

* If applied, this commit will **refactor subsystem X for readability**
* If applied, this commit will **update getting started documentation**
* If applied, this commit will **remove deprecated methods**
* If applied, this commit will **release version 1.0.0**
* If applied, this commit will **merge pull request #123 from user/branch**
* 만약 이 커밋이 적용되면 이 커밋은 **가독성을 위해 서브시스템 X를 리팩토링한다**
* 만약 이 커밋이 적용되면 이 커밋은 **Getting Started 문서를 갱신한다**
* 만약 이 커밋이 적용되면 이 커밋은 **Deprecated된 메소드를 삭제한다**
* 만약 이 커밋이 적용되면 이 커밋은 **버전 1.0.0으로 판올림한다**
* 만약 이 커밋이 적용되면 이 커밋은 **someuser/somebranch에서 온 pull request #123을 병합한다**

**안좋은 예**:

* If applied, this commit will **fixed bug with Y**
* If applied, this commit will **changing behavior of X**
* If applied, this commit will **more fixes for broken stuff**
* If applied, this commit will **sweet new API methods**
* 만약 이 커밋이 적용되면 이 커밋은 **Y로 버그가 고쳐짐**
* 만약 이 커밋이 적용되면 이 커밋은 **X 동작 변화**
* 만약 이 커밋이 적용되면 이 커밋은 **망가진 것을 좀 더 고친 것들**
* 만약 이 커밋이 적용되면 이 커밋은 **좋은 새 API 메소드**

#### 1-1. 영문의 커밋 메시지는 과거형으로 작성하지 않는다.

* Fixed 대신 Fix, Added 대신 Add, Changed 대신 Change 로 작성

#### 1-2. 첫 줄의 끝에는 마침표를 사용하지 않는다.

### 2. 첫 줄은 50자 내로, 나머지 줄은 72자 단위로 개행한다.

* vim을 에디터로 사용 하는 경우 `~/.vimrc` 설정에 다음을 추가:

```text
autocmd Filetype gitcommit setlocal spell textwidth=72 
```

* vim을 에디터로 설정하기:

```shell
$ git config --global core.editor vim
```

화면 예시:

![git-commit-vim]({{ "/assets/images/git-commit-message/git-commit-vim.png" | absolute_url }})

### 3. 두 번째 줄은 항상 비워 둠

* 제목과 본문을 구별하기 위함.

### 4. 나머지 줄은 항상 자세하게 설명

* 코드에 어떠한 문제가 있었는지, 왜 그 문제가 발생했는지, 최종적으로 새로 작성한 수정 코드는 어떤 코드인지 최대한 자세하게 설명

* **어떻게 보다는 무엇과 왜를 설명한다(어떤게는 코드가 설명한다)**

* Issue 를 닫거나 할 때는 [Closing issues via commit messages](https://help.github.com/articles/closing-issues-via-commit-messages/) 가이드 라인에 맞춰 메시지를 작성
  * 예를 들어, 커밋 메시지에 `fixes #1`과 같이 작성하면 해당 이슈가 자동으로 닫힘.

### 5. 커밋 메시지 제목의 첫 글자는 대문자로 시작(영어로 작성하는 경우)

### 6. 제목 줄에 무엇을 추가했는지 적었고 더 이상 설명할 필요가 없어도 해당 커밋이 무엇을 의미하는지, 어떤 역할을 할 수 있는지에 대해 추가로 작성

[예시1](https://github.com/ejmr/php-mode/commit/ac392ad5f0151a90d753592c4a71ce04b8f99fb3)

[예시2](https://github.com/ejmr/php-mode/commit/d084cffbf7095b1ebe9090ad18d475c7ba6eba74)

왜 이 커밋을 착성했는지에 대해 추가로 작성하면 더 좋음: [예시](https://github.com/ejmr/php-mode/commit/7f02722d5aa60d9a95a0560a439e38f80d5e5e87)

### 7. 가능한 간결하게 문법에 맞게 작성

## 참고

* [좋은 깃(Git) 커밋 메시지 작성하기](https://tech.ssut.me/2015/06/24/write-a-good-git-commit-message/)
* [How to Write a Git Commit Message](https://chris.beams.io/posts/git-commit/)
* [How to Write a Git Commit Message(번역)](https://item4.github.io/2016-11-01/How-to-Write-a-Git-Commit-Message/)
* [A useful template for commit messages](http://codeinthehole.com/tips/a-useful-template-for-commit-messages/)
