# Launch4j Maven Plugin (Forked By Seraphy)
[![Build Status](https://travis-ci.org/seraphy/launch4j-maven-plugin.svg?branch=bintray)](https://travis-ci.org/seraphy/launch4j-maven-plugin)
[ ![Download](https://api.bintray.com/packages/seraphy/maven/launch4j-maven-plugin/images/download.svg) ](https://bintray.com/seraphy/maven/launch4j-maven-plugin/_latestVersion)


Forked from https://github.com/lukaszlenart/launch4j-maven-plugin

Originally hosted at http://9stmaryrd.com/tools/launch4j-maven-plugin/


これはlukaszlenart/launch4j-maven-pluginのLaunch4jプラグインのフォークです。

以下の点を修正しています。

- objs, libsの指定で作業ディレクトリに *.o, *.aファイルをコピーする際に親フォルダが作成されおらず、コピーできない。

この生成物は[Bintray](https://bintray.com/seraphy/maven/launch4j-maven-plugin)にあります。

```xml
	<pluginRepositories>
		<pluginRepository>
			<id>bintray-seraphy-maven</id>
			<name>bintray-plugins</name>
			<url>https://dl.bintray.com/seraphy/maven</url>
		</pluginRepository>
	</pluginRepositories>
```

**<span style="color:red">2018/12/29 本家のmaven pluginに、本修正PullRequestが受理され、無事、Maven Centralにも反映されました。</span>**

**現在は、```1.7.25``` を使うことで、本修正は対応済みとなっています。**


## ちなみに、obj, libの指定方法

Launch4jはjavaを起動するためのスタブとなる部分をC言語で記述しており、リソースコンパイラとともにexe生成時にリンクして実行ファイルを作成している。

このC言語で書かれた部分はMITライセンスになっており、これを自分用のスタートアップルーチンに書き換えて使うことも可能である。

本プラグインのversion1.7.24では、Launch4jの3.12を使用している。

Launch4j 3.12では、MinGWのbinutils 2.22を使用しており、このバージョンから、おそらく、gccは32ビット版の4.6.2あたりを使っていると思われる。

Cのソースは、このLaunch4j 3.12のzip内に収められており、Dev-C++ 5.0.2以降あたりで*.devのプロジェクトファイルを開き、コンパイルすることができる。

カスタマイズして得られたhead.o, guihead.oなどのOBJファイルは、同じ環境下のライブラリファイル(*.a)とともに指定する必要がある。(順番も重要)

たとえば、以下のように記述する。

```xml
	<plugin>
		<!-- Launch4jによるjarファイルのexe化を行う. http://launch4j.sourceforge.net/docs.html
			プラグインが1.7.24の場合、使用するのはLaunch4j 3.12 である。
			https://github.com/lukaszlenart/launch4j-maven-plugin/blob/master/pom.xml -->
		<!-- <groupId>com.akathist.maven.plugins.launch4j</groupId> -->
		<groupId>jp.seraphyware.maven.plugins.launch4j</groupId>
		<artifactId>launch4j-maven-plugin</artifactId>
		<version>1.7.24.1</version>
		<executions>
			<execution>
				<id>l4j-gui</id>
				<phase>package</phase>
				<goals>
					<goal>launch4j</goal>
				</goals>
				<configuration>
					<headerType>gui</headerType>
					<outfile>target/${project.artifactId}.exe</outfile>
					<jar>target/${project.artifactId}.jar</jar>
					<errTitle>Failed to execute the ${project.artifactId}</errTitle>
					<icon>icon.ico</icon>
					<objs>
						<obj>src/Launch4JStub/w32api/crt2.o</obj>
						<obj>src/Launch4JStub/head/head.o</obj>
						<obj>src/Launch4JStub/head/guihead.o</obj>
					</objs>
					<libs>
						<lib>src/Launch4jStub/w32api/libmingw32.a</lib>
						<lib>src/Launch4jStub/w32api/libgcc.a</lib>
						<lib>src/Launch4jStub/w32api/libmsvcrt.a</lib>
						<lib>src/Launch4jStub/w32api/libkernel32.a</lib>
						<lib>src/Launch4jStub/w32api/libuser32.a</lib>
						<lib>src/Launch4jStub/w32api/libadvapi32.a</lib>
						<lib>src/Launch4jStub/w32api/libshell32.a</lib>
					</libs>
					<jre>
						<path>jre</path>
						<minVersion>1.7.0</minVersion>
						<initialHeapSize>64</initialHeapSize>
						<maxHeapSize>128</maxHeapSize>
					</jre>
					<versionInfo>
						<fileVersion>${project.version}</fileVersion>
						<txtFileVersion>${project.version}.${buildNumber}</txtFileVersion>
						<fileDescription>${project.artifactId} ${project.version} ${buildNumber}</fileDescription>
						<copyright><![CDATA[${maven.build.timestamp} ${project.developers[0].id}]]></copyright>
						<productVersion>${project.version}</productVersion>
						<txtProductVersion>${project.version}</txtProductVersion>
						<productName>${project.artifactId}</productName>
						<internalName>${project.artifactId}</internalName>
						<originalFilename>${project.artifactId}.exe</originalFilename>
					</versionInfo>
				</configuration>
			</execution>
		</executions>
	</plugin>

```

------

 - [Documentation](#documentation)
 - [Version Notes](#version-notes)
 - [FAQ](#faq)

# Documentation

Please check [this](src/main/resources/README.adoc) document for more detailed info on how to use the plugin. Please also check [Launch4j's Configuration file](http://launch4j.sourceforge.net/docs.html#Configuration_file) page.

# Version Notes

## Version notes 1.7.24.1
- objs, libsで指定した*.o, *aファイルの作業フォルダへのコピーが失敗していた問題の修正

## Version notes 1.7.24
- adds a `threadSafe` flag to the Mojo to properly mark that the plugin is thread safe, see [#72](../../issues/72)

## Version notes 1.7.23
- adds a `parallelExecution` flag that will allow to run only one instance of the plugin in the given time, see [#72](../../issues/72)

## Version notes 1.7.22
- upgrades to Launch4j version 3.12, see [#75](../../issues/75)

## Version notes 1.7.21
- fixes issue with detecting OSX, see [#58](../../issues/58)

## Version notes 1.7.20
- uses the `linux64` platform when run on 64-bit Linux, see [#59](../../pull/59)

## Version notes 1.7.19
- upgrades to the version 3.11 of Launch4j

## Version notes 1.7.18
- reverts changes introduced in **1.7.17**, see [#55](../../pull/55)

## Version notes 1.7.17
- adds support for unwrapped jar, see [#55](../../pull/55)

## Version notes 1.7.16
- detects different OSX versions to properly use proper binary bundle, see [#54](../../pull/54)

## Version notes 1.7.15
- allows override some properties loaded from an external Launch4j config file, see [#49](../../issues/49)

## Version notes 1.7.14
- fixes issue with setting `language`, see [#50](../../issues/50)

## Version notes 1.7.13
- upgrades maven plugins to latest versions, see [#47](../../issues/47)

## Version notes 1.7.12
- adds support for missing options, see [#45](../../issues/45)
  - `language` - please use one of the values as defined for the `<language/>` tag
  - `trademarks` -  a free text used as a trademarks

## Version notes 1.7.11
- upgrades to Launch4j version 3.9

## Version notes 1.7.10
- fixes broken `<configuration/>` when not using `<infile/>`

## Version notes 1.7.9
- adds capability of loading Launch4j native configuration file
```xml
<configuration>
    <infile>${project.basedir}/src/main/resources/my-app-config.xml</infile>
</configuration>
```
By default it will take from `${project.basedir}/src/main/resources/${project.artifactId}-launch4j.xml`.
Plugin execution goal should be set to `install`. It's an optional configuration, you can either use your existing configuration as it was in previous version or use native **Launch4j** [config file](http://launch4j.sourceforge.net/docs.html#Configuration_file) via `<infile>`.

## Version notes 1.7.8
- fixes issue with spaces in path to maven repository on non-Windows systems, see [#27](../../issues/27), [#28](../../issues/28)

## Version notes 1.7.7
- once again fixes problem with including dependencies in scope `runtime` (now it should be the final solution), see [#5](../../issues/5)
- adds support for `bundledJreAsFallback` and `bundledJre64Bit` properties, see [#23](../../issues/23)
- upgrades Launch4j to 3.8.0, see [#21](../../issues/21)

## Version notes 1.7.6
- fixes again problem with including dependencies in scope `runtime`, see [#5](../../issues/5)

## Version notes 1.7.5
- allows add custom headers and libraries to working dir  [#22](../../pull/22)

## Version notes 1.7.4
- fixes type in default value for `outfile` parameter  [#17](../../pull/17)

## Version notes 1.7.3
- uses Maven annotation instead of JavaDoc parameters [#15](../../pull/15)
- upgrades Maven plugins [#15](../../pull/15)
- converts tabs to spaces [5b0619](../../commit/5b0619)

## Version notes 1.7.2
- adds support for `restartOnCrash` Launch4j's option [#14](../../pull/14)

## Version notes 1.7.1
- launch4j's `abeille` dependency was excluded [#11](../../pull/11)
- versions of several plugins were updated [#11](../../pull/11)
- tabs were converted to spaces [#11](../../pull/11)

## Version notes 1.7
- uses the latest version of Launch4j (3.5.0)
- contains support for `runtimeBits`, see [#6](../../issues/6)
- ~~fixes problem with including dependencies in scope `runtime`, see [#5](../../issues/5)~~

## Version notes 1.6
- dropped Launch4j source and based on artifacts from Maven Central, see [#8](../../issues/8)
- uses the latest version of Launch4j (3.4.0)
- at least Java 1.7 is required

# FAQ
Q: I cannot build my project because `dsol-xml` dependency is missing?

A: Add this repository to your `~/.m2/settings.xml`

```xml
<repositories>
    <repository>
        <id>dsol-xml</id>
        <name>Simulation @ TU Delft</name>
        <url>http://simulation.tudelft.nl/maven/</url>
    </repository>
</repositories>
```
Q: Where can I find -SNAPSHOT builds?

A: Use the Sonatype OSS repo

```xml
<repositories>
    <repository>
        <id>sonatype-nexus-snapshots</id>
        <name>Sonatype Nexus Snapshots</name>
        <url>https://oss.sonatype.org/content/repositories/snapshots/</url>
        <releases>
            <enabled>false</enabled>
        </releases>
        <snapshots>
            <enabled>true</enabled>
        </snapshots>
    </repository>
</repositories>
```

Q: Can I use Launch4j on 64bit OS?

A: Yes but you will have to install these libs to avoid problems:

 - lib32z1
 - lib32ncurses5
 - lib32bz2-1.0 ( (has been ia32-libs in older Ubuntu versions)
 - zlib.i686
 - ncurses-libs.i686
 - bzip2-libs.i686

See [#4](../../issues/4) for more details.
