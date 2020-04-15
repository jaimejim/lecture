# Lecture Material

[![Build Status](https://travis-ci.org/jaimejim/lecture.svg?branch=master)](https://travis-ci.org/jaimejim/lecture)

Contains notes and lecture material around IoT topics.

```
└── src
    ├── 10years.md
    ├── SUMMARY.md
    ├── another10years.md
    ├── coap.md
    ├── coapddos.md
    ├── coapdiscovery.md
    ├── coaplinks.md
    ├── coapmessage.md
    ├── ietf.md
    ├── img
    ├── introduction.md
    ├── reading.md
    ├── riotos.md
    └── udpddos.md
```

## Fixing travis bad token error

Docs: <https://docs.travis-ci.com/user/environment-variables#defining-variables-in-repository-settings> and <https://help.github.com/en/github/authenticating-to-github/creating-a-personal-access-token-for-the-command-line>.

Previous steps:
Make sure that both .org and .com travis are fine: <https://travis-ci.com/github/jaimejim/lecture>, <https://travis-ci.org/github/jaimejim/lecture>.

If that looks OK, then follow the subsequent steps:

1. Create another token on the github profile. Select repo scope, and repo-hook scope.
2. Use that token to create a new environment variable on this repo. Use `travis encrypt MY_SECRET_ENV=TOKEN-JUST-MADE --add env.global` 
3. Go to travis <https://travis-ci.org/github/jaimejim/lecture/settings> add the TOKEN-JUST-MADE as a new environment variable.
4. add, commit, push. It should work now.

