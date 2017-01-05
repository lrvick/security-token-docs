# Commit signing

Signing all commits has significant value when you do it universally, and
all the more when you have CI that expects it.

## Rationale

### Eliminate CI as an attack surface

Once you have a CI system that rejects any unsigned commits, you avoid an
attacker with git access being able to explore or execute code on these systems
at all.

### Unambiguous Authorship

Short of enforced signing, anyone can commit with the email of anyone else. A 
typical VCS like Git has no way to protect against this.
 
If someone submits 5 very similar PRs, one might review the first one in detail
then not inspect the rest quite as close, because humans. By contrast however
if 4 of the 5 are signed, suddenly the outlier is suspect, could be from
someone else entirely, and will get additional scrutiny.

A dishonest or disgruntled employee could easily rewrite history to make a
mistake appear to have been committed by someone else, instead of having to
come to terms with it.

When engineers all sign their commits both at work and in open source projects,
it avoids reputation hijacking where someone might commit malicious code to
a repo with a naive maintainer hoping they will blindly trust your name without
careful inspection.

### Distributed Accountability

Only signing tags implies you are approving the whole history

By signing only tags you place the full burden of reviewing all history and
every line of code between releases on the individual cutting a release. In a
large codebase this is an unreasonable expectation.

Having signing and review at every PR between releases distributes
responsibility more fairly. When you sign a commit or sign a mege commit after
a PR you are signing only that one chunk of code or subset of changes and
creating a smaller set of checks and balances through the release cycle.

### Proof of code-review

Once a merge to master is done, CI should fail to build unless the merge commit
itself at HEAD is signed, and signed by someone different than the authors of
the included commits.

## Setup

### Git

Adjust your ~/.gitconfig to be similar to the following:

```
[user]
    email = john@doe.com
    name = John H. Doe
    signingKey = 6B61ECD76088748C70590D55E90A401336C8AAA9 # get from 'gpg --list-keys --with-colons'
[commit]
    gpgSign = true
[gpg]
    program = gpg2
```
