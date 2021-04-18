---
layout: post
title: Useful aliases and ENVs in cygwin .profile
date: 2016-05-11 08:45
author: isaaclevin
comments: true
categories: [Uncategorized]
---
alias cp='cp -i'
alias mv='mv -i'
alias df='df -h'
alias du='du -h'
alias grep='grep --color'
alias itest='mvn clean test verify -U'
alias ls='ls -h --color'
alias mci='mvn clean install -U'
alias mi='mvn install'
alias mjr='mvn jetty:run -o'
alias mjrwithprofile='mvn clean jetty:run -DAPP_ENV=dev -Dspring.profiles.active=dev'
alias ps='ps -W -a -f ux'
alias rm='rm -i'

function xtitle {
echo -ne "\033]0;$1\007"
}

export MAVEN_OPTS_BASE="-server -Xms128m -Xmx2048m -XX:MaxPermSize=256m"
export MAVEN_OPTS_DEBUG="$MAVEN_OPTS_BASE -Xdebug=true -Xnoagent -Djava.compiler=NONE -Xrunjdwp:transport=dt_socket,address=5555,server=y,suspend=n -Dcom.sun.management.jmxremote -Dcom.sun.management.jmxremote.ssl=false -Dcom.sun.management.jmxremote.authenticate=false -Dcom.sun.management.jmxremote.port=7777"
alias mvnAllOff='export MAVEN_OPTS=$MAVEN_OPTS_BASE'
alias mvnDebugOn='export MAVEN_OPTS=$MAVEN_OPTS_DEBUG'

mvnDebugOn

