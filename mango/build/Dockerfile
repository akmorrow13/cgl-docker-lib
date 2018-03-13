FROM quay.io/ucsc_cgl/spark-and-maven

MAINTAINER Alyssa Morrow, akmorrow@berkeley.edu

# Make port 8080 available for mango browser
EXPOSE 8080
# Make port 8888 available for mango notebook
EXPOSE 8888

WORKDIR /home

# clone mango TODO
wget https://oss.sonatype.org/content/repositories/snapshots/org/bdgenomics/mango/mango-assembly/0.0.1-SNAPSHOT/test.tar.gz

tar xzvf test.tar.gz

WORKDIR /home/mango-distribution-0.0.1-SNAPSHOT
