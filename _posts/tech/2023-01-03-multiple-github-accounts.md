---
layout: post
permalink: /tech/multiple-github-accounts/
title: Multiple GitHub Accounts with the Same Computer
abstract: It's not too hard to configure your computer to allow you to easily clone, push, and pull with a single GitHub account - the standard approach is to set up the account with ssh keys, add them to an ssh agent, and add your name and e-mail to the global .gitconfig file. But it's a little tricker if you want to, say, set up your computer to work wih both your personal and professional GitHub account. This post provides a solution that avoids the ssh agent entirely and instead uses git's sshCommand and includeIf features.
date: 2023-01-03
categories: tech
tags:
comments: true
---

Normally the best way to configure your computer to interact with a GitHub account is as follows:
1. Create a SSH keypair, and upload the public key to GitHub
2. Add github.com as a host in `~/.ssh/config`, and link that host to the private key
3. Add the private key to your computer's SSH agent
4. Add your name and e-mail address to `~/.gitconfig` to sign your commits
