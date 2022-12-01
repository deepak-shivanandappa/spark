# Apache Spark for Lacework

Main reasons for lacework to fork Apache spark source code -
1. Spark current version is building source code with deprecated `openjdk:11-jre-slim`( refer this URL for notice - https://hub.docker.com/_/openjdk), this means build is older, missing any security patches or bug fixes.
2. Lacework batch/service code is built using Amazon Corretto Java 8 but has problem running on `openjdk:11-jre-slim`.
3. Fix is available in spark master branch and scheduled to release in 3.4 version, but there is no time-line on 3.4 version release. https://issues.apache.org/jira/browse/SPARK-40256

Note: 
- Lacework is using `focal` image tag - these are the suite code names for releases of `Ubuntu` and indicate which release the image is based on. Also, generally lacework service/batch all running on `Ubuntu`. For more reference - https://hub.docker.com/_/eclipse-temurin
- OpenJDK is built on top of Debian, Dockerfile was modify http to https of its sources list file[https://wiki.debian.org/SourcesList#Example_sources.list], where Eclipse temurin is built on top of Ubuntu - https://manpages.ubuntu.com/manpages/xenial/man5/sources.list.5.html 

## Build instructions
Apache spark is built using Maven and following are the steps.
```bash
export MAVEN_OPTS="-Xss64m -Xmx2g -XX:ReservedCodeCacheSize=1g"

## Build source code
./build/mvn -DskipTests clean package
```

## Build the distributable package with Kubernetes support
```bash
./dev/make-distribution.sh --name lw-branch-3.3 --tgz -Pkubernetes
```

## Build and push to docker.io/lacework/graphgen
```bash
./bin/docker-image-tool.sh -r lacework/graphgen -t lw-spark-baseimg-v3.3.2 -f dist/kubernetes/dockerfiles/spark/Dockerfile  build
./bin/docker-image-tool.sh -r lacework/graphgen -t lw-spark-baseimg-v3.3.2 push
```

Follow this document for more instruction of build - https://spark.apache.org/docs/latest/building-spark.html
