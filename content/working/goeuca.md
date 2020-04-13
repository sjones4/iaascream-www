---
title: "Go Eucalyptus"
date: 2020-04-10T18:18:18-07:00
showDate: false
---

A [cli](https://github.com/sjones4/euca) and [sdk](https://github.com/sjones4/eucalyptus-sdk-go)
for eucalyptus clouds written in Go.

The command line tool provides alternatives to the _euctl_ and _euserv-*_
commands. Some useful differences from the standard tools are the bash
command completion and ease of distribution of the binary.

The sdk is useful for access to eucalyptus specific services from Go. The
standard aws sdk for Go can be used for aws services that eucalyptus supports.
The sdk also has JSON model files for eucalyptus services which may allow
generation of clients for other languages (see also the
[sdk for Java](https://github.com/sjones4/you-are-sdk))

The github projects for the cli and sdk show example usage.
