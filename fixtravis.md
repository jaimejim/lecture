## Fixing travis bad token error

```
Setting environment variables from repository settings
$ export github-token=[secure]
We were unable to parse one of your secure environment variables.
Please make sure to escape special characters such as ' ' (white space) and $ (dollar symbol) with \ (backslash) .
For example, thi$isanexample would be typed as thi\$isanexample. See https://docs.travis-ci.com/user/encryption-keys.
````

Docs: <https://docs.travis-ci.com/user/environment-variables#defining-variables-in-repository-settings> and <https://help.github.com/en/github/authenticating-to-github/creating-a-personal-access-token-for-the-command-line>.

Previous steps:
Make sure that both .org and .com travis are fine: <https://travis-ci.com/github/jaimejim/lecture>, <https://travis-ci.org/github/jaimejim/lecture>.

If that looks OK, then follow the subsequent steps:

1. Create another token on the github profile. Select repo scope, and repo-hook scope.
2. Use that token to create a new environment variable on this repo. Use `travis encrypt MY_SECRET_ENV=TOKEN-JUST-MADE --add env.global`
3. Go to travis <https://travis-ci.org/github/jaimejim/lecture/settings> add the TOKEN-JUST-MADE as a new environment variable.
4. add, commit, push. It should work now.


It might also be that the repository has the wrong settings...

