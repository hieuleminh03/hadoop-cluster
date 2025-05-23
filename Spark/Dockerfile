FROM demo/hadoopha

USER root

RUN apt-get update && apt-get install -y python3-venv
RUN python3 -m venv /opt/venv 
ENV PATH="/opt/venv/bin:$PATH"

# Download  Spark
RUN cd /usr/local && wget -O ./spark.tar.gz https://archive.apache.org/dist/spark/spark-3.0.3/spark-3.0.3-bin-hadoop2.7.tgz && tar -xvf spark.tar.gz && chown root: -R /usr/local/spark-3.0.3-bin-hadoop2.7 && mv spark-3.0.3-bin-hadoop2.7 spark

RUN echo -e $'# Set Spark environment\n\
export SPARK_HOME="/usr/local/spark"\n\
export PATH="$SPARK_HOME/bin:$PATH"' >> /root/.bashrc && source /root/.bashrc

# Set spark-env.sh
RUN cp /usr/local/spark/conf/spark-env.sh.template /usr/local/spark/conf/spark-env.sh && \
echo -e $'# Set pyspark python\n\
export PYSPARK_PYTHON="/usr/bin/python3"\n\
# Koalas env variable\n\
export PYARROW_IGNORE_TIMEZONE=1\n\
# Spark on YARN\n\
export HADOOP_CONF_DIR="/usr/local/hadoop/ect/hadoop"'>> /usr/local/spark/conf/spark-env.sh

# Set spark-defaults.conf
RUN cp /usr/local/spark/conf/spark-defaults.conf.template /usr/local/spark/conf/spark-defaults.conf && \
echo -e $'spark.driver.extraJavaOptions="-Dio.netty.tryReflectionSetAccessible=true"\n\
spark.executor.extraJavaOptions="-Dio.netty.tryReflectionSetAccessible=true"'>> /usr/local/spark/conf/spark-defaults.conf

# install package
RUN pip install jupyterlab pandas koalas pyspark

# Set jupyter Notebook
RUN jupyter lab --generate-config
RUN sed -i "757s@.*@c.ServerApp.ip = '*'@g" /root/.jupyter/jupyter_lab_config.py
RUN sed -i '608s@.*@c.ServerApp.allow_root = True@g' /root/.jupyter/jupyter_lab_config.py

# Set pyspark.sh, sparkcodes
COPY ./pysparklab.sh /usr/local/hadoop/pysparklab.sh
COPY ./sparkcodes /usr/local/hadoop/sparkcodes

# Set $SPARK_HOME/conf env file
COPY ./core-site.xml /usr/local/spark/conf/core-site.xml
COPY ./mapred-site.xml /usr/local/spark/conf/mapred-site.xml
COPY ./yarn-site.xml /usr/local/spark/conf/yarn-site.xml
COPY ./hdfs-site.xml /usr/local/spark/conf/hdfs-site.xml

# Remove downloaded spark file
RUN rm /usr/local/spark.tar.gz
