FROM continuumio/miniconda3:4.5.4
LABEL authors="Yanhai Gong" \
      description="Docker image containing all requirements for gongyh/nf-core-scgs pipeline"

# Install procps so that Nextflow can poll CPU usage
RUN apt-get update && apt-get install -y procps libpng16-16 cmake gcc g++ hmmer2 && apt-get clean -y 

# Install locale en_US.UTF-8 used by Picard
RUN apt-get install -y locales && sed -i 's/^# *\(en_US.UTF-8\)/\1/' /etc/locale.gen && locale-gen

# Install conda environments
COPY environment.yml /
RUN conda install python=3.6 conda=4.6.12 && conda env update -n base -f /environment.yml && conda clean -a

# Clean up
RUN apt-get autoremove --purge && apt-get clean && apt-get autoremove
RUN conda clean -y -a && rm -rf /opt/conda/pkgs/*
