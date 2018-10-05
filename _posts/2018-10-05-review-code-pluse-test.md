---
title: [후기]디지털 과거 Code+ 예선
updated: 2018-10-05 20:30
---

![img_01](https://raw.githubusercontent.com/jeageon/jeageon.github.io/master/_images/img_03.PNG)

9월 30일까지 예선 접수였는데 딱 30일에 해당 공고를 봐서 바로 신청해버렸습니다 하핳 실제 접수기간은 17일부터인가.. 여튼 넉넉했던거 같은데 제가 SNS에서 해당  공고를 그전에는 전혀 보지 못하다가 30일에만 여러번 보게된게 신기하네요.

여담이긴한데 요즘은 특히 SNS나 웹사이트에 뜨는 광고나 게시글들이 자동으로 사용자의 취향을 분석해서 추천해주는게 참 신기하면서도 재밌더라구요.  예를들어서 뭐 셔츠를 사고싶어서 쇼핑몰 몇번만 들락날락하면 바로 facebook이나 인스타그램에 뜨는 추천 광고들이 싹다 옷광고들로 바뀌어서 제가 쇼핑몰을 더 찾을 수고를 덜어주긴 합니다. 마찬가지로 평소에 이런 코딩테스트들을 열심히 찾아보고 다니니까 이젠 웬만한 테스트들은 sns에 광고로 뜨더라구요ㅎㅎ

여튼 마지막날 신청을 하고 바로 10월 3일 개천절날 예선 테스트를 진행하였습니다.

<div class="divider"></div>

#### 테스트 형태

**구름Test**에서 진행되었으며 **2시간동안 4문제**를 풀었습니다. 일반적인 온라인 코딩테스트의 형식를 따랐으며 특이점이라면 굉장히 많은 언어를 지원했습니다. 일반적으로 코딩테스트 보러가면 적을땐 `c`, `c++`, `java`정도 지원하고 요즘은 일반적으로 + `python`, `javascript` 까지 지원하는것 같더라구요. 많이 지원해주는 곳은 뭐 `swift`나 `ruby`, `go`까지도 지원해주는걸 본거같긴한데 이번 테스트는 정말 듣도보도 못한 언어들도 다 지원했습니다. 정확한 지원 언어는 다음과 같습니다.

- C/C++: gcc (5.4.0), ldd(Ubuntu GLIBC 2.23-0ubuntu3, 2.23), g++ (Ubuntu 5.4.0-6ubuntu1~16.04.4) 5.4.0 20160609
- Java: javac (openjdk version "1.8.0_131")
- Python: Python 2.7.12
- Python3: Python 3.5.2
- Go: Go 1.6.2 linux/amd64
- Swift: Swift version 4.0 (swift-4.0-RELEASE)
- Javascript: Nodejs v8.9.4
- Ruby: Ruby 2.3.1p112 (2016-04-26) [x86_64-linux-gnu]
- Kotlin: Kotlin version 1.1.2-5
- Scala: Scala code runner version 2.12.2
- [VB.NET](http://vb.net/): Visual Basic.Net Compiler version 0.0.0.5943 (Mono 4.0.1 - tarball)
- Pascal: Free Pascal Compiler version 3.0.2 [2017/02/05] for x86_64
- Lua: Lua 5.3.4  Copyright (C) 1994-2017 Lua.org, PUC-Rio
- Objective-C: gcc (Ubuntu 5.4.0-6ubuntu1~16.04.4) 5.4.0 20160609, GNUStep 1.25
- R: R scripting front-end version 3.4.0 (2017-04-21)
- Rust: rustc 1.18.0 (03fc9d622 2017-06-06)
- Cobol: cobc (OpenCOBOL) 1.1.0
- Clojure: Clojure 1.8.0
- Smalltalk: GNU Smalltalk version 3.2.5
- Dart: Dart VM version: 1.24.1 (Wed Jun 14 07:49:34 2017) on "linux_x64"
- Haskell: The Glorious Glasgow Haskell Compilation System, version 7.10.3
- Perl: Perl v5.26.0
- Common Lisp: GNU CLISP 2.49
- D: DMD64 D Compiler v2.074.1
- Erlang: Erlang/OTP 20.0
- PHP: PHP 7.1.6
- C#: Dotnet Core 1.0.4

#### 테스트 유형

대회 취지 자체가 국내 IT기업들의 채용 문제들을 풀어내는것이라서 여러 기업들의 채용문제들을 거의 그대로 가져온 느낌이였습니다. (*특히 **우아한형제**들이랑 **NHN***)

따라서 문제들의 기본적인 형식에 통일성이 없습니다. 이건 문제자체의 유형이랑은 좀 다른 이야기이긴한데... 일반적으로 여기저기에서 코딩테스트를 보다보면 해당 주최측에 따라서 문제의 입력의 형식과 출력의 형식에 딱 통일성이 있습니다. 그래서 문제를 풀때 입력과 출력하는 부분의 코드는 거의 모든 문제에 동일하게 사용하고 알고리즘만 바꿔가는형식으로 문제를 풀어냈는데 이번 테스트는 뭔가 입출력의 형식도 문제마다 다 다르고 입력의 조건도 다들 요상하게 달라서 문제별로 언어나 메소드를 다르게 사용해서 풀었습니다.

IT기업 코딩테스트들의 문제이기때문에 난이도는 비교적 무난합니다. 앞선 라인플러스 인턴 코딩테스트때도 언급했지만 이런 테스트들에서는 알고리즘의 효율성을 생각하는것보다 빠르고 정확한 구현의 목적이 큰거같아서 문제를 정확히 이해하고 빠른 구현을 할 수 있다면 좋은 결과를 받을 수 있을것같습니다.

<div class="divider"></div>

![img_02](https://raw.githubusercontent.com/jeageon/jeageon.github.io/master/_images/img_04.PNG)

운이 좋게도 예선은 통과했습니다 하핳

근데 본선이 부산에서 오프라인으로 진행되던데... 음 참가할지는 고민을 좀 해봐야겠네요.