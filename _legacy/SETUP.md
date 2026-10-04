# Setup

## One-time: get this folder onto your Mac and into GitHub

Download the folder from this conversation, then:

```bash
# 1. Move it next to your existing repo
mv ~/Downloads/technical-reinforcement-log ~/Desktop/

# 2. Go in
cd ~/Desktop/technical-reinforcement-log

# 3. Create the empty folders git won't track on its own
mkdir -p leetcode/problems datacamp/courses youtube/videos
touch leetcode/problems/.gitkeep datacamp/courses/.gitkeep youtube/videos/.gitkeep

# 4. Confirm you're in the right place before doing anything else
pwd
ls
```

`pwd` should print `/Users/sanghyunseong/Desktop/technical-reinforcement-log`.
If it doesn't, stop — don't run the git commands until it does.

## Fix the git identity first

Your last repo committed with `sanghyunseong@Sanghyuns-MacBook-Pro.local` because git
guessed. Set it once, globally, and it never happens again:

```bash
git config --global user.name "Sanghyun Seong"
git config --global user.email "your-github-email@example.com"
```

Use the email attached to your GitHub account, or commits won't link to your profile —
which matters when the repo *is* the evidence.

## Initialize and push

```bash
git init
git add .
git commit -m "Initialize technical reinforcement log"
git branch -M main
```

Then create an empty repo named `technical-reinforcement-log` on GitHub — **no README, no
.gitignore**, or the push will conflict. Then:

```bash
git remote add origin https://github.com/sanghyun-s/technical-reinforcement-log.git
git push -u origin main
```

## Daily loop

```bash
cd ~/Desktop/technical-reinforcement-log
# ... write your files ...
git add .
git commit -m "Day N: 3 problems (2 unaided, 1 guided + variant passed)"
git push
```

The commit message carries the solve modes. Your commit history becomes the trend line even
before anyone opens `PROGRESS.md`.

## Naming convention

```
leetcode/problems/0001-two-sum.md          ← 4-digit zero-padded, so they sort correctly
datacamp/courses/01-intermediate-python-for-developers.md
youtube/videos/2026-07-15-silbal-agentic-coding.md
```

Zero-padding matters: without it, `1000-...` sorts before `2-...`.
