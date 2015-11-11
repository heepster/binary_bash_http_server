# Komorebi
Komorebi is a lightweight netcat-based HTTP server written in Bash.  It was written to solve this situation:

1.  You have service A running on a machine that has an HTTP interface for health checking
2.  Service A's health check always returns an HTTP response code of 200 regardless of health (but returns descriptive data in the HTTP body)
3.  Service B does HTTP health checks on service A and determines health by HTTP response code

Thus, Service B considers Service A as "healthy" even if A's health check HTTP body is:

```
{ "status": "Completely Dead" }
```

Komorebi can help here because it supports arbitrary commands and translates the exit code into one of two HTTP responses:

1.  200 OK
2.  500 Internal Error

## Requirements
Komorebi has only one requirement: that `nc` is available somewhere in your $PATH.  You can install it with:

```
apt-get install netcat-openbsd
```

The traditional netcat package doesn't seem to behave correctly, so make sure you get the openbsd version.

If `netcat-openbsd` isn't installed, Komorebi will automatically try to install it if it can find `apt-get`.  It assumes you are `root` (Heh, sorry...).

## Usage

Here's one way of using Komorebi:

```
$ COMMAND='curl -s localhost:9200/health-check | grep -q GOOD' PORT=3000 ./komorebi
Starting Komorebi HTTP server on port 3000
Command: 'curl -s localhost:9200/health-check | grep -q GOOD'
```

Komorebi will return 200 if the body of the health check on port 9200 has the word "GOOD" in it.  Otherwise it will return a 500.

If you don't have `git`, you can just:

```
$ curl 'https://raw.githubusercontent.com/heepster/komorebi/master/komorebi' | COMMAND='<command>' PORT='<port>' bash
```

## Defaults

Here are the default values Komorebi will use if you don't specify:

```
COMMAND: 'date'
PORT: 1500
```
