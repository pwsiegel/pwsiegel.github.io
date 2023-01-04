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
