---
layout: default
title: Tooling Tips and Tricks
#date: 2017-12-17 12:00:00 -0800
excerpt_separator: <!--more-->
---

## Tooling Tips and Tricks

To get started into actual blogging, I'll cover one of my passions and a never ending puzzle: **development environment configuration**. Generally, my tooling has become pretty stable, but recently, I made a few nice additions worth sharing.
<!--more-->

### Productivity Expected Value
Almost every developer nowadays spends some time on this (setting up work-specific shortcut commands, baby's first vimrc, etc), albeit most people are less fanatic about this than I am. Like many effort-based skills, there's a heavy tail of things to learn, but with only a "little" bit of time and investment, you can remove a lot of really annoying mundane tasks.

Nowadays, I "almost" always first consider the value of time I'd be saving if I tried to figure out how to speed up a process in my life. If learning a new trick savings your `T` seconds each time you perform the task, and you spend `M` minutes learning the task, you need to be doing that task at least `M  * 60 / T` times a year before breaking even (Note: I'm bounding productivity estimates to a year, since tools/workflows may change pretty fast, and if you use them even longer, it's an even better ROI!). As a concrete example, if you're saving 10 seconds per use and spend 1 hour learning about a tool, you better use it at least 600 times this year. Obviously, there's also other benefits, like learning for learning's sake, looking like a l33th4X0r when pair programming, and benefiting others by sharing your productivity knowledge.

I'll put each of the tips I have to share in perspective of my own estimates (and post-hoc benefits).

### Opening GitHub Pull Requests from Terminal
At work, the typical workflow is: make a change locally, (repeat edit/test loop until ready), and then push to the company's shared Github Repo and create a Pull Request (PR) for code review. I have a shortcut to quickly take me to "https://github.com/$COMPANY/$REPO/", and I found myself often waiting for Github's polling loop to eventually show me this:

![]({{ "/assets/tooling/github_recently_pushed_branches.png" | absolute_url }})

This takes anywhere between ~10-30 seconds for me, and given I just finished working on this code, I don't want to context switch to anything else before getting it into a reviewable state. That's why I was ecstatic when my coworker Andrew showed me this neat bash function:

```bash
_build_url() {
  # shellcheck disable=SC2039
  local upstream origin branch repo pr_url target
  upstream="$(git config --get remote.upstream.url)"
  origin="$(git config --get remote.origin.url)"
  branch="$(git symbolic-ref --short HEAD)"
  repo="$(_get_repo "$origin")"
  pr_url="https://github.com/$repo/pull/new"
  target="$1"
  test -z "$target" && target=$(git for-each-ref --format='%(upstream:short)' "$(git symbolic-ref -q HEAD)" | cut -d '/' -f 2)
  test -z "$target" && target="master"
  if [ -z "$upstream" ]; then
    echo "$pr_url/$target...$branch"
  else
    # shellcheck disable=SC2039
    local origin_name upstream_name
    origin_name="$(echo "$repo" | cut -f1 -d'/')"
    upstream_name="$(_get_repo "$upstream" | cut -f1 -d'/')"
    echo "$pr_url/$upstream_name:$target...$origin_name:$branch"
  fi
}

# shellcheck disable=SC2039
open-pr() {
  # shellcheck disable=SC2039
  local url
  url="$(_build_url "$*")"
  if [ "$(uname -s)" = "Darwin" ]; then
    open "$url" 2> /dev/null
  else
    xdg-open "$url" > /dev/null 2> /dev/null
  fi
}
```

To summarize, `open-pr` opens your default browser with a url for creating a PR (in this case: `https://github.com/$COMPANY/$REPO/compare/master...ericwang-db:fooBranch`), so all you need to do is write the PR description and submit to create your PR! Fortunately, GitHub's URL is always fixed for PR creation, which makes this function straightforward and easy. Note: the example I showed is for creating a PR against an upstream git repo; If you create PRs against your own fork, you have to make sure the "upstream" git remote is not defined.

Personally, my workflows involve creating many concurrent PRs, and I typically create 5-10 PRs a week against just our work's monorepo. I'd estimate my per-shortcut savings to be ~10 seconds, so annually, I'm getting back 65 min. To be honest, that's pretty small in the grand scheme of things, but it only took a few minutes to figure out how to incorporate this (thanks to Andrew!), and I find mindless pauses waiting for spinners/pages loading pretty disruptive to mental flow.

My terminal workflow now looks like this:

```bash
# ... edit test loop ...
git commit -am "foo message"
git pr
open-pr
```

I defined `git pr` (yes force-pushing is bad ... just don't do this to your master branch!) as an alias in my `~/.gitconfig`:
```
[alias]
  pr = !"git push -f origin $(git symbolic-ref --short HEAD)"
```

### Zsh/Oh-My-Zsh Tweaks

I use Zsh and Oh-My-Zsh as my terminal shell of choice, and the out-of-box settings are awesome. I've found that I don't need to make many tweaks to my configuration (in contrast, my `~/.bashrc` was way too long/confusing), but I decided to make my own fork to make a few changes. If you're curious about the changes, they are the most recent commits in [my Oh-My-Zsh fork](https://github.com/ericewang/oh-my-zsh/). However, they can all be summed up by this screen shot:

![]({{ "/assets/tooling/zsh-autocomplete.png" | absolute_url }})

There are two changes that are worth pointing out:

#### zsh-syntax-highlighting

File paths are underlines as you type, and disappear once the path is invalid. Valid commands are "yellow", and invalid commands are "red".

The expected value of this configuration change is harder to estimate, as it's not a shortening of an existing workflow. As someone who types pretty fast and not particularly carefully, I commonly type the command and hit 'Enter', only to find that it fails with something like `can't open file 'foo'` or `error: the path "foo" does not exist`.

#### Displaying the Kubernetes context/namespace

This one is not relevant for most people, but to put things in perspective, it's the dual of showing the git branch in your terminal. This is really helpful for those who use the Kubernetes command line tool `kubectl`, as you can run commands without specifying `--context` and `--namespace` and know that you're targeting the right environment. The value this quick view provides is even further amplified with a couple more aliases:

```bash
function context {
  # If context is provided then actually use that context.
  if (( ${+1} )); then
    kubectl config use-context $1
  # Otherwise list contexts.
  else
    kubectl config get-contexts
  fi
}
function namespace() {
  # If ns is provided then actually use that ns.
  if (( ${+1} )); then
    kubectl config set-context $(current_context) --namespace $1
  # Otherwise list ns.
  else
    kubectl get namespaces
  fi
}
alias ct='context'
alias ns='namespace'
alias k='kubectl'
```

This allows you to quickly switch your environment and know what's going on, making interacting with your Kubernetes clusters so much easier! I use these commands many times a day, so shortening the repetitious patterns allows me to focus on the actual problems at hand.

### Wrap-up

Hopefully you find that some of these workflow enhancements apply to you. If you have even-better tips, or are interested in learning more, please [let me know](mailto:{{ site.personal_email }})!


