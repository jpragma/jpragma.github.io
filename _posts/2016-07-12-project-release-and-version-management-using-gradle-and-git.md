---
layout: post
title: Project release and version management using gradle and git
date: 2016-07-12 14:03
author: isaaclevin
comments: true
categories: [Uncategorized]
---
<h3>Two very useful Gradle plugins:</h3>
<strong>1. axion-release-plugin</strong>
<a href="https://github.com/allegro/axion-release-plugin"><em>https://github.com/allegro/axion-release-plugin</em></a>

This plugin derives project version from tags in git repository. I use following rules in my projects:
<ul>
 	<li>Start with default version 0.1.0-SNAPSHOT</li>
 	<li>Every release of master branch increments second digit</li>
 	<li>Production emergency fixes are done on a branch, which is created from the version tag. Such branch name must start with "hotfix-" prefix. Releasing this branch will increment 3rd digit.</li>
 	<li>Feature branches must have "topic-" prefix. They are never released, however their version includes feature name.</li>
</ul>
To check current project version run "gradle currentVersion"
To release the project run "gradle release"

<strong>2. gradle-nexus-plugin</strong>
<a href="https://github.com/bmuschko/gradle-nexus-plugin"><em>https://github.com/bmuschko/gradle-nexus-plugin</em></a>

This plugin adds ability to upload project artifacts to nexus repository. Simply run "gradle upload".


<h3>Example gradle.build</h3>

[sourcecode lang="groovy"]
buildscript {
    repositories {
        maven {url = 'http://repo1.maven.org/maven2'}
    }
}

plugins {
    id 'java'
    id 'pl.allegro.tech.build.axion-release' version '1.3.4'
    id 'com.bmuschko.nexus' version '2.3.1'
}

scmVersion {
    versionIncrementer 'incrementMinor'
    branchVersionIncrementer = [
            'hotfix-.*' : { c -&gt; c.currentVersion.incrementPatchVersion() }
    ]
    branchVersionCreator = [
            'topic-.*' : 'versionWithBranch'
    ]
}

group 'com.jpragma.myproject'
version = scmVersion.version

repositories {
    jcenter {url = 'http://jcenter.bintray.com/'}
}

dependencies {
    compile 'org.slf4j:slf4j-api:1.7.21'
    testCompile 'junit:junit:4.12'
}

nexus {
    sign = false
    repositoryUrl = 'http://localhost:8081/nexus/content/repositories/internal/'
    snapshotRepositoryUrl = 'http://localhost:8081/nexus/content/repositories/internal-snapshots/'
}
[/sourcecode]
