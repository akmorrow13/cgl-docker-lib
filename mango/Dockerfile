FROM quay.io/ucsc_cgl/spark-and-maven

MAINTAINER Alyssa Morrow, akmorrow@berkeley.edu

# Make port 8080 available for mango browser
EXPOSE 8080
# Make port 8888 available for mango notebook
EXPOSE 8888

WORKDIR /home

# clone mango
RUN git clone https://github.com/bigdatagenomics/mango.git
ENV MAVEN_OPTS "-Xmx2g"

# build mango
WORKDIR /home/mango

RUN ./scripts/move_to_spark_2.sh
RUN ./scripts/move_to_scala_2.11.sh

RUN /opt/apache-maven-3.3.9/bin/mvn package -DskipTests

# copy spark
ENV SPARK_HOME /opt/apache-spark

# Install make and pip/python dependencies
RUN apt-get update && apt-get install -y \
  make \
  python-pip \
  npm \
  nodejs \
  python-tk \
  curl

RUN ln -s /usr/bin/nodejs /usr/bin/node

RUN npm -v
RUN node -v

# set permissions for running npm. Required for mango-viz
RUN npm config set bdgenomics.mango.pileup:unsafe-perm

# TODO AM rm after fix pushed
RUN pip install ipython==5.5.0
RUN pip install jupyter


# put mango jar on the pyspark path for packaging
ENV ASSEMBLY_DIR /home/mango/mango-assembly/target
ENV ASSEMBLY_JAR "$(ls -1 "$ASSEMBLY_DIR" | grep "^mango-assembly[0-9A-Za-z\_\.-]*\.jar$" | grep -v javadoc | grep -v sources || true)"
ENV PYSPARK_SUBMIT_ARGS "--jars ${ASSEMBLY_DIR}/${ASSEMBLY_JAR} --driver-class-path ${ASSEMBLY_DIR}/${ASSEMBLY_JAR} pyspark-shell"

# set env variables for python path TODO RM
ENV PYTHONPATH ${SPARK_HOME}/python:${SPARK_HOME}/python/lib/py4j-0.10.4-src.zip

#environment variables PYSPARK_PYTHON and PYSPARK_DRIVER_PYTHON
ENV PYSPARK_PYTHON /usr/bin/python
ENV  PYSPARK_DRIVER_PYTHON /usr/bin/python

# prepare mango-viz and mango-python
WORKDIR /home/mango/mango-python
RUN make prepare && make develop

WORKDIR /home/mango/mango-viz
RUN make prepare && make develop

WORKDIR /home/mango

# by default, runs mango browser (mango-submit)
# to override to mango-notebook,
# run docker with --entrypoint=/home/mango/bin/mango-notebook
ENTRYPOINT ["/home/mango/bin/mango-submit"]
