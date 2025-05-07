FROM demo/hadoopha

USER root

# install package
RUN apt-get update && apt-get install -y python3-venv
RUN python3 -m venv /opt/venv 
ENV PATH="/opt/venv/bin:$PATH"

RUN pip install pandas koalas pyspark
